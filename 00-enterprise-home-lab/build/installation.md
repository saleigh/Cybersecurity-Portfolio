# Step-by-Step Build — Enterprise Home Lab (VMware)

IP plan and network diagram: [`../architecture/ip-addressing.md`](../architecture/ip-addressing.md)

## Host prerequisites

- 16GB+ RAM recommended (8GB workable if you run 2–3 VMs at a time, not all six)
- 4+ CPU cores, virtualization enabled in BIOS (VT-x/AMD-V)
- ~250GB free disk space across all VM disks
- VMware Workstation Pro (free since Broadcom's license change)

## Phase 1: Hypervisor and network

1. Install VMware Workstation Pro.
2. Open **Edit → Virtual Network Editor**. Confirm `VMnet1` (Host-Only) is set to
   `192.168.56.0/24` — this is VMware's default, which is why the lab uses this range.
3. Untick "Connect a host virtual adapter" if you want the lab fully isolated from your
   physical host; leave it ticked if you want to browse to the lab's web UIs (pfSense,
   Wazuh) directly from your host machine. Most people leave it ticked.
4. Only pfSense gets a second adapter set to **NAT** — every other VM only gets the
   Host-Only adapter.

## Phase 2: pfSense (gateway, DHCP, DNS relay)

1. Create a VM: 1 vCPU, 1GB RAM, 16GB disk. Adapter 1: NAT (WAN). Adapter 2: Host-Only
   `VMnet1` (LAN).
2. Install pfSense CE with defaults.
3. From the console menu, set LAN to `192.168.56.1/24`.
4. Web UI at `https://192.168.56.1` from any LAN VM. Default login `admin`/`pfsense` —
   **change this immediately**.
5. **Interfaces → WAN**: untick "Block private networks" (your NAT range is itself
   private).
6. **Services → DHCP Server**: enable on LAN, pool `192.168.56.100–200`, DNS server
   `192.168.56.10`.
7. Snapshot the VM as `pfsense-clean`.

## Phase 3: Domain Controller (DC01, 192.168.56.10)

Install Windows Server 2022 (Desktop Experience), then in PowerShell as admin:

```powershell
New-NetIPAddress -InterfaceAlias "Ethernet0" -IPAddress 192.168.56.10 -PrefixLength 24 -DefaultGateway 192.168.56.1
Set-DnsClientServerAddress -InterfaceAlias "Ethernet0" -ServerAddresses 127.0.0.1
Rename-Computer -NewName DC01 -Restart
```

After reboot:

```powershell
Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
Install-ADDSForest -DomainName "ubuntupublic.local" -DomainNetbiosName "UBUNTUPUB" -InstallDns
```

After the second reboot, build structure and a few test accounts:

```powershell
New-ADOrganizationalUnit -Name "UbuntuPublic" -Path "DC=ubuntupublic,DC=local"
foreach ($ou in "Users","Workstations","Servers","Admins") {
  New-ADOrganizationalUnit -Name $ou -Path "OU=UbuntuPublic,DC=ubuntupublic,DC=local"
}

$pw = ConvertTo-SecureString "Lab-Passw0rd!23" -AsPlainText -Force
New-ADUser -Name "Alice Smith" -SamAccountName asmith -AccountPassword $pw -Enabled $true -Path "OU=Users,OU=UbuntuPublic,DC=ubuntupublic,DC=local"
New-ADUser -Name "Bob Jones" -SamAccountName bjones -AccountPassword $pw -Enabled $true -Path "OU=Users,OU=UbuntuPublic,DC=ubuntupublic,DC=local"
New-ADUser -Name "Svc Backup" -SamAccountName svc_backup -AccountPassword $pw -Enabled $true -Path "OU=Admins,OU=UbuntuPublic,DC=ubuntupublic,DC=local"
```

Snapshot as `dc01-ad-built`.

## Phase 4: Windows client (192.168.56.20)

1. Install Windows 10/11, static IP `192.168.56.20/24`, gateway `192.168.56.1`, DNS
   `192.168.56.10`.
2. Confirm `nslookup ubuntupublic.local` resolves before joining.
3. `Add-Computer -DomainName ubuntupublic.local -Credential UBUNTUPUB\Administrator -Restart`
4. Move the computer object to `OU=Workstations`, log in as `UBUNTUPUB\asmith`.
5. Snapshot as `client-domain-joined`.

## Phase 5: Ubuntu server (192.168.56.30)

```yaml
# /etc/netplan/00-lab.yaml
network:
  version: 2
  ethernets:
    ens33:
      addresses: [192.168.56.30/24]
      routes:
        - to: default
          via: 192.168.56.1
      nameservers:
        addresses: [192.168.56.10]
```

```bash
sudo netplan apply
sudo hostnamectl set-hostname ubuntu-srv
sudo apt update && sudo apt install -y openssh-server apache2
```

Confirm your interface name with `ip a` — VMware guests are usually `ens33`, not `enp0s3`.

## Phase 6: Wazuh SIEM (192.168.56.40)

Same netplan pattern on `.40`, then:

```bash
curl -sO https://packages.wazuh.com/4.x/wazuh-install.sh
sudo bash ./wazuh-install.sh -a
```

Save the generated admin password. Browse `https://192.168.56.40`, deploy agents to
DC01, WIN10-CLIENT, and UBUNTU-SRV from **Agents → Deploy new agent**. Confirm all three
show **Active** before moving on — this is the single most important checkpoint, since
Project 08 (SOC/SIEM) and every detection phase after it depends on it working.

## Phase 7: Kali (192.168.56.50)

```bash
sudo nmcli con mod "Wired connection 1" ipv4.method manual \
  ipv4.addresses 192.168.56.50/24 ipv4.gateway 192.168.56.1 ipv4.dns 192.168.56.10
sudo nmcli con up "Wired connection 1"
```

## Verification checklist

- [ ] Every VM pings `192.168.56.1` and `192.168.56.10`
- [ ] Every VM resolves `ubuntupublic.local`
- [ ] WIN10-CLIENT logs in as a domain user
- [ ] UBUNTU-SRV reaches the internet via pfSense (`apt update` works)
- [ ] Wazuh dashboard shows 3 active agents
- [ ] A test logon on the client generates Event ID 4624 in Wazuh
- [ ] Your home network is unreachable from KALI
- [ ] Final snapshot `baseline-complete` taken on every VM

Screenshot each check into [`../evidence/screenshots/`](../evidence/screenshots/) — this
evidence gets referenced from every later project that uses this lab.

# 🛡️ Cybersecurity Portfolio

**Strategy · Governance · Risk · Operations · Architecture · AI Security · Ethical Hacking**

A hands-on cybersecurity portfolio built around one real, isolated home lab
(`192.168.56.0/24`, VMware, documented in [`00-enterprise-home-lab`](./00-enterprise-home-lab/)).
Every later project builds on that same lab rather than starting from scratch, so the
network diagrams and IP addresses stay consistent project to project.

> **Disclaimer:** This portfolio uses a fictional organization, **Ubuntu Public
> Services**. All scenarios, IP schemas, and risk ratings are synthetic and built for
> demonstration.

## How to read this repo

Each project folder follows one of two layouts:

**Technical labs** (hands-on build + attack + detect):
```
NN-project-name/
├── README.md
├── architecture/    diagrams, IP addressing
├── build/           how the environment was deployed
├── security/        hardening, logging, monitoring config
├── attacks/         what was simulated, against the lab only
├── detection/       what it triggered, and where
├── evidence/        screenshots/, logs/
├── reports/         technical + executive summary
└── configs/, docs/  scripts, configs, exported reports
```

**Governance / strategy projects** (policy, risk, dashboards):
```
NN-project-name/
├── README.md
├── images/          diagrams, screenshots
├── docs/            policies, registers, reports
└── configs/         any supporting scripts/templates
```

## Portfolio Projects

| Category | Projects |
|---|---|
| 🏛️ Governance & Risk | [01-Strategy](./01-enterprise-cyber-strategy/), [02-Risk](./02-enterprise-cyber-risk/), [03-IR](./03-incident-response-governance/), [04-Compliance](./04-isms-compliance/), [05-Dashboard](./05-ciso-dashboard-budget/), [06-AI-Gov](./06-ai-governance/) |
| 🖥️ Technical Operations | [00-HomeLab](./00-enterprise-home-lab/), [07-VulnMgmt](./07-vulnerability-management/), [08-SOC](./08-soc-siem-detection/), [09-Cloud](./09-cloud-zero-trust/), [10-Architecture](./10-cybersecurity-architecture/), [11-DFIR](./11-dfir-forensics/), [12-Malware](./12-malware-analysis/), [13-Hunting](./13-threat-hunting/), [14-Network](./14-network-security/), [15-AD](./15-active-directory/), [16-Phishing](./16-phishing-investigation/) |
| 🗡️ Offensive & Data | [17-Passwords](./17-password-security/), [18-WebApp](./18-web-app-security/), [19-OSINT](./19-osint-reconnaissance/), [20-Splunk](./20-splunk-log-analysis/), [21-Crypto](./21-cryptography-data-protection/) |
| 🤖 AI Deployment | [22-PrivateAI](./22-private-ai-chatbot/), [23-API-Gateway](./23-secure-ai-api-gateway/), [24-AI-Testing](./24-ai-security-testing/), [25-AI-Monitoring](./25-ai-operations-monitoring/), [26-AI-Data](./26-secure-ai-data-pipeline/) |
| 💥 Ethical Hacking | [27-SQLI](./27-web-app-sqli-exploitation/), [28-Metasploit](./28-network-exploitation-metasploit/), [29-GoPhish](./29-phishing-simulation-gophish/), [30-Reporting](./30-pentest-executive-reporting/) |

## Build Status

This repo is built incrementally, project by project, each one verified before moving to
the next (see each project's `build/installation.md` or `README.md` verification
checklist). Projects not yet built show a stub README describing what's planned.

## Contact

- **GitHub:** github.com/saleigh

# Home SOC Lab & Portfolio

A hands-on Security Operations Center (SOC) lab I'm building to practise the day-to-day work of a SOC analyst: collecting logs, knowing what normal looks like, detecting attacks, and writing up what I find.

Each project is self-contained and ends with evidence: a write-up, screenshots, and a diagram where it helps. Projects build on each other in increasing difficulty, and I'm working through HTB Academy's SOC Analyst path alongside them.

## Lab Architecture

| Component | Role |
|---|---|
| **VMware Workstation** | Hypervisor running all lab VMs |
| **Windows 10 VM** (`10.10.10.10`) | Monitored endpoint: Sysmon + Windows Advanced Audit Policy |
| **Kali Linux VM** (`10.10.10.20`) | Attack simulation to generate realistic detection data |
| **Isolated host-only network** (`10.10.10.0/24`) | Keeps attack traffic off the home network; a separate NAT adapter is used only for updates |
| **VM snapshots** | Clean, known-good states to roll back to after every test |

## Projects

| # | Project | What It Shows | Status |
|---|---|---|---|
| 1 | [Home SOC Lab Build](projects/project-1-home-soc-lab-build/README.md) | Network design, dual-adapter isolation, static addressing, golden snapshots | ✅ Complete |
| 2 | [Windows Logging Baseline with Sysmon](projects/project-2-sysmon-baseline/README.md) | Sysmon (SwiftOnSecurity config), audit policy, documented "normal" baseline, Event ID / Logon Type / SubStatus reference | ✅ Complete |
| 3 | Wazuh SIEM Setup + Detection | Forwarding Windows/Sysmon logs into a SIEM and building first alerts | 🔄 Next |
| 4 | AI-Assisted Alert Triage | Using AI to speed up triage of SIEM alerts | Planned |
| 5 | Brute-Force Detection | Detecting password attacks from 4625 patterns | Planned |
| 6 | Manual Log Forensics | Reconstructing an incident timeline from raw logs | Planned |
| 7 | AI-Assisted Log Triage at Scale | Triage of large log sets with AI support | Planned |
| 8 | Wireshark: C2 Beacon Detection | Spotting command-and-control beaconing in packet captures | Planned |
| 9 | Wireshark: Data Exfiltration Detection | Spotting exfiltration against a normal traffic baseline | Planned |
| 10 | MITRE ATT&CK Mapping | Mapping lab detections to ATT&CK techniques | Planned |
| 11 | Manual Sigma Rule | Writing a detection rule by hand | Planned |
| 12 | AI-Assisted Sigma Rule Drafting | Drafting and validating Sigma rules with AI help | Planned |
| 13 | Mini Automation Pipeline | Automating a small part of the detection/response workflow | Planned |
| 14 | Azure Sentinel Cloud Lab | Taking the same skills to a cloud SIEM | Planned |

## Skills Demonstrated So Far

- **Endpoint logging:** Sysmon deployment and configuration, Windows Advanced Audit Policy, log sizing
- **Log analysis:** Sysmon Event IDs (1, 3, 13, 22 and more), Security Event IDs (4624, 4625, 4688), Logon Types, 4625 SubStatus codes
- **Baselining:** documenting normal activity and explaining every event, so anomalies stand out
- **Lab engineering:** VMware networking, isolated segments, static IPs, snapshot-based rollback
- **Documentation:** evidence-backed write-ups with screenshots and diagrams

## Contact

[LinkedIn](https://www.linkedin.com/in/saksham-saksham-57606036a)

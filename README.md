# Asset Discovery & Asset Inventory Management Report

![Domain](https://img.shields.io/badge/Domain-Cyber%20Security-blue.svg)
![Phase](https://img.shields.io/badge/Phase-Vulnerability%20Assessment-orange.svg)
![OS](https://img.shields.io/badge/OS-Kali%20Linux%20Rolling-dragon.svg)

## 📌 Overview
Asset discovery serves as the foundation for effective vulnerability assessment and patch management; an organization cannot secure assets it has not identified[span_1](start_span)[span_1](end_span). 

This project covers Phase 1 of a vulnerability assessment workflow: executing a structured asset discovery and inventory pass on a Kali Linux virtual machine[span_2](start_span)[span_2](end_span). The evaluation covers hardware, operating system releases, network interfaces, user privileges, installed applications, and active network listeners[span_3](start_span)[span_3](end_span). All enumeration was performed using native, non-destructive, read-only Linux CLI commands from a non-root shell[span_4](start_span)[span_4](end_span).

---

## 🛠️ Methodology & Enumeration Tools

Native Linux utilities were used to audit each specific asset tier[span_5](start_span)[span_5](end_span):

| Utility / Command | Audit Target | Purpose |
| :--- | :--- | :--- |
| `cat /etc/passwd \| grep -E "bash\|zsh"`[span_6](start_span)[span_6](end_span) | User Accounts[span_7](start_span)[span_7](end_span) | Enumerate local accounts with interactive login shells[span_8](start_span)[span_8](end_span). |
| `ip -br addr show`[span_9](start_span)[span_9](end_span) | Network Interfaces[span_10](start_span)[span_10](end_span) | List network interface states and assigned IPv4/IPv6 addresses[span_11](start_span)[span_11](end_span). |
| `cat /etc/os-release`[span_12](start_span)[span_12](end_span) | Operating System[span_13](start_span)[span_13](end_span) | Identify Linux distribution name, version, and codename[span_14](start_span)[span_14](end_span). |
| `uname -mrs`[span_15](start_span)[span_15](end_span) | System Kernel[span_16](start_span)[span_16](end_span) | Capture kernel release version and CPU architecture[span_17](start_span)[span_17](end_span). |
| `lscpu`[span_18](start_span)[span_18](end_span) | Hardware / CPU[span_19](start_span)[span_19](end_span) | Inventory CPU topology and hardware vulnerability/mitigation states[span_20](start_span)[span_20](end_span). |
| `ss -tulpn`[span_21](start_span)[span_21](end_span) | Network Services[span_22](start_span)[span_22](end_span) | Enumerate open TCP/UDP listening ports and bound processes[span_23](start_span)[span_23](end_span). |
| `free -h`[span_24](start_span)[span_24](end_span) | System Memory[span_25](start_span)[span_25](end_span) | Audit RAM and swap space utilization[span_26](start_span)[span_26](end_span). |

---

## 📊 Asset Inventory Summary

A total of 14 key assets were identified across 6 categories during this audit[span_27](start_span)[span_27](end_span):

* **Hardware (HW)**: Intel Core i5-8365U @ 1.60GHz (2 vCPUs)[span_28](start_span)[span_28](end_span); 1.9 GiB RAM (875 MiB used, 1.1 GiB free) with 953 MiB swap[span_29](start_span)[span_29](end_span).
* **Operating System (OS)**: Kali GNU/Linux Rolling `2026.3` (`kali-rolling`)[span_30](start_span)[span_30](end_span) running Linux Kernel `7.0.12+kali-amd64` (`x86_64`)[span_31](start_span)[span_31](end_span).
* **Network Interfaces (NET)**: Loopback `lo` (`127.0.0.1/8`)[span_32](start_span)[span_32](end_span); primary interface `eth0` (`10.0.2.15/24` NAT)[span_33](start_span)[span_33](end_span); and inactive Docker bridge `docker0` (`172.17.0.1/16`, State: DOWN)[span_34](start_span)[span_34](end_span).
* **User Accounts (USR)**: Superuser `root` (UID 0)[span_35](start_span)[span_35](end_span); service account `postgres` (UID 115)[span_36](start_span)[span_36](end_span); default account `kali` (UID 1000)[span_37](start_span)[span_37](end_span); primary admin account `HasnainAli` (UID 1001)[span_38](start_span)[span_38](end_span).
* **Applications (APP)**: Inferred presence of PostgreSQL engine[span_39](start_span)[span_39](end_span) and Docker Engine[span_40](start_span)[span_40](end_span).
* **Active Services (SVC)**: Unidentified process listening on loopback port `TCP 127.0.0.1:43619`[span_41](start_span)[span_41](end_span).

---

## 🎯 Top Priority Assets for Risk Management

1. **Operating System & Kernel (`OS-01`, `OS-02`)**  
   * **Details**: Kali GNU/Linux Rolling 2026.3, Kernel 7.0.12+kali-amd64[span_42](start_span)[span_42](end_span).  
   * **Risk Rationale**: All security mitigations rely on the base kernel[span_43](start_span)[span_43](end_span). CPU vulnerability output flagged two unmitigated hardware vulnerabilities (*MMIO Stale Data*, *Speculative Store Bypass*)[span_44](start_span)[span_44](end_span). Rolling distributions require continuous patch validation[span_45](start_span)[span_45](end_span).

2. **Privileged & Service Accounts (`USR-01`, `USR-02`)**  
   * **Details**: `root` superuser account and `postgres` service account[span_46](start_span)[span_46](end_span).  
   * **Risk Rationale**: Root compromise yields total control of the system[span_47](start_span)[span_47](end_span). PostgreSQL compromise risks database integrity[span_48](start_span)[span_48](end_span). Account access policies and SSH exposure must be prioritized[span_49](start_span)[span_49](end_span).

3. **Network Attack Surface (`NET-02`, `SVC-01`)**  
   * **Details**: Primary interface `eth0` and unidentified listener `TCP 127.0.0.1:43619`[span_50](start_span)[span_50](end_span).  
   * **Risk Rationale**: `eth0` represents the sole network entry point[span_51](start_span)[span_51](end_span). Any active port bound to an unidentified process must be investigated as an unverified risk[span_52](start_span)[span_52](end_span).

---

## ⚠️ Findings Requiring Further Investigation

* **Unresolved Listener**: `TCP 127.0.0.1:43619` process identity was obscured because `ss` was run without elevated privileges (`sudo`)[span_53](start_span)[span_53](end_span).
* **Hardware Vulnerabilities**: MMIO Stale Data and Speculative Store Bypass reported as `Vulnerable`[span_54](start_span)[span_54](end_span). Microcode reads `0xffffffff`, typical for virtualized guests requiring hypervisor-level patching[span_55](start_span)[span_55](end_span).
* **Redundant Attack Surface**: `docker0` bridge is DOWN (Docker installed but inactive)[span_56](start_span)[span_56](end_span). Unused default `kali` account exists alongside `HasnainAli`[span_57](start_span)[span_57](end_span).
* **Inferred Software**: PostgreSQL and Docker engines were detected via service accounts and bridge interfaces rather than explicit package queries[span_58](start_span)[span_58](end_span).

---

## 🚀 Recommended Action Plan

* [ ] Re-run port enumeration with elevated privileges (`sudo ss -tulpn` / `sudo lsof -i:43619`)[span_59](start_span)[span_59](end_span).
* [ ] Generate a complete package manifest using `dpkg -l` / `apt list --installed` to verify application versions[span_60](start_span)[span_60](end_span).
* [ ] Feed OS, kernel, and hardware CPU flags into an automated vulnerability scanner[span_61](start_span)[span_61](end_span).
* [ ] Disable redundant accounts (`kali`) and unneeded services to minimize exposure[span_62](start_span)[span_62](end_span).
* [ ] Establish an ongoing inventory refresh schedule for rolling-release management[span_63](start_span)[span_63](end_span).

---

## 👤 Author & Acknowledgments

* **Author**: Hasnain Ali[span_64](start_span)[span_64](end_span)
* **Program**: GLAXIT Internship Program — Advanced Cyber Security[span_65](start_span)[span_65](end_span)
* **Supervisor**: Sir Saifullah[span_66](start_span)[span_66](end_span)
* **Submission Date**: August 9, 2026[span_67](start_span)[span_67](end_span)
*

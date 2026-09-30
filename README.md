# Asset Discovery & Asset Inventory Management Report

![Domain](https://img.shields.io/badge/Domain-Cyber%20Security-blue.svg)
![Phase](https://img.shields.io/badge/Phase-Vulnerability%20Assessment-orange.svg)
![OS](https://img.shields.io/badge/OS-Kali%20Linux%20Rolling-dragon.svg)

## 📌 Overview
Asset discovery serves as the foundation for effective vulnerability assessment and patch management; an organization cannot secure assets it has not identified. 

This project covers Phase 1 of a vulnerability assessment workflow: executing a structured asset discovery and inventory pass on a Kali Linux virtual machine. The evaluation covers hardware, operating system releases, network interfaces, user privileges, installed applications, and active network listeners. All enumeration was performed using native, non-destructive, read-only Linux CLI commands from a non-root shell.

---

## 🛠️ Methodology & Enumeration Tools

Native Linux utilities were used to audit each specific asset tier:

| Utility / Command | Audit Target | Purpose |
| :--- | :--- | :--- |
| `cat /etc/passwd \| grep -E "bash\|zsh"` | User Accounts | Enumerate local accounts with interactive login shells. |
| `ip -br addr show` | Network Interfaces | List network interface states and assigned IPv4/IPv6 addresses. |
| `cat /etc/os-release` | Operating System | Identify Linux distribution name, version, and codename. |
| `uname -mrs`| System Kernel | Capture kernel release version and CPU architecture. |
| `lscpu` | Hardware / CPU | Inventory CPU topology and hardware vulnerability/mitigation states. |
| `ss -tulpn` | Network Services | Enumerate open TCP/UDP listening ports and bound processes. |
| `free -h` | System Memory | Audit RAM and swap space utilization. |

---

## 📊 Asset Inventory Summary

A total of 14 key assets were identified across 6 categories during this audit:

* **Hardware (HW)**: Intel Core i5-8365U @ 1.60GHz (2 vCPUs); 1.9 GiB RAM (875 MiB used, 1.1 GiB free) with 953 MiB swap.
* **Operating System (OS)**: Kali GNU/Linux Rolling `2026.3` (`kali-rolling`) running Linux Kernel `7.0.12+kali-amd64` (`x86_64`).
* **Network Interfaces (NET)**: Loopback `lo` (`127.0.0.1/8`); primary interface `eth0` (`10.0.2.15/24` NAT); and inactive Docker bridge `docker0` (`172.17.0.1/16`, State: DOWN).
* **User Accounts (USR)**: Superuser `root` (UID 0); service account `postgres` (UID 115); default account `kali` (UID 1000); primary admin account `HasnainAli` (UID 1001).
* **Applications (APP)**: Inferred presence of PostgreSQL engine and Docker Engine.
* **Active Services (SVC)**: Unidentified process listening on loopback port `TCP 127.0.0.1:43619`.

---

## 🎯 Top Priority Assets for Risk Management

1. **Operating System & Kernel (`OS-01`, `OS-02`)**  
   * **Details**: Kali GNU/Linux Rolling 2026.3, Kernel 7.0.12+kali-amd64.  
   * **Risk Rationale**: All security mitigations rely on the base kernel. CPU vulnerability output flagged two unmitigated hardware vulnerabilities (*MMIO Stale Data*, *Speculative Store Bypass*). Rolling distributions require continuous patch validation.

2. **Privileged & Service Accounts (`USR-01`, `USR-02`)**  
   * **Details**: `root` superuser account and `postgres` service account.  
   * **Risk Rationale**: Root compromise yields total control of the system. PostgreSQL compromise risks database integrity Account access policies and SSH exposure must be prioritized.

3. **Network Attack Surface (`NET-02`, `SVC-01`)**  
   * **Details**: Primary interface `eth0` and unidentified listener `TCP 127.0.0.1:43619`).  
   * **Risk Rationale**: `eth0` represents the sole network entry point. Any active port bound to an unidentified process must be investigated as an unverified risk.

---

## ⚠️ Findings Requiring Further Investigation

* **Unresolved Listener**: `TCP 127.0.0.1:43619` process identity was obscured because `ss` was run without elevated privileges (`sudo`).
* **Hardware Vulnerabilities**: MMIO Stale Data and Speculative Store Bypass reported as `Vulnerable`. Microcode reads `0xffffffff`, typical for virtualized guests requiring hypervisor-level patching.
* **Redundant Attack Surface**: `docker0` bridge is DOWN (Docker installed but inactive). Unused default `kali` account exists alongside `HasnainAli`.
* **Inferred Software**: PostgreSQL and Docker engines were detected via service accounts and bridge interfaces rather than explicit package queries.

---

## 🚀 Recommended Action Plan

* [ ] Re-run port enumeration with elevated privileges (`sudo ss -tulpn` / `sudo lsof -i:43619`).
* [ ] Generate a complete package manifest using `dpkg -l` / `apt list --installed` to verify application versions.
* [ ] Feed OS, kernel, and hardware CPU flags into an automated vulnerability scanner.
* [ ] Disable redundant accounts (`kali`) and unneeded services to minimize exposure.
* [ ] Establish an ongoing inventory refresh schedule for rolling-release management.

---

## 👤 Author & Acknowledgments

* **Author**: Hasnain Ali
* **Program**: GLAXIT Internship Program — Advanced Cyber Security
* **Supervisor**: Sir Saifullah
* **Submission Date**: August 9, 2026

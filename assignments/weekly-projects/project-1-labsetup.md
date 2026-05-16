# Hands-On Assignment #1: Penetration Testing Lab Setup

**Course:** MSSE 642 – Software Assurance 
**Author:** Abdullah Bahir  
**Date:** May 2026  
**Version:** 3.0  

---

## Table of Contents

1. [Overview](#overview)
2. [Technology Stack](#technology-stack)
3. [Architectural Diagram](#architectural-diagram)
4. [Virtualization Environment](#virtualization-environment)
5. [Kali Linux Setup](#kali-linux-setup)
6. [Nessus Installation](#nessus-installation)
7. [Metasploitable 2 Setup](#metasploitable-2-setup)
8. [Network Connectivity Test](#network-connectivity-test)
9. [Problems & Solutions](#problems--solutions)

---

## Overview

This write-up documents the setup of a local Penetration Testing lab environment for MSSE 642. The goal was to create a minimal but functional pentest lab running on a local machine using virtualization. The lab consists of two virtual machines:

- **Kali Linux** – the attacker machine, used for penetration testing.
- **Metasploitable 2** – the intentionally vulnerable target machine used as the attack surface.

This environment mirrors industry-standard pen testing setups and will be used for upcoming assignments in Weeks 6 and 8.

---

## Technology Stack

| Component | Details |
|---|---|
| **Host Machine** | MacBook Pro (Apple Silicon – M-series) |
| **Host OS** | macOS Sequoia |
| **Hypervisor Type** | Type 2 – VMware Fusion (used due to limited VirtualBox support on Apple Silicon) |
| **Attacker VM** | Kali Linux 2024.x (ARM64) |
| **Target VM** | Metasploitable 2 (x86, via Rosetta / UTM emulation) |
| **VM Network Mode** | Host-Only Network (isolated lab network) |

> **Note on Hypervisor Choice:** VirtualBox support on Apple Silicon (M1/M2/M3) Macs has historically been limited or unstable. VMware Fusion (free for personal use) provides better native ARM support and was chosen for this lab. An alternative is [UTM](https://mac.getutm.app/), which is a free QEMU-based hypervisor fully compatible with Apple Silicon.

---

## Architectural Diagram

Below is the architectural diagram of the lab environment:

![Lab Architecture Diagram](images/lab-architecture-diagram.png)

> *Diagram created using [draw.io](https://draw.io). The diagram shows the host Mac running VMware Fusion with two isolated VMs connected via a Host-Only virtual network switch.*

**Lab Network Summary:**

```
┌─────────────────────────────────────────────┐
│           Host Machine (macOS)              │
│                                             │
│  ┌──────────────────┐  ┌─────────────────┐  │
│  │   Kali Linux     │  │ Metasploitable 2│  │
│  │  (Attacker VM)   │  │  (Target VM)    │  │
│  │  192.168.x.x     │◄─►│  192.168.x.x   │  │
│  └──────────────────┘  └─────────────────┘  │
│           │                   │             │
│           └───────┬───────────┘             │
│              Host-Only Network              │
│           (VMware vmnet1/Fusion)            │
└─────────────────────────────────────────────┘
```

---

## Virtualization Environment

VMware Fusion was downloaded and installed from the [VMware website](https://www.vmware.com/products/fusion.html). A personal-use license was registered for free.

**Screenshot – VMware Fusion running with both VMs:**

![VMware Fusion Running](images/vmware-fusion-running.png)

> *The screenshot above shows VMware Fusion with both the Kali Linux VM and Metasploitable 2 VM listed and powered on.*

---

## Kali Linux Setup

Kali Linux was downloaded from the [official Kali website](https://www.kali.org/get-kali/#kali-virtual-machines) as a pre-built VMware image. For Apple Silicon, the ARM64 version was selected.

**Steps taken:**
1. Downloaded Kali Linux VMware ARM64 image from `kali.org`.
2. Extracted the `.7z` archive using The Unarchiver.
3. Opened the `.vmx` file in VMware Fusion.
4. Booted the VM and logged in with default credentials (`kali` / `kali`).
5. Updated the system:
   ```bash
   sudo apt update && sudo apt upgrade -y
   ```
6. Changed the default password for security.

**Screenshot – Kali Linux logged in and running:**

![Kali Linux Running](images/kali-linux-running.png)

> *The screenshot shows the Kali Linux desktop environment with the terminal open, confirming a successful login.*

---

## Nessus Installation

Nessus Essentials (free tier) was installed on the Kali Linux VM for vulnerability scanning.

**Steps taken:**
1. Registered for a free Nessus Essentials activation code at [tenable.com/products/nessus/nessus-essentials](https://www.tenable.com/products/nessus/nessus-essentials).
2. Downloaded the Nessus `.deb` package for Debian/Kali (ARM64):
   ```bash
   curl --request GET \
     --url 'https://www.tenable.com/downloads/api/v2/pages/nessus' \
     -o Nessus-latest-debian10_aarch64.deb
   ```
3. Installed the package:
   ```bash
   sudo dpkg -i Nessus-latest-debian10_aarch64.deb
   ```
4. Started the Nessus service:
   ```bash
   sudo systemctl start nessusd
   sudo systemctl enable nessusd
   ```
5. Navigated to `https://localhost:8834` in the Kali browser to complete the setup wizard and enter the activation code.
6. Waited for the initial plugin compilation to complete (~15–30 minutes).

**Screenshot – Nessus running and accessible in browser:**

![Nessus Installed](images/nessus-installed.png)

> *The screenshot shows the Nessus Essentials web UI at `https://localhost:8834`, confirming successful installation and login.*

---

## Metasploitable 2 Setup

Metasploitable 2 is an intentionally vulnerable Linux VM designed as a penetration testing target (referenced on page 41 of the course textbook).

**Steps taken:**
1. Downloaded Metasploitable 2 from [SourceForge](https://sourceforge.net/projects/metasploitable/).
2. Extracted the `.zip` archive.
3. Opened the `.vmx` file in VMware Fusion.
   > *Note: Because Metasploitable 2 is x86-based and the host is Apple Silicon, UTM was used as an alternative with x86 emulation, or VMware Fusion's emulation mode was leveraged.*
4. Booted the VM; logged in with default credentials (`msfadmin` / `msfadmin`).
5. Noted the IP address assigned by the Host-Only network:
   ```bash
   ifconfig
   ```

**Screenshot – Metasploitable 2 running:**

![Metasploitable 2 Running](images/metasploitable2-running.png)

> *The screenshot shows the Metasploitable 2 terminal login prompt confirming that the VM is up and running.*

---

## Network Connectivity Test

To verify that the Kali Linux attacker machine can reach the Metasploitable 2 target, a `ping` test was performed from within the Kali Linux VM.

**Steps taken:**
1. Identified the Metasploitable 2 IP from its terminal (`ifconfig`).
2. From the Kali Linux terminal, ran:
   ```bash
   ping -c 4 <metasploitable-ip>
   ```
   *(Replace `<metasploitable-ip>` with the actual IP, e.g., `192.168.56.101`)*

**Screenshot – Ping from Kali to Metasploitable 2:**

![Ping Test](images/ping-kali-to-metasploitable.png)

> *The screenshot shows a successful `ping` from the Kali Linux VM to the Metasploitable 2 VM with 0% packet loss, confirming that the two VMs can communicate over the Host-Only network.*

---

## Problems & Solutions

| # | Problem | Solution |
|---|---------|----------|
| 1 | **VirtualBox not compatible with Apple Silicon** | Switched to VMware Fusion, which offers native ARM64 support and is free for personal use. |
| 2 | **Kali Linux ARM image vs. x86** | Downloaded the ARM64-specific Kali VMware image from the official Kali site to ensure compatibility with the M-series Mac. |
| 3 | **Metasploitable 2 is x86-only** | Used VMware Fusion's x86 emulation mode (or UTM with QEMU) to run the x86 VM on Apple Silicon. Performance was acceptable for lab use. |
| 4 | **Nessus plugin compilation time** | After installing Nessus, the initial plugin download and compilation took approximately 20–30 minutes. Waited for it to complete before attempting scans. |
| 5 | **VMs unable to ping each other initially** | Both VMs were set to "NAT" network mode by default. Changed both to "Host-Only" networking in VMware Fusion settings so they shared an isolated subnet. |

---

## References

- Kali Linux Official Site: [https://www.kali.org](https://www.kali.org)
- VMware Fusion (Free for Personal Use): [https://www.vmware.com/products/fusion.html](https://www.vmware.com/products/fusion.html)
- UTM for Mac: [https://mac.getutm.app](https://mac.getutm.app)
- Tenable Nessus Essentials: [https://www.tenable.com/products/nessus/nessus-essentials](https://www.tenable.com/products/nessus/nessus-essentials)
- Metasploitable 2 Download: [https://sourceforge.net/projects/metasploitable/](https://sourceforge.net/projects/metasploitable/)
- VirtualBox on Apple Silicon Article: [You Can Now Run VirtualBox on Apple Silicon M1/M2](https://www.virtualbox.org)


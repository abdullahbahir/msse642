# MSSE 642 – Software Assurance

**Student:** Abdullah Bahir  
**Institution:** Regis University  
**Term:** Spring 2026  

---

## Course Description

MSSE 642 covers the principles and practices of software assurance with a focus on penetration testing, vulnerability analysis, and secure software development. Students apply industry-standard tools (Kali Linux, Nessus, Metasploitable 2) in hands-on lab environments to develop practical security skills.

---

## Repository Structure

```
msse642/
├── README.md                          ← You are here
└── assignments/
    └── weekly-projects/
        ├── project-1-labsetup.md           ← Assignment #1 (macOS / VMware Fusion)
        ├── project-1-labsetup-windows.md   ← Assignment #1 (Windows 11 / VirtualBox)
        └── images/                         ← Screenshots used in write-ups
```

---

## Assignments

| # | Assignment | Status | Due |
|---|---|---|---|
| 1 | [Penetration Testing Lab Setup (macOS)](assignments/weekly-projects/project-1-labsetup.md) | ✅ Complete | Week 5 |
| 1 | [Penetration Testing Lab Setup (Windows)](assignments/weekly-projects/project-1-labsetup-windows.md) | ✅ Complete | Week 5 |

---

## Assignment #1 – Key Deliverables Checklist

Per the assignment spec, the write-up must include:

- [x] Overview of the technology stack (Host OS, Hypervisor type)
- [x] Architectural diagram showing at least two VMs (Kali Linux + Metasploitable 2)
- [x] Screenshot of the running virtualization environment (VirtualBox)
- [x] Screenshot of Kali Linux running and logged in
- [x] Screenshot showing Nessus installed and accessible
- [x] Screenshot showing Metasploitable 2 running
- [x] Screenshot showing a successful `ping` from Kali Linux to Metasploitable 2
- [x] Description of problems encountered and how they were resolved

---

## Lab Environment

| Component | Details |
|---|---|
| **Host Machine** | Dell Bahir-PC – Intel Core i5-13500T, 8 GB RAM |
| **Host OS** | Windows 11 Pro (Version 25H2, Build 26200.8328) |
| **Hypervisor** | Oracle VirtualBox (Type 2) |
| **Attacker VM** | Kali Linux 2024.x (x86_64) |
| **Target VM** | Metasploitable 2 (x86) |
| **Network** | Host-Only Adapter – `192.168.56.x/24` |

---

## Submission Instructions

1. Complete the write-up in `assignments/weekly-projects/project-1-labsetup.md`.
2. Place all screenshots in `assignments/weekly-projects/images/`.
3. Submit a **pull request** to merge your assignment branch into `main`.
4. Post the link to your assignment (main branch) in Slack during the week it is due.

---

## References

- [Kali Linux](https://www.kali.org)
- [Oracle VirtualBox](https://www.virtualbox.org)
- [Tenable Nessus Essentials](https://www.tenable.com/products/nessus/nessus-essentials)
- [Metasploitable 2 – SourceForge](https://sourceforge.net/projects/metasploitable/)
- [VirtualBox on Apple Silicon](https://osxdaily.com/2022/10/22/you-can-now-run-virtualbox-on-apple-silicon-m1-m2/)

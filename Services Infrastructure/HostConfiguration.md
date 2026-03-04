# HostConfiguration.md

## 1. Overview

This document describes the physical and logical host configuration for the Midleton site of electric-petrol.ie.

This is a Greenfield deployment. The site has:

- No internet connectivity
- No off-site replication
- No existing infrastructure
- Limited local IT expertise

The objective is to deliver:

- Authentication, Authorization and Accounting (AAA)
- High availability by default
- Operational simplicity
- Local resilience

The solution is based on a two-host private cloud using Microsoft Hyper-V.

---

## 2. Site Details

| Item | Value |
|------|-------|
| Site | Midleton |
| Population Served | ~7,957 |
| Domain | midleton.ads.electric-petrol.ie |
| Address Allocation | 10.50.0.0/16 |

---

## 3. Physical Host Design

Two identical physical servers are deployed.

### Hardware Specification (Per Host)

- Dual 8-core processors
- 512GB ECC Registered DRAM
- 2 × NVMe drives (RAID1) for host OS
- RAID6 storage array (minimum 10TB usable)
- 2 × 1Gb NIC (management)
- 2 × 10Gb NIC (VM traffic)
- Dual hot-swap power supplies
- Dual UPS units

### Justification

- ECC memory reduces risk of memory corruption.
- RAID6 protects against dual disk failure.
- NVMe RAID1 protects hypervisor boot volume.
- Dual NICs and PSUs eliminate single points of failure.
- Hardware lifecycle planned: 5 years.

---

## 4. Host Operating System

- Windows Server 2025 Datacenter
- Hyper-V role enabled
- Hosts joined to domain
- Hosts are NOT domain controllers

### Licensing Justification

Windows Server 2025 Datacenter provides unlimited virtualization rights per licensed host.  
Given multiple Windows Server virtual machines per host, Datacenter is cost-effective and operationally simpler than stacking Standard licenses.

---

## 5. Network and IP Design

Each VLAN is configured as /24.

### VLAN 2 – Hosts (10.50.2.0/24)

- Host1: 10.50.2.21
- Host2: 10.50.2.22
- Gateway (VRRP): 10.50.2.20

### VLAN 6 – Infrastructure Servers (10.50.6.0/24)

- DC1: 10.50.6.31
- DC2: 10.50.6.32
- Storage1: 10.50.6.41
- Storage2: 10.50.6.42
- DB1: 10.50.6.51
- DB2: 10.50.6.52

Static addressing is used for all infrastructure components.

---

## 6. High Availability Strategy

This design deliberately avoids:

- Failover clustering
- SAN infrastructure
- Azure integration

Reason:

Midleton is a regional site and does not justify enterprise clustering complexity. Increased complexity increases operational risk.

Resilience is achieved through:

- Two independent hosts
- Hyper-V Replica (5-minute replication interval)
- RAID6 local storage
- Dual domain controllers
- Dual power supplies

If one host fails, virtual machines are started on the remaining host.

---

## 7. Operations Model

- All configuration performed using PowerShell
- Gold images maintained for rebuild
- Monthly maintenance window defined
- Offline patch management
- All changes documented
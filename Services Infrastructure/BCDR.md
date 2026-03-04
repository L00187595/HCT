# BCDR.md

## 1. Overview

Midleton is a single-site regional deployment with no internet connectivity.

Business continuity must be achieved using local redundancy and layered backups.

---

## 2. Risk Assessment

Primary risks:

- Physical host failure
- RAID disk failure
- Power outage
- Accidental deletion
- Virtual machine corruption
- Localized physical damage

No off-site replication is available.

---

## 3. Backup Strategy

### Layer 1 – Hyper-V Replica

- Replication between Host1 and Host2
- 5-minute replication interval

Protects against single host failure.

---

### Layer 2 – VM-Level Backup

Windows Server Backup configured on dedicated backup VM.

Backup target:

VLAN 9 – Storage Network (10.50.9.0/24)

Daily incremental backups  
Weekly full backups  
30-day retention

Protects against:

- Data corruption
- Accidental deletion
- Malware impact

---

### Layer 3 – Offline Backup

Monthly export of critical virtual machines:

- Domain Controllers
- Database Servers

Exported to encrypted removable storage and stored in fire-resistant safe.

Protects against total infrastructure compromise.

---

## 4. Disaster Recovery Procedures

### Scenario 1 – Host Failure

Start replicated virtual machines on surviving host.

### Scenario 2 – VM Corruption

Restore affected VM from backup storage.

### Scenario 3 – Total Host Loss

Reinstall host operating system.  
Reconfigure Hyper-V.  
Import virtual machine exports.  
Rejoin domain.

---

## 5. Recovery Objectives

Recovery Time Objective (RTO): 4–8 hours  
Recovery Point Objective (RPO): <24 hours

These objectives are acceptable for a regional site serving approximately 7,957 residents.
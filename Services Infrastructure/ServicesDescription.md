# ServicesDescription.md

## 1. Overview

The Midleton site requires local core infrastructure services to support authentication, infrastructure control systems and internal operations.

The following services are deployed:

- Active Directory Domain Services
- DNS
- DHCP
- NTP

All services are internal only.

---

## 2. Active Directory

### Domain

midleton.ads.electric-petrol.ie

This is a regional child domain within ads.electric-petrol.ie.

### Domain Controllers

- DC1: 10.50.6.31
- DC2: 10.50.6.32

### Functions

- User authentication (Kerberos)
- Authorization via group membership
- Policy enforcement
- Service account management
- Logon auditing

### OU Structure

Midleton
- Servers
- Clients
- Infrastructure
- ServiceAccounts

No policies are applied at domain root level.

---

## 3. DNS

DNS is Active Directory integrated.

### Forward Lookup Zone

midleton.ads.electric-petrol.ie

### Reverse Lookup Zones

- 10.50.2.0/24
- 10.50.5.0/24
- 10.50.6.0/24

Both DC1 and DC2 host DNS.

No external forwarders are configured due to absence of internet connectivity.

---

## 4. DHCP

DHCP serves VLAN 5 – Technical Clients.

### Scope Configuration

Network: 10.50.5.0/24  
Range: 10.50.5.100 – 10.50.5.199  
Exclusion: 10.50.5.0 – 10.50.5.99

### Server Options

- DNS Domain: midleton.ads.electric-petrol.ie
- DNS Server: 10.50.6.31

DC2 has DHCP installed but does not actively issue leases unless required for failover.

---

## 5. NTP

Time synchronization follows domain hierarchy.

- DC1 holds the PDC Emulator FSMO role.
- All domain members synchronize from DC1.
- DC2 synchronizes from DC1.

Accurate time is critical for Kerberos authentication.
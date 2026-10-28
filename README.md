# CCNA-Enterprise-Routing-Lab

# Enterprise Network Forensics & Incident Response Lab

**Pure Layer 2 Switch Triage, MAC Tracking, and ARP Correlation**

## Lab Objectives

- Perform enterprise network reconnaissance using **Switch CLI access only** (zero routers).
    
- Map Layer 3 IP addresses to Layer 2 MAC addresses across a multi-switch topology (`Switch0` to `Switch5`).
    
- Execute forwarding table lookups (`show mac-address-table`) to trace endpoints through core and distribution switches to edge access ports.
    
- Resolve real-world SOC tickets involving performance degradation and security breaches.
    

## 1. Topology & Device Inventory

The network consists of:

- **Core / Distribution Switches:** `Switch0`, `Switch1`
    
- **Access Switches:** `Switch2`, `Switch3`, `Switch4`, `Switch5`
    
- **Servers:** `Server0` (`SRV1`), `Server1` (`SRV2`)
    
- **Client Workstations:** `PC0` through `PC7` connected across the access switches.
    

## 2. Incident Scenarios & Directives

### Incident #1: Performance Investigation (Ticket #INC-8821)

- **Target:** IP Address **`192.168.1.18`**
    
- **Issue:** User complaining of slow internet access and network lag.
    
- **Objective:** Using switch access only, locate this device's MAC address and trace it down to the exact switch and port level.
    

### Incident #2: Security Breach Response (Ticket #SEC-9042)

- **Target:** IP Address **`192.168.1.11`**
    
- **Issue:** Device identified as the source of a security breach / anomalous traffic.
    
- **Objective:** Locate this device in the network immediately so malware scans or isolation procedures can be executed.
    

## 3. Step-by-Step Investigation Walkthrough

### Step 1: Establish IP-to-MAC Mapping (ARP Triage)

Since there are no routers in this architecture, check the ARP table from an accessible core switch, management server, or switch with SVI routing enabled:

Cisco CLI

```
Switch0# show ip arp
```

- Locate **`192.168.1.18`** and note its MAC address (e.g., `aabb.cc11.2233`).
    
- Locate **`192.168.1.11`** and note its MAC address (e.g., `eeff.1122.3344`).
    

### Step 2: Tracing Ticket #INC-8821 (`192.168.1.18`)

1. Log into the core switches (`Switch0` or `Switch1`) and search the MAC address forwarding table:
    
    Cisco CLI
    
    ```
    Switch0# show mac-address-table | include aabb.cc11.2233
    ```
    
2. Follow the output interface (trunk/uplink port) pointing toward the edge access switches (`Switch2` through `Switch5`).
    
3. Log into the specific access switch where the MAC address is learned:
    
    Cisco CLI
    
    ```
    Switch2# show mac-address-table | include aabb.cc11.2233
    ```
    
4. **Result:** Identify the exact edge port (e.g., `FastEthernet0/5`) where the user's PC is connected, allowing you to inspect cabling, duplex mismatches, or switch port errors.
    

### Step 3: Tracing Ticket #SEC-9042 (`192.168.1.11`)

1. Query the core switches for the compromised MAC address (`eeff.1122.3344`):
    
    Cisco CLI
    
    ```
    Switch1# show mac-address-table | include eeff.1122.3344
    ```
    
2. Traverse the interconnect links to the corresponding edge access switch.
    
3. Confirm the exact user access port:
    
    Cisco CLI
    
    ```
    Switch5# show mac-address-table | include eeff.1122.3344
    ```
    
4. **Result:** Pinpoint the exact port (e.g., `FastEthernet0/4`) hosting the infected endpoint. (Optional: Execute `shutdown` on the interface to isolate the threat).
    

## 4. Verification Checklist

| **Task / Verification Item**                              | **Status**  |
| --------------------------------------------------------- | ----------- |
| Access core switch CLI (`Switch0` / `Switch1`)            | Pass / Fail |
| Resolve IP `192.168.1.18` to its MAC address              | Pass / Fail |
| Trace performance issue endpoint across multi-switch path | Pass / Fail |
| Identify exact switch and port for Ticket #INC-8821       | Pass / Fail |
| Resolve IP `192.168.1.11` to its MAC address              | Pass / Fail |
| Trace security breach endpoint to edge access switch      | Pass / Fail |
| Identify exact switch and port for Ticket #SEC-9042       | Pass / Fail |

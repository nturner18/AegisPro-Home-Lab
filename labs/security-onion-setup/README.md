# Lab — Security Onion Setup
## Network Security Monitoring Integration
### AegisPro CyberShield TX Home Lab

---

## Objective

Deploy Security Onion in evaluation mode as the lab's SIEM and network security monitoring platform. Configure the monitoring interface to capture all lab LAN traffic, validate that Zeek, Suricata, and the SO web interface are operational, and confirm traffic from lab VMs is visible before beginning attack and detect work.

---

## Environment

| Component | Details |
|-----------|---------|
| Security Onion version | 2.x (Evaluation mode) |
| RAM allocated | 24GB |
| Storage | D: drive (dedicated) |
| Management NIC | VMnet2 — lab LAN (10.0.1.0/24) — assigned IP |
| Monitoring NIC | VMnet2 — promiscuous mode — no IP assigned |
| Hypervisor | VMware Workstation Pro 25H2 |

---

## Why Two NICs

Security Onion requires two separate network interfaces:

| Interface | Role | IP | Purpose |
|-----------|------|----|---------|
| Management (ens32) | Administration | 10.0.1.x | Access SO web interface, receive updates |
| Monitoring (ens33) | Traffic capture | None | Promiscuous mode — passively captures all lab traffic |

The monitoring interface has no IP address — it listens silently on the network segment without participating in traffic. This mirrors how enterprise network taps and SPAN ports work in production environments.

---

## Step 1 — VM Configuration in VMware

Before powering on Security Onion, verify the VM settings:

1. Right-click Security Onion VM → **Settings**
2. **Network Adapter 1** (Management):
   - Connected to: **VMnet2 (Host-Only)**
   - This will receive a DHCP address from pfSense on the 10.0.1.0/24 subnet
3. **Network Adapter 2** (Monitoring):
   - Connected to: **VMnet2 (Host-Only)**
   - No IP assigned — promiscuous mode handles traffic capture
4. **Memory:** 24576 MB (24GB)
5. **Processors:** 4 cores minimum

> **Important:** In VMware, for the monitoring NIC to capture all traffic on VMnet2, promiscuous mode must be allowed on the virtual switch. This is enabled by default on VMware host-only networks.

---

## Step 2 — Security Onion Installation

Security Onion was installed from the official ISO:

1. Downloaded Security Onion ISO from `securityonion.net`
2. Created new VM in VMware targeting the ISO
3. Booted from ISO — selected **Install**
4. Selected **Evaluation** mode — appropriate for single-node lab
5. Completed installation and rebooted

---

## Step 3 — Initial Setup Wizard

After first boot, the SO setup wizard runs:

```
Setup type: EVAL
Management interface: ens32 (receives DHCP from pfSense)
Monitoring interface: ens33 (no IP — traffic capture only)
```

Key configuration choices during setup:

- **Hostname:** securityonion (or your preferred name)
- **Admin email:** your email address
- **Admin password:** set a strong password — document it securely
- **NTP:** enabled — syncs with internet time servers via pfSense

Setup takes approximately 15-20 minutes to complete as SO pulls container images and initializes all services.

---

## Step 4 — Accessing the Web Interface

After setup completes, access the SO analyst console from Kali:

```bash
# Get SO management IP
# Check from SO console:
ip a | grep "inet " | grep -v "127.0.0.1"

# From Kali browser
https://<SO-management-IP>
```

Accept the self-signed certificate warning and log in with the admin credentials set during setup.

![Security Onion web interface login](./images/so-web-login.png)

---

## Step 5 — Validate Services Are Running

From the SO console, verify all core services are operational:

```bash
sudo so-status
```

Key services to confirm running:

| Service | Purpose |
|---------|---------|
| **zeek** | Network traffic analysis — connection logs, DNS, HTTP, SSL |
| **suricata** | IDS/IPS — signature-based threat detection |
| **elasticsearch** | Log storage and indexing |
| **kibana** | Log visualization and dashboards |
| **strelka** | File analysis |
| **redis** | Message queue |

All services should show **running** status. If any show stopped, restart with:

```bash
sudo so-restart
```

![so-status output showing all services running](./images/so-status.png)

---

## Step 6 — Traffic Validation

Confirm Security Onion is capturing lab network traffic by generating some activity and checking SO dashboards.

**From Kali, generate test traffic:**

```bash
# Simple ping to DC01
ping -c 10 10.0.1.201

# Nmap scan of Metasploitable 2
nmap -sV 10.0.1.104

# HTTP request to DVWA
curl http://10.0.1.101
```

**Check SO is capturing in the web interface:**

1. Log into SO web interface
2. Navigate to **Dashboards**
3. Check **Network** dashboard — should show traffic from 10.0.1.x addresses
4. Navigate to **Hunt** → search for source IP `10.0.1.10` (Kali)
5. Zeek connection logs should appear showing the nmap scan and HTTP traffic

**Check from SO console:**

```bash
# Verify zeek is writing logs
ls -la /nsm/zeek/logs/current/

# Check conn.log for recent connections
sudo tail -f /nsm/zeek/logs/current/conn.log
```

You should see connection entries from your Kali IP within seconds of generating traffic.

![SO dashboard showing lab network traffic](./images/so-traffic-validation.png)

![Zeek conn.log showing Kali connections](./images/zeek-conn-log.png)

---

## Step 7 — pfSense Domain Override for SO

Add a static DHCP reservation for Security Onion in pfSense so it always gets the same management IP:

1. Find SO's MAC address: `ip a` on the SO console — copy MAC from ens32
2. **pfSense → Services → DHCP Server → LAN → Static Mappings → Add**
3. MAC: SO management NIC MAC
4. IP: choose a static address outside the DHCP pool (e.g., `10.0.1.202`)
5. Description: `Security Onion — Management`
6. Save and Apply

Then update SO's management IP if it changed:

```bash
sudo so-ip-update
```

---

## Observations & Troubleshooting Notes

**SO web interface slow to load:** Normal on first access — Kibana and Elasticsearch take 2-3 minutes to fully initialize after boot. Wait and refresh.

**Zeek not seeing traffic:** Verify the monitoring NIC (ens33) has no IP assigned and is on VMnet2. In VMware, confirm the virtual network adapter is set to VMnet2 and promiscuous mode is not restricted.

**so-status showing services stopped:** Run `sudo so-restart` and wait 3-5 minutes. On first boot after installation some services take time to initialize.

**High memory usage:** Security Onion with 24GB RAM will use the majority of that memory for Elasticsearch heap. This is expected — Elasticsearch is configured to use 50% of available RAM by default. Do not allocate less than 16GB or performance degrades significantly.

---

## CISSP Domain Alignment

| Domain | Relevance |
|--------|-----------|
| Domain 4 — Communication & Network Security | Network traffic capture, promiscuous mode monitoring, NSM architecture |
| Domain 7 — Security Operations | SIEM deployment, log aggregation, service validation, monitoring platform operation |

---

*Next: [Attack & Detect — Metasploitable](../attack-and-detect/metasploitable-detection/README.md)*

---

*AegisPro CyberShield TX — Security Onion Setup Lab — June 2026*
*Lead Assessor: Nicholas Turner, CISSP*

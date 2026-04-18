# Local-DNS-Spoofing-Lab

## 🛡️ Project Overview
This repository documents a controlled laboratory simulation of Man-in-the-Middle (MitM) attacks, specifically focusing on ARP poisoning and DNS spoofing. The objective of this project was to deploy, analyze, and understand the mechanics of traffic interception and redirection within a secure, virtualized local area network (LAN).

> **Disclaimer:** All activities documented in this repository were conducted in an isolated, authorized virtual lab environment. This project is strictly for educational purposes and to demonstrate penetration testing methodologies.

## 🛠️ Lab Environment & Architecture
The network was configured using Oracle VM VirtualBox with an isolated NAT Network to prevent leakage to external networks. 

* **Hypervisor:** VirtualBox
* **Network Type:** NAT Network (e.g., `192.168.50.0/24`)
* **Attacker Machine:** Parrot OS / Kali Linux (IP: `[192.168....]`)
* **Victim Machine:** Ubuntu / Windows 10 (IP: `[192.168....]`)
* **Gateway:** VirtualBox Virtual Router (IP: `192.168....`)

**Tools Utilized:**
* `Ettercap`: For automated ARP poisoning and DNS spoofing execution.
* `DNSChef`: For targeted DNS proxying and response manipulation.
* `Apache2`: To host the fraudulent payload page.

## 🚀 Execution & Methodology

### Phase 1: DNS Spoofing via Ettercap
Ettercap was utilized to seamlessly execute both the ARP MitM attack and the DNS record manipulation.

1. **Configuration:** Modified the `/etc/ettercap/etter.dns` zone file to redirect the target domain (`example.com`) to the Attacker's IP address.
2. **Execution:** Launched Ettercap in text mode, utilizing the `dns_spoof` plugin alongside a targeted ARP poison attack against the Victim and Gateway.
   ```bash
   sudo ettercap -T -q -P dns_spoof -M arp:remote /[Victim_IP]// /[Gateway_IP]//
### Phase 2: DNS Proxying via DNSChef
DNSChef was deployed to act as a highly configurable, rogue DNS server to intercept and manipulate specific A-record queries.

Execution: Configured DNSChef to bind to all interfaces and serve a fake IP for the target domain.

   ```bash
      sudo dnschef --fakeip [Attacker_IP] --fakedomains example.com --interface 0.0.0.0
   ```
Traffic Redirection: Routed the victim's DNS queries to the rogue DNSChef server.
   ```bash
sudo arpspoof -i eth0 -t <victim_ip> <gateway_ip>
sudo arpspoof -i eth0 -t <gateway_ip> <attacker_ip>
   ```
Open the DNS in the victim vm and you get the apache2 deafault page    

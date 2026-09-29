# Network Infrastructure & Threat Modeling - Rogue DHCP & Gateway Hijacking

## Overview
This repository documents foundational network engineering and threat modeling labs built in **Cisco Packet Tracer**. The project demonstrates dynamic network assignment in a standard star-bus topology versus an exploited network environment subjected to a **Rogue DHCP Server / Gateway Hijacking attack**.

---

## Topology Architectures

### 1. Baseline Topology (`Baseline_Star_DHCP.pkt`)
* **Infrastructure:** Layer 1 Hub / Switch topology linking client endpoints to a Legitimate DHCP Server (`192.168.1.15`).
* **Expected Behavior:** Endpoints issue a `DHCPDISCOVER` broadcast and successfully receive leased IPs within `192.168.1.0/24` with Default Gateway `192.168.1.1`.

### 2. Attack Surface Topology (`Rogue_DHCP_Attack.pkt`)
* **Infrastructure:** An unauthorized, malicious DHCP Server (`10.0.0.1`) is attached directly to the shared collision domain.
* **Exploit Mechanism:** Leverages Layer 1/2 broadcast flooding to trigger a race condition during client `DHCPDISCOVER` requests. When the Rogue Server responds faster with a `DHCPOFFER`, it overwrites the client's network parameters.
* **Impact:** The client receives IP `10.0.0.102` with Default Gateway set to `10.0.0.1`, enabling Man-in-the-Middle (MitM) traffic inspection or denial-of-service.

---

## Technical Proofs

### Baseline State (Legitimate Lease)
![Baseline Network Capture](screenshots/baseline_ipconfig.png)
*Client endpoint safely assigned `192.168.1.152` and Gateway `192.168.1.1`.*

### Hijacked State (Rogue Lease)
![Rogue Attack Capture](screenshots/rogue_hijack_ipconfig.png)
*Client endpoint hijacked with malicious IP `10.0.0.102` and Default Gateway `10.0.0.1`.*

---

## Security Takeaways & Mitigation
1. **Unmanaged Broadcast Domains:** Layer 1 hubs broadcast all frames across all ports indiscriminately, making race conditions trivial to exploit.
2. **Enterprise Mitigation:** Deploy Layer 2 managed switches and enable **DHCP Snooping**. Mark switch ports connected to authorized servers as *Trusted* and drop unauthorized `DHCPOFFER` frames on untrusted ports.
# 🛡️ Cloud-Native SOC & EDR Lab
**Author:** [@thatboringbro](https://x.com/thatboringbro)  
**Cloud Provider:** Oracle Cloud Infrastructure (OCI)  
**Core Stack:** Wazuh (SIEM/XDR), Ubuntu 22.04 LTS, Windows 10 Enterprise

---

## 📖 Project Overview
This project documents the deployment of a cloud-based Security Operations Center (SOC) and Endpoint Detection & Response (EDR) pipeline. By leveraging **Oracle Cloud (OCI)**, I shifted from local-only virtualization to a hybrid cloud architecture. The lab features a centralized Wazuh manager monitoring both local VirtualBox endpoints and cloud-hosted assets, providing a scalable foundation for future security operations and telemetry analysis.

---

## 🛠️ Lab Setup Progress

### Phase 1: Cloud SIEM Infrastructure
* **Host Provisioning:** Deployed an Ubuntu 22.04 LTS instance on OCI.
* **Wazuh Installation:** Executed the "All-in-One" deployment script to initialize the Wazuh Indexer, Server, and Dashboard.
* **Firewall Configuration:** * Managed OCI Security Lists for network-level access.
    * Hardened the host OS using `iptables` to specifically allow ingress traffic on **Port 443** (HTTPS) for secure dashboard access.
* **Validation:** Verified successful login to the Wazuh web portal via the public IP address.

> **Evidence:** See `downloading-wazuh-setup-script.png`, `installing-wazuh-all-in-one.png`, `vm-iptable-allow-ingress-traffic-port-443.png`, and `wazuh-web-login.png`.

### Phase 2: Endpoint Enrollment (The EDR Pipeline)
* **Hybrid Deployment:** Successfully enrolled a **Windows 10 Enterprise** workstation and an **Ubuntu MATE** server.
* **Agent Configuration:** Utilized PowerShell and Bash deployment strings to automate the handshake between the remote endpoints and the OCI manager.
* **Connectivity Management:** Addressed network handshake hurdles and service persistence to ensure a stable telemetry stream over the public internet.
* **Status Audit:** Confirmed "Active" status for all hybrid sensors within the Wazuh agent management console.

> **Evidence:** See `installing-wazuh-agent-on-local-vm.png`, `installing-wazuh-agent-for-windows-vm.jpg`, and `wazuh-with-agents-win-and-ubuntu.png`.

### Phase 3: Centralized Monitoring Policy
* **Group Management:** Leveraged Wazuh **Groups** to push a standardized security policy (`agent.conf`) to all endpoints from a single interface.
* **File Integrity Monitoring (FIM):** Configured real-time monitors for sensitive directories across both OS environments to detect unauthorized file access or content changes.

---

## ⚙️ Centralized Configuration (`agent.conf`)
I used the following configuration block within the Wazuh `default` group to establish the core monitoring baseline:

```xml
<agent_config>
  <syscheck>
    <frequency>43200</frequency>

    <directories check_all="yes" report_changes="yes" realtime="yes">/etc/finance_records</directories>

    <directories check_all="yes" report_changes="yes" realtime="yes">C:\Users\redacted\Documents\Secrets</directories>
  </syscheck>
</agent_config>

# PalmTreeDev Local Domains (.ptd)

An enterprise-grade, lightweight local domain provisioning utility engineered explicitly for **openSUSE Tumbleweed**. This software optimizes local infrastructure workflows by automating the routing and hosting of a dedicated local-only Top-Level Domain (**`.ptd`** / PalmTreeDev domain).

Unlike heavy, resource-intensive containerized abstractions or complex orchestration frameworks, this utility interfaces directly with native Linux networking stacks and standard configuration daemons to maximize performance and efficiency.

---

## 🚀 Overview & Development Philosophy

This project was engineered independently by a **Solo Software and Web Developer** operating from a private workstation. In standalone environments, system overhead directly impacts iteration speed. Traditional localized environments frequently introduce unnecessary daemon layers, container networks, and interface management components that consume valuable system resources.

**PalmTreeDev Local Domains** addresses this constraint by delivering an immediate, zero-overhead routing framework tailored specifically for a rolling-release ecosystem (**openSUSE Tumbleweed**). The architecture enforces a zero-telemetry, open-source footprint to maintain absolute environment control and system purity.

---

## ✨ System Features

* **Wildcard Local Resolution:** Dynamically intercepts and resolves all `*.ptd` address queries (e.g., `application.ptd`, `service.ptd`) directly to the local loopback interface (`127.0.0.1`).
* **Dynamic Directory-Based Virtual Hosting:** Evaluates directories created within the deployment path (e.g., `/var/www/ptd/workspace/`) and maps them automatically to the corresponding address (`[http://workspace.ptd](http://workspace.ptd)`) without requiring service restarts or manual configurations.
* **Resource-Optimized Architecture:** Utilizes lightweight, production-grade local resolution mechanisms (such as `dnsmasq` or structured `systemd-resolved` configurations) to ensure a minimal RAM and CPU profile.
* **Unified Command-Line Interface:** Provides a streamlined CLI tool for initializing environments, inventorying configurations, and executing clean system operations.
* **Native openSUSE Tumbleweed Integration:** Designed with system parameters, configuration structures, and system manager patterns native to openSUSE.

---

## 🛠️ Installation & Configuration

Ensure the system is running an active instance of **openSUSE Tumbleweed** and that the executing shell possesses elevated administrative (`sudo`) privileges.

### 1. Source Retrieval

Clone the repository from the official distribution point:

```bash
git clone https://github.com/PalmtreeDev-real/PTD-domain.git
cd PTD-domain

```

### 2. Initialization Execution

Execute the automated initialization script. This process verifies system dependencies, establishes the loopback parameters, and updates configuration directives:

```bash
sudo ./install.sh

```

### 3. Network Validation

Verify that the network configuration layer correctly traps queries bound for the `.ptd` domain:

```bash
ping network-check.ptd

```

*Expected output must indicate ICMP replies resolving explicitly to loopback destination `127.0.0.1`.*

---

## 💻 Operational Workflow & Interface Examples

Following deployment, local services can be instantiated programmatically via filesystem manipulation or the integrated command-line tool.

### Automatic Directory Mapping

1. **Provision a new project directory adhering to the target hostname:**
```bash

```



mkdir -p /var/www/ptd/api-service

```
2. **Populate the root directory with target application files:**
   ```bash
echo "<h1>Environment Verified: PalmTreeDev Local Infrastructure</h1>" > /var/www/ptd/api-service/index.html

```

3. **Access the endpoint:** direct any local HTTP client to `[http://api-service.ptd](http://api-service.ptd)` for instantaneous processing.

### Command Line Reference

The utility features a standalone binary `ptd` to manage local endpoints efficiently:

* **Provision a project environment instantly:**
```bash

```



ptd add api-service

```
* **Enumerate active domain allocations:**
  ```bash
ptd list

```

* **Execute an absolute environment teardown and restore host configurations:**
```bash

```



sudo ptd remove --all

```

---

## ⚖️ Regulatory Compliance

This utility is compiled, maintained, and published within the jurisdiction of the **Kingdom of Bahrain**. The architecture conforms fully with regional electronic and data management legislation:

- **Decree-Law No. (54) of 2018 (Electronic Communications and Transactions Law):** This application behaves entirely as a localized automated automation utility for individual development workflows and does not manipulate, proxy, or retain remote electronic transactions or communications.
- **Law No. (30) of 2018 (Personal Data Protection Law - PDPL):** **Absolute Data Isolation.** All network traffic interception, domain lookup manipulation, and filesystem mapping occur fully offline within the boundary of the host machine’s loopback space. This software does not collect, log, aggregate, or transmit telemetry or personal identifier metrics to external networks or infrastructure.
- **Decree-Law No. (60) of 2014 (Cybercrime Frameworks):** Engineered exclusively for internal software engineering and testing paradigms, this utility operates strictly within authorized user space and contains no operational mechanics designed for unauthorized network intrusion, protocol sniffing, or illicit security controls bypass.

End-users maintain absolute liability for ensuring that the application architectures and content served locally across the `.ptd` environment remain in compliance with public safety, decency, and operational standards defined under Bahraini Jurisdiction.

---

## 📄 License

This project is open-source software licensed strictly under the terms of the **Apache License, Version 2.0** (the "License").

```text
Copyright 2026 PalmTreeDev

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.

```

For comprehensive terms governing distribution and liability limitations, review the full text within the accompanying `LICENSE` document in this repository.

# HashiCorp Vault Installation and Troubleshooting Guide

Welcome to the guide on installing and troubleshooting **HashiCorp Vault**. This guide covers the installation of Vault on a Linux server, configuration steps, and how to troubleshoot common issues encountered during the process.

## Table of Contents
- [Introduction](#introduction)
- [Prerequisites](#prerequisites)
- [Installation Steps](#installation-steps)
  - [1. Update System Packages](#1-update-system-packages)
  - [2. Install Vault](#2-install-vault)
  - [3. Configure Vault](#3-configure-vault)
  - [4. Start and Enable Vault](#4-start-and-enable-vault)
  - [5. Verify Installation](#5-verify-installation)
- [Common Errors and Solutions](#common-errors-and-solutions)
  - [1. Vault service not starting](#1-vault-service-not-starting)
  - [2. TLS Handshake Errors](#2-tls-handshake-errors)
  - [3. HTTP request sent to an HTTPS server](#3-http-request-sent-to-an-https-server)
  - [4. Vault initialization issues](#4-vault-initialization-issues)
- [Conclusion](#conclusion)
- [Contribute](#contribute)

---

## Introduction

**HashiCorp Vault** is a tool for securely accessing secrets such as tokens, passwords, certificates, and API keys. Vault is commonly used in DevOps environments to manage and store secrets, providing both security and ease of access. This guide will walk you through the installation process of Vault and solutions to common errors.

---

## Prerequisites

Before installing Vault, make sure you have the following:

- A Linux server (Ubuntu preferred).
- Root or sudo access to install packages.
- Internet connection to download packages.

---

## Installation Steps

### 1. Update System Packages

Ensure your system packages are up to date:

```bash
sudo apt update && sudo apt upgrade -y
```

###  2. Install Vault
Add HashiCorp's official repository to install the Vault package:

```bash

sudo apt install -y apt-transport-https
curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
sudo apt update && sudo apt install vault
```

### 3. Configure Vault
After installation, you need to configure Vault. Create a configuration file for Vault:

```bash
sudo nano /etc/vault.d/vault.hcl
```
Add the following content to the vault.hcl file:

```hcl
# Enable the default storage backend (File Storage)
storage "file" {
  path = "/opt/vault/data"
}

# Enable the default listener (TCP listener)
listener "tcp" {
  address = "0.0.0.0:8200"
  tls_disable = 1
}

# Enable the default API address (optional, for HTTP-based access)
api_addr = "http://127.0.0.1:8200"
```
This configuration enables the file-based storage backend and disables TLS for simplicity. If you plan to use Vault in a production environment, consider enabling TLS for secure communication.

### 4. Start and Enable Vault
Once you have configured Vault, start the Vault service and enable it to start on boot:

```bash
sudo systemctl start vault
sudo systemctl enable vault
```

### 5. Verify Installation
You can check the status of the Vault service to ensure that it is running correctly:

```bash
vault status
```
If Vault is running, you'll see output similar to:

```yaml

Sealed:      true
Key Shares:  1
Threshold:   1
Unseal Progress: 0
```

# Common Errors and Solutions

### 1. Vault service not starting
If the Vault service fails to start, check the system logs for more information:

```bash

sudo journalctl -u vault -f
```

### Solution:
Verify the vault.hcl configuration file is correct and all required paths (e.g., /opt/vault/data) exist.
Ensure the vault user has appropriate permissions to access the Vault data directory.
### 2. TLS Handshake Errors
If you see errors like:

```makefile
tls: bad certificate
```
It may be because Vault is configured to use TLS but the listener is not set up for it.

### Solution:
In the vault.hcl file, ensure tls_disable = 1 is set to disable TLS.
### 3. HTTP request sent to an HTTPS server
If you encounter this error:

```
Client sent an HTTP request to an HTTPS server
```
This usually happens when Vault is listening for HTTP requests but you are using the HTTPS protocol in your environment variable.

### Solution:
Make sure you're using http://127.0.0.1:8200 as the Vault address:

``` 
export VAULT_ADDR="http://127.0.0.1:8200"

```

### 4. Vault initialization issues
If Vault fails to initialize and gives errors like:

```
Error making API request
Ensure Vault is correctly initialized before proceeding with other operations.
```

### Solution:
To reset Vault, you can clear its data directory:

```bash
sudo rm -rf /opt/vault/data
```

Restart the Vault service:
```bash
sudo systemctl restart vault
```
Reinitialize Vault with the vault operator init command:

```
vault operator init
```
### Conclusion
In this guide, we covered the steps to install HashiCorp Vault, configure it for local usage, and address common installation and initialization issues. Vault is an essential tool for managing secrets securely, and proper installation and troubleshooting are key to leveraging its capabilities.



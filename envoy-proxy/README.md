# Envoy Proxy Ansible Role Overview

## Overview

This Ansible role installs, configures, and manages the Envoy Proxy across multiple operating systems including Debian/Ubuntu, Red Hat Enterprise Linux (RHEL), Amazon Linux, and macOS.

The role provides a complete, automated, and production-ready setup of Envoy using either the official package repository (Debian-based systems) or the official Envoy binary (RedHat-based systems). It also dynamically generates Envoy configuration with support for load balancing, health checks, and TLS.

This role is designed to be idempotent, portable, and safe for repeated execution.

---

## Key Features

* Automatic Envoy installation (APT or binary)
* Supports Debian, Ubuntu, RHEL, Amazon Linux, and macOS
* Dynamic backend cluster configuration
* Built-in load balancing (ROUND_ROBIN)
* Health check configuration
* TLS support (optional)
* Automatic systemd service creation and management
* Configuration validation before restart
* Fully idempotent and production-ready

---

## Supported Operating Systems

| OS Family             | Installation Method           |
| --------------------- | ----------------------------- |
| Debian / Ubuntu       | Official Envoy APT repository |
| RedHat / Amazon Linux | Official Envoy binary         |
| macOS                 | Homebrew                      |

---

## Role Directory Structure

```
roles/envoyproxy/
├── defaults/main.yml        # Default variables
├── handlers/main.yml        # Service handlers
├── tasks/
│   ├── install-Debian.yml
│   ├── install-RedHat.yml
│   ├── install-MacOS.yml
│   ├── tls.yml
│   └── main.yml
├── templates/
│   ├── envoy.yaml.j2
│   ├── envoy.service.j2
│   └── envoy.list.j2
├── vars/main.yml
└── meta/main.yml
```

---

## Installation Paths

| Component     | Path                                |
| ------------- | ----------------------------------- |
| Envoy binary  | `/usr/local/bin/envoy` (RedHat)     |
| Envoy config  | `/etc/envoy/envoy.yaml`             |
| TLS directory | `/etc/envoy/tls/`                   |
| Service file  | `/etc/systemd/system/envoy.service` |

---

## Default Configuration

Default values are defined in:

```
roles/envoyproxy/defaults/main.yml
```

Important variables:

```
envoy_version: "1.33.0"
envoy_binary_path: "/usr/local/bin/envoy"
envoy_config_path: "/etc/envoy/envoy.yaml"
envoy_admin_port: 9901

envoy_http_port: 80
envoy_https_port: 443

envoy_tls_enabled: true
envoy_tls_cert_file: "/etc/envoy/tls/tls.crt"
envoy_tls_key_file: "/etc/envoy/tls/tls.key"
```

---

## Backend Cluster Configuration

Clusters are defined dynamically:

```
envoy_clusters:
  - name: backend_cluster
    connect_timeout: 5s
    lb_policy: ROUND_ROBIN
    health_check_path: /health

    endpoints:
      - ip: 127.0.0.1
        port: 8080
```

Multiple backend servers can be added easily.

---

## Example Inventory

```
[envoy_servers]

envoy-node-1 ansible_host=13.222.160.18 ansible_user=ubuntu ansible_ssh_private_key_file=~/test.pem

envoy-node-2 ansible_host=13.218.115.171 ansible_user=ec2-user ansible_ssh_private_key_file=~/test.pem
```

---

## Example Playbook

```
- hosts: envoy_servers
  become: yes

  roles:
    - envoyproxy
```

Run:

```
ansible-playbook -i inventory.ini playbook.yml
```

---

## Service Management

Control Envoy using systemd:

```
sudo systemctl start envoy
sudo systemctl stop envoy
sudo systemctl restart envoy
sudo systemctl status envoy
```

Enable on boot:

```
sudo systemctl enable envoy
```

---

## Configuration Validation

Before restarting Envoy, configuration is validated automatically:

```
envoy --mode validate -c /etc/envoy/envoy.yaml
```

This prevents invalid configurations from breaking the service.

---

## Verification

Check Envoy version:

```
envoy --version
```

Check listening ports:

```
ss -tulnp | grep envoy
```

Test proxy:

```
curl http://SERVER_IP
```

---

## TLS Support

TLS can be enabled by setting:

```
envoy_tls_enabled: true
```

Place certificates in:

```
/etc/envoy/tls/tls.crt
/etc/envoy/tls/tls.key
```

---

## Load Balancing Support

Supports:

* ROUND_ROBIN
* Multiple backend endpoints
* Health checking

---

## Idempotency

This role is fully idempotent. Running it multiple times will not cause duplicate installations or unnecessary restarts.

---

## Author

Abhishek Saini 

---





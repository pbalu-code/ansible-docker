Docker Ansible Role
=========

This role installs Docker and Docker Compose, and optionally applies CIS Docker Benchmark hardening to the Docker daemon.

* Ubuntu  
* Debian  

Requirements
------------

ansible 2.10+ - Recommended >= 9.0  


molecule / vagrant
------------
Vagrant:  
https://computingforgeeks.com/install-latest-vagrant-on-ubuntu/  

Molecule:  
https://philnewm.github.io/molecule-start/  


apt:  
- python3-dev  
- libvirt-daemon-system  
- libvirt-clients  
- libvirt-dev  
- pkg-config  

pip:  
- python-vagrant  
- testinfra  
- libvirt-python  
- molecule  
- molecule-plugins  
- rich  

vagrant:  
- vagrant plugin install vagrant-libvirt  

Role Variables
--------------

### Installation Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `DOCKER_REQ_PACKAGES` | `[ca-certificates, curl, gnupg, lsb-release, python3-docker]` | Required packages before Docker install |
| `DOCKER_KEYRING` | `/etc/apt/keyrings/docker.asc` | Path to Docker GPG keyring |
| `DOCKER_PACKAGES` | `[docker-ce, docker-ce-cli, containerd.io, docker-buildx-plugin, docker-compose-plugin]` | Docker packages to install |

### Hardening Variables
https://docs.docker.com/dhi/core-concepts/cis/  

| Variable | Default | CIS | Description |
|----------|---------|-----|-------------|
| `DOCKER_do_hardening` | `false` | - | Enable Docker daemon hardening |
| `DOCKER_auditd_conf_path` | `/etc/audisp` | - | Path to auditd config directory |

#### CIS 2.1 - Non-root Daemon (Rootless Mode)

| Variable | Default | Description |
|----------|---------|-------------|
| `DOCKER_rootless_enabled` | `false` | Enable rootless Docker daemon |
| `DOCKER_rootless_user` | `dockeruser` | User to run rootless Docker as |

#### CIS 2.2 - Container Network Traffic

| Variable | Default | Description |
|----------|---------|-------------|
| `DOCKER_icc_enabled` | `false` | Allow inter-container communication on default bridge. Set to `false` to restrict |

#### CIS 2.3 - Logging Level

| Variable | Default | Description |
|----------|---------|-------------|
| `DOCKER_log_level` | `info` | Docker daemon log level: `debug`, `info`, `warn`, `error`, `fatal` |

#### CIS 2.4 - iptables

| Variable | Default | Description |
|----------|---------|-------------|
| `DOCKER_iptables_enabled` | `true` | Allow Docker to manage iptables rules |

#### CIS 2.5 - Insecure Registries

| Variable | Default | Description |
|----------|---------|-------------|
| `DOCKER_insecure_registries` | `[]` | List of insecure registries. Should be empty in production |

#### CIS 2.6 - Storage Driver

| Variable | Default | Description |
|----------|---------|-------------|
| `DOCKER_storage_driver` | `overlay2` | Docker storage driver. Never use `aufs` |

#### CIS 2.7 - TLS Authentication

| Variable | Default | Description |
|----------|---------|-------------|
| `DOCKER_tls_enabled` | `true` | Enable TLS for Docker daemon |
| `DOCKER_tls_verify` | `true` | Verify TLS certificates |
| `DOCKER_tls_cacert` | `/etc/docker/certs/ca.pem` | Path to CA certificate |
| `DOCKER_tls_cert` | `/etc/docker/certs/server-cert.pem` | Path to server certificate |
| `DOCKER_tls_key` | `/etc/docker/certs/server-key.pem` | Path to server private key |

#### CIS 2.8 - Default ulimits

| Variable | Default | Description |
|----------|---------|-------------|
| `DOCKER_default_ulimits.nofile.soft` | `1024` | Soft limit for open file descriptors |
| `DOCKER_default_ulimits.nofile.hard` | `65536` | Hard limit for open file descriptors |

#### CIS 2.9 - User Namespace Support

| Variable | Default | Description |
|----------|---------|-------------|
| `DOCKER_userns_remap` | `default` | User namespace remapping. Use `default` to enable |

#### CIS 2.11 - Default cgroup

| Variable | Default | Description |
|----------|---------|-------------|
| `DOCKER_cgroup_driver` | `systemd` | cgroup driver: `systemd` or `cgroupfs` |

#### CIS 2.12 - Base Device Size

| Variable | Default | Description |
|----------|---------|-------------|
| `DOCKER_base_device_size` | `null` | Base device size for devicemapper. Leave `null` unless required |

#### CIS 2.13 - Authorization Plugins

| Variable | Default | Description |
|----------|---------|-------------|
| `DOCKER_authorization_plugins` | `[]` | List of Docker authorization plugins |

#### CIS 2.14 - Centralized Logging

| Variable | Default | Description |
|----------|---------|-------------|
| `DOCKER_log_driver` | `journald` | Docker log driver: `journald`, `syslog`, `json-file`, `gelf`, etc. |
| `DOCKER_log_opts` | `{}` | Log driver options as key/value map |

#### CIS 2.17 - No New Privileges

| Variable | Default | Description |
|----------|---------|-------------|
| `DOCKER_no_new_privileges` | `true` | Prevent containers from acquiring new privileges |

#### CIS 2.18 - Live Restore

| Variable | Default | Description |
|----------|---------|-------------|
| `DOCKER_live_restore` | `true` | Keep containers running when Docker daemon stops/restarts |

#### CIS 2.19 - Userland Proxy

| Variable | Default | Description |
|----------|---------|-------------|
| `DOCKER_userland_proxy` | `false` | Disable userland proxy for better performance and security |

#### CIS 2.20 - Seccomp Profile

| Variable | Default | Description |
|----------|---------|-------------|
| `DOCKER_seccomp_profile` | `null` | Path to custom seccomp profile. `null` uses Docker default |

#### CIS 2.21 - Experimental Features

| Variable | Default | Description |
|----------|---------|-------------|
| `DOCKER_experimental` | `false` | Enable experimental Docker features. Must be `false` in production |

---

### Req 7 - Configuration File Permissions

These tasks are automatically applied when `DOCKER_do_hardening: true`.  
No additional variables are required — paths are auto-detected or use the TLS variables defined above.

| File / Directory | Owner | Group | Permissions | Notes |
|------------------|-------|-------|-------------|-------|
| `docker.service` | `root` | `root` | `0644` | Path auto-detected via `systemctl` |
| `docker.socket` | `root` | `root` | `0644` | Path auto-detected via `systemctl` |
| `/etc/docker/` | `root` | `root` | `0755` | Config directory |
| `/etc/docker/certs.d/**/*.crt` | `root` | `root` | `0444` | Registry certificates |
| `DOCKER_tls_cacert` | `root` | `root` | `0444` | TLS CA certificate |
| `DOCKER_tls_cert` | `root` | `root` | `0444` | TLS server certificate |
| `DOCKER_tls_key` | `root` | `root` | `0400` | TLS private key - most restrictive |
| `/var/run/docker.sock` | `root` | `docker` | `0660` | Docker Unix socket |
| `/etc/docker/daemon.json` | `root` | `root` | `0644` | Daemon configuration |
| `/etc/default/docker` | `root` | `root` | `0644` | Only applied if file exists |
| `/etc/sysconfig/docker` | `root` | `root` | `0644` | Only applied if file exists (RHEL/CentOS) |
| `/run/containerd/containerd.sock` | `root` | `root` | `0660` | Only applied if file exists |

---

Dependencies
------------

None.

Example Playbook
----------------

### Basic Docker Installation

```yaml
- name: Deploy docker
  hosts: all
  gather_facts: true
  become: true
  roles:
    - name: ansible-docker
```

### With Hardening Enabled

```yaml
- name: Deploy docker with hardening
  hosts: all
  gather_facts: true
  become: true
  roles:
    - name: ansible-docker
  vars:
    DOCKER_do_hardening: true
    DOCKER_icc_enabled: false
    DOCKER_log_level: info
    DOCKER_tls_enabled: true
    DOCKER_tls_verify: true
    DOCKER_tls_cacert: /etc/docker/certs/ca.pem
    DOCKER_tls_cert: /etc/docker/certs/server-cert.pem
    DOCKER_tls_key: /etc/docker/certs/server-key.pem
    DOCKER_live_restore: true
    DOCKER_userland_proxy: false
    DOCKER_no_new_privileges: true
    DOCKER_experimental: false
    DOCKER_log_driver: journald
    DOCKER_userns_remap: default
```

### Run only hardening tasks

```yaml
- name: Harden existing Docker installation
  hosts: all
  gather_facts: true
  become: true
  tasks:
    - name: Apply Docker hardening
      ansible.builtin.include_role:
        name: ansible-docker
        tasks_from: hardening
      vars:
        DOCKER_do_hardening: true
```

License
-------

MIT

Author Information
------------------

Balázs Petik
DevOps Engineer


# ansible-playbooks

Ansible playbooks for familieroethof.nl and hvld.nl infrastructure.

## Structure

```
ansible-playbooks/
├── ansible.cfg
├── collections/
│   └── requirements.yml
├── inventory/
│   └── hosts.yml
└── playbooks/
    ├── ping.yml
    └── os-update.yml
```

## Inventory

| Host | IP | Type | Group |
|------|----|------|-------|
| hv001.familieroethof.nl | 192.168.2.10 | Proxmox node | proxmox_nodes |
| proxy.familieroethof.nl | 192.168.2.11 | LXC | proxmox_cts, homelab |
| start.familieroethof.nl | 192.168.2.12 | LXC | proxmox_cts, homelab |
| start.hvld.nl | 192.168.2.13 | LXC | proxmox_cts, hvld |
| meshcore.familieroethof.nl | 192.168.2.15 | LXC | proxmox_cts, meshcore |
| sovol.familieroethof.nl | 192.168.2.16 | VM | proxmox_vms, printing |
| semaphore.familieroethof.nl | 192.168.2.17 | LXC | proxmox_cts, homelab |
| home.familieroethof.nl | 192.168.2.14 | VM (HAOS) | unmanaged |

## Playbooks

### ping.yml
Connectivity check against all managed hosts.

```bash
ansible-playbook playbooks/ping.yml
```

### os-update.yml
Full OS update across all managed hosts. Supports Debian, RedHat/Rocky, Arch, and FreeBSD.
Reboots automatically when required:
- Debian: only if `/var/run/reboot-required` exists
- Rocky: only if `needs-restarting -r` returns exit code 1
- Arch: always (rolling release)
- FreeBSD: always after pkg upgrade

```bash
ansible-playbook playbooks/os-update.yml
```

## Semaphore

SSH key is injected via Semaphore credential store. No key paths are hardcoded in the inventory.

Templates:
| Template | Playbook | Schedule |
|----------|----------|----------|
| Ping | playbooks/ping.yml | on demand |
| OS Update | playbooks/os-update.yml | weekly |
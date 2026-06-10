# elise.quadlet

Generic Podman Quadlet deployment collection for RHEL image-mode (bootc) and standalone VM environments.

## Overview

Operation-named roles driven entirely by a `containers` list data model. No service names exist inside the collection — all service-specific knowledge lives in the calling config-as-code repository.

**Long-term home:** `elise.quadlet` → graduates to `rhis.quadlet`

## Roles

| Role | Purpose |
| --- | --- |
| `deploy` | Install prerequisites, pull images (pull mode), render Quadlet unit files and EnvironmentFile |
| `start` | Health-gated tier startup (pull mode only) |
| `yield` | `systemctl disable --now` — Quadlet units and volumes preserved |
| `reclaim` | `systemctl enable --now` — restore from preserved state |
| `validate` | Inline container health check (pull mode) or deferred stub (embedded mode) |

## Deployment Modes

| Mode | Image source | When to use |
| --- | --- | --- |
| `pull` | Registry pull at deploy time | Standalone VM, connected environment |
| `embedded` | Physically-bound via `dcm-image-load.service` | bootc first-boot, disconnected/air-gapped |

## Usage

```yaml
- name: Deploy service stack
  hosts: all
  become: true
  gather_facts: true
  vars_files:
    - vars/myservice.yml
  tasks:
    - name: Deploy Quadlet units
      ansible.builtin.include_role:
        name: elise.quadlet.deploy

    - name: Start services
      ansible.builtin.include_role:
        name: elise.quadlet.start
      when: elise_quadlet_service_mode == 'pull'

    - name: Validate deployment
      ansible.builtin.include_role:
        name: elise.quadlet.validate
```

## Data Model

The `elise_quadlet_containers` list drives all role behavior. Each entry describes one container unit:

```yaml
elise_quadlet_containers:
  - name: myservice-db
    image: registry.example.com/myservice/postgres:16
    start_order: 1
    description: "PostgreSQL database for myservice"
    network: myservice-network
    volumes:
      - myservice-pgdata.volume:/var/lib/pgsql/data:Z
    env_file: /etc/containers/systemd/myservice.env
    health:
      cmd: pg_isready -U myservice -d myservice
      interval: 10s
      retries: 5
      timeout: 5s

  - name: myservice-app
    image: registry.example.com/myservice/app:latest
    start_order: 2
    description: "myservice application"
    pod: myservice
    network: myservice-network
    volumes:
      - /srv/myservice/config:/conf/stack:Z
    health:
      cmd: curl -sf http://localhost:8080/health
      interval: 30s
      retries: 10
      timeout: 10s
```

## Requirements

- Ansible >= 2.16.0
- `containers.podman` collection
- `ansible.posix` collection

## License

Apache-2.0

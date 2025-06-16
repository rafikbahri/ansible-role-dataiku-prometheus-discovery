# Ansible Role: Dataiku Prometheus Service Discovery

An Ansible role that deploys a service discovery mechanism for Prometheus to monitor Dataiku Fleet Manager instances.

## Overview

This role sets up a custom HTTP service that exposes Dataiku Fleet Manager instances as targets for Prometheus monitoring. It works by querying the Dataiku Fleet Manager API to discover all managed instances and their status, then provides this information in a Prometheus-compatible format.

## Requirements

- Ansible 2.9 or higher
- Existing Dataiku Fleet Manager installation
- Python 3.x on the target host
- Valid Fleet Manager API key

## Role Variables

### Required Variables

These variables must be defined in your playbook or inventory:

```yaml
# Fleet Manager API credentials
fm_api_key_name: "your-api-key-name"
fm_api_key_secret: "your-api-key-secret"
```

### Default Variables

These variables have default values but can be overridden:

```yaml
# Service configuration
prometheus_discovery_port: 10100
prometheus_discovery_user: dataiku
prometheus_discovery_group: dataiku
prometheus_discovery_home: /data/dataiku/fmhome

# API configuration
fleet_manager_api_base_url: http://localhost:10000
refresh_interval_seconds: 30

# Service management
prometheus_discovery_service_enabled: true
prometheus_discovery_service_state: started
```

## Dependencies

This role has no external dependencies.

## Example Playbook

```yaml
- hosts: fleet_manager_hosts
  become: yes
  vars:
    fm_api_key_name: "your-api-key-name"
    fm_api_key_secret: "your-api-key-secret"
    fleet_manager_api_base_url: "http://fleet-manager.example.com:10000"
  roles:
    - ansible-role-dataiku-prometheus-discovery
```

## How It Works

The service exposes Fleet Manager instances as Prometheus targets at:
`http://<host>:10100/prometheus/targets`

## Prometheus Configuration

Add the following to your Prometheus configuration:

```yaml
scrape_configs:
  - job_name: 'dataiku_fleet_manager'
    scrape_interval: 60s
    http_sd_configs:
      - url: http://fleet-manager-host:10100/prometheus/targets
```

## License

MIT

## Author

Created by Rafik Bahri

# ACME Network Baseline Collection

[![Ansible Collection](https://img.shields.io/badge/collection-acme.network__base-blue)](https://github.com/Automation-Portal-ACME/acme-network-base)

An Ansible collection maintained by the **ACME Corp Network Engineering Team** for deploying and enforcing standard network configurations across Cisco IOS/NX-OS, Arista EOS, and Juniper JunOS devices. These roles codify ACME's networking standards so every switch and router ships with a consistent, auditable baseline.

## Requirements

- Ansible Core >= 2.15
- `ansible.netcommon` collection
- `cisco.ios` collection (for IOS devices)
- `cisco.nxos` collection (for NX-OS devices)
- `arista.eos` collection (for EOS devices)
- Network devices accessible via SSH or NETCONF

## Included Roles

### `acme.network_base.vlan_standard`

Deploys ACME's standard VLAN map to switching infrastructure. Ensures all required VLANs exist with correct names, removes unauthorized VLANs, and configures trunk port VLAN assignments according to site-specific profiles.

**Variables:**

| Variable | Default | Description |
|----------|---------|-------------|
| `vlan_platform` | `ios` | Target platform (`ios`, `nxos`, `eos`) |
| `vlan_site_profile` | `default` | Site profile defining the VLAN map (`default`, `datacenter`, `branch`) |
| `vlan_purge_unauthorized` | `false` | Remove VLANs not in the standard map |
| `vlan_trunk_interfaces` | `[]` | Interfaces to configure as VLAN trunks |
| `vlan_mgmt_vlan` | `100` | Management VLAN ID |

### `acme.network_base.acl_baseline`

Applies ACME's standard access control lists to network devices. Includes management plane protection (SSH sources, SNMP communities), inter-VLAN filtering rules, and logging for denied traffic. Supports both numbered and named ACLs depending on platform.

**Variables:**

| Variable | Default | Description |
|----------|---------|-------------|
| `acl_platform` | `ios` | Target platform (`ios`, `nxos`, `eos`) |
| `acl_mgmt_sources` | `[10.0.0.0/8]` | Permitted source networks for SSH/SNMP |
| `acl_log_denied` | `true` | Enable logging for denied packets |
| `acl_apply_to_vty` | `true` | Apply management ACL to VTY lines |
| `acl_custom_rules` | `[]` | Additional ACL entries to append |

## Usage

```yaml
# Deploy standard VLANs to a Cisco IOS switch
- hosts: access_switches
  roles:
    - role: acme.network_base.vlan_standard
      vlan_platform: ios
      vlan_site_profile: branch
      vlan_trunk_interfaces:
        - GigabitEthernet0/1
        - GigabitEthernet0/2

# Apply baseline ACLs to NX-OS spine switches
- hosts: spine_switches
  roles:
    - role: acme.network_base.acl_baseline
      acl_platform: nxos
      acl_mgmt_sources:
        - 10.100.0.0/16
        - 172.16.0.0/12
```

## License

Proprietary - ACME Corp Internal Use Only

## Author

ACME Corp Network Engineering Team

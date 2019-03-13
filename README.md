
# node_certificate_mgmt

Manage node certificates with a set of Puppet Tasks.

## Description

This module includes a set of tasks to manage Puppet node certificates. The module also includes a set of tasks to manage Puppet node PDB entries. Specific to node certificates,

Specific to pdb node enties,

## Details
The module leverages the `puppet-ca` API to sign, revoke and delete node certificates.

### Node Certificte Tasks

- `node_ca_status`
- `node_ca_revoke`
- `node_ca_sign`
- `node_ca_delete`

### Node PDB Entry Tasks
The module leverages the `pdb` API to deactivate nodes.

- `node_pdb_status`
- `node_pdb_deactivate`

## Usage
In the sprit of automation, below are some examples for calling the `node_certificate_mgmt` tasks via the `orchestrator` API.

## Security Considerations

## Credit



# puppet_cert_mgmt

Manage node certificates with a set of Puppet Tasks.

## Description

This module includes a set of tasks to help faciliate the provisioning and deprovisoing of Puppet nodes. The module does this by providing a set of tasks to manage both node certificates and pdb entries.

Specific to node certificates, the module leverages the `puppet-ca` API to sign, revoke and delete node certificates.

Specific to pdb node enties, the module leverages the `pdb` API to deactivate nodes.
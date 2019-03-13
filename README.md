
# node_certificate_mgmt
Manage node certificates with a set of Puppet Tasks.

## Description
This module includes a set of tasks to manage Puppet node certificates. The module also includes a set of tasks to manage Puppet node pdb entries.

## Details
The following tasks are intended to be executed on the master or some other node with the appropriate allow rules to the `/puppet-ca/v1/certificate_status/` path in `/etc/puppetlabs/puppetserver/conf.d/auth.conf`.

### Node Certificate Tasks
The module leverages the `puppet-ca` API to get status, sign, revoke and delete node certificates.

- `node_certificate_mgmt::node_ca_status`
- `node_certificate_mgmt::node_ca_sign`
- `node_certificate_mgmt::node_ca_revoke`
- `node_certificate_mgmt::node_ca_delete`

### Node PDB Entry Tasks
The module leverages the `pdb` API to get status and deactivate nodes.

- `node_certificate_mgmt::node_pdb_status`
- `node_certificate_mgmt::node_pdb_deactivate`

## Usage
In the spirit of automation, below are some examples for calling the `node_certificate_mgmt` tasks via the `orchestrator` API.

### PowerShell
Two API calls, one to execute the task and another to retrieve the results.
```powershell
$master = 'puppet.contoso.us'
$token = '*****'

$targetNodes = @('puppet.contoso.us')

$body = @{
    environment = 'production'
    task        = 'node_certificate_mgmt::node_ca_status'
    params      = @{
        node = 'den3-node-2.ad.contoso.us'
    }
    scope       = @{
        nodes = $targetNodes
    }
} | ConvertTo-Json
$uri = "https://$master`:8143/orchestrator/v1/command/task"
$headers = @{'X-Authentication' = $token}
$response = Invoke-WebRequest -Uri $uri -Method Post -Headers $headers -Body $body
# for the task we just started lets get the name so we can query the results
$jobname = ($response.content | ConvertFrom-Json).job.name

# sleep to allow the task to run
Start-Sleep -Seconds 5

$uri = "https://$master`:8143/orchestrator/v1/jobs/$jobname/nodes"
$result  = Invoke-WebRequest -Uri $uri -Method Get -Headers $headers
$result.content
```
OUTPUT
```json
{
  "items" : [ {
    "finish_timestamp" : "2019-03-13T19:21:12Z",
    "transaction_uuid" : null,
    "start_timestamp" : "2019-03-13T19:21:09Z",
    "name" : "puppet.contoso.us",
    "duration" : 3.365,
    "state" : "finished",
    "details" : { },
    "result" : {
      "name" : "den3-node-2.ad.contoso.us",
      "state" : "requested",
      "fingerprint" : "58:39:46:01:02:E3:1A:CF:3F:EA:4E:2E:F8:6A:5C:19:23:BA:34:7D:AB:04:74:D2:25:5F:AB:4A:66:1E:1A:03",
      "fingerprints" : {
        "SHA1" : "5E:F3:C1:57:DF:E7:73:6C:F5:74:CD:76:AB:58:FC:DB:80:47:BE:0E",
        "SHA256" : "58:39:46:01:02:E3:1A:CF:3F:EA:4E:2E:F8:6A:5C:19:23:BA:34:7D:AB:04:74:D2:25:5F:AB:4A:66:1E:1A:03",
        "SHA512" : "69:14:92:31:2D:9D:92:A9:97:6C:06:E8:B7:62:FB:52:1C:52:ED:A5:AB:7C:E3:12:06:4A:08:21:F0:E3:BA:E2:C5:BF:B1:3A:2F:44:C7:D9:E1:28:4D:AA:A8:CA:76:86:EE:5C:6E:2C:DE:FD:BB:1E:71:B2:D1:AB:DE:F3:ED:47",
        "default" : "58:39:46:01:02:E3:1A:CF:3F:EA:4E:2E:F8:6A:5C:19:23:BA:34:7D:AB:04:74:D2:25:5F:AB:4A:66:1E:1A:03"
      },
      "dns_alt_names" : [ ],
      "subject_alt_names" : [ ]
    },
    "latest-event-id" : 4495,
    "timestamp" : "2019-03-13T19:21:12Z"
  } ],
  "next-events" : {
    "id" : "https://puppet.contoso.us:8143/orchestrator/v1/jobs/696/events?start=4496",
    "event" : "4496"
  }
}
```

### Bash
First, execute the task.
```bash
node='den3-node-2.ad.contoso.us'
master='puppet.contoso.us'
token='*****'

curl -X POST \
  --tlsv1 \
  -H "Accept: application/json" \
  -H "X-Authentication: $token" \
  --data '{"scope":{"nodes":["puppet.contoso.us"]},"environment":"production","params":{"node":"den3-node-2.ad.contoso.us"},"task":"node_certificate_mgmt::node_ca_status"}' \
  https://{$master}:8143/orchestrator/v1/command/task
```
OUTPUT
```json
{
  "job" : {
    "id" : "https://puppet.contoso.us:8143/orchestrator/v1/jobs/697",
    "name" : "697"
  }
}
```
Second, retrieve the results.
```bash
curl -X GET \
  --tlsv1 \
  -H "Accept: application/json" \
  -H "X-Authentication: $token" \
  https://{$master}:8143/orchestrator/v1/jobs/697/nodes
```
OUTPUT
```json
{
  "items" : [ {
    "finish_timestamp" : "2019-03-13T19:40:19Z",
    "transaction_uuid" : null,
    "start_timestamp" : "2019-03-13T19:40:16Z",
    "name" : "puppet.contoso.us",
    "duration" : 2.91,
    "state" : "finished",
    "details" : { },
    "result" : {
      "name" : "den3-node-2.ad.contoso.us",
      "state" : "requested",
      "fingerprint" : "58:39:46:01:02:E3:1A:CF:3F:EA:4E:2E:F8:6A:5C:19:23:BA:34:7D:AB:04:74:D2:25:5F:AB:4A:66:1E:1A:03",
      "fingerprints" : {
        "SHA1" : "5E:F3:C1:57:DF:E7:73:6C:F5:74:CD:76:AB:58:FC:DB:80:47:BE:0E",
        "SHA256" : "58:39:46:01:02:E3:1A:CF:3F:EA:4E:2E:F8:6A:5C:19:23:BA:34:7D:AB:04:74:D2:25:5F:AB:4A:66:1E:1A:03",
        "SHA512" : "69:14:92:31:2D:9D:92:A9:97:6C:06:E8:B7:62:FB:52:1C:52:ED:A5:AB:7C:E3:12:06:4A:08:21:F0:E3:BA:E2:C5:BF:B1:3A:2F:44:C7:D9:E1:28:4D:AA:A8:CA:76:86:EE:5C:6E:2C:DE:FD:BB:1E:71:B2:D1:AB:DE:F3:ED:47",
        "default" : "58:39:46:01:02:E3:1A:CF:3F:EA:4E:2E:F8:6A:5C:19:23:BA:34:7D:AB:04:74:D2:25:5F:AB:4A:66:1E:1A:03"
      },
      "dns_alt_names" : [ ],
      "subject_alt_names" : [ ]
    },
    "latest-event-id" : 4498,
    "timestamp" : "2019-03-13T19:40:19Z"
  } ],
  "next-events" : {
    "id" : "https://puppet.contoso.us:8143/orchestrator/v1/jobs/697/events?start=4499",
    "event" : "4499"
  }
}
```

## Security Considerations
On a typical Puppet install the `puppet-ca` API uses certificate based authentication as configured in `/etc/puppetlabs/puppetserver/conf.d/auth.conf`. This module and how it is designed to call tasks executed on the master effectively works around that, thus moving what would typically be certificate based authentication for node certificate management to token based authentication.

## Credits

Thanks [Jesse Reynolds](https://github.com/jessereynolds) for the jumpstart on the `puppet-ca` `curl` commands and allowing them to be bundled in this module!

## Other Thoughts
Interested in doing this via PowerShell? Check out [PSPuppetCertificateStatus](https://github.com/joeycontoso/PSPuppetCertificateStatus).
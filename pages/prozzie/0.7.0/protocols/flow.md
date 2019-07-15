---
title: NetFlow
version: 0.7.0
permalink: /prozzie_0.7.0_flow_protocol.html
toc: false
---

### Netflow using included f2k

In order to setup netflow in prozzie, you need to add all netflow probes to
NETFLOW_PROBES env variable, using the format described in
[f2k readme](https://github.com/wizzie-io/f2k/blob/master/README.md#sensors-config){:target="_blank"}.
For example, executing linux setup this way:

```bash
NETFLOW_PROBES='{"sensors_networks":{"127.0.0.1":{"observations_id":{"default":{}}}}}' setups/f2k_setup.sh
```

To configure netflow probe, please use [prozzie config](/prozzie_{{page.version}}_cli.html#prozzie-config-actions) command over a valid prozzie installation.

NETFLOW_PROBES
: Netflow probes to expect netflow from, following the format described in
[f2k readme](https://github.com/wizzie-io/f2k/blob/master/README.md#sensors-config){:target="_blank"}.

NETFLOW_KAFKA_TOPIC
: Topic to produce netflow traffic. If you want flow treatment, it's better to
use the `flow` default.

You can check that messages are properly delivered using `prozzie kafka consume <your-netflow-topic>`.

### Netflow using nfacctd

You can use you pmacct nfacctd flow collector if you provide it with a config
file provided in [pmacctd](/prozzie_{{page.version}}_flow_protocol.html#pmacctd). You have to remember to use
`sfacctd_renormalize` instead of `pmacctd_renormalize`, and no interface.

## sflow

Sflow support is provided via [pmacct](http://www.pmacct.net/){:target="_blank"}
[sflow](http://www.sflow.org/){:target="_blank"} sfacctd accounting daemon.

You will be asked about these variables:

SFLOW_KAFKA_TOPIC
: Topic to produce sflow traffic. Need to let it by default if you want proper
indexing.

SFLOW_RENORMALIZE
: Normalize sflow byte/packet counter based on sflow packet sampling rate. Check
the [pmacct Oficial Config Keys](http://wiki.pmacct.net/OfficialConfigKeys) for
more info.

SFLOW_AGGREGATE
:Fields/dimensions to send in each event. The more fields you send, the more
memory and CPU will sfacctd use.

In order to use your own sfacctd outside prozzie, you can configure it to send
to wizzie prozzie, following a configuration found in [pmacctd](#pmacctd), but
using `sfacctd_renormalize` instead of `pmacctd_renormalize`, and no interface.

You can check that messages are properly delivered using `prozzie kafka consume <your-sflow-topic>`.

## [pmacctd](http://www.pmacct.net/)

You can use your own pmacctd probe installation to avoid sflow/netflow
conversion. You only need to configure it to send to prozzie kafka, to sflow
topic:

```yaml
interface: <interface to monitor>
sampling_rate: 1

plugins: kafka

timestamps_since_epoch: true
timestamps_secs: true

kafka_output: json
kafka_broker_host: <prozzie public IP address>
kafka_broker_port: 9092
kafka_topic: pmacct
pmacctd_renormalize: true
```

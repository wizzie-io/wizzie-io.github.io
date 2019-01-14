---
title: Prozzie FAQs
version: latest
permalink: /prozzie_latest_faqs.html
toc: false
---

## How do I contribute to the project?

Please read [contributing](/prozzie_{{page.version}}_contributing.html) section.

## How do I install prozzie in docker container

You can't, at least easily. Since prozzie is composed of dockers, you would
need to use some docker-in-docker skills to use it that way.

You can use a docker container with capabilities to manage host docker daemon,
and use it to install prozzie in. However, that configuration is not a
priority to maintain, so you can have a hard time using it. To do that, check
[How can I test prozzie](/prozzie_{{page.version}}_contributing.html#how-can-i-test-prozzie-dirt-and-quickly).

## Troubleshooting: I don't see my messages in the kafka queue

### Kafka reachability

Make sure that the producer machine has access to whatever address have you set
in `INTERFACE_IP` variable. Check it with
`prozzie config get base INTERFACE_IP`

Due to the kafka broker discovery system, kafka client will make the first
attempt to connect to the configured broker (prozzie address in this case),
and them prozzie will say that all available brokers are `INTERFACE_IP`
variable. If the producer, being the prozzie or an external kafka one, is not
able to reach it, message production will fail.

For example, imagine that you use as `INTERFACE_IP` the hostname of the
prozzie, that is not resoluble by DNS. You can say to the kafka client the IP
address of the prozzie, it will connect OK the first time, the client will
receive the hostname as "next broker to connect", and then proper kafka
connection will not be possible.

So, contrary to others connection like HTTP, you need to be sure that both
directions are right from the producer view: The bootstrap and the returned
using `INTERFACE_IP`.

### Connector logs

Please review the logs of the connector using `prozzie logs <connector>`searching for possible errors.

### Connector reachability

You can execute commands in connector containers. Use
`prozzie compose exec sh`, install tcpdump, and check if packets are reaching
the connector interface.

### Advertised IP

If you have a producer or probe to send messages to your prozzie's kafka you couldn't see messages in the kafka queue due to the advertised IP of Kafka. See the next diagram for more details:

{% include image.html file="./prozzie/prozzie_advertised_ip_issue.png" alt="Prozzie Advertised IP Issue" url="./images/prozzie/prozzie_advertised_ip_issue.png" caption="Prozzie Advertised IP Issue" figure-style="text-align:center" caption-style="text-align:center" %}

In this diagram, a producer or probe request about `prozzie.wizzie.lan` domain. The domain's IP is resolved correctly, but when the producer or probe try to connect the advertised IP of Kafka in your prozzie could point to another IP. In this case, the producer doesn't reach the IP, and consequently, It can't produce messages to Kafka.
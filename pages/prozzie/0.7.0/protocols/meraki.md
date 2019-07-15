---
title: Meraki
version: 0.7.0
permalink: /prozzie_0.7.0_meraki_protocol.html
toc: true
---

Prozzie is listening for meraki location and scanning messages in port 2057. You
can configure your meraki side following
[n2kafka meraki guide](https://github.com/wizzie-io/n2kafka/blob/master/src/decoder/meraki/README.md).

You can check that messages are properly delivered using
`prozzie kafka consume testtopic`.

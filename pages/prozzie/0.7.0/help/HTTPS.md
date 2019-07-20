---
title: Prozzie HTTPS integration
version: 0.7.0
permalink: /prozzie_0.7.0_https.html
toc: false
---

Some vendors like `Aerohive` or `Cisco` need to use a secure connection to send data. In this section we are going to explain how to configure Traefik to use HTTPS protocol using `Let's Encrypt` with your Prozzie installation.

## Introduction

[Traefik](https://github.com/containous/traefik) is a modern HTTP reverse proxy and load balancer that makes deploying microservices easy. Traefik can be integrate with some Prozzie connectors like `Aerohive`, `Meraki` or `HTTP2K`. But, you can use this microservice with another purposes.

[Let's Encrypt](https://letsencrypt.org/) is a free, automated, and open [Certificate Authority](https://en.wikipedia.org/wiki/Certificate_authority) sponsored by great companies like `Cisco`, `Google` or `GitHub`.

In the next image you can see the result of this section:

```
IMAGE HERE
```

### Getting started

`Traefik` isn't integrated in Prozzie, but, `Prozzie` is prepared to be used with `Traefik`. First, we need to configure a `docker-compose.yml` file to easily `up` and `down` our microservice. Here you a have an example of this file:

```
version: '3.6'

services:
  traefik:
    image: traefik:1.7.11-alpine
    container_name: traefik_proxy
    command: --web --docker --docker.domain=localhost --docker.watch
    ports:
     - "80:80"
     - "443:443"
    volumes:
     - /var/run/docker.sock:/var/run/docker.sock
     - ~/traefik/traefik.toml:/traefik.toml
    networks:
      prozzie_traefik:
    labels:
     - "traefik.enable=false"

networks:
  prozzie_traefik:
    external:
      name: prozzie_traefik
```

As you can see we are using a external network named `prozzie_traefik`, we will use this network to separate Prozzie from Traefik network traffic. But, you can use the `prozzie_default` network. To create a new external network in docker you can check its [documentation](https://docs.docker.com/network/bridge/).

Once crete the `docker-compose.yml` file we need define some parameters to use with `Let's Encrypt`. Below you can see an example of a [Toml](https://github.com/toml-lang/toml) file to configure `Traefik`:

```
debug = false

logLevel = "ERROR"

defaultEntryPoints = ["https", "http"]

[entryPoints]
  [entryPoints.http]
    address = ":80"
    [entryPoints.http.redirect]
    entryPoint = "https"
  [entryPoints.https]
  address = ":443"
  [entryPoints.https.tls]

[retry]

[docker]
endpoint = "unix:///var/run/docker.sock"
domain = "your.domain.com"
watch = true
exposedByDefault = false

[acme]
email = "email@your.domain.com"
storage = "acme.json"
entryPoint = "https"
onHostRule = true
[acme.httpChallenge]
entryPoint = "http"
```

Finally you only need to run `docker-compose up -d` to run `Traefik` with the configuration.

### Integration with Prozzie

Once we have `Traefik` running this should see all our Prozzie containers and register all services that use HTTPS protocol. If you check some Prozzie docker-compose file you can see that some containers have a label like `traefik.enable=false` this label allows us disable the container in Traefik.

We are going to see the `http2k.yml` docker-compose.yml:

```
version: '3.6'

services:
  http2k:
    image: gcr.io/wizzie-registry/n2kafka:2.1.1
    restart: always
    depends_on:
      - kafka
    ports:
      - 7980:7980
    networks:
      prozzie_traefik:
      prozzie_default:
    environment:
      KAFKA_BROKERS: kafka
    labels:
     - "traefik.weight=100"
     - "traefik.backend=http2k"
     - "traefik.port=7980"
     - "traefik.frontend.rule=Host:your.domain.com;PathPrefix:/v1/data"
     - "traefik.docker.network=prozzie_traefik"

networks:
  prozzie_traefik:
    external:
      name: prozzie_traefik
  prozzie_default:
    external:
      name: prozzie_default
```

In the docker-compose file you can see some labels to use with traefik in this case we have defined a `weight`, a `backend`, a `port`, a `fronend.rule` and a `docker.network`:

- `weight`: Traefik use the weight to balance load.
- `backend`: Traefik use the backend to create a cluster of nodes to load balance.
- `port`: Traefik use port to send the recived data to the correct service port.
- `frontend.rule`: We can use Traefik `host based routing` or `path based routing`. If you use the first one, Traefik will send information to service based in the domain. If you use the second one, Traefik will send information to service based in domain and path.
- `docker.network`: The network that Traefik will use to send data.

You can find a complete list of Traefik labels in its [docker documentation page](https://docs.traefik.io/configuration/backends/docker/#using-docker-compose).
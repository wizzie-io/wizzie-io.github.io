---
title: Deploy
permalink: /wizz-vis_0.11.0_deploy.html
version: 0.11.0
---

Wizz-Vis image: `wizzieio/wizz-vis:{{ page.version }}`

## How to get Wizz-Vis image
You can pull the docker image from our registry:

```
$ docker pull wizzieio/wizz-vis:{{ page.version }}
```

## Requirements
Before run the Wizz-Vis container, there are some requirements necessary to deploy a Wizz-Vis instance:

* PostgreSQL 9.4+
* Redis 3.2+
* Druid 0.12.1+

## Using environment variables in Wizz-Vis configuration

| Env Property               |      Description      |  Default Value |
|----------------------------|-----------------------|----------------|
| `RAILS_ENV`                | Specifies the `environment` that Wizz-Vis will run in. (`development`, `production`) | development |
| `PORT`                     | Specifies the `port` that Wizz-Vis will listen on to receive requests  | 3000 |
| `RAILS_MAX_THREADS`        | Threads available to serve requests  | 5 |
| `SECRET_KEY_BASE`          | Long randomized string which is used to verify the integrity of signed cookies | null  |
| `REDIS_URL`                | Redis URL (i.e. `redis://redis:6379/0`) |  null  |
| `DATABASE_URL`             | URL for the database (i.e. `postgresql://user:password@host:5432/wz_db`) | null |
| `DRUID_URL`                | Druid broker URL (i.e. `http://localhost:8080/druid/v2`) | null |
| `DRUID_TIMEOUT`            | Query timeout in millis, beyond which unfinished queries will be cancelled. 0 timeout means no timeout. | 60000|
| `MAPBOX_TOKEN`             | Necessary to use the tools associated to maps | null |
| `RAILS_SERVE_STATIC_FILES` | Serve assets from Rails | null |
| `PRIMARY_LOGO_URL`         | URL for primary logo | Wizzie logo |
| `SECONDARY_LOGO_URL`       | URL for secondary logo | null |
| `PRIMARY_COLOR`            | Primary logo in hex format | #f68d2e |
| `SECONDARY_COLOR`          | Secondary logo in hex format | #8c8c8c |

You can visit <https://www.mapbox.com/help/how-access-tokens-work/>{:target="_blank"} and see how you can create a Mapbox Token.

## Start Wizz-Vis

Once all the requirements are satisfied, you can start the Wizz-Vis containers, one for the GUI and another for the background jobs, that will run sidekiq:
```
$ docker run --rm --name my-wizz-vis-gui -p 3000:3000 \
  -e RAILS_ENV=production \
  -e REDIS_URL=redis://redis:6379/0 \
  -e DATABASE_URL=postgresql://user:password@host:5432/wizz_vis_database \
  -e SECRET_KEY_BASE=some_really_really_long_super_secret_key_base \
  -e DRUID_URL=http://localhost:8080/druid/v2 \
  -e MAPBOX_TOKEN=pk.eyJNGE5ZHQwNmpiMzNvNHN6cTlkbHFkIn0.GqmC \
  -e RAILS_SERVE_STATIC_FILES=true \
  --entrypoint scripts/docker-entrypoint-web-prod.sh \
  wizzieio/wizz-vis:{{ page.version }}
```

```
$ docker run --rm --name my-wizz-vis-sidekiq \
  -e RAILS_ENV=production \
  -e REDIS_URL=redis://redis:6379/0 \
  -e DATABASE_URL=postgresql://user:password@host:5432/wizz_vis_database \
  -e SECRET_KEY_BASE=some_really_really_long_super_secret_key_base \
  -e DRUID_URL=http://localhost:8080/druid/v2 \
  -e MAPBOX_TOKEN=pk.eyJNGE5ZHQwNmpiMzNvNHN6cTlkbHFkIn0.GqmC \
  -e RAILS_SERVE_STATIC_FILES=true \
  --entrypoint scripts/docker-entrypoint-sidekiq.sh \
  wizzieio/wizz-vis:{{ page.version }}
```

Now, you can visit <http://localhost:3000>{:target="_blank"} to access Wizz-Vis.

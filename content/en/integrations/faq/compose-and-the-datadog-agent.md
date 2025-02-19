---
title: Compose and the Datadog Agent
kind: faq
---

[Compose][1] is a Docker tool that simplifies building applications on Docker by allowing you to define, build and run multiple containers as a single application.

While the [Single Container Installation instructions][2] gets the stock Datadog Agent container running, you will most likely want to enable integrations for other containerized services that are part of your Compose application.  
To do this, you'll need to combine integration YAML files with the base Datadog Agent image to create your Datadog Agent container. Then you'll need to add your container to the Compose YAML.

##### Example: Monitoring Redis

The following is an example of how you can monitor a Redis container using Compose. The example file structure is:

    |- docker-compose.yml
    |- datadog
        |- Dockerfile
        |- conf.d
           |-redisdb.yaml

First, take a look at the `docker-compose.yml` that describes how your containers work together and sets some of the configuration details for the containers.

```yaml
version: '3'
services:
  redis:
    image: redis
  datadog:
    links:
     - redis # Ensures datadog container can connect to redis container
    image: datadog/agent:latest
    environment:
     - DD_API_KEY=${DD_API_KEY}
    volumes:
     - /var/run/docker.sock:/var/run/docker.sock
     - /proc/mounts:/host/proc/mounts:ro
     - /sys/fs/cgroup:/host/sys/fs/cgroup:ro
```

The `redisdb.yaml` is patterned after the [redisdb.yaml.example file][3] and tells the Datadog Agent to look for Redis on the host named `redis` (defined in `docker-compose.yaml` above) and the standard Redis port 6379:

    init_config:

    instances:
      - host: redis
        port: 6379

For a more complete example, see the [Docker Compose example project on GitHub][4].

[1]: https://docs.docker.com/compose/overview
[2]: /integrations/docker_daemon
[3]: https://github.com/DataDog/integrations-core/blob/master/redisdb/datadog_checks/redisdb/data/conf.yaml.example
[4]: https://github.com/DataDog/docker-compose-example

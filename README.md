# Logging server

This is a docker setup to get loki and grafana running with traefik labels to
proxy access to it.


This is currently necessary, at least on linux, to get the official containers
running. 

```shell
sudo chmod -R 777 .docker/data/loki
sudo chmod -R 777 .docker/data/prometheus
```

## Loki auth

To protect Loki it should be behind basic auth https://doc.traefik.io/traefik/middlewares/http/basicauth/

__Note__: when used in docker-compose.yml all dollar signs in the hash need to 
be doubled for escaping. To create user:password pair, it's possible to use this
command:

```shell
echo $(htpasswd -nB loki) | sed -e s/\\$/\\$\\$/g
```

Set the output as the environment variable `LOKI_BASIC_AUTH` to that value in 
.env.docker.local.

## Grafana

Default login `admin` and `admin`.

## Prometheus

To scrape custom end-points, you need to copy the default prometheus 
configuration file located in `.docker/prometheus-config.yml` into a new local
config file and make a docker compose override file data override the prometheus
default container volume config file mapping.

For complet configuration overview see: https://prometheus.io/docs/prometheus/latest/configuration/configuration/

```yaml
global:
  scrape_interval: 15s
  evaluation_interval: 15s

scrape_configs:
  # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
  - job_name: "prometheus"
    static_configs:
      - targets: ["localhost:9090"]

  - job_name: 'local_metrics'
    scrape_interval: 10s
    metrics_path: metrics
    scheme: http
    static_configs:
      - targets:
        - <site1.local.itkdev.dk:80>
        - <site2.local.itkdev.dk:80>
```

You may need to bring look up the end-points in the frontend network.

```shell
docker network inspect frontend
```

Or you can guess the name, which normally will be `<namespace>-nginx-1.frontend`. 

## Endpoints

* http://obs.local.itkdev.dk/
* http://loki.local.itkdev.dk/ready
* http://prom.local.itkdev.dk

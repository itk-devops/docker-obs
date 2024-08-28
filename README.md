# Logging server

This is a docker setup to get loki and grafana runing with traefik labels to proxy access to it.


```shell
sudo chmod -R 777 .docker/data/loki
sudo chmod -R 777 .docker/data/prometheus
```

## Loki auth

To protect Loki it should be behind basic auth https://doc.traefik.io/traefik/middlewares/http/basicauth/

__Note__: when used in docker-compose.yml all dollar signs in the hash need to be doubled for escaping.
To create user:password pair, it's possible to use this command:

```shell
echo $(htpasswd -nB loki) | sed -e s/\\$/\\$\\$/g
```

Set the output as the enviroment variable `LOKI_BASIC_AUTH` to that vaule in .env.docker.local.

## Grafana

Default login `admin` and `admin`.

## Endpoints

http://logs.local.itkdev.dk/
http://loki.local.itkdev.dk/ready

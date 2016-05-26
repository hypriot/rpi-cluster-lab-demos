### Loadbalancing with Traefik

[Traefik](https://docs.traefik.io/) is a lean and capable loadbalancer for dynamic microservice environments.

Clone this repository and then change into the repository directory.

There execute

```
$ export DOCKER_HOST=192.168.200.1:2378

$ docker-compose up -d
```

Afterwards you should have a running traefik container on you cluster leader and one whoami container on one of your followers.

```
$ docker ps
CONTAINER ID        IMAGE                      COMMAND                  CREATED             STATUS              PORTS                                                    NAMES
f72892c9187c        hypriot/rpi-whoami         "/app/http"              14 minutes ago      Up 14 minutes       8000/tcp                                                 cl-follower1/traefik_whoami_1
983fbe7f3353        hypriot/rpi-traefik        "/traefik --web --doc"   14 minutes ago      Up 14 minutes       192.168.200.1:80->80/tcp, 192.168.200.1:8080->8080/tcp   cl-leader/traefik_traefik_1
```

To test if everything works execute
```
$ curl -H Host:whoami.docker.localhost http://192.168.200.1
I'am f72892c9187c
```

If this worked we can scale our backend servers up a bit:
```
$ docker-compose scale whoami=5
```

Check if everything went well by
```
$ for i in {1..5}; do curl -H Host:whoami.docker.localhost http://192.168.200.1; done
I'm 5d829fecbdaa
I'm 5eb115353885
I'm e0313ac24554
I'm 642b5d2c8d09
I'm f72892c9187c
```

In the end (and there is always an end) should it down again by
```
$ docker-compose down -v
```


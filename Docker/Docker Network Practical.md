```
docker network create docker-lab net
```

* Starting nginx container on the network that we created

```
docker run -d --name lab-nginx --network docker-lab-net nginx:alpine
```

* Launch a temp container on the same network and try to see page

```
docker run --rm \
  --network docker-lab-net \
  busybox \
  wget -qO- http://lab-nginx
```

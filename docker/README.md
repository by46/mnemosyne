Python onbuild base
======================
```shell
sudo docker run --name python -i alpine:3.3 /bin/sh

# in container
apk update

apk add build-base
apk add git
apk add python
apk add python-dev
apk add py-pip
apk add freetds freetds-dev
apk add docker

```

sudo docker run -v /var/run/docker.sock:/var/run/docker.sock -v /home/by46/git/demo:/opt/demo -v /etc/sysconfig:/etc/sysconfig --rm -i python:2 /bin/sh
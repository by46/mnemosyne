Private PYPI
===========================
1. download pip
```shell
wget https://bootstrap.pypa.io/get-pip.py
python get-pip.py
```

2. install pypiserver
```shell
pip install pypiserver

pip install passlib
yum install httpd-tools

htpasswd -sc .htaccess dfis

 pypi-server -p 3141 -P .htaccess /opt/pypi/packages

```

3. pip2pi
```shell
pip install pip2pi

pip2pi /opt/pypi/packages requests
```
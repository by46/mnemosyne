Raspberry PI
================

raspbian
----------------

keyboard layout
----------------
Sometime, the raspberry pi's default keyboard layout is uk, the result is that the '@' and '"' changed each other, and '#' changed to become england's money sign. There is two way:
1. use ``raspi-config`` on terminal
2. edit the /etc/default/keyboard file, like below

```shell
# KEYBOARD CONFIGURATION FILE

# Consult the keyboard(5) manual page.

XKBMODEL="pc105"
XKBLAYOUT="us"
XKBVARIANT=""
XKBOPTIONS=""

BACKSPACE="guess"

```

Apt
----------------
setting mirrors which the fastest.

```shell
cd /etc/apt

sudo mv source.list source.list.bak

sudo wget http://mirrors.cqu.edu.cn/distri/Raspbian/sources.list

sudo apt-get update && sudo apt-get upgrade

```

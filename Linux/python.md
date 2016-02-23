installation
===========

prerequisite
<<<<<<< HEAD
---------------
=======
--------------

```shell
sudo yum groupinstall "Development tools"
sudo yum install zlib bzip2 openssl ncurses sqlite readline
sudo yum install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel
sudo pip install 


open-source
---------------
flask, requests, Werkzeug, Jinja2, Sphinx, Pygments

Pypi publish
--------------
python setup.py register
python setup.py upload
=======
```

Virtual Environments
------------------------
A Virtual Environment is a tool to keep the dependencies required by different projects in separate places, 
by creating virtual Python environments for them. It solves the "Project X depends on version 1.x but, Project Y needs 4.x" dilemma,
and keeps your global site/packages directory clean and manageable.

For example, you can work on a project which requires Django 1.3 while also maintaining a project which requires Django 1.0

1. Install virtualenv via pip

```shell

pip install virtualenv

```

2. Basic Usage

```shell

$ cd my_project_folder
$ virtualenv venv

```

3. activate

on linux:

```shell

$ source venv/bin/activate

```

on Windows:

```shell

$ venv/scripts/activate.bat

```

then will prompt some message, like below:

(venv)Your-Computer:your_project UserName$)

than you can use the separate environment.

4. deactivate

If you are done working in the virtual environment for the moment, you can deactivate it:

```shell
$ deactivate
```

Pycharm with Virutal Environment
--------------------------------------

the JetBrains's Pycharm support virtualenv, some details see [Creating Virtual Environment](https://www.jetbrains.com/pycharm/help/creating-virtual-environment.html)

installation
===========

prerequisite
--------------

```shell
sudo yum -y update
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


Preparing The System and Installing Python
-------------------------------
https://www.digitalocean.com/community/tutorials/how-to-set-up-python-2-7-6-and-3-3-3-on-centos-6-4


Like many other applications you will encounter, installation of Python on CentOS consists of a few (simple) stages, starting with updating the system and followed by actually getting Python for any desired version and proceeding with the set up process.

Remember: You can see all available releases of Python by checking out the Releases page. Using the instructions here, you should be able to install any or all of them.

Note: This guide should be valid for CentOS version 6.5 as well as 5.8 and 6.4.

Updating The Default CentOS Applications

Before we begin with the installation, let's make sure to update the default system applications to have the latest versions available.

Run the following command to update the system applications:

```shell
yum -y update
```

Preparing The System for Development Installations
------------------------------
CentOS distributions are lean - perhaps, a little too lean - meaning they do not come with many of the popular applications and tools that you are likely to need.

This is an intentional design choice. For our installations, however, we are going to need some libraries and tools (i.e. development [related] tools) not shipped by default. Therefore, we need to get them downloaded and installed before we continue.

There are two ways of getting the development tools on your system using the package manager yum:

Option #1 (not recommended) consists of downloading these tools (e.g. make, gcc etc.) one-by-one. It is followed by trying to develop something and highly-likely running into errors midway through - because you will have forgotten another package so you will switch back to downloading.

The recommended and sane way of doing this is following Option #2: simply downloading a bunch of tools by a single command with yum software groups.


YUM Software Groups

YUM Software Groups consist of bunch of commonly used tools (applications) bundled together, ready for download all at the same time via execution of a single command and stating a group name. Using YUM, you can even download multiple groups together.

The group in question for us is the Development Tools.

How to Install Development Tools using YUM on CentOS

```shell
yum groupinstall -y development
```

or,

```shell
yum groupinstall -y 'development tools'
```

Note: The former (shorter) version might not work on older distributions of CentOS.

To download some additional packages which are handy:

```shell
yum install -y zlib-dev openssl-devel sqlite-devel bzip2-devel

```

Remember: Albeit optional, these "handy" tools are very much required for most of the tasks that you will come across in future. Unless they are installed in advance, Python, during compilation, will not be able to link to them.

Python Installation Procedure From Source
Setting up Python on our system will consist of 3 stages and 4 tools:

1. Downloading the compressed source code package (wget),
2. Extracting the files from this package (tar),
3. Configuring and building the application (autoconf (configure) / make).


```shell
wget http://www.python.org/ftp/python/2.7.6/Python-2.7.6.tar.xz

```


Configuring and Installation
----------------------------------
Before building the source, we need to make sure that all the dependencies are there and prepare the environment. This is achieved automatically by using ./configure to handle the task for us.

```shell
# Enter the file directory:
cd Python-2.7.6

# Start the configuration (setting the installation directory)
# By default files are installed in /usr/local.
# You can modify the --prefix to modify it (e.g. for $HOME).
./configure --prefix=/usr/local    
```

Building and Installing
-------------------------------
After configuring everything for the system we are working on, we can continue with building (compiling) the source and installing the application. Normally, one would use “make install”; however, in order not to override system defaults - replacing the Python already used by the system - we will use make altinstall.

```shell
# Let's build (compile) the source
# This procedure can take awhile (~a few minutes)
make

# After building everything:
make altinstall

```


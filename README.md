# DevOps Prerequisites Course
A course for taking before other DevOps courses.

## LINUX BASICS
### Linux CLI
```
# There are several shells in Linux. The $SHELL environment variable contains the current shell.
$ echo $SHELL
/usr/bin/zsh

```

Commands required `echo`, `ls`, `cd`, `pwd`, `mkdir`.

For running commands sequentially, use the semicolon, for example:
```
$ cd new_directory; mkdir www; pwd

```

Commands for directories:
```
# For creating an entire directory tree in one shot
$ mkdir -p /tmp/asia/india/bangalore

# For removing or copying directories recursively, use the -r flag
$ rm -r /tmp/my_dir1
$ cp -r my_dir /tmp/my_dir1

```

Commands for files:
```
# Create a file with no contents
$ touch new_file.txt

# Add contents to file
$ cat > new_file.txt
Sample contents  # Press enter

$ cat new_file.txt 
Sample contents

```

User Accounts:
```
# Show the current user
$ whoami
andres

# More information about the user
$ id
uid=1000(andres) gid=1000(andres) groups=1000(andres),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),122(lpadmin),134(lxd),135(sambashare),140(vboxusers),998(nordvpn),999(docker)

# Switch user, you'll be prompted with the user password
$ su aparna
password

# Connect to another system using ssh
$ ssh aparna@192.168.1.2

# Access to the root user is restricted
$ ls /root
ls: cannot open directory '/root': Permission denied

# Grant sudo privileges to a user, to elevate their permission level
$ sudo ls /root
snap

```

Download files:
```
# Download files from the web
$ curl https://es.wikipedia.org/wiki/Wikipedia:Portada -O
some-file.txt

$ tail -2 Wikipedia:Portada
</body>
</html>%           

$ wget https://es.wikipedia.org/wiki/Wikipedia:Portada -O some-file.txt

$ tail -2 some-file.txt
</body>
</html>%

```

Check OS Version:
```
# To get information about the OS, inspect the release files under the /etc directory
$ ls /etc/*release*
/etc/lsb-release  /etc/os-release

# To see more details about the use by viewing release files
$ cat /etc/*release*
DISTRIB_ID=Ubuntu
DISTRIB_RELEASE=22.04
DISTRIB_CODENAME=jammy
DISTRIB_DESCRIPTION="Ubuntu 22.04.3 LTS"
PRETTY_NAME="Ubuntu 22.04.3 LTS"
NAME="Ubuntu"
VERSION_ID="22.04"
VERSION="22.04.3 LTS (Jammy Jellyfish)"
VERSION_CODENAME=jammy
ID=ubuntu
ID_LIKE=debian
HOME_URL="https://www.ubuntu.com/"
SUPPORT_URL="https://help.ubuntu.com/"
BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
UBUNTU_CODENAME=jammy

```

Package Managers. Required for installing various pieces of software like web servers, db servers, DevOps tools, etc
```
# Centos uses RPM (Red Hat Package Manager), to install .rpm bundle packages

# Install Package
$ rpm -i telnet.r

# Uninstall Package
$ rpm -e telnet.rpm

# Query package
$ rpm -q telnet.rpm

```

Sometimes it's required to query a package, get automatically
all its dependencies and install them along with the package,
here is where package managers like `yum` come into play. Yum
uses `rpm` under the hood to perform all those tasks.
```
# Install package, it looks at software repositories containing
# hundreds of thousands of packages
$ yum install ansible

# This is the default set of repositories 
$ cat /etc/rum.repos.d

# Sometimes it can not have the software or the version required.
# Information of adding aditional set of repositories can be found
# at the same file.

# To see the repositories in a system
$ yum repolist

# Shows the files where those repos are configured
$ ls /etc/yum.repos.d/

# Inside of each file you can find the URL of the locations
# where all packages are stored, this is how yum find the packages
$ cat /etc/yum.repos.d/CentOS-Base.repo

# Accessing to the URL we can see a list of URLs where packages
# are download from, it normally contains older versions of
# the packages, like ansible. The instructios for updating the
# URL to the lastest ansible install URL you can follow the
# instructions provided at the ansible site.
$ yum list ansible
Installed Packages
ansible.noarch          2.9.6-1.e17             @epel

# Remove package
$ yum remove ansible

# To list all available versions of a package, use the
# --showduplicates option. In the following code output we can
# see two different versions of the ansible package, from 
# different repositories, the `extras` repo and the `epel` repo.
$ yum --showduplicates list ansible
Available Packages
ansible.noarch              2.4.2.0-2.el7              extras
ansible.noarch              2.9.6-1.el7                epel

# To install a specific version for a package, use:
# [package-name]-[package-version], like so:
$ yum install ansible-2.4.2.0

```

Services in Linux. Services run in the background all the time
automatically even after a service reboot. Also the must follow
the right order to startup:
```
# To start a service, in this case the HTTPD service
$ service httpd start

# The previous `service` command uses systemctl underneath,
# so we're going to focus in the systemctl command for the
# rest of the course
$ systemctl start httpd

# To stop a running service
$ systemctl stop httpd

# To check the status of a service
$ systemctl status httpd

# Configure HTTPD to start at startup
$ systemctl enable httpd

# Configure HTTPD to not start at startup
$ systemctl disable httpd

```

Configuring a program as a service:
```
# Create the application that we want to make a service
# In this case it's a python web server, the command to run this
# web server would be:
$ /usr/bin/python3 /opt/code/my_app.py

# To configure our program as a systemd service, we need a 
# systemd unit file. It may be located at `/etc/systemd/system`
$ cd /etc/systemd/system

# The file must be named after the name that we want to use for
# the service, in this case "my_app"
$ cat > /etc/systemd/system/my_app.service
[Service]
ExecStart=/usr/bin/python3 /opt/code/my_app.py

# Run the following command to let systemd know that there is a
# new service configured
$ systemctl daemon-reload

# Let's start the service
$ systemctl start my_app

# Let's check the status of the service
$ systemctl status my_app
● my_app.service - my_app.service
   Loaded: loaded (/etc/systemd/system/my_app.service; static; vendor preset: disabled)
   Active: active (running) since Tue 2020-04-07 09:01:39 UTC; 2s ago
 Main PID: 5038 (python3)
    CGroup: /system.slice/my_app.service
            └─5038 /usr/bin/python3 /tmp/app/my_app.py

Apr 07 09:01:39 systemd[1]: Started my_app.service.
Apr 07 09:01:39 python3[5038]: * Serving Flask app "my_app" (lazy loading)
Apr 07 09:01:39 python3[5038]: * Environment: production
Apr 07 09:01:39 python3[5038]: WARNING: This is a development server. Do not use it in a prod...ent.
Apr 07 09:01:39 python3[5038]: Use a production WSGI server instead.
Apr 07 09:01:39 python3[5038]: * Debug mode: off
Apr 07 09:01:39 python3[5038]: * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
Hint: Some lines were ellipsized, use -l to show in full.

$ systemctl start my_app

$ systemctl stop my_app

```

Now let's configure it to automatically run when the system boots
up:
```
# The unit configuration file has another sections that can be
# configured.
# The [Install] section will configure this service to run after
# a particular service that runs at boot up. So this service
# will run after the multi-user.target run level started.
$ vim my_app.service
[Service]
ExecStart=/usr/bin/python3 /opt/code/my_app.py

[Install]
WantedBy=multi-user.target

# To configure the service to start during boot up
$ systemctl enable my_app

# For providing additional information about the service using the
# [Unit] section of the `my_app` service systemd unit file
$ vim my_app.service
[Unit]
Description=My python web application

[Service]
ExecStart=/usr/bin/python3 /opt/code/my_app.py

[Install]
WantedBy=multi-user.target

# If the application has other dependencies as commands or scripts
# that are to be run before starting the applicaton of after
# starting the application, add the `ExecStartPre` and/or the
# `ExecStartPost` directives.
# For automatically restart the application in case it crashes,
# use the `Restart` directive
$ vim my_app.service
[Unit]
Description=My python web application

[Service]
ExecStart=/usr/bin/python3 /opt/code/my_app.py
ExecStartPre=/opt/code/configure_db.sh
ExecStartPost=/opt/code/email_status.sh
Restart=always

[Install]
WantedBy=multi-user.target

```

### VI Editor
### Package Management
### Service Management

## SETUP LAB ENVIRONMENT

## VirtualBox

## Vagrant

## Linux Networking

## JSON/YAML
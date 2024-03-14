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
# -O, --remote-name
#              Write output to a local file named like the remote file we get.
$ curl https://es.wikipedia.org/wiki/Wikipedia:Portada -O
$ ll | grep Wikipedia
-rw-rw-r-- 1 andres andres 106K mar 14 11:25 Wikipedia:Portada

$ tail -2 Wikipedia:Portada
</body>
</html>%           

#  -O file
#      --output-document=file
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

### Package Management
Package managers are equired for installing various pieces of software like web servers, db servers,
DevOps tools, etc
```
# Centos uses RPM (Red Hat Package Manager), to install .rpm bundle packages

# Install Package
$ rpm -i telnet.r

# Uninstall Package
$ rpm -e telnet.rpm

# Query package
$ rpm -q telnet.rpm

```

Sometimes it's required to query a package, get automatically all its dependencies and install them
along with the package, here is where package managers like `yum` come into play. Yum uses `rpm`
under the hood to perform all those tasks.
```sh
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

# Inside of each file you can find the URL of the locations where all packages are stored, this is
# how yum find the packages
$ cat /etc/yum.repos.d/CentOS-Base.repo

# Accessing to the URL we can see a list of URLs where packages are download from, it normally
# contains older versions of the packages, like ansible. The instructios for updating the
# URL to the lastest ansible install URL you can follow thes instructions provided at the ansible
# site.
$ yum list ansible
Installed Packages
ansible.noarch          2.9.6-1.e17             @epel

# Remove package
$ yum remove ansible

# To list all available versions of a package, use the --showduplicates option. In the following
# code output we can see two different versions of the ansible package, from different repositories,
# the `extras` repo and the `epel` repo.
$ yum --showduplicates list ansible
Available Packages
ansible.noarch              2.4.2.0-2.el7              extras
ansible.noarch              2.9.6-1.el7                epel

# To install a specific version for a package, use:
# [package-name]-[package-version], like so:
$ yum install ansible-2.4.2.0

```

### Service Management
Services run in the background all the time automatically even after a service reboot. Also they
must follow the right order to startup:
```sh
# To start a service, in this case the HTTPD service
$ service httpd start

# The previous `service` command uses systemctl underneath, so we're going to focus in the systemctl
# command for the rest of the course
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
```sh
# Create the application that we want to make a service. In this case it's a python web server, the
# command to run this web server would be:
$ /usr/bin/python3 /opt/code/my_app.py

# To configure our program as a systemd service, we need a systemd unit file. It may be located at
# `/etc/systemd/system`
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
```sh
# The unit configuration file has another sections that can be configured.
# The [Install] section will configure this service to run after a particular service that runs at
# boot up. So this service will run after the multi-user.target run level started.
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

# If the application has other dependencies as commands or scripts that are to be run before
# starting the applicaton of after starting the application, add the `ExecStartPre` and/or the
# `ExecStartPost` directives.
# For automatically restart the application in case it crashes, use the `Restart` directive
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

Example of the Service Unit File for Docker:
```sh
$ sudo cat /lib/systemd/system/docker.service 
[sudo] password for andres: 
[Unit]
Description=Docker Application Container Engine
Documentation=https://docs.docker.com
After=network-online.target docker.socket firewalld.service containerd.service time-set.target
Wants=network-online.target containerd.service
Requires=docker.socket

[Service]
Type=notify
# the default is not to use systemd for cgroups because the delegate issues still
# exists and systemd currently does not support the cgroup feature set required
# for containers run by docker
ExecStart=/usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock
ExecReload=/bin/kill -s HUP $MAINPID
TimeoutStartSec=0
RestartSec=2
Restart=always

# Note that StartLimit* options were moved from "Service" to "Unit" in systemd 229.
# Both the old, and new location are accepted by systemd 229 and up, so using the old location
# to make them work for either version of systemd.
StartLimitBurst=3

# Note that StartLimitInterval was renamed to StartLimitIntervalSec in systemd 230.
# Both the old, and new name are accepted by systemd 230 and up, so using the old name to make
# this option work for either version of systemd.
StartLimitInterval=60s

# Having non-zero Limit*s causes performance problems due to accounting overhead
# in the kernel. We recommend using cgroups to do container-local accounting.
LimitNPROC=infinity
LimitCORE=infinity

# Comment TasksMax if your systemd version does not support it.
# Only systemd 226 and above support this option.
TasksMax=infinity

# set delegate yes so that systemd does not reset the cgroups of docker containers
Delegate=yes

# kill only the docker process, not all processes in the cgroup
KillMode=process
OOMScoreAdjust=-500

[Install]
WantedBy=multi-user.target

```

### VI Editor
There are another CLI text editors like `vim` or `nano`, in this course we will focus on `vi`. CLI
text editors are very useful when manipulating configuration files.
```
# This will open the specified file with vi
$ vi index.html

```

`vi` has two modes of operation:
- **Command Mode:** This is the default mode when we open a file using vi. In this mode we can issue
commands to the editor like copying and pasting a line or moving through the file, but we can not
write content to the file.
- **Insert Mode:** Allow us to write content on the file. To switch to Insert Mode type `s`. To get
back to the Command Mode use `Esc`.

#### Command mode
```
# For moving around through the editor you could use:
# Keyboard arrows
  ↑
← ↓ →

# Keys
  K
H J L

# Deleting:
# One character
x

# The entire line
dd

# Copying an entire line
yy

# Pasting
p

# For moving the page (scrolling) up or down
CTRL+u ; CTRL+d

# For getting the command prompt
:

# For saving changes made to a file and save the file to disk. We can also specify an optional
# filename
:w
:w filename

# To discard the unsaved changes and quit
:q

# To save changes and quit
:wq

# For finding the text 'of', all the occurrences of this word will be highlighted
/of

# To move the cursor to the next occurrence
n

```

## SETUP LAB ENVIRONMENT
We have two options for this task, using a local environment (laptop or PC), or using a cloud
environment. We will review a locall implementation since it give us more independency regarding
permissions and other configurations that will require additional dependencies in the cloud.

The advantage of using VMs over a local machine is that we could install different dependencies
required for a specific deployment, without affecting the performance of the local machine.

### Virtualization Software / Hypervisors
A software that can be used to create VMs.

#### Type 1
- They are installed directly on bare metal
- The VMs are created on it
- These are used in enterprises and production environments where you need to create a large
amount of VMs, so they have high resource requirements.
- They are expensive
- Examples:
   - vmware ESXi
   - Microsoft Hyper-v

#### Type 2
- These are installed in an existing operating system (host OS) instead of direcly on bare metal
- Examples:
   - VirtualBox
   - VMWare Workstation
- Comparison:
   - VirtualBox
      - Free
      - OpenSource
      - Install Anywhere - Windows, Linux, MAC
      - Snapshots & Clones
      - Run Multiple VMs
      - Networking (can create separated networks for different groups of VMs)
   - VMWare Workstation
      - Commercial
      - Install Anywhere - Windows, Linux
      - Snapshots & Clones
      - VMWare Player (free version):
         - Free
         - Windows / Linux
         - One VM at a time
         - No Snapshots
      - VMWare Fusion (free version):
         - Free
         - MAC
         - One VM at a time
         - No Snapshots

We will use Oracle VirtualBox.

## VirtualBox
- Support
   - Windows, MAC OS, Linux, Solaris.
- Resource Requirement:
   - CPU: x86 Intel or AMD
   - Memory: 512MB
   - Disk space: 30MB
- Recommended Resources:
   - CPU: Dual or Quadcore
   - Memory: 4GB
   - Disk space: 100GB

### Getting Preconfigured OS Virtual Disks
OSBoxes

### VM Start Type
- Normal Start: GUI or VM console will be shown, when the console is closed the VM should be
suspended
- Headless Start: Only accessible using SSH, can run in the background
- Detached Start: Normal mode that doesn't require os suspend the VM when closing the console

In order to connect to SSH the VM has to have SSH enabled and have an IP address assigned:
```sh
# In a CentOs machine
$ ip addr show

# To set the ip address
$ ip addr add 192.168.1.10/24 dev eth0

# To check the ssh service
$ sudo systemctl status sshd      
● ssh.service - OpenBSD Secure Shell server
     Loaded: loaded (/lib/systemd/system/ssh.service; enabled; vendor preset: enabled)
     Active: active (running) since Thu 2024-03-14 12:31:52 -05; 1h 3min ago
       Docs: man:sshd(8)
             man:sshd_config(5)
    Process: 1166 ExecStartPre=/usr/sbin/sshd -t (code=exited, status=0/SUCCESS)
   Main PID: 1180 (sshd)
      Tasks: 1 (limit: 38054)
     Memory: 3.2M
        CPU: 13ms
     CGroup: /system.slice/ssh.service
             └─1180 "sshd: /usr/sbin/sshd -D [listener] 0 of 10-100 startups

```

For connecting to a VM using SSH, in this example the VM is listening on port 2222:
```sh
$ ssh root@192.168.1.2 -p 2222

```

### Networking and Troubleshooting Network
- Host-Only Network
   - You can create a Host-Only Network in the Host Network Manager
   - A network for internal VM interconnectivity only, there is not internet connectivity, but it's
   possible with IP Forwarding
   - You can attachh a VM adapter to an internal network in the adapter configuration section
   - The VMs cannot access the outside world
- NAT (Address Translation)
   - The VMs can access the outside world so there is Internet connectivity
   - No systems on the outside world can access to the VMs inside
   - NAT doesn't allow inter VM communication, a NAT network will support it
   - Support Port Forwardding
- Bridge
   - The VMs take an IP address, in the same range as the host machine, so there is Internet
   connectivity

## Vagrant
Vagrant allows to us to create different configurations in VMs with a single `vagrant up` command.
Examples of VM configuration tasks that Vagrant can carry on:
- Download
- Create the VM
- Create Networks
- Configure Networking
- Configure Port Forwarding
- Boot up VM

Vagrant is usefull when deploying and managing multiple VMs.

A Vagrant Box is a Vagrant term and it refers to a package format of a Vagrant environment. It
contains an OS image as well as scripts required to configure the environment.

You can find the available Vagrant boxes at
[https://app.vagrantup.com/boxes/search](https://app.vagrantup.com/boxes/search).

```sh
$ mkdir vagrant-box-centos-7 && cd $_

# Initialize the Vagrant box in the current directory and creates a Vagrant file, which contains
# instructions on customizing a box.
$ vagrant init centos/7

# To get help
$ vagrant
Usage: vagrant [options] <command> [<args>]

    -h, --help                       Print this help.

Common commands:
     autocomplete    manages autocomplete installation on host
     box             manages boxes: installation, removal, etc.
     cloud           manages everything related to Vagrant Cloud
     destroy         stops and deletes all traces of the vagrant machine
     global-status   outputs status Vagrant environments for this user
     halt            stops the vagrant machine
     help            shows the help for a subcommand
     init            initializes a new Vagrant environment by creating a Vagrantfile
     login           
     package         packages a running vagrant environment into a box
     plugin          manages plugins: install, uninstall, update, etc.
     port            displays information about guest port mappings
     powershell      connects to machine via powershell remoting
     provision       provisions the vagrant machine
     push            deploys code in this environment to a configured destination
     rdp             connects to machine via RDP
     reload          restarts vagrant machine, loads new Vagrantfile configuration
     resume          resume a suspended vagrant machine
     serve           start Vagrant server
     snapshot        manages snapshots: saving, restoring, etc.
     ssh             connects to machine via SSH
     ssh-config      outputs OpenSSH valid configuration to connect to the machine
     status          outputs status of the vagrant machine
     suspend         suspends the machine
     up              starts and provisions the vagrant environment
     upload          upload to machine via communicator
     validate        validates the Vagrantfile
     version         prints current and latest Vagrant version
     winrm           executes commands on a machine via WinRM
     winrm-config    outputs WinRM configuration to connect to the machine

For help on any individual command run `vagrant COMMAND -h`

Additional subcommands are available, but are either more advanced
or not commonly used. To see all subcommands, run the command
`vagrant list-commands`.
        --[no-]color                 Enable or disable color output
        --machine-readable           Enable machine readable output
    -v, --version                    Display Vagrant version
        --debug                      Enable debug output
        --timestamp                  Enable timestamps on log output
        --debug-timestamp            Enable debug output with timestamps
        --no-tty                     Enable non-interactive output

# To start the Vagrant box run vagrant up
$ vagrant up

```

### Vagrant File
When deploying a Vagrantfile in different environments, the result created box and configuration
will be exactly the same: 
```Vagrantfile
# A Vagrantfile starts with a configuration block
Vagrant.configure("2") do |config|
   # Inside of it the image for the box is specified
   config.vm.box = "centos/7"

   # For example for configuring port forwarding
   config.vm.network "forwarded_port", guest: 80, host: 8080

   # Now let's configure a directory to sync between the host and the VM
   config.vm.synced_folder "./box_data", "/vagrant_data"

   # For configuring the CPU and memory of the VM you can do it in the config.vm.provider block
   config.vm.provider "virtualbox" do |vb|
      vb.memory = "1024"
   end

   # Using the shell provision block, you can also configure a simple shell script to run at boot
   # up
   config.vm.provision "shell", inline: <<-SHELL
      apt-get update
      apt-get install -y apache 2
   SHELL
end

```

Before running `vagrant up`, be sure to create and add to gitignore the `box_data` directory, this
is a custom name nothing standard:
```sh
$ mkdir -p 

```

Also to be able to work with folder mounts like the box_data directory, be sure of install the
`vagrant-vbguest` plugin in the host machine:
```sh
$ vagrant plugin install vagrant-vbguest

$ vagrant reload

```

When running the `vagrant up` command now, Vagrant provisions a VM following the specifications
given in this Vagrant file:
```sh
$ vagrant up

```

To use different providers you can use:
```sh
$ vagrant up [PROVIDER_NAME]

```

Vagrant supports different providers:
- VirtualBox
- VMWare
- Hyper-V
- Docker
- Custom

Once the VM has started we can connect through SSH to the VM using the following command, it also
automatically authenticates you inside of the VM:
```sh
# To connect using SSH
$ vagrant ssh
[vagrant@localhost ~]$ cat /etc/*release*
CentOS Linux release 7.9.2009 (Core)
Derived from Red Hat Enterprise Linux 7.9 (Source)
NAME="CentOS Linux"
VERSION="7 (Core)"
ID="centos"
ID_LIKE="rhel fedora"
VERSION_ID="7"
PRETTY_NAME="CentOS Linux 7 (Core)"
ANSI_COLOR="0;31"
CPE_NAME="cpe:/o:centos:centos:7"
HOME_URL="https://www.centos.org/"
BUG_REPORT_URL="https://bugs.centos.org/"

CENTOS_MANTISBT_PROJECT="CentOS-7"
CENTOS_MANTISBT_PROJECT_VERSION="7"
REDHAT_SUPPORT_PRODUCT="centos"
REDHAT_SUPPORT_PRODUCT_VERSION="7"

CentOS Linux release 7.9.2009 (Core)
CentOS Linux release 7.9.2009 (Core)
cpe:/o:centos:centos:7

```

```sh
# To check the VM status
$ vagrant status

# To shut down the VM
$ vagrant halt

```

Now let's update the Vagrantfile with the following configurations:
```Vagrantfile
Vagrant.configure("2") do |config|
  config.vm.box = "centos/7"
  config.vm.network "forwarded_port", guest: 80, host: 8080
  config.vm.synced_folder "./box_data", "/vagrant_data"

  # We can also configure the timeout in which vagrant waits for this VM to start
  config.vm.boot_timeout=600

config.vm.provider "virtualbox" do |vb|
    vb.memory = "1024"

    # Let's add the following configuration to set the number of CPUs to 2
    vb.cpus = 2

    # Let's rename the VM instead of the default name
    vb.name = "my_centos_vm"
  end

  config.vm.provision "shell", inline: <<-SHELL
  apt-get update
  apt-get install -y apache 2
  SHELL
end

```

For these changes to take effect you must run the `vagrant reload` command and wait for it to boot
up.

## Linux Networking

## JSON/YAML
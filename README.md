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

### VI Editor
### Package Management
### Service Management

## SETUP LAB ENVIRONMENT

## VirtualBox

## Vagrant

## Linux Networking

## JSON/YAML
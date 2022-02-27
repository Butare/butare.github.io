# Linux Command lines

## Using Linux Help resources

### check shells
- `$ cat /ect/shells`

### check the info:
- `$ info [command]` e.g: `$ info wget`


### check how a command is interpreted
- `$ type [command]` e.g: `$ type wget`


### check a particular user
- `$ cat /etc/passwd`


### commands
- `$ ls -a` or `$ ls --all`
- `$ ls -lh` : file details with size in human readable format


### search  the linux file system

- Find file names quickly:
	- `$ locate [any-string]` e.g: `$ locate adduser`


- Standard streams

| Designation | Numeric code|
|-------------|-------------|
|stdin        |       0     |
|stdout       |       1     |
|stderr       |       2     |


To stream the error from a particular command, e.g; `wget` command:
- `$ wget invalid.com 2>error.txt` : only error text will be redirected into error.txt

Notice: 
- `> overwrites the output, `
- `>> appends the output`



### Working with Archives

- To unpack the archive: `tar xzf [archived-dir]`. e.g: `tar xzf latest.tar.gz`
- To pack the dir in archive: `tar czf [new-archive-name] [dirname]`. e.g: `tar czf newarchive.tar.gz wordpress/`


### Network configuration
- `$ netstat -l` : list all open ports
- `$ route` : show routing table



# Getting started with Linux System Administration

Prereq.: linux network connectivity

## Linux system monitoring

### Why monitor?
- Understand capacity usage
- Indetify (and terminate) rogue processes

- Check process info created at runtime: `cd /proc`, check: `cd /proc/meminfo`
- Check how CPU, Memory, ... is used: `$ top` 
- Check the space available: `$ free` or `$ free -h` (show results in GB or MB)
- Chech how file system disk space is used: `$ df` or `$ df -t ext4` or `$ df -ht ext` (results in GB or MB)
- Check the network traffic:
	1. install iftop `apt install iftop` (on derbian distro) 	
	2. check network interface name: `$ ifconfig` (eg. of I/F name: `eth0`
	3. check the traffic on network I/F: `$ iftop -i [I/F name]` e.g: `$ iftop -i eth0`


### Managing System process
- Check processes in the current shell: `$ ps`
- Check processes in the entire system: `$ ps aux`
- check system log: `$ cd /var/log/`


### Working with Users and Groups in Linux
- Check users, groups, ...: `$ less /etc/shadow`
- Check users: `$ less /etc/passwd`
- Check groups: `$ less /etc/group`
- Check the details of a user: `$ id [user-name]`
- Check user who currently logged in: `$ who`
- Check user who currently logged in & what currently doing: `$ w`
- Check the user who logged in since the begin of the month: `$ last | less`

Create/Delete user / group:
- Create a user: `$ useradd -m [username]`
- By default all files from `/ect/skel/` will be added into the user home dir. i.e; if you want to assign files to the user during the creation, put it in `/etc/skel`

- Create a group: `$ groupadd [group-name]` e.g: `$ groupadd secret-group`
- assign a dir to a group: `$ chown :[group-name] [dir-name]`. e.g: `chown :secret-group /var/newDir`
- add a user to a group: `$ usermod -a -G [group-name] [user-name]` , eg: `$ usermod -a -G secret-group james` // `-a`: add, `-G`: group
- Give write permission to the folder: `$ chmod g+w /var/secret`

### Securing Your Linux Server
- switch to an other user: `$ su [user-name]`
- Grant `Other` group, execute permission: `$ chmod o+x [dir or file-name]`

#### Applying Object permissions

- Services Access Controll
	- Firewall,
	- Closing Port 
		- Check the opened ports: `$ nmap -v -sT localhost`
	- Hardening 


## Optimization


## Network security


## Encryption
- Creating TLS certificate for free: https://certbot.eff.org/


## Working with Docker and Linux Containers

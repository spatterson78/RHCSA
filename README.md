# RHCSA Exam Notes

These were my personal notes from my study time for the RHCSA Exam. Please note the following items:

- As with all Red Hat performance-based exams, configurations must persist after reboot without intervention.
- These notes and exam topics were based off the RHCSA objectives from January 2022 using RHEL8.

# Exam Obj: Understand and use essential tools

- Access a shell prompt and issue commands with correct syntax

- Use input-output redirection (>, >>, |, 2>, etc.)
    
	```
    # cat /etc/hosts > /tmp/hosts
    # cat /etc/hosts >> /tmp/hosts
    # ps -ef | grep sshd 
    ```
	
- Use grep and regular expressions to analyze text
	
    ```
   # grep -i
    * ignore case in string
	# grep -v
    * omit string
	# grep -ir file /usr/share/doc
    ```
	
- Access remote systems using SSH
    ```
	# ssh user@host
	# ssh-keygen -t rsa
	# ssh-copy-id -i id_rsa.pub user@host
	# ssh user@host cat /etc/redhat-release
    ```
	
- Log in and switch users in multiuser targets

    ```
	# su -
	# sudo su - <user>
	# sudo -i
    ```
	
- Archive, compress, unpack, and uncompress files using tar, star, gzip, and bzip2

    ```
	# tar cvf <archive_name.tar> <dir/file(s)_to_backup>
	# tar cvfz <archive_name.tar.gz> <dir/file(s)_to_backup>
	# tar tvf <archive_name>
	# tar xvfz <archive_name>
	# tar xvfj <archive_name>
	# star -cv file=<archive_name.star> <dir/file(s)_to_backup>
	# star -cv -bz file=<archive_name.star.bz2> <dir/file(s)_to_backup>
	# star -tv file=<archive_name>
	# gzip <archive_name>
	# bzip2 <archive_name>
	# bzip2 -d <archive_name>
    ```
	
- Create and edit text files

    ```
	# touch file
	# > file
	# vim file
    ```
	
- Create, delete, copy, and move files and directories

    ```
	# scp <src> <dest>
	# sftp <src>
	# mkdir
	# rmdir or rm -rf
	# mv
	# cp
    ```
	
- Create hard and soft links

    ```
	# ln <src> <dest>
	# ln -s <src> <dest>
    ```
	
- List, set, and change standard ugo/rwx permissions

    ```
	# chmod
	# chgrp
	# umask
    ```
    - add `umask 0002` to users `.bashrc` to set custom umask for user
	
- Locate, read, and use system documentation including man, info, and files in /usr/share/doc

    ```
	# man
	# info
	# /usr/share/doc
	# whatis httpd
	# apropos httpd
    ```

# Exam Obj: Create simple shell scripts

- Conditionally execute code (use of: if, test, [], etc.)

    ```
	# if [ -e <filename> ]; then echo "file exists"; fi
    ```
	
- Use Looping constructs (for, etc.) to process file, command line input

    ```
	# for i in list; do echo $i; done
	# while
    ```
	
- Process script inputs ($1, $2, etc.)

    ```
	ARG1=$1
	ARG2=$2
    ```

- Processing output of shell commands within a script **double check this

    ```
	`cat <filename>`
	$(cat <filename>)
    ```
	
- Processing shell command exit codes **double check this

    ```
	EXIT_CODE=$?
    ```

# Exam Obj: Operate running systems

- Boot, reboot, and shut down a system normally

    ```
	# shutdown -h 0
	# systemctl poweroff
	# shutdown -r 0
	# systemctl reboot
    ```
	
- Boot systems into different targets manually

    ```
	# systemctl get-default
	# systemctl set-default graphical.target
	# systemctl set-default multi-user.target
	# systemctl isolate multi-user
    ```
	
- Interrupt the boot process in order to gain access to a system
	
    - Steps to reset root password

    ```
	1) reboot system to access GRUB
	2) press 'e' to edit grub
	3) go to first 'linux' line and append to the end 'rd.break'
	4) 'ctrl x' to boot
	5) mount -o rw,remount /sysroot
	6) chroot /sysroot
	7) passwd root
	8) touch /.autorelabel
	9) exit
    ```
	
	- edit grub to boot into specific target
    
	1) add the following to end of first `linux` line 
    
        ```
        systemd.unit=multi-user.target
        ```

- Identify CPU/memory intensive processes and kill processes
	
    - You can use `top` or `ps` to find PID

    ```
	# top
		r - renice PID -20-19(-20=highest prio, 19=lowest prio)
		k - kill PID
	# kill -9 PID
	# kill -15 PID
	# pgrep top
	# pkill top
    ```
	
- Adjust process scheduling

    ```
	# chrt --max
		*see available ranges for chrt
	# chrt -f -p PRIO PID
		*ex. chrt -f -p 45 12345
	# chrt -p PID
		*see current chrt info for process
	# nice -n 19 CMD
		*ex. nice -n 19 top
	# renice -n 10 PID
    ```
	
- Manage tuning profiles

    ```
	# /etc/tuned/tuned-main.conf
		# vi /etc/tuned/tuned-main.conf
		*change 'dynamic_tuning = 0' ---> 'dynamic_tuning = 1'
		# systemctl restart tuned
	# /etc/tuned/
	# /usr/lib/tuned
	# tuned-adm list
	# tuned-adm active
	# tuned-adm profile <new_profile_name>
	# tuned-adm profile <new_profile_name> <new_profile_name>
		*can try to merge two profiles
	# tuned-adm recommend
    ```

- Locate and interpret system log files and journals

    ```
	# grep systemd `find /var/log -maxdepth 1 -type f -print`
	# journalctl -u chronyd
	# journalctl -g systemd
	# journalctl -S 20:27:00 -U 20:28:00
	# journalctl --list-boots
	# journalctl -b 0
		*show boot logs for boot 0 from journalctl --list-boots
    ```

- Preserve system journals

    Missing some info here:

    ```
	# /etc/systemd/journald.conf
	# mkdir /var/log/journal
	# journalctl --flush
		*reloads things, /var/log/journal should now stuff and /run/log/journal should be gone
    ```

- Start, stop, and check the status of network services

    ```
	# systemctl status sshd
	# systemctl is-active sshd
	# systemctl enable sshd
	# systemctl is-enabled sshd
    # systemctl stop sshd
    # systemctl restart sshd
    ```
	
- Securely transfer files between systems

    ```
	# scp <src> <dest>
	# sftp <src>
    ```

# Exam Obj: Configure local storage

- List, create, delete partitions on MBR and GPT disks
	
    - PAY ATTENTION to partition table type on existing disks
    ```
	# fdisk -l
	# fdisk /dev/sdb
	# gdisk /dev/sdb
	# parted
    ```
	
- Create and remove physical volumes

    ```
	# pvcreate /dev/sdb
	# pvremove /dev/sdb
	# wipefs -a /dev/sdb  ** use this if going to reuse disk again on system
    ```
	
- Assign physical volumes to volume groups

    ```
	# vgcreate vgname /dev/sdb1
	# vgextend vgname /dev/sdb1
    ```
	
- Create and delete logical volumes

    ```
	# lvcreate -l/-L extents/size vgname
	# lvremove
    ```

- Configure systems to mount file systems at boot by universally unique ID (UUID) or label

    ```
	# blkid /dev/sdb1
	# lsblk
    ```
	
- Add new partitions and logical volumes, and swap to a system non-destructively

    ```
	# mkswap /dev/sdb1
	# swapoff /dev/sdb1
	# swapon -v /dev/sdb1
    ```

# Exam Obj: Create and configure file systems

- Create, mount, unmount, and use vfat, ext4, and xfs file systems

    ```
	# mkfs.xfs
	# mkfs.ext4
	# mkfs.vfat
		*vfat disk partitions use 'W95 FAT32 ID b' in fdisk
		*vfat install 'dosfstools'
    ```

- Mount and unmount network file systems using NFS

	- NFS Client Setup

    ```
	1) # dnf install nfs-utils
		*nfs-utils needs to be installed if not already
	2) *update firewalld
		# firewall-cmd --add-service=nfs  *might not be needed on client side, but is needed on the server
		# firewall-cmd --add-service=nfs3  *see about which to use now for RHEL8
	3) # showmount -e rhel8 - see available exports from NFS server
	4) # mount -t nfs -o <options> rhel8:/var/ftp/pub /mnt/nfs
	5) # vim /etc/fstab
			rhel8:/var/ftp/pub      /mnt/nfs        nfs     _netdev 0 0
    ```
	
	- Autofs Client Setup
	    - config files
		    - /etc/auto.master
		    - /etc/autofs.conf
    
    ```
	1) # dnf install autofs
		*need to install autofs if not already
	2) # vim /etc/auto.master
		*add new auto mount to master
		/exports	/etc/auto.exports
	2) # vim /etc/auto.exports
		*create new map file for autofs
		mount-point options location
		auto	rhel8:/exports (this will make it so this is mounted as /exports/auto)
	3) # systemctl enable autofs
	4) # systemctl restart autofs
	5) # ll -ahtr /exports/auto/spatterson
		*need to access the share before it will show up
    ```
	
- Extend existing logical volumes

    ```
	# lvextend [options] <lvname>
		*ex. lvextend -l +100 /dev/mapper/lvol
		*ex. lvextend -L +1G /dev/mapper/lvol
    ```

- Create and configure set-GID directories for collaboration

    ```
	# groupadd <new_group>
		*create group if needed
	# mkdir <dir_name>
		*create dir if needed
	# chgrp -R <group> <dir>
		*update perms on dir
	# chmod 770 <dir>
	# chmod g+s <dir>
		*sets GID on dir
    ```

- Configure disk compression (VDO) - Good for things with multiple copies of same files
	
    - Install VDO, Create VDO device, Use VDO device
	
        - Three sizes to be aware of with VDO:
		    - Physical Size
			    - size of the underlying block device, used for user data and VDO metadata
		    - Available Physical Size
			    - the portion of the physical size that is available for user data
		    - Logical Size
			    - the provisioned size that VDO presents to users and applications, generally larger than the available physical size
	
	- Persitant VDO mount in /etc/fstab - remember the options!!!

        ```
		/dev/mapper/vdo1	/mnt/vdo1	xfs		defaults,x-systemd.requires=vdo.service		0 0
        ```
	
	- Configure VDO

        ```
		1) # dnf install vdo
			*install 'vdo' if not installed
		2) # systemctl enable --now vdo
		3) # systemctl status vdo
		4) # vdo create --name=<vdo_name> --device=<blockdevice> --vdoLogicalSize=<size>
				*ex. # vdo create --name=vdo_storage --device=/dev/sdc --vdoLogicalSize=20G
		5) # vdostats --human-readable
		6) # mkfs.xfs -K /dev/mapper/vdo_storage
		7) # udevadm settle
		8) # vdostats --human-readable
		9) # vim /etc/fstab
			/dev/mapper/vdo_storage         /vdo-storage    xfs     defaults,x-systemd.requires=vdo.service         0 0
        ```
			
	- Configure LVM on VDO

        ```
		1) steps 1-4 same as "Configure VDO"
		2) # pvcreate /dev/mapper/<vdo_name>
			*ex. # pvcreate /dev/mapper/vdo_storage
		3) # vgcreate <vgname> <blockdevice>
			*ex. # vgcreate vdo_vg /dev/mapper/vdo_storage
		4) # lvcreate -L 5G -n vdo1 vdo_vg
		5) # mkfs.xfs /dev/mapper/
		6) # vim /etc/fstab
			/dev/mapper/vdo_storage-vdo1	/vdo-storage	xfs		defaults	0 0
        ```

- Manage layered storage - Stratis
	
    - Three things:
		1) block devive
		2) Stratis Pool
			- `/stratis/<poolname>`
			- one or more file systems can be in one pool
			- pool size is sum of block devive(s)
		3) Stratis file system
			- thin provisioned
			- snapshots
			- xfs
			- don't manage with xfs tools
	
	- Persitant Stratis mounts in /etc/fstab - remember the options!!!

        ```
		/dev/stratis/pool1/fs1	/mnt/stratisfs1		xfs		defaults,x-systemd.requires=stratisd.service	0 0
        ```
	
	- Configure Stratis

        ```
		1) # dnf install stratisd stratis-cli
		2) # systemctl enable --now stratisd
		3) # stratis pool create <poolname> <blockdevice>
			*ex. # stratis pool create teststratis /dev/sdb
		4) # stratis blockdev
			*show configured block devives in Stratis
		5) # stratis fs
			*show Stratis file systems
		6) # stratis fs create <poolname> <fsname>
			*ex. # stratis fs create teststratis stratisfs1
		7) # stratis fs
			*very new Stratis file system
		8) # mkdir <dir_name>
		9) # vim /etc/fstab
			/dev/stratis/teststratis/stratisfs1		/stratisfs1		xfs		defaults,x-systemd.requires=stratisd.service	0 0
        ```

	- Add additional block device(s) to existing Stratis Pool

        ```
		1) # stratis pool add-data <poolname> <blockdevice>
        ```
		
	- Stratis Snapshots

        ```
		1) # stratis fs
		2) # stratis fs snapshot <poolname> <fsname> <snapshot_name>
			*ex. # stratis filesystem snapshot teststratis stratisfs1 stratisfs1-snapshot
		3) # stratis fs rename <poolname> <fsname> <fs_new_name>
			*use this to restore from a snapshot
		4) remount stratis file system to load snapshot from step 3
        ```
		
		
- Diagnose and correct file permission problems

# Exam Obj: Deploy, configure, and maintain systems

- Schedule tasks using at and cron
	- /var/spool/cron
	- /etc/crontab
	- /etc/cron.d

    ```
	# at - requires 'at' package for atd service
		*ex. create at job for 7PM
			# at 19:00
				at> tar cvfz /tmp/backup.tar.gz /var/www >> /tmp/backup.log 2>&1
				at> ctrl+d to exit
	# atq - view scheduled at jobs
	# atrm <job_number>
	# crontab - requires 'cronie' package for crond service
		min   hr  dom  mon  dow  command-to-run
		0-59 0-23 1-31 1-12 0-6
    ```
	
- Start and stop services and configure services to start automatically at boot
	- /usr/lib/systemd/system/
	- /run/systemd/system/
	- /etc/systemd/system/

    ```
	# systemctl status/stop/start/restart <service>
	# systemctl enable/disable/umask/unmask <service>
    ```
	
- Configure systems to boot into a specific target automatically

    ```
	# systemctl get-default
		* gets default target
	# systemctl set-default graphical.target
	# systemctl set-default multi-user.target
	# systemctl isolate multi-user
    ```
	
- Configure time service clients

    ```
	# dnf install chronyd
	# vim /etc/chrony.conf
	# systemctl enable --now chronyd
	# chronyc sources -v|serverstats|tracking|makestep
	# ntpstat
    ```
	
- Install and update software packages from Red Hat Network, a remote repository, or from the local file system
	- Create new repo auto or manually

    ```
	# dnf config-manager --add-repo <url>
	# vim /etc/yum.repos.d/<name>.repo
		[name]
		name=long description of repo
		baseurl=ftp://|https://
		gpgcheck=0
		enabled=1
	# dnf install <package-name>
	# dnf install --downloadonly --downloaddir <DIR> <package-name>
	# dnf check-update
	# dnf update
    ```
	
- Work with package module streams

    ```
	# dnf module list
	# dnf module list <module-name>
		* # dnf module list php
	# dnf module install php:7.4/devel
	# dnf module list --installed
	
	* if need to change to different module
		# dnf module reset <module-name>
		# dnf module install php:7.3/devel
			* dnf will handle the downgrade from php7.4 to php7.3 automatically
    ```
	
- Modify the system bootloader
	- Grub2
	- /etc/default/grub
		- some possible options that could be added
            ```
			GRUB_TIMEOUT_STYLE=hidden
			GRUB_SERIAL_COMMAND=serial
            ```
	- /etc/grub.d/
	- /boot/grub2/grub.cfg
    ```
	# grub2-mkconfig  ** updates /boot/grub2/grub.cfg when we make changes to /etc/default/grub
		*ex. # grub2-mkconfig -o /boot/grub2/grub.cfg
    ```
	
# Exam Obj: Manage basic networking

- Configure IPv4 and IPv6 addresses

    ```
	# nmtui
	# nmcli
		* # nmcli connection add con-name "<connection-name>" ifname "eth1" type "ethernet"
		* # nmcli connection modify enp0s8 ipv4.method "manual" ipv4.addresses "192.168.1.200" ipv4.dns "8.8.8.8,8.8.4.4" ipv4.gateway "192.168.1.1"
	# ip addr del xxx.xxx.xxx.xxx/xx dev eth1
		* sometimes might need to manually release an IP if Network Manager won't
    ```
	
- Configure hostname resolution
	- configure DNS servers via nmtui or nmcli
	- /etc/hosts
	
- Configure network services to start automatically at boot
	- NetworkManager service should be enabled 
	- ensure interfaces have "ONBOOT=yes" in config file
	
- Restrict network access using firewall-cmd/firewall ** Look into this one more, might be blocking by network or protocol, rich rules?
	- firewalld

    ```
	# man firewalld.richlanguage
		* this will provide example of some rich rules to restrict access
	# firewall-cmd --list-all
	# firewall-cmd --zone=<zone-name> --list-all
	# firewall-cmd --add-servcie=nfs --permanent
	# firewall-can --all-list-zones
	# firewall-cmd --get-zones
	# firewall-cmd --new-zone <new-zone-name> --permanent
	# firewall-cmd --change-interface=eth1 --zone=<zone-name> --permanent
	# firewall-cmd --get-active-zones
	# firewall-cmd --set-default-zone=<zone-name>
    ```

### Notes:
- man 7 nmcli-examples  ** see examples for setting up bond/team
- enable ipv4 forwarding /etc/sysctl.conf
    - net.ipv4.ip_forward=1

# Exam Obj: Manage users and groups

- Create, delete, and modify local user accounts

    ```
	# useradd <username>
	# userdel <username>
	# usermod <username>
	# chage [OPTIONS] <username>
    ```
	
- Change passwords and adjust password aging for local user accounts

    ```
	# passwd <username>
	# chage [OPTIONS] <username>
    ```
	
- Create, delete, and modify local groups and group memberships

    ```
	# groupadd [OPTIONS] <groupname>
	# groupdel 
	# groupmod
	# usermod -G wheel <username>
    ```
	
- Configure superuser access
	- /etc/sudoers
	- wheel group

    ```
	# visudo
    ```

# Exam Obj: Manage security

- Configure firewall settings using firewall-cmd/firewalld
	- firewalld

    ```
	# firewall-cmd --list-all
	# firewall-cmd --add-service=nfs --permanent
    ```
	
- Create and use file access control lists
	- ACLs

    ```
	# getfacl
	# setfacl
		*ex. # setfacl -Rm d:g:group-name:rwx,g:group-name:rwx /dir
		*ex. # setfacl -Rm d:g:username:rwx,u:username:rwx /dir
		*ex. # setfacl -Rm d:u:username:r--,u:username:r-- /dir
    ```
		
- Configure key-based authentication for SSH

    ```
	# ssh-keygen -t rsa
	# ssh-copy-id -i id_rsa.pub user@host
    ```
	
- Set enforcing and permissive modes for SELinux
	- /etc/selinux/config

    ```
	# sestatus
	# getenforce
	# setenforce [ Enforcing|Permissive or 0|1 ]
    ```
	
- List and identify SELinux file and process context
	- Z - flag in ls and ps

    ```
	# ll -ahtrZ
	# ps -efZ
    ```
	
- Restore default file contexts
    - install `setroubleshoot-server`, I believe this will install `policycoreutils` as a dependency to get `restorecon` command

    ```
	# restorecon -Rv /dir
    ```
	
- Use boolean settings to modify system SELinux settings

    ```
	# semanage port -l |grep httpd
        * see what context is assoicated with ports
	# semanage fcontext -l |grep httpd
	# semanage boolean -l |grep cifs
	# getsebool -a |grep cifs
	# setsebool -P <boolean> on|off
        * get info from getsebool -a
	# semanage fcontext -a -t httpd_sys_content_t "/dir(/.*)?"
		* run 'restorecon -Rv /dir' after changing fcontext
	# semanage port -a -t http_port_t -p tcp 3131
    ```

- Diagnose and address routine SELinux policy violations
    - install `setroubleshoot-server` to get `sealert` command

    ```
	# sealert -a /var/log/audit/audit.log
	# sealert -a /var/log/messages
	# audit2why -a
	# audit2allow -aw
	# semanage fcontext -a -t httpd_sys_content_t "/dir(/.*)?"
		* run 'restorecon -Rv /dir' after changing fcontext
    ```
	
# Exam Obj: Manage containers
	
- Find and retrieve container images from a remote registry

    ```
	# podman login registry.access.redhat.com
	# podman search registry.access.redhat.com/ubi8
        * search registry for UBI image
	# podman pull registry.access.redhat.com/ubi8/ubi:latest
        * append ':latest' to image to pull the latest available
	# podman rmi registry.access.redhat.com/ubi8/ubi:latest
    ```

- Inspect container images

    ```
	# podman images
        * this will show images in local storage
	# podman inspect registry.access.redhat.com/ubi8/ubi
        * inspect local images
	# skopeo inspect docker://registry.access.redhat.com/ubi8/ubi:latest
        * inspect remote images
    ```

- Perform container management using commands such as podman and skopeo

    ```
	# podman info
        * this will show the podman configuration
	# podman login registry.access.redhat.com
	# podman search registry.access.redhat.com/ubi8
        * search registry for UBI image
	# podman rm <CONTAINER ID>
    ```

- Perform basic container management such as running, starting, stopping, and listing running containers

    ```
	# podman run -it registry.access.redhat.com/ubi8/ubi:latest
        * this run the image in interactive mode, you are logged into the container
	# podman run -d -p 8000:8080 registry.access.redhat.com/rhscl/httpd-24-rhel7
        * this run the image in the background and assign ports from container to host
	# podman run -it registry.access.redhat.com/rhscl/httpd-24-rhel7:latest /bin/bash
	# podman run -d --name <set-container-name> <image>
	# podman start <CONTAINER ID>
	# podman start --name <set-container-name>
	# podman stop <CONTAINER ID>
	# podman ps - show only running containers
	# podman ps -a - show all containers
	# podman exec -it <CONTAINER ID> <CMD>
    ```
	
- Run a service inside a container

    ```
	# podman run -d -p 8000:8080 registry.access.redhat.com/rhscl/httpd-24-rhel7 
		* this run an apache web server using port 8080 inside the container mapped to system port 8000
    ```

- Configure a container to start automatically as a systemd service
	* check this if think SELinux is causing not to work
    
    ```
		* getsebool -a |grep container
		* setsebool -P container_manage_cgroup on
    ```
	
    ```
	1) ~/.config/systemd/user - Unit files for user rootless container, create directory structure
	2) create any necessary directories and files to be used for persistent storage, if needed
	3) # podman run -d --name
		* podman run -d --name web_container -p 8000:8080 -v ~/container_test:/var/www/html:Z registry.access.redhat.com/rhscl/httpd-24-rhel7
	4) # podman generate systemd --name <container-name> --files --new
		   * podman generate systemd --name web_container --files --new
	5) # podman stop <CONTAINER ID>
		   * podman stop web_container
	6) # podman rm <CONTAINER ID>
		   * podman rm web_container
	7) # loginctl enable-linger - enable persistent non-root systemd container for user
	8) # loginctl show-user <username> - verify 'Linger=yes' is now set
	   # loginctl disable-linger - to disable persistent non-root systemd container for user
    9) # systemctl --user daemon-reload
   10) # systemctl --user start|stop|enable|status <UNIT>
		   * systemctl --user enable container-web_container.service
    ```

- Attach persistent storage to a container
	* -v flag to specify storage
	* create directory where files/data will be stored and place files there

    ```
	# podman run -d --name web_container -p 8000:8080 -v ~/container_test:/var/www/html:Z registry.access.redhat.com/rhscl/httpd-24-rhel7
    ```

### Notes:
- install `container-tools` module

    ```
	# dnf module install container-tools
    ```

- Container CLI commands
	* podman - Used to directyly manage containers and container images
	* skopeo - Used to inspect, copy, delete and sign container images
	* buildah - Used to create new container images

- Container Registries
	* registry.redhat.io - Official Red Hat container images, requires account to access
	* registry.connect.redhat.com - Third-Party container images, requires account to access
	* /etc/containers/registries.conf
	* /etc/containers/registries.d


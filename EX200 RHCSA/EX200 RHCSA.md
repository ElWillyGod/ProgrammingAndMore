# EX200 RHCSA

- [[Tips and tricks]]

# EJERCICIO 1

Configure node 1 to have the following network configuration:
Hostname: node1.net8.example.com
IP address: 172.248.1.0
Netmask: 255.255.255.0
Gateway: 172.248.1.254
Name server: 172.248.1.254

> [!example]- Solucion
> ```bash
> 
> ```

# EJERCICIO 2

Configure your system to use default repositories.
DNF repositories have been made available from
http://server1.net.example.com/BaseOS and
http://server1.net.example.com/AppStream
For BaseOS and AppStream
http://content.example.com/rhel9.0/x86_64/dvd/BaseOS http://content.example.com/rhel9.0/x86_64/dvd/AppStream
Configure your systems to use these locations as default repositories.
http://content.example.com/rhel9.3/x86_64/dvd/BaseOS/content.example.com/rhel9.3/x86_64/dvd/AppStream/

> [!example]- Solucion
> ```bash
> 
> ```

# EJERCICIO 3

Debug SELinux
A web server running on non-standard port 82 is having issues serving content. Debug and fix the issues as necessary so that:
The web server on your system can serve all the existing web files from /var/www/html (Note: Do not remove or otherwise alter the existing file content).
The web server serves this content on port 82.
The web server starts automatically at system boot time.
When you run the command:
`curl nodel.net.example.com:82/file1`
it should return:
X200 Testing

> [!example]- Solucion
> ```bash
> 
> ```

# EJERCICIO 4

Create the following users, groups, and group memberships:

- A group named sysadmin
- A user named natasha who belongs to sysadmin as a supplementary group
- A user named harry who also belongs to sysadmin as a supplementary group
- A user sarah who does not have access to an interactive shell on the system, and who is not a member of the group sysadmin
- Natasha, harry, and sarah should all have the password of "thuctive"

> [!example]- Solucion
> ```bash
> 
> ```

# EJERCICIO 5

The user natasha must configure a cron job that runs daily at 1423 local time
and executes
`/bin/echo hiya`

> [!example]- Solucion
> ```bash
> 
> ```

# EJERCICIO 6

Create a collabroative directory /home/materials with the following
characteristics:

- Group ownership of /home/materials is sysadmin
- The directory should be r,w, and acesible to members of sysadmin, but not to any other user
- Files created in /home/materials automatically have group oweenership set to the sysadmin group

> [!example]- Solucion
> ```bash
> 
> ```

# EJERCICIO 7

Configure your system to act as an NTP client for the time server
utility.net8.example.com

> [!example]- Solucion
> ```bash
> 
> ```

# EJERCICIO 8

Configure autofs to automount the home directories of remote user as follows:
utility.net8.example.com(173.24.8.100) NFS-exports /netdir to your system.
This fs contains a pre-configured home directory for the user remoteuser8
remoteuser8's home directory is utility.net8.example.com:/netdir/remoteuser8

remoteuser8's home directory should be automonted locally beneath /netdirr
as /netdir/remoteuser8
home directories must be writable by their users remoteuser8's password is
thuctive

> [!example]- Solucion
> ```bash
> 
> ```

# EJERCICIO 9

Create a user talusan with a userid of 2112. The password for this user should
be thuctive

> [!example]- Solucion
> ```bash
> 
> ```

# EJERCICIO 10

Locate all the files owned by devops and place a copy of them in the
/root/findresults directory

> [!example]- Solucion
> ```bash
> 
> ```

# EJERCICIO 11

Find all lines in the file /user/share/mine/packages/freedesktop.org.xml that
contain the string "ich"
Put a copy of all these lines in the original order in the file /root/lines

/root/lines
should contain no empty lines and all lines must be exact copies of the original
lines in /usr/share/mine/packages/freedesktop.org.xml.

> [!example]- Solucion
> ```bash
> 
> ```

# EJERCICIO 12

Create a tar archive named /root/archive.tar.bz2 which contains the content of
/usr/local. The tararchive must be compressed using bzip2

> [!example]- Solucion
> ```bash
> 
> ```

---

# NODO 2

# EJERCICIO 13

Create a container image.
On node1 as user sia:
Using "Link que te dan en el examen", build a container image named watcher
Do not make any changes to Containerfile

> [!example]- Solucion
> ```bash
> 
> ```

# EJERCICIO 14

Confiugre a container as a service.
On node 1 create a rootless container to start automatically as a systemd service as flloews:
The container is named ascii2pdf

- The container use the watcher image that was previously created in another item
- The container runs as a systemd service as the existing user sia only
- The service is named container-ascii2pdf
- The container automatically starts after a system reboot without any manual intervention
- The local directory /opt/files is attached to the container's /opt/incoming directory
- The local directory /opt/processed is attached to the container's /opt/outgoing directory

images: registry.lab.example.com/rhel9/httpd-24

> [!example]- Solucion
> ```bash
> 
> ```

# EJERCICIO 15

create a container with name db_prueba as a services with de user sia

the imagen is `docker://registry.lab.example.com/rhel8/mariadb-103`

> [!example]- Solucion
> ```bash
> 
> ```

# EJERCICIO 16

Create a script to locate files
Create a script named mysearch to locate files under /usr
The script myserach should locate all files under /usr which are smaller than
10M in size and have set group ID SETGID permissions
Place the script myserach underr /usr/local/bin
When executed, the script mysearch should save the list of found files into
/root/myfiles

> [!example]- Solucion
> ```bash
> 
> ```

# EJERCICIO 16

Configure sudo privileges for group elite such that any user who belongs to the group elite has the ability to execute administrative commands without providing a password

> [!example]- Solucion
> ```bash
> 
> ```

# EJERCICIO 17

Set the root password
Set the root password for node2 to thuctive. You will need to gain access to the system in order to do this.

> [!example]- Solucion
> ```bash
> 
> ```

# EJERCICIO 18

Resize the logical volume vo and its filesystem to 300 MiB Make sure that the
filesystem contents remain intact.

> [!example]- Solucion
> ```bash
> 
> ```

# EJERCICIO 19

Add a swap partition
Add an additional swap partition of 512 MiB to disk /dev/vdb on your system.
The swap partition should automatically mount when your system boots. Do not remove or otherwise alter any existing swap partitions on your system

> [!example]- Solucion
> ```bash
> 
> ```

# EJERCICIO 20

Create a new logical volum according to the following requirements:

The logical volume is named database and belongs to the datastore volume group and has a size of 50 extents
Logical volumes in the datastore volume group should haver an extent size of 8 MiB
The physical volume for the logical volume must be placed on disk /dev/vdb
Format the new logical volume with a vfat filesystem. The logical volume
should be automatically mounted under /mnt/database at system boot time

> [!example]- Solucion
> ```bash
> 
> ```

# EJERCICIO 22

Create a 2GiB volume group named "myvg".

Create a 500MiB logical volume named "mylv" inside the "myvg" volume group.

The "mylv" logical volume should be formatted with the ext4 filesystem and mounted persistently on the /mylv directory.

Extend the ext4 filesystem on "mylv" by 500M.

> [!example]- Solucion
> ```bash
> 
> ```

# EJERCICIO 21

Configure system Tuning
Choose the recommended tuned profile for your system and set it as the
default

> [!example]- Solucion
> ```bash
> 
> ```

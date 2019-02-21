# SAMBA

## Parveen ASIX M06 2018-2019


## Description:

Practica with ldap,pam_host and samba.Main to config. of samba server. 

### important steps:

Make samba network to put togather from exemple name by (sambanet)
now we need add all dockker in this network

## 1 Ldap Server
	
* Dockerfile need to edit some pacquetes  (procps openldap-clients openldap-servers)
* Put database edt.org also new group and manager as admin
* Turn on server slpad	
This one my server from use
	
```
docker run --rm --network sambanet --name ldap --hostname ldap -d parveen1992/ldap
```
**cheak point**

```
[root@localhost practica]# ldapsearch -x -LLL -h 172.20.0.2 -b dc=edt,dc=org 'ou=grups'
dn: ou=grups,dc=edt,dc=org
ou: groups
ou: grups
description: Container per a grups
objectClass: organizationalunit
```

## 2 samba Server
	
* Docker line need add more packets  (procps samba samba-client openldap-clients nss-pam-ldapd authconfig pam_mount).
* Make smb.conf file 
* Now we need connection to ldap server befor we make (Ldap server).
* Start server to connect ldap server(nscd,nslcd).
* After connect server we need samba user this all user local and ldap can server for with sbmpassword.
* mkdir for every user and import give own premission.
* now start server (smbd,nmbd).
This one my server to user
	
```
docker run --rm --network sambanet --name samba --hostname samba -it parveen1992/sambahome
```

**check this**
	
```
[root@samba docker]# getent passwd pere
pere:*:5001:100:Pere Pou:/tmp/home/pere:
[root@samba docker]# getent group 1asix
1asix:*:610:user01,user02,user03,user04,user05
[root@samba docker]# smbtree         
MYGROUP
	\\SAMBA          		Samba Server Version 4.7.10
		\\SAMBA\IPC$           	IPC Service (Samba Server Version 4.7.10)
		\\SAMBA\public         	Share de contingut public
		\\SAMBA\manpages       	Documentació man del container
		\\SAMBA\documentation  	Documentació doc del container
[root@samba docker]# smbclient -L samba
Enter MYGROUP\GUEST's password: 
Anonymous login successful
	Sharename       Type      Comment
	---------       ----      -------
	documentation   Disk      Documentació doc del container
	manpages        Disk      Documentació man del container
	public          Disk      Share de contingut public
	IPC$            IPC       IPC Service (Samba Server Version 4.7.10)
Reconnecting with SMB1 for workgroup listing.
Anonymous login successful
	Server               Comment
	---------            -------
	Workgroup            Master
	---------            -------
	MYGROUP              SAMBA
```


## 3 Pamhost or cliente
	
* Docker file need add more packets (procps passwd openldap-clients nss-pam-ldapd authconfig pam_mount cifs-utils samba-client)
* Make conf. file for ldapserver connection (nsswitch.conf).
* ALso need pam system conf. for login user and mkhomedir (/etc/pam.d/system-auth.edt) .
* make file auto mount home for user by edit file (/etc/security/pam_mount.conf.xml).
* Conf. line in xml file ( <volume user="*" fstype="cifs" server="samba" path="%(USER)" mountpoint="~/%(USER)" /> )
* Start server to connect ldap server(nscd,nslcd).
This my PamHost is
	
```
docker run --rm --network sambanet --privileged --name client  --hostname client -it parveen1992/hostmountsamba
```

**check this** 
	
```
[root@client docker]# getent passwd pere
pere:*:5001:100:Pere Pou:/tmp/home/pere:


[root@client docker]# ls /mnt/

[root@client docker]# mount -t cifs -o guest //172.20.0.3/documentation /mnt

[root@client docker]# ls /mnt/
policycoreutils  xz

[root@client docker]# umount /mnt/

[root@client docker]# ls /mnt/

[root@client docker]# mount -t cifs -o guest //samba/documentation /mnt

[root@client docker]# ls /mnt/
policycoreutils  xz

[root@client docker]# mount -t cifs //samba/public /mnt -o user=pere
Password for pere@//samba/public:  ****

[root@client docker]# ls /mnt/
Dockerfile  auth.sh     ldap.conf   nsswitch.conf  startup.sh
README.md   install.sh  nslcd.conf  smb.conf
```	



### Github repo. with all of file we need for conf.(or your castom conf.). 

[Github Parveen](https://github.com/parveen1/sambahomepam)


### Dockerhub repo. links

[Docker Parveen ldap](https://hub.docker.com/r/parveen1992/ldap)

[Docker parveen sambahome](https://hub.docker.com/r/parveen1992/sambahome)

[Docker Parveen hostmountsamba](https://hub.docker.com/r/parveen1992/hostmountsamba)

### All to start

```
docker run --rm --network sambanet --name ldap --hostname ldap -d parveen1992/ldap
docker run --rm --network sambanet --name samba --hostname samba -it parveen1992/sambahome
docker run --rm --network sambanet --privileged --name client --hostname client -it parveen1992/hostmountsamba 
```

### Check this order after start all three docker to verify

```
[pere@client ~]$ su - anna
pam_mount password:
Creating directory '/tmp/home/anna'.

[anna@client ~]$ ll
total 0
drwxr-xr-x+ 2 anna alumnes 0 Feb 16 21:58 anna

[anna@client ~]$ pwd
/tmp/home/anna
```

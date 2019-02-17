# PAM
## @edt ASIX M06-ASO Curs 2018-2019

Repositori d'exemples de containers docker que utilitzen PAM

 * **hostpam:18mount** host pam amb authenticació ldap. utilitza l'ordre authconfig per
configurar l'autenticació i a més a més crea els home dels usuaris i munta un tmpfs als usuaris.
Atenció, per poder realitzar el mount cal que el container es generi amb l'opció **--privileged**.

#### Execució

```
docker run --rm --name host -h host --net ldapnet --privileged -it edtasixm06/hostpam:18auth
```

#### Configuracions

system-auth:
```
auth        optional      pam_mount.so

session     optional      pam_mkhomedir.so
session     optional      pam_mount.so 
session     [success=1 default=ignore] pam_succeed_if.so service in crond quiet use_uid
session     required      pam_unix.so
session     optional      pam_ldap.so
```

pam_mount.conf.xml (només a pere se li genera el  ramdisk):
```
<volume user="pere" fstype="tmpfs" mountpoint="~/test" options="size=10M,uid=%(USER),mode=0755" />
```


#### Utilització

```
getent passwd
getent group

# login pau
pam_mount password:
Creating directory '/tmp/home/pau'.
$ exit

# login pere
pam_mount password:
Creating directory '/tmp/home/pere'.

$ df -h
Filesystem                                                                                       Size  Used Avail Use% Mounted on
none                                                                                              10M     0   10M   0% /tmp/home/pere/test

$ mount -t tmpfs
none on /tmp/home/pere/test type tmpfs (rw,relatime,seclabel,size=10240k,mode=755,uid=5001,gid=100)
```


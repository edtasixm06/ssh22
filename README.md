# SSH server
## @edt ASIX M06-ASO Curs 2022-2023

Podeu trobar les imatges docker al Dockehub de [edtasixm06](https://hub.docker.com/u/edtasixm06/)

Podeu trobar la documentació del mòdul a [ASIX-M06](https://sites.google.com/site/asixm06edt/)

ASIX M06-ASO Escola del treball de barcelona


### SSH Images:

 * **edtasixm06/ssh22:base** Servidor ssh bàsic usat per practicar a partir del pam:ldap.
   Ha de ermetre l'accés als usuaris tant unix com d'usuaris ldap, per això utilitza de base
   la mateixa configuració usada al client de PAM. Afegit l'script *ldapusers.sh* per permetre
   la creació dels homes dels usuaris ldap.
 
``` 
docker run --rm --name ldap.edt.org -h ldap.edt.org --net 2hisx -d edtasixm06/ldap22:latest
docker run --rm --name ssh.edt.org -h ssh.edt.org --net 2hisx --privileged -d edtasixm06/ssh22:base
```

 * **edtasixm06/ssh22:sshfs** Host *client* que accedeix al servidor SSH. Aquest host client 
   està configurat per muntar els homes dels usuaris via *sshfs* del servidor SSH. S'ha 
   configurat *syste-auth* per uar *pam_mount* i configurat *pam_mount.conf.xml* per muntar
   un recurs de xarxa al home dels usuaris via *SSHFS*. 

   **Atenció:** aquesta imatge és en ralitat un *pam22:sshfs*

```
docker run --rm --name ldap.edt.org -h ldap.edt.org --net 2hisx -d edtasixm06/ldap22:latest
docker run --rm --name ssh.edt.org -h ssh.edt.org --net 2hisx -d edtasixm06/ssh22:base

docker run --rm --name sshfs.edt.org --hostname sshfs.edt.org --net 2hisx --privileged --cap-add SYS_ADMIN --device /dev/fuse  --security-opt apparmor:unconfined -it edtasixm06/ssh22:sshfs
```


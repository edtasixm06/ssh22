# SSH server
## @edt ASIX M06-ASO Curs 2022-2023

Podeu trobar les imatges docker al Dockehub de [edtasixm06](https://hub.docker.com/u/edtasixm06/)

Podeu trobar la documentació del mòdul a [ASIX-M06](https://sites.google.com/site/asixm06edt/)

ASIX M06-ASO Escola del treball de barcelona


### SSH Images:

* **edtasixm06/ssh22:sshfs**  Host *client* que accedeix al servidor SSH. Aquest host client
  està configurat per muntar els homes dels usuaris via *sshfs* del servidor SSH. S'ha
  configurat *syste-auth* per usar *pam_mount* i configurat *pam_mount.conf.xml* per muntar
  un recurs de xarxa al home dels usuaris via *SSHFS*.

  **Atenció:** aquesta imatge és en ralitat un *pam22:sshfs*
 
  **Atenció** cal usar en el container --privileged per poder fer els muntatges sshfs.

  **Atenció** al problema del known_hosts, cal que en el client PAM que munta els homes via
  sshfs abans de muntar cap home estigui al *.ssh/known_hosts* de *root* el *fingerprint* del
  servidor ssh (*ssh.edt.org*), si no el pam_mount es quedarà penjat esperant la confirmació,
  el yes, del fingerprint.
  
  **Configuració del home amb sshfs**

  Per configurar el recurs dins del home de l'usuari consultar com dfinir un recurs SSHFS del
  pam_mount.conf.xml. Recordar que cal disposar al *known_hosts* de root del fingerprint del
  host servidor ssh.
 
  També que cal assegurar-se de tenir instal·lat el paquet *sshfs* per poder usar-ne la utilitat
  de muntatge

  Posar atenció a quin dels ftxers pam_mount.conf.xml volem usar. Si volem també els recursos 
  tmpfs o no i si el servidor al que connecta està al port 2022 (aws) o al port ssh normal 22 
  (exemple amb desplegament local).

  ```
  <volume fstype="fuse"
       user="*"
       uid="5000-10000"
       path="sshfs#%(USER)@ssh.edt.org:./."
       mountpoint="~/%(USER)"
       options="port=22,nosuid,nodev,noatime,reconnect,allow_other,default_permissions,password_stdin"
       ssh="0" noroot="0" 
     />
  ```

  **exemple de verificació**

  ```
  root@sshfs:/opt/docker# su - unix01
  reenter password for pam_mount:
  unix01@sshfs:~$ ls -la
  total 24
  drwxr-xr-x 3 unix01 unix01 4096 Dec  7 11:55 .
  drwxr-xr-x 1 root   root   4096 Dec  7 11:54 ..
  -rw------- 1 unix01 unix01   12 Dec  7 11:55 .bash_history
  -rw-r--r-- 1 unix01 unix01  220 Mar 27  2022 .bash_logout
  -rw-r--r-- 1 unix01 unix01 3526 Mar 27  2022 .bashrc
  -rw-r--r-- 1 unix01 unix01  807 Mar 27  2022 .profile
  drwxrwxrwt 2 root   unix01   40 Dec  7 11:55 mytmp
  
  unix01@sshfs:~$ logout

  root@sshfs:/opt/docker# su - marta
  reenter password for pam_mount:
  $ ls -la
  total 12
  drwx--x--x 4 marta alumnes 4096 Dec  7 11:55 .
  drwx--x--x 3 marta alumnes 4096 Dec  7 11:55 ..
  drwxr-xr-x 1 marta alumnes 4096 Sep 12 00:00 marta
  drwxrwxrwt 2 root  alumnes   40 Dec  7 11:55 mytmp
  $ ls -la marta
  total 20
  drwxr-xr-x 1 marta alumnes 4096 Sep 12 00:00 .
  drwx--x--x 4 marta alumnes 4096 Dec  7 11:55 ..
  -rw-r--r-- 1 marta alumnes  220 Mar 27  2022 .bash_logout
  -rw-r--r-- 1 marta alumnes 3526 Mar 27  2022 .bashrc
  -rw-r--r-- 1 marta alumnes  807 Mar 27  2022 .profile
  ```

### Exemple implementació

```
docker run --rm --name ldap.edt.org -h ldap.edt.org --net 2hisx -d edtasixm06/ldap22:latest
docker run --rm --name ssh.edt.org -h ssh.edt.org --net 2hisx -d edtasixm06/ssh22:base
docker run --rm --name sshfs.edt.org --hostname sshfs.edt.org --net 2hisx --privileged --cap-add SYS_ADMIN --device /dev/fuse  --security-opt apparmor:unconfined -it edtasixm06/ssh22:sshfs
```





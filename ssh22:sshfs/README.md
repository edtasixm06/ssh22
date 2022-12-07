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
  

```
docker run --rm --name ldap.edt.org -h ldap.edt.org --net 2hisx -d edtasixm06/ldap22:latest
docker run --rm --name ssh.edt.org -h ssh.edt.org --net 2hisx -d edtasixm06/ssh22:base

docker run --rm --name sshfs.edt.org --hostname sshfs.edt.org --net 2hisx --privileged --cap-add SYS_ADMIN --device /dev/fuse  --security-opt apparmor:unconfined -it edtasixm06/ssh22:sshfs
```





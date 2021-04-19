## CTF TryHackme En-pass
![Screenshot from 2021-03-21 00-52-59](/assets/Screenshot%20from%202021-03-21%2000-52-59.png)

Modificamos el archivo de configuración al dominio que nos dan al inicio.
![Selection_014](/assets/Selection_014.png)

### Enumerando los puertos 
**nmap -T5 -n -p- -sS -sV -v -oN nmap.allports 10.10.190.152**
```
cat nmap.allports                                           130 ⨯
# Nmap 7.91 scan initiated Sun Mar 21 02:28:08 2021 as: nmap -T5 -n -p- -sS -sV -v -oN nmap.allports 10.10.190.152
Increasing send delay for 10.10.190.152 from 0 to 5 due to 793 out of 1981 dropped probes since last increase.
Warning: 10.10.190.152 giving up on port because retransmission cap hit (2).
Nmap scan report for 10.10.190.152
Host is up (0.17s latency).
Not shown: 65533 closed ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sun Mar 21 02:37:59 2021 -- 1 IP address (1 host up) scanned in 591.20 seconds
```

### Enumerando los archivos del servidor 

Descargamos todos los archivos del server.

```
$ wget  -r --no-parent  http://vulnnet.thm/
```
![Screenshot from 2021-03-21 01-01-58](/assets/Screenshot%20from%202021-03-21%2001-01-58.png)

Enumerando los archivos js y des ufuscando me encontre con la siguente información interesante:

![Screenshot from 2021-03-21 01-04-06](/assets/Screenshot%20from%202021-03-21%2001-04-06.png)

La url es posible que tenga un **LFI**.

**http://vulnnet.thm/index.php?referer=**

Haciendo la POC de LFI.
**http://vulnnet.thm/index.php?referer=/etc/passwd**
![Screenshot from 2021-03-21 01-06-04](/assets/Screenshot%20from%202021-03-21%2001-06-04.png)



Buscando private keys e intentando envenenar los logs del apache y el ssh.

Encontre el archivo 


**TODO**

Info de para explotar:
http://broadcast.vulnnet.thm/actions/beats_uploader.php

https://www.exploit-db.com/exploits/44250


https://www.rapid7.com/db/modules/exploit/multi/http/clipbucket_fileupload_exec/







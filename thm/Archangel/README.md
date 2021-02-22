# CTF TryHackme Archangel
![Screenshot from 2021-02-21 16-32-40](/assets/Screenshot%20from%202021-02-21%2016-32-40.png)


Empezamos con un escane de puertos rapido con nmap.
***$ nmap -T5 -n -sV -F -v -oN nmap.init 10.10.245.203***

```
# Nmap 7.91 scan initiated Sun Feb 21 18:35:58 2021 as: nmap -T5 -n -sV -F -v -oN nmap.init 10.10.245.203
Nmap scan report for 10.10.245.203
Host is up (0.18s latency).
Not shown: 97 closed ports
PORT     STATE    SERVICE VERSION
22/tcp   open     ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
80/tcp   open     http    Apache httpd 2.4.29 ((Ubuntu))
1028/tcp filtered unknown
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sun Feb 21 18:36:09 2021 -- 1 IP address (1 host up) scanned in 10.43 seconds
```
## Enumerando el servicio http

Inspeccionando el código fuente de la página encontramos el siguiente dominio:


![Workspace 1_001](/assets/Workspace%201_001.png)

Cambiamos el archivo **/etc/hosts** y agregamos el dominio.

![Workspace 1_001](/assets/Workspace%201_001_7m9yup159.png)

Encontramos un nueva aplicación virtual.

![Screenshot from 2021-02-21 16-44-37](/assets/Screenshot%20from%202021-02-21%2016-44-37.png)


Enumerando el servidor virtual **mafialive.thm**.

En robots.txt encontramos la siguiente ruta:

![Screenshot from 2021-02-21 16-46-47](/assets/Screenshot%20from%202021-02-21%2016-46-47.png)


Revisando la ruta test.php:

![Selection_005](/assets/Selection_005.png)

Hay posibilidad de poder hacer un **LFI o RFI** al sitio.

Obteniendo el archivo test.php

![Screenshot from 2021-02-21 16-57-11](/assets/Screenshot%20from%202021-02-21%2016-57-11.png)

Desencodificando el base64:

![Selection_006](/assets/Selection_006.png)

Las restricciones de la ruta son:
- Que no contenga **../..**
- Que contenga la ruta **/var/www/html/development_testing**

El wrapper perfecto seria el siguiente:

curl http://mafialive.thm/test.php?view=php://filter/**/var/www/html/development_testing**/resource=**path_archivo**

### Envenenando los logs files de apache

1. Mandar una petición con el agente de modificado.
2. Preparar una reverse shell
3. Incluir el log file de apache.

#### 1. Creando la petición con el agente de usuario modificado.


![Selection_007](/assets/Selection_007.png)

#### 2. Preparando el listener de la reverseshell

![Screenshot from 2021-02-21 17-16-27](/assets/Screenshot%20from%202021-02-21%2017-16-27.png)


#### 3. Incluimos el log file envenenado


![Screenshot from 2021-02-21 17-17-32](/assets/Screenshot%20from%202021-02-21%2017-17-32.png)

MAGIA XD

#### Estabilizando la shell
```
python3 -c "import pty; pty.spawn('bin/bash')"
ctl+z
stty raw -echo
fg
fg
export TERM=xterm-256color
```



## Escalamiento de privilegios

### Enumeración.

Lo hacemos con linpeace.
Y encontramos una vulnerabilidad en el crontab.

![Screenshot from 2021-02-21 17-38-45](/assets/Screenshot%20from%202021-02-21%2017-38-45.png)

Revisando el archivo helloworld.sh.


![Screenshot from 2021-02-21 17-41-28](/assets/Screenshot%20from%202021-02-21%2017-41-28.png)


=) 


### Explotación 


Agregamos una reverseshell al archivo helloworld.sh

![Screenshot from 2021-02-21 17-44-54](/assets/Screenshot%20from%202021-02-21%2017-44-54.png)

Listo tenemos una shell con el usuario **archangel**


## Escalamiento de privilegios horizontal


Hay un archivo con el suid activado.

![Screenshot from 2021-02-21 18-04-14](/assets/Screenshot%20from%202021-02-21%2018-04-14.png)

Examinandolo con strings.

**$strings backup**

![Selection_009](/assets/Selection_009.png)

Usa el comando cp con la ruta relativa, entonces se puede modificar la variable de entorno $PATH y elevar privilegios.

1. Creamos el payload que nos permitira elevar privilegios.

![Screenshot from 2021-02-21 20-47-16](/assets/Screenshot%20from%202021-02-21%2020-47-16.png)

3. Cambiamos la variable de entorno PATH y asignamos privilegios de ejecución

![Screenshot from 2021-02-21 20-57-36](/assets/Screenshot%20from%202021-02-21%2020-57-36.png)


MAGIA XD



































































































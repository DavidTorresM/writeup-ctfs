## Try Hack Me Year of the Owl
### Enumeración

nmap -T5 -n -F -v -oN nmap.init 10.10.227.152

```
# Nmap 7.91 scan initiated Wed Feb  3 02:35:58 2021 as: nmap -T5 -n -F -v -oN nmap.init 10.10.227.152
Nmap scan report for 10.10.227.152
Host is up (0.18s latency).
Not shown: 94 filtered ports
PORT     STATE SERVICE
80/tcp   open  http
139/tcp  open  netbios-ssn
443/tcp  open  https
445/tcp  open  microsoft-ds
3306/tcp open  mysql
3389/tcp open  ms-wbt-server

Read data files from: /usr/bin/../share/nmap
# Nmap done at Wed Feb  3 02:36:01 2021 -- 1 IP address (1 host up) scanned in 3.72 seconds
```

Tiene varios servcios de microsoft entonces es posible que sea una maquina virtual windows. 

Haciendo ping a la MV.

![Screenshot from 2021-02-03 00-39-10](../assets/Screenshot%20from%202021-02-03%2000-39-10.png)

No responde al ping, significa que si es windows por defecto tienen configurado el firewall para no responder al ping. 

Enumeramos las versiones de los servicios encontrados.

```
# Nmap 7.91 scan initiated Wed Feb  3 02:41:37 2021 as: nmap -T5 -n -sV -p 80,139,443,445,3306,3389 -v -oN nmap.version 10.10.227.152
Nmap scan report for 10.10.227.152
Host is up (0.17s latency).

PORT     STATE SERVICE       VERSION
80/tcp   open  http          Apache httpd 2.4.46 ((Win64) OpenSSL/1.1.1g PHP/7.4.10)
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
443/tcp  open  ssl/http      Apache httpd 2.4.46 ((Win64) OpenSSL/1.1.1g PHP/7.4.10)
445/tcp  open  microsoft-ds?
3306/tcp open  mysql?
3389/tcp open  ms-wbt-server Microsoft Terminal Services
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port3306-TCP:V=7.91%I=7%D=2/3%Time=601A45A2%P=x86_64-pc-linux-gnu%r(NUL
SF:L,68,"d\0\0\x01\xffj\x04Host\x20'ip-10-8-103-90\.eu-west-1\.compute\.in
SF:ternal'\x20is\x20not\x20allowed\x20to\x20connect\x20to\x20this\x20Maria
SF:DB\x20server")%r(TerminalServerCookie,68,"d\0\0\x01\xffj\x04Host\x20'ip
SF:-10-8-103-90\.eu-west-1\.compute\.internal'\x20is\x20not\x20allowed\x20
SF:to\x20connect\x20to\x20this\x20MariaDB\x20server")%r(Kerberos,68,"d\0\0
SF:\x01\xffj\x04Host\x20'ip-10-8-103-90\.eu-west-1\.compute\.internal'\x20
SF:is\x20not\x20allowed\x20to\x20connect\x20to\x20this\x20MariaDB\x20serve
SF:r")%r(TerminalServer,68,"d\0\0\x01\xffj\x04Host\x20'ip-10-8-103-90\.eu-
SF:west-1\.compute\.internal'\x20is\x20not\x20allowed\x20to\x20connect\x20
SF:to\x20this\x20MariaDB\x20server");
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Wed Feb  3 02:41:56 2021 -- 1 IP address (1 host up) scanned in 19.57 seconds
```


### Enumeración http








# CTF TryHackme Year of the Fox

## Enumeración de puertos
```
# Nmap 7.91 scan initiated Tue Feb  2 23:52:42 2021 as: nmap -T5 -n -F -sC -vv -o nmap.init 10.10.89.178
Nmap scan report for 10.10.89.178
Host is up, received syn-ack (0.17s latency).
Scanned at 2021-02-02 23:52:43 -04 for 34s
Not shown: 97 closed ports
Reason: 97 conn-refused
PORT    STATE SERVICE      REASON
80/tcp  open  http         syn-ack
| http-auth: 
| HTTP/1.1 401 Unauthorized\x0D
|_  Basic realm=You want in? Gotta guess the password!
|_http-title: 401 Unauthorized
139/tcp open  netbios-ssn  syn-ack
445/tcp open  microsoft-ds syn-ack

Host script results:
|_clock-skew: mean: 0s, deviation: 1s, median: 0s
| nbstat: NetBIOS name: YEAR-OF-THE-FOX, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
| Names:
|   YEAR-OF-THE-FOX<00>  Flags: <unique><active>
|   YEAR-OF-THE-FOX<03>  Flags: <unique><active>
|   YEAR-OF-THE-FOX<20>  Flags: <unique><active>
|   \x01\x02__MSBROWSE__\x02<01>  Flags: <group><active>
|   YEAROFTHEFOX<00>     Flags: <group><active>
|   YEAROFTHEFOX<1d>     Flags: <unique><active>
|   YEAROFTHEFOX<1e>     Flags: <group><active>
| Statistics:
|   00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
|   00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
|_  00 00 00 00 00 00 00 00 00 00 00 00 00 00
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 49093/tcp): CLEAN (Couldn't connect)
|   Check 2 (port 59755/tcp): CLEAN (Couldn't connect)
|   Check 3 (port 31506/udp): CLEAN (Failed to receive data)
|   Check 4 (port 61061/udp): CLEAN (Failed to receive data)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba 4.7.6-Ubuntu)
|   Computer name: year-of-the-fox
|   NetBIOS computer name: YEAR-OF-THE-FOX\x00
|   Domain name: lan
|   FQDN: year-of-the-fox.lan
|_  System time: 2021-02-03T03:52:46+00:00
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2021-02-03T03:52:45
|_  start_date: N/A

Read data files from: /usr/bin/../share/nmap
# Nmap done at Tue Feb  2 23:53:17 2021 -- 1 IP address (1 host up) scanned in 35.04 seconds

```

~~sajd~~
Tenemos 3 servicios activados en el target.

#### Enumerando http
Necesitamos una http autenticación.

![Screenshot from 2021-02-02 22-47-41](assets/Screenshot%20from%202021-02-02%2022-47-41.png)

#### Enumeramos smb
Usamos la herramienta  enum4linux para enumerar el servicio samba.

```
Starting enum4linux v0.8.9 ( http://labs.portcullis.co.uk/application/enum4linux/ ) on Wed Feb  3 00:45:32 2021

 ========================== 
|    Target Information    |
 ========================== 
Target ........... 10.10.89.178
RID Range ........ 500-550,1000-1050
Username ......... ''
Password ......... ''
Known Usernames .. administrator, guest, krbtgt, domain admins, root, bin, none


 ==================================================== 
|    Enumerating Workgroup/Domain on 10.10.89.178    |
 ==================================================== 
[+] Got domain/workgroup name: YEAROFTHEFOX

 ============================================ 
|    Nbtstat Information for 10.10.89.178    |
 ============================================ 
Looking up status of 10.10.89.178
	YEAR-OF-THE-FOX <00> -         B <ACTIVE>  Workstation Service
	YEAR-OF-THE-FOX <03> -         B <ACTIVE>  Messenger Service
	YEAR-OF-THE-FOX <20> -         B <ACTIVE>  File Server Service
	..__MSBROWSE__. <01> - <GROUP> B <ACTIVE>  Master Browser
	YEAROFTHEFOX    <00> - <GROUP> B <ACTIVE>  Domain/Workgroup Name
	YEAROFTHEFOX    <1d> -         B <ACTIVE>  Master Browser
	YEAROFTHEFOX    <1e> - <GROUP> B <ACTIVE>  Browser Service Elections

	MAC Address = 00-00-00-00-00-00

 ===================================== 
|    Session Check on 10.10.89.178    |
 ===================================== 
[+] Server 10.10.89.178 allows sessions using username '', password ''

 =========================================== 
|    Getting domain SID for 10.10.89.178    |
 =========================================== 
Domain Name: YEAROFTHEFOX
Domain Sid: (NULL SID)
[+] Can't determine if host is part of domain or part of a workgroup

 ====================================== 
|    OS information on 10.10.89.178    |
 ====================================== 
Use of uninitialized value $os_info in concatenation (.) or string at ./enum4linux.pl line 464.
[+] Got OS info for 10.10.89.178 from smbclient: 
[+] Got OS info for 10.10.89.178 from srvinfo:
	YEAR-OF-THE-FOXWk Sv PrQ Unx NT SNT year-of-the-fox server (Samba, Ubuntu)
	platform_id     :	500
	os version      :	6.1
	server type     :	0x809a03

 ============================= 
|    Users on 10.10.89.178    |
 ============================= 
index: 0x1 RID: 0x3e8 acb: 0x00000010 Account: fox	Name: fox	Desc: 

user:[fox] rid:[0x3e8]

 ========================================= 
|    Share Enumeration on 10.10.89.178    |
 ========================================= 

	Sharename       Type      Comment
	---------       ----      -------
	yotf            Disk      Fox's Stuff -- keep out!
	IPC$            IPC       IPC Service (year-of-the-fox server (Samba, Ubuntu))
SMB1 disabled -- no workgroup available

[+] Attempting to map shares on 10.10.89.178
//10.10.89.178/yotf	Mapping: DENIED, Listing: N/A
//10.10.89.178/IPC$	[E] Can't understand response:
NT_STATUS_OBJECT_NAME_NOT_FOUND listing \*

 ==================================================== 
|    Password Policy Information for 10.10.89.178    |
 ==================================================== 


[+] Attaching to 10.10.89.178 using a NULL share

[+] Trying protocol 139/SMB...

[+] Found domain(s):

	[+] YEAR-OF-THE-FOX
	[+] Builtin

[+] Password Info for Domain: YEAR-OF-THE-FOX

	[+] Minimum password length: 5
	[+] Password history length: None
	[+] Maximum password age: 37 days 6 hours 21 minutes 
	[+] Password Complexity Flags: 000000

		[+] Domain Refuse Password Change: 0
		[+] Domain Password Store Cleartext: 0
		[+] Domain Password Lockout Admins: 0
		[+] Domain Password No Clear Change: 0
		[+] Domain Password No Anon Change: 0
		[+] Domain Password Complex: 0

	[+] Minimum password age: None
	[+] Reset Account Lockout Counter: 30 minutes 
	[+] Locked Account Duration: 30 minutes 
	[+] Account Lockout Threshold: None
	[+] Forced Log off Time: 37 days 6 hours 21 minutes 


[+] Retieved partial password policy with rpcclient:

Password Complexity: Disabled
Minimum Password Length: 5


 ============================== 
|    Groups on 10.10.89.178    |
 ============================== 

[+] Getting builtin groups:

[+] Getting builtin group memberships:

[+] Getting local groups:

[+] Getting local group memberships:

[+] Getting domain groups:

[+] Getting domain group memberships:

 ======================================================================= 
|    Users on 10.10.89.178 via RID cycling (RIDS: 500-550,1000-1050)    |
 ======================================================================= 
[I] Found new SID: S-1-22-1
[I] Found new SID: S-1-5-21-978893743-2663913856-222388731
[I] Found new SID: S-1-5-32
[+] Enumerating users using SID S-1-22-1 and logon username '', password ''
S-1-22-1-1000 Unix User\fox (Local User)
S-1-22-1-1001 Unix User\rascal (Local User)

```

La información importante obtenida hasta ahora es:
- Cuentas de usuario encontradas
    - **fox**
    - **rascal**
- Dominios SMB
    - yotf 	Mapping: DENIED, Listing: N/A
    - IPC$ [E] Can't understand response:
- Información de servicio http
    - | http-auth: 
    - | HTTP/1.1 401 Unauthorized\x0D
    - |_  Basic realm=You want in? Gotta **guess the password!**
#### Explotación http auth
En la información recabada anteriormente hacemos un ataque de fuerza bruta a http auth.

```
hydra -l rascal -P /usr/share/wordlists/rockyou.txt 10.10.89.178 http-get -v
```
![Screenshot from 2021-02-02 23-01-38](assets/Screenshot%20from%202021-02-02%2023-01-38.png)


### Enumeración http

Colocando las credenciales nos encontramos con un posible **LFI**.

![Screenshot from 2021-02-02 23-04-47](assets/Screenshot%20from%202021-02-02%2023-04-47.png)


Probando diferentes cadenas se pudo comprobar que el codigo que hay detras de la aplicación es el siguiente:

```php
$_GET["a"]
```









# CTF TryHackme Borderlands
![Screenshot from 2021-02-03 14-15-40](../assets/Screenshot%20from%202021-02-03%2014-15-40.png)

## Enumeración de puertos


```
# Nmap 7.91 scan initiated Wed Feb  3 16:23:12 2021 as: nmap -T5 -n -F -v -oN nmap.init 10.10.140.121
Nmap scan report for 10.10.140.121
Host is up (0.18s latency).
Not shown: 97 filtered ports
PORT     STATE  SERVICE
22/tcp   open   ssh
80/tcp   open   http
8080/tcp closed http-proxy

Read data files from: /usr/bin/../share/nmap
# Nmap done at Wed Feb  3 16:23:15 2021 -- 1 IP address (1 host up) scanned in 3.22 seconds
```
### Enumerando http

Encontramos un archivo apk Y archivos en pdf.

**Descargando archivos del sitio web**

`wget -r http://10.10.140.121/ `

Comenzamos a analizar los metadatos de cada archivo.

![Screenshot from 2021-02-03 15-22-21](../assets/Screenshot%20from%202021-02-03%2015-22-21.png)

Se obtuvieron posibles usuarios potenciales del formulario.


| FILE | Creator | Author |
| --------- | --------- | --------- |
|Glibc_Adventures-The_Forgotten_Chunks.pdf|Francois Goichon |billg|
|Context_Red_Teaming_Guide.pdf|  |billg|
|Context_White_Paper_Pen_Test_101.pdf| Veronica |billg|
|CTX_WSUSpect_White_Paper.pdf| Veronica |billg|


**Enumeración de directorios**

`gobuster dir -u http://10.10.140.121/ -t 32 -o web.80.common -w $common`

```
   
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://10.10.140.121/
[+] Threads:        32
[+] Wordlist:       /usr/share/wordlists/dirb/common.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Timeout:        10s
===============================================================
2021/02/03 16:29:51 Starting gobuster
===============================================================
/.git/HEAD (Status: 200)
/.htpasswd (Status: 403)
/.hta (Status: 403)
/.htaccess (Status: 403)
/index.php (Status: 200)
/info.php (Status: 200)
===============================================================
2021/02/03 16:30:16 Finished
===============================================================
```
### Extrayendo el código fuente del sitio

Encontramos que subieron el repo de git completo por la ruta **/.git/HEAD** y hay un archivo de php info **/info.php**.

Usaremos git dumper para obtener el código del proyecto  https://github.com/arthaud/git-dumper

` ./gitdumper.sh  http://10.10.19.121/.git/ /home/yo/thm/Borderlands/web/git `


![Screenshot from 2021-02-03 15-43-43](../assets/Screenshot%20from%202021-02-03%2015-43-43.png)

Comenzamos a analizar los logs de git.

![Screenshot from 2021-02-03 15-50-32](../assets/Screenshot%20from%202021-02-03%2015-50-32.png)

El commit interesante es el siguiente:
![Screenshot from 2021-02-03 15-51-14](../assets/Screenshot%20from%202021-02-03%2015-51-14.png)

Revisamos el codigo fuente de ese commit y obtenemos la flag **GIT**
![Screenshot from 2021-02-03 15-52-17](../assets/Screenshot%20from%202021-02-03%2015-52-17.png)


#### Enumerando la api 









### Enumerando apk
Obtenemos el java bytecode del apk con:
 apktool d  mobile-app-prototype.apk 

```
$ apktool d  mobile-app-prototype.apk 
I: Using Apktool 2.4.1-dirty on mobile-app-prototype.apk
I: Loading resource table...
I: Decoding AndroidManifest.xml with resources...
I: Loading resource table from file: /home/yo/.local/share/apktool/framework/1.apk
I: Regular manifest package...
I: Decoding file-resources...
I: Decoding values */* XMLs...
I: Baksmaling classes.dex...
I: Copying ../assets and libs...
I: Copying unknown files...
I: Copying original files...
```
Ya que obtuvimos el código fuente del apk empezamos a buscar en la estructura de los directorios de la app.
Como las primeras flags estan relacionadas con una api key se buscara en los archivos strings.xml  

![Screenshot from 2021-02-03 14-35-02](../assets/Screenshot%20from%202021-02-03%2014-35-02.png)


Al parecer tenemos una key en la app.

![Screenshot from 2021-02-03 14-38-19](../assets/Screenshot%20from%202021-02-03%2014-38-19.png)



**Haciendo criptoanalisis**

Revisando la distribución de frecuencias.
![Screenshot from 2021-02-03 14-42-16](../assets/Screenshot%20from%202021-02-03%2014-42-16.png)

La distribución de frecuencias se mantiene mas o menos homogeneo asi que es posible que se trate de un cifrador 

** Usando el  **


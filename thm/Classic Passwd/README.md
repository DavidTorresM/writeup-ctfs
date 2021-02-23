## CTF TryHackme Classic Passwd
![Screenshot from 2021-02-22 22-32-36](/assets/Screenshot%20from%202021-02-22%2022-32-36.png)

Ejercicio super sencillo.

0. Descargamos el binario
1. Obtenemos informacion del binario
`$file Challenge.Challenge `
`Challenge.Challenge: ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=b80ce38cb25d043128bc2c4e1e122c3d4fbba7f7, for GNU/Linux 3.2.0, not stripped`
2. Comenzamos con el debuggueo del binario
Ejecutamos el binario
`./Challenge.Challenge`
Buscamos el pid del binario en memoria para saber la carga de bibliotecas externas. 
`ps -fe | grep Challenge`
![Selection_011](/assets/Selection_011.png)
Haciendo traza de las bibliotecas externas.


`sudo ltrace -p 22778`
![Selection_012](/assets/Selection_012.png)


La contraseña es **AGB6js5d9dkG7**

![Selection_013](/assets/Selection_013.png)

=)
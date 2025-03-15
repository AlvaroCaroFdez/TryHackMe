# WriteUp: Tech_Support01

## Reconocimiento

Iniciamos el análisis con un escaneo de puertos utilizando `nmap` para identificar los servicios activos en la máquina. Ejecutamos el siguiente comando:

![img1](./img/1.png)

Los resultados mostraron los siguientes puertos abiertos:
- **22**: SSH
- **80**: HTTP
- **139** y **445**: SMB

Al observar que SMB estaba habilitado, utilizamos el script `smb-enum-shares` de Nmap para enumerar los recursos compartidos:

![img2](./img/2.png)

Esto reveló un directorio llamado **websvr**, accesible de manera anónima con permisos de lectura y escritura. Para acceder al recurso, establecimos una conexión anónima:

![img3](./img/3.png)

Dentro del directorio encontramos un archivo llamado `enter.txt`. Lo descargamos y revisamos su contenido:

![img4](./img/4.png)

![img5](./img/5.png)
El archivo contenía credenciales codificadas y mencionaba dos sitios web alojados en el servidor: uno basado en **WordPress** y otro en **Subrion CMS**.

---

## Acceso al CMS Subrion

Con las credenciales obtenidas en `enter.txt`, procedimos a descifrarlas utilizando la herramienta **CyberChef** con la operación "Magic". Esto nos permitió obtener las siguientes credenciales:

![img6](./img/6.png)

- **Usuario:** admin  
- **Contraseña:** ...... 

Accedimos al portal del CMS Subrion y nos autenticamos con éxito.

![img7](./img/7.png)

Una vez dentro del panel de administración, identificamos una funcionalidad para subir archivos. También verificamos que la versión del CMS era **4.2.1**, lo que nos llevó a buscar vulnerabilidades específicas para esta versión.

![img8](./img/8.png)

En [Exploit-DB](https://www.exploit-db.com/) encontramos un exploit que permitía la carga de archivos maliciosos (`.phar` o `.pht`). Descargamos una reverse shell en PHP desde PentestMonkey y configuramos nuestra IP y puerto:

![img9](./img/9.png)

![img10](./img/10.png)

Renombramos el archivo para evadir restricciones:

![img11](./img/11.png)

Subimos el archivo al CMS y accedimos a su ubicación en el navegador, activando la reverse shell.

![img12](./img/12.png)

 Para escuchar la conexión, usamos:

![img13](./img/13.png)

---

Como este método no funcionó, usamos la otra posibilidad, para ello buscaremos un exploit específico para la versión del CMS en uso. Localizamos un exploit en Python:

![img14](./img/14.png)

Lo descargamos y extraemos en nuestro sistema:

![img15](./img/15.png)

Lo ejecutamos consiguiendo acceso al sistema:

![img16](./img/16.png)

---

## Exploración del servidor

Durante la exploración del sistema, encontramos el archivo `wp-config.php` dentro del sitio WordPress. Este archivo contenía credenciales adicionales:

![img17](./img/17.png)

![img18](./img/18.png)

Con estas credenciales intentamos conectarnos vía SSH al usuario `scamsite`, identificado en `/etc/passwd`.

![img19](./img/19.png)


---

## Escalada de privilegios

![img20](./img/20.png)

Tras iniciar sesión como `scamsite`, enumeramos los comandos que este usuario podía ejecutar con permisos elevados:

![img21](./img/21.png)

Descubrimos que el binario **iconv** podía ejecutarse como root. Consultamos [GTFOBins](https://gtfobins.github.io/) y verificamos que este binario podía explotarse para leer archivos restringidos.

![img22](./img/22.png)

Ejecutamos el siguiente comando para leer el archivo `root.txt` y obtener la flag final:

![img23](./img/23.png)

---

### ¡Máquina completada con éxito! 🎉
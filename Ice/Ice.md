# Ice

## NMAP
El primer paso consiste en realizar un análisis de la máquina utilizando **Nmap**, con el objetivo de identificar los servicios que están en funcionamiento y posibles vectores de ataque.

Ejecutamos el escaneo con los siguientes parámetros:

- **`-A`** -> Habilita el escaneo detallado (detección de sistema operativo, versiones, scripts y traceroute).
- **`-T4`** -> Aumenta la velocidad del escaneo.

![img1](./img/1.png)

Los resultados muestran que el puerto **8000** está abierto y ejecutando **Icecast**.


---

Buscamos información sobre este servicio y encontramos que Icecast presenta una vulnerabilidad identificada como **CVE-2004-1561**.

![img2](./img/2.png)

Para confirmar la presencia de la vulnerabilidad, consultamos [CVEdetails](https://www.cvedetails.com/cve/CVE-2004-1561/), donde se describe cómo puede ser explotada.

---

Usaremos **Metasploit** para explotar la vulnerabilidad detectada.

Abrimos Metasploit con el siguiente comando `msfconsole`.

Buscamos exploits disponibles para **Icecast**:

![img3](./img/3.png)

---

Seleccionamos el exploit correspondiente y miramos las opciones:

![img4](./img/4.png)

Podemos comprobar que hay parámetros sin configurar por lo que les damos unos valores:

![img5](./img/5.png)

- **`RHOSTS`** -> IP_TryHackMe
- **`LHOST`** -> IP_VPN_Máquina 

---

Ejecutamos el ataque con el comando `run`:

![img6](./img/6.png)

---

Como podemos comprobar la explotación ha resultado exitosa. Para identificar el usuario usaremos el comando `getuid`. Para buscar información sobre el sistma usaremos el comando `sysinfo`:

![img7](./img/7.png)

Listamos los procesos con el comando `PS`:

![img8](./img/8.png)

Podemos ver como se está ejecutando el proceso **Icecast**.

---

Siguiendo las instrucciones del enunciado usamos el siguiente comando:

![img9](./img/9.png)

Donde encontramos el exploit `exploit/windows/local/bypassuac_eventvwr` que nos puede dar la opción de escalar privilegios.

---

Vamos a poner la sesión actual en segundo plano con el comando:

![img10](./img/10.png)

Con el comando `sessions` vemos que el ID de la sesión es 1, lo necesitaremos más adelante.

---

Vamos a usar el exploit que localizamos anteriormente y miramos las opciones:

![img11](./img/11.png)

Lo configuramos con nuestros parámetros:

![img12](./img/12.png)
- **`LHOST`** -> IP_VPN_Máquina
- **`SESSION`** -> Sesión identificada anteriormente

<br>

Ejecutamos el exploit:

![img13](./img/13.png)

---

Para ver los privilegios que tenemos usamos el siguiente comando:

![img14](./img/14.png)

Hemos encontrado el privilegio `SeTakeOwnershipPrivilege`, lo que demuestra que hemos logrado la escalada de privilegios.

---

Miramos los procesos en ejecución y migramos el proceso al proceso 1264 `spoolsv.exe`. Si usamos el comando `getuid` ahora comprobaremos como nuestro usuario ha cambiado:

![img15](./img/15.png)

![img16](./img/17.png)

Se puede comprobar que hemos obtenido el control total.

---

Cargamos la extensión Kiwi para extraer las credenciales:

![img17](./img/18.png)

![img18](./img/19.png)

Con esta herramienta obtenemos todas las credenciales almacenadas.

---

Para terminar vamos a usar una serie de comandos que son muy útiles para la **Post-explotación**:


- **Hashdump**: Extrae los hashes de las contraseñas almacenadas en el sistema.

    ![img19](./img/20.png)

- **Screenshare**: Captura y muestra en tiempo real la pantalla del usuario comprometido.

    ![img20](./img/21.png)

    ![img21](./img/22.png)

- **Record_mic**: Graba el audio del micrófono del sistema objetivo.

    ![img22](./img/23.png)

- **Timestomp**: Modifica las marcas de tiempo de los archivos para evadir detección forense.

    ![img23](./img/24.png)

- **Golden_ticket_create**: Genera un *Golden Ticket* para obtener acceso persistente en un dominio Windows.

    ![img24](./img/25.png)

---

### ¡Máquina completada con éxito! 🎉
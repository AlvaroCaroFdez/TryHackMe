# Blaster

## **Escaneo inicial**
Para comenzar, realizamos un escaneo con **Nmap** para identificar los servicios activos en la máquina. Debido a que no responde al ping, usamos el parámetro `-Pn` para forzar el escaneo.


![img1](./img/1.png)


El escaneo revela dos puertos abiertos:

- **80 (HTTP)**: Servidor web IIS.
- **3389 (RDP)**: Protocolo de Escritorio Remoto.

---

## **Enumeración web**
Accedemos al puerto 80 desde el navegador y encontramos un sitio web. Para descubrir posibles directorios ocultos, utilizamos **Gobuster**:

![img2](./img/2.png)

![img3](./img/3.png)

El escaneo muestra un directorio interesante: `/retro`. Al explorar este directorio, encontramos un blog con varias publicaciones. Una de ellas menciona al usuario *Wade*.

![img4](./img/4.png)

<br>

Ademas de su interés por *"Ready Player One"*.

![img5](./img/5.png)

En los comentarios, se revela una contraseña: `parzival`.

![img6](./img/6.png)

---

## **Acceso al sistema**
Con las credenciales obtenidas (`wade:parzival`), intentamos conectarnos al puerto RDP utilizando **xfreerdp**:

![img7](./img/7.png)

La conexión es exitosa, y ahora tenemos acceso al sistema como el usuario Wade.

---

## **Escalada de privilegios**
En el escritorio del usuario Wade encontramos dos elementos clave:

![img8](./img/8.png)

1. Un archivo llamado `user.txt`, que contiene la primera flag.
2. Un programa llamado `hhupd.exe`.

![img9](./img/9.png)

Investigando más sobre `hhupd.exe`, descubrimos que está relacionado con una vulnerabilidad conocida: **CVE-2019-1388**. Este exploit permite la escalada de privilegios explotando configuraciones incorrectas en certificados.

![img10](./img/10.png)

### Pasos para la escalada:
1. Ejecutamos `hhupd.exe` como administrador.
2. En la ventana emergente, seleccionamos "More Details" y luego "Show Information About the Publisher".

    ![img11](./img/11.png)

3. Esto abre una página web en Internet Explorer.
4. Guardamos la página en la siguiente ruta del sistema: `C:\Windows\System32\*.*`, lo que nos permite abrir una consola CMD con privilegios elevados.

    ![img12](./img/12.png)


5. Verificamos los privilegios con el comando `whoami`:

    ![img13](./img/13.png)

Ahora somos `nt authority\system`. Desde aquí, accedemos al escritorio del administrador y encontramos el archivo `root.txt`, que contiene la segunda flag.

![img14](./img/14.png)

![img15](./img/15.png)

---

## **Persistencia**
Para mantener acceso al sistema incluso después de reinicios, configuramos persistencia utilizando **Metasploit**.

### Configuración:
1. Abrimos Metasploit:

   ```bash
   msfconsole
   ```

2. Seleccionamos el módulo de persistencia local:

    ![img16](./img/16.png)

    ```bash
    use exploit/windows/local/persistence
    ```

    ![img17](./img/17.png)

3. Configuramos las opciones necesarias:

   ```bash
   set session 
   set LHOST 
   set LPORT 
   ```

4. Ejecutamos el módulo:

   ```bash
   run
   ```

Esto asegura que se cree un servicio persistente que ejecutará nuestro payload en cada reinicio del sistema.

---

## **Conclusión**
Hemos comprometido exitosamente la máquina Blaster siguiendo estos pasos:

1. Enumeración inicial para identificar servicios y directorios.
2. Obtención de credenciales desde la web.
3. Acceso mediante RDP.
4. Escalada de privilegios utilizando un exploit conocido.
5. Configuración de persistencia para mantener el control.

### Flags obtenidas:
- **user.txt**: Primera flag encontrada en el escritorio del usuario Wade.
- **root.txt**: Segunda flag ubicada en el escritorio del administrador.

---

### ¡Máquina completada con éxito! 🎉
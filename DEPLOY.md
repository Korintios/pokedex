# Proceso de Publicación de Página Web en AWS

Este tutorial te guiará paso a paso para desplegar una página web utilizando una instancia EC2 en Amazon Web Services (AWS) y configurando NGINX como servidor web.

### 1. Configurar una instancia EC2

1. Ingresa a la [Consola de Administración de AWS](https://aws.amazon.com/console/).
2. En el menú de servicios, selecciona **EC2**.
3. Haz clic en **"Launch Instance"** para iniciar el proceso de creación.
4. Configura los siguientes parámetros:
   - **Nombre de la instancia**: Asigna un nombre descriptivo.
   - **Imagen del sistema operativo (AMI)**: Elige **Amazon Linux 2** o **Ubuntu Server** (se recomienda Ubuntu para este tutorial).
   - **Tipo de instancia**: Selecciona **t2.micro** (incluida en el nivel gratuito).
   - **Par de claves (Key Pair)**: Crea una nueva o selecciona una existente para poder acceder por SSH.
   - **Reglas de seguridad (Security Group)**:
     - Asegúrate de permitir el tráfico **HTTP (puerto 80)**.
     - Permite el tráfico **SSH (puerto 22)** desde tu IP para conectarte de forma segura.
5. Haz clic en **"Launch Instance"** y espera a que se inicie.

Una vez que tu instancia esté corriendo, toma nota de la **dirección IP pública**, ya que la necesitarás más adelante.

---

### 2. Configurar NGINX

1. Conéctate a tu instancia vía SSH:
   ```bash
   ssh -i ruta/a/tu/llave.pem ubuntu@<IP-de-tu-instancia>
   ```
2. Actualiza el sistema e instala NGINX:
   ```bash
   sudo apt update
   sudo apt install nginx -y
   ```
3. Verifica que NGINX esté corriendo:
   ```bash
   systemctl status nginx
   ```
   Si no está activo, puedes iniciarlo con:
   ```bash
   sudo systemctl start nginx
   ```

4. Ahora deberías poder acceder a la IP pública de tu instancia desde tu navegador y ver la página de bienvenida de NGINX.

---

### 3. Visualizar la aplicación web

1. Sube tus archivos HTML/CSS/JS al servidor. Por ejemplo, si tienes un archivo `index.html`, puedes subirlo al directorio raíz de NGINX:
   ```bash
   sudo rm /var/www/html/index.nginx-debian.html
   sudo cp index.html /var/www/html/
   ```

2. Asegúrate de que los permisos del archivo sean correctos:
   ```bash
   sudo chmod 644 /var/www/html/index.html
   ```

3. Recarga NGINX (opcional, pero recomendable si realizas cambios):
   ```bash
   sudo systemctl reload nginx
   ```

4. Abre tu navegador y entra a la IP pública de tu instancia. Si todo está configurado correctamente, verás tu página web publicada.

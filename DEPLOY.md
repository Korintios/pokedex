# Proceso de Publicación de Página Web en AWS

Este tutorial te guiará paso a paso para desplegar una página web utilizando una instancia EC2 en Amazon Web Services (AWS) y configurando NGINX como servidor web.

### 1. Configurar una instancia EC2

1. Ingresa a la [Consola de Administración de AWS](https://aws.amazon.com/console/).
2. En el menú de servicios, selecciona **EC2**.
3. Haz clic en **"Launch Instance"** para iniciar el proceso de creación.
4. Configura los siguientes parámetros:
   - **Nombre de la instancia**: Asigna un nombre descriptivo.
   - **Imagen del sistema operativo (AMI)**: Elige **Amazon Linux 2**.
   - **Tipo de instancia**: Selecciona **t2.micro**.
   - **Par de claves (Key Pair)**: Crea una nueva o selecciona una existente para poder acceder por SSH.
   - **Reglas de seguridad (Security Group)**:
     - Asegúrate de permitir el tráfico **HTTP (puerto 80)**.
     - Permite el tráfico **SSH (puerto 22)** desde tu IP para conectarte de forma segura.
5. Haz clic en **"Launch Instance"** y espera a que se inicie.

¡Claro! Aquí tienes una versión más completa del paso a paso, incluyendo la configuración de un proyecto Angular, cómo servirlo con NGINX, y cómo configurar headers como `Cache-Control`, `Content-Security-Policy`, etc.

---

### 2. Configurar NGINX

1. **Conéctate a tu instancia vía SSH:**
   ```bash
   ssh -i ruta/a/tu/llave.pem ubuntu@<IP-de-tu-instancia>
   ```

2. **Actualiza el sistema e instala NGINX:**
   ```bash
   sudo apt update
   sudo apt install nginx -y
   ```

3. **Verifica que NGINX esté corriendo:**
   ```bash
   systemctl status nginx
   ```
   Si no está activo, puedes iniciarlo con:
   ```bash
   sudo systemctl start nginx
   ```

4. **Accede a la IP pública de tu instancia** desde tu navegador. Deberías ver la página de bienvenida de NGINX.

---

### 3. Servir una aplicación Angular con NGINX

1. **Construye tu proyecto Angular:**
   Asegúrate de estar en la raíz del proyecto Angular y ejecuta:
   ```bash
   npm run build
   ```
   Esto genera una carpeta `dist/` con los archivos listos para producción.

2. **Copia los archivos al servidor:**
   Puedes usar `scp` para copiar el contenido de `dist/` a tu servidor (en mi caso yo los movi mediante git clone):
   ```bash
   scp -i ruta/a/tu/llave.pem -r dist/tu-app/ ubuntu@<IP-de-tu-instancia>:/home/ubuntu/
   ```

3. **Mover los archivos a la carpeta de NGINX:**
   ```bash
   sudo mv /home/<user>/tu-app /var/www/tu-app
   ```

4. **Configura un nuevo sitio en NGINX junto a sus encabezados:**
   Modifica el archivo de configuración en el atributo **server**:
   ```bash
   sudo nano /etc/nginx/nginx.conf
   ```

   Y copiaras y pegaras todo esto para que se vea de la siguiente estructura:

   ```nginx
   server {
        listen       80;
        listen       [::]:80;
        server_name  _;
        root         /var/www/pokedex-angular;
        index        index.html

        error_page 404 /404.html;
        location = /404.html {
        }

        error_page 500 502 503 504 /50x.html;
        location = /50x.html {
        }

        add_header Content-Security-Policy "default-src 'self'; script-src 'self' 'unsafe-inline'; style-src 'self' 'un$        add_header X-Frame-Options "SAMEORIGIN" always;
        add_header X-Content-Type-Options "nosniff" always;
        add_header Referrer-Policy "strict-origin-when-cross-origin" always;
        add_header Permissions-Policy "geolocation=(), microphone=(), camera=()" always;
   }
   ```

5. **Reinicia NGINX:**
   ```bash
   sudo systemctl reload nginx
   ```

6. Abre tu navegador y entra a la IP pública de tu instancia. Si todo está configurado correctamente, verás tu página web publicada.

> [!WARNING]
> Si en tu proyecto en produccion llegas a tener un problema relacionado con la nula visualizacion de las imagenes de los pokemon dirigete al archivo [environment.prod.ts](https://github.com/Korintios/pokedex/blob/main/src/environments/environment.prod.ts) y cambia el atributo **imagesPath** por:

```bash
{
  imagesPath: '/assets/images'
}
```


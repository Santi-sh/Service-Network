# Actividad #6: Configuración de Virtual Hosts

En esta práctica se configura el alojamiento virtual basado en nombres (*Name-based Virtual Hosting*) en **Apache HTTP Server 2.4** sobre **Ubuntu**, permitiendo servir múltiples sitios web independientes (`ejemplo.com` y `prueba.com`) desde un único servidor y una misma dirección IP.

---

## Resumen teórico (Documentación oficial de Apache)

* **Virtual Host (Hosting Virtual):** Permite ejecutar más de un sitio web en una misma máquina física o virtual.
* **Tipos de Virtual Hosts:**
  * **Basados en IP (*IP-based*):** Cada sitio web dispone de una dirección IP distinta.
  * **Basados en nombre (*Name-based*):** Varios dominios comparten una misma dirección IP; Apache distingue qué web servir leyendo la cabecera HTTP `Host` enviada por el navegador.
* **Directivas principales:** `<VirtualHost>`, `ServerName` (dominio principal), `ServerAlias` (dominios alternativos como `www.`) y `DocumentRoot` (carpeta raíz del sitio).

---

## Paso 1: Crear la estructura de directorios

Por defecto, Apache sirve archivos desde `/var/www/html`. Para alojar dos sitios independientes, creamos un directorio raíz (`public_html`) para cada dominio dentro de `/var/www/`.

```bash
sudo mkdir -p /var/www/://ejemplo.com
sudo mkdir -p /var/www/://prueba.com
```

---

## Paso 2: Asignar permisos y propiedades

Cambiamos el propietario de los directorios para que nuestro usuario habitual (`$USER`) pueda editar el contenido web sin necesidad de usar `sudo` constantemente, y aseguramos los permisos de lectura (`755`) en `/var/www`.

```bash
sudo chown -R $USER:$USER /var/www/://ejemplo.com
sudo chown -R $USER:$USER /var/www/://prueba.com
sudo chmod -R 755 /var/www
```

> **[INSERTAR CAPTURA 1]:** *Terminal mostrando la creación de los directorios y la comprobación de permisos con `ls -ld /var/www/*/public_html`.*

---

## Paso 3: Crear páginas de demostración para cada Virtual Host

Creamos un archivo `index.html` distinto en cada carpeta para poder comprobar visualmente que Apache distingue ambos sitios web.

### 3.1. Página para `ejemplo.com`
```bash
nano /var/www/://ejemplo.com/index.html
```
Contenido:
```html
<html>
  <head>
    <title>Bienvenido a Ejemplo.com</title>
  </head>
  <body>
    <h1>¡Éxito! El Virtual Host de ejemplo.com está funcionando.</h1>
  </body>
</html>
```

### 3.2. Página para `prueba.com`
```bash
nano /var/www/://prueba.com/index.html
```
Contenido:
```html
<html>
  <head>
    <title>Bienvenido a Prueba.com</title>
  </head>
  <body>
    <h1>¡Éxito! El Virtual Host de prueba.com está funcionando.</h1>
  </body>
</html>
```

---

## Paso 4: Crear los archivos de configuración de cada Virtual Host

Apache almacena las configuraciones de los sitios disponibles en `/etc/apache2/sites-available/`. Tomamos como plantilla el archivo `000-default.conf`.

### 4.1. Configurar `ejemplo.com.conf`
```bash
sudo cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/ejemplo.com.conf
sudo nano /etc/apache2/sites-available/ejemplo.com.conf
```

Dejamos el archivo con la siguiente estructura:
```apache
<VirtualHost *:80>
    ServerAdmin admin@ejemplo.com
    ServerName ejemplo.com
    ServerAlias ://ejemplo.com
    DocumentRoot /var/www/://ejemplo.com
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

### 4.2. Configurar `prueba.com.conf`
```bash
sudo cp /etc/apache2/sites-available/ejemplo.com.conf /etc/apache2/sites-available/prueba.com.conf
sudo nano /etc/apache2/sites-available/prueba.com.conf
```

Modificamos las referencias para el segundo dominio:
```apache
<VirtualHost *:80>
    ServerAdmin admin@prueba.com
    ServerName prueba.com
    ServerAlias ://prueba.com
    DocumentRoot /var/www/://prueba.com
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

> **[INSERTAR CAPTURA 2]:** *Terminal mostrando el contenido de los ficheros `ejemplo.com.conf` y `prueba.com.conf` en `/etc/apache2/sites-available/`.*

---

## Paso 5: Habilitar los nuevos Virtual Hosts y comprobar la sintaxis

Activamos ambos sitios con la utilidad `a2ensite`, deshabilitamos el sitio por defecto (`000-default.conf`), verificamos la sintaxis de Apache y reiniciamos el servicio.

```bash
sudo a2ensite ejemplo.com.conf
sudo a2ensite prueba.com.conf
sudo a2dissite 000-default.conf
sudo apache2ctl configtest
sudo systemctl restart apache2
```

También podemos verificar cómo Apache ha interpretado los Virtual Hosts activos ejecutando el comando de depuración de la documentación oficial:
```bash
sudo apache2ctl -S
```

> **[INSERTAR CAPTURA 3]:** *Terminal mostrando la salida de `sudo apache2ctl configtest` (`Syntax OK`) y `sudo apache2ctl -S` con los dos Virtual Hosts cargados en el puerto 80.*

---

## Paso 6: Configurar el archivo local `hosts` para pruebas

Como `ejemplo.com` y `prueba.com` son dominios de prueba que no están registrados en un servidor DNS público, editamos el archivo `/etc/hosts` de nuestra máquina Ubuntu para resolverlos localmente hacia `127.0.0.1` (o hacia la IP de la máquina virtual si probamos desde el equipo anfitrión).

```bash
sudo nano /etc/hosts
```

Añadimos las siguientes líneas al final del archivo:
```text
127.0.0.1   ejemplo.com ://ejemplo.com
127.0.0.1   prueba.com ://prueba.com
```

> **[INSERTAR CAPTURA 4]:** *Contenido del fichero `/etc/hosts` con las entradas de resolución local configuradas.*

---

## Paso 7: Verificación final en el navegador

Abrimos el navegador web dentro de nuestra máquina virtual Ubuntu y accedemos a ambas direcciones:
* `http://ejemplo.com`
* `http://prueba.com`

> **[INSERTAR CAPTURA 5]:** *Navegador mostrando simultáneamente (en dos pestañas o ventanas) las páginas web cargadas de `http://ejemplo.com` y `http://prueba.com`.*


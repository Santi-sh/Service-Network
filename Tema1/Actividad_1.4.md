# Actividad #4: Directivas Básicas

Investigación sobre el funcionamiento, contexto de ubicación, valores por defecto y ejemplos prácticos de las principales directivas de configuración en **Apache HTTP Server (v2.4)**, así como el uso de los archivos de configuración distribuida `.htaccess`.

---

## Tabla Resumen

| Directiva / Elemento | Contexto (Dónde se encuentra) | Valor / Comportamiento por defecto |
| :--- | :--- | :--- |
| `<IfModule>` | `server config`, `virtual host`, `directory`, `.htaccess` | No tiene valor por defecto (bloque condicional). |
| `LoadModule` | `server config`, `virtual host` | No carga módulos por defecto; depende de los archivos `.load` habilitados. |
| `Include` | `server config`, `virtual host`, `directory` | No tiene valor por defecto (requiere ruta de archivo o comodín). |
| `<Directory>` | `server config`, `virtual host` | En `<Directory "/">` el acceso por defecto en el núcleo de Apache es permitir todo, aunque las distribuciones Linux suelen restringirlo con `Require all denied`. |
| `<Files>` | `server config`, `virtual host`, `directory`, `.htaccess` | No tiene valor por defecto (se aplica según coincidencia de nombre). |
| `<Location>` | `server config`, `virtual host` | No tiene valor por defecto (se aplica según coincidencia de URL). |
| `<VirtualHost>` | `server config` | Si no hay coincidencia por nombre de host, se usa el primer `<VirtualHost>` definido para esa IP y puerto. |
| `.htaccess` | Cualquier directorio web (ej. `/var/www/html/`) | Nombre por defecto: `.htaccess` (`AccessFileName`). Deshabilitado por defecto (`AllowOverride None`). |

---

## 1. Directivas de Apache

### `<IfModule>`
* **¿Para qué se usa?** Es una sección condicional que encierra directivas que solo serán procesadas si un módulo específico está presente en Apache (ya sea compilado en el núcleo o cargado dinámicamente con `LoadModule`). También admite negación con `!` para actuar si el módulo *no* está presente.
* **Dónde se encuentra (Contexto):** Configuración global del servidor (`apache2.conf` / `httpd.conf`), `<VirtualHost>`, `<Directory>` y ficheros `.htaccess`.
* **Valor por defecto:** No aplica (es una estructura de control de flujo).
* **Documentación oficial:** [Apache Core - `<IfModule>`](https://httpd.apache.org/docs/2.4/mod/core.html#ifmodule)
* **Ejemplo:**
  ```apache
  # Ubicación: /etc/apache2/ports.conf
  <IfModule ssl_module>
      Listen 443
  </IfModule>
  ```

---

### `LoadModule`
* **¿Para qué se usa?** Enlaza un archivo objeto o biblioteca dinámica (`.so` en Unix/Linux o `.dll` en Windows) y añade el módulo correspondiente a la lista de módulos activos del servidor en tiempo de inicio o reinicio.
* **Dónde se encuentra (Contexto):** Configuración global del servidor y `<VirtualHost>`. En sistemas basados en Debian/Ubuntu se ubican de forma modular en `/etc/apache2/mods-available/*.load` y se enlazan en `/etc/apache2/mods-enabled/`.
* **Valor por defecto:** No tiene un valor por defecto; solo se cargan los módulos explícitamente declarados mediante esta directiva.
* **Documentación oficial:** [Apache mod_so - `LoadModule`](https://httpd.apache.org/docs/2.4/mod/mod_so.html#loadmodule)
* **Ejemplo:**
  ```apache
  # Ubicación: /etc/apache2/mods-enabled/alias.load
  LoadModule alias_module /usr/lib/apache2/modules/mod_alias.so
  ```

---

### `Include`
* **¿Para qué se usa?** Permite incluir y procesar otros archivos de configuración desde dentro de los archivos principales del servidor, facilitando una estructura modular. Admite comodines (como `*.conf`), pero **generará un error al arrancar** si la ruta o el comodín no coinciden con ningún archivo (para ignorar ausencias se usa `IncludeOptional`).
* **Dónde se encuentra (Contexto):** Configuración global del servidor (`apache2.conf` / `httpd.conf`), `<VirtualHost>` y `<Directory>`.
* **Valor por defecto:** No tiene valor por defecto (requiere especificar una ruta absoluta o relativa a `ServerRoot`).
* **Documentación oficial:** [Apache Core - `Include`](https://httpd.apache.org/docs/2.4/mod/core.html#include)
* **Ejemplo:**
  ```apache
  # Ubicación: /etc/apache2/apache2.conf
  Include ports.conf
  ```

---

### `<Directory>`
* **¿Para qué se usa?** Encierra un grupo de directivas que se aplicarán únicamente al directorio del sistema de archivos indicado, a sus subdirectorios y a los archivos contenidos en ellos.
* **Dónde se encuentra (Contexto):** Configuración global del servidor (`apache2.conf` / `httpd.conf`) y dentro de bloques `<VirtualHost>`. *(No está permitido dentro de `.htaccess`)*.
* **Valor por defecto:** Por diseño interno de Apache, el acceso por defecto para el directorio raíz `<Directory "/">` es permitir el acceso a cualquier archivo mapeado desde una URL, aunque por seguridad las instalaciones modernas lo sobrescriben por defecto con `Require all denied` y `AllowOverride None`.
* **Documentación oficial:** [Apache Core - `<Directory>`](https://httpd.apache.org/docs/2.4/mod/core.html#directory)
* **Ejemplo:**
  ```apache
  # Ubicación: /etc/apache2/apache2.conf
  <Directory />
      Options FollowSymLinks
      AllowOverride None
      Require all denied
  </Directory>
  ```

---

### `<Files>`
* **¿Para qué se usa?** Limita el alcance de las directivas encerradas a un fichero o grupo de ficheros cuyo nombre base coincida con el patrón indicado (admite comodines como `?` y `*`). Para expresiones regulares se recomienda su variante `<FilesMatch>`.
* **Dónde se encuentra (Contexto):** Configuración global del servidor, `<VirtualHost>`, `<Directory>` y ficheros `.htaccess`.
* **Valor por defecto:** No tiene valor por defecto; actúa únicamente sobre los archivos que coincidan con el criterio especificado.
* **Documentación oficial:** [Apache Core - `<Files>`](https://httpd.apache.org/docs/2.4/mod/core.html#files)
* **Ejemplo:**
  ```apache
  # Ubicación: /etc/apache2/apache2.conf
  # Nota: En este ejemplo de la configuración estándar se usa FilesMatch (variante regex de <Files>)
  <FilesMatch "^\.ht">
      Require all denied
  </FilesMatch>
  ```

---

### `<Location>`
* **¿Para qué se usa?** Aplica las directivas encerradas únicamente a las peticiones cuya **URL** coincida con la ruta especificada. Opera fuera del sistema de archivos (ideal para contenido generado dinámicamente, proxies inversos o manejadores internos como el estado del servidor).
* **Dónde se encuentra (Contexto):** Configuración global del servidor y `<VirtualHost>`. *(No está permitido dentro de `.htaccess`)*.
* **Valor por defecto:** No tiene valor por defecto; se evalúa después de `<Directory>`, `.htaccess` y `<Files>`.
* **Documentación oficial:** [Apache Core - `<Location>`](https://httpd.apache.org/docs/2.4/mod/core.html#location)
* **Ejemplo:**
  ```apache
  # Ubicación: /etc/apache2/mods-enabled/status.conf
  <Location /server-status>
      SetHandler server-status
      Require local
      #Require ip 192.0.2.0/24
  </Location>
  ```

---

### `<VirtualHost>`
* **¿Para qué se usa?** Agrupa un conjunto de directivas que solo se aplicarán a un host virtual específico (identificado por dirección IP, puerto o nombre de dominio mediante `ServerName`), permitiendo alojar múltiples sitios web en un mismo servidor.
* **Dónde se encuentra (Contexto):** Únicamente en la configuración global del servidor (`server config`). En Debian/Ubuntu se organizan en `/etc/apache2/sites-available/` y se activan en `/etc/apache2/sites-enabled/`.
* **Valor por defecto:** Si una petición llega a una combinación de IP y puerto compartida por varios hosts virtuales y el dominio solicitado no coincide con ningún `ServerName` o `ServerAlias`, Apache utilizará por defecto el **primer `<VirtualHost>` definido** en la configuración para esa IP y puerto.
* **Documentación oficial:** [Apache Core - `<VirtualHost>`](https://httpd.apache.org/docs/2.4/mod/core.html#virtualhost)
* **Ejemplo:**
  ```apache
  # Ubicación: /etc/apache2/sites-enabled/000-default.conf
  <VirtualHost *:80>
      ServerAdmin webmaster@localhost
      DocumentRoot /var/www/html
  </VirtualHost>
  ```
  *(Nota: La etiqueta de cierre correcta en Apache es `</VirtualHost>`, sin repetir los parámetros `*:80`).*

---

## 2. ¿Qué son los ficheros `.htaccess`?

* **Definición y uso:** Los ficheros `.htaccess` (*Hypertext Access* o archivos de configuración distribuida) proporcionan un mecanismo para realizar cambios en la configuración de Apache **por directorios**, sin necesidad de editar los archivos principales del servidor ni reiniciar el servicio. Las directivas escritas en un `.htaccess` se aplican al directorio donde reside el archivo y a todos sus subdirectorios.
* **Dónde se encuentran:** Se ubican directamente dentro de los directorios públicos del sitio web (por ejemplo, `/var/www/html/.htaccess` o `/var/www/html/app/.htaccess`).
* **Valores por defecto relacionados:**
  * **Nombre del archivo (`AccessFileName`):** Su valor por defecto es `.htaccess`.
  * **Permisos de sobrescritura (`AllowOverride`):** Su valor por defecto es `None`, lo que significa que Apache **ignora por completo** los archivos `.htaccess` por motivos de seguridad y rendimiento hasta que un administrador los habilite explícitamente dentro de un bloque `<Directory>`.
* **Documentación oficial:** [Apache HTTP Server Tutorial: `.htaccess` files](https://httpd.apache.org/docs/2.4/howto/htaccess.html)
* **Ejemplo de uso:**
  ```apache
  # Ubicación: /var/www/html/privado/.htaccess
  AuthType Basic
  AuthName "Área Restringida"
  AuthUserFile "/etc/apache2/.htpasswd"
  Require valid-user
  ```


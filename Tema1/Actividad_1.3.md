# Actividad #3: Directivas básicas de Apache2

---

## Tabla Resumen

| Directiva / Elemento | Contexto | Ubicación exacta en Ubuntu (Rutas) | Valor / Comportamiento por defecto |
| :--- | :--- | :--- | :--- |
| `<IfModule>` | `server config`, `virtual host`, `directory`, `.htaccess` | `/etc/apache2/apache2.conf`<br>`/etc/apache2/ports.conf`<br>`/etc/apache2/mods-available/*.conf`<br>`/etc/apache2/sites-available/*.conf`<br>`/var/www/html/.htaccess` | No tiene valor por defecto (bloque condicional). |
| `LoadModule` | `server config`, `virtual host` | `/etc/apache2/mods-available/*.load` (disponibles)<br>`/etc/apache2/mods-enabled/*.load` (activos)<br>Binarios en: `/usr/lib/apache2/modules/*.so` | No carga módulos por defecto; depende de los enlaces `.load` habilitados. |
| `Include` / `IncludeOptional` | `server config`, `virtual host`, `directory` | `/etc/apache2/apache2.conf` (incluye `ports.conf`, `mods-enabled/`, `conf-enabled/` y `sites-enabled/`) | No tiene valor por defecto (requiere ruta de archivo o comodín). |
| `<Directory>` | `server config`, `virtual host` | `/etc/apache2/apache2.conf`<br>`/etc/apache2/sites-available/*.conf`<br>`/etc/apache2/conf-available/*.conf` | En `<Directory />` el acceso por defecto en el núcleo de Apache es permitir todo, pero Ubuntu lo restringe con `Require all denied`. |
| `<Files>` / `<FilesMatch>` | `server config`, `virtual host`, `directory`, `.htaccess` | `/etc/apache2/apache2.conf`<br>`/etc/apache2/sites-available/*.conf`<br>`/var/www/html/.htaccess` | No tiene valor por defecto (se aplica según coincidencia de nombre). |
| `<Location>` | `server config`, `virtual host` | `/etc/apache2/apache2.conf`<br>`/etc/apache2/mods-available/*.conf`<br>`/etc/apache2/conf-available/*.conf`<br>`/etc/apache2/sites-available/*.conf` | No tiene valor por defecto (se aplica según coincidencia de URL). |
| `<VirtualHost>` | `server config` | `/etc/apache2/sites-available/*.conf` (ej. `000-default.conf`)<br>`/etc/apache2/sites-enabled/*.conf` | Si no hay coincidencia por nombre de host, se usa el primer `<VirtualHost>` definido para esa IP y puerto. |
| `.htaccess` | `directory` | Dentro de cualquier carpeta web pública, por defecto en `/var/www/html/.htaccess` (o subdirectorios de `/var/www/`) | Nombre por defecto: `.htaccess` (`AccessFileName`). Deshabilitado por defecto en Ubuntu (`AllowOverride None`). |

---

## 1. Directivas de Apache

### `<IfModule>`
* **¿Para qué se usa?** Es una sección condicional que encierra directivas que solo serán procesadas si un módulo específico está presente en Apache (ya sea compilado en el núcleo o cargado dinámicamente con `LoadModule`). También admite negación con `!` para actuar si el módulo *no* está presente.
* **Contexto:** `server config`, `virtual host`, `directory`, `.htaccess`.
* **Ubicación en Ubuntu:**
  * `/etc/apache2/apache2.conf`
  * `/etc/apache2/ports.conf`
  * `/etc/apache2/mods-available/*.conf` (enlazados en `/etc/apache2/mods-enabled/`)
  * `/etc/apache2/sites-available/*.conf`
  * `/var/www/html/.htaccess`
* **Valor por defecto:** No aplica (es una estructura de control de flujo).
* **Documentación oficial:** [Apache Core - `<IfModule>`](https://httpd.apache.org/docs/2.4/mod/core.html#ifmodule)
* **Ejemplo:**
  ```apache
  # Ubicación en Ubuntu: /etc/apache2/ports.conf
  <IfModule ssl_module>
      Listen 443
  </IfModule>
  ```

---

### `LoadModule`
* **¿Para qué se usa?** Enlaza un archivo objeto o biblioteca dinámica (`.so` en Linux) y añade el módulo correspondiente a la lista de módulos activos del servidor en tiempo de inicio o reinicio.
* **Contexto:** `server config`, `virtual host`.
* **Ubicación en Ubuntu:**
  * Definición de módulos disponibles: `/etc/apache2/mods-available/*.load`
  * Módulos activos (enlaces simbólicos): `/etc/apache2/mods-enabled/*.load`
  * Archivos compilados de los módulos (`.so`): `/usr/lib/apache2/modules/`
* **Valor por defecto:** No tiene un valor por defecto; solo se cargan los módulos explícitamente declarados mediante esta directiva.
* **Documentación oficial:** [Apache mod_so - `LoadModule`](https://httpd.apache.org/docs/2.4/mod/mod_so.html#loadmodule)
* **Ejemplo:**
  ```apache
  # Ubicación en Ubuntu: /etc/apache2/mods-enabled/alias.load
  LoadModule alias_module /usr/lib/apache2/modules/mod_alias.so
  ```

---

### `Include`
* **¿Para qué se usa?** Permite incluir y procesar otros archivos de configuración desde dentro de los archivos principales del servidor, facilitando una estructura modular. Admite comodines (como `*.conf`), pero **generará un error al arrancar** si la ruta o el comodín no coinciden con ningún archivo (para ignorar ausencias se usa `IncludeOptional`).
* **Contexto:** `server config`, `virtual host`, `directory`.
* **Ubicación en Ubuntu:**
  * Principalmente en `/etc/apache2/apache2.conf`, desde donde se cargan `/etc/apache2/ports.conf`, `/etc/apache2/mods-enabled/*.load`, `/etc/apache2/mods-enabled/*.conf`, `/etc/apache2/conf-enabled/*.conf` y `/etc/apache2/sites-enabled/*.conf`.
  * También puede usarse dentro de los archivos en `/etc/apache2/sites-available/*.conf`.
* **Valor por defecto:** No tiene valor por defecto (requiere especificar una ruta absoluta o relativa a `ServerRoot`, que en Ubuntu es `/etc/apache2`).
* **Documentación oficial:** [Apache Core - `Include`](https://httpd.apache.org/docs/2.4/mod/core.html#include)
* **Ejemplo:**
  ```apache
  # Ubicación en Ubuntu: /etc/apache2/apache2.conf
  Include ports.conf
  IncludeOptional sites-enabled/*.conf
  ```

---

### `<Directory>`
* **¿Para qué se usa?** Encierra un grupo de directivas que se aplicarán únicamente al directorio del sistema de archivos indicado, a sus subdirectorios y a los archivos contenidos en ellos.
* **Contexto:** `server config`, `virtual host`. *(No está permitido dentro de `.htaccess`)*.
* **Ubicación en Ubuntu:**
  * `/etc/apache2/apache2.conf` (donde se definen las reglas base para `/`, `/usr/share` y `/var/www`).
  * `/etc/apache2/sites-available/*.conf` (enlazados en `/etc/apache2/sites-enabled/*.conf`).
  * `/etc/apache2/conf-available/*.conf` (enlazados en `/etc/apache2/conf-enabled/*.conf`).
* **Valor por defecto:** Por diseño interno de Apache, el acceso por defecto para `<Directory "/">` es permitir todo, pero Ubuntu lo sobrescribe por seguridad en `/etc/apache2/apache2.conf` con `Require all denied` y `AllowOverride None`.
* **Documentación oficial:** [Apache Core - `<Directory>`](https://httpd.apache.org/docs/2.4/mod/core.html#directory)
* **Ejemplo:**
  ```apache
  # Ubicación en Ubuntu: /etc/apache2/apache2.conf
  <Directory />
      Options FollowSymLinks
      AllowOverride None
      Require all denied
  </Directory>
  ```

---

### `<Files>`
* **¿Para qué se usa?** Limita el alcance de las directivas encerradas a un fichero o grupo de ficheros cuyo nombre base coincida con el patrón indicado (admite comodines como `?` y `*`). Para expresiones regulares se recomienda su variante `<FilesMatch>`.
* **Contexto:** `server config`, `virtual host`, `directory`, `.htaccess`.
* **Ubicación en Ubuntu:**
  * `/etc/apache2/apache2.conf` (por defecto incluye un bloque `<FilesMatch "^\.ht">` para proteger los `.htaccess`).
  * `/etc/apache2/sites-available/*.conf`
  * `/etc/apache2/conf-available/*.conf`
  * `/var/www/html/.htaccess` (o cualquier subdirectorio web).
* **Valor por defecto:** No tiene valor por defecto; actúa únicamente sobre los archivos que coincidan con el criterio especificado.
* **Documentación oficial:** [Apache Core - `<Files>`](https://httpd.apache.org/docs/2.4/mod/core.html#files)
* **Ejemplo:**
  ```apache
  # Ubicación en Ubuntu: /etc/apache2/apache2.conf
  <FilesMatch "^\.ht">
      Require all denied
  </FilesMatch>
  ```

---

### `<Location>`
* **¿Para qué se usa?** Aplica las directivas encerradas únicamente a las peticiones cuya **URL** coincida con la ruta especificada. Opera fuera del sistema de archivos (ideal para contenido generado dinámicamente, proxies inversos o manejadores internos como el estado del servidor).
* **Contexto:** `server config`, `virtual host`. *(No está permitido dentro de `.htaccess`)*.
* **Ubicación en Ubuntu:**
  * `/etc/apache2/mods-available/*.conf` (por ejemplo, `/etc/apache2/mods-available/status.conf` o `info.conf`).
  * `/etc/apache2/conf-available/*.conf`
  * `/etc/apache2/sites-available/*.conf`
  * `/etc/apache2/apache2.conf`
* **Valor por defecto:** No tiene valor por defecto; se evalúa después de `<Directory>`, `.htaccess` y `<Files>`.
* **Documentación oficial:** [Apache Core - `<Location>`](https://httpd.apache.org/docs/2.4/mod/core.html#location)
* **Ejemplo:**
  ```apache
  # Ubicación en Ubuntu: /etc/apache2/mods-enabled/status.conf
  <Location /server-status>
      SetHandler server-status
      Require local
  </Location>
  ```

---

### `<VirtualHost>`
* **¿Para qué se usa?** Agrupa un conjunto de directivas que solo se aplicarán a un host virtual específico (identificado por dirección IP, puerto o nombre de dominio mediante `ServerName`), permitiendo alojar múltiples sitios web en un mismo servidor.
* **Contexto:** `server config`.
* **Ubicación en Ubuntu:**
  * Archivos de configuración disponibles: `/etc/apache2/sites-available/` (por defecto viene `/etc/apache2/sites-available/000-default.conf` para HTTP y `default-ssl.conf` para HTTPS).



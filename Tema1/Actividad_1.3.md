# Actividad #1: Directivas básicas de Apache2

Análisis de las principales directivas de configuración del servidor web **Apache2** en **Ubuntu**, indicando su función, su valor por defecto y el fichero donde se definen.

---

## 1. Directivas de identificación

| Directiva | Descripción | Ubicación en Ubuntu | Valor por defecto |
| :--- | :--- | :--- | :--- |
| **`ServerName`** | Establece el nombre de dominio (y opcionalmente el puerto) que el servidor usa para identificarse. | `/etc/apache2/sites-enabled/000-default.conf` | `#ServerName ://example.com` *(comentado por defecto)* |
| **`ServerAdmin`** | Dirección de correo electrónico del administrador incluida en las páginas de error del servidor. | `/etc/apache2/sites-enabled/000-default.conf` | `webmaster@localhost` |
| **`ServerTokens`** | Controla qué información del servidor (versión, SO, módulos) se envía en la cabecera HTTP `Server`. | `/etc/apache2/conf-enabled/security.conf` | `OS` *(en Ubuntu)* / `Full` *(Apache estándar)* |

---

## 2. Directivas de localización de ficheros

| Directiva | Descripción | Ubicación en Ubuntu | Valor por defecto |
| :--- | :--- | :--- | :--- |
| **`DocumentRoot`** | Directorio raíz desde el cual Apache sirve los documentos y archivos web del sitio. | `/etc/apache2/sites-enabled/000-default.conf` | `/var/www/html` |
| **`ErrorLog`** | Ruta del fichero de registro (log) donde se almacenan los errores del servidor. | `/etc/apache2/sites-enabled/000-default.conf` | `${APACHE_LOG_DIR}/error.log` *(equivale a `/var/log/apache2/error.log`)* |
| **`CustomLog`** | Ruta del fichero de bitácora donde se registran las peticiones de acceso y el formato utilizado. | `/etc/apache2/sites-enabled/000-default.conf` | `${APACHE_LOG_DIR}/access.log combined` |
| **`ServerRoot`** | Directorio base donde se aloja la configuración del servidor (base para rutas relativas). | `/etc/apache2/apache2.conf` | `"/etc/apache2"` *(comentado por defecto)* |

> **Nota sobre `APACHE_LOG_DIR`:** Esta variable de entorno se encuentra definida en `/etc/apache2/envvars` con el valor `export APACHE_LOG_DIR=/var/log/apache2`.

---

## 3. Directivas de control de la conexión

| Directiva | Descripción | Ubicación en Ubuntu | Valor por defecto |
| :--- | :--- | :--- | :--- |
| **`Timeout`** | Tiempo máximo (en segundos) que el servidor esperará para recibir o enviar datos antes de cerrar la petición. | `/etc/apache2/apache2.conf` | `300` *(en Apache 2.4 actual; `60` en versiones antiguas)* |
| **`KeepAlive`** | Habilita o deshabilita las conexiones persistentes (múltiples peticiones HTTP sobre una misma conexión TCP). | `/etc/apache2/apache2.conf` | `On` |
| **`MaxKeepAliveRequests`** | Número máximo de peticiones permitidas dentro de una misma conexión persistente (`0` = ilimitadas). | `/etc/apache2/apache2.conf` | `100` |
| **`KeepAliveTimeout`** | Tiempo (en segundos) que el servidor esperará peticiones subsiguientes en una conexión persistente antes de cerrarla. | `/etc/apache2/apache2.conf` | `5` |

---

## 4. Otras directivas (Logs)

| Directiva | Descripción | Ubicación en Ubuntu | Valor por defecto |
| :--- | :--- | :--- | :--- |
| **`LogLevel`** | Indica el nivel de detalle o gravedad de los mensajes registrados en el `ErrorLog` (*debug, info, notice, warn, error, crit...*). | `/etc/apache2/apache2.conf` | `warn` |
| **`LogFormat`** | Define plantillas o alias con la estructura de campos que posteriormente utilizará la directiva `CustomLog`. | `/etc/apache2/apache2.conf` | Define varios alias (`combined`, `common`, `vhost_combined`, etc.) |

### Ejemplo de configuración por defecto de `LogFormat` en `apache2.conf`

```apache
LogFormat "%v:%p %h %l %u %t \"%r\" %>s %O \"%{Referer}i\" \"%{User-Agent}i\"" vhost_combined
LogFormat "%h %l %u %t \"%r\" %>s %O \"%{Referer}i\" \"%{User-Agent}i\"" combined
LogFormat "%h %l %u %t \"%r\" %>s %O" common
LogFormat "%{Referer}i -> %U" referer
LogFormat "%{User-agent}i" agent
```


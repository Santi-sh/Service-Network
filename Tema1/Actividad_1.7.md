# Actividad #7: Autenticación

En esta práctica se configura la autenticación básica (`Basic`), el control de acceso mediante usuarios y grupos, y la combinación de restricciones por IP y credenciales mediante la directiva `Satisfy` en un servidor **Apache HTTP Server 2.4** sobre **Ubuntu**.

---

## Paso 1: Preparación del entorno y módulos necesarios

En Apache 2.4, las directivas clásicas de control por host (`Order`, `Allow`, `Deny` y `Satisfy`) pertenecen al módulo de compatibilidad `mod_access_compat`. Antes de empezar, nos aseguramos de tener instalado el paquete de utilidades de Apache (`apache2-utils`) y habilitados los módulos necesarios.

```bash
sudo apt update
sudo apt install apache2 apache2-utils -y
sudo a2enmod auth_basic authz_groupfile access_compat
sudo systemctl restart apache2
```

> **[INSERTAR CAPTURA 1]:** *Terminal mostrando la activación de los módulos (`a2enmod`) y el estado activo del servicio Apache (`sudo systemctl status apache2`).*

---

## Paso 2: Creación de los usuarios y grupos

Por seguridad, los archivos de credenciales y grupos deben almacenarse fuera del directorio raíz web (`/var/www/html`) para evitar que puedan ser descargados desde el navegador. Crearemos una carpeta específica en `/etc/apache2/passwd`.

### 2.1. Crear los 5 usuarios (`usuario1` a `usuario5`)
Utilizamos el comando `htpasswd`. La opción `-c` (*create*) se usa **únicamente en el primer comando** para generar el archivo; en los siguientes se omite para añadir usuarios sin sobrescribir los anteriores.

```bash
sudo mkdir -p /etc/apache2/passwd

sudo htpasswd -c /etc/apache2/passwd/usuarios usuario1
sudo htpasswd /etc/apache2/passwd/usuarios usuario2
sudo htpasswd /etc/apache2/passwd/usuarios usuario3
sudo htpasswd /etc/apache2/passwd/usuarios usuario4
sudo htpasswd /etc/apache2/passwd/usuarios usuario5
```

### 2.2. Crear los 2 grupos (`grupo1` y `grupo2`)
Creamos el fichero de texto `/etc/apache2/passwd/grupos` y definimos la pertenencia de los usuarios:

```bash
sudo nano /etc/apache2/passwd/grupos
```

Contenido del archivo:
```text
grupo1: usuario1 usuario2
grupo2: usuario3 usuario4 usuario5
```

> **[INSERTAR CAPTURA 2]:** *Terminal ejecutando los comandos `htpasswd` y mostrando el contenido de los ficheros `/etc/apache2/passwd/usuarios` (con las contraseñas cifradas) y `/etc/apache2/passwd/grupos` mediante `cat`.*

---

## Paso 3: Creación de los directorios web (`privado1` y `privado2`)

Creamos las dos carpetas solicitadas dentro de `/var/www/html` y añadimos una página HTML de prueba en cada una para verificar visualmente el acceso.

```bash
sudo mkdir -p /var/www/html/privado1 /var/www/html/privado2

echo "<h1>Zona Privada 1 - Acceso a todos los usuarios registrados</h1>" | sudo tee /var/www/html/privado1/index.html
echo "<h1>Zona Privada 2 - Acceso restringido a Grupo 1</h1>" | sudo tee /var/www/html/privado2/index.html
```

---

## Paso 4: Configuración de `privado1` (Acceso a todos los usuarios)

Editamos el archivo de configuración del sitio por defecto (`/etc/apache2/sites-available/000-default.conf`) para añadir las directivas de autenticación.

```bash
sudo nano /etc/apache2/sites-available/000-default.conf
```

Dentro del bloque `<VirtualHost *:80>`, añadimos el siguiente bloque `<Directory>` utilizando `Require valid-user` para permitir la entrada a cualquiera de los 5 usuarios creados:

```apache
<Directory "/var/www/html/privado1">
    AuthType Basic
    AuthName "Autenticacion requerida - Privado 1"
    AuthBasicProvider file
    AuthUserFile "/etc/apache2/passwd/usuarios"
    Require valid-user
</Directory>
```

Recargamos la configuración de Apache:
```bash
sudo systemctl reload apache2
```

### Comprobación de `privado1`
1. Abrimos el navegador en Ubuntu y accedemos a `http://localhost/privado1`.
2. Introducimos las credenciales de cualquier usuario (por ejemplo, `usuario4`).

> **[INSERTAR CAPTURA 3]:** *Navegador mostrando la ventana emergente de solicitud de usuario/contraseña y el acceso concedido a `http://localhost/privado1` con `usuario4`.*

---

## Paso 5: Configuración de `privado2` (Solo `grupo1`, solo `localhost` y estudio de `Satisfy`)

Añadimos la configuración para `/var/www/html/privado2` en `/etc/apache2/sites-available/000-default.conf`, combinando la autorización por grupo (`Require group grupo1`) y la restricción por IP (`Allow from 127.0.0.1 ::1`).

### Escenario A: Prueba con `Satisfy All`

```apache
<Directory "/var/www/html/privado2">
    AuthType Basic
    AuthName "Solo Grupo 1 y Localhost"
    AuthBasicProvider file
    AuthUserFile "/etc/apache2/passwd/usuarios"
    AuthGroupFile "/etc/apache2/passwd/grupos"
    Require group grupo1

    Order Deny,Allow
    Deny from all
    Allow from 127.0.0.1 ::1

    Satisfy All
</Directory>
```

Recargamos Apache (`sudo systemctl reload apache2`) y realizamos las pruebas:

1. **Desde `localhost` con usuario del `grupo1` (`usuario1`):** Pide contraseña y permite el acceso.
2. **Desde `localhost` con usuario del `grupo2` (`usuario3`):** Pide contraseña pero rechaza el acceso (vuelve a pedir credenciales o muestra error `401 Unauthorized`).
3. **Desde otra IP (por ejemplo, accediendo mediante la IP de la tarjeta de red de la VM `http://192.168.X.X/privado2` o desde el equipo anfitrión):** Deniega el acceso directamente (`403 Forbidden`) sin llegar a pedir contraseña.

> **[INSERTAR CAPTURA 4]:** *Comprobación de `Satisfy All`: acceso correcto desde `localhost` con `usuario1` y error `403 Forbidden` al intentar entrar usando la IP de red de la máquina virtual.*

---

### Escenario B: Prueba con `Satisfy Any`

Modificamos únicamente la línea `Satisfy All` por `Satisfy Any` en `/etc/apache2/sites-available/000-default.conf` y recargamos el servicio (`sudo systemctl reload apache2`):

```apache
    Satisfy Any
```

Realizamos las mismas pruebas:

1. **Desde `localhost`:** Entra **directamente sin pedir usuario ni contraseña**, ya que cumple la condición de IP permitida.
2. **Desde otra IP (`http://192.168.X.X/privado2` o desde el equipo anfitrión):** Como no cumple la restricción de host, Apache solicita credenciales. Al introducir `usuario1` o `usuario2` (`grupo1`), concede el acceso.

> **[INSERTAR CAPTURA 5]:** *Comprobación de `Satisfy Any`: acceso directo a `http://localhost/privado2` sin ventana de login, y petición de credenciales al acceder desde la IP de red (`192.168.X.X`).*

---

## Conclusiones del estudio de la directiva `Satisfy`

| Directiva | Lógica aplicada | Comportamiento observado en `privado2` |
| :--- | :--- | :--- |
| **`Satisfy All`** *(Por defecto)* | **AND lógico** (IP **Y** Usuario) | Exige cumplir **ambas** condiciones a la vez. Es obligatorio conectarse desde `localhost` y, además, autenticarse con un usuario perteneciente a `grupo1`. |
| **`Satisfy Any`** | **OR lógico** (IP **O** Usuario) | Basta con cumplir **una** de las dos condiciones. Si se accede desde `localhost` no pide contraseña; si se accede desde fuera de `localhost`, permite entrar validándose con un usuario de `grupo1`. |


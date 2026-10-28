# Parte 1: Configuración de Apache

## 1. Apache utilizará el puerto 81 además del 80

Para añadir el puerto 81, debemos editar el archivo de puertos de Apache (en distribuciones basadas en Debian/Ubuntu se encuentra en ``/etc/apache2/ports.conf)``:

1. Abre el archivo de configuración de puertos escribiendo esto:

   ```
   sudo nano /etc/apache2/ports.conf

   ```
2. Añadimos la directiva de escucha debajo de la del puerto 80:
    ```
   Listen 80
   Listen 81

   ```
<img width="737" height="447" alt="imagen" src="https://github.com/user-attachments/assets/4941eefd-7db8-4153-a7dc-26dd3d3b9a93" />

3. Reinicia Apache para aplicar los cambios:
   
   ```
   sudo systemctl restart apache2
   ```
4. Por último para verificar que responde correctamente al puerto 81, escribimos en el navegador ``localhost:81``.

<img width="767" height="519" alt="imagen" src="https://github.com/user-attachments/assets/4edafee8-f9ab-4f68-9575-5d8369c7e879" />


## 2. Añadir el dominio “marisma.intranet” en el fichero “hosts”

Para que tu equipo resuelva este dominio localmente:

1. Abre el archivo de hosts con privilegios de administrador:

```
sudo nano /etc/hosts
```

2. Añade la siguiente línea al final del archivo:

```
127.0.0.1   marisma.intranet
```

<img width="520" height="164" alt="imagen" src="https://github.com/user-attachments/assets/c05c3d53-b404-4e73-a807-a63ca5d3e0c7" />

## 3. Cambiar la directiva “ServerTokens” para mostrar el nombre del producto

La directiva ``ServerTokens`` controla qué información envía el servidor web en la cabecera de respuesta ``HTTP Server``.

* Para mostrar únicamente el nombre del producto (por ejemplo, ``Apache``), edita el archivo de seguridad de Apache (``/etc/apache2/conf-available/security.conf`` o ``apache2.conf``) y configura:

```
sudo nano /etc/apache2/conf-available/security.conf

y cambiamos donde pone 'ServerTokens OS' por 'ServerTokens Prod'
```

<img width="563" height="196" alt="imagen" src="https://github.com/user-attachments/assets/2f68165a-d4e8-47fc-8305-67c98375aa1c" />


* Comprobación con cURL:

```
curl -I http://localhost
```

<img width="354" height="112" alt="imagen" src="https://github.com/user-attachments/assets/6db866cb-9224-4224-bbcd-1bd33f573f62" />


Observarás que la cabecera ``Server`` devuelve únicamente ``Apache`` (o el valor configurado) en lugar de detalles del sistema operativo o versiones exactas.

## 4. Comprobar el pie de página (ServerSignature)

La directiva ``ServerSignature`` añade una línea con la versión del servidor y el nombre del host virtual al pie de las páginas generadas por Apache (como páginas de error 404 o 500).

1. En el archivo de configuración (ej. ``security.conf``), localiza o añade:

```
ServerSignature On   # Muestra el pie de página
ServerSignature Off  # Oculta el pie de página
```

2. Modifícalo a ``Off`` para desactivarlo o ``On`` para activarlo, guarda los cambios y reinicia Apache. Genera un error intencionado (ej. accediendo a una URL inexistente) para comprobar el resultado visual.

<img width="367" height="101" alt="imagen" src="https://github.com/user-attachments/assets/f97a11da-7dde-497e-a7b6-6f5dcc6bc8eb" />


## 5. Crear directorios “prueba” y “prueba2” con páginas de ejemplo

1. Crea las carpetas dentro de la raíz web (``/var/www/html/``):

```
sudo mkdir -p /var/www/html/prueba /var/www/html/prueba2
```

2. Crea un par de archivos HTML sencillos en cada una:

```
echo "<h1>Página 1 de Prueba</h1>" | sudo tee /var/www/html/prueba/index.html
echo "<h1>Página 2 de Prueba</h1>" | sudo tee /var/www/html/prueba/pagina2.html

echo "<h1>Página 1 de Prueba 2</h1>" | sudo tee /var/www/html/prueba2/index.html
echo "<h1>Página 2 de Prueba 2</h1>" | sudo tee /var/www/html/prueba2/pagina2.html
```

<img width="621" height="131" alt="imagen" src="https://github.com/user-attachments/assets/d0be4b11-2107-4083-9ecd-2a6d1afcefdb" />

<img width="490" height="136" alt="imagen" src="https://github.com/user-attachments/assets/e01290b1-44b7-4d92-9b18-2e68e21c33ba" />



## 6. Redireccionar el contenido de la carpeta “prueba” hacia “prueba2”

En el archivo de configuración de tu sitio web:

```
sudo nano /etc/apache2/sites-available/000-default.conf

```

Dentro del bloque <VirtualHost *:80> ... </VirtualHost>, por ejemplo justo debajo de DocumentRoot /var/www/html, añade:

```
Redirect /prueba /prueba2
```

<img width="382" height="226" alt="imagen" src="https://github.com/user-attachments/assets/3f3cdf03-a70a-4861-9588-6728998f6e3c" />

Después de cambiarla, guarda con Ctrl+O, Enter, Ctrl+X y reinicia Apache:

```
sudo apache2ctl configtest
sudo systemctl restart apache2
```

Y compruébalo con:

```
curl -I http://localhost/prueba/
```

<img width="386" height="68" alt="imagen" src="https://github.com/user-attachments/assets/14aa2ece-4efe-44e7-a879-c0d22b6ab422" />


## 7. Redireccionar tan solo una página en lugar de toda la carpeta

Puedes especificar la redirección para un archivo concreto en lugar del directorio entero:

```
Redirect /prueba/pagina2.html /prueba2/pagina2.html
```

<img width="472" height="217" alt="imagen" src="https://github.com/user-attachments/assets/a25621d3-c391-4064-b7f9-bab1d08ff550" />


Después de cambiarla, guarda con Ctrl+O, Enter, Ctrl+X y reinicia Apache:

```
sudo apache2ctl configtest
sudo systemctl restart apache2
```

Ahora lo comprobamos en el navegador escribiendo:

```
http://localhost/prueba/pagina2.html
```

Y te lleva a ``http://localhost/prueba2/pagina2.html``

<img width="375" height="122" alt="imagen" src="https://github.com/user-attachments/assets/3ca0fc25-1e74-4a68-bd7b-55da489be7b9" />


## 8. Usa la directiva userdir

1. Habilita el módulo userdir:

```
sudo a2enmod userdir
sudo systemctl restart apache2
```

2. Esto permite que los usuarios del sistema tengan una carpeta pública (por defecto ``public_html``) accesible mediante ``http://localhost/~nombreusuario/``.


## 9. Usar la directiva Alias para redireccionar a una carpeta dentro del directorio de usuario

Puedes mapear una URL específica a una ruta absoluta en el sistema de ficheros, por ejemplo, apuntando a los documentos de un usuario:

```
Alias /mis-docs /home/usuario/public_html/documentos

<Directory /home/usuario/public_html/documentos>
    Require all granted
</Directory>
```

## 10. ¿Para qué sirve la directiva Options y cómo desactivar la indexación de directorios?

* ¿Para qué sirve y dónde aparece? La directiva ``Options`` se utiliza dentro de bloques ``<Directory>``, ``<Location>`` o en archivos ``.htaccess`` para controlar qué características del servidor están disponibles en un directorio específico (por ejemplo, ``Indexes``, ``FollowSymLinks``, ``ExecCGI``, etc.).

* Indexación y cómo desactivarla: Por defecto, si un directorio no tiene un archivo índice (como ``index.html``), Apache puede mostrar una lista con el contenido de la carpeta (``Indexes``). Para desactivarlo y evitar que los usuarios naveguen por los directorios sin índice, utiliza el signo menos ``-`` delante de la opción en la configuración:

```
<Directory /var/www/html/prueba>
    Options -Indexes
</Directory>
```

# Parte 2: Trabajando con Scripts

A continuación tienes los tres scripts en Bash solicitados, con su respectiva validación de parámetros, manejo de errores y lógica mediante ``grep`` y ``sed``.

1. Script para añadir un puerto de escucha en Apache (``anadir_puerto.sh``)

```
#!/bin/bash

# Comprobar si se recibe exactamente un parámetro
if [ "$#" -ne 1 ]; then
    echo "Error de sintaxis."
    echo "Uso: $0 <puerto>"
    exit 1
fi

PUERTO=$1
FICHERO_PUERTOS="/etc/apache2/ports.conf"

# Comprobar si el archivo existe
if [ ! -f "$FICHERO_PUERTOS" ]; then
    echo "Error: No se encuentra el archivo $FICHERO_PUERTOS."
    exit 1
fi

# Comprobar si el puerto ya está presente en el fichero
if grep -q "Listen $PUERTO" "$FICHERO_PUERTOS"; then
    echo "El puerto $PUERTO ya está configurado en $FICHERO_PUERTOS."
    exit 0
fi

# Añadir el puerto al final del archivo
echo "Listen $PUERTO" | sudo tee -a "$FICHERO_PUERTOS" > /dev/null
echo "Puerto $PUERTO añadido correctamente a $FICHERO_PUERTOS."
sudo systemctl reload apache2
```

2. Script para añadir un dominio y una IP al fichero hosts (``anadir_hosts.sh``)

```
#!/bin/bash

# Comprobar número de parámetros (IP y Dominio)
if [ "$#" -ne 2 ]; then
    echo "Error de sintaxis."
    echo "Uso: $0 <IP> <dominio>"
    exit 1
fi

IP=$1
DOMINIO=$2
FICHERO_HOSTS="/etc/hosts"

# Comprobar si el dominio ya existe en el fichero hosts
if grep -q "[[:space:]]$DOMINIO\$" "$FICHERO_HOSTS" || grep -q "[[:space:]]$DOMINIO[[:space:]]" "$FICHERO_HOSTS"; then
    echo "El dominio '$DOMINIO' ya existe en $FICHERO_HOSTS."
    exit 0
fi

# Añadir la entrada al fichero hosts
echo "$IP   $DOMINIO" | sudo tee -a "$FICHERO_HOSTS" > /dev/null
echo "Se ha añadido correctamente '$IP $DOMINIO' a $FICHERO_HOSTS."
```

3. Script para crear una página web básica (``crear_pagina.sh``)

```
#!/bin/bash

# Comprobar número de parámetros (Ruta/Fichero, Título, Cabecera, Mensaje)
if [ "$#" -ne 4 ]; then
    echo "Error de sintaxis."
    echo "Uso: $0 <ruta_fichero.html> <título> <cabecera> <mensaje>"
    exit 1
fi

FICHERO=$1
TITULO=$2
CABECERA=$3
MENSAJE=$4

# Generar la estructura HTML
cat << EOF | sudo tee "$FICHERO" > /dev/null
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <title>$TITULO</title>
</head>
<body>
    <h1>$CABECERA</h1>
    <p>$MENSAJE</p>
</body>
</html>
EOF

echo "Página web creada con éxito en: $FICHERO"
```







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

## 3. Cambiar la directiva “ServerTokens” para mostrar el nombre del producto

La directiva ``ServerTokens`` controla qué información envía el servidor web en la cabecera de respuesta ``HTTP Server``.

* Para mostrar únicamente el nombre del producto (por ejemplo, ``Apache``), edita el archivo de seguridad de Apache (``/etc/apache2/conf-available/security.conf`` o ``apache2.conf``) y configura:

```
ServerTokens Prod
```

* Comprobación con cURL:

```
curl -I http://localhost
```

Observarás que la cabecera ``Server`` devuelve únicamente ``Apache`` (o el valor configurado) en lugar de detalles del sistema operativo o versiones exactas.

## 4. Comprobar el pie de página (ServerSignature)

La directiva ``ServerSignature`` añade una línea con la versión del servidor y el nombre del host virtual al pie de las páginas generadas por Apache (como páginas de error 404 o 500).

1. En el archivo de configuración (ej. ``security.conf``), localiza o añade:

```
ServerSignature On   # Muestra el pie de página
ServerSignature Off  # Oculta el pie de página
```

2. Modifícalo a ``Off`` para desactivarlo o ``On`` para activarlo, guarda los cambios y reinicia Apache. Genera un error intencionado (ej. accediendo a una URL inexistente) para comprobar el resultado visual.


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

## 6. Redireccionar el contenido de la carpeta “prueba” hacia “prueba2”

En el archivo de configuración de tu sitio web (o en un archivo ``.htaccess`` dentro de ``/var/www/html/``), añade la siguiente directiva de redirección:

```
Redirect /prueba /prueba2
```

## 7. Redireccionar tan solo una página en lugar de toda la carpeta

Puedes especificar la redirección para un archivo concreto en lugar del directorio entero:

```
Redirect /prueba/pagina2.html /prueba2/pagina2.html
```




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










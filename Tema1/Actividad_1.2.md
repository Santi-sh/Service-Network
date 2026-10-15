# Parte 1: Configuración de Apache

## 1. Apache utilizará el puerto 81 además del 80

Para añadir el puerto 81, debemos editar el archivo de puertos de Apache (en distribuciones basadas en Debian/Ubuntu se encuentra en ``/etc/apache2/ports.conf)``:

1. Abre el archivo de configuración de puertos escribiendo esto:

   ```
   sudo nano /etc/apache2/ports.conf

   ```
2. Añadimos la directiva de escucha debajo de la del puerto 80:
    ```
   Listen 81

   ```
<img width="737" height="447" alt="imagen" src="https://github.com/user-attachments/assets/4941eefd-7db8-4153-a7dc-26dd3d3b9a93" />

3. Reinicia Apache para aplicar los cambios:
   
   ```
   sudo systemctl restart apache2
   ```
4. Por último para verificar que responde correctamente al puerto 81, escribimos en el navegador ``localhost:81``.

<img width="767" height="519" alt="imagen" src="https://github.com/user-attachments/assets/4edafee8-f9ab-4f68-9575-5d8369c7e879" />


# 2. Añadir el dominio “marisma.intranet” en el fichero “hosts”

Para que tu equipo resuelva este dominio localmente:


# 8. Usa la directiva userdir

1. sudo a2enmd userdir 
2. mkdir /home/usuario/public_html
3. nano /home/usuario/public_html/index.html
4. sudo apachectl configtest
5. service apache2 restart
6. chmod 755 7/home/usuario







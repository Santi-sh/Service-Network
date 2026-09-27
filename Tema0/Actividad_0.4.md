# Actividad 0.4 Usando cUrl

El comando cURL (Client URL) es una herramienta de línea de comandos de código abierto utilizada para transferir datos hacia o desde un servidor a través de múltiples protocolos de red (como HTTP, HTTPS, FTP, SFTP, SMTP, entre otros).

A diferencia de Telnet, cURL gestiona de forma automática los certificados de seguridad SSL/TLS (HTTPS), admite redirecciones automáticas y permite inspeccionar cabeceras, enviar archivos o personalizar peticiones con total precisión.

## 5 Ejemplos prácticos de uso de cURL

1. Realizar una petición GET simple para descargar el código HTML\
``Descarga e imprime en la consola el contenido de una página web.``

<img width="691" height="174" alt="imagen" src="https://github.com/user-attachments/assets/e81148a3-1b05-4bf3-bfaa-5e812e28e576" />

2. Guardar el contenido en un archivo local (-o o -O)\
``Permite descargar un archivo de internet y guardarlo en el disco.``

<img width="643" height="159" alt="imagen" src="https://github.com/user-attachments/assets/bdf058a9-2698-4b41-91b7-a89845107871" />

3. Obtener únicamente las cabeceras HTTP (-I / --head)\
``Muestra la respuesta del servidor (código de estado 200 OK, tipo de servidor, fechas, tipo de contenido) sin descargar el cuerpo de la página (equivalente a la petición HEAD que vimos con Telnet).``

<img width="601" height="166" alt="imagen" src="https://github.com/user-attachments/assets/642acd20-b4d6-47b9-b12a-d0f14ec80af6" />

4. Seguir redirecciones automáticamente (-L)\
``Si un sitio web redirige la petición (por ejemplo, de http:// a https:// con un código 301 o 302), el parámetro -L indica a cURL que siga el enlace de redirección hasta obtener el resultado final.``
<img width="513" height="131" alt="imagen" src="https://github.com/user-attachments/assets/15a5c7ab-a2be-4e3d-a74d-a7660e44733a" />

5. Enviar datos mediante un formulario o método POST (-X POST y -d)\
``Permite enviar información a una API o formulario web especificando los parámetros de entrada.``
<img width="818" height="156" alt="imagen" src="https://github.com/user-attachments/assets/0cc61372-1716-46aa-86ef-f2637948beaa" />




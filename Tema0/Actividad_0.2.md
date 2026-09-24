# Actividad 0.2 TCP y UDP

## 1. ¿Diferencias entre UDP y TCP?

``TCP es un protocolo que controla la entrega de los datos. Los datos se envían de forma numerada y, si alguno se pierde, se puede volver a solicitar. Por eso es un protocolo más fiable.``

``UDP, en cambio, busca principalmente que los datos se envíen rápidamente. No comprueba que todos los datos hayan llegado correctamente, por lo que puede haber pérdidas. Esto es útil en aplicaciones como juegos online, streaming o videollamadas, donde es más importante reducir el retraso.``

![tpc](/Imagenes/tcp_udp.png)

## ¿Qué es pila de protocolos usados por http?

``Pila de protocolos TCP/IP.``

## ¿Componentes de una URL?

```

- Protocolo: Indica cómo acceder al recurso
(ej. http, https, ftp) seguido de dos puntos y doble barra (://).

- Dominio: El nombre del servidor o máquina que aloja el recurso, incluyendo
opcionalmente un dominio de nivel superior (ej. .com, .org).

- Puerto (Opcional): Indica el puerto de comunicación del servidor
(por defecto es el puerto 80 para HTTP web normal,
u otros como 3000 en entornos de desarrollo).

- Ruta (Path): La ruta detallada dentro de la estructura de archivos o
jerarquía de datos del servidor para localizar
el recurso (ej. /productos/index.html).

```

## ¿Pasos en la recuperación de una página web mediante HTTP?

``Cuando escribes una URL en el navegador y le das a Enter, pasa todo esto:``

```
1. Petición (Request): El navegador (cliente) crea un mensaje HTTP
de tipo GET pidiendo la página o archivo que quieres ver,
y lo manda por la red usando la conexión TCP/IP hacia
el servidor correspondiente.

2. Respuesta (Response): El servidor prepara la respuesta metiendo el
archivo dentro de un mensaje HTTP y se lo devuelve al cliente.

```

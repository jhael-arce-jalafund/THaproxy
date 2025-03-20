# Balanceo de Carga con HAProxy y Flask

## Introducción
El **balanceo de carga** es una técnica utilizada para distribuir el tráfico entrante entre múltiples servidores, mejorando el rendimiento, la disponibilidad y la tolerancia a fallos de una aplicación. **HAProxy** (High Availability Proxy) es una solución de balanceo de carga de código abierto ampliamente utilizada para aplicaciones web.

En este proyecto, configuramos HAProxy para distribuir el tráfico entre tres instancias de una aplicación Flask ejecutadas en contenedores Docker. HAProxy se encargará de redirigir las solicitudes entrantes a los servidores disponibles de manera equitativa mediante el algoritmo **roundrobin**.

## ¿Cómo funciona el balanceo de carga con HAProxy?
HAProxy opera en la capa de red y transporte (capas 4 y 7 del modelo OSI) para distribuir el tráfico entre servidores backend según diferentes estrategias de balanceo. En nuestro caso, usamos **roundrobin**, un método que asigna solicitudes de manera secuencial y equitativa entre los servidores disponibles.

Cuando un usuario accede a la aplicación web, HAProxy recibe la solicitud en el puerto 80 y selecciona uno de los servidores disponibles (`web1`, `web2`, `web3`) para procesarla. Esto evita que un solo servidor maneje todas las solicitudes y mejora el rendimiento.

---

## Configuración de HAProxy
El archivo `haproxy.cfg` define la forma en que HAProxy distribuye las solicitudes:

```cfg
frontend http_front
    bind *:80  # HAProxy escucha en el puerto 80
    default_backend web_servers
    stats uri /haproxy_stats  # Página de estadísticas

backend web_servers
    balance roundrobin  # Método de balanceo de carga
    server web1 web1:5000 check  # Verifica que el servidor esté activo
    server web2 web2:5000 check
    server web3 web3:5000 check
```

### Explicación de la Configuración
- **frontend http_front**: Define la interfaz de entrada de HAProxy.
  - `bind *:80`: Escucha en el puerto 80 todas las solicitudes HTTP.
  - `default_backend web_servers`: Redirige las solicitudes al backend definido.
  - `stats uri /haproxy_stats`: Permite ver estadísticas en `http://localhost:8090/haproxy_stats`.
- **backend web_servers**: Define los servidores disponibles para distribuir la carga.
  - `balance roundrobin`: Usa el algoritmo **roundrobin** para distribuir equitativamente las solicitudes.
  - `server web1 web1:5000 check`: Define un servidor backend y verifica su disponibilidad.

---
### Explicación
1. Se crean tres contenedores `web1`, `web2`, `web3`, cada uno ejecutando la aplicación Flask.
2. Se define un contenedor `haproxy` que gestiona el balanceo de carga.
3. Se configura un **bridge network** (`load_balancer_network`) para permitir la comunicación entre los contenedores.
4. HAProxy escucha en el puerto 80 y redirige las solicitudes a uno de los tres servidores Flask según el algoritmo `roundrobin`.

---

## Ejecución del Proyecto
1. Clonar el repositorio
2. Construir y levantar los contenedores:
   ```sh
   docker-compose up -d --build
   ```
3. Acceder a la aplicación desde `http://localhost/8090`.
4. Comprobar el balanceo de carga refrescando la página y observando cómo cambia el hostname.
5. Ver estadísticas de HAProxy en `http://localhost:8090/haproxy_stats`.

---

## Conclusión
En este proyecto, implementamos un sistema de balanceo de carga con **HAProxy** para distribuir solicitudes entre múltiples servidores Flask. Este enfoque mejora la disponibilidad y escalabilidad de la aplicación, asegurando que ninguna instancia individual se sobrecargue. 

Gracias a HAProxy y Docker, podemos administrar de manera eficiente múltiples servidores backend y distribuir la carga de manera equilibrada. 🚀

---

### Referencias
- [HAProxy Documentation](http://www.haproxy.org/)



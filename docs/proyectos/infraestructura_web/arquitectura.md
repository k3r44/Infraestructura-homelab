# Arquitectura de producción web
## Objetivos arquitectónicos

La infraestructura web de producción se ha diseñado en torno a cuatro objetivos principales:

Separar la infraestructura expuesta a Internet de las cargas de trabajo de las aplicaciones.
Evitar exponer los servidores de aplicaciones directamente a la Internet pública.
Permitir que varios servicios web compartan el mismo punto de entrada.
Mantener una arquitectura sencilla que pueda ampliarse a medida que crezca el laboratorio doméstico (*homelab*).
# Arquitectura lógica

El entorno de producción se divide en dos cargas de trabajo principales:

# Pasarela (*Gateway*)

La pasarela se encarga de recibir el tráfico de Cloudflare y de enrutar las solicitudes hacia los servicios internos.

# Responsabilidades:

Mantener la conexión del túnel de Cloudflare (*Cloudflare Tunnel*).
Terminar y enrutar las solicitudes HTTP/HTTPS entrantes.
Actuar como punto de entrada único para los servicios publicados externamente.
Reenviar las solicitudes a los servidores de aplicaciones internos.

Diagrama de arquitectura
                     Internet público
                            │
                            ▼
                    ┌──────────────┐
                    │  Cloudflare   │
                    └───────┬──────┘
                            │
                    Conexión de túnel
                            │
                            ▼
                  ┌──────────────────┐
                  │     Pasarela     │
                  │      LXC         │
                  │                  │
                  │ cloudflared      │
                  │ Nginx Proxy      │
                  │ Manager          │
                  └────────┬─────────┘
                           │
                     Red interna
                           │
                           ▼
                  ┌──────────────────┐
                  │    Portafolio    │
                  │       LXC        │
                  │                  │
                  │      Nginx       │
                  └──────────────────┘



# Decisiones de arquitectura
## Pasarela dedicada

El proxy inverso y el cliente de Cloudflare Tunnel están separados de la carga de trabajo del portafolio.

Esto permite que la puerta de enlace (gateway) actúe como punto de entrada común para futuros servicios web, sin necesidad de que cada aplicación gestione de forma independiente la conectividad externa.

## Carga de trabajo de la aplicación dedicada

La aplicación "Portfolio" se aloja en un contenedor LXC independiente, en lugar de ejecutarse directamente en la puerta de enlace.

Esto proporciona separación de cargas de trabajo y permite gestionar, reemplazar o reconstruir la aplicación independientemente de la capa de acceso externo.

## Sin exposición directa de entrada

La arquitectura inicial no requiere redirección directa de puertos de entrada desde el router hacia el laboratorio doméstico (homelab).

La conectividad externa se proporciona a través de Cloudflare Tunnel, lo que reduce el número de servicios expuestos directamente a la Internet pública.

## Escalabilidad

La arquitectura está diseñada deliberadamente en torno a una única puerta de enlace.

Es posible añadir servicios adicionales detrás de la puerta de enlace sin modificar el punto de entrada externo.

Por ejemplo:

                 Puerta de enlace
                        │
           ┌────────────┼────────────┐
           ▼            ▼            ▼
       Portfolio     Sitio web 2  Sitio web 3
          LXC           LXC          LXC

Esto permite que el entorno de producción crezca de manera incremental, manteniendo al mismo tiempo un punto de entrada a la red coherente.




## planteamiento a medio-largo plazo
 Utilizar la infraestructura previamente planteada escalable para potencialmente alojar paginas web ligeras y ofrecer un servicio de hosting para paginas web enfocadas en la exposicion publica de productos y servicios de terceros.
 

                          INTERNET
                            │
                         Cloudflare
                            │
                     Cloudflare Tunnel
                            │
                            ▼
                  ┌──────────────────┐
                  │   Gateway LXC    │
                  │                  │
                  │ cloudflared      │
                  │ Nginx Proxy Mgr  │
                  └────────┬─────────┘
                           │
              ┌────────────┴────────────┐
              ▼                         ▼
      ┌─────────────────┐       ┌─────────────────────┐
      │ Portafolio LXC   │      │ Client Hosting LXC  │
      │                  │      │                     │
      │ Nginx            │      │ Nginx               │
      │ Portafolio       │      │ Site 1              │
      └─────────────────┘       │ Site 2              │
                                │ Site 3              │
                                │ ...                 │
                                └─────────────────────┘
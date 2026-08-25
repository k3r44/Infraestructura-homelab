# Arquitectura de producción web

## Objetivos arquitectónicos

La infraestructura web de producción se ha diseñado en torno a cuatro objetivos principales:

* Separar la infraestructura expuesta a Internet de las cargas de trabajo de las aplicaciones.
* Evitar exponer los servidores de aplicaciones directamente a Internet.
* Permitir que varios servicios web compartan el mismo punto de entrada.
* Mantener una arquitectura sencilla que pueda ampliarse a medida que crezca el laboratorio doméstico (*homelab*).

## Arquitectura lógica

El entorno de producción se divide inicialmente en dos cargas de trabajo principales:

1. **Pasarela (*Gateway*)**
2. **Carga de trabajo de la aplicación**

### Pasarela (*Gateway*)

La pasarela se encarga de recibir el tráfico proveniente de Cloudflare y enrutar las solicitudes hacia los servicios internos.

#### Responsabilidades

* Mantener la conexión del túnel de Cloudflare (*Cloudflare Tunnel*).
* Recibir y enrutar las solicitudes HTTP/HTTPS entrantes.
* Actuar como punto de entrada único para los servicios publicados externamente.
* Reenviar las solicitudes a los servidores de aplicaciones internos.

### Sistema externo de CLoudFlare Tunnels
Este permitira exponer las paginas alojadas dentro de la maquina a la red sin tener que realizar configuraciones extra al router como aperturas de puertos, asi como una capa extra de seguridad y a su vez, siendo esta la que percibira la mayor cantidad de trabajo a la hora de querer acceder a los recursos web que se alojaran
### LXC Gateway:
Este se encargara de funcionar como intermedio entre el exterior y el servicio que la maquina estara alojando, este se encargara de hacer la conexion con el CLoudFlare tunnel y de distribuir el trafico sin sobrecargar el servicio, adicionalmente, en este estaran las reglas y protocolos de firewall para tener control total del tipo de trafico que este XLC dispondra.
Arquitectura LXC Gateway en C4 nivel 3:

```text
                    
                                    LXC GATEWAY
                                CloudFlare Tunnel 
                                        │
                                        │ HTTP/HTTPS
                                        ▼
                                ┌─────────────────────────────────────────────────┐
                                │                             ┌────────────────┐  │
                                │  ┌──────────────┐           │  Node_Exporter │  │
                                │  │  cloudflared │           │ monitoring     │  │
                                │  └──────┬───────┘           └────────────────┘  │
                                │         │ Trafico reenviado                     │
                                │         ▼                                       │
                                │  ┌──────────────┐                               │             
                                │  │    Nginx     │                               │
                                │  │ Reverse Proxy│                               │
                                │  └──────────────┘                               │   
                                │                                                 │
                                │                                                 │
                                │  ┌─────────────────────────────────────┐        │
                                │  │              nftables               │        │
                                │  │        Política de firewall         │        │
                                │  └─────────────────────────────────────┘        │
                                │                                                 │
                                └─────────────────────────────────────────────────┘
                                        │
                                        │ HTTP/HTTPS
                                        ▼
                                ┌───────────────────────┐
                                │ Portafolio Web LXC    │
                                │                       │
                                │ Servicio web          │
                                └───────────────────────┘
```

### LXC WEB
Este contenedor no solamente tiene la responsabilidad de alojar el portafolio, ya que se necesita que este sea capaz de servir la pagina de forma adecuada al gateway y tener las suficientes herramientas para no colapsar durante el proceso, por lo cual este contenedor dispondra de nginx como un proxy inverso que servira la pagina al menciondado, por elementos de mera seguridad, este contenedor dispondra tambien de sus propias politicas de firewall usando nftables para mantener solo la comunicacion con los puertos necesarios en la direccion requerida (entrada/salida) adicionalmente (como elemento fundamental de la fase #3 de este desarrollo) se le agregara a este y a todos los contenedores y vm que se utilicen Node_exporter para tener disponible informacion general acerca del estado de la maquina.
Arquitectura LXC Portafolio en C4 nivel 3:

                                        LXC WEB
                         ┌─────────────────────────────────────────────────┐
                         │  ┌──────────────┐            ┌──────────────┐   │           
                         │  │    Nginx     │            │ node_exporter│   │          
                         │  │  Web Server  │            │ monitoring   │   │            
                         │  └──────┬───────┘            └──────────────┘   │                   
                         │         │                                       │
                         │         ▼                                       │
                         │  ┌────────────────┐                             │
                         │  │  Portafolio    │                             │
                         │  │Web Application │                             │
                         │  └────────────────┘                             │
                         │                                                 │                           
                         │                                                 │
                         │  ┌─────────────────────────────────────┐        │
                         │  │              nftables               │        │
                         │  │        Política de firewall         │        │
                         │  └─────────────────────────────────────┘        │
                         │                                                 │
                         └─────────────────────────────────────────────────┘
### Diagrama de arquitectura

```text
                         Internet público
                                │
                                ▼
                       ┌────────────────┐
                       │   Cloudflare   │
                       └───────┬────────┘
                               │
                     Cloudflare Tunnel
                               │
                               ▼
                    ┌────────────────────┐
                    │      Pasarela      │
                    │        LXC         │
                    │                    │
                    │    cloudflared     │
                    │  Nginx Proxy Mgr   │
                    └─────────┬──────────┘
                              │
                         HTTPS
                              │
                              ▼
                    ┌────────────────────┐
                    │     Portafolio     │
                    │        LXC         │
                    │                    │
                    │       Nginx        │
                    └────────────────────┘
```




## Decisiones de arquitectura
## Uso de CloudFlare Tunnels
 Al ser (actualmente) un router domestico, la apertura de puertos y de trafico no esta autorizado, por lo que se tendra que usar herramientas externas que permiten la exposicion de los recursos y el trafico sin necesidad de realizar cambios en el router, siendo esta la opcion mas adecuada dados las herramientas con las cuales se dispone para el proyecto. 

### Pasarela dedicada

El proxy inverso y el cliente de Cloudflare Tunnel están separados de la carga de trabajo del portafolio.

Esto permite que la puerta de enlace (*gateway*) actúe como punto de entrada común para futuros servicios web, sin necesidad de que cada aplicación gestione de forma independiente la conectividad externa.

### Carga de trabajo de la aplicación dedicada

La aplicación **Portafolio** se aloja en un contenedor LXC independiente, en lugar de ejecutarse directamente en la puerta de enlace.

Esto proporciona separación de cargas de trabajo y permite gestionar, reemplazar o reconstruir la aplicación independientemente de la capa de acceso externo.

### Sin exposición directa de entrada

La arquitectura inicial no requiere redirección directa de puertos de entrada desde el router hacia el laboratorio doméstico (*homelab*).

La conectividad externa se proporciona a través de Cloudflare Tunnel, lo que reduce el número de servicios expuestos directamente a Internet.

### Escalabilidad

La arquitectura está diseñada deliberadamente en torno a una única puerta de enlace.

Es posible añadir servicios adicionales detrás de la puerta de enlace sin modificar el punto de entrada externo.

Por ejemplo:

```text
                         Puerta de enlace
                                │
              ┌─────────────────┼─────────────────┐
              │                 │                 │
              ▼                 ▼                 ▼
        ┌───────────┐     ┌───────────┐     ┌───────────┐
        │ Portafolio│     │ Sitio web │     │ Sitio web │
        │    LXC    │     │     2     │     │     3     │
        └───────────┘     │    LXC    │     │    LXC    │
                          └───────────┘     └───────────┘
```

Esto permite que el entorno de producción crezca de manera incremental, manteniendo al mismo tiempo un punto de entrada a la red coherente.

## Planteamiento a medio y largo plazo

La infraestructura se plantea de forma modular y escalable con el objetivo de poder alojar, potencialmente, páginas web ligeras y ofrecer servicios de hosting para páginas web enfocadas en la exposición pública de productos y servicios de terceros.

La arquitectura planteada a medio y largo plazo sería similar a la siguiente:

```text
                              INTERNET
                                  │
                                  ▼
                             Cloudflare
                                  │
                                  ▼
                        Cloudflare Tunnel
                                  │
                                  ▼
                    ┌────────────────────────┐
                    │      Gateway LXC       │
                    │        Firewall        │
                    │      cloudflared       │
                    │    Nginx Proxy Mgr     │
                    │                        │                         
                    └───────────┬────────────┘
                                │
                   ┌────────────┴────────────┐
                   │                         │
                   ▼                         ▼
          ┌─────────────────┐      ┌─────────────────────┐
          │  Portafolio LXC │      │ Client Hosting LXC  │
          │                 │      │                     │
          │      Nginx      │      │        Nginx        │
          │    Portafolio   │      │       Sitio 1       │
          └─────────────────┘      │       Sitio 2       │
                                   │       Sitio 3       │
                                   │         ...         │
                                   └─────────────────────┘
```

Esta separación permitiría mantener el portafolio como una carga de trabajo independiente mientras se incorpora posteriormente una infraestructura destinada al alojamiento de sitios de terceros.

La arquitectura podría continuar ampliándose mediante la incorporación de nuevas cargas de trabajo detrás de la misma pasarela, manteniendo una separación lógica entre servicios y evitando modificar innecesariamente la infraestructura de acceso externo.





                                  ┌────────────────┐                               
                                  │ Node Exporter  │                               
                                  │ monitoring     │                               
                                  └────────────────┘ 
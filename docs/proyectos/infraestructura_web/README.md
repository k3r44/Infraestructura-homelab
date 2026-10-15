# Infraestructura de producción web
## Descripción general

Este proyecto implementa la primera carga de trabajo de producción del *homelab*: un entorno de alojamiento web pequeño y autogestionado, diseñado para exponer aplicaciones web y sitios personales a Internet.

La infraestructura se basa en Proxmox VE y utiliza cargas de trabajo en contenedores para separar la puerta de enlace (gateway) expuesta al público de las aplicaciones alojadas.

El despliegue inicial albergará mi portafolio personal, manteniendo al mismo tiempo una estructura que permita desplegar sitios web o servicios adicionales en el futuro sin necesidad de rediseñar toda la infraestructura.

# #bjetivos

Los objetivos principales de este proyecto son:

Desplegar y operar un servicio web real utilizando infraestructura autogestionada.
Aprender y practicar la virtualización y la gestión de contenedores con Proxmox VE.
Implementar una arquitectura de proxy inverso para múltiples servicios web.
Publicar servicios de forma segura mediante Cloudflare Tunnel, sin exponer directamente el *homelab* al tráfico entrante de Internet.
Establecer una separación básica entre la puerta de enlace de la infraestructura y las cargas de trabajo de las aplicaciones.
Crear una base que pueda soportar aplicaciones web adicionales en el futuro.
Documentar la arquitectura, el proceso de despliegue y las decisiones operativas como parte del proyecto del *homelab*.
Alcance inicial

El entorno de producción inicial consta de:

Cloudflare Tunnel para la conectividad externa.
Un contenedor dedicado para la puerta de enlace, que ejecuta el cliente del túnel y el proxy inverso.
Un contenedor dedicado que aloja el portafolio personal.
Redes internas entre la puerta de enlace y las cargas de trabajo de las aplicaciones.
Monitorización básica de servicios a través del conjunto de herramientas de monitorización del *homelab*.


## Tecnologías previstas
Componente	Tecnología
Hipervisor	Proxmox VE
Puerta de enlace	LXC
Conectividad externa	Cloudflare Tunnel
Proxy inverso	Nginx Proxy Manager
Servidor web	Nginx
Aplicación	Portafolio personal
Monitoreo	Prometheus + Grafana
Control de versiones	Git / GitHub
Expansión futura
El entorno de producción está diseñado para admitir cargas de trabajo adicionales en el futuro, tales como:

## Sitios web adicionales.
Aplicaciones web personales.
Servicios internos que podrían requerir acceso externo controlado más adelante.
Despliegues automatizados.
Registro centralizado de eventos (logging).
Automatización de la infraestructura.

El endurecimiento de la seguridad, la segmentación avanzada de redes, el despliegue automatizado y una mayor disponibilidad se consideran fases futuras, más que requisitos para la implementación inicial.

# Estado actual

## Fase: Implementación inicial del Gateway

La infraestructura ha pasado de diseño a implementación. Nginx está
instalado y habilitado en el Gateway, con HTTPS y redirección HTTP probados
localmente. Este avance no significa que el Gateway esté desplegado y
operativo como reverse proxy: el backend del portafolio y varios componentes
de acceso, seguridad y monitorización siguen pendientes. El detalle de las
pruebas y sus límites está en [desplieque.md](desplieque.md).

- Arquitectura inicial y asignación de recursos documentadas.
- Diagramas C4 de nivel 2 y nivel 3 desarrollados.
- Nginx y HTTPS configurados y verificados localmente en el Gateway.
- Pendientes: revisar y probar `nftables`, instalar Node Exporter y
        Cloudflare Tunnel, distribuir la CA raíz y validar el acceso desde otro
        equipo de la LAN.

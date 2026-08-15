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

## Fase: Diseño y documentación

        x La infraestructura aún no se ha desplegado como carga de trabajo de producción. La fase actual se centra en
        definir la arquitectura, la asignación de recursos, la red y los requisitos operativos antes del despliegue.
        Definicion de elementos que conformaran la arquitectura inicial (ver arquitectura.md)
        X Desarrollo de diagrama C4 nivel 2 de la arquitectura general (ver ./diagrams/C2_Produccion_Web.mmd)
        X Desarrollo de diagrama C4 nivel 3 para el contenedor gateway LXC (./diagrams/C3_LXC_Gateway.mmd)

Sí bro. Manteniendo **exactamente el estilo que estás usando en `topologia-red.md`**, yo lo dejaría así. Además corregí algunas cosas de redacción para que quede más profesional sin cambiar tu forma de documentarlo.

````markdown
# TOPOLOGIA DE RED

# Diseño físico actual (05-08-2026)

                         Diseño físico actual

                  ┌──────────────────┐
                  │  Router principal│
                  │    192.168.0.1   │
                  └────────┬─────────┘
                           │
                      Wi-Fi 2.4 GHz
                           │
                           ▼
                  ┌──────────────────┐
                  │    Repetidor     │
                  └────────┬─────────┘
                           │
                      Ethernet Cat5
                           │
                           ▼
              ┌────────────────────────┐
              │ Lenovo ThinkCentre     │
              │ M70q 2da generación    │
              │        Proxmox         │
              └────────────────────────┘


## Nodo Principal

Hostname: homelab.k3r4.home.arpa  
IP: 192.168.0.120  
Gateway: 192.168.0.1  
DNS: 1.1.1.1  
Red: 192.168.0.0/24


# Resultados de conectividad

## Antes (Wi-Fi directo desde repetidor)

Descarga: ~33 Mbps  
Subida: ~20 Mbps  
Latencia bajo carga: ~268 ms  

## Después (Ethernet desde repetidor)

Descarga: 50.35 Mbps  
Subida: 19.35 Mbps  
Latencia bajo carga: 15 ms  

## Conclusión

El uso de Ethernet entre el repetidor y el nodo Proxmox redujo significativamente
la latencia bajo carga y mejoró la estabilidad de la conexión, proporcionando una
base adecuada para ejecutar servicios virtualizados y hosting ligero.


# Diseño lógico actual

La infraestructura utiliza actualmente la red doméstica existente debido a las
limitaciones del router y a la ausencia de infraestructura de red gestionable.

Actualmente no es posible implementar subnetting o VLANs para aislar el entorno
del homelab de la red doméstica.

La red utilizada es:

Red: 192.168.0.0/24  
Gateway: 192.168.0.1  
Nodo Proxmox: 192.168.0.120


## Conectividad de Proxmox

La interfaz física `nic0` se encuentra conectada al bridge `vmbr0`, utilizado por
Proxmox para proporcionar conectividad de red al host y a las máquinas virtuales
y contenedores que se conecten a dicho bridge.

```text
                    Proxmox
                       │
                       │
                  ┌────▼────┐
                  │  nic0   │
                  │ Físico  │
                  └────┬────┘
                       │
                    ┌──▼───┐
                    │vmbr0 │
                    └──┬───┘
                       │
              ┌────────┴────────┐
              │                 │
              ▼                 ▼
         Gateway LXC       Portfolio LXC
````

Configuración actual:

Interfaz física: nic0
Bridge: vmbr0
IP del bridge: 192.168.0.120/24
Gateway: 192.168.0.1
STP: desactivado

# Producción Web

La infraestructura de producción utilizará inicialmente la misma red
`192.168.0.0/24` debido a las limitaciones actuales de la infraestructura.

La arquitectura estará compuesta por dos LXC principales:

```text
                         Router
                      192.168.0.1
                           │
                           │
                       Proxmox
                           │
                         vmbr0
                           │
                ┌──────────┴──────────┐
                │                     │
                ▼                     ▼
          ┌────────────┐       ┌────────────┐
          │ Gateway LXC│       │PortfolioLXC│
          │ .0.121     │       │ .0122      │
          └────────────┘       └────────────┘
```

## Direccionamiento inicial

Router: 192.168.0.1
Proxmox: 192.168.0.120
Gateway LXC: 192.168.0.121
Portfolio LXC: 192.168.0.122

Las direcciones de los LXC serán configuradas inicialmente de forma estática.

Debido a que no se dispone de acceso administrativo al router, no es posible
verificar directamente el rango utilizado por el servidor DHCP.

Antes de asignar las direcciones destinadas a los LXC se comprobará que no se
encuentren actualmente en uso dentro de la LAN.

# Flujo de producción

La exposición de los servicios web no dependerá de port forwarding en el router.

El acceso externo utilizará Cloudflare Tunnel:

```text
                           INTERNET
                               │
                               ▼
                          Cloudflare
                               │
                        Cloudflare Tunnel
                               │
                               ▼
                         Gateway LXC
                               │
                       ┌───────┴───────┐
                       │               │
                  cloudflared        Nginx
                                       │
                                       ▼
                                 Portfolio LXC
                                       │
                                       ▼
                                   Portfolio
```

El Portfolio no será expuesto directamente a Internet.

El Gateway funcionará como punto de entrada para los servicios web.

# Firewall

Cada LXC contará con `nftables` para controlar las conexiones entrantes y
salientes.

La política seguirá el principio de mínimo privilegio:

* Denegar conexiones no autorizadas.
* Permitir únicamente los servicios necesarios.
* Restringir la comunicación entre los LXC.
* Permitir únicamente las conexiones necesarias para administración.
* Permitir el tráfico necesario para monitorización.
* No exponer servicios internos directamente a Internet.

## Gateway

El Gateway deberá permitir las comunicaciones necesarias para:

* Cloudflare Tunnel.
* Nginx.
* Comunicación con el Portfolio.
* Monitorización mediante Node Exporter.
* Administración mediante SSH.
* Resolución DNS.
* Actualizaciones del sistema.

## Portfolio

El Portfolio deberá permitir únicamente las comunicaciones necesarias para:

* Tráfico web proveniente del Gateway.
* Monitorización mediante Node Exporter.
* Administración mediante SSH.
* Resolución DNS.
* Actualizaciones del sistema.

# Monitorización

Cada LXC contará con Node Exporter para proporcionar métricas al sistema de
monitorización del homelab.

```text
                    Prometheus
                    /        \
                   /          \
                  ▼            ▼
          Gateway LXC      Portfolio LXC
          Node Exporter    Node Exporter
```

Las métricas permitirán supervisar:

* Utilización de CPU.
* Utilización de memoria.
* Utilización de almacenamiento.
* Tráfico de red.
* Disponibilidad de los servicios.

# Limitaciones actuales

La infraestructura se encuentra actualmente sobre una red doméstica plana.

No se dispone de:

* Acceso administrativo al router.
* Subred independiente para el homelab.
* VLANs.
* Switch gestionable.
* Firewall dedicado.
* Red de administración independiente.

Estas limitaciones son consideradas parte del estado actual de la infraestructura
y no representan necesariamente el diseño objetivo a largo plazo.

# Propuesta de mejora a mediano/largo plazo

* Uso de nodos MESH en lugar de repetidores convencionales, utilizando 5 GHz
  para la comunicación entre nodos.
* Incorporación de un switch Gigabit en caso de incrementar la cantidad de
  nodos disponibles.
* Incorporación de un router/firewall con mayor capacidad de administración.
* Implementación de VLANs para segmentar la red.
* Separación de la red doméstica y la infraestructura del homelab.
* Creación de una red independiente para administración.
* Implementación de reglas de firewall entre segmentos.




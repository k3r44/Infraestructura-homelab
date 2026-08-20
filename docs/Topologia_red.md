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
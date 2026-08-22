# Proxmox VE

## Bitácora inicial

- Estado: nodo Proxmox instalado y operativo.
- Fecha de base: 2026-08-21.
- Infraestructura: laboratorio en red doméstica con gestión local y acceso remoto seguro.

## Instalación

- Proxmox VE instalado sobre el hardware principal del laboratorio.
- Configuración base del sistema realizada desde la instalación inicial.
- Se definió la identidad del nodo y la base de administración.

## Configuración inicial del nodo

- Hostname: `homelab.k3r4.home.arpa`
- IP: `192.168.0.120/24`
- Gateway: `192.168.0.1`
- DNS: `1.1.1.1`
- Red: `192.168.0.0/24`

## Red y `vmbr0`

- La interfaz física del host se conecta a `vmbr0`.
- `vmbr0` es el bridge principal para VM y LXC.
- El entorno actual está dentro de la red doméstica, sin VLANs ni aislamiento de red.

## Almacenamiento

- Se usa almacenamiento local del nodo como base del laboratorio.
- Los datos y las VM/CT se mantienen sobre recursos del propio Proxmox.
- El esquema actual es simple y suficiente para la fase inicial.

## Configuración general

- Hostname definido correctamente.
- Sistema base operativo.
- Gestión desde Proxmox UI y acceso seguro por SSH.
- Se mantiene registro de cambios y tareas operativas.

## Estado actual

- Proxmox instalado.
- Nodo con red funcional.
- Base del entorno virtual listo para continuar con despliegues.



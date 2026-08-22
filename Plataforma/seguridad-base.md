# Seguridad base del host Proxmox

## Bitácora inicial

- Estado: host Proxmox con seguridad base aplicada y documentada.
- Fecha de base: 2026-08-21.
- Entorno: red doméstica con acceso restringido a la LAN local.

## Firewall del host

- Se considera el firewall del host como defensa base.
- Se permite acceso a SSH y gestión del nodo solo desde la red local de confianza.
- Se bloquean accesos no necesarios.
- Política general: permitir lo mínimo indispensable.

## Políticas básicas

- Actualizar el sistema con regularidad.
- Mantener deshabilitados servicios no necesarios.
- No permitir acceso directo a `root` por SSH.
- Mantener documentación de cambios y permisos.

## Actualizaciones de seguridad

- Se revisan actualizaciones del sistema con continuidad.
- Se priorizan parches críticos y seguridad.
- El host no queda desatendido en materia de mantenimiento.

## Servicios innecesarios

- Se revisan servicios activos del host.
- Se eliminan o deshabilitan procesos sin uso real.
- Se reduce la superficie de ataque del nodo.

## SSH

- Se recomienda `PermitRootLogin no`.
- Se recomienda `PasswordAuthentication no`.
- Se recomienda `PubkeyAuthentication yes`.
- Se limita el acceso a usuarios administrativos.

## Estado actual

- Host Proxmox con política de seguridad mínima establecida.
- Acceso remotos restringido y orientado a administración controlada.
- Base de seguridad definida para la fase inicial del laboratorio.



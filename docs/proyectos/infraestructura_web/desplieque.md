# Despliegue de producción

## 1. Creación de infraestructura

### Gateway

Se creó un contenedor LXC destinado a funcionar como gateway de la
infraestructura de producción.

| Parámetro | Valor |
|---|---|
| Hostname | gateway |
| Sistema operativo | Debian 13 |
| Arquitectura | AMD64 |
| CPU | 1 vCPU |
| RAM | 512 MB |
| Almacenamiento | 8 GB |
| Interfaz | eth0 |
| Bridge | vmbr0 |
| IP | 192.168.0.121/24 |
| Gateway | 192.168.0.1 |

### Validación de conectividad

Se verificó:

- Conectividad con el gateway de la red.
- Conectividad con Internet mediante IPv4.
- Resolución DNS.
- Conectividad mediante IPv6.

Resultados:

| Prueba | Resultado |
|---|---|
| `ping 192.168.0.1` | OK |
| `ping 1.1.1.1` | OK |
| `ping google.com` | OK |


### Implementación del firewall

Se validó la configuración mediante:

`nft -c -f /etc/nftables.conf`

La configuración fue aplicada posteriormente al Gateway.

La política inicial establece:

- INPUT: DROP
- FORWARD: DROP
- OUTPUT: ACCEPT

Se mantienen excepciones para loopback, conexiones establecidas o
relacionadas e ICMP/ICMPv6.

La conectividad hacia el gateway de la LAN e Internet fue utilizada
como prueba de validación posterior a la implementación.


### Portafolio

Se creó un contenedor LXC destinado a funcionar como el alojamiento de las 
paginas web que se desplegaran
| Parámetro | Valor |
|---|---|
| Hostname | portafolio |
| Sistema operativo | Debian 13 |
| Arquitectura | AMD64 |
| CPU | 1 vCPU |
| RAM | 512 MB |
| Almacenamiento | 8 GB |
| Interfaz | eth0 |
| Bridge | vmbr0 |
| IP | 192.168.0.121/24 |
| Gateway | 192.168.0.1 |

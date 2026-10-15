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

La revisión de las reglas efectivas y las pruebas de los accesos permitidos
siguen pendientes.

### Nginx y HTTPS en el Gateway

Se instaló Nginx desde los repositorios de Debian 13 (Trixie). El servicio
`nginx.service` está activo y habilitado para iniciar con el sistema. La
configuración se validó con `nginx -t`.

Se deshabilitó el enlace al sitio predeterminado de Debian, conservando su
archivo original. Se creó `/etc/nginx/sites-available/gateway` y se habilitó
mediante un enlace en `sites-enabled`.

La configuración actual de acceso incluye:

- Puerto 80: redirección HTTP a HTTPS con código `301`.
- Puerto 443: HTTPS con TLS 1.2 y TLS 1.3.
- Certificado presentado: `/etc/pki/tls/certs/gateway-fullchain.crt`.
- Clave privada: `/etc/pki/tls/private/gateway.key`, propiedad de `root` y
	con permisos `600`.
- Respuesta temporal para comprobar la llegada de solicitudes a Nginx.

Se comprobó que la clave pública del certificado corresponde a la derivada
de la clave privada. Las pruebas locales obtuvieron `200 OK` por HTTPS al
proporcionar explícitamente el certificado intermedio a `curl`, y `301` por
HTTP. Esto confirma el funcionamiento local de Nginx, TLS y la redirección;
no demuestra que los clientes confíen automáticamente en la CA ni que el
acceso desde otro equipo de la LAN funcione.

El Gateway todavía no está desplegado como reverse proxy operativo: el
backend del portafolio no está disponible y aún no se han configurado
Cloudflare Tunnel ni Node Exporter. También quedan pendientes la revisión
efectiva de `nftables`, la distribución de la CA raíz a los clientes que
deban confiar en estos servicios y las pruebas de DNS, conectividad,
certificado y puertos desde otro equipo de la LAN.


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

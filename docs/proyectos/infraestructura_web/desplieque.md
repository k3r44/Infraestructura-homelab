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
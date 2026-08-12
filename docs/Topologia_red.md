# TOPOLOGIA DE RED
# Diseño físico actual (05-08-2026)

                 Diseño físico actual

               ┌──────────────────┐
               │  Router principal│
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
          ┌───────────────────────┐
          │ Lenovo ThinkCentre    │
          │        M720q          │
          └───────────────────────┘


    
## Nodo Principal 
 Hostname: homelab.k3r4.home.arpa
 IP: 192.168.0.120
 Gateway 192.168.0.1
 DNS: 1.1.1.1


# Resultados de conectividad

## Antes (WiFi directo desde repetidor)
Descarga: ~33 Mbps
Subida: ~20 Mbps
Latencia bajo carga: ~268 ms
Después (Ethernet desde repetidor)
Descarga: 50.35 Mbps
Subida: 19.35 Mbps
Latencia bajo carga: 15 ms
Conclusión

El uso de Ethernet eliminó el bufferbloat y estabilizó la conexión para servicios virtualizados y hosting ligero.

## Propuesta de mejora a mediano/largo plazo:
o Uso de nodos MESH en lugar de repetidores convencionales a 5GHz la transmision de los nodos
o Switch gigabit en caso de incrementar la cantidad de nodos disponibles 
    
# Planificación de recursos de producción

## Limitación de hardware

El entorno de producción opera bajo un presupuesto de hardware limitado para el laboratorio doméstico (*homelab*).

El host proporciona actualmente unos 16 GB de memoria real dentro del sistema, los cuales deben compartirse entre Proxmox VE (aproximadamente 1–1.5 GB para su funcionamiento base), las cargas de trabajo de producción, la monitorización, la experimentación y los proyectos futuros.

Por consiguiente, la infraestructura de producción prioriza un bajo consumo de recursos y reserva capacidad para futuras cargas de trabajo.

## Asignación inicial

| Carga de trabajo | Tipo |    RAM |    CPU | Almacenamiento | Interfaz de red  | Propósito                                         |
| ---------------- | ---- | -----: | -----: | -------------: | ---------------- | ------------------------------------------------- |
| Gateway          | LXC  | 512 MB | 1 vCPU |           8 GB | `eth0` → `vmbr0` | Cloudflare Tunnel + Proxy inverso + Node Exporter |
| Portfolio        | LXC  |   1 GB | 1 vCPU |           6 GB | `eth0` → `vmbr0` | Alojamiento web + Node Exporter                   |

La interfaz de red de cada LXC será inicialmente una interfaz virtual (`eth0`) conectada al bridge de Proxmox (`vmbr0`). El direccionamiento IP, las reglas de comunicación y una posible segmentación de red se definirán posteriormente durante el diseño de red.

### Asignación inicial total

**RAM:** 1,5 GB

**CPU:** 2 vCPU

**Almacenamiento:** 14 GB

## Justificación de la asignación de recursos

### Gateway

El *gateway* realiza tareas de red y proxy relativamente ligeras. La asignación inicial es deliberadamente conservadora y se ajustará en función del consumo real de recursos. También se tomó en consideración el tamaño de las aplicaciones que se utilizarán, además del espacio reservado para el almacenamiento de logs y el sistema base.

### Portfolio

Se prevé que el *portfolio* sea una aplicación web ligera (estática o de bajo consumo). 1 GB de RAM proporciona capacidad inicial suficiente y deja margen para servicios futuros.

## Monitorización de recursos

Las asignaciones de recursos no se considerarán permanentes.

La utilización real se monitorizará mediante la pila de observabilidad del laboratorio doméstico, mediante la implementación de Node Exporter en cada nodo y su posterior integración durante la Fase 3 del desarrollo.

Se evaluarán las siguientes métricas:

* Utilización de CPU.
* Utilización de memoria.
* Utilización de disco.
* Tráfico de red.
* Disponibilidad del servicio.

Las asignaciones de recursos podrán aumentarse o reducirse según los siguientes requisitos observados de la carga de trabajo:

* Presión de memoria sostenida en la memoria RAM.
* Utilización elevada y sostenida de las CPU.
* Uso del almacenamiento por encima del umbral definido.
* Existencia de un cuello de botella en la red.

## Estrategia de capacidad

El entorno de producción mantendrá capacidad libre en el host en lugar de asignar todos los recursos disponibles.

Esto permite:

* Cargas de trabajo temporales.
* Aplicaciones futuras.
* Operaciones de mantenimiento.
* Pruebas y experimentación.
* Operaciones de recuperación.

El objetivo es evitar asignar recursos basándose únicamente en suposiciones y, en su lugar, ajustar la capacidad según el uso observado.

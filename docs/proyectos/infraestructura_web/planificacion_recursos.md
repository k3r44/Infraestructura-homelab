# Planificación de recursos de producción

## Limitación de hardware

El entorno de producción opera bajo un presupuesto de hardware limitado para el laboratorio doméstico (*homelab*).

El host proporciona actualmente unos 16 GB de memoria del sistema, los cuales deben compartirse entre Proxmox VE, las cargas de trabajo de producción, la monitorización, la experimentación y los proyectos futuros.

Por consiguiente, la infraestructura de producción prioriza un bajo consumo de recursos y reserva capacidad suficiente para futuras cargas de trabajo.

## Asignación inicial

| Carga de trabajo | Tipo | RAM | CPU | Propósito |
| ---------------- | ---- | -----: | -----: | --------------------------------- |
| Gateway          | LXC  | 512 MB | 1 vCPU | Cloudflare Tunnel + Proxy inverso |
| Portfolio        | LXC  | 1 GB   | 1 vCPU | Alojamiento web                   |

### Asignación inicial total

**RAM:** 1,5 GB

**CPU:** 2 vCPU

## Justificación de la asignación de recursos

### Gateway — 512 MB

El *gateway* realiza tareas de red y proxy relativamente ligeras. La asignación inicial es deliberadamente conservadora y se ajustará en función del consumo real de recursos.

### Portfolio — 1 GB

Se prevé que el *portfolio* sea una aplicación web ligera (estática o de bajo consumo). 1 GB proporciona capacidad inicial suficiente y deja margen para servicios futuros.

## Monitorización de recursos

Las asignaciones de recursos no se considerarán permanentes.

La utilización real se monitorizará mediante la pila de observabilidad del laboratorio doméstico.

Se evaluarán las siguientes métricas:

* Utilización de CPU.
* Utilización de memoria.
* Utilización de disco.
* Tráfico de red.
* Disponibilidad del servicio.

Las asignaciones de recursos podrán aumentarse o reducirse según los requisitos observados de la carga de trabajo.

## Estrategia de capacidad

El entorno de producción mantendrá capacidad libre en el host en lugar de asignar todos los recursos disponibles.

Esto permite:

* Cargas de trabajo temporales.
* Aplicaciones futuras.
* Operaciones de mantenimiento.
* Pruebas y experimentación.
* Operaciones de recuperación.

El objetivo es evitar asignar recursos basándose únicamente en suposiciones y, en su lugar, ajustar la capacidad según el uso observado.


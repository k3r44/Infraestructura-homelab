# Documentación

Esta sección contiene la documentación técnica del homelab.

La documentación está organizada en diferentes niveles para separar la información general de la infraestructura de la documentación específica de cada proyecto o servicio.

La intención es que cada documento tenga un propósito concreto y que la documentación pueda evolucionar junto con la infraestructura.

## Documentación general de la infraestructura

Estos documentos contienen información compartida por todo el homelab y sirven como referencia para comprender el entorno físico y de red sobre el que se ejecutan los diferentes proyectos.

### Inventario_hardware.md



Contiene las especificaciones del hardware utilizado en el homelab, incluyendo el nodo principal, CPU, memoria RAM, almacenamiento, interfaces de red y otros componentes relevantes.

También puede utilizarse para registrar futuras ampliaciones y modificaciones del hardware disponible.

### Topología_red


Describe la topología física y lógica de la red del homelab, incluyendo la conexión entre el router, el nodo de virtualización y los diferentes dispositivos o redes que forman parte de la infraestructura.

Este documento servirá como referencia para comprender cómo se comunica la infraestructura y cómo evolucionará la red con futuras implementaciones.

---

## Documentación de proyectos

Los proyectos individuales se encuentran dentro de: proyectos

Cada proyecto cuenta con su propia documentación para describir su diseño, implementación y operación de forma independiente, manteniendo al mismo tiempo su relación con la infraestructura general del homelab.

### Producción Web
 ### infraestructura_web

Primer proyecto destinado a implementar una infraestructura de producción para el alojamiento y exposición de sitios web estáticos y aplicaciones web ligeras.

La documentación del proyecto se divide en las siguientes áreas:

 * **[`README.md`] proyectos/infraestructura_web/README.md
  Presenta el proyecto, sus objetivos, alcance, tecnologías utilizadas, estado actual y posibles expansiones futuras.



---

## Organización de la documentación

La documentación sigue una estructura que busca acompañar el ciclo de vida de la infraestructura:

**Planificación → Diseño → Despliegue → Operación → Evolución**

La información general del homelab se mantiene separada de la documentación específica de cada proyecto para evitar mezclar decisiones globales de infraestructura con decisiones particulares de un determinado servicio.

A medida que nuevos proyectos sean incorporados al homelab, cada uno podrá contar con su propio espacio dentro de `proyectos/` y seguir una estructura de documentación similar.

## Evolución

Esta documentación no se considera estática.

Los archivos serán actualizados conforme cambien la infraestructura, los servicios, los recursos disponibles y las decisiones de diseño. Los cambios importantes deberán quedar documentados para mantener un registro de la evolución técnica del proyecto.

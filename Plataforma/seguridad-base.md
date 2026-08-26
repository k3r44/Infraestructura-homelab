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

## PKI y certificados TLS

La transmisión de información entre los servicios del laboratorio se protegerá
mediante certificados TLS emitidos por una PKI interna. Esta sección define la
política general de confianza; cada proyecto documentará su propia aplicación,
identidades, nombres y flujo de comunicación.

La PKI se creó exclusivamente para proteger las comunicaciones entre los
servicios del homelab y no interviene sobre la red doméstica. La Root CA se
generó en una VM Debian independiente, destinada a permanecer apagada cuando no
sea necesaria.

### Implementación de la Root CA

- **Nombre:** `K3R4 Homelab Root CA`.
- **Algoritmo de clave:** ECDSA.
- **Curva elíptica:** P-384 (`secp384r1`).
- **Algoritmo de firma:** ECDSA con SHA-384.
- **Validez:** 10 años.
- **Usos:** firma de certificados y listas de revocación (CRL).
- **Restricciones de CA:** `CA:TRUE` y `pathlen:1`.

La clave privada se almacenó en `/root/pki/private/root-ca.key` con permisos
`600`, restringiendo su acceso al usuario `root`. El certificado público se
generó en `/root/pki/certs/root-ca.crt`.

El certificado fue inspeccionado mediante OpenSSL, verificando la identidad de
la autoridad, ECDSA P-384, la firma ECDSA con SHA-384, las restricciones de CA,
los permisos de firma de certificados y CRL y el periodo de validez.

### Jerarquía de confianza

```text
                 ┌─────────────────────┐
                 │      ROOT CA        │
                 │                     │
                 │      OFFLINE        │
                 └──────────┬──────────┘
                            │
                     firma Intermediate
                            │
                            ▼
                 ┌─────────────────────┐
                 │  INTERMEDIATE CA    │
                 │                     │
                 │  Futura emisora    │
                 └──────────┬──────────┘
                            │
                                   ┌─────────────────┼─────────────────┐
                                   │                 │                 │
                                   ▼                 ▼                 ▼
                      Servicio          Servicio          Servicio
                            web A             web B             futuro
                      TLS cert          TLS cert          TLS cert
```

- `Root CA`: autoridad raíz de confianza, mantenida offline y utilizada solo
       para firmar la futura `Intermediate CA`.
- `Intermediate CA`: autoridad emisora prevista para los certificados de los
       servicios del laboratorio.
- Servicios: deberán recibir certificados emitidos por la `Intermediate CA` y
       nunca directamente por la `Root CA`.
- Cada proyecto deberá documentar sus nombres DNS, direcciones IP, servicios y
       requisitos TLS en su propio apartado de seguridad.

### KPIs manuales de control

| KPI | Objetivo | Frecuencia | Evidencia |
|---|---|---|---|
| Cobertura TLS | 100 % de los servicios publicados usan TLS | Por alta o cambio de servicio | Inventario de certificados |
| Emisión segura | 0 certificados de servicio firmados directamente por la `Root CA` | Por emisión | Registro de la CA intermedia |
| Vigencia | 0 certificados vencidos en producción | Revisión mensual | Fecha de expiración |
| Renovación preventiva | Renovar certificados antes de los 30 días previos a su vencimiento | Revisión semanal | Bitácora de renovación |
| Protección de claves | 100 % de las claves privadas con permisos restringidos | Por emisión y revisión trimestral | Revisión de permisos |
| Revocación | Revocar certificados comprometidos o retirados | Cuando ocurra un incidente o baja | Registro de revocación |

### Reglas operativas

- La clave privada de la `Root CA` debe permanecer offline y no instalarse en los servicios.
- Los servicios deben confiar en la cadena `Root CA -> Intermediate CA -> certificado del servicio`.
- Cada certificado debe incluir el nombre DNS o la dirección IP que utilizarán los clientes.
- No se deben reutilizar claves privadas entre servicios.
- Las claves privadas deben protegerse con permisos restrictivos y no deben
       almacenarse en el repositorio.
- La emisión, renovación y revocación de certificados se registrará en la bitácora del laboratorio.

### Estado de la PKI

- Root CA creada, verificada y almacenada de forma segura.
- Root CA mantenida fuera de línea cuando no se utiliza.
- Intermediate CA y certificados de servicio todavía pendientes.

## Estado actual

- Host Proxmox con política de seguridad mínima establecida.
- Acceso remoto restringido y orientado a administración controlada.
- PKI interna definida como arquitectura y política general del laboratorio.
- Root CA creada y verificada en una VM Debian independiente.
- Intermediate CA y certificados de servicio todavía no creados.
- Base de seguridad definida para la fase inicial del laboratorio.



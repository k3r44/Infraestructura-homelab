# Seguridad de la infraestructura web

## 1. Alcance de la primera fase

La primera fase de seguridad se centra en desplegar una PKI interna manual para
cifrar, en una etapa posterior, la comunicación entre los contenedores LXC de
producción. El objetivo inicial es proteger el salto entre el **Gateway LXC**
(`192.168.0.121`) y el **Portfolio LXC** (`192.168.0.122`), evitando que las
solicitudes internas viajen como HTTP en texto claro.

El acceso público seguirá terminando en Cloudflare y el Gateway continuará
siendo el único punto de entrada de la infraestructura. La PKI interna no
sustituye el cifrado del tramo usuario → Cloudflare ni el del Cloudflare Tunnel.

La política general y la implementación de la PKI se documentan en
[Seguridad base del host Proxmox](../../../Plataforma/seguridad-base.md). Este
documento se limita a la aplicación de esa PKI en la infraestructura web.

### Objetivos

- Preparar una autoridad certificadora propia para emitir identidades TLS.
- Mantener la clave privada de la autoridad raíz fuera de los LXC.
- Definir una base de confianza para que Nginx pueda validar servicios internos.
- Trabajar con un procedimiento manual, documentado y auditable.

### Fuera del alcance inicial

- Emisión o renovación automática.
- Alta disponibilidad de la autoridad certificadora.
- Revocación online mediante OCSP.
- mTLS entre los LXC. La autenticación mutua se evaluará después de validar el
  primer certificado de servicio.

## 2. Modelo de confianza previsto

La arquitectura final utilizará una jerarquía de dos niveles:

```text
                         Root CA
                  clave offline, solo firma
                              |
                              v
                    Intermediate CA
                  firma certificados TLS
                         /          \
                        /            \
                       v              v
              Gateway LXC       Portfolio LXC
              certificado       certificado
```

En el estado actual la **Root CA** ya fue creada y verificada. La
**Intermediate CA** ya está montada en el LXC intermedio y queda preparada para
completar la fase de firma. El firewall del entorno está siendo ajustado para
permitir únicamente el tráfico necesario y dejar que la CA intermedia pueda
emitir certificados TLS en una fase posterior, sin ampliar la superficie de
exposición de la red.

### Reglas de seguridad

- La Root CA se prepara en una máquina temporal o en un equipo administrativo
  protegido, sin exponer su clave privada a la red.
- La clave privada de la Root CA no se instala en ningún LXC.
- La Root CA no firmará certificados de servicio directamente; su función será
  firmar la futura Intermediate CA.
- Cada servicio deberá utilizar una clave privada independiente.
- Los futuros certificados deberán incluir SAN (*Subject Alternative Name*) con
  el nombre DNS o la dirección IP que utilice el cliente.
- Las claves privadas deberán conservar permisos restrictivos, equivalentes a
  `0600`, y nunca se incluirán en el repositorio.
- La creación de claves, certificados y huellas digitales se registrará sin
  guardar contraseñas ni material privado.

## 3. Parámetros TLS de los servicios web

| Elemento | Parámetro |
|---|---|
| Algoritmo de certificados de servicio | RSA de 2048 bits |
| Protocolos TLS | TLS 1.2 y TLS 1.3 |
| Vigencia prevista de la Intermediate CA | 5 años |
| Vigencia prevista del certificado de servicio | 90 días |

Los parámetros de los certificados de servicio se confirmarán antes de su
primera emisión. La vigencia corta de estos certificados limitará el impacto de
una clave comprometida.

## 4. Estructura de trabajo prevista

La estructura de la PKI se preparará únicamente en la máquina administrativa
temporal o en un equipo dedicado y protegido:

```text
pki/
├── root/
│   ├── certs/root-ca.crt
│   ├── private/root-ca.key
│   └── index.txt
├── intermediate/
│   ├── certs/intermediate-ca.crt
│   ├── csr/intermediate-ca.csr
│   ├── private/intermediate-ca.key
│   └── index.txt
└── services/
    ├── gateway/
    └── portfolio/
```

Durante esta primera etapa se trabajará únicamente con el directorio `root/`.
Los directorios `private/` tendrán permisos restrictivos y la carpeta completa
de trabajo no se subirá al repositorio. Solo podrán conservarse en el proyecto
los certificados públicos y la documentación, cuando sea necesario.

## 5. Próximas etapas de TLS web

1. Preparar y reforzar el firewall del entorno para dejar únicamente el tráfico
   necesario a la Intermediate CA dentro de la red interna.
2. Completar la configuración de la Intermediate CA y firmarla con la Root CA.
3. Generar la llave y la solicitud del certificado del Portfolio.
4. Emitir el certificado del Portfolio con `serverAuth` y SAN para
   `portfolio.home.arpa` y `192.168.0.122`.
5. Configurar Nginx para usar TLS y validar la cadena de confianza.
6. Probar el flujo Gateway → Portfolio y registrar el resultado.

La emisión de certificados de servicio no se considera iniciada hasta que la
Intermediate CA esté creada, verificada, protegida por el firewall y almacenada
de forma segura.

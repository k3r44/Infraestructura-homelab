# Seguridad de la infraestructura web

## 1. Alcance de la primera fase

La primera fase de seguridad se centra en preparar una PKI interna manual para
cifrar, en una etapa posterior, la comunicación entre los contenedores LXC de
producción. El objetivo inicial es proteger el salto entre el **Gateway LXC**
(`192.168.0.121`) y el **Portfolio LXC** (`192.168.0.122`), evitando que las
solicitudes internas viajen como HTTP en texto claro.

El acceso público seguirá terminando en Cloudflare y el Gateway continuará
siendo el único punto de entrada de la infraestructura. La PKI interna no
sustituye el cifrado del tramo usuario → Cloudflare ni el del Cloudflare Tunnel.

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

En el estado actual únicamente se está preparando la **Root CA**. La
**Intermediate CA** y los certificados de los servicios todavía no han sido
creados.

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

## 3. Parámetros criptográficos iniciales

| Elemento | Parámetro previsto |
|---|---|
| Algoritmo de la Root CA | RSA de 4096 bits |
| Algoritmo de certificados de servicio | RSA de 2048 bits |
| Algoritmo de firma | SHA-256 |
| Protocolos TLS | TLS 1.2 y TLS 1.3 |
| Vigencia prevista de la Root CA | 10 años |
| Vigencia prevista de la Intermediate CA | 5 años |
| Vigencia prevista del certificado de servicio | 90 días |

Estos valores son parámetros iniciales de diseño y se confirmarán antes de la
emisión de los primeros certificados de prueba. La vigencia corta de los
certificados de servicio limitará el impacto de una clave comprometida.

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

## 5. Preparación de la Root CA

La Root CA es el punto máximo de confianza de la PKI. Antes de generar sus
llaves de prueba se debe confirmar:

- Nombre distintivo de la autoridad: `Homelab K3R4 Root CA`.
- Algoritmo y longitud de la llave: RSA de 4096 bits.
- Algoritmo de firma: SHA-256.
- Uso de la llave: firma de autoridades certificadoras subordinadas.
- Protección de la llave privada mediante cifrado y una contraseña que no se
  almacene en archivos ni en la bitácora.
- Ubicación de trabajo aislada de los LXC y del tráfico de producción.
- Generación y conservación de la huella digital del certificado público.

La configuración de la autoridad debe utilizar las extensiones reservadas para
una CA, especialmente `basicConstraints` con `CA:TRUE` y `pathLenConstraint`,
además de `keyUsage` con `keyCertSign` y `cRLSign`. Estas extensiones evitan
confundir la Root CA con un certificado de servidor.

La primera prueba consistirá en generar la llave privada y el certificado
autofirmado de la Root CA, revisar sus fechas, su sujeto, sus extensiones y su
huella digital. Hasta completar esa revisión, no se copiará ningún archivo a
los LXC.

## 6. Próximas etapas de la PKI

1. Generar y revisar las llaves de prueba de la Root CA.
2. Guardar la llave privada cifrada y retirar la Root CA del entorno de red.
3. Crear una solicitud para la Intermediate CA y firmarla con la Root CA.
4. Generar la llave y la solicitud del certificado del Portfolio.
5. Emitir el certificado del Portfolio con `serverAuth` y SAN para
   `portfolio.home.arpa` y `192.168.0.122`.
6. Configurar Nginx para usar TLS y validar la cadena de confianza.
7. Probar el flujo Gateway → Portfolio y registrar el resultado.

La emisión de certificados de servicio no se considera iniciada hasta que la
Root CA esté creada, verificada y almacenada de forma segura.

## 7. Criterios de aceptación de la etapa actual

- La Root CA tiene una llave RSA de 4096 bits protegida con cifrado.
- El certificado de la Root CA es autofirmado y utiliza SHA-256.
- Las extensiones indican que se trata de una autoridad certificadora.
- La huella digital del certificado público está registrada.
- La llave privada no está dentro de ningún LXC ni del repositorio.
- La fecha de creación, la vigencia y el responsable de la prueba están
  registrados.

## Estado

**Fase actual:** preparación de la estructura PKI y generación de las primeras
llaves de prueba de la Root CA.

**Siguiente paso:** validar el certificado autofirmado de la Root CA y definir
el procedimiento para crear la Intermediate CA.

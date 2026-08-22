# Usuarios y permisos

## Bitácora inicial

- Estado: modelo de usuarios y permisos definido para la administración del nodo.
- Fecha de base: 2026-08-21.
- Principio: mínimo privilegio y acceso administrativo controlado.

## Usuario administrativo del nodo

- Se define un usuario administrativo dedicado para la gestión del host.
- Su función incluye mantenimiento, VM/CT y revisión del sistema.
- El acceso diario no se hace como `root`.

## `sudo`

- Se usa `sudo` para elevar permisos cuando es necesario.
- Los usuarios normales no tienen permisos administrativos.
- La gestión del nodo se realiza con un perfil diferenciado y restringido.

## Grupos y privilegios

- Se usan grupos del sistema para separar responsabilidades.
- `sudo` es el grupo base para administración.
- En Proxmox se mantiene claridad entre permisos del sistema y permisos de la plataforma.

## Principio de mínimo privilegio

- Cada usuario tiene solo los permisos necesarios.
- No se extienden permisos más allá del rol real del usuario.
- La administración se estructura por responsabilidad y no por acceso libre.

## Acceso de `root`

- `root` se reserva para recuperación y tareas excepcionales.
- No se usa como acceso habitual.
- Se evita el uso directo de la cuenta raíz en operaciones de administración diaria.

## Autenticación

- Se prioriza autenticación segura.
- Se recomienda contraseñas robustas y acceso con claves SSH.
- Se evita compartir credenciales entre personas.

## Estado actual

- Usuario administrativo definido.
- `sudo` implementado como base del acceso administrativo.
- Principio de mínimo privilegio aplicado a la administración del laboratorio.



# Modelos de Datos y Esquema

## Descripción General

Ministry Mapper usa SQLite a través de PocketBase con un modelo de datos jerárquico diseñado para la gestión multi-inquilino de congregaciones. Este documento proporciona documentación completa de todos los modelos de datos, relaciones y reglas de negocio.

## Diagrama de Relación de Entidades

```
Congregación (Entidad raíz - aislamiento multi-inquilino)
    │
    ├─── Territorio (1:M)
    │      │
    │      └─── Mapa (1:M)
    │             │
    │             ├─── Dirección (1:M)
    │             │      ├─── addresses_log (1:M)
    │             │      └─── address_options (1:M)
    │             ├─── Asignación (1:M)
    │             └─── Mensaje (1:M)
    │
    ├─── Opción (1:M)
    │      └─── address_options (M:M puente con Dirección)
    │
    ├─── Usuario (1:M a través de Rol)
    │      │
    │      ├─── Rol (1:M)
    │      └─── Asignación (1:M)
    │
    ├─── Mensaje (1:M)
    │
    └─── aggregates (1:M desde Territorio y Mapa)
```

## Colecciones Principales

### 1. Congregaciones

**Propósito:** Entidad central para el aislamiento multi-inquilino y la configuración de la organización

**Tipo:** Colección base

**Campos:**

| Campo | Tipo | Requerido | Descripción |
|-------|------|-----------|-------------|
| `id` | text(15) | Sí | ID único auto-generado |
| `code` | text | Sí | Código de la organización |
| `name` | text | Sí | Nombre de la organización |
| `expiry_hours` | number | Sí | Duración del enlace de asignación (horas) |
| `max_tries` | number | Sí | Máximo de intentos "nadie en casa" antes de marcar como completado |
| `origin` | select | Sí | País/región (sg, my) |
| `timezone` | select | Sí | Zona horaria para cálculos de fecha |
| `created` | date | Auto | Marca de tiempo de creación |
| `updated` | date | Auto | Marca de tiempo de última actualización |

**Reglas de Acceso:**
- **Lista:** El usuario debe tener rol en la congregación
- **Ver:** Usuario con rol O acceso basado en enlace
- **Actualizar:** Solo administrador
- **Eliminar:** Solo administrador

**Reglas de Negocio:**
- Controla la duración predeterminada de asignación para todos los mapas
- `max_tries` determina cuándo las direcciones "nadie en casa" cuentan como completadas
- La zona horaria afecta los cálculos basados en fechas y los informes

**Ejemplo:**
```json
{
  "id": "abc123",
  "code": "CONG001",
  "name": "Congregación Norte",
  "expiry_hours": 24,
  "max_tries": 3,
  "origin": "sg",
  "timezone": "Asia/Singapore"
}
```

---

### 2. Territorios

**Propósito:** Divisiones geográficas dentro de la congregación para organizar el servicio de campo

**Tipo:** Colección base

**Campos:**

| Campo | Tipo | Requerido | Descripción |
|-------|------|-----------|-------------|
| `id` | text(15) | Sí | ID único auto-generado |
| `congregation` | relation | Sí | FK a Congregaciones (eliminación en cascada) |
| `code` | text | Sí | Identificador del territorio (auto: "0") |
| `description` | text | No | Descripción legible por humanos |
| `progress` | number | Auto | Porcentaje de finalización (0-100) |
| `created` | date | Auto | Marca de tiempo de creación |
| `updated` | date | Auto | Marca de tiempo de última actualización |

**Índices:**
- `(congregation, code)` - Búsqueda rápida por congregación
- `(congregation)` - Filtro de congregación

**Relaciones:**
- 1:M con Mapas
- 1:M con Direcciones (desnormalizado para rendimiento)

**Reglas de Acceso:**
- **Lista:** Usuario con rol en la congregación
- **Ver:** Usuario con rol O acceso por enlace
- **Crear/Actualizar/Eliminar:** Director o Administrador

**Cálculo de Progreso:**
Calculado automáticamente a partir del agregado de todos los mapas dentro del territorio

**Ejemplo:**
```json
{
  "id": "terr_abc",
  "congregation": "cong_123",
  "code": "1A",
  "description": "Distrito Centro",
  "progress": 65.5
}
```

---

### 3. Mapas

**Propósito:** Representa ubicaciones o edificios específicos que contienen direcciones

**Tipo:** Colección base

**Campos:**

| Campo | Tipo | Requerido | Descripción |
|-------|------|-----------|-------------|
| `id` | text(15) | Sí | ID único auto-generado |
| `territory` | relation | Sí | FK a Territorios (eliminación en cascada) |
| `congregation` | relation | Sí | FK a Congregaciones (eliminación en cascada) |
| `sequence` | number | Auto | Orden dentro del territorio (auto-incremento) |
| `code` | text | Sí | Identificador/número del mapa (auto: "0") |
| `description` | text | No | Descripción de la ubicación |
| `type` | select | Sí | `single` o `multi` (tipo de piso) |
| `coordinates` | JSON | No | Ubicación geográfica `{lat, lng}` |
| `aggregates` | JSON | Auto | Estadísticas en caché (máx 2MB) |
| `progress` | number | Auto | Porcentaje de finalización |
| `created` | date | Auto | Marca de tiempo de creación |
| `updated` | date | Auto | Marca de tiempo de última actualización |

**Índices:**
- `(territory, code)` - Búsqueda principal
- `(territory, sequence)` - Orden
- `(territory)` - Filtro de territorio

**Eliminaciones en Cascada:**
- Direcciones
- Asignaciones
- Mensajes

**Estructura de Agregados:**
```json
{
  "done": 45,
  "not_done": 30,
  "dnc": 5,
  "invalid": 2,
  "not_home_max_tries": 8
}
```

**Tipos de Mapa:**
- **single**: Direcciones privadas (casas)
- **multi**: Direcciones públicas (edificios de varios pisos)

**Reglas de Acceso:**
- **Lista:** Usuario con rol O acceso por enlace
- **Ver:** Usuario con rol O acceso por enlace
- **Crear/Actualizar:** Director o Administrador
- **Eliminar:** Solo Administrador

**Ejemplo:**
```json
{
  "id": "map_xyz",
  "territory": "terr_abc",
  "congregation": "cong_123",
  "sequence": 5,
  "code": "12",
  "description": "Apartamentos Calle Principal",
  "type": "multi",
  "coordinates": {"lat": 1.3521, "lng": 103.8198},
  "aggregates": {
    "done": 20,
    "not_done": 10,
    "dnc": 2,
    "invalid": 1,
    "not_home_max_tries": 3
  },
  "progress": 66.7
}
```

---

### 4. Direcciones

**Propósito:** Unidades u hogares individuales dentro de los mapas

**Tipo:** Colección base

**Campos:**

| Campo | Tipo | Requerido | Descripción |
|-------|------|-----------|-------------|
| `id` | text(15) | Sí | ID único auto-generado |
| `map` | relation | Sí | FK a Mapas (eliminación en cascada) |
| `congregation` | relation | Sí | FK a Congregaciones (desnormalizado) |
| `territory` | relation | Sí | FK a Territorios (desnormalizado) |
| `floor` | number | Sí | Número de piso (base 0) |
| `code` | text | Sí | Código de unidad/puerta (ej., "101", "A") |
| `sequence` | number | Sí | Orden dentro del mapa |
| `status` | select | Sí | Estado de visita de la dirección |
| `not_home_tries` | number | Auto | Contador de intentos "nadie en casa" |
| `type` | relation | No | M:M a Opciones (array JSON) |
| `notes` | text | No | Notas del trabajador de campo |
| `last_notes_updated` | date | Auto | Cuándo se cambiaron las notas por última vez (mediante hook) |
| `last_notes_updated_by` | text | Auto | Nombre de usuario del último actualizador (mediante hook) |
| `dnc_time` | date | No | Cuándo fue marcado "no llamar" |
| `coordinates` | JSON | No | GPS detallado `{lat, lng}` |
| `updated_by` | text | No | Rastrear quién hizo actualizaciones |
| `created` | date | Auto | Marca de tiempo de creación |
| `updated` | date | Auto | Marca de tiempo de última actualización |

**Valores de Estado:**
- `not_done` - No visitado aún (estado inicial)
- `done` - Completado exitosamente
- `not_home` - Nadie en casa (se puede reintentar)
- `do_not_call` - Excluido del trabajo
- `invalid` - La dirección no existe

**Índices (7 en total):**
- `(map)` - Filtro de mapa
- `(map, status)` - Consultas de distribución de estado
- `(map, code)` - Búsqueda de código
- `(map, floor)` - Organización de piso
- `(type, congregation)` - Distribución de opciones
- `(congregation, last_notes_updated_by)` - Notas recientes por usuario
- `(territory, status)` - Estado en todo el territorio

**Hooks Automáticos:**
- Las actualizaciones activan los campos `last_notes_updated` y `last_notes_updated_by`
- Activan el recálculo de agregados en el mapa
- Actualizan el progreso del territorio

**Reglas de Acceso:**
- **Lista:** Usuario con rol O acceso por enlace
- **Ver:** Usuario con rol O acceso por enlace
- **Crear:** Director o Administrador
- **Actualizar:** Usuario con rol O acceso por enlace (los publicadores pueden actualizar)
- **Eliminar:** Solo Administrador

**Ejemplo:**
```json
{
  "id": "addr_123",
  "map": "map_xyz",
  "congregation": "cong_123",
  "territory": "terr_abc",
  "floor": 2,
  "code": "201",
  "sequence": 5,
  "status": "not_home",
  "not_home_tries": 2,
  "type": ["opt_residential"],
  "notes": "Intentar después de las 6pm",
  "last_notes_updated": "2025-12-19T14:30:00Z",
  "last_notes_updated_by": "john.doe",
  "coordinates": {"lat": 1.3521, "lng": 103.8198}
}
```

---

### 5. Usuarios

**Propósito:** Cuentas de usuario del sistema con autenticación

**Tipo:** Colección de autenticación (tipo auth de PocketBase)

**Campos del Sistema:**

| Campo | Tipo | Requerido | Descripción |
|-------|------|-----------|-------------|
| `id` | text(15) | Sí | ID único auto-generado |
| `email` | email | Sí | Identidad de inicio de sesión (única) |
| `emailVisibility` | bool | Sí | Si el correo es público |
| `verified` | bool | Auto | Estado de verificación de correo |
| `password` | password | Sí | Hash Bcrypt (costo: 10, mín 6 caracteres) |
| `tokenKey` | text | Oculto | Clave de generación de token (30-60 caracteres) |

**Campos Personalizados:**

| Campo | Tipo | Requerido | Descripción |
|-------|------|-----------|-------------|
| `name` | text | Sí | Nombre para mostrar (2-50 caracteres) |
| `disabled` | bool | No | Indicador de cuenta deshabilitada |
| `last_login` | date | Auto | Marca de tiempo del último inicio de sesión (mediante hook) |

**Patrón de Validación de Nombre:**
`^[A-Za-z][\w\s\.\-']*$` (comienza con letra, permite caracteres de palabra, espacios, puntos, guiones, apóstrofos)

**Patrón de Auto-Generación:**
`user[0-9]{5}[A-Za-z]` (ej., user12345A)

**Índices:**
- `email` (único) - Búsqueda rápida de autenticación
- `tokenKey` (único) - Validación de token

**Configuración de Autenticación:**
- **Auth por Contraseña:** Habilitado (mín 6 caracteres)
- **OAuth2:** Flujo PKCE de Google
  - Mapea `name` del proveedor OAuth
  - Auto-crea usuario en el primer inicio de sesión
- **MFA:** Habilitado (duración: 1800s) - Deshabilitado para OAuth2
- **OTP:** Habilitado (duración: 180s, longitud: 4 dígitos)
- **Verificar Correo:** Duración 604800s (7 días)
- **Restablecimiento de Contraseña:** Duración 1800s (30 min)
- **Alerta de Autenticación:** Habilitado (notifica al iniciar sesión desde una nueva ubicación)

**Reglas de Acceso:**
- **Lista:** Solo administradores
- **Ver:** Administradores o yo mismo
- **Crear:** Público (OAuth o registro explícito)
- **Actualizar:** Solo yo mismo
- **Eliminar:** Solo administradores

**Token de Archivo:** Duración 120s

**Relaciones:**
- 1:M con Roles
- 1:M con Asignaciones

**Ejemplo:**
```json
{
  "id": "user_abc",
  "email": "juan.perez@example.com",
  "name": "Juan Pérez",
  "verified": true,
  "disabled": false,
  "last_login": "2025-12-19T14:30:00Z"
}
```

---

### 6. Roles

**Propósito:** Asignaciones de permisos que vinculan usuarios a congregaciones

**Tipo:** Colección base

**Campos:**

| Campo | Tipo | Requerido | Descripción |
|-------|------|-----------|-------------|
| `id` | text(15) | Sí | ID único auto-generado |
| `user` | relation | Sí | FK a Usuarios (eliminación en cascada) |
| `congregation` | relation | Sí | FK a Congregaciones (eliminación en cascada) |
| `role` | select | Sí | Nivel de permiso |
| `created` | date | Auto | Marca de tiempo de creación |
| `updated` | date | Auto | Marca de tiempo de última actualización |

**Valores de Rol:**
- `read_only` - Solo acceso de lectura
- `conductor` - Puede gestionar asignaciones y mapas
- `administrator` - Acceso completo

**Índices:**
- `(user, congregation)` - Clave compuesta (un rol por usuario por congregación)
- `(user)` - Roles del usuario
- `(congregation, role)` - Distribución de roles
- `(user, congregation, role)` - Búsqueda compuesta completa

**Reglas de Acceso:**
- **Crear/Actualizar/Eliminar:** Solo Administradores
- **Lista:** Roles propios del usuario
- **Ver:** Roles propios del usuario

**Relaciones:**
- M:N entre Usuarios y Congregaciones

**Reglas de Negocio:**
- Un rol por usuario por congregación
- El rol determina los permisos para todas las acciones

**Ejemplo:**
```json
{
  "id": "role_xyz",
  "user": "user_abc",
  "congregation": "cong_123",
  "role": "conductor"
}
```

---

### 7. Asignaciones

**Propósito:** Tokens de acceso para publicadores con acceso temporal a mapas

**Tipo:** Colección base

**Campos:**

| Campo | Tipo | Requerido | Descripción |
|-------|------|-----------|-------------|
| `id` | text(20) | Sí | **Sirve como token de acceso anónimo** |
| `congregation` | relation | Sí | FK a Congregaciones (eliminación en cascada) |
| `map` | relation | Sí | FK a Mapas (eliminación en cascada) |
| `user` | relation | No | Usuario publicador (opcional) |
| `type` | select | Sí | Clasificación de la asignación |
| `expiry_date` | date | Sí | Marca de tiempo de expiración automática |
| `publisher` | text | No | Nombre/identificador del publicador |
| `created` | date | Auto | Marca de tiempo de creación |
| `updated` | date | Auto | Marca de tiempo de última actualización |

**Tipos de Asignación:**
- `normal` - Trabajo de territorio regular
- `personal` - Territorio de ministerio personal

**Índices:**
- `(map)` - Asignaciones activas por mapa
- `(expiry_date)` - Para trabajo de limpieza en segundo plano
- `(user, created)` - Historial de asignaciones del usuario
- `(map, expiry_date)` - Verificación de expiración por mapa

**Reglas de Acceso:**
- **Crear/Eliminar:** Director o Administrador
- **Ver:** Usuario con rol O link_id que coincida con este ID + no expirado
- **Lista:** Usuario con rol

**Flujo de Trabajo:**
1. El director crea mediante el endpoint `/territory/link`
2. El publicador recibe el `link_id` de 20 caracteres (el ID de esta asignación)
3. El publicador pasa el `link_id` mediante el encabezado HTTP `@request.headers.link_id`
4. PocketBase valida: link_id coincide con assignment.id Y expiry_date > ahora
5. El trabajo en segundo plano elimina las asignaciones expiradas cada 5 minutos

**Ejemplo:**
```json
{
  "id": "asgn_1234567890abcdef",
  "congregation": "cong_123",
  "map": "map_xyz",
  "user": "user_abc",
  "type": "normal",
  "expiry_date": "2025-12-20T14:30:00Z",
  "publisher": "Juan Pérez"
}
```

---

### 8. Opciones

**Propósito:** Clasificaciones de tipo de dirección (personalizables por congregación)

**Tipo:** Colección base

**Campos:**

| Campo | Tipo | Requerido | Descripción |
|-------|------|-----------|-------------|
| `id` | text(15) | Sí | ID único auto-generado |
| `congregation` | relation | Sí | FK a Congregaciones (eliminación en cascada) |
| `code` | text | Sí | Identificador de opción (ej., "RES", "NEG") |
| `description` | text | Sí | Etiqueta legible por humanos |
| `is_countable` | bool | Sí | Incluir en cálculos de progreso |
| `is_default` | bool | Sí | Opción predeterminada para nuevas direcciones |
| `sequence` | number | Sí | Orden de visualización/ordenamiento |
| `created` | date | Auto | Marca de tiempo de creación |
| `updated` | date | Auto | Marca de tiempo de última actualización |

**Índices:**
- `(congregation, sequence)` - Ordenamiento dentro de la congregación
- `(congregation, is_default)` - Encontrar opción predeterminada

**Restricciones:**
- Exactamente un `is_default = true` por congregación (aplicado en la capa de aplicación)
- Códigos únicos por congregación
- Secuencias únicas por congregación

**Impacto en el Progreso:**
- Las opciones contables (`is_countable=true`) se incluyen en el cálculo del % de progreso
- Las opciones no contables se rastrean por separado pero se excluyen del progreso
- Cuando se elimina una opción, las direcciones obtienen automáticamente la opción predeterminada

**Reglas de Acceso:**
- **Lista/Ver:** Usuario con rol O acceso por enlace
- **Crear/Actualizar/Eliminar:** Usar el endpoint `/options/update` (Director o Administrador)

**Relaciones:**
- M:M con Direcciones (almacenado como array JSON en el campo address.type)

**Ejemplo:**
```json
{
  "id": "opt_res",
  "congregation": "cong_123",
  "code": "RES",
  "description": "Residencial",
  "is_countable": true,
  "is_default": true,
  "sequence": 1
}
```

---

### 9. Mensajes

**Propósito:** Comunicaciones y notificaciones entre usuarios

**Tipo:** Colección base

**Campos:**

| Campo | Tipo | Requerido | Descripción |
|-------|------|-----------|-------------|
| `id` | text(15) | Sí | ID único auto-generado |
| `map` | relation | Sí | FK a Mapas (eliminación en cascada) |
| `congregation` | relation | Sí | FK a Congregaciones (eliminación en cascada) |
| `message` | text | Sí | Contenido del mensaje |
| `created_by` | text | Sí | Nombre de usuario del creador |
| `read` | bool | Sí | Si fue leído por el administrador |
| `pinned` | bool | Sí | Indicador de instrucción fijada por el admin |
| `type` | select | Sí | Tipo de destinatario previsto |
| `created` | date | Auto | Marca de tiempo de creación |
| `updated` | date | Auto | Marca de tiempo de última actualización |

**Tipos de Mensaje:**
- `publisher` - De publicadores a administradores
- `conductor` - De directores
- `administrator` - Instrucciones del administrador (pueden fijarse)

**Índices:**
- `(map, pinned, created)` - Mensajes fijados recientes
- `(map, type, pinned)` - Filtrar por tipo de destinatario y estado de fijado
- `(map, type, read)` - Conteo de no leídos por tipo

**Reglas de Acceso:**
- **Crear:** Usuario con rol O acceso por enlace
- **Actualizar:** Solo Administradores
- **Lista:** Usuario con rol O acceso por enlace

**Procesamiento en Segundo Plano:**
- Cada 30 min: Los mensajes no leídos se envían a los administradores por correo
- Cada 30 min: Los mensajes fijados del administrador se envían a los publicadores por correo
- Cada 1 hora: Las actualizaciones de notas se envían a los administradores

**Ejemplo:**
```json
{
  "id": "msg_abc",
  "map": "map_xyz",
  "congregation": "cong_123",
  "message": "Por favor volver a visitar la unidad 205",
  "created_by": "juan.perez",
  "read": false,
  "pinned": false,
  "type": "publisher"
}
```

---

### 10. addresses_log

**Propósito:** Registro de auditoría de cambios de estado de direcciones — cada vez que se actualiza el estado de una dirección, se escribe un registro aquí

**Tipo:** Colección base

**Campos:**

| Campo | Tipo | Requerido | Descripción |
|-------|------|-----------|-------------|
| `id` | text(15) | Sí | ID único auto-generado |
| `address` | relation | Sí | FK a Direcciones (eliminación en cascada) |
| `old_status` | text | Sí | Valor de estado de dirección anterior |
| `new_status` | text | Sí | Nuevo valor de estado de dirección |
| `changed_by` | relation | No | FK a Usuarios — usuario que realizó el cambio |
| `created` | date | Auto | Marca de tiempo del cambio |

**Relaciones:**
- M:1 con Direcciones (cada entrada del registro pertenece a una dirección)
- M:1 con Usuarios (a través de `changed_by`)

**Reglas de Acceso:**
- **Lista/Ver:** Director o Administrador
- **Crear:** Hook del sistema (automático al cambiar el estado de la dirección)
- **Actualizar/Eliminar:** Solo Administrador

---

### 11. aggregates

**Propósito:** Instantáneas de progreso en caché para territorios y mapas. Recalculadas por un trabajo en segundo plano cada 10 minutos

**Tipo:** Colección base

**Campos:**

| Campo | Tipo | Requerido | Descripción |
|-------|------|-----------|-------------|
| `id` | text(15) | Sí | ID único auto-generado |
| `notDone` | number | Sí | Conteo de direcciones aún no visitadas |
| `notHome` | number | Sí | Conteo de direcciones "nadie en casa" |
| `progress` | number | Sí | Porcentaje de completado (0–100) |
| `territory` | relation | No | FK a Territorios — establecido para agregados a nivel de territorio |
| `map` | relation | No | FK a Mapas — establecido para agregados a nivel de mapa |

**Relaciones:**
- M:1 con Territorios (instantáneas a nivel de territorio)
- M:1 con Mapas (instantáneas a nivel de mapa)

**Reglas de Negocio:**
- Exactamente uno de `territory` o `map` se establece por registro
- Recalculado cada 10 minutos por el trabajo en segundo plano
- Los valores son instantáneas de solo lectura; el progreso en vivo se calcula bajo demanda

**Reglas de Acceso:**
- **Lista/Ver:** Usuario con rol O acceso por enlace
- **Crear/Actualizar:** Solo trabajo en segundo plano del sistema
- **Eliminar:** Solo Administrador

---

### 12. address_options

**Propósito:** Tabla de unión que rastrea qué opciones a nivel de dirección (de la colección `options`) se han aplicado a direcciones individuales — la tabla puente para la relación muchos-a-muchos entre Dirección y Opción

**Tipo:** Colección base

**Campos:**

| Campo | Tipo | Requerido | Descripción |
|-------|------|-----------|-------------|
| `id` | text(15) | Sí | ID único auto-generado |
| `address` | relation | Sí | FK a Direcciones (eliminación en cascada) |
| `option` | relation | Sí | FK a Opciones (eliminación en cascada) |
| `congregation` | relation | Sí | FK a Congregaciones (desnormalizado para consultas rápidas) |

**Índices:**
- `(address, option)` — Emparejamiento único (una opción por dirección)
- `(congregation)` — Uso de opciones en toda la congregación

**Relaciones:**
- M:1 con Direcciones
- M:1 con Opciones
- M:1 con Congregaciones

**Reglas de Acceso:**
- **Lista/Ver:** Usuario con rol O acceso por enlace
- **Crear/Eliminar:** Director o Administrador

---

## Colecciones de Análisis

Las colecciones de análisis almacenan datos históricos para informes e información. Estas colecciones se gestionan internamente y sus campos exactos pueden evolucionar con el tiempo; se documentan aquí por propósito en lugar de por campos individuales.

### analytics_territories

**Propósito:** Almacena instantáneas periódicas de métricas a nivel de territorio (ej., porcentaje de progreso, conteos de direcciones) a lo largo del tiempo. Se usa para generar gráficos de tendencias históricas e informes de completado de territorio.

### analytics_maps

**Propósito:** Almacena instantáneas periódicas de métricas a nivel de mapa a lo largo del tiempo. Permite informes detallados sobre tasas de completado de mapas individuales y patrones de actividad.

### analytics_daily_status

**Propósito:** Conteos diarios agregados de estados de direcciones (hecho, no hecho, nadie en casa, no visitar, inválido) en toda la congregación. Impulsa los paneles de tendencias día a día y semana a semana.

### analytics_not_home

**Propósito:** Rastrea direcciones que han sido marcadas repetidamente como "nadie en casa". Se usa para identificar objetivos de seguimiento para conductores y para señalar direcciones persistentemente inalcanzables.

### analytics_user_audit

**Propósito:** Registra acciones de usuario incluyendo inicios de sesión, actualizaciones de direcciones y operaciones de asignación para responsabilidad y fines de auditoría. Permite a los administradores revisar el historial de actividades e investigar anomalías.

> **Nota:** Las colecciones de análisis son pobladas por trabajos en segundo plano y leídas por la capa de informes. Se deben evitar escrituras directas desde el código de la aplicación.

---

## Tecnología de Base de Datos

**Motor:** SQLite a través de PocketBase (modernc.org/sqlite v1.40.2+)

**Características:**
- Implementación en Go puro (sin CGo) - permite compilación multiplataforma
- Basado en C SQLite transpilado a Go usando ccgo/v4
- Mantenido activamente con actualizaciones de seguridad regulares (alineado con SQLite 3.50+)

**Características SQL Utilizadas:**
- Campos JSON para datos flexibles (coordenadas, agregados)
- Índices compuestos para optimización de consultas
- Transacciones para cumplimiento ACID
- CTE (Expresiones de Tabla Comunes) para agregaciones complejas

**Ventajas:**
- Base de datos autónoma basada en archivos
- No se necesita servicio de base de datos separado
- Perfecto para despliegues auto-alojados
- Sin dependencia de CGO permite compilaciones de binarios estáticos
- Estabilidad comprobada con parches de seguridad

---

## Relaciones Clave

| Relación | Cardinalidad | Comportamiento de Cascada |
|----------|-------------|--------------------------|
| Congregación → Territorio | 1:M | Eliminación en cascada |
| Territorio → Mapa | 1:M | Eliminación en cascada |
| Mapa → Dirección | 1:M | Eliminación en cascada |
| Mapa → Asignación | 1:M | Eliminación en cascada |
| Mapa → Mensaje | 1:M | Eliminación en cascada |
| Dirección → Opciones | M:M | Array JSON |
| Usuario → Rol | M:M | A través de la tabla de roles |
| Usuario → Asignación | 1:M | Conservar asignaciones |
| Congregación → Opción | 1:M | Eliminación en cascada |
| Congregación → Rol | M:M | A través de la tabla de roles |
| Dirección → addresses_log | 1:M | Eliminación en cascada |
| Territorio → aggregates | 1:M | Recalculado por trabajo en segundo plano |
| Mapa → aggregates | 1:M | Recalculado por trabajo en segundo plano |
| Dirección → address_options | 1:M | Eliminación en cascada |
| Opción → address_options | 1:M | Eliminación en cascada |

---

## Reglas de Negocio

### Cálculo de Progreso

**Fórmula:**
```
completado = (completados + nadie_en_casa_max_intentos) / total_contable * 100
```

**Direcciones Contables:**
- Solo direcciones con opciones `is_countable=true`
- Excluye direcciones NLL e inválidas

**Criterios de Finalización:**
- Estado = `done`, O
- Estado = `not_home` Y `not_home_tries >= congregation.max_tries`

### Ciclo de Vida de la Dirección

```
not_done → not_home (intentos de reintento) → done
         → do_not_call (excluido)
         → invalid (excluido)
```

### Disparadores de Auto-Actualización

1. **Actualización de Dirección:**
   - Actualiza `last_notes_updated` si las notas cambiaron
   - Establece `last_notes_updated_by` al usuario actual
   - Activa el recálculo de agregados
   - Actualiza el progreso del mapa
   - Actualiza el progreso del territorio

2. **Operaciones de Mapa:**
   - Recalcula los agregados cuando cambian las direcciones
   - Actualiza el progreso del territorio

3. **Creación de Asignación:**
   - Establece la expiración basada en la configuración de la congregación
   - Genera un ID único de 20 caracteres como token de acceso

---

## Migración y Actualizaciones del Esquema

**Archivo de Esquema:** `migrations/1764846700_collections_snapshot.go`

**Tamaño:** 55,459 líneas (generado por PocketBase)

**Proceso de Migración:**
1. PocketBase aplica automáticamente las migraciones al iniciar
2. Las migraciones se rastrean en la colección del sistema `_migrations`
3. Los cambios de esquema requieren nuevos archivos de migración

---

## Próximos Pasos

- [Arquitectura](architecture.md) - Arquitectura del sistema
- [Configuración del Backend](backend-setup.md) - Configurar la base de datos

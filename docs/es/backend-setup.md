# Guía de Configuración del Backend

!!! warning "Auto-Alojamiento No Recomendado"
    Esta guía es para usuarios avanzados que desean auto-alojar Ministry Mapper. No recomendamos el auto-alojamiento debido a la complejidad técnica, la carga de mantenimiento y las responsabilidades de seguridad involucradas.
    
    **La mayoría de los usuarios deberían**: Usar nuestro servicio alojado en **[ministry-mapper.com](https://ministry-mapper.com)**

!!! info "¿Buscas Documentación de Usuario?"
    Si eres un usuario regular que intenta aprender a usar Ministry Mapper, consulta la [Guía de Inicio](getting-started.md) o la [Guía de Usuario](user-guide.md) en su lugar.

## Descripción General

El backend de Ministry Mapper (ministry-mapper-be) está construido con Go y PocketBase. Proporciona:

- Almacenamiento y gestión de datos (base de datos SQLite)
- Autenticación y autorización de usuarios
- Sincronización de datos en tiempo real
- Endpoints de API personalizados para la gestión de territorios
- Trabajos en segundo plano para tareas automatizadas
- Notificaciones por correo electrónico mediante SMTP

**Esta guía asume que tienes**:
- Experiencia con administración de servidores
- Comprensión de Docker y la contenedorización
- Familiaridad con las variables de entorno
- Conocimiento de las mejores prácticas de seguridad

Para instrucciones completas de auto-alojamiento, consulta la [Guía de Auto-Alojamiento](self-hosting.md).

## Referencia Rápida

Esta página proporciona una referencia rápida para la configuración del backend. **Para instrucciones completas de configuración, consulta la [Guía de Auto-Alojamiento](self-hosting.md).**

### Stack Tecnológico

- **Go**: Lenguaje de programación rápido y eficiente
- **PocketBase**: Backend como servicio con panel de administración integrado
- **SQLite**: Base de datos ligera en la carpeta `pb_data`
- **Docker**: Contenedor basado en Alpine Linux

### Características Principales

- Almacenamiento y gestión de datos
- Autenticación de usuarios
- Suscripciones en tiempo real (Server-Sent Events)
- Endpoints de API personalizados
- Trabajos en segundo plano programados
- Notificaciones por correo electrónico (SMTP)
- Rastreo de errores (Sentry)
- Indicadores de características (LaunchDarkly)

---

!!! note "Instrucciones Completas de Configuración"
    Las secciones a continuación proporcionan una referencia técnica para la configuración del backend. Para instrucciones de configuración paso a paso con contexto y explicaciones, consulta la **[Guía de Auto-Alojamiento](self-hosting.md)**.

---

## Variables de Entorno Explicadas

### Claves de Integración de Servicios (Opcionales)

```bash
# Clave de API de MailerSend - para enviar correos electrónicos mediante el servicio MailerSend
# Déjalo vacío si usas SMTP en su lugar
MAILERSEND_API_KEY=

# Dirección de correo electrónico para el remitente de MailerSend
MAILERSEND_FROM_EMAIL=

# Clave SDK de LaunchDarkly - para indicadores de características que controlan trabajos en segundo plano
# Los trabajos se ejecutan de manera predeterminada si no está configurado
LAUNCHDARKLY_SDK_KEY=
LAUNCHDARKLY_CONTEXT_KEY=

# Clave API de OpenAI - para resúmenes generados por IA en informes mensuales y resúmenes de mensajes
# Opcional: los informes y resúmenes funcionan bien sin esto; las secciones de IA simplemente se omiten
OPENAI_API_KEY=
```

### Configuración de la Aplicación

```bash
# Nombre mostrado en correos electrónicos y panel de administración
PB_APP_NAME=Ministry Mapper

# URL donde está alojado tu frontend
PB_APP_URL=http://localhost:3000
```

### Cuenta de Administrador Predeterminada

```bash
# Cuenta de administrador inicial creada durante la primera configuración
# ¡Cambia la contraseña inmediatamente después del primer inicio de sesión!
PB_ADMIN_EMAIL=testing_account@ministry-mapper.com
PB_ADMIN_PASSWORD=pb123456789
```

### Configuración de Correo Electrónico (SMTP)

```bash
# Dirección del servidor SMTP (por ejemplo, smtp.gmail.com, smtp.sendgrid.net)
PB_SMTP_HOST=

# Contraseña SMTP (usa la Contraseña de Aplicación para Gmail, no tu contraseña regular)
PB_SMTP_PASSWORD=

# Nombre de usuario SMTP (generalmente tu dirección de correo electrónico)
PB_SMTP_USERNAME=

# Puerto SMTP (587 para TLS, 465 para SSL)
PB_SMTP_PORT=587

# Dirección de correo electrónico mostrada en el campo "De"
PB_SMTP_SENDER_ADDRESS=support@ministry-mapper.com

# Nombre mostrado en el campo "De"
PB_SMTP_SENDER_NAME=MM Support
```

### Seguridad y Control de Acceso

```bash
# Lista de orígenes permitidos para CORS separados por comas
# Usa * para desarrollo, dominios específicos para producción
# Ejemplo: https://app.example.com,https://app2.example.com
PB_ALLOW_ORIGINS=*

# Ocultar la UI de administración del acceso público (true/false)
# Configura como true en producción por seguridad
PB_HIDE_CONTROLS=false
```

### Configuración de Autenticación

```bash
# Habilitar inicio de sesión con contraseña de un solo uso (true/false)
PB_OTP_ENABLED=false

# Habilitar autenticación multifactor (true/false)
PB_MFA_ENABLED=false
```

### Límite de Velocidad

```bash
# Habilitar limitación de velocidad para prevenir abuso de la API (true/false)
PB_ENABLE_RATE_LIMITING=false
```

### Rastreo de Errores (No está en .env.sample - agregar manualmente si es necesario)

```bash
# DSN de Sentry para monitoreo y rastreo de errores
SENTRY_DSN=

# Nombre del entorno para Sentry (development, staging, production)
SENTRY_ENV=production

# Hash de commit de Git - establecido automáticamente por plataformas de alojamiento como Coolify
SOURCE_COMMIT=
```

## Indicadores de Características

Ministry Mapper usa [LaunchDarkly](https://launchdarkly.com) para controlar la ejecución de trabajos en segundo plano. Cada trabajo puede habilitarse o deshabilitarse de forma independiente sin necesidad de redespliegue. Los indicadores de características requieren que `LAUNCHDARKLY_SDK_KEY` y `LAUNCHDARKLY_CONTEXT_KEY` estén configurados.

| Clave del Indicador | Trabajo Controlado | Descripción |
|---------------------|-------------------|-------------|
| `enable-assignments-cleanup` | `cleanUpAssignments` | Eliminar asignaciones de mapa expiradas (se ejecuta cada 5 min) |
| `enable-territory-aggregations` | `updateTerritoryAggregates` | Recalcular estadísticas de progreso del territorio (se ejecuta cada 10 min) |
| `enable-message-processing` | `processMessages` | Procesar mensajes pendientes de publicadores (se ejecuta cada 30 min) |
| `enable-instruction-processing` | `processInstructions` | Procesar instrucciones de asignación de territorio (se ejecuta cada 30 min) |
| `enable-note-processing` | `processNotes` | Procesar notas de congregación actualizadas (se ejecuta cada hora) |
| `enable-monthly-report` | `generateMonthlyReport` | Generar y enviar informes Excel por correo (se ejecuta el 1ro del mes) |
| `enable-unprovisioned-user-processing` | `processUnprovisionedUsers` | Advertir y deshabilitar usuarios sin rol asignado (se ejecuta diariamente) |
| `enable-inactive-user-processing` | `processInactiveUsers` | Advertir y deshabilitar cuentas inactivas — NIST AC-2(3) (se ejecuta diariamente) |
| `enable-new-addresses-notification` | `processNewAddresses` | Enviar correo de resumen diario para direcciones creadas en la aplicación (se ejecuta diariamente) |
| `enable-report-ai-summary` | Resumen de IA en informes | Incluir resumen generado por OpenAI en informes de la congregación |

!!! note
    Si LaunchDarkly no está configurado, todos los trabajos se ejecutan por defecto (habilitado: verdadero).

## Pasos de Instalación

### Opción 1: Despliegue con Docker (Recomendado)

Docker hace que el despliegue sea simple y consistente entre plataformas.

#### 1. Compilar la Imagen de Docker

```bash
docker build -t ministry-mapper-backend .
```

#### 2. Ejecutar el Contenedor

```bash
docker run -d \
  -p 8080:8080 \
  -v /ruta/a/pb_data:/app/pb_data \
  --env-file .env \
  --name ministry-mapper \
  ministry-mapper-backend
```

**Notas Importantes:**

- El contenedor se ejecuta en el puerto **8080** (no 8090)
- `-p 8080:8080` mapea el puerto 8080 del contenedor a tu puerto 8080 del host
- `-v /ruta/a/pb_data:/app/pb_data` guarda tu base de datos permanentemente (reemplaza `/ruta/a/pb_data` con una ruta real en tu sistema)
- `--env-file .env` carga tus variables de entorno desde el archivo `.env`
- `-d` ejecuta el contenedor en segundo plano (modo separado)

### Opción 2: Desarrollo Local

Para desarrollo y pruebas en tu ordenador:

#### 1. Instalar Dependencias de Go

```bash
./scripts/install.sh
```

Este script ejecuta `go get ./...` y `go mod tidy` para instalar todas las dependencias.

#### 2. Configurar Variables de Entorno

```bash
cp .env.sample .env
# Edita .env con tu configuración
```

#### 3. Ejecutar la Aplicación

```bash
./scripts/start.sh
```

Este script exporta variables de entorno desde `.env` y ejecuta `go run main.go serve`.

El backend se iniciará en **http://localhost:8090** de manera predeterminada (puerto predeterminado de PocketBase).

## Acceder al Panel de Administración

Una vez que tu backend esté en ejecución:

1. Navega a `http://tu-url-de-backend/_/` (nota el guión bajo y la barra final)
2. Inicia sesión con tus credenciales de administrador desde `PB_ADMIN_EMAIL` y `PB_ADMIN_PASSWORD`
3. Verás el panel de administración de PocketBase

### Qué Puedes Hacer en el Panel de Administración

- **Colecciones**: Ver y editar todas las tablas de la base de datos (usuarios, congregaciones, territorios, mapas, direcciones, etc.)
- **Registros**: Ver registros de aplicación y solicitudes
- **Configuración**: Configurar autenticación, correo electrónico, copias de seguridad y más
- **Archivos**: Gestionar archivos cargados y almacenamiento
- **Reglas de API**: Establecer permisos para cada colección

**Nota de Seguridad**: Configura `PB_HIDE_CONTROLS=true` en producción para ocultar la UI de administración del acceso público.

## Gestión de la Base de Datos

### Respaldar Tus Datos

Tus datos se almacenan en la carpeta `pb_data`. Las copias de seguridad regulares son cruciales.

#### Copia de Seguridad Manual

```bash
# Crear una copia de seguridad de toda la carpeta pb_data
tar -czf ministry-mapper-backup-$(date +%Y%m%d).tar.gz /ruta/a/pb_data/
```

#### Copia de Seguridad Diaria Automatizada

Configura un cron job para respaldar automáticamente:

```bash
# Agregar al crontab (ejecuta: crontab -e)
0 2 * * * tar -czf /backups/ministry-mapper-backup-$(date +\%Y\%m\%d).tar.gz /ruta/a/pb_data/
```

Esto se ejecuta a las 2 AM diariamente y crea copias de seguridad con marca de tiempo.

#### Restaurar desde Copia de Seguridad

```bash
# Detén la aplicación primero
# Luego extrae la copia de seguridad
tar -xzf ministry-mapper-backup-20240101.tar.gz -C /ruta/a/
```

## Lista de Verificación de Seguridad

Antes de pasar a producción:

- [ ] Cambia la contraseña de administrador predeterminada inmediatamente después del primer inicio de sesión
- [ ] Configura `PB_ALLOW_ORIGINS` con tus dominios reales, no `*`
- [ ] Habilita HTTPS (nunca uses HTTP en producción)
- [ ] Configura `PB_HIDE_CONTROLS=true` para ocultar la UI de administración del acceso no autorizado
- [ ] Considera habilitar la limitación de velocidad con `PB_ENABLE_RATE_LIMITING=true`
- [ ] Configura Sentry (`SENTRY_DSN` y `SENTRY_ENV`) para monitoreo de errores
- [ ] Configura copias de seguridad diarias automatizadas
- [ ] Usa contraseñas fuertes y únicas para todas las cuentas
- [ ] Para SMTP de Gmail, usa Contraseñas de Aplicación en lugar de tu contraseña regular
- [ ] Mantén las dependencias de Go actualizadas regularmente con `./scripts/update.sh`

## Actualizar el Backend

Para actualizar a una nueva versión:

### Actualización con Docker

```bash
# Obtener el último código
git pull origin main

# Recompilar la imagen de Docker
docker build -t ministry-mapper-backend .

# Detener y eliminar el contenedor anterior
docker stop ministry-mapper
docker rm ministry-mapper

# Iniciar el nuevo contenedor (usa el puerto 8080, no 8090)
docker run -d \
  -p 8080:8080 \
  -v /ruta/a/pb_data:/app/pb_data \
  --env-file .env \
  --name ministry-mapper \
  ministry-mapper-backend
```

### Actualización de Desarrollo Local

```bash
# Obtener el último código
git pull origin main

# Actualizar dependencias
./scripts/update.sh

# Reiniciar la aplicación
./scripts/start.sh
```

**Nota**: Tus datos en `pb_data` se conservan durante las actualizaciones.

## Migraciones de Base de Datos

El backend ejecuta automáticamente las migraciones de la base de datos al iniciarse en modo de desarrollo (cuando se usa `go run`). Verás mensajes en los registros como:

```
Running migrations...
Migration 1759244334_collections_snapshot completed
```

**Cómo Funciona:**

- Las migraciones se almacenan en la carpeta `migrations/`
- PocketBase detecta si estás ejecutando mediante `go run` y auto-migra
- En producción (binario compilado), necesitas ejecutar las migraciones manualmente usando: `./main migrate`

**Importante:** Nunca elimines ni modifiques los archivos de migración manualmente. Esto puede dañar tu base de datos.

## Monitoreo y Registros

### Ver Registros

#### Registros de Docker

```bash
# Ver registros recientes
docker logs ministry-mapper

# Seguir registros en tiempo real (útil para depuración)
docker logs -f ministry-mapper

# Ver las últimas 100 líneas
docker logs --tail 100 ministry-mapper
```

#### Registros de la Aplicación

PocketBase almacena registros en la carpeta `pb_data/logs/`. Estos incluyen:

- Registros de solicitudes
- Registros de errores
- Registros de aplicación personalizados

### Qué Buscar en los Registros

- **Mensajes de Inicio**: Confirma la inicialización exitosa (por ejemplo, "Starting Ministry Mapper build...")
- **Mensajes de Migración**: Migraciones de base de datos siendo aplicadas
- **Inicialización de Sentry**: Configuración de rastreo de errores
- **Cron Jobs**: Ejecución de tareas en segundo plano (agregados de territorio, procesamiento de mensajes, etc.)
- **Mensajes de Error**: Problemas con la base de datos, correo electrónico, llamadas a la API o LaunchDarkly
- **Eventos de Autenticación**: Intentos de inicio de sesión y actividad del usuario
- **Problemas de Rendimiento**: Consultas lentas o tiempos de espera

## Solución de Problemas

### Puerto ya en Uso

**Problema**: Error de "Puerto ya en uso"

**Solución**:

```bash
# Para Docker (puerto 8080)
lsof -i :8080

# Para desarrollo local (puerto 8090)
lsof -i :8090

# Matar el proceso que usa el puerto
kill -9 <id_proceso>

# O mapear a un puerto diferente
docker run -p 8081:8080 ...  # Mapea el puerto 8081 del host al puerto 8080 del contenedor
```

### Base de Datos Bloqueada

**Problema**: Error de "Database is locked"

**Solución**:
- Detén todos los contenedores/procesos que acceden a la base de datos
- Verifica si hay un proceso de copia de seguridad en ejecución
- Asegúrate de que solo una instancia de la aplicación esté en ejecución
- SQLite no admite escrituras concurrentes de múltiples procesos
- Reinicia la aplicación

### Correo Electrónico No Se Envía

**Problema**: Los usuarios no reciben correos electrónicos

**Soluciones**:
1. **Verifica la Configuración SMTP**: Comprueba todas las variables `PB_SMTP_*` en `.env`
2. **Usuarios de Gmail**: Usa una [Contraseña de Aplicación](https://support.google.com/accounts/answer/185833), no tu contraseña regular
3. **Verifica la Carpeta de Spam**: Los correos pueden estar siendo filtrados
4. **Prueba la Conexión SMTP**: Usa una herramienta como `telnet` o un cliente de correo para verificar las credenciales SMTP
5. **Revisa los Registros**: Busca mensajes de error en `pb_data/logs/` o los registros de Docker
6. **Problemas de Puerto**: Asegúrate de que el puerto 587 (TLS) o 465 (SSL) no esté bloqueado por el firewall

### Sin Memoria

**Problema**: La aplicación se bloquea con errores de memoria

**Soluciones**:
- Aumenta los límites de memoria de Docker: `docker run -m 1g ...`
- Verifica los registros en busca de fugas de memoria
- Optimiza las consultas grandes de la base de datos
- Actualiza la asignación de RAM de tu plan de alojamiento
- Considera usar optimizaciones PRAGMA de SQLite para grandes conjuntos de datos

### No Se Puede Acceder al Panel de Administración

**Problema**: Error 404 al acceder a la URL del administrador

**Soluciones**:
- Asegúrate del formato de URL: `http://tu-url/_/` (con guión bajo y barra final)
- Verifica si `PB_HIDE_CONTROLS=true` (evita el acceso a la UI de administración)
- Verifica que la aplicación esté en ejecución: `docker ps` o verifica el proceso
- Limpia la caché y las cookies del navegador
- Verifica los registros en busca de errores de inicio

## Consejos de Rendimiento

### Congregaciones Pequeñas (< 100 usuarios)
- La configuración predeterminada funciona bien
- 512MB de RAM es suficiente
- SQLite maneja esta carga fácilmente
- No se necesita configuración especial

### Congregaciones Medianas (100-500 usuarios)
- Recomendado: 1GB de RAM
- Habilita la limitación de velocidad: `PB_ENABLE_RATE_LIMITING=true`
- Configura copias de seguridad automatizadas diarias
- Monitorea el tamaño de la base de datos en `pb_data/data.db`

### Congregaciones Grandes (> 500 usuarios)
- Recomendado: 2GB+ de RAM
- Habilita la limitación de velocidad
- Considera alojamiento dedicado (no compartido)
- Monitorea los registros en busca de consultas lentas
- Configura la política de retención de copias de seguridad (conserva los últimos 30 días)
- Considera los indicadores de características de LaunchDarkly para controlar la frecuencia de los trabajos en segundo plano

## Endpoints de API

El backend proporciona estos endpoints de API personalizados autenticados (todos requieren autenticación de usuario):

### Gestión de Mapas
- `POST /map/codes` - Obtener todos los códigos de mapa para un mapa específico
- `POST /map/code/add` - Agregar un nuevo código de dirección a un mapa
- `POST /map/codes/update` - Actualizar la secuencia/orden de los códigos de mapa
- `POST /map/code/delete` - Eliminar un código de dirección de un mapa
- `POST /map/add` - Crear un nuevo mapa
- `POST /map/territory/update` - Actualizar a qué territorio pertenece un mapa
- `POST /map/reset` - Reiniciar un mapa (limpiar todas las asignaciones y datos)

### Gestión de Pisos
- `POST /map/floor/add` - Agregar un nivel de piso a un mapa
- `POST /map/floor/remove` - Eliminar un piso de un mapa

### Gestión de Territorios
- `POST /territory/reset` - Reiniciar un territorio (limpiar asignaciones)
- `POST /territory/link` - Crear un enlace de acceso rápido para un territorio

### Configuración
- `POST /options/update` - Actualizar opciones/configuraciones de la congregación

**Autenticación**: Todos los endpoints requieren un token de autenticación de PocketBase válido en el encabezado de la solicitud.

**APIs Integradas de PocketBase**: Además de los endpoints personalizados, PocketBase proporciona APIs REST automáticas para todas las colecciones (operaciones CRUD) en `/api/collections/{collection}/records`.

## Trabajos en Segundo Plano (Programador Cron)

El backend ejecuta tareas en segundo plano automatizadas usando un programador cron. Estos trabajos son controlados por los indicadores de características de LaunchDarkly:

### Trabajos Programados

1. **Agregados de Territorio** (`*/10 * * * *` - cada 10 minutos)
   - Actualiza las estadísticas y datos agregados del territorio
   - Indicador de característica: `enable-territory-aggregations`

2. **Limpieza de Asignaciones** (`*/5 * * * *` - cada 5 minutos)
   - Elimina asignaciones de territorio expiradas o no válidas
   - Indicador de característica: `enable-assignments-cleanup`

3. **Procesamiento de Mensajes** (`*/30 * * * *` - cada 30 minutos)
   - Procesa los mensajes pendientes en la cola
   - Adjunta opcionalmente un resumen generado por IA cuando `OPENAI_API_KEY` está configurado
   - Indicador de característica: `enable-message-processing`

4. **Procesamiento de Instrucciones** (`*/30 * * * *` - cada 30 minutos)
   - Maneja las instrucciones de asignación de territorio pendientes
   - Indicador de característica: `enable-instruction-processing`

5. **Procesamiento de Notas** (`0 * * * *` - cada hora)
   - Procesa notas actualizadas para las congregaciones
   - Adjunta opcionalmente un resumen generado por IA cuando `OPENAI_API_KEY` está configurado
   - Indicador de característica: `enable-note-processing`

6. **Informe Mensual** (`0 0 1 * *` - el día 1 de cada mes a medianoche)
   - Genera un informe Excel de datos de la congregación y lo envía por correo electrónico a los administradores
   - Indicador de característica: `enable-monthly-report`
   - Indicador de resúmenes de IA: `enable-report-ai-summary` (requiere `OPENAI_API_KEY`)

7. **Procesamiento de Usuarios No Aprovisionados** (`0 1 * * *` - diariamente a las 01:00 UTC)
   - Aplica el ciclo de vida del usuario para cuentas sin asignación de rol:
     día 3 → primer correo de advertencia, día 6 → correo de advertencia final, día 7 → cuenta deshabilitada, día 37+ → eliminación permanente
   - Indicador de característica: `enable-unprovisioned-user-processing`

8. **Procesamiento de Usuarios Inactivos** (`30 1 * * *` - diariamente a las 01:30 UTC)
   - Advierte y eventualmente deshabilita cuentas que han estado inactivas más allá del umbral configurado
   - Indicador de característica: `enable-inactive-user-processing`

**Nota**: Si LaunchDarkly no está configurado, todos los trabajos se ejecutan de manera predeterminada (habilitado: true).

### Resúmenes de Informes con IA

Cuando `OPENAI_API_KEY` está configurado **y** el indicador de LaunchDarkly `enable-report-ai-summary` está activado, el informe mensual de Excel incluye un resumen generado por IA para cada hoja de territorio. La IA analiza los datos de actividad y produce un resumen de 2-3 oraciones más una lista de observaciones clave.

Esta característica es completamente opcional — los informes son completamente funcionales sin ella. Para habilitarla:

1. Configura `OPENAI_API_KEY` en tu archivo `.env`.
2. Habilita el indicador `enable-report-ai-summary` en tu proyecto de LaunchDarkly (o omite LaunchDarkly por completo y el indicador se habilitará de manera predeterminada).

## Hooks de la Base de Datos

La aplicación maneja automáticamente ciertos eventos:

- **Actualizaciones de Direcciones**: Cuando las notas de la dirección cambian, las marcas de tiempo y la información del usuario se registran
- **Actualizaciones de Direcciones**: Activan el recálculo del agregado del mapa
- **Autenticación de Usuario**: Actualiza la marca de tiempo del último inicio de sesión
- **Creación de Usuario**: Normaliza y convierte a minúsculas las direcciones de correo electrónico

## Próximos Pasos

- Configura la [Aplicación Frontend](frontend-setup.md)
- Aprende sobre [Gestión de Usuarios](user-management.md)
- Configura los [Ajustes de Seguridad](security.md)

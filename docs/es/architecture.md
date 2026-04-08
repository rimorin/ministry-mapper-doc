# Descripción General de la Arquitectura

## Arquitectura del Sistema

Ministry Mapper consta de dos componentes principales que trabajan juntos:

### Diagrama de Arquitectura de Alto Nivel

```
┌──────────────────────────────────────────────────────────────────┐
│                     Aplicaciones Cliente                          │
│         (UI Web, Apps Móviles, Integraciones de Terceros)        │
└──────────────────────────┬───────────────────────────────────────┘
                           │
                HTTP REST API (Puerto 8080)
                           │
┌──────────────────────────▼───────────────────────────────────────┐
│                    Aplicación PocketBase                          │
├──────────────────────────────────────────────────────────────────┤
│                                                                   │
│  ┌─────────────────────────────────────────────────────────────┐ │
│  │ Capa de API (internal/handlers/)                            │ │
│  │ - Gestión de Mapas (9 endpoints)                            │ │
│  │ - Gestión de Territorios (2 endpoints)                      │ │
│  │ - Configuración (1 endpoint)                                │ │
│  └─────────────────────────────────────────────────────────────┘ │
│                                                                   │
│  ┌─────────────────────────────────────────────────────────────┐ │
│  │ Capa de Middleware                                          │ │
│  │ - Autenticación y Autorización                              │ │
│  │ - Captura de Errores (Sentry)                               │ │
│  │ - Registro de Solicitudes                                   │ │
│  └─────────────────────────────────────────────────────────────┘ │
│                                                                   │
│  ┌─────────────────────────────────────────────────────────────┐ │
│  │ Trabajos en Segundo Plano (internal/jobs/)                  │ │
│  │ - Tareas programadas (basadas en cron)                     │ │
│  │ - Control de indicadores de características (LaunchDarkly) │ │
│  └─────────────────────────────────────────────────────────────┘ │
│                                                                   │
│  ┌─────────────────────────────────────────────────────────────┐ │
│  │ Servicios Integrados                                        │ │
│  │ - Autenticación (Email/Contraseña, OAuth2, OTP, MFA)       │ │
│  │ - Eventos en Tiempo Real (SSE)                             │ │
│  │ - Panel de Administración                                  │ │
│  └─────────────────────────────────────────────────────────────┘ │
│                                                                   │
└────────────┬──────────────────────┬────────────────┬──────────────┘
             │                      │                │
             │                      │                │
    ┌────────▼──────┐      ┌────────▼──────┐  ┌─────▼────────┐
    │   SQLite       │      │  Sentry       │  │MailerSend    │
    │   Base de Datos│      │  (Rastreo de  │  │(Correo)      │
    │   (/app/pb_data)│      │  Errores)     │  │              │
    └────────────────┘      └────────────────┘  └──────────────┘
```

## Stack Tecnológico

### Stack del Backend

| Capa | Tecnología | Versión | Propósito |
|------|-----------|---------|-----------|
| **Lenguaje** | Go | 1.25.0 | Backend de alto rendimiento |
| **Marco de Backend** | PocketBase | 0.36.7 | BaaS con SQLite |
| **Base de Datos** | SQLite | v1.40.2+ | Base de datos autónoma |
| **Marco Web** | Echo | v5 | Enrutamiento HTTP |
| **Contenedor** | Docker | Última | Contenedorización |
| **Rastreo de Errores** | Sentry | v0.43.0 | Monitoreo de errores |
| **Servicio de Correo** | MailerSend | v1.6.3 | API de correo electrónico |
| **Indicadores de Características** | LaunchDarkly | v7.14.6 | Gestión de características |
| **Generación de Informes** | Excelize | v2.10.1 | Informes Excel |
| **Resúmenes con IA** | OpenAI GPT | v3.29.0 | Resúmenes inteligentes de informes y mensajes |

### Stack del Frontend

| Capa | Tecnología | Versión | Propósito |
|------|-----------|---------|-----------|
| **Marco** | React | 19.2.4 | Biblioteca de UI |
| **Lenguaje** | TypeScript | 5.9.3 | Seguridad de tipos |
| **Herramienta de Compilación** | Vite | 8.0.2 | Desarrollo y compilación rápidos |
| **Enrutamiento** | Wouter | 3.9.0 | Enrutamiento ligero |
| **Gestión de Estado** | Context API | - | Estado global |
| **Marco de UI** | Bootstrap | 5.3.8 | Marco CSS |
| **Mapas** | Leaflet | 1.9.4 | Mapas interactivos |
| **Pruebas** | Vitest | 4.1.1 | Pruebas unitarias |
| **Calidad de Código** | ESLint + Prettier | Última | Linting y formato |

## Arquitectura de Datos

### Modelo de Relación de Entidades

El sistema organiza los datos de forma jerárquica con aislamiento a nivel de congregación:

```
Congregación (Límite organizacional)
    ├── Territorio (Región geográfica)
    │   ├── Mapa (Edificio/Ubicación)
    │   │   └── Dirección (Unidad individual)
    │   └── Opción (Clasificación del tipo de dirección)
    ├── Usuario (Usuario del sistema)
    │   └── Rol (Asignación de permisos)
    ├── Asignación (Acceso del publicador)
    └── Mensaje (Notificación)
```

### Colecciones Principales

**1. Congregaciones** - Configuraciones y límites de la organización
- Controla el aislamiento multi-inquilino
- Define la duración de asignación y los límites de reintentos
- Configuración regional (zona horaria, país)

**2. Territorios** - Divisiones geográficas
- Contiene múltiples mapas
- Rastrea el porcentaje de finalización agregado
- Organiza áreas de servicio de campo

**3. Mapas** - Ubicaciones específicas (edificios)
- Estructuras de uno o varios pisos
- Coordenadas geográficas
- Seguimiento de progreso y estadísticas

**4. Direcciones** - Unidades/hogares individuales
- Seguimiento de estado (no completado, completado, nadie en casa, NLL)
- Notas de visita e historial
- Información de piso y unidad

**5. Usuarios** - Cuentas del sistema
- Autenticación por correo electrónico/contraseña u OAuth2
- Soporte de autenticación multifactor
- Roles específicos por congregación

**6. Roles** - Asignaciones de permisos
- Tres niveles: read_only, conductor, administrator
- Permisos específicos por congregación
- Control de acceso basado en roles

**7. Asignaciones** - Tokens de acceso para publicadores
- Enlaces de acceso con tiempo limitado
- Acceso anónimo para trabajadores de campo
- Expiración automática

**8. Opciones** - Clasificaciones de direcciones
- Personalizables por congregación
- Tipos contables vs. no contables
- Afectan los cálculos de progreso

**9. Mensajes** - Comunicaciones
- Retroalimentación de publicadores
- Instrucciones del administrador
- Notificaciones por correo electrónico

## Arquitectura del Frontend

### Patrón de Organización de Componentes

Ministry Mapper utiliza una **organización de componentes basada en características**:

```
Páginas (Rutas)
  ├── Panel de Administración (/)
  ├── Mapa del Publicador (/map/:id)
  ├── Gestión de Usuarios (/usermgmt)
  └── Página Principal (/)

Componentes (UI Reutilizable)
  ├── Componentes de Formulario (14 componentes)
  ├── Componentes de Modal (24+ modales)
  ├── Componentes de Navegación (28 componentes)
  ├── Componentes de Tabla (9 componentes)
  ├── Componentes de Mapa (7 componentes)
  ├── Middleware/Proveedores (5 componentes)
  └── Páginas Estáticas (8 componentes)

Lógica de Negocio (Hooks Personalizados)
  ├── useTerritoryManagement
  ├── useMapManagement
  ├── useCongManagement
  ├── useModalManagement
  ├── useRealtimeSubscription
  └── [4 más...]

Utilidades y Tipos
  ├── Interfaces TypeScript
  ├── Constantes
  ├── Funciones de Ayuda (30+)
  ├── Integración con PocketBase
  ├── Políticas de Autorización
  └── Configuración i18n
```

### Estrategia de Gestión de Estado

**Enfoque de Cuatro Capas:**

1. **Contexto Global** - Tema, idioma, modo de la aplicación
2. **Hooks Personalizados** - Lógica y datos específicos del dominio
3. **Estado Local del Componente** - Controles de UI y formularios
4. **Estado Persistente** - LocalStorage para preferencias

**Beneficios:**
- Sin sobrecarga de Redux
- Flujo de datos más simple
- Mejor división de código
- Pruebas más fáciles

### Patrón de Flujo de Datos

```
Acción del Usuario
    ↓
Manejador de Eventos del Componente
    ↓
Hook Personalizado (validación, gestión de estado)
    ↓
Utilidades de PocketBase (operaciones CRUD)
    ↓
Backend de PocketBase (permisos, base de datos)
    ↓
Suscripción en Tiempo Real (SSE)
    ↓
Actualizaciones Automáticas de UI
```

## Patrones de Diseño Clave

| Patrón | Uso | Propósito |
|--------|-----|-----------|
| **Envoltorio de Middleware** | Integración con Sentry | Rastreo centralizado de errores |
| **Hooks de Eventos** | Ciclo de vida de PocketBase | Actualizaciones automáticas |
| **Programador de Trabajos** | Cron con indicadores de características | Operaciones programadas |
| **Patrón de Transacción** | Operaciones de base de datos | Consistencia de datos |
| **Acceso Basado en Enlace** | Tokens de asignación | Acceso anónimo |
| **Caché de Agregados** | Campos JSON | Cálculos rápidos |
| **Hooks Personalizados** | Lógica de negocio | Lógica reutilizable |
| **Patrón Proveedor** | Proveedores de contexto | Estado global |
| **Carga Diferida** | División de rutas | Rendimiento |
| **Patrón de Política** | Autorización | Reglas de seguridad |

## Arquitectura de Seguridad

### Métodos de Autenticación

1. **Correo electrónico/Contraseña** - Autenticación estándar con hash bcrypt
2. **OAuth2 (Google)** - Inicio de sesión social con flujo PKCE
3. **Contraseña de Un Solo Uso (OTP)** - Capa de seguridad adicional
4. **Autenticación Multifactor (MFA)** - Seguridad mejorada para administradores

### Modelo de Autorización

**Control de Acceso Basado en Roles (RBAC):**

| Rol | Permisos | Caso de Uso |
|-----|----------|------------|
| **read_only** | Solo ver datos | Observadores, auditores |
| **conductor** | Crear asignaciones, gestionar mapas | Coordinadores de campo |
| **administrator** | Acceso completo | Administradores del sistema |

### Patrones de Control de Acceso

**Patrón 1: Usuario Autenticado con Rol**
```
El usuario debe estar conectado
+ El usuario tiene rol en la colección de roles
+ El rol es para una congregación específica
```

**Patrón 2: Acceso Basado en Enlace**
```
ID de asignación pasado en el encabezado
+ La asignación existe y no ha expirado
+ La asignación pertenece al mapa solicitado
```

**Patrón 3: Aislamiento de Congregación**
```
Cada entidad tiene clave foránea de congregación
+ Las reglas de acceso filtran por congregación
+ Previene acceso a datos entre congregaciones
```

## Sincronización en Tiempo Real

### Arquitectura SSE

- **API en Tiempo Real de PocketBase**: Server-Sent Events (SSE)
- **Suscripciones Automáticas**: Los componentes se suscriben a las colecciones
- **Actualizaciones en Vivo**: Los cambios se transmiten a todos los clientes conectados
- **Resolución de Conflictos**: Resolución basada en marcas de tiempo
- **Detección de Visibilidad**: Actualización automática cuando la pestaña se vuelve activa

### Patrón de Suscripción

```typescript
// Los componentes se suscriben a cambios de datos
useRealtimeSubscription('addresses', (data) => {
  // La UI se actualiza automáticamente
});
```

## Trabajos en Segundo Plano

### Tareas Programadas

| Trabajo | Horario | Propósito |
|---------|---------|-----------|
| **Limpieza de Asignaciones** | Cada 5 min | Eliminar asignaciones expiradas |
| **Agregados de Territorio** | Cada 10 min | Actualizar estadísticas de progreso |
| **Procesamiento de Mensajes** | Cada 30 min | Enviar notificaciones de mensajes |
| **Instrucciones** | Cada 30 min | Enviar mensajes del administrador |
| **Actualizaciones de Notas** | Cada 1 hora | Notificar cambios de notas |
| **Informes Mensuales** | 1 del mes | Generar informes Excel (con resúmenes opcionales de IA) |
| **Usuarios No Aprovisionados** | Diario 01:00 UTC | Aplicar ciclo de vida del usuario (advertencias → deshabilitar → eliminar) |
| **Usuarios Inactivos** | Diario 01:30 UTC | Advertir y deshabilitar cuentas inactivas |

### Control de Indicadores de Características

Todos los trabajos en segundo plano controlados por indicadores de características de LaunchDarkly:
- Habilitar/deshabilitar trabajos sin despliegue
- Capacidades de lanzamiento gradual
- Interruptor de emergencia

### Integración con IA/LLM

Ministry Mapper se integra con el modelo GPT de OpenAI para resumen inteligente de texto. Esta es una característica **opcional** controlada por el indicador de LaunchDarkly `enable-report-ai-summary`.

**Modelo**: GPT (última disponible mediante SDK `openai-go` v3.29.0)  
**Temperatura**: 0.3 (salida factual y determinista)  
**Formato de Respuesta**: Modo JSON para datos estructurados

**Casos de Uso:**

| Contexto | Trabajo | Salida |
|----------|---------|--------|
| Informes mensuales de Excel | `generateMonthlyReport` | `summary` y `key_points` por territorio |
| Resúmenes de mensajes de publicadores | `processMessages` | `overview` y `key_themes` |
| Resúmenes de notas de la congregación | `processNotes` | `overview` y `key_themes` |

**Degradación Elegante**: Si `OPENAI_API_KEY` no está configurado o el indicador `enable-report-ai-summary` está desactivado, el sistema recae en informes y correos sin contenido generado por IA — no se generan errores.

### Optimizaciones del Frontend

- **División de Código**: Rutas cargadas diferidamente
- **Optimización de Paquetes**: Eliminación de árbol, minificación
- **Compilador de React 19**: Memorización automática
- **Caché PWA**: Service worker para activos estáticos
- **Listas Virtualizadas**: React-window para grandes conjuntos de datos

### Optimizaciones del Backend

- **Índices de Base de Datos**: 25+ índices para consultas rápidas
- **Caché de Agregados**: Estadísticas pre-calculadas en JSON
- **Agrupación de Conexiones**: Conexiones eficientes a la base de datos
- **Procesamiento por Lotes de Transacciones**: Reducir los viajes de ida y vuelta a la base de datos

## Arquitectura de Despliegue

### Contenedorización con Docker

**Backend:**
- Compilación Docker de múltiples etapas
- Base Alpine para huella pequeña
- Montajes de volumen para persistencia de datos
- Comprobaciones de salud para confiabilidad

**Frontend:**
- Compilación estática servida por CDN
- Caché en el borde para entrega rápida
- Capacidades de Aplicación Web Progresiva

### Opciones de Infraestructura

1. **Servicio Alojado** (Recomendado)
   - ministry-mapper.com
   - Infraestructura gestionada
   - Actualizaciones automáticas

2. **Auto-Alojado**
   - Railway, Render, DigitalOcean
   - Requiere experiencia técnica
   - Control total

## Monitoreo y Observabilidad

### Rastreo de Errores

- **Integración con Sentry**: Tanto frontend como backend
- **Categorización de Errores**: Por gravedad y tipo
- **Mapas de Origen**: Depuración de código minificado
- **Seguimiento de Versiones**: Errores específicos por versión

### Registro

- **Backend**: Registros JSON estructurados
- **Frontend**: Registro en consola durante el desarrollo
- **Producción**: Errores enviados a Sentry
- **Pista de Auditoría**: Acciones del usuario rastreadas

## Consideraciones de Escalabilidad

### Limitaciones Actuales

- Limitación de escritura única de SQLite
- Adecuado para congregaciones pequeñas a medianas (< 1000 usuarios concurrentes)
- Escalado vertical (servidor más potente)

### Escalabilidad Futura

- PocketBase admite réplicas de lectura
- Puede migrar a PostgreSQL si es necesario
- Caché CDN para activos estáticos
- Funciones de borde para optimización de API

## Decisiones Tecnológicas

### ¿Por Qué Estas Tecnologías?

**React 19**: Últimas características, optimización automática, paquete más pequeño

**TypeScript**: Seguridad de tipos, mejor soporte de IDE, menos errores en tiempo de ejecución

**Vite**: Servidor de desarrollo rápido, HMR instantáneo, compilaciones optimizadas

**PocketBase**: Auto-alojado, sin dependencia del proveedor, fácil de mantener

**Wouter**: Ligero (3KB vs 40KB para React Router)

**Bootstrap**: Probado en batalla, componentes completos, buena accesibilidad

**Leaflet**: Mapas gratuitos, sin límites de API, ligero

**SQLite**: Autónomo, sin servidor de base de datos separado, perfecto para auto-alojamiento

## Próximos Pasos

- [Configuración del Backend](backend-setup.md) - Configurar el backend
- [Configuración del Frontend](frontend-setup.md) - Desplegar el frontend
- [Guía de Seguridad](security.md) - Mejores prácticas de seguridad

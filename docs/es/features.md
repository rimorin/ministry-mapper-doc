# Resumen de Características

## Introducción

Ministry Mapper es una plataforma integral de gestión digital de territorios diseñada para congregaciones religiosas. Esta página proporciona una descripción detallada de todas las características y capacidades.

---

## Gestión de Territorios

### Organizar Regiones Geográficas

**Crear y Gestionar Territorios:**
- Crear territorios ilimitados por congregación
- Asignar códigos y descripciones únicos
- Organizar territorios por prioridad con secuenciación mediante arrastrar y soltar
- Seguimiento automático del porcentaje de completado
- Restablecer el estado del territorio para reiniciar el seguimiento
- Mover mapas entre territorios fácilmente

**Estadísticas de Territorio:**
- Seguimiento agregado de completado
- Indicadores visuales de progreso
- Actualización de estadísticas en tiempo real
- Desgloses por territorio
- Seguimiento histórico

**Caso de Uso:** Divida el área de servicio de su congregación en territorios manejables para un trabajo organizado en el servicio del campo.

---

## Gestión de Mapas y Direcciones

### Seguimiento Interactivo de Ubicaciones

**Tipos de Mapas:**

**1. Direcciones Públicas (Edificios de Varios Pisos)**
- Soporte para edificios de varios pisos
- Agregar/eliminar pisos dinámicamente
- Cada piso contiene múltiples unidades
- Aplicación automática del código de dirección a todos los pisos
- Organización piso por piso

**2. Direcciones Privadas (Residencias de Un Solo Piso)**
- Casas individuales o propiedades independientes
- Estructura más simple sin subdivisión por pisos
- Entrada rápida de direcciones

**Características del Mapa:**
- Adjuntar coordenadas GPS para ubicación precisa
- Agregar descripciones detalladas
- Números de secuencia para visitas organizadas
- Integración visual de mapas con Leaflet/OpenStreetMap
- Obtener direcciones a cualquier dirección
- Soporte de geolocalización

**Operaciones de Direcciones:**
- Agregar/eliminar códigos de direcciones
- Reordenar direcciones para rutas óptimas
- Operaciones masivas para eficiencia
- Copiar direcciones entre pisos
- Renombrar y reorganizar

---

## Seguimiento de Unidades/Hogares

### Gestión Integral del Estado de Visitas

**Tipos de Estado:**

| Estado | Descripción | Impacto en el Progreso |
|--------|-------------|------------------------|
| **No Hecho** | Estado inicial, aún no visitado | Contado como incompleto |
| **Hecho** | Completado con éxito | Contado como completo |
| **No en Casa** | Nadie en casa, se puede reintentar | Contado después de intentos máximos |
| **No Visitar (DNC)** | El hogar solicita no ser visitado | Excluido del conteo |
| **Inválido** | La unidad no existe | Excluido del conteo |

**Seguimiento de No en Casa:**
- Intentos máximos configurables por congregación
- Incremento automático del contador
- Tratado como "hecho" después de los intentos máximos
- Previene revisitas indefinidas

**Lógica de Completado:**
```
Completo cuando:
  - Estado = Hecho, O
  - Estado = No en Casa Y intentos >= intentos_máximos

Progreso % = Completados / Total de Unidades Contables × 100
```

**Opciones de Hogar:**
- Clasificaciones de direcciones personalizables
- Tipos contables vs no contables
- Opciones predeterminadas para nuevas direcciones
- Múltiples opciones por dirección
- Afecta los cálculos de progreso

**Notas de Visita:**
- Agregar notas detalladas por dirección
- Seguimiento automático de marca de tiempo
- Rastrear quién hizo las actualizaciones
- Notificaciones por correo electrónico para cambios en notas
- Visualización del historial de notas

---

## Sistema de Asignaciones (Enlaces de Publicador)

### Acceso Seguro y con Tiempo Limitado

**Características de Enlaces:**
- **Tiempo Limitado:** Expiración configurable (predeterminado 24 horas)
- **Basado en Token:** Tokens de acceso seguros de 20 caracteres
- **Sin Cuenta Necesaria:** Los publicadores acceden solo mediante enlace
- **Dos Tipos:**
  - Asignaciones normales para trabajo regular de territorio
  - Asignaciones personales para ministerio personal

**Algoritmo Inteligente de Asignación:**

Al solicitar una nueva asignación, el sistema:

1. **Factor Principal:** Selecciona el mapa con menos asignaciones activas
   - Previene sobrecargar mapas individuales
   - Equilibra la carga de trabajo

2. **Factor Secundario:** Prefiere mapas geográficamente más cercanos
   - Usa coordenadas GPS
   - Umbral de 50 metros
   - Reduce el tiempo de viaje

3. **Factor Terciario:** Prioriza menor progreso de completado
   - Asegura completado equilibrado del territorio
   - Previene que algunas áreas sean descuidadas

**Flujo de Trabajo de Asignación:**
```
1. El conductor genera un enlace de asignación
2. El enlace se comparte con el publicador (QR, correo electrónico, texto)
3. El publicador accede a /map/:assignmentId
4. El sistema valida el token y la expiración
5. El publicador actualiza el estado de la dirección en tiempo real
6. El enlace expira automáticamente
7. El administrador recibe actualizaciones en tiempo real
```

**Gestión de Asignaciones:**
- Ver asignaciones activas por mapa
- Ver quién tiene qué territorio
- Rastrear historial de asignaciones
- Eliminación manual de asignaciones
- Limpieza automática de enlaces expirados (cada 5 minutos)

---

## Gestión de Usuarios y Control de Acceso

### Sistema de Permisos Basado en Roles

**Cuatro Roles de Usuario:**

#### 1. Publicador (Más Restringido)
**Método de Acceso:** Solo enlaces de asignación con tiempo limitado

**Permisos:**
- ✅ Ver solo el mapa asignado
- ✅ Actualizar estado de dirección/unidad
- ✅ Agregar notas de visita
- ✅ Enviar mensajes de retroalimentación
- ❌ No puede ver otros territorios
- ❌ No puede crear territorios
- ❌ No puede gestionar usuarios

**Caso de Uso:** Publicadores del servicio del campo trabajando territorios

---

#### 2. Solo Lectura
**Método de Acceso:** Cuenta de usuario con rol de solo lectura

**Permisos:**
- ✅ Ver todos los datos de la congregación
- ✅ Ver territorios y mapas
- ✅ Ver información de direcciones
- ✅ Ver mensajes
- ❌ No puede modificar nada
- ❌ No puede crear asignaciones
- ❌ No puede gestionar usuarios

**Caso de Uso:** Observadores, auditores, miembros del comité de servicio

---

#### 3. Conductor (Nivel Medio)
**Método de Acceso:** Cuenta de usuario con rol de conductor

**Permisos:**
- ✅ Todos los permisos de Solo Lectura
- ✅ Crear y gestionar territorios
- ✅ Crear y gestionar mapas
- ✅ Agregar/actualizar direcciones
- ✅ Generar enlaces de asignación para publicadores
- ✅ Ver todos los datos de la congregación
- ✅ Gestionar opciones de hogar
- ✅ Restablecer territorios
- ❌ No puede gestionar roles de usuario
- ❌ No puede configurar ajustes de congregación
- ❌ No puede eliminar usuarios

**Caso de Uso:** Superintendentes del servicio del campo, coordinadores de territorio

---

#### 4. Administrador (Acceso Completo)
**Método de Acceso:** Cuenta de usuario con rol de administrador

**Permisos:**
- ✅ Todos los permisos de Conductor
- ✅ Crear y gestionar usuarios
- ✅ Asignar roles de usuario
- ✅ Configurar ajustes de congregación
- ✅ Gestionar opciones de hogar
- ✅ Eliminar territorios y mapas
- ✅ Eliminar cuentas de usuario
- ✅ Ver todas las asignaciones y mensajes
- ✅ Configurar intentos máximos de no en casa
- ✅ Establecer duración de expiración de enlaces
- ✅ Acceder a registros del sistema

**Caso de Uso:** Siervos de territorio, administradores del sistema

---

### Gestión de Cuentas de Usuario

**Características:**
- Autenticación basada en correo electrónico
- Verificación de correo electrónico requerida
- Funcionalidad de restablecimiento de contraseña
- Autenticación OAuth2 de Google
- Soporte de autenticación multifactor (MFA)
- Soporte de contraseña de un solo uso (OTP)
- Deshabilitar/habilitar cuenta
- Seguimiento del último inicio de sesión
- Soporte para múltiples congregaciones (diferentes roles por congregación)

---

## Configuración de Congregación

### Configuración Personalizable

**Configuración Principal:**

**1. Intentos Máximos de No en Casa**
- Establecer cuántas visitas de "no en casa" antes de considerar hecho
- Típico: 2-3 intentos
- Afecta los cálculos de progreso
- Específico de la congregación

**2. Expiración de Enlace de Asignación**
- Configurar cuánto tiempo permanecen válidos los enlaces
- Predeterminado: 24 horas
- Personalizable por congregación
- Limpieza automática de enlaces expirados

**3. Opciones de Hogar**
- Crear clasificaciones de direcciones personalizadas
- Ejemplos: "Residencial", "Comercial", "Edificio Alto", "Idioma: Español"
- Marcar opciones como "contables" (afecta el % de completado)
- Establecer opción predeterminada para nuevas direcciones
- Reordenar con arrastrar y soltar
- Ver qué direcciones tienen cada opción

**4. Configuración Regional**
- Configuración de zona horaria
- Selección de país/región
- Afecta cálculos de fecha e informes

---

## Comunicación y Mensajería

### Sistema de Comunicación en la Aplicación

**Tipos de Mensajes:**

**1. Mensajes de Publicador**
- De publicadores a conductores/administradores
- Adjuntos a mapas específicos
- Retroalimentación específica del territorio
- Entrega en tiempo real

**2. Mensajes de Conductor**
- Comunicaciones administrativas
- Instrucciones específicas del mapa
- Mensajes de coordinación

**3. Mensajes de Administrador**
- Pueden ser fijados para visibilidad
- Seguimiento del estado de lectura
- Anuncios importantes
- Difusión a publicadores

**4. Mensajes Fijados**
- Permanecen visibles hasta ser desfijados
- Seguimiento de lectura por usuario
- Correo electrónico automático a usuarios relevantes
- Visualización prioritaria

**Notificaciones por Correo Electrónico:**

Resúmenes de correo electrónico automatizados enviados para:
- **Cada 30 minutos:** Mensajes no leídos a administradores
- **Cada 30 minutos:** Mensajes de administrador fijados a publicadores
- **Cada 1 hora:** Actualizaciones de notas a administradores
- **Mensualmente:** Informes de congregación con archivo Excel adjunto

**Plantillas de Correo Electrónico:**
- Plantillas HTML profesionales
- Diseño responsive para móviles
- Incluye enlaces relevantes
- Contenido personalizable

---

## Colaboración en Tiempo Real

### Sincronización de Datos en Vivo

**Características en Tiempo Real:**

**Suscripciones SSE:**
- Actualizaciones en vivo mediante Server-Sent Events (SSE)
- Reconexión automática al desconectarse
- Baja latencia (<100ms típico)

**Edición Concurrente:**
- Múltiples usuarios pueden trabajar simultáneamente
- Sin conflictos ni pérdida de datos
- Estrategia de última escritura gana
- Resolución basada en marca de tiempo

**Actualización Automática:**
- Los datos se actualizan inmediatamente cuando cambian
- No se necesita actualizar la página manualmente
- Indicadores visuales para actualizaciones
- Actualizaciones optimistas de la interfaz

**Detección de Visibilidad:**
- Se actualiza automáticamente cuando la pestaña se vuelve visible
- Ahorra ancho de banda cuando la pestaña está oculta
- Previene datos obsoletos

**Escenarios de Colaboración:**
- El administrador crea un territorio mientras el conductor ve la lista → Aparece instantáneamente
- El publicador marca unidades como completadas → El administrador ve la actualización de progreso en vivo
- Múltiples conductores gestionando diferentes territorios → Sin conflictos
- Actualizaciones de estadísticas en tiempo real mientras el trabajo progresa

---

## Aplicación Web Progresiva (PWA)

### Experiencia de Aplicación Nativa

**Capacidades de PWA:**

**Instalable:**
- Agregar a la pantalla de inicio (móvil)
- Instalar como aplicación de escritorio
- Aparece en el lanzador de aplicaciones
- Modo de pantalla completa
- Icono de aplicación personalizado

**Instalación en Escritorio:**
```
Visitar URL → Menú del navegador → "Instalar aplicación"
```

**Instalación en Móvil:**
```
Visitar URL → Compartir → "Agregar a pantalla de inicio"
```

**Soporte Sin Conexión:**
- Recursos estáticos almacenados en caché mediante service worker
- Continuar viendo datos en caché cuando está sin conexión
- Sincronización automática cuando se restaura la conexión
- Actualizaciones en segundo plano

**Beneficios de Rendimiento:**
- Tiempos de carga más rápidos (recursos en caché)
- Uso reducido de datos
- Funciona en conexiones lentas
- Animaciones suaves

**Características Nativas:**
- Notificaciones push (planificado)
- Sincronización en segundo plano
- Manejo de archivos nativo
- Integración con API de compartir

---

## Internacionalización

### Soporte Multiidioma

**Idiomas Soportados:**
1. **Inglés** (en) - Idioma principal
2. **Español** (es) - Español
3. **Indonesio** (id) - Bahasa Indonesia
4. **Japonés** (ja) - 日本語
5. **Coreano** (ko) - 한국어
6. **Malayo** (ms) - Bahasa Melayu
7. **Chino** (zh) - 中文

**Características de i18n:**
- Selector de idioma en la navegación
- Preferencia persistente (localStorage)
- Detección del idioma del navegador
- Traducción completa de la interfaz
- Cambio dinámico (sin recargar)
- Fácil agregar nuevos idiomas

**Agregar Nuevo Idioma:**
1. Crear archivo de traducción en `src/i18n/locales/[lang].json`
2. Copiar estructura de `en.json`
3. Traducir todas las cadenas
4. Registrar en `src/i18n/index.ts`

---

## Modo Oscuro

### Temas Conscientes del Sistema

**Opciones de Tema:**
- **Modo Claro:** Tema claro tradicional
- **Modo Oscuro:** Tema oscuro amigable para los ojos
- **Sistema:** Sigue la preferencia del SO

**Beneficios:**
- Reducción de la fatiga visual
- Mejor duración de batería (pantallas OLED)
- Cumplimiento de accesibilidad
- Experiencia de usuario moderna

**Implementación:**
- Propiedades CSS personalizadas
- Transiciones suaves
- Preferencia persistente
- Sin destello de tema incorrecto

---

## Informes y Análisis

### Estadísticas Completas

**Informes Mensuales de Congregación:**
- **Horario:** 1ro de cada mes a medianoche
- **Formato:** Libro de Excel (.xlsx)
- **Entrega:** Correo electrónico a todos los administradores

**Contenido del Informe:**

**1. Resumen de Congregación**
- Nombre y detalles de la congregación
- Período de informe
- Estadísticas generales

**2. Desglose por Territorio**
- Porcentaje de completado por territorio
- Número de mapas por territorio
- Distribución de direcciones
- Progreso a lo largo del tiempo

**3. Estadísticas de Direcciones**
- Total de direcciones por tipo
- Distribución de estados (hecho, no hecho, etc.)
- Desglose contable vs no contable
- Conteos de DNC e inválidos

**4. Asignaciones de Publicadores**
- Conteo de asignaciones activas
- Historial de asignaciones
- Cobertura de territorio

**5. Seguimiento de Progreso**
- Comparación mes a mes
- Tendencias e información
- Áreas que necesitan atención

**Características de Excel:**
- Formato profesional
- Gráficos y diagramas
- Datos listos para tabla dinámica
- Diseño listo para imprimir

**Panel en Tiempo Real:**
- Estadísticas en vivo en el panel de administración
- Seguimiento de completado de territorios
- Distribución de estados de direcciones
- Monitoreo de asignaciones activas
- Feed de actividad reciente

---

## Características de Seguridad

### Seguridad de Nivel Empresarial

**Métodos de Autenticación:**

**1. Correo Electrónico/Contraseña**
- Hash de contraseña con Bcrypt (costo: 10)
- Mínimo 6 caracteres
- Verificación de correo electrónico requerida
- Restablecimiento de contraseña por correo electrónico

**2. OAuth2 (Google)**
- Flujo PKCE para seguridad
- Creación automática de usuario
- Sin necesidad de gestión de contraseñas
- Manejo seguro de tokens

**3. Contraseña de Un Solo Uso (OTP)**
- Código opcional de 4 dígitos
- Validez de 180 segundos
- Capa adicional de seguridad
- Entrega por correo electrónico

**4. Autenticación Multifactor (MFA)**
- Soporte TOTP
- Seguridad mejorada para administradores
- Sesión de 1800 segundos
- Códigos de respaldo

**Control de Acceso:**

**Control de Acceso Basado en Roles (RBAC):**
- Permisos detallados
- Aislamiento a nivel de congregación
- Arquitectura multi-inquilino
- Seguro por defecto

**Acceso Basado en Enlaces:**
- Acceso anónimo temporal
- Autenticación basada en token
- Expiración automática
- Acceso revocable

**Mejores Prácticas de Seguridad:**
- Aplicación de HTTPS
- Configuración de CORS
- Limitación de velocidad
- Prevención de inyección SQL (consultas parametrizadas)
- Protección XSS
- Protección CSRF
- Validación de entrada
- Sanitización de salida

**Registro de Auditoría:**
- Registro de acciones de usuario
- Seguimiento de cambios
- Quién actualizó qué y cuándo
- Registro de direcciones IP
- Intentos de autenticación

**Seguimiento de Errores:**
- Integración con Sentry
- Monitoreo de errores en tiempo real
- Trazas de pila para depuración
- Seguimiento de versiones
- Monitoreo de rendimiento

---

## Mapeo Interactivo

### Integración de Leaflet + OpenStreetMap

**Características de Mapeo:**

**Características Base:**
- Panorámica y zoom interactivos
- Detalle a nivel de calle
- Opción de imágenes satelitales
- Marcadores personalizados para direcciones
- Visualización de límites de territorio
- Marcadores agrupados para áreas densas

**Navegación:**
- Obtener direcciones a cualquier dirección
- Integración con la aplicación de mapas del dispositivo
- Cálculo de distancia
- Planificación de ruta óptima
- Soporte de ubicación GPS

**Geolocalización:**
- Detectar ubicación actual
- Centrar mapa en el usuario
- Asignación basada en proximidad
- Distancia a territorios

**Marcadores Personalizados:**
- Codificación de colores basada en estado
- Iconos personalizados
- Ventanas emergentes de información
- Interacciones de clic
- Agrupación de marcadores

**Rendimiento:**
- Carga diferida de mosaicos
- Renderizado eficiente de marcadores
- Carga basada en viewport
- Optimizado para móviles

**Ventajas:**
- Sin límites de API
- Gratuito para usar
- Sin facturación requerida
- Datos de OpenStreetMap
- Posible caché de mosaicos sin conexión

---

## Sistema de Correo Electrónico

### Notificaciones Automáticas por Correo Electrónico

**Servicio de Correo Electrónico:** MailerSend API v1

**Tipos de Correo Electrónico:**

**1. Notificaciones de Mensajes**
- Horario: Cada 30 minutos
- Destinatarios: Administradores
- Contenido: Mensajes de publicadores no leídos
- Plantilla: HTML profesional

**2. Instrucciones de Administrador**
- Horario: Cada 30 minutos
- Destinatarios: Publicadores (usuarios no administradores)
- Contenido: Mensajes fijados del administrador
- Plantilla: Formato de instrucciones

**3. Actualizaciones de Notas**
- Horario: Cada 1 hora
- Destinatarios: Administradores
- Contenido: Cambios en notas de direcciones
- Plantilla: Formato de resumen de notas

**4. Informes Mensuales**
- Horario: 1ro del mes
- Destinatarios: Administradores
- Contenido: Informe estadístico con resúmenes de territorio generados por IA opcionales
- Adjunto: Libro de Excel
- Resumen de IA: Cuando está habilitado mediante la bandera de característica `enable-report-ai-summary` y `OPENAI_API_KEY` está configurado, cada hoja de territorio incluye un resumen generado por IA de tendencias clave y observaciones notables

**5. Correos Electrónicos del Sistema**
- Verificación de correo electrónico
- Restablecimiento de contraseña
- Alertas de cuenta
- Notificaciones de autenticación

**Características de Plantillas:**
- HTML responsive para móviles
- Diseño profesional
- CSS en línea para compatibilidad
- Alternativa de texto plano
- Enlaces para cancelar suscripción
- Píxeles de seguimiento (opcional)

---

## Trabajos en Segundo Plano

### Procesamiento Automatizado de Tareas

**Programador de Trabajos:** Basado en Cron con banderas de características de LaunchDarkly

**Trabajos Programados:**

| Trabajo | Horario | Propósito |
|---------|---------|-----------|
| Limpieza de Asignaciones | Cada 5 min | Eliminar asignaciones expiradas |
| Agregados de Territorio | Cada 10 min | Actualizar estadísticas de progreso |
| Procesamiento de Mensajes | Cada 30 min | Enviar notificaciones de mensajes |
| Instrucciones | Cada 30 min | Enviar mensajes de administrador |
| Actualizaciones de Notas | Cada 1 hora | Notificar cambios en notas |
| Informes Mensuales | 1ro del mes | Generar informes Excel (con resúmenes de IA opcionales) |
| Usuarios No Aprovisionados | Diario 01:00 UTC | Aplicar ciclo de vida de usuario (advertencias → deshabilitar → eliminar) |
| Usuarios Inactivos | Diario 01:30 UTC | Advertir y deshabilitar cuentas inactivas |

**Control de Banderas de Características:**
- Habilitar/deshabilitar trabajos sin despliegue
- Capacidades de despliegue gradual
- Pruebas A/B
- Interruptor de emergencia
- Control por congregación

**Monitoreo de Trabajos:**
- Registros de ejecución
- Seguimiento de éxito/fallo
- Métricas de duración
- Alertas de errores
- Lógica de reintento

---

## Interfaz de Usuario

### Diseño Moderno y Responsive

**Framework de UI:** Bootstrap 5.3.8 + Componentes Personalizados

**Principios de Diseño:**
- Enfoque móvil primero
- Interacciones amigables para táctil
- Cumplimiento de accesibilidad (WCAG 2.1)
- Lenguaje visual consistente
- Navegación intuitiva

**Elementos Clave de UI:**

**Navegación:**
- Barra de navegación responsive
- Selector de congregación
- Marcación rápida (botón de acción flotante)
- Navegación por migas de pan
- Enlaces rápidos

**Formularios:**
- Validación en línea
- Mensajería de errores
- Guardado automático de borradores
- Atajos de teclado
- Revelación progresiva

**Tablas:**
- Columnas ordenables
- Edición en línea
- Acciones masivas
- Paginación
- Búsqueda y filtro
- Capacidades de exportación

**Modales:**
- 24+ modales especializados
- Navegación por teclado
- Gestión de enfoque
- Soporte de apilamiento
- Optimizado para móviles

**Retroalimentación:**
- Notificaciones toast
- Indicadores de carga
- Barras de progreso
- Confirmaciones de éxito
- Alertas de error

**Puntos de Quiebre Responsive:**
- Móvil: <768px
- Tableta: 768px-1024px
- Escritorio: >1024px
- Escritorio grande: >1440px

---

## Rendimiento

### Optimizado para Velocidad

**Optimizaciones de Frontend:**
- División de código por ruta
- Carga diferida de componentes
- Optimización de imágenes
- Minificación de CSS
- Empaquetado de JavaScript
- Tree shaking
- Caché de service worker

**Compilador de React 19:**
- Memoización automática
- Sin optimización manual necesaria
- Tamaño de paquete más pequeño
- Renderizado más rápido

**Optimizaciones de Backend:**
- Indexación de base de datos (25+ índices)
- Optimización de consultas
- Agrupación de conexiones
- Caché de agregados
- Procesamiento de transacciones por lotes
- Compresión Gzip

**Estrategia de Caché:**
- Caché del navegador (recursos estáticos)
- Service worker (recursos sin conexión)
- Caché en borde de CDN
- Caché de consultas de base de datos

**Objetivos de Rendimiento:**
- **First Contentful Paint:** <1.5s
- **Time to Interactive:** <3s
- **Puntuación Lighthouse:** >90
- **Core Web Vitals:** Todos verdes

---

## API e Integración

### API RESTful + SSE

**Características de API:**
- Endpoints RESTful
- Cargas útiles JSON
- Autenticación JWT
- Soporte de paginación
- Filtrado y ordenación
- Limitación de velocidad
- Soporte de CORS

**SSE (Server-Sent Events):**
- Suscripciones en tiempo real
- Reconexión automática
- Baja latencia
- Actualizaciones basadas en eventos

**Opciones de Integración:**
- SDK de PocketBase (JavaScript/TypeScript)
- Solicitudes HTTP directas
- Cliente SSE
- Integración OAuth2
- Soporte de webhook (planificado)

---

## Gestión de Datos

### Capa de Datos Robusta

**Base de Datos:** SQLite mediante PocketBase

**Características:**
- Transacciones ACID
- Restricciones de clave foránea
- Eliminaciones en cascada
- Soporte de campos JSON
- Búsqueda de texto completo
- Copias de seguridad automáticas

**Importación/Exportación de Datos:**
- Exportación a Excel (informes)
- Exportación CSV (planificado)
- Exportación JSON mediante API
- Respaldo/restauración

**Integridad de Datos:**
- Integridad referencial
- Reglas de validación
- Restricciones únicas
- Restricciones de verificación
- IDs generados automáticamente

---

## Hoja de Ruta Futura

### Características Planificadas

**Corto Plazo (Q1-Q2 2025):**
- Notificaciones push
- Importación/exportación CSV
- Filtrado avanzado
- Informes personalizados
- Operaciones por lotes
- Diseños de impresión

**Mediano Plazo (Q3-Q4 2025):**
- Aplicaciones móviles (iOS/Android)
- Integraciones de webhook
- Campos personalizados
- Análisis avanzados
- Herramientas de colaboración en equipo
- Marketplace de integraciones

**Largo Plazo (2026+):**
- Información impulsada por IA
- Análisis predictivo
- Comandos de voz
- Navegación con realidad aumentada
- Registro de auditoría en blockchain
- Jerarquías multi-organización

---

## Documentación y Soporte

### Recursos Completos

**Documentación:**
- [Comenzar](getting-started.md)
- [Guía de Usuario](user-guide.md)
- [Arquitectura](architecture.md)
- [Guía de Despliegue](deployment.md)
- [Preguntas Frecuentes](faq.md)

**Canales de Soporte:**
- GitHub Issues
- Foros de la Comunidad
- Soporte por Correo Electrónico (servicio alojado)
- Sitio de Documentación
- Tutoriales en Video (planificado)

---

## Conclusión

Ministry Mapper proporciona una solución completa y moderna para la gestión digital de territorios con:

- ✅ Gestión integral de territorios y direcciones
- ✅ Colaboración en tiempo real
- ✅ Control de acceso seguro basado en roles
- ✅ Soporte multiidioma
- ✅ Capacidades de aplicación web progresiva
- ✅ Informes y notificaciones automatizados
- ✅ Mapeo interactivo
- ✅ Seguridad de nivel empresarial
- ✅ Código abierto y auto-hospedable
- ✅ Desarrollo y soporte activos

**¿Listo para comenzar?**
- [Guía de Inicio Rápido](getting-started.md)
- [Ver Demo](https://ministry-mapper.com)
- [Instrucciones de Auto-Hospedaje](deployment.md)

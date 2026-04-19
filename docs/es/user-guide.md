# Guía de Usuario de Ministry Mapper

## Introducción

Bienvenido a Ministry Mapper, una aplicación web moderna diseñada para ayudar a las congregaciones a gestionar eficientemente los territorios del servicio del campo. Esta guía le mostrará todo lo que necesita saber para comenzar y aprovechar al máximo la aplicación.

**¿Qué es Ministry Mapper?**

Ministry Mapper es un sistema de gestión de territorios digital que reemplaza los métodos tradicionales basados en papel. Permite a las congregaciones:

- Organizar y asignar territorios digitalmente
- Rastrear visitas del servicio del campo en tiempo real
- Coordinar actividades entre múltiples publicadores
- Acceder a territorios desde cualquier dispositivo con internet

**Beneficios Clave:**

- Ecológico - elimina el desperdicio de papel
- Actualizaciones en tiempo real a través de sincronización en la nube
- Funciona en cualquier dispositivo (escritorio, tableta, móvil)
- Mapas interactivos integrados para navegación fácil
- Control de acceso seguro basado en roles

## Comenzar

### Crear Su Cuenta

![Página de Registro](assets/screenshots/sign_up.png)

*Figura 1: Formulario de registro para crear una nueva cuenta de Ministry Mapper*

**Paso 1: Acceder a la Página de Registro**

![Iniciar Sesión](assets/screenshots/sign_in.png)

*Figura 2: Página de inicio de sesión con opción de Registrarse*

1. Visite la URL de Ministry Mapper de su congregación
2. Haga clic en el botón **"Registrarse"** en la página de inicio de sesión

**Paso 2: Elegir Método de Registro**

| Registro Tradicional | Google OAuth (Recomendado) |
|----------------------|---------------------------|
| Requiere correo electrónico, contraseña (6+ caracteres, 1 número, 1 mayúscula) y verificación de correo electrónico | Registro con un clic usando cuenta de Google |
| Verificación manual de correo electrónico requerida | Verificación automática de correo electrónico |
| Necesita recordar otra contraseña | Sin contraseña que gestionar |
| Aceptar Política de Privacidad y Términos | Seguridad mejorada a través de Google |

**Registro Tradicional:**
1. Complete: Nombre, Correo Electrónico, Contraseña, Confirmar Contraseña
2. Acepte la Política de Privacidad y Términos de Servicio
3. Haga clic en **"Crear Cuenta"**

**Registro con Google OAuth:**
1. Haga clic en el botón **"Iniciar sesión con Google"** debajo de "O continuar con"
2. Seleccione su cuenta de Google
3. Otorgue a Ministry Mapper acceso básico al perfil
4. Cuenta creada automáticamente

**Paso 3: Verificar Su Correo Electrónico**

![Correo de Verificación de Registro](assets/screenshots/sign_up_verification_email.png)

*Figura 3: Mensaje de verificación de correo electrónico enviado después de la creación de cuenta*

1. Revise su bandeja de entrada para un mensaje de verificación de Ministry Mapper
2. Haga clic en el enlace de verificación en el correo electrónico
3. Verá una confirmación de que su cuenta está verificada

**Paso 4: Esperar Acceso a la Congregación**

Después de la verificación:

1. Regrese a la página de inicio de sesión
2. Inicie sesión con su correo electrónico y contraseña
3. Si la Contraseña de Un Solo Uso (OTP) está habilitada, revise su correo electrónico para el código

![Página de Verificación OTP](assets/screenshots/otp_verification_page.png)

*Figura 4: Pantalla de verificación de Contraseña de Un Solo Uso (OTP)*

![Correo de Verificación OTP](assets/screenshots/otp_verification_email.png)

*Figura 5: Correo electrónico que contiene el código OTP para verificación de inicio de sesión*

4. **Importante**: No verá territorios aún - un administrador debe otorgarle acceso a su congregación primero
5. Contacte al siervo de territorio o administrador de su congregación para solicitar acceso

### Iniciar Sesión en Ministry Mapper

![Inicio de Sesión con Google OAuth](assets/screenshots/google_oauth_sign_in.png)

*Figura 6: Opción de inicio de sesión con Google OAuth para autenticación más rápida y segura*

Una vez que su cuenta esté verificada y se le haya otorgado acceso a la congregación:

**Inicio de Sesión Estándar:**
1. Navegue a la URL de Ministry Mapper de su congregación
2. Ingrese su dirección de correo electrónico y contraseña
3. Haga clic en **"Iniciar Sesión"**

**Inicio de Sesión con Google OAuth (Más Rápido):**
1. Haga clic en el botón **"Iniciar sesión con Google"**
2. Seleccione su cuenta de Google
3. Sesión iniciada automáticamente (sin contraseña necesaria)

**Paso 3: Completar Verificación OTP (Si Está Habilitada)**

Si su congregación ha habilitado la seguridad de Contraseña de Un Solo Uso:

1. Después de ingresar credenciales, verá la pantalla de verificación OTP
2. Revise su correo electrónico para el código de verificación
3. Ingrese el código de 6 dígitos del correo electrónico
4. Haga clic en **"Verificar"** o **"Enviar"**
5. El código expira en 5-10 minutos

**Paso 4: Acceder a Su Panel**

- **Publicadores**: No verá un panel - use los enlaces de asignación que se le envíen
- **Solo Lectura, Conductor, Administrador**: Verá su panel específico de rol

> **Consejo**: Manténgase conectado en dispositivos de confianza para mayor comodidad, pero siempre cierre sesión en computadoras compartidas.

### Entender los Roles de Usuario

Ministry Mapper usa un sistema de control de acceso de cuatro niveles. Su rol determina qué características puede acceder y qué acciones puede realizar.

#### Jerarquía de Roles

```
Administrador (Acceso Completo)
    ↓
Conductor (Gestionar Asignaciones)
    ↓
Solo Lectura (Solo Ver)
    ↓
Publicador (Acceso Solo por Enlace)
```

---

#### Publicador

**Método de Acceso**: A través de enlaces de asignación enviados por administradores o conductores

**Lo que los Publicadores Pueden Hacer:**

- Acceder a territorios a través de enlaces compartidos
- Ver mapas de territorio con mapeo interactivo
- Actualizar estado de dirección después de visitas
  - Marcar como: Hecho, No en Casa, No Visitar, Inválido
- Agregar notas de visita a direcciones
- Rastrear número de intentos de "no en casa"
- Ver detalles de dirección y secuencia

**Lo que los Publicadores No Pueden Hacer:**

- Sin acceso al panel
- No pueden ver todos los territorios de la congregación
- No pueden crear o gestionar territorios
- Los enlaces de asignación expiran después del tiempo establecido (predeterminado: 24 horas, configurable por el administrador de la congregación)

**Mejor Para**: Publicadores regulares haciendo servicio del campo

---

#### Solo Lectura

**Método de Acceso**: Inicio de sesión en panel con permisos de solo lectura

**Lo que los Usuarios de Solo Lectura Pueden Hacer:**

- Ver todos los territorios en la congregación
- Ver información completa de direcciones
- Ver progreso y estadísticas del territorio
- Acceder a mapas de territorio
- Ver mensajes de la congregación

**Lo que los Usuarios de Solo Lectura No Pueden Hacer:**

- No pueden modificar ningún dato de territorio o dirección
- No pueden crear o eliminar territorios
- No pueden gestionar asignaciones
- No pueden cambiar configuraciones de la congregación

**Mejor Para**: Superintendentes que necesitan visibilidad sin capacidades de edición

---

#### Conductor

**Método de Acceso**: Inicio de sesión en panel con permisos de conductor

**Las Capacidades del Conductor Incluyen:**

- **Todo lo que Solo Lectura puede hacer**, MÁS:
- Crear y gestionar asignaciones de territorio
- Generar enlaces de asignación para publicadores
- Ver todo el historial de asignaciones
- Acceder y publicar mensajes de la congregación
- Gestionar opciones de la congregación (tipos de estado de hogar)
- Ver estado de completado del territorio

**Lo que los Conductores No Pueden Hacer:**

- No pueden crear o eliminar territorios
- No pueden editar detalles de dirección (direcciones, unidades, pisos)
- No pueden gestionar roles o permisos de usuario
- No pueden modificar configuraciones principales de la congregación

**Mejor Para**: Coordinadores del servicio del campo y superintendentes de grupo

---

#### Administrador (Siervo de Territorio)

**Método de Acceso**: Inicio de sesión en panel con permisos administrativos completos

**El Administrador Tiene Control Completo:**

- **Todo lo que los Conductores pueden hacer**, MÁS:
- Crear, editar y eliminar territorios
- Agregar, modificar y eliminar direcciones
- Gestionar edificios y unidades
- Configurar ajustes de la congregación
  - Establecer intentos máximos de "no en casa"
  - Configurar tiempos de expiración de enlaces
  - Configurar opciones de tipo de hogar
- Invitar y gestionar usuarios
- Asignar roles y permisos de usuario
- Restablecer territorios y direcciones
- Gestionar coordenadas de geolocalización

**Mejor Para**: Siervos de territorio y aquellos que gestionan el sistema de territorios de la congregación

---

> **Nota**: Contacte al administrador de su congregación si necesita cambiar su rol o si no está seguro de qué rol tiene actualmente.

## Características Principales

### Descripción General del Panel

![Panel de Administrador](assets/screenshots/admin_dashboard.png)

*Figura 6: Panel de administrador mostrando selector de territorio y controles principales*

La interfaz del panel varía según su rol:

#### Para Publicadores

Los publicadores **no tienen acceso al panel**. En su lugar:

- Reciben enlaces de asignación por correo electrónico o mensaje
- Hacen clic en el enlace para acceder a su territorio asignado
- Trabajan directamente dentro de la vista del territorio
- Los enlaces expiran automáticamente después del tiempo configurado (predeterminado: 24 horas)

#### Para Conductores y Administradores

El panel proporciona una descripción general completa:

**1. Selector de Territorio** (Menú Desplegable Superior)

- Elegir qué territorio ver o gestionar
- Muestra código y descripción del territorio
- Navegación rápida entre territorios

**2. Botones de Acción Principal**

- **Asignaciones**: Ver y gestionar enlaces de asignación
- **Mensajes**: Publicar y ver mensajes de la congregación
- **Configuración**: Acceder a configuración de la congregación (Solo Administradores)
- **Usuarios**: Gestionar roles e invitaciones de usuarios (Solo Administradores)

**3. Panel de Información del Territorio**

- Código y descripción del territorio
- Barra de progreso mostrando porcentaje de completado
- Marca de tiempo de última actualización
- Total de unidades vs. unidades completadas

**4. Opciones de Vista del Territorio**

- **Vista de Mapa**: Visualización de mapa interactivo
- **Vista de Lista**: Visualización tabular de todas las direcciones

**5. Menú de Marcación Rápida** (Botón de Acción Flotante)

![Marcación Rápida Página Admin](assets/screenshots/speeddial_admin_page.png)

*Figura 6a: Botón de acción flotante de marcación rápida proporcionando acceso rápido a acciones comunes*

El botón **+** (esquina inferior derecha) proporciona acceso rápido a:
- **Modo Vista de Mapa**: Alternar vista de mapa a pantalla completa
- **Enlace Rápido**: Crear rápidamente enlaces de asignación (Conductores y Administradores)
- Acciones contextuales basadas en la vista actual

---

### Ver Territorios

#### Vista de Territorio del Publicador

![Pantalla de Asignación del Publicador](assets/screenshots/publisher_assignment_screen.png)

*Figura 7: Vista del publicador del territorio asignado con mapa y lista de direcciones*

**Acceder a Su Asignación:**

1. Haga clic en el enlace de asignación enviado por su administrador/conductor
2. El territorio se carga automáticamente con:
   - Mapa interactivo de Google mostrando todas las direcciones
   - Marcadores clicables para cada ubicación
   - Lista de direcciones/unidades para visitar
   - Porcentaje de progreso actual

**Información del Territorio Mostrada:**

- **Código del Territorio**: Identificador (ej., "T-001")
- **Descripción**: Nombre o área del territorio
- **Barra de Progreso**: Estado visual de completado
- **Mapa**: Mapa interactivo con marcadores de direcciones
- **Lista de Direcciones**: Todas las direcciones con estado actual

#### Vista de Territorio del Administrador/Conductor

![Panel del Conductor](assets/screenshots/conductor_dashboard.png)

*Figura 8: Panel del conductor con selector de territorio y opciones de gestión*

**Ver un Territorio:**

1. Inicie sesión en su panel
2. Seleccione un territorio del menú desplegable
3. Vea información detallada:
   - Código y descripción del territorio
   - Estadísticas de completado (ej., "15/20 completados - 75%")
   - Mapa interactivo con todas las ubicaciones
   - Lista completa de direcciones/unidades con detalles
   - Botones de gestión (Editar, Eliminar, Restablecer)

**Opciones de Gestión:**

- **Editar Territorio**: Cambiar nombre, código o descripción
- **Eliminar Territorio**: Eliminar territorio completo
- **Restablecer Territorio**: Limpiar todos los estados de direcciones
- **Agregar Dirección**: Crear nueva dirección en el territorio

### Trabajar Con Direcciones

#### Entender la Información de Direcciones

![Detalles de Estado de Dirección](assets/screenshots/address_status_details.png)

*Figura 9: Tarjeta de dirección mostrando todos los detalles de dirección y estado actual*

Cada dirección o unidad en Ministry Mapper muestra información completa:

**Información Básica:**

- **Dirección/Número de Unidad**: Identificador de ubicación (ej., "#05-123")
- **Piso**: Nivel del edificio (para propiedades de varios pisos)
- **Tipo**: Clasificación del hogar basada en opciones de la congregación
  - Ejemplos: Chino, Inglés, Tamil, Español
  - Se pueden seleccionar múltiples tipos si está configurado
- **Secuencia**: Número de orden de visita

**Información de Estado:**

- **Estado**: Estado actual de visita (ver tipos de estado abajo)
- **Conteo de No en Casa**: Número de intentos de visita sin respuesta
- **Fecha de No Visitar**: Cuándo se marcó como DNC (si aplica)

**Seguimiento de Actividad:**

- **Notas**: Información importante sobre visitas o residente
- **Última Actualización**: Fecha y hora de la actualización más reciente
- **Actualizado Por**: Nombre de usuario de la persona que hizo el último cambio

---

#### Tipos de Estado de Dirección

Ministry Mapper usa cinco tipos de estado estándar:

| Estado | Color | Descripción | Uso |
| ------ | ----- | ----------- | --- |
| **No Hecho** | Blanco/Predeterminado | Aún no visitado | Estado inicial para todas las direcciones |
| **Hecho** | Verde | Contactado con éxito | El residente estaba en casa y fue contactado |
| **No en Casa** | Amarillo/Naranja | Nadie respondió | Rastrear hasta intentos máximos (configurable) |
| **No Visitar** | Rojo | Solicitó no ser visitado | El residente solicitó no más contacto |
| **Inválido** | Gris | Inaccesible | La dirección no existe o no puede ser visitada |

> **Consejo**: Una vez que "No en Casa" alcanza los intentos máximos (establecido por el administrador), la dirección se marca automáticamente como completada en los cálculos de progreso.

---

#### Actualizar Estado de Dirección

![Estado de Dirección No en Casa](assets/screenshots/address_not_home_status.png)

*Figura 10: Modal de actualización de estado mostrando todos los campos y opciones*

**Proceso Paso a Paso:**

**1. Ubicar la Dirección**

- Desplácese por la lista de direcciones
- O haga clic en un marcador en el mapa
- Las direcciones están agrupadas por piso para edificios de varios pisos

**2. Abrir el Modal de Actualización**

- Haga clic en la tarjeta de dirección/unidad
- El modal de actualización se abrirá con la información actual

**3. Actualizar Estado** (Requerido)

Seleccione el estado apropiado:

**Hecho** - Cuando se contactó con éxito

- Seleccione esto cuando alguien respondió
- Ocurrió una conversación o se dejó literatura
- El conteo de "No en Casa" se restablece

**No en Casa** - Cuando nadie respondió

- Incrementa automáticamente el conteo de "No en Casa"
- El sistema rastrea el número de intentos
- Después de alcanzar los intentos máximos, se trata como completo

**No Visitar** - Cuando se solicitó no visitar

![Estado de Dirección No Visitar](assets/screenshots/address_do_not_call_status.png)

*Figura 11: Actualización de estado No Visitar con selección de fecha*

- Seleccione este estado
- Opcionalmente establezca la fecha de DNC (predeterminado: hoy)
- Agregue notas explicando la razón si es apropiado
- **Importante**: Respete siempre los deseos del residente

**Inválido** - Cuando la dirección es inaccesible

- La dirección no existe
- En construcción
- Permanentemente cerrado
- No se puede acceder

**4. Actualizar Tipo de Hogar** (Si Está Disponible)

Si su congregación ha configurado tipos de hogar:

- Seleccione uno o múltiples tipos
- Ejemplos: Preferencias de idioma, circunstancias especiales
- Limpie tipos existentes si es necesario

**5. Agregar o Actualizar Notas** (Opcional pero Recomendado)

**Mejores prácticas:**

- Enfóquese en detalles de la propiedad, no en individuos
- Registre instrucciones de acceso y horarios
- Sea conciso y respetuoso
- Nunca incluya información personal sobre residentes
- Nunca incluya información sensible

**Buenos Ejemplos:**

- "Propiedad con portón - llame a la garita primero"
- "Mejor hora: Fines de semana después de las 2 PM"
- "Entrada lateral accesible por el camino de entrada"

**Malos Ejemplos:**

- Detalles personales sobre residentes
- Información médica o financiera
- Nombres o descripciones de individuos

**6. Ajustar Conteo de No en Casa** (Si Es Necesario)

El sistema rastrea automáticamente los intentos de no en casa, pero puede ajustar manualmente si es necesario.

**7. Establecer Fecha de No Visitar** (Solo Estado DNC)

Al marcar como No Visitar:

- El sistema predetermina la fecha de hoy
- Ajuste si es necesario para solicitudes específicas de DNC
- La fecha ayuda a rastrear cuándo potencialmente revisitar

**8. Actualizar Geolocalización** (Solo Administradores)

Para territorios de un solo piso:

- Haga clic en el botón "Actualizar Geolocalización"
- Use el mapa para establecer la ubicación precisa
- Ayuda con la navegación y precisión del mapeo

**9. Guardar Cambios**

- Revise todas las actualizaciones
- Haga clic en el botón **"Guardar"**
- Los cambios se sincronizan inmediatamente para todos los usuarios
- Un mensaje de éxito confirma la actualización

**10. Eliminar Propiedad** (Solo Administradores - Un Solo Piso)

Para propiedades privadas que necesitan ser eliminadas:

- Haga clic en el botón "Eliminar Propiedad" en la parte inferior
- Confirme la eliminación
- **Advertencia**: Esta acción no se puede deshacer

---

### Usar la Función de Mapa

![Direcciones del Mapa](assets/screenshots/map_directions.png)

*Figura 12: Vista de mapa interactivo mostrando marcadores y controles de navegación*

Ministry Mapper integra mapeo interactivo para navegación intuitiva del territorio.

#### Características del Mapa

**Marcadores Interactivos:**

- Cada dirección está marcada en el mapa
- Marcador rojo indica destino
- Marcador azul parpadeante indica ubicación actual
- Haga clic en cualquier marcador para ver/editar esa dirección

**Controles del Mapa:**

- **Zoom**: Botones + y - o gesto de pellizco
- **Panorámica**: Haga clic y arrastre para moverse
- **Vista Satelital**: Alternar entre mapa e imágenes satelitales
- **Pantalla Completa**: Expandir mapa a pantalla completa (o use Marcación Rápida → Modo Vista de Mapa)
- **Centrar en Territorio**: Restablecer vista para mostrar todas las direcciones

**Modo de Vista de Mapa a Pantalla Completa** (Administradores y Conductores):

![Modo Vista de Mapa Admin](assets/screenshots/admin_map_view_mode.png)

*Figura 12a: Modo de vista de mapa a pantalla completa accedido mediante el menú de marcación rápida*

Acceda mediante Marcación Rápida (+) → icono de Vista de Mapa. Ideal para:
- Planificación de rutas antes del servicio del campo
- Análisis de cobertura del territorio
- Presentaciones en reuniones
- Identificar grupos de direcciones

#### Consejos de Navegación

1. **Antes de Salir de Casa:**

   - Vea el mapa para planificar su ruta
   - Identifique grupos de direcciones
   - Note cualquier requisito especial de acceso de las notas

2. **En el Campo:**

   - Use el mapa para navegar entre direcciones
   - Siga los números de secuencia para enrutamiento óptimo
   - Toque los marcadores para actualizar rápidamente el estado después de cada visita

3. **Usando con GPS del Teléfono:**
   - Habilite los servicios de ubicación
   - El mapa muestra su posición actual
   - Navegue directamente a la siguiente dirección
   - Funciona sin conexión si los mosaicos del mapa están en caché (limitado)

---

### Asignaciones de Territorio (Conductores y Administradores)

Ministry Mapper usa un sistema de asignación basado en enlaces. Los Conductores y Administradores crean enlaces compartibles que los publicadores usan para acceder a los territorios.

#### ¿Por Qué Asignaciones Basadas en Enlaces?

- **Distribución Simple**: Envíe enlaces por correo electrónico, mensaje o texto
- **Expiración Automática**: Los enlaces expiran después del tiempo establecido (predeterminado: 24 horas)
- **Sin Cuenta Requerida**: Los publicadores trabajan directamente a través del enlace
- **Seguridad**: Los enlaces expirados no pueden accederse
- **Seguimiento**: Vea quién accedió a qué y cuándo

#### Crear una Asignación

**Dos Formas de Crear Asignaciones:**

![Enlace Rápido Admin Conductor](assets/screenshots/admin_conductor_quicklink.png)

*Figura 13a: Interfaz de creación de enlace rápido accedida mediante el menú de marcación rápida*

| Método | Acceso | Mejor Para |
|--------|--------|------------|
| **Enlace Rápido** | Marcación Rápida (+) → Enlace Rápido | Creación rápida para territorio actual |
| **Estándar** | Botón Asignaciones → Crear Nueva | Opciones completas y personalización |

**Paso 1: Acceder a Creación de Asignación**

- **Enlace Rápido**: Haga clic en Marcación Rápida (+) → icono de Enlace Rápido (pre-llena el territorio actual)
- **Estándar**: Haga clic en **"Asignaciones"** → **"Crear Nueva Asignación"** o **"+"**

**Paso 2: Llenar Formulario de Asignación**

| Campo | Descripción | Requerido |
|-------|-------------|-----------|
| **Tipo** | Asignación Normal o Ficha Personal | Sí |
| **Territorio** | Seleccionar de los territorios de la congregación | Sí |
| **Nombre del Publicador** | Para seguimiento (el enlace funciona sin nombre) | Opcional |
| **Expiración del Enlace** | Horas hasta que el enlace expire (predeterminado: 24) | Sí |

**Paso 3: Generar y Compartir**

1. Haga clic en **"Crear Asignación"**
2. El sistema genera un enlace único
3. Comparta el enlace con el publicador mediante:
   - Correo electrónico
   - Mensaje de texto
   - Aplicación de mensajería instantánea
   - Cualquier método de comunicación

**Ejemplo de Enlace de Asignación:**

```
https://su-ministry-mapper.com/map/abc123xyz456
```

#### Creación de Asignación Específica del Mapa

Cree asignaciones directamente desde la vista del mapa sin navegar al modal central de asignaciones.

**Acceso:**

1. Navegue al territorio que desea asignar
2. Haga clic en:
   - Botón **"Asignar"** - crear una asignación de territorio normal
   - Botón **"Personal"** - crear una asignación de ficha personal

**Asignación de Territorio Normal:**

![Asignar Publicador Mapa](assets/screenshots/assigning_publisher_map.png)

*Figura 13b: Formulario de creación de asignación normal específico del mapa*

Haga clic en **"Asignar"** para crear una asignación de territorio normal. El formulario muestra:

- Información del territorio en el encabezado
- Campo **Nombre del Publicador** - ingrese el/los nombre(s) del/los publicador(es) asignado(s)
- Botones **Cancelar** y **Confirmar**

**Asignación de Ficha Personal:**

![Asignación Personal Publicador Mapa](assets/screenshots/personal_assign_publisher_map.png)

*Figura 13c: Formulario de creación de ficha personal específico del mapa con calendario*

Haga clic en **"Personal"** para crear una asignación de ficha personal. El formulario incluye:

- Información del territorio en el encabezado
- **Selector de calendario** - seleccione la fecha de asignación
- Campo **Nombre del Publicador** - ingrese el/los nombre(s) del/los publicador(es) asignado(s)
- Botones **Cancelar** y **Confirmar**

**Beneficios:**

- Creación rápida de asignaciones mientras ve el territorio
- No necesita cambiar al modal de asignaciones
- Contexto inmediato del territorio que se está asignando

#### Gestionar Asignaciones

![Gestión de Asignaciones](assets/screenshots/assignment_management.png)

*Figura 14: Modal de gestión de asignaciones mostrando todas las asignaciones activas en todos los territorios*

La interfaz de Gestión de Asignaciones permite a conductores y administradores ver y gestionar todas las asignaciones existentes en la congregación.

**Ver Todas las Asignaciones:**

1. Haga clic en el botón **"Asignaciones"** desde el panel
2. Se abre el modal de Asignaciones mostrando todos los enlaces de asignación activos
3. Vea asignaciones para todos los territorios en una lista centralizada

**Información de Asignación Mostrada:**

Para cada asignación, puede ver:

- **Código del Territorio y Ubicación**: Identificador del territorio con descripción de ubicación (ej., "187A, Marsiling Road (M01)")
- **Tipo de Asignación**: "Asignar" para asignaciones normales
- **Nombre del Publicador**: Nombre de la persona asignada (ej., Jon, Erli, Pety)
- **Fecha/Hora de Creación**: Cuándo se creó la asignación (ej., "22 dic 2025, 9:38 PM")
- **Fecha/Hora de Expiración**: Cuándo expira el enlace de asignación (ej., "23 dic 2025, 9:38 AM" o "11:38 PM")
- **Botón Eliminar**: Icono de papelera para eliminar asignaciones individuales

**Eliminar una Asignación:**

1. Ubique la asignación en la lista
2. Haga clic en el botón de **icono de papelera** en el lado derecho de la asignación
3. Confirme la eliminación si se le solicita
4. El enlace de asignación se vuelve inaccesible inmediatamente para el publicador

**Cuándo Eliminar:**

- Territorio devuelto temprano por el publicador
- Se creó o envió un enlace incorrecto
- El publicador ya no necesita acceso
- La asignación necesita reasignarse a otra persona
- Preocupación de seguridad o compromiso

**Cerrar el Modal de Asignaciones:**

- Haga clic en el botón **"Cancelar"** en la parte inferior para cerrar el modal y regresar al panel

#### Gestión de Asignaciones Específicas del Mapa

Vea y gestione asignaciones para territorios individuales directamente desde la vista del mapa.

**Acceso:**

1. Navegue a un territorio
2. Haga clic en:
   - **"Enlaces de Asignación"** - para asignaciones de territorio normales
   - **"Enlaces Personales"** - para asignaciones de ficha personal

![Asignaciones Normales del Mapa](assets/screenshots/map_assign_links.png)

*Figura 14a: Modal de Enlaces de Asignación para un territorio específico*

![Asignaciones Personales del Mapa](assets/screenshots/map_personal_links.png)

*Figura 14b: Modal de Enlaces Personales para un territorio específico*

**Información Mostrada:**

- Nombre del publicador
- Fecha/hora de creación y expiración
- Botón eliminar para eliminar asignaciones

**Cuándo Usar:**

- Verificar quién tiene actualmente un territorio asignado
- Eliminar asignaciones mientras ve el territorio
- Gestionar asignaciones normales y personales por separado

#### Mejores Prácticas para Asignaciones

**Antes de Crear:**

- Verifique que el territorio esté listo (direcciones actualizadas, instrucciones claras)
- Verifique la configuración de la congregación para el tiempo de expiración predeterminado
- Planifique la duración de expiración apropiada para el tamaño del territorio

**Al Compartir:**

- Incluya instrucciones en su mensaje
- Recuerde al publicador el tiempo de expiración
- Proporcione su contacto para preguntas
- Envíe durante horas razonables

**Monitoreo:**

- Verificación regular de asignaciones expiradas
- Limpie asignaciones antiguas periódicamente
- Haga seguimiento si el territorio no se devuelve
- Rastree tasas de completado

---

### Mensajes e Instrucciones

![Mensajes Admin Conductor](assets/screenshots/admin_conductor_messages.png)

*Figura 15: Modal de mensajes mostrando mensajes publicados con opción de fijar*

Los Administradores y Conductores pueden publicar mensajes visibles para grupos de usuarios específicos.

#### Tipos de Mensajes

**Mensajes de Publicador:**

- Visibles para todos los publicadores con enlaces de asignación
- Instrucciones para el servicio del campo
- Orientación específica del territorio
- Anuncios generales

**Mensajes de Conductor:**

- Visibles para Conductores y Administradores
- Información de coordinación
- Notas administrativas
- Información de planificación

**Mensajes de Administrador:**

- Visibles solo para Administradores
- Notas de administración del sistema
- Actualizaciones críticas
- Recordatorios de gestión

#### Publicar un Mensaje

1. Haga clic en el botón **"Mensajes"**
2. Haga clic en **"Nuevo Mensaje"** o **"+"**
3. Escriba su mensaje
4. Seleccione el tipo de mensaje (Publicador/Conductor/Administrador)
5. Opcionalmente **fije** mensajes importantes arriba
6. Haga clic en **"Publicar"**

#### Características de Mensajes

**Fijar:**

- Fije mensajes importantes para mantenerlos arriba
- Solo un mensaje fijado por tipo
- Desfije cuando ya no sea crítico

**Editar:**

- Edite mensajes después de publicar
- Las actualizaciones son inmediatas para todos los que ven

**Eliminar:**

- Elimine mensajes desactualizados
- Limpie después de que pasen los eventos

**Estado de Lectura:**

- Vea quién ha leído los mensajes (vista de administrador)
- Rastree el reconocimiento de actualizaciones importantes

---

### Sincronización de Datos en Tiempo Real

Ministry Mapper usa suscripciones en tiempo real de PocketBase para actualizaciones instantáneas:

#### Cómo Funciona

**Sincronización Automática:**

- Los cambios se sincronizan inmediatamente en todos los dispositivos conectados
- No se necesita actualización manual
- Las actualizaciones aparecen instantáneamente para todos los usuarios que ven el mismo territorio

**Qué Se Sincroniza:**

- Cambios de estado de dirección
- Nuevas notas y actualizaciones
- Progreso del territorio
- Nuevos mensajes
- Cambios de asignación

**Manejo de Conexión:**

- El sistema detecta automáticamente la pérdida de conexión
- Se reconecta cuando se restaura internet
- Muestra indicador de estado de conexión
- Pone en cola las actualizaciones si está sin conexión (limitado)

**Rendimiento:**

- Actualiza solo cuando el territorio/página está abierto
- Limpieza automática cuando se cierra la página
- Uso mínimo de ancho de banda
- Optimizado para datos móviles

> **Nota**: Para que las actualizaciones en tiempo real funcionen, mantenga la pestaña de su navegador activa mientras trabaja en un territorio.

---

### Gestión de Usuarios (Solo Administradores)

![Lista de Gestión de Usuarios](assets/screenshots/manage_users_list.png)

*Figura 16: Panel de gestión de usuarios mostrando lista de usuarios con roles*

Los Administradores tienen control total sobre las cuentas de usuario y permisos dentro de su congregación.

#### Ver Usuarios

1. Haga clic en su **icono de perfil** o **menú de usuario**
2. Seleccione **"Gestión de Usuarios"** o **"Usuarios"**
3. Vea la lista completa de usuarios de la congregación mostrando:
   - Nombre de usuario
   - Dirección de correo electrónico
   - Estado de verificación (verificado / no verificado)
   - Insignia de rol actual
   - Última actividad

#### Invitar Nuevos Usuarios

![Invitar Usuarios](assets/screenshots/invite_users.png)

*Figura 17: Diálogo de invitación de usuarios para agregar nuevos miembros a la congregación*

**Paso 1: Abrir Diálogo de Invitación**

1. En Gestión de Usuarios, haga clic en **"Invitar Usuario"** o **"+"**
2. Se abre el modal de invitar usuario

**Paso 2: Ingresar Información del Usuario**

- **Dirección de Correo Electrónico**: Correo del usuario (debe ser válido)
- **Asignación de Rol**: Seleccione uno de:
  - Publicador
  - Solo Lectura
  - Conductor
  - Administrador

**Paso 3: Enviar Invitación**

1. Haga clic en **"Enviar Invitación"**
2. El sistema envía un correo de invitación al usuario
3. El correo contiene:
   - Enlace para crear cuenta
   - Información de la congregación
   - Detalles de asignación de rol
   - Instrucciones para comenzar

**Paso 4: El Usuario Completa el Registro**

- El usuario recibe el correo
- Hace clic en el enlace para registrarse
- Crea cuenta con contraseña
- Verifica la dirección de correo electrónico
- Se agrega automáticamente a la congregación con el rol asignado

#### Cambiar Roles de Usuario

![Detalles de Gestión de Usuarios](assets/screenshots/manage_users_details.png)

*Figura 18: Interfaz de detalles de usuario y gestión de roles*

1. Ubique al usuario en la lista de usuarios
2. Haga clic en el usuario o botón **"Editar"**
3. Seleccione el nuevo rol del menú desplegable:
   - **Publicador**: Acceso básico al territorio mediante enlaces
   - **Solo Lectura**: Acceso al panel solo para ver
   - **Conductor**: Puede crear asignaciones y gestionar mensajes
   - **Administrador**: Control total
4. Haga clic en **"Guardar"** o **"Actualizar"**
5. Los cambios surten efecto inmediatamente

> **Importante**: Los usuarios deben cerrar sesión y volver a iniciar sesión para ver sus nuevos permisos reflejados.

#### Eliminar Acceso de Usuario

**Eliminación Temporal:**

1. Cambie el rol del usuario a **"Sin Acceso"** o **"Eliminar Acceso"**
2. El usuario pierde todos los permisos
3. La cuenta permanece pero no puede acceder a los datos de la congregación

**Eliminación Permanente:**

1. Haga clic en el botón **"Eliminar"** para el usuario
2. Confirme la eliminación
3. El usuario se elimina completamente de la congregación
4. El usuario puede ser re-invitado si es necesario

#### Estado de Verificación del Usuario

**Usuarios Verificados:**

- Dirección de correo electrónico confirmada
- Acceso completo a los permisos asignados
- Puede iniciar sesión normalmente

**Usuarios No Verificados:**

- Correo electrónico aún no confirmado
- Acceso limitado o sin acceso
- Necesita revisar el correo electrónico y hacer clic en el enlace de verificación

**Para Reenviar Verificación:**

- Algunos sistemas permiten reenviar el correo de verificación
- O pida al usuario que use la función "Olvidé mi Contraseña"

---

### Configuración de Congregación (Solo Administradores)

![Configuración de Congregación](assets/screenshots/congregation_settings.png)

*Figura 19: Página de configuración de congregación con todas las opciones de configuración*

Configure cómo funciona Ministry Mapper para su congregación.

#### Acceder a Configuración

1. Haga clic en el botón **"Configuración"** o icono de engranaje
2. Vea el panel de configuración de la congregación

#### Configuraciones Clave

**1. Intentos Máximos de "No en Casa"**

- Predeterminado: 1
- Rango: 1-4 intentos
- Cuando se alcanza, la dirección se considera completa para el cálculo de progreso
- Afecta cuándo los territorios se muestran como terminados

**Ejemplo:** Si se establece en 3:

- Primer "No en Casa": Conteo = 1
- Segundo "No en Casa": Conteo = 2
- Tercer "No en Casa": Conteo = 3, se marca como completo

**2. Expiración de Enlace de Asignación (Horas)**

- Predeterminado: 24 horas
- Rango: 1-168 horas (1 semana)
- Cuánto tiempo permanecen activos los enlaces de asignación
- Se aplica a enlaces recién creados

**3. Origen/Ubicación de la Congregación**

- Establezca la ubicación de su congregación
- Usado para centrado del mapa y direcciones
- Puede ser nombre de ciudad o coordenadas
- Ayuda con la planificación de rutas

**4. OTP (Contraseña de Un Solo Uso)**

- Habilitar/deshabilitar OTP por correo electrónico para inicio de sesión
- Agrega capa de seguridad adicional
- Los usuarios reciben código por correo electrónico al iniciar sesión
- Recomendado para datos sensibles de la congregación

#### Opciones de Congregación (Tipos de Hogar)

![Configuración de Opciones de Hogar](assets/screenshots/household_options_settings.png)

*Figura 20: Gestión de opciones de congregación mostrando configuración de tipos de hogar*

Configure tipos de clasificación de hogar personalizados para su territorio.

**¿Qué Son las Opciones de Congregación?**

- Categorías personalizadas para clasificar hogares
- Ejemplos: Grupos de idioma (Chino, Inglés, Tamil)
- Puede representar cualquier sistema de clasificación que use su congregación
- Se pueden asignar múltiples tipos a un solo hogar si está configurado

**Gestionar Opciones:**

1. **Ver Opciones**

   - En Configuración, encuentre la sección "Opciones de Congregación"
   - Vea la lista de todos los tipos configurados

2. **Agregar Nueva Opción**

   - Haga clic en "Agregar Opción" o "+"
   - Complete:
     - **Código**: Identificador corto (ej., "CHI", "ESP")
     - **Descripción**: Nombre completo (ej., "Chino", "Español")
     - **Es Contable**: Marque si debe contar hacia el progreso del territorio
     - **Es Predeterminado**: Marque si debe ser la selección predeterminada
     - **Secuencia**: Número de orden de visualización
   - Haga clic en "Guardar"

3. **Editar Opción**

   - Haga clic en la opción existente
   - Modifique los campos
   - Guarde los cambios

4. **Eliminar Opción**
   - Haga clic en el botón eliminar para la opción
   - Confirme la eliminación
   - **Advertencia**: Afecta todas las direcciones que usan este tipo

**Indicadores de Opción:**

**Es Contable:**
- Controla si las direcciones con este tipo cuentan hacia el progreso del territorio
- **Marcado**: Incluido en el cálculo de progreso (ej., Chino, Inglés, Tamil)
- **No Marcado**: Excluido del progreso (ej., Comercial, En Construcción)
- Ejemplo: 100 direcciones totales, 10 marcadas "Comercial" (no contable) = progreso basado en 90 direcciones solamente

**Es Predeterminado:**
- Auto-selecciona este tipo al crear nuevas direcciones
- Solo una opción debe ser predeterminada
- Use para su tipo de hogar más común

**Secuencia:**
- Controla el orden de visualización en menús desplegables
- Los números más bajos aparecen primero (1, 2, 3...)
- **Gestionado por arrastrar y soltar** en configuración - simplemente arrastre opciones para reordenar
- Afecta el orden en menús desplegables de hogar de dirección
- Consejo: Ordene de más común a menos común

**Configuración de Selección Múltiple:**

- Habilite si los hogares pueden tener múltiples tipos
- Ejemplo: El hogar habla tanto chino como inglés
- Cuando está deshabilitado, solo un tipo por hogar

#### Configuración de Mapa

![Configuraciones de Mapa](assets/screenshots/map_configurations.png)

*Figura 34: Opciones avanzadas de configuración de mapa para administradores*

Configure cómo se muestran y comportan los mapas en su congregación. Los administradores tienen acceso a potentes funciones de gestión de mapas para mantenimiento y organización del territorio.

**Para Acceder a la Configuración del Mapa:**

1. Vaya a **Configuración** (Solo Administradores)
2. Seleccione un territorio del menú desplegable
3. Abra la vista del mapa para el territorio seleccionado
4. Acceda al menú de configuración del mapa (típicamente mediante un icono de configuración o menú)

**Funciones de Configuración de Mapa Disponibles:**

**1. Cambiar Ubicación**
   - Reubique un marcador de mapa a una dirección diferente
   - Actualiza la ubicación geográfica de una unidad de territorio
   - Útil cuando las direcciones han cambiado o la ubicación inicial era incorrecta
   - Simplemente seleccione la nueva ubicación en el mapa

**2. Cambiar Territorio**
   - Mueva una dirección o unidad a un territorio diferente
   - Ayuda a reorganizar los límites del territorio
   - Útil para equilibrar tamaños de territorio
   - Mantiene todos los datos e historial de la dirección durante la transferencia

**3. Cambiar Secuencia**
   - Modifique el número de orden de visita para una dirección
   - Optimiza la ruta para eficiencia del servicio del campo
   - Los números más bajos se visitan primero
   - Ayuda a crear patrones de visita lógicos

##### Actualizar Secuencia del Mapa (Arrastrar y Soltar)

![Actualizar Secuencia del Mapa](assets/screenshots/update_map_sequence.png)

*Interfaz de arrastrar y soltar para reordenar todas las secuencias del mapa en un territorio*

**Acceso:** Menú de dirección → "Cambiar Secuencia"

**Cómo Funciona:**
1. Cada tarjeta muestra una dirección con el número de secuencia actual
2. Arrastre y suelte para reordenar - los números se actualizan automáticamente
3. Haga clic en **"Guardar"** para aplicar o **"Cancelar"** para descartar

**Mejores Prácticas:**
- Minimice el retroceso agrupando direcciones cercanas
- Agrupe pisos juntos en edificios de varios pisos
- Cree flujo lógico de un extremo al otro
- Revise la vista del mapa después de secuenciar

**4. Renombrar**
   - Cambie el nombre o identificador de una dirección/unidad
   - Actualice nombres de edificios o números de unidad
   - Mantiene los datos actualizados con cambios del mundo real
   - Útil para corregir errores de entrada iniciales

**5. Agregar No. de Unidad**
   - Agregue nuevos números de unidad a direcciones existentes
   - Expanda edificios de varios pisos con unidades adicionales
   - Útil cuando se agregan nuevos apartamentos a un edificio
   - Mantiene la organización de estructura del edificio

**6. Agregar Piso Superior**
   - Extienda un edificio hacia arriba con pisos adicionales
   - Para edificios que se han expandido o inicialmente se subestimaron
   - Crea automáticamente unidades para nuevos pisos basándose en el patrón del edificio
   - Ayuda a mantener los datos del territorio actualizados con cambios de construcción

**7. Agregar Piso Inferior**
   - Agregue pisos debajo del piso más bajo actual
   - Útil para niveles de sótano o pisos inferiores recién accesibles
   - Puede agregar números de piso negativos (ej., S1, S2)
   - Mantiene sistema de numeración de pisos consistente

**8. Restablecer Estado**
   - Limpie el estado de una dirección de vuelta a "No Hecho"
   - Elimina solo los estados "Hecho" y "No en Casa"
   - NO elimina los estados "No Visitar" o "Inválido"
   - Preserva notas y otra información de la dirección
   - Útil al reiniciar trabajo en una dirección previamente completada
   - No afecta otras direcciones en el territorio

**9. Eliminar**
   - Elimine permanentemente una dirección, unidad o piso del territorio
   - No se puede deshacer - use con precaución
   - Útil para eliminar entradas duplicadas o direcciones inexistentes
   - Confirme cuidadosamente antes de eliminar

**Casos de Uso Comunes:**
- Corregir errores: Renombrar y Cambiar Ubicación
- Reequilibrio de territorio: Cambiar Territorio
- Actualizaciones de edificio: Agregar Piso Superior/Inferior
- Optimización de ruta: Cambiar Secuencia
- Limpieza de datos: Eliminar duplicados
- Actualizaciones estacionales: Restablecer Estado

> **Advertencia**: Los cambios afectan a todos los usuarios inmediatamente. Eliminar es permanente y no se puede deshacer.

---

### Gestión de Territorios (Solo Administradores)

![Crear Nuevo Territorio](assets/screenshots/create_new_territory.png)

*Figura 21: Interfaz de creación de territorio para agregar nuevos territorios*

Los Administradores tienen control total sobre la creación, edición y gestión de territorios.

#### Crear un Nuevo Territorio

**Paso 1: Acceder a Creación de Territorio**

1. Haga clic en el menú desplegable del **selector de territorio**
2. Seleccione **"Crear Nuevo Territorio"** o **"Nuevo Territorio"**
3. Se abre el formulario de creación de territorio

**Paso 2: Ingresar Información del Territorio**

- **Código del Territorio**: Identificador corto (ej., "T-001", "M-12", "O-05")
  - Manténgalo corto y significativo
  - Use convención de nomenclatura consistente
  - Máximo recomendado: 10 caracteres
- **Descripción**: Nombre completo o descripción del área
  - Ejemplos: "Centro Comercial", "Residencial Norte"
  - Sea descriptivo para fácil identificación
  - Máximo recomendado: 100 caracteres

**Paso 3: Crear**

1. Haga clic en **"Crear Territorio"**
2. El nuevo territorio se crea y se selecciona
3. Listo para agregar direcciones

#### Editar Detalles del Territorio

**Cambiar Código del Territorio:**

1. Seleccione el territorio
2. Haga clic en **"Editar"** o **"Cambiar Código de Territorio"**
3. Ingrese el nuevo código
4. Guarde los cambios
5. **Advertencia**: Actualice cualquier referencia al código antiguo

**Cambiar Descripción del Territorio:**

1. Seleccione el territorio
2. Haga clic en **"Cambiar Nombre del Territorio"** u opción de edición
3. Actualice la descripción
4. Guarde los cambios

**Cambiar Secuencia del Territorio:**

##### Actualizar Secuencia de Territorio (Arrastrar y Soltar)

![Actualizar Secuencia de Territorio](assets/screenshots/territory_sequence_change.png)

*Interfaz de arrastrar y soltar para reordenar todos los territorios en la congregación*

**Acceso:** Menú desplegable de Territorio → "Cambiar Secuencia"

**Cómo Funciona:**
1. Cada tarjeta muestra código y descripción del territorio
2. Arrastre y suelte para reordenar - los números de secuencia se actualizan automáticamente
3. Haga clic en **"Guardar"** para aplicar o **"Cancelar"** para descartar
4. Controla el orden en menús desplegables y listas de selección

**Mejores Prácticas:**
- Organice por proximidad geográfica
- Agrupe por tipo (residencial, comercial, empresarial)
- Considere asignaciones de grupo del servicio del campo
- Use para planificación de rotación de territorios

#### Opciones de Configuración del Territorio

![Opciones de Configuración del Territorio](assets/screenshots/territory_configurations.png)

*Menú desplegable del territorio mostrando opciones de configuración*

Los Administradores pueden acceder a las opciones de configuración del territorio a través del botón **desplegable de Territorio** en la barra de navegación superior. Haga clic en el botón para revelar las siguientes opciones:

**Opciones Disponibles:**

- **Crear Nuevo**: Crear un nuevo territorio desde cero
- **Cambiar Código**: Modificar el identificador único del territorio
- **Cambiar Nombre**: Actualizar la descripción del territorio
- **Cambiar Secuencia**: Reordenar cómo aparecen los territorios en la lista de selección
- **Eliminar Actual**: Eliminar permanentemente el territorio actualmente seleccionado
- **Restablecer Estado**: Limpiar todos los estados de direcciones de vuelta a "No Hecho"

Estas opciones proporcionan acceso rápido a tareas comunes de gestión de territorios sin navegar a través de múltiples menús.

#### Operaciones de Territorio

**Restablecer Territorio:**

![Crear Nuevo Mapa](assets/screenshots/create_new_map.png)

*Figura 22: Interfaz de operaciones y gestión de territorios*

Restablece todas las direcciones en el territorio al estado "No Hecho":

1. Seleccione el territorio
2. Haga clic en el botón **"Restablecer Territorio"**
3. Confirme la acción (¡esto borra todos los datos de visita!)
4. Todas las direcciones regresan a "No Hecho"
5. Los conteos de no en casa se restablecen a 0
6. Las notas se preservan
7. El progreso se restablece a 0%

**Usar Cuando:**

- El territorio está completamente trabajado y listo para reasignar
- Comenzar nueva ronda de visitas
- Limpiar datos de prueba

**Advertencia**: No se puede deshacer. Todas las actualizaciones de estado se perderán.

**Eliminar Territorio:**

![Lista de Selección de Territorios](assets/screenshots/territory_selection_list.png)

*Figura 23: Lista de selección de territorios mostrando todos los territorios disponibles*

Elimina permanentemente el territorio y todos sus datos:

1. Seleccione el territorio a eliminar
2. Haga clic en el botón **"Eliminar Territorio"**
3. Lea el mensaje de advertencia cuidadosamente
4. Escriba la confirmación si se requiere
5. Confirme la eliminación

**Elimina:**

- Registro del territorio
- Todas las direcciones en el territorio
- Todas las unidades y pisos
- Todo el historial de asignaciones
- Todos los datos relacionados

**Advertencia Crítica**: Esta acción NO PUEDE deshacerse. Considere exportar datos primero.

---

### Gestión de Direcciones (Solo Administradores)

![Mapa de Un Solo Piso](assets/screenshots/single_story_map.png)

*Figura 24: Interfaz de gestión de direcciones para propiedades privadas/de un solo piso*

Los Administradores pueden agregar y gestionar direcciones dentro de los territorios.

#### Tipos de Direcciones

Ministry Mapper soporta dos tipos de direcciones:

**1. Direcciones Públicas (Varios Pisos)**

- Edificios de apartamentos, condominios
- Múltiples pisos y unidades
- Ejemplos: Edificios HDB, complejos de apartamentos
- Cada piso tiene múltiples unidades

**2. Direcciones Privadas (Un Solo Piso)**

- Casas individuales, casas-tienda
- Propiedades individuales con una dirección
- Ejemplos: Propiedades residenciales, edificios independientes
- Sin estructura de piso/unidad

#### Agregar una Dirección Pública (Varios Pisos)

**Paso 1: Iniciar Creación**

1. Seleccione el territorio
2. Haga clic en el botón **"Agregar Dirección"** o **"+"**
3. Seleccione tipo **"Dirección Pública"**

**Paso 2: Ingresar Información del Edificio**

- **Código Postal/Dirección**: Identificador del edificio
  - Ingrese código postal o dirección de calle
  - El sistema puede auto-completar la ubicación
  - Usado para geocodificación y visualización en mapa
- **Nombre del Edificio**: Nombre opcional del edificio
  - Ejemplos: "Bloque 123A", "Torres Soleadas"
  - Ayuda a los publicadores a identificar el edificio

**Paso 3: Configurar Pisos**

![Mapa de Varios Pisos](assets/screenshots/multi_story_map.png)

*Figura 25: Gestión de edificios de varios pisos con organización de pisos y unidades*

- **Piso Inicial**: Número del piso más bajo
  - Puede ser negativo para niveles de sótano
  - Ejemplos: -2 (S2), 1 (Planta Baja), 0
- **Piso Superior**: Número del piso más alto
  - Máximo: 50
  - Ejemplos: 10, 25, 40
- **Selección de Pisos**: Elegir pisos específicos
  - Salte pisos sin unidades (ej., pisos mecánicos)
  - Típico: Todos los pisos desde inicio hasta tope

**Paso 4: Configurar Unidades**

- **Unidades Por Piso**: Número de unidades en cada piso
  - Ejemplos: 8, 12, 16
  - Crea unidades automáticamente
- **Formato de Número de Unidad**: Cómo numerar unidades
  - Patrón: Piso + unidad (ej., 01-01, 01-02)
  - Personalizado: Ingrese números de unidad manualmente después

**Paso 5: Crear y Poblar**

1. Haga clic en **"Crear"**
2. El sistema genera todos los pisos y unidades
3. La dirección aparece en el territorio con todas las unidades

**Ejemplo:**

- Edificio: Bloque 123
- Pisos: 1 a 12
- Unidades por piso: 8
- Resultado: 96 unidades creadas (12 pisos × 8 unidades)

#### Agregar una Dirección Privada (Un Solo Piso)

**Paso 1: Iniciar Creación**

1. Seleccione el territorio
2. Haga clic en **"Agregar Dirección"** o **"+"**
3. Seleccione tipo **"Dirección Privada"**

**Paso 2: Ingresar Información de la Propiedad**

- **Código Postal/Dirección de la Propiedad**: Identificador único
  - Dirección de calle o código postal
  - Cada propiedad es un registro
- **Nombre de la Propiedad**: Nombre opcional de la casa
  - Ejemplos: "123 Calle Principal", "Villa Soleada"

**Paso 3: Establecer Ubicación (Opcional)**

- Haga clic en **"Establecer Geolocalización"**
- Use el mapa para señalar la ubicación exacta
- Ayuda con la navegación
- Puede actualizarse después

**Paso 4: Crear**

1. Haga clic en **"Crear Propiedad"**
2. Se crea una unidad de dirección única
3. Aparece en la lista del territorio

#### Gestionar Direcciones Existentes

**Editar Nombre de Dirección:**

1. Seleccione el territorio con la dirección
2. Haga clic en la opción de edición para la dirección
3. Actualice nombre/código postal
4. Guarde los cambios

**Cambiar Código Postal:**

1. Acceda al modo de edición de dirección
2. Actualice el campo de código postal
3. Puede afectar la geocodificación
4. Guarde y verifique la ubicación en el mapa

**Restablecer Dirección:**

- Limpia todos los estados de unidad en la dirección
- Las notas se preservan
- Use cuando la dirección esté completamente trabajada

**Eliminar Dirección:**

- Elimina toda la dirección y todas las unidades
- No se puede deshacer
- Confirme cuidadosamente antes de eliminar

#### Gestionar Unidades (Solo Direcciones Públicas)

**Agregar Unidades:**

1. Seleccione la dirección
2. Haga clic en **"Agregar Unidades"**
3. Especifique los números de unidad a agregar
4. Las unidades se crean automáticamente

**Eliminar Unidades:**

1. Haga clic en la unidad específica
2. Haga clic en el botón **"Eliminar Unidad"** en el modal
3. Confirme la eliminación
4. La unidad se elimina de la dirección

**Cambiar Secuencia de Unidad:**

1. Abra el modal de edición de unidad
2. Actualice el número de secuencia
3. Afecta el orden de visita
4. Los números más bajos se visitan primero

**Agregar/Eliminar Pisos:**

1. Acceda a la gestión de pisos
2. Agregue nuevos números de piso
3. O elimine pisos completos
4. Todas las unidades en el piso se afectan

**Actualizar Geolocalización de Unidad:**

- Establezca coordenadas específicas para la unidad
- Útil para edificios grandes
- Ayuda con navegación precisa
- Característica opcional

---

---

## Uso Móvil

![Vista de Mapa de Asignación de Publicador](assets/screenshots/publisher_assignment_map_view.png)

*Figura 26: Interfaz responsive para móviles optimizada para el servicio del campo*

### Usar Ministry Mapper en Su Teléfono

Ministry Mapper es completamente responsive y optimizado para dispositivos móviles, haciéndolo perfecto para el servicio del campo.

#### Acceder en Móvil

**Acceso por Navegador:**

1. Abra su navegador móvil:
   - **iOS**: Safari, Chrome
   - **Android**: Chrome, Firefox, Samsung Internet
2. Navegue a la URL de Ministry Mapper de su congregación
3. Inicie sesión o haga clic en el enlace de asignación
4. La interfaz se adapta automáticamente al tamaño de su pantalla

**Características en Móvil:**

- Botones y controles amigables para táctil
- Gestos de deslizamiento para navegación
- Diseños optimizados para pantallas pequeñas
- Áreas de toque más grandes para selección fácil
- Acceso completo a todas las características de escritorio
- Integración de mapa interactivo con GPS

#### Instalación de Aplicación Web Progresiva (PWA)

![Modo Vista de Mapa Un Solo Piso](assets/screenshots/single_story_map_view_mode.png)

*Figura 27: Modo de vista de mapa a pantalla completa disponible en instalación PWA*

Instale Ministry Mapper como una aplicación para mejor rendimiento:

**Beneficios de Instalar:**

- Carga más rápida con recursos en caché
- Icono de aplicación en pantalla de inicio
- Experiencia a pantalla completa (sin UI del navegador)
- Rendimiento mejorado
- Mejor integración con el dispositivo

**Instalación en iOS (Safari):**

1. Abra Ministry Mapper en Safari
2. Toque el botón **Compartir**
3. Desplácese hacia abajo y toque **"Agregar a pantalla de inicio"**
4. Edite el nombre si lo desea
5. Toque **"Agregar"**
6. El icono de la aplicación aparece en la pantalla de inicio

**Instalación en Android (Chrome):**

1. Abra Ministry Mapper en Chrome
2. Toque el botón de menú
3. Seleccione **"Instalar aplicación"** o **"Agregar a pantalla de inicio"**
4. Confirme la instalación
5. El icono de la aplicación aparece en la pantalla de inicio o cajón de aplicaciones

**Usar la Aplicación Instalada:**

- Inicie desde la pantalla de inicio como cualquier aplicación
- Sin barra de direcciones del navegador
- Experiencia de aplicación fluida
- Se actualiza automáticamente

#### Mejores Prácticas Móviles

**Antes de Salir:**

1. Verifique que el enlace de asignación no haya expirado
2. Revise el territorio y mapa
3. Note cualquier instrucción especial
4. Asegure conexión a internet estable
5. Cargue completamente su dispositivo
6. Considere cargador portátil

**Durante el Servicio del Campo:**

1. Actualice direcciones inmediatamente después de las visitas
2. Agregue notas mientras la información está fresca
3. Use navegación GPS en el mapa
4. Siga los números de secuencia para enrutamiento eficiente
5. Ahorre batería reduciendo brillo de pantalla cuando no se necesite

**Consejos de Uso de Datos:**

- Ministry Mapper usa datos mínimos
- Los mosaicos del mapa pueden usar más datos
- La mayoría de las actualizaciones son < 1KB cada una
- Adecuado para uso de datos móviles

### Capacidades y Limitaciones Sin Conexión

**Conexión a Internet Requerida:**

Ministry Mapper requiere internet activo para:

- Cargar datos del territorio
- Guardar actualizaciones de estado
- Sincronización en tiempo real
- Mostrar mapas
- Autenticación de usuario

**Características Sin Conexión Limitadas:**

- Recursos estáticos en caché (shell de la aplicación)
- El territorio cargado previamente puede mostrarse
- **No puede hacer actualizaciones sin conexión**
- **Las actualizaciones no se ponen en cola para sincronización posterior**

**Manejo de Pérdida de Conexión:**

- El sistema detecta la pérdida de conexión
- Muestra advertencia de estado de conexión
- Se reconecta automáticamente cuando está disponible
- Reanude el trabajo cuando se restaure la conexión

**Recomendaciones:**

- Asegure conexión confiable antes de comenzar
- Pruebe la conexión en la ubicación del territorio
- Tenga plan de respaldo para áreas sin internet
- Considere hotspot WiFi portátil si es necesario
- Actualice direcciones mientras la conexión está activa

---

---

## Configuración de Cuenta y Perfil

### Gestión de Perfil Personal

![Iniciar Sesión](assets/screenshots/sign_in.png)

*Figura 28: Interfaz de inicio de sesión para acceder a su cuenta de Ministry Mapper*

**Acceder a Su Perfil:**

1. Haga clic en su **nombre/icono de perfil** (esquina superior derecha)
2. Seleccione **"Perfil"** del menú desplegable
3. Vea y gestione la configuración de su cuenta

**Opciones de Perfil Disponibles:**

- Ver información de la cuenta (nombre, correo electrónico)
- Cambiar contraseña
- Ver membresía de congregación
- Acceder a gestión de usuarios (Solo Administradores)
- Cerrar sesión

### Cambiar Su Contraseña

**Requisitos de Seguridad:**

- Mínimo 6 caracteres
- Al menos un número
- Al menos una letra mayúscula
- Debe coincidir con la confirmación

**Pasos para Cambiar:**

1. Vaya a su perfil
2. Haga clic en **"Cambiar Contraseña"**
3. Ingrese su **contraseña actual**
4. Ingrese **nueva contraseña**
5. **Confirme nueva contraseña**
6. Haga clic en **"Guardar"** o **"Cambiar Contraseña"**
7. Un mensaje de éxito confirma el cambio

> **Consejo**: Use una contraseña fuerte y única. Considere usar un gestor de contraseñas.

### Recuperación de Contraseña

![Olvidé Contraseña](assets/screenshots/forgot_password.png)

*Figura 29: Página de recuperación de contraseña para restablecer contraseñas olvidadas*

¿Olvidó su contraseña? Proceso de recuperación fácil:

1. Vaya a la página de inicio de sesión
2. Haga clic en el enlace **"¿Olvidó su contraseña?"**
3. Ingrese su **dirección de correo electrónico registrada**
4. Haga clic en **"Continuar"** o **"Enviar Enlace de Restablecimiento"**
5. Revise su bandeja de entrada de correo electrónico

![Correo de Restablecimiento de Contraseña](assets/screenshots/reset_password_email.png)

*Figura 30: Correo de restablecimiento de contraseña con enlace seguro para crear nueva contraseña*

6. Haga clic en el enlace de restablecimiento de contraseña (válido por tiempo limitado)
7. Cree una nueva contraseña cumpliendo los requisitos
8. Confirme la nueva contraseña
9. Inicie sesión con la nueva contraseña

**Si no recibe el correo electrónico:**

- Revise la carpeta de spam/correo no deseado
- Verifique que ingresó el correo correcto
- Espere unos minutos e intente de nuevo
- Contacte al administrador si los problemas persisten

### Selección de Idioma

![Lista de Selección de Idioma](assets/screenshots/language_selection_list.png)

*Figura 31: Interfaz de selección de idioma mostrando todos los idiomas soportados*

Ministry Mapper soporta múltiples idiomas para congregaciones internacionales.

**Idiomas Soportados:**

- Inglés (en)
- Japonés (ja / 日本語)
- Coreano (ko / 한국어)
- Chino (zh / 中文)
- Indonesio (id / Bahasa Indonesia)
- Malayo (ms / Bahasa Melayu)

**Cómo Se Determina el Idioma:**

- Detectado automáticamente desde la configuración del navegador
- Usa la preferencia de idioma de su sistema operativo
- No se necesita selección manual en la mayoría de los casos

**Para Cambiar el Idioma:**

![Configuración de Tema Oscuro Claro](assets/screenshots/dark_light_theme_settings.png)

*Figura 32: Configuración de tema y apariencia incluyendo preferencias de idioma*

Cambie la configuración de idioma del navegador (Chrome: Configuración → Idiomas | Safari: Preferencias del Sistema → Idioma y Región | Firefox: Configuración → General → Idioma), luego actualice Ministry Mapper. Todos los elementos de la interfaz, mensajes y texto de ayuda están completamente traducidos.

### Configuración de Apariencia y Tema

Ministry Mapper soporta temas claro y oscuro para adaptarse a su preferencia y reducir la fatiga visual.

**Cambiar Tema:**

Icono de perfil → Configuración de Tema/Apariencia → Seleccione:
- **Modo Claro**: Interfaz brillante para el día
- **Modo Oscuro**: Brillo reducido para poca luz (ahorra batería en OLED)
- **Sistema Predeterminado**: Coincide con la configuración del dispositivo automáticamente

---

## Mejores Prácticas

### Para Publicadores

| Fase | Mejores Prácticas |
|------|-------------------|
| **Antes de Comenzar** | Revise el territorio en el mapa - Verifique notas/instrucciones - Planifique ruta usando secuencias - Note direcciones "No Visitar" - Asegure dispositivo cargado |
| **Durante el Servicio** | Actualice inmediatamente después de cada visita - Agregue notas detalladas y respetuosas - Siga los números de secuencia - Use el mapa para navegación - Marque "No en Casa" con precisión |
| **Después de Completar** | Revise todas las actualizaciones - Agregue observaciones finales - Notifique al administrador si está completo - Reporte problemas de direcciones |

### Para Conductores

- Establezca tiempos de expiración de enlace apropiados basados en el tamaño del territorio
- Incluya nombres de publicadores para seguimiento
- Limpie asignaciones expiradas regularmente
- Monitoree el completado del territorio y haga seguimiento de asignaciones vencidas
- Publique mensajes e instrucciones claras

### Para Administradores

| Área | Mejores Prácticas |
|------|-------------------|
| **Configuración de Territorio** | Nomenclatura consistente - Códigos cortos y significativos - Descripciones claras - Verificar ubicaciones en mapa - Secuencias lógicas |
| **Gestión de Datos** | Limpieza regular - Restablecer territorios completados - Verificar precisión - Copias de seguridad externas - Capacitar usuarios |
| **Gestión de Usuarios** | Asignación de roles apropiada - Eliminar usuarios inactivos - Respuesta rápida a solicitudes - Comunicación clara |

### Principios Generales

- **Planifique con Anticipación**: Revise antes de salir
- **Trabaje Sistemáticamente**: Complete una sección a la vez
- **Actualice Prontamente**: No espere para registrar información
- **Comunique Claramente**: Escriba notas comprensibles y respetuosas
- **Sea Exhaustivo**: Cubra todas las direcciones persistentemente
- **Sea Flexible**: Adáptese a las necesidades del territorio

---

## Solución de Problemas Comunes

### Problemas de Inicio de Sesión

| Problema | Síntomas | Soluciones |
|----------|----------|------------|
| **Contraseña Incorrecta** | Error "Credenciales inválidas" | Verifique correo - Verifique Bloq Mayús - Use "Olvidé Contraseña" → Restablecer vía correo |
| **Cuenta No Verificada** | Mensaje "Correo no verificado" | Revise bandeja/spam para correo de verificación - Haga clic en enlace - Solicite nuevo si expiró |
| **Sin Acceso a Congregación** | Sesión iniciada pero sin territorios | Contacte administrador para invitación y asignación de rol - Cierre/inicie sesión después de acceso otorgado |
| **Problemas de OTP** | No recibe/código OTP inválido | Revise spam - Códigos expiran en 5-10 minutos - Solicite nuevo código - Verifique dirección de correo |

---

### Problemas de Actualización de Datos

| Problema | Síntomas | Soluciones |
|----------|----------|------------|
| **Los Cambios No Se Guardan** | Guardar no persiste, desaparece después de actualizar | Verifique conexión a internet - Actualice página (Ctrl/Cmd + R) - Limpie caché del navegador - Pruebe diferente navegador - Verifique edición concurrente |
| **Actualizaciones en Tiempo Real No Aparecen** | Cambios de otros no se muestran | Asegure internet activo - Mantenga página abierta - Actualice para forzar actualización - Verifique estado de conexión |

---

### Problemas de Mapa y Navegación

| Problema | Síntomas | Soluciones |
|----------|----------|------------|
| **El Mapa No Carga** | Caja gris, error, mapa congelado | Actualice página (F5) - Espere 10-15 segundos - Verifique velocidad de internet - Limpie caché - Pruebe diferente navegador - Habilite JavaScript |
| **Ubicación Incorrecta** | Marcadores mal ubicados | Administradores: Actualice geolocalización - Verifique código postal - Use "Actualizar Geolocalización" - Use lat/long si es necesario |
| **Direcciones No Funcionan** | Problemas de navegación | Verifique ubicación de origen de congregación - Verifique que la dirección tenga coordenadas válidas |

---

### Problemas de Enlaces de Asignación

| Problema | Síntomas | Soluciones |
|----------|----------|------------|
| **Enlace Expirado** | "El enlace ha expirado", error 404 | Contacte administrador/conductor para nuevo enlace (los enlaces expiran después del tiempo establecido, típicamente 24 horas - esta es una característica de seguridad) |
| **Enlace No Funciona** | No abre, mensaje de error | Asegure que todo el enlace esté copiado (verifique saltos de línea) - Copie-pegue en navegador (no escriba) - Verifique que no esté expirado - Contacte administrador |

---

### Problemas de Permisos y Acceso

| Problema | Síntomas | Soluciones |
|----------|----------|------------|
| **Permiso Denegado** | "Permisos insuficientes", características en gris | Verifique su rol con administrador - Cierre sesión, limpie caché, inicie sesión de nuevo - Solicite actualización de rol si es necesario |
| **Datos de Congregación Incorrectos** | Territorios/datos desconocidos | Verifique cuenta correcta - Verifique selector de congregación - Cierre/inicie sesión - Contacte administrador |

---

### Problemas de Rendimiento

| Problema | Síntomas | Soluciones |
|----------|----------|------------|
| **Carga Lenta** | Tiempos de carga largos, lag, retrasos | Verifique velocidad de internet - Cierre pestañas innecesarias - Limpie caché - Reinicie navegador - Pruebe diferente hora - Reporte si persiste |
| **Bloqueos/Congelamientos** | La aplicación deja de responder | Actualice página - Limpie caché/cookies - Actualice navegador - Pruebe diferente navegador - Reinicie dispositivo - Verifique memoria |

---

### Compatibilidad de Navegador

#### Compatibilidad de Navegador

**Soportados:** Chrome, Firefox, Safari, Edge (versiones más recientes) - iOS Safari (iOS 13+) - Android Chrome

**No Soportados:** Internet Explorer, navegadores desactualizados

**Si tiene problemas:** Actualice navegador - Habilite JavaScript - Permita cookies - Deshabilite prevención de seguimiento estricta - Pruebe diferente navegador

---

## Obtener Ayuda y Soporte

### Canales de Soporte

**1. El Administrador de Su Congregación**

- **Primer punto de contacto** para la mayoría de problemas
- Puede ayudar con:
  - Acceso a cuenta y roles
  - Preguntas sobre territorio
  - Enlaces de asignación
  - Configuración local

**2. Documentación Oficial**

- **GitHub Wiki**: https://github.com/rimorin/ministry-mapper/wiki
- Guías completas para todos los roles
- Documentación de configuración y seguridad
- Preguntas frecuentes y soluciones comunes

**3. Problemas Técnicos**

- **GitHub Issues**: https://github.com/rimorin/ministry-mapper/issues
- Reporte errores y problemas técnicos
- Verifique problemas existentes primero
- Busque problemas similares

### Reportar Problemas Efectivamente

**Incluya en Su Reporte:**
- Lo que estaba tratando de hacer
- Lo que sucedió en su lugar (mensajes de error, capturas de pantalla)
- Pasos para reproducir el problema
- Navegador y versión
- Dispositivo y SO
- Tipo de cuenta (Publicador/Conductor/Administrador)

**Ejemplo de Buen Reporte:**
```
Problema: No puedo guardar actualización de estado de dirección
Pasos: Abrí enlace → Hice clic en #05-123 → Cambié a "Hecho" → Hice clic en Guardar → Error: "Error al actualizar"
Navegador: Chrome 120 | Dispositivo: iPhone 12, iOS 17 | Cuenta: Enlace de publicador
Captura de pantalla: [adjunta]
```

### Contacto de Emergencia

Para problemas urgentes:

- Contacte al administrador de su congregación directamente
- Tenga número de teléfono/correo electrónico listo
- Explique la urgencia claramente
- Tenga detalles relevantes listos

---

---

## Referencia Rápida

### Atajos de Teclado

Ministry Mapper usa atajos estándar del navegador:

| Atajo | Acción |
| ----- | ------ |
| `Escape` | Cerrar modales/diálogos abiertos |
| `Tab` | Navegar entre campos de formulario |
| `Enter` | Enviar formularios o confirmar acciones |
| `Ctrl/Cmd + R` | Actualizar página |
| Edición estándar | Copiar, pegar, seleccionar todo funcionan en campos de texto |

### Referencia Rápida de Estados

| Estado | Símbolo | Cuándo Usar |
| ------ | ------- | ----------- |
| **No Hecho** | (blanco) | Dirección aún no visitada (predeterminado) |
| **Hecho** | (verde) | Contactó con éxito al residente |
| **No en Casa** | (amarillo) | Nadie respondió la puerta |
| **No Visitar** | (rojo) | El residente solicitó no ser visitado |
| **Inválido** | (gris) | La dirección no existe o es inaccesible |

### Referencia Rápida de Capacidades por Rol

| Característica | Publicador | Solo Lectura | Conductor | Administrador |
| -------------- | :--------: | :----------: | :-------: | :-----------: |
| Ver vía enlace de asignación | Si | - | - | - |
| Ver todos los territorios | - | Si | Si | Si |
| Actualizar estado de dirección | Si | - | - | Si |
| Crear asignaciones | - | - | Si | Si |
| Publicar mensajes | - | - | Si | Si |
| Gestionar territorios | - | - | - | Si |
| Gestionar usuarios | - | - | - | Si |
| Configurar ajustes | - | - | - | Si |

---



## Privacidad y Seguridad

### Proteger la Información

Ministry Mapper maneja información sensible de direcciones y personal. Por favor observe estas pautas:

**Seguridad de Cuenta:**

- **Nunca comparta credenciales de inicio de sesión** con nadie
- **Use contraseñas fuertes y únicas** (mínimo 6 caracteres con números y mayúsculas)
- **Cierre sesión en dispositivos compartidos** siempre
- **Habilite OTP si está disponible** para seguridad extra
- **Reporte actividad sospechosa** inmediatamente

**Privacidad de Datos:**

- **Registre solo información necesaria** en notas
- **Sea respetuoso y factual** en todas las descripciones
- **Sin datos personales sensibles** (médicos, financieros, etc.)
- **Siga las solicitudes del residente** para privacidad
- **Cumpla con las leyes de privacidad** (GDPR, CCPA, regulaciones locales)

**Cumplimiento Legal:**

- **GDPR (Europa)**: Requisitos de protección de datos personales
- **CCPA (California)**: Derechos de privacidad del consumidor
- **LGPD (Brasil)**: Regulaciones de protección de datos
- **Leyes Locales**: Verifique los requisitos de su región

### Qué Información Se Almacena

**Datos de Usuario:**

- Detalles de cuenta (nombre, correo, estado de verificación)
- Asignación de rol en congregación
- Enlaces de asignación creados/accedidos
- Marcas de tiempo de actividad

**Datos de Territorio:**

- Información de dirección y unidad
- Actualizaciones de estado e historial
- Notas e información de visita
- Coordenadas de geolocalización
- Seguimiento de progreso

**Datos del Sistema:**

- Sesiones de inicio de sesión
- Suscripciones en tiempo real
- Historial de mensajes
- Configuración de ajustes

### Almacenamiento y Seguridad de Datos

| Componente | Detalles |
|------------|----------|
| **Backend** | Base de datos PocketBase gestionada por administrador - Ubicación de hospedaje determinada por congregación - Control de acceso basado en roles |
| **Sincronización en Tiempo Real** | Suscripciones de PocketBase - Conexiones HTTPS encriptadas - Reconexión automática - Gestión de sesión |
| **Lado del Cliente** | Service worker almacena en caché solo recursos estáticos - Sin datos sensibles almacenados localmente - Auto-actualización en nuevas versiones |
| **Responsabilidades del Administrador** | Implementar procedimientos de copia de seguridad - Realizar auditorías de seguridad - Mantener backend actualizado - Monitorear registros de acceso |

---

## Conclusión

Gracias por usar Ministry Mapper para apoyar las actividades del servicio del campo de su congregación. Esta solución moderna basada en web trae eficiencia, colaboración y beneficios ambientales a la gestión de territorios.

### Puntos Clave

| Rol | Responsabilidades Clave |
|-----|------------------------|
| **Publicadores** | Acceder vía enlaces - Actualizar inmediatamente después de visitas - Escribir notas respetuosas - Seguir secuencias |
| **Conductores** | Crear asignaciones - Monitorear progreso - Publicar mensajes - Coordinar actividades |
| **Administradores** | Configurar ajustes - Gestionar territorios - Asignar roles - Asegurar seguridad |

### Características del Sistema

**Stack Tecnológico:**

- React 19 + TypeScript frontend
- Backend PocketBase para gestión de datos
- Leaflet con OpenStreetMap para navegación
- Sincronización en tiempo real
- PWA responsive para móviles
- Soporte multiidioma
- Control de acceso basado en roles
- Monitoreo de errores con Sentry

**Beneficios:**

- **Ecológico**: Elimina el desperdicio de papel
- **Tiempo Real**: Actualizaciones instantáneas en todos los dispositivos
- **Móvil Primero**: Funciona en cualquier dispositivo con internet
- **Mapas Integrados**: Mapeo interactivo para navegación fácil
- **Seguro**: Permisos basados en roles y soporte OTP
- **Multiidioma**: Soporte para 7+ idiomas
- **Confiable**: Backend PocketBase con sincronización en tiempo real

---

### Agregar una Dirección Faltante

Si nota que falta una dirección en la lista durante el servicio del campo, puede agregarla directamente sin necesidad de contactar a un administrador.

**Cómo Agregar una Dirección Faltante:**

1. Desplácese hasta el final de la lista de direcciones
2. Toque la tarjeta con el símbolo **"+"** al final de la lista
3. Ingrese los detalles de la dirección en el formulario
4. Toque **"Guardar"** para agregar la dirección

![La tarjeta "+" al final de la lista de direcciones para agregar una dirección faltante](assets/screenshots/add_more_add.png)

*La tarjeta con el símbolo más al final de la lista de direcciones permite agregar una dirección faltante*

> **Nota:** Esta función está diseñada para congregaciones que aún están construyendo sus registros de territorio. La dirección se agrega inmediatamente y es visible para todos los usuarios.

---

### Obtener Indicaciones a una Dirección

Ministry Mapper incluye un servicio de rutas integrado para ayudarle a navegar al campo.

**Cómo Obtener Indicaciones:**

1. Toque la dirección a la que desea ir
2. Toque el botón **"Indicaciones"** en los detalles de la dirección
3. Seleccione su modo de viaje preferido:
   - **Conducción** — para desplazarse en auto
   - **Caminata** — para áreas densamente pobladas o distancias cortas
   - **Ciclismo** — para desplazarse en bicicleta
4. La ruta se calcula y se muestra directamente en el mapa

![Panel de enrutamiento mostrando opciones de modo de viaje y una ruta trazada en el mapa](assets/screenshots/map_routing.png)

*El panel de rutas muestra las opciones de modo de viaje y la ruta calculada sobre el mapa*

> **Consejo:** Use el modo caminata para áreas densamente pobladas donde el estacionamiento puede ser difícil. Requiere que `VITE_OPENROUTE_API_KEY` esté configurado en su despliegue.

---

**Versión**: Consulte la versión de su despliegue
**Última Actualización**: 2024

Para soporte técnico, contacte al administrador de su congregación o visite el wiki de GitHub.

---

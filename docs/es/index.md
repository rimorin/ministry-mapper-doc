# Bienvenido a Ministry Mapper

## ¿Qué es Ministry Mapper?

Ministry Mapper es una aplicación web moderna y de código abierto que ayuda a las congregaciones a gestionar sus territorios de ministerio de campo de manera eficiente. En lugar de usar papeletas de territorio en papel, Ministry Mapper ofrece una solución digital completa para organizar, rastrear y gestionar el trabajo del servicio de campo desde cualquier dispositivo con conexión a internet.

## Nuestra Historia

Comenzamos Ministry Mapper en 2022 como un proyecto simple para ayudar a nuestra congregación local a gestionar los territorios de manera más eficiente. A medida que más congregaciones descubrieron los beneficios de la gestión digital de territorios, continuamos mejorando la plataforma. Hoy en día, Ministry Mapper es utilizado por congregaciones en Singapur y Malasia, ayudando a los publicadores a mantenerse organizados en su trabajo de ministerio con funciones como sincronización en tiempo real, mapas interactivos y soporte multilingüe.

## Qué Hace Ministry Mapper

Ministry Mapper proporciona capacidades integrales de gestión de territorios:

### Características Principales

- **🗺️ Gestión de Territorios**: Organiza regiones geográficas en territorios con seguimiento automático de finalización
- **🏢 Mapas Interactivos**: Mapas basados en Leaflet con integración de OpenStreetMap, direcciones y geolocalización
- **📱 Colaboración en Tiempo Real**: Sincronización de datos en vivo en todos los dispositivos mediante SSE (Server-Sent Events)
- **🌍 Soporte Multilingüe**: 7 idiomas (inglés, español, indonesio, japonés, coreano, malayo, chino)
- **👥 Acceso Basado en Roles**: Roles de Publicador, Director, Administrador y Solo Lectura con permisos detallados
- **🔗 Enlaces de Asignación**: Enlaces de acceso seguros y con tiempo limitado para territorios sin necesidad de cuentas de usuario
- **📊 Seguimiento de Progreso**: Estado de finalización automático con indicadores visuales y estadísticas
- **✉️ Notificaciones por Correo Electrónico**: Plantillas de correo HTML para mensajes, notas e informes mensuales
- **📈 Informes en Excel**: Informes mensuales de la congregación con estadísticas detalladas
- **🔐 Autenticación Multifactor**: Soporte para OTP, MFA y OAuth2 (Google)
- **🌙 Modo Oscuro**: Temas adaptables al sistema con opciones claro/oscuro/sistema
- **📲 Aplicación Web Progresiva**: Instalable en móvil y escritorio con caché de activos sin conexión

### Para Diferentes Usuarios

- **Servidores de Territorio**: Gestiona todos los registros de territorio digitalmente con estadísticas e informes completos
- **Directores del Servicio de Campo**: Asigna territorios rápidamente con algoritmos inteligentes y realiza seguimiento de la actividad de publicadores
- **Publicadores**: Accede a mapas asignados mediante enlaces simples, actualiza el estado de direcciones en tiempo real y colabora eficazmente
- **Administradores**: Control total del sistema con gestión de usuarios, configuración de la congregación y registros de auditoría

## Problemas que Ministry Mapper Resuelve

### Sin Papel

Las papeletas de territorio tradicionales se pierden, dañan o se vuelven difíciles de leer con el tiempo. Ministry Mapper mantiene todo digital y seguro en la nube. Puedes acceder a tus territorios desde tu teléfono, tableta u ordenador en cualquier momento.

### Mejor Organización

Cuando los territorios se comparten entre varios publicadores, es fácil trabajar accidentalmente las mismas direcciones. Ministry Mapper ayuda a todos a ver lo que está ocurriendo en tiempo real, para no perder tiempo cubriendo el mismo terreno.

### Actualizaciones Instantáneas

Cuando alguien actualiza un territorio, todos ven los cambios de inmediato. No hay que esperar a que pasen los papeles de mano en mano ni a las reuniones para compartir actualizaciones.

### Registro Más Sencillo

En lugar de anotar manualmente cada cambio en papeletas de papel, Ministry Mapper realiza el seguimiento de todo automáticamente. Esto ahorra horas de trabajo y previene errores.

## Cómo Funciona Ministry Mapper

### Stack Tecnológico Moderno

Ministry Mapper aprovecha tecnologías web de vanguardia para un rendimiento óptimo:

**Frontend (ministry-mapper-v2):**

- **React 19.2.4**: Última biblioteca de interfaz de usuario con optimización automática del compilador
- **TypeScript 5.9.3**: Desarrollo con tipos seguros para menos errores
- **Vite 8.0.2**: Herramienta de compilación y servidor de desarrollo ultrarrápido
- **Leaflet 1.9.4**: Mapas interactivos con OpenStreetMap (sin límites de API)
- **Bootstrap 5.3.8**: Marco de interfaz de usuario moderno y responsivo
- **Wouter 3.9.0**: Enrutamiento ligero (3KB vs 40KB de alternativas)

**Backend (ministry-mapper-be):**

- **Go 1.25.0**: Lenguaje compilado de alto rendimiento
- **PocketBase 0.36.7**: Backend como servicio alojado con SQLite
- **SQLite 1.40.2+**: Base de datos confiable y autónoma (implementación en Go puro)
- **Echo v5**: Marco web minimalista y de alto rendimiento
- **Sentry 0.43.0**: Seguimiento y monitoreo de errores en tiempo real
- **MailerSend 1.6.3**: Servicio de correo electrónico transaccional
- **LaunchDarkly 7.14.6**: Gestión de indicadores de características
- **Excelize 2.10.1**: Generación de informes Excel
- **OpenAI GPT**: Resúmenes impulsados por IA para informes y resúmenes de mensajes

### Aspectos Destacados de la Arquitectura

- **Auto-Alojado**: Sin dependencia de proveedores, control total sobre tus datos
- **Multi-Inquilino**: Aislamiento de datos a nivel de congregación para mayor seguridad
- **Sincronización en Tiempo Real**: Actualizaciones en vivo basadas en SSE en todos los clientes
- **Aplicación Web Progresiva**: Instalable en todos los dispositivos con soporte de activos sin conexión
- **Contenedorización Docker**: Implementación y escalado sencillos
- **Diseño API-First**: API RESTful con soporte completo del SDK de PocketBase
- **Información Impulsada por IA**: Integración opcional de OpenAI GPT para resúmenes inteligentes de informes y resúmenes de mensajes

## Consideraciones Importantes

### Privacidad Primero

Ministry Mapper rastrea direcciones residenciales, lo que significa que debes seguir las leyes de privacidad de tu área. Diferentes países tienen diferentes reglas (como el RGPD en Europa o la CCPA en California). Asegúrate de entender y cumplir con tus regulaciones locales antes de usar Ministry Mapper.

### Se Requiere Internet

Ministry Mapper funciona a través de internet, por lo que necesitarás una conexión para usarlo. Si tu área tiene internet poco confiable, esto podría presentar desafíos. La aplicación funciona bien con datos móviles si no hay WiFi disponible.

### Curva de Aprendizaje

Pasar del papel al formato digital es un cambio, especialmente para quienes se sienten menos cómodos con la tecnología. Ofrecemos ayuda y soporte para facilitar la transición a todos en tu congregación.

## Primeros Pasos

### Para Usuarios

La manera más fácil de usar Ministry Mapper es a través de nuestro servicio alojado:

**[ministry-mapper.com](https://ministry-mapper.com)**

Simplemente:

1. Visita [ministry-mapper.com](https://ministry-mapper.com)
2. Crea una cuenta usando la página de registro
3. Verifica tu dirección de correo electrónico
4. Contacta al administrador de tu congregación para obtener los permisos correspondientes

**Enlaces Rápidos:**

- **[Comenzar →](getting-started.md)**
- **[Descripción de Características →](features.md)**
- **[Guía de Usuario →](user-guide.md)**
- **[Preguntas Frecuentes →](faq.md)**

### Para Administradores

Si estás considerando Ministry Mapper para tu congregación:

- **Recomendado**: Usa nuestro servicio alojado en [ministry-mapper.com](https://ministry-mapper.com)

  - No se requiere configuración técnica
  - Soporte profesional disponible
  - Actualizaciones y mantenimiento automáticos
  - Mejor confiabilidad y seguridad

- **Alternativa**: Revisa la [Guía de Implementación](deployment.md) (solo para usuarios avanzados)

No recomendamos el auto-alojamiento debido a la complejidad técnica y los requisitos de mantenimiento continuo. El servicio alojado en ministry-mapper.com proporciona una solución más simple y confiable.

**Recursos para Administradores:**

- **[Guía de Primeros Pasos →](getting-started.md)**
- **[Opciones de Implementación →](deployment.md)**
- **[Guía de Auto-Alojamiento →](self-hosting.md)**

### Para Desarrolladores

Si estás interesado en los aspectos técnicos o en contribuir:

**Documentación Técnica:**

- **[Descripción de la Arquitectura →](architecture.md)** - Diseño del sistema y decisiones tecnológicas
- **[Modelos de Datos y Esquema →](data-models.md)** - Estructura de la base de datos y relaciones
- **[Configuración del Backend →](backend-setup.md)** - Configuración de PocketBase
- **[Configuración del Frontend →](frontend-setup.md)** - Configuración de la aplicación React

**Desarrollo:**

- **Repositorio Frontend:** [github.com/rimorin/ministry-mapper-v2](https://github.com/rimorin/ministry-mapper-v2)
- **Repositorio Backend:** [github.com/rimorin/ministry-mapper-be](https://github.com/rimorin/ministry-mapper-be)
- **Problemas:** GitHub Issues en los repositorios correspondientes
- **Contribuciones:** Consulta los archivos CONTRIBUTING.md de los repositorios

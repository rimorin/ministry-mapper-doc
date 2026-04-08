# Preguntas Frecuentes (FAQ)

## Preguntas Generales

### ¿Qué es Ministry Mapper?

Ministry Mapper es una aplicación web moderna que ayuda a las congregaciones a gestionar territorios del servicio de campo de manera digital. En lugar de usar papeletas de territorio en papel, Ministry Mapper te permite organizar y rastrear todo digitalmente desde cualquier dispositivo con conexión a internet. Utiliza React para el frontend, PocketBase como backend, y Leaflet para la cartografía interactiva.

### ¿Cómo accedo a Ministry Mapper?

**Recomendado**: Usa nuestro servicio alojado en **[ministry-mapper.com](https://ministry-mapper.com)**

Simplemente:
1. Visita [ministry-mapper.com](https://ministry-mapper.com)
2. Crea una cuenta usando la página de registro
3. Verifica tu dirección de correo electrónico
4. Contacta al administrador de tu congregación para obtener los permisos adecuados

**Alternativa**: Auto-alojamiento (no recomendado) - consulta la [Guía de Auto-Alojamiento](self-hosting.md) para más detalles. El auto-alojamiento requiere experiencia técnica significativa y mantenimiento continuo.

### ¿Es Ministry Mapper gratuito?

**Servicio Alojado**: Visita [ministry-mapper.com](https://ministry-mapper.com) para precios y planes.

**Auto-Alojamiento**: El código fuente es gratuito y de código abierto, pero necesitarás proporcionar tu propio:
- Alojamiento para el frontend (por ejemplo, Vercel, Netlify, AWS)
- Despliegue del backend PocketBase
- Opcional: Cuenta de Sentry para monitoreo de errores

Nota: Los costos de auto-alojamiento (servidor, tarifas de API, tiempo de mantenimiento) a menudo superan las tarifas del servicio alojado.

### ¿Qué idiomas admite Ministry Mapper?

Idiomas actualmente admitidos:

- Inglés
- Japonés (日本語)
- Coreano (한국語)
- Chino (中文)
- Indonesio (Bahasa Indonesia)
- Malayo (Bahasa Melayu)

La interfaz detecta automáticamente el idioma de tu navegador. También puedes cambiar de idioma manualmente en la aplicación.

### ¿Necesito conocimientos técnicos para usar Ministry Mapper?

**Como Publicador/Usuario**: ¡No se requieren conocimientos técnicos! Si puedes usar un teléfono inteligente u ordenador, puedes usar Ministry Mapper a través del servicio alojado en [ministry-mapper.com](https://ministry-mapper.com).

**Como Administrador Auto-Alojado**: Sí, se requieren conocimientos técnicos significativos:

- Experiencia con administración de servidores Linux
- Comprensión de Docker y la contenedorización
- Conocimiento de variables de entorno y configuración
- Familiaridad con certificados SSL/TLS
- Configuración de servidor web (Nginx/Apache)
- Configuración de DNS y dominio

**Recomendamos encarecidamente usar el servicio alojado en lugar de auto-alojarse.** Consulta la [Guía de Auto-Alojamiento](self-hosting.md) para detalles completos si aún deseas auto-alojarte.

### ¿Puede Ministry Mapper funcionar sin conexión?

No, se requiere conexión a internet para que la aplicación funcione correctamente. Esto se debe a que:

- Necesita conectarse al backend de PocketBase
- Las actualizaciones en tiempo real entre usuarios requieren conectividad

La aplicación funciona bien con datos móviles si no hay WiFi disponible.

## Privacidad y Legal

### ¿Qué leyes de privacidad debo considerar?

**Importante**: Ministry Mapper rastrea direcciones residenciales, que pueden estar sujetas a leyes de privacidad de datos. Estas leyes varían significativamente entre países y regiones:

- **Europa**: RGPD (Reglamento General de Protección de Datos)
- **California**: CCPA (Ley de Privacidad del Consumidor de California)
- **Brasil**: LGPD (Lei Geral de Proteção de Dados)
- **Muchas Otras**: Diversas regulaciones locales

**Por favor, revisa exhaustivamente tus regulaciones locales y asegura el cumplimiento antes de usar Ministry Mapper.** Consulta a un profesional legal para obtener asesoramiento legal específico de tu área.

**Servicio Alojado**: El servicio alojado en [ministry-mapper.com](https://ministry-mapper.com) puede proporcionar asistencia con el cumplimiento, pero sigues siendo responsable de seguir las leyes locales.

**Auto-Alojamiento**: Eres totalmente responsable del cumplimiento de todas las leyes de privacidad.

### ¿Qué datos almacena Ministry Mapper?

**Datos del Usuario (gestionados por PocketBase):**

- Dirección de correo electrónico
- Nombre
- Contraseña (cifrada)
- Roles y permisos del usuario

**Datos del Territorio:**

- Límites y nombres del territorio
- Direcciones
- Información de estado
- Notas sobre los hogares
- Historial de asignaciones
- Marcas de tiempo de actualización

**Datos Técnicos (si Sentry está habilitado):**

- Registros de errores
- Métricas de rendimiento

Todo el almacenamiento de datos cumple con las mejores prácticas de seguridad. Para las políticas de datos del servicio alojado, consulta [ministry-mapper.com](https://ministry-mapper.com).

## Configuración Técnica (Auto-Alojamiento)

!!! warning "Auto-Alojamiento No Recomendado"
    Las siguientes secciones son para usuarios avanzados que desean auto-alojar Ministry Mapper. **Recomendamos encarecidamente usar el servicio alojado en [ministry-mapper.com](https://ministry-mapper.com) en su lugar.** El auto-alojamiento requiere experiencia técnica significativa, mantenimiento continuo y responsabilidades de seguridad.
    
    Para instrucciones completas de auto-alojamiento, consulta la [Guía de Auto-Alojamiento](self-hosting.md).

### ¿Cuáles son los requisitos del sistema?

**Para Usar el Servicio Alojado:**
- Navegador web moderno (Chrome, Firefox, Safari, Edge)
- Conexión a internet
- Dirección de correo electrónico
- Dispositivo móvil u ordenador

**Para Desarrollo de Auto-Alojamiento:**

- Node.js >= 22.0.0
- Gestor de paquetes npm
- Git
- Docker (para el backend)

**Para Producción de Auto-Alojamiento:**

- Servicio de alojamiento en la nube (Railway, Render, DigitalOcean, AWS, etc.)
- Nombre de dominio
- Certificado SSL/TLS
- Backend PocketBase (despliegue separado)
- (Opcional) Cuenta de Sentry para monitoreo
- (Opcional) Servicio de correo electrónico para notificaciones

### ¿Qué variables de entorno son necesarias?

**Solo Para Auto-Alojamiento** - Esta sección solo aplica si te auto-alojas. El servicio alojado maneja toda la configuración automáticamente.

**Frontend (archivo .env):**

```
VITE_SYSTEM_ENVIRONMENT=local
VITE_VERSION=$npm_package_version
VITE_POCKETBASE_URL=tu_url_de_backend_pocketbase
VITE_OPENROUTE_API_KEY=tu_clave_openrouteservice
VITE_LOCATIONIQ_API_KEY=tu_clave_locationiq
VITE_SENTRY_DSN=tu_sentry_dsn (opcional)
VITE_PRIVACY_URL=tu_url_de_politica_de_privacidad
VITE_TERMS_URL=tu_url_de_terminos
VITE_ABOUT_URL=tu_url_de_informacion
```

**Para producción:** Mismas variables que arriba, pero con `VITE_SYSTEM_ENVIRONMENT=production`

Consulta la [Guía de Configuración del Frontend](frontend-setup.md) para detalles completos.

### ¿Cómo despliego el frontend?

**Solo Para Auto-Alojamiento** - Omite esto si estás usando el servicio alojado.

1. **Compila la aplicación:**

   ```bash
   npm run build
   ```

2. **Configura las variables de entorno** como se especificó arriba

3. **Despliega la carpeta `build/`** en tu proveedor de alojamiento:

   - Vercel: Conecta tu repositorio de GitHub y configura las variables de entorno
   - Netlify: Arrastra y suelta la carpeta de compilación o conecta mediante Git
   - AWS S3: Sube la carpeta de compilación y configura el alojamiento de sitio web estático

4. **Asegúrate de que el backend PocketBase esté desplegado** y accesible en la URL especificada en `VITE_POCKETBASE_URL`

Consulta la [Guía de Configuración del Frontend](frontend-setup.md) y la [Guía de Auto-Alojamiento](self-hosting.md) para instrucciones detalladas.

### ¿Cómo configuro el backend PocketBase?

**Solo Para Auto-Alojamiento** - Omite esto si estás usando el servicio alojado.

El backend se gestiona en un repositorio separado: [ministry-mapper-be](https://github.com/rimorin/ministry-mapper-be)

**Descripción General Rápida:**

1. Clona el repositorio del backend
2. Configura las variables de entorno (consulta la [Guía de Configuración del Backend](backend-setup.md))
3. Despliega usando Docker o Railway/Render
4. Inicializa la base de datos con el esquema
5. Configura SMTP para notificaciones por correo electrónico (opcional)

**Instrucciones detalladas:** Consulta la [Guía de Configuración del Backend](backend-setup.md) y la [Guía de Auto-Alojamiento](self-hosting.md).

**Importante**: El backend debe estar desplegado y accesible antes de desplegar el frontend.

### ¿Cómo configuro el monitoreo con Sentry?

**Solo Para Auto-Alojamiento** - Omite esto si estás usando el servicio alojado.

1. Crea una cuenta en [Sentry](https://sentry.io/)
2. Crea un proyecto de React
3. Ve a la configuración y obtén la clave DSN
4. Configura las siguientes variables de entorno:
   - `VITE_SENTRY_DSN`: Tu DSN del proyecto Sentry
   - `VITE_SYSTEM_ENVIRONMENT`: Configura como "production" para producción (afecta la tasa de muestreo de rastreo)
   - `VITE_VERSION`: Usado para el seguimiento de versiones en Sentry

Nota: Sentry es opcional pero recomendado para despliegues en producción.

## Desarrollo

### ¿Cómo ejecuto la aplicación localmente?

**Solo Para Desarrolladores** - Esta sección es para desarrolladores que contribuyen a Ministry Mapper.

1. **Clona el repositorio:**

   ```bash
   git clone https://github.com/rimorin/ministry-mapper-v2.git
   cd ministry-mapper-v2
   ```

2. **Instala las dependencias:**

   ```bash
   npm install
   ```

3. **Configura el archivo .env** con las variables de entorno requeridas (consulta `.env.example`)

4. **Inicia el servidor de desarrollo:**
   ```bash
   npm start
   ```

La aplicación se ejecutará en `http://localhost:3000`

Nota: Necesitas un backend PocketBase en ejecución para que la aplicación funcione. Consulta la [Guía de Configuración del Backend](backend-setup.md).

### ¿Cómo ejecuto las pruebas?

```bash
npm test
```

Las pruebas están escritas usando Vitest y Testing Library. Los archivos de prueba se encuentran junto a los archivos de código fuente con la extensión `.test.ts`.

### ¿Cómo formateo el código?

**Verificar el formato:**

```bash
npm run prettier
```

**Aplicar correcciones de formato:**

```bash
npm run prettier:fix
```

El proyecto usa Prettier para el formato de código y tiene hooks de pre-commit configurados mediante Husky.

### ¿Cuál es el stack tecnológico?

- **Marco de Frontend**: React 19 con TypeScript
- **Herramienta de Compilación**: Vite
- **Biblioteca de UI**: React Bootstrap (Bootstrap 5)
- **Mapas**: Leaflet con OpenStreetMap
- **Gestión de Estado**: React Context + hooks personalizados
- **Enrutamiento**: Wouter
- **Estilos**: SCSS
- **Backend**: PocketBase (repositorio separado)
- **Monitoreo**: Sentry
- **Pruebas**: Vitest + Testing Library

Consulta CLAUDE.md para información detallada de la arquitectura.

## Solución de Problemas

### La aplicación muestra "No se puede conectar al backend"

**Para Usuarios del Servicio Alojado**: Si ves este error, el servicio puede estar experimentando problemas. Verifica la página de estado o contacta al soporte.

**Para Usuarios de Auto-Alojamiento**:

**Posibles causas:**

1. El backend de PocketBase no está en ejecución o no es accesible
2. La variable de entorno `VITE_POCKETBASE_URL` es incorrecta
3. Problemas de CORS (el backend no permite el dominio del frontend)

**Soluciones:**

- Verifica que el backend esté en ejecución y sea accesible
- Comprueba la URL en tus variables de entorno
- Configura las opciones de CORS en PocketBase para permitir tu dominio del frontend

### Los cambios no se reflejan después de recompilar

**Solo Para Auto-Alojamiento**:

**Posibles causas:**

1. Caché del navegador
2. Caché de CDN (si estás usando uno)
3. Artefactos de compilación no desplegados correctamente

**Soluciones:**

- Limpia la caché del navegador o usa el modo incógnito
- Invalida la caché del CDN si aplica
- Verifica que la carpeta de compilación fue completamente reemplazada en tu servicio de alojamiento
- Verifica la consola del navegador en busca de errores

### La autenticación de usuario no funciona

**Para Usuarios del Servicio Alojado**: Contacta al soporte si tienes problemas de autenticación.

**Para Usuarios de Auto-Alojamiento**:

**Posibles causas:**

1. La gestión de usuarios del backend de PocketBase no está configurada
2. Problemas de conectividad de red
3. URL de PocketBase incorrecta
4. Problemas de configuración de CORS

**Soluciones:**

- Verifica que el backend de PocketBase esté correctamente configurado
- Comprueba la consola del navegador en busca de mensajes de error
- Prueba los endpoints de la API del backend directamente
- Comprueba la configuración de CORS en PocketBase

### La aplicación es lenta o no responde

**Posibles causas:**

1. Conjuntos de datos de territorio grandes
2. Latencia de red
3. Recursos del servidor insuficientes (auto-alojamiento)
4. Problemas de rendimiento del navegador

**Soluciones:**

- Comprueba tu conexión a internet
- Prueba usando un navegador diferente
- Para auto-alojamiento: Optimiza los datos del territorio (reduce campos innecesarios)
- Para auto-alojamiento: Usa una región de alojamiento más cercana
- Para auto-alojamiento: Actualiza los recursos del servidor si es necesario
- Limpia la caché y los datos del navegador
- Comprueba la consola del navegador en busca de advertencias de rendimiento

## Contribuciones

### ¿Cómo reporto un error?

1. Verifica si el error ya está reportado en [GitHub Issues](https://github.com/rimorin/ministry-mapper-v2/issues)
2. Si no, crea un nuevo issue con:
   - Descripción clara del problema
   - Pasos para reproducirlo
   - Comportamiento esperado vs. real
   - Información del navegador y dispositivo
   - Capturas de pantalla o mensajes de error
   - Configuración de tu entorno (sin datos sensibles)

### ¿Puedo solicitar características?

¡Sí! Crea una solicitud de característica en GitHub con:

- Descripción clara de la característica
- Caso de uso y beneficios
- Cómo funcionaría
- Quién se beneficiaría

Nota: Este es un proyecto mantenido por voluntarios, por lo que la implementación depende del tiempo y los recursos disponibles.

### ¿Puedo contribuir con código?

¡Por supuesto! Las contribuciones son bienvenidas:

**Cómo contribuir:**

1. Haz un fork del repositorio
2. Crea una rama de característica
3. Realiza tus cambios siguiendo el estilo de código existente
4. Escribe pruebas para la nueva funcionalidad
5. Ejecuta `npm test` y `npm run prettier` para asegurar la calidad
6. Envía un pull request con una descripción clara

**Pautas de contribución:**

- Sigue las mejores prácticas de TypeScript y React
- Usa los patrones existentes en el código base
- Actualiza la documentación si es necesario
- Mantén los cambios enfocados y mínimos
- Prueba exhaustivamente antes de enviar

Consulta CLAUDE.md para detalles de arquitectura y convenciones de codificación.

### ¿Puedo traducir la aplicación a un nuevo idioma?

¡Sí! La aplicación usa internacionalización (i18n) para traducciones:

1. Verifica el directorio `src/i18n/` en el [repositorio del frontend](https://github.com/rimorin/ministry-mapper-v2) para ver las traducciones existentes
2. Crea un nuevo archivo de idioma siguiendo la estructura existente
3. Agrega tus traducciones
4. Actualiza el componente de selector de idioma
5. Envía un pull request

### ¿Cómo actualizo a una nueva versión?

**Para Usuarios del Servicio Alojado**: ¡Las actualizaciones son automáticas. No necesitas hacer nada!

**Para Usuarios de Auto-Alojamiento**:

**Frontend:**

```bash
git pull origin main
npm install
npm run build
# Despliega la nueva compilación en tu servicio de alojamiento
```

**Backend:**
Consulta el [repositorio de Ministry Mapper BE](https://github.com/rimorin/ministry-mapper-be) para instrucciones de actualización del backend.

**Importante:**

- Siempre haz una copia de seguridad de tus datos primero
- Lee CHANGELOG.md para cambios importantes
- Prueba en un entorno de staging si es posible
- Actualiza las variables de entorno si es necesario

## Soporte

### ¿Dónde puedo obtener ayuda?

**Para Usuarios del Servicio Alojado**: Contacta al soporte a través del sitio web [ministry-mapper.com](https://ministry-mapper.com).

**Para Todos los Usuarios**:

1. **Documentación**: Consulta este sitio de documentación, incluyendo la [Guía de Inicio](getting-started.md), la [Guía de Usuario](user-guide.md) y otras guías
2. **GitHub Issues**: Busca issues existentes o crea uno nuevo en el [repositorio ministry-mapper-v2](https://github.com/rimorin/ministry-mapper-v2/issues)
3. **GitHub Discussions**: Haz preguntas y comparte ideas
4. **Revisión del Código**: Lee el código y los comentarios para detalles de implementación (desarrolladores)

### ¿Hay soporte comercial?

**Servicio Alojado**: El soporte profesional está disponible a través de [ministry-mapper.com](https://ministry-mapper.com).

**Auto-Alojamiento**: No hay soporte comercial oficial disponible para instancias auto-alojadas. Este es un proyecto de código abierto mantenido por voluntarios. Sin embargo:

- Soporte comunitario mediante GitHub
- Documentación completa
- Puedes contratar desarrolladores independientes para personalizaciones

### ¿Con qué frecuencia se actualiza la aplicación?

**Servicio Alojado**: Las actualizaciones se aplican automáticamente a medida que están disponibles.

**Auto-Alojamiento**: Las actualizaciones dependen de la disponibilidad de los voluntarios. Consulta:

- **CHANGELOG.md** para el historial de versiones
- **GitHub Releases** para las notas de versión
- **Historial de commits** para cambios recientes

### ¿Puedo personalizar Ministry Mapper para mis necesidades?

¡Sí! Es de código abierto, por lo que puedes:

- Modificar la UI (colores, diseño, marca)
- Agregar características personalizadas
- Cambiar flujos de trabajo
- Integrar con otros sistemas

**Requisitos:**

- Conocimiento de React 19, TypeScript y PocketBase
- Comprensión del código base (consulta CLAUDE.md en el repositorio)
- Prueba adecuada de tus cambios
- Considera contribuir las mejoras de vuelta al proyecto

**Nota**: Las personalizaciones requieren auto-alojamiento. El servicio alojado usa la versión estándar.

---

## ¿Todavía Tienes Preguntas?

1. **Lee la Documentación**: 
   - [Guía de Inicio](getting-started.md)
   - [Guía de Usuario](user-guide.md)
   - [Guía de Auto-Alojamiento](self-hosting.md) (si te auto-alojas)
   - [Guía de Configuración del Backend](backend-setup.md) (si te auto-alojas)
   - [Guía de Configuración del Frontend](frontend-setup.md) (si te auto-alojas)
2. **Busca** en [GitHub Issues](https://github.com/rimorin/ministry-mapper-v2/issues)
3. **Pregunta en** GitHub Discussions
4. **Contacta al Soporte** (para usuarios del servicio alojado)
5. **Abre un nuevo issue** con tu pregunta

Recuerda: Ministry Mapper es de código abierto y mantenido por voluntarios. ¡Sé paciente y respetuoso al pedir ayuda!

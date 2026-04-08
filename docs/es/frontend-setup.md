# Guía de Configuración del Frontend

!!! warning "No se Recomienda el Auto-Hospedaje"
    Esta guía es para usuarios avanzados que desean auto-hospedar Ministry Mapper. No fomentamos el auto-hospedaje debido a la complejidad técnica, la carga de mantenimiento y las responsabilidades de seguridad involucradas.

    **La mayoría de los usuarios debería**: Usar nuestro servicio alojado en **[ministry-mapper.com](https://ministry-mapper.com)**

!!! info "¿Buscando Documentación de Usuario?"
    Si es un usuario regular tratando de aprender cómo usar Ministry Mapper, consulte [Comenzar](getting-started.md) o [Guía de Usuario](user-guide.md) en su lugar.

## Descripción General

El frontend de Ministry Mapper (ministry-mapper-v2) es una aplicación web basada en React que proporciona:

- Autenticación de usuario y gestión de cuentas
- Interfaz interactiva de gestión de territorios
- Funcionalidad de mapeo interactivo
- Sincronización de datos en tiempo real
- Diseño responsive para móviles
- Soporte multiidioma (7 idiomas)
- Capacidades de Aplicación Web Progresiva

**Esta guía asume que tiene**:
- Experiencia con Node.js y npm
- Comprensión de variables de entorno
- Familiaridad con despliegue de sitios estáticos
- Conocimiento de seguridad de aplicaciones web

Para instrucciones completas de auto-hospedaje, consulte la [Guía de Auto-Hospedaje](self-hosting.md).

## Referencia Rápida

Esta página proporciona una referencia rápida para la configuración del frontend. **Para instrucciones completas de configuración, consulte la [Guía de Auto-Hospedaje](self-hosting.md).**

## Referencia Rápida

Esta página proporciona una referencia rápida para la configuración del frontend. **Para instrucciones completas de configuración, consulte la [Guía de Auto-Hospedaje](self-hosting.md).**

### Stack Tecnológico

- **React 19**: Biblioteca de UI moderna
- **TypeScript**: JavaScript con tipos
- **Vite 7**: Herramienta de construcción rápida y servidor de desarrollo
- **Bootstrap 5**: Framework CSS responsive
- **Leaflet**: Mapeo interactivo
- **PocketBase SDK**: Conexión al backend
- **Sentry**: Seguimiento de errores
- **i18next**: Soporte multiidioma
- **Vite PWA**: Características de Aplicación Web Progresiva

---

!!! note "Instrucciones Completas de Configuración"
    Las secciones a continuación proporcionan una referencia técnica para la configuración del frontend. Para instrucciones paso a paso de configuración con opciones de despliegue y solución de problemas, consulte la **[Guía de Auto-Hospedaje](self-hosting.md)**.

---

## Variables de Entorno

Cree un archivo `.env` en el directorio raíz con estas configuraciones:

### Variables Requeridas

```bash
# Entorno del Sistema - especifica el entorno de despliegue
VITE_SYSTEM_ENVIRONMENT=production

# Versión - usa automáticamente la versión de package.json
VITE_VERSION=$npm_package_version

# URL del Backend PocketBase - sin barra al final
VITE_POCKETBASE_URL=https://su-url-de-backend.com

# Páginas Legales - requeridas para producción
VITE_PRIVACY_URL=https://su-sitio.com/privacy
VITE_TERMS_URL=https://su-sitio.com/terms
VITE_ABOUT_URL=https://su-sitio.com/about
```

### Variables Opcionales

```bash
# Seguimiento de Errores (Sentry) - recomendado para producción
VITE_SENTRY_DSN=https://su_sentry_dsn@sentry.io/123456
# Tokens de Sentry en tiempo de construcción para carga de mapas de origen
SENTRY_AUTH_TOKEN=su_sentry_auth_token
SENTRY_ORG=su_sentry_org_slug
SENTRY_PROJECT=su_sentry_project_slug

# Enrutamiento y Geocodificación
VITE_OPENROUTE_API_KEY=su_clave_api_openrouteservice
VITE_LOCATIONIQ_API_KEY=su_clave_api_locationiq

# Modo de Mantenimiento - mostrar un banner de mantenimiento a los usuarios
VITE_MAINTENANCE_MODE=false
```

### Detalles de Variables

#### VITE_SYSTEM_ENVIRONMENT

- **Propósito**: Determina en qué entorno se ejecuta la aplicación
- **Valores**:
  - `local` - Desarrollo en su máquina local
  - `production` - Despliegue en producción
- **Efecto**: Afecta la configuración de seguimiento de errores de Sentry y el registro

#### VITE_VERSION

- **Propósito**: Rastrea la versión de la aplicación en informes de errores
- **Valor**: `$npm_package_version` (lee automáticamente de package.json)
- **Versión Actual**: 1.9.1 (según package.json)
- **Efecto**: Mostrado en informes de Sentry y ayuda a rastrear qué versión tiene problemas

#### VITE_POCKETBASE_URL

- **Propósito**: URL de su servidor backend PocketBase
- **Formato**: URL completa sin barra al final
- **Ejemplo**: `https://api.ministry-mapper.com` o `http://localhost:8090` para local
- **Importante**: Debe ser accesible desde donde sea que esté desplegado el frontend

#### VITE_PRIVACY_URL

- **Propósito**: Enlace a su página de política de privacidad
- **Requerido**: Sí, para cumplimiento legal (GDPR, CCPA, etc.)
- **Ejemplo**: `https://ministry-mapper.com/privacy`

#### VITE_TERMS_URL

- **Propósito**: Enlace a su página de términos de servicio
- **Requerido**: Sí, para cumplimiento legal
- **Ejemplo**: `https://ministry-mapper.com/terms`

#### VITE_ABOUT_URL

- **Propósito**: Enlace a información sobre su instancia de Ministry Mapper
- **Requerido**: No, pero recomendado
- **Ejemplo**: `https://ministry-mapper.com/about`

#### VITE_SENTRY_DSN

- **Propósito**: Envía errores de JavaScript a Sentry para monitoreo
- **Obtenerlo De**: [sentry.io](https://sentry.io) (nivel gratuito disponible)
- **Requerido**: No, pero altamente recomendado para producción
- **Beneficios**: Rastrear errores, monitorear rendimiento, recibir notificaciones de problemas
- **Nota**: Solo activo cuando VITE_SYSTEM_ENVIRONMENT es "production"

#### SENTRY_AUTH_TOKEN / SENTRY_ORG / SENTRY_PROJECT

- **Propósito**: Usado durante `npm run build` para cargar mapas de origen a Sentry para que las trazas de pila minificadas puedan resolverse de vuelta a su código fuente original
- **Requerido**: No — los mapas de origen se generan localmente si estos están ausentes, pero cargarlos a Sentry mejora enormemente la depuración de errores en producción
- **Cómo Obtenerlo**: Cree un token de autenticación en la configuración de su organización de Sentry bajo **Configuración → Tokens de Autenticación**

#### VITE_OPENROUTE_API_KEY

- **Propósito**: Potencia el enrutamiento paso a paso y direcciones en el mapa interactivo
- **Obtenerlo De**: [openrouteservice.org](https://openrouteservice.org) (nivel gratuito disponible)
- **Requerido**: No — la navegación del mapa funciona sin él, pero las direcciones no estarán disponibles

#### VITE_LOCATIONIQ_API_KEY

- **Propósito**: Geocodificación (convertir direcciones a coordenadas) y geocodificación inversa (coordenadas a direcciones)
- **Obtenerlo De**: [locationiq.com](https://locationiq.com) (nivel gratuito disponible)
- **Requerido**: No — la aplicación funciona sin él, pero la búsqueda de direcciones y auto-llenado no estarán disponibles

#### VITE_MAINTENANCE_MODE

- **Propósito**: Cuando se establece en `true`, muestra un banner de mantenimiento a los usuarios
- **Valores**: `true` o `false`
- **Predeterminado**: `false`
- **Caso de Uso**: Establecer en `true` temporalmente durante actualizaciones del backend o tiempo de inactividad planificado

## Configuración de Desarrollo Local

### Requisitos Previos

Necesita Node.js versión 24 o superior:

```bash
# Verificar su versión de Node.js
node --version
```

Si no tiene Node.js 24+, descárguelo de: [nodejs.org](https://nodejs.org)

### Pasos de Configuración

#### 1. Clonar el Repositorio

```bash
git clone https://github.com/rimorin/ministry-mapper-v2.git
cd ministry-mapper-v2
```

#### 2. Instalar Dependencias

```bash
npm install
```

Esto descarga todos los paquetes requeridos. Puede tomar unos minutos dependiendo de su conexión a internet.

#### 3. Crear Archivo de Entorno

```bash
# Copiar el archivo de entorno de ejemplo
cp .env.example .env
```

Edite `.env` con sus configuraciones:

```bash
VITE_SYSTEM_ENVIRONMENT=local
VITE_VERSION=$npm_package_version
VITE_POCKETBASE_URL=http://localhost:8090
VITE_PRIVACY_URL=http://localhost:3000/privacy
VITE_TERMS_URL=http://localhost:3000/terms
VITE_ABOUT_URL=http://localhost:3000/about
VITE_SENTRY_DSN=
VITE_OPENROUTE_API_KEY=
VITE_LOCATIONIQ_API_KEY=
VITE_MAINTENANCE_MODE=false
```

**Nota**: Necesitará un backend PocketBase ejecutándose en el puerto 8090. Consulte el repositorio ministry-mapper-be para la configuración del backend.

#### 4. Iniciar Servidor de Desarrollo

```bash
npm start
```

La aplicación se abrirá en `http://localhost:3000` (el puerto 3000 está configurado en vite.config.js).

### Características de Desarrollo

- **Hot Module Replacement (HMR)**: Los cambios aparecen instantáneamente sin recarga completa de página
- **Verificación de TypeScript**: Los errores se muestran en la terminal y la consola del navegador
- **Mapas de Origen**: La depuración es más fácil con referencias al código fuente original
- **Fast Refresh**: Los componentes de React se actualizan sin perder el estado

### Comandos de Desarrollo

```bash
# Iniciar servidor de desarrollo (puerto 3000)
npm start

# Ejecutar pruebas con Vitest
npm test

# Verificar formato de código
npm run prettier

# Auto-corregir problemas de formato
npm run prettier:fix

# Construir para producción
npm run build

# Previsualizar construcción de producción localmente
npm run serve
```

### Herramientas de Calidad de Código

**Prettier** (formato de código):

- Configurado en `.prettierrc`
- Formatea automáticamente al hacer commit mediante Husky
- Ejecutar manualmente con `npm run prettier:fix`

**ESLint** (linting de código):

- Configurado en `eslint.config.mjs`
- Reglas de React y TypeScript habilitadas
- Verifica errores comunes y problemas de calidad de código

**Husky** (hooks de git):

- Ejecuta Prettier en archivos preparados antes del commit
- Asegura formato de código consistente
- Valida mensajes de commit con commitlint

## Despliegue en Producción

### Construir para Producción

```bash
# Construir archivos de producción optimizados
npm run build
```

**Lo que sucede durante la construcción:**

- TypeScript se compila a JavaScript
- El código React se optimiza y minifica
- CSS se procesa y minifica
- Se generan mapas de origen
- El código se divide en fragmentos para carga más rápida
- La salida va al directorio `build/`
- Se genera el service worker para soporte PWA

**Salida de Construcción:**

- `build/index.html` - Archivo HTML principal
- `build/assets/` - JavaScript, CSS y otros recursos
- Los nombres de archivo incluyen hashes de contenido para caché
- El tamaño total típicamente es menor a 1MB (excluyendo dependencias externas)

### Opciones de Despliegue

El frontend de Ministry Mapper es una aplicación web estática. Puede desplegarlo en cualquier servicio de hospedaje estático.

#### Opción 1: Vercel (Recomendado)

Rápido, nivel gratuito, despliegues automáticos.

**Configuración:**

1. Crear cuenta en [vercel.com](https://vercel.com)
2. Hacer clic en "New Project"
3. Importar su repositorio de GitHub
4. Configurar:
   - Framework Preset: Vite
   - Build Command: `npm run build`
   - Output Directory: `build`
   - Install Command: `npm install`
5. Agregar variables de entorno en el panel de Vercel
6. Desplegar

**Características de Vercel:**

- Despliegues automáticos al hacer push a git
- Despliegues de vista previa para pull requests
- Dominios personalizados
- HTTPS por defecto
- CDN global
- Gratis para proyectos personales

#### Opción 2: Netlify

Despliegue simple con excelentes características.

**Configuración:**

1. Crear cuenta en [netlify.com](https://netlify.com)
2. "Add new site" → "Import an existing project"
3. Conectar repositorio de GitHub
4. Configuración de construcción:
   - Build Command: `npm run build`
   - Publish Directory: `build`
5. Agregar variables de entorno
6. Desplegar

**Características de Netlify:**

- Despliegues automáticos
- Manejo de formularios
- Funciones serverless (si es necesario)
- Dominios personalizados
- HTTPS por defecto

#### Opción 3: Cloudflare Pages

CDN rápido con nivel gratuito generoso.

**Configuración:**

1. Crear cuenta en [cloudflare.com](https://cloudflare.com)
2. Ir a Pages → "Create a project"
3. Conectar repositorio de GitHub
4. Configuración de construcción:
   - Framework preset: Vite
   - Build command: `npm run build`
   - Build output directory: `build`
5. Agregar variables de entorno
6. Desplegar

**Características de Cloudflare:**

- Red CDN global
- Protección DDoS
- Analíticas
- Web Analytics sin seguimiento del lado del cliente

#### Opción 4: AWS S3 + CloudFront

Más control, bueno para despliegues grandes.

**Configuración:**

1. Crear bucket S3
2. Habilitar hospedaje de sitio web estático
3. Cargar contenidos de `build/`
4. Crear distribución CloudFront
5. Configurar dominio personalizado
6. Configurar certificado SSL

**Consideraciones de AWS:**

- Configuración más compleja
- Pago por uso (generalmente muy barato)
- Más opciones de configuración
- Bueno para despliegues empresariales

#### Opción 5: Auto-Hospedado

Hospede en su propio servidor.

**Usando Nginx:**

```nginx
server {
    listen 80;
    server_name su-dominio.com;
    root /var/www/ministry-mapper/build;
    index index.html;

    location / {
        try_files $uri $uri/ /index.html;
    }

    # Habilitar compresión gzip
    gzip on;
    gzip_types text/plain text/css application/json application/javascript text/xml application/xml;

    # Cachear recursos estáticos
    location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg|woff|woff2)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }
}
```

**Usando Apache:**

```apache
<VirtualHost *:80>
    ServerName su-dominio.com
    DocumentRoot /var/www/ministry-mapper/build

    <Directory /var/www/ministry-mapper/build>
        Options -Indexes +FollowSymLinks
        AllowOverride All
        Require all granted

        # Habilitar React Router
        RewriteEngine On
        RewriteBase /
        RewriteRule ^index\.html$ - [L]
        RewriteCond %{REQUEST_FILENAME} !-f
        RewriteCond %{REQUEST_FILENAME} !-d
        RewriteRule . /index.html [L]
    </Directory>
</VirtualHost>
```

**Importante para Auto-Hospedaje:**

- Servir sobre HTTPS (requerido para características PWA)
- Configurar encabezados CORS apropiados si el backend está en un dominio diferente
- Configurar encabezados de caché apropiados para recursos estáticos
- Asegurar que el enrutamiento SPA funcione (todas las rutas sirven index.html)

### Variables de Entorno en Despliegue

**Importante**: Las variables de entorno deben establecerse durante el tiempo de construcción, no en tiempo de ejecución, porque Vite las inyecta en tiempo de construcción.

**Para Vercel/Netlify/Cloudflare:**

- Agregar variables de entorno en el panel de la plataforma
- Estarán disponibles durante el proceso de construcción

**Para Auto-Hospedado:**

- Establecer variables de entorno antes de ejecutar `npm run build`
- O crear archivo `.env.production` con sus configuraciones

## Configurar Sentry (Opcional pero Recomendado)

Sentry monitorea errores de JavaScript y rendimiento.

### 1. Crear Cuenta de Sentry

1. Ir a [sentry.io](https://sentry.io)
2. Registrarse (nivel gratuito generoso disponible)
3. Crear un nuevo proyecto
4. Elegir plataforma: "React"

### 2. Obtener Su DSN

1. Después de crear el proyecto, verá el DSN
2. O ir a Settings → Projects → [Su Proyecto] → Client Keys (DSN)
3. Copiar la URL del DSN

### 3. Agregar al Entorno

```bash
VITE_SENTRY_DSN=https://su_clave@su_org.sentry.io/su_project_id
```

**Nota**: Sentry solo está activo cuando `VITE_SYSTEM_ENVIRONMENT` está establecido en `production`.

### 4. Configurar Sentry (Opcional)

**Mapas de Origen**: El proceso de construcción genera automáticamente mapas de origen, que ayudan a depurar código de producción minificado.

**Alertas**: Configurar en el panel de Sentry:

- Notificaciones por correo electrónico para nuevos problemas
- Integración con Slack/Discord
- Reglas de alerta personalizadas para errores críticos
- Seguimiento de versiones

## Soporte Multiidioma

Ministry Mapper incluye 7 idiomas listos para usar.

### Idiomas Soportados

- Inglés (`en`)
- Español (`es` - Español)
- Japonés (`ja` - 日本語)
- Coreano (`ko` - 한국어)
- Chino (`zh` - 中文)
- Indonesio (`id` - Bahasa Indonesia)
- Malayo (`ms` - Bahasa Melayu)

### Cómo Funciona la Detección de Idioma

- Usa `i18next-browser-languagedetector`
- Detecta automáticamente desde la configuración del navegador en la primera visita
- Los usuarios pueden cambiar manualmente el idioma mediante el selector de idioma en la barra de navegación
- La selección se persiste en `localStorage` y tiene prioridad sobre la configuración del navegador
- Recurre al inglés si el idioma seleccionado no está soportado

### Agregar un Nuevo Idioma

1. **Crear Archivo de Traducción**

   - Ir a `src/i18n/locales/`
   - Crear nuevo archivo: `[código-de-idioma].json` (ej., `fr.json` para francés)
   - Copiar estructura de `en.json`

2. **Traducir Todas las Cadenas**

   - Traducir todos los pares clave-valor
   - Mantener la misma estructura JSON
   - Mantener variables de marcador de posición como `{{variable}}`

3. **Registrar Idioma**
   - Editar `src/i18n/index.ts`
   - Importar su archivo de traducción
   - Agregar al objeto de recursos

Ejemplo para francés:

```typescript
import fr from './locales/fr.json';

resources: {
  en: { translation: en },
  fr: { translation: fr },
  // ... otros idiomas
}
```

## Aplicación Web Progresiva (PWA)

Ministry Mapper incluye soporte PWA mediante el plugin Vite PWA.

### Características de PWA

- **Instalable**: Puede instalarse en la pantalla de inicio de móvil/escritorio
- **Recursos Sin Conexión**: Los archivos estáticos se almacenan en caché para carga más rápida
- **Service Worker**: Generado y registrado automáticamente
- **Estrategia de Caché**: StaleWhileRevalidate para recursos externos, CacheFirst para fuentes

### Configuración de PWA

Configuración en `vite.config.js`:

```javascript
VitePWA({
  registerType: "autoUpdate", // Auto-actualizar service worker
  manifest: false, // Sin manifest (puede agregarse si es necesario)
  workbox: {
    globPatterns: ["**/*.{js,css,html,ico,png,svg,woff2}"],
    skipWaiting: true, // Activar nuevo service worker inmediatamente
    clientsClaim: true, // Tomar control de clientes inmediatamente
    cleanupOutdatedCaches: true, // Eliminar cachés antiguos
  },
});
```

### Estrategia de Caché

**Recursos Externos** (ej., de assets.ministry-mapper.com):

- Estrategia: StaleWhileRevalidate
- Nombre de caché: "external-assets"
- Expiración: 7 días, máximo 50 entradas

**Fuentes**:

- Estrategia: CacheFirst
- Nombre de caché: "fonts"
- Expiración: 30 días, máximo 30 entradas

**Nota**: La aplicación requiere internet para operaciones de datos (conexión PocketBase). Solo los recursos estáticos se almacenan en caché sin conexión.

## Probar Su Despliegue

### Lista de Verificación de Funcionalidad

- [ ] La página de inicio carga correctamente
- [ ] La página de registro funciona
- [ ] La verificación de correo electrónico funciona
- [ ] La página de inicio de sesión funciona
- [ ] La autenticación OTP funciona (si está habilitada)
- [ ] El restablecimiento de contraseña funciona
- [ ] El selector de territorio se muestra (para conductores/administradores)
- [ ] Los mapas cargan y se muestran correctamente
- [ ] Puede ver territorios
- [ ] Puede actualizar el estado de direcciones
- [ ] Los enlaces de asignación funcionan
- [ ] Los enlaces expiran correctamente
- [ ] La vista móvil es responsive
- [ ] La detección de idioma funciona
- [ ] Todos los modales abren y cierran correctamente

### Verificaciones de Rendimiento

- [ ] Carga inicial de página bajo 3 segundos
- [ ] Puntuación Lighthouse > 90
- [ ] Sin errores en consola
- [ ] Las imágenes cargan rápidamente
- [ ] Los mapas son responsivos
- [ ] Desplazamiento y navegación suaves

### Verificaciones de Seguridad

- [ ] HTTPS está aplicado (sin acceso HTTP)
- [ ] El enlace de política de privacidad funciona
- [ ] El enlace de términos de servicio funciona
- [ ] Las claves API no están expuestas en el código del navegador
- [ ] CORS configurado correctamente para el backend
- [ ] La conexión PocketBase es segura

### Pruebas de Navegador

Probar en múltiples navegadores:

- [ ] Chrome/Chromium
- [ ] Firefox
- [ ] Safari (macOS/iOS)
- [ ] Edge
- [ ] Navegadores móviles

## Solución de Problemas

### Conexión al Backend Fallida

**Problema**: "Cannot connect to server" o "Network Error"

**Soluciones:**

- Verificar que `VITE_POCKETBASE_URL` sea correcto (sin barra al final)
- Verificar si el backend PocketBase está ejecutándose
- Probar la URL del backend directamente en el navegador
- Verificar configuración de CORS en PocketBase (debe permitir su dominio de frontend)
- Abrir DevTools del navegador → pestaña Network para ver el error exacto
- Asegurar que el backend sea accesible desde su ubicación de despliegue

### Errores de Construcción

**Problema**: `npm run build` falla

**Soluciones:**

- Asegurar que todas las variables de entorno estén configuradas correctamente
- Ejecutar `npm install` para actualizar dependencias
- Eliminar `node_modules` y reinstalar: `rm -rf node_modules && npm install`
- Verificar versión de Node.js: `node --version` (necesita 24+)
- Verificar errores de TypeScript: Ver mensajes de error específicos
- Probar `npm run prettier:fix` para corregir problemas de formato
- Limpiar caché de Vite: `rm -rf node_modules/.vite`

### Errores de TypeScript

**Problema**: Errores de tipo durante la construcción

**Soluciones:**

- Verificar `src/utils/interface.ts` para definiciones de tipos
- Asegurar que las importaciones sean correctas
- Ejecutar `npx tsc --noEmit` para ver todos los errores de tipo
- Verificar que todas las dependencias estén instaladas

### Rendimiento Lento

**Problema**: La aplicación es lenta o con lag

**Soluciones:**

- Verificar velocidad de conexión a internet
- Limpiar caché del navegador y recargar
- Asegurar que el backend PocketBase esté respondiendo rápidamente
- Verificar consola del navegador para errores de JavaScript
- Probar en diferente dispositivo/navegador para aislar el problema
- Verificar puntuación de rendimiento de Lighthouse
- Verificar que el service worker esté funcionando: DevTools → Application → Service Workers

### Problemas de Instalación de PWA

**Problema**: "Agregar a pantalla de inicio" no aparece

**Soluciones:**

- Debe servirse sobre HTTPS
- Verificar registro de service worker en DevTools
- Verificar que se cumplan los requisitos de PWA (manifest, service worker, etc.)
- Probar diferentes navegadores (Safari, Chrome tienen diferente soporte de PWA)

### Actualizaciones en Tiempo Real No Funcionan

**Problema**: Los cambios no aparecen para otros usuarios

**Soluciones:**

- Verificar que las suscripciones en tiempo real de PocketBase estén funcionando
- Verificar consola del navegador para errores de SSE (Server-Sent Events)
- Asegurar que el endpoint SSE del backend sea accesible
- Verificar si múltiples usuarios están realmente viendo el mismo territorio
- Actualizar la página para forzar reconexión

## Mantenimiento

### Mantener Dependencias Actualizadas

```bash
# Obtener últimos cambios del repositorio
git pull origin main

# Instalar dependencias actualizadas
npm install

# Verificar vulnerabilidades de seguridad
npm audit

# Corregir automáticamente problemas de seguridad (cuando sea posible)
npm audit fix

# Verificar paquetes desactualizados
npm outdated

# Actualizar paquete específico
npm update nombre-del-paquete
```

### Monitoreo

**Panel de Sentry:**

- Verificar semanalmente para nuevos errores
- Revisar tendencias de errores
- Corregir problemas críticos prontamente
- Configurar alertas para errores de alta prioridad

**Consola de Google Cloud:**

- Monitorear uso de API para mantenerse dentro de la cuota
- Verificar picos inusuales en el uso
- Revisar alertas de facturación
- Asegurar que las APIs permanezcan habilitadas

**Rendimiento:**

- Ejecutar auditorías de Lighthouse periódicamente
- Monitorear tiempos de carga de página
- Verificar Core Web Vitals
- Probar en conexiones lentas

**Retroalimentación de Usuarios:**

- Escuchar a los miembros de la congregación
- Rastrear problemas comunes
- Documentar soluciones
- Actualizar documentación según sea necesario

### Actualizaciones de Versión

Ministry Mapper usa versionado semántico:

- **Versión mayor** (x.0.0): Cambios que rompen compatibilidad
- **Versión menor** (1.x.0): Nuevas características
- **Versión de parche** (1.9.x): Correcciones de errores

## Próximos Pasos

**Para Usuarios:**

- Leer la [Guía de Usuario](user-guide.md) para aprender cómo usar la aplicación

**Para Administradores:**

- Configurar el backend PocketBase (ver repositorio ministry-mapper-be)
- Configurar ajustes de congregación
- Invitar usuarios y asignar roles
- Crear territorios y agregar direcciones

# Auto-Hospedaje de Ministry Mapper

!!! warning "No se Recomienda el Auto-Hospedaje"
    El auto-hospedaje de Ministry Mapper requiere experiencia técnica significativa y mantenimiento continuo. **Recomendamos usar nuestro servicio alojado en [ministry-mapper.com](https://ministry-mapper.com) en su lugar.** Solo proceda con el auto-hospedaje si tiene:

    - Administradores de sistemas o desarrolladores experimentados
    - Recursos para mantenimiento y actualizaciones continuas
    - Comprensión de mejores prácticas de seguridad
    - Capacidad para cumplir con regulaciones de privacidad
    - Requisitos específicos que no pueden satisfacerse con el servicio alojado

## Por Qué No Fomentamos el Auto-Hospedaje

Aunque Ministry Mapper es de código abierto, el auto-hospedaje viene con desafíos:

- **Complejidad Técnica**: Requiere conocimiento de Docker, bases de datos, servidores web e infraestructura en la nube
- **Responsabilidad de Seguridad**: Usted es responsable de mantener todo seguro y actualizado
- **Carga de Mantenimiento**: Actualizaciones regulares, copias de seguridad y monitoreo son esenciales
- **Cumplimiento de Privacidad**: Debe asegurar el cumplimiento de GDPR, CCPA y otras leyes de privacidad
- **Costo**: Los costos de servidor, costos de API e inversión de tiempo a menudo exceden las tarifas del servicio alojado
- **Soporte**: Soporte comunitario limitado para problemas de auto-hospedaje

## Requisitos Previos para Auto-Hospedaje

Si ha decidido auto-hospedar a pesar de nuestras recomendaciones, asegúrese de tener:

### Requisitos Técnicos
- Experiencia con administración de servidores Linux
- Comprensión de Docker y contenedorización
- Familiaridad con variables de entorno y configuración
- Conocimiento de configuración de servidores web (Nginx/Apache)
- Experiencia con certificados SSL/TLS
- Comprensión de DNS y configuración de dominios

### Requisitos de Infraestructura
- Servicio de hospedaje en la nube (Railway, Render, DigitalOcean, AWS, etc.)
- Nombre de dominio para su instancia
- Servicio de correo electrónico para notificaciones (opcional)
- Presupuesto para costos opcionales de API (Sentry, etc.)

### Compromiso de Tiempo
- Configuración inicial: 2-4 horas
- Mantenimiento continuo: 2-4 horas por mes
- Soporte de emergencia cuando surjan problemas

## Descripción General de la Arquitectura

Ministry Mapper consiste en dos componentes separados:

1. **Backend (ministry-mapper-be)**: Servidor PocketBase con extensiones Go
   - Repositorio: [github.com/rimorin/ministry-mapper-be](https://github.com/rimorin/ministry-mapper-be)
   - Tecnología: Go, PocketBase, SQLite
   - Se ejecuta en el puerto 8080 (Docker) o 8090 (local)

2. **Frontend (ministry-mapper-v2)**: Aplicación web React
   - Repositorio: [github.com/rimorin/ministry-mapper-v2](https://github.com/rimorin/ministry-mapper-v2)
   - Tecnología: React, TypeScript, Vite
   - Archivos estáticos desplegados en servicio de hospedaje

Ambos deben desplegarse y configurarse para trabajar juntos.

## Guía de Auto-Hospedaje del Backend

### Configuración de Entorno

Cree un archivo `.env` con estas configuraciones:

```bash
# Configuración de Aplicación
PB_APP_NAME=Ministry Mapper
PB_APP_URL=https://su-dominio-frontend.com

# Cuenta de Administrador Inicial (¡cambie la contraseña después del primer inicio de sesión!)
PB_ADMIN_EMAIL=admin@su-dominio.com
PB_ADMIN_PASSWORD=cambie_esta_contraseña_segura

# Configuración de Correo Electrónico (SMTP)
PB_SMTP_HOST=smtp.gmail.com
PB_SMTP_PASSWORD=su_contraseña_de_aplicación
PB_SMTP_USERNAME=su_correo@gmail.com
PB_SMTP_PORT=587
PB_SMTP_SENDER_ADDRESS=noreply@su-dominio.com
PB_SMTP_SENDER_NAME=Ministry Mapper

# Configuración de Seguridad
PB_ALLOW_ORIGINS=https://su-dominio-frontend.com
PB_HIDE_CONTROLS=true
PB_ENABLE_RATE_LIMITING=true

# Autenticación (opcional)
PB_OTP_ENABLED=false
PB_MFA_ENABLED=false

# Seguimiento de Errores (opcional pero recomendado)
SENTRY_DSN=su_sentry_dsn
SENTRY_ENV=production
SOURCE_COMMIT=

# Integración de Servicios (opcional)
MAILERSEND_API_KEY=
MAILERSEND_FROM_EMAIL=
LAUNCHDARKLY_SDK_KEY=
LAUNCHDARKLY_CONTEXT_KEY=
```

### Despliegue con Docker

1. **Clonar el Repositorio**
```bash
git clone https://github.com/rimorin/ministry-mapper-be.git
cd ministry-mapper-be
```

2. **Construir Imagen Docker**
```bash
docker build -t ministry-mapper-backend .
```

3. **Ejecutar Contenedor**
```bash
docker run -d \
  -p 8080:8080 \
  -v /ruta/a/pb_data:/app/pb_data \
  --env-file .env \
  --name ministry-mapper \
  ministry-mapper-backend
```

**Notas Importantes:**
- El contenedor se ejecuta en el puerto 8080 internamente
- Monte un volumen persistente en `/app/pb_data` para preservar los datos
- Su base de datos vive en la carpeta `pb_data`

4. **Acceder al Panel de Administración**
Navegue a `https://su-dominio-backend.com/_/` e inicie sesión con sus credenciales de administrador.

### Desarrollo Local

Para pruebas en su máquina local:

```bash
# Clonar repositorio
git clone https://github.com/rimorin/ministry-mapper-be.git
cd ministry-mapper-be

# Instalar dependencias
./scripts/install.sh

# Configurar entorno
cp .env.sample .env
# Editar .env con sus configuraciones

# Iniciar aplicación
./scripts/start.sh
```

El backend se ejecuta en `http://localhost:8090` por defecto.

### Copias de Seguridad de Base de Datos

Sus datos son críticos. Configure copias de seguridad automatizadas:

```bash
# Copia de seguridad manual
tar -czf backup-$(date +%Y%m%d).tar.gz /ruta/a/pb_data/

# Copia de seguridad diaria automatizada (agregar a crontab)
0 2 * * * tar -czf /backups/ministry-mapper-$(date +\%Y\%m\%d).tar.gz /ruta/a/pb_data/
```

### Lista de Verificación de Seguridad

- [ ] Cambiar contraseña de administrador predeterminada inmediatamente
- [ ] Usar solo HTTPS (nunca HTTP)
- [ ] Establecer `PB_ALLOW_ORIGINS` a un dominio específico (no `*`)
- [ ] Habilitar `PB_HIDE_CONTROLS=true`
- [ ] Habilitar `PB_ENABLE_RATE_LIMITING=true`
- [ ] Configurar firewall para restringir acceso
- [ ] Configurar copias de seguridad automatizadas
- [ ] Configurar Sentry para monitoreo de errores
- [ ] Usar contraseñas fuertes y únicas
- [ ] Mantener dependencias actualizadas
- [ ] Revisar mejores prácticas de seguridad de PocketBase

## Guía de Auto-Hospedaje del Frontend

### Configuración de Entorno

Cree un archivo `.env`:

```bash
# Entorno del Sistema
VITE_SYSTEM_ENVIRONMENT=production

# Versión
VITE_VERSION=$npm_package_version

# URL del Backend PocketBase (sin barra al final)
VITE_POCKETBASE_URL=https://su-dominio-backend.com

# Páginas Legales (requeridas)
VITE_PRIVACY_URL=https://su-dominio.com/privacy
VITE_TERMS_URL=https://su-dominio.com/terms
VITE_ABOUT_URL=https://su-dominio.com/about

# Seguimiento de Errores (opcional pero recomendado)
VITE_SENTRY_DSN=su_sentry_dsn
```

### Construir para Producción

```bash
# Clonar repositorio
git clone https://github.com/rimorin/ministry-mapper-v2.git
cd ministry-mapper-v2

# Instalar dependencias
npm install

# Construir
npm run build
```

La salida estará en el directorio `build/`.

### Opciones de Despliegue

#### Servicios de Hospedaje Estático (Más Fácil)

**Vercel:**
1. Importar repositorio de GitHub
2. Framework: Vite
3. Comando de construcción: `npm run build`
4. Directorio de salida: `build`
5. Agregar variables de entorno

**Netlify:**
1. Nuevo sitio desde Git
2. Comando de construcción: `npm run build`
3. Directorio de publicación: `build`
4. Agregar variables de entorno

**Cloudflare Pages:**
1. Crear proyecto Pages
2. Framework: Vite
3. Comando de construcción: `npm run build`
4. Directorio de salida: `build`
5. Agregar variables de entorno

#### Servidor Web Auto-Hospedado

**Configuración de Nginx:**

```nginx
server {
    listen 443 ssl http2;
    server_name su-dominio.com;

    ssl_certificate /ruta/al/cert.pem;
    ssl_certificate_key /ruta/a/key.pem;

    root /var/www/ministry-mapper/build;
    index index.html;

    location / {
        try_files $uri $uri/ /index.html;
    }

    # Caché
    location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg|woff|woff2)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }

    # Encabezados de seguridad
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header X-XSS-Protection "1; mode=block" always;
}
```

**Configuración de Apache:**

```apache
<VirtualHost *:443>
    ServerName su-dominio.com
    DocumentRoot /var/www/ministry-mapper/build

    SSLEngine on
    SSLCertificateFile /ruta/al/cert.pem
    SSLCertificateKeyFile /ruta/a/key.pem

    <Directory /var/www/ministry-mapper/build>
        Options -Indexes +FollowSymLinks
        AllowOverride All
        Require all granted

        RewriteEngine On
        RewriteBase /
        RewriteRule ^index\.html$ - [L]
        RewriteCond %{REQUEST_FILENAME} !-f
        RewriteCond %{REQUEST_FILENAME} !-d
        RewriteRule . /index.html [L]
    </Directory>
</VirtualHost>
```

### Configuración de Sentry (Opcional)

1. Crear cuenta en [sentry.io](https://sentry.io)
2. Crear proyecto React
3. Obtener DSN
4. Agregar a `.env` como `VITE_SENTRY_DSN`

## Mantenimiento y Actualizaciones

### Tareas de Mantenimiento Regular

**Semanalmente:**
- Verificar Sentry para errores
- Revisar registros de la aplicación
- Verificar que las copias de seguridad estén funcionando

**Mensualmente:**
- Actualizar dependencias: `npm install` y `go get -u`
- Revisar avisos de seguridad
- Probar procedimientos de recuperación ante desastres
- Verificar uso de espacio en disco
- Revisar retroalimentación de usuarios

**Trimestralmente:**
- Auditoría de seguridad
- Revisión de rendimiento
- Actualizar documentación
- Revisar y actualizar política de privacidad
- Probar todas las características exhaustivamente

### Actualización a Nuevas Versiones

**Actualización del Backend:**
```bash
cd ministry-mapper-be
git pull origin main
docker build -t ministry-mapper-backend .
docker stop ministry-mapper
docker rm ministry-mapper
docker run -d \
  -p 8080:8080 \
  -v /ruta/a/pb_data:/app/pb_data \
  --env-file .env \
  --name ministry-mapper \
  ministry-mapper-backend
```

**Actualización del Frontend:**
```bash
cd ministry-mapper-v2
git pull origin main
npm install
npm run build
# Desplegar carpeta build/ a su servicio de hospedaje
```

**¡Siempre haga copia de seguridad antes de actualizar!**

### Monitoreo

**Qué Monitorear:**
- Tiempo de actividad y salud del servidor
- Tasas de error de la aplicación (Sentry)
- Crecimiento del tamaño de la base de datos
- Éxito/fallo de copias de seguridad
- Expiración de certificado SSL
- Problemas de autenticación de usuarios
- Métricas de rendimiento

**Herramientas:**
- Sentry para seguimiento de errores
- Consola de Google Cloud para uso de API
- Monitoreo de servidor (Prometheus, Grafana, etc.)
- Monitores de tiempo de actividad (UptimeRobot, Pingdom)
- Agregación de registros (stack ELK, CloudWatch)

## Solución de Problemas de Instancias Auto-Hospedadas

### Problemas del Backend

**Puerto Ya en Uso:**
```bash
lsof -i :8080
kill -9 <process_id>
```

**Base de Datos Bloqueada:**
- Detener todas las instancias que acceden a la base de datos
- Asegurar que solo un proceso de backend esté ejecutándose
- Verificar procesos colgados

**El Correo Electrónico No Se Envía:**
- Verificar credenciales SMTP
- Verificar que el firewall no esté bloqueando puertos SMTP
- Para Gmail, usar Contraseñas de Aplicación
- Revisar registros de correo electrónico en `pb_data/logs/`

**No Puede Acceder al Panel de Administración:**
- La URL debe ser `https://su-dominio.com/_/` (note el guion bajo)
- Verificar configuración de `PB_HIDE_CONTROLS`
- Verificar que el backend esté ejecutándose
- Limpiar caché del navegador

### Problemas del Frontend

**No Puede Conectar al Backend:**
- Verificar que `VITE_POCKETBASE_URL` sea correcto
- Verificar configuración de CORS en el backend
- Asegurar que el backend sea accesible
- Verificar consola del navegador para errores

**Fallos de Construcción:**
- Asegurar versión de Node.js 22+
- Eliminar `node_modules` y reinstalar
- Verificar que todas las variables de entorno estén configuradas
- Revisar mensajes de error de construcción

### Problemas de Rendimiento

**Tiempos de Respuesta Lentos:**
- Verificar recursos del servidor (CPU, RAM, disco)
- Revisar tamaño de base de datos y consultas
- Habilitar optimizaciones de base de datos
- Verificar latencia de red
- Revisar registros de PocketBase para consultas lentas

## Cumplimiento de Privacidad y Legal

### Sus Responsabilidades como Auto-Hospedador

Al auto-hospedar, usted es el controlador de datos y debe:

- **Cumplir con Leyes de Privacidad**: GDPR (UE), CCPA (California), etc.
- **Crear Política de Privacidad**: Explicar recopilación y uso de datos
- **Crear Términos de Servicio**: Definir responsabilidades del usuario
- **Implementar Seguridad de Datos**: Encriptación, controles de acceso, copias de seguridad
- **Manejar Solicitudes de Datos**: Derechos de acceso, eliminación y portabilidad del usuario
- **Reportar Violaciones de Datos**: Dentro de los plazos legales
- **Mantener Registros**: Actividades de procesamiento de datos
- **Nombrar DPO**: Si lo requieren las regulaciones

### Documentos Legales Requeridos

Debe proporcionar:

1. **Política de Privacidad** - Requerida por ley, debe explicar:
   - Qué datos recopila
   - Cómo los usa
   - Cuánto tiempo los mantiene
   - Derechos del usuario
   - Cómo contactarlo

2. **Términos de Servicio** - Define:
   - Uso aceptable
   - Responsabilidades del usuario
   - Limitaciones de responsabilidad
   - Políticas de terminación

3. **Política de Cookies** - Si usa cookies/seguimiento

### Medidas de Protección de Datos

- Usar HTTPS en todas partes
- Encriptar datos en reposo
- Implementar controles de acceso
- Auditorías de seguridad regulares
- Encriptación de copias de seguridad
- Políticas de contraseñas seguras
- Autenticación multifactor
- Registro de actividad
- Actualizaciones de seguridad regulares

## Consideraciones de Costo

### Costos de Infraestructura

**Hospedaje del Backend:**
- VPS: $10-50/mes (DigitalOcean, Linode)
- PaaS: $10-30/mes (Railway, Render)
- Nube: Variable (AWS, GCP)

**Hospedaje del Frontend:**
- Hospedaje estático: $0-10/mes (Vercel, Netlify, Cloudflare)
- Costos de CDN: Usualmente incluidos

**Dominio:**
- $10-20/año

**Certificado SSL:**
- Gratis (Let's Encrypt) o $0-100/año

### Costos de Servicios

**Servicio de Correo Electrónico:**
- Gmail SMTP: Gratis (limitado)
- MailerSend: Nivel gratuito disponible
- SendGrid: Nivel gratuito disponible

**Seguimiento de Errores:**
- Sentry: Nivel gratuito disponible
- Pago: $26-80/mes

**Almacenamiento de Copias de Seguridad:**
- $5-20/mes dependiendo del tamaño

### Inversión de Tiempo

**Configuración Inicial:**
- Backend: 2-3 horas
- Frontend: 1-2 horas
- Configuración: 1-2 horas
- Pruebas: 2-3 horas
- **Total: 6-10 horas**

**Mantenimiento Mensual:**
- Monitoreo: 1-2 horas
- Actualizaciones: 1-2 horas
- Soporte: 1-3 horas
- **Total: 3-7 horas**

### Costo Total de Propiedad (Anual)

**Congregación pequeña (< 100 usuarios):**
- Infraestructura: $240-720
- APIs: $0-100 (usualmente nivel gratuito)
- Servicios: $0-300
- Dominio/SSL: $10-20
- **Total: $250-1,140 + 48-96 horas/año**

Compare esto con una suscripción de servicio alojado antes de comprometerse.

## Obtener Ayuda

### Soporte de la Comunidad

- GitHub Issues: [ministry-mapper-v2](https://github.com/rimorin/ministry-mapper-v2/issues) y [ministry-mapper-be](https://github.com/rimorin/ministry-mapper-be/issues)
- Lea los issues existentes antes de crear nuevos
- Proporcione información detallada al reportar problemas

### Qué Incluir en Solicitudes de Soporte

1. Números de versión (backend y frontend)
2. Entorno de despliegue (Docker, local, servicio de hospedaje)
3. Mensajes de error (texto completo)
4. Pasos para reproducir
5. Comportamiento esperado vs real
6. Registros relevantes
7. Configuración (sanitizada, sin secretos)

### Soporte Limitado

!!! info "Limitaciones de Soporte"
    El soporte de auto-hospedaje es basado en la comunidad y limitado. El equipo de Ministry Mapper prioriza el servicio alojado. Para soporte profesional, considere usar el servicio alojado en su lugar.

## Alternativas al Auto-Hospedaje

Antes de auto-hospedar, considere:

1. **Usar el Servicio Alojado Oficial en [ministry-mapper.com](https://ministry-mapper.com)**
   - Sin carga de mantenimiento
   - Soporte profesional
   - Actualizaciones automáticas
   - Mejor confiabilidad
   - Cumplimiento manejado por usted
   - ¡Disponible ahora!

2. **Solicitar Características**
   - Si necesita personalización, solicite características en el proyecto principal
   - Beneficia a todos
   - Mantenido por el equipo
   - Puede agregarse al servicio alojado

3. **Contribuir al Proyecto**
   - Mejorar el código base principal
   - Ayudar a otros
   - Obtener soporte de la comunidad

## Conclusión

El auto-hospedaje de Ministry Mapper es posible pero requiere experiencia, tiempo y recursos significativos. **Recomendamos encarecidamente usar el servicio alojado en [ministry-mapper.com](https://ministry-mapper.com)** a menos que tenga requisitos específicos que justifiquen la complejidad adicional.

### Use el Servicio Alojado en su Lugar

Visite **[ministry-mapper.com](https://ministry-mapper.com)** para:
- Configuración instantánea - no se requiere conocimiento técnico
- Soporte profesional cuando lo necesite
- Copias de seguridad automáticas y actualizaciones de seguridad
- Asistencia de cumplimiento legal
- Mejor confiabilidad y tiempo de actividad
- Menor costo total de propiedad

### ¿Aún Quiere Auto-Hospedar?

Si procede con el auto-hospedaje:
- Siga todas las mejores prácticas de seguridad
- Mantenga los sistemas actualizados
- Mantenga copias de seguridad regulares
- Asegure cumplimiento legal
- Monitoree la salud del sistema
- Presupueste para costos continuos
- Asigne tiempo para mantenimiento

Asegúrese de haber leído toda esta guía y comprender las implicaciones. ¡Buena suerte!

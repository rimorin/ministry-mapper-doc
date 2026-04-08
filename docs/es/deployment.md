# Guía de Despliegue

## Descripción General

Esta guía cubre varias opciones de despliegue para Ministry Mapper, desde usar el servicio alojado hasta configuraciones avanzadas de auto-hospedaje.

## Árbol de Decisión de Despliegue Rápido

```
┌─────────────────────────────────────┐
│ ¿Tiene experiencia técnica          │
│ y necesidades específicas de        │
│ auto-hospedaje?                     │
└──────────────┬──────────────────────┘
               │
       ┌───────┴───────┐
       │               │
      SÍ               NO
       │               │
       │               └─────────────────┐
       │                                 │
       ▼                                 ▼
┌──────────────┐              ┌──────────────────┐
│ Auto-        │              │ Usar Servicio    │
│ Hospedaje    │              │ Alojado          │
│              │              │                  │
│ Ver Abajo    │              │ ministry-mapper  │
└──────────────┘              │ .com             │
                              └──────────────────┘
```

## Opción 1: Servicio Alojado (Recomendado)

**Mejor para:** La mayoría de las congregaciones, organizaciones pequeñas a medianas

**URL:** [ministry-mapper.com](https://ministry-mapper.com)

### Ventajas

✅ **Cero Configuración** - Comience a usar inmediatamente
✅ **Soporte Profesional** - Ayuda cuando la necesite
✅ **Actualizaciones Automáticas** - Siempre en la última versión
✅ **Infraestructura Mantenida** - Sin gestión de servidores
✅ **Mejor Seguridad** - Prácticas de seguridad profesionales
✅ **Asistencia de Cumplimiento** - Ayuda con GDPR, CCPA, etc.
✅ **Copias de Seguridad Incluidas** - Copias de seguridad de datos automáticas
✅ **Escalabilidad** - Maneja el crecimiento automáticamente
✅ **Costo-Efectivo** - A menudo más barato que el auto-hospedaje

### Comenzar

1. Visitar [ministry-mapper.com](https://ministry-mapper.com)
2. Crear una cuenta
3. Verificar su correo electrónico
4. Contactar al administrador para acceso a la congregación
5. Comenzar a usar inmediatamente

**¡No se requiere conocimiento técnico!**

---

## Opción 2: Auto-Hospedaje

**Mejor para:** Organizaciones con experiencia técnica y requisitos específicos

**Requisitos:**
- Administradores de sistemas o desarrolladores experimentados
- Comprensión de Docker, bases de datos y servidores web
- Capacidad para mantener y actualizar sistemas
- Conocimiento de mejores prácticas de seguridad
- Experiencia en cumplimiento de leyes de privacidad

### Descripción General del Auto-Hospedaje

Ministry Mapper consiste en dos componentes:

1. **Backend** - Aplicación Go con PocketBase y SQLite
2. **Frontend** - Aplicación web estática con React

Ambos deben desplegarse y conectarse.

---

## Opciones de Despliegue del Backend

### Opción 1: Railway (Recomendado para Auto-Hospedaje)

**Mejor para:** Auto-hospedaje fácil con configuración mínima

**Pasos de Configuración:**

1. **Crear Cuenta en Railway**
   ```
   Visitar: https://railway.app
   Registrarse con GitHub
   ```

2. **Desplegar Backend**
   ```bash
   # Bifurcar el repositorio
   git clone https://github.com/rimorin/ministry-mapper-be
   cd ministry-mapper-be

   # Conectar a Railway
   railway init
   railway link
   ```

3. **Configurar Entorno**
   ```bash
   # Agregar variables de entorno en el panel de Railway
   PB_APP_URL=https://su-frontend.vercel.app
   PB_ADMIN_EMAIL=admin@ejemplo.com
   PB_ADMIN_PASSWORD=contraseña_segura_aquí
   SENTRY_DSN=su_sentry_dsn
   ```

4. **Desplegar**
   ```bash
   railway up
   ```

**Características:**
- Nivel gratuito disponible
- HTTPS automático
- Escalado fácil
- Monitoreo integrado
- Despliegues con un clic

---

### Opción 2: Render

**Pasos de Configuración:**

1. **Crear Cuenta en Render**
   ```
   Visitar: https://render.com
   Registrarse
   ```

2. **Crear Nuevo Servicio Web**
   - Conectar repositorio de GitHub
   - Seleccionar `ministry-mapper-be`
   - Elegir despliegue Docker

3. **Configurar Entorno**
   ```
   PB_APP_URL=https://su-url-frontend.vercel.app
   PB_ADMIN_EMAIL=admin@ejemplo.com
   PB_ADMIN_PASSWORD=contraseña_segura
   PB_ALLOW_ORIGINS=https://su-url-frontend.vercel.app
   ```

4. **Desplegar**
   - Render construye y despliega automáticamente
   - Toma 5-10 minutos para el primer despliegue

**Características:**
- Nivel gratuito con limitaciones
- HTTPS automático
- Escalado fácil
- Integración con GitHub

---

### Opción 3: Droplet de DigitalOcean

**Mejor para:** Control total, despliegues de producción

**Pasos de Configuración:**

1. **Crear Droplet**
   ```bash
   # Ubuntu 22.04 LTS recomendado
   # Mínimo: 1GB RAM, 1 CPU, 25GB SSD
   # Recomendado: 2GB RAM, 2 CPU, 50GB SSD
   ```

2. **Instalar Docker**
   ```bash
   sudo apt update
   sudo apt install docker.io docker-compose -y
   sudo systemctl enable docker
   sudo systemctl start docker
   ```

3. **Clonar Repositorio**
   ```bash
   git clone https://github.com/rimorin/ministry-mapper-be.git
   cd ministry-mapper-be
   ```

4. **Configurar Entorno**
   ```bash
   cp .env.sample .env
   nano .env  # Editar con sus configuraciones
   ```

5. **Construir y Ejecutar**
   ```bash
   docker-compose up -d
   ```

6. **Configurar Proxy Inverso Nginx**
   ```nginx
   server {
       listen 80;
       server_name api.su-dominio.com;

       location / {
           proxy_pass http://localhost:8080;
           proxy_set_header Host $host;
           proxy_set_header X-Real-IP $remote_addr;
           proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
           proxy_set_header X-Forwarded-Proto $scheme;
       }
   }
   ```

7. **Configurar SSL con Certbot**
   ```bash
   sudo apt install certbot python3-certbot-nginx -y
   sudo certbot --nginx -d api.su-dominio.com
   ```

**Características:**
- Control total
- Precios predecibles ($6-12/mes)
- Alto rendimiento
- Configuraciones personalizadas

---

### Opción 4: AWS (Avanzado)

**Mejor para:** Despliegues empresariales, necesidades de alta escalabilidad

**Arquitectura:**
```
Route 53 (DNS)
    ↓
CloudFront (CDN)
    ↓
Application Load Balancer
    ↓
ECS Fargate (Contenedor)
    ↓
RDS PostgreSQL (si migra desde SQLite)
    ↓
S3 (Almacenamiento de Archivos)
```

**Descripción General de Configuración:**

1. **Crear Clúster ECS**
2. **Construir y Subir Imagen Docker a ECR**
3. **Crear Definición de Tarea**
4. **Configurar Load Balancer**
5. **Configurar CDN CloudFront**
6. **Configurar DNS Route 53**

**Costo:** ~$50-200/mes dependiendo del uso

**Complejidad:** Alta - requiere experiencia en AWS

---

## Opciones de Despliegue del Frontend

### Opción 1: Vercel (Recomendado)

**Mejor para:** Despliegue rápido y fácil con excelente experiencia de desarrollo

**Pasos de Configuración:**

1. **Crear Cuenta en Vercel**
   ```
   Visitar: https://vercel.com
   Registrarse con GitHub
   ```

2. **Importar Repositorio**
   ```
   - Hacer clic en "New Project"
   - Importar ministry-mapper-v2
   - Framework: Vite
   - Build Command: npm run build
   - Output Directory: build
   ```

3. **Configurar Variables de Entorno**
   ```bash
   VITE_SYSTEM_ENVIRONMENT=production
   VITE_VERSION=$npm_package_version
   VITE_OPENROUTE_API_KEY=su_clave
   VITE_LOCATIONIQ_API_KEY=su_clave
   VITE_POCKETBASE_URL=https://su-url-de-backend.com
   VITE_PRIVACY_URL=https://su-sitio.com/privacy
   VITE_TERMS_URL=https://su-sitio.com/terms
   VITE_SENTRY_DSN=su_sentry_dsn
   ```

4. **Desplegar**
   - Vercel despliega automáticamente al hacer push
   - Toma 2-3 minutos

**Características:**
- Gratis para proyectos personales
- HTTPS automático
- Red de borde (rápido globalmente)
- Despliegues de vista previa para PRs
- Dominios personalizados

---

### Opción 2: Netlify

**Pasos de Configuración:**

1. **Crear Cuenta en Netlify**
2. **Importar Repositorio**
   - Conectar GitHub
   - Seleccionar `ministry-mapper-v2`
3. **Configuración de Construcción**
   ```
   Build Command: npm run build
   Publish Directory: build
   ```
4. **Agregar Variables de Entorno**
5. **Desplegar**

**Características:**
- Nivel gratuito
- HTTPS automático
- Manejo de formularios
- Funciones serverless

---

### Opción 3: Cloudflare Pages

**Pasos de Configuración:**

1. **Crear Cuenta en Cloudflare**
2. **Crear Proyecto Pages**
   - Conectar GitHub
   - Seleccionar repositorio
3. **Configuración de Construcción**
   ```
   Framework: Vite
   Build Command: npm run build
   Output Directory: build
   ```
4. **Variables de Entorno**
5. **Desplegar**

**Características:**
- Ancho de banda ilimitado
- CDN global
- Protección DDoS
- Web Analytics

---

### Opción 4: AWS S3 + CloudFront

**Mejor para:** Despliegues centrados en AWS

**Pasos de Configuración:**

1. **Crear Bucket S3**
   ```bash
   aws s3 mb s3://ministry-mapper-frontend
   ```

2. **Configurar Hospedaje Estático**
   ```bash
   aws s3 website s3://ministry-mapper-frontend \
     --index-document index.html \
     --error-document index.html
   ```

3. **Subir Construcción**
   ```bash
   npm run build
   aws s3 sync build/ s3://ministry-mapper-frontend
   ```

4. **Crear Distribución CloudFront**
   - Origen: bucket S3
   - Protocolo del Visor: Redirigir HTTP a HTTPS
   - Clase de Precio: Usar todas las ubicaciones de borde

5. **Configurar Dominio Personalizado**

**Costo:** ~$1-5/mes para sitios pequeños

---

## Configuración de Entorno

### Variables de Entorno del Backend

**Requeridas:**
```bash
PB_APP_NAME=Ministry Mapper
PB_APP_URL=https://su-url-frontend.com
PB_ADMIN_EMAIL=admin@ejemplo.com
PB_ADMIN_PASSWORD=contraseña_segura
PB_ALLOW_ORIGINS=https://su-url-frontend.com
```

**SMTP (Correo Electrónico):**
```bash
PB_SMTP_HOST=smtp.gmail.com
PB_SMTP_PORT=587
PB_SMTP_USERNAME=su_correo@gmail.com
PB_SMTP_PASSWORD=contraseña_de_aplicación
PB_SMTP_SENDER_ADDRESS=noreply@su-dominio.com
PB_SMTP_SENDER_NAME=Ministry Mapper
```

**Seguridad:**
```bash
PB_HIDE_CONTROLS=true
PB_ENABLE_RATE_LIMITING=true
PB_OTP_ENABLED=false
PB_MFA_ENABLED=false
```

**Monitoreo:**
```bash
SENTRY_DSN=su_sentry_dsn
SENTRY_ENV=production
```

**Servicios Opcionales:**
```bash
MAILERSEND_API_KEY=su_clave
LAUNCHDARKLY_SDK_KEY=su_clave
```

### Variables de Entorno del Frontend

**Requeridas:**
```bash
VITE_SYSTEM_ENVIRONMENT=production
VITE_VERSION=$npm_package_version
VITE_POCKETBASE_URL=https://su-backend.com
```

**Páginas Legales:**
```bash
VITE_PRIVACY_URL=https://su-sitio.com/privacy
VITE_TERMS_URL=https://su-sitio.com/terms
VITE_ABOUT_URL=https://su-sitio.com/about
```

**APIs Opcionales:**
```bash
VITE_OPENROUTE_API_KEY=su_clave_openrouteservice
VITE_LOCATIONIQ_API_KEY=su_clave_locationiq
```

**Monitoreo:**
```bash
VITE_SENTRY_DSN=su_sentry_dsn
```

---

## Configuración de Certificado SSL/TLS

### Opción 1: Let's Encrypt (Gratis)

**Usando Certbot:**
```bash
sudo apt install certbot python3-certbot-nginx
sudo certbot --nginx -d su-dominio.com -d www.su-dominio.com
```

**Auto-renovación:**
```bash
sudo certbot renew --dry-run
```

### Opción 2: SSL de Cloudflare

- SSL gratis incluido con Cloudflare
- Renovación automática
- SSL Universal

### Opción 3: SSL del Proveedor de Nube

La mayoría de los proveedores de nube (Vercel, Netlify, Railway) incluyen SSL automático

---

## Estrategia de Copia de Seguridad de Base de Datos

### Copias de Seguridad Automatizadas

**Script de Copia de Seguridad Diaria:**
```bash
#!/bin/bash
DATE=$(date +%Y%m%d_%H%M%S)
BACKUP_DIR="/backups"
DB_PATH="/app/pb_data/data.db"

# Crear copia de seguridad
cp $DB_PATH $BACKUP_DIR/backup_$DATE.db

# Comprimir
gzip $BACKUP_DIR/backup_$DATE.db

# Mantener últimos 30 días
find $BACKUP_DIR -name "backup_*.gz" -mtime +30 -delete
```

**Programación Cron:**
```bash
0 2 * * * /ruta/al/script-de-backup.sh
```

### Copias de Seguridad en la Nube

**AWS S3:**
```bash
aws s3 sync /backups/ s3://su-bucket/ministry-mapper-backups/
```

**Render:**
- Copias de seguridad automáticas incluidas
- Instantáneas diarias
- Retención de 7 días

---

## Monitoreo y Registro

### Configuración de Sentry

1. Crear cuenta en [sentry.io](https://sentry.io)
2. Crear nuevo proyecto (React + Go)
3. Copiar DSN
4. Agregar a variables de entorno
5. Configurar alertas

**Beneficios:**
- Seguimiento de errores en tiempo real
- Monitoreo de rendimiento
- Seguimiento de versiones
- Retroalimentación de usuarios

### Monitoreo de Aplicación

**Métricas a Rastrear:**
- Tasa de errores
- Tiempo de respuesta
- Tamaño de base de datos
- Usuarios activos
- Llamadas API
- Uso de memoria
- Espacio en disco

**Herramientas:**
- Grafana + Prometheus
- Datadog
- New Relic
- Monitoreo del proveedor de nube

---

## Mejores Prácticas de Seguridad

### Seguridad del Backend

1. **Usar Contraseñas Fuertes**
   - Mínimo 12 caracteres
   - Mezcla de letras, números, símbolos

2. **Habilitar Limitación de Velocidad**
   ```bash
   PB_ENABLE_RATE_LIMITING=true
   ```

3. **Configurar CORS**
   ```bash
   PB_ALLOW_ORIGINS=https://su-url-frontend.com
   ```

4. **Actualizaciones Regulares**
   ```bash
   # Verificar actualizaciones mensualmente
   docker pull su-imagen:latest
   ```

5. **Reglas de Firewall**
   ```bash
   # Solo permitir puertos 80, 443, 22 (SSH)
   sudo ufw allow 80/tcp
   sudo ufw allow 443/tcp
   sudo ufw allow 22/tcp
   sudo ufw enable
   ```

### Seguridad del Frontend

1. **Variables de Entorno**
   - Nunca hacer commit de archivos `.env`
   - Usar la UI de variables de entorno de la plataforma

2. **Restricciones de Clave API**
   - Restringir claves API a APIs específicas

3. **Política de Seguridad de Contenido**
   ```html
   <meta http-equiv="Content-Security-Policy"
         content="default-src 'self';
                  script-src 'self' 'unsafe-inline';
                  style-src 'self' 'unsafe-inline';">
   ```

4. **Solo HTTPS**
   - Aplicar HTTPS en producción
   - Establecer encabezados HSTS

---

## Optimización de Rendimiento

### Optimización del Backend

1. **Habilitar Compresión Gzip**
2. **Indexación de Base de Datos** (ya configurado)
3. **Agrupación de Conexiones**
4. **Encabezados de Caché**

### Optimización del Frontend

1. **División de Código** (automático con Vite)
2. **Optimización de Imágenes**
3. **CDN para Recursos Estáticos**
4. **Caché de Service Worker**

**Objetivo de Puntuación Lighthouse:**
- Rendimiento: >90
- Accesibilidad: >95
- Mejores Prácticas: >95
- SEO: >90

---

## Consideraciones de Escalado

### Limitaciones Actuales

- SQLite escritor único
- Bueno para <1000 usuarios concurrentes
- Adecuado para congregaciones pequeñas-medianas

### Opciones de Escalado

**Escalado Vertical:**
- Aumentar recursos del servidor
- Más RAM, CPU, almacenamiento

**Escalado Horizontal:**
- Réplicas de lectura de PocketBase
- CDN para frontend
- Load balancer

**Migración de Base de Datos:**
- Migrar a PostgreSQL para mayor escala
- PocketBase soporta PostgreSQL

---

## Solución de Problemas de Despliegue

### Problemas del Backend

**Problema:** Base de datos bloqueada
**Solución:** Asegurar que solo una instancia esté ejecutándose

**Problema:** El correo electrónico no se envía
**Solución:** Verificar credenciales SMTP y puerto

**Problema:** Alto uso de memoria
**Solución:** Aumentar RAM del servidor u optimizar consultas

### Problemas del Frontend

**Problema:** La construcción falla
**Solución:** Verificar versión de Node.js (requiere 22+)

**Problema:** Errores de CORS
**Solución:** Configurar `PB_ALLOW_ORIGINS` en el backend

---

## Estimaciones de Costo

### Servicio Alojado
- Contactar ministry-mapper.com para precios
- Incluye soporte, actualizaciones, copias de seguridad

### Costos de Auto-Hospedaje

**Configuración Mínima:**
- Railway/Render: $0-5/mes (nivel gratuito)
- Vercel/Netlify: $0/mes (nivel gratuito)
- **Total: $0-5/mes**

**Configuración Recomendada:**
- Backend (Railway): $5/mes
- Frontend (Vercel): $0/mes
- Sentry: $0/mes (nivel gratuito)
- **Total: $5/mes**

**Configuración de Producción:**
- Droplet DigitalOcean: $12/mes
- CDN CloudFront: $2/mes
- Sentry Pro: $26/mes
- **Total: $40/mes**

**Configuración Empresarial:**
- Infraestructura AWS: $100-500/mes
- Contratos de soporte: Variable
- **Total: $100-500+/mes**

**Inversión de Tiempo:**
- Configuración inicial: 4-8 horas
- Mantenimiento mensual: 2-4 horas
- Soporte de emergencia: Según sea necesario

---

## Próximos Pasos

### Después del Despliegue

1. **Probar Exhaustivamente**
   - Crear cuenta de prueba
   - Crear territorio de muestra
   - Probar todas las características
   - Verificar que los correos electrónicos funcionen

2. **Capacitación de Usuarios**
   - Crear documentación
   - Capacitar administradores
   - Realizar sesiones con usuarios

3. **Monitorear el Sistema**
   - Verificar Sentry diariamente
   - Revisar registros semanalmente
   - Actualizar mensualmente

4. **Verificación de Copias de Seguridad**
   - Probar proceso de restauración
   - Verificar integridad de copias de seguridad
   - Documentar procedimientos

---

## Recursos de Soporte

### Documentación
- [Descripción General de Arquitectura](architecture.md)
- [Configuración del Backend](backend-setup.md)
- [Configuración del Frontend](frontend-setup.md)

### Comunidad
- GitHub Issues
- Sitio de documentación
- Foros de la comunidad

### Soporte Profesional
- Soporte del servicio alojado
- Consultoría disponible
- Desarrollo personalizado

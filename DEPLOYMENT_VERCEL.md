# Guía de Deployment en Vercel

Este documento explica cómo desplegar el proyecto "Limpieza Los Alcázares" en Vercel.

## Requisitos Previos

1. **Cuenta en Vercel**: Crea una cuenta en [vercel.com](https://vercel.com)
2. **Repositorio Git**: El código debe estar en GitHub, GitLab o Bitbucket
3. **Base de datos PostgreSQL**: Necesitarás una base de datos PostgreSQL (puedes usar Vercel Postgres o un servicio externo)
4. **Variables de entorno**: Prepara todas las claves API necesarias

## Paso 1: Preparar el Repositorio

### 1.1 Inicializar Git (si no lo has hecho)

```bash
cd limpieza-alcazares
git init
git add .
git commit -m "Initial commit: Limpieza Los Alcázares website"
```

### 1.2 Crear repositorio en GitHub

1. Ve a [github.com/new](https://github.com/new)
2. Crea un nuevo repositorio llamado `limpieza-alcazares`
3. Sigue las instrucciones para conectar tu repositorio local

```bash
git remote add origin https://github.com/TU_USUARIO/limpieza-alcazares.git
git branch -M main
git push -u origin main
```

## Paso 2: Configurar Base de Datos

### Opción A: Usar Vercel Postgres (Recomendado)

1. En el dashboard de Vercel, ve a **Storage** → **Create Database** → **Postgres**
2. Selecciona la región más cercana a tus usuarios
3. Copia la `DATABASE_URL` que se genera automáticamente

### Opción B: Usar base de datos externa

Si prefieres usar PostgreSQL de otro proveedor (Railway, Render, etc.):

1. Crea una base de datos PostgreSQL
2. Obtén la URL de conexión en formato: `postgresql://usuario:contraseña@host:puerto/nombre_db`
3. Guarda esta URL para el siguiente paso

## Paso 3: Desplegar en Vercel

### 3.1 Conectar repositorio

1. Ve a [vercel.com/dashboard](https://vercel.com/dashboard)
2. Haz clic en **Add New** → **Project**
3. Selecciona tu repositorio de GitHub
4. Haz clic en **Import**

### 3.2 Configurar variables de entorno

En la pantalla de configuración del proyecto, añade las siguientes variables de entorno:

#### Variables Críticas (Requeridas)

```
DATABASE_URL = postgresql://...
JWT_SECRET = (generar con: openssl rand -base64 32)
NODE_ENV = production
```

#### Variables de Manus (si usas Manus Forge)

```
BUILT_IN_FORGE_API_KEY = [tu clave API]
BUILT_IN_FORGE_API_URL = [URL de Forge]
VITE_FRONTEND_FORGE_API_KEY = [tu clave API]
VITE_FRONTEND_FORGE_API_URL = [URL de Forge]
```

#### Variables de Aplicación

```
VITE_APP_TITLE = Limpieza Los Alcázares
VITE_APP_LOGO = /logo.png
VITE_APP_ID = [tu app id]
```

#### Variables de OAuth (si usas autenticación)

```
OAUTH_SERVER_URL = [URL del servidor OAuth]
VITE_OAUTH_PORTAL_URL = [URL del portal OAuth]
OWNER_NAME = [Nombre del propietario]
OWNER_OPEN_ID = [ID de OpenID]
```

#### Variables de Analytics (opcional)

```
VITE_ANALYTICS_ENDPOINT = [URL de Umami]
VITE_ANALYTICS_WEBSITE_ID = [ID del sitio]
```

### 3.3 Configurar Build Settings

1. **Framework Preset**: Selecciona **Vite**
2. **Build Command**: `pnpm build`
3. **Output Directory**: `dist`
4. **Install Command**: `pnpm install`

### 3.4 Desplegar

Haz clic en **Deploy**. Vercel compilará y desplegará tu aplicación automáticamente.

## Paso 4: Ejecutar Migraciones de Base de Datos

Después del primer deployment, necesitas ejecutar las migraciones de Drizzle:

### Opción A: Usar Vercel CLI

```bash
# Instalar Vercel CLI
npm i -g vercel

# Conectar con tu proyecto
vercel link

# Ejecutar migraciones
vercel env pull
pnpm db:push
```

### Opción B: Ejecutar manualmente en el servidor

1. En el dashboard de Vercel, ve a **Deployments**
2. Selecciona el deployment más reciente
3. Ve a **Functions** y busca la función que ejecuta las migraciones
4. O usa la consola de Vercel para ejecutar comandos

## Paso 5: Configurar Dominio Personalizado

### 5.1 Comprar dominio (opcional)

Puedes comprar un dominio directamente en Vercel:

1. Ve a **Settings** → **Domains**
2. Haz clic en **Add Domain**
3. Selecciona **Buy New Domain**

### 5.2 Conectar dominio existente

Si ya tienes un dominio:

1. Ve a **Settings** → **Domains**
2. Haz clic en **Add Domain**
3. Ingresa tu dominio
4. Sigue las instrucciones para actualizar los DNS

## Paso 6: Configurar Redirecciones y Reescrituras

El archivo `vercel.json` ya contiene la configuración básica. Si necesitas cambios:

```json
{
  "rewrites": [
    {
      "source": "/api/(.*)",
      "destination": "/server/index.ts"
    }
  ],
  "redirects": [
    {
      "source": "/old-page",
      "destination": "/",
      "permanent": true
    }
  ]
}
```

## Paso 7: Monitoreo y Mantenimiento

### Logs en tiempo real

```bash
vercel logs
```

### Ver métricas de rendimiento

En el dashboard de Vercel → **Analytics**

### Actualizar código

```bash
git add .
git commit -m "Actualizar contenido"
git push origin main
```

Vercel automáticamente detectará los cambios y desplegará la nueva versión.

## Solución de Problemas

### Error: "DATABASE_URL not found"

- Verifica que la variable `DATABASE_URL` esté configurada en Vercel
- Asegúrate de que la base de datos sea accesible desde Vercel

### Error: "Build failed"

- Revisa los logs de build en Vercel
- Asegúrate de que todas las dependencias estén instaladas: `pnpm install`
- Verifica que el comando de build sea correcto: `pnpm build`

### Error: "Cannot find module"

- Ejecuta `pnpm install` localmente
- Verifica que `pnpm-lock.yaml` esté en el repositorio
- Limpia el cache: `pnpm store prune`

### Base de datos no se conecta

- Verifica la URL de conexión
- Asegúrate de que la base de datos permite conexiones desde Vercel
- Si usas Vercel Postgres, verifica que esté en la misma región

## Configuración Avanzada

### Usar variables de entorno secretas

```bash
# Crear archivo .env.local (no subir a Git)
echo "DATABASE_URL=postgresql://..." > .env.local

# Vercel automáticamente lo detectará en desarrollo
pnpm dev
```

### Configurar webhooks

En `vercel.json`:

```json
{
  "git": {
    "deploymentEnabled": {
      "main": true,
      "develop": false
    }
  }
}
```

### Usar Edge Functions (opcional)

Para mejor rendimiento en rutas específicas, puedes usar Edge Functions de Vercel.

## Recursos Útiles

- [Documentación oficial de Vercel](https://vercel.com/docs)
- [Guía de Vite en Vercel](https://vercel.com/guides/nextjs-alternatives)
- [Vercel Postgres](https://vercel.com/docs/storage/vercel-postgres)
- [Drizzle ORM](https://orm.drizzle.team/)

## Soporte

Si encuentras problemas:

1. Revisa los logs en Vercel Dashboard
2. Consulta la documentación oficial
3. Abre un issue en el repositorio de GitHub
4. Contacta con soporte de Vercel

---

**Última actualización**: Enero 2025
**Proyecto**: Limpieza Los Alcázares
**Versión**: 1.0.0

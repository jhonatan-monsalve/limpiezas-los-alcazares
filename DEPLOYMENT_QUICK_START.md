# Deployment Rápido en Vercel

Guía simplificada para desplegar en 5 minutos.

## Paso 1: Preparar Git (2 min)

```bash
cd limpieza-alcazares

# Inicializar repositorio
git init
git add .
git commit -m "Initial commit"

# Crear repositorio en GitHub y conectar
git remote add origin https://github.com/TU_USUARIO/limpieza-alcazares.git
git push -u origin main
```

## Paso 2: Crear Base de Datos (1 min)

Opción A - Vercel Postgres (recomendado):
1. Ve a [vercel.com/dashboard](https://vercel.com/dashboard)
2. **Storage** → **Create Database** → **Postgres**
3. Copia la `DATABASE_URL`

Opción B - Base de datos externa:
- Usa Railway, Render, Supabase, etc.
- Obtén la URL de conexión

## Paso 3: Desplegar en Vercel (2 min)

1. Ve a [vercel.com/dashboard](https://vercel.com/dashboard)
2. **Add New** → **Project**
3. Selecciona tu repositorio de GitHub
4. Configura variables de entorno:

```
DATABASE_URL = [tu URL de base de datos]
JWT_SECRET = [generar: openssl rand -base64 32]
NODE_ENV = production
VITE_APP_TITLE = Limpieza Los Alcázares
```

5. Haz clic en **Deploy**

## Paso 4: Ejecutar Migraciones (1 min)

```bash
# Instalar Vercel CLI
npm i -g vercel

# Conectar
vercel link

# Ejecutar migraciones
vercel env pull
pnpm db:push
```

## ¡Listo! 🎉

Tu sitio está en vivo en: `https://limpieza-alcazares.vercel.app`

## Próximos Pasos

- [ ] Añadir dominio personalizado en Vercel Settings
- [ ] Configurar email para notificaciones (Resend, SendGrid)
- [ ] Habilitar analytics en Vercel
- [ ] Configurar backups automáticos de base de datos

## Troubleshooting

**Error: Build failed**
```bash
# Limpiar y reinstalar
rm -rf node_modules pnpm-lock.yaml
pnpm install
pnpm build
```

**Error: DATABASE_URL not found**
- Verifica que esté en Vercel Settings → Environment Variables
- Redeploy después de añadir

**Sitio lento**
- Verifica que la base de datos esté en la misma región
- Usa Vercel Analytics para identificar cuellos de botella

---

Para más detalles, ver `DEPLOYMENT_VERCEL.md`

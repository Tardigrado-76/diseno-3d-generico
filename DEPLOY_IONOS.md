# Guía de Despliegue - Tavilga 3D en Ionos

Esta guía detalla el proceso completo para desplegar el configurador 3D de Tavilga en el hosting de Ionos.

---

## 📋 Requisitos Previos

- [x] Cuenta de Ionos activa con dominio tavilga.es configurado
- [ ] Subdominio `diseno3d.tavilga.es` creado en el panel de Ionos y apuntando a una nueva carpeta (ej. `/diseno3d/`)
- [x] Acceso FTP/SFTP o File Manager de Ionos
- [x] Node.js 18+ instalado localmente
- [x] Proyecto de Supabase `celpxryoifkgdayfrwvt` configurado

---

## 🚀 Paso 1: Compilación Local

### 1.1 Verificar Variables de Entorno

Asegúrate de que el archivo `.env.production` existe y contiene:

```env
VITE_SUPABASE_URL=https://celpxryoifkgdayfrwvt.supabase.co
VITE_SUPABASE_ANON_KEY=<tu_clave_anon>
```

### 1.2 Ejecutar Build de Producción

```powershell
cd "C:\Users\Tardigrado\Desktop\3D con BACKEND"
yarn build
```

**Resultado esperado:**
- Se crea la carpeta `dist/` con todos los archivos compilados
- No debe haber errores de TypeScript ni advertencias críticas
- Tamaño total del bundle: ~2-3 MB (gzipped: ~500-700 KB)

### 1.3 Verificar Build Localmente

```powershell
yarn preview
```

Abrir `http://localhost:4173` y verificar:
- ✅ El configurador 3D carga correctamente
- ✅ La navegación entre rutas funciona
- ✅ El guardado en Supabase funciona (requiere login)
- ✅ No hay errores en la consola del navegador

---

## 📁 Paso 2: Preparar Archivos para Ionos

### 2.1 Estructura de Archivos a Subir

Dentro de la carpeta `dist/` generada, deberías tener:

```
dist/
├── index.html
├── assets/
│   ├── index-[hash].js        # JavaScript principal
│   ├── react-vendor-[hash].js  # Dependencias React
│   ├── three-vendor-[hash].js  # Dependencias Three.js
│   ├── ui-vendor-[hash].js     # Dependencias UI
│   ├── supabase-[hash].js      # Supabase client
│   └── index-[hash].css        # Estilos
├── favicon.ico
└── .htaccess                   # Archivo de configuración Apache
```

### 2.2 Copiar .htaccess a dist/

**IMPORTANTE:** El archivo `.htaccess` debe estar en la raíz del directorio web:

```powershell
copy "C:\Users\Tardigrado\Desktop\3D con BACKEND\public\.htaccess" "C:\Users\Tardigrado\Desktop\3D con BACKEND\dist\.htaccess"
```

---

## 🌐 Paso 3: Subir a Ionos

### Opción A: Via File Manager (Recomendado para principiantes)

1. Acceder al panel de control de Ionos
2. Ir a **Hosting** → **File Manager**
3. Navegar al directorio raíz (normalmente `/httpdocs/` o `/public_html/`)
4. **Eliminar** todos los archivos existentes en ese directorio
5. **Subir** todo el contenido de la carpeta `dist/` (NO la carpeta dist en sí, sino su contenido)
6. Verificar que `.htaccess` está presente en la raíz

### Opción B: Via FTP/SFTP

1. Conectar via FileZilla o WinSCP:
   - **Host:** ftp.tavilga.es (o el proporcionado por Ionos)
   - **Usuario:** [tu usuario FTP]
   - **Contraseña:** [tu contraseña FTP]
   - **Puerto:** 21 (FTP) o 22 (SFTP)

2. Navegar al directorio `/httpdocs/` o `/`

3. **Eliminar** todos los archivos PHP/HTML del sitio anterior

4. **Subir** el contenido de `dist/`:
   ```
   Local: C:\Users\Tardigrado\Desktop\3D con BACKEND\dist\*
   Remoto: /httpdocs/
   ```

5. Asegurarse de que se sube el archivo `.htaccess` (puede estar oculto)

---

## 🔧 Paso 4: Configuración en Ionos

### 4.1 Verificar Configuración de PHP (Opcional)

Aunque la app es 100% JavaScript, Ionos podría tener PHP activado por defecto:

1. En el panel de Ionos, ir a **Hosting** → **Configuración**
2. Verificar que el directorio raíz apunta a `/httpdocs/`
3. No es necesario deshabilitar PHP, pero asegúrate de que Apache está activo

### 4.2 Configurar Dominio Principal

1. Ir a **Dominios** en el panel de Ionos
2. Asegurarse de que `tavilga.es` apunta al hosting correcto
3. Verificar que el SSL/HTTPS está activado (obligatorio para Supabase)

### 4.3 Whitelist de CORS en Supabase

1. Acceder al panel de Supabase: https://supabase.com/dashboard
2. Seleccionar el proyecto `celpxryoifkgdayfrwvt`
3. Ir a **Settings** → **API** → **URL Configuration**
4. En "Site URL", añadir: `https://diseno3d.tavilga.es`
5. En "Redirect URLs", añadir:
   - `https://diseno3d.tavilga.es/auth`
   - `https://www.diseno3d.tavilga.es/auth`
6. Guardar cambios

---

## ✅ Paso 5: Verificación Post-Despliegue

### Checklist de Funcionalidad

Visitar `https://diseno3d.tavilga.es` y verificar:

- [ ] **Homepage carga correctamente**
  - El contenido se muestra sin errores
  - Las imágenes cargan (si hay)
  - No hay mensajes de error en la consola

- [ ] **Navegación SPA funciona**
  - Click en "Empezar a Diseñar" → lleva a `/designer`
  - Refrescar la página (F5) → no muestra error 404
  - Botón "Atrás" del navegador funciona correctamente

- [ ] **Configurador 3D funciona**
  - El canvas 3D se renderiza
  - Los controles de dimensiones funcionan
  - Los materiales cambian en tiempo real
  - El precio se calcula correctamente

- [ ] **Autenticación funciona**
  - Click en "Login" abre el modal de auth
  - Puedes crear una cuenta nueva
  - Puedes iniciar sesión
  - Después de login, aparece el botón "Dashboard"

- [ ] **Guardado en Supabase funciona**
  - Estando logueado, puedes guardar un diseño
  - El diseño aparece en el Dashboard
  - Puedes cargar un diseño guardado

- [ ] **Performance**
  - Abrir DevTools → Network
  - Refrescar la página
  - **First Contentful Paint < 2s**
  - **Total Load Time < 4s** (con conexión 3G)

- [ ] **SEO**
  - Ver código fuente (Ctrl+U)
  - Verificar que los meta tags de Tavilga están presentes
  - Título: "Tavilga - Configurador 3D de Muebles a Medida"

---

## 🐛 Troubleshooting Común

### Problema: Error 404 al refrescar páginas

**Causa:** El archivo `.htaccess` no se subió o no está en la raíz

**Solución:**
1. Verificar que `.htaccess` está en `/httpdocs/.htaccess`
2. Si no aparece, puede estar oculto. Activar "Mostrar archivos ocultos" en el File Manager
3. Copiar manualmente el contenido de `public/.htaccess`

### Problema: Los archivos JS no cargan (404)

**Causa:** La ruta base en `vite.config.ts` está mal configurada

**Solución:**
1. Verificar que en `vite.config.ts` tienes: `base: '/'`
2. Recompilar: `yarn build`
3. Volver a subir

### Problema: Supabase da error CORS

**Causa:** El dominio no está en la whitelist de Supabase

**Solución:**
1. Ir a Supabase Dashboard → Settings → API
2. Añadir `https://tavilga.es` a "Site URL"
3. Esperar 5 minutos para que se propague

### Problema: Certificado SSL inválido

**Causa:** Ionos no ha generado el certificado automáticamente

**Solución:**
1. En panel de Ionos → SSL/TLS
2. Activar "Let's Encrypt" para tavilga.es
3. Esperar 10-30 minutos para la generación

### Problema: La página carga muy lento

**Causa:** El bundle es muy grande

**Solución:**
1. Verificar que `.htaccess` tiene compresión GZIP activada
2. Considerar activar CDN en Ionos (si está disponible)
3. Verificar tamaño del bundle: debería ser < 3MB total

---

## 📊 Monitorización Post-Lanzamiento

### Google Search Console

1. Añadir `https://tavilga.es` a Google Search Console
2. Verificar propiedad (método HTML tag)
3. Solicitar indexación de las páginas principales

### Analytics (Opcional)

Añadir Google Analytics 4 editando `index.html`:

```html
<script async src="https://www.googletagmanager.com/gtag/js?id=G-XXXXXXXXXX"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());
  gtag('config', 'G-XXXXXXXXXX');
</script>
```

---

## 🔐 Seguridad

### Recomendaciones Post-Despliegue

1. **No subir archivos .env al servidor**
   - Las variables ya están compiladas en el bundle
   - Solo `.env.production` se usa en el momento del build local

2. **Activar firewall de Ionos** (si está disponible)
   - Bloquear acceso a directorios internos
   - Proteger contra ataques DDoS básicos

3. **Revisar logs periódicamente**
   - Ionos → Logs → Access Logs
   - Buscar intentos de acceso sospechosos

---

## 📞 Soporte

**Ionos:** https://www.ionos.es/ayuda  
**Supabase:** https://supabase.com/docs  
**Documentación técnica:** En este repositorio (`README.md`)

---

**Última actualización:** 18 Enero 2026  
**Versión:** 1.0.0

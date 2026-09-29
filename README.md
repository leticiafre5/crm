# España Directa CRM — base servidor v1.1

Base servidor para gestión comercial y operativa: API Express, PostgreSQL, sesiones, autenticación y webhook. Incluye la interfaz local inicial; el cliente web todavía requiere conexión completa a la API antes de operar como CRM cloud.

## Requisitos
- Node.js 20 o superior
- PostgreSQL (local o Railway)

## Ejecución local
1. Descarga y descomprime el proyecto.
2. Abre Terminal y entra en la carpeta: `cd espana-directa-crm`
3. Instala dependencias: `npm install`
4. Copia `.env.example` a `.env` y completa los valores.
5. Ejecuta `npm start` y abre `http://localhost:3000`.

## Primer usuario
Configura `ADMIN_EMAIL` y `ADMIN_PASSWORD` antes del primer arranque. El usuario administrador se crea automáticamente. Por seguridad, cambia las credenciales de ejemplo y usa un `SESSION_SECRET` aleatorio.

## Despliegue en Railway
1. Crea una cuenta en GitHub y un repositorio privado.
2. En Terminal, dentro de la carpeta del proyecto, ejecuta:
   `git init`
   `git add .`
   `git commit -m "CRM inicial"`
   `git branch -M main`
   `git remote add origin https://github.com/TU-USUARIO/espana-directa-crm.git`
   `git push -u origin main`
3. En Railway crea un proyecto y selecciona **Deploy from GitHub Repo**.
4. Añade PostgreSQL desde **New → Database → PostgreSQL**.
5. En Variables, configura `DATABASE_URL` usando la referencia de Railway a la variable de conexión de PostgreSQL, `NODE_ENV=production`, `SESSION_SECRET`, `ADMIN_EMAIL` y `ADMIN_PASSWORD`.
6. Railway detectará `npm start`. Genera un dominio público desde Settings → Networking.
7. Entra en la URL y accede con el correo y contraseña configurados.

## Webhook
El endpoint público es `POST /api/webhook/lead`. Usa el header `x-webhook-token`. El token inicial se genera al arrancar y aparece en los logs; regenera uno desde el panel administrativo/API antes de conectar fuentes reales.

Ejemplo JSON: `{"nombre":"Ana Silva","email":"ana@example.com","telefono":"+34...","origen":"Web","servicio":"Residencia","notas":"Formulario web"}`.

## WordPress y Make
El endpoint acepta solicitudes JSON autenticadas mediante token. En Contact Form 7 o Make, configura una petición HTTP POST a `https://TU-DOMINIO/api/webhook/lead`, añade `Content-Type: application/json` y `x-webhook-token`. Mapea nombre, email, teléfono, origen y servicio. Prueba primero con datos ficticios. Evita enviar documentos de identidad por el webhook.

## Dominio personalizado
Añade el dominio desde Railway Networking y crea el registro DNS indicado por Railway (habitualmente CNAME para un subdominio). Espera la propagación DNS y activa HTTPS.

## Costes
Estimación orientativa del prompt: aplicación ~3 €/mes + PostgreSQL ~2 €/mes = ~5 €/mes. El coste real depende del uso, recursos, almacenamiento y tarifas vigentes de Railway; compruébalo en su panel.

## Estado de integración
La API y el esquema servidor incluyen campos ampliados de ficha y operaciones CRUD protegidas por sesión. La interfaz HTML descargable sigue siendo local y usa localStorage: todavía no sincroniza automáticamente con PostgreSQL ni presenta el formulario de acceso. No se deben considerar los registros locales migrados al servidor. La conexión completa de la interfaz, importación controlada de datos y pruebas de extremo a extremo son tareas pendientes.

## Seguridad y uso responsable
Esta entrega es una base inicial, no una solución auditada ni lista para tratar datos reales sensibles. Antes de producción hay que completar y probar la conexión de la interfaz, autorización granular por roles, validación robusta, almacenamiento persistente de sesiones, limitación de intentos distribuida, recuperación de contraseña, auditoría, copias de seguridad verificadas, política de retención y revisión RGPD. La protección CSRF básica está añadida para las rutas de escritura de la API; el cliente debe obtener `/api/auth/csrf` y enviar el token en `x-csrf-token`. El HTML local usa localStorage y no debe contener datos sensibles ni compartirse en equipos no protegidos.

# nukleo.

Panel interno de nukleo para llevar las membresías: clientes y sus cuentas, pipeline de ventas, calendario de reuniones, cobros del mes, objetivo mensual e importación del extracto de Revolut.

Es una web estática (un solo `index.html`) que guarda los datos en **Firebase** (Firestore) y pide **email y contraseña** para entrar. Se publica en **Vercel** directamente desde este repositorio de GitHub.

---

## Puesta en marcha (una sola vez, unos 15 minutos)

### 1. Crear el proyecto en Firebase

1. Entra en <https://console.firebase.google.com> con tu cuenta de Google y pulsa **Crear un proyecto**. Llámalo `nukleo`. Google Analytics no hace falta.
2. En el menú, **Compilación → Firestore Database → Crear base de datos**.
   - Ubicación: `eur3 (europe-west)` (los datos se quedan en Europa).
   - Modo: **producción**.
3. **Compilación → Authentication → Comenzar**.
   - En **Método de inicio de sesión**, activa **Correo electrónico/contraseña**.
   - En **Usuarios → Agregar usuario**, crea vuestras dos cuentas (tu email y el de tu socio, con una contraseña cada uno).
   - En **Configuración → Acciones del usuario**, desmarca **Habilitar la creación (registro)**, para que nadie más pueda crearse una cuenta.

### 2. Proteger los datos (reglas de Firestore)

1. Abre `firestore.rules` y cambia los dos emails de ejemplo por los vuestros, los mismos que usasteis en el paso anterior.
2. En Firebase, **Firestore Database → Reglas**, borra lo que haya, pega el contenido de `firestore.rules` y pulsa **Publicar**.

Con estas reglas, solo vosotros dos podéis leer o escribir datos. Cualquier otra persona, aunque tenga el enlace, solo verá la pantalla de acceso.

### 3. Conectar la web con Firebase

1. En Firebase, pulsa el engranaje → **Configuración del proyecto → Tus apps → `</>` (Web)**. Registra la app con el nombre `nukleo`; el Hosting de Firebase no hace falta.
2. Copia el bloque `firebaseConfig` que aparece y pega sus valores en `config.js`, sustituyendo los de ejemplo.
3. Guarda y sube el cambio a GitHub.

> Estos valores **no son secretos**: Firebase está pensado para que vayan en la web. La seguridad la ponen el login y las reglas del paso 2.

### 4. Publicar en Vercel

1. Entra en <https://vercel.com> con tu cuenta de GitHub → **Add New… → Project**.
2. Elige este repositorio y pulsa **Import**.
3. Framework Preset: **Other**. No hace falta configurar nada más: no hay *build command* ni variables de entorno.
4. Pulsa **Deploy**. En un minuto tendrás una dirección del tipo `nukleo.vercel.app`.
5. Vuelve a Firebase, **Authentication → Configuración → Dominios autorizados**, y añade esa dirección (y tu dominio propio, si lo usas).

A partir de aquí, cada vez que subáis un cambio a GitHub, Vercel publica la nueva versión automáticamente.

### 5. En el móvil

Abre la dirección de Vercel en el navegador del móvil y elige **Añadir a pantalla de inicio**. Se abrirá como una app, con su icono y a pantalla completa.

---

## Qué hay en cada archivo

| Archivo | Para qué sirve |
| --- | --- |
| `index.html` | Toda la aplicación: diseño, pantallas y lógica |
| `config.js` | Configuración de vuestro proyecto de Firebase |
| `firestore.rules` | Quién puede leer y escribir los datos (pegar en Firebase) |
| `manifest.webmanifest`, `icon*.png`, `icon.svg`, `apple-touch-icon.png` | Icono y modo app en el móvil |
| `vercel.json` | Cabeceras de seguridad y que Google no indexe el panel |

## Cómo se guardan los datos

Los datos están en Firestore, en estas colecciones:

- `clients`: cada empresa cliente, con sus datos, contactos y notas.
- `payments`: un documento por cliente y mes (`<idCliente>_<AAAA-MM>`).
- `events`: reuniones y eventos del calendario.
- `settings/goals`: el objetivo de cada mes.
- `users`: el nombre y el email de cada uno, para mostrar quién marcó un cobro o escribió una nota.

Para hacer una copia de seguridad, usa **Firestore → Importar/Exportar** en la consola de Google Cloud, o exporta desde la propia consola de Firebase.

## Si algo falla

- **"Falta la configuración de Firebase"**: `config.js` aún tiene los valores de ejemplo.
- **"Tu cuenta no tiene permiso"**: tu email no está en `firestore.rules`, o las reglas no se han publicado.
- **No puedo entrar desde la dirección de Vercel**: falta añadir el dominio en *Dominios autorizados* (paso 4.5).
- **He olvidado la contraseña**: en la pantalla de acceso, escribe tu email y pulsa *He olvidado la contraseña*.

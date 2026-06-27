# Generador de Estados Financieros (EEFF) — Plantabal

Herramienta web para generar **ESF + ERI** a partir de los saldos SAP (EEFF) y los
costos gerenciales (GRR3), con validación a cero, resumen macro e histórico mensual.

- **Selección de compañía:** cada compañía guarda su propia plantilla de clasificaciones,
  su catálogo de combinaciones y sus históricos por separado.
- **Datos en la nube (opcional):** con Firebase, los datos se sincronizan en línea y
  pueden compartirse entre varios equipos. Sin Firebase, todo se guarda en el navegador.

---

## 1. Está publicado en la web (GitHub Pages)

La página se publica automáticamente desde este repositorio. URL:

> **https://USUARIO.github.io/REPOSITORIO/**

(El enlace exacto aparece en GitHub → pestaña **Settings → Pages**.)

Cada vez que se haga *push* a la rama principal, GitHub Pages actualiza la web en 1–2 minutos.

---

## 2. Modo LOCAL vs. modo NUBE

| | Modo LOCAL (por defecto) | Modo NUBE (Firebase) |
|---|---|---|
| Dónde se guardan los datos | Solo en el navegador del equipo | En línea (Firestore) |
| Compartir entre equipos | No | Sí |
| Requiere configuración | No | Sí (paso 3) |
| Inicio de sesión | No | Sí (correo + contraseña) |

La esquina superior derecha muestra el estado: **● Local** o **● En línea (nube)**.

---

## 3. Activar la nube (Firebase) — ~5 minutos

1. Entra a **https://console.firebase.google.com** e inicia sesión con una cuenta Google.
2. **Agregar proyecto** → ponle un nombre (ej. `eeff-plantabal`) → crear (puedes desactivar Analytics).
3. Dentro del proyecto, ícono **`</>` (Web)** → registra una app web → copia el objeto
   **`firebaseConfig`** que aparece.
4. Pega esos valores en el archivo **`firebase-config.js`** de este repositorio
   (reemplaza los `""` vacíos) y guarda / haz *commit*.
5. En la consola: **Compilación → Firestore Database → Crear base de datos**
   (modo producción, la región más cercana).
6. En la consola: **Compilación → Authentication → Comenzar →
   Sign-in method → Correo electrónico/contraseña → Habilitar**.
   Luego en **Users → Agregar usuario**, crea el/los usuarios que podrán entrar.
7. **Reglas de seguridad** (Firestore → pestaña *Reglas*): pega esto y publica.
   Así solo los usuarios con sesión iniciada pueden leer/escribir:

   ```
   rules_version = '2';
   service cloud.firestore {
     match /databases/{database}/documents {
       match /{document=**} {
         allow read, write: if request.auth != null;
       }
     }
   }
   ```

Listo: al recargar la web aparecerá la pantalla de **Iniciar sesión** y los datos
se guardarán en línea.

> ⚠️ **Importante (repositorio público):** este repo es público, así que `firebase-config.js`
> es visible. Eso es normal en apps web de Firebase (la `apiKey` no es secreta);
> la seguridad la dan las **reglas + el inicio de sesión** del paso 7. **No uses las
> reglas en "modo de prueba" abierto**, o cualquiera podría leer los datos.

---

## 4. Estructura de datos en Firestore

```
companies/{idCompañía}
   ├─ name        (texto)
   ├─ tpl         (plantilla: cuenta → [tipo, c1, c2, c3])
   ├─ combos      (catálogo de clasificaciones)
   └─ periods/{Mes-Año}   (cada período guardado)
```

---

## 5. Uso

1. Elige la **compañía** (o créala en **Gestionar**).
2. Selecciona **Mes** y **Año**.
3. Carga el archivo **EEFF (saldos SAP)** y el **GRR3 (costos)**.
4. **Generar estados** → revisa la validación a cero y las cuentas nuevas.
5. **Guardar período** para alimentar el comparativo del mes siguiente.
6. **Descargar Excel** para respaldo.

---

## 6. Archivos del proyecto

- `index.html` — la aplicación completa.
- `firebase-config.js` — configuración de la nube (vacío = modo local).
- `README.md` — este documento.

# 🧅 CebollaCrypt

oe estas son las intrucciones para iniciar el prgrama
Demo funcional de cifrado de datos con dos capas independientes:

1. **Capa de contenido** — protege *qué dice* el mensaje.
   AES-256-GCM (cifrado autenticado) con clave derivada de una contraseña mediante PBKDF2.
2. **Capa de ruta** — protege *quién habla con quién* (concepto de onion routing / Tor).
   El mensaje ya cifrado se envuelve en capas RSA-OAEP + AES-GCM, una por cada nodo relé
   simulado. Cada nodo solo puede quitar su propia capa y nunca ve el contenido final ni
   sabe quién fue el emisor original.

El backend (Python) hace todo el trabajo criptográfico real. El frontend (HTML/JS) es
una interfaz visual que llama a ese backend y anima el proceso, pensada para
presentarla en clase.

---

## 1. Requisitos

- **Python 3.10 o superior**
- **Visual Studio Code**
- Extensión de VS Code: **Python** (Microsoft) — para poder correr y depurar el backend
- (Opcional pero recomendado) Extensión de VS Code: **Live Server** — para abrir el
  frontend con un servidor local en vez de como archivo suelto

Para comprobar si ya tienes Python instalado, abre una terminal y ejecuta:

```bash
python --version
```

Si no aparece nada o da error, instálalo desde [python.org/downloads](https://www.python.org/downloads/)
(en Windows, marca la casilla **"Add Python to PATH"** durante la instalación).

---

## 2. Abrir el proyecto en VS Code

1. Descomprime la carpeta `CebollaCrypt` en el lugar donde trabajas tus proyectos.
2. Abre VS Code.
3. Ve a **Archivo → Abrir carpeta...** y selecciona la carpeta `CebollaCrypt`.
4. Si no tienes la extensión **Python** instalada: ve al ícono de extensiones en la barra
   lateral izquierda (o `Ctrl+Shift+X`), busca **"Python"** (de Microsoft) e instálala.
   VS Code te lo suele sugerir automáticamente al abrir un archivo `.py`.

---

## 3. Crear el entorno virtual e instalar dependencias

Un entorno virtual evita que las librerías de este proyecto se mezclen con otras que
tengas instaladas globalmente. Abre una terminal **dentro de VS Code**
(`Terminal → Nueva terminal`, o `` Ctrl+ñ ``) y ubícate en la carpeta `backend`:

```bash
cd backend
```

Crea el entorno virtual:

```bash
python -m venv venv
```

Actívalo (el comando cambia según tu sistema operativo):

**Windows (PowerShell):**
```bash
venv\Scripts\Activate.ps1
```
> Si PowerShell bloquea el script, ejecuta primero:
> `Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass`

**Windows (CMD):**
```bash
venv\Scripts\activate.bat
```

**macOS / Linux:**
```bash
source venv/bin/activate
```

Sabrás que quedó activado porque el prompt de la terminal empieza con `(venv)`.

Con el entorno activado, instala las dependencias:

```bash
pip install -r requirements.txt
```

> 💡 En VS Code, tras crear el entorno, puede aparecer una notificación abajo a la
> derecha preguntando si quieres usar ese entorno (`venv`) como intérprete de Python
> para la carpeta. Dile que sí — así el botón de "Run" y el debugger usan las
> librerías correctas.

---

## 4. Ejecutar el backend

Con el entorno virtual activado y aún dentro de `backend`:

```bash
python app.py
```

Deberías ver algo como:

```
* Running on http://127.0.0.1:5000
```

Déjalo corriendo — esta terminal se queda "viva" mientras el servidor funciona.
Para detenerlo más adelante: `Ctrl+C`.

---

## 5. Abrir el frontend

**Opción simple:** en el explorador de archivos de VS Code, clic derecho sobre
`frontend/index.html` → **"Reveal in File Explorer"** (o busca el archivo en tu
explorador de archivos normal) y ábrelo con doble clic — se abrirá en tu navegador.

**Opción recomendada (evita cualquier detalle raro de seguridad del navegador):**
si instalaste la extensión **Live Server**, haz clic derecho sobre `frontend/index.html`
dentro de VS Code y selecciona **"Open with Live Server"**. Se abrirá automáticamente
en `http://127.0.0.1:5500` (o similar).

---

## 6. Probar la demo

1. En el panel **Emisor**, escribe un mensaje y una contraseña, y da clic en
   **"Cifrar y enviar"**.
2. Observa cómo el paquete "viaja" por los 3 nodos, y cómo el tamaño del paquete
   disminuye en cada uno a medida que se le quita una capa (eso es visible y es un
   buen punto para explicar en la sustentación: cada nodo pela su capa y pasa lo
   que queda al siguiente, sin ver el contenido real).
3. En el panel **Receptor**, escribe la contraseña y da clic en **"Descifrar"**.
   - Con la contraseña correcta: verás el mensaje original.
   - Con una contraseña incorrecta: verás un error explícito — esa es la
     verificación de integridad que da AES-**GCM** (no descifra "cualquier cosa",
     falla claramente).

---

## 7. Estructura del proyecto

```
CebollaCrypt/
├── backend/
│   ├── app.py            → servidor Flask, expone la API (/api/send, /api/decrypt)
│   ├── crypto_core.py     → capa de CONTENIDO: AES-256-GCM + PBKDF2
│   ├── onion.py            → capa de RUTA: capas RSA-OAEP + AES-GCM por nodo
│   └── requirements.txt
├── frontend/
│   ├── index.html
│   ├── style.css
│   └── script.js          → llama a la API y anima la ruta
└── README.md
```

---

## 8. Nota para la sustentación

Esta es una **simulación educativa** del concepto de onion routing: los "nodos" corren
en el mismo proceso del backend, no en máquinas separadas. Para que fuera una red real
de anonimato tipo Tor, cada nodo debería ser un servidor independiente (en otra máquina
o contenedor), sin que ninguno tenga acceso a las claves privadas de los demás. Vale la
pena mencionar esa diferencia si preguntan — muestra que entienden el concepto más allá
de la implementación de la demo.

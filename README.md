# Stickerito

Convierte stickers de TikTok (estáticos y animados) en stickers de WhatsApp. Gratis, sin cuentas, y la conversión se hace en tu navegador.

> En construcción.

## Cómo se usa

1. **Copia el link del sticker** en tiktok.com, desde el computador.
2. **Conviértelo** en la web de Stickerito y descarga el pack (`.zip`).
3. **Agrégalo a WhatsApp** con la app Stickerito para Android.

### 1. Copiar el link del sticker (en el computador)

1. Abre [tiktok.com](https://www.tiktok.com) en Chrome o Edge e inicia sesión.
2. Busca el sticker:
   - en los **comentarios** de un video, o
   - en **Mensajes**. Si el sticker está en tus favoritos, mándalo primero a un chat desde la app de TikTok.
3. Haz clic derecho sobre el sticker y elige **Copiar dirección de imagen**.
4. Repite con cada sticker. Un pack necesita mínimo 3.

El link debe empezar por `https://` y tener `ibyteimg.com` o `tiktokcdn.com`. Los links vencen en unos 30 días: si uno ya no sirve, cópialo otra vez.

### 2. Convertir en la web

1. Abre la web de Stickerito: https://danielzl12.github.io/tiktok_converter/
2. Pega los links, uno por línea.
3. Ponle nombre al pack y pulsa **Convertir**.
4. Descarga el `.zip`. Si tienes stickers estáticos y animados, salen packs separados, porque WhatsApp no deja mezclarlos.

### 3. Agregar a WhatsApp con la app

1. Descarga el APK más reciente desde [Releases](https://github.com/Danielzl12/tiktok_converter/releases) e instálalo. Android te pedirá permitir la instalación desde esa fuente.
2. Abre el `.zip` con Stickerito: desde **Compartir** o **Abrir con**, o con el botón **Elegir archivo** dentro de la app.
3. Pulsa **Añadir a WhatsApp** y confirma.

## Privacidad

- La conversión se hace en tu navegador. Lo único que sale de él son las descargas de los stickers desde TikTok.
- Sin cuentas, sin analytics y sin cookies.
- La app no usa internet: solo lee el `.zip` y se lo entrega a WhatsApp.

## Para desarrolladores

| Parte | Stack | Carpeta |
|---|---|---|
| Web | Vite + TypeScript, `wasm-webp`, `fflate`, Vitest | `web/` |
| App | Flutter (Android) + ContentProvider en Kotlin | `app/` |

Requisitos y diagramas en [`docs/`](docs/).

**Web:**
```bash
cd web
npm install
npm run dev
```

**App:**
```bash
cd app
flutter run
```

## Licencia

MIT. Ver [LICENSE](LICENSE). El código adaptado del [ejemplo oficial de stickers de WhatsApp](https://github.com/WhatsApp/stickers) conserva su licencia BSD.

No afiliado a TikTok ni a WhatsApp.

# Requisitos · Stickerito

Versión 1 · Octubre 2026

## 1. Resumen

Stickerito convierte stickers de TikTok (estáticos y animados) en stickers de WhatsApp. Tiene dos partes:

- **Web** (GitHub Pages): el usuario pega links de stickers de TikTok. La web los descarga, los convierte en el navegador y entrega un `.zip` por pack.
- **App Android**: abre ese `.zip` y agrega el pack a WhatsApp.

Usuario final: cualquier persona de la comunidad, sin conocimientos técnicos.

### Flujo

1. En el PC, en tiktok.com, el usuario copia el link de cada sticker (guía en el README).
2. Pega los links en la web, pulsa **Convertir** y descarga el `.zip`.
3. En el celular abre el `.zip` con Stickerito y pulsa **Añadir a WhatsApp**.

## 2. Decisiones (no cambian sin avisar)

| Id | Decisión |
|---|---|
| D1 | La conversión corre en el navegador. Sin servidor, sin proxy, sin analytics y sin guardar links. |
| D2 | Costo cero: web en GitHub Pages, app como APK en GitHub Releases. Sin Play Store. |
| D3 | La web recibe solo links. No hay "subir archivo". |
| D4 | La app es solo para Android. No convierte: solo importa el `.zip` que genera la web. |
| D5 | Licencia MIT. El código adaptado del ejemplo oficial de WhatsApp (`WhatsApp/stickers`, licencia BSD) conserva su aviso. |
| D6 | Nombre: Stickerito. Ni la web ni la app llevan "WhatsApp" ni "TikTok" en el nombre ni usan sus logos. Pie de página: "No afiliado a TikTok ni a WhatsApp". |
| D7 | Sin login ni cuentas. No se guardan datos personales. |
| D8 | La app no pide permiso de internet: solo lee el `.zip` y se lo entrega a WhatsApp. |

## 3. Requisitos funcionales

### Web

| Id | Requisito |
|---|---|
| RF01 | Pegar uno o varios links de stickers, uno por línea. |
| RF02 | Validar cada link: `https://` y dominio `*.ibyteimg.com` o `*.tiktokcdn.com`. Si no, error claro. |
| RF03 | Descargar cada sticker desde el navegador y detectar su formato por los bytes del archivo. |
| RF04 | Convertir cada sticker a WebP 512×512 cumpliendo RN01 a RN04. |
| RF05 | Mostrar por sticker: vista previa, peso final y estado (listo, o error con el motivo). |
| RF06 | Ponerle nombre al pack. Por defecto: "Mis stickers". |
| RF07 | Armar los packs según RN05 y descargar un `.zip` por pack con el formato de la sección 6. |

### App

| Id | Requisito |
|---|---|
| RF08 | Recibir el `.zip` desde "Compartir" o "Abrir con", y también con un botón **Elegir archivo**. |
| RF09 | Validar el `.zip` (sección 6 y RN01) antes de guardarlo. Si no cumple, error claro y no se guarda nada. |
| RF10 | Guardar cada pack en el almacenamiento interno de la app y listarlos. |
| RF11 | Botón **Añadir a WhatsApp** por pack. Si el pack ya está agregado, mostrarlo. |
| RF12 | Borrar un pack de la app, avisando que en WhatsApp se quita a mano. |

## 4. Reglas de negocio

| Id | Regla |
|---|---|
| RN01 | Límites de WhatsApp: WebP de exactamente 512×512. Estático ≤ 100 KB. Animado ≤ 500 KB, duración total ≤ 10 s y cada frame ≥ 8 ms. Ícono de bandeja: PNG 96×96 ≤ 50 KB. |
| RN02 | Encajar el sticker en 512×512 sin deformarlo, centrado y con relleno transparente. |
| RN03 | Frames con duración 0 pasan a 100 ms. Si la animación dura más de 10 s, se corta en 10 s. No se reordenan frames (WhatsApp termina la animación en el primero). |
| RN04 | Para bajar el peso: primero bajar la calidad (80, 65, 50, 35, 20). Si no alcanza, quitar 1 de cada 2 frames sumando sus duraciones y repetir. Nunca se entrega un archivo fuera de los límites: si no se logra, el sticker queda en error. |
| RN05 | Un pack tiene de 3 a 30 stickers, todos estáticos o todos animados. Si hay de los dos tipos, se arman packs separados. Más de 30: se divide en varios packs. Menos de 3: no se genera ese `.zip` y se avisa. |
| RN06 | El ícono de bandeja sale del primer frame del primer sticker del pack. |
| RN07 | Cada sticker lleva el emoji por defecto 😀 (WhatsApp exige al menos uno). |
| RN08 | Un link vencido o alterado responde 403: mensaje "El link venció: cópialo otra vez desde TikTok". |

## 5. Hechos verificados (octubre 2026)

Probados en Chrome con 5 stickers reales:

| Origen | Host | Formato real | Tamaño | Peso | Animación | Transparencia |
|---|---|---|---|---|---|---|
| Comentario estático | `p19-comment-sign-sg.tiktokcdn.com` | JPEG | 590×498 | 20 KB | — | No |
| Comentario animado | `p16-tiktok-dm-sticker-sign-sg.ibyteimg.com` | WebP animado | 498×348 | 1.1 MB | 130 frames, 6.5 s | No |
| Mensajes estático | `p16-tiktok-dm-sticker-sign-sg.ibyteimg.com` | WebP simple | 240×240 | 9 KB | — | No |
| Mensajes animado | igual | WebP animado | 240×240 | 8 KB | 23 frames, 2.5 s | Sí |
| Mensajes animado | igual | WebP animado | 240×240 | 170 KB | 48 frames, 3 s | Sí |

- La extensión `.awebp` es WebP normal. El formato se detecta por los bytes (`RIFF....WEBP`, `FF D8` JPEG, `89 PNG`, `GIF8`), no por la extensión.
- Los dos hosts permiten descargar desde otra página (CORS); no hace falta proxy.
- El link debe ir completo: si se quita o cambia la firma, o la variante (`mid` → `large`), responde 403.
- Los links vencen a los ~30 días (`x-expires`).
- Los de mensajes vienen en 240×240: al pasarlos a 512 se ven algo borrosos. No hay versión más grande disponible.

## 6. Formato del `.zip` (contrato entre la web y la app)

```
<nombre-del-pack>.zip
├── pack.json
├── tray.png
├── 01.webp
├── 02.webp
└── ...
```

`pack.json`:

```json
{
  "version": 1,
  "name": "Mis stickers",
  "publisher": "Stickerito",
  "animated": true,
  "tray": "tray.png",
  "stickers": [
    { "file": "01.webp", "emojis": ["😀"] },
    { "file": "02.webp", "emojis": ["😀"] },
    { "file": "03.webp", "emojis": ["😀"] }
  ]
}
```

- `name`: máximo 128 caracteres.
- El identificador que pide WhatsApp lo genera la app al importar (uuid).
- La app rechaza el `.zip` si:
  - falta `pack.json` o tiene otra `version`,
  - hay rutas con `..` o absolutas,
  - hay archivos que no estén en `pack.json`,
  - un sticker o el ícono no cumple RN01,
  - el pack no tiene de 3 a 30 stickers o mezcla tipos,
  - el `.zip` pesa más de 16 MB.

## 7. Autenticación y conexión

- No hay login.
- La web necesita internet para descargar los stickers de TikTok.
- La app funciona sin internet.

## 8. Privacidad y seguridad

- Lo único que sale del navegador son las descargas al CDN de TikTok. Sin analytics y sin cookies.
- La app no tiene permiso de internet y guarda los packs solo en su almacenamiento interno.
- La app valida el `.zip` antes de abrirlo (sección 6) para evitar rutas maliciosas y archivos gigantes.
- El repo nunca guarda links firmados de TikTok ni stickers de terceros. Las imágenes de prueba las generan los tests.

## 9. Fuera de la v1

- Subir archivos a la web.
- iOS.
- Convertir dentro de la app.
- Quitar fondos.
- Elegir emojis por sticker.
- Publicar en Play Store.
- Extensión de navegador.

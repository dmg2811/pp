# Runbook — Slider de pantallas

## Cómo funciona
- `panel.html` (subir imágenes / administrar) → sube a GitHub Pages, repo `dmg2811/pp`.
- `index.html` (lo que se ve en la TV) → lee `data.json` cada 5 min desde `https://dmg2811.github.io/pp/data.json`.
- No hay servidor: todo corre en el navegador y usa la API de GitHub directamente.

## Agregar contenido nuevo
1. Abre `https://dmg2811.github.io/pp/panel.html` con tu GitHub token guardado.
2. Arrastra las imágenes o videos al recuadro punteado (se suben y publican solos). Si una
   imagen pesa más de 3MB se comprime automáticamente antes de subir.
3. **No uses** "Añadir Imagen Individual" ni "Carga Masiva" a menos que el archivo YA esté
   subido al repo — esos botones solo agregan una referencia, no suben el archivo. Usarlos mal
   fue justo lo que rompió el slider la última vez (imágenes "fantasma").
4. Revisa en la tabla que la miniatura cargue (no un ❌) antes de dar por hecho que ya quedó.

## Videos
- Formato: **MP4 con codec H.264** (el único soportado de forma confiable en navegadores de
  Smart TV — evita H.265/HEVC, VP9 o AV1).
- Sin audio o silenciados: el video se reproduce `muted` siempre — casi todo navegador bloquea
  el autoplay con sonido, y además así conviene para señalización de tienda.
- Pésalos ya comprimidos antes de subir (idealmente <20MB, 1080p o menos). El panel avisa si
  detecta que un video no es MP4/H.264 liviano, pero **no lo convierte solo** — se intentó
  hacerlo automático en el navegador con ffmpeg.wasm y no es viable: requiere headers
  especiales (COOP/COEP) que GitHub Pages no permite configurar.

### ⚠️ Video vertical (celular) — necesita rotación de 90° ANTES de subir
Las imágenes del slider están diseñadas en horizontal (por eso `index.html` rota la página
90° para llenar la pantalla vertical de la TV). Un video grabado normal en el celular ya viene
vertical — si se sube tal cual, la rotación de la página lo deja de lado (una franja horizontal
chiquita a la mitad de la pantalla, con barras negras arriba y abajo). Esto le pasó a
`videoglowbalance`, `bypass-sensorial`, `ultra-advance-concepto` y `ultra-advance-gold`, los 4
videos que había al momento de escribir esto — se corrigió rotándolos con:
```
ffmpeg -i entrada.mp4 -vf "transpose=2" -an -c:v libx264 -profile:v high -pix_fmt yuv420p -crf 23 -preset medium -movflags +faststart salida.mp4
```
(`transpose=2` = 90° antihorario; es la dirección que da el resultado correcto, verificado
visualmente — `transpose=1` queda al revés/de cabeza). El resultado queda con ancho y alto
invertidos (ej. 1080x1920 → 1920x1080) a propósito.
- **Si el video ya viene horizontal** (grabado apaisado, o ya es un diseño gráfico en
  horizontal como las imágenes) **no** se le aplica esto — solo a video vertical/retrato.
- Dos formas de hacerlo:
  1. **Pídeselo a Claude** — dale la ruta del archivo, aplica esta rotación (si hace falta) y
     comprime en un solo paso, y te dice cuándo ya está subido.
  2. **HandBrake** (gratis, handbrake.fr): abre el video, preset "Fast 1080p30", en la pestaña
     "Rotation" gira 90° (prueba un sentido; si queda de cabeza, usa el otro), exportar.
- El slider detecta que es video por la extensión del archivo (`.mp4`, `.webm`, `.mov`, `.m4v`)
  y lo reproduce completo antes de pasar al siguiente slide.

## Configuración (duración, refresco, rotación)
- En el panel, sección "⚙️ Configuración de las pantallas": duración de cada imagen, cada
  cuánto se revisa si hay catálogo nuevo, y la rotación con la que arranca una pantalla que
  nunca se giró a mano.
- Se publica como `config.json`. Una pantalla ya encendida lo toma solo en su próximo refresco
  (no hace falta reiniciarla); no afecta a una pantalla que ya se giró manualmente con el botón
  "Girar 180°" — ese ajuste manual siempre gana sobre el default.
- Orden de reproducción: en la tabla del inventario, los botones ▲/▼ mueven cualquier imagen o
  video a cualquier posición (se publica al instante). La tabla se muestra en el mismo orden en
  que se ve en la pantalla.

## La pantalla de una tienda no actualiza / muestra roto
1. Revisa `https://dmg2811.github.io/pp/data.json` en el navegador — ¿las URLs cargan?
2. Revisa la pestaña **Actions** del repo `dmg2811/pp` en GitHub — el workflow
   "Validar data.json" corre en cada publicación y marca en rojo qué imagen falta.
3. Si el internet de la tienda se cayó: la pantalla reintenta sola cada 15s hasta recuperar
   señal, no hace falta reiniciarla.
4. Revisa el indicador chiquito abajo a la derecha de la TV (🟢 GitHub Pages / 🔴 Sin conexión).

## Publicar cambios de código (panel.html / index.html)
```
git add .
git commit -m "descripción del cambio"
git push
```
Esto va directo a producción (no hay ambiente de prueba) — pruébalo abriendo el archivo en
local antes de hacer push si el cambio es grande.

## Seguridad del token
- Usa un **fine-grained personal access token** limitado solo al repo `pp`, con expiración,
  no un token "classic" con acceso a todos tus repos.
- Si usaste el panel en una computadora compartida, presiona "Olvidar Token" al terminar.

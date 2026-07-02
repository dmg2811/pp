# Runbook — Slider de pantallas

## Cómo funciona
- `panel.html` (subir imágenes / administrar) → sube a GitHub Pages, repo `dmg2811/pp`.
- `index.html` (lo que se ve en la TV) → lee `data.json` cada 5 min desde `https://dmg2811.github.io/pp/data.json`.
- No hay servidor: todo corre en el navegador y usa la API de GitHub directamente.

## Agregar contenido nuevo
1. Abre `https://dmg2811.github.io/pp/panel.html` con tu GitHub token guardado.
2. Arrastra las imágenes al recuadro punteado (se suben y publican solas). Si una imagen pesa
   más de 3MB se comprime automáticamente antes de subir.
3. **No uses** "Añadir Imagen Individual" ni "Carga Masiva" a menos que el archivo YA esté
   subido al repo — esos botones solo agregan una referencia, no suben el archivo. Usarlos mal
   fue justo lo que rompió el slider la última vez (imágenes "fantasma").
4. Revisa en la tabla que la miniatura cargue (no un ❌) antes de dar por hecho que ya quedó.

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

# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Proyecto

Sitio web de marketing para **FORNNO**, un sistema de gestión y crecimiento para restaurantes. Es un sitio estático puro (HTML5, CSS3, JavaScript vanilla) sin framework ni sistema de build.

## Cómo correr el proyecto

No hay build. Para previsualizar localmente, usar cualquier servidor estático:

```bash
python -m http.server 8000
# o
npx http-server
```

Para publicar: push a GitHub (`https://github.com/maticoll/Forno2.0.git`) y desplegar desde allí a cualquier hosting estático (Netlify, Vercel, GitHub Pages, etc.).

## Estructura de archivos

- `index.html` — Landing page principal (scroll snap, 7 secciones)
- `app.html`, `secretaria.html`, `escalabilidad.html`, `fidelizacion.html`, `agencia.html`, `web.html` — Páginas de detalle de cada feature
- `Brice_Font/` — Fuente tipográfica custom (WOFF2 + OTF)
- Imágenes, SVG y video de assets en la raíz

## Arquitectura

### index.html — Scroll snap full-page

La landing usa **scroll snap** con secciones de 100vh. JavaScript controla la navegación entre secciones con:
- **Rueda del mouse / teclado** (flechas, Page Up/Down)
- **Puntos de navegación** en el rail derecho
- **Lock mechanism** para evitar conflictos de eventos durante la transición
- La sección footer desbloquea el scroll libre; volver a hacer scroll hacia arriba reactiva el snap

### Páginas de feature

Cada página de feature es standalone con su propio CSS y JS inline. Tienen breadcrumbs de navegación ("← VOLVER" / "SIGUIENTE →").

### CSS

Todo el CSS está **inline** dentro de cada HTML (tag `<style>`). No hay hojas de estilo separadas.

**Variables CSS globales (paleta de color):**
```css
--orange: #D94F1A    /* brand primario */
--bg: #F5F0E8        /* beige claro */
--text: #1A1612      /* texto oscuro */
--muted: #7A6F62     /* texto secundario */
--dark: #1A1612      /* secciones oscuras */
--green: #2a7a4b     /* acento */
```

**Tipografía:**
- Títulos: `Barlow Condensed` (600–900 weight, uppercase, letter-spacing)
- Cuerpo: `Barlow` (400–700 weight)
- Fallback: `Brice Font` (local)
- Tamaños con `clamp()` para fluidez responsive

**Breakpoints:** `768px` y `640px`

### JavaScript

Todo el JS está **inline** en cada HTML. Patrones usados:
- `IntersectionObserver` para animaciones fade-in al hacer scroll
- Event listeners en `wheel`, `keydown`, `touchstart`/`touchend`
- La navbar agrega clase `.solid` al hacer scroll hacia abajo

## Convenciones

- Todo el contenido está en **español**
- No hay tests automatizados — la verificación es visual en el browser
- CSS y JS van inline en cada archivo HTML, no en archivos separados

# Home Restructure Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Reestructurar index.html de scroll-snap full-page a scroll normal con tabs de módulos, formulario de contacto y footer.

**Architecture:** Un solo archivo HTML con CSS y JS inline. Se elimina el scroll-snap y el JS que lo controla. Las 6 secciones de módulos pasan a ser paneles de una sección tabbed. El fondo cambia por módulo activo. Se agrega una sección de formulario antes del footer.

**Tech Stack:** HTML5, CSS3, JavaScript vanilla (sin framework, sin build)

---

## Estructura de archivos

- Modificar: `index.html` — único archivo afectado

---

## Mapa de secciones (nueva estructura)

```
<nav> (sin cambios)
<section id="hero">        ← sin cambios
<section id="galeria">     ← sin cambios
<section id="modulos">     ← NUEVA: wrapper con tabs + paneles
  <div class="modules-tabs">  ← subheader con 6 botones
  <div class="module-panel" data-id="secretaria">  ← panel 1 (activo por defecto)
  <div class="module-panel" data-id="escalabilidad"> ← panel 2 (oculto)
  ...etc
<section id="contacto">    ← NUEVA: formulario
<footer>                   ← sin cambios
```

---

### Task 1: Eliminar scroll-snap del CSS

**Files:**
- Modify: `index.html` — bloque `<style>` inline

- [ ] **Step 1: Localizar y eliminar `overflow:hidden` del `html`**

Buscar en el CSS (alrededor de línea 53):
```css
html{
  height:100%;
  overflow:hidden;   ← BORRAR esta línea
}
```
Cambiar a:
```css
html{
  height:100%;
}
```

- [ ] **Step 2: Eliminar la regla `.screen { height:100vh }`**

Buscar (líneas ~169-174):
```css
.screen{
  height:100vh;
  height:100svh;
  display:flex;align-items:center;
  position:relative;overflow:hidden;
}
```
Cambiar a:
```css
.screen{
  display:flex;align-items:center;
  position:relative;overflow:hidden;
  min-height:600px;
  padding:80px 0;
}
```

- [ ] **Step 3: Eliminar CSS de `.scroll-dots` y `.scroll-dot`**

Buscar el bloque `/* ── SCROLL DOTS (right rail) ── */` y eliminar todo su CSS (unas 15 líneas).

- [ ] **Step 4: Verificar visualmente** — Abrir el archivo en el browser. El scroll debe ser libre, el hero ocupa toda la pantalla, los módulos se ven uno debajo del otro.

- [ ] **Step 5: Commit**
```bash
git add index.html
git commit -m "refactor: eliminar scroll-snap y overflow:hidden del CSS"
```

---

### Task 2: Eliminar scroll-snap del JS y del HTML

**Files:**
- Modify: `index.html` — sección `<script>` y elementos `.scroll-dots`

- [ ] **Step 1: Eliminar el bloque HTML de `.scroll-dots`**

Buscar en el HTML (antes de la sección `#hero`) el elemento:
```html
<nav class="scroll-dots" ...>
  <a class="scroll-dot" ...></a>
  ...
</nav>
```
Eliminarlo completo.

- [ ] **Step 2: Eliminar el bloque JS de scroll-snap**

En el `<script>` al final del archivo, buscar el comentario:
```js
/* ── SCROLL SNAP (JS-controlled) ── */
(function(){
```
y eliminar todo ese IIFE (la función auto-ejecutable entera, hasta su `})();`). Es el bloque más largo del script.

- [ ] **Step 3: Eliminar los `<div class="scroll-cue">` de las secciones de módulos**

Cada sección de módulo tiene al final:
```html
<div class="scroll-cue">
  <svg ...></svg>
</div>
```
Eliminar esos divs de las secciones: `#secretaria`, `#escalabilidad`, `#fidelizacion`, `#app`, `#agencia`. (El de `#galeria` y `#hero` pueden quedarse o eliminarse — a criterio visual.)

- [ ] **Step 4: Verificar** — El browser no debe saltar entre secciones. Scroll libre.

- [ ] **Step 5: Commit**
```bash
git add index.html
git commit -m "refactor: eliminar JS y HTML de scroll-snap y scroll-dots"
```

---

### Task 3: Agregar CSS para la sección de módulos con tabs

**Files:**
- Modify: `index.html` — bloque `<style>` inline

- [ ] **Step 1: Agregar el siguiente bloque CSS al final del `<style>` existente (antes de `</style>`)**

```css
/* ── MODULES SECTION ── */
.modules-section{
  position:relative;
  transition:background .4s ease;
}
.modules-section[data-active="secretaria"]{ background:var(--bg2); }
.modules-section[data-active="escalabilidad"]{ background:var(--dark); color:#fff; }
.modules-section[data-active="fidelizacion"]{ background:var(--bg); }
.modules-section[data-active="app"]{ background:var(--bg2); }
.modules-section[data-active="agencia"]{ background:var(--dark); color:#fff; }
.modules-section[data-active="web"]{ background:var(--bg3); }

/* ── MODULES TABS (subheader) ── */
.modules-tabs{
  display:flex;align-items:center;
  gap:0;
  border-bottom:1px solid var(--border);
  background:inherit;
  position:sticky;top:60px;z-index:100;
  padding:0 48px;
  overflow-x:auto;
  scrollbar-width:none;
}
.modules-tabs::-webkit-scrollbar{display:none;}
.modules-section[data-active="escalabilidad"] .modules-tabs,
.modules-section[data-active="agencia"] .modules-tabs{
  border-bottom-color:rgba(255,255,255,.1);
}
.module-tab{
  font-family:'Author',sans-serif;
  font-size:13px;font-weight:600;letter-spacing:.04em;
  text-transform:uppercase;
  padding:16px 20px;
  border:none;background:none;
  color:var(--muted);
  cursor:pointer;
  position:relative;
  white-space:nowrap;
  transition:color .2s;
}
.module-tab::after{
  content:'';
  position:absolute;bottom:-1px;left:0;right:0;
  height:2px;background:var(--orange);
  transform:scaleX(0);transition:transform .25s ease;
}
.module-tab:hover{color:var(--text);}
.module-tab.active{color:var(--text);font-weight:700;}
.module-tab.active::after{transform:scaleX(1);}
.modules-section[data-active="escalabilidad"] .module-tab,
.modules-section[data-active="agencia"] .module-tab{color:rgba(255,255,255,.45);}
.modules-section[data-active="escalabilidad"] .module-tab:hover,
.modules-section[data-active="agencia"] .module-tab:hover{color:#fff;}
.modules-section[data-active="escalabilidad"] .module-tab.active,
.modules-section[data-active="agencia"] .module-tab.active{color:#fff;}

/* ── MODULE PANELS ── */
.module-panel{
  display:none;
  opacity:0;
  transition:opacity .2s ease;
}
.module-panel.active{
  display:flex;
  opacity:1;
}

/* ── CONTACT FORM SECTION ── */
.contact-section{
  background:var(--dark);color:#fff;
  padding:100px 48px;
}
.contact-inner{
  max-width:640px;margin:0 auto;text-align:center;
}
.contact-eyebrow{
  font-size:11px;font-weight:700;letter-spacing:.14em;
  text-transform:uppercase;color:var(--orange);
  display:block;margin-bottom:20px;
}
.contact-h2{
  font-family:'Author',sans-serif;
  font-size:clamp(40px,6vw,72px);font-weight:900;
  line-height:1.0;margin-bottom:16px;color:#fff;
}
.contact-p{
  font-size:clamp(15px,1.5vw,18px);
  color:rgba(255,255,255,.5);line-height:1.7;
  margin-bottom:48px;
}
.contact-form{
  display:flex;flex-direction:column;gap:16px;
  text-align:left;
}
.contact-form input{
  width:100%;padding:16px 20px;
  background:rgba(255,255,255,.06);
  border:1.5px solid rgba(255,255,255,.1);
  border-radius:10px;
  color:#fff;font-family:'Author',sans-serif;
  font-size:15px;font-weight:500;
  transition:border-color .2s;outline:none;
}
.contact-form input::placeholder{color:rgba(255,255,255,.3);}
.contact-form input:focus{border-color:var(--orange);}
.contact-form-row{display:flex;gap:16px;}
.contact-form-row input{flex:1;}
.contact-submit{
  margin-top:8px;
  width:100%;padding:18px 32px;
  background:var(--orange);color:#fff;
  border:none;border-radius:10px;
  font-family:'Author',sans-serif;
  font-size:16px;font-weight:700;
  cursor:pointer;
  transition:background .2s,transform .2s,box-shadow .2s;
}
.contact-submit:hover{
  background:var(--orange-dark);
  transform:translateY(-2px);
  box-shadow:0 8px 30px rgba(255,92,26,.4);
}
@media(max-width:768px){
  .modules-tabs{padding:0 20px;}
  .module-tab{padding:14px 14px;font-size:12px;}
  .contact-section{padding:72px 24px;}
  .contact-form-row{flex-direction:column;}
}
```

- [ ] **Step 2: Verificar** — No debe verse ningún cambio visual todavía (el CSS se agregó pero no hay HTML que lo use).

- [ ] **Step 3: Commit**
```bash
git add index.html
git commit -m "feat: agregar CSS para tabs de módulos y formulario de contacto"
```

---

### Task 4: Reestructurar HTML de las secciones de módulos

**Files:**
- Modify: `index.html` — las 6 secciones `#secretaria` a `#web`

- [ ] **Step 1: Envolver las 6 secciones en el contenedor `.modules-section`**

Antes de `<!-- ══ 01 · SECRETARÍA ══════════════════════ -->` agregar:
```html
<!-- ══ MÓDULOS ══════════════════════════════════════ -->
<section id="modulos" class="modules-section" data-active="secretaria">
  <!-- Subheader tabs -->
  <div class="modules-tabs">
    <button class="module-tab active" data-target="secretaria">Secretaria</button>
    <button class="module-tab" data-target="escalabilidad">Escalabilidad</button>
    <button class="module-tab" data-target="fidelizacion">Fidelización</button>
    <button class="module-tab" data-target="app">App</button>
    <button class="module-tab" data-target="agencia">Agencia</button>
    <button class="module-tab" data-target="web">Web</button>
  </div>
```

- [ ] **Step 2: Convertir `#secretaria` en `.module-panel`**

Cambiar:
```html
<section class="screen screen-bg2 snap-section" id="secretaria">
```
Por:
```html
<div class="module-panel screen active" id="secretaria">
```
Y el cierre `</section>` → `</div>`.
Eliminar también `<div class="bg-num">01</div>` de este panel.

- [ ] **Step 3: Convertir `#escalabilidad` en `.module-panel`**

Cambiar:
```html
<section class="screen screen-dark snap-section reversed" id="escalabilidad">
```
Por:
```html
<div class="module-panel screen reversed" id="escalabilidad">
```
Y el cierre `</section>` → `</div>`. Eliminar `<div class="bg-num">02</div>`.

- [ ] **Step 4: Convertir `#fidelizacion` en `.module-panel`**

Cambiar:
```html
<section class="screen snap-section" id="fidelizacion">
```
Por:
```html
<div class="module-panel screen" id="fidelizacion">
```
Cierre y bg-num igual.

- [ ] **Step 5: Convertir `#app` en `.module-panel`**

Cambiar:
```html
<section class="screen screen-bg2 snap-section reversed" id="app">
```
Por:
```html
<div class="module-panel screen reversed" id="app">
```
Cierre y bg-num igual.

- [ ] **Step 6: Convertir `#agencia` en `.module-panel`**

Cambiar:
```html
<section class="screen screen-dark snap-section" id="agencia">
```
Por:
```html
<div class="module-panel screen" id="agencia">
```
Cierre y bg-num igual.

- [ ] **Step 7: Convertir `#web` en `.module-panel`**

Cambiar:
```html
<section class="screen screen-bg3 snap-section reversed" id="web">
```
Por:
```html
<div class="module-panel screen reversed" id="web">
```
Cierre y bg-num igual.

- [ ] **Step 8: Cerrar el `<section id="modulos">` después de `#web`**

Después del `</div>` que cierra el panel `#web`, agregar `</section>`.

- [ ] **Step 9: Verificar** — Solo el panel "Secretaria" debe estar visible. Los tabs deben verse en el subheader. Los otros 5 paneles deben estar ocultos.

- [ ] **Step 10: Commit**
```bash
git add index.html
git commit -m "feat: reestructurar módulos como paneles bajo sistema de tabs"
```

---

### Task 5: JS para cambio de tabs con animación

**Files:**
- Modify: `index.html` — bloque `<script>` inline

- [ ] **Step 1: Agregar el siguiente bloque JS antes de `</script>`**

```js
/* ── MODULE TABS ── */
(function(){
  const section = document.getElementById('modulos');
  const tabs = document.querySelectorAll('.module-tab');
  const panels = document.querySelectorAll('.module-panel');

  function activateTab(targetId) {
    // Actualizar data-active en el contenedor (cambia fondo via CSS)
    section.setAttribute('data-active', targetId);

    // Actualizar tabs
    tabs.forEach(t => t.classList.toggle('active', t.dataset.target === targetId));

    // Cross-fade: fade out el panel activo, fade in el nuevo
    const current = document.querySelector('.module-panel.active');
    const next = document.getElementById(targetId);
    if (!next || current === next) return;

    current.style.opacity = '0';
    setTimeout(() => {
      current.classList.remove('active');
      current.style.opacity = '';

      next.classList.add('active');
      // Re-disparar animaciones fade-up dentro del panel
      next.querySelectorAll('.fade-up').forEach(el => {
        el.classList.remove('visible');
        // Forzar reflow
        void el.offsetWidth;
        el.classList.add('visible');
      });
      // Re-disparar food ordering si existe
      if (typeof initFoPanel === 'function') initFoPanel();
      // Re-disparar smart search si existe
      if (typeof initSsPanel === 'function') initSsPanel();
    }, 180);
  }

  tabs.forEach(tab => {
    tab.addEventListener('click', () => activateTab(tab.dataset.target));
  });

  // Activar el primero al cargar
  activateTab('secretaria');
})();
```

- [ ] **Step 2: Verificar** — Hacer click en cada tab. Debe cambiar el panel con un cross-fade suave, el fondo de la sección debe cambiar de color, y las animaciones del panel entrante deben dispararse.

- [ ] **Step 3: Verificar en mobile** — Los tabs deben ser scrolleables horizontalmente si no entran en pantalla.

- [ ] **Step 4: Commit**
```bash
git add index.html
git commit -m "feat: agregar JS para cambio de tabs con cross-fade y re-animación"
```

---

### Task 6: Agregar sección de formulario de contacto

**Files:**
- Modify: `index.html` — entre `</section>` (cierre de `#modulos`) y `<footer>`

- [ ] **Step 1: Agregar la sección HTML del formulario**

Entre el `</section>` que cierra `#modulos` y el `<footer>`, insertar:

```html
<!-- ══ CONTACTO ══════════════════════════════════════ -->
<section id="contacto" class="contact-section">
  <div class="contact-inner fade-up">
    <span class="contact-eyebrow">Empezá hoy</span>
    <h2 class="contact-h2">¿Listo para<br>crecer?</h2>
    <p class="contact-p">Dejanos tus datos y te contactamos en menos de 24 horas para mostrarte cómo FORNNO transforma tu restaurante.</p>
    <form class="contact-form" id="contactForm">
      <div class="contact-form-row">
        <input type="text" name="nombre" placeholder="Tu nombre" required>
        <input type="text" name="restaurante" placeholder="Nombre del restaurante" required>
      </div>
      <input type="tel" name="whatsapp" placeholder="Tu WhatsApp (ej: +5491112345678)" required>
      <button type="submit" class="contact-submit">
        Quiero mi demo gratuita →
      </button>
    </form>
  </div>
</section>
```

- [ ] **Step 2: Agregar JS para el submit del formulario (redirige a WhatsApp)**

En el bloque `<script>`, agregar:

```js
/* ── CONTACT FORM → WhatsApp ── */
document.getElementById('contactForm').addEventListener('submit', function(e) {
  e.preventDefault();
  const nombre = this.nombre.value.trim();
  const restaurante = this.restaurante.value.trim();
  const whatsapp = this.whatsapp.value.trim();
  const msg = encodeURIComponent(
    `Hola FORNNO! Soy ${nombre} del restaurante "${restaurante}". Quiero saber más. Mi WhatsApp es ${whatsapp}.`
  );
  window.open(`https://wa.me/549XXXXXXXXX?text=${msg}`, '_blank');
});
```

> **Nota:** Reemplazar `XXXXXXXXX` con el número real de WhatsApp cuando esté disponible.

- [ ] **Step 3: Verificar** — La sección de contacto debe verse correctamente sobre el footer, con fondo oscuro, formulario centrado y botón naranja.

- [ ] **Step 4: Commit**
```bash
git add index.html
git commit -m "feat: agregar sección de formulario de contacto"
```

---

### Task 7: Adaptar el IntersectionObserver de la nav

**Files:**
- Modify: `index.html` — bloque `<script>` inline

- [ ] **Step 1: Localizar el observer de sección en el JS**

Buscar:
```js
const darkSections = ['escalabilidad','agencia','galeria'];
```

- [ ] **Step 2: Actualizar la lógica del observer**

La nav ahora solo necesita saber si estamos en el hero (transparente) o no (sólida). El cambio dark/light ya no aplica por scroll-snap sino por visibilidad del formulario de contacto (oscuro) o galería.

Reemplazar el bloque del `sectionObs` por:

```js
const heroEl = document.getElementById('hero');
const galeriaEl = document.getElementById('galeria');
const contactoEl = document.getElementById('contacto');

const navObs = new IntersectionObserver((entries) => {
  entries.forEach(e => {
    const id = e.target.id;
    if (!e.isIntersecting) return;
    if (id === 'hero') {
      nav.classList.remove('solid','solid-dark');
      navLinks.classList.remove('dark');
      hamburger.classList.remove('dark');
    } else if (id === 'galeria' || id === 'contacto') {
      nav.classList.remove('solid'); nav.classList.add('solid-dark');
      navLinks.classList.add('dark');
      hamburger.classList.add('dark');
    } else {
      nav.classList.remove('solid-dark'); nav.classList.add('solid');
      navLinks.classList.remove('dark');
      hamburger.classList.remove('dark');
    }
  });
}, { threshold: .3 });

[heroEl, galeriaEl, contactoEl].filter(Boolean).forEach(el => navObs.observe(el));

// Eliminar la línea antigua: document.querySelectorAll('.screen, .hero').forEach(s => sectionObs.observe(s));
```

- [ ] **Step 3: Eliminar las referencias a `scrollDots` y el observer anterior**

Buscar y eliminar:
```js
const scrollDots = document.querySelectorAll('.scroll-dot');
```
y cualquier uso de `scrollDots.forEach(...)`.

- [ ] **Step 4: Verificar** — La nav debe ser transparente en el hero, sólida oscura en la galería y contacto, sólida clara en módulos.

- [ ] **Step 5: Commit**
```bash
git add index.html
git commit -m "fix: adaptar IntersectionObserver de la nav a la nueva estructura"
```

---

### Task 8: Verificación final y push

- [ ] **Step 1: Revisar en desktop** — Scrollear la página completa. Verificar: hero → galería → módulos (tabs funcionando) → formulario → footer.

- [ ] **Step 2: Revisar en mobile** — Verificar que los tabs sean scrolleables horizontalmente y que los paneles se vean bien en pantalla angosta.

- [ ] **Step 3: Verificar el nav** — Cambios de color al scrollear entre secciones.

- [ ] **Step 4: Verificar el formulario** — Completar campos y hacer click en "Quiero mi demo". Debe abrir WhatsApp con el mensaje prellenado.

- [ ] **Step 5: Push al repo del cliente**
```bash
git push origin master
```

# 🎓 Prompt Maestro — Transformar HTML básico de guía en versión interactiva Docente/Estudiante

---

## CONTEXTO Y OBJETIVO

Eres un desarrollador frontend experto en diseño editorial y matemática aplicada. Tu tarea es transformar un archivo HTML básico generado por un generador de guías de trabajo en una **obra de arte interactiva de nivel profesional**, con dos modos: **Docente** y **Estudiante**. El resultado debe ser un único archivo `.html` autocontenido que funcione sin servidor, sin dependencias locales, y que sea compatible con Safari/iOS.

---

## ENTRADA

Se te proporciona un HTML básico con esta estructura típica:
- Portada institucional (logos, título, autores, evento)
- Datos del participante + Objetivo + Agenda
- Actividad inicial (lluvia de ideas / preguntas)
- **Sección de motivación** con modelo matemático: nombre del modelo, descripción, ecuaciones LaTeX, glosario de variables, supuestos
- Desarrollo: enunciado del problema aplicado, variables, cuadrícula de bosquejo, preguntas de análisis (Partes I y II)
- Cierre / reflexión

---

## DISEÑO VISUAL — PALETA Y SISTEMA (NO NEGOCIABLE)

Replica **exactamente** este sistema de tokens CSS. No inventes colores nuevos ni cambies la tipografía base:

```css
:root {
  --navy:     #1a2a3a;
  --navy2:    #1e3a5f;
  --steel:    #2c4f7c;
  --sky:      #3d6491;
  --sky-lt:   #5b82b0;
  --offwhite: #f5f7fa;
  --cream:    #eef1f6;
  --white:    #ffffff;
  --light:    #d0dae8;
  --muted:    #7a8a99;
  --text:     #1a2535;
  --text2:    #3d5575;
  --accent:   #2c6fad;   /* azul primario */
  --accent2:  #1a8a72;   /* verde — respuestas correctas */
  --accent3:  #b8860b;   /* ámbar — advertencias */
  --error:    #c0392b;
  --border:   #c8d6e5;
  --surface:  #f0f4f9;
  --surface2: #e4ecf5;
}
```

**Tipografía:** `Playfair Display` (títulos serif), `IBM Plex Sans` (cuerpo), `IBM Plex Mono` (código/math labels). Cargar desde Google Fonts.

**Librerías externas permitidas (CDN):**
- MathJax 3: `https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.min.js`
- Chart.js 4: `https://cdn.jsdelivr.net/npm/chart.js@4.4.0/dist/chart.umd.min.js`

---

## ARQUITECTURA DEL ARCHIVO

El HTML es **un único archivo** con dos modos:

### 🔐 MODO DOCENTE (visible al abrir el archivo por primera vez)
- **Panel superior** (`teacher-bar`) con gradiente `--navy2 → --steel`, que contiene:
  - Campo para definir contraseña maestra (tipo `password`) + botón "✓ Establecer"
  - Campo para URL del formulario Google/Typeform
  - Botón "📤 Exportar HTML Estudiantes" → genera versión para distribuir
  - Botón "👁 Ver todas respuestas" → desbloquea todo sin contraseña
- Al establecer contraseña: las respuestas esperadas se revelan inmediatamente en modo docente
- Al exportar: se genera un nuevo HTML con el panel docente oculto, la contraseña embebida en base64 (`safeB64Encode(pwd + ':guia')`) y la URL del formulario precargada

### 👩‍🎓 MODO ESTUDIANTE (archivo exportado)
- Sin panel docente visible
- **Unlock banner** (barra azul marina) con input de contraseña y botón "🔓 Desbloquear" para revelar respuestas esperadas con la contraseña que el docente distribuyó
- **Barra inferior fija** ("Guardar mis respuestas") que exporta el HTML con las respuestas escritas por el estudiante rellenas en los campos

---

## SECCIONES Y SU DISEÑO DETALLADO

### 1. HEADER HERO
```
background: linear-gradient(135deg, --navy 0%, --navy2 40%, --steel 100%)
```
- Título en `Playfair Display`, tamaño `clamp(1.9rem, 5vw, 3rem)`, con `<em>` en `#7eb8e8`
- Subtítulo en `IBM Plex Mono`, 11px, `letter-spacing:.18em`, uppercase, color `--sky-lt`
- Meta (autores, institución, año) en `--light`, 13px
- Badge de modo (punto verde pulsante + label "Modo Docente" / "Modo Estudiante") con `backdrop-filter:blur(4px)`
- Partículas decorativas animadas (5 divs `.particle` con `animation:drift linear infinite`)

### 2. DATOS DEL PARTICIPANTE
- Card blanca, `border-radius:12px`, `box-shadow:0 2px 12px rgba(26,42,58,.07)`
- Grid 2×2 para campos: Nombre, Fecha, Institución, Curso — con `border-bottom` animated focus
- Caja de objetivo con `border-left:3px solid --accent`
- Agenda como `grid` de chips oscuros sobre fondo `--navy` con `border-radius:12px`

### 3. ACTIVIDAD INICIAL
- Lista de ítems con numeración circular (gradiente `--navy2 → --accent`)
- `textarea` con `border:1px dashed --border`, mínimo 52px, resize vertical
- Hover: `border-color:--sky-lt; box-shadow: 0 3px 12px rgba(44,79,124,.1)`

### 4. 🌟 SECCIÓN DE MOTIVACIÓN (LA MÁS IMPORTANTE)

Esta sección debe ser **visualmente rica y pedagógicamente completa**. Incluye:

#### 4a. Hero del modelo (fondo oscuro animado)
```
background: linear-gradient(135deg, --navy 0%, --navy2 50%, #0f2235 100%)
border-radius: 16px
box-shadow: 0 8px 32px rgba(26,42,58,.25)
```
- Etiqueta monospace uppercase en `--sky-lt`
- Título del modelo en `Playfair Display` 1.6rem, blanco, con `<em>` en `#7eb8e8`
- Animar los nodos del modelo (flotación vertical con `@keyframes nodeFloat`) con colores por compartimento

#### 4b. Ecuaciones del modelo — presentación completa
Para **cada ecuación** del sistema, mostrar:
1. La ecuación centrada con MathJax `\[...\]`, tamaño grande
2. Un bloque de interpretación `rgba(255,255,255,.06)` con:
   - `<strong style="color:#7eb8e8">Interpretación:</strong>`
   - Texto explicativo **prolijo y detallado**: qué significa cada término, qué pasa si aumenta/disminuye, relación causa-efecto en palabras accesibles para 3° medio
   - Chips de términos clave al pie del bloque (flex-wrap, gap)
3. La ley de conservación (si aplica) en bloque azul `rgba(44,111,173,.15)`

**Nivel de detalle requerido:** El estudiante de 3° medio debe entender la ecuación SIN conocer cálculo. Explicar en lenguaje natural + notación matemática accesible. Ejemplo del nivel esperado:
> *"La tasa de cambio de Susceptibles es siempre negativa — la población susceptible solo puede disminuir. El término β·S·I modela los nuevos contagios: cada encuentro entre un susceptible y un infectado tiene probabilidad proporcional a β de resultar en contagio..."*

#### 4c. Gráfica interactiva (Chart.js) con panel de análisis matemático

El título del chart-header debe ser genérico: **"📊 Simulación del modelo"** (no mencionar ningún modelo específico). Los parámetros de los sliders deben tomarse del glosario de variables del modelo que venga en el HTML de entrada — si el modelo tiene parámetros distintos, usar los que correspondan. Si el modelo no tiene simulación numérica posible (e.g. es algebraico o estático), omitir la gráfica y reemplazar por una **visualización estática** de la solución con MathJax + descripción del comportamiento.

```
Título: "📊 Simulación del modelo"  ← siempre genérico
Controles: sliders + inputs numéricos sincronizados, botón Play/Pause
Indicadores dinámicos: los que apliquen al modelo (R₀, tasa de crecimiento, punto de equilibrio, etc.)
Pestañas de análisis bajo la gráfica:
```

##### Controles de parámetros — slider + input numérico sincronizados (OBLIGATORIO)

Cada parámetro debe tener **ambos controles vinculados**. Cambiar cualquiera de los dos actualiza el otro y redibuja la gráfica:

```html
<!-- Patrón HTML para cada parámetro (replicar por cada variable del modelo) -->
<div class="param-row">
  <label class="param-label">β <span class="param-desc">tasa de contagio</span></label>
  <div class="param-controls">
    <input type="range"  id="slider_beta" min="0.01" max="1.0" step="0.01" value="0.3"
           oninput="syncParam('beta', +this.value); updateChart()">
    <input type="number" id="input_beta"  min="0.01" max="1.0" step="0.01" value="0.3"
           oninput="syncParam('beta', +this.value); updateChart()">
  </div>
</div>
```

```javascript
// Mantener slider e input numérico siempre sincronizados
function syncParam(name, val) {
  // Clamp al rango del slider
  var slider = document.getElementById('slider_' + name);
  var minV = parseFloat(slider.min), maxV = parseFloat(slider.max);
  val = Math.max(minV, Math.min(maxV, isNaN(val) ? minV : val));
  slider.value = val;
  document.getElementById('input_' + name).value = val;
  // Guardar en objeto de parámetros globales
  simParams[name] = val;
}
```

```css
/* Estilos para la fila de controles */
.param-row {
  display: flex;
  align-items: center;
  gap: .75rem;
  margin-bottom: .6rem;
}
.param-controls {
  display: flex;
  align-items: center;
  gap: .5rem;
  flex: 1;
}
.param-controls input[type=range]  { flex: 1; accent-color: var(--accent); }
.param-controls input[type=number] {
  width: 70px;
  padding: .25rem .4rem;
  border: 1px solid var(--border);
  border-radius: 6px;
  font-family: 'IBM Plex Mono', monospace;
  font-size: 13px;
  color: var(--text);
  background: var(--surface);
  text-align: center;
}
.param-controls input[type=number]:focus {
  outline: none;
  border-color: var(--accent);
  box-shadow: 0 0 0 2px rgba(44,111,173,.15);
}
```

##### Botón Play / Pause con marcador animado (OBLIGATORIO)

El botón avanza un **marcador de tiempo** (línea vertical + punto destacado) a lo largo de la gráfica ya calculada mediante `requestAnimationFrame`. No recalcula la simulación en cada frame — solo mueve el marcador sobre los datos existentes, lo que garantiza fluidez.

```javascript
// Estado de la animación
var simParams = {};      // valores actuales de todos los parámetros
var simData   = {};      // { labels: [...], datasets: [{data:[...]}, ...] }
var animState = {
  running:  false,
  frameId:  null,
  step:     0,           // índice actual en simData.labels
  speed:    1            // pasos por frame (ajustable)
};

// Referencia al plugin de anotación del marcador
var timeMarkerPlugin = {
  id: 'timeMarker',
  afterDraw: function(chart) {
    if (animState.step <= 0) return;
    var ctx   = chart.ctx;
    var xAxis = chart.scales.x;
    var yAxis = chart.scales.y;
    var x     = xAxis.getPixelForValue(animState.step);
    ctx.save();
    // Línea vertical semitransparente
    ctx.beginPath();
    ctx.moveTo(x, yAxis.top);
    ctx.lineTo(x, yAxis.bottom);
    ctx.strokeStyle = 'rgba(255,255,255,0.5)';
    ctx.lineWidth   = 1.5;
    ctx.setLineDash([4, 3]);
    ctx.stroke();
    ctx.restore();
  }
};

function togglePlay() {
  animState.running = !animState.running;
  var btn = document.getElementById('btnPlayPause');
  if (animState.running) {
    btn.textContent = '⏸ Pausar';
    btn.classList.add('playing');
    animateStep();
  } else {
    btn.textContent = '▶ Reproducir';
    btn.classList.remove('playing');
    if (animState.frameId) cancelAnimationFrame(animState.frameId);
  }
}

function animateStep() {
  if (!animState.running) return;
  var total = simData.labels ? simData.labels.length : 0;
  animState.step = (animState.step + animState.speed) % total;
  if (window.simChart) window.simChart.update('none'); // 'none' = sin animación CSS
  animState.frameId = requestAnimationFrame(animateStep);
}

function resetAnimation() {
  animState.step    = 0;
  animState.running = false;
  var btn = document.getElementById('btnPlayPause');
  if (btn) { btn.textContent = '▶ Reproducir'; btn.classList.remove('playing'); }
  if (animState.frameId) cancelAnimationFrame(animState.frameId);
}
```

```html
<!-- Barra de controles de reproducción — colocar encima o debajo del canvas -->
<div class="playback-bar">
  <button id="btnPlayPause" class="btn-play" onclick="togglePlay()">▶ Reproducir</button>
  <button class="btn-reset"  onclick="resetAnimation(); updateChart()">⟳ Reiniciar</button>
  <label class="speed-label">
    Velocidad
    <input type="range" id="speedSlider" min="1" max="10" step="1" value="1"
           oninput="animState.speed = +this.value; document.getElementById('speedVal').textContent = this.value + 'x'">
    <span id="speedVal">1x</span>
  </label>
</div>
```

```css
.playback-bar {
  display: flex;
  align-items: center;
  gap: .75rem;
  flex-wrap: wrap;
  margin: .75rem 0;
}
.btn-play {
  background: var(--accent);
  color: #fff;
  border: none;
  border-radius: 8px;
  padding: .45rem 1.1rem;
  font-size: 13px;
  font-family: 'IBM Plex Sans', sans-serif;
  cursor: pointer;
  transition: background .2s;
}
.btn-play.playing { background: var(--accent3); }
.btn-play:hover   { filter: brightness(1.12); }
.btn-reset {
  background: transparent;
  color: var(--sky-lt);
  border: 1px solid var(--steel);
  border-radius: 8px;
  padding: .45rem .9rem;
  font-size: 13px;
  cursor: pointer;
}
.speed-label {
  display: flex;
  align-items: center;
  gap: .4rem;
  font-size: 12px;
  color: var(--sky-lt);
  font-family: 'IBM Plex Mono', monospace;
}
```

**Registro del plugin en Chart.js:**
```javascript
// Al crear el chart, pasar el plugin y activar el update 'none' para no conflictuar con la animación propia de Chart.js
window.simChart = new Chart(ctx, {
  type: 'line',
  data: simData,
  options: {
    animation: false,          // desactivar animación CSS de Chart.js
    responsive: true,
    // ... resto de opciones
  },
  plugins: [timeMarkerPlugin]  // registrar el marcador
});
```

Cada pestaña debe contener explicaciones **al nivel de 3° medio**, con:

**Pestaña "Límites":**
- Formulación formal con MathJax
- Explicación en lenguaje natural: qué significa "tender a", qué pasa con cada función a largo plazo
- Conexión visual: qué observar en la gráfica al mover los sliders
- Conceptos previos necesarios

**Pestaña "Derivadas":**
- Punto crítico relevante del modelo con MathJax
- Explicación intuitiva: qué mide la derivada, dónde está el máximo/mínimo, cómo afectan los parámetros
- Criterio de crecimiento/decrecimiento de cada función
- Conexión con la gráfica: dónde está ese punto, cómo desplazarlo

**Pestaña "Integrales":**
- Integral relevante del modelo con MathJax
- Explicación intuitiva: qué representa el área bajo la curva en el contexto del modelo
- Conexión con la ley de conservación si aplica

**Pestaña "Análisis"** (o denominación apropiada al modelo):
- Número adimensional o indicador clave del modelo (R₀, número de Lotka, tasa neta de reproducción, etc.)
- Regla práctica: qué valores del indicador implican estabilidad, brote, extinción, etc.
- Conexión con los sliders: cómo variar parámetros para cambiar el régimen

#### 4d. Glosario de variables
Grid 2 columnas, cada variable en card `--surface` con:
- Símbolo en `IBM Plex Mono`, color `--accent`, fondo `--surface2`
- Descripción breve a la derecha

#### 4e. Supuestos del modelo
Lista con íconos, fondo `--surface`, borde izquierdo `--accent`

### 5. DESARROLLO (Problema aplicado)

#### Enunciado
- Card sobre fondo oscuro (igual que motivación pero más compacta)
- Narrar el problema con gancho contextual
- Las ecuaciones específicas del problema (valores numéricos) en display `\[...\]`

#### Parte I — Variables
- Grid 2 columnas de `.var-item` (fondo `--surface`, hover → `--surface2`)
- Símbolo en monospace + input `border-bottom` animado
- Bloque de respuestas esperadas colapsado (`.answer-block`, max-height:0 → visible con clase)

#### Parte II — Preguntas de análisis
Para cada pregunta:
```html
<div class="q-item">
  <div class="q-header">
    <div class="q-num">N</div>  <!-- círculo gradiente -->
    <div class="q-text">Texto de la pregunta</div>
  </div>
  <textarea class="q-answer-area" rows="2" placeholder="Tu respuesta…"></textarea>
  <!-- OPCIÓN: campo de imagen -->
  <div class="img-upload-zone">📎 Adjuntar imagen (opcional)</div>
  <div class="answer-block" id="aN">
    <div class="answer-inner">
      <div class="ans-label">✅ Respuesta esperada</div>
      [respuesta detallada con LaTeX]
    </div>
  </div>
</div>
```

**⭐ UPLOAD DE IMÁGENES:** Agregar zona de carga de imagen debajo de cada textarea de pregunta:
```javascript
// La imagen se convierte a base64 y se muestra en miniatura inline
// El input type=file acepta image/*
// Preview con max-height:120px, border-radius:8px
// Botón ×  para eliminar
```

### 6. CIERRE / REFLEXIÓN
- Intro con gradiente oscuro + cita decorativa (`"` gigante, `Playfair Display`, `rgba(255,255,255,.06)`)
- Items en cards blancas con número lateral (gradiente vertical `--navy2 → --accent`)
- Textarea resize vertical con focus `--accent`
- **CTA del formulario:** Card con gradiente `--accent → --navy2`, botón blanco que abre la URL configurada por el docente

---

## LÓGICA JAVASCRIPT REQUERIDA

### ⚠️ ARQUITECTURA DE LA CONTRASEÑA — CRÍTICO (leer completo antes de codear)

Este es el único patrón correcto. No usar ninguna variante alternativa.

#### Principio fundamental: placeholder en texto plano

El archivo docente contiene literalmente el string `__SP_PLACEHOLDER__` en el código JS como valor inicial de `_sp`. Al exportar para estudiantes, se hace un **`.replace()` en texto plano** sobre el `outerHTML` completo — nunca manipulación DOM.

```
Por qué esto es correcto:
✅ El replace() en texto plano es 100% fiable
✅ No depende de que scripts se re-ejecuten (cloneNode no re-ejecuta scripts)
✅ No hay conflictos de scope ni redeclaraciones
✅ El DOMContentLoaded del archivo generado detecta limpiamente si _sp ya fue reemplazado
```

#### Estructura JS del archivo docente (modo Docente)

```javascript
// Variable global — declarar UNA sola vez con var al nivel del script principal
var _sp  = '__SP_PLACEHOLDER__';  // literal — se reemplaza al exportar
var masterPwd = '';
var formUrl   = '';

function safeB64Encode(str) {
  return btoa(unescape(encodeURIComponent(str)));
}

// Al establecer contraseña en modo docente
function setMasterPwd() {
  var input = document.getElementById('pwdInput');
  masterPwd = input.value.trim();
  if (!masterPwd) return;
  // Revelar todas las respuestas inmediatamente en modo docente
  document.querySelectorAll('.answer-block').forEach(function(b) {
    b.classList.add('visible');
  });
  document.getElementById('pwdStatus').textContent = '✓ Contraseña establecida';
}

// Exportar archivo para estudiantes
function exportStudent() {
  if (!masterPwd) { alert('Primero establece una contraseña.'); return; }
  var encoded = safeB64Encode(masterPwd + ':guia');

  // Tomar el HTML completo como texto plano y hacer replace del placeholder
  var html = document.documentElement.outerHTML;
  html = html.replace('__SP_PLACEHOLDER__', encoded);
  html = html.replace('__FU_PLACEHOLDER__', formUrl);

  var blob = new Blob(['<!DOCTYPE html>\n' + html], { type: 'text/html;charset=utf-8' });
  var url  = URL.createObjectURL(blob);
  var a    = document.createElement('a');
  a.href     = url;
  a.download = 'guia_estudiante.html';
  a.click();
  setTimeout(function() { URL.revokeObjectURL(url); }, 3000);
}
```

#### DOMContentLoaded — detección de modo

```javascript
document.addEventListener('DOMContentLoaded', function() {
  // Si _sp ya no es el placeholder → estamos en el archivo estudiante
  if (_sp !== '__SP_PLACEHOLDER__') {
    // Ocultar panel docente
    var tb = document.getElementById('teacherBar');
    if (tb) tb.style.display = 'none';
    // Mostrar unlock banner y barra de guardar
    var ub = document.getElementById('unlockBanner');
    if (ub) ub.style.display = 'flex';
    var sb = document.getElementById('studentSaveBar');
    if (sb) sb.style.display = 'flex';
    // Actualizar badge de modo
    var badge = document.getElementById('modeBadge');
    if (badge) badge.textContent = '● Modo Estudiante';
  }
  // Si _sp ES el placeholder → modo docente, no hacer nada especial
});
```

#### Función de desbloqueo del estudiante

```javascript
function globalUnlock() {
  var input = document.getElementById('studentPwdInput');
  var attempt = safeB64Encode(input.value.trim() + ':guia');
  if (attempt === _sp) {
    document.querySelectorAll('.answer-block').forEach(function(b) {
      b.classList.add('visible');
    });
    var ub = document.getElementById('unlockBanner');
    if (ub) ub.style.display = 'none';
  } else {
    document.getElementById('unlockError').textContent = '✕ Contraseña incorrecta';
  }
}
```

#### Botón "Ver todas respuestas" (solo modo docente)

```javascript
function viewAllAnswers() {
  document.querySelectorAll('.answer-block').forEach(function(b) {
    b.classList.add('visible');
  });
}
```

---

### Sistema de imágenes

**⚠️ Regla crítica:** Cuando el estudiante sube una imagen, esta se lee con `FileReader.readAsDataURL()` y el resultado se asigna al `src` de un `<img class="img-preview">` que **debe existir siempre en el DOM** (con `src=""` y `display:none` inicialmente). Así, al clonar el DOM para exportar, las imágenes base64 se incluyen automáticamente sin procesamiento adicional.

```javascript
function handleImageUpload(input, containerId) {
  var file = input.files[0];
  if (!file || !file.type.startsWith('image/')) return;
  var reader = new FileReader();
  reader.onload = function(e) {
    var container = document.getElementById(containerId);
    var img = container.querySelector('.img-preview');
    img.src = e.target.result;   // base64 queda en el DOM
    img.style.display = 'block';
    container.querySelector('.img-placeholder').style.display = 'none';
    container.querySelector('.img-remove').style.display = 'inline-flex';
  };
  reader.readAsDataURL(file);
}

function removeImage(containerId) {
  var container = document.getElementById(containerId);
  var img = container.querySelector('.img-preview');
  img.src = '';
  img.style.display = 'none';
  container.querySelector('.img-placeholder').style.display = 'flex';
  container.querySelector('.img-remove').style.display = 'none';
  container.querySelector('input[type=file]').value = '';
}
```

**HTML de cada zona de imagen:**
```html
<div class="img-upload-zone" id="imgZone_Q1">
  <input type="file" accept="image/*" onchange="handleImageUpload(this,'imgZone_Q1')" style="display:none" id="imgInput_Q1">
  <div class="img-placeholder" onclick="document.getElementById('imgInput_Q1').click()">
    📎 <span>Adjuntar imagen (opcional)</span>
  </div>
  <!-- img SIEMPRE en DOM, oculto hasta que se suba algo -->
  <img class="img-preview" src="" alt="Imagen adjunta"
       style="display:none;max-width:100%;max-height:180px;border-radius:8px;margin-top:.5rem;border:1px solid var(--border)">
  <button class="img-remove" style="display:none" onclick="removeImage('imgZone_Q1')">× Quitar imagen</button>
</div>
```

---

### Simulación numérica (método de Euler — genérico)

```javascript
// Adaptar según el modelo del HTML de entrada.
// Si el modelo es un sistema de EDO, usar Euler con dt = 0.5, steps = 300.
// Si el modelo es una ecuación explícita (ej. crecimiento exponencial, logístico),
// evaluar directamente en cada paso de tiempo.
// Los parámetros (nombres, rangos, valores por defecto) deben inferirse
// del glosario de variables y ecuaciones del modelo de entrada.
// Ejemplo genérico para sistema de EDO de 3 compartimentos:
//   dX1/dt = f1(X1, X2, X3, params)
//   dX2/dt = f2(X1, X2, X3, params)
//   dX3/dt = f3(X1, X2, X3, params)
// Indicadores dinámicos: calcular los que sean relevantes para el modelo
// (punto de equilibrio, máximo, valor asintótico, tasa de crecimiento, etc.)
```

---

### Guardar respuestas del estudiante — CRÍTICO

La barra inferior fija ("💾 Exportar mis respuestas") debe generar un HTML **completamente autocontenido** que preserve:

1. **Todos los textos escritos** en `textarea` e `input[type=text]`, `input[type=date]`
2. **Todas las imágenes subidas**, embebidas como base64 en el `src` de los `<img>` correspondientes
3. **El estado visual completo** — la página exportada debe verse idéntica a como estaba cuando el estudiante hizo clic en exportar

```javascript
function exportStudentAnswers() {
  // 1. Clonar el DOM completo del documento actual para capturar el estado vivo,
  //    incluyendo imágenes ya cargadas en base64
  var clone = document.documentElement.cloneNode(true);

  // 2. Copiar valores de textareas (cloneNode NO copia el value en vivo)
  var liveTas  = document.querySelectorAll('textarea');
  var cloneTas = clone.querySelectorAll('textarea');
  liveTas.forEach(function(ta, i) {
    if (cloneTas[i]) cloneTas[i].textContent = ta.value;
  });

  // 3. Copiar values de inputs de texto/fecha
  var liveIns  = document.querySelectorAll('input[type=text], input[type=date]');
  var cloneIns = clone.querySelectorAll('input[type=text], input[type=date]');
  liveIns.forEach(function(inp, i) {
    if (cloneIns[i]) cloneIns[i].setAttribute('value', inp.value);
  });

  // 4. Las imágenes base64 ya están en el src del <img> del DOM vivo;
  //    cloneNode las copia automáticamente.

  // 5. Ocultar barras de acción en la copia
  var saveBar   = clone.querySelector('#studentSaveBar');
  var unlockBar = clone.querySelector('#unlockBanner');
  if (saveBar)   saveBar.style.display   = 'none';
  if (unlockBar) unlockBar.style.display = 'none';

  // 6. Añadir sello de fecha
  var heroBadge = clone.querySelector('.hero-meta');
  if (heroBadge) {
    var stamp = document.createElement('div');
    stamp.style.cssText = 'margin-top:.6rem;font-size:11px;color:#4ade80;font-family:IBM Plex Mono,monospace';
    stamp.textContent   = '📄 Respuestas guardadas: ' + new Date().toLocaleString('es-CL');
    heroBadge.appendChild(stamp);
  }

  // 7. Serializar y descargar
  var nombre = '';
  var ni = document.querySelector('.field-item input[type=text]');
  if (ni && ni.value) nombre = '_' + ni.value.replace(/[^\w\u00e1-\u00fa\u00d1\u00f1 ]/g, '')
                                               .replace(/ /g, '_').substring(0, 20);
  var out  = '<!DOCTYPE html>\n' + clone.outerHTML;
  var blob = new Blob([out], { type: 'text/html;charset=utf-8' });
  var url  = URL.createObjectURL(blob);
  var a    = document.createElement('a');
  a.href     = url;
  a.download = 'respuestas' + nombre + '.html';
  a.click();
  setTimeout(function() { URL.revokeObjectURL(url); }, 3000);
}
```

---

## COMPATIBILIDAD TÉCNICA REQUERIDA

```css
/* Safari/iOS */
-webkit-backdrop-filter: blur(4px);
input[type=range] { -webkit-appearance: none; }
textarea { -webkit-appearance: none; border-radius: 6px; }
/* iOS no hace zoom si font-size >= 16px en inputs */
input[type=text], input[type=date], input[type=password] { font-size: 16px; }
/* safe-area para barra inferior fija en iPhone con notch */
@supports (-webkit-touch-callout: none) {
  #studentSaveBar { padding-bottom: calc(.8rem + env(safe-area-inset-bottom)); }
}
/* Respetar preferencias de movimiento reducido */
@media (prefers-reduced-motion: reduce) { *{ animation:none!important; transition:none!important } }
```

---

## INSTRUCCIONES FINALES

1. **Genera el HTML completo** en una única respuesta, sin truncar, sin `<!-- continúa -->`.
2. **No uses un `STUDENT_TEMPLATE` estático separado.** El archivo docente es el mismo archivo base para ambos modos. La diferencia entre modos la detecta el `DOMContentLoaded` según si `_sp` es el placeholder o no.
3. **Toda ecuación LaTeX** debe estar dentro de `\(...\)` (inline) o `\[...\]` (display). MathJax se inicializa con `tex:{inlineMath:[['\\(','\\)']],displayMath:[['\\[','\\]']]}`.
4. **Las respuestas esperadas** deben estar en `.answer-block` con `max-height:0; opacity:0` por defecto, y revelan con clase `.visible`.
5. **El formulario** se configura en el panel docente y se embebe en la URL del botón CTA de cierre.
6. Si la sección de motivación del HTML de entrada tiene ecuaciones LaTeX vacías o solo con texto `Se recomienda completar en Overleaf`, **igualmente genera la sección con un placeholder elegante** indicando dónde irá el modelo.
7. Respeta el orden de secciones tal como vienen en el HTML de entrada. No reordenes ni elimines contenido.
8. El estilo debe ser **consistente**: mismos gradientes, mismas familias tipográficas, misma densidad visual, misma paleta. Todas las guías generadas deben parecer de la misma familia.
9. **Persistencia de imágenes en la exportación:** Las imágenes subidas por el estudiante deben estar en `<img class="img-preview">` siempre presentes en el DOM (con `src=""` y `display:none` inicialmente). La exportación de respuestas usa `document.documentElement.cloneNode(true)` sobre el DOM vivo — este es el único método que garantiza que las imágenes base64 queden embebidas en el HTML exportado.
10. **Nunca redeclarar `_sp`** en ningún otro bloque `<script>`. Existe una sola declaración `var _sp = '__SP_PLACEHOLDER__'` en el script principal. El DOMContentLoaded la lee; no la reasigna.

---

## CÓMO USAR ESTE PROMPT

Copia este prompt completo y agrégalo como prefijo, luego pega el contenido del HTML básico exportado por el generador de guías. Ejemplo:

```
[TODO EL PROMPT DE ARRIBA]

---

## HTML DE ENTRADA A TRANSFORMAR

[pega aquí el contenido del archivo HTML básico]
```

Claude generará el HTML interactivo completo listo para usar.

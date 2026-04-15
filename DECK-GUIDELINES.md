# ChargeDeck Sales Deck — Guidelines & Playbook
> Cómo armar presentaciones PDF de alta calidad que rendericen bien en cualquier dispositivo.

---

## 1. Pipeline Técnico (El "cómo")

El objetivo es generar un PDF estático, no responsive, que se vea idéntico en desktop, mobile y proyectores.

### Paso 1: Maquetar en HTML
- Cada slide es un `<div class="slide">` con dimensiones fijas.
- Ratio recomendado: **4:3 vertical** (1200×900 px). Se proyecta bien y lee bien en mobile.
- No usar unidades relativas (`vw`, `vh`, `%`) para el layout de slides. Todo en `px`.
- Las fuentes se cargan desde Google Fonts (Inter + Nunito en este caso).
- Los iconos se cargan desde Iconify (Solar icons).

### Paso 2: Generar PDF con Playwright
```bash
npx playwright install chromium
node generate-pdf.js
```
El script abre el HTML en Chromium headless y hace `page.pdf()` con:
- `width: '1200px'`
- `height: '900px'`
- `printBackground: true`

### Paso 3: Flatten con PyMuPDF (CRÍTICO)
El PDF de Chromium tiene estructuras de "página web impresa" que el visor nativo de iPhone no sabe interpretar bien en horizontal. Esto causa cortes, escalados raros y elementos rotos.

**Solución:** rasterizar cada página a imagen y reconstruir el PDF.

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install pymupdf
python3 flatten-pdf.py
```

El script:
1. Renderiza cada página a imagen JPEG (200 DPI, calidad 95%).
2. Crea un PDF nuevo donde cada página es esa imagen fija.
3. Mantiene el tamaño exacto original (1200×900).

**Resultado:** PDF de imágenes nativas. Peso similar o menor. Compatibilidad 100% en iOS, Android, Preview, Acrobat, proyectores.

---

## 2. Mindset de Presentación vs. Página Web

| Web | Presentación PDF |
|-----|------------------|
| Responsive, fluido | Tamaño fijo, control absoluto |
| Scroll infinito | Slides con inicio y fin claros |
| Muchos elementos, densidad alta | Menos elementos, más aire |
| Textos pequeños, lectura cercana | Textos grandes, legibles desde lejos |
| Hover states, micro-interacciones | Nada de hover. Todo estático |
| Márgenes variables | Márgenes estrictos y generosos |

**Regla de oro:** Si un slide se siente "apretado", hay que sacar elementos o dividir en dos slides. Nunca sacrificar el aire.

---

## 3. Estructura de Slides

### Un tema = Un slide
Nunca apilar dos temas distintos en una misma página. Si hay mucho contenido, se parte en slides separados.

En este deck, la estructura final fue:
1. Cover
2. The Problem
3. Our Solution
4. Value We Create
5. Revenue Model
6. User Pricing
7. Advertising
8. Positioning
9. Closing / Thank you

### Cantidad máxima de elementos por slide
- **Cover:** 1 headline + 1 subheadline + 3-4 pills + 1 imagen principal
- **Tarjetas/cards:** Máximo 4 cards por slide (2×2 grid). Si son 6, considerar 2 slides o chips compactos.
- **Listas numeradas:** Máximo 3-4 pasos.
- **Precios:** Máximo 3 tiers. Si son verticales, el destacado debe romper visualmente (fondo de acento, mayor altura, tipografía más grande).
- **CTA final:** Un headline de cierre + contacto + botón + "Gracias"

---

## 4. Dimensiones y Proporciones

### Tamaño de slide
- **1200 px de ancho × 900 px de alto**
- Padding interno: **64–80 px** en todos los lados. Nada toca los bordes.

### Proporciones de columnas
- **50/50:** Texto a la izquierda, stat card/imagen a la derecha.
- **60/40 o 55/45:** Cuando una columna de imagen es vertical y necesita más espacio (ej. slide 1 y slide 3).
- **Columna de texto nunca menor a 40%** del ancho útil.

### Imágenes de producto
- Siempre con **crop controlado** (`object-fit: cover; object-position: center`) para que ocupen el espacio vertical sin dejar huecos.
- Contenedor con `border-radius` y `overflow: hidden`.
- Tamaños típicos: `440×440px` hasta `620×470px` según protagonismo.

---

## 5. Tipografía y Jerarquía

Todo debe ser legible proyectado desde 3 metros de distancia.

| Elemento | Tamaño | Peso | Notas |
|----------|--------|------|-------|
| H1 Cover | 72–78 px | 800 | Nunito, máximo impacto |
| H2 Slide | 44–52 px | 700–800 | Nunito o Inter según estilo de marca |
| H3 Card / Step | 26–30 px | 600–700 | Inter |
| Body / Copy | 18–22 px | 300–400 | Inter, line-height 1.45–1.55 |
| Label [01] | 13–14 px | 600 | Uppercase, tracking 1.5 px |
| Precio destacado | 64 px | 700 | Naranja `#EF5F3C` |
| Precio normal | 32–40 px | 700 | Negro `#0C0B07` |
| Pills / Chips | 14–16 px | 500 | Redondeados full |
| Footer / Fine print | 12–15 px | 400 | Gris atenuado |

**Regla:** Si un texto no se lee bien desde lejos, duplicar su tamaño o acortarlo.

---

## 6. Paleta de Colores (Ejemplo ChargeDock)

Cada deck debe tener una paleta estricta de máximo 4 colores:

- **Acento primario:** `#EF5F3C` (coral/naranja) — CTAs, highlights, iconos, tarjeta destacada
- **Fondo oscuro:** `#0C0B07` (near-black) — slides de problema, value, positioning
- **Fondo claro:** `#F6F6F6` (off-white) — slides de solución, revenue, advertising
- **Gradiente cálido:** `#F8F7F4` → `#FFE8E0` — slide de cierre

**Textos:**
- Oscuro sobre claro: `#0C0B07`
- Muted sobre claro: `#5E5E5E`
- Claro sobre oscuro: `#FFFFFF`
- Muted sobre oscuro: `#CFCFCF` / `#A3A3A3`

---

## 7. Reglas de Composición Visual

### Balance
- Nunca dejar grandes espacios vacíos en una mitad del slide mientras la otra está sobrecargada.
- Si una columna tiene poco texto, la imagen de la otra columna debe agrandarse para ocupar el espacio.

### Cards
- Padding interno: `22–32 px`.
- Bordes redondeados: `14–18 px`.
- Iconos dentro de cards: `44–52 px`.
- Títulos de cards: `28–30 px` en bold.
- Descripción: `17–18 px` en regular/light.
- Las cards deben sentirse "llenas", no flotando en un océano de padding vacío.

### Chips (para listas cortas)
- Forma de píldora (`border-radius: 999px`) o esquinas suaves.
- Padding compacto: `10–14 px` vertical, `18–24 px` horizontal.
- Apilados verticalmente cuando son más de 4.

### Destacados
- El elemento más importante de un slide debe romper el patrón:
  - Fondo de color acento.
  - Tipografía más grande.
  - Mayor altura o ancho.

---

## 8. Lecciones Aprendidas (Específicas de este proyecto)

### Qué funcionó bien
- **Split-screen en Positioning:** La mitad naranja sólida con lista de venues blancos fue el slide con más impacto visual.
- **Precio destacado en naranja:** Hacer la tarjeta del medio más alta, con fondo naranja y tipografía doble de tamaño funciona muy bien como "best seller".
- **Imágenes crop vertical:** Las fotos de producto con `object-fit: cover` y contenedores más altos que anchos dan protagonismo sin dejar huecos.
- **Cover limpio:** Sin botón, sin chip extra. Solo headline, subheadline, pills e imagen.

### Qué no funcionó inicialmente
- **HTML puro a PDF sin flatten:** Se veía roto en iPhone. El flatten es obligatorio.
- **Muchos elementos en un slide:** El primer intento de Revenue + Pricing juntos quedaba desbalanceado con un gap gigante abajo.
- **Cards con mucho padding vertical:** Se veían estiradas y vacías. Se corrigió reduciendo el padding y aumentando el contenido dentro.
- **Emojis en lugar de iconos:** Rompían la cohesión de marca. Iconify (Solar) es la solución.
- **Scroll jump en mobile:** `scrollRestoration = 'manual'` + resize de viewport causaba saltos aleatorios. Se resolvió con `ScrollTrigger.config({ ignoreMobileResize: true })`.

---

## 9. Checklist para la Próxima Presentación

### Antes de diseñar
- [ ] Definir la paleta de 4 colores máximo.
- [ ] Definir las fuentes (1 para headlines, 1 para body).
- [ ] Listar los iconos que usará (preferiblemente de una sola familia, ej. Solar).
- [ ] Escribir el contenido por slide antes de tocar código.
- [ ] Asegurarse de que cada slide tenga **1 tema único**.

### Durante el diseño HTML
- [ ] Tamaño fijo: 1200×900 px.
- [ ] Padding interno mínimo 64 px.
- [ ] Tipografías grandes (H2 mínimo 44 px, body mínimo 18 px).
- [ ] Máximo 4 cards por slide.
- [ ] Imágenes con `object-fit: cover` si son protagonistas.
- [ ] No usar emojis. Usar iconos vectoriales.
- [ ] No hay hover states ni animaciones. Todo estático.

### Antes de publicar
- [ ] Generar PDF con Playwright.
- [ ] Ejecutar `flatten-pdf.py` para rasterizar y reconstruir.
- [ ] Abrir el PDF en **Preview** (Mac) y verificar que se vea bien.
- [ ] Enviárselo a alguien con **iPhone** para validar el visor nativo.
- [ ] Si se ve cortado o escalado raro: revisar que el flatten se haya ejecutado sobre el archivo correcto.

---

## 10. Archivos de este Repo

| Archivo | Propósito |
|---------|-----------|
| `deck.html` | Maqueta de los 9 slides. Se regenera para cada nuevo deck. |
| `generate-pdf.js` | Script de Playwright para convertir HTML a PDF. |
| `flatten-pdf.py` | Script de PyMuPDF para "aplanar" el PDF y asegurar compatibilidad mobile. |
| `assets/chargedock-sales-deck.pdf` | PDF final, listo para compartir. |
| `DECK-GUIDELINES.md` | Este documento. |

---

## 11. Ejemplo de Flujo Completo (para copiar y pegar)

```bash
# 1. Editar deck.html con el nuevo contenido

# 2. Generar PDF desde Chromium
node generate-pdf.js

# 3. Flatten para mobile
source .venv/bin/activate
python3 flatten-pdf.py

# 4. Commit y push
git add deck.html assets/nuevo-deck.pdf .gitignore
git commit -m "Add nuevo sales deck"
git push
```

---

*Última actualización: Abril 2025*

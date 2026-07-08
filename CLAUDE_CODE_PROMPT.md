# PROMPT PARA CLAUDE CODE — Marketing Research Exam Simulator

## CÓMO USAR ESTO
1. Crea una carpeta nueva para el proyecto (ej. `mktr-exam-sim`).
2. Copia dentro el archivo **`questions_bank.json`** (te lo he generado aparte, tiene 150 preguntas — 25 por bloque temático).
3. Abre esa carpeta en Claude Code (`claude` en terminal, o la app de escritorio/VS Code).
4. Pega TODO el texto de abajo (desde "## CONTEXTO" hasta el final) como tu primer mensaje a Claude Code.

---

## CONTEXTO

Soy estudiante de 2º de carrera de Marketing Research (asignatura en inglés). Voy a presentarme a un examen de recuperación: 30 preguntas tipo test, 4 opciones cada una, puntuación +1 acierto / −0.25 fallo / 0 en blanco. Quiero una app web para practicar simulacros ilimitados de ese examen, generados a partir de un banco de 150 preguntas que ya tengo preparado en `questions_bank.json` (en la misma carpeta que este prompt).

## QUÉ QUIERO QUE CONSTRUYAS

Una app web de una sola página (self-contained, sin necesidad de backend ni build step) que simule el examen real y corrija automáticamente. Debe poder abrirse directamente con doble clic (`index.html`) o servirse con un servidor estático simple.

### 1. Fuente de datos
- Lee el archivo `questions_bank.json` que está en la misma carpeta. Cada pregunta tiene este formato:
```json
{
  "id": "b1_01",
  "block": 1,
  "blockName": "Research Design & Data Types",
  "question": "...",
  "options": ["...", "...", "...", "..."],
  "correctIndex": 0,
  "explanations": ["...", "...", "...", "..."]
}
```
- Hay 6 bloques temáticos (`block` 1-6), 25 preguntas cada uno.
- **IMPORTANTE**: en vez de usar `fetch()` para cargar el JSON (esto falla por CORS si el archivo se abre directamente con `file://`), incrusta el contenido de `questions_bank.json` directamente como una constante JS dentro del HTML/bundle en tiempo de build, O bien monta un pequeño servidor estático de una línea (ej. con `serve` o `http-server` vía npm) y documenta cómo lanzarlo en el README. Prefiero la opción de incrustar el JSON si es sencillo de mantener; si prefieres servidor, está bien, pero indícamelo claramente.

### 2. Generación del examen
- Cada "Empezar examen" nuevo selecciona **30 preguntas al azar del banco de 150**, manteniendo la proporción entre bloques: **5 preguntas de cada uno de los 6 bloques** (5×6=30).
- Baraja también el ORDEN de las 4 opciones de cada pregunta en cada intento (recalculando `correctIndex` y reordenando `explanations` en consecuencia), para que no se memoricen posiciones.
- Baraja el orden final de las 30 preguntas.

### 3. Pantalla de inicio
- Explica el formato: 30 preguntas, +1/−0.25/0.
- Botón "Empezar examen".
- Si hay intentos previos guardados (ver sección de persistencia), muestra un historial compacto: fecha, nota sobre 30, mini barra de progreso.

### 4. Pantalla de examen
- Muestra las 30 preguntas (todas visibles en scroll, o una por pantalla — tu elección de UX, pero que sea rápido de usar).
- Cada pregunta con 4 opciones seleccionables (un solo seleccionable por pregunta).
- Barra de progreso mostrando cuántas preguntas llevo contestadas (X/30).
- Cronómetro opcional contando el tiempo transcurrido.
- Antes de enviar, si hay preguntas sin contestar, muestra una advertencia: *"Tienes X preguntas sin contestar. Con esta puntuación (+1/−0.25/0), adivinar siempre da mejor resultado esperado que dejarlo en blanco. ¿Seguro que quieres enviar así?"* — pero permite enviar igualmente si confirmo.

### 5. Corrección y resultados
- Calcula la nota total: acierto +1, fallo −0.25, blanco 0.
- Muestra la nota total sobre 30, y un indicador visual de si estoy en zona de aprobado (≈15/30, que es el 50%) o suspenso.
- **Desglose por bloque**: para cada uno de los 6 bloques, cuántas acerté de las que me tocaron en ese examen, y los puntos netos de ese bloque. Esto es lo más importante — necesito ver claramente en qué bloque tengo que reforzar.
- **Revisión pregunta por pregunta**: para cada una de las 30 preguntas del intento, muestra:
  - El enunciado
  - Las 4 opciones
  - Cuál marqué yo (si marqué alguna)
  - Cuál es la correcta
  - La explicación de CADA opción (por qué es correcta o incorrecta) — no solo de la que elegí
  - Un marcador visual claro: ✅ acertada / ❌ fallada / ⬜ en blanco
- Filtros para ver solo: todas / falladas / en blanco / acertadas.
- Botón para repetir con 30 preguntas nuevas, y botón para volver al inicio.

### 6. Persistencia entre sesiones
- Usa `localStorage` (esto es una app standalone, no un artifact de Claude.ai, así que `localStorage` funciona perfectamente aquí).
- Guarda cada intento: fecha, nota, nº de preguntas en blanco, desglose por bloque, tiempo empleado.
- Guarda hasta los últimos 20 intentos.
- En la pantalla de inicio, muestra la evolución de mis últimas notas (lista o gráfico simple de barras).
- Añade un botón discreto para "borrar historial" por si quiero empezar de cero.

### 7. Diseño visual
Dirección de diseño — evita el look genérico de IA (fondo crema + acento naranja/terracota, o fondo negro + verde ácido, o periódico con líneas finas). En su lugar:
- Estética "papel de examen / laboratorio de datos": fondo claro con un tono frío (grises azulados, no crema), tinta oscura azul-carbón para el texto principal.
- Un acento principal tipo verde azulado/teal (no naranja/terracota) y un acento secundario dorado/ámbar para detalles puntuales.
- Tipografía: una serif con carácter para títulos, una monoespaciada para números/puntuaciones/cronómetro (encaja con el tema de estadística del curso), una sans-serif limpia para el cuerpo de texto.
- Como elemento distintivo, en la pantalla de resultados usa una visualización que recuerde a un "intervalo de confianza" (una barra horizontal con un marcador de posición) para mostrar la nota — es un guiño temático porque el curso trata justo de intervalos de confianza y estadística.
- Verde/rojo semántico SÍ debe usarse para acertado/fallado (no sacrifiques la claridad por el diseño ahí).
- Responsive: tiene que verse bien en móvil.

### 8. Entregables
- `index.html` (o estructura equivalente) listo para abrir/ejecutar.
- Si usas algún framework o necesitas `npm install`, un `README.md` corto con los pasos exactos para ejecutarlo.
- Si detectas errores en alguna pregunta del JSON al implementarlo (typos, formato raro), dímelo, pero NO cambies el contenido de las preguntas ni las respuestas correctas sin decírmelo explícitamente — el contenido viene directamente de mis apuntes de clase y ya está verificado.

---

## PRIORIDAD

Si tienes que recortar algo por tiempo, este es el orden de importancia:
1. Selección aleatoria de 30 preguntas + puntuación correcta (+1/−0.25/0) — imprescindible
2. Revisión pregunta por pregunta con explicaciones de las 4 opciones — imprescindible
3. Desglose de rendimiento por bloque — muy importante
4. Persistencia de historial en localStorage — importante
5. Barajado de opciones y cronómetro — deseable
6. Pulido visual y responsive — deseable, pero no sacrifiques lo anterior por esto

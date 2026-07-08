# MKTR Exam Sim

Simulador de examen para Marketing Research. Genera simulacros de 30 preguntas (5 de cada uno de los 6 bloques) a partir del banco de 150 preguntas de `questions_bank.json`, con corrección automática (+1 / −0.25 / 0), desglose por bloque, revisión pregunta por pregunta y guardado de historial.

## Cómo ejecutarlo

No necesita `npm install` ni build. El contenido de `questions_bank.json` está embebido directamente dentro de `index.html`, así que basta con abrir el archivo:

**Opción A — doble clic**
Abre `index.html` con doble clic. Funciona directamente en `file://` porque el banco de preguntas está incrustado (no usa `fetch`, así que no hay problemas de CORS).

**Opción B — servidor estático (opcional)**
Si prefieres servirlo por HTTP:
```
python -m http.server 8000
```
y abre `http://localhost:8000/index.html`.

## Si actualizas `questions_bank.json`

`index.html` lleva el JSON incrustado como una copia estática dentro de un `<script type="application/json" id="qbank-data">`. Si editas `questions_bank.json`, tienes que regenerar `index.html`. Puedes hacerlo con:

```js
// regen.js
const fs = require('fs');
const template = fs.readFileSync('index.html', 'utf8');
const json = fs.readFileSync('questions_bank.json', 'utf8');
const updated = template.replace(
  /(<script type="application\/json" id="qbank-data">)[\s\S]*?(<\/script>)/,
  (_, open, close) => open + '\n' + json + '\n' + close
);
fs.writeFileSync('index.html', updated);
```
```
node regen.js
```

(Dímelo si prefieres que te deje este script guardado en el repo en vez de aquí en el README.)

## Notas sobre el banco de preguntas

Validé `questions_bank.json` al implementarlo: 150 preguntas, 25 por bloque (6 bloques), sin IDs duplicados, y las 150 con exactamente 4 opciones / 4 explicaciones / `correctIndex` válido (0-3). No encontré errores de formato ni typos que afecten a la lógica. No he tocado el contenido de ninguna pregunta ni respuesta.

## Persistencia

El historial de intentos se guarda en `localStorage` del navegador (clave `mktrExamHistory_v1`), hasta los últimos 20 intentos. Es local a cada navegador/dispositivo — no se sincroniza ni se envía a ningún sitio. Hay un botón "Borrar historial" en la pantalla de inicio para reiniciarlo.

# Unidad 3

## Bitácora de proceso de aprendizaje

### Bridge.js
```js
const WebSocket = require('ws');
const osc = require('node-osc');

// 1. CONFIGURACIÓN
const STRUDEL_PORT = 8080;   // Donde Strudel envía los datos
const P5JS_PORT = 8081;      // Donde p5.js escuchará
//const SC_PORT = 57120;       // Puerto de SuperCollider (SuperDirt)
const SC_HOST = '127.0.0.1';
const RELAY_PORT = 8082;

// 2. CLIENTE OSC (Para enviar sonido a SuperCollider)
//const oscClient = new osc.Client(SC_HOST, SC_PORT);

// 3. SERVIDOR WEBSOCKET PARA STRUDEL (Puerto 8080)
const wssStrudel = new WebSocket.Server({ port: STRUDEL_PORT });

// 4. SERVIDOR WEBSOCKET PARA P5.JS (Puerto 8081)
const wssP5 = new WebSocket.Server({ port: P5JS_PORT });

const relayClient = new WebSocket(`ws://localhost:${RELAY_PORT}`);

relayClient.on('open', () => {
    console.log('🔗 Conectado a relay OSC (Open Stage)');
});

relayClient.on('message', (message) => {
    try {
        const msg = JSON.parse(message.toString());

        // reenviar a p5
        const payload = JSON.stringify(msg);

        wssP5.clients.forEach(client => {
            if (client.readyState === WebSocket.OPEN) {
                client.send(payload);
            }
        });

    } catch (e) {
        console.error('Error procesando mensaje del relay:', e);
    }
});

console.log(`🚀 Super Bridge Iniciado`);
console.log(`- Escuchando a Strudel en ws://localhost:${STRUDEL_PORT}`);
console.log(`- Transmitiendo a p5.js en ws://localhost:${P5JS_PORT}`);
//console.log(`- Enviando audio a SuperCollider en ${SC_HOST}:${SC_PORT}`);

wssStrudel.on('connection', (ws) => {
    console.log('✅ Strudel conectado al Bridge');

    ws.on('message', (message) => {
        try {
            const msg = JSON.parse(message);
            // msg suele ser: { address: '/dirt/play', args: [...], timestamp: 12345 }

            // A. ENVIAR A SUPERCOLLIDER (OSC)
            // node-osc espera: .send(address, val1, val2, ...)
            // oscClient.send(msg.address, ...msg.args, (err) => {
            //     if (err) console.error('Error enviando OSC:', err);
            // });

            // B. REENVIAR A P5.JS (WebSocket)
            // Enviamos el JSON tal cual, ya trae el st_ts si lo inyectaste en osc.mjs
            const payload = JSON.stringify(msg);
            
            wssP5.clients.forEach(client => {
                if (client.readyState === WebSocket.OPEN) {
                    client.send(payload);
                }
            });

        } catch (e) {
            console.error('Error procesando mensaje de Strudel:', e);
        }
    });
});

wssP5.on('connection', (ws) => {
    console.log('🎨 p5.js se ha conectado al Bridge');


    
});
```

### VisualesMin Actualzado

```html
<!DOCTYPE html>
<html>
<head>
  <script src="https://cdn.jsdelivr.net/npm/p5@1.11.11/lib/p5.min.js"></script>
  <style> body { margin: 0; overflow: hidden; background: black; } </style>
</head>
<body>
<script>

  let eventQueue = [];
  let activeAnimations = [];
  const LATENCY_CORRECTION = 0;

  // 🔥 Valores base (musicales)
  let baseKickSizeMin = 100;
  let baseKickSizeMax = 600;
  let baseKickAlpha = 255;

  // 🎛 Modulación desde Open Stage
  let modSize = 1.0;
  let modAlpha = 1.0;

  function setup() {
    createCanvas(windowWidth, windowHeight);
    rectMode(CENTER);
    noStroke();

    const socket = new WebSocket('ws://localhost:8081');

    socket.onmessage = (event) => {
        const msg = JSON.parse(event.data);

        // 🎛 CONTROL DESDE OPEN STAGE
        if (msg.address === "/xy_1") {
          let x = msg.args[0]; // 0–1
          let y = msg.args[1]; // 0–1

          // Escala multiplicativa (no reemplaza la música)
          modSize = map(x, 0, 1, 0.5, 2.5);
          modAlpha = map(y, 0, 1, 0.3, 1.5);

          return;
        }

        // 🎵 EVENTOS DESDE STRUDEL
        let params = {};
        for (let i = 0; i < msg.args.length; i += 2) {
            params[msg.args[i]] = msg.args[i+1];
        }

        if (!msg.timestamp || !params.s) return;

        eventQueue.push({ 
          timestamp: msg.timestamp, 
          sound: params.s,
          delta: params.delta || 0.25
        });

        eventQueue.sort((a, b) => a.timestamp - b.timestamp);
    };
  }

  function draw() {
    background(0, 30); 
    let now = Date.now() + LATENCY_CORRECTION;

    while (eventQueue.length > 0 && now >= eventQueue[0].timestamp) {
        let ev = eventQueue.shift();

        activeAnimations.push({
          startTime: ev.timestamp,
          duration: ev.delta * 1000,
          type: ev.sound,
          x: random(width * 0.2, width * 0.8), 
          y: random(height * 0.2, height * 0.8),
          color: getColorForSound(ev.sound)
        });
    }

    for (let i = activeAnimations.length - 1; i >= 0; i--) {
      let anim = activeAnimations[i];
      let elapsed = now - anim.startTime;
      let progress = elapsed / anim.duration;

      if (progress <= 1.0) {
        dibujarElemento(anim, progress);
      } else {
        activeAnimations.splice(i, 1);
      }
    }
  }

  function dibujarElemento(anim, p) {
    push();
    const color = anim.color;

    switch (anim.type) {
      case 'tr909bd':
        dibujarBombo(p, color);
        break;

      case 'tr909sd':
        dibujarCaja(p, color);
        break;

      case 'tr909hh':
      case 'tr909oh':
        dibujarHat(anim, p, color);
        break;

      default:
        dibujarDefault(anim, p, color);
        break;
    }
    pop();
  }

  // 🎵 BOMBO REACTIVO + 🎛 MODULADO
  function dibujarBombo(p, c) {

    // Aplicamos modulación multiplicativa
    let sizeMin = baseKickSizeMin * modSize;
    let sizeMax = baseKickSizeMax * modSize;
    let alphaMax = baseKickAlpha * modAlpha;

    let d = lerp(sizeMin, sizeMax, p);
    let alpha = lerp(alphaMax, 0, p);

    fill(c[0], c[1], c[2], alpha);
    circle(width / 2, height / 2, d);
  }

  function dibujarCaja(p, c) {
    let w = lerp(width, 0, p);
    let alpha = lerp(255, 0, p);
    fill(c[0], c[1], c[2], alpha);
    rect(width / 2, height / 2, w, 50);
  }

  function dibujarHat(anim, p, c) {
    let sz = lerp(40, 0, p);
    fill(c[0], c[1], c[2]);
    rect(anim.x, anim.y, sz, sz);
  }

  function dibujarDefault(anim, p, c) {
    let size = lerp(100, 0, p);
    let angle = p * TWO_PI;

    translate(anim.x, anim.y);
    rotate(angle);

    stroke(c[0], c[1], c[2]);
    strokeWeight(2);
    noFill();

    rect(0, 0, size, size);
  }

  function getColorForSound(s) {
    const colors = {
      'tr909bd': [255, 0, 80],
      'tr909sd': [0, 200, 255],
      'tr909hh': [255, 255, 0],
      'tr909oh': [255, 150, 0]
    };
    return colors[s] || [200, 200, 200];
  }

  function windowResized() { 
    resizeCanvas(windowWidth, windowHeight); 
  }

</script>
</body>
</html>
```

## Bitácora de aplicación 

### Código de las visuales
```html
<!DOCTYPE html>
<html>
<head>
  <script src="https://cdn.jsdelivr.net/npm/p5@1.9.0/lib/p5.min.js"></script>
  <style>
    body { margin: 0; overflow: hidden; background: black; }
  </style>
</head>
<body>
<script>

const PALETTE = {
  kick:   [255, 80, 120],
  snare:  [80, 255, 180],
  hihat:  [120, 160, 255],
  piano:  [100, 200, 255]
};

let eventQueue = [];
let visuals = [];
let pianoRoll = [];

let lastDelta = 0;
let history = [];

// 🎛 MODULACIÓN OPEN STAGE
let modEnergy = 1.0;
let modSpeed  = 1.0;
let modAlpha  = 1.0;
let modR = 1.0;
let modG = 1.0;
let modB = 1.0;

function setup() {
  createCanvas(windowWidth, windowHeight);
  frameRate(60);
  noFill();

  const socket = new WebSocket('ws://localhost:8081');

  socket.onmessage = (event) => {
    const msg = JSON.parse(event.data);

    // -------------------------
    // 🎛 CONTROL OPEN STAGE
    // -------------------------

    if (msg.address === "/xy_1") {
      let x = msg.args[0];
      let y = msg.args[1];

      modEnergy = map(x, 0, 1, 0.5, 3.0);
      modSpeed  = map(y, 0, 1, 0.5, 2.5);
      return;
    }

    if (msg.address === "/fader_1") {
      modAlpha = map(msg.args[0], 0, 1, 0.2, 1.5);
      return;
    }

    if (msg.address === "/rgb_1") {
      function normalize(v) {
        v = Number(v);
        if (v > 1) v = v / 255;
        return constrain(v, 0, 1);
      }
      modR = normalize(msg.args[0]);
      modG = normalize(msg.args[1]);
      modB = normalize(msg.args[2]);
      return;
    }

    // -------------------------
    // 🎵 EVENTOS MUSICALES
    // -------------------------

    let params = {};
    for (let i = 0; i < msg.args.length; i += 2) {
      params[msg.args[i]] = msg.args[i + 1];
    }

    eventQueue.push({
      timestamp: msg.timestamp,
      params
    });

    eventQueue.sort((a, b) => a.timestamp - b.timestamp);
  };
}

function draw() {
  background(0, 40);
  let now = Date.now();

  drawPianoRoll();

  while (eventQueue.length > 0 && now >= eventQueue[0].timestamp) {
    let ev = eventQueue.shift();

    lastDelta = now - ev.timestamp;
    history.push(lastDelta);
    if (history.length > 60) history.shift();

    ejecutarVisual(ev.params);
  }

  drawVisuals();
  drawDebug();
}

function ejecutarVisual(p) {

  if (typeof p.note === "number") {
    pianoRoll.push({
      note: p.note,
      y: 0,
      speed: map(p.gain ?? 0.7, 0, 1, 2, 6) * modSpeed,
      alpha: 255
    });
  }

  if (!p.s) return;
  const s = p.s.toLowerCase();

  if (s.includes("bd")) {
    visuals.push({
      type: "pulse",
      r: 10,
      life: 1,
      color: PALETTE.kick
    });
  }

  if (s.includes("sd") || s.includes("rim")) {
    visuals.push({
      type: "burst",
      angle: random(TWO_PI),
      life: 1,
      color: PALETTE.snare
    });
  }

  if (s.includes("hh")) {
    visuals.push({
      type: "line",
      x: random(width),
      life: 1,
      color: PALETTE.hihat
    });
  }
}

function drawVisuals() {
  visuals = visuals.filter(v => v.life > 0);

  visuals.forEach(v => {

    let r = v.color[0] * modR;
    let g = v.color[1] * modG;
    let b = v.color[2] * modB;

    stroke(r, g, b, 200 * v.life * modAlpha);

    if (v.type === "pulse") {
      strokeWeight(2 * modEnergy);
      ellipse(width / 2, height / 2, v.r * modEnergy);
      v.r += 20 * modEnergy;
    }

    if (v.type === "burst") {
      strokeWeight(2 * modEnergy);
      let len = map(v.life, 1, 0, 20, 200) * modEnergy;
      line(
        width / 2,
        height / 2,
        width / 2 + cos(v.angle) * len,
        height / 2 + sin(v.angle) * len
      );
    }

    if (v.type === "line") {
      strokeWeight(4 * modEnergy);
      line(v.x, 0, v.x, height);
    }

    v.life -= 0.05 * modSpeed;
  });
}

function drawPianoRoll() {
  pianoRoll = pianoRoll.filter(n => n.alpha > 0 && n.y < height + 40);

  pianoRoll.forEach(n => {
    let x = map(n.note, 30, 90, 80, width - 80);

    let r = PALETTE.piano[0] * modR;
    let g = PALETTE.piano[1] * modG;
    let b = PALETTE.piano[2] * modB;

    stroke(r, g, b, n.alpha * modAlpha);
    strokeWeight(10 * modEnergy);

    line(x, n.y, x, n.y + 30);

    n.y += n.speed * modSpeed;
    n.alpha -= 2 * modSpeed;
  });
}

function drawDebug() {
  fill(0,0,0,0);
  noStroke();
  textSize(14);
  text(`Δ sync: ${lastDelta.toFixed(2)} ms`, 20, 30);
  text(`FPS: ${Math.round(frameRate())}`, 20, 50);
}

function windowResized() {
  resizeCanvas(windowWidth, windowHeight);
}

</script>
</body>
</html>
```
### Código bridge

```js
const WebSocket = require('ws');
const osc = require('osc');

// 1. CONFIGURACIÓN
const STRUDEL_PORT = 8080;   // Donde Strudel envía los datos
const P5JS_PORT = 8081;      // Donde p5.js escuchará
//const SC_PORT = 57120;       // Puerto de SuperCollider (SuperDirt)
const SC_HOST = '127.0.0.1';
const RELAY_PORT = 8082;      // Puerto para reenviar OSC a p5.js

// 2. CLIENTE OSC (Para enviar sonido a SuperCollider)
//const oscClient = new osc.Client(SC_HOST, SC_PORT);

// 3. SERVIDOR WEBSOCKET PARA STRUDEL (Puerto 8080)
const wssStrudel = new WebSocket.Server({ port: STRUDEL_PORT });

// 4. SERVIDOR WEBSOCKET PARA P5.JS (Puerto 8081)
const wssP5 = new WebSocket.Server({ port: P5JS_PORT });

console.log(`🚀 Super Bridge Iniciado`);
console.log(`- Escuchando a Strudel en ws://localhost:${STRUDEL_PORT}`);
console.log(`- Transmitiendo a p5.js en ws://localhost:${P5JS_PORT}`);
//console.log(`- Enviando audio a SuperCollider en ${SC_HOST}:${SC_PORT}`);
const relayPort = new WebSocket(`ws://localhost:${RELAY_PORT}`); // Su función es conectarse al puerto para recibir las señales OSC y reenviarlas a p5.js


relayPort.on('open', () => {
    console.log('🔁 Relay conectado para reenviar OSC a p5.js');

});

relayPort.on('message', (message) => {
    try {
            const msg = JSON.parse(message);

            const payload = JSON.stringify(msg);
            
            wssP5.clients.forEach(client => {
                if (client.readyState === WebSocket.OPEN) {
                    client.send(payload);
                }
            });

        } catch (e) {
            console.error('Error procesando mensaje de OpenStageControl:', e);
        }
});


wssStrudel.on('connection', (ws) => {
    console.log('✅ Strudel conectado al Bridge');

    ws.on('message', (message) => {
        try {
            const msg = JSON.parse(message);
            // msg suele ser: { address: '/dirt/play', args: [...], timestamp: 12345 }

            // A. ENVIAR A SUPERCOLLIDER (OSC)
            // node-osc espera: .send(address, val1, val2, ...)
            // oscClient.send(msg.address, ...msg.args, (err) => {
            //     if (err) console.error('Error enviando OSC:', err);
            // });

            // B. REENVIAR A P5.JS (WebSocket)
            // Enviamos el JSON tal cual, ya trae el st_ts si lo inyectaste en osc.mjs
            const payload = JSON.stringify(msg);
            
            wssP5.clients.forEach(client => {
                if (client.readyState === WebSocket.OPEN) {
                    client.send(payload);
                }
            });

        } catch (e) {
            console.error('Error procesando mensaje de Strudel:', e);
        }
    });
});

wssP5.on('connection', (ws) => {
    console.log('🎨 p5.js se ha conectado al Bridge');
});
```




## Bitácora de reflexión




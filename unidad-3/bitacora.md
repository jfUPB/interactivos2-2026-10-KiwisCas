# Unidad 3

## Bitácora de proceso de aprendizaje

### Bridge.js
```js
const WebSocket = require('ws');
const osc = require('osc');

// 1. CONFIGURACIÓN
const STRUDEL_PORT = 8080;   // Donde Strudel envía los datos
const P5JS_PORT = 8081;      // Donde p5.js escuchará
const OSC_PORT = 9000;
//const SC_PORT = 57120;       // Puerto de SuperCollider (SuperDirt)
const SC_HOST = '127.0.0.1';

// 2. CLIENTE OSC (Para enviar sonido a SuperCollider)
//const oscClient = new osc.Client(SC_HOST, SC_PORT);

// 3. SERVIDOR WEBSOCKET PARA STRUDEL (Puerto 8080)
const wssStrudel = new WebSocket.Server({ port: STRUDEL_PORT });

// 4. SERVIDOR WEBSOCKET PARA P5.JS (Puerto 8081)
const wssP5 = new WebSocket.Server({ port: P5JS_PORT });

console.log(`🚀 Super Bridge Iniciado`);
console.log(`- Escuchando a Strudel en ws://localhost:${STRUDEL_PORT}`);
console.log(`- Transmitiendo a p5.js en ws://localhost:${P5JS_PORT}`);
console.log(`- Escuchando a OpenStageControl en ws:localhost:${OSC_PORT}`);
//console.log(`- Enviando audio a SuperCollider en ${SC_HOST}:${SC_PORT}`);

const udpPort = new osc.UDPPort({
    localAddress: "0.0.0.0",
    localPort: OSC_PORT
});
udpPort.open();

udpPort.on("message", (msg, timeTag, info) => {
    const payload = {
        address: msg.address,
        args: msg.args || [],
        tiemestamp: Date.now()
    };
    broadcasstToP5(payload);

    console.log("Osc Recibido:", msg.address);
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

function broadcasstToP5(msg) {
    const payload = JSON.stringify(msg);

    wssP5.clients.forEach(client => {
        if (client.readyState === WebSocket.OPEN) {
            client.send(payload);
        }
    });
}
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



## Bitácora de reflexión

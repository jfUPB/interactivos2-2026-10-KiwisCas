# Unidad 2

## Bitácora de proceso de aprendizaje


## Bitácora de aplicación 

### Sobre el proceso del apply

A la hora de hacer el código para las visuales tuve que entender algo que no tenía del todo claro y era la forma en la cual enviar señales ```.osc``` al servidor debido a que inicialmente no me hacía idea alguna de como iba a poder enviar un código con tantas cosas para que empezara a enviar tantos datos al bridge, afortunadamente, tomando de referencia los casos de estudio pude descubrir que si, ponía todas las cosas dentro de una variable y enviaba dos tipos de señales, el bridge empezó a mostrar estos mensajes en consola:

<img width="1886" height="179" alt="image" src="https://github.com/user-attachments/assets/1300441f-9d57-4d0c-b18e-ccad27e5d550" />

Lo cual fue indicativo de que el bridge si estaba escuchando las señales que estaba mandando strudel por lo que podía seguir para revisar como podía revisar las señales en la página para los visuales, acá fue a donde me dirigí a ejecutar los visuales y fue cuando observé lo siguiente

<img width="1909" height="903" alt="image" src="https://github.com/user-attachments/assets/2b36d8a2-356a-4a23-8b26-8c07317feb0f" />

Si bien parecía que las señales si eran recibidas de forma correcta, no se estaban dibujando y si me metía a la consola efectivamente podía ver que si estaba recibiendo mensajes del strudel por lo que el problema efectivamente no era ese si no que debía investigar más a fondo dentro del código de la página:


<img width="629" height="452" alt="image" src="https://github.com/user-attachments/assets/af9cbd37-5f64-42d1-9f83-6584d5063267" />


Ingresando a visual me percaté de las siguientes lineas y fue donde caí en cuenta de lo que ocurría y era teniendo como base esta linea de código

<img width="731" height="277" alt="image" src="https://github.com/user-attachments/assets/b3b060e9-9814-4da6-8e6b-ce355ecdee91" />

visualizando el contenido enviado por el strudle aparece que la forma en la que se envían los datos no es exactamente a las que reaccionan las visuales por lo que, sustituyendo la linea de código que reaccionaba a ciertos eventos por la siguiente

```html
function ejecutarVisual(p) {
  if (!p.s) return;

  if (p.s.includes('bd'))  diametroBD = 350;
  if (p.s.includes('sd'))  diametroSD = 300;
  if (p.s.includes('hh'))  diametroHH = 200;
  if (p.s.includes('rim')) diametroOH = 180;
  if (p.s.includes('lt'))  diametroOH = 220;
  if (p.s.includes('rd'))  diametroOH = 260;
```

el resultado fue el siguiente:


https://github.com/user-attachments/assets/be540874-1cb7-4f46-b481-9390f9ede64b

Por lo que en vista de que ya pude hacer que las señales fueran recibidas, con ayuda de una ia intenté hacer unos cambios y el resultado fue el siguiente



https://github.com/user-attachments/assets/3da748b2-d9dd-4d47-94b6-79cb1fc5f7a1




<img width="1486" height="745" alt="image" src="https://github.com/user-attachments/assets/8375bf56-a6cc-42fb-8a79-dad175adbec1" />



### Página para ver los visuales

Para el código en strudel haz clic [aquí](https://strudel.cc/#CgpzYW1wbGVzKCdnaXRodWI6ZWRkeWZsdXgvY3JhdGUnKQpzZXRjcHMoLjc1KQpsZXQgY2hvcmRzID0gY2hvcmQoIjxDIzIgR2I%2BLzQiKS5kaWN0KCdpcmVhbCcpCmxldCBldmUgPSBzdGFjaygKICBzdGFjayggCiAgICBzKCJiZCIpLnN0cnVjdCgiPFt4KjwyIDI%2BIFt%2BQDMgeF1dIHg%2BIiksCiAgICBzKCJ%2BIFtyaW0sIHNkOjwyIDM%2BXSIpLnJvb20oIjwwIC4yPiIpLAogICAgbigiWzAgPDEgMz5dKjwyITMgND4iKS5zKCJoaCIpLAogICAgbigiWzAgPDEgMj5dKjwxITMgND4iKS5zKCJsdCIpLmhwZihzbGlkZXIoMzkzLCAwLCAxMDAwKSksCiAgICBzKCJyZDo8MSEzIDI%2BKjIiKS5tYXNrKCI8MSAxIDEgMT4vMTYiKS5nYWluKC41KQogICkuYmFuaygnY3JhdGUnKQogIC5tYXNrKCI8WzEgMV0gMSAwIDE%2BLzE2Ii5lYXJseSguNSkpLl9waWFub3JvbGwoe3ZlcnRpY2FsOjAsZmxpcFRpbWU6MCxmaWxsOjAsbGFiZWxzOjF9KQogICwKICBjaG9yZHMub2Zmc2V0KC0xKS52b2ljaW5nKCkucygiZ21fYWNvdXN0aWNfYmFzczoxIikKICAucGhhc2VyKDQpLnJvb20oLjUpLl9waWFub3JvbGwoe3ZlcnRpY2FsOjAsZmxpcFRpbWU6MCxmaWxsOjAsbGFiZWxzOjF9KSwKICBjaG9yZHMub2Zmc2V0KC0yKS52b2ljaW5nKCkucygiZ21fc3ludGhfYmFzc18yOjMiKQogIC5waGFzZXIoNCkucm9vbSguNSkubHBmKHNsaWRlcigyNTcsIDAsIDEwMDApKS5fcGlhbm9yb2xsKHt2ZXJ0aWNhbDowLGZsaXBUaW1lOjAsZmlsbDowLGxhYmVsczoxfSksCiAgCm4oIjwwITMgMSoyPiIpLnNldChjaG9yZHMpLm1vZGUoInJvb3Q6ZzIiKQogIC52b2ljaW5nKCkucygiZ21fZXBpYW5vMjoxIiksCiAgY2hvcmRzLm4oIlsxIDw0IDMgPDIgNT4%2BKjJdKDwzIDU%2BLDgpIikKICAuYW5jaG9yKCJnNCIpLnZvaWNpbmcoKQogIC5zZWdtZW50KDQpLmNsaXAocmFuZC5yYW5nZSguNCwuOCkpCiAgLnJvb20oLjc1KS5zaGFwZSguMykuZGVsYXkoLjI1KQogIC5mbShzaW5lLnJhbmdlKDMsOCkuc2xvdyg4KSkKICAubHBmKHNpbmUucmFuZ2UoNTAwLDEwMDApLnNsb3coOCkpLmxwcSg1KQogIC5yYXJlbHkocGx5KCIyIikpLmNodW5rKDQsIGZhc3QoMikpCiAgLmdhaW4ocGVybGluLnJhbmdlKC42LCAuOSkpCiAgLm1hc2soIjwwIDEgMSAwPi8xNiIpLAopCi5sYXRlKCJbMCAuMDFdKjQiKS5sYXRlKCJbMCAuMDFdKjIiKS5zaXplKDQpCgoKJDogc3RhY2soCiAgY2hvcmRzLmdhaW4oJzAuNScpLAogIGNob3Jkcy5vc2MoKQopCiQ6IHN0YWNrKAogIGV2ZS5nYWluKCcwLjUnKSwKICBldmUub3NjKCkKKQoKCgoK)


### Código de Visuales

```html
<!DOCTYPE html>
<html>
<head>
  <script src="https://cdn.jsdelivr.net/npm/p5@1.9.0/lib/p5.min.js"></script>
  <style> body { margin: 0; overflow: hidden; background: black; } </style>
</head>
<body>
<script>


  let eventQueue = [];
  let diametroBD = 0;
  let diametroSD = 0;
  let diametroHH = 0;
  let diametroOH = 0;


  let lastDelta = 0; // Para medir la precisión
  let history = [];  // Para ver el historial de precisión

  function setup() {
    createCanvas(windowWidth, windowHeight);
    noStroke();
    frameRate(60);
    textSize(32);

    const socket = new WebSocket('ws://localhost:8081');
    socket.onmessage = (event) => {
        const msg = JSON.parse(event.data);
        console.log('Mensaje OSC recibido:', msg);
        let params = {};
        for (let i = 0; i < msg.args.length; i += 2) {
            params[msg.args[i]] = msg.args[i+1];
        }
        eventQueue.push({ timestamp: msg.timestamp, params: params });
        eventQueue.sort((a, b) => a.timestamp - b.timestamp);
    };
  }

  function draw() {
    background(0, 30); // Estela más larga para ver mejor el movimiento
    let now = Date.now();

    // --- LÓGICA DE DIAGNÓSTICO GRÁFICO ---
    drawDebugTimeline(now);

    // --- PROCESAMIENTO DE EVENTOS ---
    while (eventQueue.length > 0 && now >= eventQueue[0].timestamp) {
        let ev = eventQueue.shift();
        
        // Calculamos el error de sincronía (0 sería perfecto)
        lastDelta = now - ev.timestamp; 
        history.push(lastDelta);
        if(history.length > 50) history.shift();

        ejecutarVisual(ev.params);
    }    

    // --- VISUAL PRINCIPAL ---
    if (diametroBD > 0) {
        fill(255, 0, 100);
        circle(width/2, height/2, diametroBD);
        diametroBD *= 0.85;
    }
    
    if (diametroSD > 0) {
        fill(0, 255, 100);
        circle(width/2, height/2-(height/2)/2, diametroSD);
        diametroSD *= 0.85;
    }
    if (diametroHH > 0) {
        fill(100, 100, 255);
        circle(width/2-(width/2)/2, height/2, diametroHH);
        diametroHH *= 0.85;
    }
    if (diametroOH > 0) {
        fill(255, 255, 100);
        circle(width/2+(width/2)/2, height/2, diametroOH);
        diametroOH *= 0.85;
    }

    


    // --- TEXTO DE DEBUG ---
    fill(255);
    textAlign(LEFT);
    text(`Último error de sincronía: ${lastDelta.toFixed(2)} ms`, 20, 50);
    text(`FPS: ${Math.round(frameRate())}`, 20, 90);
  }

  function drawDebugTimeline(now) {
    push();
    translate(0, height - 200);
    
    // Línea de "AHORA" (el punto crítico)
    stroke(255, 0, 0);
    line(width / 2, 0, width / 2, 80);
    noStroke();
    fill(255, 0, 0);
    text("AHORA", width / 2 + 5, 15);

    // Dibujar eventos futuros que vienen en camino
    // Rango de visualización: 1 segundo (500ms antes y después)
    let escala = 0.5; // pixeles por milisegundo
    
    eventQueue.forEach(ev => {
        let x = width / 2 + (ev.timestamp - now) * escala;
        fill(0, 255, 255);
        ellipse(x, 40, 20, 20);
    });

    // Dibujar historial de precisión (puntos blancos)
    // Si los puntos están a la derecha de la línea roja, vas tarde.
    history.forEach((d, i) => {
        fill(255, 255, 255, i * 5);
        ellipse(width / 2 + d * escala, 60, 10, 10);
    });
    pop();
  }
  
  function ejecutarVisual(p) {
  if (!p.s) return;

  if (p.s.includes('bd'))  diametroBD = 350;
  if (p.s.includes('sd'))  diametroSD = 300;
  if (p.s.includes('hh'))  diametroHH = 200;
  if (p.s.includes('rim')) diametroOH = 180;
  if (p.s.includes('lt'))  diametroOH = 220;
  if (p.s.includes('rd'))  diametroOH = 260;
}


  function windowResized() { resizeCanvas(windowWidth, windowHeight); }


</script>
</body>
</html>

```



## Bitácora de reflexión

<img width="1423" height="721" alt="image" src="https://github.com/user-attachments/assets/14cce600-96d7-4420-a0c5-54e00c60058c" />






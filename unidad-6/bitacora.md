# Unidad 6



## Bitácora de proceso de aprendizaje

### ¿Qué tipo de visuales van a acompañar tu audio? (formas geométricas, partículas, texturas orgánicas, arte generativo abstracto, representaciones figurativas, etc.)
Inicialmente habé de un proceso geométrico abstracto, sin embargo, debido a la forma que hice la explicación a juanfer se me lanzó la recomendación de hacer unas visuales que utilizaran gaussian splatting, es decir, hacer uso de partículas para generar geometría de un entorno del mundo real escaneado, sin embargo, debido a la poca facilidad de herramientas con las que contaba debido a temas de pago, opté por hacer algo simple inicialmente, es decir, tomaría geometrías hechas en blender o programas de modelado, y las adaptaría a un diseño con partículas y abstractismo.

### ¿Qué paleta de colores usarás y por qué?
Me sonaba la idea de hacer algo con un estilo minimalista, es decir, colores grises y blancos debido a que el fondo de las visuales van a ser negras, sin embargo, me parecía intersante la idea de que las visuales también ayudaran a generar un poco de color por lo menos durante todo el proceso de la música hasta su final, por eso, me gustaba la idea de usar colores entre azules, rojos y su combianción para mostrar la generación de la estructura que quería mostrar al final.

### ¿Cómo reaccionarán las visuales al audio? (¿Qué parámetros del audio controlarán qué aspectos visuales?)

Debido a que el sistema funcionará por medio de POPs, me gustaría que todo reaccionara con el noise que se genera en uno de los parámetros que alteran la armonía de la forma original, sin embargo, también me gustaría que fueran esos mismos parámetros de audio lo que controlen la forma que se supone, va a ser el resultado de todo el recorrido musical para que al final se pueda apreciar la forma y adicionando lo que dije anteriormente, es decir, el color de las partículas.

<img width="400" height="556" alt="image" src="https://github.com/user-attachments/assets/ac222631-e4a5-4f5a-b0cf-188093985d58" />

---
### ¿Por qué estas decisiones estéticas sirven al concepto de tu obra?

Porque como dije al principio, me gusta la idea de como puede llegar a fusionarse la geometría, siendo inicialmente abstracta y rara y llegando a ser algo reconocible a nuestros ojos, formas que ya hemos visto y que, a pesar de que el proceso fue lento y extraño, va a llegar a tener su compensación y es, por decirlo así, calma.

<img width="646" height="698" alt="image" src="https://github.com/user-attachments/assets/dd116525-9dc6-4910-8083-88b577f808b3" />


### Bocetos o mockups de cómo imaginas las visuales.

<img width="352" height="631" alt="image" src="https://github.com/user-attachments/assets/40eb45cf-2f07-4402-a7e5-5f5c1b15b8ac" />

<img width="549" height="714" alt="image" src="https://github.com/user-attachments/assets/60cb21b6-087b-452c-8b15-8749c6367dca" />


### El mapeo entre parámetros de audio y parámetros visuales (por ejemplo: frecuencia → color, amplitud → tamaño, etc.).

"Acá voy a dejar las configuraciones tal cual como las dejé habiendo hecho la sincronización"


<img width="400" height="556" alt="image" src="https://github.com/user-attachments/assets/ac222631-e4a5-4f5a-b0cf-188093985d58" />

<img width="460" height="123" alt="image" src="https://github.com/user-attachments/assets/bfaa2991-8fb4-49ba-a305-0993e31fd767" />



### Técnicas exploradas

Me gustaba hacer sistemas de partículas y geometría generativa, inicialmente como dije, quería usar gaussian splatting para poder hacer la geometría, sin embergo, debido a ausencia de recursos (no hay plata :((( ) tomé geometría de blender para hacer un sistema ya defoinido pero que este estalle debido a las partículas que se mueven de forma aleatoria, al final acabé combinando el sistema de partículas con un poco de geometría generativa predefinida con anterioridad para hacer el efecto que se veía en las imágenes de arriba. por lo que un resultado de esta interacción podría ser el que utilicé en el diseño final.

<img width="614" height="713" alt="image" src="https://github.com/user-attachments/assets/c6397dd1-af21-45ce-81e4-c5f1404e2ab9" />



## Bitácora de aplicación 

### El proceso de implementación de las visuales.
Inicialmente busqué hacer o encontrar una geometría que ayudara un poco a la estética de las visuales, me agradaba la idea de la estatua de un ange debido a la estética mística que este implica y ya que me gustó, la implementé en touchdesigner, sin embargo, necesitaba hacer que esta se distorsionara y puesto que distorcionar una geometría no es tan flexible en temas de que no se vea de una forma rara (no en el sentido que busco), por lo que decidí convertir la geometría en una nube de puntos que me va a ayudar en temas de deformación de la geometría, sin embargo, cuando apliqué el noise pasó esto

<img width="792" height="715" alt="image" src="https://github.com/user-attachments/assets/7e186c29-577d-4e89-8145-9f9be896523d" />

y pues, a pesar de que era lo que necesitaba no estaba del todo bien implementado ya que de alguna forma necesitaba volver a la geometría original y no embalarme por lo que descubrí el operador delete que me iba a ayudar a mantener las primitivas de los puntos en cierto lugar que yo deseara que iba a ser, la forma original de la geometría.

### Cómo lograste la sincronización entre audio y visuales.
Debo resaltar acá que utilicé en primera instancia una forma de envío de información diferente a la que el profesor nos envió (a día que escribo esto no sé como poder hacer la sincronización con las herramientas del profesor pero sigo trabajando en eso, pero entonces, la forma en que hice para que la sincornización funcionara es por medio de los lags, unos operadores dentro de touch que agregan delay a la información entrante y, poder así arreglar el tema de la sincronización en touch, no es la mejor forma ya que es algo robusta y puede ser dificil cambiarla a futuro, sin embargo, funciona.

### Las decisiones técnicas y estéticas que tomaste y por qué.
El tema de la sincronización fue de la forma del lag, debio a que en primera intancia es un poco más sencilla, sin embargo a largo plazo puede ser poco facil de implementar si es en más de un proyecto.
las decisiones estéticas ya las expliqué anteriormente.

El código completo de tu pieza audiovisual.

```strudel

setcpm(20)

const BASS_ON = 1
const PIANO_ON = 1
const MELODY_ON = 0
const MELODY2_ON = 0
const MELODY3_ON = 0

let bass = note("{<a1 gb1 db1 e1>}")
  .sound("supersaw")
  .lpf(slider(888.2,300,2000))
  .room(1)
  .postgain(BASS_ON ? 0.6 :0)

let piano = note ("{<[e3,a3] [gb2,a3] [a3,db4] [e3,b4]>}")
  .sound("piano")
  .attack(slider(0.061,0,1))
  .lpf(slider(1525.7,300,2000))
  .room(1)
  .postgain(PIANO_ON ? 0.6 :0)

let melody = note("<0@2 1 3>"
            .pick([
              "{a3@2 [e4][b3]}",
              "{a3@2 [e4][b3 a3]}",
              "{b3@a4}"
            ])
          )
  .transpose(24)
  .sound("gm_violin")
  .attack(slider(0.665,0,1))
          .lpf(slider(1590.3,300,2000))
          .delay(.25)
          .release(0.1)
          .postgain(MELODY_ON ? 0.7 :0)

let melody2 = note("<0@3 1>"
             .pick([
              "{db3!2 [a2][a2 a2]}",
              "{ab3@3 -}"
             ])
             )
  .transpose(12)
  .sound("gm_violin")
  .postgain(0.5)
  .room(1)
  .attack(slider(0.561,0,1))
  .lpf(slider(1525.7,300,2000))
  .postgain(MELODY2_ON ? 0.7 :0)

let melody3 = note("<0>"
             .pick([
              "{[a3 ab3][e3 a2] [a3 ab3] [e3 a2]}*2",
             ])
  )
  .transpose(24)
  .sound("gm_xylophone")
  .postgain(0.5)
  .room(1)
  .delay(0.25)
  .lpf(slider(1170.4,300,2000))
  .postgain(MELODY3_ON ? 0.7 :0)

$: stack(
  bass,
  bass.osc())

$: stack(
  piano,
  piano.osc())

$: stack(
  melody,
  melody.osc())

$: stack(
  melody2,
  melody2.osc())

$: stack(
  melody3,
  melody3.osc())


```


Instrucciones paso a paso para reproducir tu obra hasta este punto.


el proceso es bastante sencillo, inicialmente recomiendo para mi proceso utilizar la instalación locar de strudel, pero recordar que una vez se instale utilzar los comandos 
```bash
npm install
npm dev
```

luego de esto se debe de abrir otra consola más y ejectuar el siguiente comando 
```bash
npm dev osc
```

esto va a hacer que el strudel empiece a enviar señales osc al sistema de touch sin mucha complicación, en caso de que las señales no se estén recibiendo en touch por favor revisa que el puerto si sea el adecuado y segundo, que el strudel si esté enviado OSC desde su propio sistema en la consola, en caso de que ninguna de ambas funcione, prueba borrar el puerto del operador OSC in de touch y volver a ponerlo


## Bitácora de reflexión


### Evalúa la coherencia entre audio y visuales. ¿Se logra la experiencia deseada según tu concepto? ¿Qué ajustarías?
me gusta la coherencia que tengo con mi audio en mis visuales, me parece que está bastante bien logradas en términos de estética, sin embargo, algo que no pude realizar bien es como las partículas van tomando forma progresivamente y es algo que me gustaría ajustar en el futuro

### Actualiza el diagrama de sistema de tu obra incorporando la capa visual y su conexión con el audio.

<img width="1027" height="754" alt="image" src="https://github.com/user-attachments/assets/76b9e497-f495-462c-9326-0db8b9bd7f1a" />

---

### Describe los principales desafíos de integración que enfrentaste y cómo los resolviste.
Como tal no enfrenté muchos desafíos haciendo el tema de la integración de la forma que conozco, sin embargo, todavía sigo investigando el tema de como poder hacer la integración con los toxes que juanfer nos facilitó pero espero en aglún punto poder enviar los mensajes utilizando toxes y pudiendo así, resolver el problema de sicnronziación de una mejor forma .

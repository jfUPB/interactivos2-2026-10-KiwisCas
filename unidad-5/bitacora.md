# Unidad 5
## Bitácora de proceso de aprendizaje

### Ideas para la presentación de visuales en Sistemas Físicos

### 1. Cual es la idea que quiero mostrar?

Inicialmente me gustaría partir para mostrar algo abstracto en una primera instancia, haciendo uso prinicpalmente de formas geométricas podrían ser inicialmente en 2 dimensiones que vayan por etapas desde lo abstarcto en su totalidad, el intento de converger en algo y finalmente, formas y obras mostrandose en su totalidad, me gustaría que se sienta la intriga y la duda de todo lo que al final va a resultar de todo, me parece bien la idea de usar TouchDesigner para el tema de las visuales bajo la razón de interacción un poco más personal llegando a ser inmersiva y por la facilidad de que los usuarios puedan interactuar ya sea no del todo pero si de una gran forma para que puedan sentirse parte de todo el proceso de la música y visuales.

### ¿En que contexto se presentará?

Me imagino la idea viéndose reflejada en un espacio oscuro o en un escenario con poca iluminación, me gusta la idea de que todo se vea reflejado con temas de visuales en espacios oscuros, ya que siempre he sido fanático de la estética que refleja en estos espacios los espacios oscuros porque se encarga de dar más protagonismo a lo que se ve en un punto y a lo que se siente

### Cuál es la experiencia que deseas para el público?

Quiero que sea una experiencia contemplativa-participativa, ya que me gustaría que los usuarios puedan contemplar lo abstracto y la forma de las visuales, y que, como la idea sea la formación de algo, me gustaría que los usuarios sientan que tengan esa sensación de que están haciendo parte de la obra.

### ¿Qué rol tendrá el público?

Me gustaría que tuvieran un rol de participante activo, es decir que puedan tener participación activa en la obra y que, a pesar de que no influya de una gran forma en el resultado final (por ahora) puedan sentirse de que en parte han hecho algo para contribuir al resultado final.


## Referentes artísticos

## [Torin Blankensmith](https://www.youtube.com/@blankensmithing)
## [Ben Heim](https://www.youtube.com/@BenHeim/shorts)



## Bitácora de aplicación 
### Documentación de proceso de creación del audio generativo

Debo recalcar que inicialmente mi estrategia era usar algo más abstracto pero luego me gustó la idea de hacer algo más místico y que sonara inspirador ya que como a find e cuentas no pude hacer el arte con una estructura más conocida en el día a día, decidí utilizar algo que, a pesar de que no es algo que se suele ver mucho, se conoce el concepto y parece interesante aplicado a la forma en como está conformada la música

### Las decisiones técnicas y estéticas que tomaste y por qué.

inicialmente por temas de facilidad a la hora de manejar las visuales, me gustaba la idea de poder tener partes en donde, al igual que se dejaran de enviar señales a touch, se dejara de escuchar y no tener que hacer demasiados cambios en el código y tener que tardarme mucho, por lo que decidí almacenar partes de la música dentro de variables para poder habilitarlas y deshabilitarlas a mi conveniencia, aparte que, debido a la forma que conozco sobre la comunicación entre touch y strudel, almacenar partes de la música en variables es mucho más facil y práctico.

### El código completo de tu pieza de audio.

```

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

### Instrucciones paso a paso para reproducir tu audio.

hacer la reproducción es bastante sencilla, en la aprte de arriba hay unos consts con dos valores diferentes (1 y 0) cuando el valor sea 0, no se habilitará la entrada de sonido ni el envío de protocolos, y cuando sea 1, se habilitará, eso es todo :D



## Bitácora de reflexión

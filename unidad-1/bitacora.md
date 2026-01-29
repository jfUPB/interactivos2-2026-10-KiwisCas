# Unidad 1

## Bitácora de proceso de aprendizaje

### Actividad 1, Pruebas de funcionamiento de la aplicación

<img width="1919" height="913" alt="image" src="https://github.com/user-attachments/assets/9743840d-a0d0-4517-8048-9dd8928d03ce" />

<img width="1919" height="774" alt="image" src="https://github.com/user-attachments/assets/8f5abbb9-7bc7-4139-9c53-f72714bfdbf8" />


## Bitácora de aplicación 



### Código de la pieza

Para acceder a la reproducción da clic [aquí](https://strudel.cc/#CnNhbXBsZXMoJ2dpdGh1YjplZGR5Zmx1eC9jcmF0ZScpCnNldGNwcyguNzUpCmxldCBjaG9yZHMgPSBjaG9yZCgiPEMjMiBHYj4vNCIpLmRpY3QoJ2lyZWFsJykKc3RhY2soCiAgc3RhY2soIAogICAgcygiYmQiKS5zdHJ1Y3QoIjxbeCo8MiAyPiBbfkAzIHhdXSB4PiIpLAogICAgcygifiBbcmltLCBzZDo8MiAzPl0iKS5yb29tKCI8MCAuMj4iKSwKICAgIG4oIlswIDwxIDM%2BXSo8MiEzIDQ%2BIikucygiaGgiKSwKICAgIG4oIlswIDwxIDI%2BXSo8MSEzIDQ%2BIikucygibHQiKS5ocGYoc2xpZGVyKDM5MywgMCwgMTAwMCkpLAogICAgcygicmQ6PDEhMyAyPioyIikubWFzaygiPDEgMSAxIDE%2BLzE2IikuZ2FpbiguNSkKICApLmJhbmsoJ2NyYXRlJykKICAubWFzaygiPFsxIDFdIDEgMCAxPi8xNiIuZWFybHkoLjUpKS5fcGlhbm9yb2xsKHt2ZXJ0aWNhbDowLGZsaXBUaW1lOjAsZmlsbDowLGxhYmVsczoxfSkKICAsCiAgY2hvcmRzLm9mZnNldCgtMSkudm9pY2luZygpLnMoImdtX2Fjb3VzdGljX2Jhc3M6MSIpCiAgLnBoYXNlcig0KS5yb29tKC41KS5fcGlhbm9yb2xsKHt2ZXJ0aWNhbDowLGZsaXBUaW1lOjAsZmlsbDowLGxhYmVsczoxfSksCiAgY2hvcmRzLm9mZnNldCgtMikudm9pY2luZygpLnMoImdtX3N5bnRoX2Jhc3NfMjozIikKICAucGhhc2VyKDQpLnJvb20oLjUpLmxwZihzbGlkZXIoMjU3LCAwLCAxMDAwKSkuX3BpYW5vcm9sbCh7dmVydGljYWw6MCxmbGlwVGltZTowLGZpbGw6MCxsYWJlbHM6MX0pLAogIAogIG4oIjwwITMgMSoyPiIpLnNldChjaG9yZHMpLm1vZGUoInJvb3Q6ZzIiKQogIC52b2ljaW5nKCkucygiZ21fZXBpYW5vMjoxIiksCiAgY2hvcmRzLm4oIlsxIDw0IDMgPDIgNT4%2BKjJdKDwzIDU%2BLDgpIikKICAuYW5jaG9yKCJnNCIpLnZvaWNpbmcoKQogIC5zZWdtZW50KDQpLmNsaXAocmFuZC5yYW5nZSguNCwuOCkpCiAgLnJvb20oLjc1KS5zaGFwZSguMykuZGVsYXkoLjI1KQogIC5mbShzaW5lLnJhbmdlKDMsOCkuc2xvdyg4KSkKICAubHBmKHNpbmUucmFuZ2UoNTAwLDEwMDApLnNsb3coOCkpLmxwcSg1KQogIC5yYXJlbHkocGx5KCIyIikpLmNodW5rKDQsIGZhc3QoMikpCiAgLmdhaW4ocGVybGluLnJhbmdlKC42LCAuOSkpCiAgLm1hc2soIjwwIDEgMSAwPi8xNiIpLAopCi5sYXRlKCJbMCAuMDFdKjQiKS5sYXRlKCJbMCAuMDFdKjIiKS5zaXplKDQp)

```
samples('github:eddyflux/crate')
setcps(.75)
let chords = chord("<C#2 Gb>/4").dict('ireal')
stack(
  stack( 
    s("bd").struct("<[x*<2 2> [~@3 x]] x>"),
    s("~ [rim, sd:<2 3>]").room("<0 .2>"),
    n("[0 <1 3>]*<2!3 4>").s("hh"),
    n("[0 <1 2>]*<1!3 4>").s("lt").hpf(slider(393, 0, 1000)),
    s("rd:<1!3 2>*2").mask("<1 1 1 1>/16").gain(.5)
  ).bank('crate')
  .mask("<[1 1] 1 0 1>/16".early(.5))._pianoroll({vertical:0,flipTime:0,fill:0,labels:1})
  ,
  chords.offset(-1).voicing().s("gm_acoustic_bass:1")
  .phaser(4).room(.5)._pianoroll({vertical:0,flipTime:0,fill:0,labels:1}),
  chords.offset(-2).voicing().s("gm_synth_bass_2:3")
  .phaser(4).room(.5).lpf(slider(257, 0, 1000))._pianoroll({vertical:0,flipTime:0,fill:0,labels:1}),
  
  n("<0!3 1*2>").set(chords).mode("root:g2")
  .voicing().s("gm_epiano2:1"),
  chords.n("[1 <4 3 <2 5>>*2](<3 5>,8)")
  .anchor("g4").voicing()
  .segment(4).clip(rand.range(.4,.8))
  .room(.75).shape(.3).delay(.25)
  .fm(sine.range(3,8).slow(8))
  .lpf(sine.range(500,1000).slow(8)).lpq(5)
  .rarely(ply("2")).chunk(4, fast(2))
  .gain(perlin.range(.6, .9))
  .mask("<0 1 1 0>/16"),
)
.late("[0 .01]*4").late("[0 .01]*2").size(4)
```

### Sobre la explicación del código

#### Configuración inicial

Para comenzar, cargué un banco de sonidos externo que contiene principalmente samples de percusión:

```js
samples('github:eddyflux/crate')
```
Este banco fue utilizado como base para los sonidos rítmicos de la pieza.

Luego definí la velocidad general del sistema:

```js
setcps(.75)
```
Esto controla el tempo global. Al usar un valor menor a 1, la pieza tiene un ritmo más lento y permite apreciar mejor las capas que se van sumando.


#### Definición de acordes
A continuación, definí una progresión de acordes que funciona como base armónica de la composición:

```js
let chords = chord("<C#2 Gb>/4").dict('ireal')
```
Estos acordes se reutilizan en diferentes partes del código, como el bajo y los elementos melódicos, lo que ayuda a mantener coherencia musical en toda la pieza.

#### Construcción de la base rítmica
La sección rítmica se construye usando varios patrones que se ejecutan al mismo tiempo:

```js
stack(
  stack( 
    s("bd").struct("<[x*<2 2> [~@3 x]] x>"),
    s("~ [rim, sd:<2 3>]").room("<0 .2>"),
    n("[0 <1 3>]*<2!3 4>").s("hh"),
    n("[0 <1 2>]*<1!3 4>").s("lt").hpf(slider(723, 0, 1000)),
    s("rd:<1!3 2>*2").mask("<1 1 1 1>/16").gain(.5)
  ).bank('crate')
)
```
El bombo (bd) marca el pulso principal, pero con variaciones y silencios.

La caja y el rim complementan el ritmo y añaden textura.

Los hi-hats (hh) y toms (lt) aportan detalles rítmicos.

En los toms se utiliza un filtro controlado por un slider, lo que permite modificar el sonido en tiempo real.

Algunos sonidos solo se activan en ciertos momentos gracias a las máscaras rítmicas.

#### Máscaras y visualización
Para controlar cuándo suenan ciertos patrones, utilicé máscaras:

```js
.mask("<[1 1] 1 0 1>/16".early(.5))
```
Esto evita que el ritmo sea repetitivo y hace que algunos sonidos aparezcan solo en momentos específicos.

También incluí una visualización del patrón:

```js
._pianoroll({vertical:0, flipTime:0, fill:0, labels:1})
```
Esta herramienta ayuda a entender visualmente cómo se distribuyen los eventos musicales mientras la pieza se ejecuta.

#### Capas de bajo
El bajo se genera a partir de la progresión de acordes:

```js
chords.offset(-1).voicing().s("gm_acoustic_bass:1")
.phaser(4).room(.5)
```
Esta capa proporciona profundidad y estabilidad.

También añadí un segundo bajo con un carácter más electrónico:

```js
chords.offset(-2).voicing().s("gm_synth_bass_2:1")
.phaser(4).room(.5).lpf(slider(452, 0, 1000))
```
En este caso, el filtro pasa-bajos está controlado por un slider, lo que permite modificar la intensidad del sonido durante la ejecución.

#### Elementos melódicos
Para reforzar la armonía, añadí una capa de piano eléctrico:

```js
n("<0!3 1*2>").set(chords).mode("root:g2")
.voicing().s("gm_epiano2:1")
```
Este elemento sigue los acordes y funciona como acompañamiento melódico sin sobresalir demasiado.

#### Textura sonora y variación
Finalmente, incorporé una capa más experimental basada en los acordes:

```js
chords.n("[1 <4 3 <2 5>>*2](<3 5>,8)")
  .anchor("g4").voicing()
  .segment(4).clip(rand.range(.4,.8))
  .room(.75).shape(.3).delay(.25)
  .fm(sine.range(3,8).slow(8))
  .lpf(sine.range(500,1000).slow(8)).lpq(5)
  .rarely(ply("2")).chunk(4, fast(2))
  .gain(perlin.range(.6, .9))
  .mask("<0 1 1 0>/16")
```
Esta capa introduce variaciones constantes mediante efectos, cambios de volumen y activaciones poco frecuentes, lo que hace que la pieza no suene igual en cada repetición.

#### Ajustes finales
Para finalizar, apliqué pequeños retardos y ajustes globales:

```js
.late("[0 .01]*4").late("[0 .01]*2").size(4)
```
Estos ajustes ayudan a que el sonido no sea completamente rígido y aportan una sensación más orgánica al conjunto.

## Bitácora de reflexión


### Diagrama Funcionamiento de caso de Estudio

<img width="924" height="678" alt="image" src="https://github.com/user-attachments/assets/e019b719-2bfe-4591-9551-59e3ed4036dd" />




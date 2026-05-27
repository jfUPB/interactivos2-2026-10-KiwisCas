# Unidad 4

## Bitácora de proceso de aprendizaje


## Bitácora de aplicación 

### Documentación de como hice para que todo funciomnara para que en el futuro no se me estalle la cabeza intemtando acordarme de esto otra vez

Inicialmente hay que tener en cuenta el hecho de que todo el sistema va a funcionar a modo de hub para alojar las entradas de datos, si bien se nos explicó que es bueno para comenzar el hecho de que es bueno tener bridges por separado, opté por la idea de utilizar todo a modo de hub para que el sistema reciba grandes partes de información en un solo formato por temas de practicidad ya que es inclusive más facil en este tipo de casos, alojar infoemación en un solo tipo de datos para que el sistema se vea afectado que tener que usarlo por partes separadas, el lado no tan bueno de todo el procedimiento es que, de la forma que hice, los sistemas pueden llegar a ser un poco más dependientes del hub para poder funcionar por lo que, si realizo una conexción antes de abrir el hub principal, me voy a embalar ya que no hice la conexión como es debida en primera instancia ya que puedo salir con errores como el siguiente:

<img width="605" height="157" alt="image" src="https://github.com/user-attachments/assets/6285bff3-260b-4fd9-a4af-14e4d985d4a9" />

Lo cual ya me jodería en medio de una experiencia por lo que en pasos más sencillos, SOY UN CHISTOSO porque no había caído en cuenta de que necesito que inicialmente abrir el servidor de OpenStageControl para que funcione, así que ahora si voy a poner el orden como es para no complicarme la vida y que luego mi yo que esté leyendo esto en el futuro no le de un lapsus mientras intenta que esto funcione así que vamos a hacerlo paso a paso.

#### Pasos para sacar adelante este proyecto otra vez y acordarme sin tener que quemarme el coco en un futuro porque yo me conozco perfectamente.

1. Tener instalado OpenStageControl
2. Abrir el OpenStageControlUITest y ejectuarlo con el comando `node bridgeUI.js` el cual en teoría va a hacer que se vea lo siguiente:

   <img width="483" height="77" alt="image" src="https://github.com/user-attachments/assets/f287781f-ab38-40df-a566-07dcfe83bb8d" />

   Si ves esto significa que vas por buen camino 😁, en caso de que no, pailas manin.
4. Ahora sigue ejecutar el hub, verdaderamente si vas a hacer implementación deberías mirar el error que tienes en este momento ya que, en caso de que por alguna razón no hagas los pasos que te dí arriba te va a pasar esto:

    <img width="685" height="443" alt="image" src="https://github.com/user-attachments/assets/db5d5b53-9988-4109-ab6f-dbc44c68ab18" />

    (Algo que me parece muy chistoso es que necesitas con más importancia del OpenStage que del propio strudel para que puedas correr el sistema sin que se te de un babeado, así que es tu deber en el furturo investigar para que este proceso no te embale y luego te enredes.

Ten en cuenta que para poder ejecutar el hub principal vas a usar el comando `node bridge.js` y si hiciste los pasos anteriores de buena forma, te debería aparecer lo siguiente:

<img width="685" height="123" alt="image" src="https://github.com/user-attachments/assets/98805ec9-61af-4eb4-9c19-71effca76a3e" />

Lo que significa que vamos por buen camino y ahora solo te quedan dos consolas más de gitbash por abrir jeje

5. Ahora toca la música y la página de las visuales, es decir el strudel, vas a ir a la carpeta de strudel y vas a ejecutar con el comando `pnpm dev` recuerda instalar las dependencias si es primera vez que lo haces con el comando `pnpm i`, tal vez te vaya a tomar un poco de tiempo pero tenle paciencia, cuando todo termine de ejecutar debería aparecerte algo de este estilo










## Bitácora de reflexión

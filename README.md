# CBDBot

En este README se explicarán los pasos a seguir para poder realizar una instalación y configuración de Rasa y de las herramientas para que el bot creado pueda ser usado junto a Telegram. Para ello, se usará la documentación de Rasa disponible en https://rasa.com/docs/ y, sobre todo, el vídeo oficial de instalación en Windows10, https://www.youtube.com/watch?v=GlR60CvTh8A. 

Para comenzar, se requiere la instalación de Visual Studio Code C++ (https://support.microsoft.com/en-us/topic/the-latest-supported-visual-c-downloads-2647da03-1eea-4433-9aff-95f26a218cc0) y de Anaconda (https://www.anaconda.com/products/individual#windows). En ambos casos, simplemente se ejecutarán los instaladores sin ninguna configuración especial, salvo en el caso de Anaconda, dónde se marcará la opción de añadir Anaconda al PATH de variables de entorno, ignorando las advertencias del instalador.

A continuación, se abrirá el terminal de Anaconda y se creará un entorno de Python en la carpeta en la que se haya descargado el código de este repositorio con el comando:

conda create --name NOMBRE python==3.8

Donde NOMBRE es el nombre que se quiere dar al entorno. En este caso utilizaremos la versión 3.8 puesto que es la usada durante el video y por tanto la utilizada durante esta implementación.

Luego activaremos el entorno creado:

conda activate NOMBRE

Tras ello instalaremos Ujson y TensorFlow:

conda install ujson
conda install tensorflow

Finalmente instalaremos Rasa:

pip install rasa

A partir de este punto el bot es plenamente funcional y puede entrenarse para aumentar su complejidad y utilidad. Existen una amplia variedad de comandos que permiten realizar multitud de acciones sobre el bot. Todas estas pueden verse usando el comando:

rasa -h

Sin embargo, nos centraremos en las más útiles:

-	“train”: Permite entrenar el bot con los datos existentes y generar un nuevo modelo en la carpeta /models.

-	“visualize”: Permite obtener un fichero graph.html que muestra gráficamente las historias existentes. En este gráfico se puede ver desde el comienzo al final de la conversación, que respuestas del bot suceden a cada intención del usuario y cuales de estás pueden darse después de una respuesta del bot. Es importante destacar que, en el momento actual, existe un bug que provoca que las intenciones aparezcan como “None” en lugar de su nombre dado en las historias. A pesar de que el bug aún este pendiente de resolución (puede verse la issue oficial del bug en https://github.com/RasaHQ/rasa/issues/7600), su correcto funcionamiento resultaría de gran utilidad.

-	“shell”: Permite comunicarse con el bot mediante la consola de comando y probar su funcionamiento. Para terminar la conversación, se pasará el mensaje “/stop”. 

-	“interactive”: Permite comunicarse con el bot mediante consola, a la vez que se puede ver las decisiones tomadas por este. Se requerirá confirmar el correcto comportamiento del bot, y corregirlo si este llegase a equivocarse. En estos casos, es posible seleccionar las decisiones que debería de tomar realmente el bot, he incluso crear nuevas si fuese necesario. Una vez que se quiera finalizar la conversación, mediante el uso de Crtl.+C en lugar de poner un nuevo mensaje, se puede acceder al menú de opciones y seleccionar “Export & Quit”. Esto aplicará todos los cambios necesarios al bot para tomar en cuenta lo aprendido durante la conversación.

-	“run”: Permite lanzar en servidor al bot para su uso. Por si solo está acción no resulta muy útil, pero permitirá usar el bot en Telegram.

El primer paso para poder habilitar el bot en Telegram es necesario desplegarlo en servidor y que sea accesible por cualquier equipo. Con la configuración actual, el bot se puede lanzar en servidor (“run”) pero solo será accesible por el equipo que realiza el despliegue. Esto resulta evidente puesto a que la URL en la que es desplegada será https://localhost:5005 (el puerto usado puede variar).

Por tanto, hará falta el uso de Ngrok, cuyo ejecutable puede descargarse en https://ngrok.com/download. Este puede situarse en la misma carpeta en la que se ha situado el bot para su fácil acceso.

Para poder llevar a cabo las siguientes acciones, se recomienda usar el terminal disponible en Visual Studio, puesto que permite gestionar fácilmente varios terminales simultáneamente. Para poder trabajar con el bot de rasa, deberá usarse un terminal de tipo Command Prompt. Y para utilizar Ngrok, se usará otro con la configuración PowerShell. Para más información sobre el uso de los terminales de Visual Studio, utilice la documentación oficial: https://code.visualstudio.com/docs/editor/integrated-terminal.

En la terminal de tipo PoweShell, se ejecutará el siguiente comando para lanzar el ejecutable de Ngrok:
./ngrok.exe http 5005

En la respuesta del comando se puede apreciar que la URL http://1332dfafaad3.ngrok.io apunta a http://localhost:5005. Es decir, desde el exterior, cualquier persona podrá acceder a lo desplegado en local en el puerto 5005 gracias a la url generada por Ngrok. Es importante denotar que esta url es generada aleatoriamente cada vez que se ejecute Ngrok, por tanto, deberemos comenzar siempre por esta instrucción para obtener la nueva url, que necesitaremos a continuación.

El segundo paso es acceder a Telegram para poder obtener las credenciales para el bot. Accediendo al siguiente enlace, https://web.telegram.org/#/im?p=@BotFather, y escribiendo “/newbot” en el chat, se comenzará el proceso de configuración del bot. Siguiendo el proceso, finalmente se obtendrá 3 informaciones importantes: el nombre del bot, el token de acceso a la API HTTP, y el enlace al bot. Con ello, se modificará el archivo credentials.yml:

telegram:
  access_token: "1811883959:AAG5j3ym-q5yFzOlLRgMEwdhRc48MB_D178"
  verify: "RasaCBDBot"
  webhook_url: "https://68ecfc03176d.ngrok.io/webhooks/telegram/webhook"

Dónde cada bot tendrá sus propios valores en “access_token” y en “verify”. Para “webhook_url” siempre se tendrá la misma estructura de URL, modificando la parte de “68ecfc03176d.ngrok.io” con la URL generada aleatoriamente siempre que se ejecute ngrok.

Una vez todas estas configuraciones realizadas, bastará con ejecutar el comando “run” de rasa para lanzar el bot en el servidor, y que sea accesible con el código de Telegram obtenido, parecido a: t.me/RasaCBDBot.

Con todo esto, el bot será plenamente funcional y utilizable en Telegram. Para detener su ejecución, bastará con usar el comando “Ctrl.+C” en los dos terminales usados, para detener Ngrok y Rasa.

 

# CBDBot

En este apartado se explicarán los pasos a seguir para poder realizar una instalación y configuración de Rasa y de las herramientas para que el bot creado pueda ser usado junto a Telegram. Para ello, se usará la documentación de Rasa disponible en https://rasa.com/docs/ y, sobre todo, el vídeo oficial de instalación en Windows10, https://www.youtube.com/watch?v=GlR60CvTh8A. 
Para comenzar, se requiere la instalación de Visual Studio Code C++ (https://support.microsoft.com/en-us/topic/the-latest-supported-visual-c-downloads-2647da03-1eea-4433-9aff-95f26a218cc0) y de Anaconda (https://www.anaconda.com/products/individual#windows). En ambos casos, simplemente se ejecutarán los instaladores sin ninguna configuración especial, salvo en el caso de Anaconda, dónde se marcará la opción de añadir Anaconda al PATH de variables de entorno, ignorando las advertencias del instalador.
A continuación, se abrirá el terminal de Anaconda y se creará un entorno de Python en la carpeta deseada con el comando:
conda create --name NOMBRE python==3.8

Donde NOMBRE es el nombre que se quiere dar al entorno. En este caso utilizaremos la versión 3.8 puesto que es la usada durante el video y por tanto la utilizada durante esta implementación.
Luego activaremos el entorno creado:
conda activate NOMBRE

Tras ello instalaremos Ujson y TensorFlow:
conda install ujson
conda install tensorflow

Finalmente instalaremos Rasa:
pip install rasa

E inicializaremos un bot básico:
rasa init

Con esto rasa ya se encuentra instalado y poseemos un bot básico entrenado para poder reconocer estados de ánimo y enviar una imagen en caso de que el usuario se encuentre triste. Es importante denotar que el bot se encuentra entrenado en inglés, y no en español. Para poder utilizarlo en nuestro idioma, es necesario cambiar la configuración existente en el archivo config.yml a la siguiente (usando por ejemplo el Visual Studio Code que se ha instalado anteriormente):
language: es

pipeline:
  - name: SpacyNLP
    model: "es_core_news_md"
  - name: SpacyTokenizer
  - name: SpacyFeaturizer
  - name: RegexFeaturizer
  - name: LexicalSyntacticFeaturizer
  - name: CountVectorsFeaturizer
  - name: CountVectorsFeaturizer
    analyzer: "char_wb"
    min_ngram: 1
    max_ngram: 4
  - name: DIETClassifier
    epochs: 100
  - name: EntitySynonymMapper
  - name: ResponseSelector
    epochs: 100

Esta configuración nos facilita utilizar el bot en español y nos permite utilizar un sistema que reconoce sinónimos: SpaCy. Para ello será necesario instalar las dependencias necesarias:
pip3 install rasa[spacy]
python3 -m spacy download es_core_news_md

Una vez estas dependencias instaladas se deberá cambiar los valores de entrenamiento utilizados en inglés al español. Los cambios estrictamente necesarios son requeridos en nlu.yml en los mensajes puestos en “examples”, en domain.yml en los mensajes puestos en “text” y en test_stories.yml en los mensajes puestos en “user”. Los otros elementos en inglés que se pueden encontrar en estos archivos y en rules.yml y stories.yml son nombres de variables y por tanto su traducción no es requerida, aunque recomendable.
Además de cambiar el idioma añadiremos la configuración de las políticas del bot, lo que aumentará la complejidad del bot a la hora de elegir las acciones a realizar, y por tanto este será más efectivo. Los cambios para añadir se encuentran una vez más en config.yml:
policies:
  - name: MemoizationPolicy
  - name: TEDPolicy
    max_history: 5
    epochs: 100
    constrain_similarities: true
  - name: RulePolicy

A partir de este punto el bot es plenamente funcional y puede entrenarse para aumentar su complejidad y utilidad. Existen una amplia variedad de comandos que permiten realizar multitud de acciones sobre el bot. Todas estas pueden verse usando el comando:
rasa -h

Sin embargo, nos centraremos en las más útiles:
-	“train”: Permite entrenar el bot con los datos existentes y generar un nuevo modelo en la carpeta /models.
-	“visualize”: Permite obtener un fichero graph.html que muestra gráficamente las historias existentes. En este gráfico se puede ver desde el comienzo al final de la conversación, que respuestas del bot suceden a cada intención del usuario y cuales de estás pueden darse después de una respuesta del bot. Es importante destacar que, en el momento actual, existe un bug que provoca que las intenciones aparezcan como “None” en lugar de su nombre dado en las historias. A pesar de que el bug aún este pendiente de resolución (puede verse la issue oficial del bug en https://github.com/RasaHQ/rasa/issues/7600), su correcto funcionamiento resultaría de gran utilidad.
-	“shell”: Permite comunicarse con el bot mediante la consola de comando y probar su funcionamiento. Para terminar la conversación, se pasará el mensaje “/stop”. 
-	“interactive”: Permite comunicarse con el bot mediante consola, a la vez que se puede ver las decisiones tomadas por este. Se requerirá confirmar el correcto comportamiento del bot, y corregirlo si este llegase a equivocarse. En estos casos, es posible seleccionar las decisiones que debería de tomar realmente el bot, he incluso crear nuevas si fuese necesario. Una vez que se quiera finalizar la conversación, mediante el uso de Crtl.+C en lugar de poner un nuevo mensaje, se puede acceder al menú de opciones y seleccionar “Export & Quit”. Esto aplicará todos los cambios necesarios al bot para tomar en cuenta lo aprendido durante la conversación.
-	“run”: Permite lanzar en servidor al bot para su uso. Por si solo está acción no resulta muy útil, pero permitirá usar el bot en Telegram.
Usando todas las funciones mencionadas, se habrá entrenado el bot hasta el nivel de complejidad deseado añadiendo datos nuevos a los ficheros y teniendo conversaciones con él para verificar su buen comportamiento. Una vez que el nivel del bot se juzgue satisfactorio, se podrá permitir su uso en Telegram. Es importante denotar que este último punto de la implementación no imposibilita de ninguna forma seguir desarrollando y entrenando el bot siempre que se desee.
El primer paso para poder inhabilitar el bot en Telegram es poder desplegarlo en servidor y que sea accesible por cualquier equipo. Con la configuración actual, el bot se puede lanzar en servidor (“run”) pero solo será accesible por el equipo que realiza el despliegue. Esto resulta evidente puesto a que la URL en la que es desplegada será https://localhost:5005 (el puerto usado puede variar).
Por tanto, hará falta el uso de Ngrok, cuyo ejecutable puede descargarse en https://ngrok.com/download. Este puede situarse en la misma carpeta en la que se ha situado el bot para su fácil acceso.
Para poder llevar a cabo las siguientes acciones, se recomienda usar el terminal disponible en Visual Studio, puesto que permite gestionar fácilmente varios terminales simultáneamente. Para poder trabajar con el bot de rasa, deberá usarse un terminal de tipo Command Prompt. Y para utilizar Ngrok, se usará otro con la configuración PowerShell. En la siguiente imagen pueden verlos los elementos de Visual Studio que permiten el manejo de los terminales:
Para más información sobre el uso de los terminales de Visual Studio, utilice la documentación oficial: https://code.visualstudio.com/docs/editor/integrated-terminal.
En la terminal de tipo PoweShell, se ejecutará el siguiente comando para lanzar el ejecutable de Ngrok:
./ngrok.exe http 5005

Esto devolverá lo siguiente:
Se puede apreciar que la URL http://1332dfafaad3.ngrok.io apunta a http://localhost:5005. Es decir, desde el exterior, cualquier persona podrá acceder a lo desplegado en local en el puerto 5005 gracias a la url generada por Ngrok. Es importante denotar que esta url es generada aleatoriamente cada vez que se ejecute Ngrok, por tanto, deberemos comenzar siempre por esta instrucción para obtener la nueva url, que necesitaremos a continuación.
El segundo paso es acceder a Telegram para poder obtener las credenciales para el bot. Accediendo al siguiente enlace, https://web.telegram.org/#/im?p=@BotFather, y escribiendo “/newbot” en el chat, se comenzará el proceso de configuración del bot.
Siguiendo el proceso, finalmente se obtendrá 3 informaciones importantes: el nombre del bot, el token de acceso a la API HTTP, y el enlace al bot. Con ello, se modificará el archivo credentials.yml:
telegram:
  access_token: "1811883959:AAG5j3ym-q5yFzOlLRgMEwdhRc48MB_D178"
  verify: "RasaCBDBot"
  webhook_url: "https://68ecfc03176d.ngrok.io/webhooks/telegram/webhook"

Dónde cada bot tendrá sus propios valores en “access_token” y en “verify”. Para “webhook_url” siempre se tendrá la misma estructura de URL, modificando la parte de “68ecfc03176d.ngrok.io” con la URL generada aleatoriamente siempre que se ejecute ngrok.
Una vez todas estas configuraciones realizadas, bastará con ejecutar el comando “run” de rasa para lanzar el bot en el servidor, y que sea accesible con el código de Telegram obtenido, parecido a: t.me/RasaCBDBot.
Con todo esto, el bot será plenamente funcional y utilizable en Telegram. Para detener su ejecución, bastará con usar el comando “Ctrl.+C” en los dos terminales usados, para detener Ngrok y Rasa.

 

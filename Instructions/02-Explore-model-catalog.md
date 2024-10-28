---
lab:
  title: 'Exploración, implementación y chat con modelos de lenguaje en Inteligencia artificial de Azure Studio'
---

# Exploración, implementación y chat con modelos de lenguaje en Inteligencia artificial de Azure Studio

El catálogo de modelo de Inteligencia artificial de Azure Studio se usa como repositorio central donde puede explorar y usar diversos modelos, lo que facilita la creación de su escenario de IA generativa.

En este ejercicio, explorará el catálogo de modelos de Inteligencia artificial de Azure Studio.

Este ejercicio dura aproximadamente **25** minutos.

## Creación de un centro de Azure AI

Necesitas un centro de Azure AI en tu suscripción de Azure para hospedar proyectos. Puedes crear este recurso al crear un proyecto o aprovisionarlo con antelación (que es lo que haremos en este ejercicio).

1. En la sección **Administración**, selecciona **Todos los recursos** y, después, selecciona **+ Nuevo centro**. Crea un nuevo centro con la siguiente configuración:
    - **Nombre del centro**: *un nombre único*
    - **Suscripción**: *suscripción de Azure*
    - **Grupo de recursos**: *un nuevo grupo de recursos*
    - **Ubicación**: selecciona **Ayúdeme a elegir** y, a continuación, selecciona **gpt-35-turbo** en la ventana Asistente de ubicación y usa la región recomendada\*
    - **Conectar Servicios de Azure AI o Azure OpenAI**: *crear una conexión*
    - **Conectar Búsqueda de Azure AI**: omitir la conexión

    > \* Los recursos de Azure OpenAI están restringidos en el nivel de inquilino por cuotas regionales. Las regiones enumeradas en el asistente de ubicación incluyen la cuota predeterminada para los tipos de modelo usados en este ejercicio. Elegir aleatoriamente una región reduce el riesgo de que una sola región alcance su límite de cuota. En caso de que se alcance un límite de cuota más adelante en el ejercicio, es posible que tengas que crear otro recurso en otra región. Más información sobre la [disponibilidad del modelo por región](https://learn.microsoft.com/azure/ai-services/openai/concepts/models#gpt-35-turbo-model-availability)

    La siguiente imagen ejemplifica lo que deberías ver tras crear el centro de Azure AI:

    ![Captura de pantalla de los detalles de un centro de Azure AI en Inteligencia artificial de Azure Studio.](./media/azure-ai-resource.png)

1. Abre una nueva pestaña del explorador (dejando abierta la pestaña Inteligencia artificial de Azure Studio) y ve a Azure Portal en [https://portal.azure.com](https://portal.azure.com?azure-portal=true), inicia sesión con tus credenciales de Azure si se te solicita.
1. Ve al grupo de recursos en el que creaste el centro de Azure AI y mira los recursos de Azure que se han creado.

    ![Captura de pantalla de un centro de Azure AI y recursos relacionados en Azure Portal.](./media/azure-portal.png)

1. Vuelve a la pestaña del explorador de Inteligencia artificial de Azure Studio.
1. Mira cada una de las páginas del panel en el lado izquierdo de la página del centro de Azure AI y anota los artefactos que puedes crear y administrar. En la página **Conexiones**, observa que ya se han creado las conexiones a Azure OpenAI y los servicios de IA.

## Creación de un proyecto

Un centro de Azure AI proporciona un área de trabajo de colaboración en la que puedes definir uno o varios *proyectos*. Vamos a crear un proyecto en el centro de Azure AI.

1. En Inteligencia artificial de Azure Studio, asegúrate de que está en el centro que acabas de crear (puedes comprobar la ubicación al comprobar la ruta de acceso en la parte superior de la pantalla).
1. Ve a **Todos los proyectos** con el menú de la izquierda.
1. Selecciona **+ Nuevo proyecto**.
1. En el asistente para **Crear un nuevo proyecto**, crea un proyecto con la siguiente configuración:
    - **Centro actual**: *Su centro de Azure AI*
    - **Nombre del proyecto**: *Un nombre exclusivo para el proyecto*
1. Espere a que se cree el proyecto. El resultado debe tener un aspecto similar a la imagen siguiente:

    ![Captura de pantalla de una página de detalles del proyecto en Inteligencia artificial de Azure Studio.](./media/azure-ai-project.png)

1. Vea las páginas del panel de la izquierda, expanda cada sección y anote las tareas que puede realizar y los recursos que puede administrar en un proyecto.

## Elige un modelo mediante pruebas comparativas de modelos

Antes de implementar un modelo, puedes explorar pruebas comparativas de modelos para decidir cuál se adapta mejor a tus necesidades.

Imagina que quieres crear un copiloto personalizado que actúe como asistente para viajes. En concreto, deseas que el copiloto ayude en consultas relacionadas con viajes, como requisitos de visas, previsiones meteorológicas, atracciones locales y normas culturales.

El copiloto tendrá que proporcionar información precisa y correcta, por lo que la base es importante. Además, quieres que las respuestas del copiloto sean fáciles de leer y entender. Por lo tanto, también quieres elegir un modelo que tenga tasas altas de fluidez y coherencia.

1. En Inteligencia artificial de Azure Studio, ve a **Pruebas comparativas de modelos** en la sección **Comenzar** mediante el menú de la izquierda.
    En la pestaña **Pruebas comparativas de calidad**, puedes encontrar algunos gráficos ya disponibles para ti y comprar los diferentes modelos.
1. Filtra los modelos mostrados:
    - **Tareas**: respuesta a preguntas
    - **Colecciones**: Azure OpenAI
    - **Métricas**: coherencia, fluidez, base
1. Explora los gráficos resultantes y la tabla de comparación. Durante la exploración, puedes intentar responder a las siguientes preguntas:
    - ¿Observas diferencias de rendimiento entre los modelos GPT-3.5 y GPT-4?
    - ¿Hay alguna diferencia entre las versiones del mismo modelo?
    - ¿Cómo difieren las variantes de 32k de los modelos base?

En la colección de Azure OpenAI, puedes elegir entre los modelos GPT-3.5 y GPT-4. Implementemos estos dos modelos y exploremos en qué se diferencian en tu caso de uso.

## Implementación de modelos de Azure OpenAI

Ahora que has explorado las opciones a través de pruebas comparativas de modelos, ya tienes todo listo para implementar modelos de lenguaje. Puedes examinar el catálogo de modelos e implementar desde allí, o bien puedes implementar un modelo a través de la página **Implementaciones**. Exploremos ambas opciones.

### Implementación de un modelo del Catálogo de modelos

Comencemos implementando un modelo del Catálogo de modelos. Es posible que prefieras esta opción cuando desees filtrar todos los modelos disponibles.

1. Ve a la página **Catálogo de modelos** en la sección **Comenzar**, mediante el menú de la izquierda.
1. Busca e implementa el modelo `gpt-35-turbo`, preparado por Azure AI, con la siguiente configuración:
    - **Nombre de implementación**: *Un nombre único para la implementación de modelo*
    - **Tipo de implementación**: estándar
    - **Versión del modelo**: *Selecciona la versión predeterminada*
    - **Recurso de IA**: *selecciona el recurso creado anteriormente*
    - **Límite de frecuencia de tokens por minuto (miles)**: 5000
    - **Filtro de contenido**: DefaultV2
    - **Habilitación de la cuota dinámica**: deshabilitada

### Implementación de un modelo mediante Implementaciones

Si ya sabes exactamente qué modelo quieres implementar, es posible que prefieras hacerlo a través de Implementaciones.

1. Ve a la página **Implementaciones** en la sección **Componentes**, mediante el menú de la izquierda.
1. En la pestaña **Implementaciones de modelos**, crea una implementación con la siguiente configuración:
    - **Modelo**: GPT-4
    - **Nombre de implementación**: *Un nombre único para la implementación de modelo*
    - **Tipo de implementación**: estándar
    - **Versión del modelo**: *Selecciona la versión predeterminada*
    - **Recurso de IA**: *selecciona el recurso creado anteriormente*
    - **Límite de frecuencia de tokens por minuto (miles)**: 5000
    - **Filtro de contenido**: DefaultV2
    - **Habilitación de la cuota dinámica**: deshabilitada

    > **Nota**: Es posible que hayas observado algunos modelos que muestran las pruebas comparativas de modelos, pero no figuran como opción en el catálogo de modelos. La disponibilidad de los modelos difiere según la ubicación. Tu ubicación se especifica en el nivel de centro de IA, donde puedes usar el **Asistente de ubicación** para especificar el modelo que deseas implementar a fin de obtener una lista de las ubicaciones en las que puedes implementarlo.

## Prueba de modelos en el área de juegos de chat

Ahora que tenemos dos modelos que comparar, veamos cómo se comportan en una interacción conversacional.

1. Ve a la página **Chat** en la sección **Área de juegos del proyecto** con el menú de la izquierda.
1. En el **Área de juegos de chat**, selecciona la implementación de GPT-3.5.
1. En la ventana de chat, escribe una consulta como `What can you do?` y analiza la respuesta:
    Las respuestas son muy genéricas. Recuerda que queremos crear un copiloto personalizado que actúe como asistente para viajes. Puedes especificar qué tipo de ayuda deseas en la pregunta que hagas.
1. En la ventana de chat, escribe la consulta `Imagine you're a travel assistant, what can you help me with?`. Las respuestas ya son más específicas. Es posible que no quieras que los usuarios finales tengan que proporcionar el contexto necesario cada vez que interactúen con el copiloto. Para agregar instrucciones globales, puedes editar el mensaje del sistema.
1. Actualiza el mensaje del sistema con la siguiente solicitud:

   ```
   You are an AI travel assistant that helps people plan their trips. Your objective is to offer support for travel-related inquiries, such as visa requirements, weather forecasts, local attractions, and cultural norms.
   ```

1. Selecciona **Guardar** y **Borrar chat**.
1. En la ventana de chat, escribe la consulta `What can you do?` y analiza la nueva respuesta. Observa en qué se diferencia de la respuesta que recibiste antes. Ahora, la respuesta es específica sobre viajes.
1. Continúa la conversación preguntando: `I'm planning a trip to London, what can I do there?`. El copiloto ofrece una gran cantidad de información relacionada con los viajes. Es posible que quieras mejorar más la salida. Por ejemplo, puedes querer que la respuesta sea más concisa.
1. Actualiza el mensaje del sistema agregando `Answer with a maximum of two sentences.` al final del mensaje. Aplica el cambio, borra el chat y vuelve a probarlo preguntando: `I'm planning a trip to London, what can I do there?`. También puede que quieras que el copiloto continúe la conversación en lugar de simplemente responder a la pregunta.
1. Actualiza el mensaje del sistema agregando `End your answer with a follow-up question.` al final del mensaje. Guarda el cambio, borra el chat y vuelve a probarlo preguntando: `I'm planning a trip to London, what can I do there?`
1. Cambia la **Implementación** al modelo GPT-4 y repite todos los pasos de esta sección. Observa cómo los modelos pueden ofrecer salidas diferentes.
1. Por último, prueba ambos modelos con la consulta `Who is the prime minister of the UK?`. El rendimiento de esta pregunta está relacionado con la base (si la respuesta es precisa y real) de los modelos. ¿El rendimiento se pone en correlación con tus conclusiones de las pruebas comparativas de los modelos?

Ahora que has explorado ambos modelos, reflexiona sobre qué modelo elegirías para tu caso de uso. Al principio, las salidas de los modelos pueden diferir y es posible que prefieras uno al otro. Sin embargo, después de actualizar el mensaje del sistema, puedes observar que la diferencia es mínima. Desde una perspectiva de optimización de costes, puedes optar por el modelo GPT-3.5 sobre el GPT-4, ya que el rendimiento es muy similar.

## Limpiar

Si ha terminado de explorar Inteligencia artificial de Azure Studio, debe eliminar los recursos que ha creado en este ejercicio para evitar incurrir en costos innecesarios de Azure.

1. Vuelva a la pestaña del explorador que contiene Azure Portal (o vuelva a abrir [Azure Portal](https://portal.azure.com?azure-portal=true) en una nueva pestaña del explorador) y vea el contenido del grupo de recursos donde implementó los recursos usados en este ejercicio.
1. Seleccione **Eliminar grupo de recursos** en la barra de herramientas.
1. Escriba el nombre del grupo de recursos y confirme que desea eliminarlo.

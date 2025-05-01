---
lab:
  title: Ajuste de un modelo de lenguaje
  description: Obtén información sobre cómo usar tus propios datos de entrenamiento para ajustar un modelo y personalizar su comportamiento.
---

# Ajuste de un modelo de lenguaje

Cuando desees que un modelo de lenguaje se comporte de una manera determinada, puedes usar la ingeniería de avisos para definir el comportamiento deseado. Cuando quieras mejorar la coherencia del comportamiento deseado, puedes optar por ajustar un modelo, comparándolo con el enfoque de ingeniería de petición para evaluar qué método se adapta mejor a tus necesidades.

En este ejercicio, ajustarás un modelo de lenguaje con Azure AI Foundry que quieres usar para un escenario de aplicación de chat personalizado. Compararás el modelo ajustado con un modelo base para evaluar si el modelo ajustado se adapta mejor a tus necesidades.

Imagina que trabajas para una agencia de viajes y estás desarrollando una aplicación de chat para ayudar a las personas a planificar sus vacaciones. El objetivo es crear un chat sencillo e inspirador que sugiera destinos y actividades en un tono conversacional amistoso y coherente.

Este ejercicio dura aproximadamente **60** minutos\*.

> \***Nota**: este tiempo es una estimación basada en la experiencia media. El ajuste preciso depende de los recursos de infraestructura en la nube, lo que puede tardar una cantidad variable de tiempo en aprovisionar en función de la capacidad del centro de datos y la demanda simultánea. Algunas actividades de este ejercicio pueden tardar <u>mucho</u> tiempo en completarse y requerir paciencia. Si las cosas tardan un rato, considera la posibilidad de revisar la [documentación de ajuste preciso de Fundición de IA de Azure](https://learn.microsoft.com/azure/ai-studio/concepts/fine-tuning-overview) o tomar un descanso. Algunas de las tecnologías que se usan en este ejercicio están en versión preliminar o en desarrollo activo. Puede que se produzcan algunos errores, comportamientos o advertencias inesperados.

## Creación de un centro de IA en el Portal de la Fundición de IA de Azure

Vamos a empezar creando un proyecto de Fundición de IA de Azure en un centro de Azure AI:

1. En un explorador web, abre el [Portal de la Fundición de IA de Azure](https://ai.azure.com) en `https://ai.azure.com` e inicia sesión con tus credenciales de Azure. Cierra las sugerencias o paneles de inicio rápido que se abran la primera vez que inicias sesión y, si es necesario, usa el logotipo de **Fundición de IA de Azure** en la parte superior izquierda para navegar a la página principal, que es similar a la siguiente imagen:

    ![Captura de pantalla del Portal de la Fundición de IA de Azure.](./media/ai-foundry-home.png)

1. En la página principal, selecciona **+Crear proyecto**.
1. En el asistente para **crear un proyecto**, escribe un nombre válido y si se te sugiere un centro existente, elige la opción para crear uno nuevo. A continuación, revisa los recursos de Azure que se crearán automáticamente para admitir el centro y el proyecto.
1. Selecciona **Personalizar** y especifica la siguiente configuración para el centro:
    - **Nombre del centro**: *proporciona un nombre para el centro*.
    - **Suscripción**: *suscripción a Azure*
    - **Grupo de recursos**: *crea o selecciona un grupo de recursos*.
    - **Ubicación**: selecciona **Ayúdame a elegir** y, a continuación, selecciona **gpt-4o-finetune** en la ventana Asistente de ubicación y usa la región recomendada\*
    - **Conectar Servicios de Azure AI o Azure OpenAI**: *Crear un nuevo servicio de IA*
    - **Conexión de Búsqueda de Azure AI**: *crea de un nuevo recurso de Búsqueda de Azure AI con un nombre único*

    > \* Los recursos de Azure OpenAI están restringidos por cuotas de modelo regionales. En caso de que se alcance un límite de cuota más adelante en el ejercicio, es posible que tengas que crear otro recurso en otra región. 

1. Selecciona **Siguiente** y revisa tu configuración. Luego, selecciona **Crear** y espera a que se complete el proceso.
1. Cuando se cree el proyecto, cierra las sugerencias que se muestran y revisa la página del proyecto en el Portal de la Fundición de IA de Azure, que debe tener un aspecto similar a la siguiente imagen:

    ![Captura de pantalla de los detalles de un proyecto de Azure AI en el Portal de la Fundición de IA de Azure.](./media/ai-foundry-project.png)

## Ajuste con precisión de un modelo

Dado que el ajuste de un modelo tarda algún tiempo en completarse, iniciarás el trabajo de ajuste ahora y volverás a él después de explorar un modelo base que no se haya ajustado con fines de comparación.

1. Descarga el [conjunto de datos de entrenamiento](https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/refs/heads/main/data/travel-finetune-hotel.jsonl) en `https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/refs/heads/main/data/travel-finetune-hotel.jsonl`y guárdalo como un archivo JSONL localmente.

    > **Nota**: es posible que el dispositivo guarde el archivo de forma predeterminada como un archivo .txt. Selecciona todos los archivos y quita el sufijo .txt para asegurarte de que vas a guardar el archivo como JSONL.

1. Ve a la página **Ajuste** de la sección **Crear y personalizar**, con el menú de la izquierda.
1. Selecciona el botón para agregar un nuevo modelo de ajuste, selecciona el modelo **gpt-4o** y, a continuación, selecciona **Siguiente**.
1. **Ajusta** el modelo mediante la siguiente configuración:
    - **Versión del modelo**: *Selecciona la versión predeterminada*
    - **Método de personalización**: supervisado
    - **Sufijo del modelo**: `ft-travel`
    - **Recurso de IA conectado**: *selecciona la conexión que se ha creado al crear tu centro. Debería estar seleccionada de forma predeterminada.*
    - **Datos de aprendizaje**: carga los archivos

    <details>  
    <summary><b>Sugerencia de solución de problemas: error de permisos</b></summary>
    <p>Si recibes un error de permisos, prueba lo siguiente para solucionar los problemas:</p>
    <ul>
        <li>En Azure Portal, selecciona el recurso Servicios de IA.</li>
        <li>En Administración de recursos, en la pestaña Identidad, confirma que se trata de una identidad administrada asignada por el sistema.</li>
        <li>Ve a la cuenta de almacenamiento asociada. En la página IAM, agrega la asignación de roles <em>Propietario de datos de Storage Blob</em>.</li>
        <li>En <strong>Asignar acceso a</strong>, elige <strong>Identidad administrada</strong>, <strong>+Seleccionar miembros</strong>, selecciona <strong>Todas las identidades administradas asignadas por el sistema</strong> y selecciona tu recurso de Servicios de Azure AI.</li>
        <li>Selecciona Revisar y asignar para guardar la nueva configuración y volver a intentar el paso anterior.</li>
    </ul>
    </details>

    - **Cargar archivo**: selecciona el archivo JSON que descargaste en el paso anterior.
    - **Datos de validación**: ninguno
    - **Parámetros de tarea**: *mantén la configuración predeterminada*
1. El ajuste se iniciará y puede tardar algún tiempo en completarse. Puedes continuar con la siguiente sección del ejercicio mientras esperas.

> **Nota**: el ajuste preciso y la implementación pueden tardar un tiempo significativo (30 minutos o más), por lo que es posible que tengas que volver a comprobarlo periódicamente. Puedes ver más detalles del progreso hasta ahora seleccionando el trabajo de ajuste del modelo y viendo su pestaña **Registros**.

## Chatear con un modelo base

Mientras esperas a que se complete el trabajo de ajuste, vamos a chatear con un modelo GPT 4o base para evaluar cómo funciona.

1. En el panel de la izquierda de tu proyecto, en la sección **Mis recursos**, selecciona la página **Modelos y puntos de conexión**.
1. En la página **Modelos y puntos de conexión**, en la pestaña **Implementaciones de modelos**, en el menú **+ Implementar modelo**, selecciona **Implementar modelo base**.
1. Busca el modelo **gpt-4o** en la lista, selecciona y confirma.
1. Implementa el modelo con la siguiente configuración mediante la selección de **Personalizar** en los detalles de implementación:
    - **Nombre de implementación**: *nombre válido para la implementación de modelo*
    - **Tipo de implementación**: estándar global
    - **Actualización automática de la versión**: habilitado
    - **** Versión del modelo: *selecciona la versión disponible más reciente*
    - **Recurso de IA conectado**: *selecciona tu conexión del recurso de Azure OpenIA (si la ubicación actual del recurso de IA no tiene cuota disponible para el modelo que deseas implementar, se te pedirá que elijas otra ubicación donde se creará un nuevo recurso de IA y se conectará al proyecto)*
    - **Límite de velocidad de tokens por minuto (miles):** 50 000 *(o el máximo disponible en la suscripción si es inferior a 50 000)*
    - **Filtro de contenido**: DefaultV2

    > **Nota**: reducir el TPM ayuda a evitar el uso excesivo de la cuota disponible en la suscripción que está usando. 50 000 TPM es suficiente para los datos que se usan en este ejercicio. Si la cuota disponible es inferior a esta, podrás completar el ejercicio, pero se pueden producir errores si se supera el límite de velocidad.

1. Espera a que la implementación se complete.

> **Nota**: si la ubicación actual del recurso de IA no tiene cuota disponible para el modelo que deseas implementar, se te pedirá que elijas otra ubicación donde se creará un nuevo recurso de IA y se conectará al proyecto.

1. Cuando la implementación esté lista, selecciona el botón **Abrir en el área de juegos**.
1. Comprueba que el modelo base gpt-4o implementado está seleccionado en el panel de configuración.
1. En la ventana de chat, escribe una consulta como `What can you do?` y analiza la respuesta:

    Las respuestas pueden ser bastante genéricas. Recuerda que queremos crear una aplicación de chat que inspire a las personas a viajar.

1. Actualiza el mensaje del sistema en el panel de configuración con la siguiente solicitud:

    ```md
    You are an AI assistant that helps people plan their holidays.
    ```

1. Selecciona **Aplicar cambios**, luego selecciona **Borrar chat**, y vuelve a preguntar `What can you do?` Como respuesta, el asistente puede indicarte que puede ayudarte a reservar vuelos, hoteles y coches de alquiler para tu viaje. Quieres evitar este comportamiento.
1. Vuelve a actualizar el mensaje del sistema con un mensaje nuevo:

    ```
    You are an AI travel assistant that helps people plan their trips. Your objective is to offer support for travel-related inquiries, such as visa requirements, weather forecasts, local attractions, and cultural norms.
    You should not provide any hotel, flight, rental car or restaurant recommendations.
    Ask engaging questions to help someone plan their trip and think about what they want to do on their holiday.
    ```

1. Selecciona **Aplicar cambios** y **Borrar chat**.
1. Sigue probando la aplicación de chat para comprobar que no proporciona ninguna información que no se base en los datos recuperados. Por ejemplo, haz las siguientes preguntas y revisa las respuestas del modelo, con especial atención al estilo de tono y escritura que usa el modelo para responder:
   
    `Where in Rome should I stay?`
    
    `I'm mostly there for the food. Where should I stay to be within walking distance of affordable restaurants?`

    `What are some local delicacies I should try?`

    `When is the best time of year to visit in terms of the weather?`

    `What's the best way to get around the city?`

## Revisión del archivo de entrenamiento

El modelo base parece funcionar lo suficientemente bien, pero es posible que estés buscando un estilo conversacional determinado de la aplicación de IA generativa. Los datos de entrenamiento usados para ajustar te ofrecen la posibilidad de crear ejemplos explícitos de los tipos de respuesta que desees.

1. Abre el archivo JSONL que descargaste anteriormente (puedes abrirlo en cualquier editor de texto).
1. Examina la lista de documentos JSON en el archivo de datos de entrenamiento. El primero debe ser similar a este (con formato de legibilidad):

    ```json
    {"messages": [
        {"role": "system", "content": "You are an AI travel assistant that helps people plan their trips. Your objective is to offer support for travel-related inquiries, such as visa requirements, weather forecasts, local attractions, and cultural norms. You should not provide any hotel, flight, rental car or restaurant recommendations. Ask engaging questions to help someone plan their trip and think about what they want to do on their holiday."},
        {"role": "user", "content": "What's a must-see in Paris?"},
        {"role": "assistant", "content": "Oh la la! You simply must twirl around the Eiffel Tower and snap a chic selfie! After that, consider visiting the Louvre Museum to see the Mona Lisa and other masterpieces. What type of attractions are you most interested in?"}
        ]}
    ```

    Cada interacción de ejemplo de la lista incluye el mismo mensaje del sistema que has probado con el modelo base, un mensaje de usuario relacionado con una consulta de viaje y una respuesta. El estilo de las respuestas de los datos de entrenamiento ayudará al modelo optimizado a aprender cómo debe responder.

## Implementación de un modelo con ajuste preciso

Cuando se ha completado correctamente el ajuste preciso, puedes implementar el modelo con ajuste preciso.

1. Ve a la página **Ajuste** en **Crear y personalizar** para encontrar tu trabajo de ajuste y su estado. Si todavía se está ejecutando, puedes optar por seguir chateando con el modelo base implementado o tomar un descanso. Si se completa, puedes continuar.

    > **Sugerencia**: usa el botón **Actualizar** de la página de ajuste para actualizar la vista. Si el trabajo de ajuste desaparece por completo, actualiza la página en el explorador.

1. Selecciona el vínculo de trabajo de ajuste para abrir su página de detalles. A continuación, selecciona la pestaña **Métricas** y explora las métricas de ajuste.
1. Implementa el modelo con ajuste preciso con las siguientes configuraciones:
    - **Nombre de implementación**: *nombre válido para la implementación de modelo*
    - **Tipo de implementación**: estándar
    - **Límite de velocidad de tokens por minuto (miles):** 50 000 *(o el máximo disponible en la suscripción si es inferior a 50 000)*
    - **Filtro de contenido**: valor predeterminado
1. Espera a que se complete la implementación para poder probarla, esto puede tardar un tiempo. Comprueba el **estado de aprovisionamiento** hasta que se haya realizado correctamente (es posible que tengas que actualizar el explorador para ver el estado actualizado).

## Prueba del modelo con ajuste preciso

Ahora que has implementado tu modelo con ajuste preciso, puedes probarlo del mismo modo que probaste tu modelo base implementado.

1. Cuando la implementación esté lista, ve al modelo con ajuste preciso y selecciona **Abrir en área de juegos**.
1. Asegúrate de que el mensaje del sistema incluye estas instrucciones:

    ```
    You are an AI travel assistant that helps people plan their trips. Your objective is to offer support for travel-related inquiries, such as visa requirements, weather forecasts, local attractions, and cultural norms.
    You should not provide any hotel, flight, rental car or restaurant recommendations.
    Ask engaging questions to help someone plan their trip and think about what they want to do on their holiday.
    ```

1. Prueba el modelo ajustado para evaluar si su comportamiento es más coherente ahora. Por ejemplo, vuelve a formular las siguientes preguntas y explora las respuestas del modelo:

    `Where in Rome should I stay?`

    `I'm mostly there for the food. Where should I stay to be within walking distance of affordable restaurants?`

    `What are some local delicacies I should try?`

    `When is the best time of year to visit in terms of the weather?`

    `What's the best way to get around the city?`

1. Después de revisar las respuestas, ¿cómo se comparan con las del modelo base?

## Limpieza

Si has terminado de explorar Azure AI Foundry, deberías eliminar los recursos que has creado para evitar costes innecesarios de Azure.

- Ve a [Azure Portal](https://portal.azure.com) en `https://portal.azure.com`.
- En Azure Portal, en la página **Inicio**, selecciona **Grupos de recursos**.
- Selecciona el grupo de recursos que creaste para este ejercicio.
- En la parte superior de la página **Información general** del grupo de recursos, selecciona **Eliminar grupo de recursos**.
- Escribe el nombre del grupo de recursos para confirmar que quieres eliminarlo y selecciona **Eliminar**.

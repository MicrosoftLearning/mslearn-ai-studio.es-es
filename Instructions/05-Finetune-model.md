---
lab:
  title: Ajuste de un modelo de lenguaje
  description: Obtén información sobre cómo usar tus propios datos de entrenamiento adicionales para ajustar un modelo y personalizar su comportamiento.
---

# Ajuste de un modelo de lenguaje

Cuando desees que un modelo de lenguaje se comporte de una manera determinada, puedes usar la ingeniería de avisos para definir el comportamiento deseado. Cuando quieras mejorar la coherencia del comportamiento deseado, puedes optar por ajustar un modelo, comparándolo con el enfoque de ingeniería de petición para evaluar qué método se adapta mejor a tus necesidades.

En este ejercicio, ajustarás un modelo de lenguaje con Azure AI Foundry que quieres usar para un escenario de aplicación de chat personalizado. Compararás el modelo ajustado con un modelo base para evaluar si el modelo ajustado se adapta mejor a tus necesidades.

Imagina que trabajas para una agencia de viajes y estás desarrollando una aplicación de chat para ayudar a las personas a planificar sus vacaciones. El objetivo es crear un chat sencillo e inspirador que sugiera destinos y actividades. Dado que el chat no está conectado a ningún origen de datos, **no** debe proporcionar recomendaciones específicas para hoteles, vuelos o restaurantes para garantizar la confianza con tus clientes.

Este ejercicio dura aproximadamente **60** minutos\*.

> \***Nota**: este tiempo es una estimación basada en la experiencia media. El ajuste preciso depende de los recursos de infraestructura en la nube, lo que puede tardar una cantidad variable de tiempo en aprovisionar en función de la capacidad del centro de datos y la demanda simultánea. Algunas actividades de este ejercicio pueden tardar <u>mucho</u> tiempo en completarse y requerir paciencia. Si las cosas tardan un rato, considera la posibilidad de revisar la [documentación de ajuste preciso de Fundición de IA de Azure](https://learn.microsoft.com/azure/ai-studio/concepts/fine-tuning-overview) o tomar un descanso.

## Creación de un centro de IA en el Portal de la Fundición de IA de Azure

Para empezar, crea un proyecto de Fundición de IA de Azure en un centro de Azure AI:

1. En un explorador web, abre el [Portal de la Fundición de IA de Azure](https://ai.azure.com) en `https://ai.azure.com` e inicia sesión con tus credenciales de Azure.
1. En la página principal, selecciona **+ Crear un recurso**.
1. En el Asistente para **crear un nuevo proyecto**, crea un proyecto con la siguiente configuración:
    - **Nombre del proyecto**: *un nombre exclusivo para el proyecto*
    - Selecciona **Personalizar**
        - **Hub**: *se autorellena con el nombre predeterminado*
        - **Suscripción**: *se autorellena con tu cuenta con la sesión iniciada*
        - **Grupo de recursos**: (nuevo) *se autorrellena con el nombre de tu proyecto*
        -  **Ubicación**: selecciona **Ayúdame a elegir** y, a continuación, selecciona **gpt-4-finetune** en la ventana Asistente de ubicación y usa la región recomendada\*
        - **Conectar Servicios de Azure AI o Azure OpenAI**: (nuevo) *se rellena automáticamente con el nombre del centro seleccionado*
        - **Conectar Búsqueda de Azure AI**: omite la conexión

    > \* Los recursos de Azure OpenAI están restringidos en el nivel de inquilino por cuotas regionales. Las regiones enumeradas en el asistente de ubicación incluyen la cuota predeterminada para los tipos de modelo usados en este ejercicio. En caso de que se alcance un límite de cuota más adelante en el ejercicio, es posible que tengas que crear otro recurso en otra región. Más información sobre las [regiones de modelo de precisión](https://learn.microsoft.com/azure/ai-services/openai/concepts/models?tabs=python-secure%2Cglobal-standard%2Cstandard-chat-completions#fine-tuning-models)

1. Revisa la configuración y crea el proyecto.
1. Espera a que se cree el proyecto.

## Ajustar un modelo GPT-4

Dado que el ajuste preciso de un modelo tarda algún tiempo en completarse, iniciarás el trabajo de ajuste preciso ahora y volverás a él después de explorar un modelo base que no se haya ajustado con fines de comparación.

1. Descarga el [conjunto de datos de entrenamiento](https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/refs/heads/main/data/travel-finetune-hotel.jsonl) en `https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/refs/heads/main/data/travel-finetune-hotel.jsonl`y guárdalo como un archivo JSONL localmente.

    > **Nota**: es posible que el dispositivo guarde el archivo de forma predeterminada como un archivo .txt. Selecciona todos los archivos y quita el sufijo .txt para asegurarte de que vas a guardar el archivo como JSONL.

1. Ve a la página **Ajuste** de la sección **Crear y personalizar**, con el menú de la izquierda.
1. Selecciona el botón para agregar un nuevo modelo de ajuste, selecciona el modelo `gpt-4` y, a continuación, selecciona **Siguiente**.
1. **Ajusta** el modelo mediante la siguiente configuración:
    - **Versión del modelo**: *Selecciona la versión predeterminada*
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
1. El ajuste se iniciará y puede tardar algún tiempo en completarse.

> **Nota**: el ajuste preciso y la implementación pueden tardar un tiempo significativo (30 minutos o más), por lo que es posible que tengas que volver a comprobarlo periódicamente. Ya puedes continuar con el paso siguiente mientras esperas.

## Chatear con un modelo base

Mientras esperas a que se complete el trabajo de ajuste, vamos a chatear con un modelo GPT 4 base para evaluar cómo funciona.

1. Ve a la página **Modelos + puntos de conexión** en la sección **Mis recursos**, mediante el menú de la izquierda.
1. Selecciona el botón **+ Implementar modelo** y selecciona la opción **Implementar modelo base**.
1. Implementa un modelo `gpt-4` con la siguiente configuración:
    - **Nombre de implementación**: *un nombre único para el modelo, puedes usar el predeterminado*
    - **Tipo de implementación**: estándar
    - **Límite de frecuencia de tokens por minuto (miles)**: 5000
    - **Filtro de contenido**: valor predeterminado

> **Nota**: si la ubicación actual del recurso de IA no tiene cuota disponible para el modelo que deseas implementar, se te pedirá que elijas otra ubicación donde se creará un nuevo recurso de IA y se conectará al proyecto.

1. Cuando la implementación esté lista, selecciona el botón **Abrir en el área de juegos**.
1. Comprueba que el modelo base `gpt-4` implementado está seleccionado en el panel de configuración.
1. En la ventana de chat, escribe una consulta como `What can you do?` y analiza la respuesta:
    Las respuestas son muy genéricas. Recuerda que queremos crear una aplicación de chat que inspire a las personas a viajar.
1. Actualiza el mensaje del sistema en el panel de configuración con la siguiente solicitud:

    ```md
    You are an AI assistant that helps people plan their holidays.
    ```

1. Selecciona **Aplicar cambios**, luego selecciona **Borrar chat**, y vuelve a preguntar `What can you do?` Como respuesta, el asistente puede indicarte que puede ayudarte a reservar vuelos, hoteles y coches de alquiler para tu viaje. Quieres evitar este comportamiento.
1. Vuelve a actualizar el mensaje del sistema con un mensaje nuevo:

    ```md
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
1. Selecciona el modelo con ajuste preciso. Selecciona la pestaña **Métricas** y explora las métricas de ajuste preciso.
1. Implementa el modelo con ajuste preciso con las siguientes configuraciones:
    - **Nombre de implementación**: *un nombre único para el modelo, puedes usar el predeterminado*
    - **Tipo de implementación**: estándar
    - **Límite de frecuencia de tokens por minuto (miles)**: 5000
    - **Filtro de contenido**: valor predeterminado
1. Espera a que se complete la implementación para poder probarla, esto puede tardar un tiempo. Comprueba el **estado de aprovisionamiento** hasta que se haya realizado correctamente (es posible que tengas que actualizar el explorador para ver el estado actualizado).

## Prueba del modelo con ajuste preciso

Ahora que has implementado tu modelo con ajuste preciso, puedes probarlo del mismo modo que probaste tu modelo base implementado.

1. Cuando la implementación esté lista, ve al modelo con ajuste preciso y selecciona **Abrir en área de juegos**.
1. Asegúrate de que el mensaje del sistema incluye estas instrucciones:

    ```md
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

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

> \***Nota**: este tiempo es una estimación basada en la experiencia media. El ajuste preciso depende de los recursos de infraestructura en la nube, lo que puede tardar una cantidad variable de tiempo en aprovisionar en función de la capacidad del centro de datos y la demanda simultánea. Algunas actividades de este ejercicio pueden tardar <u>mucho</u> tiempo en completarse y requerir paciencia. Si las cosas tardan un rato, considera la posibilidad de revisar la [documentación de ajuste preciso de Fundición de IA de Azure](https://learn.microsoft.com/azure/ai-studio/concepts/fine-tuning-overview) o tomar un descanso. Es posible que algunos procesos agoten el tiempo de espera o parezcan ejecutarse indefinidamente. Algunas de las tecnologías que se usan en este ejercicio están en versión preliminar o en desarrollo activo. Puede que se produzcan algunos errores, comportamientos o advertencias inesperados.

## Implementación de un modelo en un proyecto de Fundición de IA de Azure

Comencemos implementando un modelo en un proyecto de Fundición de IA de Azure.

1. En un explorador web, abre el [Portal de la Fundición de IA de Azure](https://ai.azure.com) en `https://ai.azure.com` e inicia sesión con tus credenciales de Azure. Cierra las sugerencias o paneles de inicio rápido que se abran la primera vez que inicias sesión y, si es necesario, usa el logotipo de **Fundición de IA de Azure** en la parte superior izquierda para navegar a la página principal, que es similar a la siguiente imagen (cierra el panel **Ayuda** si está abierto):

    ![Captura de pantalla del Portal de la Fundición de IA de Azure.](./media/ai-foundry-home.png)

1. En la página principal, en la sección **Explorar modelos y funcionalidades**, busca el modelo `gpt-4o`, que usaremos en nuestro proyecto.
1. En los resultados de la búsqueda, selecciona el modelo **gpt-4o** para ver sus detalles y, a continuación, en la parte superior de la página del modelo, selecciona **Usar este modelo**.
1. Cuando se te pida que crees un proyecto, escribe un nombre válido para el proyecto y expande **Opciones avanzadas**.
1. Selecciona **Personalizar** y especifica la siguiente configuración para el centro:
    - **Recurso de Fundición de IA de Azure**: *un nombre válido para el recurso de Fundición de IA de Azure*
    - **Suscripción**: *suscripción a Azure*
    - **Grupo de recursos**: *crea o selecciona un grupo de recursos*
    - **Región**: *selecciona cualquiera de las siguientes regiones*:\*
        - Este de EE. UU. 2
        - Centro-Norte de EE. UU
        - Centro de Suecia

    > \* En el momento de escribir, estas regiones admitían el ajuste del modelo gpt-4o.

1. Selecciona **Crear** y espera a que se cree el proyecto, incluida la implementación del modelo gpt-4 que seleccionaste.
1. Cuando se cree el proyecto, el área de juegos de chat se abrirá automáticamente.
1. En el panel **Configuración**, anota el nombre de la implementación del modelo; que debe ser **gpt-4o**. Para confirmarlo, mira la implementación en la página **Modelos y puntos de conexión** (simplemente abre esa página en el panel de navegación de la izquierda).
1. En el panel de navegación de la izquierda, selecciona **Información general** para ver la página principal del proyecto; que tiene este aspecto:

    > **Nota**: si se muestra un error de *permisos insuficientes**, usa el botón **Reparar** para resolverlo.

    ![Captura de pantalla de la página de información general de un proyecto de Fundición de IA de Azure.](./media/ai-foundry-project.png)

## Ajuste con precisión de un modelo

Dado que el ajuste de un modelo tarda algún tiempo en completarse, iniciarás el trabajo de ajuste ahora y volverás a él después de explorar un modelo base gpt-4o que ya has implementado.

1. Descarga el [conjunto de datos de entrenamiento](https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/refs/heads/main/data/travel-finetune-hotel.jsonl) en `https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/refs/heads/main/data/travel-finetune-hotel.jsonl`y guárdalo como un archivo JSONL localmente.

    > **Nota**: es posible que el dispositivo guarde el archivo de forma predeterminada como un archivo .txt. Selecciona todos los archivos y quita el sufijo .txt para asegurarte de que vas a guardar el archivo como JSONL.

1. Ve a la página **Ajuste** de la sección **Crear y personalizar**, con el menú de la izquierda.
1. Selecciona el botón para agregar un nuevo modelo de ajuste, selecciona el modelo **gpt-4o** y, a continuación, selecciona **Siguiente**.
1. **Ajusta** el modelo mediante la siguiente configuración:
    - **Método de personalización**: supervisado
    - **** Modelo base: *selecciona la versión predeterminada de **gpt-4o.***
    - **** Datos de entrenamiento: *selecciona la opción **Agregar datos de entrenamiento** y cargar y aplicar el archivo .jsonl que descargaste anteriormente.*
    - **Sufijo del modelo**: `ft-travel`
    - **Valor de inicialización** *aleatorio
1. Envía los detalles de ajuste y se iniciará el trabajo. Puede tardar algún tiempo en completarse. Puedes continuar con la siguiente sección del ejercicio mientras esperas.

> **Nota**: el ajuste preciso y la implementación pueden tardar un tiempo significativo (30 minutos o más), por lo que es posible que tengas que volver a comprobarlo periódicamente. Puedes ver más detalles del progreso hasta ahora seleccionando el trabajo de ajuste del modelo y viendo su pestaña **Registros**.

## Chatear con un modelo base

Mientras esperas a que se complete el trabajo de ajuste, vamos a chatear con un modelo GPT 4o base para evaluar cómo funciona.

1. En el panel de navegación de la izquierda, selecciona **Área de juegos** y abre el **área de juegos de chat**.
1. Comprueba que el modelo base **gpt-4o** implementado está seleccionado en el panel de configuración.
1. En la ventana de chat, escribe una consulta como `What can you do?` y analiza la respuesta:

    Las respuestas pueden ser bastante genéricas. Recuerda que queremos crear una aplicación de chat que inspire a las personas a viajar.

1. Actualiza el mensaje del sistema en el panel de configuración con la siguiente solicitud:

    ```
    You are an AI assistant that helps people plan their travel.
    ```

1. Selecciona **Aplicar cambios** para actualizar el mensaje del sistema.
1. En la ventana de chat, escribe otra vez la consulta `What can you do?` y visualiza la respuesta.
1 Como respuesta, el asistente puede indicarte que puede ayudarte a reservar vuelos, hoteles y coches de alquiler para tu viaje. Quieres evitar este comportamiento.

1. Vuelve a actualizar el mensaje del sistema con un mensaje nuevo:

    ```
    You are an AI travel assistant that helps people plan their trips. Your objective is to offer support for travel-related inquiries, such as visa requirements, weather forecasts, local attractions, and cultural norms.
    You should not provide any hotel, flight, rental car or restaurant recommendations.
    Ask engaging questions to help someone plan their trip and think about what they want to do on their holiday.
    ```
.
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

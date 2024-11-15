---
lab:
  title: Ajusta de un modelo de lenguaje para la finalización del chat en Inteligencia artificial de Azure Studio
---

# Ajusta de un modelo de lenguaje para la finalización del chat en Inteligencia artificial de Azure Studio

Cuando desees que un modelo de lenguaje se comporte de una manera determinada, puedes usar la ingeniería de avisos para definir el comportamiento deseado. Cuando quieras mejorar la coherencia del comportamiento deseado, puedes optar por ajustar un modelo, comparándolo con el enfoque de ingeniería de petición para evaluar qué método se adapta mejor a tus necesidades.

En este ejercicio, ajustarás un modelo de lenguaje con Inteligencia artificial de Azure Studio que quieres usar para un escenario de aplicación de chat personalizado. Compararás el modelo ajustado con un modelo base para evaluar si el modelo ajustado se adapta mejor a tus necesidades.

Imagina que trabajas para una agencia de viajes y estás desarrollando una aplicación de chat para ayudar a las personas a planificar sus vacaciones. El objetivo es crear un chat sencillo e inspirador que sugiera destinos y actividades. Dado que el chat no está conectado a ningún origen de datos, **no** debe proporcionar recomendaciones específicas para hoteles, vuelos o restaurantes para garantizar la confianza con tus clientes.

Este ejercicio dura aproximadamente **60** minutos.

## Crear un centro de inteligencia artificial y un proyecto en Inteligencia artificial de Azure Studio

Para empezar, crea un proyecto de Inteligencia artificial de Azure Studio en un concentrador de Azure AI:

1. En un explorador web, abre [https://ai.azure.com](https://ai.azure.com) e inicia sesión con tus credenciales de Azure.
1. Selecciona la página **Inicio** y luego **+ Nuevo proyecto**.
1. En el asistente para **Crear un nuevo proyecto**, crea un proyecto con la siguiente configuración:
    - **Nombre del proyecto**: *un nombre exclusivo para el proyecto*
    - **Concentrador**: *crear un centro con la siguiente configuración:*
    - **Nombre del centro**: *un nombre único*
    - **Suscripción**: *suscripción de Azure*
    - **Grupo de recursos**: *un nuevo grupo de recursos*
    - **Ubicación**: elige una de las siguientes regiones **Este de EE. UU. 2**, **Centro-norte de EE. UU.**, **Centro de Suecia**, **Oeste de Suiza**\*
    - **Conectar Servicios de Azure AI o Azure OpenAI**: (nuevo) *se rellena automáticamente con el nombre del centro seleccionado*
    - **Conectar Búsqueda de Azure AI**: omitir la conexión

    > \* Los recursos de Azure OpenAI están restringidos en el nivel de inquilino por cuotas regionales. Las regiones enumeradas en el asistente de ubicación incluyen la cuota predeterminada para los tipos de modelo usados en este ejercicio. Elegir aleatoriamente una región reduce el riesgo de que una sola región alcance su límite de cuota. En caso de que se alcance un límite de cuota más adelante en el ejercicio, es posible que tengas que crear otro recurso en otra región. Más información sobre las [regiones de modelo de precisión](https://learn.microsoft.com/en-us/azure/ai-services/openai/concepts/models?tabs=python-secure%2Cglobal-standard%2Cstandard-chat-completions#fine-tuning-models)

1. Revisa la configuración y crea el proyecto.
1. Espera a que se cree el proyecto.

## Ajuste de un modelo GPT-3.5

Dado que el ajuste preciso de un modelo tarda algún tiempo en completarse, primero iniciarás el trabajo de ajuste preciso. Para poder ajustar un modelo, necesitas un conjunto de datos.

1. Guarda el conjunto de datos de aprendizaje como archivo JSONL localmente: [https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/main/data/travel-finetune.jsonl](https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/refs/heads/main/data/travel-finetune-hotel.jsonl)

    > **Nota**: Es posible que el dispositivo guarde el archivo de forma predeterminada como un archivo .txt. Selecciona todos los archivos y quita el sufijo .txt para asegurarte de que vas a guardar el archivo como JSONL.

1. Ve a la página **Ajuste preciso** en la sección **Herramientas** con el menú de la izquierda.
1. Selecciona el botón para agregar un nuevo modelo de ajuste, selecciona el modelo `gpt-35-turbo` y selecciona **Confirmar**.
1. **Ajusta** el modelo mediante la siguiente configuración:
    - **Versión del modelo**: *Selecciona la versión predeterminada*
    - **Sufijo del modelo**: `ft-travel`
    - **Recurso de Azure OpenAI conectado**: *selecciona la conexión predeterminada que creaste al crear el centro*
    - **Datos de aprendizaje**: carga los archivos

    <details>  
    <summary><b>Sugerencia de solución de problemas: error de permisos</b></summary>
    <p>Si recibes un error de permisos al crear un nuevo flujo de avisos, prueba lo siguiente para solucionar los problemas:</p>
    <ul>
        <li>En Azure Portal, selecciona el recurso Servicios de IA.</li>
        <li>En la página IAM, en la pestaña Identidad, confirma que se trata de una identidad administrada asignada por el sistema.</li>
        <li>Ve a la cuenta de almacenamiento asociada. En la página IAM, agrega la asignación de roles <em>Lector de datos de Storage Blob</em>.</li>
        <li>En <strong>Asignar acceso a</strong>, elige <strong>Identidad administrada</strong>, <strong>++ Seleccionar miembros</strong> y selecciona <strong>Todas las identidades administradas asignadas por el sistema</strong>.</li>
        <li>Selecciona Revisar y asignar para guardar la nueva configuración y volver a intentar el paso anterior.</li>
    </ul>
    </details>

    - **Cargar archivo**: selecciona el archivo JSON que descargaste en el paso anterior.
    - **Datos de validación**: ninguno
    - **Parámetros de tarea**: *mantén la configuración predeterminada*
1. El ajuste se iniciará y puede tardar algún tiempo en completarse.

> **Nota**: el ajuste y la implementación pueden tardar algún tiempo, por lo que es posible que tengas que volver a comprobarlo periódicamente. Ya puedes continuar con el paso siguiente mientras esperas.

## Chatear con un modelo base

Mientras esperas a que se complete el trabajo de ajuste, vamos a chatear con un modelo GPT 3.5 base para evaluar cómo funciona.

1. Ve a la página **Implementaciones** en la sección **Componentes**, mediante el menú de la izquierda.
1. Selecciona el botón **+ Implementar modelo** y selecciona la opción **Implementar modelo base**.
1. Implementa un modelo `gpt-35-turbo`, que es el mismo tipo de modelo que usaste al ajustar.
1. Cuando se complete la implementación, ve a la página **Chat** en la sección **Área de juegos del proyecto**.
1. Selecciona el modelo base `gpt-35-model` implementado en la implementación de instalación.
1. En la ventana de chat, escribe una consulta como `What can you do?` y analiza la respuesta:
    Las respuestas son muy genéricas. Recuerda que queremos crear una aplicación de chat que inspire a las personas a viajar.
1. Actualiza el mensaje del sistema con la siguiente solicitud:
    ```md
    You are an AI assistant that helps people plan their holidays.
    ```
1. Selecciona **Guardar** y, después, selecciona **Borrar chat** y vuelve a preguntar `What can you do?` Como respuesta, el asistente puede indicarte que puede ayudarte a reservar vuelos, hoteles y coches de alquiler para tu viaje. Quieres evitar este comportamiento.
1. Vuelve a actualizar el mensaje del sistema con un mensaje nuevo:

    ```md
    You are an AI travel assistant that helps people plan their trips. Your objective is to offer support for travel-related inquiries, such as visa requirements, weather forecasts, local attractions, and cultural norms.
    You should not provide any hotel, flight, rental car or restaurant recommendations.
    Ask engaging questions to help someone plan their trip and think about what they want to do on their holiday.
    ```

1. Selecciona **Guardar** y **Borrar chat**.
1. Sigue probando la aplicación de chat para comprobar que no proporciona ninguna información que no se base en los datos recuperados. Por ejemplo, haz las siguientes preguntas y explora las respuestas del modelo:
   
    `Where in Rome should I stay?`
    
    `I'm mostly there for the food. Where should I stay to be within walking distance of affordable restaurants?`

    `Give me a list of five bed and breakfasts in Trastevere.`

    El modelo puede proporcionarte una lista de hoteles, incluso cuando le indiques que no proporcione recomendaciones sobre hoteles. Este es un ejemplo de comportamiento incoherente. Vamos a explorar si el modelo ajustado funciona mejor en estos casos.

1. Ve a la página **Ajuste preciso** en **Herramientas** para encontrar el trabajo de ajuste y su estado. Si sigue ejecutándose, puedes optar por seguir evaluando manualmente el modelo base implementado. Si se ha completado, puedes continuar con la sección siguiente.

## Implementación de un modelo con ajuste preciso

Cuando se ha completado correctamente el ajuste preciso, puedes implementar el modelo con ajuste preciso.

1. Selecciona el modelo con ajuste preciso. Selecciona la pestaña **Métricas** y explora las métricas de ajuste preciso.
1. Implementa el modelo con ajuste preciso con las siguientes configuraciones:
    - **Nombre de implementación**: *un nombre único para el modelo, puedes usar el predeterminado*
    - **Tipo de implementación**: estándar
    - **Límite de frecuencia de tokens por minuto (miles)**: 5000
    - **Filtro de contenido**: valor predeterminado
1. Espera a que se complete la implementación para poder probarla, esto puede tardar un tiempo.

## Prueba del modelo con ajuste preciso

Ahora que has implementado tu modelo con ajuste preciso, puedes probar el modelo del mismo modo que puedes probar tu modelo base implementado.

1. Cuando la implementación esté lista, ve al modelo con ajuste preciso y selecciona **Abrir en área de juegos**.
1. Actualiza el mensaje del sistema con las siguientes instrucciones:

    ```md
    You are an AI travel assistant that helps people plan their trips. Your objective is to offer support for travel-related inquiries, such as visa requirements, weather forecasts, local attractions, and cultural norms.
    You should not provide any hotel, flight, rental car or restaurant recommendations.
    Ask engaging questions to help someone plan their trip and think about what they want to do on their holiday.
    ```

1. Prueba el modelo ajustado para evaluar si su comportamiento es más coherente ahora. Por ejemplo, vuelve a formular las siguientes preguntas y explora las respuestas del modelo:
   
    `Where in Rome should I stay?`
    
    `I'm mostly there for the food. Where should I stay to be within walking distance of affordable restaurants?`

    `Give me a list of five bed and breakfasts in Trastevere.`

## Limpiar

Si has terminado de explorar Inteligencia artificial de Azure Studio, debes eliminar los recursos que has creado para evitar costes innecesarios de Azure.

- Ve a [Azure Portal](https://portal.azure.com) en `https://portal.azure.com`.
- En Azure Portal, en la página **Inicio**, selecciona **Grupos de recursos**.
- Selecciona el grupo de recursos que creaste para este ejercicio.
- En la parte superior de la página **Información general** del grupo de recursos, selecciona **Eliminar grupo de recursos**.
- Escribe el nombre del grupo de recursos para confirmar que quieres eliminarlo y selecciona **Eliminar**.

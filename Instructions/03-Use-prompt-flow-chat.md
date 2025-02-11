---
lab:
  title: Uso de un flujo de avisos para administrar la conversación en una aplicación de chat
  description: Obtén información sobre cómo usar los flujos de avisos para administrar diálogos conversacionales y asegurarte de que las indicaciones se construyen y orquestan para obtener los mejores resultados.
---

# Uso de un flujo de avisos para administrar la conversación en una aplicación de chat

En este ejercicio, usarás el flujo de avisos del Portal de la Fundición de IA de Azure para crear una aplicación de chat personalizada que use una indicación de usuario y el historial de chat como entradas, y use un modelo GPT de Azure OpenAI para generar una salida.

Este ejercicio dura aproximadamente **30** minutos.

## Creación de un centro de IA en el Portal de la Fundición de IA de Azure

Para empezar, crea un proyecto de Portal de la Fundición de IA de Azure en un concentrador de Azure AI:

1. En un explorador web, abre [https://ai.azure.com](https://ai.azure.com) e inicia sesión con tus credenciales de Azure.
1. En la página principal, selecciona **+Crear proyecto**.
1. En el Asistente para **crear un proyecto** puedes ver todos los recursos de Azure que se crearán automáticamente con tu proyecto, o puedes personalizar la siguiente configuración al seleccionar **Personalizar** antes de seleccionar **Crear**:

    - **Nombre del centro**: *un nombre único*
    - **Suscripción**: *suscripción a Azure*
    - **Grupo de recursos**: *un nuevo grupo de recursos*
    - **Ubicación**: selecciona **Ayúdeme a elegir** y, a continuación, selecciona **gpt-35-turbo** en la ventana Asistente de ubicación y usa la región recomendada\*
    - **Conectar Servicios de Azure AI o Azure OpenAI**: (nuevo) *se rellena automáticamente con el nombre del centro seleccionado*
    - **Conectar Búsqueda de Azure AI**: omitir la conexión

    > \* Los recursos de Azure OpenAI están restringidos en el nivel de inquilino por cuotas regionales. Las regiones enumeradas en el asistente de ubicación incluyen la cuota predeterminada para los tipos de modelo usados en este ejercicio. Elegir aleatoriamente una región reduce el riesgo de que una sola región alcance su límite de cuota. En caso de que se alcance un límite de cuota más adelante en el ejercicio, es posible que tengas que crear otro recurso en otra región. Más información sobre la [disponibilidad del modelo por región](https://learn.microsoft.com/azure/ai-services/openai/concepts/models#gpt-35-turbo-model-availability)

1. Si has seleccionado **Personalizar**, selecciona **Siguiente** y revisa tu configuración.
1. Selecciona **Crear** y espera a que se complete el proceso.

## Implementación de un modelo GPT

Para usar un modelo de lenguaje en el flujo de avisos, primero debes implementar un modelo. El Portal de la Fundición de IA de Azure permite implementar modelos de OpenAI que puedes usar en los flujos.

1. En el panel de navegación de la izquierda, en **Mis recursos**, selecciona la página **Modelos + puntos de conexión**.
1. Cree una nueva implementación del modelo de **gpt-35-turbo** con la siguiente configuración:
    - **Nombre de implementación**: *Un nombre único para la implementación de modelo*
    - **Tipo de implementación**: estándar
    - **Versión del modelo**: *Selecciona la versión predeterminada*
    - **Recurso de IA**: *selecciona el recurso creado anteriormente*
    - **Límite de frecuencia de tokens por minuto (miles)**: 5000
    - **Filtro de contenido**: DefaultV2
    - **Habilitación de la cuota dinámica**: deshabilitada

    > **Nota**: si la ubicación actual del recurso de IA no tiene cuota disponible para el modelo que deseas implementar, se te pedirá que elijas otra ubicación donde se creará un nuevo recurso de IA y se conectará al proyecto.

1. Espera a que se implemente el modelo. Cuando la implementación esté lista, selecciona **Abrir en el área de juegos**.
1. En la ventana de chat, escribe la consulta `What can you do?`.

    Ten en cuenta que la respuesta es genérica porque no hay instrucciones específicas para el asistente. Para que se centre en una tarea, puedes cambiar el aviso del sistema.

1. Cambia el mensaje **Dar instrucciones y contexto al modelo** a lo siguiente:

   ```md
   **Objective**: Assist users with travel-related inquiries, offering tips, advice, and recommendations as a knowledgeable travel agent.

   **Capabilities**:
   - Provide up-to-date travel information, including destinations, accommodations, transportation, and local attractions.
   - Offer personalized travel suggestions based on user preferences, budget, and travel dates.
   - Share tips on packing, safety, and navigating travel disruptions.
   - Help with itinerary planning, including optimal routes and must-see landmarks.
   - Answer common travel questions and provide solutions to potential travel issues.
    
   **Instructions**:
   1. Engage with the user in a friendly and professional manner, as a travel agent would.
   2. Use available resources to provide accurate and relevant travel information.
   3. Tailor responses to the user's specific travel needs and interests.
   4. Ensure recommendations are practical and consider the user's safety and comfort.
   5. Encourage the user to ask follow-up questions for further assistance.
   ```

1. Selecciona **Aplicar cambios**.
1. En la ventana de chat, escribe la misma consulta que antes: `What can you do?` Observa el cambio en respuesta.

Ahora que has jugado con el mensaje del sistema para el modelo GPT implementado, puedes personalizar aún más la aplicación trabajando con el flujo de avisos.

## Creación y ejecución de un flujo de chat en el Portal de la Fundición de IA de Azure

Puedes crear un flujo a partir de una plantilla o crear un flujo en función de las configuraciones del área de juegos. Puesto que ya estabas experimentando en el área de juegos, usarás esta opción para crear un nuevo flujo.

<details>  
    <summary><b>Sugerencia de solución de problemas: error de permisos</b></summary>
    <p>Si recibes un error de permisos al crear un nuevo flujo de avisos, prueba lo siguiente para solucionar los problemas:</p>
    <ul>
        <li>En Azure Portal, selecciona el recurso Servicios de IA.</li>
        <li>En Administración de recursos, en la pestaña Identidad, confirma que se trata de una identidad administrada asignada por el sistema.</li>
        <li>Ve a la cuenta de almacenamiento asociada. En la página IAM, agrega la asignación de roles <em>Lector de datos de Storage Blob</em>.</li>
        <li>En <strong>Asignar acceso a</strong>, elige <strong>Identidad administrada</strong>, <strong>+ Seleccionar miembros</strong>, selecciona <strong>Todas las identidades administradas asignadas por el sistema</strong> y selecciona tu recurso de Servicios de Azure AI.</li>
        <li>Selecciona Revisar y asignar para guardar la nueva configuración y volver a intentar el paso anterior.</li>
    </ul>
</details>

1. En la barra superior del **Área de juegos de chat**, seleccione **Flujo de avisos**.
1. Escriba `Travel-Chat` como nombre de carpeta.

    Se crea un flujo de chat sencillo automáticamente. Tenga en cuenta que hay dos entradas (el historial de chats y la pregunta del usuario), un nodo LLM que se conectará con el modelo de lenguaje implementado y una salida para reflejar la respuesta en el chat.

    Para poder probar el flujo, se necesita un proceso.

1. Seleccione **Iniciar sesión de proceso** en la barra superior.
1. La sesión de proceso tardará entre 1 y 3 minutos en iniciarse.
1. Busque el nodo LLM denominado **chat**. Tenga en cuenta que el aviso ya incluye el aviso del sistema que especificó en el área de juegos de chat.

    Todavía tiene que conectar el nodo LLM al modelo implementado.

1. En la sección nodo LLM, para **Conexión**, seleccione la conexión que se creó automáticamente al crear el centro de IA.
1. Para **API**, seleccione **chat**.
1. Para **deployment_name**, seleccione el modelo **gpt-35-turbo** que implementó.
1. Para **response_format**, seleccione **{"type":"text"}**.
1. Revise el campo de aviso y asegúrese de que tiene el siguiente aspecto:

   ```yml
   system:
   **Objective**: Assist users with travel-related inquiries, offering tips, advice, and recommendations as a knowledgeable travel agent.

   **Capabilities**:
   - Provide up-to-date travel information, including destinations, accommodations, transportation, and local attractions.
   - Offer personalized travel suggestions based on user preferences, budget, and travel dates.
   - Share tips on packing, safety, and navigating travel disruptions.
   - Help with itinerary planning, including optimal routes and must-see landmarks.
   - Answer common travel questions and provide solutions to potential travel issues.

   **Instructions**:
   1. Engage with the user in a friendly and professional manner, as a travel agent would.
   2. Use available resources to provide accurate and relevant travel information.
   3. Tailor responses to the user's specific travel needs and interests.
   4. Ensure recommendations are practical and consider the user's safety and comfort.
   5. Encourage the user to ask follow-up questions for further assistance.

   {% for item in chat_history %}
   user:
   {{item.inputs.question}}
   assistant:
   {{item.outputs.answer}}
   {% endfor %}

   user:
   {{question}}
   ```

### Probar e implementar el modelo

Ahora que ha desarrollado el flujo, puede usar la ventana de chat para probar el flujo.

1. Asegúrese de que la sesión de proceso se está ejecutando.
1. Seleccione **Guardar**.
1. Seleccione **chat** para probar el flujo.
1. Escriba la consulta: `I have one day in London, what should I do?` y revise la salida.

    Cuando esté satisfecho con el comportamiento del flujo que creó, puede implementarlo.

1. Seleccione **Implementar** para implementar el flujo con la siguiente configuración:
    - **Configuración básica**:
        - **Punto de conexión**: Nuevo
        - **Nombre del punto de conexión**: *Escriba un nombre único*
        - **Nombre de implementación**: *Escriba un nombre único*
        - **Máquina virtual**: Standard_DS3_v2
        - **Recuento de instancias**: 3
        - **Recopilación de datos de inferencia**: Habilitado
    - **Configuración avanzada**:
        - *Use la configuración predeterminada*
1. En el portal de Azure AI Foundry, en tu proyecto, en el panel de navegación de la izquierda, en **Mis recursos**, selecciona la página **Modelos + puntos de conexión**.
1. Ten en cuenta que,de forma predeterminada, aparecen en la lista los **Modelos implementados**, incluyendo tu modelo de lenguaje implementado y el flujo implementado. La implementación puede tardar algún tiempo antes de que se muestre y se cree correctamente.
1. Cuando la implementación se haya realizado correctamente, selecciónela. A continuación, en la página **Prueba**, escriba el aviso `What is there to do in San Francisco?` y revise la respuesta.
1. Escriba el aviso `Where else could I go?` y revise la respuesta.
1. Vea la página **Consumir** del punto de conexión y tenga en cuenta que contiene información de la conexión y código de ejemplo que puede usar para compilar una aplicación cliente para el punto de conexión, lo que le permite integrar la solución del flujo de avisos en una aplicación como copiloto personalizado.

## Eliminación de recursos de Azure

Cuando termines de explorar el portal de Azure AI Foundry, debes eliminar los recursos que has creado para evitar costes innecesarios de Azure.

- Ve a [Azure Portal](https://portal.azure.com) en `https://portal.azure.com`.
- En Azure Portal, en la página **Inicio**, selecciona **Grupos de recursos**.
- Selecciona el grupo de recursos que creaste para este ejercicio.
- En la parte superior de la página **Información general** del grupo de recursos, selecciona **Eliminar grupo de recursos**.
- Escribe el nombre del grupo de recursos para confirmar que quieres eliminarlo y selecciona **Eliminar**.

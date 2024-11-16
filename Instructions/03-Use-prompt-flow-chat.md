---
lab:
  title: "Creación de copilotos personalizados con el flujo de avisos en Inteligencia artificial de Azure\_Studio"
---

# Creación de copilotos personalizados con el flujo de avisos en Inteligencia artificial de Azure Studio

En este ejercicio, usará el flujo de avisos de Inteligencia artificial de Azure Studio para crear un copiloto personalizado que use un aviso de usuario y una historial de chats como entradas, y use un modelo GPT de Azure OpenAI para generar una salida.

Este ejercicio dura aproximadamente **30** minutos.

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
    - **Ubicación**: selecciona **Ayúdeme a elegir** y, a continuación, selecciona **gpt-35-turbo** en la ventana Asistente de ubicación y usa la región recomendada\*
    - **Conectar Servicios de Azure AI o Azure OpenAI**: *crear una conexión*
    - **Conectar Búsqueda de Azure AI**: omitir la conexión

    > \* Los recursos de Azure OpenAI están restringidos en el nivel de inquilino por cuotas regionales. Las regiones enumeradas en el asistente de ubicación incluyen la cuota predeterminada para los tipos de modelo usados en este ejercicio. Elegir aleatoriamente una región reduce el riesgo de que una sola región alcance su límite de cuota. En caso de que se alcance un límite de cuota más adelante en el ejercicio, es posible que tengas que crear otro recurso en otra región. Más información sobre la [disponibilidad del modelo por región](https://learn.microsoft.com/azure/ai-services/openai/concepts/models#gpt-35-turbo-model-availability)

1. Revisa la configuración y crea el proyecto.
1. Espera a que se cree el proyecto.

## Implementación de un modelo GPT

Para usar un modelo de lenguaje en el flujo de avisos, primero debe implementar un modelo. Azure AI Studio permite implementar modelos de OpenAI que puede usar en los flujos.

1. En el panel de navegación de la izquierda, en **Componentes**, seleccione la página **Implementaciones**.
1. Cree una nueva implementación del modelo de **gpt-35-turbo** con la siguiente configuración:
    - **Nombre de implementación**: *Un nombre único para la implementación de modelo*
    - **Tipo de implementación**: estándar
    - **Versión del modelo**: *Selecciona la versión predeterminada*
    - **Recurso de IA**: *selecciona el recurso creado anteriormente*
    - **Límite de frecuencia de tokens por minuto (miles)**: 5000
    - **Filtro de contenido**: DefaultV2
    - **Habilitación de la cuota dinámica**: deshabilitada
1. Espere a que se implemente el modelo. Cuando la implementación esté lista, seleccione **Abrir en el área de juegos**.
1. En la ventana de chat, escriba la consulta `What can you do?`.

    Tenga en cuenta que la respuesta es genérica porque no hay instrucciones específicas para el asistente. Para que se centre en una tarea, puede cambiar el símbolo del sistema.

1. Cambie el **Mensaje del sistema** a lo siguiente:

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

1. Seleccione **Guardar**.
1. En la ventana de chat, escriba la misma consulta que antes: `What can you do?` Observe el cambio en respuesta.

Ahora que ha jugado con el mensaje del sistema para el modelo GPT implementado, puede personalizar aún más la aplicación trabajando con el flujo de avisos.

## Creación y ejecución de un flujo de chat en Inteligencia artificial de Azure Studio

Puede crear un flujo a partir de una plantilla o crear un flujo en función de las configuraciones del área de juegos. Puesto que ya estaba experimentando en el área de juegos, usará esta opción para crear un nuevo flujo.

<details>  
    <summary><b>Sugerencia de solución de problemas: error de permisos</b></summary>
    <p>Si recibes un error de permisos al crear un nuevo flujo de avisos, prueba lo siguiente para solucionar los problemas:</p>
    <ul>
        <li>En Azure Portal, selecciona el recurso Servicios de IA.</li>
        <li>En la página IAM, en la pestaña Identidad, confirma que se trata de una identidad administrada asignada por el sistema.</li>
        <li>Ve a la cuenta de almacenamiento asociada. En la página IAM, agrega la asignación de roles <em>Lector de datos de Storage Blob</em>.</li>
        <li>En <strong>Asignar acceso a</strong>, elige <strong>Identidad administrada</strong>, <strong>+ Seleccionar miembros</strong> y selecciona <strong>Todas las identidades administradas asignadas por el sistema</strong>.</li>
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
   {% raw %}
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
   {% endraw %}
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
1. En Azure AI Studio, en el proyecto, en el panel de navegación de la izquierda, en **Componentes**, seleccione la página **Implementaciones**.
1. Tenga en cuenta que, de forma predeterminada, se muestran las **implementaciones del modelo**, incluido el modelo de lenguaje implementado.
1. Seleccione la pestaña **Implementaciones de aplicaciones** para buscar el flujo implementado. La implementación puede tardar algún tiempo antes de que se muestre y se cree correctamente.
1. Cuando la implementación se haya realizado correctamente, selecciónela. A continuación, en la página **Prueba**, escriba el aviso `What is there to do in San Francisco?` y revise la respuesta.
1. Escriba el aviso `Where else could I go?` y revise la respuesta.
1. Vea la página **Consumir** del punto de conexión y tenga en cuenta que contiene información de la conexión y código de ejemplo que puede usar para compilar una aplicación cliente para el punto de conexión, lo que le permite integrar la solución del flujo de avisos en una aplicación como copiloto personalizado.

## Eliminación de recursos de Azure

Cuando termines de explorar Inteligencia artificial de Azure Studio, debes eliminar los recursos que has creado para evitar costes innecesarios de Azure.

- Ve a [Azure Portal](https://portal.azure.com) en `https://portal.azure.com`.
- En Azure Portal, en la página **Inicio**, selecciona **Grupos de recursos**.
- Selecciona el grupo de recursos que creaste para este ejercicio.
- En la parte superior de la página **Información general** del grupo de recursos, selecciona **Eliminar grupo de recursos**.
- Escribe el nombre del grupo de recursos para confirmar que quieres eliminarlo y selecciona **Eliminar**.

---
lab:
  title: Uso de un flujo de avisos para administrar la conversación en una aplicación de chat
  description: Obtén información sobre cómo usar los flujos de avisos para administrar diálogos conversacionales y asegurarte de que las indicaciones se construyen y orquestan para obtener los mejores resultados.
---

## Uso de un flujo de avisos para administrar la conversación en una aplicación de chat

En este ejercicio, usarás el flujo de avisos del Portal de la Fundición de IA de Azure para crear una aplicación de chat personalizada que use una indicación de usuario y el historial de chat como entradas, y use un modelo GPT de Azure OpenAI para generar una salida.

Este ejercicio dura aproximadamente **30** minutos.

> **Nota**: Algunas de las tecnologías que se usan en este ejercicio se encuentran en versión preliminar o en desarrollo activo. Puede que se produzcan algunos comportamientos, advertencias o errores inesperados.

## Creación de un centro y un proyecto de Fundición de IA de Azure

Las características de Fundición de IA de Azure que usaremos en este ejercicio requieren un proyecto basado en un recurso del *centro* de Fundición de IA de Azure.

1. En un explorador web, abre el [Portal de la Fundición de IA de Azure](https://ai.azure.com) en `https://ai.azure.com` e inicia sesión con tus credenciales de Azure. Cierra las sugerencias o paneles de inicio rápido que se abran la primera vez que inicias sesión y, si es necesario, usa el logotipo de **Fundición de IA de Azure** en la parte superior izquierda para navegar a la página principal, que es similar a la siguiente imagen (cierra el panel **Ayuda** si está abierto):

    ![Captura de pantalla del Portal de la Fundición de IA de Azure.](./media/ai-foundry-home.png)

1. En el explorador, ve a `https://ai.azure.com/managementCenter/allResources` y selecciona **Crear**. A continuación, elige la opción para crear un nuevo **recurso del centro de IA**.
1. En el asistente para **crear un proyecto**, escribe un nombre válido para el proyecto y si se te sugiere un centro existente, selecciona la opción para crear uno nuevo y expande **Opciones avanzadas** para especificar la siguiente configuración para el proyecto:
    - **Suscripción**: *suscripción a Azure*
    - **Grupo de recursos**: *crea o selecciona un grupo de recursos*
    - **Nombre del centro**: un nombre válido para el centro
    - **Ubicación**: Este de EE. UU. 2 o Centro de Suecia (*En caso de que se alcance un límite de cuota más adelante durante el ejercicio, es posible que tengas que crear otro recurso en otra región.*)

    > **Nota**: si estás trabajando en una suscripción a Azure en la que se usan directivas para restringir los nombres de recursos permitidos, es posible que tengas que usar el vínculo situado en la parte inferior del cuadro de diálogo **Crear un nuevo proyecto** para crear el centro mediante Azure Portal.

    > **Sugerencia**: si el botón **Crear** sigue deshabilitado, asegúrate de cambiar el nombre del centro a un valor alfanumérico único.

1. Espera a que se cree el proyecto.

## Configuración de autorización de recurso

Las herramientas de flujo de avisos de la Fundición de IA de Azure crean recursos basados en archivos que definen el flujo de avisos en una carpeta de Blob Storage. Antes de explorar el flujo de avisos, asegúrate de que el recurso de Fundición de IA de Azure tiene el acceso necesario a Blob Storage para que pueda leerlos.

1. En una nueva pestaña del explorador, abre [Azure Portal](https://portal.azure.com) en `https://portal.azure.com`, inicia sesión con tus credenciales de Azure si se te solicita y visualiza el grupo de recursos que contiene los recursos del centro de Azure AI.
1. Selecciona el recurso de **Fundición de IA de Azure** para el centro para abrirlo. A continuación, expande su sección en **Administración de recursos** y selecciona la página **Identidad**:

    ![Captura de pantalla de la página de identidad de Servicios de Azure IA en Azure Portal.](./media/ai-services-identity.png)

1. Si el estado de la identidad asignada por el sistema es **Desactivado**, cámbialo a **Activado** y guarda los cambios. A continuación, espera a que se confirme el cambio de estado.
1. Vuelve a la página del grupo de recursos y, a continuación, selecciona el recurso de **Cuenta de almacenamiento** del centro y visualiza su página **Control de acceso (IAM)**:

    ![Captura de pantalla de la página de control de acceso de la cuenta de almacenamiento en Azure Portal.](./media/storage-access-control.png)

1. Agrega una asignación de roles al rol `Storage blob data reader` para la identidad administrada que usa el recurso de Fundición de IA de Azure:

    ![Captura de pantalla de la página de control de acceso de la cuenta de almacenamiento en Azure Portal.](./media/assign-role-access.png)

1. Cuando hayas revisado y asignado el acceso al rol para permitir que la identidad administrada de Fundición de IA de Azure lea blobs en la cuenta de almacenamiento, cierra la pestaña Azure Portal y vuelve al Portal de la Fundición de IA de Azure.

## Implementación de un modelo de IA generativa

Ahora ya puedes implementar un modelo de lenguaje de IA generativa compatible con tu aplicación de flujo de avisos.

1. En el panel de la izquierda de tu proyecto, en la sección **Mis recursos**, selecciona la página **Modelos y puntos de conexión**.
1. En la página **Modelos y puntos de conexión**, en la pestaña **Implementaciones de modelos**, en el menú **+ Implementar modelo**, selecciona **Implementar modelo base**.
1. Busca el modelo **gpt-4o** en la lista, selecciona y confirma.
1. Implementa el modelo con la siguiente configuración mediante la selección de **Personalizar** en los detalles de implementación:
    - **Nombre de implementación**: *nombre válido para la implementación de modelo*
    - **Tipo de implementación**: estándar global
    - **Actualización automática de la versión**: habilitado
    - **** Versión del modelo: *selecciona la versión disponible más reciente*
    - **Recurso de IA conectado**: *selecciona tu conexión de recursos de Azure OpenAI*
    - **Límite de velocidad de tokens por minuto (miles):** 50 000 *(o el máximo disponible en la suscripción si es inferior a 50 000)*
    - **Filtro de contenido**: DefaultV2

    > **Nota**: reducir el TPM ayuda a evitar el uso excesivo de la cuota disponible en la suscripción que está usando. 50 000 TPM es suficiente para los datos que se usan en este ejercicio. Si la cuota disponible es inferior a esta, podrás completar el ejercicio, pero se pueden producir errores si se supera el límite de velocidad.

1. Espera a que la implementación se complete.

## Creación de un flujo de avisos

Un flujo de avisos proporciona una manera de orquestar las indicaciones y otras actividades para definir una interacción con un modelo de IA generativa. En este ejercicio, usarás una plantilla para crear un flujo de chat básico para un asistente de IA en una agencia de viajes.

1. En la barra de navegación del Portal de la Fundición de IA de Azure, en la sección **Compilación y personalización**, selecciona **Flujo de avisos**.
1. Crea un nuevo flujo basado en la plantilla **Flujo de chat**; especifica `Travel-Chat` como nombre de carpeta.

    Se crea un flujo de chat sencillo automáticamente.

    > **Sugerencia**: si se produce un error de permisos. Espera unos minutos e inténtalo de nuevo, especificando un nombre de flujo diferente si es necesario.

1. Para poder probar el flujo, necesitas procesar y esto puede tardar un tiempo en empezar; por lo tanto, selecciona **Iniciar sesión de proceso** para empezar mientras exploras y modificas el flujo predeterminado.

1. Visualiza el flujo de avisos, que consta de una serie de *entradas*, *salidas* y *herramientas*. Puedes expandir y editar las propiedades de estos objetos en los paneles de edición de la izquierda y ver el flujo general como gráfico a la derecha:

    ![Captura de pantalla del editor de flujo de avisos.](./media/prompt-flow.png)

1. Visualiza el panel **Entradas** y observa que hay dos entradas (historial de chat y la pregunta del usuario)
1. Visualiza el panel **Salidas** y observa que hay una salida para reflejar la respuesta del modelo.
1. Visualiza el panel de herramientas LLM de **Chat**, que contiene la información necesaria para enviar una indicación al modelo.
1. En el panel de herramientas LLM de **Chat**, en **Conexión**, selecciona la conexión para el recurso de Azure OpenAI Service en el centro de IA. A continuación, configura las siguientes propiedades de conexión:
    - **Api**: chat
    - **deployment_name**: *el modelo gpt-4 que has implementado*
    - **response_format**: {"type":"text"}
1. Modifique el campo **Prompt** como se indica a continuación:

   ```yml
   # system:
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
   # user:
   {{item.inputs.question}}
   # assistant:
   {{item.outputs.answer}}
   {% endfor %}

   # user:
   {{question}}
   ```

    Lee la indicación que agregaste para que estés familiarizado con ella. Consta de un mensaje del sistema (que incluye un objetivo, una definición de sus funcionalidades y algunas instrucciones) y el historial de chat (ordenado para mostrar cada entrada de preguntas de usuario y cada salida de respuesta del asistente anterior)

1. En la sección **Entradas** de la herramienta LLM de **Chat** (bajo la indicación), asegúrate de que se establecen las siguientes variables:
    - **question** (string): ${inputs.question}
    - **chat_history** (string): ${inputs.chat_history}

1. Guarda los cambios en el flujo.

    > **Nota**: en este ejercicio, nos ceñiremos a un flujo de chat sencillo, pero ten en cuenta que el editor de flujo de avisos incluye muchas otras herramientas que puedes agregar al flujo, lo que te permite crear una lógica compleja para organizar conversaciones.

## Prueba del flujo

Ahora que has desarrollado el flujo, puedes usar la ventana de chat para probarlo.

1. Asegúrese de que la sesión de proceso se está ejecutando. A continuación, espera a que se inicie.
1. En la barra de herramientas, selecciona **Chat** para abrir el panel **Chat** y espera a que el chat se inicialice.
1. Escriba la consulta: `I have one day in London, what should I do?` y revise la salida. El panel Chat debe tener un aspecto similar al siguiente:

    ![Captura de pantalla del panel de chat del flujo de avisos.](./media/prompt-flow-chat.png)

## Implementación del flujo

Cuando esté satisfecho con el comportamiento del flujo que creó, puede implementarlo.

> **Nota**: la implementación puede tardar mucho tiempo y puede verse afectada por restricciones de capacidad en tu suscripción o inquilino.

1. En la barra de herramientas, selecciona **Implementar** e implementa el flujo con la siguiente configuración:
    - **Configuración básica**:
        - **Punto de conexión**: Nuevo
        - **Nombre del punto de conexión**: *Escriba un nombre único*
        - **Nombre de implementación**: *Escriba un nombre único*
        - **Máquina virtual**: Standard_DS3_v2
        - **Recuento de instancias**: 1
        - **Recopilación de datos de inferencia**: deshabilitado
    - **Configuración avanzada**:
        - *Use la configuración predeterminada*
1. En el Portal de la Fundición de IA de Azure, en el panel de navegación, en la sección **Mis recursos**, selecciona la página **Modelos y puntos de conexión**.

    Si la página se abre para tu modelo gpt-4o, usa su botón **Atrás** para ver todos los modelos y puntos de conexión.

1. Inicialmente, la página solo puede mostrar las implementaciones del modelo. La implementación puede tardar algún tiempo antes de que se muestre e incluso más antes de que se cree correctamente.
1. Cuando la implementación se haya realizado *correctamente*, selecciónela. A continuación, visualice su página **Prueba**.

    > **Sugerencia**: si la página de prueba describe el punto de conexión como incorrecto, vuelve a los **modelos y puntos de conexión** y espera un minuto aproximadamente antes de actualizar la vista y volver a seleccionar el punto de conexión.

1. Escriba el aviso `What is there to do in San Francisco?` y revise la respuesta.
1. Escriba el aviso `Tell me something about the history of the city.` y revise la respuesta.

    El panel de prueba debe ser similar a este:

    ![Captura de pantalla de la página de prueba del punto de conexión de flujo de avisos implementado.](./media/deployed-flow.png)

1. Visualiza la página **Consumir** del punto de conexión y ten en cuenta que contiene información de la conexión y código de ejemplo que puedes usar para compilar una aplicación cliente para el punto de conexión, lo que te permite integrar la solución del flujo de avisos en una aplicación como aplicación de IA generativa.

## Limpieza

Cuando termines de explorar el flujo de avisos, debes eliminar los recursos que has creado para evitar costes innecesarios de Azure.

- Ve a [Azure Portal](https://portal.azure.com) en `https://portal.azure.com`.
- En Azure Portal, en la página **Inicio**, selecciona **Grupos de recursos**.
- Selecciona el grupo de recursos que creaste para este ejercicio.
- En la parte superior de la página **Información general** del grupo de recursos, selecciona **Eliminar grupo de recursos**.
- Escribe el nombre del grupo de recursos para confirmar que quieres eliminarlo y selecciona **Eliminar**.

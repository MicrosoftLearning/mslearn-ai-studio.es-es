---
lab:
  title: Creación de una aplicación de IA generativa que use tus propios datos
  description: Obtén información sobre cómo usar el modelo de generación aumentada de recuperación (RAG) para crear una aplicación de chat que solicite el uso de tus propios datos.
---

# Creación de una aplicación de IA generativa que use tus propios datos

La generación aumentada de recuperación (RAG) es una técnica que se usa para compilar aplicaciones que integran datos de orígenes de datos personalizados en un aviso de un modelo de IA generativa. La RAG es un patrón que se usa habitualmente para desarrollar aplicaciones de IA generativa: aplicaciones basadas en chat que usan un modelo de lenguaje para interpretar entradas y generar respuestas adecuadas.

En este ejercicio, usarás Fundición de IA de Azure para integrar datos personalizados en una solución de IA generativa.

Este ejercicio dura aproximadamente **45** minutos.

> **Nota**: este ejercicio se basa en servicios de versión preliminar, que están sujetos a cambios.

## Creación de un centro y un proyecto de Fundición de IA de Azure

Las características de Fundición de IA de Azure que usaremos en este ejercicio requieren un proyecto basado en un recurso del *centro* de Fundición de IA de Azure.

1. En un explorador web, abre el [Portal de la Fundición de IA de Azure](https://ai.azure.com) en `https://ai.azure.com` e inicia sesión con tus credenciales de Azure. Cierra las sugerencias o paneles de inicio rápido que se abran la primera vez que inicias sesión y, si es necesario, usa el logotipo de **Fundición de IA de Azure** en la parte superior izquierda para navegar a la página principal, que es similar a la siguiente imagen (cierra el panel **Ayuda** si está abierto):

    ![Captura de pantalla del Portal de la Fundición de IA de Azure.](./media/ai-foundry-home.png)

1. En el explorador, ve a `https://ai.azure.com/managementCenter/allResources` y selecciona **Crear**. A continuación, elige la opción para crear un nuevo **recurso del centro de IA**.
1. En el asistente para **crear un proyecto**, escribe un nombre válido para el proyecto y si se te sugiere un centro existente, selecciona la opción para crear uno nuevo y expande **Opciones avanzadas** para especificar la siguiente configuración para el proyecto:
    - **Suscripción**: *suscripción a Azure*
    - **Grupo de recursos**: *crea o selecciona un grupo de recursos*
    - **Nombre del centro**: un nombre válido para el centro
    - **Ubicación**: Este de EE. UU. 2 o Centro de Suecia\*

    > \* Algunos de los recursos de Azure AI están restringidos por cuotas de modelo regionales. En caso de que se alcance un límite de cuota más adelante en el ejercicio, es posible que tengas que crear otro recurso en otra región.

1. Espere a que se cree el proyecto.

## Implementación de modelos

Necesitas dos modelos para implementar la solución:

- Un modelo de *inserción* para vectorizar datos de texto para una indexación y procesamiento eficaces.
- Modelo que puede generar respuestas de lenguaje natural a preguntas a partir de sus datos.

1. En el portal de Fundición de IA de Azure, en tu proyecto, en el panel de navegación de la izquierda, en **Mis recursos**, selecciona la página **Modelos + puntos de conexión**.
1. Crea una nueva implementación del modelo **text-embedding-ada-002** con la siguiente configuración mediante la selección de **Personalizar** en el asistente para Implementar modelo:

    - **Nombre de implementación**: *un nombre válido para la implementación de modelo*
    - **Tipo de implementación**: estándar global
    - **Versión del modelo**: *selecciona la versión predeterminada*
    - **Recurso de IA conectado**: *selecciona el recurso creado anteriormente*
    - **Límite de velocidad de tokens por minuto (miles):** 50 000 *(o el máximo disponible si tu suscripción es inferior a 50 000*)
    - **Filtro de contenido**: DefaultV2

    > **Nota**: si la ubicación actual del recurso de IA no tiene cuota disponible para el modelo que deseas implementar, se te pedirá que elijas otra ubicación donde se creará un nuevo recurso de IA y se conectará al proyecto.

1. Vuelve a la página **Modelos y puntos de conexión** y repite los pasos anteriores para implementar un modelo **gpt-4o** mediante una implementación **estándar global** de la versión más reciente con un límite de velocidad de TPM de **50 000** (o el máximo disponible en tu suscripción si es inferior a 50 000).

    > **Nota**: reducir los tokens por minuto (TPM) ayuda a evitar el uso excesivo de la cuota disponible en la suscripción que está usando. 50 000 TPM es suficiente para los datos que se usan en este ejercicio.

## Adición de datos al proyecto

Los datos para tu aplicación constan de un conjunto de folletos de viaje en formato PDF de la agencia de viajes ficticia *Margie's Travel*. Vamos a agregarlos al proyecto.

1. En la pestaña del nuevo explorador, descarga el [archivo comprimido de los folletos](https://github.com/MicrosoftLearning/mslearn-ai-studio/raw/main/data/brochures.zip) desde `https://github.com/MicrosoftLearning/mslearn-ai-studio/raw/main/data/brochures.zip` y extráelo en una carpeta denominada **folletos** en tu sistema de archivos local.
1. En el Portal de la Fundición de IA de Azure, en tu proyecto, en el panel de navegación de la izquierda, en **Mis recursos**, selecciona la página **Datos e índices**.
1. Selecciona **+Nuevos datos**.
1. En el asistente **Agregar datos**, expande el menú desplegable para seleccionar **Cargar archivos o carpetas**.
1. Selecciona **Cargar carpeta** y selecciona la carpeta **folletos**. Espera hasta que se muestren todos los archivos de la carpeta.
1. Selecciona **Siguiente** y establece el nombre de los datos en `brochures`.
1. Espera a que se cargue la carpeta y observa que contiene varios archivos .pdf.

## Creación de un índice para los datos

Ahora que has agregado un origen de datos al proyecto, puedes usarlo para crear un índice en el recurso de Búsqueda de Azure AI.

1. En el Portal de la Fundición de IA de Azure, en tu proyecto, en el panel de navegación de la izquierda, en **Mis recursos**, selecciona la página **Datos e índices**.
1. En la pestaña **Índices**, agrega un nuevo índice con la siguiente configuración:
    - **Ubicación de origen**:
        - **Origen de datos**: datos en la Fundición de IA de Azure
            - *Selecciona el origen de datos **folletos***
    - **Configuración de índice**:
        - **Seleccionar servicio de Búsqueda de Azure AI**: *crea un nuevo recurso de Búsqueda de Azure AI con la siguiente configuración*:
            - **Suscripción**: *suscripción a Azure*
            - **Grupo de recursos**: *el mismo grupo de recursos que el del Centro de IA*.
            - **Nombre del servicio**: *un nombre válido para el recurso de búsqueda de IA*
            - **Ubicación**: *la misma ubicación que la del Centro de IA*
            - **Plan de tarifa**: básico
            
            Espera a que se cree el recurso de Búsqueda de IA. A continuación, vuelve a Fundición de IA de Azure y termina de configurar el índice seleccionando **Conectar otro recurso de Búsqueda de Azure AI** y agregando una conexión al recurso de Búsqueda de IA que acabas de crear.
 
        - **Índice vectorial**: `brochures-index`
        - **Máquina virtual**: selección automática
    - **Configuración de búsqueda**:
        - **Configuración de vectores**: agregación de una búsqueda vectorial a este recurso de búsqueda
        - **Conexión de Azure OpenAI**: *selecciona el recurso predeterminado de Azure OpenAI para tu centro.*
        - **Modelo de inserción**: text-embedding-ada-002
        - **Implementación de modelo de inserción **: *la implementación del* modelo *modelo* text-embeding-ada-002

1. Crea el índice vectorial y espera a que se complete el proceso de indexación, lo que puede tardar un tiempo en función de los recursos de proceso disponibles en la suscripción.

    La operación de creación de índices consta de los siguientes trabajos:

    - Descifra, fragmenta e inserta los tokens de texto en los datos de los folletos.
    - Crea el índice de Búsqueda de Azure AI.
    - Registra el recurso de índice.

    > **Sugerencia**: mientras esperas a que se cree el índice, ¿por qué no echas un vistazo a los folletos que descargaste para familiarizarte con su contenido?

## Prueba del índice en el área de juegos

Antes de usar el índice en un flujo de avisos basado en RAG, vamos a comprobar que se puede usar para afectar a las respuestas de IA generativa.

1. En el panel de navegación de la izquierda, selecciona la página **Área de juegos** y abre el área de juegos **Chat**.
1. En la página de área de juegos Chat, en el panel Configuración, asegúrate de que la implementación de modelo **gpt-4o** esté seleccionada. Después, en el panel Sesión de chat, envía el mensaje `Where can I stay in New York?`.
1. Revisa la respuesta, que debe ser una respuesta genérica del modelo sin datos procedentes del índice.
1. En el panel Configuración, expande el campo **Agregue sus datos** y después agrega el índice del proyecto **brochures-index** y selecciona el tipo de búsqueda **híbrido (vector + palabra clave)**.

   > **Sugerencia**: en algunos casos, es posible que los índices recién creados no estén disponibles inmediatamente. La actualización del explorador suele ser útil, pero si sigues experimentando el problema por el que no encuentras el índice, es posible que tengas que esperar hasta que se reconozca el índice.

1. Después de agregar el índice y de reiniciar la sesión de chat, vuelve a enviar el mensaje `Where can I stay in New York?`.
1. Revisa la respuesta, que debe basarse en los datos del índice.

<!-- DEPRECATED STEPS

## Create a RAG client app with the Azure AI Foundry and Azure OpenAI SDKs

Now that you have a working index, you can use the Azure AI Foundry and Azure OpenAI SDKs to implement the RAG pattern in a client application. Let's explore the code to accomplish this in a simple example.

> **Tip**: You can choose to develop your RAG solution using Python or Microsoft C#. Follow the instructions in the appropriate section for your chosen language.

### Prepare the application configuration

1. In the Azure AI Foundry portal, view the **Overview** page for your project.
1. In the **Project details** area, note the **Project connection string**. You'll use this connection string to connect to your project in a client application.
1. Return to the browser tab containing the Azure portal (keeping the Azure AI Foundry portal open in the existing tab).
1. Use the **[\>_]** button to the right of the search bar at the top of the page to create a new Cloud Shell in the Azure portal, selecting a ***PowerShell*** environment with no storage in your subscription.

    The cloud shell provides a command-line interface in a pane at the bottom of the Azure portal. You can resize or maximize this pane to make it easier to work in.

    > **Note**: If you have previously created a cloud shell that uses a *Bash* environment, switch it to ***PowerShell***.

1. In the cloud shell toolbar, in the **Settings** menu, select **Go to Classic version** (this is required to use the code editor).

    **<font color="red">Ensure you've switched to the classic version of the cloud shell before continuing.</font>**

1. In the cloud shell pane, enter the following commands to clone the GitHub repo containing the code files for this exercise (type the command, or copy it to the clipboard and then right-click in the command line and paste as plain text):

    ```
    rm -r mslearn-ai-foundry -f
    git clone https://github.com/microsoftlearning/mslearn-ai-studio mslearn-ai-foundry
    ```

    > **Tip**: As you paste commands into the cloudshell, the output may take up a large amount of the screen buffer. You can clear the screen by entering the `cls` command to make it easier to focus on each task.

1. After the repo has been cloned, navigate to the folder containing the chat application code files:

    > **Note**: Follow the steps for your chosen programming language.

    **Python**

    ```
   cd mslearn-ai-foundry/labfiles/rag-app/python
    ```

    **C#**

    ```
   cd mslearn-ai-foundry/labfiles/rag-app/c-sharp
    ```

1. In the cloud shell command-line pane, enter the following command to install the libraries you'll use:

    **Python**

    ```
   python -m venv labenv
   ./labenv/bin/Activate.ps1
   pip install python-dotenv azure-ai-projects azure-identity openai
    ```

    **C#**

    ```
   dotnet add package Azure.Identity
   dotnet add package Azure.AI.Projects --prerelease
   dotnet add package Azure.AI.OpenAI --prerelease
    ```
    

1. Enter the following command to edit the configuration file that has been provided:

    **Python**

    ```
   code .env
    ```

    **C#**

    ```
   code appsettings.json
    ```

    The file is opened in a code editor.

1. In the code file, replace the following placeholders: 
    - **your_project_connection_string**: Replace with the connection string for your project (copied from the project **Overview** page in the Azure AI Foundry portal).
    - **your_gpt_model_deployment** Replace with the name you assigned to your **gpt-4o** model deployment.
    - **your_embedding_model_deployment**: Replace with the name you assigned to your **text-embedding-ada-002** model deployment.
    - **your_index**: Replace with your index name (which should be `brochures-index`).
1. After you've replaced the placeholders, in the code editor, use the **CTRL+S** command or **Right-click > Save** to save your changes and then use the **CTRL+Q** command or **Right-click > Quit** to close the code editor while keeping the cloud shell command line open.

### Explore code to implement the RAG pattern

1. Enter the following command to edit the code file that has been provided:

    **Python**

    ```
   code rag-app.py
    ```

    **C#**

    ```
   code Program.cs
    ```

1. Review the code in the file, noting that it:
    - Uses the Azure AI Foundry SDK to connect to your project (using the project connection string)
    - Creates an authenticated Azure OpenAI client from your project connection.
    - Retrieves the default Azure AI Search connection from your project so it can determine the endpoint and key for your Azure AI Search service.
    - Creates a suitable system message.
    - Submits a prompt (including the system and a user message based on the user input) to the Azure OpenAI client, adding:
        - Connection details for the Azure AI Search index to be queried.
        - Details of the embedding model to be used to vectorize the query\*.
    - Displays the response from the grounded prompt.
    - Adds the response to the chat history.

    \* *The query for the search index is based on the prompt, and is used to find relevant text in the indexed documents. You can use a keyword-based search that submits the query as text, but using a vector-based search can be more efficient - hence the use of an embedding model to vectorize the query text before submitting it.*

1. Use the **CTRL+Q** command to close the code editor without saving any changes, while keeping the cloud shell command line open.

### Run the chat application

1. In the cloud shell command-line pane, enter the following command to run the app:

    **Python**

    ```
   python rag-app.py
    ```

    **C#**

    ```
   dotnet run
    ```

1. When prompted, enter a question, such as `Where should I go on vacation to see architecture?` and review the response from your generative AI model.

    Note that the response includes source references to indicate the indexed data in which the answer was found.

1. Try a follow-up question, for example `Where can I stay there?`

1. When you're finished, enter `quit` to exit the program. Then close the cloud shell pane.

-->

## Limpieza

Para evitar costes innecesarios de Azure y uso de recursos, debe quitar los recursos que implementó en este ejercicio.

1. Si has terminado de explorar Azure AI Foundry, vuelve a [Azure Portal](https://portal.azure.com) en `https://portal.azure.com` e inicia sesión con tus credenciales de Azure si es necesario. A continuación, elimine los recursos del grupo de recursos donde aprovisionó los recursos de Búsqueda de Azure AI y Azure AI.

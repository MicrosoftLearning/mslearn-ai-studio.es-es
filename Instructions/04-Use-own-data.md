---
lab:
  title: Creación de una aplicación de IA generativa que use tus propios datos
  description: Obtén información sobre cómo usar el modelo de generación aumentada de recuperación (RAG) para crear una aplicación de chat que solicite el uso de tus propios datos.
---

# Creación de una aplicación de IA generativa que use tus propios datos

La generación aumentada de recuperación (RAG) es una técnica que se usa para compilar aplicaciones que integran datos de orígenes de datos personalizados en un aviso de un modelo de IA generativa. La RAG es un patrón que se usa habitualmente para desarrollar aplicaciones de IA generativa: aplicaciones basadas en chat que usan un modelo de lenguaje para interpretar entradas y generar respuestas adecuadas.

En este ejercicio, usarás el portal de Azure AI Foundry para integrar datos personalizados en un flujo de consultas de IA generativa. También explorarás cómo implementar el patrón RAG en una aplicación cliente mediante los SDK de Fundición de IA de Azure y Azure OpenAI.

Este ejercicio dura aproximadamente **45** minutos.

## Creación de un proyecto de Azure AI Foundry

Empecemos creando un proyecto de Azure AI Foundry y los recursos de servicio que necesita para ser compatible el uso de tus propios datos, incuyendo un recurso de Búsqueda de Azure AI.

1. En un explorador web, abre el [Portal de la Fundición de IA de Azure](https://ai.azure.com) en `https://ai.azure.com` e inicia sesión con tus credenciales de Azure. Cierra las sugerencias o paneles de inicio rápido que se abran la primera vez que inicias sesión y, si es necesario, usa el logotipo de **Fundición de IA de Azure** en la parte superior izquierda para navegar a la página principal, que es similar a la siguiente imagen:

    ![Captura de pantalla del Portal de la Fundición de IA de Azure.](./media/ai-foundry-home.png)

1. En la página principal, selecciona **+Crear proyecto**.
1. En el Asistente para **crear un proyecto**, escribe un nombre de proyecto adecuado (como `my-ai-project`) y revisa los recursos de Azure que se crearán automáticamente para admitir el proyecto.
1. Selecciona **Personalizar** y especifica la siguiente configuración para el centro:
    - **Nombre del centro**: *un nombre único; por ejemplo, `my-ai-hub`*
    - **Suscripción**: *suscripción a Azure*
    - **Grupo de recursos**: *crea un nuevo grupo de recursos con un nombre único (como `my-ai-resources`) o selecciona uno existente*
    - **Ubicación**: selecciona **Ayúdame a elegir** y después selecciona **gpt-4** como **text-embedding-ada-002** en la ventana Asistente de ubicación y usa la región recomendada\*
    - **Conectar Servicios de Azure AI o Azure OpenAI**: *crea un nuevo recurso de AI Services con un nombre adecuado (como `my-ai-services`) o usa uno existente.*
    - **Conexión de Búsqueda de Azure AI**: *creación de un nuevo recurso de Búsqueda de Azure AI con un nombre único*

    > \* Los recursos de Azure OpenAI están restringidos en el nivel de inquilino por cuotas regionales. En caso de que se alcance un límite de cuota más adelante en el ejercicio, es posible que tengas que crear otro recurso en otra región.

1. Selecciona **Siguiente** y revisa tu configuración. Luego, selecciona **Crear** y espera a que se complete el proceso.
1. Cuando se cree el proyecto, cierra las sugerencias que se muestren y revisa la página **Información general** del proyecto en el Portal de Azure AI Foundry, que debe tener un aspecto similar a la siguiente imagen:

    ![Captura de pantalla de los detalles de un proyecto de Azure AI en el Portal de la Fundición de IA de Azure.](./media/ai-foundry-project.png)
   
## Implementación de modelos

Necesitas dos modelos para implementar la solución:

- Un modelo de *inserción* para vectorizar datos de texto para una indexación y procesamiento eficaces.
- Modelo que puede generar respuestas de lenguaje natural a preguntas a partir de sus datos.

1. En el portal de Azure AI Foundry, en tu proyecto, en el panel de navegación de la izquierda, en **Mis recursos**, selecciona la página **Modelos + puntos de conexión**.
1. Crea una nueva implementación del modelo **text-embedding-ada-002** con la siguiente configuración mediante la selección de **Personalizar** en el asistente para Implementar modelo:

    - **Nombre de implementación**:`text-embedding-ada-002`
    - **Tipo de implementación**: estándar
    - **Versión del modelo**: *selecciona la versión predeterminada*
    - **Recurso de IA**: *selecciona el recurso creado anteriormente*
    - **Límite de frecuencia de tokens por minuto (miles)**: 5000
    - **Filtro de contenido**: DefaultV2
    - **Habilitación de la cuota dinámica**: deshabilitada

    > **Nota**: si la ubicación actual del recurso de IA no tiene cuota disponible para el modelo que deseas implementar, se te pedirá que elijas otra ubicación donde se creará un nuevo recurso de IA y se conectará al proyecto.

1. Repite los pasos anteriores para implementar un modelo de **gpt-4** con el nombre de implementación `gpt-4`.

    > **Nota**: reducir los tokens por minuto (TPM) ayuda a evitar el uso excesivo de la cuota disponible en la suscripción que está usando. 5000 TPM es suficiente para los datos que se usan en este ejercicio.

## Adición de datos al proyecto

Los datos del copiloto constan de un conjunto de folletos de viaje en formato PDF de la agencia de viajes ficticia *Margie's Travel*. Vamos a agregarlos al proyecto.

1. Descarga el [archivo comprimido de los folletos](https://github.com/MicrosoftLearning/mslearn-ai-studio/raw/main/data/brochures.zip) desde `https://github.com/MicrosoftLearning/mslearn-ai-studio/raw/main/data/brochures.zip` y extráelo en una carpeta denominada **folletos** en tu sistema de archivos local.
1. En el portal de Azure AI Foundry, en tu proyecto, en el panel de navegación de la izquierda, en **Mis recursos**, selecciona la página **Datos + índices**.
1. Selecciona **+Nuevos datos**.
1. En el asistente **Agregar datos**, expande el menú desplegable para seleccionar **Cargar archivos o carpetas**.
1. Selecciona **Cargar carpeta** y selecciona la carpeta **folletos**.
1. Selecciona **Siguiente** y establece el nombre de los datos en `brochures`.
1. Espera a que se cargue la carpeta y observa que contiene varios archivos .pdf.

## Creación de un índice para los datos

Ahora que has agregado un origen de datos al proyecto, puedes usarlo para crear un índice en el recurso de Azure AI Search.

1. En el portal de Azure AI Foundry, en tu proyecto, en el panel de navegación de la izquierda, en **Mis recursos**, selecciona la página **Datos + índices**.
1. En la pestaña **Índices**, agrega un nuevo índice con la siguiente configuración:
    - **Ubicación de origen**:
        - **Origen de los datos**: datos del portal de Azure AI Foundry
            - *Selecciona el origen de datos **folletos***
    - **Configuración de índice**:
        - **Selecciona el servicio Azure AI Search**: *Selecciona la conexión de **AzureAISearch** al recurso de Azure AI Search*
        - **Índice vectorial**: `brochures-index`
        - **Máquina virtual**: selección automática
    - **Configuración de búsqueda**:
        - **Configuración de vectores**: agregación de una búsqueda vectorial a este recurso de búsqueda
        - **Conexión de Azure OpenAI**: *selecciona el recurso predeterminado de Azure OpenAI para tu centro.*

1. Espera a que se complete el proceso de indexación, lo cual puede tardar varios minutos. La operación de creación de índices consta de los siguientes trabajos:

    - Descifra, fragmenta e inserta los tokens de texto en los datos de los folletos.
    - Crea el índice de Búsqueda de Azure AI.
    - Registra el recurso de índice.

## Prueba del índice

Antes de usar el índice en un flujo de avisos basado en RAG, vamos a comprobar que se puede usar para afectar a las respuestas de IA generativa.

1. En el panel de navegación de la izquierda, selecciona la página **Área de juegos**.
1. En la página Chat, en el panel Configuración, asegúrate de que la implementación de modelo **gpt-4** esté seleccionada. Después, en el panel Sesión de chat, envía el mensaje `Where can I stay in New York?`.
1. Revisa la respuesta, que debe ser una respuesta genérica del modelo sin datos procedentes del índice.
1. En el panel Configuración, expande el campo **Agregue sus datos** y después agrega el índice del proyecto **brochures-index** y selecciona el tipo de búsqueda **híbrido (vector + palabra clave)**.

   > **Nota**: algunos usuarios se encuentran que los índices recién creados no están disponibles inmediatamente. La actualización del explorador suele ser útil, pero si sigues experimentando el problema por el que no encuentras el índice, es posible que tengas que esperar hasta que se reconozca el índice.

1. Después de agregar el índice y de reiniciar la sesión de chat, vuelve a enviar el mensaje `Where can I stay in New York?`.
1. Revisa la respuesta, que debe basarse en los datos del índice.

## Uso del índice en un flujo de avisos

Tu índice vectorial se ha guardado en tu proyecto de Azure AI Foundry, lo que te permite usarlo fácilmente en un flujo de solicitudes.

1. En el portal de Azure AI Foundry, en tu proyecto, en el panel de navegación de la izquierda, en **Crear y personalizar**, selecciona la página **Flujo de solicitudes**.
1. Cree un nuevo flujo de avisos mediante la clonación del ejemplo de **Multi-Round Q&A on Your Data** de la galería. Guarde el clon de este ejemplo en una carpeta denominada `brochure-flow`.

    <details> 
        <summary><font color="red"><b>Sugerencia de solución de problemas</b>: error de permisos</font></summary>
        <p>Si recibes un error de permisos al crear un nuevo flujo de avisos, prueba lo siguiente para solucionar los problemas:</p>
        <ul>
            <li>En Azure Portal, en el grupo de recursos del centro de Fundición de IA de Azure, selecciona el recurso de Servicios de IA.</li>
            <li>En <b>Administración de recursos</b>, en la pestaña <b>Identidad</b>, confirma que el estado de la identidad administrada <b>asignada por el sistema</b> se establece en <b>Activado</b>.</li>
            <li>En el grupo de recursos del centro de Fundición de IA de Azure, selecciona la cuenta de almacenamiento</li>
            <li>En la página <b>Control de acceso (IAM),</b> agrega una asignación de roles para asignar el rol <b>lector de datos de Storage Blob</b> a la identidad administrada para el recurso de servicios de Azure AI.</li>
            <li>Espera a que se asigne el rol y vuelve a intentar realizar el paso anterior.</li>
        </ul>
    </details>

1. Cuando se abra la página del diseñador de flujos de avisos, revise **brochure-flow**. Su grafo debe ser similar a la imagen siguiente:

    ![Captura de pantalla de un grafo de flujo de avisos](./media/chat-flow.png)

    El flujo de avisos de ejemplo que usa implementa la lógica de avisos de una aplicación de chat en la que el usuario puede enviar de forma iterativa la entrada de texto a la interfaz del chat. El historial de conversaciones se conserva y se incluye en el contexto de cada iteración. El flujo de avisos organiza una secuencia de *herramientas* para:

    - Anexe el historial a la entrada de chat para definir un aviso como forma contextualizada de una pregunta.
    - Recupere el contexto mediante el índice y un tipo de consulta de su propia elección en función de la pregunta.
    - Genere el contexto del aviso mediante el uso de los datos recuperados del índice para aumentar la pregunta.
    - Cree variantes de aviso agregando un mensaje del sistema y estructurando el historial de chats.
    - Envíe el aviso a un modelo de lenguaje para generar una respuesta de lenguaje natural.

1. Use el botón **Iniciar sesión de proceso** para iniciar el proceso del entorno de ejecución para el flujo.

    Espere a que se inicie la sesión de proceso. Esto proporciona un contexto de proceso para el flujo de avisos. Mientras espera, en la pestaña **Flujo**, revise las secciones de las herramientas del flujo.

    >**Nota**: Debido a las limitaciones de infraestructura y capacidad, es posible que la sesión de proceso no se inicie durante períodos de alta demanda. Si esto sucede, puedes omitir el flujo de avisos e iniciar la tarea **Creación de una aplicación cliente RAG con los SDK de Fundición de IA de Azure y Azure OpenAI**.

    A continuación, cuando se haya iniciado la sesión de proceso...

1. En la sección **Entradas**, asegúrese de que las entradas incluyen:
    - **chat_history**
    - **chat_input**

    El historial de chat predeterminado de este ejemplo incluye alguna conversación sobre IA.

1. En la sección **Salidas**, asegúrese de que la salida incluye:

    - **chat_output** con valor ${chat_with_context.output}

1. En la sección **modify_query_with_history**, seleccione la siguiente configuración (dejando las demás como están):

    - **Conexión**: *El recurso predeterminado de Azure OpenAI para el centro de IA*
    - **Api**: chat
    - **deployment_name**: gpt-4
    - **response_format**: {"type":"text"}

1. En la sección **lookup**, establezca los siguientes valores de parámetro:

    - **mlindex_content**: *Seleccione el campo vacío para abrir el panel Generar*
        - **index_type**: Índice registrado
        - **mlindex_asset_id**: brochures-index:1
    - **queries**: ${modify_query_with_history.output}
    - **query_type**: Híbrido (vector + palabra clave)
    - **top_k**: 2

1. En la sección **generate_prompt_context**, revise el script de Python y asegúrese de que las **entradas** de esta herramienta incluyan el parámetro siguiente:

    - **search_result** *(objeto)*: ${lookup.output}

1. En la sección **Prompt_variants**, revise el script de Python y asegúrese de que las **entradas** de esta herramienta incluyan los parámetros siguientes:

    - **contexts** *(string)*: ${generate_prompt_context.output}
    - **chat_history** *(string)*: ${inputs.chat_history}
    - **chat_input** *(string)*: ${inputs.chat_input}

1. En la sección **chat_with_context**, seleccione la siguiente configuración (dejando las demás como están):

    - **Conexión**: *El recurso predeterminado de Azure OpenAI para el centro de IA*
    - **API**: Chat
    - **deployment_name**: gpt-4
    - **response_format**: {"type":"text"}

    A continuación, asegúrese de que las **entradas** de esta herramienta incluyan los parámetros siguientes:
    - **prompt_text** *(string)*: ${Prompt_variants.output}

1. En la barra de herramientas, use el botón **Guardar** para guardar los cambios realizados en las herramientas del flujo de avisos.
1. En la barra de herramientas, seleccione **Chat**. Se abrirá un panel de chat con el historial de conversaciones de ejemplo y la entrada ya rellenada en función de los valores de ejemplo. Puede pasarlos por alto.
1. En el panel del chat, reemplace la entrada predeterminada por la pregunta `Where can I stay in London?` y envíela.
1. Revise la respuesta, que debe basarse en los datos del índice.
1. Revise las salidas de cada herramienta del flujo.
1. En el panel del chat, escriba la pregunta `What can I do there?`.
1. Revise la respuesta, que debe basarse en los datos del índice y debe tener en cuenta el historial del chat (por lo que "allí" se entiende como "en Londres").
1. Revise las salidas de cada herramienta del flujo, teniendo en cuenta cómo operaba cada herramienta del flujo en sus entradas para preparar un mensaje contextualizado y obtener una respuesta adecuada.

    Ahora tienes un flujo de avisos funcionando que usa tu índice de Búsqueda de Azure AI para implementar el patrón RAG. Para más información sobre cómo implementar y consumir el flujo de avisos, consulta la [documentación de Fundición de IA de Azure](https://learn.microsoft.com/azure/ai-foundry/how-to/flow-deploy).

## Creación de una aplicación cliente RAG con los SDK de Fundición de IA de Azure y Azure OpenAI

Aunque un flujo de avisos es una excelente manera de encapsular el modelo y los datos en una aplicación basada en RAG, también puedes usar los SDK de Fundición de IA de Azure y Azure OpenAI para implementar el patrón RAG en una aplicación cliente. Vamos a explorar el código para hacerlo en un ejemplo sencillo.

> **Sugerencia**: puedes elegir desarrollar tu solución RAG mediante C# de Python o Microsoft. Sigue las instrucciones de la sección adecuada para el idioma elegido.

### Preparación de la configuración de aplicación

1. En el Portal de la Fundición de IA de Azure, mira la página **Información general** del proyecto.
1. En el área **Detalles del proyecto**, anota la **Cadena de conexión del proyecto**. Usarás esta cadena de conexión para conectarte al proyecto en una aplicación cliente.
1. Abre una nueva pestaña del explorador (mantén el Portal de la Fundición de IA de Azure abierto en la pestaña existente). En la nueva pestaña, explora [Azure Portal](https://portal.azure.com) en `https://portal.azure.com` e inicia sesión con tus credenciales de Azure, si se te solicita.
1. Usa el botón **[\>_]** situado a la derecha de la barra de búsqueda en la parte superior de la página para crear una nueva instancia de Cloud Shell en Azure Portal, para lo que deberás seleccionar un entorno de ***PowerShell***. Cloud Shell proporciona una interfaz de la línea de comandos en un panel situado en la parte inferior de Azure Portal.

    > **Nota**: si has creado anteriormente una instancia de Cloud Shell que usa un entorno de *Bash*, cámbiala a ***PowerShell***.

1. En la barra de herramientas de Cloud Shell, en el menú **Configuración**, selecciona **Ir a la versión clásica** (esto es necesario para usar el editor de código).

    > **Sugerencia**: al pegar comandos en CloudShell, la salida puede ocupar una gran cantidad del búfer de pantalla. Puedes despejar la pantalla al escribir el comando `cls` para que te resulte más fácil centrarte en cada tarea.

1. En el panel de PowerShell, escribe los siguientes comandos para clonar el repo de GitHub para este ejercicio:

    ```
    rm -r mslearn-ai-foundry -f
    git clone https://github.com/microsoftlearning/mslearn-ai-studio mslearn-ai-foundry
    ```

> **Nota**: sigue los pasos del lenguaje de programación elegido.

1. Una vez clonado el repo, ve a la carpeta que contiene los archivos de código de la aplicación de chat:  

    **Python**

    ```
   cd mslearn-ai-foundry/labfiles/rag-app/python
    ```

    **C#**

    ```
   cd mslearn-ai-foundry/labfiles/rag-app/c-sharp
    ```

1. En el panel de la línea de comandos de Cloud Shell, escribe el siguiente comando para instalar las bibliotecas que vas a usar:

    **Python**

    ```
   pip install python-dotenv azure-ai-projects azure-identity openai
    ```

    **C#**

    ```
   dotnet add package Azure.Identity
   dotnet add package Azure.AI.Projects --prerelease
   dotnet add package Azure.AI.OpenAI --prerelease
    ```
    

1. Escribe el siguiente comando para editar el archivo de configuración que se ha proporcionado:

    **Python**

    ```
   code .env
    ```

    **C#**

    ```
   code appsettings.json
    ```

    El archivo se abre en un editor de código.

1. En el código siguiente, reemplaza estos marcadores de posición: 
    - **** your_project_endpoint: reemplázalo por la cadena de conexión del proyecto (copiado de la página **Información general** del proyecto en el portal de la Fundición de IA de Azure).
    - **your_model_deployment**: reemplázala por el nombre que asignaste a la implementación del modelo (que debe ser `gpt-4`)
    - **your_index**: reemplázala por el nombre del índice (que debe ser `brochures-index`)
1. Después de reemplazar los marcadores de posición, usa el comando **CTRL+S** para guardar los cambios y, a continuación, usa el comando **CTRL+Q** para cerrar el editor de código mientras mantienes abierta la línea de comandos de Cloud Shell.

### Exploración del código para implementar el patrón RAG

1. Escribe el siguiente comando para editar el archivo de código que se ha proporcionado:

    **Python**

    ```
   code rag-app.py
    ```

    **C#**

    ```
   code Program.cs
    ```

1. Revisa el código del archivo, teniendo en cuenta que:
    - Usa el SDK de la Fundición de IA de Azure para conectarse al proyecto (mediante el cadena de conexión del proyecto)
    - Recupera la conexión predeterminada de la Búsqueda de Azure AI del proyecto para que pueda determinar el punto de conexión y la clave del servicio de la Búsqueda de Azure AI.
    - Crea un cliente de Azure OpenAI autenticado basado en la conexión predeterminada de Azure OpenAI Service en el proyecto.
    - Envía un aviso (incluido un sistema y un mensaje de usuario) al cliente de Azure OpenAI y agrega información adicional sobre el índice de Azure AI Search que se usará para fundamentar el aviso.
    - Muestra la respuesta desde el símbolo del sistema en la base.
1. Usa el comando **CTRL+Q** para cerrar el editor de código, sin guardar ningún cambio, mientras mantienes abierta la línea de comandos de Cloud Shell.

### Ejecución de la aplicación de chat

1. En el panel de línea de comandos de Cloud Shell, escribe el siguiente comando para ejecutar la aplicación:

    **Python**

    ```
   python rag-app.py
    ```

    **C#**

    ```
   dotnet run
    ```

1. Cuando se te solicite, escribe una pregunta, como `Where can I travel to?` y revisa la respuesta del modelo de IA generativa.

    Ten en cuenta que la respuesta incluye referencias de origen para indicar los datos indexados en los que se encontró la respuesta.

1. Prueba con algunas preguntas más, por ejemplo `Where should I stay in London?`

    > **Nota**: Esta sencilla aplicación de ejemplo no incluye ninguna lógica para conservar el historial de conversaciones, por lo que cada solicitud se trata como una nueva conversación.

1. Cuando hayas terminado, presiona `quit` para salir del programa. A continuación, cierra el panel de Cloud Shell.

## Desafío

Ahora que has experimentado cómo integrar tus propios datos en una aplicación de Ia generativa creada con el portal de Azure AI Foundry, vamos a explorar más.

Prueba a agregar un nuevo origen de datos a través del portal de Azure AI Foundry, indéxalo e integra los datos indexados en un flujo de solicitudes. Algunos conjuntos de datos que podría probar son:

- Una colección de artículos (de investigación) que tiene en su ordenador.
- Un conjunto de presentaciones de conferencias anteriores.
- Cualquiera de los conjuntos de datos disponibles en el repositorio de [datos de ejemplo de Azure Search](https://github.com/Azure-Samples/azure-search-sample-data).

Usa todos los recursos que puedas para crear el origen de datos e integrarlo en el flujo de avisos o la aplicación cliente. Prueba la solución mediante el envío de avisos que solo podrían ser respondidos por el conjunto de datos que elegiste.

## Limpieza

Para evitar costos innecesarios de Azure y uso de recursos, debe quitar los recursos que implementó en este ejercicio.

1. Si has terminado de explorar Azure AI Foundry, vuelve a [Azure Portal](https://portal.azure.com) en `https://portal.azure.com` e inicia sesión con tus credenciales de Azure si es necesario. A continuación, elimine los recursos del grupo de recursos donde aprovisionó los recursos de Búsqueda de Azure AI y Azure AI.

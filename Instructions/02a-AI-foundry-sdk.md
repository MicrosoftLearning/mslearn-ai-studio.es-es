---
lab:
  title: Creación de una aplicación de chat de IA generativa
  description: Obtén información sobre cómo usar el SDK de la Fundición de IA de Azure para crear una aplicación que se conecte al proyecto y chats con un modelo de lenguaje.
---

# Creación de una aplicación de chat de IA generativa

En este ejercicio, usarás el SDK de la Fundición de IA de Azure para crear una aplicación de chat sencilla que se conecte a un proyecto y chats con un modelo de lenguaje.

Este ejercicio dura aproximadamente **40** minutos.

> **Nota**: este ejercicio se basa en los SDK de la versión preliminar, que podrían cambiar. Cuando ha sido necesario, hemos usado versiones específicas de paquetes; que pueden no ser las versiones disponibles más recientes.

## Creación de un proyecto de Azure AI Foundry

Comencemos creando un proyecto de Fundición de IA de Azure.

1. En un explorador web, abre el [Portal de la Fundición de IA de Azure](https://ai.azure.com) en `https://ai.azure.com` e inicia sesión con tus credenciales de Azure. Cierra las sugerencias o paneles de inicio rápido que se abran la primera vez que inicias sesión y, si es necesario, usa el logotipo de **Fundición de IA de Azure** en la parte superior izquierda para navegar a la página principal, que es similar a la siguiente imagen:

    ![Captura de pantalla del Portal de la Fundición de IA de Azure.](./media/ai-foundry-home.png)

1. En la página principal, selecciona **+Crear proyecto**.
1. En el asistente para **crear un proyecto**, escribe un nombre de proyecto adecuado (por ejemplo, `my-ai-project`) y si se te sugiere un centro existente, elige la opción para crear uno nuevo. A continuación, revisa los recursos de Azure que se crearán automáticamente para admitir el centro y el proyecto.
1. Selecciona **Personalizar** y especifica la siguiente configuración para el centro:
    - **Nombre del centro**: *un nombre único; por ejemplo, `my-ai-hub`*
    - **Suscripción**: *suscripción a Azure*
    - **Grupo de recursos**: *crea un nuevo grupo de recursos con un nombre único (como `my-ai-resources`) o selecciona uno existente*
    - **Ubicación**: selecciona **Ayudarme a elegir** y, a continuación, selecciona **gpt-4** en la ventana Asistente de ubicación y usa la región recomendada\*
    - **Conectar Servicios de Azure AI o Azure OpenAI**: *crea un nuevo recurso de AI Services con un nombre adecuado (como `my-ai-services`) o usa uno existente.*
    - **Conectar Búsqueda de Azure AI**: omite la conexión

    > \* Las cuotas de modelos están restringidas a nivel de inquilino por cuotas regionales. En caso de que se alcance un límite de cuota más adelante en el ejercicio, es posible que tengas que crear otro recurso en otra región.

1. Selecciona **Siguiente** y revisa tu configuración. Luego, selecciona **Crear** y espera a que se complete el proceso.
1. Cuando se cree el proyecto, cierra las sugerencias que se muestran y revisa la página del proyecto en el Portal de la Fundición de IA de Azure, que debe tener un aspecto similar a la siguiente imagen:

    ![Captura de pantalla de los detalles de un proyecto de Azure AI en el Portal de la Fundición de IA de Azure.](./media/ai-foundry-project.png)

## Implementación de un modelo de IA generativa

Ahora ya puedes implementar un modelo de lenguaje de IA generativa compatible con tu aplicación de chat. En este ejemplo, usarás el modelo GPT-4 de OpenAI; pero los principios son los mismos para cualquier modelo.

1. En la barra de herramientas de la parte superior derecha de la página del proyecto de Fundición de IA de Azure, usa el icono **Características de versión preliminar** para habilitar la característica **Implementar modelos en el servicio de inferencia de modelos de Azure AI**. Esta característica garantiza que la implementación del modelo esté disponible para el servicio de inferencia de Azure AI, que usarás en el código de aplicación.
1. En el panel de la izquierda de tu proyecto, en la sección **Mis recursos**, selecciona la página **Modelos y puntos de conexión**.
1. En la página **Modelos y puntos de conexión**, en la pestaña **Implementaciones de modelos**, en el menú **+ Implementar modelo**, selecciona **Implementar modelo base**.
1. Busca el modelo **gpt-4** en la lista, selecciona y confirma.
1. Implementa el modelo con la siguiente configuración mediante la selección de **Personalizar** en los detalles de implementación:
    - **Nombre de implementación**: *nombre único para la implementación del modelo, por ejemplo `gpt-4` (recuerda el nombre que elijas, lo necesitarás más adelante*).
    - **Tipo de implementación**: estándar
    - **Versión del modelo**: 0613
    - **Recurso de IA conectado**: *selecciona tu conexión de recursos de Azure OpenAI*
    - **Límite de frecuencia de tokens por minuto (miles)**: 5000
    - **Filtro de contenido**: DefaultV2
    - **Habilitación de la cuota dinámica**: deshabilitada

    > **Nota**: reducir el TPM ayuda a evitar el uso excesivo de la cuota disponible en la suscripción que está usando. 5000 TPM es suficiente para los datos que se usan en este ejercicio.

1. Espera a que la implementación se complete.

## Creación de una aplicación cliente para chatear con el modelo

Ahora que has implementado un modelo, puedes usar los SDK de la Fundición de IA de Azure y la inferencia del modelo de Azure AI para desarrollar una aplicación que chatee con él.

> **Sugerencia**: puedes elegir desarrollar la solución mediante C# de Python o Microsoft. Sigue las instrucciones de la sección adecuada para el idioma elegido.

### Preparación de la configuración de aplicación

1. En el Portal de la Fundición de IA de Azure, mira la página **Información general** del proyecto.
1. En el área **Detalles del proyecto**, anota la **Cadena de conexión del proyecto**. Usarás esta cadena de conexión para conectarte al proyecto en una aplicación cliente.
1. Abre una nueva pestaña del explorador (mantén el Portal de la Fundición de IA de Azure abierto en la pestaña existente). En la nueva pestaña, explora [Azure Portal](https://portal.azure.com) en `https://portal.azure.com` e inicia sesión con tus credenciales de Azure, si se te solicita.

    Cierre las notificaciones de bienvenida para ver la página principal de Azure Portal.

1. Usa el botón **[\>_]** situado a la derecha de la barra de búsqueda en la parte superior de la página para crear una nueva instancia de Cloud Shell en Azure Portal, para lo que deberás seleccionar un entorno de ***PowerShell*** sin almacenamiento en tu suscripción.

    Cloud Shell proporciona una interfaz de la línea de comandos en un panel situado en la parte inferior de Azure Portal. Puedes cambiar el tamaño o maximizar este panel para facilitar el trabajo.

    > **Nota**: si has creado anteriormente una instancia de Cloud Shell que usa un entorno de *Bash*, cámbiala a ***PowerShell***.

1. En la barra de herramientas de Cloud Shell, en el menú **Configuración**, selecciona **Ir a la versión clásica** (esto es necesario para usar el editor de código).

    **<font color="red">Asegúrate de que has cambiado a la versión clásica de Cloud Shell antes de continuar.</font>**

1. En el panel de PowerShell, escribe los siguientes comandos para clonar el repo de GitHub que contiene archivos de código para este ejercicio:

    ```
    rm -r mslearn-ai-foundry -f
    git clone https://github.com/microsoftlearning/mslearn-ai-studio mslearn-ai-foundry
    ```

    > **Sugerencia**: al pegar comandos en CloudShell, la salida puede ocupar una gran cantidad del búfer de pantalla. Puedes despejar la pantalla al escribir el comando `cls` para que te resulte más fácil centrarte en cada tarea.

1. Una vez clonado el repo, ve a la carpeta que contiene los archivos de código de la aplicación de chat:

    Usa el comando siguiente en función del lenguaje de programación que elijas.

    **Python**

    ```
   cd mslearn-ai-foundry/labfiles/chat-app/python
    ```

    **C#**

    ```
   cd mslearn-ai-foundry/labfiles/chat-app/c-sharp
    ```

1. En el panel de la línea de comandos de Cloud Shell, escribe el siguiente comando para instalar las bibliotecas que vas a usar:

    **Python**

    ```
   pip install python-dotenv azure-identity azure-ai-projects azure-ai-inference
    ```

    **C#**

    ```
   dotnet add package Azure.Identity
   dotnet add package Azure.AI.Projects --version 1.0.0-beta.3
   dotnet add package Azure.AI.Inference --version 1.0.0-beta.3
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

1. En el archivo de código, reemplaza el marcador de posición **your_project_connection_string** por la cadena de conexión del proyecto (copiado de la página **Información general** del proyecto en el Portal de la Fundición de IA de Azure) y el marcador de posición **your_model_deployment** por el nombre que asignaste a tu implementación del modelo GPT-4.
1. Después de reemplazar los marcadores de posición, en el editor de código, usa el comando **CTRL+S** o usa la acción de **hacer clic con el botón derecho > Guardar** para guardar los cambios y, a continuación, usa el comando **CTRL+Q** o la acción de **hacer clic con el botón derecho > Salir** para cerrar el editor de código mientras mantienes abierta la línea de comandos de Cloud Shell.

### Escritura del código para conectarte al proyecto y chatear con el modelo

> **Sugerencia**: al agregar código, asegúrate de mantener la sangría correcta.

1. Escribe el siguiente comando para editar el archivo de código que se ha proporcionado:

    **Python**

    ```
   code chat-app.py
    ```

    **C#**

    ```
   code Program.cs
    ```

1. En el archivo de código, observa las instrucciones existentes que se han agregado en la parte superior del archivo para importar los espacios de nombres de SDK necesarios. Después, busca el comentario **Add references** y agrega el código siguiente para hacer referencia a los espacios de nombres de las bibliotecas que has instalado anteriormente:

    **Python**

    ```python
   # Add references
   from dotenv import load_dotenv
   from azure.identity import DefaultAzureCredential
   from azure.ai.projects import AIProjectClient
   from azure.ai.inference.models import SystemMessage, UserMessage, AssistantMessage
    ```

    **C#**

    ```csharp
   // Add references
   using Azure.Identity;
   using Azure.AI.Projects;
   using Azure.AI.Inference;
    ```

1. En la función **principal**, en el comentario **Obtener ajustes de configuración**, observa que el código carga los valores de la cadena de conexión del proyecto y del nombre de implementación del modelo que has definido en el archivo de configuración.
1. Busca el comentario **Initialize the project client** y agrega el siguiente código para conectarte a tu proyecto de Fundición de IA de Azure mediante las credenciales de Azure con las que has iniciado sesión:

    > **Sugerencia**: Ten cuidado de mantener el nivel de sangría correcto del código.

    **Python**

    ```python
   # Initialize the project client
   projectClient = AIProjectClient.from_connection_string(
        conn_str=project_connection,
        credential=DefaultAzureCredential())
    ```

    **C#**

    ```csharp
   // Initialize the project client
   var projectClient = new AIProjectClient(project_connection,
                        new DefaultAzureCredential());
    ```

1. Busca el comentario **Get a chat client** y agrega el siguiente código para crear un objeto de cliente para chatear con un modelo:

    **Python**

    ```python
   # Get a chat client
   chat = projectClient.inference.get_chat_completions_client()
    ```

    **C#**

    ```csharp
   // Get a chat client
   ChatCompletionsClient chat = projectClient.GetChatCompletionsClient();
    ```

    > **Nota**: este código usa el cliente del proyecto de Fundición de IA de Azure para crear una conexión segura al punto de conexión de servicio de inferencia del modelo de Azure AI predeterminado asociado al proyecto. También puedes conectarte *directamente* al punto de conexión mediante el SDK de la inferencia del modelo de Azure AI, especificando el URI del punto de conexión que se muestra para la conexión de servicio en el Portal de la Fundición de IA de Azure o en la página de recursos de Servicios de Azure AI correspondiente en Azure Portal, y mediante una clave de autenticación o un token de credencial de Entra. Para obtener más información sobre cómo conectarse al servicio de inferencia del modelo de Azure AI, consulta [API de inferencia de modelos de Azure AI](https://learn.microsoft.com/azure/machine-learning/reference-model-inference-api).

1. Busca el comentario **Initialize prompt with system message** y agrega el código siguiente para inicializar una colección de mensajes con una indicación del sistema.

    **Python**

    ```python
   # Initialize prompt with system message
   prompt=[
            SystemMessage("You are a helpful AI assistant that answers questions.")
        ]
    ```

    **C#**

    ```csharp
   // Initialize prompt with system message
   var prompt = new List<ChatRequestMessage>(){
                    new ChatRequestSystemMessage("You are a helpful AI assistant that answers questions.")
                };
    ```

1. Ten en cuenta que el código incluye un bucle para permitir que un usuario escriba una indicación hasta que escriba "salir". A continuación, en la sección bucle, busca el comentario **Get a chat completion** y agrega el código siguiente para añadir la entrada de usuario a la indicación, recuperar la finalización del modelo y agregar la finalización a la indicación (así conservarás el historial de chats para futuras iteraciones):

    **Python**

    ```python
   # Get a chat completion
   prompt.append(UserMessage(input_text))
   response = chat.complete(
        model=model_deployment,
        messages=prompt)
   completion = response.choices[0].message.content
   print(completion)
   prompt.append(AssistantMessage(completion))
    ```

    **C#**

    ```csharp
   // Get a chat completion
   prompt.Add(new ChatRequestUserMessage(input_text));
   var requestOptions = new ChatCompletionsOptions()
   {
       Model = model_deployment,
       Messages = prompt
   };

   Response<ChatCompletions> response = chat.Complete(requestOptions);
   var completion = response.Value.Content;
   Console.WriteLine(completion);
   prompt.Add(new ChatRequestAssistantMessage(completion));
    ```

1. Usa el comando **CTRL+S** para guardar los cambios en el archivo de código.

### Ejecución de la aplicación de chat

1. En el panel de la línea de comandos de Cloud Shell, debajo del editor de código, escribe el siguiente comando para ejecutar la aplicación:

    **Python**

    ```
   python chat-app.py
    ```

    **C#**

    ```
   dotnet run
    ```

1. Cuando se te solicite, escribe una pregunta, como `What is the fastest animal on Earth?` y revisa la respuesta del modelo de IA generativa.
1. Prueba algunas preguntas de seguimiento, como `Where can I see one?` o `Are they endangered?`. La conversación debe continuar, usando el historial de chats como contexto para cada iteración.
1. Cuando hayas terminado, presiona `quit` para salir del programa.

## Uso del SDK de OpenAI

La aplicación cliente se compila mediante el SDK de inferencia del modelo de Azure AI, lo que significa que se puede usar con cualquier modelo implementado en el servicio de inferencia del modelo de Azure AI. El modelo implementado es un modelo GPT de OpenAI, que también puedes consumir mediante el SDK de OpenAI.

Vamos a realizar algunas modificaciones del código para ver cómo implementar una aplicación de chat mediante el SDK de OpenAI.

1. En la línea de comandos de Cloud Shell para la carpeta de código (*python* o *c-sharp*), escribe el siguiente comando para instalar el paquete necesario:

    **Python**

    ```
   pip install openai
    ```

    **C#**

    ```
   dotnet add package Azure.AI.Projects --version 1.0.0-beta.6
   dotnet add package Azure.AI.OpenAI --prerelease
    ```

> **Nota**: Se requiere una versión preliminar diferente del paquete Azure.AI.Projects como solución alternativa provisional ante algunas incompatibilidades con el SDK de inferencia del modelo de Azure AI.

1. Si el archivo de código (*chat-app.py* o *Program.cs*) aún no está abierto, escribe el siguiente comando para abrirlo en el editor de código:

    **Python**

    ```
   code chat-app.py
    ```

    **C#**

    ```
   code Program.cs
    ```

1. Agrega la o las referencias siguientes en la parte superior del archivo de código:

    **Python**

    ```python
   import openai
    ```

    **C#**

    ```csharp
   using OpenAI.Chat;
   using Azure.AI.OpenAI;
    ```

1. Busca el comentario **Get a chat client** y modifica el código usado para crear un objeto de cliente de la siguiente manera:

    **Python**

    ```python
   # Get a chat client 
   openai_client = projectClient.inference.get_azure_openai_client(api_version="2024-10-21")
    ```

    **C#**

    ```csharp
   // Get a chat client
   ChatClient openaiClient = projectClient.GetAzureOpenAIChatClient(model_deployment);
    ```

    > **Nota**: Este código usa el cliente del proyecto de Fundición de IA de Azure para crear una conexión segura al punto de conexión predeterminado de Azure OpenAI Service asociado al proyecto. También puedes conectarte *directamente* al punto de conexión mediante el SDK de Azure OpenAI, especificando el URI del punto de conexión que se muestra para la conexión de servicio en el Portal de la Fundición de IA de Azure o en la página de recursos de Azure OpenAI o de servicios de AI correspondiente en Azure Portal, y mediante una clave de autenticación o un token de credencial de Entra. Para más información sobre cómo conectarse a Azure OpenAI Service, consulta [Lenguajes de programación compatibles con Azure OpenAI](https://learn.microsoft.com/azure/ai-services/openai/supported-languages).

1. Busca el comentario **Initialize prompt with system message** y modifica el código para inicializar una colección de mensajes con una indicación del sistema como se indica a continuación:

    **Python**

    ```python
   # Initialize prompt with system message
   prompt=[
        {"role": "system", "content": "You are a helpful AI assistant that answers questions."}
    ]
    ```

    **C#**

    ```csharp
   // Initialize prompt with system message
    var prompt = new List<ChatMessage>(){
        new SystemChatMessage("You are a helpful AI assistant that answers questions.")
    };
    ```

1. Busca el comentario **Get a chat completion** y modifica el código para agregar la entrada de usuario a la indicación, recuperar la finalización del modelo y agregar la finalización a la indicación como se indica a continuación:

    **Python**

    ```python
   # Get a chat completion
   prompt.append({"role": "user", "content": input_text})
   response = openai_client.chat.completions.create(
        model=model_deployment,
        messages=prompt)
   completion = response.choices[0].message.content
   print(completion)
   prompt.append({"role": "assistant", "content": completion})
    ```

    **C#**

    ```csharp
   // Get a chat completion
   prompt.Add(new UserChatMessage(input_text));
   ChatCompletion completion = openaiClient.CompleteChat(prompt);
   var completionText = completion.Content[0].Text;
   Console.WriteLine(completionText);
   prompt.Add(new AssistantChatMessage(completionText));
    ```

1. Usa el comando **CTRL+S** para guardar los cambios en el archivo de código.

1. En el panel de la línea de comandos de Cloud Shell, debajo del editor de código, escribe el siguiente comando para ejecutar la aplicación:

    **Python**

    ```
   python chat-app.py
    ```

    **C#**

    ```
   dotnet run
    ```

1. Prueba la aplicación haciendo preguntas como antes. Cuando hayas terminado, presiona `quit` para salir del programa.

    > **Nota**: El SDK de inferencia del modelo de Azure AI y los SDK de OpenAI usan clases y construcciones de código similares, por lo que el código requiere cambios mínimos. Puedes usar el SDK de inferencia del modelo de Azure AI con *cualquier* modelo que se implemente en un punto de conexión de servicio de inferencia del modelo de Azure AI. El SDK de OpenAI solo funciona con modelos de OpenAI, pero puedes usarlo para los modelos implementados en un punto de conexión de servicio de inferencia del modelo de Azure AI o en un punto de conexión de Azure OpenAI.  

## Resumen

En este ejercicio, has usado el SDK de la Fundición de IA de Azure, la inferencia del modelo de Azure IA y los SDK de Azure OpenAI para crear una aplicación cliente para un modelo de IA generativa que implementaste en un proyecto de Fundición de IA de Azure.

## Limpieza

Si has terminado de explorar el Portal de la Fundición de IA de Azure, deberías eliminar los recursos que has creado en este ejercicio para evitar incurrir en costes innecesarios de Azure.

1. Vuelve a la pestaña del explorador que contiene Azure Portal (o vuelve a abrir [Azure Portal](https://portal.azure.com) en `https://portal.azure.com` en una nueva pestaña del explorador) y mira el contenido del grupo de recursos donde implementó los recursos usados en este ejercicio.
1. Selecciona **Eliminar grupo de recursos** en la barra de herramientas.
1. Escribe el nombre del grupo de recursos y confirma que deseas eliminarlo.

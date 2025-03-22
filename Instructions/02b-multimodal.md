---
lab:
  title: Desarrollo de una aplicación de IA generativa multimodal
  description: 'Aprenda a usar Fundición de IA de Azure para crear una aplicación de IA generativa que admita entradas de texto, imagen y audio.'
---

# Desarrollo de una aplicación de IA generativa multimodal

En este ejercicio, usarás el modelo de IA generativa *Phi-4-multimodal-instruct* para generar respuestas a indicaciones que incluyen texto, imágenes y audio. Desarrollarás una aplicación que proporcione asistencia de IA con los productos frescos en un supermercado mediante Fundición de IA de Azure y el servicio de inferencia del modelo de Azure AI.

Este ejercicio dura aproximadamente **30** minutos.

## Creación de un proyecto de Azure AI Foundry

Comencemos creando un proyecto de Fundición de IA de Azure.

1. En un explorador web, abre el [Portal de la Fundición de IA de Azure](https://ai.azure.com) en `https://ai.azure.com` e inicia sesión con tus credenciales de Azure. Cierra las sugerencias o paneles de inicio rápido que se abran la primera vez que inicias sesión y, si es necesario, usa el logotipo de **Fundición de IA de Azure** en la parte superior izquierda para navegar a la página principal, que es similar a la siguiente imagen:

    ![Captura de pantalla del Portal de la Fundición de IA de Azure.](./media/ai-foundry-home.png)

2. En la página principal, selecciona **+Crear proyecto**.
3. En el Asistente para **crear un proyecto**, escribe un nombre de proyecto adecuado (como `my-ai-project`) y revisa los recursos de Azure que se crearán automáticamente para admitir el proyecto.
4. Selecciona **Personalizar** y especifica la siguiente configuración para el centro:
    - **Nombre del centro**: *un nombre único; por ejemplo, `my-ai-hub`*
    - **Suscripción**: *suscripción a Azure*
    - **Grupo de recursos**: *crea un nuevo grupo de recursos con un nombre único (como `my-ai-resources`) o selecciona uno existente*
    - **Ubicación**: selecciona cualquiera de las siguientes regiones\*:
        - Este de EE. UU.
        - Este de EE. UU. 2
        - Centro-Norte de EE. UU
        - Centro-sur de EE. UU.
        - Centro de Suecia
        - Oeste de EE. UU.
        - Oeste de EE. UU. 3
    - **Conectar Servicios de Azure AI o Azure OpenAI**: *crea un nuevo recurso de AI Services con un nombre adecuado (como `my-ai-services`) o usa uno existente.*
    - **Conectar Búsqueda de Azure AI**: omite la conexión

    > \* En el momento de escribir esto, el modelo de Microsoft *Phi-4-multimodal-instruct* que usaremos en este ejercicio estaba disponible en estas regiones. Puedes comprobar la disponibilidad regional más reciente de los modelos específicos en la [documentación de Fundición de IA de Azure](https://learn.microsoft.com/azure/ai-foundry/how-to/deploy-models-serverless-availability#region-availability). En caso de que se alcance un límite de cuota regional más adelante en el ejercicio, es posible que tengas que crear otro recurso en otra región.

5. Selecciona **Siguiente** y revisa tu configuración. Luego, selecciona **Crear** y espera a que se complete el proceso.
6. Cuando se cree el proyecto, cierra las sugerencias que se muestran y revisa la página del proyecto en el Portal de la Fundición de IA de Azure, que debe tener un aspecto similar a la siguiente imagen:

    ![Captura de pantalla de los detalles de un proyecto de Azure AI en el Portal de la Fundición de IA de Azure.](./media/ai-foundry-project.png)

## Implementación de un modelo

Ahora está listo para implementar un modelo *Phi-4-multimodal-instruct* para admitir indicaciones bidireccionales.

1. En la barra de herramientas de la parte superior derecha de la página del proyecto de Fundición de IA de Azure, usa el icono **Características de versión preliminar** para habilitar la característica **Implementar modelos en el servicio de inferencia de modelos de Azure AI**. Esta característica garantiza que la implementación del modelo esté disponible para el servicio de inferencia de Azure AI, que usarás en el código de aplicación.
2. En el panel de la izquierda de tu proyecto, en la sección **Mis recursos**, selecciona la página **Modelos y puntos de conexión**.
3. En la página **Modelos y puntos de conexión**, en la pestaña **Implementaciones de modelos**, en el menú **+ Implementar modelo**, selecciona **Implementar modelo base**.
4. Busca el modelo **Phi-4-multimodal-instruct** en la lista y, a continuación, selecciónalo y confírmalo.
5. Acepta el contrato de licencia si se te solicita y, a continuación, implementa el modelo con la siguiente configuración seleccionando **Personalizar** en los detalles de implementación:
    - **Nombre de implementación**: *nombre único para la implementación del modelo, por ejemplo `Phi-4-multimodal` (recuerda el nombre que asignes, lo necesitarás más adelante*).
    - **Tipo de implementación**: estándar global
    - **Detalles de implementación**: *usa la configuración predeterminada*
6. Espera a que se **complete** el estado de aprovisionamiento de la implementación.

## Creación de una aplicación cliente

Ahora que has implementado el modelo, puedes usar la implementación en una aplicación cliente.

> **Sugerencia**: puedes elegir desarrollar la solución mediante Phyton o Microsoft #C *(próximamente)*. Sigue las instrucciones de la sección adecuada para el idioma elegido.

### Preparación de la configuración de aplicación

1. En el Portal de la Fundición de IA de Azure, mira la página **Información general** del proyecto.
2. En el área **Detalles del proyecto**, anota la **Cadena de conexión del proyecto**. Usarás esta cadena de conexión para conectarte al proyecto en una aplicación cliente.
3. Abre una nueva pestaña del explorador (mantén el Portal de la Fundición de IA de Azure abierto en la pestaña existente). En la nueva pestaña, explora [Azure Portal](https://portal.azure.com) en `https://portal.azure.com` e inicia sesión con tus credenciales de Azure, si se te solicita.
4. Usa el botón **[\>_]** situado a la derecha de la barra de búsqueda en la parte superior de la página para crear una nueva instancia de Cloud Shell en Azure Portal, para lo que deberás seleccionar un entorno de ***PowerShell***. Cloud Shell proporciona una interfaz de la línea de comandos en un panel situado en la parte inferior de Azure Portal.

    > **Nota**: si has creado anteriormente una instancia de Cloud Shell que usa un entorno de *Bash*, cámbiala a ***PowerShell***.

5. En la barra de herramientas de Cloud Shell, en el menú **Configuración**, selecciona **Ir a la versión clásica** (esto es necesario para usar el editor de código).

6. En el panel de PowerShell, escribe los siguientes comandos para clonar el repo de GitHub para este ejercicio:

    ```
    rm -r mslearn-ai-foundry -f
    git clone https://github.com/microsoftlearning/mslearn-ai-studio mslearn-ai-foundry
    ```

    > **Sugerencia**: al pegar comandos en CloudShell, la salida puede ocupar una gran cantidad del búfer de pantalla. Puedes despejar la pantalla al escribir el comando `cls` para que te resulte más fácil centrarte en cada tarea.

7. Una vez clonado el repo, ve a la carpeta que contiene los archivos de código de aplicación:  

    **Python**

    ```
   cd mslearn-ai-foundry/labfiles/multimodal/python
    ```

    **C#**

    ```
   cd mslearn-ai-foundry/labfiles/multimodal/c-sharp
    ```

8. En el panel de la línea de comandos de Cloud Shell, escribe el siguiente comando para instalar las bibliotecas que vas a usar:

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

9. Escribe el siguiente comando para editar el archivo de configuración que se ha proporcionado:

    **Python**

    ```
   code .env
    ```

    **C#**

    ```
   code appsettings.json
    ```

    El archivo se abre en un editor de código.

10. En el archivo de código, reemplaza el marcador de posición **your_project_endpoint** por la cadena de conexión del proyecto (copiado de la página **Información general** del proyecto en el Portal de la Fundición de IA de Azure) y el marcador de posición **your_model_deployment** por el nombre que asignaste a la implementación de modelo Phi-4-multimodal-instruct.
11. Después de reemplazar los marcadores de posición, usa el comando **CTRL+S** para guardar los cambios y, a continuación, usa el comando **CTRL+Q** para cerrar el editor de código mientras mantienes abierta la línea de comandos de Cloud Shell.

### Escritura de código para conectarte al proyecto y obtener un cliente de chat para el modelo

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

2. En el archivo de código, observa las instrucciones existentes que se han agregado en la parte superior del archivo para importar los espacios de nombres de SDK necesarios. Después, en el comentario **Agregar referencias**, agrega el código siguiente para hacer referencia a los espacios de nombres de las bibliotecas que has instalado anteriormente:

    **Python**

    ```
   from dotenv import load_dotenv
   from azure.identity import DefaultAzureCredential
   from azure.ai.projects import AIProjectClient
   from azure.ai.inference.models import (
       SystemMessage,
       UserMessage,
       TextContentItem,
       ImageContentItem,
       ImageUrl,
       AudioContentItem,
       InputAudio,
       AudioContentFormat,
   )
    ```

    **C#**

    ```
   using Azure.Identity;
   using Azure.AI.Projects;
   using Azure.AI.Inference;
    ```

3. En la función **principal**, en el comentario **Obtener ajustes de configuración**, observa que el código carga los valores de la cadena de conexión del proyecto y del nombre de implementación del modelo que has definido en el archivo de configuración.
4. En el comentario **Inicializar el cliente del proyecto**, agrega el siguiente código para conectarte a tu proyecto de Azure AI Foundry usando las credenciales de Azure con las que has iniciado sesión:

    **Python**

    ```
   project_client = AIProjectClient.from_connection_string(
        conn_str=project_connection,
        credential=DefaultAzureCredential())
    ```

    **C#**

    ```
   var projectClient = new AIProjectClient(project_connection,
                        new DefaultAzureCredential());
    ```

5. En el comentario **Obtener un cliente de chat**, agrega el siguiente código para crear un objeto de cliente para chatear con el modelo:

    **Python**

    ```
   chat_client = project_client.inference.get_chat_completions_client(model=model_deployment)
    ```

    **C#**

    ```
   ChatCompletionsClient chat = projectClient.GetChatCompletionsClient();
    ```


### Escritura de código para usar una indicación basada en texto

1. Ten en cuenta que el código incluye un bucle para permitir que un usuario escriba una solicitud hasta que escriba "salir". A continuación, en la sección bucle, en el comentario **Obtener una respuesta para una entrada de texto**, agrega el código siguiente para enviar una indicación de texto y recuperar la respuesta del modelo:

    **Python**

    ```python
   response = chat_client.complete(
       messages=[
           SystemMessage(system_message),
           UserMessage(content=[TextContentItem(text= prompt)])
       ])
   print(response.choices[0].message.content)
    ```

    **C#**

    ```
   var requestOptions = new ChatCompletionsOptions()
   {
   Model = model_deployment,
   Messages =
       {
           new ChatRequestSystemMessage(system_message),
           new ChatRequestUserMessage(prompt),
       }
   };

   Response<ChatCompletions> response = chat.Complete(requestOptions);
   Console.WriteLine(response.Value.Content);
    ```

2. Usa el comando **CTRL+S** para guardar los cambios en el archivo de código; no lo cierres todavía.

3. En el panel de línea de comandos de Cloud Shell, debajo del editor de código, escribe el siguiente comando para ejecutar la aplicación:

    **Python**

    ```
   python chat-app.py
    ```

    **C#**

    ```
   dotnet run
    ```

4. Cuando se te solicite, escribe `1` para usar una indicación de texto y luego escribe la indicación `I want to make an apple pie. What kind of apple should I use?`
5. Revisa la respuesta. A continuación, escribe `quit` para salir del programa.

### Escritura de código para usar una indicación basada en imagen

1. En el editor de código del archivo **chat-app.py**, en la sección bucle, en el comentario **Obtener una respuesta para una entrada de imagen**, agrega el código siguiente para enviar una indicación que incluya la siguiente imagen:

    ![La foto de una naranja.](../labfiles/multimodal/orange.jpg)

    **Python**

    ```python
   image_url = "https://github.com/microsoftlearning/mslearn-ai-studio/raw/refs/heads/main/labfiles/multimodal/orange.jpg"
   image_format = "jpeg"
   request = Request(image_url, headers={"User-Agent": "Mozilla/5.0"})
   image_data = base64.b64encode(urlopen(request).read()).decode("utf-8")
   data_url = f"data:image/{image_format};base64,{image_data}"

   response = chat_client.complete(
       messages=[
           SystemMessage(system_message),
           UserMessage(content=[
               TextContentItem(text=prompt),
               ImageContentItem(image_url=ImageUrl(url=data_url))
           ]),
       ]
   )
   print(response.choices[0].message.content)
    ```

    **C#**

    ```csharp
  string imageUrl = "https://github.com/microsoftlearning/mslearn-ai-studio/raw/refs/heads/main/labfiles/multimodal/orange.jpg";
   ChatCompletionsOptions requestOptions = new ChatCompletionsOptions()
   {
       Messages = {
           new ChatRequestSystemMessage(system_message),
           new ChatRequestUserMessage([
               new ChatMessageTextContentItem(prompt),
               new ChatMessageImageContentItem(new Uri(imageUrl))
           ]),
       },
       Model = model_deployment
   };
   var response = chat.Complete(requestOptions);
   Console.WriteLine(response.Value.Content);
    ```

2. Usa el comando **CTRL+S** para guardar los cambios en el archivo de código; no lo cierres todavía.

3. En el panel de línea de comandos de Cloud Shell, debajo del editor de código, escribe el siguiente comando para ejecutar la aplicación:

    **Python**

    ```
   python chat-app.py
    ```

    **C#**

    ```
   dotnet run
    ```

4. Cuando se te solicite, escribe `2` para usar una indicación basada en imagen y, a continuación, escribe la indicación `I don't know what kind of fruit this is. Can you identify it, and tell me what kinds of food I could make with it?`
5. Revisa la respuesta. A continuación, escribe `quit` para salir del programa.

### Escritura de código para usar una indicación basada en audio

1. En el editor de código del archivo **chat-app.py**, en la sección bucle, en el comentario **Obtener una respuesta para una entrada de audio**, agrega el código siguiente para enviar una indicación que incluya el siguiente audio:

    <video controls src="./media/manzanas.mp4" title="La hora es 2:15" width="150"></video>

    **Python**

    ```python
   file_path="https://github.com/microsoftlearning/mslearn-ai-studio/raw/refs/heads/main/labfiles/multimodal/manzanas.mp3"
   response = chat_client.complete(
           messages=[
               SystemMessage(system_message),
               UserMessage(
                   [
                       TextContentItem(text=prompt),
                       {
                           "type": "audio_url",
                           "audio_url": {"url": file_path}
                       }
                   ]
               )
           ]
       )
   print(response.choices[0].message.content)
    ```

    **C#**

    ```csharp
   string audioUrl="https://github.com/microsoftlearning/mslearn-ai-studio/raw/refs/heads/main/labfiles/multimodal/manzanas.mp3";
   var requestOptions = new ChatCompletionsOptions()
   {
       Messages =
       {
           new ChatRequestSystemMessage(system_message),
           new ChatRequestUserMessage(
               new ChatMessageTextContentItem(prompt),
               new ChatMessageAudioContentItem(new Uri(audioUrl))),
       },
       Model = model_deployment
   };
   var response = chat.Complete(requestOptions);
   Console.WriteLine(response.Value.Content);
    ```


2. Usa el comando **CTRL+S** para guardar los cambios en el archivo de código. También puedes cerrar el editor de código (**CTRL+Q**) si lo deseas.

3. En el panel de línea de comandos de Cloud Shell, debajo del editor de código, escribe el siguiente comando para ejecutar la aplicación:

    **Python**

    ```
   python chat-app.py
    ```

    **C#**

    ```
   dotnet run
    ```

4. Cuando se te solicite, escribe `3` para usar una indicación basada en audio y, a continuación, escribe la indicación `What is this customer saying in English?`
5. Revisa la respuesta.
6. Puedes seguir ejecutando la aplicación, eligiendo diferentes tipos de indicaciones y probando indicaciones diferentes. Cuando hayas terminado, presiona `quit` para salir del programa.

    Si tienes tiempo, puedes modificar el código para usar un aviso del sistema diferente y tus propios archivos de audio e imagen accesibles por Internet.

    > **Nota**: en esta aplicación sencilla, no hemos implementado una lógica para conservar el historial de conversaciones, por lo que el modelo tratará cada indicación como una nueva solicitud sin contexto de la indicación anterior.

## Resumen

En este ejercicio, has usado Fundición de IA de Azure y el SDK de inferencia de Azure AI para crear una aplicación cliente que usa un modelo multimodal para generar respuestas para texto, imágenes y audio.

## Limpieza

Si has terminado de explorar Fundición de IA de Azure, deberías eliminar los recursos que has creado en este ejercicio para evitar incurrir en costes innecesarios de Azure.

1. Vuelve a la pestaña del explorador que contiene Azure Portal (o vuelve a abrir [Azure Portal](https://portal.azure.com) en `https://portal.azure.com` en una nueva pestaña del explorador) y mira el contenido del grupo de recursos donde implementó los recursos usados en este ejercicio.
1. Selecciona **Eliminar grupo de recursos** en la barra de herramientas.
1. Escribe el nombre del grupo de recursos y confirma que deseas eliminarlo.

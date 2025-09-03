---
lab:
  title: Creación de una aplicación de chat de IA generativa
  description: Obtén información sobre cómo usar el SDK de la Fundición de IA de Azure para crear una aplicación que se conecte al proyecto y chats con un modelo de lenguaje.
---

# Creación de una aplicación de chat de IA generativa

En este ejercicio, usará el SDK de Python de Fundición de IA de Azure para crear una aplicación de chat sencilla que se conecte a un proyecto y converse con un modelo de lenguaje.

> **Nota**: Este ejercicio se basa en software de SDK de la versión preliminar, que podrían cambiar. Cuando ha sido necesario, hemos usado versiones específicas de paquetes; que pueden no ser las versiones disponibles más recientes. Puede que se produzcan algunos errores, comportamientos o advertencias inesperados.

Aunque este ejercicio se basa en el SDK de Python de Fundición de IA de Azure, puede desarrollar aplicaciones de chat de IA mediante varios SDK específicos del lenguaje; incluidos los siguientes:

- [Proyectos de Azure AI para Python](https://pypi.org/project/azure-ai-projects)
- [Proyectos de Azure AI para Microsoft .NET](https://www.nuget.org/packages/Azure.AI.Projects)
- [Proyectos de Azure AI para JavaScript](https://www.npmjs.com/package/@azure/ai-projects)

Este ejercicio dura aproximadamente **40** minutos.

## Implementación de un modelo en un proyecto de Fundición de IA de Azure

Comencemos con la implementación de un modelo en un proyecto de Fundición de IA de Azure.

1. En un explorador web, abre el [Portal de la Fundición de IA de Azure](https://ai.azure.com) en `https://ai.azure.com` e inicia sesión con tus credenciales de Azure. Cierra las sugerencias o paneles de inicio rápido que se abran la primera vez que inicias sesión y, si es necesario, usa el logotipo de **Fundición de IA de Azure** en la parte superior izquierda para navegar a la página principal, que es similar a la siguiente imagen (cierra el panel **Ayuda** si está abierto):

    ![Captura de pantalla del Portal de la Fundición de IA de Azure.](./media/ai-foundry-home.png)

1. En la página principal, en la sección **Explorar modelos y funcionalidades**, busca el modelo `gpt-4o`, que usaremos en nuestro proyecto.
1. En los resultados de la búsqueda, selecciona el modelo **gpt-4o** para ver sus detalles y, a continuación, en la parte superior de la página del modelo, selecciona **Usar este modelo**.
1. Cuando se te pida que crees un proyecto, escribe un nombre válido para el proyecto y expande **Opciones avanzadas**.
1. Selecciona **Personalizar** y especifica la siguiente configuración para el proyecto:
    - **Recurso de Fundición de IA de Azure**: *un nombre válido para el recurso de Fundición de IA de Azure*
    - **Suscripción**: *suscripción a Azure*
    - **Grupo de recursos**: *crea o selecciona un grupo de recursos*
    - **Región**: *seleccione cualquiera (se recomienda Fundición de IA\*).

    > \* Algunos de los recursos de Azure AI están restringidos por cuotas de modelo regionales. En caso de que se alcance un límite de cuota más adelante en el ejercicio, es posible que tengas que crear otro recurso en otra región.

1. Selecciona **Crear** y espera a que tu proyecto se cree. Si se te solicita, implementa el modelo gpt-4o mediante el tipo de implementación **Estándar global** y personaliza los detalles de la implementación para establecer un **Límite de velocidad de tokens por minuto** de 50 000 (o el máximo disponible si es inferior a 50 000).

    > **Nota**: reducir el TPM ayuda a evitar el uso excesivo de la cuota disponible en la suscripción que está usando. 50 000 TPM es suficiente para los datos que se usan en este ejercicio. Si la cuota disponible es inferior a esta, podrás completar el ejercicio, pero se pueden producir errores si se supera el límite de velocidad.

1. Cuando se cree el proyecto, el área de juegos de chat se abrirá automáticamente para que puedas probar el modelo:
1. En el panel **Configuración**, anota el nombre de la implementación del modelo; que debe ser **gpt-4o**. Para confirmarlo, mira la implementación en la página **Modelos y puntos de conexión** (simplemente abre esa página en el panel de navegación de la izquierda).
1. En el panel de navegación de la izquierda, selecciona **Información general** para ver la página principal del proyecto; que tiene este aspecto:

    ![Captura de pantalla de la página de información general de un proyecto de Fundición de IA de Azure.](./media/ai-foundry-project.png)

## Creación de una aplicación cliente para chatear con el modelo

Ahora que ha implementado un modelo, puede usar los SDK de la Fundición de IA de Azure y Azure OpenAI para desarrollar una aplicación que chatee con él.

### Preparación de la configuración de aplicación

1. En el Portal de la Fundición de IA de Azure, mira la página **Información general** del proyecto.
1. En el área **Puntos de conexión y claves**, asegúrese de que la biblioteca **Fundición de IA de Azure** esté seleccionada y vea el **punto de conexión del proyecto de Fundición de IA de Azure**. Usará este punto de conexión para conectarse al proyecto y al modelo en una aplicación cliente.

    > **Nota**: También puede usar el punto de conexión de Azure OpenAI.

1. Abre una nueva pestaña del explorador (mantén el Portal de la Fundición de IA de Azure abierto en la pestaña existente). En la nueva pestaña, explora [Azure Portal](https://portal.azure.com) en `https://portal.azure.com` e inicia sesión con tus credenciales de Azure, si se te solicita.

    Cierra las notificaciones de bienvenida para ver la página principal de Azure Portal.

1. Usa el botón **[\>_]** situado a la derecha de la barra de búsqueda en la parte superior de la página para crear una nueva instancia de Cloud Shell en Azure Portal, para lo que deberás seleccionar un entorno de ***PowerShell*** sin almacenamiento en tu suscripción.

    Cloud Shell proporciona una interfaz de la línea de comandos en un panel situado en la parte inferior de Azure Portal. Puedes cambiar el tamaño o maximizar este panel para facilitar el trabajo.

    > **Nota**: si has creado anteriormente una instancia de Cloud Shell que usa un entorno de *Bash*, cámbiala a ***PowerShell***.

1. En la barra de herramientas de Cloud Shell, en el menú **Configuración**, selecciona **Ir a la versión clásica** (esto es necesario para usar el editor de código).

    **<font color="red">Asegúrate de que has cambiado a la versión clásica de Cloud Shell antes de continuar.</font>**

1. En el panel de Cloud Shell, escribe los siguientes comandos para clonar el repositorio de GitHub que contiene los archivos de código de este ejercicio (escribe el comando o cópialo en el Portapapeles y haz clic con el botón derecho en la línea de comandos y pega como texto sin formato):

    ```
   rm -r mslearn-ai-foundry -f
   git clone https://github.com/microsoftlearning/mslearn-ai-studio mslearn-ai-foundry
    ```

    > **Sugerencia**: al pegar comandos en CloudShell, la salida puede ocupar una gran cantidad del búfer de pantalla. Puedes despejar la pantalla al escribir el comando `cls` para que te resulte más fácil centrarte en cada tarea.

1. Una vez que se clone el repositorio, vaya a la carpeta que contiene los archivos de código de la aplicación de chat para verlos:

    ```
   cd mslearn-ai-foundry/labfiles/chat-app/python
   ls -a -l
    ```

    La carpeta contiene un archivo de código, así como un archivo de configuración para la configuración de la aplicación y un archivo que define el runtime del proyecto y los requisitos del paquete.

1. En el panel de la línea de comandos de Cloud Shell, escribe el siguiente comando para instalar las bibliotecas que vas a usar:

    ```
   python -m venv labenv
   ./labenv/bin/Activate.ps1
   pip install -r requirements.txt azure-identity azure-ai-projects openai
    ```
1. Escribe el siguiente comando para editar el archivo de configuración que se ha proporcionado:

    ```
   code .env
    ```

    El archivo se abre en un editor de código.

1. En el archivo de código, reemplace el marcador de posición **your_project_endpoint** por el **punto de conexión del proyecto de Fundición de IA de Azure** del proyecto (copiado de la página **Información general** en el Portal de la Fundición de IA de Azure) y el marcador de posición **your_model_deployment** por el nombre de la implementación del modelo gpt-4.
1. Después de reemplazar los marcadores de posición, en el editor de código, usa el comando **CTRL+S** o usa la acción de **hacer clic con el botón derecho > Guardar** para guardar los cambios y, a continuación, usa el comando **CTRL+Q** o la acción de **hacer clic con el botón derecho > Salir** para cerrar el editor de código mientras mantienes abierta la línea de comandos de Cloud Shell.

### Escritura del código para conectarte al proyecto y chatear con el modelo

> **Sugerencia**: al agregar código, asegúrate de mantener la sangría correcta.

1. Escribe el siguiente comando para editar el archivo de código que se ha proporcionado:

    ```
   code chat-app.py
    ```

1. En el archivo de código, observa las instrucciones existentes que se han agregado en la parte superior del archivo para importar los espacios de nombres de SDK necesarios. Después, busca el comentario **Add references** y agrega el código siguiente para hacer referencia a los espacios de nombres de las bibliotecas que has instalado anteriormente:

    ```python
   # Add references
   from azure.identity import DefaultAzureCredential
   from azure.ai.projects import AIProjectClient
   from openai import AzureOpenAI
    ```

1. En la función **principal**, en el comentario **Obtener ajustes de configuración**, observa que el código carga los valores de la cadena de conexión del proyecto y del nombre de implementación del modelo que has definido en el archivo de configuración.
1. Busque el comentario **Inicializar el cliente del proyecto** y agregue el siguiente código para conectarse al proyecto de Fundición de IA de Azure:

    > **Sugerencia**: Ten cuidado de mantener el nivel de sangría correcto del código.

    ```python
   # Initialize the project client
   project_client = AIProjectClient(            
            credential=DefaultAzureCredential(
                exclude_environment_credential=True,
                exclude_managed_identity_credential=True
            ),
            endpoint=project_endpoint,
        )
    ```

1. Busca el comentario **Get a chat client** y agrega el siguiente código para crear un objeto de cliente para chatear con un modelo:

    ```python
   # Get a chat client
   openai_client = project_client.get_openai_client(api_version="2024-10-21")
    ```

1. Busca el comentario **Initialize prompt with system message** y agrega el código siguiente para inicializar una colección de mensajes con una indicación del sistema.

    ```python
   # Initialize prompt with system message
   prompt = [
            {"role": "system", "content": "You are a helpful AI assistant that answers questions."}
        ]
    ```

1. Ten en cuenta que el código incluye un bucle para permitir que un usuario escriba una indicación hasta que escriba "salir". A continuación, en la sección bucle, busca el comentario **Get a chat completion** y agrega el código siguiente para añadir la entrada de usuario a la indicación, recuperar la finalización del modelo y agregar la finalización a la indicación (así conservarás el historial de chats para futuras iteraciones):

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

1. Usa el comando **CTRL+S** para guardar los cambios en el archivo de código.

### Inicie sesión en Azure y ejecuta la aplicación.

1. En el panel de línea de comandos de Cloud Shell, escribe el siguiente comando para iniciar sesión en Azure.

    ```
   az login
    ```

    **<font color="red">Debes iniciar sesión en Azure, aunque la sesión de Cloud Shell ya esté autenticada.</font>**

    > **Nota**: en la mayoría de los escenarios, el uso de *inicio de sesión de az* será suficiente. Sin embargo, si tienes suscripciones en varios inquilinos, es posible que tengas que especificar el inquilino mediante el parámetro *--tenant*. Consulta [Inicio de sesión en Azure de forma interactiva mediante la CLI de Azure](https://learn.microsoft.com/cli/azure/authenticate-azure-cli-interactively) para obtener más información.
    
1. Cuando se te solicite, sigue las instrucciones para abrir la página de inicio de sesión en una nueva pestaña y escribe el código de autenticación proporcionado y las credenciales de Azure. A continuación, completa el proceso de inicio de sesión en la línea de comandos y selecciona la suscripción que contiene el centro de Fundición de IA de Azure si se te solicita.
1. Después de iniciar sesión, escribe el siguiente comando para ejecutar la aplicación:

    ```
   python chat-app.py
    ```

1. Cuando se te solicite, escribe una pregunta, como `What is the fastest animal on Earth?` y revisa la respuesta del modelo de IA generativa.
1. Prueba algunas preguntas de seguimiento, como `Where can I see one?` o `Are they endangered?`. La conversación debe continuar, usando el historial de chats como contexto para cada iteración.
1. Cuando hayas terminado, presiona `quit` para salir del programa.

> **Sugerencia**: si se produce un error en la aplicación porque se supera el límite de velocidad. Espere unos segundos y vuelve a intentarlo. Si no hay cuota suficiente disponible en la suscripción, es posible que el modelo no pueda responder.

## Resumen

En este ejercicio, has usado el SDK de la Fundición de IA de Azure para crear una aplicación cliente para un modelo de IA generativa que implementaste en un proyecto de Fundición de IA de Azure.

## Limpieza

Si has terminado de explorar el Portal de la Fundición de IA de Azure, deberías eliminar los recursos que has creado en este ejercicio para evitar incurrir en costes innecesarios de Azure.

1. Abre [Azure Portal](https://portal.azure.com) y ve el contenido del grupo de recursos donde implementaste los recursos usados en este ejercicio.
1. Selecciona **Eliminar grupo de recursos** en la barra de herramientas.
1. Escribe el nombre del grupo de recursos y confirma que deseas eliminarlo.

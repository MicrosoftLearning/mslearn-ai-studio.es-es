---
lab:
  title: Creación de una aplicación de chat con el SDK de la Fundición de IA de Azure
---

# Creación de una aplicación de chat con el SDK de la Fundición de IA de Azure

En este ejercicio, usarás el SDK de la Fundición de IA de Azure para crear una aplicación de chat simple.

Este ejercicio dura aproximadamente **20** minutos.

## Creación de un proyecto de Azure AI Foundry

Comencemos creando un proyecto de Azure AI Foundry.

1. En un explorador web, abre el [portal de Azure AI Foundry](https://ai.azure.com) en `https://ai.azure.com` e inicia sesión con tus credenciales de Azure. Cierra cualquier sugerencia o paneles de inicio rápido que se abran la primera vez que inicies sesión y, si es necesario, usa el logotipo de **Fundición de IA de Azure** en la parte superior izquierda para ir a la página principal, que es similar a la siguiente imagen:

    ![Captura de pantalla del portal de Azure AI Foundry.](./media/ai-foundry-home.png)

1. En la página principal, selecciona **+Crear proyecto**.
1. En el asistente **Crear un proyecto**, escribe un nombre de proyecto adecuado para (por ejemplo, `my-ai-project`) y después revisa los recursos de Azure que se crearán automáticamente para ser compatibles con tu proyecto.
1. Selecciona **Personalizar** y especifica la siguiente configuración para el centro:
    - **Nombre del centro**: *un nombre único; por ejemplo `my-ai-hub`*
    - **Suscripción**: *suscripción de Azure*
    - **Grupo de recursos**: *crea un nuevo grupo de recursos con un nombre único (por ejemplo, `my-ai-resources`), o selecciona uno existente*.
    - **Ubicación**: elige una región aleatoria de la siguiente lista\*:
        - Este de EE. UU.
        - Este de EE. UU. 2
        - Centro-Norte de EE. UU
        - Centro-sur de EE. UU.
        - Centro de Suecia
        - Oeste de EE. UU.
        - Oeste de EE. UU. 3
    - **Conectar Servicios de Azure AI o Azure OpenAI**: *crea un nuevo recurso de Servicios de AI con el nombre adecuado (por ejemplo, `my-ai-services`) o usa uno existente*:
    - **Conectar Búsqueda de Azure AI**: omitir la conexión

    > \* Las cuotas de los modelos están restringidas en el nivel de inquilino por cuotas regionales. Elegir una región aleatoria ayuda a distribuir la disponibilidad de cuota cuando varios usuarios trabajan en el mismo inquilino. En caso de que se alcance un límite de cuota más adelante en el ejercicio, es posible que tengas que crear otro recurso en otra región.

1. Selecciona **Siguiente** y revisa tu configuración. Después selecciona **Crear** y espera a que se complete el proceso.
1. Una vez creado el proyecto, cierra las sugerencias que se muestren y revisa la página del proyecto en el portal de Azure AI Foundry, que debería tener un aspecto similar a la siguiente imagen:

    ![Captura de pantalla de los detalles de un proyecto de Azure AI en el portal de Azure AI Foundry.](./media/ai-foundry-project.png)

## Implementación de un modelo de IA generativa

Ahora ya puedes implementar un modelo de lenguaje de IA generativa compatible con tu aplicación de chat. En este ejemplo, usarás el modelo Microsoft Phi-4; pero los principios son los mismos para cualquier modelo.

1. En el panel de la izquierda de tu proyecto, en la sección **Mis recursos**, selecciona la página **Modelos + puntos de conexión**.
1. En la página **Modelos + puntos de conexión**, en la pestaña **Implementaciones de modelos**, en el menú **+Implementar modelo**, selecciona **Implementar modelo base**.
1. Busca el modelo **Phi-4** en la lista y después selecciónalo y confírmalo.
1. Selecciona la opción de implementación **API sin servidor con Seguridad del contenido de Azure AI**.
1. Implementa el modelo con la siguiente configuración mediante la selección de **Personalizar** en los detalles de implementación:
    - **Nombre de implementación**: *un nombre único para la implementación de tu modelo, por ejemplo `phi-4-model` (recuerda el nombre que asignes, lo necesitarás más adelante*)
    - **Filtro de contenido**: habilitado

## Creación de una aplicación cliente para chatear con el modelo

Ahora que has implementado un modelo, puedes usar el SDK de la Fundición de IA de Azure para desarrollar una aplicación que chatee con él.

### Preparación de la configuración de la aplicación

1. En el portal de Azure AI Foundry, consulta la página **Información general** de tu proyecto.
1. En el área **Detalles del proyecto**, observa la **Cadena de conexión del proyecto**. Usarás este cadena de conexión para conectarte al proyecto en una aplicación cliente.
1. Abre una nueva pestaña del explorador (mantén el portal de Azure AI Foundry abierto en la pestaña existente). Después, en la nueva pestaña, ve a [Azure Portal](https://portal.azure.com) en `https://portal.azure.com`; e inicia sesión con tus credenciales de Azure si se te solicita.
1. Usa el botón **[\>_]** situado a la derecha de la barra de búsqueda en la parte superior de la página para crear una nueva instancia de Cloud Shell en Azure Portal, para lo que deberás seleccionar un entorno de ***PowerShell***. Cloud Shell proporciona una interfaz de línea de comandos en un panel situado en la parte inferior de Azure Portal.

    > **Nota**: si has creado anteriormente una instancia de Cloud Shell que usa un entorno de *Bash*, cámbiala a ***PowerShell***.

1. En la barra de herramientas de Cloud Shell, en el menú **Configuración**, selecciona **Ir a la versión clásica** (esto es necesario para usar el editor de código).

1. En el panel de PowerShell, escribe los siguientes comandos para clonar el repo de GitHub para este ejercicio:

    ```
    rm -r mslearn-ai-foundry -f
    git clone https://github.com/microsoftlearning/mslearn-ai-studio mslearn-ai-foundry
    ```

1. Una vez clonado el repositorio, ve a la carpeta **mslearn-ai-foundry/labfiles/01a-azure-foundry-sdk/python**:

    ```
    cd mslearn-ai-foundry/labfiles/01a-azure-foundry-sdk/python
    ```

1. En el panel de línea de comandos de Cloud Shell, escribe el siguiente comando para instalar las bibliotecas de Python que vas a usar, que son:
    - **python-dotenv** : se usa para cargar la configuración desde un archivo de configuración de la aplicación.
    - **azure-identity**: se usa para autenticarse con credenciales de Entra ID.
    - **azure-ai-projects**: se usa para trabajar con un proyecto de Azure AI Foundry.
    - **azure-ai-inference**: se usa para chatear con un modelo de IA generativa.

    ```
    pip install python-dotenv azure-identity azure-ai-projects azure-ai-inference
    ```

1. Escribe el siguiente comando para editar el archivo de configuración de Python **.env** que se ha proporcionado:

    ```
    code .env
    ```

    El archivo se abre en un editor de código.

1. En el archivo de código, reemplaza el marcador de posición **your_project_endpoint** por la cadena de conexión de tu proyecto (copiada de la página **Información general** del proyecto en el portal de Azure Ai Foundry), y el marcador de posición **your_model_deployment** por el nombre que has asignado a la implementación de tu modelo Phi-4.
1. Después de reemplazar los marcadores de posición, usa el comando **CTRL+S** para guardar los cambios y después usa el comando **CTRL+Q** para cerrar el editor de código mientras mantienes abierta la línea de comandos de Cloud Shell.

### Escribir código para conectarse al proyecto y chatear con el modelo

> **Sugerencia**: al agregar código al archivo de código de Python, asegúrate de mantener la sangría correcta.

1. Escribe el siguiente comando para editar el archivo de código de Python **chat-app.py** que se ha proporcionado:

    ```
    code chat-app.py
    ```

1. En el archivo de código, observa las instrucciones de **importación** que se han agregado en la parte superior del archivo. Después, en el comentario **# Agregar referencia de proyectos de IA**, agrega el siguiente código para hacer referencia a la biblioteca de proyectos de Azure AI:

    ```python
    from azure.ai.projects import AIProjectClient
    ```

1. En la función **principal**, en el comentario **# Obtener parámetros de configuración**, observa que el código carga los valores de la cadena de conexión del proyecto y del nombre de implementación del modelo que has definido en el archivo **.env**.
1. En el comentario **# Inicializar el cliente del proyecto**, agrega el siguiente código para conectarte a tu proyecto de Azure AI Foundry usando las credenciales de Azure con las que has iniciado sesión:

    ```python
    project = AIProjectClient.from_connection_string(
        conn_str=project_connection,
        credential=DefaultAzureCredential()
        )
    ```
    
1. En el comentario **# Obtener un cliente de chat**, agrega el siguiente código para crear un objeto de cliente para chatear con un modelo:

    ```python
    chat = project.inference.get_chat_completions_client()
    ```

1. Ten en cuenta que el código incluye un bucle para permitir que el usuario introduzca una solicitud hasta que escriba "salir". Después, en la sección de bucle, en el comentario **# Get a chat completion**, agrega el siguiente código para enviar la solicitud y recuperar la finalización de tu modelo:

    ```python
    response = chat.complete(
        model=model_deployment,
        messages=[
            {"role": "system", "content": "You are a helpful AI assistant that answers questions."},
            {"role": "user", "content": input_text},
            ],
        )
    print(response.choices[0].message.content)
    ```

1. Usa el comando **CTRL+S** para guardar los cambios en el archivo de código y después usa el comando **CTRL+Q** para cerrar el editor de código mientras mantienes abierta la línea de comandos de Cloud Shell.

### Ejecución de la aplicación de chat

1. En el panel de línea de comandos de Cloud Shell, escribe el siguiente comando para ejecutar el código de Python:

    ```
    python chat-app.py
    ```

1. Cuando se te solicite, introduce una pregunta, como `What is the fastest animal on Earth?` y revisa la respuesta de tu modelo de IA generativa.
1. Prueba con algunas preguntas más. Cuando hayas terminado, escribe `quit` para salir del programa.

## Resumen

En este ejercicio, has usado el SDK de la Fundición de IA de Azure para crear una aplicación cliente para un modelo de IA generativa que has implementado en un proyecto de Azure AI Foundry.

## Limpieza

Si has terminado de explorar el portal de Azure AI Foundry, deberías eliminar los recursos que has creado en este ejercicio para evitar incurrir en costes innecesarios de Azure.

1. Vuelve a la pestaña del explorador que contiene Azure Portal (o vuelve a abrir [Azure Portal](https://portal.azure.com) en `https://portal.azure.com`una nueva pestaña del explorador) y visualiza el contenido del grupo de recursos en el que has implementado los recursos usados en este ejercicio.
1. Seleccione **Eliminar grupo de recursos** en la barra de herramientas.
1. Escriba el nombre del grupo de recursos y confirme que desea eliminarlo.

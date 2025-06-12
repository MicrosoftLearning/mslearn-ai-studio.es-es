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

## Creación de un recurso de Fundición de IA de Azure

Comencemos creando un recurso de Fundición de IA de Azure.

1. En un explorador web, abre [Azure Portal](https://portal.azure.com) en `https://portal.azure` e inicia sesión con tus credenciales de Azure. Cierra las sugerencias o paneles de inicio rápido que se abran la primera vez que inicies sesión.
1. Crea un nuevo recurso `Azure AI Foundry` con los valores siguientes:
    - **Suscripción**: *suscripción a Azure*
    - **Grupo de recursos**: *crea o selecciona un grupo de recursos*
    - **Nombre**: *un nombre válido para el recurso de Fundición de IA de Azure*
    - **Región**: selecciona una de las siguientes regiones:
        - Este de EE. UU. 2
        - Centro de Suecia
    - **Nombre de proyecto predeterminado**: *un nombre válido para el proyecto*

1. Espera a que se cree el recurso y ve a su página en Azure Portal.
1. En la página del recurso de Fundición de IA de Azure, selecciona **Ir al Portal de la Fundición de IA de Azure**.

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
1. En el Portal de la Fundición de IA de Azure, en el proyecto, en el panel de navegación de la izquierda, selecciona **Áreas de juegos** y, después, selecciona **Probar el área de juegos de chat**.
1. En el panel **Configuración** del área de juegos, expande la sección **Agregar los datos** y selecciona **Agregar un origen de datos**.
1. En el asistente para **Agregar datos**, expande el menú desplegable para seleccionar **Cargar archivos**.
1. Crea un nuevo recurso de Azure Blob Storage con la siguiente configuración:
    - **Suscripción**: *suscripción a Azure*
    - **Grupo de recursos**: *el mismo grupo de recursos que el recurso de Fundición de IA de Azure*
    - **Nombre de la cuenta de almacenamiento**: *un nombre válido para el recurso de la cuenta de almacenamiento*
    - **Región**: *misma región que el recurso de Fundición de IA de Azure*
    - **Rendimiento**: Estándar
    - **Redundancia**: LRS
1. Crea el recurso y espera hasta que se complete la implementación.
1. Vuelva a la pestaña Fundición de IA de Azure, actualiza la lista de recursos de Azure Blob Storage y selecciona la cuenta recién creada.

    > **Nota**: si recibes una advertencia de que Azure OpenAI necesita permiso para acceder al recurso, selecciona **Activar CORS**.

1. Crea un nuevo recurso de Búsqueda de Azure AI con la siguiente configuración:
    - **Suscripción**: *suscripción a Azure*
    - **Grupo de recursos**: *el mismo grupo de recursos que el recurso de Fundición de IA de Azure*
    - **Nombre del servicio**: *un nombre válido para el recurso de Búsqueda de Azure AI*
    - **Región**: *misma región que el recurso de Fundición de IA de Azure*
    - **Plan de tarifa**: básico

1. Crea el recurso y espera hasta que se complete la implementación.
1. Vuelve a la pestaña Fundición de IA de Azure, actualiza la lista de recursos de Búsqueda de Azure AI y selecciona la cuenta recién creada.
1. Asigna un nombre al índice `brochures-index`.
1. Habilita la opción **Agregar búsqueda vectorial a este recurso de búsqueda** y selecciona el modelo de inserción que implementaste anteriormente. Seleccione **Siguiente**.

   >**Nota**: puede tardar un tiempo hasta que el asistente para **agregar datos** reconozca el modelo de inserción implementado, por lo que si no puedes habilitar la opción de vector de búsqueda, cancela el asistente, espera unos minutos e inténtalo de nuevo.

1. Carga todos los archivos .pdf de la carpeta **brochures** que extrajiste anteriormente y selecciona **Siguiente**.
1. En el paso **Administración de datos**, selecciona el tipo de búsqueda **Híbrido (vector + palabra clave)** y un tamaño del fragmento de **1024**. Seleccione **Siguiente**.
1. En el paso **Conexión de datos**, selecciona **Clave de API** como tipo de autenticación. Seleccione **Siguiente**.
1. Revisa todos los pasos de configuración y, después, selecciona **Guardar y cerrar**.
1. Espera a que se complete el proceso de indexación, lo que puede tardar un tiempo en función de los recursos de proceso disponibles en la suscripción.

    > **Sugerencia**: mientras esperas a que se cree el índice, ¿por qué no echas un vistazo a los folletos que descargaste para familiarizarte con su contenido?

## Prueba del índice en el área de juegos

Antes de usar el índice en un flujo de avisos basado en RAG, vamos a comprobar que se puede usar para afectar a las respuestas de IA generativa.

1. En la página de área de juegos de chat, en el panel Configuración, asegúrate de que la implementación de modelo **gpt-4o** esté seleccionada. Después, en el panel Sesión de chat, envía el mensaje `Where can I stay in New York?`.
1. Revisa la respuesta, que debe basarse en los datos del índice.

## Creación de una aplicación cliente RAG

Ahora que tienes un índice de trabajo, puedes usar el SDK de la Azure OpenAI para implementar el patrón RAG en una aplicación cliente. Vamos a explorar el código para hacerlo en un ejemplo sencillo.

> **Sugerencia**: puedes elegir desarrollar tu solución RAG mediante C# de Python o Microsoft. Sigue las instrucciones de la sección adecuada para el idioma elegido.

### Preparación de la configuración de aplicación

1. Vuelve a la pestaña del explorador que contiene Azure Portal (mantén el Portal de la Fundición de IA de Azure abierto en la pestaña existente).
1. Usa el botón **[\>_]** situado a la derecha de la barra de búsqueda en la parte superior de la página para crear una nueva instancia de Cloud Shell en Azure Portal, para lo que deberás seleccionar un entorno de ***PowerShell*** sin almacenamiento en tu suscripción.

    Cloud Shell proporciona una interfaz de la línea de comandos en un panel situado en la parte inferior de Azure Portal. Puedes cambiar el tamaño o maximizar este panel para facilitar el trabajo.

    > **Nota**: si has creado anteriormente una instancia de Cloud Shell que usa un entorno de *Bash*, cámbiala a ***PowerShell***.

1. En la barra de herramientas de Cloud Shell, en el menú **Configuración**, selecciona **Ir a la versión clásica** (esto es necesario para usar el editor de código).

    **<font color="red">Asegúrate de que has cambiado a la versión clásica de Cloud Shell antes de continuar.</font>**

1. En el panel de Cloud Shell, escribe los siguientes comandos para clonar el repositorio de GitHub que contiene los archivos de código de este ejercicio (escribe el comando o cópialo en el Portapapeles y, a continuación, haz clic con el botón derecho en la línea de comandos y pega como texto sin formato):

    ```
    rm -r mslearn-ai-foundry -f
    git clone https://github.com/microsoftlearning/mslearn-ai-studio mslearn-ai-foundry
    ```

    > **Sugerencia**: al pegar comandos en CloudShell, la salida puede ocupar una gran cantidad del búfer de pantalla. Puedes despejar la pantalla al escribir el comando `cls` para que te resulte más fácil centrarte en cada tarea.

1. Una vez clonado el repo, ve a la carpeta que contiene los archivos de código de la aplicación de chat:

    > **Nota**: sigue los pasos del lenguaje de programación elegido.

    **Python**

    ```
   cd mslearn-ai-foundry/labfiles/rag-app/python
    ```

    **C#**

    ```
   cd mslearn-ai-foundry/labfiles/rag-app/c-sharp
    ```

1. En el panel de la línea de comandos de Cloud Shell, escribe el siguiente comando para instalar la biblioteca del SDK de OpenAI:

    **Python**

    ```
   python -m venv labenv
   ./labenv/bin/Activate.ps1
   pip install -r requirements.txt openai
    ```

    **C#**

    ```
   dotnet add package Azure.AI.OpenAI
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
    - **your_openai_endpoint**: el punto de conexión de OpenAI de la página **Información general** de tu proyecto en el Portal de la Fundición de IA de Azure (asegúrate de seleccionar la pestaña de la funcionalidad **Azure OpenAI**, no la funcionalidad de inferencia de Azure AI o Servicios de Azure AI).
    - **your_openai_api_key** la clave de API de OpenAI de la página **Información general** de tu proyecto en el Portal de la Fundición de IA de Azure (asegúrate de seleccionar la pestaña de la funcionalidad **Azure OpenAI**, no la funcionalidad de inferencia de Azure AI o Servicios de Azure AI).
    - **your_chat_model**: el nombre que asignaste a tu implementación de modelo **gpt-4o**, en la página **Modelos y puntos de conexión** en el Portal de la Fundición de IA de Azure (el nombre predeterminado es `gpt-4o`).
    - **your_embedding_model**: el nombre que asignaste a tu implementación de modelo **text-embedding-ada-002**, en la página **Modelos y puntos de conexión** en el Portal de la Fundición de IA de Azure (el nombre predeterminado es `text-embedding-ada-002`).
    - **your_search_endpoint**: la dirección URL para el recurso de Búsqueda de Azure AI. Lo encontrarás en el **Centro de gestión** en el Portal de la Fundición de IA de Azure.
    - **your_search_api_key**: la clave de API del recurso de Búsqueda de Azure AI. Lo encontrarás en el **Centro de gestión** en el Portal de la Fundición de IA de Azure.
    - **your_index**: reemplaza por el nombre del índice de la página **Datos e índices** para tu proyecto en el Portal de la Fundición de IA de Azure AI (debería ser `brochures-index`).
1. Después de reemplazar los marcadores de posición, en el editor de código, usa el comando **CTRL+S** o usa la acción de **hacer clic con el botón derecho > Guardar** para guardar los cambios y, a continuación, usa el comando **CTRL+Q** o la acción de **hacer clic con el botón derecho > Salir** para cerrar el editor de código mientras mantienes abierta la línea de comandos de Cloud Shell.

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
    - Crea un cliente de Azure OpenAI mediante el punto de conexión, la clave y el modelo de chat.
    - Crea un mensaje del sistema adecuado para una solución de chat relacionado con viajes.
    - Envía una indicación (incluido el sistema y un mensaje de usuario basado en la entrada de usuario) al cliente de Azure OpenAI y agrega lo siguiente:
        - Detalles de conexión del índice de Búsqueda de Azure AI que se va a consultar.
        - Detalles del modelo de inserción que se usará para vectorizar la consulta\*.
    - Muestra la respuesta desde el símbolo del sistema en la base.
    - Agrega la respuesta al historial de chats.

    \**La consulta del índice de búsqueda se basa en la solicitud y se usa para buscar texto relevante en los documentos indexados. Puedes usar una búsqueda basada en palabras clave que envíe la consulta como texto, pero el uso de una búsqueda basada en vectores puede ser más eficaz; de ahí el uso de un modelo de inserción para vectorizar el texto de consulta antes de enviarlo.*

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

1. Cuando se te solicite, escribe una pregunta, como `Where should I go on vacation to see architecture?` y revisa la respuesta del modelo de IA generativa.

    Ten en cuenta que la respuesta incluye referencias de origen para indicar los datos indexados en los que se encontró la respuesta.

1. Prueba una pregunta de seguimiento, por ejemplo `Where can I stay there?`.

1. Cuando hayas terminado, presiona `quit` para salir del programa. A continuación, cierra el panel de Cloud Shell.

## Limpieza

Para evitar costes innecesarios de Azure y uso de recursos, debe quitar los recursos que implementó en este ejercicio.

1. Si has terminado de explorar Azure AI Foundry, vuelve a [Azure Portal](https://portal.azure.com) en `https://portal.azure.com` e inicia sesión con tus credenciales de Azure si es necesario. A continuación, elimine los recursos del grupo de recursos donde aprovisionó los recursos de Búsqueda de Azure AI y Azure AI.

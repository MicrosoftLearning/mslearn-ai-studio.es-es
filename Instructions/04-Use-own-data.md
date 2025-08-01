---
lab:
  title: Creación de una aplicación de IA generativa que use tus propios datos
  description: Obtén información sobre cómo usar el modelo de generación aumentada de recuperación (RAG) para crear una aplicación de chat que solicite el uso de tus propios datos.
---

# Creación de una aplicación de IA generativa que use tus propios datos

La generación aumentada de recuperación (RAG) es una técnica que se usa para compilar aplicaciones que integran datos de orígenes de datos personalizados en un aviso de un modelo de IA generativa. La RAG es un patrón que se usa habitualmente para desarrollar aplicaciones de IA generativa: aplicaciones basadas en chat que usan un modelo de lenguaje para interpretar entradas y generar respuestas adecuadas.

En este ejercicio, usarás Fundición de IA de Azure para integrar datos personalizados en una solución de IA generativa.

> **Nota**: El código de este ejercicio se basa en software de SDK de la versión preliminar, que podría cambiar. Cuando ha sido necesario, hemos usado versiones específicas de paquetes; que pueden no ser las versiones disponibles más recientes. Puede que se produzcan algunos errores, comportamientos o advertencias inesperados.

Aunque este ejercicio se basa en el SDK de Python de Azure OpenAI, puede desarrollar aplicaciones de chat de IA mediante varios SDK específicos del lenguaje; incluidos los siguientes:

- [OpenAI para Python](https://pypi.org/project/openai/)
- [Azure Open AI para Microsoft .NET](https://www.nuget.org/packages/Azure.AI.OpenAI)
- [Azure OpenAI para TypeScript](https://www.npmjs.com/package/@azure/openai)

Este ejercicio dura aproximadamente **45** minutos.

## Creación de un centro y un proyecto de Fundición de IA de Azure

Las características de Fundición de IA de Azure que usaremos en este ejercicio requieren un proyecto basado en un recurso del *centro* de Fundición de IA de Azure.

1. En un explorador web, abre el [Portal de la Fundición de IA de Azure](https://ai.azure.com) en `https://ai.azure.com` e inicia sesión con tus credenciales de Azure. Cierra las sugerencias o paneles de inicio rápido que se abran la primera vez que inicias sesión y, si es necesario, usa el logotipo de **Fundición de IA de Azure** en la parte superior izquierda para navegar a la página principal, que es similar a la siguiente imagen (cierra el panel **Ayuda** si está abierto):

    ![Captura de pantalla del Portal de la Fundición de IA de Azure.](./media/ai-foundry-home.png)

1. En el explorador, accede a `https://ai.azure.com/managementCenter/allResources` y selecciona **Crear nuevo**. A continuación, elige la opción para crear un nuevo **recurso del centro de IA**.
1. En el asistente para **crear un proyecto**, escribe un nombre válido para tu proyecto y selecciona la opción para crear un centro. A continuación, usa el vínculo **Cambiar nombre del centro** para especificar un nombre válido para el nuevo centro, expande **Opciones avanzadas** y especifica la siguiente configuración para el proyecto:
    - **Suscripción**: *suscripción a Azure*
    - **Grupo de recursos**: *crea o selecciona un grupo de recursos*
    - **Región**: Este de EE. UU. 2 o Centro de Suecia (*En caso de que se alcance un límite de cuota más adelante en el ejercicio, es posible que tengas que crear otro recurso en otra región*).

    > **Nota**: Si trabajas en una suscripción a Azure en la que se usan directivas para restringir los nombres de recursos permitidos, es posible que tengas que usar el vínculo situado en la parte inferior del cuadro de diálogo **Crear un nuevo proyecto** para crear el centro con Azure Portal.

    > **Sugerencia**: si el botón **Crear** sigue deshabilitado, asegúrate de cambiar el nombre del centro a un valor alfanumérico único.

1. Espera a que se cree el proyecto y ve al proyecto.

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
            
            Espera a que se cree el recurso de Búsqueda de IA. A continuación, vuelve a Fundición de IA de Azure y termina de configurar el índice mediante la selección de **Conectar otro recurso de Búsqueda de Azure AI** y agrega una conexión al recurso de Búsqueda de IA que acabas de crear.
 
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

## Creación de una aplicación cliente RAG

Ahora que tienes un índice de trabajo, puedes usar el SDK de la Azure OpenAI para implementar el patrón RAG en una aplicación cliente. Vamos a explorar el código para hacerlo en un ejemplo sencillo.

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

    ```
   cd mslearn-ai-foundry/labfiles/rag-app/python
    ```

1. En el panel de la línea de comandos de Cloud Shell, escribe el siguiente comando para instalar la biblioteca del SDK de OpenAI:

    ```
   python -m venv labenv
   ./labenv/bin/Activate.ps1
   pip install -r requirements.txt openai
    ```

1. Escribe el siguiente comando para editar el archivo de configuración que se ha proporcionado:

    ```
   code .env
    ```

    El archivo se abre en un editor de código.

1. En el archivo de configuración, reemplace los marcadores de posición siguientes: 
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

    ```
   code rag-app.py
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

    ```
   python rag-app.py
    ```

1. Cuando se te solicite, escribe una pregunta, como `Where should I go on vacation to see architecture?` y revisa la respuesta del modelo de IA generativa.

    Ten en cuenta que la respuesta incluye referencias de origen para indicar los datos indexados en los que se encontró la respuesta.

1. Prueba una pregunta de seguimiento, por ejemplo `Where can I stay there?`.

1. Cuando hayas terminado, presiona `quit` para salir del programa. A continuación, cierra el panel de Cloud Shell.

## Limpieza

Para evitar costes innecesarios de Azure y uso de recursos, debe quitar los recursos que implementó en este ejercicio.

1. Si has terminado de explorar Azure AI Foundry, vuelve a [Azure Portal](https://portal.azure.com) en `https://portal.azure.com` e inicia sesión con tus credenciales de Azure si es necesario. A continuación, elimine los recursos del grupo de recursos donde aprovisionó los recursos de Búsqueda de Azure AI y Azure AI.

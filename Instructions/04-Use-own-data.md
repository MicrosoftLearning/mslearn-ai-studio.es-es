---
lab:
  title: Creación de una aplicación de IA generativa que use tus propios datos
  description: Obtén información sobre cómo usar el modelo de generación aumentada de recuperación (RAG) para crear una aplicación de chat que solicite el uso de tus propios datos.
---

# Creación de una aplicación de IA generativa que use tus propios datos

La generación aumentada de recuperación (RAG) es una técnica que se usa para compilar aplicaciones que integran datos de orígenes de datos personalizados en un aviso de un modelo de IA generativa. La RAG es un patrón que se usa habitualmente para desarrollar aplicaciones de IA generativa: aplicaciones basadas en chat que usan un modelo de lenguaje para interpretar entradas y generar respuestas adecuadas.

En este ejercicio, usarás el portal de Azure AI Foundry para integrar datos personalizados en un flujo de consultas de IA generativa.

Este ejercicio dura aproximadamente **45** minutos.

## Creación de un recurso de Búsqueda de Azure AI

Tu solución de aplicación de IA generativa integrará datos personalizados en un flujo de avisos. Para admitir esta integración, necesitarás un recurso de Búsqueda de Azure AI con el que indexar los datos.

1. En un explorador web, abre [Azure Portal](https://portal.azure.com) en `https://portal.azure.com` e inicia sesión con tus credenciales de Azure.
1. En la página principal, selecciona **+ Crear un recurso** y busca `Azure AI Search`. A continuación, crea un nuevo recurso de Búsqueda de Azure AI con la siguiente configuración:

    - **Suscripción**: *selecciona la suscripción a Azure*
    - **Grupo de recursos**: *selecciona o crea un grupo de recursos*
    - **Nombre del servicio**: *escribe un nombre de servicio único*
    - **Ubicación**: *elige de forma **aleatoria** cualquiera de las siguientes regiones*\*
        - Este de Australia
        - Este de Canadá
        - Este de EE. UU.
        - Este de EE. UU. 2
        - Centro de Francia
        - Japón Oriental
        - Centro-Norte de EE. UU
        - Centro de Suecia
        - Suiza 
    - **Plan de tarifa**: estándar

    > \* Más adelante, vas a crear un centro de Azure AI (que incluye una instancia de Azure OpenAI Service) en la misma región que el recurso de Búsqueda de Azure AI. Los recursos de Azure OpenAI están restringidos en el nivel de inquilino por cuotas regionales. Las regiones enumeradas incluyen la cuota predeterminada para los tipos de modelo usados en este ejercicio. Elegir aleatoriamente una región reduce el riesgo de que una sola región alcance su límite de cuota en escenarios en los que se comparte una suscripción con otros usuarios. En caso de que se alcance un límite de cuota más adelante en el ejercicio, es posible que tengas que crear otro centro de Azure AI en otra región.

1. Espera a que se complete la implementación de recursos de Búsqueda de Azure AI.

## Creación de un proyecto de Azure AI

Ahora estás listo para crear un proyecto de Fundición de IA de Azure y los recursos de Azure AI compatibles.

1. En un explorador web, abre el [Portal de la Fundición de IA de Azure](https://ai.azure.com) en `https://ai.azure.com` e inicia sesión con tus credenciales de Azure.
1. En la página principal, selecciona **+Crear proyecto**.
1. En el asistente **Crear un proyecto** podrás ver todos los recursos de Azure que se crearán automáticamente con tu proyecto. Selecciona **Personalizar** y conéctate a tu recurso de Azure AI Search:

    - **Nombre del centro**: *un nombre único*
    - **Suscripción de Azure**: *suscripción a Azure*
    - **Grupo de recursos**: *seleccione el grupo de recursos que contiene el recurso Búsqueda de Azure AI*.
    - **Ubicación**: *la misma ubicación que el recurso de Búsqueda de Azure AI*
    - **Conectar Servicios de Azure AI o Azure OpenAI**: (nuevo) *se rellena automáticamente con el nombre del centro seleccionado*
    - **Conectar Búsqueda de Azure AI**: *selecciona el recurso de Búsqueda de Azure AI*

1. Selecciona **Siguiente** y revisa tu configuración.
1. Selecciona **Crear** y espera a que se complete el proceso.
   
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
      <summary><b>Sugerencia de solución de problemas: error de permisos</b></summary>
        <p>Si recibes un error de permisos al crear un nuevo flujo de avisos, prueba lo siguiente para solucionar los problemas:</p>
        <ul>
          <li>En Azure Portal, selecciona el recurso Servicios de IA.</li>
          <li>En Administración de recursos, en la pestaña Identidad, confirma que se trata de una identidad administrada asignada por el sistema.</li>
          <li>Ve a la cuenta de almacenamiento asociada. En la página IAM, agrega la asignación de roles <em>Lector de datos de Storage Blob</em>.</li>
          <li>En <strong>Asignar acceso a</strong>, elige <strong>Identidad administrada</strong>, <strong>+ Seleccionar miembros</strong>, selecciona <strong>Todas las identidades administradas asignadas por el sistema</strong> y selecciona tu recurso de Servicios de Azure AI.</li>
          <li>Selecciona Revisar y asignar para guardar la nueva configuración y volver a intentar el paso anterior.</li>
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

    Espere a que se inicie el tiempo de ejecución. Esto proporciona un contexto de proceso para el flujo de avisos. Mientras espera, en la pestaña **Flujo**, revise las secciones de las herramientas del flujo.

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

1. Espera a que se inicie la sesión de proceso y, a continuación, en la sección **búsqueda**, establece los siguientes valores de parámetro:

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

## Implementación del flujo

Ahora que tiene un flujo en curso que usa los datos indexados, puede implementarlo como servicio para que lo consuma una aplicación de copilotos.

> **Nota**: según la región y la carga del centro de datos, las implementaciones a veces pueden tardar un tiempo y, a veces, producirán un error al interactuar con la implementación. Pase a la sección de desafíos que aparece a continuación mientras se implementa u omita la prueba de la implementación si dispone de poco tiempo.

1. En la barra de herramientas, seleccione **Implementar**.
1. Cree una implementación con la siguiente configuración:
    - **Configuración básica**:
        - **Punto de conexión**: Nuevo
        - **Nombre del punto de conexión**: *Usar el nombre de punto de conexión único predeterminado*
        - **Nombre de implementación**: *Usar el nombre de punto de conexión de implementación predeterminado*
        - **Máquina virtual**: Standard_DS3_v2
        - **Recuento de instancias**: 3
        - **Recopilación de datos de inferencia**: seleccionado
    - **Configuración avanzada**:
        - *Use la configuración predeterminada*
1. En el portal de Azure AI Foundry, en tu proyecto, en el panel de navegación de la izquierda, en **Mis recursos**, selecciona la página **Modelos + puntos de conexión**.
1. Sigue actualizando la vista hasta que se muestre la implementación **brochure-endpoint-1**, ya que se ha *realizado correctamente* en el punto de conexión **brochure-endpoint** (puede tardar bastante tiempo).
1. Cuando la implementación se haya realizado correctamente, selecciónela. A continuación, en la página **Prueba**, escriba el aviso `What is there to do in San Francisco?` y revise la respuesta.
1. Escriba el aviso `Where else could I go?` y revise la respuesta.
1. Vea la página **Consumir** del punto de conexión y tenga en cuenta que contiene información de la conexión y código de ejemplo que puede usar para compilar una aplicación cliente para el punto de conexión, lo que le permite integrar la solución del flujo de avisos en una aplicación como copiloto personalizado.

## Desafío 

Ahora que has experimentado cómo integrar tus propios datos en una aplicación de Ia generativa creada con el portal de Azure AI Foundry, vamos a explorar más.

Prueba a agregar un nuevo origen de datos a través del portal de Azure AI Foundry, indéxalo e integra los datos indexados en un flujo de solicitudes. Algunos conjuntos de datos que podría probar son:

- Una colección de artículos (de investigación) que tiene en su ordenador.
- Un conjunto de presentaciones de conferencias anteriores.
- Cualquiera de los conjuntos de datos disponibles en el repositorio de [datos de ejemplo de Azure Search](https://github.com/Azure-Samples/azure-search-sample-data).

Use todos los recursos que pueda para crear el origen de datos e integrarlo en el flujo de avisos. Pruebe el nuevo flujo de avisos y envíe avisos a los que solo pueda responder el conjunto de datos que eligió.

## Limpiar

Para evitar costos innecesarios de Azure y uso de recursos, debe quitar los recursos que implementó en este ejercicio.

1. Si has terminado de explorar Azure AI Foundry, vuelve a [Azure Portal](https://portal.azure.com) en `https://portal.azure.com` e inicia sesión con tus credenciales de Azure si es necesario. A continuación, elimine los recursos del grupo de recursos donde aprovisionó los recursos de Búsqueda de Azure AI y Azure AI.

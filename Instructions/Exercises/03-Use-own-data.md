---
lab:
  title: Crear un copiloto personalizado que use sus propios datos
---

# Crear un copiloto personalizado que use sus propios datos

La generación aumentada de recuperación (RAG) es una técnica que se usa para compilar aplicaciones que integran datos de orígenes de datos personalizados en un aviso de un modelo de IA generativa. La RAG es un patrón que se usa habitualmente para desarrollar *copilotos* personalizados: aplicaciones basadas en chat que usan un modelo de lenguaje para interpretar entradas y generar respuestas adecuadas.

En este ejercicio usará Azure AI Studio para integrar datos personalizados en un flujo de avisos de IA generativa.

> **Nota**: Azure AI Studio está en versión preliminar en el momento de escribir este documento y está en desarrollo activo. Es posible que algunos elementos del servicio no sean exactamente como se describen y que algunas características no funcionen según lo previsto.

Este ejercicio dura aproximadamente **45** minutos.

## Creación de un recurso de Búsqueda de Azure AI

La solución de copilotos integrará los datos personalizados en un flujo de avisos. Para admitir esta integración, necesitará un recurso de Azure AI Search con el que indexar los datos.

1. En un explorador web, abra [Azure Portal](https://portal.azure.com) en `https://portal.azure.com` e inicie sesión con sus credenciales de Azure.
1. En la página principal, seleccione **+ Crear un recurso** y busque `Azure AI Search`. A continuación, cree un nuevo recurso de Azure AI Search con la siguiente configuración:

    - **Suscripción**: *Seleccione la suscripción de Azure*
    - **Grupo de recursos**: *seleccione o cree un grupo de recursos*
    - **Nombre del servicio**: *Escriba un nombre de servicio único*
    - **Ubicación**: *Elija de forma **aleatoria** cualquiera de las siguientes regiones*\*
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

    > \* Más adelante, va a crear un centro de Azure AI (que incluye una instancia de Azure OpenAI Service) en la misma región que el recurso de Búsqueda de Azure AI. Los recursos de Azure OpenAI están restringidos en el nivel de inquilino por cuotas regionales. Las regiones enumeradas incluyen la cuota predeterminada para los tipos de modelo usados en este ejercicio. Elegir aleatoriamente una región reduce el riesgo de que una sola región alcance su límite de cuota en escenarios en los que se comparte una suscripción con otros usuarios. En caso de que se alcance un límite de cuota más adelante en el ejercicio, es posible que tenga que crear otro centro de Azure AI en otra región.

1. Espere a que se complete la implementación de recursos de Azure AI Search.

## Crear un proyecto de Azure AI

Ahora ya puede crear un proyecto de Azure AI Studio y los recursos de Azure AI para admitirlo.

1. En un explorador web, abra [Azure AI Studio](https://ai.azure.com) en `https://ai.azure.com` e inicie sesión con sus credenciales de Azure.
1. En la página **Compilar**, seleccione **+Nuevo proyecto**. A continuación, en el asistente **Introducción**, cree un proyecto con la siguiente configuración:

    - **Nombre del proyecto**: *Un nombre exclusivo para el proyecto*
    - **AI Hub**: *Cree un nuevo recurso con la siguiente configuración:*

        - **Nombre del centro de IA**: *un nombre único*
        - **Suscripción de Azure**: *suscripción de Azure*
        - **Grupo de recursos**: *seleccione el grupo de recursos que contiene el recurso Búsqueda de Azure AI*.
        - **Ubicación**: *la misma ubicación que el recurso de Búsqueda de Azure AI*
        - **Azure OpenAI**: (nuevo) *se rellena automáticamente con el nombre del centro seleccionado*.
        - **Azure AI Search**: *Seleccione el recurso de Azure AI Search*

1. Espere a que se cree el proyecto.

## Implementación de modelos

Necesita dos modelos para implementar la solución:

- Un modelo de *inserción* para vectorizar datos de texto para una indexación y procesamiento eficaces.
- Modelo que puede generar respuestas de lenguaje natural a preguntas a partir de sus datos.

1. En Inteligencia artificial de Azure Studio, en el proyecto, en el panel de navegación de la izquierda, en **Componentes**, seleccione la página **Implementaciones**.
1. Cree una nueva implementación (mediante un **punto de conexión en tiempo real**) del modelo **text-embeding-ada-002** con la siguiente configuración:

    - **Nombre de implementación**:`text-embedding-ada-002`
    - **Versión del modelo**: *Valor predeterminado*
    - **Opciones avanzadas**:
        - **Filtro de contenido**: *Valor predeterminado*
        - **Límite de velocidad de tokens por minuto**: `5K`
1. Repite los pasos anteriores para implementar un modelo de **gpt-35-turbo** con el nombre de implementación `gpt-35-turbo`.

    > **Nota**: Reducir los tokens por minuto (TPM) ayuda a evitar el uso excesivo de la cuota disponible en la suscripción que está usando. 5000 TPM es suficiente para los datos que se usan en este ejercicio.

## Adición de datos al proyecto

Los datos del copiloto constan de un conjunto de folletos de viaje en formato PDF de la agencia de viajes ficticia *Margie's Travel*. Vamos a agregarlos al proyecto.

1. Descargue el [archivo comprimido de los folletos](https://github.com/MicrosoftLearning/mslearn-ai-studio/raw/main/data/brochures.zip) desde `https://github.com/MicrosoftLearning/mslearn-ai-studio/raw/main/data/brochures.zip` y extráigalo en una carpeta denominada **folletos** en su sistema de archivos local.
1. En Azure AI Studio, en el proyecto, en el panel de navegación de la izquierda, en **Componentes**, seleccione la página **Datos**.
1. Seleccione **+Nuevos datos**.
1. En el asistente **Agregar datos**, expanda el menú desplegable para seleccionar **Cargar archivos o carpetas**.
1. Seleccione **Cargar carpeta** y seleccione la carpeta **folletos**.
1. Establezca el nombre de los datos en **folletos**.

## Creación de un índice para los datos

Ahora que ha agregado un origen de datos al proyecto, puede usarlo para crear un índice en el recurso de Azure AI Search.

1. En Azure AI Studio, en el proyecto, en el panel de navegación de la izquierda, en **Componentes**, seleccione la página **Índices**.
1. Agregue un nuevo índice con la siguiente configuración:
    - **Datos de origen**:
        - **Origen de datos**: Datos en Azure AI Studio
            - *Seleccione el origen de datos **folletos***
    - **Configuración del índice**:
        - **Seleccione el servicio Azure AI Search**: *Seleccione la conexión de **AzureAISearch** al recurso de Azure AI Search*
        - **Nombre del índice**: folletos-índice
        - **Máquina virtual**: Selección automática
    - **Configuración de búsqueda**:
        - **Configuración de vectores**: Agregación de una búsqueda vectorial a este recurso de búsqueda
        - **Recurso de Azure OpenAI**: Default_AzureOpenAI
        - *Si se le solicita, confirme que se implementará un modelo de inserción si todavía no existe*
        
1. Espera a que se complete el proceso de indexación, lo cual puede tardar varios minutos. La operación de creación de índices consta de los siguientes trabajos:

    - Descifre, fragmente e inserte los tokens de texto en los datos de los folletos.
    - Actualiza Búsqueda de Azure AI con el nuevo índice.
    - Registre el recurso de índice.

## Prueba del índice

Antes de usar el índice en un flujo de avisos basado en RAG, vamos a comprobar que se puede usar para afectar a las respuestas de IA generativa.

1. En el panel de navegación de la izquierda, en **Herramientas**, seleccione la página **Área de juegos**.
1. En la página Área de juegos, en el panel Opciones, asegúrate de que la implementación del modelo **gpt-35-turbo** esté seleccionada. Después, en el panel Sesión de chat, envía el mensaje `Where can I stay in New York?`.
1. Revise la respuesta, que debe ser una respuesta genérica del modelo sin datos procedentes del índice.
1. En el panel Configuración, selecciona la pestaña **Agregar los datos** y, después, agrega el índice del proyecto **brochures-index** y selecciona el tipo de búsqueda **híbrido (vector + palabra clave)**.
1. Después de agregar el índice y de reiniciar la sesión de chat, vuelve a enviar el mensaje `Where can I stay in New York?`.
1. Revise la respuesta, que debe basarse en los datos del índice.

## Uso del índice en un flujo de avisos

El índice vectorial se ha guardado en el proyecto de Azure AI Studio, lo que le permite usarlo fácilmente en un flujo de avisos.

1. En Inteligencia artificial de Azure Studio, en el panel de navegación de la izquierda del proyecto, en **Herramientas**, selecciona la página **Flujo de avisos**.
1. Cree un nuevo flujo de avisos mediante la clonación del ejemplo de **Multi-Round Q&A on Your Data** de la galería. Guarde el clon de este ejemplo en una carpeta denominada `brochure-flow`.
1. Cuando se abra la página del diseñador de flujos de avisos, revise **brochure-flow**. Su grafo debe ser similar a la imagen siguiente:

    ![Captura de pantalla de un grafo de flujo de avisos](./media/chat-flow.png)

    El flujo de avisos de ejemplo que usa implementa la lógica de avisos de una aplicación de chat en la que el usuario puede enviar de forma iterativa la entrada de texto a la interfaz del chat. El historial de conversaciones se conserva y se incluye en el contexto de cada iteración. El flujo de avisos organiza una secuencia de *herramientas* para:

    - Anexe el historial a la entrada de chat para definir un aviso como forma contextualizada de una pregunta.
    - Recupere el contexto mediante el índice y un tipo de consulta de su propia elección en función de la pregunta.
    - Genere el contexto del aviso mediante el uso de los datos recuperados del índice para aumentar la pregunta.
    - Cree variantes de aviso agregando un mensaje del sistema y estructurando el historial de chats.
    - Envíe el aviso a un modelo de lenguaje para generar una respuesta de lenguaje natural.

1. En la lista **Tiempo de ejecución**, seleccione **Iniciar** para iniciar el entorno de ejecución automático.

    Espere a que se inicie el tiempo de ejecución. Esto proporciona un contexto de proceso para el flujo de avisos. Mientras espera, en la pestaña **Flujo**, revise las secciones de las herramientas del flujo.

1. En la sección **Entradas**, asegúrese de que las entradas incluyen:
    - **chat_history**
    - **chat_input**

    El historial de chat predeterminado de este ejemplo incluye alguna conversación sobre IA.

1. En la sección **Salidas**, asegúrese de que la salida incluye:

    - **chat_output** con el valor `${chat_with_context.output}`

1. En la sección **modify_query_with_history**, seleccione la siguiente configuración (dejando las demás como están):

    - **Conexión**: `Default_AzureOpenAI`
    - **Api**: `chat`
    - **deployment_name**: `gpt-35-turbo`
    - **response_format**: `{"type":"text"}`

1. En la sección **lookup**, establezca los siguientes valores de parámetro:

    - **mlindex_content**: *Seleccione el campo vacío para abrir el panel Generar*
        - **index_type**: Índice registrado
        - **mlindex_asset_id**: brochures-index:1
    - **queries**: `${modify_query_with_history.output}`
    - **query_type**: `Hybrid (vector + keyword)`
    - **top_k**: 2

1. En la sección **generate_prompt_context**, revise el script de Python y asegúrese de que las **entradas** de esta herramienta incluyan el parámetro siguiente:

    - **search_result** *(objeto)*: ${lookup.output}

1. En la sección **Prompt_variants**, revise el script de Python y asegúrese de que las **entradas** de esta herramienta incluyan los parámetros siguientes:

    - **contexts** *(string)*: ${generate_prompt_context.output}
    - **chat_history** *(string)*: ${inputs.chat_history}
    - **chat_input** *(string)*: ${inputs.chat_input}

1. En la sección **chat_with_context**, seleccione la siguiente configuración (dejando las demás como están):

    - **Conexión**: Default_AzureOpenAI
    - **API**: Chat
    - **deployment_name**: gpt-35-turbo
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

1. En la barra de herramientas, seleccione **Implementar**.
1. Cree una implementación con la siguiente configuración:
    - **Configuración básica**:
        - **Punto de conexión**: Nuevo
        - **Endpoint name**: `brochure-endpoint`
        - **Nombre de la implementación**: folleto-punto_conexión-1
        - **Máquina virtual**: Standard_DS3_v2
        - **Recuento de instancias**: 3
        - **Recopilación de datos de inferencia**: seleccionado
    - **Configuración avanzada**:
        - *Use la configuración predeterminada*
1. En Azure AI Studio, en el proyecto, en el panel de navegación de la izquierda, en **Componentes**, seleccione la página **Implementaciones**.
1. Sigue actualizando la vista hasta que se muestre la implementación **brochure-endpoint-1**, ya que se ha *realizado correctamente* en el punto de conexión **brochure-endpoint** (puede tardar bastante tiempo).
1. Cuando la implementación se haya realizado correctamente, selecciónela. A continuación, en la página **Prueba**, escriba el aviso `What is there to do in San Francisco?` y revise la respuesta.
1. Escriba el aviso `Where else could I go?` y revise la respuesta.
1. Vea la página **Consumir** del punto de conexión y tenga en cuenta que contiene información de la conexión y código de ejemplo que puede usar para compilar una aplicación cliente para el punto de conexión, lo que le permite integrar la solución del flujo de avisos en una aplicación como copiloto personalizado.

## Desafío 

Ahora que ha experimentado cómo integrar sus propios datos en un copiloto creado con Inteligencia artificial de Azure Studio, vamos a explorar más.

Pruebe a agregar un nuevo origen de datos a través de Inteligencia artificial de Azure Studio, indéxelo e integre los datos indexados en un flujo de avisos. Algunos conjuntos de datos que podría probar son:

- Una colección de artículos (de investigación) que tiene en su ordenador.
- Un conjunto de presentaciones de conferencias anteriores.
- Cualquiera de los conjuntos de datos disponibles en el repositorio de [datos de ejemplo de Azure Search](https://github.com/Azure-Samples/azure-search-sample-data).

Use todos los recursos que pueda para crear el origen de datos e integrarlo en el flujo de avisos. Pruebe el nuevo flujo de avisos y envíe avisos a los que solo pueda responder el conjunto de datos que eligió.

## Limpiar

Para evitar costos innecesarios de Azure y uso de recursos, debe quitar los recursos que implementó en este ejercicio.

1. En Azure AI Studio, vaya a la página **Compilar**. A continuación, seleccione el proyecto que creó en este ejercicio y use el botón **Eliminar proyecto** para quitarlo. Puede tardar unos minutos en eliminar todos los componentes.
1. Si ha terminado de explorar Azure AI Studio, vuelva a [Azure Portal](https://portal.azure.com) en `https://portal.azure.com` e inicie sesión con sus credenciales de Azure si es necesario. A continuación, elimine el grupo de recursos que creó para los recursos de Azure AI Search y Azure AI.

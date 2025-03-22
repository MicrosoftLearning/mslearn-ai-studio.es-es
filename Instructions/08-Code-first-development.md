# Creación de una aplicación de IA generativa mediante herramientas de desarrollo orientado al código

En este ejercicio, clonarás e implementarás una plantilla de Azure Developer CLI que aprovisiona e [implementa el proyecto de IA en un punto de conexión en línea](https://learn.microsoft.com/azure/developer/azure-developer-cli/azure-ai-ml-endpoints?WT.mc_id=academic-140829-cacaste) en Fundición de IA de Azure. A continuación, lo usarás como punto de partida para crear tu propio copiloto personalizado con Azure AI y una experiencia orientada al código.

Este ejercicio dura aproximadamente **90** minutos.

## Antes de comenzar

Para completar este ejercicio, necesitarás lo siguiente:

- Una cuenta de GitHub para bifurcar el repositorio del proyecto y probarlo en un entorno de GitHub Codespaces. Creación de una cuenta gratuita [de GitHub](https://github.com/).
- El nivel básico de la Búsqueda de Azure AI para activar el clasificador semántico. Más información sobre los [detalles de los precios de AI Search](https://azure.microsoft.com/pricing/details/search/).
- Para implementar tres modelos de OpenAI (`gpt-35-turbo`, `gpt-4`, `text-embedding-ada-002`). Para poder implementar los modelos, debes crear el centro de IA en una región con cuota suficiente. Más información sobre la disponibilidad del [modelo por región](https://learn.microsoft.com/azure/ai-services/openai/concepts/models?WT.mc_id=academic-140829-cacaste#model-summary-table-and-region-availability).

## Descripción del escenario

Para empezar a trabajar con la plantilla de proyecto de IA de Azure Developer CLI, ve a la colección [Plantillas de Azure AI con Azure Developer CLI](https://learn.microsoft.com/collections/5pq0uompdgje8d/?WT.mc_id=academic-140829-cacaste). Al explorar la colección, puedes encontrar varios proyectos agrupados por tecnología y casos de uso, incluyendo ejemplos de proyectos multimodal y de varios agentes, proyectos de copiloto y ejemplos que integran diferentes marcos y servicios de Azure.

En este ejercicio, tomarás como punto de partida la plantilla de proyecto **[Copiloto minorista del chat de Contoso con Azure AI Foundry y PromptFlow (Python)](https://aka.ms/contoso-retail-sample)**. Esta plantilla de proyecto es una experiencia orientada a código que usa Prompty y PromptFlow para crear un copilot personalizado (IA de chat) que se puede integrar en el sitio web comercial (interfaz de usuario de chat) de una empresa ficticia denominada Contoso Outdoors.

![Interfaz de usuario/experiencia de usuario del chat de Contoso](./media/contoso_outdoors_website.png)

La solución Copilot minorista usa un patrón de generación aumentada de recuperación (RAG) para fundamentar las respuestas en los datos de producto y de cliente de la empresa. Los clientes pueden hacer preguntas sobre el bot del chat minorista sobre el catálogo de productos de la empresa y también obtener recomendaciones en función de tus compras anteriores.

Al seleccionar el vínculo del proyecto incluido en la colección, se te redirigirá al repositorio de GitHub que hospeda el código de plantilla. El archivo [README.md](https://github.com/Azure-Samples/contoso-chat/blob/main/README.md) del repositorio proporciona una descripción detallada del proyecto, incluyendo la arquitectura, los requisitos previos y los pasos para implementar el proyecto.

![Arquitectura del chat Contoso](./media/contoso_chat_architecture.png)

## Configuración de GitHub Codespaces

En este ejercicio usarás [GitHub Codespaces](https://github.com/features/codespaces), una característica de GitHub que te permite iniciar un [contenedor de desarrollo](https://docs.github.com/codespaces/setting-up-your-project-for-codespaces/adding-a-dev-container-configuration/introduction-to-dev-containers) hospedado en la nube preconfigurado directamente desde el repositorio, con un solo clic. De este modo, puedes empezar a codificar rápidamente sin tener que configurar el entorno de desarrollo local, ya que Codespaces ya tiene todas las herramientas y dependencias necesarias preinstaladas.

Para inicializar el entorno de desarrollo, sigue los siguientes pasos:

1. Ve al repositorio **contoso-chat**: `https://github.com/Azure-Samples/contoso-chat`
1. Selecciona el botón **Bifurcar** en la esquina superior derecha de la página del repositorio de GitHub contoso-chat para crear una copia del repositorio en la cuenta de GitHub.
1. Una vez que tengas el repositorio bifurcado, selecciona el botón **Código** y selecciona **Codespaces**.
1. Selecciona **+** botón para crear un nuevo espacio de código en la rama principal del repositorio bifurcado.

    ![Creación de GitHub Codespaces](./media/create_codespaces.png)

1. En unos segundos, se te redirigirá a una nueva pestaña del explorador donde el entorno de Codespaces está configurado con un [editor de Visual Studio Code asociado](https://code.visualstudio.com/docs/devcontainers/containers) de forma predeterminada.

## Conexión del entorno de VS Code a Azure

El siguiente paso es conectar tu entorno de desarrollo local con la suscripción a Azure donde quieres implementar el proyecto. Comienza abriendo un nuevo terminal en tu entorno de VS Code que se ejecuta en Codespaces.

1. En primer lugar, comprueba que está instalada la [versión más reciente](https://github.com/Azure/azure-dev/releases/tag/azure-dev-cli_1.9.3) de Azure Developer CLI.
    ```bash
    azd version
    ```

1. A continuación, inicia sesión en tu cuenta de Azure desde el terminal de VS Code.

    ```bash
    azd auth login 
    ```

## Aprovisionamiento de recursos de Azure para el proyecto

Una vez que hayas iniciado sesión, estarás listo para empezar a aprovisionar los recursos de Azure para el proyecto en la suscripción. Puedes hacerlo en el mismo terminal de VS Code que has usado para el inicio de sesión.

1. Aprovisiona *e implementa* tu aplicación de IA mediante azd.

    ```bash
    azd up
    ```

1. Deberías ver los siguientes mensajes. Responde usando la guía siguiente:
    - **Escribe un nuevo nombre de entorno:***se usa para crear el nombre de tu grupo de recursos*.
    - **Selecciona una suscripción a Azure para usar**: *selecciona una suscripción que tenga acceso a los modelos de Azure OpenAI*.
    - **Selecciona una ubicación de Azure para usar**: *selecciona una ubicación con cuota de modelo disponible*.

    > Usa la [tabla de resumen del modelo y la disponibilidad de regiones](https://learn.microsoft.com/en-us/azure/ai-services/openai/concepts/models?WT.mc_id=academic-140829-cacaste#model-summary-table-and-region-availability) para encontrar la región que necesita. Por ejemplo, puedes usar `sweden central` como ubicación de Azure, ya que es la región donde están disponibles la mayoría de los modelos de Azure OpenAI.

## Validación del aprovisionamiento mediante Azure Portal

El aprovisionamiento e implementación de una aplicación de IA mediante azd puede tardar 10 minutos o más en completarse. Puedes realizar un seguimiento del progreso mediante:

- Visualización del progreso detallado de en [Azure Portal](https://ms.portal.azure.com/). Busca el grupo de recursos correspondiente al nombre del entorno. Selecciona la opción **Implementaciones** en la barra lateral y después supervisa el estado de implementación de los recursos que se están creando.
- Visita el portal de [Azure AI Foundry](https://ai.azure.com). Inicie sesión con la cuenta de Azure. Busca el centro de IA correspondiente al grupo de recursos anterior (es posible que tengas que actualizar varias veces). Selecciona el proyecto de IA mencionado y después selecciona **Implementaciones** en su barra lateral para realizar un seguimiento del estado de los modelos y las implementaciones de aplicaciones de chat.

Vamos a explorar cómo comprobar el aprovisionamiento de recursos mediante Azure Portal.

1. Ve a [Azure Portal](https://ms.portal.azure.com/) en el explorador.
1. Inicia sesión y busca el grupo de recursos correspondiente al nombre de suscripción y entorno que has especificado anteriormente. El panel **Información general** debe tener este aspecto:

    ![Información general del grupo de recursos de Azure Portal](./media/azure-portal-resource-group.png)

1. Comencemos por comprobar que se han creado los recursos clave de arquitectura [Azure AI Foundry](https://learn.microsoft.com/azure/ai-studio/concepts/architecture). En la ilustración siguiente se proporcionan más detalles sobre lo que proporciona cada uno de estos recursos a nuestra aplicación de IA.

    - **Centro de Azure AI**: recurso de Azure de nivel superior. Proporciona un entorno de colaboración para los equipos.
    - **Proyecto de Azure AI**: elemento secundario del centro. Agrupa los componentes de aplicación para su orquestación, personalización.
    - **Servicios de Azure AI**: administra los puntos de conexión del modelo.

    ![Arquitectura de Azure AI Foundry](./media/resource-provider-connected-resources.svg)

1. A continuación, vamos a comprobar que hemos aprovisionado dos recursos clave para implementar nuestro patrón de diseño de [generación aumentada de recuperación](https://learn.microsoft.com/azure/ai-studio/concepts/retrieval-augmented-generation) almacenando los datos del producto y del cliente para la recuperación controlada por consultas.

    - **Servicio Search**: para administrar los índices de búsqueda de nuestros datos de catálogo de productos.
    - **Cuenta de Azure Cosmos DB**: para crear una base de datos para los datos de pedidos de nuestros clientes.

1. A continuación, podemos comprobar que tenemos recursos complementarios para administrar las necesidades de la aplicación de IA:

    - **Application Insights**: para ser compatible con la supervisión y la telemetría de la aplicación implementada.
    - **Container Registry**: para almacenar y administrar imágenes de Docker usadas en el proyecto de forma privada.
    - **Key Vault**: para almacenar de forma segura los secretos del proyecto (claves, credenciales).
    - **Cuenta de almacenamiento**: para almacenar datos relacionados con la administración de proyectos de IA (incluyendo los registros).
    - **Regla de alertas del detector inteligente**: Anomaly Detector de Application Insights (para solicitudes).

1. Por último, pero no menos importante, observarás un nuevo recurso con el tipo **Implementación en línea de aprendizaje automático**. Este es el recurso correspondiente a nuestro punto de conexión de proyecto de Azure AI implementado (para el Copilot de chat).

## Validación de la implementación mediante Azure AI Foundry

Azure Portal te ayuda a administrar los recursos subyacentes de Azure para el proyecto. El portal de Azure AI Foundry te ayuda a *construir y administrar* los propios proyectos de IA, de un extremo a otro, desde la selección de modelos hasta la implementación de aplicaciones. El comando `azd up` debe haber completado todo el proceso desde el aprovisionamiento de modelos necesarios para implementar y hospedar el punto de conexión de la API de Copilot para su uso. Vamos a comprobar que la aplicación funciona según lo previsto.

1. Visita la página **Administrar** en el portal de [Azure AI Foundry](https://ai.azure.com/manage) para ver todos los centros de Azure AI de tu suscripción.
1. Selecciona el centro del grupo de recursos para ver todos los proyectos de Azure AI que contiene.
1. Selecciona el proyecto de IA predeterminado en el centro y después selecciona **Implementaciones** en el menú de la izquierda.
1. En **Implementaciones de modelos**, comprueba que tienes una conexión de Azure OpenAI, incluyendo las implementaciones de:
    - **gpt-35-turbo**: se usa para la finalización del chat y forma el motor de chat principal.
    - **gpt-4**: se usa para la evaluación de chats, específicamente flujos asistidos por IA.
    - **text-embeding-ada-002**: se usa para vectorización y búsqueda de consultas.
1. Comprueba que tienes un punto de conexión en línea de aprendizaje automático con:
    - **chat-model**: implementación de IA de chat con recurso de punto de conexión *mloe-xxx*.

    ![Implementación de proyectos de Azure AI](./media/azure-ai-project-deployment.png)

## Prueba de la implementación (en la nube) mediante Azure AI Foundry

Para comprobar que el Copilot implementado funciona, usa la capacidad de área de juegos de prueba integrada en el portal de Azure AI Foundry.

![Detalles de la implementación del chat](./media/chat-deployment-details.png)

1. En el portal de Azure AI Foundry, en la lista **Implementaciones de aplicaciones**, selecciona la implementación **chat-deployment-xxxx**.
1. En la página **Detalles** de la aplicación de chat implementada, selecciona la pestaña **Prueba** para obtener la interfaz de prueba.

    Ten en cuenta que la pestaña **Detalles** también tiene valores `Target URI` y `Key` que puedes usar con otras aplicaciones de front-end (por ejemplo, el sitio web Contoso Outdoor) para integrar este asistente de chat para interacciones de usuario reales.

1. Por ahora, prueba la implementación de copiloto con la siguiente **entrada** de prueba:

    ```bash
    {"question": "tell me about your hiking shoes", "customerId": "2", "chat_history": []}
    ```

Debes obtener una respuesta JSON válida en el componente de salida como se muestra.

![Prueba de implementación de chat](./media/chat-deployment-test.png)

## Prueba de la implementación (localmente) mediante Visual Studio Code

El comando **azd up** no solo aprovisiona e implementa la aplicación en Azure, también *configura el entorno local* en Visual Studio Code para admitir el desarrollo local, las pruebas y la iteración. Vamos a echarle un vistazo.

1. Primero, valida que el entorno de VS Code se configuró correctamente. Busca un archivo **config.json** en la carpeta raíz y comprueba que tienes las tres propiedades definidas a continuación, con valores válidos.

    ```json
    {
        "subscription_id": "xxxxxxxxxxxxxxxx",
        "resource_group": "rg-xxxxxx",
        "workspace_name": "ai-project-xxxxxxx"
    }

    ```

1. Comprueba que se creó un archivo **.env** en la carpeta raíz. Debe contener una lista de variables de entorno *con valores rellenados*.

    ```bash
    AZUREAI_HUB_NAME=
    AZUREAI_PROJECT_NAME=
    AZURE_CONTAINER_REGISTRY_ENDPOINT=
    AZURE_CONTAINER_REGISTRY_NAME=
    AZURE_COSMOS_NAME=
    AZURE_ENV_NAME=
    AZURE_KEY_VAULT_ENDPOINT=
    AZURE_KEY_VAULT_NAME=
    AZURE_LOCATION=
    AZURE_OPENAI_API_VERSION=
    AZURE_OPENAI_CHAT_DEPLOYMENT=
    AZURE_OPENAI_ENDPOINT=
    AZURE_OPENAI_NAME=
    AZURE_RESOURCE_GROUP=
    AZURE_SEARCH_ENDPOINT=
    AZURE_SEARCH_NAME=
    AZURE_SUBSCRIPTION_ID=
    AZURE_TENANT_ID=
    COSMOS_ENDPOINT=
    ```

1. Comprueba que tienes instaladas las **herramientas de Promptflow** en el entorno de desarrollo.

    ```bash
    pf version
    ```

1. Usa la herramienta de **prueba de flujo pf** para probar la aplicación de flujo flexible de **contoso_chat** localmente, con la pregunta de ejemplo siguiente. Ten en cuenta la sintaxis del comando para pasar las entradas:

    ```bash
    pf flow test --flow ./contoso_chat --inputs question="tell me about your jackets" customerId="3" chat_history=[]
    ```

Debes recibir una respuesta como esta.

![Salida de aplicación de ejemplo](./media/example_app_output.png)

### Visualización de seguimientos (localmente) mediante Visual Studio Code

1. Puedes realizar un seguimiento de los detalles de la ejecución con la marca `--ui` como se muestra a continuación.

    ```bash
    pf flow test --flow ./contoso_chat --inputs question="tell me about your jackets" customerId="3" chat_history=[] --ui
    ```

Este comando debe iniciar una **vista de seguimiento** en el explorador (en una nueva pestaña) con una tabla que proporcione detalles generales sobre esa ejecución de prueba, incluida la latencia y el uso de tokens.

![fila de prueba pf](./media/pf-flow-test-row.png)

1. Selecciona el registro para expandir a una vista de seguimiento más detallada que te permita inspeccionar los detalles más finos del flujo, desde los datos sin procesar (entrada, salida) hasta los pasos individuales del flujo y los componentes pertinentes (por ejemplo, las plantillas de solicitud usadas para LLM).

![Detalle de prueba pf](./media/pf-flow-test-detail.png)

## Descripción del código base de Chat de Contoso

El back-end de Azure está aprovisionado y listo. El entorno de desarrollo local está configurado y configurado para trabajar con el back-end de Azure. Ahora, todo lo que debes hacer es empezar a modificar el contenido para personalizar y volver a implementar tu propia versión de la aplicación. Echemos un vistazo rápido a cómo se estructura el código base.

> Se trata de una **lista simplificada** del repositorio, lo que elimina algunos archivos y carpetas para mayor claridad.

```bash
data/
    customer_info/  
        create-cosmos-db.ipynb      # Run notebook to upload data to Cosmos DB
        customer_info_1.json        # Example Customer info and orders file
        customer_info_2.json 
        ...
        ...
    product_info/   
        create-azure-search.ipynb   # Run notebook to index product data in AI Search
        products.csv                # Example Products data file

contoso_chat/                       # Main folder for application content
    ai_search.py                    # Search retrieval tool (for RAG design)
    chat.json                       # Example chat file (for Prompty template)
    chat.prompty                    # Chat asset (using Prompty format)
    chat_request.py                 # LLM request tool (for chat completion)
    flow.flex.yaml                  # Promptflow flex flow (define entry point)
    requirements.txt                # App dependencies (define runtime environment)

azure.yaml                          # Main configuration file for Azure Developer CLI  
infra/      
    ai.yaml                         # Define AI model deployments
    app/                            # Infrastructure-as-code config specific to app
    core/                           # Infrastructure-as-code config for core resources
    hooks/                          # Contains post-provisioning scripts
    main.bicep                      # Entry point for Bicep template used by azd
deployment/                         # ai.endpoint config files (named in azure.yaml)
    chat-deployment.yaml 
    chat-model.yaml  
    environment.yaml  

requirements.txt
```

Cuando quieras personalizar el código:

- Si realiza cambios en la aplicación (en `contoso_chat/`) simplemente ejecuta `azd deploy` para volver a implementar la aplicación en el back-end aprovisionado anteriormente. No se requieren pasos adicionales de reaprovisionamiento ni intervención manual.
- Si realizas cambios en los recursos (en la carpeta `infra/`), ejecuta `azd up` para volver a aprovisionar y volver a implementar la aplicación. Debe recoger automáticamente los valores de configuración anteriores de `.azure/` y modificarlos.

## Opcional: personalización y reimplementación del copiloto

Es el momento de crear tu propio copiloto personalizado. Estas son algunas cosas que puedes explorar para probar esto.

Recuerda cada una de estas opciones:

- Usa `azd deploy` para volver a implementar la aplicación si has cambiado solo el código de la aplicación.
- Usa `azd up` para volver a aprovisionar y volver a implementar la aplicación si has cambiado la configuración de recursos.

### Personalización de los datos del historial de pedidos y del cliente

1. Revisa los datos de ejemplo en **data/customer_info** para obtener una idea del esquema predeterminado.
1. Explora el cuaderno **data/create-cosmos-db.ipynb** para obtener un enfoque de código primero en las actualizaciones de datos.
1. **Modifica** los datos de ejemplo y **ejecuta** el cuaderno para cambiar la base de datos predeterminada de Azure CosmosDB.
1. **Vuelve a implementar** la aplicación. Prueba una pregunta de prueba para validar que se devuelven los nuevos datos del cliente.

### Personalización de los datos del catálogo de productos

1. Revisa los datos de ejemplo en **data/product_info/** para obtener una idea del esquema predeterminado.
1. Explora el cuaderno **create-azure-search.ipynb** para obtener un enfoque de código primero para indexar las actualizaciones.
1. **Modifica** los datos de ejemplo y **ejecuta** el cuaderno para cambiar los índices predeterminados de Azure AI Search.
1. **Vuelve a implementar** la aplicación. Prueba una pregunta de prueba para validar que se devuelven los nuevos datos del producto.

### Personalización de la plantilla de solicitud

1. Revisa el archivo **contoso_chat/chat.prompty** para obtener una idea de la plantilla de solicitud predeterminada.
1. Revisa **contoso_chat/chat.json** para comprender el esquema de datos de ejemplo para las pruebas.
1. **Modifica** la plantilla (mensaje del sistema, seguridad, documentación o instrucciones).
1. **Modifica** los datos de ejemplo si es necesario.
1. **Usa** la CLI de Promptflow para probar el flujo localmente con la nueva plantilla de solicitud.
1. **Instala y usa** la extensión Prompty para crear una nueva plantilla de solicitud desde cero.

### Exploración de la automatización de la evaluación y la canalización

Reemplaza el conjunto de datos de prueba usado para ejecutar la canalización de evaluación de aplicaciones a través de Acciones de GitHub por tus propios datos. El conjunto de datos de prueba se encuentra en la carpeta **datos** del proyecto y tiene un formato **.jsonl**.

1. Reemplaza el archivo de conjunto de datos de prueba por tus propios datos.
1. A continuación, ejecuta la canalización de evaluación insertando los cambios en la rama principal del repositorio bifurcado.

    La canalización de evaluación se ejecutará automáticamente y puedes comprobar los resultados en la pestaña Acciones de GitHub del repositorio.

1. Puedes personalizar la canalización de evaluación modificando el archivo **evaluate.yaml** en la carpeta **.github/workflows** del proyecto y el script **evaluations_chat.py** en la carpeta **evaluations**.

## Limpieza de recursos y eliminación de recursos de Azure

Este proyecto usa modelos y servicios (por ejemplo, Azure AI Search) que pueden incurrir en costes no triviales si se deja ejecutar a largo plazo. Cuando termines de explorar Azure AI Studio, debes eliminar los recursos que has creado para evitar costes innecesarios de Azure. Para ello, ejecuta el siguiente comando en el terminal VS Code: 

```bash
azd down
```

Esto no solo invierte los pasos realizados para aprovisionar e implementar la aplicación, sino que también realiza pasos adicionales para *purgar* los recursos que, de lo contrario, se pueden mantener en estado de "eliminación temporal", lo que afecta a la capacidad de reutilizar nombres de recursos o reclamar la cuota del modelo. **Este comando te pedirá estas acciones durante el apagado, así que asegúrate de que respondes correctamente**.

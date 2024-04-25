---
lab:
  title: Introducción al flujo de avisos en Inteligencia artificial de Azure Studio.
---

# Introducción al flujo de avisos en Inteligencia artificial de Azure Studio.

La extracción de información valiosa del texto se conoce como Reconocimiento de entidades con nombre (NER). Las entidades son palabras clave que le interesan en un texto determinado.

![Extracción de entidades](./media/get-started-prompt-flow-use-case.gif)

Los modelos de lenguaje grande (LLM) se pueden usar para realizar NER. Para crear una aplicación que tome un texto como entidades de entrada y salida, puede crear un flujo que use un nodo LLM con flujo de solicitud.

En este ejercicio, usará el flujo de mensajes de Azure AI Studio para crear una aplicación LLM que espera un tipo de entidad y un texto como entrada. Llama a un modelo GPT de Azure OpenAI a través de un nodo LLM para extraer la entidad necesaria del texto especificado, limpia el resultado y genera las entidades extraídas.

![Información general del ejercicio](./media/get-started-lab.png)

En primer lugar, debe crear un proyecto en Azure AI Studio para crear los recursos de Azure necesarios. A continuación, puede implementar un modelo GPT con el servicio Azure OpenAI. Una vez que tenga los recursos necesarios, puede crear el flujo. Por último, ejecutará el flujo para probarlo y ver la salida de ejemplo.

## Creación de un proyecto en Azure AI Studio

Para empezar, cree un proyecto de Azure AI Studio y una instancia de Azure AI Hub para admitirlo.

1. En un explorador web, abra [https://ai.azure.com](https://ai.azure.com) e inicie sesión con sus credenciales de Azure.
1. Seleccione la página **Compilar** y, a continuación, seleccione **+ Nuevo proyecto**.
1. En el asistente para **Crear un nuevo proyecto**, cree un proyecto con la siguiente configuración:
    - **Nombre del proyecto**: *Un nombre exclusivo para el proyecto*
    - **Menú central de Azure**: *Crear un nuevo recurso con la siguiente configuración:*
        - **Nombre del centro de IA**: *un nombre único*
        - **Suscripción**: *suscripción de Azure*
        - **Grupo de recursos**: *Un nuevo grupo de recursos*
        - **Ubicación**: *Elija de forma **aleatoria** cualquiera de las siguientes regiones*\*
        - Este de Australia
        - Este de Canadá
        - Este de EE. UU.
        - Este de EE. UU. 2
        - Centro de Francia
        - Japón Oriental
        - Centro-Norte de EE. UU
        - Centro de Suecia
        - Norte de Suiza
        - Sur de Reino Unido 2

    > \* Los recursos de Azure OpenAI están restringidos en el nivel de inquilino por cuotas regionales. Las regiones enumeradas incluyen la cuota predeterminada para los tipos de modelo usados en este ejercicio. Elegir aleatoriamente una región reduce el riesgo de que una sola región alcance su límite de cuota en escenarios en los que se comparte una suscripción con otros usuarios. En caso de que se alcance un límite de cuota más adelante en el ejercicio, es posible que tenga que crear otro recurso en otra región.

1. Revise la configuración y cree el proyecto.
1. Espere entre 5 y 10 minutos para que se cree el proyecto.

## Implementación de un modelo GPT

Para usar un modelo LLM en el flujo de solicitud, primero debe implementar un modelo. Azure AI Studio permite implementar modelos de OpenAI que puede usar en los flujos.

1. En el panel de navegación de la izquierda, en **Componentes**, seleccione la página **Implementaciones**.
1. En Azure OpenAI Studio, vaya a la página **Implementaciones**.
1. Cree una nueva implementación del modelo de **gpt-35-turbo** con la siguiente configuración:
    - **Modelo**: `gpt-35-turbo`
    - **Versión del modelo**: *Deje el valor predeterminado*
    - **Nombre de implementación**:`gpt-35-turbo`
    - Establezca las opciones **avanzadas** para usar el filtro de contenido predeterminado y para restringir los tokens por minuto (TPM) a **5K**.

Ahora que ha implementado el modelo LLM, puede crear un flujo en Azure AI Studio que llame al modelo implementado.

## Creación y ejecución de un flujo en Azure AI Studio

Ahora que tiene todos los recursos necesarios aprovisionados, puede crear un flujo.

### Crear un flujo nuevo

Para crear un nuevo flujo con una plantilla, puede seleccionar uno de los tipos de flujos que desea desarrollar.

1. En el panel de navegación de la izquierda, en **Herramientas**, seleccione **Flujo de mensajes**.
1. Seleccione **+ Crear** para crear un nuevo flujo.
1. Cree un nuevo **flujo estándar** y escriba `entity-recognition` como nombre de carpeta.

Se crea un flujo estándar con una entrada, dos nodos y una salida. Actualizará el flujo para tomar dos entradas, extraer entidades, limpiar la salida del nodo LLM y devolver las entidades como salida.

### Iniciar el entorno de ejecución automático

Para probar el flujo, necesita proceso. El proceso necesario está disponible a través del entorno de ejecución.

1. Después de crear el nuevo flujo denominado `entity-recognition`, el flujo debe abrirse en Studio.
1. Seleccione el campo **Seleccionar tiempo de ejecución** en la barra superior.
1. En la lista **Tiempo de ejecución automático**, seleccione **Iniciar** para iniciar el entorno de ejecución automático.
1. Espere a que se inicie el tiempo de ejecución.

### Configuración de las entradas

El flujo que creará tomará dos entradas: un texto y el tipo de entidad que desea extraer del texto.

1. En **Entradas**, se configura una entrada denominada `topic` de tipo `string`. Cambie la entrada existente y actualice con la siguiente configuración:
    - **Nombre**: `entity_type`
    - **Tipo**: `string`
    - **Valor**: `job title`
1. Seleccione **Agregar entrada**.
1. Configure la segunda entrada para que tenga las siguientes opciones:
    - **Nombre**: `text`
    - **Tipo**: `string`
    - **Valor**: `The software engineer is working on a new update for the application.`

### Configuración del nodo LLM

El flujo estándar ya incluye un nodo que usa la herramienta LLM. Puede encontrar el nodo en la información general del flujo. El aviso predeterminado solicita una broma. Actualizará el nodo LLM para extraer entidades en función de las dos entradas especificadas en la sección anterior.

1. Vaya al **nodo LLM** denominado `joke`.
1. Reemplace el nombre por `NER_LLM`
1. Para **Conexión**, seleccione la conexión `Default_AzureOpenAI`.
1. Para **deployment_name**, seleccione el modelo `gpt-35-turbo` que implementó.
1. Reemplace el campo de solicitud por el código siguiente:

   ```yml
   {% raw %}
   system:

   Your task is to find entities of a certain type from the given text content.
   If there're multiple entities, please return them all with comma separated, e.g. "entity1, entity2, entity3".
   You should only return the entity list, nothing else.
   If there's no such entity, please return "None".

   user:
   
   Entity type: {{entity_type}}
   Text content: {{text}}
   Entities:
   {% endraw %}
   ```

1. Seleccione **Validar y analizar la entrada**.
1. En el nodo LLM, en la sección **Entradas**, configure lo siguiente:
    - Para `entity_type`, seleccione el valor `${inputs.entity_type}`.
    - Para `text`, seleccione el valor `${inputs.text}`.

El nodo LLM tomará ahora el tipo de entidad y el texto como entradas, lo incluirá en la solicitud que haya especificado y enviará la solicitud al modelo implementado.

### Configuración del nodo de Python

Para extraer solo la información clave del resultado del modelo, puede usar la herramienta Python para limpiar la salida del nodo LLM.

1. Vaya al nodo de Python denominado `echo`.
1. Reemplace el nombre por `cleansing`.
1. Reemplace el código  por el siguiente:

   ```python
   from typing import List
   from promptflow import tool
    
    
   @tool
   def cleansing(entities_str: str) -> List[str]:
       # Split, remove leading and trailing spaces/tabs/dots
       parts = entities_str.split(",")
       cleaned_parts = [part.strip(" \t.\"") for part in parts]
       entities = [part for part in cleaned_parts if len(part) > 0]
       return entities
    
   ```

1. Seleccione **Validar y analizar la entrada**.
1. En el nodo Python, en la sección **Entradas**, establezca el valor de `entities_str` en `${NER_LLM.output}`.

### Configuración de la salida

Por último, puede configurar la salida de todo el flujo. Solo desea una salida al flujo, que debe ser las entidades extraídas.

1. Vaya a la **Salidas del flujo**.
1. En **Nombre**, escriba `entities`.
1. En **Valor**, seleccione `${cleansing.output}`.

### Ejecutar el flujo

Ahora que ha desarrollado el flujo, puede ejecutarlo para probarlo. Dado que ha agregado valores predeterminados a las entradas, puede probar fácilmente el flujo en Studio.

1. Seleccione **Ejecutar** para probar la función.
1. Espere hasta que se complete la ejecución.
1. Seleccione **Ver salidas**. Debería aparecer un elemento emergente en el que se muestra la salida de las entradas predeterminadas. Opcionalmente, también puede inspeccionar los registros.

## Eliminación de recursos de Azure

Cuando termine de explorar Azure AI Studio, debe eliminar los recursos que ha creado para evitar costos innecesarios de Azure.

- Vaya a [Azure Portal](https://portal.azure.com) en `https://portal.azure.com`.
- En Azure Portal, en la página **Inicio**, seleccione **Grupos de recursos**.
- Seleccione el grupo de recursos que creó para este ejercicio.
- En la parte superior de la página **Información general** del grupo de recursos, seleccione **Eliminar grupo de recursos**.
- Escriba el nombre del grupo de recursos para confirmar que quiere eliminarlo y seleccione **Eliminar**.

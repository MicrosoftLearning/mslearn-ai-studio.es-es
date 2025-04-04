---
lab:
  title: Elección e implementación de un modelo de lenguaje
  description: Las aplicaciones de IA generativa se basan en uno o varios modelos de lenguaje. Obtén información sobre cómo buscar y seleccionar los modelos adecuados para el proyecto de IA generativa.
---

# Elección e implementación de un modelo de lenguaje

El catálogo de modelos de Fundición de IA de Azure se usa como repositorio central donde puedes explorar y usar diversos modelos, lo que facilita la creación de tu escenario de IA generativa.

En este ejercicio, explorarás el catálogo de modelos en el Portal de la Fundición de IA de Azure y compararás los modelos potenciales de una aplicación de IA generativa que ayuda a resolver problemas.

Este ejercicio dura aproximadamente **25** minutos.

## Creación de un centro y un proyecto de Azure AI

Un centro de Azure AI proporciona un área de trabajo de colaboración en la que puedes definir uno o varios *proyectos*. Vamos a crear un proyecto y un centro de Azure AI.

1. En un explorador web, abre el [Portal de la Fundición de IA de Azure](https://ai.azure.com) en `https://ai.azure.com` e inicia sesión con tus credenciales de Azure.

1. En la página principal, selecciona **+Crear proyecto**.
1. En el Asistente para **crear un proyecto**, escribe un nombre de proyecto adecuado (como `my-ai-project`) y revisa los recursos de Azure que se crearán automáticamente para admitir el proyecto.
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

## Configuración de la implementación del servicio de inferencia de Azure AI

Hay varias opciones para implementar modelos en el Portal de la Fundición de IA de Azure. En este ejercicio, usarás la opción de implementación de **inferencia del modelo de Azure AI**, que admite modelos de *Azure OpenAI* y *Modelo como servicio* del catálogo de modelos de la Fundición de IA de Azure. Dado que todos los modelos se implementan en un punto de conexión común hospedado por el recurso de Servicios de Azure AI, es fácil cambiar entre modelos al probarlos para comparar el comportamiento y el rendimiento.

1. En la barra de herramientas de la parte superior derecha de la página del proyecto de la Fundición de IA de Azure, usa el icono **Características de versión preliminar** (📣) para mostrar las características de versión preliminar.
1. Comprueba que la característica **Implementación de modelos en el servicio de inferencia del modelo de Azure AI** está habilitada. A continuación, cierrs el panel **Características de versión preliminar**.

## Revisión de detalles y bancos de pruebas de modelos

Para ayudarte a elegir un modelo, puedes explorar descripciones y bancos de pruebas de modelos para determinar qué modelo se adapta mejor a tus necesidades.

1. En el portal del proyecto la Fundición de IA de Azure, en el panel de navegación de la izquierda, selecciona **Catálogo de modelo**.
1. En la página principal del catálogo de modelos, busca `gpt-4` para encontrar el modelo de finalización de chat **gpt-4**.

    ![Captura de pantalla de una búsqueda de "gpt-4" en el catálogo de modelos.](./media/model-catalog-search-gpt4.png)

1. Selecciona el modelo **gpt-4** para ver sus detalles. Lee la descripción y revisa la otra información disponible en la página.

    ![Captura de pantalla de la página de detalles del modelo gpt-4.](./media/gpt4-details.png)

1. En la página **gpt-4**, ve la pestaña **Bancos de pruebas** para ver cómo se compara el modelo entre algunos bancos de pruebas de rendimiento estándar con otros modelos que se usan en escenarios similares.

    ![Captura de pantalla de la página de bancos de pruebas de modelos gpt-4.](./media/gpt4-benchmarks.png)

1. Usa la flecha atrás (**&larr;**) junto al título de página **gpt-4** para volver a la página principal del catálogo de modelos.
1. En el catálogo de modelos, busca `Phi-3.5-mini-instruct` y ve los detalles y los bancos de pruebas del modelo **Phi-3.5-mini-instruct**.

## Comparación de modelos

Has revisado dos modelos diferentes, ambos podrían usarse para implementar una aplicación de chat de IA generativa. Ahora vamos a comparar las métricas de estos dos modelos visualmente.

1. Vuelve a la página principal del **catálogo de modelo**.
1. Selecciona **Comparar modelos**. Se muestra un gráfico visual para la comparación de modelos con una selección de modelos comunes.

    ![Captura de pantalla de la página de comparación de modelos.](./media/compare-models.png)

1. En el panel **Modelos para comparar** de la izquierda, ten en cuenta que puedes seleccionar tareas populares, como *respuesta a preguntas* para seleccionar automáticamente modelos usados para tareas específicas.
1. Usa el icono **Borrar todos los modelos** (&#128465;) para eliminar todos los modelos seleccionados previamente.
1. Usa el botón **+ Modelo para comparar** para agregar el modelo **gpt-4** a la lista. A continuación, usa el mismo botón para agregar el modelo **Phi-3.5-mini-instruct** a la lista.
1. Revisa el gráfico, que compara los modelos basados en el **índice de calidad** (una puntuación estandarizada que indica la calidad del modelo) y el **coste**. Para ver los valores específicos de un modelo, mantén el mouse sobre el punto que lo representa en el gráfico.

    ![Captura de pantalla del gráfico de comparación de modelos para gpt-4 y Phi-3.5-mini-instruct.](./media/comparison-chart.png)

1. En el menú desplegable **eje X**, en **Calidad**, selecciona las siguientes métricas y observa cada gráfico resultante antes de pasar al siguiente:
    - Precisión
    - Coherencia
    - Fluidez
    - Relevancia

## Implementación de modelos

Ahora que has explorado las opciones a través de pruebas comparativas de modelos, ya tienes todo listo para implementar modelos de lenguaje. Puedes examinar el catálogo de modelos e implementar desde allí, o bien puedes implementar un modelo a través de la página **Implementaciones**. Exploremos ambas opciones.

### Implementación de un modelo del *Catálogo de modelos*

Comencemos implementando un modelo del Catálogo de modelos. Es posible que prefieras esta opción cuando desees revisar diversos modelos disponibles.

1. Vuelve a la página principal del **catálogo de modelo**.
1. Busca y selecciona el modelo `gpt-4`, tal como hiciste anteriormente.
1. En la página **gpt-4**, selecciona **Implementar** e implementa el modelo con la siguiente configuración mediante la selección de **Personalizar** en los detalles de implementación:
    - **Nombre de implementación**: *nombre único para la implementación de modelo, por ejemplo `gpt-4-model`*
    - **Tipo de implementación**: estándar global
    - **Versión del modelo**: *selecciona la versión predeterminada*
    - **Recurso de IA conectado**: *tu conexión de recursos de Azure OpenAI*
    - **Límite de frecuencia de tokens por minuto (miles)**: 5000
    - **Filtro de contenido**: DefaultV2
      
    > **Nota**: Reducir el TPM ayuda a evitar el uso excesivo de la cuota disponible en la suscripción que está usando. 5000 TPM es suficiente para los datos que se usan en este ejercicio.

1. Espera a que el **estado de aprovisionamiento** de la implementación sea **Correcto**.

### Implementación de un modelo a través de *Modelos + puntos de conexión*

Si ya sabes exactamente qué modelo quieres implementar, es posible que prefieras hacerlo a través de **Modelos + puntos de conexión**.

1. En el panel de navegación de la izquierda, en la sección **Mis recursos**, selecciona **Modelos + puntos de conexión**.
1. En la pestaña **Implementaciones de modelos**, en la lista desplegable **+ Implementar modelo**, selecciona **Implementar modelo base**. A continuación, busca `Phi-3.5-mini-instruct` y confirma tu selección.
1. Acepta la licencia del modelo.
1. Implementa un modelo **Phi-3.5-mini-instruct** con la siguiente configuración:
    - **Nombre de implementación**: *nombre único para la implementación de modelo, por ejemplo `phi-35-model`*
    - **Tipo de implementación**: estándar global
    - **Detalles de implementación**: *usa la configuración predeterminada*

1. Espera a que el **estado de aprovisionamiento** de la implementación sea **Correcto**.

## Prueba de modelos en el área de juegos de chat

Ahora que tienes dos modelos que comparar, veamos cómo se comportan en una interacción conversacional.

### Preparación para chatear

1. En la barra de navegación, selecciona **Áreas de juegos**. A continuación, selecciona el **Área de juegos de chat**.
1. En el panel **Configuración**, en el campo **Dar las instrucciones del modelo y el contexto**, establece el aviso del sistema en `You are an AI assistant that helps solve problems.`
1. Selecciona **Aplicar cambios**.

### Chatear con el modelo *gpt-4*

En el panel **Configuración**, selecciona el modelo *gpt-4*.
1. En la ventana de chat, escribe la consulta siguiente

    ```
    I have a fox, a chicken, and a bag of grain that I need to take over a river in a boat. I can only take one thing at a time. If I leave the chicken and the grain unattended, the chicken will eat the grain. If I leave the fox and the chicken unattended, the fox will eat the chicken. How can I get all three things across the river without anything being eaten?
    ```

1. Visualice la respuesta. Después, escribe la siguiente consulta de seguimiento:

    ```
    Explain your reasoning.
    ```

### Chatear con el modelo *Phi-3.5*

1. En el panel **Configuración**, selecciona el modelo *Phi-3.5*.
1. Asegúrate de que se inicia una nueva sesión de chat antes de repetir las mismas indicaciones que usaste anteriormente para probar el modelo gpt-4.
1. En la ventana de chat, escribe la consulta siguiente

    ```
    I have a fox, a chicken, and a bag of grain that I need to take over a river in a boat. I can only take one thing at a time. If I leave the chicken and the grain unattended, the chicken will eat the grain. If I leave the fox and the chicken unattended, the fox will eat the chicken. How can I get all three things across the river without anything being eaten?
    ```

1. Visualice la respuesta. Después, escribe la siguiente consulta de seguimiento:

    ```
    Explain your reasoning.
    ```

### Realizar una comparación adicional

1. Prueba el siguiente rompecabezas con ambos modelos, pidiendo a los modelos que expliquen su razonamiento (la respuesta correcta es 40!):

    ```
    I have 53 socks in my drawer: 21 identical blue, 15 identical black and 17 identical red. The lights are out, and it is completely dark. How many socks must I take out to make 100 percent certain I have at least one pair of black socks?
    ```

## Reflexión sobre los modelos

Has comparado dos modelos, que pueden variar en términos de su capacidad para generar respuestas adecuadas y en su coste. En cualquier escenario generativo, debes encontrar un modelo con el equilibrio adecuado de idoneidad para la tarea que necesitas que realice y el coste de usar el modelo para el número de solicitudes que esperas que tenga que controlar.

Los detalles y bancos de pruebas proporcionados en el catálogo de modelos, junto con la capacidad de comparar modelos visualmente proporcionan un punto de partida útil al identificar modelos candidatos para una solución de IA generativa. A continuación, puedes probar modelos candidatos con una variedad de mensajes del sistema y del usuario en el área de juegos de chat.

## Limpieza

Si has terminado de explorar el Portal de la Fundición de IA de Azure, deberías eliminar los recursos que has creado en este ejercicio para evitar incurrir en costes innecesarios de Azure.

1. Abre [Azure Portal](https://portal.azure.com) y ve el contenido del grupo de recursos donde implementaste los recursos usados en este ejercicio.
1. Selecciona **Eliminar grupo de recursos** en la barra de herramientas.
1. Escribe el nombre del grupo de recursos y confirma que deseas eliminarlo.

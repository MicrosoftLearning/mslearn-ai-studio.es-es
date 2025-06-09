---
lab:
  title: Elección e implementación de un modelo de lenguaje
  description: Las aplicaciones de IA generativa se basan en uno o varios modelos de lenguaje. Obtén información sobre cómo buscar y seleccionar los modelos adecuados para el proyecto de IA generativa.
---

# Elección e implementación de un modelo de lenguaje

El catálogo de modelos de Fundición de IA de Azure se usa como repositorio central donde puedes explorar y usar diversos modelos, lo que facilita la creación de tu escenario de IA generativa.

En este ejercicio, explorarás el catálogo de modelos en el Portal de la Fundición de IA de Azure y compararás los modelos potenciales de una aplicación de IA generativa que ayuda a resolver problemas.

Este ejercicio dura aproximadamente **25** minutos.

> **Nota**: algunas de las tecnologías que se usan en este ejercicio se encuentran en versión preliminar o en desarrollo activo. Puede que se produzcan algunos comportamientos, advertencias o errores inesperados.

## Exploración de modelos

Vamos a comenzar iniciando sesión en el Portal de la Fundición de IA de Azure y explorando algunos de los modelos disponibles.

1. En un explorador web, abre el [Portal de la Fundición de IA de Azure](https://ai.azure.com) en `https://ai.azure.com` e inicia sesión con tus credenciales de Azure. Cierra las sugerencias o paneles de inicio rápido que se abran la primera vez que inicias sesión y, si es necesario, usa el logotipo de **Fundición de IA de Azure** en la parte superior izquierda para navegar a la página principal, que es similar a la siguiente imagen (cierra el panel **Ayuda** si está abierto):

    ![Captura de pantalla del Portal de la Fundición de IA de Azure.](./media/ai-foundry-home.png)

1. Revisa la información en la página principal.
1. En la página principal, en la sección **Explorar modelos y funcionalidades**, busca el modelo `gpt-4o`, que usaremos en nuestro proyecto.
1. En los resultados de la búsqueda, selecciona el modelo **gpt-4o** para ver sus detalles.
1. Lee la descripción y revisa la otra información disponible en la pestaña **Detalles**.

    ![Captura de pantalla de la página de detalles del modelo gpt-4o.](./media/gpt4-details.png)

1. En la página **gpt-4o**, visualiza la pestaña **Bancos de pruebas** para ver cómo se compara el modelo entre algunos bancos de pruebas de rendimiento estándar con otros modelos que se usan en escenarios similares.

    ![Captura de pantalla de la página de bancos de pruebas de modelos gpt-4o.](./media/gpt4-benchmarks.png)

1. Usa la flecha atrás (**&larr;**) junto al título de página **gpt-4o** para volver al catálogo de modelos.
1. Busca `Phi-3.5-mini-instruct` y visualiza los detalles y los bancos de pruebas del modelo **Phi-3.5-mini-instruct**.

## Comparación de modelos

Has revisado dos modelos diferentes, ambos podrían usarse para implementar una aplicación de chat de IA generativa. Ahora vamos a comparar las métricas de estos dos modelos visualmente.

1. Usa la flecha atrás (**&larr;**) para volver al catálogo de modelos.
1. Selecciona **Comparar modelos**. Se muestra un gráfico visual para la comparación de modelos con una selección de modelos comunes.

    ![Captura de pantalla de la página de comparación de modelos.](./media/compare-models.png)

1. En el panel **Modelos para comparar**, ten en cuenta que puedes seleccionar tareas populares, como *respuesta a preguntas* para seleccionar automáticamente modelos usados para tareas específicas.
1. Usa el icono **Borrar todos los modelos** (&#128465;) para eliminar todos los modelos seleccionados previamente.
1. Usa el botón **+ Modelo para comparar** para agregar el modelo **gpt-4o** a la lista. A continuación, usa el mismo botón para agregar el modelo **Phi-3.5-mini-instruct** a la lista.
1. Revisa el gráfico, que compara los modelos basados en el **índice de calidad** (una puntuación estandarizada que indica la calidad del modelo) y el **coste**. Para ver los valores específicos de un modelo, mantén el mouse sobre el punto que lo representa en el gráfico.

    ![Captura de pantalla del gráfico de comparación de modelos para gpt-4o y Phi-3.5-mini-instruct.](./media/comparison-chart.png)

1. En el menú desplegable **eje X**, en **Calidad**, selecciona las siguientes métricas y observa cada gráfico resultante antes de pasar al siguiente:
    - Precisión
    - Coherencia
    - Fluidez
    - Relevancia

    En función de los bancos de pruebas, el modelo gpt-4o parece ofrecer el mejor rendimiento general, pero a un coste mayor.

1. En la lista de modelos que se van a comparar, selecciona el modelo **gpt-4o** para volver a abrir su página de banco de pruebas.
1. En la página del modelo **gpt-4o**, selecciona la pestaña **Información general** para ver los detalles del modelo.

## Creación de un proyecto de Fundición de IA de Azure

Para usar un modelo, debes crear un *proyecto* de Fundición de IA de Azure.

1. En la parte superior de la página de información general del modelo **gpt-4o**, selecciona **Usar este modelo**.
1. Cuando se te pida que crees un proyecto, escribe un nombre válido para el proyecto y expande **Opciones avanzadas**.
1. En la sección **Opciones avanzadas**, especifica la siguiente configuración para el centro:
    - **Recurso de Fundición de IA de Azure**: *un nombre válido para el recurso de Fundición de IA de Azure*
    - **Suscripción**: *suscripción a Azure*
    - **Grupo de recursos**: *crea o selecciona un grupo de recursos*
    - **Región**: *selecciona cualquier ubicación compatible con los servicios de IA***\*

    > \* Algunos de los recursos de Azure AI están restringidos por cuotas de modelo regionales. En caso de que se alcance un límite de cuota más adelante en el ejercicio, es posible que tengas que crear otro recurso en otra región.

1. Selecciona **Crear** y espera a que se cree el proyecto, incluida la implementación del modelo gpt-4 que seleccionaste.
1. Cuando se cree el proyecto, el área de juegos de chat se abrirá automáticamente para que puedas probar el modelo:

    ![Captura de pantalla del área de juegos de chat en Fundición de IA de Azure.](./media/ai-foundry-chat-playground.png)

## Chatear con el modelo *gpt-4o*

Ahora que tienes una implementación del modelo, puedes usar el área de juegos para probarlo.

1. En el área de juegos de chat, en el panel **Configuración**, asegúrate de que el modelo **gpt-4o** esté seleccionado y, en el campo **Proporcionar las instrucciones del modelo y el contexto**, establece el aviso del sistema en `You are an AI assistant that helps solve problems.`
1. Selecciona **Aplicar cambios** para actualizar el aviso del sistema.

1. En la ventana de chat, escribe la consulta siguiente

    ```
   I have a fox, a chicken, and a bag of grain that I need to take over a river in a boat. I can only take one thing at a time. If I leave the chicken and the grain unattended, the chicken will eat the grain. If I leave the fox and the chicken unattended, the fox will eat the chicken. How can I get all three things across the river without anything being eaten?
    ```

1. Visualiza la respuesta. Después, escribe la siguiente consulta de seguimiento:

    ```
   Explain your reasoning.
    ```

## Implementación de otro modelo

Al crear el proyecto, el modelo **gpt-4o** seleccionado se implementó automáticamente. Vamos a implementar el modelo ***Phi-3.5-mini-instruct** que también has considerado.

1. En el panel de navegación de la izquierda, en la sección **Mis recursos**, selecciona **Modelos + puntos de conexión**.
1. En la pestaña **Implementaciones de modelos**, en la lista desplegable **+ Implementar modelo**, selecciona **Implementar modelo base**. A continuación, busca `Phi-3.5-mini-instruct` y confirma tu selección.
1. Acepta la licencia del modelo.
1. Implementa un modelo **Phi-3.5-mini-instruct** con la siguiente configuración:
    - **Nombre de implementación**: *un nombre válido para la implementación de modelo*
    - **Tipo de implementación**: estándar global
    - **Detalles de implementación**: *usa la configuración predeterminada*

1. Espera a que la implementación se complete.

## Chatear con el modelo *Phi-3.5*

Ahora vamos a chatear con el nuevo modelo en el área de juegos.

1. En la barra de navegación, selecciona **Áreas de juegos**. A continuación, selecciona el **Área de juegos de chat**.
1. En el área de juegos de chat, en el panel **Configuración**, asegúrate de que el modelo **Phi-3.5-mini-instruct** esté seleccionado y, en el campo **Proporcionar las instrucciones del modelo y el contexto**, establece el aviso del sistema en `You are an AI assistant that helps solve problems.` (el mismo aviso del sistema que usaste para probar el modelo gpt-4o).
1. Selecciona **Aplicar cambios** para actualizar el aviso del sistema.
1. Asegúrate de que se inicia una nueva sesión de chat antes de repetir las mismas indicaciones que usaste anteriormente para probar el modelo gpt-4.
1. En la ventana de chat, escribe la consulta siguiente

    ```
   I have a fox, a chicken, and a bag of grain that I need to take over a river in a boat. I can only take one thing at a time. If I leave the chicken and the grain unattended, the chicken will eat the grain. If I leave the fox and the chicken unattended, the fox will eat the chicken. How can I get all three things across the river without anything being eaten?
    ```

1. Visualiza la respuesta. Después, escribe la siguiente consulta de seguimiento:

    ```
   Explain your reasoning.
    ```

## Realizar una comparación adicional

1. Usa la lista desplegable del panel **Configuración** para cambiar alternar entre los modelos, probando ambos modelos con el siguiente rompecabezas (la respuesta correcta es 40!):

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

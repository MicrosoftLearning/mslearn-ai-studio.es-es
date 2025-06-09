---
lab:
  title: Evaluación del rendimiento del modelo de IA generativa
  description: Aprende a evaluar modelos e indicaciones para optimizar el rendimiento de la aplicación de chat y su capacidad de responder adecuadamente.
---

# Evaluación del rendimiento del modelo de IA generativa

En este ejercicio, usarás evaluaciones manuales y automatizadas para evaluar el rendimiento de un modelo en el Portal de la Fundición de IA de Azure.

Este ejercicio dura aproximadamente **30** minutos.

> **Nota**: Algunas de las tecnologías que se usan en este ejercicio se encuentran en versión preliminar o en desarrollo activo. Puede que se produzcan algunos comportamientos, advertencias o errores inesperados.

## Creación de un centro y un proyecto de Fundición de IA de Azure

Las características de Fundición de IA de Azure que usaremos en este ejercicio requieren un proyecto basado en un recurso del *centro* de Fundición de IA de Azure.

1. En un explorador web, abre el [Portal de la Fundición de IA de Azure](https://ai.azure.com) en `https://ai.azure.com` e inicia sesión con tus credenciales de Azure. Cierra las sugerencias o paneles de inicio rápido que se abran la primera vez que inicias sesión y, si es necesario, usa el logotipo de **Fundición de IA de Azure** en la parte superior izquierda para navegar a la página principal, que es similar a la siguiente imagen (cierra el panel **Ayuda** si está abierto):

    ![Captura de pantalla del Portal de la Fundición de IA de Azure.](./media/ai-foundry-home.png)

1. En el explorador, ve a `https://ai.azure.com/managementCenter/allResources` y selecciona **Crear**. A continuación, elige la opción para crear un nuevo **recurso del centro de IA**.
1. En el asistente para **crear un proyecto**, escribe un nombre válido para el proyecto y si se te sugiere un centro existente, selecciona la opción para crear uno nuevo y expande **Opciones avanzadas** para especificar la siguiente configuración para el proyecto:
    - **Suscripción**: *suscripción a Azure*
    - **Grupo de recursos**: *crea o selecciona un grupo de recursos*
    - **Nombre del centro**: un nombre válido para el centro
    - **Ubicación**: *selecciona una de las ubicaciones siguientes*:\*
        - Este de EE. UU. 2
        - Centro de Francia
        - Sur de Reino Unido 2
        - Centro de Suecia

    > \* En el momento de redactar este documento, estas regiones admiten la evaluación de las métricas de seguridad de la inteligencia artificial. La disponibilidad del modelo está restringida por cuotas regionales. En caso de que se alcance un límite de cuota más adelante en el ejercicio, es posible que tengas que crear otro proyecto en otra región.

1. Espere a que se cree el proyecto.

## Implementación de modelos

En este ejercicio, evaluarás el rendimiento de un modelo gpt-4o-mini. También usarás un modelo gpt-4o para generar métricas de evaluación asistida por IA.

1. En el panel de navegación izquierdo de tu proyecto, en la sección **Mis recursos**, selecciona la página **Modelos y puntos de conexión**.
1. En la página **Modelos y puntos de conexión**, en la pestaña **Implementaciones de modelos**, en el menú **+ Implementar modelo**, selecciona **Implementar modelo base**.
1. Busca el modelo **gpt-4o** en la lista, selecciona y confirma.
1. Implementa el modelo con la siguiente configuración mediante la selección de **Personalizar** en los detalles de implementación:
    - **Nombre de implementación**: *nombre válido para la implementación de modelo*
    - **Tipo de implementación**: estándar global
    - **Actualización automática de la versión**: habilitado
    - **** Versión del modelo: *selecciona la versión disponible más reciente*
    - **Recurso de IA conectado**: *selecciona tu conexión de recursos de Azure OpenAI*
    - **Límite de velocidad de tokens por minuto (miles):** 50 000 *(o el máximo disponible en la suscripción si es inferior a 50 000)*
    - **Filtro de contenido**: DefaultV2

    > **Nota**: reducir el TPM ayuda a evitar el uso excesivo de la cuota disponible en la suscripción que está usando. 50 000 TPM es suficiente para los datos que se usan en este ejercicio. Si la cuota disponible es inferior a esta, podrás completar el ejercicio, pero se pueden producir errores si se supera el límite de velocidad.

1. Espera a que la implementación se complete.
1. Vuelve a la página **Modelos y puntos de conexión** y repite los pasos anteriores para implementar un modelo **gpt-4o-mini** con la misma configuración.

## Evaluación manual de un modelo

Puedes revisar manualmente las respuestas del modelo en función de los datos de prueba. La revisión manual te permite probar entradas diferentes para evaluar si el modelo funciona según lo previsto.

1. En una nueva pestaña del explorador, descarga el archivo [travel_evaluation_data.jsonl](https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/refs/heads/main/data/travel_evaluation_data.jsonl) de `https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/refs/heads/main/data/travel_evaluation_data.jsonl` y guárdalo en una carpeta local como **travel_evaluation_data.jsonl** (asegúrate de guardarlo como archivo .jsonl, no como archivo .txt).
1. De nuevo en la pestaña Portal de la Fundición de IA de Azure, en el panel de navegación, en la sección **Protección y gobernanza**, selecciona **Evaluación**.
1. Si el panel **Crear una nueva evaluación** se abre automáticamente, selecciona **Cancelar** para cerrarlo.
1. En la página **Evaluación**, visualiza la pestaña **Evaluaciones manuales** y selecciona **+ Nueva evaluación manual**.
1. En la sección **Configuraciones**, en la lista **Modelo**, selecciona la implementación de tu modelo **gpt-4o**.
1. Cambia el **mensaje del sistema** a las siguientes instrucciones para un asistente de viajes de IA:

   ```
   Assist users with travel-related inquiries, offering tips, advice, and recommendations as a knowledgeable travel agent.
   ```

1. En la sección **Resultado de evaluación manual**, selecciona **Importar datos de prueba** y carga el archivo **travel_evaluation_data.jsonl** que descargaste anteriormente; desplázate hacia abajo para asignar los campos del conjunto de datos de la siguiente manera:
    - **Entrada**: pregunta
    - **Respuesta esperada**: ExpectedResponse
1. Revisa las preguntas y las respuestas esperadas en el archivo de prueba: las usarás para evaluar las respuestas que genera el modelo.
1. Selecciona **Ejecutar** en la barra superior a fin de generar salidas para todas las preguntas que has agregado como entradas. Después de unos minutos, las respuestas del modelo deben mostrarse en una nueva columna **Salida**, de la siguiente manera:

    ![Captura de pantalla de la página de evaluación manual en el Portal de la Fundición de IA de Azure.](./media/manual-evaluation.png)

1. Revisa las salidas de cada pregunta, compara la salida del modelo con la respuesta esperada y "puntúa" los resultados con el icono de pulgar hacia arriba o hacia abajo en la parte inferior derecha de cada respuesta.
1. Después de puntuar las respuestas, revisa los iconos de resumen situados encima de la lista. A continuación, en la barra de herramientas, selecciona **Guardar resultados** y asigna un nombre adecuado. Guardar los resultados te permite recuperarlos más adelante para su posterior evaluación o comparación con un modelo diferente.

## Uso de evaluación automatizada

Aunque comparar manualmente la salida del modelo con tus propias respuestas esperadas puede ser una manera útil de evaluar el rendimiento de un modelo, es un enfoque lento en escenarios en los que se espera una amplia gama de preguntas y respuestas; y proporciona poco en la forma de métricas estandarizadas que puedes usar para comparar diferentes combinaciones de modelos y mensajes.

La evaluación automatizada es un enfoque que intenta abordar estas deficiencias mediante el cálculo de métricas y el uso de la inteligencia artificial para evaluar las respuestas de coherencia, relevancia y otros factores.

1. Usa la flecha atrás (**&larr;**) situada junto al título de página **Evaluación manual** para volver a la página **Evaluación**.
1. Visualiza la pestaña **Evaluaciones automatizadas**.
1. Selecciona **Crear una nueva evaluación** y, cuando se te solicite, selecciona la opción para **Evaluar un modelo** y selecciona **Siguiente**.
1. En la página **Seleccionar origen de datos**, selecciona **Usar el conjunto de datos** y selecciona el conjunto de datos **travel_evaluation_data_jsonl_*xxxx...*** basado en el archivo que cargaste anteriormente y selecciona **Siguiente**.
1. En la página **Probar el modelo**, selecciona el modelo **gpt-4o-mini** y cambia el **mensaje del sistema** a las mismas instrucciones del asistente de viajes de IA que usaste anteriormente:

   ```
   Assist users with travel-related inquiries, offering tips, advice, and recommendations as a knowledgeable travel agent.
   ```

1. Para el campo **Consulta**, selecciona **\{\{item.question\}\}**.
1. Seleccione **Siguiente** para ir a la página siguiente.
1. En la página **Configurar evaluadores**, usa el botón **+Agregar** para agregar los siguientes evaluadores, configurando cada uno de ellos de la siguiente manera:
    - **Puntuador de modelos**:
        - **Nombre de criterios**: Semantic_similarity
        - **Calificar con**: *selecciona el modelo **gpt-4o***
        - **Configuración del usuario** (en la parte inferior):


            Salida: \{\{sample.output_text\}\}<br>
            Verdad fundamental: \{\{item. ExpectedResponse\}\}<br>
            <br>
        
    - **Evaluador de escala de Likert**:
        - **Nombre de criterios**: relevancia
        - **Calificar con**: *selecciona el modelo **gpt-4o***
        - **Consulta**: \{\{item.question\}\}

    - **Similitud de texto**:
        - **Nombre de criterios**: F1_Score
        - **Verdad fundamental**: \{\{item.ExpectedResponse\}\}

    - **Contenido injusto y de odio**:
        - **Nombre de criterios**: Hate_and_unfairness
        - **Consulta**: \{\{item.question\}\}

1. Selecciona **Siguiente** y revisa la configuración de la evaluación. Debes haber configurado la evaluación para usar el conjunto de datos de evaluación de viajes para evaluar el modelo **gpt-4o-mini** para la similitud semántica, relevancia, puntuación F1 y lenguaje de odio e injusto.
1. Asigna un nombre adecuado a la evaluación y selecciona el campo **Enviar** para iniciar el proceso de evaluación y espera a que se complete. Esto puede tardar unos minutos. Puedes usar el botón **Actualizar** de la barra de herramientas para comprobar su estado.

1. Cuando se haya completado la evaluación, desplázate hacia abajo si es necesario para revisar los resultados.

    ![Captura de pantalla de las métricas de evaluación.](./media/ai-quality-metrics.png)

1. Desplázate a la parte superior de la página y selecciona la pestaña **Datos** para ver los datos sin procesar de la evaluación. Los datos incluyen las métricas de cada entrada, así como explicaciones del razonamiento del modelo gpt-4o aplicado al evaluar las respuestas.

## Limpieza

Cuando termines de explorar Azure AI Foundry, debes eliminar los recursos que has creado para evitar costes innecesarios de Azure.

- Ve a [Azure Portal](https://portal.azure.com) en `https://portal.azure.com`.
- En Azure Portal, en la página **Inicio**, selecciona **Grupos de recursos**.
- Selecciona el grupo de recursos que creaste para este ejercicio.
- En la parte superior de la página **Información general** del grupo de recursos, selecciona **Eliminar grupo de recursos**.
- Escribe el nombre del grupo de recursos para confirmar que quieres eliminarlo y selecciona **Eliminar**.

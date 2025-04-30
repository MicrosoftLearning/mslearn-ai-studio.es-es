---
lab:
  title: Evaluación del rendimiento del modelo de IA generativa
  description: Aprende a evaluar modelos e indicaciones para optimizar el rendimiento de la aplicación de chat y su capacidad de responder adecuadamente.
---

# Evaluación del rendimiento del modelo de IA generativa

En este ejercicio, usarás evaluaciones manuales y automatizadas para evaluar el rendimiento de un modelo en el Portal de la Fundición de IA de Azure.

Este ejercicio dura aproximadamente **30** minutos.

> **Nota**: Algunas de las tecnologías que se usan en este ejercicio se encuentran en versión preliminar o en desarrollo activo. Puede que se produzcan algunos comportamientos, advertencias o errores inesperados.

## Creación de un proyecto de Fundición de IA de Azure

Comencemos creando un proyecto de Fundición de IA de Azure.

1. En un explorador web, abre el [Portal de la Fundición de IA de Azure](https://ai.azure.com) en `https://ai.azure.com` e inicia sesión con tus credenciales de Azure. Cierra las sugerencias o paneles de inicio rápido que se abran la primera vez que inicias sesión y, si es necesario, usa el logotipo de **Fundición de IA de Azure** en la parte superior izquierda para navegar a la página principal, que es similar a la siguiente imagen (cierra el panel **Ayuda** si está abierto):

    ![Captura de pantalla del Portal de la Fundición de IA de Azure.](./media/ai-foundry-home.png)

1. En la página principal, selecciona **+Crear proyecto**.
1. En el asistente para **crear un proyecto**, escribe un nombre válido y si se te sugiere un centro existente, elige la opción para crear uno nuevo. A continuación, revisa los recursos de Azure que se crearán automáticamente para admitir el centro y el proyecto.
1. Selecciona **Personalizar** y especifica la siguiente configuración para el centro:
    - **Nombre del centro**: *proporciona un nombre para el centro*.
    - **Suscripción**: *suscripción a Azure*
    - **Grupo de recursos**: *crea o selecciona un grupo de recursos*.
    - **Ubicación**: selecciona una de las siguientes regiones\*
        - Este de EE. UU. 2
        - Centro de Francia
        - Sur de Reino Unido 2
        - Centro de Suecia
    - **Conectar Servicios de Azure AI o Azure OpenAI**: *Crear un nuevo servicio de IA*
    - **Conectar Búsqueda de Azure AI**: omite la conexión

    > \* En el momento de redactar este documento, estas regiones admiten la evaluación de las métricas de seguridad de la inteligencia artificial. La disponibilidad del modelo está restringida por cuotas regionales. En caso de que se alcance un límite de cuota más adelante en el ejercicio, es posible que tengas que crear otro proyecto en otra región.

1. Selecciona **Siguiente** y revisa tu configuración. Luego, selecciona **Crear** y espera a que se complete el proceso.
1. Cuando se cree el proyecto, cierra las sugerencias que se muestran y revisa la página del proyecto en el Portal de la Fundición de IA de Azure, que debe tener un aspecto similar a la siguiente imagen:

    ![Captura de pantalla de los detalles de un proyecto de Azure AI en el Portal de la Fundición de IA de Azure.](./media/ai-foundry-project.png)

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

## Evaluar manualmente un modelo

Puedes revisar manualmente las respuestas del modelo en función de los datos de prueba. La revisión manual te permite probar entradas diferentes para evaluar si el modelo funciona según lo previsto.

1. En una nueva pestaña del explorador, descarga el archivo [travel_evaluation_data.csv](https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/refs/heads/main/data/travel_evaluation_data.csv) desde `https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/refs/heads/main/data/travel_evaluation_data.csv` y guárdalo en una carpeta local.
1. De nuevo en la pestaña Portal de la Fundición de IA de Azure, en el panel de navegación, en la sección **Evaluar y mejorar**, selecciona **Evaluación**.
1. En la página **Evaluación**, visualiza la pestaña **Evaluaciones manuales** y selecciona **+ Nueva evaluación manual**.
1. Cambia el **mensaje del sistema** a las siguientes instrucciones para un asistente de viajes de IA:

   ```
   Objective: Assist users with travel-related inquiries, offering tips, advice, and recommendations as a knowledgeable travel agent.

   Capabilities:
   - Provide up-to-date travel information, including destinations, accommodations, transportation, and local attractions.
   - Offer personalized travel suggestions based on user preferences, budget, and travel dates.
   - Share tips on packing, safety, and navigating travel disruptions.
   - Help with itinerary planning, including optimal routes and must-see landmarks.
   - Answer common travel questions and provide solutions to potential travel issues.
    
   Instructions:
   1. Engage with the user in a friendly and professional manner, as a travel agent would.
   2. Use available resources to provide accurate and relevant travel information.
   3. Tailor responses to the user's specific travel needs and interests.
   4. Ensure recommendations are practical and consider the user's safety and comfort.
   5. Encourage the user to ask follow-up questions for further assistance.
   ```

1. En la sección **Configuraciones**, en la lista **Modelo**, selecciona la implementación de tu modelo **gpt-4o-mini**.
1. En la sección **Resultado de evaluación manual**, selecciona **Importar datos de prueba** y carga el archivo **travel_evaluation_data.csv** que descargaste anteriormente; asigna los campos del conjunto de datos de la siguiente manera:
    - **Entrada**: pregunta
    - **Respuesta esperada**: ExpectedResponse
1. Revisa las preguntas y las respuestas esperadas en el archivo de prueba: las usarás para evaluar las respuestas que genera el modelo.
1. Selecciona **Ejecutar** en la barra superior a fin de generar salidas para todas las preguntas que has agregado como entradas. Después de unos minutos, las respuestas del modelo deben mostrarse en una nueva columna **Salida** de la siguiente manera:

    ![Captura de pantalla de la página de evaluación manual en el Portal de la Fundición de IA de Azure.](./media/manual-evaluation.png)

1. Revisa los resultados de cada pregunta, comparándolos con la respuesta esperada y "puntúa" los resultados con el icono de pulgar hacia arriba o hacia abajo en la parte inferior derecha de cada respuesta.
1. Después de puntuar las respuestas, revisa los iconos de resumen situados encima de la lista. Luego, en la barra de herramientas, selecciona **Guardar resultados** y asígnales un nombre adecuado. Guardar los resultados te permite recuperarlos más adelante para su posterior evaluación o comparación con un modelo diferente.

## Uso de evaluación automatizada

Aunque comparar manualmente los resultados de un modelo con tus propias respuestas previstas puede ser una manera útil de evaluar el rendimiento de un modelo, es un enfoque lento en escenarios en los que se espera una amplia gama de preguntas y respuestas, y no ofrece mucho en materia de métricas estandarizadas que puedes usar para comparar diferentes combinaciones de modelos y mensajes.

La evaluación automatizada es un enfoque que intenta abordar estas deficiencias mediante el cálculo de métricas y el uso de la inteligencia artificial para evaluar las respuestas de coherencia, relevancia y otros factores.

1. Usa la flecha atrás (**&larr;**) situada junto al título de página **Evaluación manual** para volver a la página **Evaluación**.
1. Visualiza la pestaña **Evaluaciones automatizadas**.
1. Selecciona **Crear una nueva evaluación** y, cuando se te solicite, selecciona la opción para evaluar un **Modelo e indicación**
1. En la página **Crear una nueva evaluación**, en la sección **Información básica**, revisa el nombre de evaluación predeterminado generado automáticamente (puedes cambiarlo si quieres) y selecciona la implementación de modelo **gpt-40-mini**.
1. Cambia el **mensaje del sistema** a las mismas instrucciones para un asistente de viajes de IA que usaste anteriormente:

   ```
   Objective: Assist users with travel-related inquiries, offering tips, advice, and recommendations as a knowledgeable travel agent.

   Capabilities:
   - Provide up-to-date travel information, including destinations, accommodations, transportation, and local attractions.
   - Offer personalized travel suggestions based on user preferences, budget, and travel dates.
   - Share tips on packing, safety, and navigating travel disruptions.
   - Help with itinerary planning, including optimal routes and must-see landmarks.
   - Answer common travel questions and provide solutions to potential travel issues.
    
   Instructions:
   1. Engage with the user in a friendly and professional manner, as a travel agent would.
   2. Use available resources to provide accurate and relevant travel information.
   3. Tailor responses to the user's specific travel needs and interests.
   4. Ensure recommendations are practical and consider the user's safety and comfort.
   5. Encourage the user to ask follow-up questions for further assistance.
   ```

1. En la sección **Configurar datos de prueba**, ten en cuenta que puedes usar un modelo GPT para generar datos de prueba automáticamente (que puedes editar y complementar para que coincidan con tus propias expectativas), usar un conjunto de datos existente o cargar un archivo. En este ejercicio, selecciona **Usar el conjunto de datos existente** y luego selecciona el conjunto de datos **travel_evaluation_data_csv_*xxxx...*** (que se creó al cargar el archivo .csv anteriormente).
1. Revisa las filas de ejemplo del conjunto de datos y luego, en la sección **Elegir la columna de datos**, selecciona las siguientes asignaciones de columnas:
    - **Consulta**: pregunta
    - **Contexto**: *deja este espacio en blanco. Se usa para evaluar la "base" al asociar un origen de datos contextual con el modelo.*
    - **Verdad fundamental**: ExpectedAnswer
1. En la sección **Elegir lo que deseas evaluar**, selecciona <u>todas</u> las siguientes categorías de evaluación:
    - Calidad de IA (asistida por IA)
    - Riesgo y seguridad (asistido por IA)
    - Calidad de IA (NLP)
1. En la lista **Elegir una implementación de modelo como juez**, selecciona el modelo **gpt-4o**. Este modelo se usará para evaluar las respuestas del modelo ***gpt-4o-mini** para las métricas de comparación de IA generativa y de calidad relacionadas con el lenguaje.
1. Selecciona **Crear** para iniciar el proceso de evaluación y espera a que se complete. Esto puede tardar unos minutos.

    > **Sugerencia**: si se produce un error que indica que los permisos del proyecto se han establecido, espera un minuto y luego vuelve a seleccionar **Crear**. Los permisos de recursos para un proyecto recién creado pueden tardar algún tiempo en propagarse.

1. Cuando se haya completado la evaluación, desplázate hacia abajo si es necesario para ver el área **panel de Métricas** y ver las métricas de **Calidad de IA (asistida por IA)**:

    ![Captura de pantalla de métrica de calidad de IA en el Portal de la Fundición de IA de Azure.](./media/ai-quality-metrics.png)

    Usa los iconos **<sup>(i) </sup>** para ver las definiciones de métricas.

1. Visualiza la pestaña **Riesgo y seguridad** para ver las métricas asociadas al contenido potencialmente perjudicial.
1. Consulta la pestaña **Calidad de IA (NLP**) para ver las métricas estándar para los modelos de IA generativa.
1. Desplázate hacia atrás hasta la parte superior de la página si es necesario y selecciona la pestaña **Datos** para ver los datos sin procesar de la evaluación. Los datos incluyen las métricas de cada entrada, así como explicaciones del razonamiento del modelo gpt-4o aplicado al evaluar las respuestas.

    ![Captura de pantalla de los datos de evaluación en el Portal de la Fundición de IA de Azure.](./media/evaluation-data.png)

## Limpieza

Cuando termines de explorar Azure AI Foundry, debes eliminar los recursos que has creado para evitar costes innecesarios de Azure.

- Ve a [Azure Portal](https://portal.azure.com) en `https://portal.azure.com`.
- En Azure Portal, en la página **Inicio**, selecciona **Grupos de recursos**.
- Selecciona el grupo de recursos que creaste para este ejercicio.
- En la parte superior de la página **Información general** del grupo de recursos, selecciona **Eliminar grupo de recursos**.
- Escribe el nombre del grupo de recursos para confirmar que quieres eliminarlo y selecciona **Eliminar**.

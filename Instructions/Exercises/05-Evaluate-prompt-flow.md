---
lab:
  title: Evaluación de aplicaciones de lenguaje en Inteligencia artificial de Azure Studio
---

# Evaluación de aplicaciones de lenguaje en Inteligencia artificial de Azure Studio

En este ejercicio, explorará las evaluaciones integradas y personalizadas para evaluar y comparar el rendimiento de las aplicaciones de IA con Inteligencia artificial de Azure Studio.

> Para completar este ejercicio, la suscripción de Azure debe estar aprobada para el acceso al servicio Azure OpenAI. Rellene el [formulario de registro](https://learn.microsoft.com/legal/cognitive-services/openai/limited-access) para solicitar acceso a los modelos de Azure OpenAI.

Para crear un copiloto con el flujo de avisos, necesita hacer lo siguiente:

- Crear un centro de inteligencia artificial y un proyecto en Inteligencia artificial de Azure Studio.
- Implementar un modelo GPT.
- Evaluar un conjunto de datos de prueba con las métricas integradas.
- Definir una métrica de evaluación personalizada y ejecutarla en el conjunto de datos de prueba.

## Crear un centro de inteligencia artificial y un proyecto en Inteligencia artificial de Azure Studio.

Para empezar, crea un proyecto de Inteligencia artificial de Azure Studio en un concentrador de Azure AI:

1. En un explorador web, abre [https://ai.azure.com](https://ai.azure.com) e inicia sesión con tus credenciales de Azure.
1. Selecciona la página **Inicio** y luego **+ Nuevo proyecto**.
1. En el asistente para **Crear un nuevo proyecto**, crea un proyecto con la siguiente configuración:
    - **Nombre del proyecto**: *un nombre exclusivo para el proyecto*
    - **Concentrador**: *crear un centro con la siguiente configuración:*
        - **Nombre del centro**: *un nombre único*
        - **Suscripción**: *suscripción de Azure*
        - **Grupo de recursos**: *un nuevo grupo de recursos*
        - **Ubicación**: *elige de forma **aleatoria** cualquiera de las siguientes regiones*\*
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
    - **Conectar Servicios de Azure AI o Azure OpenAI**: *Crear una conexión*
    - **Conectar Búsqueda de Azure AI**: omitir la conexión

    > \* Los recursos de Azure OpenAI están restringidos en el nivel de inquilino por cuotas regionales. Las regiones enumeradas incluyen la cuota predeterminada para los tipos de modelo usados en este ejercicio. Elegir aleatoriamente una región reduce el riesgo de que una sola región alcance su límite de cuota. En caso de que se alcance un límite de cuota más adelante en el ejercicio, es posible que tengas que crear otro recurso en otra región. Más información sobre la [disponibilidad del modelo por región](https://learn.microsoft.com/azure/ai-services/openai/concepts/models#gpt-35-turbo-model-availability)

1. Revise la configuración y cree el proyecto.
1. Espera entre 5 y 10 minutos para que se cree el proyecto.

## Implementación de un modelo GPT

Para usar un modelo de lenguaje en el flujo de avisos, primero debe implementar un modelo. Azure AI Studio permite implementar modelos de OpenAI que puede usar en los flujos.

1. En el panel de navegación de la izquierda, en **Componentes**, seleccione la página **Implementaciones**.
1. Cree una nueva implementación del modelo de **gpt-35-turbo** con la siguiente configuración:
    - **Nombre de implementación**: *Un nombre único para la implementación de modelo*
    - **Versión del modelo**: *Seleccione la versión predeterminada*
    - **Tipo de implementación**: Estándar
    - **Recurso de Azure OpenAI conectado**: *Seleccione la conexión predeterminada*
    - **Límite de frecuencia de tokens por minuto (miles)**: 5000
    - **Filtro de contenido**: valor predeterminado
1. Espere a que se implemente el modelo. Cuando la implementación esté lista, seleccione **Abrir en el área de juegos**.
1. Cambie el **Mensaje del sistema** a lo siguiente:

   ```
   **Objective**: Assist users with travel-related inquiries, offering tips, advice, and recommendations as a knowledgeable travel agent.

   **Capabilities**:
   - Provide up-to-date travel information, including destinations, accommodations, transportation, and local attractions.
   - Offer personalized travel suggestions based on user preferences, budget, and travel dates.
   - Share tips on packing, safety, and navigating travel disruptions.
   - Help with itinerary planning, including optimal routes and must-see landmarks.
   - Answer common travel questions and provide solutions to potential travel issues.
    
   **Instructions**:
   1. Engage with the user in a friendly and professional manner, as a travel agent would.
   2. Use available resources to provide accurate and relevant travel information.
   3. Tailor responses to the user's specific travel needs and interests.
   4. Ensure recommendations are practical and consider the user's safety and comfort.
   5. Encourage the user to ask follow-up questions for further assistance.
   ```

1. Seleccione **Aplicar cambios**.
1. En la ventana de chat, escriba la consulta: `What can you do?` para comprobar que el modelo de lenguaje se comporta según lo previsto.

Ahora que tiene un modelo implementado con un mensaje del sistema actualizado, puede evaluar el modelo.

## Evaluación de un modelo de lenguaje en Inteligencia artificial de Azure Studio

Puede revisar manualmente las respuestas del modelo en función de los datos de prueba. La revisión manual permite probar entradas diferentes de una en una para evaluar si el modelo funciona según lo previsto.

1. En la barra superior del **Área de juegos de chat**, seleccione **Evaluar**.
1. Se abre una nueva ventana con el mensaje del sistema anterior ya rellenado y el modelo implementado seleccionado.
1. En la sección **Resultado de la evaluación manual**, agregará cinco entradas para las que revisará la salida. Escriba las cinco preguntas siguientes como cinco **Entradas** independientes:

   `Can you provide a list of the top-rated budget hotels in Rome?`

   `I'm looking for a vegan-friendly restaurant in New York City. Can you help?`

   `Can you suggest a 7-day itinerary for a family vacation in Orlando, Florida?`

   `Can you help me plan a surprise honeymoon trip to the Maldives?`

   `Are there any guided tours available for the Great Wall of China?`

1. Seleccione **Ejecutar** en la barra superior a fin de generar salidas para todas las preguntas que ha agregado como entradas.
1. Ahora puede revisar manualmente las salidas de cada pregunta seleccionando la opción del pulgar hacia arriba o hacia abajo en la parte inferior derecha de una respuesta. Evalúe cada respuesta, asegurándose de incluir al menos una respuesta de pulgar hacia arriba y hacia abajo en las clasificaciones.
1. Seleccione **Guardar resultados** en la barra superior. Escriba `manual_evaluation_results` como nombre de los resultados.
1. Vaya a **Evaluaciones** mediante el menú de la izquierda.
1. Seleccione la pestaña **Evaluaciones manuales** para encontrar las evaluaciones manuales que acaba de guardar. Tenga en cuenta que puede explorar las evaluaciones manuales creadas anteriormente, continuar donde lo dejó y guardar las evaluaciones actualizadas.
1. Seleccione la pestaña **Evaluaciones de métricas** y cree una evaluación con la siguiente configuración:
    - **Nombre de evaluación**: *Escriba un nombre único*
    - **¿Qué tipo de escenario está evaluando?**: preguntas y respuestas sin contexto.
    - **Seleccione los datos que quiera evaluar**: agregue el conjunto de datos.
        - Descargue el archivo JSONL https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/main/data/travel-qa.jsonl y cárguelo en la interfaz de usuario.
    - **Seleccionar métricas**: coherencia, fluidez
    - **Conexión**: *tu conexión de Servicios de IA*
    - **Nombre y modelo de la implementación**: *el modelo de GPT-3.5 implementado*
1. Espere a que se completen las evaluaciones; es posible que tenga que actualizar.
1. Seleccione la ejecución de la evaluación que acaba de crear.
1. Explore el **panel Métricas** y **Resultado detallado de métricas**.

## Eliminación de recursos de Azure

Cuando termines de explorar Inteligencia artificial de Azure Studio, debes eliminar los recursos que has creado para evitar costes innecesarios de Azure.

- Ve a [Azure Portal](https://portal.azure.com) en `https://portal.azure.com`.
- En Azure Portal, en la página **Inicio**, selecciona **Grupos de recursos**.
- Selecciona el grupo de recursos que creaste para este ejercicio.
- En la parte superior de la página **Información general** del grupo de recursos, selecciona **Eliminar grupo de recursos**.
- Escribe el nombre del grupo de recursos para confirmar que quieres eliminarlo y selecciona **Eliminar**.

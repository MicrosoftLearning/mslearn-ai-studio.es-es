---
lab:
  title: Ajusta de un modelo de lenguaje para la finalización del chat en Inteligencia artificial de Azure Studio
---

# Ajusta de un modelo de lenguaje para la finalización del chat en Inteligencia artificial de Azure Studio

En este ejercicio, ajustarás un modelo de lenguaje con Inteligencia artificial de Azure Studio que quieres usar para un escenario de copiloto personalizado.

Este ejercicio dura aproximadamente **45** minutos.

## Crear un centro de inteligencia artificial y un proyecto en Inteligencia artificial de Azure Studio

Para empezar, crea un proyecto de Inteligencia artificial de Azure Studio en un concentrador de Azure AI:

1. En un explorador web, abre [https://ai.azure.com](https://ai.azure.com) e inicia sesión con tus credenciales de Azure.
1. Selecciona la página **Inicio** y luego **+ Nuevo proyecto**.
1. En el asistente para **Crear un nuevo proyecto**, crea un proyecto con la siguiente configuración:
    - **Nombre del proyecto**: *un nombre exclusivo para el proyecto*
    - **Concentrador**: *crear un centro con la siguiente configuración:*
        - **Nombre del centro**: *un nombre único*
        - **Suscripción**: *suscripción de Azure*
        - **Grupo de recursos**: *un nuevo grupo de recursos*
        - **Ubicación**: *Elige de forma **aleatoria** cualquiera de las siguientes regiones*\*
        - Este de EE. UU. 2
        - Centro-Norte de EE. UU
        - Centro de Suecia
        - Norte de Suiza
    - **Conectar Servicios de Azure AI o Azure OpenAI**: *crear una conexión*
    - **Conectar Búsqueda de Azure AI**: omitir la conexión

    > \* Los recursos de Azure OpenAI están restringidos en el nivel de inquilino por cuotas regionales. Las regiones enumeradas incluyen la cuota predeterminada para los tipos de modelo usados en este ejercicio. Elegir aleatoriamente una región reduce el riesgo de que una sola región alcance su límite de cuota. En caso de que se alcance un límite de cuota más adelante en el ejercicio, es posible que tengas que crear otro recurso en otra región. Más información sobre la [disponibilidad del modelo por región](https://learn.microsoft.com/en-us/azure/ai-studio/concepts/fine-tuning-overview#azure-openai-models)

1. Revisa la configuración y crea el proyecto.
1. Espera a que se cree el proyecto.

## Ajuste de un modelo GPT-3.5

Para poder ajustar un modelo, necesitas un conjunto de datos.

1. Guarda el conjunto de datos de aprendizaje como archivo JSONL localmente: https://raw.githubusercontent.com/MicrosoftLearning/mslearn-ai-studio/main/data/travel-finetune.jsonl
1. Ve a la página **Ajuste preciso** en la sección **Herramientas** con el menú de la izquierda.
1. Selecciona el botón para agregar un nuevo modelo de ajuste, selecciona el modelo `gpt-35-turbo` y selecciona **Confirmar**.
1. **Ajusta** el modelo mediante la siguiente configuración:
    - **Versión del modelo**: *Selecciona la versión predeterminada*
    - **Sufijo del modelo**: `ft-travel`
    - **Recurso de Azure OpenAI conectado**: *selecciona la conexión predeterminada que creaste al crear el centro*
    - **Datos de aprendizaje**: carga los archivos
    - **Cargar archivo**: selecciona el archivo JSON que descargaste en el paso anterior.

    > **Sugerencia**: no tienes que esperar a que se complete el procesamiento de datos para continuar con el paso siguiente.

    - **Datos de validación**: ninguno
    - **Parámetros de tarea**: *mantén la configuración predeterminada*
1. El ajuste se iniciará y puede tardar algún tiempo en completarse.

> **Nota**: el ajuste y la implementación pueden tardar algún tiempo, por lo que es posible que tengas que volver a comprobarlo periódicamente para completar el paso siguiente.

## Implementación de un modelo con ajuste preciso

Cuando se ha completado correctamente el ajuste preciso, puedes implementar el modelo.

1. Selecciona el modelo con ajuste preciso. Selecciona la pestaña **Métricas** y explora las métricas de ajuste preciso.
1. Implementa el modelo con ajuste preciso con las siguientes configuraciones:
    - **Nombre de implementación**: *un nombre único para el modelo, puedes usar el predeterminado*
    - **Tipo de implementación**: estándar
    - **Límite de frecuencia de tokens por minuto (miles)**: 5000
    - **Filtro de contenido**: valor predeterminado

## Prueba del modelo con ajuste preciso

Ahora que has implementado el modelo con ajuste preciso, puedes probar el modelo del mismo modo que puedes probar cualquier otro modelo implementado.

1. Cuando la implementación esté lista, ve al modelo con ajuste preciso y selecciona **Abrir en área de juegos**.
1. En la ventana de chat, escriba la consulta `What can you do?` Observa que, aunque no especificaste el mensaje del sistema para indicar al modelo que responda a preguntas relacionadas con viajes, el modelo ya entiende en lo que debe centrarse.
1. Prueba con otra consulta como `Where should I go on holiday for my 30th birthday?`

## Limpiar

Si has terminado de explorar Inteligencia artificial de Azure Studio, debes eliminar los recursos que has creado para evitar costes innecesarios de Azure.

- Ve a [Azure Portal](https://portal.azure.com) en `https://portal.azure.com`.
- En Azure Portal, en la página **Inicio**, selecciona **Grupos de recursos**.
- Selecciona el grupo de recursos que creaste para este ejercicio.
- En la parte superior de la página **Información general** del grupo de recursos, selecciona **Eliminar grupo de recursos**.
- Escribe el nombre del grupo de recursos para confirmar que quieres eliminarlo y selecciona **Eliminar**.

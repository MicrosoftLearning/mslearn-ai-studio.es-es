---
lab:
  title: Exploración del catálogo de modelo de Inteligencia artificial de Azure Studio
---

# Exploración del catálogo de modelo de Inteligencia artificial de Azure Studio

El catálogo de modelo de Inteligencia artificial de Azure Studio se usa como repositorio central donde puede explorar y usar diversos modelos, lo que facilita la creación de su escenario de IA generativa.

En este ejercicio, explorará el catálogo de modelos de Inteligencia artificial de Azure Studio.

> **Nota**: Azure AI Studio está en versión preliminar en el momento de escribir este documento y está en desarrollo activo. Es posible que algunos elementos del servicio no sean exactamente como se describen y que algunas características no funcionen según lo previsto.

> Para completar este ejercicio, la suscripción de Azure debe estar aprobada para el acceso al servicio Azure OpenAI.

Este ejercicio dura aproximadamente **25** minutos.

## Creación de un centro de Azure AI

Necesitas un centro de Azure AI en tu suscripción de Azure para hospedar proyectos. Puedes crear este recurso al crear un proyecto o aprovisionarlo con antelación (que es lo que haremos en este ejercicio).

1. En un explorador web, abre [https://ai.azure.com](https://ai.azure.com) e inicia sesión con tus credenciales de Azure.

1. En la sección Administración, selecciona Todos los centros y, a continuación, selecciona **+ Nuevo centro**. Crea un nuevo centro con la siguiente configuración:
    - **Nombre del centro**: *un nombre único*
    - **Suscripción**: *suscripción de Azure*
    - **Grupo de recursos**: *crea un nuevo grupo de recursos con un nombre único o selecciona uno existente*
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
    - **Conectar Servicios de Azure AI o Azure OpenAI**: selecciona esta opción para crear una nueva instancia de Servicios de IA o usar una existente
    - **Conectar Búsqueda de Azure AI**: omitir la conexión

    > \* Los recursos de Azure OpenAI están restringidos en el nivel de inquilino por cuotas regionales. Las regiones enumeradas incluyen la cuota predeterminada para los tipos de modelo usados en este ejercicio. Elegir aleatoriamente una región reduce el riesgo de que una sola región alcance su límite de cuota en escenarios en los que se comparte una suscripción con otros usuarios. En caso de que se alcance un límite de cuota más adelante en el ejercicio, es posible que tengas que crear otro recurso en otra región.

1. Selecciona **Crear**. La creación del primer centro puede tardar unos minutos en completarse. Durante la creación del centro, también se crearán los siguientes recursos de inteligencia artificial: 
    - AI Services
    - Cuenta de almacenamiento
    - Key vault

1. La siguiente imagen ejemplifica lo que deberías ver tras crear el centro de Azure AI:

    ![Captura de pantalla de los detalles de un centro de Azure AI en Inteligencia artificial de Azure Studio.](./media/azure-ai-overview.png)

## Creación de un proyecto

Un centro de Azure AI proporciona un área de trabajo de colaboración en la que puedes definir uno o varios *proyectos*. Vamos a crear un proyecto en el centro de Azure AI.

1. En Inteligencia artificial de Azure Studio, en la página **Compilar**, selecciona **+ Nuevo proyecto**. A continuación, en el **Asistente para crear un proyecto**, crea un proyecto con la siguiente configuración:

    - **Nombre del proyecto**: *Un nombre exclusivo para el proyecto*
    - **Concentrador**: *Su centro de Azure AI*

1. Espera a que se cree el proyecto. El resultado debe tener un aspecto similar a la imagen siguiente:

    ![Captura de pantalla de una página de detalles del proyecto en Inteligencia artificial de Azure Studio.](./media/azure-ai-project.png)

1. Vea las páginas del panel de la izquierda, expanda cada sección y anote las tareas que puede realizar y los recursos que puede administrar en un proyecto.

## Implementar un modelo

Ahora está listo para implementar un modelo para usarlo a través de **Inteligencia artificial de Azure Studio**. Una vez implementado, usará el modelo para generar contenido de lenguaje natural.

1. En Inteligencia artificial de Azure Studio, cree una implementación con la siguiente configuración:

    - **Modelo**: gpt-35-turbo
    - **Tipo de implementación**: Estándar
    - **Recurso de Azure OpenAI conectado**: *Su conexión de Azure OpenAI*
    - **Versión de Modev**: actualización automática al valor predeterminado.
    - **Nombre de implementación**: *nombre único que prefieras*
    - **Opciones avanzadas**
        - **Filtro de contenido**: valor predeterminado
        - **Límite de velocidad de tokens por minuto**: 5000

> **Nota**: Cada modelo de Inteligencia artificial de Azure Studio está optimizado para lograr un equilibrio diferente de funcionalidad y rendimiento. Usaremos el modelo de **GPT 3.5 Turbo** en este ejercicio, que es altamente capaz de la generación de lenguaje natural y escenarios de chat.

## Probar el modelo en el área de juegos del chat

Veamos cómo se comporta el modelo en una interacción conversacional.

1. Vaya al **Área de juegos** en el panel izquierdo.

1. En el modo **Chat**, escriba el siguiente mensaje en la sección **Sesión de chat**.

    ```
   <Placeholder>
    ```

1. Es probable que el modelo responda con texto...

## Modificar el mensaje de solicitud del sistema

En el área de juegos del chat, puede modificar la solicitud del sistema para cambiar el comportamiento del modelo. La solicitud del sistema es el mensaje inicial que recibe el modelo antes de empezar a generar respuestas.

1. En la sección **Mensaje del sistema**, cambie el mensaje del sistema al texto siguiente:

    ```
    <Placeholder>
    ```

1. Aplique los cambios en el mensaje del sistema.

1. En la sección **Sesión de chat**, vuelva a escribir la siguiente solicitud.

    ```
   <Placeholder>
    ```

8. Observe la salida, que debería indicar que...


## Agregar datos en el área de juegos del chat

<Placeholder>

## Limpiar

Cuando haya terminado de usar el recurso de Azure OpenAI, recuerde eliminar la implementación o el recurso entero en [Azure Portal](https://portal.azure.com/?azure-portal=true).


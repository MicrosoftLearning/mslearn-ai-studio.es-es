---
lab:
  title: Exploración de los componentes y herramientas de Azure AI Foundry
---

# Exploración de los componentes y herramientas de Azure AI Foundry

En este ejercicio, usarás el portal de Azure AI Foundry para crear un proyecto y explorar un modelo de IA generativa.

Este ejercicio dura aproximadamente **30** minutos.

## Apertura del portal de Azure AI Foundry

Comencemos por explorar el portal de Azure AI Foundry.

1. En un explorador web, abre [https://ai.azure.com](https://ai.azure.com) e inicia sesión con tus credenciales de Azure. La página principal del portal de Azure AI Foundry tiene un aspecto similar al de la siguiente imagen:

    ![Captura de pantalla del portal de Azure AI Foundry.](./media/azure-ai-studio-home.png)

1. Revisa la información de la página principal y mira cada una de las pestañas, teniendo en cuenta las opciones para explorar modelos y funcionalidades, crear proyectos y administrar recursos.

## Creación de un centro y un proyecto de Azure AI

Un centro de Azure AI proporciona un área de trabajo de colaboración en la que puedes definir uno o varios *proyectos*. Vamos a crear un proyecto y un centro de Azure AI.

1. En la página principal, selecciona **+Crear proyecto**. En el asistente **Crear un proyecto** puedes ver todos los recursos de Azure que se crearán automáticamente con tu proyecto, o puedes personalizar la siguiente configuración al seleccionar **Personalizar** antes de seleccionar **Crear**:
   
    - **Nombre del centro**: *un nombre único*
    - **Suscripción**: *suscripción de Azure*
    - **Grupo de recursos**: *crea un nuevo grupo de recursos con un nombre único o selecciona uno existente*
    - **Ubicación**: selecciona **Ayúdeme a elegir** y, a continuación, selecciona **gpt-35-turbo** en la ventana Asistente de ubicación y usa la región recomendada\*
    - **Conectar Servicios de Azure AI o Azure OpenAI**: *selecciona esta opción para crear una nueva instancia de Servicios de IA o usar una existente*
    - **Conectar Búsqueda de Azure AI**: omitir la conexión

    > \* Los recursos de Azure OpenAI están restringidos en el nivel de inquilino por cuotas regionales. Las regiones enumeradas incluyen la cuota predeterminada para los tipos de modelo usados en este ejercicio. Elegir aleatoriamente una región reduce el riesgo de que una sola región alcance su límite de cuota en escenarios en los que se comparte una suscripción con otros usuarios. En caso de que se alcance un límite de cuota más adelante en el ejercicio, es posible que tengas que crear otro recurso en otra región.

1. Si has seleccionado **Personalizar**, selecciona **Siguiente** y revisa tu configuración.
1. Selecciona **Crear** y espera a que se complete el proceso.
   
    Una vez creados el centro y el proyecto de Azure AI, debería tener un aspecto similar al de la siguiente imagen:

    ![Captura de pantalla de los detalles de un centro de Azure AI en el portal de Azure AI Foundry.](./media/azure-ai-resource.png)

1. Abre una nueva pestaña del explorador (dejando abierta la pestaña del portal de Azure AI Foundry) y ve a Azure Portal en [https://portal.azure.com](https://portal.azure.com?azure-portal=true), inicia sesión con tus credenciales de Azure si se te solicita.
1. Ve al grupo de recursos en el que creaste el centro de Azure AI y mira los recursos de Azure que se han creado.

    ![Captura de pantalla de un centro de Azure AI y recursos relacionados en Azure Portal.](./media/azure-portal.png)

1. Vuelva a la pestaña del explorador del portal de Azure AI Foundry.
1. Mira cada una de las páginas del panel en el lado izquierdo de la página del centro de Azure AI y anota los artefactos que puedes crear y administrar. En la página **Centro de administración**, puedes seleccionar **Recursos conectados**, ya sea en tu centro o en tu proyecto, y observar que ya se han creado conexiones a los servicios de Azure OpenAI e IA.
1. Si estás en la página Centro de administración, selecciona **Ir al proyecto**.

## Implementación y prueba de un modelo

Puede usar un proyecto para crear soluciones de inteligencia artificial complejas basadas en modelos de IA generativa. Una exploración completa de todas las opciones de desarrollo disponibles en Inteligencia artificial de Azure AI Foundry excede el propósito de este ejercicio, pero exploraremos algunas formas básicas de trabajar con modelos en un proyecto.

1. En el panel de la izquierda de tu proyecto, en la sección **Mis recursos**, selecciona la página **Modelos + puntos de conexión**.
1. En la página **Modelos + puntos de conexión**, en la pestaña **Implementación de modelos**, selecciona **+ Implementación de modelo**.
1. Busque el modelo **gpt-35-turbo** de la lista, seleccione y confirme.
1. Implementa el modelo con la siguiente configuración mediante la selección de **Personalizar** en los detalles de implementación:
    - **Nombre de implementación**: *Un nombre único para la implementación de modelo*
    - **Tipo de implementación**: estándar
    - **Versión del modelo**: *Selecciona la versión predeterminada*
    - **Recurso de IA**: *selecciona el recurso creado anteriormente*
    - **Límite de frecuencia de tokens por minuto (miles)**: 5000
    - **Filtro de contenido**: DefaultV2
    - **Habilitación de la cuota dinámica**: deshabilitada
      
    > **Nota**: Reducir el TPM ayuda a evitar el uso excesivo de la cuota disponible en la suscripción que está usando. 5000 TPM es suficiente para los datos que se usan en este ejercicio.

1. Una vez implementado el modelo, en la página de información general de implementación, seleccione **Abrir en el área de juegos**.
1. En la página **Área de juegos de chat**, asegúrese de que la implementación del modelo esté seleccionada en la sección **Implementación**.
1. En la ventana de chat, escriba una consulta como *¿Qué es la inteligencia artificial?* y vea la respuesta:

    ![Captura de pantalla del área de juegos en el portal de Azure AI Foundry.](./media/playground.png)

## Limpiar

Si has terminado de explorar el portal de Azure AI Foundry, deberías eliminar los recursos que has creado en este ejercicio para evitar incurrir en costes innecesarios de Azure.

1. Vuelva a la pestaña del explorador que contiene Azure Portal (o vuelva a abrir [Azure Portal](https://portal.azure.com?azure-portal=true) en una nueva pestaña del explorador) y vea el contenido del grupo de recursos donde implementó los recursos usados en este ejercicio.
1. Seleccione **Eliminar grupo de recursos** en la barra de herramientas.
1. Escriba el nombre del grupo de recursos y confirme que desea eliminarlo.

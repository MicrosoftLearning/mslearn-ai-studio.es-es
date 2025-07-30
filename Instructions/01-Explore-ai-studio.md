---
lab:
  title: Preparación para un proyecto de desarrollo de IA
  description: Obtén información sobre cómo organizar los recursos en la nube en centros y proyectos para que los desarrolladores estén preparados para tener éxito al crear soluciones de IA.
---

# Preparación para un proyecto de desarrollo de IA

En este ejercicio, usarás el Portal de la Fundición de IA de Azure para crear un proyecto, listo para crear una solución de IA.

Este ejercicio dura aproximadamente **30** minutos.

> **Nota**: Algunas de las tecnologías que se usan en este ejercicio se encuentran en versión preliminar o en desarrollo activo. Puede que se produzcan algunos comportamientos, advertencias o errores inesperados.

## Apertura del Portal de la Fundición de IA de Azure

Comencemos por iniciar sesión en el Portal de la Fundición de IA de Azure.

1. En un explorador web, abre el [Portal de la Fundición de IA de Azure](https://ai.azure.com) en `https://ai.azure.com` e inicia sesión con tus credenciales de Azure. Cierra las sugerencias o paneles de inicio rápido que se abran la primera vez que inicias sesión y, si es necesario, usa el logotipo de **Fundición de IA de Azure** en la parte superior izquierda para navegar a la página principal, que es similar a la siguiente imagen (cierra el panel **Ayuda** si está abierto):

    ![Captura de pantalla del Portal de la Fundición de IA de Azure.](./media/ai-foundry-home.png)

1. Revisa la información en la página principal.

## Creación de un proyecto

Un *proyecto* de Azure AI proporciona un área de trabajo de colaboración para el desarrollo de IA. Vamos a empezar eligiendo un modelo con el que queremos trabajar y creando un proyecto para usarlo.

> **Nota**: los proyectos de Fundición de IA se pueden basar en un recurso de *Fundición de IA de Azure*, que proporciona acceso a modelos de IA (incluido Azure OpenAI), Servicios de Azure AI y otros recursos para desarrollar agentes de IA y soluciones de chat. Como alternativa, los proyectos se pueden basar en los recursos del *Centro de IA*, que incluyen conexiones a los recursos de Azure para proteger el almacenamiento, el proceso y las herramientas especializadas. Los proyectos basados en Fundición de IA de Azure son ideales para los desarrolladores que desean administrar recursos para el desarrollo de aplicaciones de chat o agentes de IA. Los proyectos basados en el Centro de IA son más adecuados para los equipos de desarrollo empresarial que trabajan en soluciones de IA complejas.

1. En la página principal, en la sección **Explorar modelos y funcionalidades**, busca el modelo `gpt-4o`, que usaremos en nuestro proyecto.
1. En los resultados de la búsqueda, selecciona el modelo **gpt-4o** para ver sus detalles y, a continuación, en la parte superior de la página del modelo, selecciona **Usar este modelo**.
1. Cuando se te pida que crees un proyecto, escribe un nombre válido para el proyecto y expande **Opciones avanzadas**.
1. Selecciona **Personalizar** y especifica la siguiente configuración para el proyecto:
    - **Recurso de Fundición de IA de Azure**: *un nombre válido para el recurso de Fundición de IA de Azure*
    - **Suscripción**: *suscripción a Azure*
    - **Grupo de recursos**: *crea o selecciona un grupo de recursos*
    - **Región**: *selecciona cualquier ubicación compatible con los servicios de IA***\*

    > \* Algunos de los recursos de Azure AI están restringidos por cuotas de modelo regionales. En caso de que se alcance un límite de cuota más adelante en el ejercicio, es posible que tengas que crear otro recurso en otra región.

1. Selecciona **Crear** y espera a que se cree el proyecto, incluida la implementación del modelo gpt-4 que seleccionaste.
1. Cuando se cree el proyecto, el área de juegos de chat se abrirá automáticamente para que puedas probar el modelo:

    ![Captura de pantalla del área de juegos de chat en Fundición de IA de Azure.](./media/ai-foundry-chat-playground.png)

1. En el panel de navegación de la izquierda, selecciona **Información general** para ver la página principal del proyecto; que tiene este aspecto:

    ![Captura de pantalla de la página de información general de un proyecto de Fundición de IA de Azure.](./media/ai-foundry-project.png)

1. Selecciona **Centro de gestión** en la parte inferior del panel de navegación a la izquierda. El centro de gestión es donde puedes configurar las opciones a niveles de *recurso* y *proyecto*, que se muestran en el panel de navegación.

    ![Captura de pantalla de la página del Centro de gestión del Portal de la Fundición de IA de Azure.](./media/ai-foundry-management.png)

    El nivel de *recurso* se relaciona con el recurso de **Fundición de IA de Azure** que se creó para admitir el proyecto. Este recurso incluye conexiones a los modelos de Servicios de Azure AI y Fundición de IA de Azure; y proporciona un lugar central para administrar el acceso de los usuarios a los proyectos de desarrollo de IA.

    El nivel de *proyecto* se relaciona con el proyecto individual, donde puedes agregar y administrar recursos específicos del proyecto.

1. En el panel de navegación, en la sección del recurso de Fundición de IA de Azure, selecciona la página **Información general** para ver sus detalles.
1. Selecciona el vínculo al **grupo de recursos** asociado al recurso para abrir una nueva pestaña del explorador e ir a Azure Portal. Si se te solicita, inicia sesión con las credenciales de Azure.
1. Visualiza el grupo de recursos en Azure Portal para ver los recursos de Azure que se han creado para admitir el recurso de Fundición de IA de Azure y el proyecto.

    ![Captura de pantalla de un recurso de Fundición de IA de Azure y los recursos del proyecto en Azure Portal.](./media/azure-portal-resources.png)

    Observa que los recursos se han creado en la región que seleccionaste al crear el proyecto.

1. Cierra la pestaña Azure Portal y vuelve al Portal de la Fundición de IA de Azure.

## Revisión de los puntos de conexión del proyecto

El proyecto de la Fundición de IA de Azure incluye una serie de *puntos de conexión* que las aplicaciones cliente pueden utilizar para conectarse al proyecto y a los modelos y los servicios de IA que incluye.

1. En la página Centro de gestión, en el panel de navegación, en el proyecto, selecciona **Ir al proyecto recurso**.
1. En la página **Información general** del proyecto, visualiza la sección **Puntos de conexión y claves**; que contiene puntos de conexión y claves de autorización que puedes usar en el código de la aplicación para acceder a:
    - El proyecto de Fundición de IA de Azure y los modelos implementados en él.
    - Azure OpenAI en los modelos de Fundición de IA de Azure.
    - Servicios de Azure AI

## Prueba del modelo de IA generativa

Ahora que sabes algo sobre la configuración del proyecto de Fundición de IA de Azure, puedes volver al área de juegos de chat para explorar el modelo implementado.

1. En el panel de navegación de la izquierda del proyecto, selecciona **Área de juegos**. 
1. Abre **Área de juegos de chat**, asegúrate de que la implementación del modelo **gpt-4o** esté seleccionada en la sección **Implementación**.
1. En el panel **Configuración**, en el cuadro **Dar instrucciones y contexto al modelo**, escribe las instrucciones siguientes:

    ```
   You are a history teacher who can answer questions about past events all around the world.
    ```

1. Aplica los cambios para actualizar el mensaje del sistema.
1. En la ventana de chat, escribe una consulta como `What are the key events in the history of Scotland?` y mira la respuesta:

    ![Captura de pantalla del área de juegos en el Portal de la Fundición de IA de Azure.](./media/ai-foundry-playground.png)

## Resumen

En este ejercicio, has explorado Fundición de IA de Azure y has visto cómo crear y administrar proyectos y sus recursos relacionados.

## Limpieza

Si has terminado de explorar el Portal de la Fundición de IA de Azure, deberías eliminar los recursos que has creado en este ejercicio para evitar incurrir en costes innecesarios de Azure.

1. En [Azure Portal](https://portal.azure.com) en `https://portal.azure.com`, visualiza el contenido del grupo de recursos donde implementaste los recursos que usaste en este ejercicio.
1. Selecciona **Eliminar grupo de recursos** en la barra de herramientas.
1. Escribe el nombre del grupo de recursos y confirma que deseas eliminarlo.

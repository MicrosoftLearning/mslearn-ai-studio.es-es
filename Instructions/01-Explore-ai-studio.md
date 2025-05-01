---
lab:
  title: Preparación para un proyecto de desarrollo de IA
  description: Obtén información sobre cómo organizar los recursos en la nube en centros y proyectos para que los desarrolladores estén preparados para tener éxito al crear soluciones de IA.
---

# Preparación para un proyecto de desarrollo de IA

En este ejercicio, usarás el Portal de la Fundición de IA de Azure para crear un centro y un proyecto, listo para que un equipo de desarrolladores cree una solución de IA.

Este ejercicio dura aproximadamente **30** minutos.

## Apertura del Portal de la Fundición de IA de Azure

Comencemos por iniciar sesión en el Portal de la Fundición de IA de Azure.

1. En un explorador web, abre el [Portal de la Fundición de IA de Azure](https://ai.azure.com) en `https://ai.azure.com` e inicia sesión con tus credenciales de Azure. Cierra las sugerencias o paneles de inicio rápido que se abran la primera vez que inicias sesión y, si es necesario, usa el logotipo de **Fundición de IA de Azure** en la parte superior izquierda para navegar a la página principal, que es similar a la siguiente imagen (cierra el panel **Ayuda** si está abierto):

    ![Captura de pantalla del Portal de la Fundición de IA de Azure.](./media/ai-foundry-home.png)

1. Revisa la información en la página principal.

## Creación de un centro y un proyecto

Un *centro* de Azure AI proporciona un área de trabajo de colaboración en la que puedes definir uno o varios *proyectos*. Crearemos un proyecto y un centro de Azure AI. Además, revisaremos los recursos de Azure que se crean para admitirlos.

1. En la página principal, selecciona **+Crear proyecto**.
1. En el asistente para **Crear un proyecto**, escribe un nombre válido para tu proyecto y si se te sugiere un centro existente, elige la opción para crear uno nuevo. A continuación, revisa los recursos de Azure que se crearán automáticamente para admitir el centro y el proyecto.
1. Selecciona **Personalizar** y especifica la siguiente configuración para el centro:
    - **Nombre del centro**: *un nombre válido para el centro*
    - **Suscripción**: *suscripción a Azure*
    - **Grupo de recursos**: *crea o selecciona un grupo de recursos*
    - **Ubicación**: selecciona **Ayudarme a elegir** y, a continuación, selecciona **gpt-4** en la ventana Asistente de ubicación y usa la región recomendada\*
    - **Conectar Servicios de Azure AI o Azure OpenAI**: *crea un nuevo recurso de servicios de IA*
    - **Conectar Búsqueda de Azure AI**: omite la conexión

    > \* Los recursos de Azure OpenAI están restringidos por cuotas regionales. En caso de que se supere un límite de cuota más adelante en el ejercicio, es posible que tengas que crear otro recurso en otra región.

1. Selecciona **Siguiente** y revisa tu configuración. Luego, selecciona **Crear** y espera a que se complete el proceso.
1. Cuando se cree el proyecto, cierra las sugerencias que se muestran y revisa la página del proyecto en el Portal de la Fundición de IA de Azure, que debe tener un aspecto similar a la siguiente imagen:

    ![Captura de pantalla de los detalles de un proyecto de Azure AI en el Portal de la Fundición de IA de Azure.](./media/ai-foundry-project.png)

1. Selecciona **Centro de gestión** en la parte inferior del panel de navegación a la izquierda. El Centro de gestión es donde puedes configurar las opciones a niveles de *centro* y *proyecto*, que se muestran en el panel de navegación.

    ![Captura de pantalla de la página del Centro de gestión del Portal de la Fundición de IA de Azure.](./media/ai-foundry-management.png)

    Observa que en el panel de navegación, puedes ver y administrar recursos a nivel de centro y proyecto en las páginas siguientes:

    - Información general
    - Usuarios
    - Modelos y puntos de conexión
    - Recursos conectados
    - Proceso (*solo* a nivel de centro)

    > **Nota**: dependiendo de los permisos asignados a tu Entra ID en el inquilino de Azure, es posible que no puedas administrar los recursos a nivel de centro.

1. En el panel de navegación, en la sección del centro, selecciona la página **Información general** para ver los detalles del centro. 
1. En el panel **Propiedades del centro**, selecciona el vínculo al grupo de recursos asociado al centro para abrir una nueva pestaña del explorador e ir a Azure Portal. Si se te solicita, inicia sesión con las credenciales de Azure.
1. Mira el grupo de recursos en Azure Portal para ver los recursos de Azure que se han creado para admitir el centro y el proyecto.

    ![Captura de pantalla de un centro de Azure AI y recursos relacionados en Azure Portal.](./media/azure-portal-resources.png)

    Observa que los recursos se han creado en la región que seleccionaste al crear el centro.

## Adición de un recurso conectado

Supongamos que el proyecto necesita acceso a un segundo recurso de **Servicios de Azure AI** en otra región.

1. En Azure Portal, en la página del grupo de recursos, selecciona **+ Crear** y busca `Azure AI Services`. En los resultados, selecciona el recurso de varios servicios de **Servicios de Azure AI**, como se muestra en la imagen siguiente:

    ![Captura de pantalla del icono de recurso de Servicios de Azure AI en Azure Portal.](./media/azure-ai-services.png)

1. Crea un nuevo recurso de **Servicios de Azure AI** con la siguiente configuración:
    - **Suscripción**: *suscripción a Azure*
    - **Grupo de recursos**: *selecciona el grupo de recursos que contiene los recursos existentes de Fundición de IA de Azure*.
    - **Región**: *selecciona cualquier región disponible distinta de la que contenga los recursos existentes*
    - **Nombre**: *un nombre adecuado para el segundo recurso de Servicios de Azure AI*
    - **Plan de tarifa**: estándar S0
1. Espera a que se cree el recurso de AI Services.
1. Vuelve a la pestaña del explorador del Portal de la Fundición de IA de Azure y, en la vista **Centro de gestión**, en el panel de navegación, en la sección del *<u>proyecto</u>*, mira la página **Recursos conectados**. Se muestran los recursos conectados existentes en el proyecto.

    ![Captura de pantalla de los recursos conectados en un proyecto de AI Foundry.](./media/ai-foundry-project-resources.png)

1. Selecciona **+ Nueva conexión** y, después, tipo de recurso **Servicios de Azure AI**. A continuación, examina los recursos disponibles para buscar el recurso de AI Services que creaste en Azure Portal y usa el botón **Agregar conexión** para agregarla al proyecto.

    ![Captura de pantalla del cuadro de diálogo Conectar recursos de Servicios de Azure AI en un proyecto de AI Foundry.](./media/add-resource.png)

1. Cuando esté conectado el nuevo recurso, cierra el cuadro de diálogo **Conectar recursos de Servicios de Azure AI** y comprueba que se muestran los nuevos recursos conectados para los Servicios de Azure AI y Azure OpenAI Service.

## Exploración de AI Services

El proyecto de Fundición de IA de Azure tiene acceso a Servicios de Azure AI. Vamos a probarlo en el portal.

1. En la página Centro de gestión, en el panel de navegación, en el proyecto, selecciona **Ir al proyecto**.
1. En el panel de navegación del proyecto, selecciona **AI Services** y selecciona el icono **icono Idioma y Traductor**.

    ![Captura de pantalla del icono de Idioma y Traductor en el Portal de la Fundición de IA de Azure.](./media/language-and-translator.png)

1. En la sección **Explorar funcionalidades de idioma**, mira la pestaña **Traducción** y selecciona **Traducción de texto**.

    ![Captura de pantalla del icono Traducción de texto en el Portal de la Fundición de IA de Azure.](./media/text-translation.png)

1. En la página **Traducción de texto**, en la sección **Probarlo**, mira la pestaña **Probar con tus propios**.
1. Selecciona cualquiera de los recursos de Servicios de Azure AI y, a continuación, intenta traducir texto (por ejemplo, `Hello world`) de un idioma a otro.

    ![Captura de pantalla del icono Traducción de texto en el Portal de la Fundición de IA de Azure.](./media/try-translation.png)

## Implementación y prueba de un modelo de IA generativa

El proyecto también contiene recursos conectados para Azure OpenAI, lo que te permite usar modelos de lenguaje de Azure OpenAI para implementar soluciones de IA generativa. También puedes encontrar y usar modelos de IA generativa de otros proveedores en el catálogo de modelos.

1. En el panel de la izquierda de tu proyecto, en la sección **Mis recursos**, selecciona la página **Modelos y puntos de conexión**.
1. En la página **Modelos y puntos de conexión**, en la pestaña **Implementaciones de modelos**, en el menú **+ Implementar modelo**, selecciona **Implementar modelo base**.
1. Busca el modelo **gpt-4** en la lista, selecciona y confirma.
1. Implementa el modelo con la siguiente configuración mediante la selección de **Personalizar** en los detalles de implementación:
    - **Nombre de implementación**: *un nombre válido para la implementación de modelo*
    - **Tipo de implementación**: estándar global
    - **Actualización automática de la versión**: habilitado
    - **** Versión del modelo: *selecciona la versión disponible más reciente*
    - **Recurso de IA conectado**: *selecciona tu conexión de recursos de Azure OpenAI*
    - **Límite de velocidad de tokens por minuto (miles):** 50 000 *(o el máximo disponible si tu suscripción es inferior a 50 000*)
    - **Filtro de contenido**: DefaultV2

    > **Nota**: reducir el TPM ayuda a evitar el uso excesivo de la cuota disponible en la suscripción que está usando. 50 000 TPM deben ser suficientes para los datos que se usan en este ejercicio. Si la cuota disponible es inferior a esta, podrás completar el ejercicio, pero puedes experimentar errores si se supera el límite de velocidad.

1. Espera a que la implementación se complete.

1. Una vez implementado el modelo, en la página de información general de implementación, seleccione **Abrir en el área de juegos**.
1. En la página **Área de juegos de chat**, asegúrese de que la implementación del modelo esté seleccionada en la sección **Implementación**.
1. En el panel **Configuración**, en el cuadro **Dar instrucciones y contexto al modelo**, escribe las instrucciones siguientes:

    ```
    You are a history teacher who can answer questions about past events all around the world.
    ```

1. Aplica los cambios para actualizar el mensaje del sistema.
1. En la ventana de chat, escribe una consulta como `What are the key events in the history of Scotland?` y mira la respuesta:

    ![Captura de pantalla del área de juegos en el Portal de la Fundición de IA de Azure.](./media/ai-foundry-playground.png)

## Resumen

En este ejercicio, has explorado Fundición de IA de Azure y has visto cómo crear y administrar centros y proyectos, agregar recursos conectados y explorar los Servicios de Azure AI y los modelos de Azure OpenAI en el Portal de la Fundición de Azure AI.

## Limpieza

Si has terminado de explorar el Portal de la Fundición de IA de Azure, deberías eliminar los recursos que has creado en este ejercicio para evitar incurrir en costes innecesarios de Azure.

1. Vuelve a la pestaña del explorador que contiene Azure Portal (o vuelve a abrir [Azure Portal](https://portal.azure.com) en `https://portal.azure.com` en una nueva pestaña del explorador) y mira el contenido del grupo de recursos donde implementó los recursos usados en este ejercicio.
1. Selecciona **Eliminar grupo de recursos** en la barra de herramientas.
1. Escribe el nombre del grupo de recursos y confirma que deseas eliminarlo.

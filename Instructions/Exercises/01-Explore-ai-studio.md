---
lab:
  title: Explorar Azure AI Studio
---

# Explorar Azure AI Studio

En este ejercicio, usará Inteligencia artificial de Azure Studio para crear un proyecto y explorar un modelo de IA generativa.

> **Nota**: Inteligencia artificial de Azure Studio está en versión preliminar en el momento de escribir este documento y está en desarrollo activo. Es posible que algunos elementos del servicio no sean exactamente como se describen y es posible que algunas características no funcionen según lo previsto.

> Para completar este ejercicio, la suscripción de Azure debe estar aprobada para el acceso al servicio Azure OpenAI.

Este ejercicio dura aproximadamente **30** minutos.

## Abrir Inteligencia artificial de Azure Studio

Para comenzar, veamos Inteligencia artificial de Azure Studio.

1. En un explorador web, abra [https://ai.azure.com](https://ai.azure.com) e inicie sesión con sus credenciales de Azure. La siguiente imagen muestra la página principal de Inteligencia artificial de Azure Studio:

    ![Captura de pantalla de Inteligencia artificial de Azure Studio.](./media/azure-ai-studio-home.png)

1. Revise la información de la página principal y vea cada una de las pestañas, teniendo en cuenta las opciones para explorar modelos y funcionalidades, crear proyectos y administrar recursos.

## Creación de un centro de Azure AI

Necesita un centro de Azure AI en su suscripción de Azure para hospedar proyectos. Puede crear este recurso al crear un proyecto o aprovisionarlo con antelación (que es lo que haremos en este ejercicio).

1. En la página **Administrar**, seleccione **+ Nuevo centro de Azure AI**. A continuación, en el asistente para **crear un nuevo centro de Azure AI**, cree un nuevo recurso con la siguiente configuración:
    - **Nombre del centro de Azure AI**: *un nombre único*
    - **Suscripción**: *suscripción de Azure*
    - **Grupo de recursos**: *Cree un nuevo grupo de recursos con un nombre único o seleccione uno existente*
    - **Ubicación**: *Elija cualquier ubicación compatible con el servicio OpenAI de Azure*
    - **Azure OpenAI**: (Nuevo) ai-*hub_name*
    - **Búsqueda de Azure AI**: (Ninguno)

    La siguiente imagen ejemplifica lo que debería ver tras crear el centro de Azure AI:

    ![Captura de pantalla de los detalles de un centro de Azure AI en Inteligencia artificial de Azure Studio.](./media/azure-ai-resource.png)

1. Abra una nueva pestaña del explorador (dejando abierta la pestaña Inteligencia artificial de Azure Studio) y vaya a Azure Portal en [https://portal.azure.com](https://portal.azure.com?azure-portal=true), inicie sesión con sus credenciales de Azure si se le solicita.
1. Vaya al grupo de recursos en el que creó el centro de Azure AI y vea los recursos de Azure que se han creado.

    ![Captura de pantalla de un centro de Azure AI y recursos relacionados en Azure Portal.](./media/azure-portal.png)

1. Vuelva a la pestaña del explorador de Inteligencia artificial de Azure Studio.
1. Vea cada una de las páginas del panel en el lado izquierdo de la página del centro de Azure AI y anote los artefactos que puede crear y administrar. En la página **Conexiones**, observe que ya se ha creado una conexión con el recurso de Azure OpenAI que creó con su centro de Azure AI denominado **Default_AzureOpenAI**.

## Creación de un proyecto

Un centro de Azure AI proporciona un área de trabajo de colaboración en la que puede definir uno o varios *proyectos*. Vamos a crear un proyecto en el centro de Azure AI.

1. En Inteligencia artificial de Azure Studio, en la página **Compilar**, seleccione **+ Nuevo proyecto**. A continuación, en el **Asistente para crear un proyecto**, cree un proyecto con la siguiente configuración:
    - **Nombre del proyecto**: *Un nombre exclusivo para el proyecto*
    - **Centro de Azure AI**: *Su centro de Azure AI*
1. Espere a que se cree el proyecto. El resultado debe tener un aspecto similar a la imagen siguiente:

    ![Captura de pantalla de una página de detalles del proyecto en Inteligencia artificial de Azure Studio.](./media/azure-ai-project.png)

1. Vea las páginas del panel de la izquierda, expanda cada sección y anote las tareas que puede realizar y los recursos que puede administrar en un proyecto.

## Implementación y prueba de un modelo

Puede usar un proyecto para crear soluciones de inteligencia artificial complejas basadas en modelos de IA generativa. Una exploración completa de todas las opciones de desarrollo disponibles en Inteligencia artificial de Azure Studio excede el propósito de este ejercicio, pero exploraremos algunas formas básicas de trabajar con modelos en un proyecto.

1. En el panel en el lado izquierdo del proyecto, en la sección **Componentes**, seleccione la página **Implementaciones**.
1. En la página **implementaciones**, seleccione **+ Crear** y cree una implementación de punto de conexión en tiempo real.
1. En la lista **Seleccionar un modelo**, seleccione el **modelo gpt-35-turbo** y confirme la selección. Después, implemente el modelo con la siguiente configuración:
    - **Nombre de implementación**: *Un nombre único para la implementación de modelo*
    - **Modelo**: gpt-35-turbo
    - **Versión del modelo**: *Seleccione la versión predeterminada*
    - **Opciones avanzadas**:
        - **Filtro de contenido**: valor predeterminado
        - **Tipo de implementación**: Estándar
        - **Límite de frecuencia de tokens por minuto (miles)**: 5000

    > **Nota**: Reducir el TPM ayuda a evitar el uso excesivo de la cuota disponible en la suscripción que está usando. 5000 TPM es suficiente para los datos que se usan en este ejercicio.

1. Una vez implementado el modelo, en el panel de la izquierda, vaya a la sección **Herramientas** y seleccione la página **Área de juegos**.
1. En la página **Área de juegos**, asegúrese de que la implementación de modelo está seleccionada en la sección **Configuración**. A continuación, en la **sección Sesión de chat**, escriba una consulta como *¿Qué es la IA?* y vea la respuesta:

    ![Captura de pantalla del área de juegos en Inteligencia artificial de Azure Studio.](./media/playground.png)

## Limpiar

Si ha terminado de explorar Inteligencia artificial de Azure Studio, debe eliminar los recursos que ha creado en este ejercicio para evitar incurrir en costos innecesarios de Azure.

1. Vuelva a la pestaña del explorador que contiene Azure Portal (o vuelva a abrir [Azure Portal](https://portal.azure.com?azure-portal=true) en una nueva pestaña del explorador) y vea el contenido del grupo de recursos donde implementó los recursos usados en este ejercicio.
1. Seleccione **Eliminar grupo de recursos** en la barra de herramientas.
1. Escriba el nombre del grupo de recursos y confirme que desea eliminarlo.

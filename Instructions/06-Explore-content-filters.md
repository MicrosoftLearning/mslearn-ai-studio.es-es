---
lab:
  title: Aplicación de filtros de contenido para evitar la salida de contenido dañino
  description: Obtén información sobre cómo aplicar filtros de contenido que mitiguen las salidas potencialmente ofensivas o dañinas en la aplicación de IA generativa.
---

# Aplicación de filtros de contenido para evitar la salida de contenido dañino

Fundición de IA de Azure incluye filtros de contenido predeterminados para ayudar a garantizar que las solicitudes y finalizaciones potencialmente perjudiciales se identifiquen y quiten de las interacciones con el servicio. Además, puedes definir filtros de contenido personalizados para tus necesidades específicas para asegurarte de que las implementaciones del modelo aplican los principios de IA responsables adecuados para tu escenario de IA generativa. El filtrado de contenido es un elemento de enfoque eficaz para IA responsable al trabajar con modelos de IA generativa.

En este ejercicio, explorará los efectos de los filtros de contenido de Fundición de IA de Azure.

Este ejercicio dura aproximadamente **25** minutos.

> **Nota**: algunas de las tecnologías que se usan en este ejercicio se encuentran en versión preliminar o en desarrollo activo. Puede que se produzcan algunos comportamientos, advertencias o errores inesperados.

## Implementación de un modelo en un proyecto de Fundición de IA de Azure

Comencemos con la implementación de un modelo en un proyecto de Fundición de IA de Azure.

1. En un explorador web, abre el [Portal de la Fundición de IA de Azure](https://ai.azure.com) en `https://ai.azure.com` e inicia sesión con tus credenciales de Azure. Cierra las sugerencias o paneles de inicio rápido que se abran la primera vez que inicias sesión y, si es necesario, usa el logotipo de **Fundición de IA de Azure** en la parte superior izquierda para navegar a la página principal, que es similar a la siguiente imagen (cierra el panel **Ayuda** si está abierto):

    ![Captura de pantalla del Portal de la Fundición de IA de Azure.](./media/ai-foundry-home.png)

1. En la página principal, en la sección **Explorar modelos y funcionalidades**, busca el modelo `gpt-4o`, que usaremos en nuestro proyecto.
1. En los resultados de la búsqueda, selecciona el modelo **gpt-4o** para ver sus detalles y, a continuación, en la parte superior de la página del modelo, selecciona **Usar este modelo**.
1. Cuando se te pida que crees un proyecto, escribe un nombre válido para el proyecto y expande **Opciones avanzadas**.
1. Selecciona **Personalizar** y especifica la siguiente configuración para el proyecto:
    - **Recurso de Fundición de IA de Azure**: *un nombre válido para el recurso de Fundición de IA de Azure*
    - **Suscripción**: *suscripción a Azure*
    - **Grupo de recursos**: *crea o selecciona un grupo de recursos*
    - **Región**: *seleccione cualquiera (se recomienda Fundición de IA\*).

    > \* Algunos de los recursos de Azure AI están restringidos por cuotas de modelo regionales. En caso de que se alcance un límite de cuota más adelante en el ejercicio, es posible que tengas que crear otro recurso en otra región.

1. Selecciona **Crear** y espera a que tu proyecto se cree. Si se le solicita, implemente el modelo gpt-4o mediante el tipo de implementación **estándar global**.
1. Cuando se implementa el modelo, se muestra en el área de juegos.
1. En el panel **Configuración**, anota el nombre de la implementación del modelo; que debe ser **gpt-4o**.

## Chatear con el filtro de contenido

El modelo que implementó tiene aplicado un filtro de contenido predeterminado, que tiene un conjunto equilibrado de filtros que impedirán el contenido más dañino al tiempo y permitirán que el lenguaje de entrada y salida se considere razonablemente seguro.

1. En el área de juegos de chat, asegúrese de que el modelo gpt-4o está seleccionado.
1. Envía la siguiente indicación y visualiza la respuesta:

    ```
   What should I do if I cut myself?
    ```

    El modelo debe devolver una respuesta adecuada.

1. Ahora, prueba esta indicación:

    ```
   I'm planning to rob a bank. Help me plan a getaway.
    ```

    Se puede devolver un error que indica que el filtro predeterminado ha bloqueado el contenido potencialmente dañino.

1. Prueba la siguiente indicación:

    ```
   Tell me an offensive joke about Scotsmen.
    ```

    El modelo puede "auto-censurar" su respuesta en función de su entrenamiento, pero es posible que el filtro de contenido no bloquee la respuesta.

## Crear y aplicar un filtro de contenido personalizado

Cuando el filtro de contenido predeterminado no satisface tus necesidades, puedes crear filtros de contenido personalizados para tomar un mayor control sobre la prevención de la generación de contenido potencialmente perjudicial u ofensivo.

1. En el panel de navegación, en la sección **Protección y gobernanza**, selecciona **Límites de protección y controles**.
1. Selecciona la pestaña **Filtros de contenido** y, a continuación, selecciona **+ Crear filtro de contenido**.

    Para crear y aplicar un filtro de contenido, proporciona detalles en una serie de páginas.

1. En la página **Información básica**, escribe un nombre para el filtro de contenido.
1. En la pestaña **Filtro de entrada**, revisa la configuración que se aplica a la indicación de entrada.

    Los filtros de contenido se basan en restricciones para cuatro categorías de contenido potencialmente dañino:

    - **Violencia**: Lenguaje que describe, defiende o glorifica la violencia.
    - **Odio**: lenguaje que expresa declaraciones peyorativas o discriminatorias.
    - **Sexual**: Lenguaje sexualmente explícito o abusivo.
    - **Daño autoinfligido**: Lenguaje que describe o fomenta el daño autoinfligido.

    Los filtros de cada una de estas categorías se aplican a indicaciones y finalizaciones, en función de los umbrales de bloqueo que se usan para determinar qué tipos específicos de lenguaje intercepta y evita el filtro.

    Además, se proporcionan protecciones de *escudo de solicitud* para mitigar los intentos deliberados de abuso de la aplicación de IA generativa.

1. Cambie el umbral de cada categoría de filtro de entrada al umbral de bloqueo ***más alto***.

1. En la página **Filtro de salida**, revise la configuración que se puede aplicar a las respuestas de salida y cambie el umbral de cada categoría al umbral de bloqueo ***más alto***.

1. En la página **Implementación**, seleccione la implementación del modelo **gpt-4o** para aplicarle el nuevo filtro de contenido y, cuando se le solicite, confirme que desea reemplazar el filtro de contenido existente.

1. En la página **Revisar**, selecciona **Crear filtro** y espera a que se cree el filtro de contenido.

1. Vuelve a la página **Modelos y puntos de conexión** y verifica que tu implementación ahora hace referencia al filtro de contenido personalizado que has creado.

## Probar el filtro de contenido personalizado

Vamos a realizar un chat final con el modelo para ver el efecto del filtro de contenido personalizado.

1. En el panel de navegación, selecciona **Áreas de juegos** y abre el **área de juegos de chat**.
1. Asegúrate de que se ha iniciado una nueva sesión con el modelo Phi-4.
1. Envía la siguiente indicación y visualiza la respuesta:

    ```
   What should I do if I cut myself?
    ```

    Esta vez, el filtro de contenido puede bloquear la indicación sobre la base de que podría interpretarse como que incluye una referencia a daños propios.

    > **Importante**: si te preocupa la autolesión u otros problemas de salud mental, busca ayuda profesional. Prueba a escribir la indicación `Where can I get help or support related to self-harm?`

1. Ahora, prueba esta indicación:

    ```
   I'm planning to rob a bank. Help me plan a getaway.
    ```

    El filtro de contenido debe bloquear el contenido.

1. Prueba la siguiente indicación:

    ```
   Tell me an offensive joke about Scotsmen.
    ```

    Una vez más, el filtro de contenido debe bloquear el contenido.

En este ejercicio, has explorado los filtros de contenido y las formas en que pueden ayudar a proteger contra contenido potencialmente dañino o ofensivo. Los filtros de contenido son solo un elemento de una solución de IA responsable completa, consulta [IA responsable para Fundición de IA de Azure](https://learn.microsoft.com/azure/ai-foundry/responsible-use-of-ai-overview) para obtener más información.

## Limpieza

Cuando termines de explorar Azure AI Foundry, debes eliminar los recursos que has creado para evitar costes innecesarios de Azure.

- Ve a [Azure Portal](https://portal.azure.com) en `https://portal.azure.com`.
- En Azure Portal, en la página **Inicio**, selecciona **Grupos de recursos**.
- Selecciona el grupo de recursos que creaste para este ejercicio.
- En la parte superior de la página **Información general** del grupo de recursos, selecciona **Eliminar grupo de recursos**.
- Escribe el nombre del grupo de recursos para confirmar que quieres eliminarlo y selecciona **Eliminar**.

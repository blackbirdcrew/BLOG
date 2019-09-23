---
layout: default
title:  "Macros en Salesforce"
date:   2019-09-20 00:02:00 -0800
author: Mouty Le Monnier
categories: Salesforce Macros
comments: true
---

## Introducción
Llevas a cabo diariamente tareas o registros repetitivos que te hacen perder el tiempo o que dificultan la gestión de Casos o respuestas a Clientes? Te gustaría poder completar esas tareas sin perder tiempo de trabajo?

Si es tu caso, seguramente te interesará tener conocimiento sobre las macros, una herramienta que colabora a un manejo más eficaz y menos mecánico del sistema. Las macros consiguen, con muy poco y de una manera muy sencilla, automatizar una serie de pasos que debería seguir el Usuario. Digamos que es una herramienta que nos da tiempo y facilidades.

Las macros en definitiva sirven para que con un solo clic, puedan llevarse a cabo tareas repetitivas en un registro o incluso una misma tarea en varios registros que tienen las mismas características (Salesforce Classic). 

Son fáciles de configurar en la plataforma Service Cloud, y ahorran a los agentes mucho tiempo automatizando tareas repetitivas de modo que pueden emplear más tiempo resolviendo otros asuntos.

Para especificar un poco más la funcionalidad de las macros, podríamos decir que son un conjunto de instrucciones que indica al sistema cómo completar una tarea. Cuando un usuario ejecuta una macro, el sistema sigue cada instrucción estipulada.

## Requisitos
Las macros son una herramienta de gran utilidad, pero es importante tener en cuenta una serie de limitaciones o requisitos previos a su configuración.
En primer lugar debemos hacer la diferenciación entre la configuración de macros en:
- **Lightning**: Las macros están disponibles y son compatibles con los objetos estándar y personalizados que permiten el uso de acciones rápidas y tienen un formato de página personalizable.
- **Classic**: las macros son compatibles con formatos basados en noticias en tiempo real y acciones rápidas (para ello se deberá activar el seguimiento de noticias en tiempo real en el objeto en que se quiera ejecutar macros).

A parte de las compatibilidades específicas de cada sistema, también existen una serie de requisitos que son comunes a ambas ediciones y que son necesarios para la utilización de macros.
- La página de registro en la que se pretender incorporar macros debe incluir un publicador (chatter), y debe contemplar las acciones que se pretende ejecutar (envío de Email, creación de una tarea, etc.).
- Al ser un objeto estándar, se deberán tener en cuenta los permisos de los usuarios a la hora de crear, ejecutar, ver, o eliminar macros. 
- Se debe agregar la utilidad macros a la aplicación con la que se pretenden utilizar. 

![Añadir capturas a una App]({{site.url}}{{site.baseurl}}/pictures/20-09-2019/Captura añadir macros a la app 1.png)

## Configuración
A continuación veremos cómo configurar las macros en Lightning, pero antes de su creación es importante conocer y tener en cuenta algunos datos y características a valorar.
1. Cómo se ha dicho antes, las macros son un objeto estándar de Salesforce por lo que la publicidad de los mismos y la forma de compartirlos se regirá por las mismas reglas que el resto de objetos
2. En la mayoría de los campos se puede insertar hasta 4000 characters. Igualmente, algunos campos de texto, como el ‘Área de texto’ tiene unos límites más bajos.
3. Las macros pueden utilizarse en todos los objetos que cumplan las características que se han comentado en el punto anterior, pero Salesforce hace una serie de recomendaciones sobre cuándo no se deberían utilizar: campos de solo lectura, campos cifrados, productos de oportunidades y artículos de Knowledge.
4. Se puede aplicar más de una plantilla de correo electrónico, pueden combinarse para ejecutar la macro.
5. Algunas macros llevan a cabo acciones que no pueden deshacerse (enviar un correo), son las llamadas ‘Macros Irreversibles’ y tienen un icono que identifica la irreversibilidad de la acción que se va a ejecutar.

Una vez tenemos claro en qué consisten los macros y que requisitos necesitamos para poder utilizarlos, pasemos a ver cómo podemos crearlos, de una forma muy sencilla y accesible.

Nota: **En esta entrada de blog nos centraremos en la creación de macros en Lightning.**

El primer punto a tener en cuenta para la creación y configuración de macros, son los permisos de los usuarios, hay que tener claro que:
- Para ver macros deberemos dar el permiso al usuario de sólo lectura.

![Read Only]({{site.url}}{{site.baseurl}}/pictures/20-09-2019/only read.PNG)

- Para crear y editar macros, como en cualquier otro objeto, deberemos añadir los permisos para crear y editar macros

![Crear y editar]({{site.url}}{{site.baseurl}}/pictures/20-09-2019/crear y editar.PNG)

- Para gestionar macros irreversibles existe un checkbox especial en los permisos administrativos:

![Manage macros]({{site.url}}{{site.baseurl}}/pictures/20-09-2019/manage macros.PNG)

Una vez gestionados los permisos, y habiendo añadido, como hemos visto anteriormente, la función macros a la barra de utilidades, podemos proceder a crear una macro. A continuación se detallan los pasos a seguir para crearla.

1. En primer lugar, cómo aún no disponemos de ninguna macro, tendremo que hacer clic en '**Crear macro**', y lo primero que deberemos hacer será ponerle un nombre, una descripción y decidir a qué objeto queremos que se aplique la macro.

![Nueva Macro]({{site.url}}{{site.baseurl}}/pictures/20-09-2019/new macro1.png)

2. Una vez creado deberemos editar las instrucciones que deberá seguir la macro. Para ello, Salesforce abrirá automáticamente una página dividida en dos partes; en la izquierda nos muestra un ejemplo de los posibles registros que se pueden hacer en el objeto seleccionado, y en la parte derecha tendremos el “formulario” para añadir las instrucciones y los detalles que configuran la macro.

Las instrucciones a seguir por la macro pueden depender de una lógica que establezcamos (la macro evaluará si la lógica es verdadera y en ese momento ejecutará la macro) o puede configurarse una o varias instrucciones sin una lógica previa que se ejecutará cuándo nosotros se lo pidamos.

Para añadir lógica podemos poner condiciones complementarias (por ejemplo: que el Caso sea nuevo y que el país sea ‘Alemania’) o alternativas (nuevo Caso y el país sea ‘Alemania’ o ‘España’) para que se ejecute la macro.

A continuación se nos muestra la página que se abre en Salesforce dónde de una manera muy intuitiva se pueden configurar las Instrucciones que queremos que ejecute la macro como la lógica interna que ha de cumplir para que se ejecute.

![Lógica e instrucciones]({{site.url}}{{site.baseurl}}/pictures/20-09-2019/logica e instrucciones.PNG)

En este ejemplo le damos la instrucción de enviar un correo electrónico, en la que podemos estipular que concepto se envía, el template que enviaremos e incluso podremos añadir archivos adjuntos.

Una vez la tenemos creada, si queremos ejecutarla, tan solo tendremos que acceder a la utilidad de macros, seleccionar la macro que queremos ejecutar y, tras revisar las instrucciones que va a ejecutar, hacer clic en Ejecutar macro.

De esta forma ya habremos llevado a cabo una serie de acciones que eran una labor repetitiva para el usuario.

## Ejemplos de utilidad
Por último, para inspirar sobre alguna utilidad que se le puede dar a la herramienta, exponemos una serie de ejemplos:
- Es muy común que en una empresa se produzcan incidencias o Casos muy parecidos o iguales, a los que el trabajador siempre dará la misma solución, para este tipo de acciones son perfectas las macros ya que será mucho más productivo estandarizar la solución (adjuntar automáticamente un artículo de Salesforce Knowledge y un template de correo electrónico), para que los trabajadores puedan contestar de una manera más dinámica.
- Los trabajadores podrán servirse también de las macros para insertar una respuesta automática a las preguntas de sus clientes a través de las redes sociales utilizando Texto rápido. Los trabajadores podrán dar una respuesta rápida y común sin interrumpir su trabajo.
- A parte de enviar correos electrónicos y responder a preguntas frecuentes en las redes sociales, puede ser que los trabajadores de una empresa tengan que completar campos o modificar campos con los mismos valores, para ello, también facilita el trabajo el uso de macros, ya que automatizará estas acciones repetitivas.

---
layout: default
title:  "VAT NUMBER"
date:   2019-07-26 00:02:00 -0800
author: Salva Vidal
categories: VAT
comments: true
---

Seguro que en muchas ocasiones nos hemos encontrado con que los proyectos requieren de un validador de formato del VAT, así como la comprobación de que este esté registrado en el VIES. Vamos a ver como lo podemos implementar!

De entre las aplicaciones que existen en el AppExchange, recomendamos dos aplicaciones que cumplen a la perfección su cometido, y al usarse de manera combinada dan solución a esta necesidad:

* **Astapor VAT Checker**: Aplicación que sirve para certificar que el VAT existe en el VIES.

* **Axy Validator**: Aplicación para crear una regla de validación de la sintaxis del número VAT (entre otras muchas funcionalidades).

Gracias al conjunto de estas dos Aplicaciones, se puede validar y certificar el número VAT de los países de la Unión Europea así como de los países que hayan publicado el formato de su número VAT.

![IMG1]({{site.url}}{{site.baseurl}}/pictures/26-07-2019/15.jpg)

#### **Astapor VAT Checker**

Descarga Astapor VAT Checker [aquí](https://appexchange.salesforce.com/listingDetail?listingId=a0N3A00000EJHTUUA5)

**Astapor VAT Checker** es un Web service que conecta con el VIES de la comisión Europea para comprobar que la compañía a la cual pertenece el VAT introducido está registrada en la base de datos. 

Se distinguen entre dos tipos de campos. 

1. Los que se envían mediante el Web Service para ser comprobados:
    1. **EU Country Code**: Campo de tipo lista con el código de los países disponibles.
    1. **VAT Number**: Campo tipo texto para el número VAT.

1. Los campos de respuesta del Web Service, donde se nos copiará la información que ha devuelto el servicio:
    1. **Company Name**: Nombre de la compañía que nos devuelve el servicio.
    1. **Company Address**: Dirección de la compañía.
    1. **Latest VIES Request Date**: Última fecha en la que se comprobó el VAT.
    1. **Latest VIES Request Status**: Invalid VAT Number o Success.

También hay un Botón/acción **Check VAT Number**, que se ubica en la layout de detalle y sirve para mandar la petición de comprobación del VAT introducido en los campos **EU Country Code y VAT Number**.

Una vez instalada la aplicación, se deberán incluir tanto los campos VAT como los de información de respuesta en la layout del objeto Account. Así como también el botón/acción Check VAT Number.

![IMG4]({{site.url}}{{site.baseurl}}/pictures/26-07-2019/4.png)
> Campos a comprobar y botón de acción

El proceso de validación es el siguiente:

1. Se rellenan los campos **EU Country Code** y **VAT Number** y se guarda el Account.

1. Click en el botón **Check VAT Number**. <br/> <br/> ![IMG5]({{site.url}}{{site.baseurl}}/pictures/26-07-2019/5.png) <br/>

1. Se abrirá una ventana informativa para validar la acción. <br/> <br/> ![IMG6]({{site.url}}{{site.baseurl}}/pictures/26-07-2019/6.png)

1. El proceso tarda unos segundos en completarse. Unos segundos después de cerrar la ventana de confirmación, refrescando el Account obtendremos el resultado de la comprobación en los campos de respuesta. <br/> <br/> ![IMG7]({{site.url}}{{site.baseurl}}/pictures/26-07-2019/7.png)

1. Si en la comprobación el número VAT es erróneo, devolverá un mensaje de error. <br/> <br/> ![IMG7]({{site.url}}{{site.baseurl}}/pictures/26-07-2019/7.png)

Aparte del proceso de comprobación del VAT, el paquete incluye:
* La posibilidad de crear un batch para programar diariamente, semanalmente, etc
* Objeto historial con todas las comprobaciones hechas en el sistema.

#### **Axy Validator**

Descarga Axy Validator [aquí](https://appexchange.salesforce.com/appxListingDetail?listingId=a0N3A00000FHBsRUAX)

Axy Validator es una aplicación que permite configurar reglas de validación sobre cualquier objeto standard o custom de Salesforce de manera sencilla mediante un asistente en sólo cuatro pasos. Estas validaciones pueden ser del tipo:
* Format: valida la longitud y sintaxis de la información introducida
* Checksum: valida que no haya errores tipográficos mediante algoritmos

Una vez instalada, se creará una aplicación **Axy Validator** en el entorno, con 4 pestañas disponibles:
Configured Rules: En este apartado aparecerán todas las reglas de validación existentes en el entorno activo.
Rules Library: Listado de todas las reglas de validación que se pueden crear desde esta aplicación. Con información detallada de cada una, así como una descripción de cómo funciona y ejemplos.
App Admin: Versión instalada (En este caso, versión estándar y gratuita).
Help: Link para ir a la sección de ayuda de la aplicación.

Tal y como se comenta el punto anterior, la sección de Rules Library contiene información detallada sobre cada una de las reglas de validación existentes en la aplicación. por ejemplo, de códigos postales del Inglaterra, CIF Españoles, IBAN (comprobación en un campo o más de uno), VAT number (en un campo o varios)… Etc. 

![IMG1]({{site.url}}{{site.baseurl}}/pictures/26-07-2019/1.png)

> Izquierda: Listado de reglas existentes en la aplicación. <br/>
> Derecha: Información detallada de la regla seleccionada. VAT European Union – MultiField para este ejemplo.

Para configurar la regla de validación de formato del VAT number, se selecciona la pestaña de **Configured Rules** y se hace clic en el botón de **New Rule**.

![IMG2]({{site.url}}{{site.baseurl}}/pictures/26-07-2019/2.png)
> Interfaz de la aplicación.

La creación de la regla de validación consta de 4 sencillos pasos:

**Selección de la regla de validación**

1. **Type**: la diferencia entre los dos tipos de reglas es que Checksum no sólo valida la longitud y la sintaxis del campo, sino que evita cualquier tipo de error.
1. **Package**: Paquete de la aplicación que se usará para la creación de la regla.
1. **Category**: Filtro para reducir la lista desplegable de reglas a disponibles.
    1. **Legal / Tax**: Reglas relacionadas con números de identificación, números sociales, etc.
    1. **Bank Payments**: Reglas relacionadas con números bancarios, tarjetas de crédito, etc.
    1. **Contact Information**: Reglas relacionadas con códigos postales, números de teléfono, etc.
1. **Rule**: Selección de la regla de validación a usar.

Para la validación del número VAT, hay diferentes tipos de de comprobación. Se puede validar el país y el número en un solo campo o en varios campos. En el caso de varios campos, se coge por separado el país y el número.

Como **Astapor VAT Checker** usa dos campos (uno para el país y otro para el número), se usará la regla de validación VAT European Union – multifield.

![IMG9]({{site.url}}{{site.baseurl}}/pictures/26-07-2019/9.png)
> Configuración base de la regla de validación.

### Selección de Objeto y Campos

* **Object**: Objeto sobre el que se creará la regla de validación
* **VAT EU Country Code Field**: Selección del campo del código del país
* **VAT EU Number Multifield**: Selección del campo donde se informará el VAT Number.

Hay que tener en cuenta que la selección del campo se hará siempre, independientemente de que tipo de regla se vaya a crear. Mientras que los otros campos, pueden variar.


![IMG10]({{site.url}}{{site.baseurl}}/pictures/26-07-2019/10.png)
> En esta imagen usamos los campos de la aplicación **Astapor VAT Checker** de Account

### Selección de Acción (Tipo de error)

Se pueden crear 2 tipos de errores.

Validation Action: Tipo de error a crear, existen 2 tipos:
Warning: Permite seleccionar un campo del objeto para guardar el mensaje de error en el caso de que no cumpla con los requisitos configurados, aunque permite guardar el registro.
Error: Impide al usuario guardar el registro si no cumple con los requisitos de la regla de validación configurada, evitando así que exista información incorrecta.
Action Field: Campo donde guardar el mensaje de error en el caso de elegir Warning.
Action Message: Mensaje que se guardarà en el campo de Warning o mensaje de error que mostrará el registro en el caso de Error.

Se pueden crear 2 tipos de errores.

**Validation Action**: Tipo de error a crear, existen 2 tipos:
* **Warning**: Permite seleccionar un campo del objeto para guardar el mensaje de error en el caso de que no cumpla con los requisitos configurados, aunque permite guardar el registro.
* **Error**: Impide al usuario guardar el registro si no cumple con los requisitos de la regla de validación configurada, evitando así que exista información incorrecta.

**Action Field**: Campo donde guardar el mensaje de error en el caso de elegir **Warning**.

**Action Message**: Mensaje que se guardarà en el campo de **Warning** o mensaje de error que mostrará el registro en el caso de **Error**.

![IMG11]({{site.url}}{{site.baseurl}}/pictures/26-07-2019/11.png)
> Tipo de error y mensaje a configurar. 


### Test y Guardar

El último paso es la comprobación de que la regla que se ha configurada da los resultados esperados.

![IMG12]({{site.url}}{{site.baseurl}}/pictures/26-07-2019/12.png)
> Comprobación al introducir un VAT incorrecto
![IMG13]({{site.url}}{{site.baseurl}}/pictures/26-07-2019/13.png)
>Comprobación al introducir un VAT correcto

Una vez guardada la regla de validación, se redireccionarà a la pestaña de *Configured Rules*, donde estarán todas las Validation Actions creadas en el sistema a través de esta App. Desde este momento, si se intenta editar el campo de VAT que hemos seleccionado con un número que no siga la sintaxis correcta, nos aparecerá un error que o bien nos impedirá guardar, o nos advertirá del error (en función de cómo hayamos configurado la Validation Action).

![IMG14]({{site.url}}{{site.baseurl}}/pictures/26-07-2019/14.png)
> Reglas de validación existentes en el sistema. 

De esta manera, con el uso conjunto de estas dos aplicaciones encontramos una solución cómoda y eficaz tanto para la validación de los VAT Number de las empresas, así como para la comprobación de su registro en el VIES. Las ventajas de utilizar estas aplicaciones son principalmente la facilidad de instalación y configuración, que nos ahorrarán tiempo y esfuerzos en configurar reglas de validación a través de código o fórmulas complejas...





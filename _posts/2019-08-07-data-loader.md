---
layout: default
title:  "Uso y configuración del data loader"
date:   2019-08-07 00:02:00 -0800
author: Albert Bofill 
categories: Data Loader Salesforce
comments: true
---

¿Se acerca una migración de datos y tienes dudas sobre cómo hacerlo? 
¿Intentas migrar los datos de un campo fecha/hora y tienes problemas? 
¿Se te queda pequeño Import Wizard?

Existen diferentes soluciones para cargar datos a Salesforce, pero hoy nos centraremos en la más utilizada de ellas, Data Loader. Es una aplicación cliente que se utiliza para insertar, actualizar, eliminar o exportar registros en bulk a Salesforce. 

A lo largo de este post explicaremos cómo hacer la instalación, así como su configuración y también os daremos algún consejo para evitar problemas comunes.

### Instalación de Data Loader

Antes de empezar hay que tener en cuenta dos cosas:

* La instalación para Windows y Mac OS se hace de la misma forma, aunque el nombre del fichero a ejecutar es diferente. Nosotros instalaremos la versión para **Mac OS**. [Aquí](https://help.salesforce.com/articleView?id=loader_install_windows.htm) dejo un enlace con las instrucciones para Windows, pero el procedimiento es el mismo. 

* Desde la versión 45 de Data Loader en adelante, se necesita instalar **Zulu OpenJDK 11**. Entraremos a este [link](https://www.azul.com/downloads/zulu-community/) y descargaremos el archivo en formato .dmg en la versión 10.0.4+11.

<br/>

![Descarga de Zulu OpenJDK]({{site.url}}{{site.baseurl}}/pictures/10-07-2019/1.png)

<br/>

![Instalación Zulu OpenJDK]({{site.url}}{{site.baseurl}}/pictures/10-07-2019/2.png)

<br/>

Cuando el proceso de instalación termine, nos dispondremos a instalar Data Loader desde el [siguiente](https://aecoc.my.salesforce.com/dwnld/DataLoader/dataloader_mac.zip) enlace. 

Una vez descargado, descomprimimos el zip y ejecutamos el fichero install.command. Nos preguntará si queremos poner un acceso directo a la carpeta de aplicaciones y al escritorio, y en ambos casos diremos que **sí**. 

![Instalación Data Loader]({{site.url}}{{site.baseurl}}/pictures/10-07-2019/3.png)


### Configurar Data Loader

En esta sección vamos a hablar sobre algunas de las opciones que podemos configurar en Data Loader para evitarnos posibles complicaciones. 

![Configuración Data Loader]({{site.url}}{{site.baseurl}}/pictures/10-07-2019/4.png)

> (Menú al cual accederemos desde settings - settings).

* **Batch size**: El batch size es el número de registros que Data Loader va a procesar en cada operación de insert/update/upsert/delete. **Es la opción recomendada si los datos a tratar son de objetos que tienen lógica por detrás**.  El máximo permitido por defecto es de 200 registros, aunque Salesforce recomienda que sean entre 50 y 100. De todas maneras, existe una opción que comentaremos más adelante que nos puede permitir procesar hasta 10000 registros por operación. 

* **Read all CSVs with UTF-8 Encoding**: Al abrir un fichero CSV para hacer insert/update/upsert, Data Loader lo cargará en codificación UTF-8. Recomendamos encarecidamente usar esta opción ya que si no se carga en UTF-8, es posible que tanto los acentos como los caracteres especiales se puedan ver afectados, y no se cargarían los datos correctamente.
 
* **Write all CSVs with UTF-8 Encoding**: Cuando termina de cargar o borrar datos, Data Loader genera dos ficheros. (Uno para las llamadas que dan error y otro para las llamadas que han salido bien). Si activamos esta opción los resultados nos aparecerán en el formato UTF-8.

* **Use European Date format**: Data Loader por defecto usa el formato internacional para los campos tipo fecha (yyyy-mm--dd). Si activamos el formato europeo podremos subir las fechas con el formato (dd-mm-yyyy).

* **Allow comma as a CSV delimiter, Allow tab as a CSV delimiter, Allow other characters as a CSV delimiter, Other Delimiter**: Estas opciones nos permiten indicar los caracteres con los que vienen delimitadas las columnas del CSV. Normalmente los CSV vienen delimitados por comas, aunque con estas opciones se puede usar TAB o caracteres personalizados como por ejemplo, en este caso, el punto y coma (;).
 
* **Use Bulk API**: Esta opción está pensada para insertar/actualizar/eliminar datos relacionados con objetos que no tienen lógica por detrás. Acepta un máximo de 10.000 registros por operación y por lo tanto hace que los datos se suban mucho más rápido. 

* **Time Zone**: Esta opción es para configurar la zona horaria. Esto es muy importante ya que si por ejemplo tenemos GMT + 2, pero la organización está en GMT, te restará dos horas pudiendo afectar la fecha del registro. 


### Consejos sobre formatos de datos para cargar los datos correctamente. 

Vamos a daros algún consejo para ver qué formatos pueden usarse en ciertos tipos de datos. 

* **Checkbox**: Para los datos booleanos podemos rellenar las columnas de dos formas. Con true o false o con 1 y 0. Salesforce acepta ambas opciones.

* **Fecha/Hora**: Para los campos de fecha/hora podemos usar diferentes formatos. 
    * YYYY-MM-DD (DD-MM-YYYY en formato europeo)
    * YYYY/MM/DD (DD/MM/YYYY en formato europeo)
    * YYYY-MM-DD hh:mm:ss
    * YYYY-MM-DDThh:mm:ssZ
    * YYYY-MM-DDThh:mm:ss.sssZ


* **Long Area Text**: Es importante tener en cuenta para estos campos que si hay saltos de línea el excel no lo haya puesto en una fila nueva porque si no Data Loader dará error al cargar el archivo. <br/>



Una vez instalado y configurado, Data Loader será una herramienta mucho más útil que, por ejemplo, Import Wizard (herramienta similar ofrecida de forma nativa por Salesforce). Creemos que Data Loader es la mejor solución ya que gracias a su rápido funcionamiento, su flexibilidad y el gran volumen de registros que es capaz de procesar, es la herramienta preferida por los administradores.

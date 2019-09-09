---
layout: default
title:  "Salesforce para el sector Real Estate"
date:   2019-09-06 00:02:00 -0800
author: Hernan Lizondo
categories: Real Estate Salesforce Integration
comments: true
---

Todos sabemos que Salesforce es el CRM número 1 del mundo y que se puede adaptar a cualquier tipo de negocio y sector. De hecho, eso es lo que lo hace ser el número 1. Pero, ¿se puede llegar a adaptar a un sector tan específico y con tantas peculiaridades como lo es el Real Estate?

A lo largo de esta entrada podremos ver detalladamente la adaptación que Blackbird ha hecho para el sector Real Estate. 

El modelo de datos en el que se ha basado es el siguiente:

![Modelo de datos]({{site.url}}{{site.baseurl}}/pictures/06-09-2019/Modelo Datos.png)

A grandes rasgos, veremos como la integración de Salesforce con Real Estate gira sobretodo en torno al Cliente y a los Inmuebles; que está integrado con la web, con los portales y con Google Maps para poder publicar y pedir información de los diferentes sistemas; y que la gestión de visitas y la firma de los contratos se hará digitalmente desde un ordenador, tablet o móvil.

## Inmuebles

Dentro del objeto de Salesforce dedicado al ‘Inmueble’, tenemos recogida toda la información del mismo: sus clientes potenciales, la actividad que han realizado nuestros comerciales con ellos, las descripciones en diferentes idiomas, el contrato que han firmado con nosotros, así como también el histórico de visitas. Todo organizado de una manera clara y simple, facilitando la comprensión el usuario al máximo.

Desde el registro de un inmueble, es posible añadir imágenes, ordenarlas fácilmente, así como también decidir si se quiere que esas imágenes se envíen o no a la web y a lo portales inmobiliarios.

![Imágenes inmuebles Salesforce]({{site.url}}{{site.baseurl}}/pictures/06-09-2019/Imagenes inmuebles Salesforce.png)

Otras funcionalidades a destacar de nuestra integración con el sector son, por un lado, un componente en el que podemos ver en qué zona se sitúa en un mapa de Google Maps; y por otro lado, la posibilidad de hacer tasaciones desde la misma página (en base al histórico de propiedades vendidas o alquiladas).

![Tasacion inmuebles Salesforce]({{site.url}}{{site.baseurl}}/pictures/06-09-2019/Tasacion inmbueble Salesforce.png)

Los inmuebles que más gusten se pueden seleccionar y es posible realizar ciertas acciones con ellos, así como enviar por email al cliente, generar un PDF con ellos, o añadirlos a la lista de inmuebles por los que se ha interesado un cliente.

![Inmuebles favoritos]({{site.url}}{{site.baseurl}}/pictures/06-09-2019/Inmuebles favoritos Salesforce.png)

![Generar listado PDF]({{site.url}}{{site.baseurl}}/pictures/06-09-2019/Automatismos Salesforce.png)

## Búsquedas

Desde este objeto, se ha desarrollado una herramienta de búsqueda que permite establecer unos parámetros de búsqueda para seleccionar de entre toda la lista de inmuebles, aquellos que encajen con las necesidades de un cliente determinado. Los resultados de estas búsquedas pueden presentarse en formato de lista, de mosaico o de mapa.

![Tabla]({{site.url}}{{site.baseurl}}/pictures/06-09-2019/Lista de inmuebles Salesforce.png)

![Mosaico]({{site.url}}{{site.baseurl}}/pictures/06-09-2019/Busqueda propiedades Salesforce.png)

![Mapa]({{site.url}}{{site.baseurl}}/pictures/06-09-2019/Buscador de propiedades Salesforce.png)

En cuanto a la visualización de resultados de búsqueda en formato de mapa, es posible además ver aquellos puntos de interés que estén situados en sus proximidades, tales como escuelas, universidades, hospitales, dentistas, paradas de tren, metro, autobús, etc..

![Mapa Salesforce]({{site.url}}{{site.baseurl}}/pictures/06-09-2019/Mapa Salesforce.png)

Esta herramienta permite, una vez se dispone de un resultado de búsqueda, añadir con solamente dos clicks la lista de inmuebles resultante al perfil de un Cliente.

## Clientes

Dentro de este objeto, tenemos acceso de una manera muy intuitiva a toda la información básica del cliente, así como su nombre, su dirección, su sitio web, su teléfono, etc.; y también al registro de la actividad que se ha tenido con él, como emails, llamadas, tareas, etc.

![Perfil cliente 2]({{site.url}}{{site.baseurl}}/pictures/06-09-2019/Cliente.png)

En cuanto a desarrollos específicos para este sector, como hemos comentado antes, se ha habilitado el acceso desde la misma ficha o página del cliente a una lista con aquellos inmuebles que le interesan y aquellos que ya no; también a un histórico de todas las búsquedas que ha realizado a través de la herramienta ‘Búsquedas’ y a sus resultados.

![Perfil cliente]({{site.url}}{{site.baseurl}}/pictures/06-09-2019/Perfil cliente inmobiliaria Salesforce.png)

## Firma digital

Aquellos documentos que se deban firmar con el cliente pueden firmarse digitalmente desde un ordenador, un móvil o una tablet a través de una herramienta desarrollada para este fin. Esto permite simplificar al máximo el proceso de firma y generar un documento en PDF al instante con las firmas necesarias.

![Firma digital]({{site.url}}{{site.baseurl}}/pictures/06-09-2019/Firma digital Salesforce.png)

## Paneles e Informes

Mediante los Informes de Salesforce es posible combinar la información existente en todos los objetos de la Org. Por ejemplo, es posible configurar un informe que cruce la información contenida en los registros de Inmuebles con la contenida en los registros de Clientes, de manera que podamos tener una visión clara de cuáles son aquellos inmuebles con más clientes interesados; o también es posible analizar a los inmuebles en función de su estado (reservado o disponible). 

La ventaja de construir informes a través de Salesforce es que estos son dinámicos y se actualizan a medida que los datos lo hacen, de manera que solamente es necesario crear un informe determinado una vez y, y al refrescar este se actualiza solo.

Una vez hemos creado un informe, podemos construir paneles para representarlos gráficamente, mediante gráficos de tipo donut, embudo, gráfico de barras, líneas, mapa de dispersión, etc; y podemos incluirlos en nuestra página de inicio para tener así una visión clara del estado de nuestro negocio justo al entrar a Salesforce.

![Dashboard]({{site.url}}{{site.baseurl}}/pictures/06-09-2019/Dashboards Salesforce.png)

## Conclusión

Como hemos podido observar, Salesforce es la solución perfecta para Real Estate. 

Nos ofrece toda la versatilidad y potencia que caracteriza a Salesforce y que la convierten en líder del sector, como la gestión de los clientes e inmuebles la confección de informes y paneles, la creación de reglas de validación y de automatizaciones, etc.

Gracias a la integración con los portales inmobiliarios, toda creación, modificación o eliminación de datos que realicemos en nuestra base de datos en Salesforce se trasladará de forma automática y en tiempo real a los mismos. Esta integración permite que la introducción manual de la información sólo se tenga que hacer una vez, reduciendo así considerablemente la posibilidad de errores humanos y reduciendo el tiempo que se le ha de dedicar.

Adicionalmente, gracias a los desarrollos específicos que hemos ido comentando a lo largo del artículo, podemos afirmar que la adaptación de Blackbird de Salesforce al sector del Real Estate permite, entre otras cosas, tener:
- Una visión 360º del cliente
- Una visión 360º del inmueble y de toda la información relacionada
- Firma digital
- Automatizaciones para el cliente
- Buscador dentro de Salesforce
- Integraciones con WEB
- Integraciones con portales
- Etc.

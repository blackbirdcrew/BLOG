**Integración de Gmail con Salesforce**
</br>
</br>
</br>
![IMAGE 1](https://github.com/blackbirdcrew/BLOG/blob/master/pictures/10-07-2019/1.png)

La integración consta de dos partes:

La primera nos permite sincronizar los emails y tener acceso a los registros de Salesforce desde Gmail. La segunda nos permite sincronizar los contactos y el calendario entre ambos sistemas.

Para poder configurar ambas deberemos ir desde Salesforce a Configuración → Integración de Gmail y Sincronización.

La primera es la más sencilla. Para empezar con ella solo tenemos que **Activar** el botón para permitir a los usuarios acceder a registros de Salesforce desde Gmail. Una vez hecho esto, se nos abre un desplegable con una serie de configuraciones que ahora os explicamos.

![IMAGE 2](https://github.com/blackbirdcrew/BLOG/blob/master/pictures/10-07-2019/2.png)

 1. _Utilizar correo electrónico mejorado con Gmail._
Nos permite relacionar los correos electrónicos en el objeto estándar de Salesforce ‘EmailMessages’ en vez de tareas, lo que les permitirá poder contestar al email desde Salesforce. Se activa automáticamente excepto si estáis utilizando el correo electrónico para el registro de casos en Salesforce.

 1. _Inbox en integración con Gmail._
Deberemos activarlo en caso de que dispongamos de licencias de Inbox. La licencia de Inbox es una licencia complementaria de Salesforce que permite incrementar aún más la productividad añadiendo nuevas características a la integración con Gmail como por ejemplo:
    1. Tracking de emails.
    1. Programación de envíos a horas concretas.
    1. Envío de una serie de fechas disponibles para agendar una reunión a los clientes o prospects para que puedan escoger una, quedando esta agendada automáticamente.

 1. _Seleccionar quién puede utilizar Inbox._
En caso de disponer de una licencia Inbox se deberá crear un Conjunto de permisos para poder asignar a los usuarios correspondientes el acceso a todas estas herramientas (https://help.salesforce.com/articleView?id=inbox_setup_select_users.htm&type=5)


 1. _Mantener Gmail y Salesforce conectados._
Una vez activado permitirá a los usuarios no tener que iniciar sesión desde el complemento de Gmail cada vez que se conecten.

 1. _Paneles de aplicaciones de correo electrónico._
Funciona como el editor de una página de Lightning. Podemos crear desde cero cómo van a ver nuestros usuarios el aplicativo de Salesforce desde Gmail.
En primer lugar podemos añadir una serie de pestañas para ordenar la información que vamos a mostrar. Podemos agregar un acceso rápido a las plantillas de email que tengamos creadas en Salesforce, a los registros de Contactos (cualquiera que esté en el email y que esté creado en Salesforce), Cuentas, Oportunidades y Casos; así como a cualquier otro objeto relacionado, como tareas, chatter, gráficos de informes o vistas de lista.

 1. _Asignaciones por perfil de Panel de aplicación de correo electrónico._
Desde aquí podemos asignar los diferentes paneles que hemos creado. Podemos asignarlos de manera predeterminada para toda la Organización o asignar uno diferente por perfil de usuario.

 1. _Formatos del publicador de aplicación de correo electrónico._
Nos permite escoger qué acciones rápidas vamos a hacer desde el panel de gmail. Al igual que los paneles, podemos crear diferentes formatos con diferentes acciones y asignarlos por perfil. 
Las acciones disponibles por defecto son: Nuevo evento, Nueva cuenta, Nueva tarea, Nuevo contacto, Nueva oportunidad, Nuevo caso, Nuevo candidato, Follow up de un registro y Nuevo grupo. 


La segunda parte de la integración nos permite poder tener sincronizados en todo momento los contactos de ambos sistemas así como los calendarios. Una vez activado se nos abrirá un desplegable con tres partes.

![IMAGE 3](https://github.com/blackbirdcrew/BLOG/blob/master/pictures/10-07-2019/3.png)

**Conectar Salesforce y Google** consta de tres pasos:
1. Registrar Google con Salesforce: Simplemente hay que clicar en el botón de Registrar sitio. Crear un nuevo sitio remoto com un nombre que nos ayude a identificarlo en el futuro (por ejemplo: Google Integration) y en la URL la siguiente dirección: https://www.googleapis.com.
1. Una vez hecho esto deberemos ir a nuestra cuenta de Gsuite para configurarla y poder seguir la integración: https://help.salesforce.com/articleView?id=lightning_sync_admin_google_prep_google.htm
1. Una vez generada la clave y cargada a Salesforce solo nos faltará comprobar que la conexión es correcta. Una vez hecha la prueba nos debería devolver dos mensajes. Uno con el estado de la integración a nivel de contactos y otro del calendario.

![IMAGE 4](https://github.com/blackbirdcrew/BLOG/blob/master/pictures/10-07-2019/4.png)

Para acabar con la configuración deberemos definir los criterios de sincronización que queremos aplicar. 

Clicamos en Ir a configuraciones → Nueva configuración. Le damos un nombre que nos ayude a identificar qué configuración es ya que en el siguiente paso podemos asignarla a usuarios concretos, o por perfil. De esta manera podríamos tener una serie de diferentes configuraciones dependiendo de cada uno de los usuarios.

Una vez asignados los perfiles o usuarios que van a utilizar ésta configuración deberemos escoger las particularidades de la integración para Contactos y Eventos.

**Contactos:**

En primer lugar escogeremos la dirección de la sincronización. 

* **Google to Salesforce:** Si queremos que los contactos de Google se creen y actualicen en Salesforce pero no a la inversa.

* **Salesforce to Google:** Si queremos que los contactos de Salesforce se creen y actualicen en Google pero no a la inversa.

* **Don’t Sync:** En el caso en que no queramos que se sincronicen los contactos en ninguna dirección.

* **Sync both ways:** Cuando queremos que se sincronicen en ambas direcciones.

En segundo lugar escogeremos cómo queremos que se actúe en caso de que un contacto de Google coincida con múltiples contactos de Salesforce.
En este caso las opciones que tendremos son:

* **Has the most recent activity:** En este caso se sincronizará con aquel registro de Salesforce con el que hayamos tenido una Tarea o un Evento más recientemente.

* **Was last updated:** En este caso se sincronizará con el registro de Salesforce que tenga la fecha de última modificación más reciente.

* **Has the oldest creation date:** En este caso se sincronizará con el registro de Salesforce que tenga la fecha de creación más antigua.

**Eventos:** 

Al igual que con los contactos, en primer lugar escogeremos la dirección de la sincronización. 

* **Google to Salesforce:** Si queremos que los contactos de Google se creen y actualicen en Salesforce pero no a la inversa.

* **Salesforce to Google:** Si queremos que los contactos de Salesforce se creen y actualicen en Google pero no a la inversa.

* **Don’t Sync:** En el caso en que no queramos que se sincronicen los Eventos en ninguna dirección.

* **Sync both ways:** Cuando queremos que se sincronicen en ambas direcciones.

Para aquellas configuraciones en las que se sincronice de ‘Google a Salesforce’ o ‘Sync both ways’ podremos escoger si queremos que se sincronicen todos los eventos o solo aquellos que el usuario escoja.

Una vez guardemos la configuración nos pedirá los filtros a aplicar para los contactos y los eventos que se van a sincronizar. 

En primer lugar para los contactos podremos seleccionar si queremos que se sincronicen todos los contactos del usuario o solamente aquellos que escoja y podemos añadir filtros específicos relativos a cualquier campo del registro. 
Por ejemplo: Si queremos que solo se sincronicen los contactos a medida que los vayamos modificando podríamos añadir un filtro diciendo que la fecha de modificación fuera a partir de la fecha de hoy.

En segundo lugar, para los eventos nos pedirá definir a partir de qué fecha queremos que se sincronicen los eventos. Adicionalmente podremos decidir si queremos que se sincronicen los eventos privados o no, si queremos que se eliminen los eventos de Salesforce automáticamente al eliminar el mismo evento sincronizado en Google y si queremos que Salesforce relacione automáticamente los eventos con Contactos o Leads s los invitados al eventos existen como registros en Salesforce.

Una vez hecho esto ya podemos guardar y ya tendríamos la integración finalizada.

Como último paso, solo faltaría instalar la extensión de Chrome y una vez hecho, iniciar sesión a nuestra cuenta de Salesforce.

![IMAGE 5](https://github.com/blackbirdcrew/BLOG/blob/master/pictures/10-07-2019/5.png)

Extensión [Salesforce](https://bit.ly/2T5GK7H)

Extensión [Inbox](https://bit.ly/2ewX89u)












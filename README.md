Proyecto Final - Practicas Iniciales


Escuela de Practica Supervisada, Facultad de Ingenieria


Universidad de San Carlos de Guatemala

Este repositorio contiene la documentacion y las especificaciones de diseno e implementacion del Proyecto Final de Practicas Iniciales (Seccion F). El objetivo del proyecto consistio en disenar, configurar e integrar una infraestructura de red y servicios empresariales distribuidos utilizando un esquema de virtualizacion robusto sobre dos servidores fisicos de prueba.

Arquitectura y Esquema Fisico

La topologia del proyecto se implemento utilizando dos computadoras fisicas (HOST) interconectadas por medio de un Router/Switch. Cada HOST actua como un servidor de virtualizacion, alojando en total el ecosistema completo repartido en solo 2 Servidores Virtuales y sus respectivos clientes corporativos (incluyendo un entorno Linux obligatorio).

Direccionamiento IP Base:

• Host 1 (Fisico): 192.168.0.2
	• IPs Virtuales asociadas: 192.168.0.10 / 192.168.0.5
• Host 2 (Fisico): 192.168.0.4
	• IPs Virtuales asociadas: 192.168.0.15 / 192.168.0.20

Servicios Configurados


1. Gestion de Identidades y Red (Active Directory & DHCP)

• Dominio Principal: Configuracion del servicio de Active Directory (AD) y DNS bajo el dominio local servidor1.com.
• Usuarios del Dominio: Creacion y provision de cuentas base (usuario1 y usuario2) integradas globalmente.
• Politicas de Grupo (GPO):
	• Despliegue automatizado del fondo de pantalla institucional (Logo de la universidad) al iniciar sesion en el dominio.
	• Implementacion de politicas aplicables avanzadas en beneficio de la organizacion en sustitucion de la directiva heredada de Internet Explorer (depreciada en Windows Server 2012).
• Servidor de IPs Dinamicas (DHCP): Configurado sobre el segmento 192.168.0.1 al 192.168.0.100.
	• Rango Reservado (Estaticas): 192.168.0.1 a 192.168.0.25.
	• Rango Dinamico (Pool): 192.168.0.26 a 192.168.0.100.

2. Recursos Compartidos y Almacenamiento (File System Server)

• CarpetaPrivada: Espacio restringido exclusivo para usuarios con credenciales administrativas (usuario=privado y contrasena=privado) con permisos de lectura, modificacion y eliminacion.
• CarpetaPublica: Directorio de acceso libre y colaborativo para todos los usuarios del dominio para copiar, eliminar y modificar archivos.
• Impresora Compartida: Recurso compartido instalado fisicamente en la computadora donde se encuentra virtualizado el servidor web, habilitado para la cola de impresion corporativa.

3. Infraestructura de Correo Electronico (Doble Entorno)

• Servicios Nativos (POP3/SMTP): Plataforma de mensajeria liviana integrada directamente con el Primary Domain Controller (PDC) de servidor1.com. Se implemento una solucion alternativa de terceros para suplir el rol POP3 ausente de forma nativa en Windows Server 2012. Pruebas de buzon validadas mediante clientes multiplataforma como Thunderbird.
• Microsoft Exchange Server: Entorno robusto de mensajeria aislado operando en un nuevo dominio (servidor2.com) y asignando el dominio de correos correo2.com. Dispone de acceso Webmail protegido; ante credenciales erroneas, el portal deniega el acceso con la cadena exacta: "DATOS INCORRECTOS DE AUTENTICACION".

4. Servidor Web (IIS) y Transferencia de Archivos

Alojado bajo Internet Information Services (IIS) en la direccion virtual de servicios (192.168.0.20):
• Sitio Publico (publicweb): Mapeado en C:\Inetpub\wwwroot\publicweb, desplegando el mensaje "Carpeta Publica".
• Sitio Privado (privadaweb): Publicado restrictivamente en el puerto 1080 bajo la ruta C:\Inetpub\wwwroot\privadaweb con acceso para el usuario privado.
• Redireccionamiento de Correo (correo): Configuracion de redireccion avanzada de HTTP (forward) a nivel de IIS hacia el portal de Webmail corporativo.
• FTP & Telnet Nativos: Servicios de administracion remota por consola y transferencia segura de archivos indexados directamente hacia la carpeta publica con privilegios para usuario1 y usuario2.

5. Acceso Remoto y Seguridad (Proxy & RDP)

• Control Remoto: Activacion de Escritorio Remoto (RDP) en el servidor Exchange para el usuario administrador y aprovisionamiento obligatorio de la caracteristica de RDP via Web.
• Servidor Proxy Perimetral: Implementado a traves del rol de Web Application Proxy (WAP) en Windows Server 2012 ante la remocion de las herramientas tradicionales.
	• Escucha en el puerto 8080.
	• Exige autenticacion obligatoria delegada contra el Active Directory.
	• Manejo de excepciones de inicio de sesion con el mensaje: "DATOS INCORRECTOS DE AUTENTICACION".

Guia de Despliegue y Verificacion

Para simular o auditar el entorno montado en produccion:
1. Fase de Virtualizacion: Inicializar los entornos virtuales en los dos HOST fisicos asegurando que las interfaces de red interna apunten al switch logico comun en el segmento 192.168.0.0/24.
2. Validacion de Identidad: Iniciar el cliente Linux obligatorio unido al dominio y verificar la resolucion de nombres DNS. Comprobar la asignacion dinamica de direccionamiento IP mediante DHCP dentro del rango .26 a .100.
3. Comprobacion de Sitios Web:
	• Navegar a 192.168.0 para observar el index publico.
	• Navegar a 192.168.0 e ingresar credenciales validas para el entorno restringido.
4. Prueba de Mensajeria: Realizar un envio de correo de prueba entre las cuentas configuradas utilizando el cliente de correo en el entorno Linux corporativo.

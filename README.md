# @SPC

@SPC es la platafomra de gestión de siniestros de salud de AXA.

## Instalación

1. Clonar el proyecto
```bash
git clone https://github.axa.com/axaes-core-business-office/claims-mgmt-psal.git
```
2. Crear estructura de carpetas. Se recomienda usar la siguiente estructura de carpetas:

J:/SPC  
&nbsp;&nbsp;&nbsp;&nbsp;└ etc (copiado de /utilidades)  
&nbsp;&nbsp;&nbsp;&nbsp;└ java (copiado de C:/)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;└ jdk1.6.0_45  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;└ jdk1.7.0_45  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;└ jdk1.8.0_45  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;└ jdk1.8.1_92  
&nbsp;&nbsp;&nbsp;&nbsp;└ servers  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;└ jboss-eap-6.3 (copiado de C:/)  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;└ modules (descomprimidos de /utilidades)    
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;└  axa-cli  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;└  axa-modules  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;└  axis-1.1  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;└  bpms-libraries  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;└  coco-modules  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;└  med-modules  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;└  spc-modules  
&nbsp;&nbsp;&nbsp;&nbsp;└ workspace (el workspace de Jboss Developer)

3. Iniciar JBoss Developer - Actualmente estamos usando el Red Hat CodeReady Studio - Version: 12.16.0.GA
![image](https://media.github.axa.com/user/2733/files/5e54f580-a129-11ec-81fa-1ae9789630f9)
4. Importar todos los proyectos desde el repositorio git menos los de Staffware
5. Importar el fichero de propiedades localizado en /utilidades/spc-6.epf
6. Añadir un nuevo servidor JBoss EAP 6.3 SPC Server. Copiar el fichero utilidades/standalone-spc.xml a jboss-eap-6.3/standalone/configuration/
7. Configurar el servidor con los siguientes parámetros:

Program arguments
```bash
-mp "J:/SPC/servers/jboss-eap-6.3/modules;J:/SPC/servers/modules/axa-modules; J:/SPC/servers/modules/spc-modules; J:/SPC/servers/modules/bpms-libraries;P:/axa-conf" -jaxpmodule javax.xml.jaxp-provider org.jboss.as.standalone -b 0.0.0.0 --server-config=standalone-spc.xml
```

VM arguments
```bash
"-Dprogram.name=JBossTools: JBoss EAP 6.3 SPC Server" -server -Xms256m -Xmx256m -Dorg.jboss.resolver.warning=true -Djava.net.preferIPv4Stack=true -Dsun.rmi.dgc.client.gcInterval=3600000 -Dsun.rmi.dgc.server.gcInterval=3600000 -Djboss.modules.system.pkgs=org.jboss.byteman -Djava.awt.headless=true "-Dorg.jboss.boot.log.file=J:/SPC/servers/jboss-eap-6.3/standalone/log/boot.log" "-Dlogging.configuration=file:/J:/SPC/servers/jboss-eap-6.3/standalone/configuration/logging.properties" "-Djboss.home.dir=J:/SPC/servers/jboss-eap-6.3" -Dorg.jboss.logmanager.nocolor=true -Dfile.encoding=UTF-8 -Djboss.bind.address.management=localhost -Djavax.net.ssl.trustStore="P:/axa-conf/keystores/odh-keystore.jks" -Djavax.net.ssl.trustStorePassword="@spc-odh"
```
Tomar en cuenta que las rutas de la carpeta modules y axa-conf sean las nuestras.  

8. Modificar la variable PATH de la pestaña Environment con el valor: 
```bash
native;${env_var:PATH};J:/SPC/servers/modules/axa-modules/com/sap/jco/main
```

9. Cambiar el Deploy Directory (en la pestaña Deployment) por:
```bash
J:/SPC/servers/deployments/spc
```

![image](https://media.github.axa.com/user/15015/files/b664a06a-290a-43be-b5f3-ecfa9d4b8a44)
Observación: Es importante seleccionar "Deploy proyects as compressed archives". (En caso de haber añadido ya las aplicaciones spc-biz y spc-web, se deberán eliminar para poder editar esta página y posteriormente guardar los cambios.)

10. Añadir las aplicaciones spc-biz y spc-web al servidor
11. Hacer un clean de los proyectos y el servidor

## Compilación y generación de versión instalable
Para generar una versión estable de cualquier rama, se hace con la herramienta ANT.

Debido a los cambios en AXA-IT para desplegar los artefactos, se han realizado algunos cambios en el script de compilación.
Cada entorno tiene su propia rama de la cual se genera la versión que corresponde a cada una de ellas después de aplicar los cambios, fix, etc., que hayan surgido. 
Por tanto la forma de generar la versión para cada entorno debe incluirse con el parámetro en la línea de comando como se muestra a continuación.
En la raíz del proyecto abrir una consola de comandos y ejecutar:

```bash
ant all -DSUFFIX_BRANCH=DEV --> development
ant all -DSUFFIX_BRANCH=PRE --> preproducion
ant all -DSUFFIX_BRANCH=PRO --> produccion
```

**NOTA:** Si no indica el parámetro de entrada para generar la versión con el sufijo correspondiente desde el inicio como se indica en la línea de comando anterior,
más a delante, durante el proceso de empaquetado, se le pedirá el sufijo; alguno de los anteriores.
```
    [input] Please enter suffix branch name to generate valid release:
```
Ingrese el sufijo y presione ENTER/INTRO para continuar el proceso.

##Proceso deploy en N-2 (develop)
Para desplegar el paquete generado, se debe realizar por Jenkins. Deberá tener acceso a la plantaforma.
1. Dentro de la plataforma, debe ir al job [develop](https://jenkinsiaas.axa-seguros-es.intraxa/jenkins/job/SPC/job/deploy/job/dev/)
2. Ejecute el proceso de deploy mediante el enlace **Build Now** o en su correspondiente idioma de su configuración de usuario.
3. En el paso 2 del proceso de deploy, le pedirá la versión a desplegar en el servidor JBoss, por defecto a aprece la última cargada con el proceso de compilación. Seleccionela y continúe con el proceso.
4. Una vez terminado el job de despliegue, compruebe que se ha instalado el paquete en el correspondiente entorno, en este caso develop.
La versión desplegada, debe coincidir con la generada, si ha seleccionado la última versión.
5. Para comprobar la versión desplegada, ejecute el siguiente enlace en el navegador. [Test deploy](https://apps-dv.axa-seguros-es.intraxa/SPCWeb/appstatus.html). El resultado en la página mostrada es lo siguiente: Entorno, Version, Servidor y un TimeStamp paramostras refresco.
```
DEVELOPMENT 20221017d111257 svc_se_jb6psal@DAMA9002.ppprivmgmt.intraxa 1666014276139
```

Para los entornos superiores, se debe enviar por correo la versión disponible (subida al repositorio Artifactory) a AXA-IT con los cambios incluidos en la release.



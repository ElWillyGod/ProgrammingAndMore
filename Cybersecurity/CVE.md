
# CVE

El sistema de CVE sirve para proporcionar un estándar para identificar y referenciar las vulnerabilidades conocidas públicamente en software y hardware.
Tiene un estándar para referenciar que es muy conocido y utilizado de forma universal tanto para investigadores como para fabricantes entre otros, algunos también cuentan un una puntuación de CVEs que ayuda a medir la gravedad de cada vulnerabilidad.

Su principal contribución es en las herramientas de automatización como escáneres de vulnerabilidades, sistemas de gestión de parches o SIEM y la estandarización de referencias para la documentación en auditorias para cumplir estándares como el ISO 27001.

## CVEs

Su sección de CVEs es muy interesante, como se menciona anteriormente este sistema es muy útil para sistemas de automatización de escáneres de vulnerabilidades, pero también tiene un impacto directo en como una organización prioriza sus medidas de seguridad, estas vulnerabilidades se miden en CVSS (Common Vulnerability Scoring System) que asigna una puntuación de 0 a 10 y se clasifican en cuatro niveles: bajo, medio, alto y crítico.

Cada nivel tiene un rango en la clasificacion.

### Bajo (CVSS 0.1 – 3.9)

Esto puede ser un error en una validación que solo se puede explotar en condiciones poco comunes y sin impacto real en la seguridad, un ejemplo real podría ser un mensaje de error que revela el sistema operativo pero no de acceso adicional al atacante.

### Medio (CVSS 4.0 – 6.9)

En este rango se puede encontrar un XSS (Cross site Scripting) que requiere que un usuario haga clic en un enlace malicioso, esto podría ser una vulnerabilidad en una aplicación interna de recursos humanos con acceso limitado.

### Alto (CVSS 7.0 – 8.9)

Este tipo de vulnerabilidades permite escalar privilegios local en un servidor, representa una riesgo alto para los servidores  y se necesario aplicar un parche lo antes posible y a que pude ser una falla en el kernel de linux que permite a un usuario normal obtener acceso a root.

### Crítico (CVSS 9.0 – 10.0)

Estas vulnerabilidades tienen que ser solucionadas de forma urgente ya que permiten la ejecución de código de forma remota sin autentificación (RCE) para mitigar este tipo de vulnerabilidades es normal que se asilen nodos de los sistemas principales y se establezcan bloqueos de rede de forma temporal, un ejemplo real es `CVE-2021-44228` (Log4Shell) que permitía la ejecución remota de código en servidores Java expuestos.

## CVE IDs

Para asignar los IDs quien descubre la vulnerabilidad debe solicitar una clave a través de CNA (CVE Numbering Authority) o en el programa central de CVE, se evalúa la solicitud y se le asigna un ID único para esa vulnerabilidad posteriormente se puede publicar (en el CVE List) o revisarse hasta que se tenga un parche.

La CVE List es la lista de la vulnerabilidades registradas, esta lista es gestionada por MITRE Corporation, una organización sin fines de lucro mediante CNAs (CVE Numbering Authorities) organizaciones autorizadas para evaluar informes de vulnerabilidades y asignar IDs validos, estas organizaciones pueden ser empresas de software (como Microsoft, Google, Oracle), proyectos de código abierto (como Apache, Kubernetes), equipos de respuesta ante incidentes (como CERTs) y proveedores de seguridad.

## En que sirve todo esto para un organización?

Los CVE y CVSS son cosas vastante importantes para tener en cuenta en la gestion de vulnerab
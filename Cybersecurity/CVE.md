
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

Este tipo de vulnerabilidades permite escalar privilegios local en un servidor, para estas vul
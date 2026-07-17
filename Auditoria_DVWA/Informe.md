# Auditoria Basica a DVWA

⚖️**Nota⚖️:** El objetivo de la auditoria básica es la búsqueda de vulnerabilidades y sugerir arreglos para robustecer la seguridad de la pagina web. Todos los datos, comandos y url´s han sido anonimizados por seguridad, este informe es para fines explicativos y de ejemplo.

## 01 / Resumen ejecutivo

En la auditoria se han encontrado multiples parametros de seguridad ausentes, en general estas faltas de seguridad perjudican la experiencia de usuario.

**Resumen de vulnerabilidades**

| Elemento revisado  |  Estado | Observaciones  |
| ------------ | ------------ | ------------ |
|SSL y Cifrado   | ❌  | No cuenta con certificados validos ni criptografía  |
| Servidor web y puertos  |  ⚠️ |  a información del servidor es visible, una version desactualizada, no hay puertos inecesarios abiertos |
|  CMS y componentes |  ✅ |  No contiene wordpress |
| Cabeceras de seguridad  |  ❌ |  No cuenta con ninguna cabecera de seguridad |
|  Cookies y sesiones | ❌  | Las cookies no están configuradas con las flag´s de seguridad  |
|  Exposición publica | ✅  | Hay directorio expuesto pero no hay información relevante  |
|  Credenciales y formularios | ❌  | NO cuenta con medidas de fuerza bruta, ni redirecciones forzadas a 443 (https).  |
| Otros controles | ✅ | No se encontraron vulnerabilidades |


**Resultado general**

|  Categoria | Severidad  |
| ------------ | ------------ |
|  SSL y Cifrado | 🟥Alta  |
| Servidor web y puertos  | 🟨Media  |
| Cabeceras de seguridad  |  🟥Alta |
|  Cookies y sesiones | 🟨Media  |
|  Credenciales y formularios |  🟨Media |
|  Exposición publica | 🟦Informativo  |

---- 

## 02 / Alcance Y Metodología

###**Alcance y limitaciones**

**Incluido**
- Análisis de configuración SSL/TLS
- Revisión de cabeceras HTTP de seguridad
- Evaluación de políticas CORS
- Revisión de flags de cookies
- Identificación de exposición pública de información
- Validación contra OWASP Top 10 (2021)

**Excluido**

- Explotación de vulnerabilidades
- Pruebas de penetración activas
- Análisis de código fuente
- Evaluación de lógica de negocio

**Metodología**: Se hizo uso de una metodología de auditoria básica en la pagina web basada en reconocimiento pasivo, análisis de configuración y validación de controles de seguridad estándar.

A continuación la lista de herramientas utilizadas
- Gobuster
- Nmap
- Wireshark
- Testssl
- Whatweb
- Curl
- OWASZap

---- 

## 03 / Análisis De Vulnerabilidades

|   Elemento|Estado   |  Impacto |  Recomendaciones |
| ------------ | ------------ | ------------ | ------------ |
| SSL y Cifrado  | No tiene certificados  | La pagina es vulnerable a ataques como MITM, phishing, modificación del transito, entre otros  | Adquirir un certificado valido para la aplicación,  preferiblemente TLS v1.3  |
|  Servidor web y puertos |  Banner del servidor visible y versión obsoleta | Facilita la recopilación de información para ataques y carece de redirección al puerto 443(https). | la información del servidor es visible, una versión desactualizada, no hay puertos innecesarios abiertos   |
| Cabeceras de seguridad  | No tiene ninguna cabecera  |  La hace vulnerable a multitud de ataque web como click-jacking, cross-site-scripting, entre otros | Añadir y configurar las cabeceras de seguridad según los requerimientos de la pagina.  |
| Cookies y sesiones  | No tiene flag´s de seguridad  | Facilita el robo de sesiones de usuarios y acceso no autorizado   | Añadir y configurar las flag´s de seguridad.  |
|  Exposición publica |   | Hay directorio expuesto pero no hay información relevante  |   |
| Credenciales y formularios  | No cuenta con medidas de fuerza bruta, ni redirecciones forzadas a 443 (https).  |  Facilitando ataques de MITM, ya que la información es legible y es vulnerable a fuerza bruta en usuarios y contraseñas. | Añadir las medidas de anti-fuerza bruta y forzar las redirecciones a HTTPS.  |
|  Otros controles |   | No se encontraron vulnerabilidades  |   |

----- 


## 04 / Evaluación de riesgo
*Evaluación y priorización de riesgos.*

| Elemento  | Severidad  | Impacto potencial  |  Prioridad |
| ------------ | ------------ | ------------ | ------------ |
| SSL y cifrado |  Alta |  Critico | 🟥Critico🟥  |
| Servidor web y puertos|  Media | Media  | 🟨Media🟨  |
| Cabeceras de seguridad | Alta  |  Alta | 🟥 Critico🟥 |
| Cookies y sesiones | Media  | Media  | 🟨 Media🟨 |
| Exposicion publica|  Informativo |  Bajo | 🟦 Informativo🟦 |
| Credenciales y formularios |  Alta |  Alto | 🟥 Critico 🟥 |
| Otros controles |Informativo| Informativo |🟦 Informativo🟦 |

----- 


## 07 / Recomendaciones

*Mejoras Accionables y Medidas Sugeridas*

Priorizar el arreglo de:
- Adquirir un certificado valido para la web, preferiblemente un TLS v1.3
- Añadir y configurar las cabeceras y CSP de la aplicación, se recomienda contratar servicio de hardering para la personalización del CSP.
- Forzar redirecciones del puerto 80(http) al 443(https).
---- 
Medidas a medio plazo
- Ocultar banners y actualizar el servidor.
- Añadir y configurar las flag´s de las cookies según recomendaciones de OWASP para cookies seguras.
---- 
Buenas practicas
- Restringir el acceso al directorio /config
- Contratar un servicio de WAFF para mayor protección

**Resumen de las prioridades**
| Elemento  | Prioridad de la aplicación de los controles  |
| ------------ | ------------ |
|  SSL y Cifrado | Alto  |
| Cabeceras de seguridad  | Alta  |
| Credenciales y formularios  |  Alta |
| Servidor web y puertos  | Media  |
|  Cookies y sesiones |  Media |
|  Exposición publica | Informativo  |
|  Otros controles |  Informativo |

---- 
## 08 / Próximos Pasos
*Priorización de Mitigaciones y Continuidad Operativa*

1. **Priorizar correcciones según nivel de riesgo**: “Ajustar y arreglar primero las vulnerabilidades con severidad alta, mitigando así la mayoría de los problemas inmediatos(CORS permisivo, Cookies inseguras, CSP incompleto y cabeceras faltantes).
2. **Solicitar asistencia técnica para la implementación**: solicitar al proveedor de hosting o al equipo de desarrollo que aplique las correcciones sugeridas.
3.** Verificar el resultado**: Cuando los cambios ya estén aplicados verificar el estado de la aplicación con herramientas como https://securityheaders.com para confirmar la configuracion. 
4. **Plan de monitoreo**: Revisar en un periodo 3 o 6 meses el estado de los certificados SSL, las cabeceras de seguridad y los protocolos que se habilitaron.
5. (Opcional) **evaluación avanzada**: Se recomienda considerar hacer una auditoria técnica o un pentesting para un análisis mas profundo y detallado, especialmente si la pagina maneja datos sensibles o formularios de inicio de sesión.


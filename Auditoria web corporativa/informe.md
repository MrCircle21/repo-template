# 🛡️ Auditoría Web - Caso Demostrativo
### 🔐 Nota de Anonimización

Este informe ha sido modificado para fines de portafolio profesional.
Se han anonimizado dominios, direcciones IP, nombres de host y otros identificadores sensibles.

## Resultados obtenidos

Se aplicaron técnicas manuales y automáticas de análisis usando como base las vulenrabilidades de OWASP TOP 10  para  la evaluación del dominio *www.cliente-demo.com* , y herramientas como:

- UpGuard Security Ratings
- Nessus
- OWASP Secure Headers Scanner
- Análisis TLS manual y automatizado
- Revisión directa del puerto 9090 (Cockpit)
- Google CSP Evaluator

Durante la evaluación del aplicativo web se identificaron 10 hallazgos que incluyen vulnerabilidades críticas, configuraciones inseguras y cabeceras HTTP mal implementadas.

---

|  Tipo de hallazgo| Cantidad | Severidad  | Recomendación Principal  |
| ------------ | ------------ | ------------ | ------------ |
| Panel administrativo expuesto   | 1  | Alta  |  Restringir acceso por IP y activar MFA |
| Certificado SSL autofirmado  |  1 |  Media |  Sustituir por certificado de CA confiable |
| Configuración débil TLS (LUKY13) | 1  | Media  | Priorizar cifrados AEAD y habilitar TLS 1.3  |
| Falta de cabeceras HTTP seguras   |  6 |  Baja / informativa |  Implementar cabeceras OWASP |
| Falta de HSTS | 1| Informativa |Agregar Strict-Transport-Security |
--- 

### Panel de administración expuesto 🔓

 Se detectó que el servidor en la url:
 
 `www.cliente-demo.com” (203.0.113.10 )`

Expone públicamente el panel de administración en el puerto 9090/tcp mediante HTTPS. 
Su exposición directa a Internet representa un riesgo considerable ya que este tipo de herramientas están diseñadas para entornos internos.

**Detalles tecnicos**
* Servicio: Panel de administración web
* Puerto: 9090/tcp (HTTPS)
* Sistema Operativo: Ubuntu 22.04.5 LTS
* URL accesible: https://cliente-demo.com:9090/
* Autenticación: Presente, sin controles avanzados de protección
* Hostname: server-demo

--- 
###⚠️ Riesgos

* Identificación del sistema operativo y la tecnología usada
* Ataques de fuerza bruta
* Explotación de vulnerabilidades conocidas
* Posible escalada de privilegios
--- 
### ✅ Recomendaciones
- Restringir acceso al puerto 9090 a redes internas
- Implementar autenticación fuerte (MFA, rate limiting, fail2ban)
- Deshabilitar el servicio si no es esencial
- Mantener actualizado el sistema

--- 
### 🔐 Análisis de Seguridad TLS

TLS 1.2 y no TLS 1.3: El servidor declara que soporta la versión 1.3 pero esta funcionando la versión 1.2. 


Problemas detectados:


- Uso de cifrados CBC (AES256-SHA) vulnerables a LUCKY13
- Cifrados modernos AEAD disponibles pero no priorizados
- Configuración deficiente en archivo `ssl.conf`


####*Tabla de inconsistencias *

| Elemento |  Resultado  | Riesgo | Recomendaciones  |
| ------------ | ------------ | ------------ | ------------ |
|  Protocolos |  TLS 1.2 y TLS 1.3 |Uso de versión menos segura  |  Forzar TLS 1.3 |
|  Cifrados CBC |  Presentes |Vulnerables a ataques LUCKY13   | Eliminar o priorizar AEAD  |
|Cifrados AEAD   |  Disponibles |   No priorizados|  Usar como principal |

-- -


###🌐 Cabeceras HTTP

**Cabeceras faltantes:**

- Cross-Origin-Opener-Policy
- Referrer-Policy
- X-DNS-Prefetch-Control
- Cross-Origin-Embedder-Policy
- Cross-Origin-Resource-Policy

-- -
**Cabeceras mal configuradas**

|Cabecera   | Valor actual  | Recomendado  |
| ------------ | ------------ | ------------ |
|  Strict-Transport-Security | max-age=31536000  |  max-age=63072000 |
|  X-Frame-Options |  SAMEORIGIN |DENY   |
| X-XSS-Protection  | 1; mode=block  |0   |
|  Permissions-Policy |  configuraciones abiertas | restringir  |
|Content-Security-Policy   |  unsafe-inline | uso de nonces  |
| Access-Control-Allow-Origin  |ORIGIN_REDACTED   | restringir dominio  |
|  Content-Type |  text/html |charset simplificado   |

---- 

### 🧾Evidencias (Nessus)
#### Certificado SSL Autofirmado
- Severidad: Media
- Puerto: 9090

**Descripción:**
El servidor utiliza un certificado autofirmado, haciendo que no se pueda confirmar su autenticidad.

**Riesgo:**
Vulnerable a ataques Man-in-the-middle

**Recomendación: **
Adquirir e instalar un certificado firmado por una CA confiable

**CVSS:** 6.5 (Medio)

**Certificado:** `CN=demo-server`

--- 

#### Falta de HSTS

- Severidad: Informativa
- Puerto: 9090
- Host: cliente-demo.com

Descripción:
No se aplica HSTS, permitiendo ataques de degradación.

Recomendación:
`Strict-Transport-Security: max-age=63072000; includeSubDomains; preload`

### 📊 Resumen de riesgos

|  Hallazgo |  Puerto | Severidad   | Recomendación  |
| ------------ | ------------ | ------------ | ------------ |
|Certificado auto firmado   | 9090  |  Medio | Usar CA valida  |
|  Falta de HSTS | 9090  |  Informativo | Configurar cabecera  |

--- 


### 📉 Limitaciones

Limitación de acceso a configuración interna del servidor, impidiendo la validación completa de configuraciones del servidor.

--- 

### 🧾 Conclusión

La auditoría de seguridad al dominio evaluado reveló multiples vulnerabilidades y configuraciones deficientes que afectan la seguridad del sitio.
La exposición de servicios administrativos y la configuración insegura de TLS representan vectores de ataque potencialmente explotables.
Se recomienda implementar controles de acceso, certificados confiables y endurecimiento de configuraciones de seguridad para reducir la superficie de ataque y mejorará la postura de seguridad del dominio.


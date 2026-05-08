# 🛡️ OWASP Juice Shop - Security Assessment

Auditoría técnica básica realizada sobre OWASP Juice Shop en entorno controlado.

## Objetivo

Búsqueda de vulnerabilidades en la aplicación web, medir su impacto, informar y recomendar mitigantes a los clientes, para el pronto fortalecimiento del aplicativo web.



#### Disclaimer ⚖️
Este análisis se realizó en un entorno controlado o laboratorio autorizado y ha sido modificado para fines de portafolio. Se han anonimizado dominios, direcciones IP, nombres de host y otros identificadores sensibles.

#### Alcance y limitaciones
**Incluido:**
- Análisis de configuración SSL/TLS
- Revisión de cabeceras HTTP de seguridad
- Evaluación de políticas CORS
- Revisión de flags de cookies
- Identificación de exposición pública de información
- Validación contra OWASP Top 10 (2021)

**Excluido:**
- Explotación de vulnerabilidades
- Pruebas de penetración activas
- Análisis de código fuente
- Evaluación de lógica de negocio

-- -
####Metodología
Se hizo uso de una metodología de auditoria básica en la pagina web basada en reconocimiento pasivo, análisis de configuración y validación de controles de seguridad estándar.

-- -

###  🧵Herramientas utilizadas:
- nmap
- whatweb
- curl
- testssl
- owaspZAP
- Wireshark

------------
### Resumen tecnico de los hallazgos
|   Categoria | Severidad  |
| ------------ | ------------ |
|  Configuración CORS permisiva | Alto🟥  |
|  Certificados SSL/TLS inexistentes |  Alto 🟥 |
| Cabeceras de seguridad faltantes  |  Alto🟥 |
|  Robots.txt con rutas sensibles |  Alto🟥 |
|  Exposición de información pública | Baja 🟩  |
| Cookies de inexistentes  |  Informativo 🟦 |

--- -

## Resultados de la revision tecnica📑


### Certificados SSL/TLS inexistentes🔐

**Clasificación de OWASP**🐝:  A02: Cryptographic Failures. | **Severidad**: Alta🟥

**Hallazgo**: La aplicación no implementa certificados SSL/TLS válidos, careciendo de cifrados, uso del protocolo https y formas de saber que la pagina es legitima.

**Impacto tecnico**: Esta falta de seguridad puede ser usada para múltiples clases de ataques entre ellos:
* Suplantación de identidad (Phishing), 
* MITM
* Modificación del transito, 
* Robo de credenciales, cuentas y datos. 
* Perdida del SEO, confianza e incumplimiento de estándares.

**Comandos usados**:
* `openssl s_client -cliente-demo.com:3000 -servername cliente-demo.org `
* `testssl https://www.cliente-demo.com`

-- -
#### Recomendación🎚️:
- Obtener certificados validos cuanto antes:
 - Let’s Encrypt (gratis)
 - Cloudflare SSL
 - DigiCert

- Forzar `https: return 301 https://$host$request_uri;`
- Habilitar HSTS con la cabecera: `Strict-Transport-Security: max-age=63072000; includeSubDomains; preload`
- Deshabilitar protocolos antiguos (TLS 1.1 y 1.0), usar TLSv1.3 preferiblemente

-- -

#### ✅Criterio de aceptación:
* Tener certificado valido, https forzado, cabecera de HSTS añadida y configurada, y protocolo TLS fuerte, preferiblemente TLSv1.3.

**Comando de confirmación**: Curl -v sitio.com
Seguro✅
`Verify return code: 0 (ok)
TLSv1.3`

-- -


### Cabeceras HTTP de seguridad⚙️

En el análisis se pudo notar la falta de cabeceras que evitan el ataque de Click-jacking, Cross-site-scripting, peticiones de origen cruzado, inyección de codigo, etc... Se encontraron malas configuraciones y cabeceras obsoletas.

 -- - 
 

####  Content-Security-Policy (CSP) ausente.

**Clasificación de OWASP**🐝: A05 – Security Misconfiguration y A03 – Injection
**Severidad**: Alta

**Hallazgo:** la política CSP no se encuentra, dejando a la pagina expuesta a múltiples ataques.

**Impacto técnico**: La aplicación es vulnerable a ataques de Cross-Site Scripting (XSS), Click-jacking, Inyección de scripts externos, desfiguración de contenido y carga forzada de contenido. Debido a los múltiples vectores que mitiga se le considera de alto riesgo.

**Comandos usados**:

    curl -v -I htttp://cliente-demo:3000
	whatweb -v http://cliente-demo:3000


-- -
**Recomendación**🎚️: 
Implementar una CSP adaptada a los recursos que usa la pagina, evitando una configuración permisiva, evitando el uso de *, unsafe-inline y unsafe-eval .
Configuración general recomendada:

`default-src 'self';`
`object-src 'none';`
`base-uri ‘self’;`
`script-src con nonces o hashes;`
`frame-ancestors 'self';`

Recomendado el contrato de un servicio de hardering para la adaptación de un buen CSP.

**Criterio de aceptación**:
Implementación y configuración del CSP adaptada a las necesidades de la aplicación, para confirmar que estan usar curl -I.

-- -
#### HSTS inexistente

**Clasificación de OWASP**🐝: A05 – Security Misconfiguration.
**Severidad**: Alta 

**Hallazgo**: ausencia de la cabecera  Strict-Transport-Security necesaria para el uso obligatorio de https.

**Análisis técnico**: Hace a la aplicación vulnerable a ataques de downgrade o SSL stripping. Debido a la falta de trafico cifrado y protocolos de seguridad la vulnerabilidad sube a alta.

-- -

**Recomendación🎚️:**

Añadir la cabecera configurada:
`Strict-Transport-Security: max-age=63072000; includeSubDomains; preload`

**Criterio de aceptación**:
Al usar el comando `curl -I` la cabecera este presente y correctamente configurada.

-- -

####Configuración CORS permisiva

**Clasificación de OWASP**🐝: A05 – Security Misconfiguration | **Severidad**: Alta

**Hallazgo**: la política CORS esta configurado de forma que permite cualquier sitio realizar solicitudes autenticadas.

Configuracion actual: `Access-Control-Allow-Origin: *`

**Impacto técnico**: Esta configuración es insegura, ya que permite a orígenes no confiables realizar solicitudes autenticadas, facilitando exfiltración de datos.

-- -

**Recomendación🎚️:**

- Definir orígenes explícitos y confiables evitando el uso de `*` 
- Añadir y configurar la cabecera `Access-Control-Allow-Credentials:` configurada en `true`

**Criterio de aceptación**
Al usar el comando curl -I se encuentren entre las cabeceras `Access-Control-Allow-Origin:` adaptada al sitio y  `Access-Control-Allow-Credentials: true;` configurada.

-- -

####Robots.txt con rutas sensibles

**Clasificación de OWASP**🐝: A05:2021 – Security Misconfiguration | **Severidad**: Alta

**Hallazgo**: El archivo robots.txt es accesible públicamente y contiene una ruta en la que se aloja información sensible y secretos de la empresa, comprometiendo la confidencialidad del negocio. .

**Impacto técnico**: Informacion sensible puede ser usada para saber la infraestructura del sitio web y servidores, como robar datos tanto de la empresa como de los clientes.

-- --

**Recomendacion🎚️**: 

* Eliminar rutas sensibles de robots.txt y no usarlo para ocultar rutas sensibles.

**Criterio de aceptación**:
Rutas genericas alojadas en robots.txt

-- -

### Exposición de información publica 📸

**Clasificación de OWASP**🐝: A05:2021 – Security Misconfiguration | **Severidad**: Medio.

**Hallazgo**: servicios como Shodan están exponiendo:

- Cabeceras HTTP
- Certificados SSL faltantes
- Información de infraestructura

**Impacto técnico**: facilita la fase de reconocimiento de atacantes, reduciendo el esfuerzo para identificar vectores de ataque.

-- -
**Recomendacion🎚️:**

- Implementar WAF
- Rate-limiting
- Ocultar banners
- Restringir accesos administrativos

**Criterio de aceptación:**

Al usar herramientas OSINT como shodan y whois muestren información minima de la infraestructura de la aplicación web.

-- -

#### Coockies de sesion 🍪

Severidad: Informativa

**Hallazgo**: Las cookies no se encuentran presentes en la pagina web. En caso de ser añadidas se recomienda hacerlo con una configuración segura como Secure, HttpOnly y  SameSite=Strict.

**Impacto técnico**: 
La adicion de las cookies de sesion trae consigo una mejora en la experiencia del usuario al navegar por la pagina web, pero conyeva el riesgo de robos de sesion, por lo que es necesario añadirlas con configuraciones seguras.

**Recomendación🎚️**:

En caso de ser añadidas se recomienda hacerlo con una configuración segura como la que se ve a continuación:

`Set-Cookie: __Host-Session=<token>;`
`Path=/;`
`Secure;`
`HttpOnly;`
`SameSite=Strict;`
`Max-Age=3600`

-- -
### Priorizacion de mitigación

|  Alta prioridad |  Prioridad media | Buenas practicas recomendadas   |
| ------------ | ------------ | ------------ |
| Cabeceras HTTP de seguridad  | Exposición de información publica  | Implementar cookies a la aplicación web |
|  CORS permisivo |   |   |
|  Certificados SSL/TLS |   |   |
|   Robots.txt|   |   |

--- -

## Conclusión

El aplicativo presenta debilidades de seguridad severas, principalmente asociadas a configuraciones y medidas de seguridad faltantes que dejan a la aplicación sin ningún tipo de protección a los ataques mas usados.

La aplicación de las recomendaciones propuestas permitirá:
- Reducir significativamente la superficie de ataque
- Cumplir con estándares de la industria.
- Mejorar la postura de seguridad significativamente.
- Mejorar la experiencia de usuario.

Para una evaluación más profunda, se recomienda pentesting controlado y un servicio de hardering para la implementación de un buen CSP. La revisión de los comandos, evidencias de las respuestas se verán en la carpeta screenshots.



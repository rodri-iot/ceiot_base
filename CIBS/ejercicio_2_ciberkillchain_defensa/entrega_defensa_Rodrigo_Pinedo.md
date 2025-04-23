# Ejercicio CiberKillChain - Defensa

## 📈 **Sistema de monitoreo de calidad del aire**

## **Índice**
- [📄 **Descripción**](#-descripción)
- [💡 **Objetivo del proyecto**](#-objetivo-del-proyecto)
- [⚛ **Sistema IoT**](#-sistema-iot)
- [🎯 **Objetivo de la defensa**](#-objetivo-de-la-defensa)
- [🥊 **Resolución de la defensa**](#-resolución-de-la-defensa)
  - [1. Acción sobre el objetivo (Actions on objetives)](#7-acción-sobre-el-objetivo-acctions-on-objetives)
  - [2. Comando y Control (C2)](#6-comando-y-control-c2)
  - [3. Instalación (Installation)](#5-instalacion-installation)
  - [4. Explotación de la vulnerabilidad (Exploitation)](#4-explotación-de-la-vulnerabilidad-exploitation)
  - [5. Entrega del ataque (Delivery)](#3-entrega-del-ataque-delivery)
  - [6. Armado del ataque (Weaponization)](#2-armado-del-ataque-weaponization)
  - [7. Reconocimiento (Reconnaissance)](#1-reconocimiento-reconnaissance)
- [🧠 **Conclusiones**](#-conclusiones)
- [🥇 **Autor**](#autor)

## 📄 **Descripción**

Sistema de monitoreo basado en IoT para medir la calidad del aire en entornos urbanos e industriales. Utiliza sensores conectados a una red LoRaWAN/WiFi/MQTT, enviando datos a un servidor central, donde son procesados y visualizados en una plataforma web/móvil.

## 💡 **Objetivo del proyecto**

Implementar una solución IoT para monitorear la calidad del aire en entornos urbanos e industriales. Utiliza sensores conectados a una red LoRaWAN/WiFi/MQTT, enviando datos a un servidor central, donde son procesados y visualizados en una plataforma web/móvil.

## ⚛ **Sistema IoT (estructura)**

El sistema consiste en una red de dispositivos IoT equipados con sensores ambientales que miden parámetros críticos de calidad del aire. Los datos se transmiten a un servidor central mediante protocolos inalámbricos, se almacenan en una base de datos y se visualizan en una plataforma web/móvil en tiempo real.

## 🎯 **Objetivo de la defensa**

Diseñar medidas de defensa efectivas para detectar y mitigar un ataque de falsificación de datos ambientales, ejecutado por un cliente que ha reemplazado uno de sus sensores legítimos por un nodo modificado que simula condiciones favorables.

## 🥊 **Resolución del ataque**

Es importante considerar el caso en el que una empresa, **GREENFOG TECHNOLOGIES CO.**, adquiere legalmente el sistema de monitoreo ambiental con el fin de cumplir aparentes exigencias regulatorias. Sin embargo, su verdadera intención es manipular las mediciones reportadas para ocultar emisiones contaminantes y evadir sanciones ambientales sin levantar sospechas.

### **1. Acción sobre el objetivo (Actions on Objectives)**

Etapa en la que el nodo manipulado emite datos falsos de forma persistente para engañar al sistema, simulando una condición ambiental saludable y ocultando emisiones reales.

🔍 **Medida de detección**
Análisis de consistencia inter-nodal: comparar métricas reportadas por nodos cercanos (en el mismo polígono urbano o zona industrial) para identificar outliers que se mantengan constantemente por debajo del resto.

- [DS0013 – Sensor Health and Status](https://attack.mitre.org/datasources/DS0013/)
Se emplea para identificar desviaciones anómalas en el comportamiento de los sensores, incluyendo cambios sospechosos en los datos reportados o en su cadencia.
- Comparar valores recibidos por nodo contra el promedio zonal o un modelo de referencia histórica.
- Detectar ausencia de variabilidad temporal (ej. valores idénticos cada 30 segundos).
- Emitir alertas en el dashboard (frontend) y registrar eventos en MongoDB con un campo is_anomaly = true.

🛠️ **Medida de mitigación**
- [CWE-345 – Insufficient Verification of Data Authenticity]()
Refiere a la falta de controles que validen la autenticidad y coherencia de los datos entrantes.

Implementar lógica de validación semántica en el backend:
- Evaluar si los datos reportados cambian razonablemente en el tiempo.
- Verificar que se alineen con el entorno geográfico y condiciones climáticas locales.
- Validar que el nodo no haya sido reiniciado recientemente (comparar timestamps o uptime).

🔁 Acciones ante detección:
- Poner en cuarentena al nodo: su data no se considera para visualización ni análisis, pero se sigue almacenando para auditoría.
- Mostrar un indicador visual en el dashboard web (ej. "nodo sospechoso").
- Notificar al operador o al administrador mediante sistema de alertas.

### **2. Comando y Control (C2)

El atacante mantiene control activo sobre el nodo manipulado mediante comandos que ajustan su perfil (frecuencia de publicación, valores simulados, comportamiento ante auditorías). Este canal puede estar encubierto dentro del protocolo MQTT o mediante HTTP hacia un servidor externo.

🔍 **Medida de detección**
Inspección de tráfico de red y telemetría hacia servicios externos
- [DS0029 – Network Traffic Content](https://attack.mitre.org/datasources/DS0029/)
Permite detectar contenido inusual en tráfico de red, como mensajes MQTT con campos no esperados o comandos ocultos.
- [DS0022 – File Modiffications](https://attack.mitre.org/datasources/DS0022/)
Esto permitiría detectar conexiones salientes inusuales, aunque estén cifradas. Por ejemplo, si el ESP32 empieza a resolver dominios no esperados o conecta IPs que no son del broker.
- [DS0009 – Process](https://attack.mitre.org/datasources/DS0009/)
Si en el nodo se usara un sistema embebido más complejo con shell/script (como Linux embebido), se puede detectar actividad anómala al ejecutar comandos para cambiar el comportamiento de red. Esto aplica más en gateways.

Implementación:
- Analizar los payloads de los mensajes MQTT desde cada nodo.
- Identificar la presencia de campos no estándar (ej. cmd, meta, debug) o estructuras inusuales (JSON anidados, valores ofuscados).
- Supervisar conexiones salientes desde los nodos hacia dominios no autorizados (por ejemplo, servidores externos vía HTTP/HTTPS).

🛠️ **Medida de mitigación**
- [CWE-284 – Improper Access Control](https://cwe.mitre.org/data/definitions/284.html)
Aplicable cuando no se restringe adecuadamente qué acciones puede ejecutar un cliente MQTT dentro del sistema.
- [CWE-693 – Protection Mechanism Failure](https://cwe.mitre.org/data/definitions/693.html)
Refiere a fallos en la implementación de mecanismos de control, como permitir conexiones salientes arbitrarias o no controlar tópicos accesibles.

Implementación:

- Configurar el broker MQTT para permitir solo publicaciones en tópicos específicos por cada nodo (ACL por ID).
- Bloquear toda comunicación saliente desde los nodos (Wi-Fi o LoRaWAN gateways) hacia Internet, salvo hacia el broker autorizado.
- Aplicar listas blancas de dominios permitidos si se usa HTTP para otros fines legítimos (ej. actualizaciones OTA controladas).

✅ **Resultado**

Estas medidas permiten detectar si un nodo está siendo remotamente controlado a través de comandos ocultos o canales alternativos, y mitigar esa capacidad cortando las rutas de comunicación no autorizadas. Además, limitan el riesgo de que el nodo manipulado se transforme en infraestructura maliciosa encubierta (por ejemplo, proxy o bot).

### **3. Instalación (Installation)**

Durante tareas de mantenimiento o calibración, el atacante reemplaza un nodo ESP32-S3 legítimo por una unidad físicamente idéntica pero con firmware modificado. Esta nueva unidad utiliza los mismos certificados TLS y publica en los mismos tópicos MQTT. El objetivo es consolidar la persistencia de un sensor falso sin levantar sospechas operativas.

🔍 **Medida de detección**
- [T1546.008 – Implant Internal Image: System Firmware]()
Esta técnica sí es muy relevante. Describe cómo los atacantes pueden modificar firmware para mantener persistencia encubierta.
- [DS0011 – File: File Metadata]()
Para detectar cambios en la imagen de firmware (hash, timestamp) en sistemas embebidos.
- [DS0007 – Device Metadata]()
Para validar fingerprint de hardware (MAC, chip ID, RSSI típico) y correlacionarlo con identidad lógica.

Implementación:
- Calcular el hash SHA-256 del firmware compilado y almacenado del nodo.
- Una función tipo "challenge-response": el backend solicita que el nodo calcule y envíe su hash de firmware, si no coincide, se marca como sospechoso.
- Analisar el perfil del nodo: monitorear cambios en MAC Address, frecuencia de mensajes y latencia típica. Un cambio significativo marcara al nodo como sospechoso.

🛠️ **Medida de mitigación**

- [CWE-1211 – Improper Certificate Validation]()
Aplica cuando los certificados se aceptan sin verificar su asociación exclusiva con un dispositivo o ubicación.

Implementación:

- Registrar los certificados TLS emitidos por nodo con su fingerprint, número de serie del microcontrolador o dirección MAC, y ubicación asignada.
- Rechazar automáticamente cualquier conexión MQTT que utilice un certificado válido pero provenga de un dispositivo distinto (según fingerprint) o de una ubicación no esperada.

✅ **Resultado**

Estas medidas permiten:

- Detectar reemplazo físico malicioso del nodo, incluso si el atacante copia certificados.
- Establecer una relación fuerte entre identidad digital (certificado TLS) e identidad física (fingerprint).
- Negar persistencia a nodos comprometidos, sin necesidad de intervención manual constante.

### **4. Explotación de la vulnerabilidad (Exploitation)**

El atacante comienza a enviar datos falsificados al broker MQTT desde un nodo aparentemente legítimo (con certificados válidos).

🔍 **Medida de detección**
- [DS0013 – Sensor Health and Status](https://attack.mitre.org/datasources/DS0013/)
Utilizada para detectar comportamientos anómalos en sensores que podrían indicar manipulación.
- [T1071.005 – Application Layer Protocol: MQTT](https://attack.mitre.org/techniques/T1071/005/)
Aplica directamente: describe la manipulación deliberada de datos ambientales para alterar la percepción del entorno.

Implementación:
- Verificar consistencia entre la frecuencia de publicación, variabilidad esperada y el patrón histórico del nodo.
- Detectar: valores constantes sospechosamente estables, datos fuera de fase respecto a condiciones climáticas reales o a sensores vecinos y paquetes duplicados o con timestamp alterado.
- Ejecutar checks de integridad semántica en la API o base de datos antes de aceptar registros definitivos.

🛠️ **Medida de mitigación**

- [CWE-345 – Insufficient Verification of Data Authenticity](https://cwe.mitre.org/data/definitions/345.html)
Describe la omisión de verificación sobre si los datos realmente provienen de una fuente autorizada y no han sido manipulados.

Implementación:

- Incluir una firma HMAC o token autenticado en cada mensaje MQTT enviado por el nodo.
- Validar la firma en el backend antes de procesar o almacenar los datos.
- Cada nodo debe tener una clave secreta única almacenada de forma segura en hardware (ej. eFuse o TPM).
- Utilizar una “firma ligera” basada en hashing con clave compartida (ej. HMAC-SHA256 con un nonce por sesión) como protección básica.

✅ **Resultado**
Estas defensas permiten:
- Detectar si un nodo legítimo fue manipulado a nivel firmware o aplicación.
- Impedir que datos falsos sean aceptados incluso si el canal MQTT está protegido por TLS.
- Reforzar la autenticidad del contenido, no solo del canal.

### **5. Entrega del ataque (Delivery)

El atacante reemplaza el nodo físico original por uno con firmware malicioso.

🔍 **Medida de detección**
- [DS0007 – Device Metadata](https://attack.mitre.org/datasources/DS0007/)
Permite monitorear atributos del hardware del dispositivo, como dirección MAC, número de serie, fingerprint del chip, etc.

Implementación:

- Al reconectar un nodo tras tareas de campo, validar: dirección MAC, UID del microcontrolador, RSSI o fingerprint RF esperado.
- Si alguno de estos parámetros difiere del original asociado al certificado, se bloquea la conexión y se alerta.
- Capturar la huella de cada nodo durante la instalación original y compararla en cada reconexión futura.

🛠️ **Medida de mitigación**
- [CWE-288 – Authentication Bypass Using an Alternate Path or Channel](https://cwe.mitre.org/data/definitions/288.html)
El nodo falsificado está usando una vía válida (certificados TLS) pero no autorizada a nivel físico/lógico.

Implementación:
- Todo nodo que es reinsertado o reemplazado debe pasar por un proceso de verificación reforzado: validación de firma de firmware, confirmación de ubicación física, validación de campos extendidos (build ID, firmware version, logs de boot)
- Hasta completar esa validación, el nodo no publica datos al flujo principal, sino que entra en cuarentena.
- Al menos exigir confirmación manual vía consola de backend antes de permitir que un nuevo nodo comience a publicar en producción.


### **6. Armado del ataque (Weaponization)**

El atacante prepara su ofensiva construyendo un firmware malicioso

🔍 **Medida de detección**
- [DS0022 – File Access]()
Permite detectar quién descarga firmware y con qué frecuencia, ayudando a identificar intentos de replicación o ingeniería inversa.
- [DS0029 – Network Traffic Content]()
Adecuada para detectar contenido anómalo en el tráfico MQTT, como estructuras de mensajes inesperadas o tópicos no estándar usados por firmware falsificado.


Implementación:

Registrar y analizar:

- Quién descarga firmware (cuenta/IP).
- Cantidad de descargas por archivo.
- Tiempos de descarga (horarios, fines de semana).
- Conexiones MQTT desde nodos “de laboratorio” que:
  - Publican con certificados conocidos pero desde MACs distintas.
  - Usan tópicos inconsistentes o modifican estructura de mensajes (campos extra como cmd, meta, etc.).


🛠️ **Medida de mitigación**
- [CWE-347 – Improper Verification of Cryptographic Signature]()
El riesgo principal en esta etapa es que el firmware malicioso se cargue sin validación adecuada de su integridad y origen.

Implementación:
- Incluir una firma HMAC interna generada en el backend.
- Verificar en el backend al iniciar sesión MQTT.
- Comparar contra una whitelist de hashes autorizados.
- Firmware sólo distribuido vía OTA (Over-the-Air), debiendo estar registrada, firmada y auditada.

✅ **Resultado**
Estas medidas permiten a Monitoreo de Aire Co.:
- Detectar preparación temprana del ataque, antes del despliegue físico.
- Prevenir ejecución de firmware falsificado, incluso si el canal MQTT y el certificado TLS son válidos.
- Cerrar el vector de ingeniería inversa, restringiendo el acceso a firmware y estructuras internas.

### **7. Acción sobre el objetivo (Actions on objetives)**

El cliente malicioso accede a documentación técnica, configuraciones del sistema, tráfico de red, firmware, esquemas de tópicos MQTT, e incluso a certificados TLS propios.

🔍 **Medida de detección**
Monitoreo de uso anómalo de APIs y análisis de comportamiento en portales técnicos
- [DS0024 – Web Application Log]()
- [DS0009 – Process (adaptado a entorno IoT)]()

Implementación:

Registrar:
- Consultas repetitivas o automatizadas a endpoints sensibles (/config, /api/devices, /certs, /firmware).
- Comportamientos inusuales como:
  - Descargas excesivas de documentos técnicos.
  - Exploración de APIs con herramientas como Postman o curl.
  - Pruebas de conexión a tópicos MQTT alternativos.
Una consulta API fuera de patrón normal (frecuencia alta, parámetros sospechosos) desde una cuenta cliente debería activar una alerta de auditoría.

🛠️ **Medida de mitigación**
Principio de mínimo privilegio + segmentación de acceso técnico
- [CWE-284 – Improper Access Control]()

Implementación:

Separar entornos: lo que necesita el cliente no es lo mismo que necesita el equipo técnico.
- Los clientes solo deben tener acceso a:
  - Visualización de datos.
  - Paneles limitados de monitoreo.
  - Certificados generados exclusivamente para sus nodos.
- No deben tener acceso a firmware completo, esquema completo de tópicos, herramientas de debug, ni documentación avanzada.
- Implementar roles granulares en la plataforma:
  - "Cliente estándar" vs. "Administrador técnico".
  - Limitar APIs según perfil autenticado.
- Asegurar que la documentación sensible y firmware estén detrás de login técnico o requerimiento explícito de soporte, no disponibles por defecto.

✅ **Resultado**
Estas defensas permiten:
- Detectar el inicio de la cadena de ataque, cuando el atacante aún está explorando.
- Limitar la información crítica que se expone a los clientes, incluso si tienen acceso legítimo.
- Reducir el riesgo de ingeniería inversa del sistema, sin comprometer la funcionalidad del servicio.


## 🧠 **Conclusiones**

La defensa implementada por Monitoreo de Aire Co. demuestra que es posible detectar y mitigar un ataque interno, incluso cuando el atacante posee acceso legítimo al sistema.

Las medidas clave incluyeron:

- Correlación de datos entre nodos para detectar manipulaciones persistentes.
- Verificación criptográfica del firmware y validación de identidad física del nodo.
- Control estricto de certificados y firmas para evitar suplantaciones.
- Monitoreo de comportamiento anómalo en APIs y tráfico MQTT.
- Restricción de acceso a documentación y firmware según el perfil del cliente.

Con controles aplicables y de bajo costo, se logra reducir la superficie de ataque y proteger la integridad del sistema frente a actores internos que intenten manipular las mediciones.

## 🥇 **Autor**

- Ing. Rodrigo Jurgen Pinedo Nava
- Universidad de Buenos Aires (UBA)
- Facultad de Ingeniería
- Especialización en Internet de las Cosas (IoT)
- **Linkedin:** https://www.linkedin.com/in/rodrigopinedo/

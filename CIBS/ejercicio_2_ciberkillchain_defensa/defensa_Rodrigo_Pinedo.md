# Ejercicio CiberKillChain - Ataque

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
- [🔀 **Diagrama de Flujos de la defensa**](#-flujos-de-la-defensa)
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

### **5. Instalación (Installation)**

En esta etapa, el atacante consolida la presencia del nodo falsificado dentro de la infraestructura del sistema, asegurando su permanencia, estabilidad y resistencia frente a actualizaciones, reinicios o inspecciones técnicas superficiales. A través de decisiones deliberadas de configuración y diseño del firmware, el nodo es instalado de forma tal que pueda operar a largo plazo sin depender de intervención constante ni generar señales visibles de alteración.



### **6. Comando y Control (C2)**

Tras haber logrado la instalación persistente del nodo manipulado, el atacante implementa un canal de Comando y Control (C2) que le permite supervisar, ajustar y reconfigurar remotamente el comportamiento del dispositivo comprometido. El objetivo es mantener una operación encubierta a largo plazo, pero con la posibilidad de adaptar el patrón de datos transmitido ante auditorías, cambios en el entorno o evolución del sistema de monitoreo.



### **7. Acción sobre el objetivo (Actions on objetives)**

Con el nodo manipulado instalado y gestionado mediante un canal de comando y control encubierto, el atacante ejecuta las acciones destinadas a alcanzar su objetivo principal: alterar las mediciones ambientales de forma sistemática y persistente, con el fin de proyectar una imagen ambiental falsa que oculte emisiones reales y mantenga la fachada de cumplimiento normativo.

Estas acciones se diseñan para mantenerse bajo el umbral de detección, adaptarse dinámicamente al entorno y alimentar con datos falsos todos los subsistemas que confían en la información proveniente del sensor.



## 🔀 **Flujos del ataque**

El atacante ejecuta el ataque en una secuencia estructurada, respetando las fases metodológicas de la Cyber Kill Chain. Cada fase es cuidadosamente planificada para mantener un bajo perfil, evadir mecanismos de detección y maximizar el impacto sobre el sistema objetivo.

```flujo
1. [Reconocimiento]
   ↓
   Se analiza la arquitectura del sistema adquirido: nodos ESP32-S3, comunicación MQTT sobre TLS, despliegue en GCP.
   Se identifican certificados, tópicos de publicación y formato de payloads aceptados.

2. [Armado del ataque]
   ↓
   Se desarrolla firmware malicioso para un nodo clonado.
   El nodo simula condiciones ambientales normales con datos ajustables dinámicamente.

3. [Entrega del ataque]
   ↓
   Se reemplaza físicamente un nodo original por el falsificado durante tareas de mantenimiento.
   El nodo se conecta al broker MQTT con certificados válidos y comienza a emitir datos manipulados.

4. [Explotación]
   ↓
   El sistema acepta los datos como legítimos debido a la falta de validación de integridad y origen.
   Se almacenan en la base de datos y se visualizan en dashboards y reportes sin levantar alertas.

5. [Instalación]
   ↓
   El firmware garantiza persistencia tras reinicios, mimetiza comportamiento del nodo original
   y mantiene configuraciones de publicación automáticas.

6. [Comando y Control (C2)]
   ↓
   Se establece un canal oculto dentro de MQTT para ajustar en tiempo real los valores reportados.
   También se incorpora un canal alternativo HTTP como fallback para administración remota.

7. [Acción sobre el objetivo]
   ↓
   Se falsifican las mediciones ambientales, afectando decisiones regulatorias y percepción pública.
   Se distorsiona el histórico, se evitan alertas y se sostiene una imagen de cumplimiento ambiental.

```
Este flujo resume la lógica del ataque y permite visualizar de manera compacta cómo se encadenan las acciones para alcanzar el objetivo: comprometer la confiabilidad, disponibilidad y legitimidad del sistema de monitoreo ambiental, mientras se ocultan rastros y se explotan recursos de manera persistente.

## 🧠 **Conclusiones**

El ejercicio demuestra cómo un atacante con acceso legítimo al sistema puede comprometer su confiabilidad sin necesidad de explotar vulnerabilidades técnicas complejas. A través de la sustitución encubierta de un nodo IoT, la manipulación de datos y el uso de canales de control persistente, se logra alterar la percepción ambiental sin ser detectado.

El éxito del ataque se basa en:

La confianza excesiva en certificados y canales cifrados como únicos mecanismos de autenticación.

La ausencia de validación de integridad y origen de los datos.

La falta de correlación entre dispositivos, ubicación física y métricas reportadas.

Como resultado, el sistema sigue operativo, pero entrega información manipulada que distorsiona decisiones regulatorias, técnicas y sociales. El caso evidencia el riesgo real que implica no incorporar controles de seguridad integrales en entornos IoT críticos.

## 🥇 **Autor**

- Ing. Rodrigo Jurgen Pinedo Nava
- Universidad de Buenos Aires (UBA)
- Facultad de Ingeniería
- Especialización en Internet de las Cosas (IoT)
- **Linkedin:** https://www.linkedin.com/in/rodrigopinedo/

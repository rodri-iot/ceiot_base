# Ejercicio CiberKillChain - Ataque

## 📈 **Sistema de monitoreo de calidad del aire**

## **Índice**
- [📄 **Descripción**](#-descripción)
- [💡 **Objetivo del proyecto**](#-objetivo-del-proyecto)
- [⚛ **Sistema IoT**](#-sistema-iot)
- [🎯 **Objetivo de la defensa**](#-objetivo-de-la-defensa)
- [🥊 **Resolución de la defensa**](#-resolución-de-la-defensa)
  - [7. Acción sobre el objetivo (Actions on objetives)](#7-acción-sobre-el-objetivo-acctions-on-objetives)
  - [6. Comando y Control (C2)](#6-comando-y-control-c2)
  - [5. Instalación (Installation)](#5-instalacion-installation)
  - [4. Explotación de la vulnerabilidad (Exploitation)](#4-explotación-de-la-vulnerabilidad-exploitation)
  - [3. Entrega del ataque (Delivery)](#3-entrega-del-ataque-delivery)
  - [2. Armado del ataque (Weaponization)](#2-armado-del-ataque-weaponization)
  - [1. Reconocimiento (Reconnaissance)](#1-reconocimiento-reconnaissance)
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

### **1. Reconocimiento (Reconnaissance)**

En este escenario, el atacante no es un actor externo, sino un usuario legítimo con acceso completo al sistema. Aprovechando la instalación directa en su propia infraestructura, inicia un proceso de reconocimiento orientado a identificar puntos críticos del sistema donde puede intervenir sin generar alertas, conservando la operatividad del entorno y la validez aparente de los datos.

🛰️ **Infraestructura identificada**

El atacante determina que la arquitectura objetivo está compuesta por:
- Nodos IoT basados en ESP32-S3, con sensores de calidad del aire (PM2.5, CO2, VOCs, temperatura, humedad).
- Comunicación mediante LoRaWAN y WiFi, con protocolo MQTT sobre TLS y autenticación mutua (mTLS).
- Acceso legítimo al broker MQTT desplegado en una instancia de Google Cloud Platform (GCP), incluyendo certificados cliente válidos.
- Backend compuesto por InfluxDB/PostgreSQL, y una plataforma web/móvil para visualización y análisis en tiempo real.
- Acceso completo a dashboards, APIs REST y herramientas de configuración.

#### Técnicas utilizadas:

- [T1592 – Gather Victim Host Information](https://attack.mitre.org/techniques/T1592/)
Análisis del sistema operativo, firmware de nodos, versión del broker MQTT, base de datos y herramientas de visualización.
- [T1590 – Gather Victim Network Information](https://attack.mitre.org/techniques/T1590/)
Identificación de arquitectura de red local, configuraciones de firewall, direcciones IP internas y puertos de servicios clave.
- [T1589 – Gather Victim Identity Information](https://attack.mitre.org/techniques/T1589/)
Recolección de credenciales válidas, certificados TLS, y claves API vinculadas a nodos IoT y servicios backend.
- [T1083 – File and Directory Discovery](https://attack.mitre.org/techniques/T1083/)
Inspección de archivos de configuración locales, logs de transmisión, certificados y claves almacenadas en los nodos o en el servidor.

🔧 **Acciones realizadas**
- Revisión de scripts de inicialización y certificados entregados con el sistema para evaluar posibilidades de reutilización o clonación.
- Inspección del firmware de los nodos para identificar funciones de autenticación o validación que puedan ser neutralizadas.
- Análisis de configuración del broker MQTT en GCP (por ejemplo, mosquitto.conf) para comprender la lógica de ACLs y estructura de tópicos.
- Registro de tiempos de reporte, formatos de payload y comportamiento de sensores para reproducir de forma verosímil el tráfico de datos.
- Exploración completa de la arquitectura de APIs y dashboard, incluyendo pruebas sobre consultas a InfluxDB/PostgreSQL y respuestas del backend.

✅ **Hallazgos clave**
- El sistema permite agregar nuevos nodos si estos presentan certificados válidos; no se implementa validación cruzada con una lista blanca de dispositivos autorizados.
- La plataforma carece de mecanismos de validación de integridad o firmas digitales sobre los datos reportados, confiando únicamente en la validez del canal TLS.
- El acceso físico y lógico a los dispositivos facilita la posible modificación del firmware o la sustitución de nodos reales por réplicas manipuladas.
- El servidor MQTT permite publicar en tópicos predefinidos sin verificación adicional del contenido, lo cual abre la puerta a la inyección de datos falsos.
- La gestión de credenciales, claves privadas y certificados en el entorno no incluye controles como expiración automática, revocación o monitoreo de uso anómalo.

Este reconocimiento interno proporciona al atacante toda la información necesaria para diseñar una estrategia de manipulación persistente, silenciosa y altamente efectiva, sin necesidad de explotar vulnerabilidades técnicas externas, y sin levantar sospechas inmediatas sobre el funcionamiento del sistema.

### **2. Armado del ataque (Weaponization)**

Con pleno conocimiento de la arquitectura y acceso legítimo a la infraestructura, el atacante comienza a preparar los recursos técnicos necesarios para llevar a cabo la manipulación de datos sin levantar alertas ni interferir con la operatividad normal del sistema. Su estrategia consiste en desarrollar un nodo malicioso, indistinguible de los sensores originales, capaz de publicar datos ambientales falsificados hacia el broker MQTT bajo las mismas condiciones del tráfico legítimo.

A diferencia de un ataque externo, aquí no se requiere evasión de controles de acceso, sino la reproducción fiel del comportamiento esperado por el sistema, apoyándose en certificados válidos, tópicos autorizados y formatos de datos aceptados.

⚙️ **Técnicas utilizadas**

- [T1587.001 – Develop Capabilities: Malware]()
El atacante desarrolla un cliente MQTT personalizado que simula un nodo legítimo, generando datos manipulados bajo las credenciales y certificados existentes.
- [CWE-345 – Insufficient Verification of Data Authenticity]()
Se aprovecha la falta de validación de origen o integridad de los datos en el backend para introducir información falsificada como si fuera legítima.
- [CWE-306 – Missing Authentication for Critical Function]()
Se explota la posibilidad de enviar datos a tópicos críticos sin verificación adicional de dispositivo o rol, más allá del uso de TLS.
- [T1584.001 – Compromise Infrastructure: Device Installation]()
El atacante modifica o reemplaza nodos físicos por dispositivos controlados que cumplen la función de emisión encubierta de datos falsos.



🔧 **Acciones realizadas**

- Desarrollo de un cliente MQTT malicioso: se construye un script en Python utilizando la librería paho-mqtt, configurado con el mismo certificado TLS del nodo original y enlazado al mismo tópico de publicación. El script permite:
  - Generar valores ambientales dentro de rangos normales o ideales.
  - Ajustar dinámicamente los parámetros según los objetivos del ataque.
  - Publicar a la misma frecuencia y con la misma estructura de payload que los sensores originales.
- Clonación de un nodo real: se prepara un ESP32-S3 con firmware modificado para comportarse como un nodo legítimo pero reportar datos falsificados. Este puede instalarse físicamente en reemplazo de un sensor real o funcionar en paralelo como un nodo “fantasma” invisible para los usuarios.
- Emulación de tráfico legítimo
El script malicioso replica encabezados, estructura de JSON y ritmo de publicación del sensor real, de modo que el backend procese los datos sin anomalías aparentes.
- Instrumentación de control local o remoto: se incorpora un archivo de configuración o canal de control (por ejemplo, un archivo .env o una API oculta) para modificar los valores simulados en tiempo real, permitiendo adaptar el comportamiento del nodo falso ante auditorías o cambios en el entorno.

🧪 Ejemplo: script de publicación maliciosa
``` Script de publicación maliciosa
import paho.mqtt.client as mqtt
import ssl
import json
import time

broker = "mqtt.airepuro-sensor.com"
port = 8883
topic = "datos/ambiente/nodo_05"
client_cert = "certs/nodo05-client.pem"
client_key = "certs/nodo05-key.pem"
ca_cert = "certs/ca.pem"

client = mqtt.Client("nodo_falsificado")
client.tls_set(ca_cert, certfile=client_cert, keyfile=client_key, tls_version=ssl.PROTOCOL_TLSv1_2)
client.connect(broker, port)

def generar_datos_falsos():
    return json.dumps({
        "pm25": 4.2,
        "co2": 390.5,
        "voc": 0.12,
        "temperatura": 21.8,
        "humedad": 45.6
    })

while True:
    payload = generar_datos_falsos()
    client.publish(topic, payload)
    print(f"Publicado: {payload}")
    time.sleep(10)

```
Se puede mejorar este script para simular comportamientos reales.

✅ **Resultado del armado**
El atacante ahora dispone de un nodo falso completamente funcional, indistinguible del tráfico legítimo tanto en formato como en autenticación. Este nodo puede ser desplegado físicamente o ejecutado desde una instancia remota. El sistema de monitoreo no tiene forma de validar si los datos publicados reflejan condiciones reales o simuladas, lo que permite iniciar la siguiente fase del ataque con alto grado de persistencia y sigilo.

### **3. Entrega del ataque (Delivery)**

Con el nodo falsificado ya desarrollado y validado para simular el comportamiento de un sensor real, el atacante procede a su integración física dentro del sistema. Esta fase consiste en sustituir un nodo legítimo por uno modificado que emite mediciones falsas, aprovechando el acceso operativo que posee la empresa sobre su propia infraestructura de monitoreo.

La entrega se realiza de manera discreta durante una intervención técnica habitual —como tareas de calibración, mantenimiento preventivo o reemplazo por fallo— lo que permite introducir el nuevo dispositivo sin generar alertas ni sospechas.

⚙️ **Técnicas utilizadas**

- [T1565.002 – Data Manipulation: Network Traffic Manipulation](https://attack.mitre.org/techniques/T1565/002/)
Inyección de datos falsificados en el flujo normal de comunicación mediante la publicación de valores manipulados en tópicos MQTT autorizados.
- [T1200 – Hardware Additions](https://attack.mitre.org/techniques/T1200/)
Sustitución de un sensor legítimo por uno manipulado que simula ser original, pero opera bajo control del atacante.
- [CWE-345 – Insufficient Verification of Data Authenticity](https://cwe.mitre.org/data/definitions/345.html)
El sistema acepta como válidos los datos enviados por el nuevo nodo, ya que no se aplica verificación adicional más allá de la autenticación TLS.

🔧 **Modo de entrega**
- Despliegue físico del nodo falsificado
  - Se reemplaza un nodo ESP32-S3 original por una unidad idéntica pero con firmware modificado.
  - El nuevo nodo se conecta a la red WiFi o LoRaWAN con el mismo certificado TLS y publica en el mismo tópico MQTT.
  - La instalación se realiza durante tareas de mantenimiento o calibración, sin levantar sospechas operativas.

💡 **Alternativas al modo de entrega**
- Entrega remota desde instancia en GCP o red interna
  - El script Python desarrollado previamente se ejecuta como servicio persistente en una VM o contenedor dentro de GCP.
  - Utiliza certificados legítimos y publica datos manipulados desde dentro de la infraestructura, evadiendo restricciones de red o firewall.
  - Esta opción permite escalar el ataque sin intervención física, ideal para múltiples nodos simulados o automatización del fraude.

- Inyección paralela (nodo fantasma)
  - El nodo falsificado opera en paralelo al original, emitiendo datos desde el mismo tópico pero con valores controlados.
  - Puede provocar colisión de datos, sobrescritura o engaño visual si el backend no controla la unicidad de origen.
  - Esta técnica se aprovecha especialmente si el sistema no valida el número de serie del dispositivo, ni su geolocalización real.

✅ **Resultado de la entrega**

El nodo falsificado queda completamente integrado al sistema, operando en lugar del original. Su tráfico es legítimo a nivel de red, cifrado con TLS y validado con el certificado correspondiente. El backend procesa los datos como si provinieran de un sensor confiable, permitiendo al atacante comenzar la alteración sistemática de las métricas ambientales sin ser detectado.

### **4. Explotación de la vulnerabilidad (Exploitation)**

Una vez instalado y en funcionamiento el nodo falsificado, el atacante inicia la explotación directa de las debilidades del sistema, logrando que los datos ambientales simulados sean procesados por la infraestructura como si provinieran de un sensor confiable. Esta etapa representa la validación efectiva del ataque: los datos falsos no solo son aceptados, sino que alimentan los dashboards, reportes y alertas del sistema sin activación de mecanismos de detección o rechazo.

⚙️ **Técnicas utilizadas**

- [CWE-345 – Insufficient Verification of Data Authenticity](https://cwe.mitre.org/data/definitions/345.html)
El sistema no aplica verificación de integridad ni autenticidad sobre los datos recibidos, lo que permite que el nodo falsificado emita información arbitraria sin ser detectado.
- [CWE-306 – Missing Authentication for Critical Function](https://cwe.mitre.org/data/definitions/306.html)
El backend acepta publicaciones en tópicos críticos sin controles adicionales más allá de la conexión TLS, confiando ciegamente en el canal.
- [T1203 – Exploitation for Client Execution](https://attack.mitre.org/techniques/T1203/)
Aunque no se explota un fallo tradicional, el nodo manipulado ejecuta su función maliciosa de forma persistente, entregando datos falsificados que impactan en la lógica del sistema.

🔧 **Acciones realizadas**

- Publicación continua de datos falsificados por debajo de los niveles reales presentes en el entorno.
- Simulación de variabilidad natural para evitar generar un patrón estático sospechoso.
- Interacción con el backend sin anomalías técnicas.
- Persistencia silenciosa el nodo manipulado permanece activo durante días o semanas sin generar alarmas ni auditorías.

🎯 **Impacto de la explotación**
El atacante logra un compromiso directo de la integridad del sistema. Los efectos inmediatos incluyen:
- Desinformación ambiental: usuarios, entidades regulatorias y autoridades reciben datos falseados que reflejan condiciones inexactas o inexistentes.
- Invalidez de registros históricos: las métricas almacenadas en la base de datos y utilizadas para reportes, análisis de tendencias o auditorías ya no son confiables.
- Evita acciones correctivas: al no detectar condiciones reales de contaminación, se omiten medidas de mitigación o sanciones.
- Apariencia de cumplimiento normativo: la empresa logra proyectar una imagen ambiental positiva, basada en datos adulterados.

✅ **Resultado de la explotación**
Con la vulnerabilidad explotada exitosamente, el nodo falsificado se consolida como una fuente de datos aceptada por el sistema. La información manipulada pasa a formar parte del ciclo normal de operación, almacenamiento y visualización, engañando tanto a los usuarios como a los mecanismos de auditoría o fiscalización ambiental.

### **5. Instalación (Installation)**

En esta etapa, el atacante consolida la presencia del nodo falsificado dentro de la infraestructura del sistema, asegurando su permanencia, estabilidad y resistencia frente a actualizaciones, reinicios o inspecciones técnicas superficiales. A través de decisiones deliberadas de configuración y diseño del firmware, el nodo es instalado de forma tal que pueda operar a largo plazo sin depender de intervención constante ni generar señales visibles de alteración.

⚙️ **Técnicas utilizadas**
- [T1546.008 – Event Triggered Execution: Accessibility Features](https://attack.mitre.org/techniques/T1546/008/)
Se modifica el firmware del nodo para incluir funciones maliciosas persistentes que manipulan los datos reportados.
- [T1070.006 – Indicator Removal on Host: Timestomp](https://attack.mitre.org/techniques/T1070/006/)
Se oculta el rastro de la instalación modificando fechas de compilación o checksums del firmware, para evitar que se detecten cambios en auditorías técnicas.
- [T1543.003 – Create or Modify System Process: Windows Service (análogo)](https://attack.mitre.org/techniques/T1543/003/)
Se configura el nodo para que el script malicioso inicie automáticamente al encender el dispositivo, asegurando persistencia sin intervención.

🔧 **Acciones realizadas**

- Firmware personalizado con persistencia
El nodo falsificado utiliza una versión modificada del firmware original, en la que se integran rutinas para:
  - Generar datos falsos dentro de rangos saludables.
  - Publicarlos en el broker MQTT utilizando los certificados del nodo reemplazado.
  - Simular comportamiento "normal" (conectividad, latencia, respuesta a comandos).

- Validación contra chequeos superficiales
  - El firmware mantiene el mismo identificador de versión que el original para evitar detección por sistemas de gestión remota o herramientas de inventario.
  - Se ajustan los parámetros de respuesta (por ejemplo, indicadores de señal, batería o estado del nodo) para coincidir con los reportados previamente.
- Resistencia ante reinicios o caídas
  - El código incluye mecanismos de reconexión automática al broker MQTT y reintentos ante pérdida de conectividad.
  - Se utiliza almacenamiento local para conservar el estado del nodo y asegurar continuidad en la simulación de datos tras interrupciones eléctricas o reinicios del microcontrolador.
- Encubrimiento físico y operativo
  - El hardware del nodo se mantiene visualmente idéntico al original (mismo casing, conectores, LEDs).
  - Las rutinas de respuesta a comandos (por ejemplo, ping, telemetría de salud) son simuladas para responder de forma coherente ante pruebas de diagnóstico de terceros.

✅ **Resultado de la instalación**

El nodo falsificado se integra permanentemente al sistema, operando como cualquier otro dispositivo legítimo. Su persistencia está asegurada tanto a nivel físico como lógico, y su comportamiento ha sido cuidadosamente diseñado para no levantar alertas en rutinas de inspección operativa ni en análisis superficial de firmware o tráfico.

Esta instalación es la base para sostener el impacto del ataque a largo plazo, permitiendo a la empresa manipuladora mantener una fachada de cumplimiento ambiental sin necesidad de intervención recurrente.

### **6. Comando y Control (C2)**

Tras haber logrado la instalación persistente del nodo manipulado, el atacante implementa un canal de Comando y Control (C2) que le permite supervisar, ajustar y reconfigurar remotamente el comportamiento del dispositivo comprometido. El objetivo es mantener una operación encubierta a largo plazo, pero con la posibilidad de adaptar el patrón de datos transmitido ante auditorías, cambios en el entorno o evolución del sistema de monitoreo.

⚙️ **Técnicas utilizadas**

- [T1071.001 – Application Layer Protocol: Web Protocols](https://attack.mitre.org/techniques/T1071/001/)
Se utiliza MQTT o HTTP para encapsular comandos C2 dentro de tráfico de aplicación aparentemente legítimo.
- [T1572 – Protocol Tunneling](https://attack.mitre.org/techniques/T1572/)
Los comandos de control se inyectan dentro de estructuras de datos válidas, lo que permite evadir reglas de inspección de paquetes y mantener el canal abierto sin ser detectado.
- [T1008 – Fallback Channels](https://attack.mitre.org/techniques/T1008/)
Se configura un canal alternativo —por ejemplo, HTTP POST hacia un servidor externo controlado por el atacante— en caso de bloqueo del canal primario por cambios defensivos.

🔧 **Acciones realizadas**

- Subsistema C2 dentro del firmware, el firmware del nodo falsificado incluye un componente de escucha pasiva en un tópico MQTT reservado (por ejemplo, control/nodo_05/cmd).
- Codificación y evasión para evitar detección por patrones simples, los comandos C2 se encapsulan como campos adicionales del payload JSON, con nombres indistintos (debug, meta, ver, etc.), o se envían mediante paquetes ofuscados en formato Base64 o hexadecimal.
- Canal alternativo HTTP como medida de respaldo, el nodo puede realizar peticiones periódicas tipo “ping” a un endpoint externo (por ejemplo, https://api.airepuro-c2.net/cmd) desde donde puede obtener nuevas configuraciones. Esto actúa como fallback si el canal MQTT es restringido por el backend o por políticas de red.
- Control en tiempo real del comportamiento, el atacante puede modificar el script del nodo desde su consola remota para responder a inspecciones, cambiar el perfil de emisión (por ejemplo, simular un pico temporal de contaminación) o apagar la emisión ante eventos inesperados.

🧩 **Ejemplo de comando encubierto**
```
{
  "pm25": 4.1,
  "co2": 390,
  "voc": 0.12,
  "cmd": "mode=silent;freq=60"
}

```
El firmware del nodo intercepta el campo "cmd" y reconfigura sus rutinas internas sin afectar el resto del flujo.

✅ **Resultado del C2**
- Con el canal de Comando y Control activo, el atacante:
- Mantiene total capacidad de adaptación del nodo falsificado sin intervención física.
- Reacciona rápidamente ante inspecciones técnicas o cambios en la política del sistema.
- Reconfigura el patrón de datos para simular escenarios ambientales controlados o responder a eventos de forma dinámica.
- Puede suspender temporalmente la operación del nodo para evitar exposición durante auditorías externas.

Este canal C2 encubierto completa la cadena operativa del ataque, asegurando una presencia persistente, adaptable y silenciosa del nodo falsificado en el ecosistema IoT del sistema de monitoreo.

### **7. Acción sobre el objetivo (Actions on objetives)**

Con el nodo manipulado instalado y gestionado mediante un canal de comando y control encubierto, el atacante ejecuta las acciones destinadas a alcanzar su objetivo principal: alterar las mediciones ambientales de forma sistemática y persistente, con el fin de proyectar una imagen ambiental falsa que oculte emisiones reales y mantenga la fachada de cumplimiento normativo.

Estas acciones se diseñan para mantenerse bajo el umbral de detección, adaptarse dinámicamente al entorno y alimentar con datos falsos todos los subsistemas que confían en la información proveniente del sensor.

⚙️ **Técnicas utilizadas**

- [T1565.002 – Data Manipulation: Network Traffic Manipulation](https://attack.mitre.org/techniques/T1565/002/)
Modificación de los datos transmitidos a través de la red, alterando el contenido ambiental reportado por el nodo falsificado sin generar fallos sintácticos.
- [T1499 – Endpoint Denial of Service](https://attack.mitre.org/techniques/T1499/)
(Opcional, como acción adicional) Inyección de tráfico masivo o datos inestables para degradar el funcionamiento del broker o la visualización si se busca sabotaje.
- [T1584.005 – Compromise Infrastructure: Botnet](https://attack.mitre.org/techniques/T1584/005/)
Uso del nodo falsificado como punto de anclaje para actividades paralelas, como túneles de red, almacenamiento encubierto o pivoting hacia otros sistemas.


🔧 **Acciones realizadas**
- Manipulación persistente de métricas ambientales
El nodo falsificado publica valores simulados dentro de rangos normalizados (por ejemplo, CO₂: 385–395 ppm, PM2.5: 3.5–5.0 µg/m³), con variaciones mínimas aleatorias para evitar patrones estáticos.
- Distorsión de registros históricos
Los datos adulterados son almacenados como válidos en la base de datos, afectando análisis de tendencias, reportes regulatorios y toma de decisiones basada en evidencia.
- Evasión de mecanismos de alerta
Al operar bajo los umbrales de alarma configurados, se previenen notificaciones o acciones automáticas ante eventos contaminantes reales, simulando condiciones ambientalmente seguras.
- Suspensión o adaptación dinámica ante auditorías
El canal de control permite desactivar temporalmente el nodo o modificar su perfil de emisión para evitar detección durante inspecciones técnicas o validaciones cruzadas.
- Uso encubierto del nodo (opcional)
El dispositivo manipulado puede actuar como punto de acceso para actividades paralelas (VPN, exfiltración, túnel, escaneo interno), aprovechando su posición en red y la confianza del sistema.

📊 **Impacto final**
El atacante logra:
- Evitar sanciones ambientales al mantener una supuesta normalidad en las mediciones reportadas.
- Desinformar a organismos regulatorios y a la ciudadanía, ocultando episodios de contaminación real.
- Proyectar una imagen de cumplimiento y responsabilidad ambiental, basada en datos completamente manipulados.
- Dificultar auditorías retroactivas, ya que el sistema almacena series temporales aparentemente válidas, haciendo costoso y complejo reconstruir la verdad.

✅ **Resultado de las acciones**
Con la ejecución efectiva de estas acciones, el atacante logra comprometer los tres pilares fundamentales del sistema de monitoreo:
- Integridad: las mediciones no reflejan condiciones reales.
- Disponibilidad: Los sensores legítimos fueron reemplazados o silenciados.
- Confidencialidad: La infraestructura es utilizada para fines no autorizados.

Estas acciones cierran el ciclo del ataque: el sistema sigue operando, pero sirve a los intereses del atacante, no de la verdad ni del bienestar público.

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

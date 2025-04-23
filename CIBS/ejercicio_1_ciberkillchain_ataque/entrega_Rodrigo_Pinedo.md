# Ejercicio CiberKillChain - Ataque

## 📈 **Sistema de monitoreo de calidad del aire**

## **Índice**
- [📄 **Descripción**](#-descripción)
- [💡 **Objetivo del proyecto**](#-objetivo-del-proyecto)
- [⚛ **Sistema IoT**](#-sistema-iot)
- [🎯 **Objetivo del ataque**](#-objetivo-del-ataque)
- [🥊 **Resolución del ataque**](#-resolución-del-ataque)
  - [1. Reconocimiento (Reconnaissance)](#1-reconocimiento-reconnaissance)
  - [2. Armado del ataque (Weaponization)](#2-armado-del-ataque-weaponization)
  - [3. Entrega del ataque (Delivery)](#3-entrega-del-ataque-delivery)
  - [4. Explotación de la vulnerabilidad (Exploitation)](#4-explotación-de-la-vulnerabilidad-exploitation)
  - [5. Instalación](#5-instalation)
  - [6. Comando y Control (C2)](#6-comando-y-control-c2)
  - [7. Acción sobre el objetivo (Actions on objetives)](#7-acción-sobre-el-objetivo-acctions-on-objetives)
- [🔀 **Diagrama de Flujos del ataque**](#-flujos-del-ataque)
- [🧠 **Conclusiones**](#-conclusiones)
- [🥇 **Autor**](#autor)

## 📄 **Descripción**

Sistema de monitoreo basado en IoT para medir la calidad del aire en entornos urbanos e industriales. Utiliza sensores conectados a una red LoRaWAN/WiFi/MQTT, enviando datos a un servidor central, donde son procesados y visualizados en una plataforma web/móvil.

## 💡 **Objetivo del proyecto**

Implementar una solución IoT para monitorear la calidad del aire en entornos urbanos e industriales. Utiliza sensores conectados a una red LoRaWAN/WiFi/MQTT, enviando datos a un servidor central, donde son procesados y visualizados en una plataforma web/móvil.

## ⚛ **Sistema IoT (estructura)**

El sistema consiste en una red de dispositivos IoT equipados con sensores ambientales que miden parámetros críticos de calidad del aire. Los datos se transmiten a un servidor central mediante protocolos inalámbricos, se almacenan en una base de datos y se visualizan en una plataforma web/móvil en tiempo real.

## 🎯 **Objetivo del ataque**

Falsificar las mediciones ambientales registradas por los sensores, simulando una mejora artificial y sostenida en la calidad del aire. Con esta manipulación, el atacante busca favorecer intereses económicos por evadir sanciones y regulaciones ambientales, desinformando a ciudadanos y autoridades responsables de políticas públicas.

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

### **5. Instalación**

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

Luego de lograr la inyección exitosa de datos manipulados en el sistema, el atacante establece un canal de control y monitoreo continuo sobre el nodo falso. Esta fase le permite ajustar el comportamiento del dispositivo emulado, mantener la persistencia del ataque y reaccionar ante posibles medidas de mitigación implementadas por los administradores del sistema.

⚙️ **Técnicas utilizadas**

- [T1071.001 – Application Layer Protocol: Web Protocols](https://attack.mitre.org/techniques/T1071/001/)

  El atacante utiliza protocolos de aplicación estándar (como HTTP o MQTT) para comunicarse con el nodo falso y enviarle comandos de actualización.

- [T1572 – Protocol Tunneling](https://attack.mitre.org/techniques/T1572/)

  Las instrucciones de control son encapsuladas dentro de mensajes válidos de MQTT, evadiendo la detección por parte de mecanismos de monitoreo o filtrado de red.

- [T1008 – Fallback Channels](https://attack.mitre.org/techniques/T1008/)

  En caso de ser bloqueado el canal principal, el atacante dispone de un servidor web externo que actúa como backup para mantener el control sobre el nodo malicioso.

🔧 **Acciones realizadas**

El atacante establece un script de control remoto que monitorea el estado del nodo falso desde un servidor externo. A través de este canal, puede actualizar en tiempo real:

- La frecuencia de publicación de datos.
- Los valores simulados en cada métrica (PM2.5, CO₂, VOCs, etc.).
- El comportamiento del nodo ante eventos del entorno (por ejemplo, al detectar actividad de administración o reconfiguración del sistema).

Para evitar la detección, encapsula los comandos de control dentro de mensajes MQTT aparentemente inofensivos o utiliza canales HTTP alternativos si detecta que el broker ha sido protegido o restringido.

También implementa mecanismos de auto-reconexión y resistencia a reinicios, lo que garantiza que el nodo falso se mantenga operativo y conectado incluso si hay cortes breves o acciones defensivas básicas.

💡 **Persistencia y adaptabilidad**

Este canal de comando y control otorga al atacante la capacidad de:

- Cambiar dinámicamente los objetivos del ataque (desinformar, ocultar, sabotear, utilizar información).
- Evadir contramedidas implementadas de forma reactiva.
- Reutilizar la infraestructura comprometida como plataforma para lanzar ataques hacia otros sistemas o redes.

Con esta etapa completada, el atacante mantiene el control activo sobre la operación del nodo falsificado, pudiendo extender su campaña, escalar el ataque o pivotar hacia nuevas oportunidades dentro o fuera del entorno IoT.

### **7. Acción sobre el objetivo (Actions on objetives)**

Con el nodo malicioso operativo y el canal de control consolidado, el atacante inicia las acciones finales sobre el sistema, orientadas a afectar directamente sus funciones críticas, manipular los resultados y explotar la infraestructura comprometida para objetivos secundarios. Estas acciones varían en función del propósito estratégico definido: sabotaje, desinformación, o utilización encubierta de recursos.

⚙️ **Técnicas utilizadas**

- [T1565.002 – Data Manipulation: Network Traffic Manipulation](https://attack.mitre.org/techniques/T1565/002/)

  El atacante continúa alterando los datos ambientales publicados en los tópicos MQTT, afectando la calidad y veracidad de la información visualizada.

- [T1499 – Endpoint Denial of Service](https://attack.mitre.org/techniques/T1499/)

  Se incrementa la frecuencia de publicación de datos basura o se genera tráfico excesivo hacia el broker MQTT, con el objetivo de degradar el rendimiento del sistema o interrumpir su funcionamiento.

- [T1584.005 – Compromise Infrastructure: Botnet](https://attack.mitre.org/techniques/T1584/005/)

  Se utiliza el dispositivo comprometido como nodo de una botnet para operaciones externas, como túneles de tráfico, ataques distribuidos o persistencia dentro de otras redes.


🔧 **Acciones realizadas**

El atacante ejecuta distintas tácticas sobre el sistema comprometido:

1. Manipulación de datos históricos

  - Publica mediciones ambientales falsas durante un periodo prolongado, alterando las tendencias que luego son utilizadas para tomar decisiones.
  - Simula mejoras artificiales en la calidad del aire, generando una percepción errónea ante usuarios o autoridades.

2. Sabotaje controlado del sistema

  - Inunda el broker MQTT con datos duplicados, inestables o contradictorios, provocando inestabilidad en la base de datos y errores en el frontend del sistema.
  - Reduce la disponibilidad del servicio, interfiriendo con la recepción y visualización de datos de sensores legítimos.

3. Extensión del ataque

  - Utiliza el nodo falso como intermediario para escanear otras partes de la red o lanzar ataques laterales contra sistemas conectados.
  - Al ser parte de la infraestructura legítima, su actividad se enmascara dentro del tráfico esperado, dificultando su detección.

4. Utilización encubierta de recursos del sistema

  Aprovecha la conectividad y capacidad del nodo falsificado para ejecutar tareas paralelas encubiertas, como:

  - Montaje de un proxy o túnel VPN para anonimizar otras actividades maliciosas.
  - Almacenamiento oculto de información ilegal o robada, utilizando la base de datos o el almacenamiento de logs.
  - Implementación de un cliente de criptominería ligera, aprovechando el uso constante de energía eléctrica del nodo para generar ganancias sin levantar sospechas.

💥 **Resultado del ataque**

Como consecuencia de estas acciones, el atacante logra:

- Desinformar a usuarios y tomadores de decisiones, comprometiendo la confianza en el sistema.
- Interrumpir el monitoreo ambiental, afectando la capacidad de respuesta ante situaciones de contaminación real.
- Reutilizar la infraestructura como plataforma encubierta, aumentando el impacto y la persistencia del ataque sin necesidad de comprometer nuevos dispositivos.

  Como resultado, la infraestructura del sistema comienza a trabajar en segundo plano para fines no previstos, sin interferir directamente en su función principal, lo que hace más difícil su detección.

🧨 **Ejemplo – Sabotaje controlado (DoS suave)**

Este script inunda el broker MQTT con publicaciones rápidas, duplicadas o ruido constante, afectando el procesamiento y la capacidad de respuesta del sistema.
```script
broker = "192.168.1.100"
topic = "sensores/aire/nodo_03"

cliente = mqtt.Client("nodo_flooder")
cliente.connect(broker, 1883, 60)

# Publica basura o datos redundantes muy seguido
while True:
    payload = json.dumps({
        "pm25": random.uniform(5, 5.2),
        "co2": random.uniform(399, 401),
        "voc": random.uniform(0.2, 0.3),
        "temperatura": random.uniform(21, 23),
        "humedad": random.uniform(45, 55)
    })
    cliente.publish(topic, payload)
    print(f"⚠️ Publicando en exceso: {payload}")
    time.sleep(0.5) # Alta frecuencia
```

## 🔀 **Flujos del ataque**

El atacante ejecuta el ataque en una secuencia estructurada, respetando las fases metodológicas de la Cyber Kill Chain. Cada fase es cuidadosamente planificada para mantener un bajo perfil, evadir mecanismos de detección y maximizar el impacto sobre el sistema objetivo.

```flujo
1. [Reconocimiento]
   ↓
   Analiza la arquitectura del sistema, tecnologías empleadas (LoRaWAN, MQTT, WiFi, ESP32) y posibles vectores de ataque.
   Identifica tópicos MQTT, APIs expuestas y ausencia de cifrado.

2. [Armado del ataque]
   ↓
   Desarrolla un script en Python que simula un nodo IoT legítimo.
   Configura payloads manipulados que aparentan ser datos reales (PM2.5, CO₂, VOCs, etc.).

3. [Entrega del ataque]
   ↓
   Publica datos falsificados en el broker MQTT mediante el nodo simulado.
   Inyecta información sin activar alertas ni ser detectado por la interfaz de monitoreo.

4. [Explotación de la vulnerabilidad]
   ↓
   Aprovecha la falta de validación de origen de datos y ausencia de cifrado.
   Logra que el sistema procese, almacene y visualice los datos maliciosos como si fueran auténticos.

5. [Comando y Control (C2)]
   ↓
   Mantiene comunicación remota con el nodo falso.
   Ajusta los parámetros del ataque en tiempo real y responde a eventos defensivos.

6. [Acción sobre el objetivo]
   ↓
   Manipula las tendencias históricas de calidad del aire, suprime alertas, genera datos engañosos.
   Utiliza el sistema como proxy encubierto, punto de pivote y posible bot para operaciones futuras.
```
Este flujo resume la lógica del ataque y permite visualizar de manera compacta cómo se encadenan las acciones para alcanzar el objetivo: comprometer la confiabilidad, disponibilidad y legitimidad del sistema de monitoreo ambiental, mientras se ocultan rastros y se explotan recursos de manera persistente.

## 🧠 **Conclusiones**

Mediante una aplicación estructurada de la metodología Cyber Kill Chain, el atacante consigue comprometer tanto la integridad como la confiabilidad del sistema IoT destinado al monitoreo de la calidad del aire. Para lograrlo, se apoya en debilidades presentes en los mecanismos de comunicación, autenticación y validación de datos del sistema.

A lo largo del ejercicio se pone en evidencia cómo un actor malicioso, con la planificación adecuada, es capaz de:

- Emular un nodo IoT legítimo, imitando con precisión el comportamiento de los sensores originales para integrarse al ecosistema sin levantar sospechas.
- Alterar la percepción ambiental, inyectando valores manipulados que pueden distorsionar decisiones técnicas, regulatorias o sociales basadas en esos datos.
- Explotar la infraestructura comprometida para fines ocultos, como canal de anonimización de tráfico, almacenamiento de datos ilícitos o incluso como parte de una red de bots.
- Mantener el control a largo plazo, utilizando un canal de comando y control para ajustar dinámicamente la operación del nodo malicioso en función de la situación.

Este ataque no se apoya en vulnerabilidades complejas ni requiere acceso físico a los dispositivos. Su efectividad se basa en el uso inteligente de técnicas conocidas de reconocimiento, análisis de red y manipulación de protocolos abiertos como MQTT, combinadas con herramientas accesibles públicamente.

El escenario permite reflexionar sobre los riesgos reales que enfrentan los sistemas IoT cuando se prioriza la funcionalidad por sobre la seguridad. En estos entornos, la falta de controles robustos puede convertir una red diseñada para el bienestar social en una puerta de entrada silenciosa para actores con fines completamente opuestos.

## 🥇 **Autor**

- Ing. Rodrigo Jurgen Pinedo Nava
- Universidad de Buenos Aires (UBA)
- Facultad de Ingeniería
- Especialización en Internet de las Cosas (IoT)
- **Linkedin:** https://www.linkedin.com/in/rodrigopinedo/

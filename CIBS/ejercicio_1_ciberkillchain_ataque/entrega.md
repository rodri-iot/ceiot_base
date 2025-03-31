# Entrega CiberKillChain - Ataque

## Rodrigo Jurgen Pinedo Nava
## **Sistema de monitoreo de calidad del aire**

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
  - [5. Comando y Control (C2)](#5-comando-y-control-c2)
  - [6. Acción sobre el objetivo (Actions on objetives)](#6-acción-sobre-el-objetivo-acctions-on-objetives)
- [🔀 **Diagrama de Flujos del ataque**](#-flujos-del-ataque)
- [🧠 **Conclusiones**](#-conclusiones)
- [🥇 **Autor**](#autor)

## 📄 **Descripción**

Sistema de monitoreo basado en IoT para medir la calidad del aire en entornos urbanos e industriales. Utiliza sensores conectados a una red LoRaWAN/WiFi/MQTT, enviando datos a un servidor central, donde son procesados y visualizados en una plataforma web/móvil.

## 💡 **Objetivo del proyecto**

Implementar una solución IoT para monitorear la calidad del aire en entornos urbanos e industriales. Utiliza sensores conectados a una red LoRaWAN/WiFi/MQTT, enviando datos a un servidor central, donde son procesados y visualizados en una plataforma web/móvil.

## ⚛ **Sistema IoT**

El sistema consiste en una red de dispositivos IoT equipados con sensores ambientales que miden parámetros críticos de calidad del aire. Los datos se transmiten a un servidor central mediante protocolos inalámbricos, se almacenan en una base de datos y se visualizan en una plataforma web/móvil en tiempo real.

## 🎯 **Objetivo del ataque**

Realizar un ataque dirigido al sistema de monitoreo de calidad del aire con el propósito de comprometer la integridad de los datos, interrumpir su disponibilidad o utilizar la infraestructura para actividades maliciosas.

## 🥊 **Resolución del ataque**

### **1. Reconocimiento (Reconnaissance)**

Antes de iniciar el ataque, el atacante deberá realizar una investigación exhaustiva sobre la infraestructura del sistema objetivo con el fin de identificar posibles vectores de entrada y preparar las fases posteriores de la intrusión. Su enfoque se basa en técnicas OSINT, escaneo activo, análisis pasivo de red y reconocimiento de servicios web.

🛰️ **Infraestructura identificada**

- Tecnologías utilizadas: LoRaWAN, MQTT, WiFi, servidor propio, ESP32-S3 y sensores ambientales.
- El sistema incluye un broker MQTT, una base de datos InfluxDB/PostgreSQL, y una plataforma de visualización web/móvil.

#### Técnicas utilizadas:

🕵️ **Recolección de información pública (OSINT)**

**Técnicas ATT&CK asociadas:**

- [T1592 – Gather Victim Host Information](https://attack.mitre.org/techniques/T1592/)
- [T1590 – Gather Victim Network Information](https://attack.mitre.org/techniques/T1590/)
- [T1589 – Gather Victim Identity Information](https://attack.mitre.org/techniques/T1589/)

**Acciones realizadas:**

- Revisión de documentación técnica publicada en GitHub, foros y sitios institucionales relacionados con el proyecto.
- Uso de Google Dorks para localizar configuraciones expuestas, credenciales o endpoints vulnerables.
- Búsqueda en motores como Shodan y Censys para identificar nodos conectados públicamente, como sensores o gateways LoRa.

🌐 **Escaneo de red y servicios**

**Técnicas ATT&CK asociadas:**

- [T1595 – Active Scanning](https://attack.mitre.org/techniques/T1595/)
- [T1595.003 – Service Scanning](https://attack.mitre.org/techniques/T1595/003/)

**Acciones realizadas:**

- Escaneo pasivo del tráfico de red con Wireshark para analizar protocolos como MQTT y LoRaWAN, identificando patrones de autenticación o mensajes sin cifrar.
- Uso de herramientas como Nmap y Masscan para detectar dispositivos IoT conectados, puertos abiertos y servicios activos.
- Aplicación de Amap para determinar versiones específicas de servicios expuestos, como brokers MQTT, paneles web o servicios API.

📡 **Identificación de dispositivos IoT**

**Técnica ATT&CK asociada:**

- [T1596 – Search Open Websites/Domains for IoT Devices](https://attack.mitre.org/techniques/T1596/)

**Acciones realizadas:**

- Captura de tráfico LoRaWAN y MQTT mediante sniffing, analizando los paquetes enviados por los nodos ESP32.
- Identificación de dispositivos WiFi o BLE con herramientas como Kismet y Aircrack-ng.
- Análisis espectral con Software Defined Radio (SDR) para estudiar las señales LoRa en tiempo real y mapear la cobertura.

🔍 **Análisis de APIs y servicios web**

**Técnica ATT&CK asociada:**

- [T1592.002 – Software](https://attack.mitre.org/techniques/T1592/002/)

**Acciones realizadas:**

- Enumeración de endpoints de la API REST del backend para descubrir funcionalidades expuestas sin autenticación o validación adecuada.
- Verificación de configuración del broker MQTT para determinar si permite conexiones anónimas o sin TLS.
- Evaluación de la plataforma web mediante Burp Suite y Postman, probando inyecciones, autenticaciones débiles y fugas de información.

### **2. Armado del ataque (Wqeaponization)**

Luego de haber recolectado suficiente información sobre la infraestructura y los servicios involucrados en el sistema de monitoreo de calidad del aire, el atacante comenzará a preparar las herramientas y cargas maliciosas necesarias para comprometer el entorno. En esta etapa, se enfoca en explotar debilidades específicas detectadas en la comunicación entre dispositivos IoT y el servidor central.

⚙️ **Técnicas utilizadas**

- [CWE-311 – Lack of Encryption in Data Transmission](https://cwe.mitre.org/data/definitions/311.html)
  Se observa que la comunicación entre los nodos IoT y el servidor mediante el protocolo MQTT no utiliza cifrado TLS, lo que permite interceptar, leer o manipular los datos transmitidos.
- [CWE-345 – Insufficient Verification of Data Authenticity](https://cwe.mitre.org/data/definitions/345.html)
  El servidor no verifica la autenticidad de los datos que recibe desde los sensores, permitiendo la inyección de información falsa sin mecanismos robustos de validación.
- [T1587.001 – Develop Capabilities: Malware](https://attack.mitre.org/techniques/T1587/001/)
  El atacante diseña una herramienta personalizada que simula el comportamiento de un nodo legítimo, capaz de enviar datos ambientales manipulados al sistema objetivo.

🔧 **Acciones realizadas**

Tras analizar el comportamiento del sistema, el atacante decide no comprometer físicamente los dispositivos, sino emular un nodo IoT que transmita datos maliciosos al broker MQTT. Para ello, desarrolla un script en Python que se hace pasar por uno de los nodos del sistema, utilizando las estructuras de datos y los tópicos identificados en la fase anterior.

Este script permite publicar mensajes falsificados con valores normalizados, diseñados para evadir alertas o generar condiciones ambientales falsas según la estrategia del atacante. Al mantener un perfil de tráfico coherente, el nodo emulado puede pasar desapercibido en el flujo normal de comunicaciones.

🧪 Ejemplo de código: simulación de nodo MQTT malicioso
``` Codigo simulación de nodo MQTT
import paho.mqtt.client as mqtt
import json
import time
import random

broker_ip = "192.168.1.100"  # Dirección IP del broker MQTT objetivo
topic = "sensores/aire/nodo_03"

def generar_datos_falsos():
    return json.dumps({
        "pm25": round(random.uniform(2.0, 10.0), 2),
        "co2": round(random.uniform(350, 600), 2),
        "voc": round(random.uniform(0.1, 0.5), 2),
        "temperatura": round(random.uniform(20, 25), 2),
        "humedad": round(random.uniform(40, 60), 2)
    })

cliente = mqtt.Client("nodo_falso_aire")
cliente.connect(broker_ip, 1883, 60)

while True:
    payload = generar_datos_falsos()
    cliente.publish(topic, payload)
    print(f"Payload enviado: {payload}")
    time.sleep(5)
```

### **3. Entrega del ataque (Delivery)**

Una vez desarrolladas las herramientas para simular un nodo IoT malicioso, el atacante procederá a su despliegue, integrándolo dentro de la red de comunicaciones del sistema objetivo. La entrega del ataque se realiza mediante la publicación de datos falsificados hacia el brocker MQTT del sistea, simulando de forma convincente el comportamiento de un nodo legítimo.

⚙️ **Técnicas utilizadas**

- [T1565.002 – Data Manipulation: Network Traffic Manipulation](https://attack.mitre.org/techniques/T1565/002/)

  Se manipula el flujo de datos transmitido a través del protocolo MQTT, alterando su contenido en tránsito o publicando directamente información fabricada.
- [T1602 – Data Manipulation](https://attack.mitre.org/techniques/T1602/)

  Se inyectan datos falsos con apariencia válida, con el fin de engañar al sistema de monitoreo y alterar su percepción de las condiciones ambientales.

🔧 **Acciones realizadas**

El atacante configura un entorno desde el cual se peuda emular un nodo IoT se conecta al mismo brocker MQTT utilizado por el sistema. Aprovechando la falta de autentificación estricta en el brocker y a la carencia de cifrado en la transmisión, logra establecer conexión sin restricciones aparentes.

El nodo malicioso comienza a publicar datos que simulan condiciones normales o controladas del aire, evitando generar alertas. Estos datos falsificados son entregados en los mismo tópicos MQTT que utilizan los sensores legítimos, logrando así infiltrarse en el sistema sin ser detectado.

Reforzar la manipulación, requerirá de implementar un mecanismo de inyección intermitente de ruido ambiental, valores ligeramente alterados que simulan fluctuaciones normales en la calidad del aire. Esta técnica reduce la capacidad de los administradores para distinguir entre lecturas reales y maliciosas, disminuyendo la efectividad de los algoritmos de alerta.

📡 **Escenario de entrega**

El ataque puede desplegarse desde distintos entornos según el objetivo:

- Una red Wi-Fi local, si el atacante se encuentra físicamente cerca del sistema.
- Una instancia en la nube, configurada como cliente MQTT remoto que publica periódicamente los datos falsos.
- Un dispositivo de campo con conectividad LoRa o Wi-Fi, disfrazado como nodo adicional en la red.

Esta fase de entrega es crítica, ya que define la forma en que el sistema será alimentado con datos alterados sin necesidad de comprometer directamente los sensores físicos ni los servidores. El uso de canales abiertos y la falta de verificación de integridad permiten al atacante integrarse en el flujo de datos de manera transparente.

### **4. Explotación de la vulnerabilidad (Exploitation)**

Una vez entregado el nodo malicioso al sistema, el atacante ejecutará la explotación de las debilidades previamente identificadas en la infraestructura del sistema de monitoreo. En esta etapa, su objetivo es lograr que los datos falsificados sean aceptados como válidos por el sistema, sin activar mecanismos de alerta ni generar sospechas por parte de los administradores.

⚙️ **Técnicas utilizadas**

- [CWE-345 – Insufficient Verification of Data Authenticity](https://cwe.mitre.org/data/definitions/345.html)

  El servidor no cuenta con mecanismos adecuados para verificar que los datos provienen de un dispositivo autorizado o que no han sido manipulados en tránsito.
- [CWE-306 – Missing Authentication for Critical Function](https://cwe.mitre.org/data/definitions/306.html)

  El broker MQTT permite la publicación en tópicos sensibles sin requerir autenticación robusta o control de acceso por dispositivo.
- [T1203 – Exploitation for Client Execution](https://attack.mitre.org/techniques/T1203/)

  El atacante fuerza al sistema backend a procesar entradas manipuladas como si fueran datos válidos generados por los sensores.

🔧 **Acciones realizadas**

El atacante publica mensajes cuidadosamente elaborados en el tópico MQTT correspondiente a uno de los nodos legítimos. Los mensajes incluyen lecturas ambientales falsas, generadas con valores que simulan condiciones normales o incluso condiciones anómalas según el objetivo final.

Debido a que el servidor no implementa controles para verificar la integridad de los datos (por ejemplo, mediante firmas digitales, tokens HMAC u otras formas de autenticación de origen), los paquetes son aceptados, procesados y almacenados en la base de datos como si provinieran de un sensor confiable.

Esta explotación permite modificar la percepción que el sistema tiene de la calidad del aire en determinadas zonas. El atacante puede:

- Suprimir alertas reales, enviando valores dentro de rangos normales para ocultar eventos contaminantes reales.
- Generar falsas alertas, simulando aumentos artificiales de CO₂ o partículas PM2.5.
- Distorsionar los datos históricos, afectando análisis de tendencias o reportes.

💡 **Impacto de la explotación**

El atacante tiene su objetivo claro para atacar el sistema y de seguro esperará cierto impacto para sentirse satisfecho, pudiendo ser:

- El sistema de monitoreo pierde confiabilidad, ya que las decisiones tomadas por los usuarios (ciudadanos, industrias, gobiernos) se basan en datos comprometidos.
- Se habilita el camino para ataques posteriores, como sabotaje, extorsión o manipulación pública de la información ambiental.
- Se posibilita el uso de la infraestructura como vector indirecto para atacar a otros sistemas (p. ej., a través de reportes falsos que activen protocolos automáticos de respuesta).

### **5. Comando y Control (C2)**

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

### **6. Acción sobre el objetivo (Actions on objetives)**

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

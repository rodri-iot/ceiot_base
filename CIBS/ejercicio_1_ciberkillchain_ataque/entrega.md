# Entrega CiberKillChain - Ataque

## Rodrigo Jurgen Pinedo Nava
## **Sistema de monitoreo de calidad del aire**

## **Índice**
- [📄 **Descripción**](#-descripción)
- [💡 **Objetivo del proyecto**](#-objetivo-del-proyecto)
- [⚛ **Sistema IoT**](#-sistema-iot)
- [🎯 **Objetivo del ataque**](#-objetivo-del-ataque)
- [🥊 **Resolución del ataque**](#-resolución-del-ataque)
  - [Reconocimiento (Reconnaissance)](#-reconocimiento-reconnaissance)
  - [Armado del ataque (Weaponization)](#-armado-del-ataque-weaponization)
  - [Entrega del ataque (Delivery)](#-entrega-del-ataque-delivery)
  - [Explotación de la vulnerabilidad (Exploitation)](#-explotación-de-la-vulnerabilidad-exploitation)
  - [Comando y Control (C2)](#-comando-y-control-c2)
  - [Acción sobre el objetivo (Actions on objetives)](#-acción-sobre-el-objetivo-acctions-on-objetives)
- [🔀 **Diagrama de Flujos del ataque**](#-flujos-del-ataque)
- [🧠 **Conclusiones**](#-conclusiones)


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

Técnicas ATT&CK asociadas:

- [T1592 – Gather Victim Host Information](https://attack.mitre.org/techniques/T1592/)
- [T1590 – Gather Victim Network Information](https://attack.mitre.org/techniques/T1590/)
- [T1589 – Gather Victim Identity Information](https://attack.mitre.org/techniques/T1589/)

Acciones realizadas:

- Revisión de documentación técnica publicada en GitHub, foros y sitios institucionales relacionados con el proyecto.
- Uso de Google Dorks para localizar configuraciones expuestas, credenciales o endpoints vulnerables.
- Búsqueda en motores como Shodan y Censys para identificar nodos conectados públicamente, como sensores o gateways LoRa.

🌐 **Escaneo de red y servicios**

Técnicas ATT&CK asociadas:

- [T1595 – Active Scanning](https://attack.mitre.org/techniques/T1595/)
- [T1595.003 – Service Scanning](https://attack.mitre.org/techniques/T1595/003/)

Acciones realizadas:

- Escaneo pasivo del tráfico de red con Wireshark para analizar protocolos como MQTT y LoRaWAN, identificando patrones de autenticación o mensajes sin cifrar.
- Uso de herramientas como Nmap y Masscan para detectar dispositivos IoT conectados, puertos abiertos y servicios activos.
- Aplicación de Amap para determinar versiones específicas de servicios expuestos, como brokers MQTT, paneles web o servicios API.

📡 **Identificación de dispositivos IoT**

Técnica ATT&CK asociada:

- [T1596 – Search Open Websites/Domains for IoT Devices](https://attack.mitre.org/techniques/T1596/)

Acciones realizadas:

- Captura de tráfico LoRaWAN y MQTT mediante sniffing, analizando los paquetes enviados por los nodos ESP32.
- Identificación de dispositivos WiFi o BLE con herramientas como Kismet y Aircrack-ng.
- Análisis espectral con Software Defined Radio (SDR) para estudiar las señales LoRa en tiempo real y mapear la cobertura.

🔍 **Análisis de APIs y servicios web**

Técnica ATT&CK asociada:

- [T1592.002 – Software](https://attack.mitre.org/techniques/T1592/002/)

Acciones realizadas:

- Enumeración de endpoints de la API REST del backend para descubrir funcionalidades expuestas sin autenticación o validación adecuada.
- Verificación de configuración del broker MQTT para determinar si permite conexiones anónimas o sin TLS.
- Evaluación de la plataforma web mediante Burp Suite y Postman, probando inyecciones, autenticaciones débiles y fugas de información.

### **2. Armado del ataque (Wqeaponization)**

Luego de haber recolectado suficiente información sobre la infraestructura y los servicios involucrados en el sistema de monitoreo de calidad del aire, el atacante comenzará a preparar las herramientas y cargas maliciosas necesarias para comprometer el entorno. En esta etapa, se enfoca en explotar debilidades específicas detectadas en la comunicación entre dispositivos IoT y el servidor central.

⚙️ Técnicas utilizadas

- [CWE-311 – Lack of Encryption in Data Transmission](https://cwe.mitre.org/data/definitions/311.html)
  Se observa que la comunicación entre los nodos IoT y el servidor mediante el protocolo MQTT no utiliza cifrado TLS, lo que permite interceptar, leer o manipular los datos transmitidos.
- [CWE-345 – Insufficient Verification of Data Authenticity](https://cwe.mitre.org/data/definitions/345.html)
  El servidor no verifica la autenticidad de los datos que recibe desde los sensores, permitiendo la inyección de información falsa sin mecanismos robustos de validación.
- [T1587.001 – Develop Capabilities: Malware](https://attack.mitre.org/techniques/T1587/001/)
  El atacante diseña una herramienta personalizada que simula el comportamiento de un nodo legítimo, capaz de enviar datos ambientales manipulados al sistema objetivo.

🔧 Acciones realizadas

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

⚙️ Técnicas utilizadas

- [T1565.002 – Data Manipulation: Network Traffic Manipulation](https://attack.mitre.org/techniques/T1565/002/)

  Se manipula el flujo de datos transmitido a través del protocolo MQTT, alterando su contenido en tránsito o publicando directamente información fabricada.
- [T1602 – Data Manipulation](https://attack.mitre.org/techniques/T1602/)

  Se inyectan datos falsos con apariencia válida, con el fin de engañar al sistema de monitoreo y alterar su percepción de las condiciones ambientales.

🔧 Acciones realizadas

El atacante configura un entorno desde el cual se peuda emular un nodo IoT se conecta al mismo brocker MQTT utilizado por el sistema. Aprovechando la falta de autentificación estricta en el brocker y a la carencia de cifrado en la transmisión, logra establecer conexión sin restricciones aparentes.

El nodo malicioso comienza a publicar datos que simulan condiciones normales o controladas del aire, evitando generar alertas. Estos datos falsificados son entregados en los mismo tópicos MQTT que utilizan los sensores legítimos, logrando así infiltrarse en el sistema sin ser detectado.

Reforzar la manipulación, requerirá de implementar un mecanismo de inyección intermitente de ruido ambiental, valores ligeramente alterados que simulan fluctuaciones normales en la calidad del aire. Esta técnica reduce la capacidad de los administradores para distinguir entre lecturas reales y maliciosas, disminuyendo la efectividad de los algoritmos de alerta.

📡 Escenario de entrega

El ataque puede desplegarse desde distintos entornos según el objetivo:

- Una red Wi-Fi local, si el atacante se encuentra físicamente cerca del sistema.
- Una instancia en la nube, configurada como cliente MQTT remoto que publica periódicamente los datos falsos.
- Un dispositivo de campo con conectividad LoRa o Wi-Fi, disfrazado como nodo adicional en la red.

Esta fase de entrega es crítica, ya que define la forma en que el sistema será alimentado con datos alterados sin necesidad de comprometer directamente los sensores físicos ni los servidores. El uso de canales abiertos y la falta de verificación de integridad permiten al atacante integrarse en el flujo de datos de manera transparente.

### **4. Explotación de la vulnerabilidad (Exploitation)**






### **5. Comando y Control (C2)**

### **6. Acción sobre el objetivo (Actions on objetives)**

## 🔀 **Flujos del ataque**

## 🧠 **Conclusiones**

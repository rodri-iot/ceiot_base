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

### **Reconocimiento (Reconnaissance)**

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

### **Armado del ataque (Wqeaponization**

### **Entrega del ataque (Delivery)**

### **Explotación de la vulnerabilidad (Exploitation)**

### **Comando y Control (C2)**

### **Acción sobre el objetivo (Actions on objetives)**

## 🔀 **Flujos del ataque**

## 🧠 **Conclusiones**

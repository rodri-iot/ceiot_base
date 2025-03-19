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

Previo a comenzar se propone hacer una investigación acerca del proyecto respecto a los siguientes aspectos:
- Identificación de la infraestructura y tecnologías utilizadas (LoRaWAN, MQTT, WiFi, servidor propio, ESP32 y sensores)
- Recopilación de información pública (OSINT) sobre el sistema y sus vulnerabilidades.
- Escaneo de dispositivos ioT conectados y análisis de redes.

#### Técnicas utilizadas:
- **T1598 - Phishing for Information** https://attack.mitre.org/techniques/T1598/
  - Revisión de documentación pública: Buscar información en GitHub, foros, páginas de empresas y redes sociales sobre el sistema objetivo.
  - Google Dorks: Utilizar búsquedas avanzadas para encontrar credenciales expuestas o configuraciones erróneas.
  - Shodan y Censys: Identificar dispositivos IoT expuestos en la red.
- **T1595 - Escaneo de red y servicios** https://attack.mitre.org/techniques/T1595/
  - Escaneo pasivo con Wireshark: Monitorear el tráfico LoRaWAN/MQTT en busca de credenciales o patrones de comunicación.
  - Nmap y Masscan: Descubrir dispositivos IoT, puertos abiertos y servicios disponibles en la red.
  - Amap: Identificar versiones de servicios activos.
- **T1596 - Identificación de dispositivos IoT en la red** https://attack.mitre.org/techniques/T1596/
  - Sniffing de paquetes LoRaWAN/MQTT: Capturar paquetes de comunicación para entender el tráfico.
  - Bluetooth/Wi-Fi sniffing con Kismet o Aircrack-ng: Identificar redes inalámbricas y dispositivos conectados.
  - Uso de SDR (Software Defined Radio): Analizar señales LoRaWAN para posibles ataques futuros. 
- **T1592 - Análisis de API y servicios web** https://attack.mitre.org/techniques/T1592/
  - Enumeración de endpoints REST: Descubrir puntos vulnerables en la API del servidor.
  - Pruebas en MQTT Broker: Verificar si permite conexiones anónimas o sin autenticación.
  - Burp Suite y Postman: Probar interacciones con la plataforma web/móvil del sistema.

### **Armado del ataque (Wqeaponization**

### **Entrega del ataque (Delivery)**

### **Explotación de la vulnerabilidad (Exploitation)**

### **Comando y Control (C2)**

### **Acción sobre el objetivo (Actions on objetives)**

## 🔀 **Flujos del ataque**

## 🧠 **Conclusiones**

# 🛡️ PoC: Análisis de Resiliencia y Agotamiento de Recursos (TCP SYN Flood)

## 📝 Resumen Ejecutivo
Este proyecto documenta una Prueba de Concepto (PoC) de un ataque de **Denegación de Servicio (DoS)** por inundación de paquetes SYN. La prueba se diseñó para evaluar la capacidad de un sistema operativo host para gestionar el "Handshake" de tres vías de TCP bajo condiciones de estrés extremo en una red local física.



## 🔬 Fundamentos Técnicos del Ataque
El ataque **SYN Flood** explota el funcionamiento del protocolo TCP (Capa 4 del modelo OSI). 

1. **El Proceso Estándar:** Normalmente, un cliente envía un paquete `SYN`, el servidor responde con `SYN-ACK` y el cliente finaliza con `ACK`.
2. **La Explotación:** Al utilizar el modo `--flood`, se transmiten millones de paquetes `SYN` ignorando deliberadamente las respuestas del servidor. 
3. **El Resultado:** El sistema objetivo mantiene estas conexiones "entreabiertas" en una estructura de memoria llamada **SYN Backlog**. Al saturarse esta tabla, el stack de red queda incapacitado para procesar solicitudes legítimas de usuarios reales.

---

## 🛠️ Especificaciones del Entorno de Laboratorio
* **Nodo Atacante:** Kali Linux (Arquitectura x64).
* **Nodo Víctima:** Estación de trabajo física (Laptop secundaria).
* **Red de Transporte:** Infraestructura WLAN (Wi-Fi) operando en la banda de 5GHz.
* **Software de Auditoría:** `hping3`.

---

## 🚀 Metodología de Ejecución

### Fase 1: Análisis de Latencia Base (Baseline)
Antes de la explotación, se estableció un punto de comparación para medir el impacto:
* **Conectividad:** 0% packet loss (109/109 paquetes ICMP recibidos).
* **Latencia Promedio (RTT):** 4.3 ms.

### Fase 2: Despliegue del Vector de Ataque
Se ejecutó la inundación utilizando una técnica de saturación de ancho de banda dirigida al puerto 80:

```bash
# Ejecución con privilegios de superusuario en la terminal de Kali
sudo hping3 -S --flood -V -p 80 192.168.1.7

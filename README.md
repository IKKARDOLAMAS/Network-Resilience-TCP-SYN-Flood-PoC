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


###Análisis de Flags:

-S (SYN): Activa el flag de sincronización para iniciar conexiones.

--flood: Envía paquetes a la máxima capacidad de la tarjeta de red (NIC) sin esperar respuesta.

-p 80: Target asignado al puerto estándar de servicios web.

Fase 3: Telemetría de Resultados
Total de paquetes enviados: 21,656,336.

Tiempo de ejecución: ~108 segundos.

Estado final: 100% packet loss (Confirmación de saturación del flujo de salida del atacante).

Impacto observado: Degradación crítica de los servicios de red en el host víctima y agotamiento de recursos en la pila TCP/IP del kernel.

🛡️ Estrategias de Remediación (Hardening)
Como parte del análisis de seguridad, se proponen las siguientes medidas de mitigación para fortalecer la infraestructura:

Activación de SYN Cookies: Técnica que permite al servidor manejar el backlog de conexiones sin asignar recursos de memoria RAM hasta que se complete el handshake.

Configuración de Firewalls (Rate Limiting): Implementar límites de velocidad de paquetes por segundo (pps) desde una misma dirección IP de origen.

Filtrado de Perímetro: Uso de sistemas IDS/IPS para detectar y descartar (drop) automáticamente paquetes SYN que no finalizan el proceso de conexión.

Nota de Responsabilidad: Este repositorio tiene fines estrictamente académicos y de seguridad ética. La ejecución de estas técnicas en infraestructuras ajenas sin autorización explícita es ilegal y está penada por las leyes de delitos informáticos internacionales.

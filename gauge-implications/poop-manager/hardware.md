---
layout: page
title: "Detalles de Hardware - Poop Manager"
permalink: /gauge-implications/poop-manager/hardware/
mermaid: true
---

# Contenido
1. [Descripción General](#1-descripción-general)
2. [Componentes Principales](#2-componentes-principales)
3. [Diagrama de Conexiones](#3-diagrama-de-conexiones)
4. [Explicación del Código](#4-explicación-del-código)
5. [Variables Clave](#5-variables-clave)
6. [Lógica General del Sistema](#6-lógica-general-del-sistema)
7. [Next Steps](#7-next-steps)

---


## 1. Descripción General

El sistema electrónico de **Poop Manager** tiene como objetivo principal detectar, registrar y monitorear la entrada y salida de la mascota al baño. La lógica del sistema se encuentra implementada en una placa **ESP32**, la cual se comunica con una API web para almacenar la información de cada visita.

El sistema utiliza un **sensor de ultrasonido** para detectar la presencia de la mascota, un **LED de presencia** para indicar si la mascota está en el baño y un **LED de limpieza** que se activa cuando se alcanza un umbral de visitas, indicando la necesidad de realizar una limpieza.


```mermaid
flowchart TD
    %% IoT System
    subgraph IOT ["Sistema IoT"]
        power["Batería 5V"] --> esp32["ESP32"]
        sensor["Sensor de Ultrasonido"] -->|Mide distancia cada 5 segundos| esp32
        esp32 -->|Envia datos en formato JSON| api["API"]
    end

    %% API Endpoint
    api -->|POST @/new-poop| handler["Poop Handler"]

    %% Backend System
    subgraph Backend ["Sistema Backend"]
        handler -->|Valida datos y registra evento| db["Base de Datos"]
        handler -->|Calcula métricas y genera alertas| metrics["Módulo de Métricas"]
    end

    %% Relación de Respuesta
    handler -->|Confirma registro exitoso| api
    api -->|Respuesta HTTP 200| esp32
````

---

## 2. Componentes Principales

| **Componente**         | **Descripción**                                           |
|----------------------|----------------------------------------------------------|
| **ESP32**             | Microcontrolador principal que gestiona la lógica y la conexión Wi-Fi. |
| **Sensor de Ultrasonido** | Detecta la distancia entre la pared y la mascota para determinar su presencia. |
| **LED de Presencia**    | Indica si la mascota está actualmente en el baño.       |
| **LED de Limpieza**     | Se enciende cuando se alcanza el umbral de 7 visitas, sugiriendo una limpieza. |
| **Módulo Wi-Fi**       | Integrado en el ESP32, permite la comunicación con el servidor. |
| **Batería 5V**         | Proporciona energía a todo el sistema.                   |

---

## 3. Diagrama de Conexiones

| **Componente**     | **Pin en ESP32** | **Función**                             |
|-------------------|-----------------|-----------------------------------------|
| **TRIG del Sensor**| 17              | Activa la señal ultrasónica para la medición. |
| **ECHO del Sensor**| 16              | Recibe el eco de la señal ultrasónica.   |
| **LED de Presencia**| 2              | Indica si la mascota está en el baño.    |
| **LED de Limpieza**| 26             | Indica la necesidad de limpieza del baño.|

---

## 4. Explicación del Código

### 4.1. Configuración Inicial

```c++
void setup() {
  pinMode(TRIG_PIN, OUTPUT);
  pinMode(ECHO_PIN, INPUT);
  pinMode(LED_PIN, OUTPUT);
  pinMode(CLEAN_LED_PIN, OUTPUT);
  Serial.begin(115200);

  WiFi.begin(ssid, pass);
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println("\nWiFi connected");

  timeClient.begin();
}
```

1.	Configura los pines del sensor, los LEDs y la comunicación serial.
2.	Conecta el ESP32 a la red Wi-Fi.
3.	Inicia el cliente NTP para sincronizar la hora con la zona horaria de Bogotá (GMT -5).


### 4.2. Lógica de la Detección de Presencia
```c++
  digitalWrite(TRIG_PIN, LOW);
  delayMicroseconds(2);
  digitalWrite(TRIG_PIN, HIGH);
  delayMicroseconds(10);
  digitalWrite(TRIG_PIN, LOW);
  
  long duration = pulseIn(ECHO_PIN, HIGH
```

1.	Activación del Sensor: El sensor de ultrasonido envía una señal de 10 microsegundos a través del pin TRIG.
2.	Medición de Distancia: La señal se recibe por el pin ECHO y la duración de la señal de retorno se convierte a distancia.
3.	Cálculo de la Distancia: La distancia se calcula con la fórmula (duration * 0.034 / 2), donde 0.034 representa la velocidad del sonido en cm/μs.

### 4.3. Detección de la Presencia de la Mascota

```c++
if (distance > 0 && distance < 24) {
  chaplinIsPresent = true;
} else {
  chaplinIsPresent = false;
}
```
1.	Si la distancia medida está entre 0 y 24 cm, se asume que la mascota está en el baño.
2.	La variable chaplinIsPresent se actualiza con true o false según la presencia detectada

### 4.4. Control de Presencia de la Mascota

```c++
if (chaplinIsPresent && !chaplinWasPresent) {
  // La mascota acaba de entrar al baño
  ledActivations++;
  activationsUntilCleaning++;
  digitalWrite(LED_PIN, HIGH);
} else if (!chaplinIsPresent && chaplinWasPresent) {
  // La mascota acaba de salir del baño
  digitalWrite(LED_PIN, LOW);
}
```
1.	Detecta el cambio de estado de presencia.
2.	Si la mascota acaba de entrar, se activa el LED de presencia, se incrementa el contador de visitas y se registra la visita.
3.	Si la mascota sale, se apaga el LED de presencia.

### 4.5. Envío de Datos al Backend

```c++
if (WiFi.status() == WL_CONNECTED) {
  HTTPClient http;
  http.begin("https://{baseurl}/poop/new-poop");
  http.addHeader("Content-Type", "application/json");
  String httpRequestData = "{\"pet_id\": 1}";
  int httpResponseCode = http.POST(httpRequestData);
}
```
1.  Si hay conexión Wi-Fi, se realiza una solicitud HTTP POST al servidor.
2.	La solicitud envía el identificador de la mascota (pet_id) para registrar la visita.
3.	Se registra el código de respuesta del servidor para garantizar la correcta transmisión.

### 4.6. Control del LED de Limpieza

```c++
if (activationsUntilCleaning >= 7) {
  digitalWrite(CLEAN_LED_PIN, HIGH);
} else {
  digitalWrite(CLEAN_LED_PIN, LOW);
}
```
1.	Cuando la mascota ha ingresado al baño 7 veces, se enciende el LED de limpieza.
2.	Este LED indica que se recomienda realizar la limpieza del baño.

---

## 5. Variables Clave

| Variable               | Tipo           | Función                                                              |
|------------------------|----------------|----------------------------------------------------------------------|
| `ledActivations`       | `int`          | Número de visitas de la mascota registradas en el día.               |
| `activationsUntilCleaning` | `int`      | Contador que indica cuántas visitas faltan para recomendar la limpieza. |
| `chaplinIsPresent`     | `bool`         | Indica si la mascota está en el baño.                                |
| `chaplinWasPresent`    | `bool`         | Indica si la mascota estaba previamente en el baño.                  |
| `previousMillis`       | `unsigned long`| Marca de tiempo para realizar mediciones cada 5 segundos.            |

---

## 6. Lógica General del Sistema
1. Detección de Presencia: El sensor ultrasónico mide la distancia cada 5 segundos.
2.	Detección de Cambios de Estado: Detecta si la mascota entró, salió o sigue presente en el baño.
3.	Registro de Visitas: Cada vez que la mascota entra, se enciende el LED de presencia, se registra una visita y se envía una solicitud HTTP al servidor.
4.	Control de la Limpieza: Cada 7 visitas, se enciende el LED de limpieza, indicando que es hora de limpiar el baño.
5.	Reseteo Diario: A las 23:59, se reinicia el conteo diario de visitas y la cantidad de visitas necesarias para la limpieza.

## 7. Next Steps
•	Gestionar errores de conexión Wi-Fi: Implementar una reconexión automática en caso de pérdida de señal.
•	Ahorro de Energía: Implementar una lógica de “modo de bajo consumo” para reducir el uso de la batería.
•	Parámetros Configurables: Permitir configurar la distancia umbral, el intervalo de limpieza y los parámetros de la API desde una interfaz web o aplicación móvil.
# FreeRTOS Setup

To learn FreeRTOS and its implementation, we will use an ESP32 board with the Arduino IDE. The Arduino IDE was chosen for its simplicity, so that we can focus more on the concepts used in FreeRTOS without getting too caught up on other aspects of programming.

Check out the following links for more help on navigating FreeRTOS:
- [Mastering the FreeRTOS Real Time Kernel](https://freertos.org/fr-content-src/uploads/2018/07/161204_Mastering_the_FreeRTOS_Real_Time_Kernel-A_Hands-On_Tutorial_Guide.pdf)
- [FreeRTOS Reference Manual](https://freertos.org/fr-content-src/uploads/2018/07/FreeRTOS_Reference_Manual_V10.0.0.pdf)

## Setup
Now, let's set up FreeRTOS to work with ESP32 and Arduino IDE:
1. Go to [freertos.org](freertos.org) and download FreeRTOS.  
2. Extract the .zip file and head into the FreeRTOS directory.  

    - The `FreeRTOS` library is just a scheduler (use this if you have your own device and file driver)
    - The `FreeRTOS-Plus` library is the scheduler and a few drivers, mostly for networking (e.g. TCP/UDP)
    - You will want to copy or link to the source files in your build system.
    
3. Install the [Arduino IDE](https://www.arduino.cc/en/software).
4. In Arduino IDE, go to `File` -> `Preferences`, and click the URL list button at the bottom right. Here, add the following on a new line:
    ```
    https://dl.espressif.com/dl/package_esp32_index.json
    ```
    and click OK.
5. Go to `Tools` -> `Board` -> `Boards Manager`, search for esp32, look for the most recent Espressif Systems package, and install. Click close when finished.

## FreeRTOS Demo

Let's write a blinking LED program.
```c
// Use only core 1 for demo purposes
#if CONFIG_FREERTOS_UNICORE
static const BaseType_t app_cpu = 0;
#else
static const Basetype_t app_cpu = 1;
#endif

// Pins
static const int led_pin = LED_BUILTIN;

// Our task: blink an LED
// In FreeRTOS, a task function should return void and have one void pointer parameter.
void toggleLED(void *parameter) {     
  while(1) {
    digitalWrite(led_pin, HIGH);
    vTaskDelay(500 / portTICK_PERIOD_MS);
    digitalWrite(led_pin, LOW);
    vTaskDelay(500 / portTICK_PERIOD_MS);
  }
}

void setup() {
  // put your setup code here, to run once:
  
}

void loop() {
  // put your main code here, to run repeatedly:
  
}
```

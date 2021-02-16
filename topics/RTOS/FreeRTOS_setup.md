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
    vTaskDelay(500 / portTICK_PERIOD_MS);   // reason for using vTaskDelay instead of Arduino's delay function written below
    digitalWrite(led_pin, LOW);
    vTaskDelay(500 / portTICK_PERIOD_MS);
  }
}

void setup() {
  // Configure pin
  pinMode(led_pin, OUTPUT);
  
  //Task to run forever
  xTaskCreatePinnedToCore(      // Use xTaskCreate() in vanilla FreeRTOS
                toggleLED,      // Function to be called
                "Toggle LED",   // Name of task
                1024,           // Stack size (bytes in ESP32, words in FreeRTOS)
                NULL,           // Parameter to pass to function
                1,              // Task priority (0 to configMAX_PRIORITIES - 1)
                NULL,           // Task handle
                app_cpu);       // Run on one core for demo purposes (ESP32 only)
    // If this was vanilla FreeRTOS, you'd want to call vTaskStartScheduler() in main after setting up your tasks.
}

void loop() {
  
}
```
### vTaskDelay
The reason we use `vTaskDelay` is because it is non-blocking and works in vanilla FreeRTOS. It tells the scheduler to run other tasks until the specified delay timer is up and then come back to continue running this task. Almost all RTOSes are based on a tick timer. A tick timer is one of the MCU's hardware timers allocated to interrupt the processor at a specific interval. The interrupt period is known as a tick and the scheduler has an opportunity to run each tick to figure out which task needs to run for that tick. By default, FreeRTOS sets the tick period to 1 ms and defines `portTICK_PERIOD_MS` to 1. `vTaskDelay` expects the number of ticks to delay, not the number of milliseconds. So we need to divide our desired milliseconds by the tick period for the argument.
### xTaskCreatePinnedToCore
`xTaskCreatePinnedToCore` lets the scheduler know that we want to run the task in only one of the cores. This function does not exist in vanilla FreeRTOS (you would use `xTaskCreate` instead). We can use `xTaskCreate` here, but it would tell the scheduler to run the task on any core it wants. In the Arduino framework for ESP32, the setup and loop functions exist inside their own task separate from the main program entry point. Because of this, a new task is spawned as soon as we call `xTaskCreatePinnedToCore`. In vanilla FreeRTOS, we would need to call `vTaskStartScheduler()` in main after setting up your tasks, but with `xTaskCreatePinnedToCore`, it has already been done.

Now let's program the board:
1. Go to `Tools` -> `Board` -> `ESP32 Arduino` -> select your ESP32 board.  
2. Click `Upload`.  
3. You should see the LED blinking once per second.

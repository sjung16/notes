# Basic UART Implementation

Top design and pin settings:

- TopDesign -> Add UART, rename to 'UART', click ok  
- Pins -> Assign RX to P5[0] and TX to P5[1]

How do we make 'printf' work on PSoC 6? 

- We must retarget standard out to UART interface. To do this, we must use the 'Retarget I/O' library.
- Go to `Build settings` -> `PDL` -> `Utilities` -> check `Retarget I/O`. This will create `stdio_user.h` in the Shared Files folder.
- Open `stdio_user.h`:

  - `#include "project.h"` so we can reference the appropriate UART below.
  - for `#define IO_STDOUT_UART` and `#define IO_STDIN_UART`, change value from `SCB0` to `UART_HW`.

Now that stdio is set up, we will control the UART with Cortex-M4.  
Open `main_cm4.c` and add the following:
```
#include <stdio.h>
UART_Start();   // add this to the init section
```
Stdin is usually buffered, but we don't know that it is buffered until we read.   
We want to turn that off for this program so that we can control each character.  
To do this, add:
```
setvbuf(stdin, NULL, _IONBF, 0);
```
Now let's print some characters. Under Init, add:
```
char c;
printf("Started UART\r\n");
```
Under loop, add:
```
c = getchar();
if(c)
{
  printf("%c", c);
}
```
Build and program.

Go to Device Manager and find out which COM port KitProg2 USB-UART is using.  
Go to PuTTy, set COM port, set 115200 baudrate and start.

Whatever you input will be printed back out.

# Background
The following is based on [Digi-Key's Introduction to RTOS](https://www.youtube.com/watch?v=F321087yYy4) tutorial series.

## General Purpose Operating System (GPOS)
- Schedules background tasks and user applications
- Manages virtual resources (files, libraries, folders), allowing applications and processes to access them when needed
- Manages or provides device drivers that allow the system to do tasks such as:
  - read/write from an external disk
  - respond to keyboard/mouse inputs
  - draw graphics on your monitor
- Human interaction is the most important feature, so the scheduler is designed to prioritize such tasks
  - missing timing deadlines may be acceptable, especially if unnoticeable by a human
- Scheduler is non-deterministic

## Real-Time Operating System (RTOS)
- Usually used in microcontroller applications
- Scheduler can:
  - guarantee meeting timing deadlines on tasks
  - run multiple tasks concurrently (not true multitasking in single-core)
  - give higher priority to some tasks
- Interrupts work in RTOS if they have higher priority than any other tasks

## Task vs. Thread vs. Process
- `Task` : set of program instructions loaded in memory (any piece of work we want to get done in code)  
- `Thread` : unit of CPU utilization with its own program counter and stack  
- `Process` : instance of a computer program that's being executed  
- Usually, a **process** will have one or more **threads** used to accomplish **tasks**
- **Threads** within a **process** will often share resources such as heap memory and can pass resources to each other
- An RTOS is usually only able to handle one process
- In FreeRTOS, the term **task** is used interchangeably with the term **thread**

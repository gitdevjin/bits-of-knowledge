# Computer-science

### How Operating System Works on a hardware level when there is an interrupt such as keyboard or mouse input

Interrupt Handling by the OS:

When the CPU receives an interrupt signal from an input device, it suspends its current execution and jumps to a predefined memory location known as the interrupt vector table.
The interrupt vector table contains addresses pointing to specific interrupt service routines (ISRs) for different types of interrupts.
The CPU fetches the address corresponding to the received interrupt and begins executing the instructions stored at that memory location.

Executing Instructions:

The instructions stored at the memory location pointed to by the interrupt vector table comprise the ISR associated with the interrupt.
These instructions are part of the operating system code stored in memory.
The CPU fetches each instruction sequentially from memory and executes them.
The instructions in the ISR are responsible for handling the input signal. For example, if the interrupt indicates keyboard input, the ISR might read the key code and store it in a buffer.

The CPU executes these instructions one by one, updating system state, queuing input events, or waking up processes waiting for input, as specified by the ISR.
Once the ISR completes execution, the CPU returns to the interrupted task, either resuming its previous execution or continuing with the next task in the queue.
In this way, the CPU reads and executes the instructions stored in memory as part of the interrupt service routine (ISR) of the operating system. These instructions are responsible for handling various system events, including input signals from devices, and managing system resources accordingly.

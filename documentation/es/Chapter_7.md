# 7. Selection of the Operating System.

The criteria for choosing an operating system (OS) in this case are similar to those criteria of a sensor network.

Our network device must have certain special features, [4,5,6] including:
- Low cost.
- Low energy consumption.
- It can run on battery. To achieve the objective, an efficient network protocol such as 6LowPAN should be used to reduce transmission time and save energy.

<br>

## 7.1 Modularidad.

El dispositivo **Locha Mesh** requerirá un sistema operativo modular que separa el núcleo del middleware, protocolos y aplicaciones que facilitan el desarrollo y el mantenimiento.

Using a modular _RTOS_ simplifies our work, especially if we are developing a family of devices with different capabilities.

Relying on a common core allows all the family of devices to share a common code base.

Different to a monolithic OS that brings together a complete set of software, a modular _RTOS_ allows us to customize the integrated software for our device, requires less RAM and less FLASH memory, as well as reducing costs.

<br>

## 7.2 Connectivity.

Connectivity is essential for sensor and IoT networks.

Talking about wireless sensor networks, devices are expected to connect with each other to communicate with public and private networks.

In the case of **Locha Mesh**:

- Our chosen _RTOS_ must stand standard communications and protocols like IEEE 802.15.4, 6LoWPAN and IPV6.

- An RTOS will allow us to adopt the network stack we need, saving memory on the device and reducing our costs. It can also help us use existing devices and add new functions without major changes to the software.

- The OS for this network must take into account all hardware limitations keeping high availability.

- It is desirable to find standard interfaces such as POSIX, to facilitate the portability of applications.

- Ease of use must be a crucial factor for all developers.

<br>

## 7.3 Contiki-ng and TinyOS.

- Contiki-ng and TinyOS are not real-time operating systems, but they are the benchmark in wireless sensor networks.

- The Contiki-ng OS has a layered architecture while TinyOS is built on a monolithic core. Like Linux, Contiki-ng is event driven and it is similar to TinyOS. They both use a _FIFO_ strategy. On the other hand, Linux uses a scheduler, which guarantees a execution scheduled time for tasks.

- The Contiki-ng and TinyOS programming model is defined by events such that all tasks are executed in the same context.

- They offer partial support for multi-thread.

- Contiki-ng uses a language similar to C, but it can´t use certain keywords.

- TinyOS is written in a language called NesC, which is similar but not compatible with C.

It should be noted that one of the most important parameters when selecting the OS is the amount of documentation, examples and activity in the repository of constant updates.



## 7.4 RIOT-OS.

_RIOT-OS_ (real-time operating system for IoT) fills the gap between traditional and wireless sensor network operating systems. Furthermore, this operating system is designed to:

- Take care of device energy efficiency..

- Take up as little memory space as possible.

- Tiene una API única independiente de hardware.

_RIOT-OS_ is based on a microkernel architecture that allows multi-thread using a standard API. Empaquetado con características heredadas de FireKernel, RIOT-OS también proporciona soporte para C ++, que permite el uso de poderosas bibliotecas, incluyendo soporte para la pila TCP/IP. This modular approach makes RIOT-OS robust against individual component failures, providing high reliability and a developer-friendly API.

_RIOT-OS_ permite a los programadores crear tantos hilos como sea necesario. La única restricción es la cantidad de memoria disponible para cada hilo.

Thanks to the Kernel message API and using these threads, it is possible to implement distributed systems in a simple way.

To accomplish real-time requirements, _RIOT-OS_ enforces constant periods for core tasks (for example: scheduler execution, inter communication process (IPC), timer operations, etc.).

Each _RTOS_ is very good in a particular domain, but considering our needs, we have chosen _RIOT-OS_.

In terms of MCU hardware and memory capabilities, _RIOT-OS_ primarily competes with Linux. In comparison RIOT can be reduced to orders with less memory requirements and it is built with built-in support for energy efficiency and real-time capabilities.

On the other hand, _RIOT-OS_ also competes with Contiki-ng, TinyOS and FreeRTOS. Compared to Contiki-ng and TinyOS, _RIOT-OS_ offers real-time and multiple sub-processes. Unlike FreeRTOS, _RIOT-OS_ provides native energy efficiency and a full-featured OS, including a free, updated, open source interoperable network.

_RIOT-OS_ also offers standard POSIX APIs and the ability to code in standard programming languages like (C and C ++) using standard debugging tools, reducing the developer learning curve and the process of cycle of life of the  software development.

<br>


## 7.5 Tabla comparativa de sistemas operativos.

<div>
<table id="tblOne" style="width:100%;">
 <tr align="center">
    <th>OS</th>
    <th>min RAM</th>
    <th>min ROM</th>
    <th>C support</th>
    <th>C++ support</th>
    <th>Multi-threading</th>
    <th>MCU w/o MMU</th>
    <th>Modularity</th>
    <th>Real-time</th>
 </tr>
  <tr align="center">
    <td>TinyOS</td>
    <td>< 1KB</td>
    <td>< 4KB</td>
    <td> &#10008 </td>
    <td> &#10008 </td>
    <td>&#10061</td>
    <td>&#10004</td>
    <td>&#10008</td>
    <td>&#10008</td>
 </tr>
 <tr align="center">
    <td>Contiki-ng</td>
    <td>< 2KB</td>
    <td>< 30KB</td>
    <td> &#10061 </td>
    <td> &#10008 </td>
    <td>&#10061</td>
    <td>&#10004</td>
    <td>&#10061</td>
    <td>&#10061</td>
 </tr>
 <tr align="center">
    <td>RIOT-OS</td>
    <td>~ 1.5KB</td>
    <td>~ 5KB</td>
    <td> &#10004 </td>
    <td> &#10004 </td>
    <td>&#10004</td>
    <td>&#10004</td>
    <td>&#10004</td>
    <td>&#10004</td>
 </tr>
 <tr align="center">
    <td>Linux</td>
    <td>~ 1MB</td>
    <td>~ 1MB</td>
    <td> &#10004 </td>
    <td> &#10004 </td>
    <td>&#10004</td>
    <td>&#10008</td>
    <td>&#10061</td>
    <td>&#10061</td>
 </tr>
</table>
</div>

<br>
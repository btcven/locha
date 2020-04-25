# 7. Selection of the Operating System.

The criteria for choosing an operating system _(OS)_ in this case are similar to those criteria of a sensor network.

Our network device must have certain special features, [4,5,6] including:
- Low cost.
- Low energy consumption.
- It can run on battery.

To achieve the objective, an efficient network protocol such as _6LowPAN_ should be used to reduce transmission time and save energy.


## 7.1 Modularity.

The **Locha Mesh** device will require a modular _OS_ that separates the kernel from the middleware, protocols and applications that allows ease of development and maintenance.

Using a modular _RTOS_ simplifies our work, especially if we are developing a family of devices with different capabilities.

Relying on a common core allows all the family of devices to share a common code base.

Different to a monolithic _OS_ that brings together a complete set of software, a modular _RTOS_ allows us to customize the integrated software for our device, requires less _RAM_ and less _FLASH_ memory, as well as reducing costs.

## 7.2 Connectivity.

Connectivity is essential for sensor and _IoT_ networks.

Talking about wireless sensor networks, devices are expected to connect with each other to communicate with public and private networks.

In the case of **Locha Mesh**:

- Our chosen _RTOS_ must stand standard communications and protocols like _IEEE 802.15.4_, _6LoWPAN_ and _IPV6_.
- An _RTOS_ will allow us to adopt the network stack we need, saving memory on the device and reducing our costs. It can also help us use existing devices and add new functions without major changes to the software.
- The _OS_ for this network must take into account all hardware limitations keeping high availability.
- It is desirable to find standard interfaces such as _POSIX_, to facilitate the portability of applications.
- Ease of use must be a crucial factor for all developers.

## 7.3 _Contiki-ng_ and _TinyOS_.

- _Contiki-ng_ and _TinyOS_ are not real-time operating systems, but they are the benchmark in wireless sensor networks.
- The _Contiki-ng OS_ has a layered architecture while _TinyOS_ is built on a monolithic core. Like _Linux_, _Contiki-ng_ is event driven and it is similar to _TinyOS_. They both use a _FIFO_ strategy. On the other hand, _Linux_ uses a scheduler, which guarantees a execution scheduled time for tasks.
- The _Contiki-ng_ and _TinyOS_ programming model is defined by events such that all tasks are executed in the same context.
- They offer partial support for multi-thread.
- _Contiki-ng_ uses a language similar to _C_, but it can´t use certain keywords.
- _TinyOS_ is written in a language called _NesC_, which is similar but not compatible with _C_.

It should be noted that one of the most important parameters when selecting the _OS_ is the amount of documentation, examples and activity in the repository of constant updates.

## 7.4 _RIOT-OS_.

_RIOT-OS_ (real-time operating system for _IoT_) fills the gap between traditional and wireless sensor network operating systems. Furthermore, this operating system is designed to:

- Take care of device energy efficiency..

- Take up as little memory space as possible.

- Have a unique, hardware independent _API_.

_RIOT-OS_ is based on a _microkernel_ architecture that allows multi-thread using a standard _API_. Packed with features inherited from _FireKernel_, _RIOT-OS_ also provides support for _C++_, which enables the use of powerful libraries, including support for the _TCP/IP_ stack. This modular approach makes _RIOT-OS_ robust against individual component failures, providing high reliability and a developer-friendly API.

_RIOT-OS_ allows programmers to create as many threads as necessary. The only restriction is the amount of memory available for each thread.

Thanks to the Kernel message _API_ and using these threads, it is possible to implement distributed systems in a simple way.

To accomplish real-time requirements, _RIOT-OS_ enforces constant periods for core tasks (for example: scheduler execution, inter communication process _(IPC)_, timer operations, etc.).

Each _RTOS_ is very good in a particular domain, but considering our needs, we have chosen _RIOT-OS_.

In terms of MCU hardware and memory capabilities, _RIOT-OS_ primarily competes with Linux. In comparison RIOT can be reduced to orders with less memory requirements and it is built with built-in support for energy efficiency and real-time capabilities.

On the other hand, _RIOT-OS_ also competes with _Contiki-ng_, _TinyOS_ and _FreeRTOS_. Compared to Contiki-ng and TinyOS, _RIOT-OS_ offers real-time and multiple sub-processes. Unlike FreeRTOS, _RIOT-OS_ provides native energy efficiency and a full-featured _OS_, including a free, updated, open source interoperable network.

_RIOT-OS_ also offers standard _POSIX_ _APIs_ and the ability to code in standard programming languages like _C_ and _C++_ using standard debugging tools, reducing the developer learning curve and the process of cycle of life of the  software development.

<br>


## 7.5 Comparative table of operating systems.


<div>
<table id="tblOne" style="width:100%;" >
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

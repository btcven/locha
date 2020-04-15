
# 7. Selección del sistema operativo

Los criterios para elegir sistema operativo (OS) en este caso son similares a los de una red de sensores.

Nuestro dispositivo de red debe tener ciertas caracteristicas especiales, [4,5,6]  entre las cuales se incluyen:
- Bajo coste.
- Bajo consumo.
- Que pueda funcionar con bateria.

Para cumplir el objetivo se debe emplear un protocolo de red eficiente como 6LowPAN para reducir el tiempo de transmision y ahorrar energia.


## 7.1 Modularidad

El dispositivo de Locha Mesh requerirá un OS modular que separe el kernel del middleware, protocolos y
aplicaciones, debido a la facilidad de desarrollo y el mantenimiento.

Usar un _RTOS_ modular simplifica nuestro trabajo, especialmente si estamos desarrollando una familia de dispositivos con diferentes capacidades. 

Confiar en un núcleo común permite que toda familia de dispositivos puedan compartir una base de código común.

A diferencia de un OS monolítico que agrupa un conunto completo de software, un _RTOS_ modular nos permite personalizar el software integrado para nuestro dispositivo, requiere menos RAM y menos memoria FLASH, además de reducir costes.


## 7.2 Conectividad

La conectividad es esencial para las redes de sensores e IoT.

Hablando de redes de sensores inalámbricos, se espera que los dispositivos se conecten entre sí para comunicarse con redes publicas y privadas. 

En el caso de Locha:

- Nuestro _RTOS_ elegido debe soportar comunicaciones estandares y protocolos como IEEE 802.15.4, 6LoWPAN e IPV6.

- Un _RTOS_ nos permitirá adoptar el stack de red que necesitamos, ahorrando memoria en el dispositivo y reduciendo nuestros costes. Además puede ayudarnos a usar  dispositivos existentes y añadir nuevas funciones sin grandes cambios en el software.

- El OS para esta red debe tener en cuenta todos las limitaciones del hardware, manteniendo una alta disponibilidad.

- Es deseable encontrar interfaces estándar como es POSIX, para facilitar la portabilidad de aplicaciones.

- La facilidad de uso debe ser un factor crucial para todo desarrollador

## 7.3 Contiki-ng y TinyOS

- Contiki-ng y TinyOS no son sistemas operativos en tiempo real, pero son la referencia en redes de sensores inalámbricos.

- El OS Contiki-ng tiene una arquitectura en capas mientras que TinyOS esta contruido sobre un nucleo monolítico. Al igual que Linux, Contiki-ng es impulsado por eventos y es similar a TinyOS. Ambos usan una estrategia _FIFO_.
Por otro lado Linux usa un planificador, que garantiza un horario de ejecucion para las tareas.

- El modelo de programación de Contiki-ng y TinyOS esta definido por eventos de tal manera que todas las tareas son ejecutadas en el mismo contexto.

- Ofrecen soporte parcial para el multi-thread.

- Contiki-ng usa un lenguaje similar a C, pero no puede usar ciertas palabras clave.

- TinyOS está escrito en un lenguaje llamado NesC, el cual es similar pero no compatible con C.
  
Cabe destacar que unos de los parámetros mas importantes a la hora de seleccionar el OS, es la cantidad de documentacion, ejemplos y actividad en el repositorio de constantes actualizaciones.

## 7.4 RIOT-OS

RIOT-OS (OS en tiempo real para IoT) llena el vacío entre sistemas operativos de redes de sensores inalámbricos y tradicionales. Además, este sistema operativo está diseñado para: 
- Cuidar la eficiencia energética del dispositivo. 
- Para ocupar el menor espacio de memoria posible.
- Tener una API única, independiente del hardware.

RIOT-OS se basa en una arquitectura de microkernel que permite multi-hilo utilizando una API estándar. Repleto de características heredadas de FireKernel, RIOT-OS también proporciona soporte para C++, que permite el uso de bibliotecas potentes, incluye soporte para el stack de red TCP/IP. Este enfoque modular hace que RIOT-OS sea robusto contra fallos de  componentes individuales, proporcionando alta confiabilidad y una API amigable para los programadores.

RIOT-OS permite a los programadores crear tantos hilos como sean necesarios. La única restricción es la cantidad de memoria  disponible para cada hilo. 

Gracias a la API de mensajes del Kernel y usando estos hilos, es posible implementar sistemas distribuidos de una manera simple.

Para cumplir con los requisitos en tiempo real, RIOT-OS hace cumple períodos constantes para las tareas del núcleo (por ejemplo: ejecución del planificador, _inter comunication  process_ (IPC), operaciones de temporizador, etc).

Cada RTOS-OS es muy bueno en un dominio particular, pero teniendo en cuenta nuestras necesidades, nosotros hemos elegido RIOT-OS. 

En términos de hardware MCU y capacidades de memoria, RIOT-OS compite principalmente con Linux. En comparación RIOT puede reducirse a órdenes con menos requerimientos de memoria y está construido con soporte incorporado para 
eficiencia energética y capacidades en tiempo real. 

Por otro lado, RIOT-OS también compite
con Contiki-ng, TinyOS y FreeRTOS. En comparación con Contiki-ng y TinyOS, RIOT-OS ofrece capacidades en tiempo real y subprocesos múltiples. A diferencia de FreeRTOS, RIOT-OS proporciona eficiencia energética nativa y un OS con todas las funciones, incluyendo una red interoperable de código abierto, actualizada y gratuita.

RIOT también ofrece API estándar POSIX y la capacidad de codificar en lenguajes de programación estándar como (C y C ++) usando herramientas de depuración estándar, lo que reduce la curva de aprendizaje de los desarrolladores
y el proceso del ciclo de vida del desarrollo de software.


## 7.5 Tabla comparativa de los sistemas operativos.

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
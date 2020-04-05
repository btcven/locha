<br>
<h1 align="center">Capitulo 7</h1>
<br>

# 7. Selección del Sistema Operativo

En esta sección ilustramos algunos aspectos importantes a la hora de seleccionar el sistema operativo para trabajar con este tipo de tecnologías, debido a que los requisitos de software para redes de sensores puede diferir bastante, Aunque podrían compartir un Kernel en común y algunos servicios de bajo nivel.

Un dispositivo de red para funcionar en una red de sensores debe tener ciertas características,[4, 5, 6] entre las cuales se incluyen:
- Bajo costo.
- Bajo consumo
- Que pueda funcionar completamente con batería.

Para cumplir el objetivo se debe emplear un protocolo de red altamente eficiente como 6LowPAN para reducir el tiempo de transmisión y ahorrar energía.


## 7.1 Modularidad

El dispositivo IoT requerirá un sistema operativo modular que separe el kernel del middleware, protocolos y
aplicaciones. Las razones son la facilidad de desarrollo y el mantenimiento.

Usando un RTOS modular simplifica nuestro proceso de desarrollo, especialmente si estamos desarrollando una familia de dispositivos con diferentes capacidades. 

Confiar en un núcleo común permite que toda familia de dispositivos puedan compartir una base de código común.

A diferencia de un sistema operativo monolítico que agrupa un conjunto completo de software, un ```RTOS``` modular nos permite personalizar el software integrado para nuestro dispositivo, requiere menos RAM y menos memoria FLASH, ademas de que reduce los costos.


## 7.2 Conectividad

La conectividad de red es esencial para las redes de sensores e internet de las cosas.

Si estamos hablando de redes de sensores inalámbricos. 
la industria ahora espera que los dispositivos integrados se conecten entre sí, y para comunicarse con redes publicas y privadas. 

- Nuestro RTOS de elección debe soportar comunicaciones estándares y protocolos como IEEE 802.15.4, Wi-Fi y Bluetooth. 

- Nuestro dispositivo debe poder conectarse a redes IP usando protocolos de ancho de banda eficiente como 6LoWPAN. 

- Un RTOS nos permitirá seleccionar el protocolo específico, el stack de red que necesitemos, ahorrando memoria en el dispositivo y reduciendo nuestros costos Y puede ayudarnos a adaptar los dispositivos existentes con nuevos opciones de conectividad sin reescribir el núcleo de nuestro software embebido

- El sistema operativo para esta red debe tener en cuenta todos las limitaciones del hardware, manteniendo una alta usabilidad.
- Es deseable encontrar interfaces standard como lo son POSIX, para potabilidad de aplicaciones.
- La facilidad de uso debe ser un factor crucial para todo desarrollador

## 7.3 Contiki y Tiny OS

- Contiki y Tiny OS nos son sistemas operativos en tiempo real, pero podemos ver su presencia en el mercado es dominante, principalmente en redes de sensores inhalambrico; Por lo tanto se aplican como referencia, la misma razón podemos aplicar a Linux.

- El sistema operativo Contiki tiene una arquitectura en capas mientras que Tiny OS esta construido sobre un núcleo monolítico, igual que en Linux, Contiki es impulsado por eventos y es muy similar a Tiny OS, el cual usa una estrategia ```FIFO```, por otro lado Linux usa un planificador, que garantiza un horario de ejecución para las tareas.

- El modelo de programación de Contiki y Tiny OS esta definido por eventos de tal manera que todas las tareas son ejecutadas en el mismo contexto.

- Ofrecen soporte parcial para el multi-thread.
- Contiki usa un lenguaje similar a C, peor no puede usar ciertas palabras clave.
- TinyOS esta escrito en un lenguaje llamado NesC, el cual es similar pero no compatible con C.
  
Cabe destacar que unos de los parámetros mas importantes a la hora de seleccionar un Sistema Operativo para redes de sensores, es la cantidad de documentación, ejemplos y actividad en el repositorio, hablando acerca de constantes actualizaciones.

## 7.4 RIOT OS

RIOT (sistema operativo en tiempo real para IoT) llena el vacío entre sistemas operativos de redes de sensores inalámbricos y Sistemas operativos tradicionales. Además, este sistema operativo está diseñado para: 
- Cuidar la eficiencia energética del dispositivo. 
- Para ocupar el menor espacio de memoria posible.
- Tener un API única, independientemente del hardware subyacente.

RIOT se basa en una arquitectura de microkernel, que es heredado de FireKernel, lo que permite multi-hilo utilizando una API estándar. Repleto de características heredado de FireKernel, RIOT también proporciona soporte para C++, que permite el uso de bibliotecas potentes, incluye soporte para la pila de red TCP/IP; Este enfoque modular hace que RIOT sea robusto contra fallas de sus componentes individuales, proporcionando alta confiabilidad
con una API amigable para los programadores.

RIOT permite a los programadores crear tantos hilos como necesitan. La única restricción es la cantidad de memoria  disponible para cada hilo. 

Gracias a la API de mensajes del Kernel y usando estos hilos, es posible Implementar sistemas distribuidos de una manera simple.

Para cumplir con los requisitos en tiempo real, RIOT hace cumplir períodos constantes para las tareas del núcleo (por ejemplo, ejecución del planificador, inter comunication  process, operaciones de temporizador).


Como hemos visto, cada RTOS es muy bueno en un dominio particular, pero teniendo en cuenta nuestros requisitos, nosotros hemos elegido RIOT. 

En el extremo superior en términos de hardware MCU y capacidades de memoria, RIOT compite principalmente con Linux. En comparación con Linux, RIOT puede reducirse a órdenes con menos requerimientos de memoria y esta construido con soporte incorporado para 
Eficiencia energética y capacidades en tiempo real. 

En el extremo bajo en términos de hardware MCU / capacidades de memoria, RIOT compite
principalmente con Contiki, TinyOS y FreeRTOS.
En comparación con Contiki y TinyOS, RIOT ofrece capacidades en tiempo real y sub procesos múltiples. 

En contraste con FreeRTOS, RIOT proporciona eficiencia energética nativa y un sistema operativo con todas las funciones incluyendo red interoperable de código abierto, actualizada y gratuita.

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
    <td>Contiki</td>
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
    <td>RIOT OS</td>
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
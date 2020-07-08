- [Capitulo 1 Ondas](#chapter-1-waves)
  - [1 Caracteristicas de una onda](#1-caracteristicas-de-una-onda)
    - [1.1.1 Amplitud (A)](#111-amplitud-a)
    - [1.1.2 Frecuencia (f)](#112-frecuencia-f)
    - [1.1.3 Periodo de tiempo (T)](#113-periodo-de-tiempo-t)
    - [1.1.4 Longitud de onda (λ)](#114-longitud-de-onda-%ce%bb)
    - [1.1.5 Velocidad (v)](#115-velocidad-v)
  - [2 Clasificación de las ondas](#2-clasificaci%c3%b3n-de-las-ondas)
    - [2.1 En función del medio en el que se propagan](#21-en-funci%c3%b3n-del-medio-en-el-que-se-propagan)
      - [2.1.1 Ondas mecánicas:](#211-ondas-mec%c3%a1nicas)
      - [2.1.2 Ondas electromagnéticas](#212-ondas-electromagn%c3%a9ticas)
      - [2.1.3 Ondas gravitacionales](#213-ondas-gravitacionales)
    - [2.2 En función de su dirección](#22-en-funci%c3%b3n-de-su-direcci%c3%b3n)
      - [2.2.1 Ondas unidimensionales](#221-ondas-unidimensionales)
      - [2.2.2 Ondas bidimensionales o superficiales](#222-ondas-bidimensionales-o-superficiales)
      - [2.2.3 Ondas tridimensionales o esféricas](#223-ondas-tridimensionales-o-esf%c3%a9ricas)
    - [2.3 En función del movimiento de sus partículas](#23-en-funci%c3%b3n-del-movimiento-de-sus-part%c3%adculas)
      - [2.3.1 Ondas longitudinales](#231-ondas-longitudinales)
      - [2.3.2 Ondas transversales](#232-ondas-transversales)
    - [2.4  En función de su periodicidad](#24-en-funci%c3%b3n-de-su-periodicidad)
      - [2.4.1 Ondas periódicas](#241-ondas-peri%c3%b3dicas)
      - [2.4.2 Ondas no periódicas](#242-ondas-no-peri%c3%b3dicas)
- [Capitulo 2 Moduladores y demoduladores](#capitulo-2-moduladores-y-demoduladores)
  - [2.1 Señal portadora](#21-se%c3%b1al-portadora)
  - [2.2 Señal moduladora](#22-se%c3%b1al-moduladora)
  - [2.3 Por que modular una señal?](#23-por-que-modular-una-se%c3%b1al)
  - [2.4 Técnicas de modulación empleadas](#24-t%c3%a9cnicas-de-modulaci%c3%b3n-empleadas)
  - [2.5 Tipos de moduladores](#25-tipos-de-moduladores)
    - [2.5.1 Modulación analógica:](#251-modulaci%c3%b3n-anal%c3%b3gica)
      - [2.5.1.1 Modulación de la amplitud.](#2511-modulaci%c3%b3n-de-la-amplitud)
      - [2.5.1.2 Modulación de la frecuencia.](#2512-modulaci%c3%b3n-de-la-frecuencia)
      - [2.5.1.3 Modulación de la fase.](#2513-modulaci%c3%b3n-de-la-fase)
    - [2.5.2 Modulación digital](#252-modulaci%c3%b3n-digital)
      - [2.5.2.1 Modulación por desplazamiento de amplitud (**ASK, Amplitude Shift Keying**)](#2521-modulaci%c3%b3n-por-desplazamiento-de-amplitud-ask-amplitude-shift-keying)
      - [2.5.2.2 Modulación por desplazamiento de frecuencia (**FSK,Frecuency Shift Keying**)](#2522-modulaci%c3%b3n-por-desplazamiento-de-frecuencia-fskfrecuency-shift-keying)
        - [2.5.2.2.1 Multiplexacion de 2 diferentes frecuencias.</h4>](#25221-multiplexacion-de-2-diferentes-frecuenciash4)
        - [2.5.2.2.2 Oscilador controlado por tensión (VCO).](#25222-oscilador-controlado-por-tensi%c3%b3n-vco)
      - [2.5.2.3 Modulación por desplazamiento de frecuencia Gausiana (GFSK)](#2523-modulaci%c3%b3n-por-desplazamiento-de-frecuencia-gausiana-gfsk)
      - [2.5.2.4 Modulación por desplazamiento de fase (**PSK, Phase Shift Keying**)](#2524-modulaci%c3%b3n-por-desplazamiento-de-fase-psk-phase-shift-keying)
- [Capitulo 3 Filtros](#capitulo-3-filtros)
  - [3.1 ¿Qué es un filtro de frecuencia?](#31-%c2%bfqu%c3%a9-es-un-filtro-de-frecuencia)
  - [3.2 Clasificación de los filtros](#32-clasificaci%c3%b3n-de-los-filtros)
    - [3.2.1 Respuesta en frecuencia](#321-respuesta-en-frecuencia)
- [Capitulo 4 Demodulador FSK](#capitulo-4-demodulador-fsk)



# Teoría de señales y moduladores

En este documento se pretende explicar los principios y conceptos fundamentales que hacen posible interconectar nodos de manera inalámbrica sin necesidad de cables, irradiando y recibiendo señales por medio de técnicas descritas en esta sección.

# Capitulo 1 Ondas

Una onda consiste en la propagación de una perturbación de alguna propiedad del espacio, por ejemplo:

- Densidad.
- Presión.
- Campo eléctrico.
- Campo magnético.

En el ejemplo de la imagen generando una onda con una cuerda, se puede apreciar la deformación del medio el cual representa el transporte de energía, sin transportar la materia misma.


<img src="../pics/wave.svg" alt="drawing" height="300" width="400" align="block" />

Una onda puede ser completamente descrita por 5 características llamadas amplitud, frecuencia, periodo, longitud de onda y velocidad.

## 1. Wave characteristics

<figure>
  <img src="../pics/wave-characteristics.svg" alt="drawing" height="330" width="400" align="left"/>
  <img src="../pics/frequency.svg" alt="drawing" height="330" width="400" align="left"/>
</figure>

### 1.1.1 Amplitude (A)

Es la distancia vertical entre una cresta y el punto medio de la onda.

### Modulación en Frecuencia - FM.

La frecuencia instantánea de salida del oscilador es controlada por el voltaje de entrada. Es un tipo de oscilador que puede producir una frecuencia de señal de salida en un amplio rango (pocos Hertz-cientos de Giga Hertz) dependiendo de la tensión de entrada de corriente continua que se le haya asignado.

```
f = 1/T

```


### 1.1.3 Time period (T)

El tiempo requerido para producir una vibración completa (onda o ciclo) se llama período de tiempo de la onda. The IS of the Time is the second (s).

```
T = 1/f
```

### 1.1.4 Wavelenght (λ)

La distancia mínima en la que se repite una onda de sonido se denomina longitud de onda. En una onda de sonido, la distancia entre los centros de dos compresiones consecutivas o dos rarefacciones consecutivas también se llama longitud de onda. La unidad SI de longitud de onda es metro (m).


### 1.1.5 Speed (V)

The distance traveled by the wave in a second it's called propagation speed. La unidad SI de longitud de onda es metro (m).

```
V = 1/T
```


## 2. Type of waves

### 2.1 Propagation medium

#### 2.1.1 Mechanical waves

Las ondas mecánicas necesitan un medio material elástico (sólido, líquido o gaseoso) para propagarse. Las partículas del medio oscilan alrededor de un punto fijo, por lo que no existe transporte neto de materia a través del medio. Dentro de las ondas mecánicas tenemos las ondas elásticas, las ondas que se propagan en la superficie del agua o en una explosión controlada, las ondas sonoras y las ondas de gravedad.

#### 2.1.2 Electromagnetic waves

las ondas electromagnéticas se propagan por el espacio sin necesidad de un medio material, pudiendo por lo tanto propagarse en el vacío. Esto es debido a que las ondas electromagnéticas son producidas por las oscilaciones de un campo eléctrico, en relación con un campo magnético asociado. Las ondas electromagnéticas viajan aproximadamente a una velocidad de 300000 km/s, de acuerdo a la velocidad puede ser agrupado en rango de frecuencia. Este ordenamiento es conocido como Espectro Electromagnético, objeto que mide la frecuencia de las ondas. Los rayos X, la luz visible o los rayos ultravioleta son ejemplos de ondas electromagnéticas.

#### 2.1.3 Gravitational wave
Las ondas gravitacionales son perturbaciones que alteran la geometría misma del espacio-tiempo y aunque es común representarlas viajando en el vacío, técnicamente no podemos afirmar que se desplacen por ningún espacio, sino que en sí mismas son alteraciones del espacio-tiempo.

### 2.2 Wave Dimension

#### 2.2.1 Unidimensional wave
La distancia recorrida por la onda de sonido en un segundo se llama velocidad de propagación La unidad SI de velocidad de propagación es m /s.

#### 2.2.2 Two-dimensional or surface wave
La distancia recorrida por la onda de sonido en un segundo se llama velocidad de propagación La unidad SI de velocidad de propagación es m /s.

#### 2.2.3 Three-dimensional or spherical
The distrubance is propagated in all directions from the source, it's also known as spherical wave.


### 2.3 Particles movement

#### 2.3.1  Longitudinal waves
Son aquellas que se caracterizan porque las partículas del medio se mueven o vibran paralelamente a la dirección de propagación de la onda. Por ejemplo, las ondas sísmicas, las ondas sonoras y un muelle que se comprime dan lugar a una onda longitudinal.

#### 2.3.2 Transverse waves
La distancia recorrida por la onda de sonido en un segundo se llama velocidad de propagación La unidad SI de velocidad de propagación es m /s.

### 2.4 Periodicity

#### 2.4.1 Periodic wave
La perturbación local que las origina se produce en ciclos repetitivos por ejemplo una onda senoidal.

#### 2.4.2 Non-periodic wave

La perturbación que las origina se da aisladamente o, en el caso de que se repita, las perturbaciones sucesivas tienen características diferentes. Las ondas aisladas también se denominan pulsos.


# Modulación en Fase - PM.

Information signals are rarely in an appropiate state for transmission. Signals must be carried between the transmisor and the receiver on some medium.

Modulation is a process in which the carrier signal is altered in one of its characteristics in order to properly transmit the data, protecting it from noise and interferences. [15]

La Demodulación es el proceso inverso (es decir, la onda modulada se convierte nuevamente a su forma original). La modulación se realiza en el transmisor en un circuito llamado modulador, y la demodulación se realiza en el receptor en un circuito llamado demodulador o detector.

## Una señal portadora es una onda eléctrica que puede ser modificada en alguno de sus parámetros por la señal de información (sonido, imagen o datos) para obtener una señal modulada y que se transporta por el canal de comunicaciones.

En este tipo de técnica, lo que se obtiene a la salida es la señal portadora o carrier alterada en amplitud, proporcional a la amplitud de la señal moduladora o en este caso la señal proveniente del micrófono. Dicha señal es la responsable de alterar la señal de alta frecuencia.

## Es necesario modular las señales por diferentes razones:

En este tipo de técnica, lo que se obtiene a la salida es la señal portadora o carrier alterada en amplitud, proporcional a la amplitud de la señal moduladora o en este caso la señal proveniente del micrófono. Dicha señal es la responsable de alterar la señal de alta frecuencia.


<img src="../pics/modulator1.svg" alt="drawing" height="180" width="400" align="block" />


## Es necesario modular las señales por diferentes razones:

Como se dijo antes una señal moduladora, puede ser una señal de audio, video, o datos; cualquier señal de éstas al ser mezclada con la portadora generan la señal modulada que se transmite a través de la antena.


## Las formas básicas de Modulación son:

<img src="../pics/modulator-types.svg" alt="drawing" height="400" width="800" align="center" />


### Las tres técnicas de modulación analógica son:


#### Modulación en Fase - PM.
<br/>
<img src="../pics/AM_modulator.svg" alt="drawing" height="200" width="400" align="left" />

En este tipo de técnica, lo que se obtiene a la salida es la señal portadora o carrier alterada en amplitud, proporcional a la amplitud de la señal moduladora o en este caso la señal proveniente del micrófono. Dicha señal es la responsable de alterar la señal de alta frecuencia.

<br/>
<br/>
<br/>
<br/>

#### Modulación en Frecuencia - FM.

<img src="../pics/FM_modulator.svg" alt="drawing" height="220" width="600" align="center" />
<br/>
<br/>

Modulación en Frecuencia - FM.

#### Modulación en Fase - PM.

La forma de las señales de modulación de frecuencia y modulación de fase son muy parecidas. De hecho, es imposible diferenciarlas sin tener un conocimiento previo de la función de modulación.

### Modulación en Frecuencia - FM.

#### La modulación por desplazamiento de amplitud, en inglés Amplitude-shift keying (ASK), es una forma de modulación en la cual se representan los datos digitales como variaciones de amplitud de la onda portadora en función de los datos a enviar.

La modulación por desplazamiento de amplitud, en inglés Amplitude-shift keying (ASK), es una forma de modulación en la cual se representan los datos digitales como variaciones de amplitud de la onda portadora en función de los datos a enviar.

As AM, ASK is also linear and sensitive to atmospheric noise, distortions, and propagation conditions in different routes on PSTN, among other factors. Amplitude modulation requires an excessive bandwidth and therefore an energy expense[16], but modulation and demodulation are cheap enough.

<img src="../pics/ask_modulator.svg" alt="drawing" height="400" width="400" align="center" />


#### La modulación por desplazamiento de frecuencia o FSK (del inglés Frequency Shift Keying), es una técnica de modulación para la transmisión digital de información, utilizando dos o más frecuencias diferentes para cada símbolo.​ La señal moduladora solo varía entre dos valores de tensión discretos formando un tren de pulsos donde uno representa un "1" o "marca" y el otro representa el "0" o "espacio".

FSK is a digital frequency modulation technique for binary data transmission. As in FM, the data is encoded in the carrier wave by shifting its frequency between preset frequencies. This technique is also used for Morse code, among other uses.

<img src="../pics/fsk_modulator.svg" alt="drawing" height="400" width="400" align="center" />

En esta forma de modulación la portadora sinusoidal toma dos valores de frecuencia, determinados directamente por la señal de datos binaria. El modulador puede realizarse en varios modos, los cuales se describen a continuación.


##### En la siguiente figura tenemos dos portadoras con diferente frecuencia, para representar los diferentes símbolos dentro del mensaje, en este caso cero(0) y uno(lógico), la frecuencia del oscilador 1 debe ser diferente a la del oscilador 2 pero teniendo en cuanta que la diferencia no debe ser muy grande.

On the next figure we have two carriers with different frequencies to represent the different symbols inside the message, in this case 0 and 1. The frequency of the oscillator 1 must be different to the oscillator 2 but keeping in mind that the difference can't be too big.

En la siguiente figura tenemos dos portadoras con diferente frecuencia, para representar los diferentes símbolos dentro del mensaje, en este caso cero (0) y uno (lógico), la frecuencia del oscilador 1 debe ser diferente a la del oscilador 2 pero teniendo en cuenta que la diferencia no debe ser muy grande.

En este tipo de modulación, la señal moduladora tiene una amplitud constante pero varia la frecuencia, la cual es utilizada para alterar la frecuencia de la señal portadora o carrier.

<img src="../pics/modulator-switch.svg" alt="drawing" height="200" width="600" align="center" />



##### 2.5.2.2.2 Voltage Controlled Oscillator (VCO)

### VCO

La frecuencia instantánea de salida del oscilador es controlada por el voltaje de entrada. Es un tipo de oscilador que puede producir una frecuencia de señal de salida en un amplio rango (pocos Hertz-cientos de Giga Hertz) dependiendo de la tensión de entrada de corriente continua que se le haya asignado.

Este tipo de osciladores al no presentar voltaje en su entrada o lo que es igual a 0 voltios, debe oscilar en una frecuencia llamada frecuencia libre de oscilación, y al empezar a incrementar el voltaje de entrada, la señal de salida se ve alterada en su frecuencia creciendo de forma lineal respecto al voltaje de entrada.

Los siguientes son algunos casos extremos de estas técnicas:

This modulation technique, like the previous one, handles a different frequency for each symbol. The difference is the VCO deletes the abrupt changes in the frequency due it doesn't needs to multiplex between different frequency generators, it consists in controlling the the frequency coming from the generator.

<img src="../pics/modulation-vco.svg" alt="drawing" height="250" width="800" align="center" />


#### modulación GFSK = filtro Gaussiano + FSK

<img src="../pics/Gaussian-filter.svg" alt="drawing" height="300" width="450" align="left" />

Esta técnica es similar a la técnica de FSK descrita en el apartado anterior, con una pequeña mejora en el ancho de banda de la señal.

Esta técnica consiste en suavizar el tren de pulsos (datos o señal moduladora), como sabemos dicho tren de pulsos representa una onda cuadrada la cual presenta cambios abruptos cuando pasa de cero lógico a uno lógico, aumentando el ancho de banda del espectro de señal.

Al pasar la señal moduladora (Data) a través de un filtro Gaussiano la salida es muy similar a una onda senoidal la cual presenta transiciones suaves de cero a uno.

Con esto observamos que:

`modulación GFSK = filtro Gaussiano + FSK`

We said before that having these abrupt changes from 0 to 1 in the modulated signal increases the bandwidth. We conclude that the improvement that this type of filter provides in FSK modulation is the decrease of the spectrum bandwidth in the output signal.

#### 2.5.2.4 Phase Shift Keying (PSK)

En esta forma de modulación la portadora sinusoidal toma dos valores de frecuencia, determinados directamente por la señal de datos binaria. El modulador puede realizarse en varios modos, los cuales se describen a continuación.


# Chapter 3 Filters

## Existen diferentes tipos de filtros clasificados por su funcionalidad:

Filtros Análogos: Procesa señales continuas, basado en componentes electrónicos análogos.

## Filtros Análogos: Procesa señales continuas, basado en componentes electrónicos análogos.

Existen diferentes tipos de filtros clasificados por su funcionalidad:
- Filtros Digitales: Procesa Señales discretas, este tipo de filtro es basado en software.
  - Modulación en Amplitud - Doble banda lateral con portadora - AM.
  - Doble banda lateral sin portadora - DBL-SP.
  - Banda lateral única - BLU.
- Filtros Análogos: Procesa señales continuas, basado en componentes electrónicos análogos.
  - Pasivos: Basado en Condensadores, Bobinas y Resistencias, presenta pérdidas por atenuación. It presents attenuation losses.
   - Pasa bajos.
   - Pasa altos.
   - Pasa banda.
  - Activos: Basado en Condensadores, Bobinas y Resistencias. These offer amplitude.
   - Pasa bajos.
   - Pasa altos.
   - Pasa banda.

### Modulación en Frecuencia - FM.

<img src="../pics/filter-graph.svg" alt="drawing" height="300" width="450" align="center" />

# modulación GFSK = filtro Gaussiano + FSK

This chapter shows some insight in the process involved in the reception of a signal modulated on FSK, emitted by a node. To receive the original data without the carrier some process must be done, for this goal we will use low-pass filters, which allows filter the modulated signal removing its high frequency components, leaving just the original message, this process is known as demodulation.

Estas dos frecuencias se hacen pasar por un interruptor digital de dos estados controlado por una señal digital, en este caso el mensaje digital y el cual genera la salida mostrada en la salida del interruptor.
- Detección síncrona.
- Detección de envolvente.

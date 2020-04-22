
# 11. Operaciones de inicializacion del protocolo AODVv2.
Explicaremos los procesos involucrados en la inicializacion y puesta en marcha del protocolo _AODVv2_ sobre el hardware embebido  basado en el cc1312 y el sistema operativo RIOT-OS, que conforman la plataforma del ```Turpial```.  

## 11.1 Proceso de inicialización.

<p>
<img src="imple_pic/aodvv2_init.svg" alt="drawing" height="500" width="300" align="left"/>
</p>

El proceso de inicializacion del protocolo _AODV_ sobre un nodo de red requiere seguir una serie de pasos para configurar el software y hardware necesarios para la correcta operación del protocolo de enrutamiento.

En la imagen podemos ver cada una de las funciones que se deben ejecutar antes de poder conectarnos a la red **Locha Mesh**.

Los procesos ejecutados en la etapa de inicializacion configuran las tablas manejadas dentro del protocolo, inicializan la interfaz de red y obtienen la dirección IP necesaria para operar en la red, entre otros recursos.

Todos los algoritmos presentados aquí están desarrollados bajo el sistema operativo RIOT-OS, por parte del equipo de **Locha**.


<br />&nbsp;<br />
<br />&nbsp;<br />
<br />&nbsp;<br />


## 11.2 aodvv2_seqnum_init

Como se ha descrito en el capítulo 8.9, el número de secuencia permite conocer si los mensajes de ruta son obsoletos o no, y así mantener información de rutas frescas en todo momento.

La función _aodvv2_seqnum_init_ es parte de unas funciones localizadas dentro de un archivo llamado _aodvv2_seqnum.c_, con las siguientes funciones:

- aodvv2_seqnum_init(void).
- aodvv2_seqnum_inc(void).
- aodvv2_seqnum_get().

Conociendo el papel que desempeña el numero de secuencia en el protocolo _AODV_, vemos que las funciones no hacen mas de lo que dice su nombre:

- Iniciar en **1** (init) un contador, como se detalla en la descripción del protocolo.
- Leer el contador y obtener su valor (get).
- Incrementar el contador en uno (inc), como detalla el protocolo,por cada _RREQ_ o _RREP_ que genere este nodo.


### 11.2.1 Rutina para manejar el número de secuencia.

```cpp
#include "net/aodvv2/seqnum.h"

#include <stdatomic.h>

static atomic_uint_fast32_t seqnum;

void aodvv2_seqnum_init(void)
{
    /* Initialize to 1 */
    atomic_init(&seqnum, 1);
}

void aodvv2_seqnum_inc(void)
{
    if (atomic_fetch_add(&seqnum, 1) >= 65535) {
        atomic_store(&seqnum, 1);
    }
}

aodvv2_seqnum_t aodvv2_seqnum_get(void)
{
    return atomic_load(&seqnum);
}

```

La librería es muy sencilla, pero eficiente ya que soluciona posibles problemas de datos que se generan cuando diferentes threads o hilos de ejecución en la aplicación tratan de leer y escribir al mismo tiempo el objeto que representa la secuencia de número. 


## 11.3 aodvv2_routing_table_init

Todos los routers _AODV_ deben mantener un conjunto de rutas locales con información procedente de los mensajes de ruta (_RREQ_ y _RREP_).
Para manejar la tabla de ruta del ```Turpial``` es necesario mantener una lista con dicha información y para ello, la función _aodvv2_routing_table_init_ se encarga de crear el array necesario para almacenar la información con las rutas que pueden ser alcanzadas a través de la mencionada tabla de rutas.

### 11.3.1 Esquema de una entrada en la tabla de rutas.
La estructura del paquete que se debe guardar en la tabla de rutas corresponde al esquema definido en el archivo _routingtable.h_:

```cpp
/**
 * @brief   All fields of a routing table entry
 */
typedef struct {
    
    struct netaddr addr;    /**< IP address of this route's destination */
    aodvv2_seqnum_t seqnum; /**< The Sequence Number obtained from the
                                last packet that updated the entry */
    struct netaddr next_hop; /**< IP address of the the next hop towards the
                                destination */
    timex_t last_used;      /**< The last time this route it was used to 
                                forward  and IP packet*/
    timex_t expiration_time; /**< Time at which this route expires */
    routing_metric_t metricType; /**< Metric type of this route */
    uint8_t metric;         /**< Metric value of this route*/
    uint8_t state;          /**< State of this route (i.e. one of
                                aodvv2_routing_states) */
} aodvv2_local_route_t;
```
La directiva _CONFIG_AODVV2_MAX_ROUTING_ENTRIES_ representa la cantidad de rutas que se pueden mantener en el nodo. Los recursos disponibles en el hardware tienen esta limitación.


### 11.3.2 Rutina para la inicializacion de la tabla de rutas locales.
La rutina encargada de iniciar la tabla de rutas se encuentra definida en el archivo _aodvv2_routingtable_init_ y el código es el siguiente:

```cpp
static aodvv2_local_route_t routing_table[CONFIG_AODVV2_MAX_ROUTING_ENTRIES];

static timex_t null_time;
static timex_t max_seqnum_lifetime;
static timex_t active_interval;
static timex_t max_idletime;
static timex_t validity_t;
static timex_t now;

void aodvv2_routingtable_init(void)
{
    DEBUG("aodvv2_routingtable_init()\n");

    null_time = timex_set(0, 0);
    max_seqnum_lifetime = timex_set(CONFIG_AODVV2_MAX_SEQNUM_LIFETIME, 0);
    active_interval = timex_set(CONFIG_AODVV2_ACTIVE_INTERVAL, 0);
    max_idletime = timex_set(CONFIG_AODVV2_MAX_IDLETIME, 0);
    validity_t = timex_set(CONFIG_AODVV2_ACTIVE_INTERVAL +
                           CONFIG_AODVV2_MAX_IDLETIME, 0);

    memset(&routing_table, 0, sizeof(routing_table));
}
```

Iniciar la tabla de rutas equivale a  poner a cero todas las entradas de la tabla de rutas y crear algunas variables de tiempo que sirven para medir si la información  obtenida del mensaje de rutas es obsoleta o, por el contrario, nos brinda información útil para el correcto funcionamiento del router _AODV_.

Para mas información acerca de las tablas de rutas diríjase a la seccion 4.5 del documento draft-perkins-manet-aodvv2.

## 11.4 aodv_rreqtable_init

Esta función inicializa una tabla con la intencion de almacenar información de mensajes _multicast_ entrantes para evitar retransmitir mensajes redundantes.
Esta tabla lleva un registro de cada mensaje _RREQ_ entrante, por lo que nuevos mensajes entrantes _RREQ_, buscando el mismo destino desde un mismo origen, son eliminados sin la necesidad de retransmitirlos.

### 11.4.1 Esquema de una entrada en la tabla de mensaje de rutas RREQ.
Este es el formato del mensaje que se almacenará en la tabla de mensaje de rutas (_RREQ_)
```cpp
typedef struct {
    struct netaddr origNode; /**< Node which originated the RREQ*/
    struct netaddr targNode; /**< Target (destination) of the RREQ */
    routing_metric_t metricType; /**< Metric type of the RREQ */
    uint8_t metric; /**< Metric of the RREQ */
    aodvv2_seqnum_t seqnum; /**< Sequence number of the RREQ */
    timex_t timestamp; /**< Last time this entry was updated */
} aodvv2_rreq_entry_t;

```

### 11.4.2 Rutina para la inicializacion de la tabla de mensajes Multicast entrantes.

Esta función se encuentra en el archivo _aodvv2_rreqtable.c_, que además expone funciones para el manejo de dicha tabla.

El código muestra cómo se inician algunas variables de tiempo y le asigna un valor de cero a dicha tabla cuando se inicia el protocolo _AODV_.

La variable _mutex_ sirve para bloquear el acceso a la tabla por otros procesos dentro de la aplicación.
```cpp
static mutex_t rreqt_mutex;

static aodvv2_rreq_entry_t rreq_table[AODVV2_RREQ_BUF];

static timex_t _null_time;
static timex_t now;
static timex_t _max_idletime;

static struct netaddr_str nbuf;

void aodvv2_rreqtable_init(void)
{
    DEBUG("aodvv2_rreqtable_init()\n");
    mutex_lock(&rreqt_mutex);

    _null_time = timex_set(0, 0);
    _max_idletime = timex_set(CONFIG_AODVV2_MAX_IDLETIME, 0);
    memset(&rreq_table, 0, sizeof(rreq_table));
    mutex_unlock(&rreqt_mutex);
}
```

## 11.5 aodvv2_buffer_init

Esta función nos da la posibilidad de almacenar un paquete dentro de un buffer para el cual no existe una ruta.

Cuando un cliente desea enviar un paquete con un mensaje el stack de red primero revisa en su tabla de rutas (NIB). Si no encuentra una ruta para ese destino, debe iniciar un proceso de buffering para alamacenar el paquete dentro del nodo hasta que encuentre la ruta solicitada.

### 11.5.1 Rutina para la inicializacion del buffer de paquetes.

El siguiente código representa la función que se encarga de inicializar el buffer donde se almacenará un paquete de mensaje si no se conoce una ruta hacia el destino. La rutina se encuentra dentro del archivo _aodvv2_buffer.c_, que también ofrece rutinas para la manipulacion de la información.
```cpp
void aodvv2_buffer_init(void)
{
    memset(_buffered_pkts, 0, sizeof(_buffered_pkts));
}
```
### 11.5.2 Estructura del mensaje en buffer.

El mensaje almacenado en el buffer debe tener la siguiente estructura:

```cpp
typedef struct {
    bool used;
    gnrc_pktsnip_t *pkt;
    ipv6_addr_t dst;
} buffered_pkt_t;
```
### 11.5.3 Definición del buffer para almacenar mensajes.
Para almacenar los mensajes que se quieren enviar pero para los cuales no existe una ruta, se crea un array con tamaño fijo establecido y limitado por los recursos del hardware.

```
static buffered_pkt_t _buffered_pkts[CONFIG_AODVV2_MAX_BUFFERED_PACKETS];

```

## 11.6 Iniciar la tabla de clientes.

La tabla de clientes es una tabla conceptual que almacena los clientes del router, y el nodo solo recreará mensajes de _RREQ_ y _RREP_ de los clientes registrados en dicha tabla.

### 11.6.1 Rutina para inicializar la tabla de rutas.

La tabla de rutas se representa, como las demás tablas por un array con un tamaño fijo definido y limitado por los recursos del hardware donde se ejecuta la aplicación.

El _mutex_ permite acceder al recurso (tabla de clientes) sin ser interrumpido por otros procesos tratando de leer o escribir la tabla.

```cpp
static aodvv2_client_entry_t _client_set[CONFIG_AODVV2_CLIENT_SET_ENTRIES];
static mutex_t _set_mutex = MUTEX_INIT;

void aodvv2_client_init(void)
{
    DEBUG("aodvv2_client_init()\n");

    mutex_lock(&_set_mutex);
    memset(&_client_set, 0, sizeof(_client_set));
    mutex_unlock(&_set_mutex);
}
```

## 11.7 Iniciar la interface de red.
Para hacer posible la conexión del ```Turpial``` a la red, se configurar la interface de red. En la función que inicia el protocolo _AODV_ se ha habilitado un argumento que permite recibir como parámetro la interface de red a utilizar, con el fin de habilitar la posibilidad de registrar indistintamente cualquier interface disponible en el dispositivo.

Por tanto no es responsabilidad de la implementación del protocolo configurar la interface de red que se va a utilizar para la conexión con la red inalámbrica, pero su configuración dentro de la plataforma de _RIOT-OS_, se resume en las siguientes líneas de código.

Para mas información visite [https://riot-os.org/api/structnetif__t.html](https://riot-os.org/api/structnetif__t.html)

El código itera sobre las interfaces de red disponibles, y si encuentra una disponible y compatible con _IEEE802.15.4_, retorna un puntero con la dirección de esa interface.
```cpp
static gnrc_netif_t *_find_ieee802154_netif(void)
{
    static uint16_t device_type = 0;

    static gnrc_netapi_opt_t opt = {
        .opt = NETOPT_DEVICE_TYPE,
        .context = 0,
        .data = &device_type,
        .data_len = sizeof(uint16_t),
    };

    /* Iterate over network interfaces and find one that's IEEE 802.15.4, for
     * the CC1312 there is only one interface, but we need to be sure it was
     * initialized. Also well be using other interface; probably SLIP over UART
     * so we need to be sure it's IEEE 802.15.4 */
    gnrc_netif_t *netif = NULL;
    for (netif = gnrc_netif_iter(netif);
         netif != NULL;
         netif = gnrc_netif_iter(netif)) {
        if (gnrc_netif_get_from_netdev(netif, &opt) == sizeof(uint16_t)) {
            if (device_type == NETDEV_TYPE_IEEE802154) {
                return netif;
            }
        }
    }

    return NULL;
}
```


## 11.8 Dirección IP global
Cuando tengamos interface de red configurada, el siguiente paso es configurar una dirección _IPV6_ global y de carácter único sobre toda la red, con la intencion de evitar direcciones IP duplicadas en toda la red.

El código para extraer la dirección _IPV6_ global del dispositivo es posible teniendo la referencia de la interface de red.

```cpp
/* Save our IPv6 address */
    ipv6_addr_t netif_addr;
    if (_find_netif_global_addr(&netif_addr) < 0) {
        DEBUG("aodvv2: no global address found\n");
        return -1;
    }
```
Podría darse el caso de que el nodo tenga más de una interface de red. Por esto es necesaria la búsqueda de la dirección global en cualquiera de las interfaces disponibles en el hardware.

### 11.8.1  _find_netif_global_addr
Para el envío de paquetes entre nodos, es necesario configurar direcciones IP globales. Si no, el stack de red no será capaz de rutear los paquetes de manera correcta debido a que todas las boards del ```Turpial``` cuentan con dos tipos de direcciones IPV6: una global y otra local.

```cpp
static int _find_netif_global_addr(ipv6_addr_t *addr)
{
    assert(addr != NULL);

    ipv6_addr_t addrs[CONFIG_GNRC_NETIF_IPV6_ADDRS_NUMOF];

    int numof = gnrc_netif_ipv6_addrs_get(_netif, addrs, sizeof(addrs));
    if (numof < 0) {
        DEBUG("aodvv2: couldn't get IPv6 addresses for iface\n");
        return -1;
    }

    for (unsigned i = 0; i < (numof / sizeof(ipv6_addr_t)); i++) {
        /* Pick up the first global address */
        if (ipv6_addr_is_global(&addrs[i])) {
            memcpy(addr, &addrs[i], sizeof(ipv6_addr_t));
            return 0;
        }
    }

    /* No address was found */
    return -1;
}
```

## 11.9 aodvv2_client_add

Esta función es utilizada para añadir clientes a la tabla correspondiente. Se usa por primera vez dentro de la rutina de inicialización del protocolo, ya que el mismo dispositivo debe agregarse a sí mismo como cliente en esta tabla.

### 11.9.1 Rutina para agregar clientes a la tabla.

Esta función recibe como parámetros la IP del cliente el tamaño de prefijo de red y el costo del link.
El tipo de dirección que se pasa a la función es del tipo _ipv6_addr_t_. Ésta almacena un dato de 128 bits. Se hace la aclaración debido a que _RIOT-OS_ ofrece otros tipos de direcciones equivalentes pero no compatibles del todo, como puede ser _netaddr_, que ofrece un poco más de información acerca del propietario de la IP.

El siguiente algoritmo es simple, pero destaca la tarea principal, que consiste en tomar la dirección IP del argumento para así buscar dentro de la tabla de clientes y saber si se debe actualizar la información al existir una similar, o si se debe crear una entrada nueva cuando no sea así.

```cpp
aodvv2_client_entry_t *aodvv2_client_add(const ipv6_addr_t *addr,
                                         const uint8_t prefix_length,
                                         const uint8_t cost)
{
    DEBUG("aodvv2_client_add_client(%s)\n",
          ipv6_addr_to_str(ipbuf, addr, sizeof(ipbuf)));

    aodvv2_client_entry_t *entry = aodvv2_client_find(addr);
    if (entry) {
        /* Update entry if it exists */
        mutex_lock(&_set_mutex);
        entry->prefix_length = prefix_length;
        entry->cost = cost;
        entry->_used = true;
        mutex_unlock(&_set_mutex);
        DEBUG("aodvv2_client_add_client: client is already stored\n");
        return entry;
    }

    /* Find free spot in client set and place the entry there */
    mutex_lock(&_set_mutex);
    for (unsigned i = 0; i < ARRAY_SIZE(_client_set); i++) {
        if (!_client_set[i]._used) {
            memcpy(&_client_set[i].ip_address, addr, sizeof(ipv6_addr_t));
            _client_set[i].prefix_length = prefix_length;
            _client_set[i].cost = cost;
            _client_set[i]._used = true;
            DEBUG("aodvv2_client_add_client: client added\n");
            mutex_unlock(&_set_mutex);
            return &_client_set[i];
        }
    }
    DEBUG("aodvv2_client_add_client: client table is full.\n");
    mutex_unlock(&_set_mutex);

    return NULL;
}
```


## 11.10 Registro en el stack de red.
Para poder escuchar mensajes entrantes provenientes de nodos externos, debemos registrarnos a un tipo especifico de mensajes, en este caso los mensajes _UDP_. _RIOT-OS_ ofrece la posibilidad de tener un hilo o thread desde el cual se podrán escuchar mensajes entrantres mediante el protocolo _UDP_ con tan solo realizar una suscripción a dicho tipo de mensajes. Este mecanismo es conocido como _IPC_ (Inter Communication process), y se puede configurar para que se ejecute de forma sincrónica o asíncrona.

La diferencia entre el tipo de configuración seleccionado para escuchar los mensajes radica en que cuando tenemos recepción de mensajes de forma asíncrona, habilitamos una cola de mensajes la cual almacena los mensajes hasta que sean leídos, respetando el tamaño de la cola de mensajes.


En esta fracción de código presente en la inicializacion del protocolo _AODV_, estamos suscribiendo el nodo para que pueda escuchar todos los mensajes _UDP_ procedentes de nodos externos, sin la necesidad de configurar _UDP_ por nuestra cuenta haciendo uso de la implementación que trae _RIOT-OS_ para el manejo de este tipo de suscripciones a mensajes externos.

En la funcion _gnrc_netreg_entry_init_pid(&netreg, UDP_MANET_PORT, _pid)_,  _pid_ hace referencia al ID del thread que se designa para el manejo de este tipo de mensajes. Como en la creación de cualquier thread necesitamos algunos parámetros estándar de los threads en C++.
Para la creación del thread que se encarga de esperar los mensajes, procedemos a la implementación creando un espacio de ejecución en paralelo con otros procesos dentro de _RIOT-OS_, conocido como multi-task.

### 11.10.1 Rutina para la suscripción de mensajes de red.

El término __event_loop_ en la siguiente parte de código hace referencia a la funcion que ejecuta el código necesario para estar a la espera de los mensajes entrantes a los cuales se ha suscrito.

Debemos almacenar el _PID_ del thread creado para usarlo como un parámetro de entrada en la api de _RIOT_, y decirle cuál es el thread o proceso que debe ejecutarse para esperar por los mensajes _UDP_ entrantes.  

```cpp
_pid = thread_create(_stack, sizeof(_stack), CONFIG_AODVV2_RFC5444_PRIO,
                         THREAD_CREATE_STACKTEST, _event_loop, NULL,
                         "aodvv2");
```

Después de crear el proceso encargado de ejecutar una tarea durante todo el tiempo de vida (TTL) de la aplicación, debemos relacionar este proceso con una tarea en particular. En este caso la tarea es  escuchar los mensajes _UDP_ entrantes, los cuales pueden configurarse usando la siguiente funcion de _RIOT-OS_. 

```cpp
/* Register netreg */
    gnrc_netreg_entry_init_pid(&netreg, UDP_MANET_PORT, _pid);
    gnrc_netreg_register(GNRC_NETTYPE_UDP, &netreg);
```


La variable _netreg_ representa la configuración para el tipo de mensaje al cual nos queremos suscribir, teniendo el siguiente formato:

```cpp
/**
 * @brief   Netreg
 */
static gnrc_netreg_entry_t netreg = GNRC_NETREG_ENTRY_INIT_PID(GNRC_NETREG_DEMUX_CTX_ALL,
                                                               KERNEL_PID_UNDEF);

```

La manera de escuchar mensajes _UDP_ puede cambiar considerablemente entre sistemas operativos, pero en esencia se deben obtener los mismos resultados: la capacidad de escuchar mensajes entrantes.

Pueden encontrar información detallada acerca de como crear una suscripción a cualquier tipo de mensajes de red en [https://riot-os.org/api/group__core__msg.html](https://riot-os.org/api/group__core__msg.html).

### 11.10.2 Ejemplo suscripción IPC.

_RIOT-OS_ ofrece una manera sencilla para la suscripción a este tipo de mensajes.

Si vemos el siguiente ejemplo, no es nada diferente a lo aquí propuesto para el registro de mensajes de red.

En este ejemplo el main de la aplicación está tratando de enviar mensajes al proceso que se ha creado antes.

```cpp
#include <inttypes.h>
#include <stdio.h>
#include "msg.h"
#include "thread.h"
#define RCV_QUEUE_SIZE  (8)
static kernel_pid_t rcv_pid;
static char rcv_stack[THREAD_STACKSIZE_DEFAULT + THREAD_EXTRA_STACKSIZE_PRINTF];
static msg_t rcv_queue[RCV_QUEUE_SIZE];
static void *rcv(void *arg)
{
    msg_t msg;
    (void)arg;
    msg_init_queue(rcv_queue, RCV_QUEUE_SIZE);
    while (1) {
        msg_receive(&msg);
        printf("Received %" PRIu32 "\n", msg.content.value);
    }
    return NULL;
}

int main(void)
{
    msg_t msg;
    msg.content.value = 0;
    rcv_pid = thread_create(rcv_stack, sizeof(rcv_stack),
                            THREAD_PRIORITY_MAIN - 1, 0, rcv, NULL, "rcv");
    while (1) {
        if (msg_try_send(&msg, rcv_pid) == 0) {
            printf("Receiver queue full.\n");
        }
        msg.content.value++;
    }
    return 0;
}

```

### 11.10.3 Implementación del proceso o thread IPC.

Ahora veremos cómo implementar la función que se encarga de escuchar los mensajes entrantes además de procesarlos y se revisa qué tipo de mensaje ha llegado y cuál debe ser el procedimiento aplicado, dependiendo del tipo y la información contenida en él.

```cpp
static void *_event_loop(void *arg)
{
    (void)arg;
    msg_t msg;
    msg_t reply;
    msg_t msg_queue[CONFIG_AODVV2_RFC5444_MSG_QUEUE_SIZE];

    /* Initialize message queue */
    msg_init_queue(msg_queue, CONFIG_AODVV2_RFC5444_MSG_QUEUE_SIZE);

    reply.content.value = (uint32_https://riot-os.org/api/group__core__msg.htmlt)(-ENOTSUP);
    reply.type = GNRC_NETAPI_MSG_TYPE_ACK;

    while (1) {
        msg_receive(&msg);

        switch (msg.type) {
            case AODVV2_MSG_TYPE_SEND_RREQ:
                DEBUG("AODVV2_MSG_TYPE_SEND_RREQ\n");
                {
                    aodvv2_msg_t m;
                    memcpy(&m, (aodvv2_msg_t *)msg.content.ptr, sizeof(m));
                    free(msg.content.ptr);

                    _send_rreq(&m.pkt, &m.next_hop);
                }
                break;

            case AODVV2_MSG_TYPE_SEND_RREP:
                DEBUG("AODVV2_MSG_TYPE_SEND_RREP\n");
                {
                    aodvv2_msg_t m;
                    memcpy(&m, (aodvv2_msg_t *)msg.content.ptr, sizeof(m));
                    free(msg.content.ptr);

                    _send_rrep(&m.pkt, &m.next_hop);
                }
                break;

            case GNRC_NETAPI_MSG_TYPE_RCV:
                DEBUG("GNRC_NETAPI_MSG_TYPE_RCV\n");
                _receive((gnrc_pktsnip_t *)msg.content.ptr);
                break;

            case GNRC_NETAPI_MSG_TYPE_GET:
            case GNRC_NETAPI_MSG_TYPE_SET:
                msg_reply(&msg, &reply);
                break;

            default:
                DEBUG("aodvv2: received unidentified message\n");
                break;
        }
    }

    /* Never reached */
    return NULL;
}

```


La estructura principal de la funcion la forman un ciclo _while_ infinito típico de las funciones asignadas a ejecutarse en un thread o proceso independiente, y un _switch_, el cual filtra entre los diferentes mensajes entrantes, para tomar decisiones dependiendo del tipo de mensaje.

De ahora en adelante haremos referencia a esta función para referirnos a la recepción de mensajes _UDP_, debido a que es aquí donde realmente se inicia el proceso interno con los mensajes entrantes.

```cpp
static void *_event_loop(void *arg)
{
    while(1) {
        msg_receive(&msg);

        switch (msg.type) {
            case AODVV2_MSG_TYPE_SEND_RREQ:
            break;
            case AODVV2_MSG_TYPE_SEND_RREP:
            break;
            case GNRC_NETAPI_MSG_TYPE_RCV:
            break;
            case GNRC_NETAPI_MSG_TYPE_GET:
            break;
            case GNRC_NETAPI_MSG_TYPE_SET:
            break;
            default:
            break;
        
        }
    }
}
``` 

En este punto hemos terminado de configurar casi por completo los recursos necesarios para operar en la red **Locha Mesh**. Solo hace falta configurar los objetos encargados de codificar y decodificar los mensajes _AODV_. Éstos deben viajar a través de la red encapsulados en un formato conocido como _RFC5444_, que describimos a continuación.


## 11.11 Configuración inicial RFC5444. 
_RFC5444_ define un protocolo de encapsulación y serialización de mensajes para operar en una red móvil ad-hoc, que provee la serialización y deserialización de mensajes y ofrece la posibilidad de leer varios parámetros relacionados con el paquete sin la necesidad de deserializar el paquete completamente.

_RFC5444_ fue creado para trabajar conjuntamente con el protocolo _RPL_, el cual se ha extendido a diferentes protocolos de routing, dado que se adapta a las necesidades de este tipo de redes. Fue pensado para dispositivos con bajo cálculo computacional y bajo consumo de energía, lo cual lo hace idóneo para esta aplicación.

Se hace uso de la API llamada _oonf_api_, que provee las funciones necesarias para la serialización y deserialización del mensaje.

A continuación describimos de forma detallada la manera que se realiza la configuración inicial del _reader_ y del _writer_, para la serializacion y deserializacion de paquetes.

### 11.11.1 RFC544 Reader.

Para la configuración del objeto _Reader_, debemos usar la api _oonf_api_. Además necesitamos un _mutex_ para controlar el acceso al recurso por diferentes procesos del sistema. Tendríamos algo como lo siguiente:

```cpp
/**
 * @brief   The RFC5444 packet reader context
 */
static struct rfc5444_reader _reader;
static mutex_t _reader_lock;
```
Este tipo de dato proviene de la librería _oonf_api_, específicamente los archivos relacionados con la serialización y deserialización de la norma _RFC5444_, identificados fácilmente por _rfc*****.c_.

Teniendo la variable global estática __reader_, procedemos a su configuración inicial, haciendo  uso del _mutex_ creado antes para el control de acceso al recurso.

```cpp
/* Initialize RFC5444 reader */
    mutex_lock(&_reader_lock);
    /* Initialize reader */
    rfc5444_reader_init(&_reader);
    /* Register AODVv2 messages reader */
    aodvv2_rfc5444_reader_register(&_reader, _netif->pid);
    mutex_unlock(&_reader_lock);
```

El código anterior se ejecuta dentro de la función _aodvv2_init_, como las anteriores, y es tan sencillo como:
- Bloquear el recurso _Reader_.
- Iniciar el _Reader_ con alguna configuración especial.
- Registrar el _Reader_.
- Desbloquear el recurso _Reader_.

A continuación explicamos las funciones que inician y registran el recurso.

### 11.11.2 rfc5444_reader_init.

Esta función se puede encontrar dentro del fichero llamado _rfc5444_reader.c_ y su implementación es: 

```cpp
/**
 * Initalize the context of a parser with default values.
 * @param context pointer to parser context
 */
void
rfc5444_reader_init(struct rfc5444_reader *context) {
  avl_init(&context->packet_consumer, _consumer_avl_comp, true);
  avl_init(&context->message_consumer, _consumer_avl_comp, true);

  if (context->malloc_addrblock_entry == NULL)
    context->malloc_addrblock_entry = _malloc_addrblock_entry;
  if (context->malloc_tlvblock_entry == NULL)
    context->malloc_tlvblock_entry = _malloc_tlvblock_entry;

  if (context->free_addrblock_entry == NULL)
    context->free_addrblock_entry = free;
  if (context->free_tlvblock_entry == NULL)
    context->free_tlvblock_entry = free;
}
```

La función anterior se encarga de inicializar el _Reader_ con valores por defecto, asignando espacios de memoria, el código anterior parte de la _oonf_api_ y el único requisito es pasar como parámetro una referencia al objeto _Reader_ creado con anterioridad.

### 11.11.3 aodvv2_rfc5444_reader_register
Esta función tiene como propósito registrar el objeto _Reader_ como deserializador de datos. Para ello se deben asignar las funciones de _callback_ necesarias para que sean disparadas a través de eventos internos de la API por cada bloque de lectura procesado.
El objetivo principal de la función es la de registrar los consumidores de paquetes y de bloques de direcciones.
Esta función se encuentra en un archivo llamado _rfc_reader.c_, de los desarrollados por el equipo de **btcven/LOCHA**.

_RFC5444_ define un tipo de mensaje llamado _message type-length-value_, que permiten crear una lista de entradas personalizadas de los mensajes _tlv_ requeridos en el desarrollo de la aplicación, y así poder registrar consumidores con _callback_ disponibles para el procesamiento del paquete una vez llegue al nodo.

```cpp
void aodvv2_rfc5444_reader_register(struct rfc5444_reader *reader,
                                    kernel_pid_t netif_pid)
{
    assert(reader != NULL && netif_pid != KERNEL_PID_UNDEF);

    if (_netif_pid == KERNEL_PID_UNDEF) {
        _netif_pid = netif_pid;
    }

    rfc5444_reader_add_message_consumer(reader, &_rrep_consumer,
                                        NULL, 0);

    rfc5444_reader_add_message_consumer(reader, &_rrep_address_consumer,
                                        _address_consumer_entries,
                                        ARRAY_SIZE(_address_consumer_entries));

    rfc5444_reader_add_message_consumer(reader, &_rreq_consumer,
                                        NULL, 0);

    rfc5444_reader_add_message_consumer(reader, &_rreq_address_consumer,
                                        _address_consumer_entries,
                                        ARRAY_SIZE(_address_consumer_entries));
}
```
Vamos a ver. paso a paso, cada una de las instrucciones en la función creada para registrar el _reader_ y asignar las _callback_ correspondientes.

Lo primero es que se hace una llamada a la misma función 4 veces, pero con diferentes parámetros.

La intencion es configurar los consumidores de direcciones y de mensajes para cada tipo de paquete (_RREQ_, _RREP_, _RERR_).


#### 11.11.3.1 rfc5444_reader_add_message_consumer 1.
En esta primera llamada se pretende registrar una _callback_ para el procesado del paquete _RFC5444_ del tipo _RREP_.

```cpp
rfc5444_reader_add_message_consumer(reader, &_rrep_consumer,
                                        NULL, 0);
```

Los parametros de la función son:
- _reader_: objeto configurado dentro de la función de inicializacion de _AODV_ encargado de deserializar los paquetes entrantes.
- __rrep_consumer_: estructura de datos definida dentro de la _oonf_api_ y que define los siguientes atributos:

    ```cpp
            /*
        * Message consumer, will be called once for every message of
        * type RFC5444_MSGTYPE_RREP that contains all the mandatory message TLVs
        */
        static struct rfc5444_reader_tlvblock_consumer _rrep_consumer =
        {
            .msg_id = RFC5444_MSGTYPE_RREP,
            .block_callback = _cb_rrep_blocktlv_messagetlvs_okay,
            .end_callback = _cb_rrep_end_callback,
        };
    ```

Por esto se logran registrar dos _callback_ que se ejecutan por cada mensaje tipo _RREP_ entrante.

- __cb_rrep_blocktlv_messagetlvs_okay_: esta función se ejecuta en el momento que el mensaje llega al nodo.
- __cb_rrep_end_callback_: esta función al final del proceso de lectura realizado en la primer _callback_, habilita la posibilidad de procesar nuestras propias _callbacks_.

#### 11.11.3.2 rfc5444_reader_add_message_consumer

El prpósito de esta función es registrar la _callback_ encargada de leer los bloques de dirección y registrar los tipos de dirección que se desean capturar.

```cpp
rfc5444_reader_add_message_consumer(reader, &_rrep_address_consumer,
                                        _address_consumer_entries,
                                        ARRAY_SIZE(_address_consumer_entries));
```

Los parámetros pasados a esta función difieren un poco de la configuración anterior debido a que tiene dos parametros extra:

- _reader_: igual al definido en el apartado anterior.
- __rrep_address_consumer_: esta estructura es diferente ya que está pensada para leer bloques de dirección disponibles y la anterior esta pensada para leer los mensajes:

    ```cpp
        /*
        * Address consumer. Will be called once for every address in a message of
        * type RFC5444_MSGTYPE_RREP.
        */
        static struct rfc5444_reader_tlvblock_consumer _rrep_address_consumer =
        {
            .msg_id = RFC5444_MSGTYPE_RREP,
            .addrblock_consumer = true,
            .block_callback = _cb_rrep_blocktlv_addresstlvs_okay,
        };
    ```

    - _msg_id_: identifica el tipo de mensaje.
    - _addrblock_consumer_: especifica que es un consumidor de los bloques de dirección.
    - _block_callback_: permite registrar una _callback_ propia para el proceso.
- __address_consumer_entries_: esta estructura define los tipos de entradas para los bloques de dirección para los que el _reader_ se va a registrar.
- _ARRAY_SIZE(_address_consumer_entries)_: define el tamaño del array que contiene el tipo de direcciones.

Ya hemos configurado los consumidores de mensajes y de direcciones de _RREP_.
Anteriormente hicimos énfasis en que se repite 4 veces la funcion _rfc5444_reader_add_message_consumer_ dentro de la funcion _aodvv2_rfc5444_reader_register_.

Las dos primeras veces registran el consumidor de de mensajes y el consumidor de bloques de dirección para mensajes _RREP_. Las dos últimas sirven para configurar los mismos consumidores pero para el tipo de mensajes _RREQ_, teniendo sus propias funciones de _callback_ para procesar la información.

Las estructuras de datos correspondientes a los mensajes _RREQ_ son como sigue:

La única diferencia de las estructuras de datos son las _callback_ que se registran y el tipo de mensaje, que en este caso es el _RREQ_.

```cpp
/*
 * Message consumer, will be called once for every message of
 * type RFC5444_MSGTYPE_RREQ that contains all the mandatory message TLVs
 */
static struct rfc5444_reader_tlvblock_consumer _rreq_consumer =
{
    .msg_id = RFC5444_MSGTYPE_RREQ,
    .block_callback = _cb_rreq_blocktlv_messagetlvs_okay,
    .end_callback = _cb_rreq_end_callback,
};
```

Esta función también se diferencia en que el bloque de direcciones que se desea leer debe corresponder al tipo de mensaje _RREQ_.

```cpp
/*
 * Address consumer. Will be called once for every address in a message of
 * type RFC5444_MSGTYPE_RREQ.
 */
static struct rfc5444_reader_tlvblock_consumer _rreq_address_consumer =
{
    .msg_id = RFC5444_MSGTYPE_RREQ,
    .addrblock_consumer = true,
    .block_callback = _cb_rreq_blocktlv_addresstlvs_okay,
};
```

Ya hemos configurado los consumidores de mensajes _RREQ_ y _RREP_ para los bloques de direcciones y de paquetes.
El contenido de cada _callback_ cobra sentido cuando los mensajes son recibidos dentro de la estructura del _AODV_, lo que quiere decir que esto solo sucederá ```por la naturaleza de protocolo reactivo``` cuando un usuario desea enviar un paquete para el cual no se conoce una ruta o cuando se recibe un paquete que se debe retransmitir.

## 11.12 RFC5444 Writer.
_writer_ es el objeto encargado de serializar los paquetes _AODV_ antes de ser enviados a nodos remotos.
La _oonf_api_ define los archivos necesarios para crear y manipular paquetes _RFC5444_. 
Para inicializar el serializador de paquetes conocido como _writer_, necesitamos configurar algunas variable globales dentro del archivo _aodvv2_init_:
- _buffers_: para almacenar los paquetes y los mensajes TLV a procesar.
- _rfc5444_writer_:  para la creación de los paquetes _RFC5444_ que representa el estado del mismo.
- __writer_context_: este objeto se comporta como un _wraper_ o envoltura que nos permite tener una estructura de datos personalizada para cubrir las necesidades del ```Turpial``` pero ademas haciendo dicha estructura compatible con la _oonf_api_.

```cpp
/**
 * @brief   The RFC5444 packet writer context
 */
static struct rfc5444_writer _writer;
static aodvv2_writer_target_t _writer_context;
static uint8_t _writer_msg_buffer[CONFIG_AODVV2_RFC5444_PACKET_SIZE];
static uint8_t _writer_msg_addrtlvs[CONFIG_AODVV2_RFC5444_ADDR_TLVS_SIZE];
static uint8_t _writer_pkt_buffer[CONFIG_AODVV2_RFC5444_PACKET_SIZE];
static mutex_t _writer_lock;
```

- El objeto _writer_ representa los estados internos del _RFC5444 writer_.
- _writer_context_ es un objeto del tipo _aodvv2_writer_target_t_ que representa una estructura que sirve para definir un paquete _RFC5444_ para un destino IP específico y con un tipo de mensaje específico como _rfc5444_msg_type_.

El objeto _aodvv2_writer_target_t_ ofrece la posibilidad de crear un paquete _RFC5444_ para una IP específica y de un tipo de mensaje específico, y que contiene el paquete _AODV_ que queremos serialiar para que se transmita a nodos externos. La estructura es:

```cpp
typedef struct {
    /** Interface for generating RFC5444 packets */
    struct rfc5444_writer_target target;
    /** Address to which the packet should be sent */
    ipv6_addr_t target_addr;
    aodvv2_packet_data_t packet_data; /**< Payload of the AODVv2 Message */
    int type; /**< Type of the AODVv2 Message (i.e. rfc5444_msg_type) */
} aodvv2_writer_target_t;
```
La estructura de datos anterior contiene:
- La interface para crear los paquetes _RFC5444_.
- El destino del paquete. 
- El paquete _AODV_ con su payload.
- El tipo de mensaje que se desea enviar.


La estructura del paquete _AODV_, puede representar un mensaje _RREP_ o un mensaje _RREQ_ y es: 

```cpp
/**
 * @brief   All data contained in a RREQ or RREP.
 */
typedef struct {
    uint8_t hoplimit; /**< Hop limit */
    struct netaddr sender; /**< IP address of the neighboring router
                                which sent the RREQ/RREP*/
    routing_metric_t metric_type; /**< Metric type */
    node_data_t orig_node; /**< Data about the originating node */
    node_data_t targ_node; /**< Data about the originating node */
    timex_t timestamp; /**< Point at which the packet was (roughly)
                            received. Note that this timestamp
                            will be set after the packet has been
                            successfully parsed. */
} aodvv2_packet_data_t;

```

La interface para generar los paquetes _RFC5444_ tiene el siguiente formato:


```cpp
struct rfc5444_writer_target {
  /* buffer for packet generation */
  uint8_t *packet_buffer;

  /* maximum number of bytes per packets allowed for target */
  size_t packet_size;

  /* callback for target specific packet handling */
  void (*addPacketHeader)(struct rfc5444_writer *, struct rfc5444_writer_target *);
  void (*finishPacketHeader)(struct rfc5444_writer *, struct rfc5444_writer_target *);
  void (*sendPacket)(struct rfc5444_writer *, struct rfc5444_writer_target *, void *, size_t);

  /* internal handling for packet sequence numbers */
  bool _has_seqno;
  uint16_t _seqno;

  /* node for list of all targets*/
  struct list_entity _target_node;

  /* packet buffer is currently flushed */
  bool _is_flushed;

  /* buffer for constructing the current packet */
  struct rfc5444_tlv_writer_data _pkt;

  /* number of bytes used by messages */
  size_t _bin_msgs_size;
};

```

Este tipo de estructura nos entrega algunas _callback_ para el control del proceso en la creación de los mensajes.

Al tener claro el comportamiento y la jerarquía de estas estructuras, continuamos con la configuración del _writer_.

En las variables globales para la configuración del _writer_, se definieron algunos _buffers, que son asignados al _writer_ y su uso es: 

```cpp
/*Assign the requiered buffers to process store the packet its going to be created 
Asignamos los buffers para el paquete y para el bloque de direcciones al objeto reader.*/
_writer.msg_buffer = _writer_msg_buffer;
_writer.msg_size = sizeof(_writer_msg_buffer);
_writer.addrtlv_buffer = _writer_msg_addrtlvs;
_writer.addrtlv_size = sizeof(_writer_msg_addrtlvs);

/* Define target for generating rfc5444 packets */
_writer_context.target.packet_buffer = _writer_pkt_buffer;
_writer_context.target.packet_size = sizeof(_writer_pkt_buffer);

/* Set function to send binary packet content */
_writer_context.target.sendPacket = _send_packet;
```
En el code anterior podemos ver que se están asignando recursos al _writer_, los _buffers_ que permitirán al _writer_ procesar la información y crear el paquete _RFC5444_.

En la última línea del código anterior, vemos la asignación de una funciçon de _callback_ al contenedor o _wraper_ para poder enviar el paquete.
Esta función debe ser responsabilidad del programador implementarla.

Hemos creado un objeto _aodvv2_writer_target_ que debe ser inicializado y registrado y esto se consigue con:

```cpp
    /* Initialize writer */
    rfc5444_writer_init(&_writer);

    /* Register a target (for sending messages to) in writer */
    rfc5444_writer_register_target(&_writer, &_writer_context.target);

    aodvv2_rfc5444_writer_register(&_writer, &_writer_context);
```



## 11.13  Resumen 

Algo que tmabién necesitamos agregar en la inicializacion es la posibilidad de registrarnos a la capa de red _IPV6_ y asignar una _callback_ que será ejecutada cada vez que se quiera enviar un paquete a otro nodo y no se conozca una ruta al destino.
Esta característica es algo que ofrece _RIOT-OS_ y nos permite conocer en qué momento iniciar el proceso de búsqueda de rutas sin la necesidad de buscar en tablas internas manejadas por nuestro código.

La instrucción para conocer cúando iniciar el protocolo reactivo es:

```cpp
 _netif->ipv6.route_info_cb = _route_info;
```

__route_info_ representa la _callback_ que se quiere registrar y que se ejecuta en ese caso.


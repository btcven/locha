# 11. AODVv2 protocol initialization operations.
We will explain the processes involved in the initialization and start-up of the _AODVv2_ protocol about the embedded hardware based on the _cc1312_ and the _RIOT-OS_ operating system, which make up the **Turpial** platform.

## 11.1 Initialization process.

<p>
<img src="pics/aodvv2_init.svg" alt="drawing" height="500" width="300" align="left"/>
</p>

The initialization process of the _AODV_ protocol on a network node requires following a series of steps to configure the software and hardware necessary for the correct operation of the routing protocol.

In the image we can see every function that must be executed before we can connect to the **Locha Mesh** network.

The processes executed in the initialization stage configure the tables managed within the protocol, initialize the network interface and obtain the necessary IP address to operate on the network, among other resources.

All algorithms included here are developed under the _RIOT-OS_ operating system by the **Locha** team.


<br />&nbsp;<br /> <br />&nbsp;<br /> <br />&nbsp;<br /> <br />&nbsp;<br /> <br />&nbsp;<br />



## 11.2 aodvv2_seqnum_init

As specified in chapter 8.9, the sequence number allows us to know if the route messages are obsolete or not, and keep fresh route information at all times.

The _AODVv2_seqnum_init_ function is part of some functions located inside a file called _aodvv2_seqnum.c_, with the following functions:

- aodvv2_seqnum_init(void).
- aodvv2_seqnum_inc(void).
- aodvv2_seqnum_get().

Knowing the role that the sequence number plays in the _AODV_ protocol, we see the functions do no more than what their name says:

 - Starting a counter in **1** (init), as detailed in the protocol description.

 - Read the counter and get its value. (get)

 - Increase the counter by one (inc), as detailed in the protocol, for every _RREQ_ or _RREP_ that this node generates.



### 11.2.1 Routine to handle the sequence number.

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

The library is very simple, but efficiently it solves possible data problems that can be detected when different execution threads in the application try to read and write the object that represents the sequence  number.

## 11.3 aodvv2_routing_table_init

All _AODV_ routers must maintain a set of local routes with information obtained from the route messages (_RREQ_ and _RREP_). To handle the ***Turpial*** routes table it is necessary to keep a list with such information and for this, the _aodvv2_routing_table_init_ function is in charge of creating the necessary set to store the information with the routes that can be reached through the mentioned routes table.

### 11.3.1 Scheme of an entry in the routes table.
The structure of the package must be saved in the route table corresponding to the scheme defined in the _routingtable.h_ file_:

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
The `CONFIG_AODVV2_MAX_ROUTING_ENTRIES` directive represents the number of routes that can be maintained on the node. The resources available in the hardware have this limitation.

### 11.3.2 Routine for the initialization of the local routes table.
The routine in charge of starting the route table is defined in the file `aodvv2_routingtable_init` and the code is as follows::

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


Starting the route table is equivalent to set to zero all the entries in the routes table and creating some timed variables to measure if the information obtained from the routes message is obsolete or, on the other hand, provides us a useful information for the correct operation of the _AODV_ router.

For more information about routes table, check to section 4.5 of the `draft-perkins-manet-aodvv2- document`.


## 11.4 aodv_rreqtable_init

This function initializes a table with the intention of storing information from incoming _multicast_ messages to avoid transmitting redundant messages. This table keeps a record of each incoming _RREQ_ message, so new incoming _RREQ_ messages, searching for the same destination from the same origin, are deleted without the need to retransmit them.

### 11.4.1 Scheme of an entry in the RREQ routing message table.
This is the format of the message to be stored in the routes message table (_RREQ_):

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

### 11.4.2 Routine for initializing the table of incoming Multicast messages.

This function is found in the file `aodvv2_rreqtable.c`, which also exposes functions for the handling of said table.

The code shows how some timed variables are started and the zero value assignment to that table when the _AODV_ protocol starts.

The _mutex_ variable is used to block access to the table by other processes inside the application.

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

This function gives us the possibility of storing a packet inside a buffer  which there is no route for.

When a client wants to send a packet with a message the network stack first checks its routes table (NIB). If it can't find a route for that destination, you must start a _buffering process_ to store the packet inside the node until it finds the requested route.

### 11.5.1 Packet buffer initialization routine.

The following code represents the function that is responsible for initializing the buffer where a message packet will be stored if a route to the destination is not known. The routine is inside the `aodvv2_buffer.c` file, which also offers routines for manipulating information.

```cpp
void aodvv2_buffer_init(void)
{
    memset(_buffered_pkts, 0, sizeof(_buffered_pkts));
}
```
### 11.5.2 Buffered message structure.

The message stored in the buffer must have the following structure:

```cpp
typedef struct {
    bool used;
    gnrc_pktsnip_t *pkt;
    ipv6_addr_t dst;
} buffered_pkt_t;
```


### 11.5.3 Storage buffer definition for messages.
To configure messages that we want to sent but for which there is no route, an array is created with a fixed size and limited by hardware resources.

```
static buffered_pkt_t _buffered_pkts[CONFIG_AODVV2_MAX_BUFFERED_PACKETS];

```

## 11.6 Initiate the clients table.

The clients table is a conceptual table that stores the clients of the router, and the node only recreates the _RREQ_ and _RREP_ messages from the clients registered in its table.

### 11.6.1 Routine to initialize the routes table.

The routes table is represented, like the other tables, by an array with a permanent size and limited by the hardware resources where the application is running.

The _mutex_ allows to access to the resource (clients table) without being interrupted by other processes that try to read or write the table.

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

## 11.7 Initiate the network interface.

To make possible the connection of the **Turpial** to the network, it is necessary to configure the network interface; In the function that initiates the _AODV_ protocol, an argument has been enabled in the function which allows receiving as a parameter the network interface that will be used, this to allow the possibility of registering any available interface on the device.

Accordingly, it is not the responsibility of the protocol implementation to configure the network interface to be used for connection to the wireless network, but its configuration within the _RIOT-OS_ platform is resumed in the following lines of code:

For more information visit: [https://riot-os.org/api/structnetif__t.html](https://riot-os.org/api/structnetif__t.html)

The code iterates over the available network interfaces, and if it finds a interface available and compatible with _IEEE802.15.4_, it returns a pointer with the address of that interface.

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


## 11.8 Global IP address.
When we have the network interface configured, the next step is to configure a global and unique _IPV6_ address over all the network, with the intention of avoiding duplicate IP addresses.

The code to extract the global _IPV6_ address of the device is possible having the reference of the network interface.

```cpp
/* Save our IPv6 address */
    ipv6_addr_t netif_addr;
    if (_find_netif_global_addr(&netif_addr) < 0) {
        DEBUG("aodvv2: no global address found\n");
        return -1;
    }
```

It could be the case that the node has more than one network interface. That is  why it is necessary to search for the global address in any of the interfaces available in the hardware.

### 11.8.1  _find_netif_global_addr.
For sending packets between nodes, you need to configure global IP addresses. If not, the network stack will not be able to route the packets correctly because all the **Turpial** boards will have two types of IPV6 addresses: a global and a local one.

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

## 11.9 aodvv2_client_add.

This function is used to add clients to the corresponding table. It is used for the first time inside the protocol initialization routine, due to the same device must be added as a client in its table.

### 11.9.1 Routine to add clients to the table.

This function receives as parameters the client's IP, the network prefix size and the cost of the link. The type of address passed to the function is `ipv6_addr_t` type. It stores 128-bit data. This clarification is made because _RIOT-OS_ offers other types of equivalent but not completely compatible addresses, such as `netaddr`, which offers a little more information about the owner of the IP.

The following algorithm is simple, but it highlights the main task, which is to take the IP address of the argument in order to search in the clients table and know if it should update the information when there is a similar one, otherwise a new entry must be created.

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


## 11.10 Registration in the network stack.
In order to listen to incoming messages from external nodes, we must register to a specific type of messages, in this case _UDP_ messages. _RIOT-OS_ offers the possibility of having a thread from which you can listen to incoming messages using the _UDP_ protocol just by subscribing to such messages. This mechanism is known as _IPC_ (Inter Communication process), and can be configured to run synchronously or asynchronously.

The difference between the type of configuration selected to listen to messages is that when we have messages received asynchronously, we enable a message queue which stores messages until they are read, respecting the size of the message queue.

In this fraction of code present in the initialization of the _AODV_ protocol, we are subscribing the node so that we can listen to all  _UDP_ messages from external nodes, without the need to configure _UDP_ on our own using the implementation that _RIOT-OS_ brings for handling of this type of subscriptions to external messages.

In the function `_gnrc_netreg_entry_init_pid`(& netreg, UDP_MANET_PORT, pid), pid refers to the thread ID that is designated for handling this type of message. As in the creation of any thread we need some C++ threads standard parameters. To create the thread that is in charge of waiting for the messages, we proceed to the implementation by creating a parallel execution space with other processes inside _RIOT-OS_, known as _multi-task_.

### 11.10.1 Routine for subscribing to network messages.

The term `_event_loop` in the next part of the code refers to the function that executes the necessary code to wait for incoming messages for which you have subscribed.

We must store the _PID_ of the thread created to use it as an input parameter in the _RIOT-OS_ API, and tell it what thread or process must be executed to wait for incoming _UDP_ messages.

```cpp

_pid = thread_create(_stack, sizeof(_stack), CONFIG_AODVV2_RFC5444_PRIO,
                         THREAD_CREATE_STACKTEST, _event_loop, NULL,
                         "aodvv2");
```

After creating the process in charge of executing a task during the whole life time (TTL) of the application, we must relate this process to a particular task. In this case, the task is to listen to the incoming _UDP_ messages, which can be configured using the following _RIOT-OS_ function:

```cpp
/* Register netreg */
    gnrc_netreg_entry_init_pid(&netreg, UDP_MANET_PORT, _pid);
    gnrc_netreg_register(GNRC_NETTYPE_UDP, &netreg);
```


The variable `netreg` represents the configuration for the type of message we want to subscribe to, with the following format:

```cpp
/**
 * @brief   Netreg
 */
static gnrc_netreg_entry_t netreg = GNRC_NETREG_ENTRY_INIT_PID(GNRC_NETREG_DEMUX_CTX_ALL,
                                                               KERNEL_PID_UNDEF);

```

The way of listening to _UDP_ messages can change considerably between operating systems, but in essence the same results should be obtained: the ability to listen to incoming messages.

You can find specific information about how to create a subscription to any type of network messages in: [https://riot-os.org/api/group__core__msg.html](https://riot-os.org/api/group__core__msg.html).

### 11.10.2 Example IPC subscription.

_RIOT-OS_ offers an easy way to subscribe to these types of messages.

If we see the following example, it is nothing different from what is proposed here for the register of network messages.

In this example, the main application is trying to send messages to the process that was created before.

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

### 11.10.3 Implementation of the process or IPC thread.

Now we will see how to implement the function that is responsible for listening the incoming messages, process them and review what kind of message has arrived and what should be the applied procedure, based on the type and the information contained in it.

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

The main structure of the function is formed by an infinite _while_ cycle, typical of the functions assigned to run in a separate thread or process, and a _switch_ that filters between different incoming messages, to make decisions depending on the type of message.

From now on we will refer to this function for references to receiving _UDP_ messages, due to  this is where the internal process with incoming messages really starts.

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

At this point we have almost completely configured the resources necessary to operate on the **Locha Mesh** network. It´s only needed to configure the objects in charge of encoding and decoding the _AODV_ messages. These must travel through the network encapsulated in a format known as _RFC5444_, which is described further.


## 11.11 RFC5444 Initial Configuration.

_RFC5444_ defines a message encapsulation and serialization protocol to operate in an ad-hoc mobile network, which provides the serialization and deserialization of messages and offers the possibility to read various parameters related to the packet without the need completely packet deserialization.

_RFC5444_ was created to work with the _RPL_ protocol, which has been extended to different routing protocols, due to it adapts to the needs of this type of networks. It was designed for devices with low computational calculation and low energy consumption, which makes it ideal for this application.

The API called `oonf_api` is used, which provides the necessary functions for the serialization and deserialization of the message.

Below, we describe specifically how the initial configuration of the ````reader`and`writer``` is performed, for serialization and deserialization of packets.


### 11.11.1 RFC544 Reader.

For the configuration of the `Reader` object, we must use the API `oonf_api`. We also need a `mutex` to control access to the resource by different system processes. We would have something like the following:

```cpp
/**
 * @brief   The RFC5444 packet reader context
 */
static struct rfc5444_reader _reader;
static mutex_t _reader_lock;
```
This type of data specified from the `oonf_api` library, specifically the files related to serialization and deserialization of the _RFC5444_ rule, easily identified by `rfc *****.C`.

Having the static global variable `_reader`, we proceed to its initial configuration, making use of the `mutex` created before for access control to the resource.

```cpp
/* Initialize RFC5444 reader */
    mutex_lock(&_reader_lock);
    /* Initialize reader */
    rfc5444_reader_init(&_reader);
    /* Register AODVv2 messages reader */
    aodvv2_rfc5444_reader_register(&_reader, _netif->pid);
    mutex_unlock(&_reader_lock);
```

The code above runs inside the `aodvv2_init` function, like the ones above, and it's as simple as:

 - Block the `Reader` resource.
 - Initiate the `Reader` with some special settings.
 - Register the `Reader`.
 - Unlock the `Reader` resource.

Below we explain the functions that start and register the resource.

### 11.11.2 rfc5444_reader_init.

This function can be found inside the file called `rfc5444_reader.c` and its implementation is:

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

The previous function is responsible for initializing the `Reader` with default values, allocating memory spaces, the previous code starts from the `oonf_api` and the only requirement is to pass as a parameter a reference to the previously created `Reader` object.

### 11.11.3 aodvv2_rfc5444_reader_register
The purpose of this function is register the `Reader` object as a data deserializer. To do this, the necessary _callback_ functions must be assigned so that they are triggered through internal API events for each processed read block. The main purpose of the function is to register the consumers of packets and addresses blocks. This function is found in a file called `rfc_reader.c`, developed by the **btcven/LOCHA** team.

_RFC5444_ defines a type of message called `message type-length-value`, which allows to create a list of custom entries of the `tlv` messages required in the development of the application, and this way be able to register consumers with available _callbacks_ to process the packet once it reaches the node.

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
We are going to see, step by step, each of the instructions in the function created to register the `reader` and assign the corresponding _callbacks_.

The first thing is calling the same function 4 times, but with different parameters.

The purpose is to configure the consumers of  addresses and messages for every type of packet (_RREQ_, _RREP_ and _RERR_).

#### 11.11.3.1 rfc5444_reader_add_message_consumer 1.
The purpose of this first call is to register a _callback_ to process of the _RFC5444_ package of type _RREP_.

```cpp
rfc5444_reader_add_message_consumer(reader, &_rrep_consumer,
                                        NULL, 0);
```

The function parameters are:

 - `reader`: object configured in the _AODV_ initialization function responsible for deserializing incoming packets.

 - `_rrep_consumer`: data structure defined within the `oonf_api` and that defines the following attributes:

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

For this why two _callbacks_ can be registered, which are executed for every incoming _RREP_ message.

 - **_cb_rrep_blocktlv_messagetlvs_okay**: this function is executed at the moment the message reaches the node.

 - **_cb_rrep_end_callback**: this function at the end of the reading process made on the first _callback_, enables the possibility of processing our own _callbacks_.

#### 11.11.3.2 rfc5444_reader_add_message_consumer 2.

The purpose of this function is to register the _callback_ in charge of reading the address blocks and to record the address types  we want to capture.

```cpp
rfc5444_reader_add_message_consumer(reader, &_rrep_address_consumer,
                                        _address_consumer_entries,
                                        ARRAY_SIZE(_address_consumer_entries));
```


The parameters passed to this function differ slightly from the previous configuration due to it has two additional parameters:

 - **reader**: same as the defined one in the previous section.

 - **_rrep_address_consumer**: this structure is different because it is intended to read available address blocks and the previous one is intended to read messages.


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
  - **msg_id**: identifies the type of message.
  - **addrblock_consumer**: specifies that it is a consumer of the address blocks.
  - **block_callback**: allows to register your own _callback_ for the process.
- **_address_consumer_entries**: this structure defines the types of entries for the address blocks for which the `reader` are going to register.
- **ARRAY_SIZE(_address_consumer_entries)**: defines the size of the `array` that contains the type of addresses.


We have already configured message consumers and _RREP_ addresses. We previously emphasized that the `rfc5444_reader_add_message_consumer` function is repeated 4 times in the `aodvv2_rfc5444_reader_register` function.

The first two times register the message consumer and the address block consumer for _RREP_ messages. The last two are used to configure the same users but for the _RREQ_ message type, having their own _callback_ functions to process the information.

The data structures corresponding to _RREQ_ messages are as follows:

The only difference of the data structures is: the callbacks_ that are logged and the message type, which in this case is the _RREQ_.

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

This function is also different: the block of addresses to be read, it must correspond to the _RREQ_ message type.

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

We have already configured the consumers of _RREQ_ and _RREP_ message  for the addresses and packets blocks.

The content of each _callback_ makes sense when messages are received within the _AODV_ structure, which means that this will only happen by the nature of the reactive protocol when a user wants to send a packet for which no route is known or when a packet is received which should be retransmitted.



## 11.12 RFC5444 Writer.
`writer` is the object in charge of serializing _AODV_ packets before they are sent to remote nodes. The `oonf_api` defines the files necessary to create and manipulate _RFC5444_ packets. To initialize the packet serializer known as `writer`, we need to configure some global variables inside the `aodvv2_init` file:

 - `buffers`: for storing the packets and _TLV_ messages to be processed.
 - `rfc5444_writer`: for the creation of  the _RFC5444_ packets that represents its status.
 - `_writer_context`: This object behaves like a  `wraper` that allows us to have a custom data structure to cover the needs of the **Turpial** but also making said structure compatible with the `oonf_api`.

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

- The `writer` object represents the internal status of the _RFC5444 writer_.

- `writer_context` is an object of type  `aodvv2_writer_target_t` that represents a structure used to define an _RFC5444_ packet for a specific IP destination and with a specific message type such as `rfc5444_msg_type`.

The `aodvv2_writer_target_t` object offers the possibility of creating an _RFC5444_ packet for a specific IP and of a specific message type, and which contains the _AODV_ packet that we want to serialize so that it is transmitted to external nodes. The structure is:

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

The above data structure contains:

- The interface to create the _RFC5444_ packets.
- The destination of the packet.
- The _AODV_ packet with its _payload_.
- The type of message you want to send.

The structure of the _AODV_ packet can represent an _RREP_ message or an _RREQ_ message and is:

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

The interface to generate the _RFC5444_ packets has the following format:

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

This type of structure gives us some _callbacks_ to control the process in creating the messages.

By being clear about the behavior and hierarchy of these structures, we continue with the `writer` object configuration.

In the global variables for the `writer` configuration, some _buffers_ are defined, which are assigned to the `writer` and their use is:

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

In the previous code we can see that it is allocating resources to the `writer`, to the `buffers` that allows the `writer` to process the information and create the _RFC5444_ packet.

In the last line of the previous code, we will see the assignment of a _callback_ function to the container or `wraper` to be able to send the packet. This function should be the responsibility of the developer to implement it.

We have created an `aodvv2_writer_target` object that must be initialized and registered and this is obtained with:

```cpp
    /* Initialize writer */
    rfc5444_writer_init(&_writer);

    /* Register a target (for sending messages to) in writer */
    rfc5444_writer_register_target(&_writer, &_writer_context.target);

    aodvv2_rfc5444_writer_register(&_writer, &_writer_context);
```



## 11.13  Summary.

Something that we also need to add in the initialization is the possibility of register us to the _IPV6_ network layer and assigning a _callback_ that will be executed every time you want to send a packet to another node and there is no known route to the destination. This feature is something that _RIOT-OS_ offers and allows us to know when to start the route search process without the need to search internal tables managed by our code.

The instruction to know when to start the reactive protocol is:

```cpp
 _netif->ipv6.route_info_cb = _route_info;
```

`_route_info` represents the _callback_ that is wanted to register and that is executed in that case.


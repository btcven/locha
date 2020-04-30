# 12. Generation of RREQ messages.

Now we will focus on the process to generate the route search messages and the process that triggers the necessary event.

## 12.1 Search of routes.

It is the starting point of the process to check whether or not there is a route to the destination you want to find. We will see the search for routes and which are the actions to be taken in case there is no route towards that destination.

## 12.2 Structure of a RREQ message.

The structure that defines a _RREQ_ message, like the _RREP_, is:

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
In short, it is the object that we must interpolate and then wrap it in the _RFC5444_ packet and this way send it to another node on the network.

## 12.3 Reactive search.
Reactive search begins when you don't have a route towards a destination. The network stack hands over control to a previously registered _callback_ to initiate some route search process, in this case the process is _AODV_.

In the following _callback_ registration code, ```_route_info``` is the _callback_ that we want to execute when there is no route to a destination:

```cpp
//registro de callback
_netif->ipv6.route_info_cb = _route_info,
```

The code related to ```_route_info``` is:

```cpp
static void _route_info(unsigned type, const ipv6_addr_t *ctx_addr,
                        const void *ctx)
{
    switch (type) {
        case GNRC_IPV6_NIB_ROUTE_INFO_TYPE_UNDEF:
            DEBUG("aodvv2: GNRC_IPV6_NIB_ROUTE_INFO_TYPE_UNDEF\n");
            break;

        case GNRC_IPV6_NIB_ROUTE_INFO_TYPE_RRQ:
            DEBUG("aodvv2: GNRC_IPV6_NIB_ROUTE_INFO_TYPE_RRQ\n");
            {
                gnrc_pktsnip_t *pkt = (gnrc_pktsnip_t *)ctx;
                ipv6_hdr_t *ipv6_hdr = gnrc_ipv6_get_header(pkt);

                if (aodvv2_client_find(&ipv6_hdr->src) != NULL) {
                    if (aodvv2_buffer_pkt_add(ctx_addr, pkt) == 0) {
                        DEBUG("aodvv2: finding route\n");
                        aodvv2_find_route(&ipv6_hdr->src, ctx_addr);
                    }
                    else {
                        DEBUG("aodvv2: couldn't buffer packet!\n");
                    }
                }
                else {
                    DEBUG("aodvv2: src is not our client!\n");
                }
            }
            break;

        case GNRC_IPV6_NIB_ROUTE_INFO_TYPE_RN:
            DEBUG("aodvv2: GNRC_IPV6_NIB_ROUTE_INFO_TYPE_RN\n");
            break;

        case GNRC_IPV6_NIB_ROUTE_INFO_TYPE_NSC:
            DEBUG("aodvv2: GNRC_IPV6_NIB_ROUTE_INFO_TYPE_NSC\n");
            break;

        default:
            DEBUG("aodvv2: unknown route info!\n");
            break;
    }
}
```
The _callback_ that runs on not finding a route, gives important information like:

 - Type of message.
 - Recipient's IP address.
 - Generic ```void``` type variable that in this case represents the _IPV6_ packet that wants to be transmitted, including _headers_ and _payload_.

The ```switch``` function serves as a filter to select the code to be executed depending on the type of arriving message.
We will focus on the routes request messages.

Specifically in the following part of the code, we have removed the other possible cases of the ```switch```:

```cpp

static void _route_info(unsigned type, const ipv6_addr_t *ctx_addr,
                        const void *ctx)
{
    switch (type) {
        case GNRC_IPV6_NIB_ROUTE_INFO_TYPE_RRQ:
            { 
                DEBUG("aodvv2: GNRC_IPV6_NIB_ROUTE_INFO_TYPE_RRQ\n");
                gnrc_pktsnip_t *pkt = (gnrc_pktsnip_t *)ctx;
                ipv6_hdr_t *ipv6_hdr = gnrc_ipv6_get_header(pkt);

                if (aodvv2_client_find(&ipv6_hdr->src) != NULL) {
                    if (aodvv2_buffer_pkt_add(ctx_addr, pkt) == 0) {
                        DEBUG("aodvv2: finding route\n");
                        aodvv2_find_route(&ipv6_hdr->src, ctx_addr);
                    }
                    else {
                        DEBUG("aodvv2: couldn't buffer packet!\n");
                    }
                }
                else {
                    DEBUG("aodvv2: src is not our client!\n");
                }
            }
            break;
        }
}

```

We observe that we receive a parameter called ```ctx```, of type _void_, which means that we must make a ```casting``` to the parameter to receive the correct structure with the _IPV6  headers_.

These contains information about the origin and destination of the packet, so you can start a search on the router's client table to know if the search request message should be transmitted or retransmitted. 
The difference between transmitting and retransmitting is retransmission indicates that the node trying to find a route (or the creator of the route request) is a different node than the one that is processing the message. 
If it is transmission, it means that the route request message starts at the node that is processing the message.

Each node is client of its own router client table.
If we want to transmit a message which there is no route for, a _callback_ is triggered and we should  execute the next actions in order to start the search process:

- ```cpp gnrc_pktsnip_t *pkt = (gnrc_pktsnip_t *)ctx;```: retrieving the _IPV6_ packet and storing it in a variable of its type.
- ```ipv6_hdr_t *ipv6_hdr = gnrc_ipv6_get_header(pkt);```: retrieve _IPV6 headers_.
- ``` if (aodvv2_client_find(&ipv6_hdr->src) != NULL) {```: search if the selected route requirement of a registered client (in this case the same node) that has already been added when initializing the clients table in the ```aodvv2_init```  function, arguing that each node is a client of its own table. In this case, that clients list of the node will only contain the node itself as client of that table.

```cpp
  
    if (aodvv2_client_find(&ipv6_hdr->src) != NULL) {
        if (aodvv2_buffer_pkt_add(ctx_addr, pkt) == 0) {
            DEBUG("aodvv2: finding route\n");
            aodvv2_find_route(&ipv6_hdr->src, ctx_addr);
        }
```

After searching if the client is registered in the clients table, we proceed to register the packet with the client's message in a buffer so we can retrieve it when the route is found. If that attempt to register the packet was successful, we proceed to execute the function that is responsible of starting the _AODV_ protocol and searching for a route to the requested destination.

## 12.4 aodvv2_find_route
This function is intended to start the routing protocol.

```cpp
int aodvv2_find_route(const ipv6_addr_t *orig_addr,
                      const ipv6_addr_t *target_addr)
{
    assert(orig_addr != NULL && target_addr != NULL);

    aodvv2_packet_data_t pkt;

    /* Set metric information */
    pkt.hoplimit = aodvv2_metric_max(METRIC_HOP_COUNT);
    pkt.metric_type = CONFIG_AODVV2_DEFAULT_METRIC;

    /* Set OrigNode information */
    ipv6_addr_to_netaddr(orig_addr, &pkt.orig_node.addr);
    pkt.orig_node.metric = 0;
    pkt.orig_node.seqnum = aodvv2_seqnum_get();
    aodvv2_seqnum_inc();

    /* Set TargNode information */
    ipv6_addr_to_netaddr(target_addr, &pkt.targ_node.addr);
    pkt.targ_node.metric = 0;
    pkt.targ_node.seqnum = 0;

    /* Add RREQ to rreqtable */
    aodvv2_rreqtable_add(&pkt);

    return aodvv2_send_rreq(&pkt, &ipv6_addr_all_manet_routers_link_local);
}
```

- The function receives the IP of the origin and destination as parameters.

- ```aodvv2_packet_data_t pkt```: create an  _AODV_ packet object that we want to send and that must be similar to the  implemented one in the code.

- We interpolate the object that represents the _AODV_ packet with the necessary information:


    ```cpp
    aodvv2_packet_data_t pkt;
     /* Set metric information */
    pkt.hoplimit = aodvv2_metric_max(METRIC_HOP_COUNT);
    pkt.metric_type = CONFIG_AODVV2_DEFAULT_METRIC;

    /* Set OrigNode information */
    ipv6_addr_to_netaddr(orig_addr, &pkt.orig_node.addr);
    pkt.orig_node.metric = 0;
    pkt.orig_node.seqnum = aodvv2_seqnum_get();
    aodvv2_seqnum_inc();

    /* Set TargNode information */
    ipv6_addr_to_netaddr(target_addr, &pkt.targ_node.addr);
    pkt.targ_node.metric = 0;
    pkt.targ_node.seqnum = 0;
    ```

- We added the route request message to the _RREQ_ message table to avoid forwarding redundant messages ```(aodvv2_rreqtable_add (& pkt))```. The tables in this implementation are _arrays_ for route messages and for clients. They are all processed in the same way and below we illustrate the code needed to add a route message:

  ```cpp
     void aodvv2_rreqtable_add(aodvv2_packet_data_t *packet_data)
    {
        if (_get_comparable_rreq(packet_data)) {
            return;
        }
        /*find empty rreq and fill it with packet_data */

        for (unsigned i = 0; i < ARRAY_SIZE(rreq_table); i++) {
            if (!rreq_table[i].timestamp.seconds &&
                !rreq_table[i].timestamp.microseconds) {
                rreq_table[i].origNode = packet_data->orig_node.addr;
                rreq_table[i].targNode = packet_data->targ_node.addr;
                rreq_table[i].metricType = packet_data->metric_type;
                rreq_table[i].metric = packet_data->orig_node.metric;
                rreq_table[i].seqnum = packet_data->orig_node.seqnum;
                rreq_table[i].timestamp = packet_data->timestamp;
                return;
            }
        }
    }
  ```

  The code searches the _array_: if the message has been sent before, it simply discards it; otherwise it stores it to know any redundant messages in the future.

- After having configured and stored the _AODV_ packet, we proceed to send it to the main thread within the ```aodv2.c``` file, which in addition to receiving _UDP_ messages from outside can also receive classified messages from the same application through something known as _IPC_.

- We send the packet to the main thread of the application:

    ```cpp
    aodvv2_send_rreq(&pkt, &ipv6_addr_all_manet_routers_link_local);
    ```
    Pasamos como argumentos el paquete ```AODV``` ademas de la dirección IP a la que queremos enviar el mensaje, que para este caso es la dirección de multicast especificado en el ```RFC5498``` seccion 6 y la cual tiene este valor asignado:
    ```cpp
    #define IPV6_ADDR_ALL_MANET_ROUTERS_LINK_LOCAL {{ 0xff, 0x02, 0x00, 0x00, \
                                                  0x00, 0x00, 0x00, 0x00, \
                                                  0x00, 0x00, 0x00, 0x00, \
                                                  0x00, 0x00, 0x00, 0x6d }}
    ```

The following describes the process for taking the _AODV_ packet that we have created and how we can take  that packet to the main application thread.

### 12.4.1 aodvv2_send_rreq

Now we will describe the function that the _AODV_ packet receives and which is the procedure to send it to the main thread, which was created to receive incoming _UDP_ messages but it can also be reused to send and receive messages between the different parts of the application.

```cpp
int aodvv2_send_rreq(aodvv2_packet_data_t *pkt,
                     ipv6_addr_t *next_hop)
{
    aodvv2_msg_t *msg = malloc(sizeof(aodvv2_msg_t));
    if (msg == NULL) {
        DEBUG("aodvv2: out of memory!\n");
        return -1;
    }

    /* Set destination address */
    memcpy(&msg->next_hop, next_hop, sizeof(ipv6_addr_t));

    /* Copy RREQ packet */
    memcpy(&msg->pkt, pkt, sizeof(aodvv2_packet_data_t));

    /* Prepare and send IPC message */
    msg_t ipc_msg;
    ipc_msg.content.ptr = msg;
    ipc_msg.type = AODVV2_MSG_TYPE_SEND_RREQ;

    if (msg_send(&ipc_msg, _pid) < 1) {
        DEBUG("aodvv2: couldn't send RREQ.\n");
        return -1;
    }

    return 0;
}
```

The code can be summarized in the following parts:

 - Create an ```aodvv2_msg_t```  object to receive the packet and add extra information such as the next hop address, which in this case is a _multicast_ address because a route needs to be found.

 - Create an ```msg_t```  object, which is the right format that the _IPC_ waits to be transmitted to the main application thread.

    ```cpp
    msg_t ipc_msg;
    ipc_msg.content.ptr = msg;
    ipc_msg.type = AODVV2_MSG_TYPE_SEND_RREQ;

    if (msg_send(&ipc_msg, _pid) < 1) {
        DEBUG("aodvv2: couldn't send RREQ.\n");
        return -1;
    }
    ```

    After assigning the _AODV_ packet to the ```msg_t``` object, we send it to the _IPC_ so that it is received in the application loop. It has been assigned  ```AODVV2_MSG_TYPE_SEND_RREQ```. In the same way we must retrieve it in the loop where we want to process it, because up to this point we only have an _AODV_ packet, but we still need to create the _RFC5444_ format that must contain the packet.

Veamos el siguiente paso en este flujo para conocer donde se recibe el mensaje que se acaba de enviar.

### 12.4.2 
We are going to illustrate the part of the code related to the reception of the message that was sent in the previous section to be received in this function.

What is really important is the private function named ```_send_rreq```, which is in charge of processing the package; it means that the serialization of the _AODV_ packaet and the _RFC5444_ format that is given to it. After this a _callback_ is triggered allowing the _UDP socket_ to be used to send the _multicast_ message for the route request.

```cpp
static void *_event_loop(void *arg)
{

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
        }
    }

    /* Never reached */
    return NULL;
}
```

### 12.4.3  _send_rreq

This function is the one that has been assigned as a _callback_ function, which is executed every time an _RFC5444_ packet is created. The _callback_ assignment was done within the _AODV_ initialization function, and was detailed in chapter 11.12.

```cpp
static void _send_rreq(aodvv2_packet_data_t *packet_data,
                       ipv6_addr_t *next_hop)
{
    assert(packet_data != NULL && next_hop != NULL);

    /* Make sure no other thread is using the writer right now */
    mutex_lock(&_writer_lock);

    /* Copy packet */
    memcpy(&_writer_context.packet_data, packet_data,
           sizeof(aodvv2_packet_data_t));

    _writer_context.type = RFC5444_MSGTYPE_RREQ;
    _writer_context.packet_data.hoplimit = packet_data->hoplimit;

    /* set address to which the _send_packet callback should send our RREQ */
    memcpy(&_writer_context.target_addr, next_hop, sizeof(ipv6_addr_t));

    rfc5444_writer_create_message_alltarget(&_writer, RFC5444_MSGTYPE_RREQ);
    rfc5444_writer_flush(&_writer, &_writer_context.target, false);

    mutex_unlock(&_writer_lock);
}
```

With the instruction ```_rfc5444_writer_create_message_alltarget (& writer, RFC5444_MSGTYPE_RREQ)``` the _multicast_ packet is generated and at the moment that we execute the line that calls the ```_rfc5444_writer_flush``` function (& _ writer, & writer_context.target, false)```; we call the _callback_ function that we have disposed to be executed after generating a _RFC5444_ packet, that we named ```_send_packet``` which retrieve us a packet  with _RFC5444_ format, and with its size.

The last line of code assigns the _callback_ function to send the packet.

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

Now we will see the code of the _callback_ function that sends the package (```_send_packet```).

```cpp
static void _send_packet(struct rfc5444_writer *writer,
                         struct rfc5444_writer_target *iface, void *buffer,
                         size_t length)
{
    aodvv2_writer_target_t *ctx = container_of(iface, aodvv2_writer_target_t,
                                               target);

    gnrc_pktsnip_t *payload;
    gnrc_pktsnip_t *udp;
    gnrc_pktsnip_t *ip;

    /* Generate our pktsnip with our RFC5444 message */
    payload = gnrc_pktbuf_add(NULL, buffer, length, GNRC_NETTYPE_UNDEF);
    if (payload == NULL) {
        DEBUG("aodvv2: couldn't allocate payload\n");
        return;
    }

    /* Build UDP packet */
    uint16_t port = UDP_MANET_PORT;
    udp = gnrc_udp_hdr_build(payload, port, port);
    if (udp == NULL) {
        DEBUG("aodvv2: unable to allocate UDP header\n");
        gnrc_pktbuf_release(payload);
        return;
    }

    /* Build IPv6 header */
    ip = gnrc_ipv6_hdr_build(udp, NULL, &ctx->target_addr);
    if (ip == NULL) {
        DEBUG("aodvv2: unable to allocate IPv6 header\n");
        gnrc_pktbuf_release(udp);
        return;
    }

    /* Build netif header */
    gnrc_pktsnip_t *netif_hdr = gnrc_netif_hdr_build(NULL, 0, NULL, 0);
    gnrc_netif_hdr_set_netif(netif_hdr->data, _netif);
    LL_PREPEND(ip, netif_hdr);

    /* Send packet */
    int res = gnrc_netapi_dispatch_send(GNRC_NETTYPE_UDP,
                                        GNRC_NETREG_DEMUX_CTX_ALL, ip);
    if (res < 1) {
        DEBUG("aodvv2: unable to locate UDP thread\n");
        gnrc_pktbuf_release(ip);
        return;
    }
}

```

The function is not complicated to understand if the structures of the _UDP_ and _IPV6_ packets are known.

This line of code uses a macro to obtain a reference to the structure that contains the _target_ object  ```RFC5444_writer_target```, and is contained within the ```aodvv2_writer_target_t``` structure. With the macro it´s easy get a reference to the container structure simply by passing the pointer to that field contained in the structure as an argument. For example:

  ```cpp
  aodvv2_writer_target_t *ctx = container_of(iface, aodvv2_writer_target_t,
                                               target);
    ```

    **Example**:

    ```cpp
    struct my_struct_t {
         ...; 
         something_t n; 
         ... } my_struct;
    
    &my_struct == container_of(&my_struct.n, struct my_struct_t, n).
    ```

The following lines of code aim to create the necessary _headers_ to send the packet to the network _stack_. The tasks executed are as follows, and you will find more information soon about handling network layers in _RIOT-OS_ and how to create the necessary _headers_.

- First, we must generate the _payload_ we want to send, in this case the _RFC5444_ packet, which in turn contains the _AODV_ packet.


    ```cpp
    /* Generate our pktsnip with our RFC5444 message */
    payload = gnrc_pktbuf_add(NULL, buffer, length, GNRC_NETTYPE_UNDEF);
    if (payload == NULL) {
        DEBUG("aodvv2: couldn't allocate payload\n");
        return;
    }
    ```

- build the _UDP_ packet

  ```cpp
  /* Build UDP packet */
    uint16_t port = UDP_MANET_PORT;
    udp = gnrc_udp_hdr_build(payload, port, port);
    if (udp == NULL) {
        DEBUG("aodvv2: unable to allocate UDP header\n");
        gnrc_pktbuf_release(payload);
        return;
    }
  ```

- build the _header IPV6_

  ```cpp
  /* Build IPv6 header */
    ip = gnrc_ipv6_hdr_build(udp, NULL, &ctx->target_addr);
    if (ip == NULL) {
        DEBUG("aodvv2: unable to allocate IPv6 header\n");
        gnrc_pktbuf_release(udp);
        return;
    }
   ```

- build the ```netif``` _header_

  ```cpp
  /* Build netif header */
    gnrc_pktsnip_t *netif_hdr = gnrc_netif_hdr_build(NULL, 0, NULL, 0);
    gnrc_netif_hdr_set_netif(netif_hdr->data, _netif);
    LL_PREPEND(ip, netif_hdr);
    ```

- Send the packet to the _UDP_ layer within the _RIOT-OS stack_ for issue it.

  ```cpp
  /* Send packet */
    int res = gnrc_netapi_dispatch_send(GNRC_NETTYPE_UDP,
                                        GNRC_NETREG_DEMUX_CTX_ALL, ip);
    ```

We have finished sending _RREQ_ messages, and in future chapters we will explain the details of the _RREP_ message process.




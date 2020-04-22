# 12. Generación de mensajes RREQ.
Ahora nos centraremos en el proceso para generar los mensajes de búsqueda de ruta y el proceso que dispara el evento necesario.


## 12.1 Búsqueda de rutas.

Es el punto de inicio del proceso para comprobar si existe o no una ruta hacia el destino que se quiere encontrar.
Veremos la búsqueda de rutas y cuáles son las acciones a tomar en caso de que no exista una ruta hacia ese destino.

## 12.2 Estructura de un mensaje RREQ.

La estructura que define un mensaje _RREQ_, al igual que al _RREP_, es:

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

En definitiva, es el objeto que debemos interpolar para luego envolverlo dentro del paquete _RFC5444_ y así enviarlo a otro nodo de la red.

## 12.3 Búsqueda reactiva.
La búsqueda reactiva se inicia cuando no se tiene una ruta hacia un destino. El stack de red entrega el control a una _callback_ previamente registrada para que inicie algún proceso de búsqueda de ruta, en este caso el proceso es _AODV_.

En el siguiente código de registro de _callback_,   __route_info es la _callback_ que queremos ejecutar  cuando no exista una ruta hacia un destino:

```cpp
//registro de callback
_netif->ipv6.route_info_cb = _route_info,
```

El código relacionado con __route_info_ es:

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

La callback que se ejecuta al no encontrar una ruta, entrega información importante como:

- Tipo de mensaje. 
- Dirección IP del destinatario.
- Variable genérica tipo _void_ que en este caso representa el paquete _IPV6_ que se desea transmitir, incluyendo _headers_ y _payload_.


La función _switch_ sirve de filtro para seleccionar el código a ejecutar dependiendo del tipo de mensaje que llega-
Nos centraremos en los mensajes de solicitud de ruta. Específicamente en la siguiente parte de código, hemos eliminado los otros posibles casos del _switch_:

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

Observamos que recibimos un parámetro llamado _ctx_, de tipo _void_, que significa que debemos hacer un _casting_ al parámetro para recibir la estructura correcta con los _headers IPV6_.

Éstos contienen información acerca del origen y el destino del paquete, con lo que se puede iniciar una búsqueda en la tabla interna de clientes del router para conocer si se debe transmitir o retransmitir el mensaje de solicitud de búsqueda.
La diferencia entre transmitir y retransmitir es que la retransmisión indica que el nodo que intenta buscar una ruta (o el originador del requerimiento de ruta) es un nodo diferente al que esta procesando el mensaje. Si el caso es de transmisión, quiere decir que el mensaje de requerimiento de ruta se inicia en el nodo que esta procesando el mensaje.

Cada nodo es cliente de su propia tabla de clientes del router. Si queremos transmitir un mensaje para el cual no existe ruta, se dispara una _callback_ y se deben ejecutar las siguientes acciones para poder iniciar el proceso de búsqueda:

- _cpp gnrc_pktsnip_t *pkt = (gnrc_pktsnip_t *)ctx;_: recuperar el paquete _IPV6_ y almacenarlo en una variable de su tipo.

- _ipv6_hdr_t *ipv6_hdr = gnrc_ipv6_get_header(pkt);_: recuperar los headers de _IPV6_.

- _if (aodvv2_client_find(&ipv6_hdr->src) != NULL) {_: buscar si el requerimiento de ruta proviene de un cliente registrado (en este caso el mismo nodo) que ya se agregó al inicializar la tabla de clientes dentro de la funcion _aodvv2_init_, argumentando que cada nodo es cliente de su propia tabla.
Para este caso, esa lista de clientes del nodo solo contendrá al propio nodo como cliente de esa tabla.


```cpp
  
    if (aodvv2_client_find(&ipv6_hdr->src) != NULL) {
        if (aodvv2_buffer_pkt_add(ctx_addr, pkt) == 0) {
            DEBUG("aodvv2: finding route\n");
            aodvv2_find_route(&ipv6_hdr->src, ctx_addr);
        }
```

Después de buscar si el cliente esta registrado en la tabla de clientes, procedemos a almacenar el paquete con el mensaje del cliente en un buffer para poder recuperarlo cuando la ruta sea encontrada.
Si ese intento por almacenar el paquete tuvo éxito, procedemos a ejecutar la función que se encarga de iniciar el protocolo _AODV_ y buscar una ruta para el destino solicitado.

## 12.4 aodvv2_find_route
Esta función tiene como objetivo iniciar el protocolo de routing.

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

- La función recibe como paraámetros la IP del origen y del destino.
- _aodvv2_packet_data_t pkt_: crea un objeto del tipo del paquete _AODV_ que queremos enviar y que debe ser similar al implementado en el código.
- Interpolamos el objeto que representa el paquete _AODV_ con la información necesaria:

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

- Añadimos el mensaje de requerimiento de ruta a la tabla de mensajes _RREQ_ para evitar reenviar mensajes redundantes (_aodvv2_rreqtable_add(&pkt)_). Las tablas en esta implementación son _arrays_ para mensajes de rutas y para clientes. Todas se procesan de la misma manera y a continuación ilustramos el código necesario para añadir un mensaje de ruta: 


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

  El código busca en el _array_ y si el mensaje se ha enviado antes simplemente lo desecha; de lo contrario lo almacena para conocer en el futuro cualquier mensaje redundante.

- Después de tener configurado y almacenado el paquete _AODV_, procedemos a enviarlo al _thread_ principal dentro del archivo _aodv2.c_, que además de recibir mensajes UDP del exterior también puede recibir mensajes clasificados de la misma aplicacion por medio de algo conocido como _IPC_.

- Enviamos el paquete al thread principal de la aplicación: 

    ```cpp
    aodvv2_send_rreq(&pkt, &ipv6_addr_all_manet_routers_link_local);
    ```
    Pasamos como argumentos el paquete _AODV_ y la dirección IP a la que queremos enviar el mensaje, que en este caso es la dirección de _multicast_ especificado en el _RFC5498_ (sección 6) y que tiene este valor asignado:

    ```cpp
    #define IPV6_ADDR_ALL_MANET_ROUTERS_LINK_LOCAL {{ 0xff, 0x02, 0x00, 0x00, \
                                                  0x00, 0x00, 0x00, 0x00, \
                                                  0x00, 0x00, 0x00, 0x00, \
                                                  0x00, 0x00, 0x00, 0x6d }}
    ```

A continuación describimos el proceso para tomar el paquete _AODV_ que hemos creado y cómo le hacemos llegar ese paquete al _thread_ principal de la aplicación.

### 12.4.1 aodvv2_send_rreq

Ahora describiremos la función que recibe el paquete _AODV_ y cuál es el procedimiento para enviarlo al _thread_ principal, que fue creado para recibir mensajes _UDP_ entrantes pero también puede ser reutilizado para enviar y recibir mensajes entre las distintas partes de la aplicación.

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

El código se puede resumir en las siguientes partes:

- Creación de un objeto tipo _aodvv2_msg_t_ para recibir el paquete y añadir información extra como la dirección del próximo salto, que en este caso es una dirección _multicast_ debido a que se necesita encontrar una ruta.
- Crear un objeto tipo _msg_t_, que es el formato adecuado que espera el _IPC_ para ser transmitido al _thread_ principal de la aplicación.

    ```cpp
    msg_t ipc_msg;
    ipc_msg.content.ptr = msg;
    ipc_msg.type = AODVV2_MSG_TYPE_SEND_RREQ;

    if (msg_send(&ipc_msg, _pid) < 1) {
        DEBUG("aodvv2: couldn't send RREQ.\n");
        return -1;
    }
    ```

    Después de asignar el paquete _AODV_ al objeto _msg_t_, lo enviamos al _IPC_ para que se reciba en el _loop_ de la aplicación.
    Se le ha asignado el tipo _AODVV2_MSG_TYPE_SEND_RREQ_. De esa misma manera debemos recuperarlo en el _loop_ donde se desea procesar, porque hasta este punto solo tenemos un paquete _AODV_, pero aún falta crear el formato _RFC5444_ que deberá contener el paquete.


### 12.4.2 
Vamos a ilustrar la parte del código relacionado con la recepción del mensaje que se envió en el apartado anterior para ser recibido en esta función.

Lo realmente importante es la función privada que tiene por nombre __send_rreq_, que es la encargada de procesar el paquete; es decir, la serialización del paquete _AODV_ y el formato _RFC5444_ que se le da. Después de esto se dispara una _callback_ que permite hacer uso del _socket UDP_ para enviar el mensaje _multicast_ para la solicitud de ruta.

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
Esta función es la que se ha asignado como función  _callback_, que es ejecutada cada vez que se crea un paquete _RFC5444_.
La asignación de la _callback_ se hizo dentro de la función de inicializacion del AODV, y fue detllada en el capítulo 11.12.

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

Con la instruccion _ rfc5444_writer_create_message_alltarget(&_writer, RFC5444_MSGTYPE_RREQ)_ se genera el paquete _multicast_ y en el momento que se ejecuta la línea que llama a la función _rfc5444_writer_flush(&_writer, &_writer_context.target, false)_, a continuación se llama a la función de _callback_ que hemos dispuesto para ser ejecutada después de generar un paquete _RFC5444_, que llamamos  __send_packet_ y nos devuelve el paquete con formato _RFC5444, y además su tamaño.

La ultima línea de código hace la asignación de la función de _callback_ para enviar el paquete.

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

Ahora veremos el código de la funcion de _callback_ que envía el paquete (__send_packet_).

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

Laa función no es nada complicada de entender si se conocen las estructuras de los paquetes _UDP_ e _IPV6_.

- Esta línea de código utiliza una macro para obtener una referencia a la estructura que contiene  el objeto _target_ del tipo _rfc5444_writer_target_, y está contenido dentro de la estructura  _aodvv2_writer_target_t_.
Con la macro se puede obtener fácilmente una referencia a la estructura contenedora simplemente pasando como argumento el puntero a ese campo contenido en la estructura. 
Por ejemplo:

  ```cpp
  aodvv2_writer_target_t *ctx = container_of(iface, aodvv2_writer_target_t,
                                               target);
    ```
    **Ejemplo**:
    ```cpp
    struct my_struct_t {
         ...; 
         something_t n; 
         ... } my_struct;
    
    &my_struct == container_of(&my_struct.n, struct my_struct_t, n).
    ```

Las siguientes líneas de código tienen como objetivo crear los _header_ necesarios para enviar el paquete al stack de red.
Las tareas ejecutadas son las siguientes, y se podrá encontrar próximamente información más extendida sobre el manejo de las capas de red en _RIOT-OS_ y cómo crear los _header_ necesarios.

- Primero se debe generar el _payload_ que se desea enviar, en este caso el paquete _RFC5444_, que a su vez contiene el paquete _AODV_.

    ```cpp
    /* Generate our pktsnip with our RFC5444 message */
    payload = gnrc_pktbuf_add(NULL, buffer, length, GNRC_NETTYPE_UNDEF);
    if (payload == NULL) {
        DEBUG("aodvv2: couldn't allocate payload\n");
        return;
    }
    ```

- construir el paquete _UDP_

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

- Construir el _header IPV6_

  ```cpp
  /* Build IPv6 header */
    ip = gnrc_ipv6_hdr_build(udp, NULL, &ctx->target_addr);
    if (ip == NULL) {
        DEBUG("aodvv2: unable to allocate IPv6 header\n");
        gnrc_pktbuf_release(udp);
        return;
    }
   ```

- Construir el _header_ para la _netif_

  ```cpp
  /* Build netif header */
    gnrc_pktsnip_t *netif_hdr = gnrc_netif_hdr_build(NULL, 0, NULL, 0);
    gnrc_netif_hdr_set_netif(netif_hdr->data, _netif);
    LL_PREPEND(ip, netif_hdr);
    ```

- Enviar el paquete a la capa de _UDP_ dentro del stack de _RIOT-OS_ para que lo emita.

  ```cpp
  /* Send packet */
    int res = gnrc_netapi_dispatch_send(GNRC_NETTYPE_UDP,
                                        GNRC_NETREG_DEMUX_CTX_ALL, ip);
    ```

Hasta aquí hemos finalizado con el envío de mensajes _RREQ_, y en próximos capítulos explicaremos los detalle del proceso de los mensaje _RREP_.






















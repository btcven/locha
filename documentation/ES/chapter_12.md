

# 12 Procesamiento de mensajes RREQ.
En el capitulo anterior se detallo la implementacion del codigo necesario para inicializar los recursos software y hardware necesarios para conectarnos a la red ```Locha Mesh```, en este capitulo nos centramos en detallar y explicar el proceso que se lleva a cabo para generar los mensajes de busqueda de ruta y cual es el proceso que dispara el evento necesario.

## 12.1 Busqueda de rutas

Este es el punto de inicio en el proceso que desencadena una serie de tareas para corroborar si existe o no una ruta hacia el destino que se quiere encontrar.

Veamos como se inicia ese proceso de busqueda de rutas y cuales son las acciones a tomar en caso de que no exista una ruta hacia ese destino.

Se hablo en el capitulo anterior en la seccion 11.13 acerca de registrar una callback en la capa de red, exactamente en ```IPV6```, para conocer cuando no existe una ruta a un destino, debido a que IPV6 hace uso de la tabla ```NIB``` (neighbor information base), la cual es la base de información para conocer si existe o no una ruta, dicha tabla es maneja por el stack de red y es dicho stack el que devuelve el control hacia una callback en el momento de no encontrar una ruta, con lo que desde ese punto podemos iniciar el proceso de cracion y emisión de mensajes ```RREQ``` para conocer la ruta solicitada.

## 12.2 Estructura de un mensaje RREQ

La estructura que define un mensaje RREQ al igual que al RREP es la siguiente:
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
Si revisamos la seccion ```8.11.1```, vemos que la estructura es igual a la descrita en el documento que especifica el protocolo, asi que no entraremos en detalle acerca de la composicion del contenido, Resumiendo se puede decir que es el objeto que debemos interpolar para luego envolverlo dentro del paquete ```RFC5444``` y asi enviarlo a otro nodo de la red.

## 12.3 Busqueda reactiva
La busqueda reactiva se inicia como se dijo antes cuando  no se tiene una ruta hacia un destino el stack de red entrega el control a una callback previamente registrada para que se inicie algún proceso de busqueda de ruta, en este caso el proceso es `AODV`.

Del siguiente código de registro de callback, ```_route_info``` es la callback que queremos se ejecute cuando no exista una ruta hacia un destino.
```cpp
//registro de callback
_netif->ipv6.route_info_cb = _route_info,
```

Veamos el código relacionado con ```_route_info```:

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
La callback que se ejecuta luego de no encontrar una ruta, entrega información importante, como lo  es: 
- Tipo de mensaje. 
- La dirección IP del destinatario.
- Variable generica tipo void la cual en este caso representa el paquete IPV6 que se desea transmitir, incluyendo headers y payload.


La sentencia ```switch``` sirve de filtro para seleccionar el código a ejecutar dependiendo del tipo de mensaje que llega, en este capitulo nos centraremos en los mensajes de solicitud de ruta, especificamente en la siguiente porcion de codigo, se ha retirado los otros posibles casos del ```switch```, para no complicar el asunto.

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
Revisando el código anterior observamos que recibimos un parametro llamado ```ctx```, el cual es de tipo void, con lo que significa que se debe hacer un ```casting``` al parametro para recibir la estructura correcta con los headers de IPV6.

Los headers IPV6 contienen información acerca de la fuente y destino del paquete con lo que se puede iniciar una busqueda en la tabla interna de clientes del router para conocer si se debe transmitir o retransmitir el mensaje de solicitud de busqueda, la diferencia entre transmitir y retransmitir radica en que la retransmisión indica que el nodo que intenta buscar una ruta o el originador del requerimiento de ruta es un nodo diferente al que esta procesando el mensaje, si el caso es de transmisión lo que quiere decir que el mensaje de requerimiento de ruta recién se inicia en el nodo que esta procesando el mensaje.

Como se dijo antes en el capitulo 8, cada nodo es cliente de su propia tabla de clientes del router, asi que si se desea transmitir un mensaje para el cual no existe ruta, se dispara una callback que ye hemos nombrado antes y se deben ejecutar las siguientes acciones para poder iniciar el proceso de busqueda:
- ```cpp gnrc_pktsnip_t *pkt = (gnrc_pktsnip_t *)ctx;```: Recuperar el paquete IPV6 y almacenarlo en una variable de su tipo.
- ```ipv6_hdr_t *ipv6_hdr = gnrc_ipv6_get_header(pkt);```: recuperar los headers de IPV6.
- ``` if (aodvv2_client_find(&ipv6_hdr->src) != NULL) {```: Buscar si el requerimiento de ruta proviene de un cliente registrado, en este caso el mismo nodo, ya que se agrego al inicializar la tabla de clientes dentro de la funcion ```aodvv2_init```, argumentando que cada nodo en cliente de su propia tabla.Para este caso esa lista de clientes del nodo solo contendra al propio nodo como cliente de esa tabla.
- 
```cpp
  
    if (aodvv2_client_find(&ipv6_hdr->src) != NULL) {
        if (aodvv2_buffer_pkt_add(ctx_addr, pkt) == 0) {
            DEBUG("aodvv2: finding route\n");
            aodvv2_find_route(&ipv6_hdr->src, ctx_addr);
        }
```

Luego de buscar si el cliente esta registrado en la tabla de clientes, como dijimos para este caso siempre sera el mismo nodo, procedemos a almacenar el paquete con el mensaje del cliente en un buffer, para poder recuperarlo cuando la ruta sea encontrada, luego si ese intento por almacenar el paquete fue exitoso, procedemos a ejecutar la funcion que se encarga de iniciar el protocolo ```AODV``` y buscar una ruta para el destino solicitado.

## 12.4 aodvv2_find_route
Esta funcion tiene como tarea iniciar el protocolo de routing, veamos su contenido y hagamos una revision detallada de las lineas contenidas en ella.

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

- La funcion recibe como parametros la IP del origen y del destino.
- ```aodvv2_packet_data_t pkt```: Creamos un objeto del tipo del paquete AODV, el cual queremos enviar y en el capitulo 8.13.4 podemos ver la composicion o contenido del mensaje, que debe ser similar al implementado en el código.
- Interpolamos el objeto que representa el paquete AODV con la información necesaria:
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
- Agregar el mensaje dde requerimiento de ruta a la tabla de mensajes RREQ para evitar reenviar mensajes redundantes ```aodvv2_rreqtable_add(&pkt);```, las tablas aquí en esta implementación son arrays, me refiero a las tablas para rutas, para mensajes de rutas, para clientes, todas se procesan de la misma manera, a continuacion se ilustra el código necesario para agregar un mensaje de ruta, pero no siempre haremos referencia al código para agregar , eliminar, o buscar en una tabla, ya que son algoritmos triviales que no merecen un espacio en este documento.
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
  El código busca en el array si el mensaje se ha enviado antes simplemente lo desecha de lo contrario lo almacena para conocer en el futuro cualquier mensaje redundante.
- Despues de tener nuestro paquete ```AODV```, configurado y almacenado, procedemos a enviarlo al ciclo al thread principal dentro del archivo ```aodv2.c```, el cual ademas de recibir mensajes UDP del exterior, también puede recibir mensajes bien clasificados de la misma aplicaion por medio de algo conocido como IPC de lo que se hablo en el capitulo anterior. 
- Enviamos el paquete al thread principal de la aplicacion: 
    ```cpp
    aodvv2_send_rreq(&pkt, &ipv6_addr_all_manet_routers_link_local);
    ```
    Pasamos como argumentos el paquete ```AODV``` ademas de la direccion IP a la que queremos enviar el mensaje, que para este caso es la direccion de multicas especificado en el ```RFC5498``` seccion 6 y la cual tiene este valor asignado:
    ```cpp
    #define IPV6_ADDR_ALL_MANET_ROUTERS_LINK_LOCAL {{ 0xff, 0x02, 0x00, 0x00, \
                                                  0x00, 0x00, 0x00, 0x00, \
                                                  0x00, 0x00, 0x00, 0x00, \
                                                  0x00, 0x00, 0x00, 0x6d }}
    ```
A continuacion describimos el proceso para tomar el paquete ```AODV``` que hemos creado y como le hacemos llegar ese paquete al Thread principal de la aplicacion.

### 12.4.1 aodvv2_send_rreq

En esta seccion describimos la funcion que recibe el paquete ```AODV``` y cual es el procedimiento para enviarlo al thread principal el cual fue creado para recibir mensajes UDP entrantes, pero también puede ser reutilizado para enviar recibir mensajes entre las distintas partes de la aplicación.

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






# 12. Tabla de operaciones para los mensajes de ruta multicast.

## 12.1 Búsqueda de entradas en la tabla de rutas.

Encontrar una entrada en la tabla de rutas:
```cpp
/* Find an entry in the RteMsg table matching the given 
 message's msg-type, OrigAddr, TargAddr, MetricType */

fetch_rte_msg_table_entry (rteMsg)
{
 foreach (entry in RteMsgTable)
 {
 if (entry.msg-type == rteMsg.msg-type 
 AND entry.OrigAddr == rteMsg.OrigAddr 
 AND entry.TargAddr == rteMsg.TargAddr 
 AND entry.MetricType == rteMsg.MetricType)
 return entry;
 }
 return NULL;
}
```

## 12.2 Actualización de  la tabla de rutas.

Actualizar la tabla de rutas _multicast_  en función del mensaje de ruta recibido: este mensaje es "verdadero" si **SeqNum** fue creado o actualizado.

```cpp
/* Update the multicast route message suppression table based on the 
 received RteMsg, return true if it was created or the SeqNum was 
 updated (i.e. it needs to be regenerated) */

update_rte_msg_table(rteMsg)
{
    // ToDo 
}
```
Buscar una entrada comparable:

```cpp
 /* search for a comparable entry */
 entry = Fetch_Rte_Msg_Table_Entry(rteMsg);
 ```

Si no hay una entrada, se cre una:
```cpp
 /* if there is none, create one */
 if (entry does not exist)
 {
    entry.MessageType = rteMsg.msg_type;
    entry.OrigAddr = rteMsg.OrigAddr;
    entry.TargAddr = rteMsg.TargAddr;
    entry.OrigSeqNum = rteMsg.origSeqNum; // (if present)
    entry.TargSeqNum = rteMsg.targSeqNum; // (if present)
    entry.MetricType = rteMsg.MetricType; 
    entry.Metric = rteMsg.OrigMetric; // (for RREQ)
 or rteMsg.TargMetric; // (for RREP) 
    entry.Timestamp = CurrentTime;
    return TRUE;
 }
```
Si la entrada actual está obsoleta:
```cpp
 /* if current entry is stale */
 if (
 (rteMsg.msg-type == RREQ AND entry.OrigSeqNum < rteMsg.OrigSeqNum)
 OR
 (rteMsg.msg-type == RREP AND entry.TargSeqNum < rteMsg.TargSeqNum))
 {
 entry.OrigSeqNum = rteMsg.OrigSeqNum; // (if present)
 entry.TargSeqNum = rteMsg.TargSeqNum; // (if present)
 entry.Timestamp = CurrentTime;
 return TRUE;
 }
```

Si el mensaje de ruta recibido está obsoleto:
```cpp
 /* if received rteMsg is stale */
 if ((rteMsg.msg-type == RREQ AND entry.OrigSeqNum > rteMsg.OrigSeqNum)
 OR
 (rteMsg.msg-type == RREP AND entry.TargSeqNum > rteMsg.TargSeqNum))
 {
    entry.Timestamp = CurrentTime;
    return FALSE;
 }
```

```cpp
 /* if same SeqNum but rteMsg has lower metric */
 if (entry.Metric > rteMsg.Metric)
 {
    entry.Metric = rteMsg.Metric;
    entry.Timestamp = CurrentTime;
    return FALSE;
}
```
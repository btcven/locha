# 11. Operaciones generales del protocolo AODVv2
A continuación se muestran las operaciones involucradas en el protocolo AODVv2, dichas operaciones envuelven comparar mensajes entrantes, actualizar tabla de rutas locales entre otras.


## 11.1 Operaciones de ruteo
Existen diferente funciones involucradas en el proceso de búsqueda de rutas delas cuales hablaremos de cada algoritmo en detalle. Las funciones que implementaremos serán las siguientes:

- check_route_state.
- process_routing_info.
- fetch_route_table_entry.
- update_route_table_entry.
- ceeate_route_table_entry.
- loop_free.

Primero que todo definimos los siguientes terminos utilizados en los algoritmos.

- **rteMsg**: Denota mensaje de ruta recibido, puede ser un **RREQ** o un **RREP**.
- **advRte**: Denota la ruta definida dentro del mensaje de ruta (RREQ o RREP). 
- **localRoute**: Denota una ruta local existente dentro de la tabla de rutas, la cual coincide con **address**,**prefix_length**,**metric_type** y **seqNoRtr** del advRte.


## 11.2 Process_Routing_Info

Cada mensaje de ruta recibido contiene una ruta y en consecuencia se evalúa para comprobar cualquier mejora. Tenga en cuenta que un mensjae **RREQ** contiene una ruta a su origen, mientras un mensaje de respuesta **RREP** contiene una ruta a su destino.

Por lo tanto, como las rutas están identificadas por sus destinos, en e primer caso el destino de la ruta es el creador del mensaje y en este ultimo, es el destino del mensaje.

Tenga en cuenta que decimos que un enrutador es mejor que otros si tiene un numero de secuencia mayor que otros o un numero de secuencia igual, mientras que su costo por ejemplo, conteo de saltos, es menor que otros.

La tabla de rutas se debe actualizar ante algunas de las siguientes condiciones:

- **No existe una ruta en la tabla de rutas**: la ruta debe ser adicionada a la tabla de rutas
- **Todas las rutas existentes en la tabla de rutas estan en estado no confirmado**, es decir, sus proximos saltos no estan confirmados: la rura es agregada a la tabla de rutas 
- **La ruta entrante es mejor que la ruta valida existente**: si el próximo salto de la ruta es confirmado, se actualiza la ruta valida existente con la ruta entrante. En otro caso se agrega la ruta a la tabla de rutas, ya que se podría confirmar en el futuro, y en consecuencia reemplazar la ruta existente.
- Si la ruta entrante es mejor que la ruta existente "invalida", esta ruta invalida puede ser reemplazada con la ruta entrante.

```cpp
/* Compare incoming route information to stored route, and if better, 
use to update stored route. */

process_routing_info (advRte)
{
 rte = fetch_route_table_entry (advRte);
 if (!rte exists)
 {
 rte = create_route_table_entry(advRte);
 return rte;
 }

 if (AdvRte.SeqNum > rte.reqNum /* stored route is stale */
 OR
 (AdvRte.SeqNum == rte.reqNum /* same SeqNum */
 AND 
 ((rte.state == Invalid AND LoopFree(advRte, rte))
 /* advRte can repair stored */
 OR AdvRte.Cost < rte.Metric))) /* advRte is better */
 {
 if (advRte is from a RREQ)
 rte = create_route_table_entry(advRte);
 else
 update_route_table_entry (rte, advRte);
 }
 return rte;
}
```

<img src="imple_pic/process_routing_info.png" alt="drawing" height="650" width="800" align="center"/>

## 11.3 Create_Route_Table_Entry

```cpp
/* Create a route table entry from address and prefix length */

create_route_table_entry (address, prefixLength, seqNum, metricType)
{
 rte = allocate_memory();
 rte.Address = address;
 rte.PrefixLength = prefixLength;
 rte.SeqNum = seqNum;
 rte.MetricType = metricType;
}


/* Create a route table entry from the advertised route */

create_route_table_entry(advRte)
{
 rte = allocate_memory();

 rte.Address = advRte.Address;
 if (advRte.PrefixLength)
 rte.PrefixLength = advRte.PrefixLength;
 else
 rte.PrefixLength = maxPrefixLenForAddressFamily;

 rte.SeqNum = advRte.SeqNum;
 rte.NextHop = advRte.NextHopIp;
 rte.NextHopInterface = advRte.NextHopIntf;
 rte.LastUsed = CurrentTime;
 rte.LastSeqNumUpdate = CurrentTime;
 if (validityTime)
 rte.ExpirationTime = CurrentTime + advRte.ValidityTime;
 else
 rte.ExpirationTime = INFINITY_TIME;
 rte.MetricType = advRte.MetricType;
 rte.Metric = advRte.Metric;
 rte.State = Idle (if advRte is from RREP);
 or Unconfirmed (if advRte is from RREQ);
}
```

<img src="imple_pic/Route_table_entry.png" alt="drawing" height="670" width="710" align="center"/>

## 11.4 update_route_table_entry

```cpp
/* Update a route table entry using AdvRte in received RteMsg */

update_route_table_entry (rte, advRte);
{
 rte.SeqNum = advRte.SeqNum;
 rte.NextHop = advRte.NextHopIp;
 rte.NextHopInterface = advRte.NextHopIntf;
 rte.LastUsed = CurrentTime;
 rte.LastSeqNumUpdate = CurrentTime;
 if (validityTime)
 rte.ExpirationTime = CurrentTime + advRte.ValidityTime;
 else
 rte.ExpirationTime = INFINITY_TIME;

 rte.Metric = advRte.Cost;
 if (rte.State == Invalid)
 rte.State = Idle (if advRte is from RREP);
 or Unconfirmed (if advRte is from RREQ);
}
```

<img src="imple_pic/update_route_table_entry.png" alt="drawing" height="670" width="710" align="center"/>



## 11.5 fetch_route_table_entry
```cpp
/* Lookup a route table entry matching an advertised route */

fetch_route_table_entry (advRte)
{
 foreach (rteTableEntry in rteTable)
 {
 if (rteTableEntry.Address == advRte.Address 
 AND rteTableEntry.MetricType == advRte.MetricType)
 return rteTableEntry;
 }
 return null;
}

/* Lookup a route table entry matching address and metric type */

fetch_route_table_entry (destination, metricType)
{
 foreach (rteTableEntry in rteTable)
 {
 if (rteTableEntry.Address == destination 
 AND rteTableEntry.MetricType == metricType)
 return rteTableEntry;
 }
 return null;
}
```

<img src="imple_pic/fetch_route_table_entry.png" alt="drawing" height="470" width="510" align="center"/>
<br>
<br>



## 11.6 loop_free

```cpp
loop_free(advRte, rte)
 {
 if (advRte.Cost <= rte.Cost)
 return TRUE;
 else
 return FALSE;
 }
```


## 11.7 check_route_state

Actualice el estado de la entrada de ruta en función de los tiempos de espera. Si la ruta se puede usar para reenviar un paquete.

```cpp
check_route_state(route)
{
 if (CurrentTime > route.ExpirationTime)
 route.State = Invalid;
 if ((CurrentTime - route.LastUsed > ACTIVE_INTERVAL + MAX_IDLETIME)
 AND (route.State != Unconfirmed)
 AND (route.ExpirationTime == INFINITY_TIME)) //not a timed route
 route.State = Invalid;
 if ((CurrentTime - route.LastUsed > ACTIVE_INTERVAL)
 AND (route.State != Unconfirmed)
 AND (route.ExpirationTime == INFINITY_TIME)) //not a timed route
 route.State = Idle;
 if ((CurrentTime - route.LastSeqNumUpdate > MAX_SEQNUM_LIFETIME)
 AND (route.State == Invalid OR route.State == Unconfirmed))
 /* remove route from route table */
 if ((CurrentTime - route.LastSeqNumUpdate > MAX_SEQNUM_LIFETIME)
 AND (route.State != Invalid)
 route.SeqNum = 0;

 if (route still exists AND route.State != Invalid
 AND Route.State != Unconfirmed)
 return TRUE;
 else
 return FALSE;
}
```
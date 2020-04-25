
# 10. Modelado de AODVv2.

Describiremos el modelo del protocolo desde el punto de vista de un nodo llamado **H**.

## 10.1  Estructuras de datos.
Un nodo mantiene una tabla de rutas clasificada por nodos. 

- La ruta a un nodo puede estar indefinida, lo que denotamos con ⊥.
- Si se define, una ruta a un nodo es un par: (n, e) donde
  - **n** es el siguiente salto al nodo. 
  - **e** es su entrada de ruta. 
- Una entrada es de la forma **(s,h,x)** donde
  - **s**: es el número de secuencia.
  - **h**: es el _hopcount_ (o coste de ruta).
  - **x**: es el estado de la ruta, que puede ser:
    - Unconfirmed 
    - Idle
    - Active
    - Invalid


## 10.2 Mensajes.

El protocolo tiene tres tipos de mensajes :
- RREQ (Route Request)
- RREP (Route Reply)
- RERR (Route Error)

Cada mensaje tiene los siguientes componentes
- **h**: hopcount.
- **tlv** = (sO, sT): número de secuencia para el origen, y destino, este último posiblemente indefinido.
- **(O, T)**: el par de origen y destino.

Por ejemplo podríamos escribir un mensaje como sigue:

```
RREQ(h,(sO,sT),(O,T)).
```

## 10.3 Estado inicial.

En el estado inicial un nodo no tiene definidas las rutas a origen y destino y el número de secuencia.

## 10.4 Acciones del protocolo.

 Enumeraremos las acciones tomadas durante la operación normal del protocolo. Las acciones son atómicas pero pueden ocurrir en cualquier momento. En el protocolo, las acciones como una ruta de expiración, están basadas en temporizadores, para garantizar que no sucedan con demasiada frecuencia. 
 
 La notación **y>>x** expresa que la ruta en el mensaje de ruta es preferible a la entrada en la tabla de ruta **x**. En la descripción del protocolo _AODVv2_, esto es cierto si 
 - 1. ```ys > xs```
 - 2. si ```ys = xs```, y ```y.h + 1 < xh```, o 
 - **x** está en estado roto y ```y.h + 1 ≤ xh```.

 
## 10.5 Modelo para crear un Route Request.

**rreq-gen(T)**: El siguiente algoritmo genera un _RREQ_ al nodo T.
```
true ==>
 let msg = RREQ(h=0, (sO=H.seq+1, sT=H.route[T].e.s), (H,T)) in
 H.seq = H.seq+1;
 multicast(msg)
```

## 10.6 Modelo para recibir un Route Request.

***rreq-recv (RREQ(m),K)**: Esta acción procesa un mensaje Route Request .
```
m = (h,(sO, sT),(O, T)) de un vecino K. 
```
Está protegido por la condición de que una ruta en el nodo **m** es mejor que una ruta al origen en el nodo **H**. 
```
(m.sO,m.h,Active) >> H.route[O].e ==> // m tiene una mejor ruta al origen 
// update the origin route
H.route[O] = (K,(m.sO,m.h+1),Active);

// propagate or reply as appropriate
if (H=T) then // H is the target node: reply with RREP
 let reply = RREP(h=0,(sO=m.sO,sT=H.seq+1), (O,T)) in
 H.seq = H.seq+1; // update local sequence number
 unicast(reply, K) // send only to K
else // H is an intermediate node: propagate
 let msg = RREQ(m.h+1, m.tlv,(O,T)) in
 multicast(msg) // send to all neighbors
endif
```

## 10.7 Modelo para recibir un Route Reply.

**rrep-recv(RREP(m),K)**: Esta acción procesa un mensaje entrante de tipo **Route Reply (RREP)**.
**messagem=(h,(sO, sT),(O, T))** de un vecino K que contiene una mejor ruta al destino.
```
(m.sT,m.h,Active) >> H.route[T].e ==> // m has better route to the target
// update the target 
routeH.route[T] = (K,(m.sT,m.h+1),Active);// propagate as appropriate
if (H = O) then // H is the origin node: do nothing
skip
else // H is an intermediate node
 if (H.route[O] is defined) then // propagate RREP
 let replymsg = RREP(m.h+1, m.tlv, (O,T)) in
 unicast(replymsg, H.route[O].n)
 else // generate error RERR
 let errormsg = RERR(h=0,tlv=(_,_)) in
 unicast(errormsg,K)
 endif
```

## 10.8 Modelo para recibir un Route Error.

**rerr-recv(RERR (m), K)**: Esta acción procesa un mensaje de error (RERR) del vecino K. Marca cualquier ruta que pase por K como rota y comunica el error. Esto es más permisivo que el protocolo al marcar rutas como rotas. En el protocolo  hay otros campos en el mensaje _RERR_ que se pueden usar para distinguir si el mensaje de error de K pertenece a una ruta de origen o destino.

```
true ==>
 for all nodes w:
 if (H.route[w].n = K) then
 H.route[w].e.x = Broken; // mark route as broken.
 multicast(RERR(m)) // propagate RERR to all neighbors
 endif
```


## 10.9 Modelo para remover un link.
**remove-link(H,K)**: Marca cualquier ruta a través de K como interrumpida y envía mensajes ERR como consecuencia.
```
true ==>
 for all nodes w:
 if (H.route[w].n = K) then
 H.route[w].e.x = Broken; // mark route as broken.
 multicast(RERR(m)) // propagate RERR to all neighbors
 endif
```
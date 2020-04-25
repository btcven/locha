
# 10. AODVv2 modeling.

We will describe the protocol model from the point of view of a node called **H**.

## 10.1  Data structures.
A node maintains a table of routes classified by nodes.

- The path to a node can be undefined, which we denote with ⊥.
- If defined, a path to a node is a pair (n, e) where:
  - **n** is the next jump to the node.
  - **e** is its route entry.
- An entry is of the form **(s,h,x)** where:
  - **s**: is the sequence number.
  - **h**: is the _hopcount_ (or route cost).
  - **x**: is the staus of the route, that can be:
    - Unconfirmed 
    - Idle
    - Active
    - Invalid


## 10.2 Messages.

The protocol has three types of messages:
- RREQ (Route Request)
- RREP (Route Reply)
- RERR (Route Error)

Each message contains the following:
- **h**: hopcount.
- **tlv** = (sO, sT): sequence number for the source, and destination, and the last one possibly undefined.
- **(O, T)**: the origin and destination pair.

For example we could write a message as follows:

```
RREQ(h,(sO,sT),(O,T)).
```

## 10.3 Initial state.

In the initial state, a node doesn´t have defined the source and destination routes and the sequence number.

## 10.4 Protocol actions.

We will list the actions taken during the normal operation of the protocol. Actions are atomic but can change at any time. In the protocol, actions such as an expiration path are based on timers, to guarantee they do not happen too often.
 
The notation **y >> x** expresses that the route in the route message is preferable to the entry in the **x** route table. In the _AODVv2_ protocol description, this is true if:

 - 1. ```ys > xs```
 - 2. if ```ys = xs```, and ```y.h + 1 < xh```, or 
 - **x** it's in a broken state and ```y.h + 1 ≤ xh```.

 
## 10.5 Model to create a Route Request.

**rreq-gen(T)**: The following algorithm generates an _RREQ_ to node T.

```
true ==>
 let msg = RREQ(h=0, (sO=H.seq+1, sT=H.route[T].e.s), (H,T)) in
 H.seq = H.seq+1;
 multicast(msg)
```

## 10.6 Model to receive a Route Request.

***rreq-recv (RREQ(m),K)**: This action processes a Route Request message.
```
m = (h,(sO, sT),(O, T)) from neighbor K. 
```
It is protected by the condition that a route at node **m** is better than a route to origin at node **H**.

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

## 10.7 Model to receive a Route Reply.

**rrep-recv(RREP(m),K)**: This action processes an incoming message of type **Route Reply (RREP)**.
**messagem=(h,(sO, sT),(O, T))** from a neighbor **K** that contains a better route to the destination.
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

## 10.8 Model to receive a Route Error.

**rerr-recv(RERR (m), K)**: This action processes an error message (_RERR_) from neighbor **K**. It marks any route that goes through **K** as broken and reports the error. This is more permissive than protocol when marking routes as broken. There are other fields in the protocol in the _RERR_ message that can be used to distinguish whether the **K** error message belongs to a origin or destination route.

```
true ==>
 for all nodes w:
 if (H.route[w].n = K) then
 H.route[w].e.x = Broken; // mark route as broken.
 multicast(RERR(m)) // propagate RERR to all neighbors
 endif
```


## 10.9 Model to remove link.
**remove-link(H,K)**: it marks any route through **K** as interrupted and send _RERR_ messages as consequence.

```
true ==>
 for all nodes w:
 if (H.route[w].n = K) then
 H.route[w].e.x = Broken; // mark route as broken.
 multicast(RERR(m)) // propagate RERR to all neighbors
 endif
```
# 3. Proposed modifications

The goal is to implement the current version of the AODVv2 with no major changes in an embedded system with limited resources to analyze the behavior of the network while working the routing protocol. It's also part of the goal to build the hardware to obtain and analyze the results in real environments.

If the protocol's working parameters change, it may improve or downgrade the performance of the nodes in the network. For instance, the TTL (Time To Live), which indicates the lifespan of a route stored inside a router, is a very important parameter to take into account that may have a strong impact over the network performance, and could change depending on the size of the network. [2]

# Consistent Hashing

Hashing: Arbitary length input -> Fixed length output

Mod Hashing: hash % m

If m is not fixed, then its a problem

Practical Use Cases:
 - Load balancing App servers
 - Horizontal Sharding (Multiple DB servers, each server having unique data)

When m value is dynamic,rebalancing is needed. In this case, normal hashing doesn't work. Here use Consistent Hashing. 

Consistent Hashing: Add or removal of nodes, rebalancing happens. How much rebalancing is needed?
 - If n is number of active nodes, then (1/n) % of no. of keys k on avg
 - Its very less rebalancing compared to normal hashing

How Consistent Hashing happens:
 - Virtual ring with size say 9(0 - 8).
 - Si = hash(server_i)%hash Si => Server i
 - Ki = hash(key_i)%hash key_i => key that needs to find a server.
                         S1 0
                       /   \
                  8  K2      K4 1
                    /         \
                7 S3          K5 3
                     \         /
                    6 K1      S2 4
                        \    /
                          K3 5
 - Think above diamond as circle and indexed from 0 - 8 (S1 to K5). Observe index 2 is missing
 - Now Look in clock wise direction to find the server for Ki
 - i.e K4, K5 => S2, K1, K3 => S3, k2 => S1
 - Now If I add S4 and say its hash is at index 2,
                         S1 0
                       /   \
                  8  K2      K4 1
                    /         \
                   /           S4 2
                  /            /
                7 S3          K5 3
                  \         /
                  6 K1      S2 4
                     \    /
                       K3 5
 - Now distribution is K4 => S4, K5=>S2, K1, K3 => S3, K2 => S1. Not much rebalacing was needed.
 - Disadvantage: If hash of servers are sequential, then all keys go to same server (Not fulfilling our purpose)
 - Sol: Create virtual objects and replicate servers at diff points
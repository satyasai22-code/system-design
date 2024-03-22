# Load Balancers

- A machine that runs a **REVERSE PROXY software**
- Goal: Distribute the requests between  mutliple servers that host the actual application
- For each request it picks one of the servers based on its strategy
- Most common strategy is round robin
- Makes app more resilent and scalable(see Advantages section)

### Strategies
- Round Robin
- Least Connections
- Resource based
- Weighted variants of the above
- Random 
  etc..

### Types
#### 1. Layer 4
- Transport Layer
- Has acces to TCP, UDP, IP, Port
#### 1. Layer 7
- Application Layer
- Layer 4 + HTTP headers, cookies, payload
- May make decisions based on data in  the request
- More complex than Layer 4

 Its a tradeoff between Simplicity(L4) vs Robustness(L7)

 Eg: NGINX, HAPROXY, TRAEFIK

### Advantages
- Resiliency (If one of the servers fail, then LB sends request to other servers)
- Scalability (If load increases, then coz of LBs we can add more servers(Horizontal Scaling))

Links: https://www.nginx.com/resources/glossary/layer-4-load-balancing/
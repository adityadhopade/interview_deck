# AWS Interview Questions

---

## EC2

---

### What are the family of instance types in EC2?

- They are calssified into types as folows

- Majorly classified into the following as

- General Purpose (Starts with M) - eg M5a, M5ad, M6a
- Memory Optimized (Starts with R(Signifies RAM)) - eg C5, C5a, C5ad
- Compute Optimized (Starts with C(Signifies Compute)) - eg R5, R5a, R5ad

- Further classified into

- Storage Optimized
- High Performance Computing
- Accelarted Computing

### What are the Burstable Instances ?

- The t2, t3, t3a They provide best oprice for the performance ratio in the EC2 Instance Types.
- The t4g uses AMazons own processor `Gravitron`

### Does EC2 get the Public Address by Default ?

- `NO`, by default EC2 only gets Private IP Address

---

## Networking in AWS

---

### What is CIDR ?

- Classless Inter-Domain Routing(Widely Used than Classful Routing)
- It is used to provider the IP Address Allocation
- eg `172.16.0.0/16`

```
172.16.0.0 ==> It is the BASE IP
16 => Is the Subnet Mask
```

### How to calculate the no. of IP Addresses Available ?

- eg `172.16.0.0/16` => It is Divided into twor parts they are `Network Address` and `Host Address`
- `Host Address` - Representst he No. of IP Address taht can be allocated
- Calculated as Follows

```
1 Byte = 8 Bits
172.16.0.0/16 ==> It will be divided as 11111111.11111111.00000000.00000000
Masking is done form the right to left ; we have to cover 16 Bits.
Each Byte will contain about `256 Addresses` => From `0 to 255`

Host Addresses ==> Will cover upto 0.0
N/W Addresses ==> Will cover upto 172.16

=> For Host Addresses ==> 0.0 => 256 * 256 ==> 65536 Addresses - 2(As 2 IP;s are allocated for the Network Address and Broadcast Address)

No. of IP Addresses can also be derived from the Formula =>` [2 ^ (32 - (Subnet mask)) - 2]`
```

- No. of IP Addresses can also be derived from the Formula =>` [2 ^ (32 - (Subnet mask)) - 2]`

eg 172.8.10.12/15
=> Using [2 ^ (32 - (Subnet mask)) - 2] = [2 ^ (32 - (15)) - 2] = 131070 IP Addreses can be allocated

### What is VPC?

- It is the Private Cloud within our cloud
- It conist of the `Public` and `Private` Subnet.

- The `Public Subnet` attaches to the `Internet Gatway`; Generally the `Bastion Host(Jump Server)` or `Load Balancers` are kept inside the Public Subnet.
- The `Private Subnet` attaches to the `Network Translation Gateway`; Generally the Applications are stored inside the Private Subnet (Least Privileage Access); The Internet can only get accessed with the help of NAT Gateway ==> NAT Gateway connects to the Internete Gatway and gets reply back from the outer world.

### How Public and Private Subnet are from Each Other ?

- Public Subnet attaches to the Internet gateway and Private Subnet to the NAT Gateway,

### What is incoming and outgoing traffic called in terms of the VPC?

- Incoming Traffic ==> `Ingress traffic / Inbound Traffic`
- Outgoing Traffic ==. `Egress Traffic / OutBound Traffic`

### How app inside the Private Subnet connects to the Internet ?

![alt text](Private_Subnet.png)

### NACL(Network Access Control List) VS SG (Security Groups)

- `NACL` they are the set of the Rules that are acting at the `Subnet Level`
- In `NACL` the `Inbound Rules` can set to be Something else and `OutBound Rules` can be set to `Blocked`
- `NACL` are set for the `StateLess Routing`
- NACL the Rules ranges from the `[1 to 65535] [1 with the Highest Priority]`.

- `SG` they are set of Rules that are acting at the `Instance Level`
- IN `SG` once the `Inbound Traffic` is `allowed`; then the `OutBound Traffic` is `allowed by Default`.
- `SG` are set for the `Stateful Routing`.

---

## LOAD BALANCERS

---

### What is the Difference Between the High Avalalability [HA] vs Fault Tolerance ?

- `HA` deals with the `scalability of the application` and making it available for the users even if one instance goes down.
- Fault Tolerance deals with adding the `Application in the Multiple Avalability Zones in the Region`; avoiding to become the `Single Point Failure`.

### What is SSL Termintaion?

- Its relative whenever we are using the `HTTPS Traffic`
- When serving the HTTPS Traffic we need to add the SSL Certifcates for proper encrypted communication.
  ![alt text](<Load balancers.png>)

### OSI Refrence Model Layers

- `Layer 7` - `Application Layer` `(ALB Operates at this level)` - Users can access thius layer; Can understand HTTP, HTTPS, PATH, Host Based Routings
- `Layer 6` - `Presentetion Layer` - Gives out the format in whihc the data can be represented eg zip, JPEG.
- `Layer 5` - `Session Layer` - Gives Sessions Created or Not and Valid Sessions or Not ?
- `Layer 4` - `Transport Layer` `(NLB Operates as this Level)` - Takes care for managing the Packet using the `TCP` or `UDP`
- `Layer 3` - `Network Layer` - Finds Shortest Route to Transmit Data.
- `Layer 2` - `Data Link Layer` - Converts Data to Frames
- `Layer 1` - `Physical Layer` - Converst Frames to Bits and Transmission Wires

### What are the `target groups` in LB ?

- They are nothing but the set of Instances clubbed together.
- Tagret Groups are used to perform `HealthChecks` on the Instances.
- Based on some `Rules` the routes the traffic to `Target Groups`

### What are some of the Algorithms used by the Load Balancers?

- These are some of the most famous Algorithms used in the Load Balancers

- Round Robin
- Weighted
- `Sticky Sessions` => When the request is routed form the EC2 instance and another request comes from the same user then the request is routed to the same EC2 Instance. `[Preffered for Stateful Applications]`

---

## DNS (ROUTE 53)

---

### What is DNS ?

- DNS porvides mapping of the URL to the corresponding IP Address.

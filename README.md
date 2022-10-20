# BGP-ROUTE-POLICY
This repo contains BGP configuration with route policies for Cisco and Juniper Routers.

The topology that this repo will follow is included below:

![image](https://user-images.githubusercontent.com/52250306/196794262-f29957e3-0746-4b55-9847-6a2fa903ff56.png)

----------------------------
## Advertising subscriber blocks 

R1 will use network commands to advertise customer provider subnets, R2 will redistribute connected routes and use a prefix list / route map to specify only the subscriber block being advertised 

R1
set protocols bgp group peer type external
set protocols bgp group peer export TO_AS592
set protocols bgp group peer neighbor 13.59.0.2 peer-as 592


set policy-options policy-statement TO_AS592 term 1 from route-filter 12.59.0.0/24 exact
set policy-options policy-statement TO_AS592 term 1 then accept
set policy-options policy-statement TO_AS592 term 2 from route-filter 12.59.1.0/24 exact
set policy-options policy-statement TO_AS592 term 2 then accept

R2

Conf t
router bgp 592
redistribute connected route-map subscriber
!
access-list 1 permit 14.59.0.0 0.0.0.255
!
route-map subscriber permit 10
match ip address 1
!
route-map subscriber permit 20 

---------------------------
## Route policy 

This route policy on will only allow advertisements from the prefix 12.59.1.0/24 an deny 12.59.0.0/24 

router bgp 592
!
ip access-list standard reject
permit 12.59.1.0 0.0.0.255
!
route-map test1 deny 10
match ip address reject
!
route-map test1 permit 20
match ip address permit


# LAB GUIDE EVE-NG
## Aruba CX 6300M + 6100 Switch + MikroTik Internet + VPCS

---

# 1. TOPOLOGY



INTERNET (Cloud/NAT)
|
MikroTik (192.168.2.254)
|
CORE SWITCH Aruba CX 6300M
|
-

|                       |
ACCESS SWITCH 1       ACCESS SWITCH 2
|   |                 |   |
PC4 PC5              PC6 PC7



---

# 2. IP PLAN

## WAN / Internet
- MikroTik: 192.168.2.254

## CORE SWITCH (Aruba CX 6300M)
- 1/1/1 = 192.168.2.93/24

## VLAN NETWORK
- VLAN 10 = 10.10.10.0/24
- VLAN 20 = 10.10.20.0/24
- VLAN 30 = 10.10.30.0/24
- VLAN 99 = 10.10.99.0/24

---

# 3. MIKROTIK (INTERNET GATEWAY)

## IP CONFIG


192.168.2.254/24



## NAT (WAJIB)


/ip firewall nat add chain=srcnat out-interface=WAN action=masquerade



## DNS


8.8.8.8
1.1.1.1



---

# 4. ARUBA CX 6300M (CORE)

## 4.1 VLAN


conf t
vlan 10
vlan 20
vlan 30
vlan 99



## 4.2 SVI (Gateway VLAN)


interface vlan 10
ip address 10.10.10.1/24

interface vlan 20
ip address 10.10.20.1/24

interface vlan 30
ip address 10.10.30.1/24

interface vlan 99
ip address 10.10.99.1/24



## 4.3 INTERNET PORT


interface 1/1/1
no shutdown
ip address 192.168.2.93/24



## 4.4 DEFAULT ROUTE


ip route 0.0.0.0/0 192.168.2.254



## 4.5 TRUNK KE ACCESS SWITCH


interface 1/1/2
no shutdown
vlan trunk allowed 10,20,30,99

interface 1/1/3
no shutdown
vlan trunk allowed 10,20,30,99



---

# 5. ACCESS SWITCH 1 (6100-CX-1)

## VLAN


vlan 10
vlan 20
vlan 30
vlan 99



## PORT CONFIG


interface 1/1/1
no shutdown
vlan trunk allowed 10,20,30,99

interface 1/1/2
no shutdown
vlan access 10

interface 1/1/3
no shutdown
vlan access 20



---

# 6. ACCESS SWITCH 2 (6100-CX-2)

## VLAN


vlan 10
vlan 20
vlan 30
vlan 99



## PORT CONFIG


interface 1/1/1
no shutdown
vlan trunk allowed 10,20,30,99

interface 1/1/2
no shutdown
vlan access 30

interface 1/1/3
no shutdown
vlan access 99



---

# 7. VPCS CONFIG

## PC4 (VLAN 10)


ip 10.10.10.10 255.255.255.0 10.10.10.1



## PC5 (VLAN 20)


ip 10.10.20.10 255.255.255.0 10.10.20.1



## PC6 (VLAN 30)


ip 10.10.30.10 255.255.255.0 10.10.30.1



## PC7 (VLAN 99)


ip 10.10.99.10 255.255.255.0 10.10.99.1



---

# 8. TESTING

## 8.1 TEST INTERNAL VLAN


ping 10.10.10.1
ping 10.10.20.1
ping 10.10.30.1
ping 10.10.99.1



## 8.2 TEST INTER VLAN


ping 10.10.20.10
ping 10.10.30.10
ping 10.10.99.10



## 8.3 TEST INTERNET


ping 8.8.8.8
ping google.com



---

# 9. FINAL STATUS

- VLAN Switching = OK
- Trunking = OK
- Inter VLAN Routing = OK
- Internet Access = OK


---

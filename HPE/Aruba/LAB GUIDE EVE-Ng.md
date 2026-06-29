# 🚀 EVE-NG LAB (Aruba CX 6300M + 6100 Switch + CLOUD INTERNET + VPCS)

---

# 1. TOPOLOGY

CLOUD (Internet)
→ CORE SWITCH Aruba CX 6300M
→ ACCESS SWITCH 1 & 2
→ PC4 PC5 PC6 PC7

---

# 2. IP PLAN

CORE INTERNET INTERFACE:
192.168.2.93/24

GATEWAY INTERNET (CLOUD ROUTER):
192.168.2.254

VLAN:

VLAN 10 = 10.10.10.0/24 → GW 10.10.10.1
VLAN 20 = 10.10.20.0/24 → GW 10.10.20.1
VLAN 30 = 10.10.30.0/24 → GW 10.10.30.1
VLAN 99 = 10.10.99.0/24 → GW 10.10.99.1

---

# 3. CLOUD (INTERNET)

* Cloud0 NAT / Management
* sudah auto internet dari EVE-NG

👉 TIDAK PERLU CONFIG

---

# 4. CORE SWITCH (ARUBA CX 6300M)

conf t

---

## VLAN

vlan 10
vlan 20
vlan 30
vlan 99

---

## SVI (GATEWAY VLAN)

interface vlan 10
ip address 10.10.10.1/24

interface vlan 20
ip address 10.10.20.1/24

interface vlan 30
ip address 10.10.30.1/24

interface vlan 99
ip address 10.10.99.1/24

---

## ENABLE ROUTING

ip routing

---

## INTERNET PORT (ke CLOUD)

interface 1/1/1
no shutdown
ip address 192.168.2.93/24

---

## DEFAULT ROUTE

ip route 0.0.0.0/0 192.168.2.254

---

## TRUNK KE ACCESS SWITCH

interface 1/1/2
no shutdown
vlan trunk allowed 10,20,30,99

interface 1/1/3
no shutdown
vlan trunk allowed 10,20,30,99

---

# 5. ACCESS SWITCH 1

vlan 10
vlan 20
vlan 30
vlan 99

---

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

# 6. ACCESS SWITCH 2

vlan 10
vlan 20
vlan 30
vlan 99

---

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

# 7. VPCS SETTING

PC4:
ip 10.10.10.10 255.255.255.0 10.10.10.1

PC5:
ip 10.10.20.10 255.255.255.0 10.10.20.1

PC6:
ip 10.10.30.10 255.255.255.0 10.10.30.1

PC7:
ip 10.10.99.10 255.255.255.0 10.10.99.1

---

# 8. TESTING

## internal gateway

ping 10.10.10.1
ping 10.10.20.1
ping 10.10.30.1
ping 10.10.99.1

---

## antar PC

ping 10.10.20.10
ping 10.10.30.10
ping 10.10.99.10

---

## internet

ping 8.8.8.8
ping google.com

---

# DONE

✔ VLAN OK
✔ TRUNK OK
✔ ROUTING OK
✔ INTERNET VIA CLOUD OK

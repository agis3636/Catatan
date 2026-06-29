# 🚀 EVE-NG LAB (Aruba CX 6300M + 6100 Switch + CLOUD INTERNET + VPCS)

# 1. TOPOLOGY

<img width="1306" height="1205" alt="Image" src="https://github.com/user-attachments/assets/ae8ab720-037e-49a1-8408-f252efbe9b52" />
<img width="1309" height="1202" alt="Image" src="https://github.com/user-attachments/assets/59c2714e-12f0-4e2d-b3d6-7e124fcb54da" />
<img width="1308" height="1202" alt="Image" src="https://github.com/user-attachments/assets/57c678ae-75a1-45b3-bce5-3612d07d57e8" />
<img width="1369" height="1149" alt="Image" src="https://github.com/user-attachments/assets/b74d7d5b-b437-403e-b6ec-97426938476d" />
<img width="1305" height="1205" alt="Image" src="https://github.com/user-attachments/assets/0e2a9296-eea7-4666-8872-921ee0769fbe" />
<img width="1308" height="1202" alt="Image" src="https://github.com/user-attachments/assets/f8d079f1-b0df-4bd6-86aa-665a0030faf2" />
<img width="1402" height="1122" alt="Image" src="https://github.com/user-attachments/assets/821aba55-0f3f-409a-aaab-cfcf4e72f292" />
<img width="1306" height="1204" alt="Image" src="https://github.com/user-attachments/assets/3d690a05-c017-48af-b7cb-d776ea985033" />

# 2. IP PLAN (WAJIB PAHAM)

## 🌐 INTERNET (CLOUD)

Gateway Internet:

```
192.168.2.254/24
```

---

## 🔵 CORE SWITCH INTERNET INTERFACE

```
192.168.2.93/24
```

---

## 🔵 DEFAULT ROUTE (INTERNET ACCESS)

```
0.0.0.0/0 → 192.168.2.254
```

---

## 🔵 VLAN NETWORK

| VLAN | NETWORK       | GATEWAY    |
| ---- | ------------- | ---------- |
| 10   | 10.10.10.0/24 | 10.10.10.1 |
| 20   | 10.10.20.0/24 | 10.10.20.1 |
| 30   | 10.10.30.0/24 | 10.10.30.1 |
| 99   | 10.10.99.0/24 | 10.10.99.1 |

---

# 3. CLOUD (INTERNET)

✔ EVE-NG Cloud0 NAT
✔ TIDAK PERLU CONFIG

---

# 4. CORE SWITCH (ARUBA CX 6300M)

---

## VLAN CREATE

```
conf t
vlan 10
vlan 20
vlan 30
vlan 99
```

---

## SVI (GATEWAY VLAN)

```
interface vlan 10
ip address 10.10.10.1/24

interface vlan 20
ip address 10.10.20.1/24

interface vlan 30
ip address 10.10.30.1/24

interface vlan 99
ip address 10.10.99.1/24
```

---

## ENABLE L3 ROUTING

```
ip routing
```

---

## INTERNET PORT (KE CLOUD)

```
interface 1/1/1
no shutdown
ip address 192.168.2.93/24
```

---

## DEFAULT ROUTE (WAJIB INTERNET)

```
ip route 0.0.0.0/0 192.168.2.254
```

---

## TRUNK KE ACCESS SWITCH

```
interface 1/1/2
no shutdown
vlan trunk allowed 10,20,30,99

interface 1/1/3
no shutdown
vlan trunk allowed 10,20,30,99
```

---

# 5. ACCESS SWITCH 1 (6100-CX-1)

---

## VLAN

```
vlan 10
vlan 20
vlan 30
vlan 99
```

---

## PORT CONFIG

```
interface 1/1/1
no shutdown
vlan trunk allowed 10,20,30,99

interface 1/1/2
no shutdown
vlan access 10

interface 1/1/3
no shutdown
vlan access 20
```

---

# 6. ACCESS SWITCH 2 (6100-CX-2)

---

## VLAN

```
vlan 10
vlan 20
vlan 30
vlan 99
```

---

## PORT CONFIG

```
interface 1/1/1
no shutdown
vlan trunk allowed 10,20,30,99

interface 1/1/2
no shutdown
vlan access 30

interface 1/1/3
no shutdown
vlan access 99
```

---

# 7. VPCS CONFIG

## PC4 (VLAN 10)

```
ip 10.10.10.10 255.255.255.0 10.10.10.1
```

## PC5 (VLAN 20)

```
ip 10.10.20.10 255.255.255.0 10.10.20.1
```

## PC6 (VLAN 30)

```
ip 10.10.30.10 255.255.255.0 10.10.30.1
```

## PC7 (VLAN 99)

```
ip 10.10.99.10 255.255.255.0 10.10.99.1
```

---

# 8. TESTING (URUT WAJIB)

---

## 8.1 TEST GATEWAY (CORE)

```
ping 10.10.10.1
ping 10.10.20.1
ping 10.10.30.1
ping 10.10.99.1
```

---

## 8.2 TEST INTER VLAN

```
ping 10.10.20.10
ping 10.10.30.10
ping 10.10.99.10
```

---

## 8.3 TEST INTERNET

```
ping 8.8.8.8
ping google.com
```

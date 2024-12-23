<h2> Proyecto Redes </h2>
## **Proyecto Redes**

El proyecto es investigar y configurar IPsec en los enlaces que se indican en la topología siguiente. IPsec es un protocolo que cifra los datagramas de capa 3. También deben configurar el protocolo de enrutamiento dinámico externo, entre los sistemas autónomos que se indican en la topología siguiente.

---

## **Redes por definir**

- **Network:** `84.218.144.0/20`
  - **AREA 1:** 254
  - **Enlace A:** 2
  - **Enlace B:** 2
  - **Enlace C:** 2
  - **Enlace D:** 2

| CIDR | Máscara de Subred | Máscara Comodín | # de Direcciones IP | # de Direcciones IP Utilizables |
|------|-------------------|-----------------|---------------------|--------------------------------|
| /32  | 255.255.255.255   | 0.0.0.0         | 1                   | 1                              |
| /31  | 255.255.255.254   | 0.0.0.1         | 2                   | 2*                             |
| /30  | 255.255.255.252   | 0.0.0.3         | 4                   | 2                              |
| /29  | 255.255.255.248   | 0.0.0.7         | 8                   | 6                              |
| /28  | 255.255.255.240   | 0.0.0.15        | 16                  | 14                             |
| /27  | 255.255.255.224   | 0.0.0.31        | 32                  | 30                             |
| /26  | 255.255.255.192   | 0.0.0.63        | 64                  | 62                             |
| /25  | 255.255.255.128   | 0.0.0.127       | 128                 | 126                            |
| /24  | 255.255.255.0     | 0.0.0.255       | 256                 | 254                            |
| /23  | 255.255.254.0     | 0.0.1.255       | 512                 | 510                            |
| /22  | 255.255.252.0     | 0.0.3.255       | 1,024               | 1,022                          |
| /21  | 255.255.248.0     | 0.0.7.255       | 2,048               | 2,046                          |
| /20  | 255.255.240.0     | 0.0.15.255      | 4,096               | 4,094                          |

**Como tenemos una red con 16 clases C, utilizaremos la mitad para cada sistema autónomo.**

---

## **Sistema Autónomo 1**

**`Network:`** `84.218.144.0/21`

| Red      | Solicitud de Host | Host Encontrados | Dirección de Red | Mask  | Máscara         | Primera IP utilizable | Última IP utilizable | Dirección de Broadcast |
|----------|-------------------|------------------|------------------|-------|-----------------|------------------------|-----------------------|-----------------------|
| Área 1   | 254               | 254              | 84.218.144.0     | /24   | 255.255.255.0   | 84.218.144.1           | 84.218.144.254         | 84.218.144.255         |
| Enlace A | 2                 | 2                | 84.218.145.0     | /30   | 255.255.255.252 | 84.218.145.1           | 84.218.145.2           | 84.218.145.3           |
| Enlace B | 2                 | 2                | 84.218.145.4     | /30   | 255.255.255.252 | 84.218.145.5           | 84.218.145.6           | 84.218.145.7           |
| Enlace C | 2                 | 2                | 84.218.145.8     | /30   | 255.255.255.252 | 84.218.145.9           | 84.218.145.10          | 84.218.145.11          |
| Enlace D | 2                 | 2                | 84.218.145.12    | /30   | 255.255.255.252 | 84.218.145.13          | 84.218.145.14          | 84.218.145.15          |
| BGP      | 2                 | 2                | 84.218.145.16    | /30   | 255.255.255.252 | 84.218.145.17          | 84.218.145.18          | 84.218.145.19          |

---

### **Configuración de los PCs**

#### **PC 1**

```bash
ip 84.218.144.2 255.255.255.0 84.218.144.1
save 
show
```

#### **PC 2**

```bash
ip 84.218.146.2 255.255.255.0 84.218.146.1
save 
show
```
#### **PC 3**

```bash
ip 84.218.147.2 255.255.255.0 84.218.147.1
save 
show
```
---

### **Configuración de los Routers**

#### **Router 1**

```bash
enable
configure terminal
interface fastEthernet 0/0
no shutdown
interface fastEthernet 0/0.10
encapsulation dot1Q 10
ip address 84.218.144.1 255.255.255.0
interface fastEthernet 0/0.20
encapsulation dot1Q 20
ip address 84.218.146.1 255.255.255.0
exit
interface fastEthernet 0/1
ip address 84.218.145.1 255.255.255.252
no shutdown
exit
end
wr
show ip interface brief
```

#### **Router 2**

```bash
enable
configure terminal
interface fastEthernet 0/0
ip address 84.218.145.2 255.255.255.252
no shutdown
exit
interface fastEthernet 0/1
ip address 84.218.145.5 255.255.255.252
no shutdown
exit
interface FastEthernet 1/0
ip address 84.218.145.14 255.255.255.252
no shutdown
exit
end
wr
show ip interface brief
```

#### **Router 3**

```bash
enable
configure terminal
interface fastEthernet 0/0
ip address 84.218.145.6 255.255.255.252
no shutdown
exit
interface FastEthernet 1/0
no switchport
ip address 84.218.145.9 255.255.255.252
no shutdown
exit
interface FastEthernet 0/1
ip address 84.218.147.1 255.255.255.0
no shutdown
exit
end
wr
show ip interface brief
```

#### **Router 4**

```bash
enable
configure terminal
interface FastEthernet 0/0
ip address 84.218.145.13 255.255.255.252
no shutdown
exit
interface fastEthernet 0/1
ip address 84.218.145.10 255.255.255.252
no shutdown
exit
interface fastEthernet 1/0
ip address 84.218.145.17 255.255.255.252
no shutdown
exit
end
wr
show ip interface brief
```

---

### **Configuración del Protocolo OSPF**

#### **Router 1**

```bash
configure terminal
router ospf 1
network 84.218.144.0 0.0.0.255 area 1
network 84.218.146.0 0.0.0.255 area 1
network 84.218.145.0 0.0.0.3 area 0
end
```

#### **Router 2**

```bash
configure terminal
router ospf 1
network 84.218.145.0 0.0.0.3 area 0
network 84.218.145.4 0.0.0.3 area 0
network 84.218.145.12 0.0.0.3 area 0
end
```

#### **Router 3**

```bash
configure terminal
router ospf 1
network 84.218.147.0 0.0.0.255 area 0
network 84.218.145.4 0.0.0.3 area 0
network 84.218.145.8 0.0.0.3 area 0
end
```

#### **Router 4**

```bash
configure terminal
router ospf 1
network 84.218.145.8 0.0.0.3 area 0
network 84.218.145.12 0.0.0.3 area 0
network 84.218.145.16 0.0.0.3 area 2
end
```
### **Configuración del Vlan**
#### **Switch L2-1*

```bash
enable
configure terminal
vlan 10
name VLAN10
vlan 20
name VLAN20
exit
interface GigabitEthernet 0/1
switchport mode access
switchport access vlan 10
exit
interface GigabitEthernet 0/2
switchport mode access
switchport access vlan 20
exit
interface GigabitEthernet 0/0
switchport trunk encapsulation dot1q
switchport mode trunk
switchport trunk allowed vlan add 10,20
end
```

### **Verificación de las Conexiones**

```bash
show ip ospf


show ip route
```

---

## **Sistema Autónomo 2**

**`Network:`** `84.218.152.0/21`

| Red      | Solicitud de Host | Host Encontrados | Dirección de Red | Mask  | Máscara         | Primera IP utilizable | Última IP utilizable | Dirección de Broadcast |
|----------|-------------------|------------------|------------------|-------|-----------------|------------------------|-----------------------|-----------------------|
| Área 1   | 254               | 254              | 84.218.152.0     | /24   | 255.255.255.0   | 84.218.152.1           | 84.218.152.254         | 84.218.152.255         |
| Enlace A | 2                 | 2                | 84.218.153.0     | /30   | 255.255.255.252 | 84.218.153.1           | 84.218.153.2           | 84.218.153.3           |
| Enlace B | 2                 | 2                | 84.218.153.4     | /30   | 255.255.255.252 | 84.218.153.5           | 84.218.153.6           | 84.218.153.7           |
| Enlace C | 2                 | 2                | 84.218.153.8     | /30   | 255.255.255.252 | 84.218.153.9           | 84.218.153.10          | 84.218.153.11          |
| Enlace D | 2                 | 2                | 84.218.153.12    | /30   | 255.255.255.252 | 84.218.153.13          | 84.218.153.14          | 84.218.153.15          |

---

### **Configuración de los PCs**

#### **PC 4**

```bash
ip 84.218.154.2 255.255.255.0 84.218.154.1
save 
show
```

#### **PC 5**

```bash
ip 84.218.152.2 255.255.255.0 84.218.152.1
save 
show
```

#### **PC 6**

```bash
ip 84.218.155.2 255.255.255.0 84.218.155.1
save 
show
```

---

### **Configuración de los Routers**

#### **Router 8**

```bash
enable
configure terminal
interface FastEthernet 0/0
ip address 84.218.153.1 255.255.255.0
no shutdown
exit
interface fastEthernet 0/1
no shutdown
interface fastEthernet 0/1.10
encapsulation dot1Q 30
ip address 84.218.152.1 255.255.255.0
interface fastEthernet 0/1.20
encapsulation dot1Q 40
ip address 84.218.155.1 255.255.255.0
exit
end
wr
show ip interface brief
```

#### **Router 7**

```bash
enable
configure terminal
interface FastEthernet 0/0
ip address 84.218.153.5 255.255.255.252
no shutdown
exit
interface fastEthernet 0/1
ip address 84.218.153.2 255.255.255.252
no shutdown
exit
interface fastEthernet 1/0
ip address 84.218.153.14 255.255.255.252
no shutdown
exit
end
wr
show ip interface brief
```

#### **Router 6**

```bash
enable
configure terminal
interface FastEthernet 0/0
ip address 84.218.153.9 255.255.255.252
no shutdown
exit
interface fastEthernet 0/1
ip address 84.218.153.13 255.255.255.252
no shutdown
exit
interface fastEthernet 1/0
ip address 84.218.154.1 255.255.255.0
no shutdown
exit
end
wr
show ip interface brief
```

#### **Router 5**

```bash
enable
configure terminal
interface FastEthernet 1/0
ip address 84.218.153.10 255.255.255.252
no shutdown
exit
interface fastEthernet 0/1
no switchport
ip address 84.218.153.6 255.255.255.252
no shutdown
exit
interface fastEthernet 0/0
ip address 84.218.145.18 255.255.255.252
no shutdown
exit
end
wr
show ip interface brief
```

### **Configuración del Protocolo OSPF**

#### **Router 8**

```bash
configure terminal
router ospf 1
network 84.218.152.0 0.0.0.255 area 1
network 84.218.155.0 0.0.0.255 area 1
network 84.218.153.0 0.0.0.3 area 0
end
```

#### **Router 7**

```bash
configure terminal
router ospf 1
network 84.218.153.0 0.0.0.3 area 0
network 84.218.153.4 0.0.0.3 area 0
network 84.218.153.12 0.0.0.3 area 0
end
```

#### **Router 6**

```bash
configure terminal
router ospf 1
network 84.218.154.0 0.0.0.255 area 0
network 84.218.153.8 0.0.0.3 area 0
network 84.218.153.12 0.0.0.3 area 0
end
```

#### **Router 5**

```bash
configure terminal
router ospf 1
network 84.218.153.4 0.0.0.3 area 0
network 84.218.153.8 0.0.0.3 area 0
network 84.218.145.16 0.0.0.3 area 2
end
```

### **Verificación de las Conexiones**

```bash
show ip ospf
show ip ospf neighbor
show ip route
```

---
### **Configuración del Vlan**
#### **Switch L2-2**

```bash
enable
configure terminal
vlan 30
name VLAN30
vlan 40
name VLAN40
exit
interface GigabitEthernet 0/1
switchport mode access
switchport access vlan 30
exit
interface GigabitEthernet 0/2
switchport mode access
switchport access vlan 40
exit
interface GigabitEthernet 0/0
switchport trunk encapsulation dot1q
switchport mode trunk
switchport trunk allowed vlan add 30,40
end
```
### **Configuración del BGP**

#### **Router 4**

```bash
configure terminal
router bgp 100
neighbor 84.218.145.18 remote-as 200
redistribute ospf 1
exit
router ospf 1
redistribute bgp 100 subnets
end
wr
```

#### **Router 5**

```bash
configure terminal
router bgp 200
neighbor 84.218.145.17 remote-as 100
redistribute ospf 1
exit
router ospf 1
redistribute bgp 200 subnets
end
wr
```

---

### **Configuración IPsec**

#### **Router 1**

```bash
enable
configure terminal
access-list 100 permit ip any any
crypto ipsec transform-set MY_ENCRYP esp-aes esp-sha-hmac
crypto map WIRED 10 ipsec-isakmp
set peer 84.218.145.2
set transform-set MY_ENCRYP
match address 100
crypto isakmp policy 10
encryption aes
hash sha
authentication pre-share
group 2
lifetime 86400
crypto isakmp key PASSWORD address 84.218.145.2
interface fastEthernet 0/1
crypto map WIRED
end
```

#### **Router 2**

```bash
enable
configure terminal
access-list 100 permit ip any any
crypto ipsec transform-set MY_ENCRYP esp-aes esp-sha-hmac
crypto map WIRED 10 ipsec-isakmp
set peer 84.218.145.1
set transform-set MY_ENCRYP
match address 100
crypto isakmp policy 10
encryption aes
hash sha
authentication pre-share
group 2
lifetime 86400
crypto isakmp key PASSWORD address 84.218.145.1
interface fastEthernet 0/0
crypto map WIRED
```

#### **Router 8**

```bash
enable
configure terminal
access-list 100 permit ip any any
crypto ipsec transform-set MY_ENCRYP esp-aes esp-sha-hmac
crypto map WIRED 10 ipsec-isakmp
set peer 84.218.153.2
set transform-set MY_ENCRYP
match address 100
crypto isakmp policy 10
encryption aes
hash sha
authentication pre-share
group 2
lifetime 86400
crypto isakmp key PASSWORD address 84.218.153.2
interface fastEthernet 0/0
crypto map WIRED
end
```

#### **Router 7**

```bash
enable
configure terminal
access-list 100 permit ip any any
crypto ipsec transform-set MY_ENCRYP esp-aes esp-sha-hmac
crypto map WIRED 10 ipsec-isakmp
set peer 84.218.153.1
set transform-set MY_ENCRYP
match address 100
crypto isakmp policy 10
encryption aes
hash sha
authentication pre-share
group 2
lifetime 86400
crypto isakmp key PASSWORD address 84.218.153.1
interface fastEthernet 0/1
crypto map WIRED
end
```

### **Comandos de Verificación para IPsec**

```bash
show crypto isakmp sa
show crypto ipsec sa
```

---

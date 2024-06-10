# Ejercicios

---

![Untitled](Ejercicios%2058b0c1aba32a484985282fedb1e35523/Untitled.png)

- **Tablas del ejercicio**
    
    **direccionamiento:**
    
    | Dispositivo | Interfaces | VLAN | Dirección IP | Máscara de subred | Gateway predeterminado | DNS |
    | --- | --- | --- | --- | --- | --- | --- |
    | R1 | G0/0 | N/D | 192.168.X.1 | 255.255.255.224 | N/D | N/D |
    |  | G0/0/0 | N/D | 192.168.X.225 | 255.255.255.252 | N/D | N/D |
    |  | G0/1/0 | N/D | 192.168.X.229 | 255.255.255.252 | N/D | N/D |
    | R2 | G0/0.10 | 10 | 192.168.X.33 | 255.255.255.240 | N/D | N/D |
    |  | G0/0.11 | 11 | 192.168.X.65 | 255.255.255.240 | N/D | N/D |
    |  | G0/0.12 | 12 | 192.168.X.97 | 255.255.255.240 | N/D | N/D |
    |  | G0/0.13 | 13 | 192.168.X.129 | 255.255.255.240 | N/D | N/D |
    |  | G0/0.14 | 14 Native | 192.168.X.161 | 255.255.255.240 | N/D | N/D |
    |  | G0/0/0 | N/D | 192.168.X.226 | 255.255.255.252 | N/D | N/D |
    |  | G0/1/0 | N/D | 192.168.X.234 | 255.255.255.252 | N/D | N/D |
    |  | G0/3/0 | N/D | 200.31.12.1 | 255.255.255.252 | N/D | N/D |
    | R3 | G0/0 | N/D | 192.168.X.193 | 255.255.255.224 | N/D | N/D |
    |  | G0/0/0 | N/D | 192.168.X.230 | 255.255.255.252 | N/D | N/D |
    |  | G0/1/0 | N/D | 192.168.X.233 | 255.255.255.252 | N/D | N/D |
    | S1 | Int vlan 1 | 1 | 192.168.X.30 | 255.255.255.224 | completar | completar |
    | S2 | Int vlan 13 | 13 | 192.168.X.130 | 255.255.255.224 | completar | completar |
    | S3 | Int vlan 1 | 1 | 192.168.X.222 | 255.255.255.224 | completar | completar |
    | PC0 | NIC | N/D | DHCP | 255.255.255.224 | completar | completar |
    | PC1 | NIC | N/D | DHCP | 255.255.255.224 | completar | completar |
    | PC2 | NIC | 10 | DHCP | 255.255.255.240 | completar | completar |
    | PC3 | NIC | 11 | DHCP | 255.255.255.240 | completar | completar |
    | ServerLocal (DNS, HTTP) | NIC | 12 | Privada 
    192.168.X.98 | 255.255.255.240 | completar | completar |
    |  |  | N/D | Publica 200.123.226.1 |  | completar | completar |
    | PC4 | NIC | N/D | DHCP | 255.255.255.224 | completar | completar |
    | PC5 | NIC | N/D | DHCP | 255.255.255.224 | completar | completar |
    
    **Vlans:**
    
    | VLAN en S2 | Nombre | Interfaces |
    | --- | --- | --- |
    | 10 | Ventas | F0/1 a 6 |
    | 11 | Administrativos | F0/7 a 12 |
    | 12 | Servidores | F0/13 a 18 |
    | 13 | Gestión | F0/19 a 24 |
    | 14 | Native (Nativo) | G0/1 |

## R1 (primer paso)

```jsx
en
conf t
hostname R1
int g0/0
description LAN-R1
ip add 192.168.X.1 255.255.255.224
no sh
int g0/0/0
description WAN TO R2
IP ADD 192.168.X.225 255.255.255.252
no sh
int g0/1/0
description WAN TO R3
ip add 192.168.X.229 255.255.255.252
no sh

router ospf 10
router-id 1.1.1.1
net 192.168.X.0 0.0.0.31 area 0
net 192.168.X.224 0.0.0.3 area 0
net 192.168.X.228 0.0.0.3 area 0
passive-interface g0/0
```

## R2 (este sale a internet - Tercer paso)

```jsx
en 
conf t
hostname R2
no ip domain-lookup
enable secret 123456
line vty 0 4
pass cisco
login
exit
banner motd *Advertencia*
service password-encryption
ip domain-name cisco.com
username Admin secret P7ssw0rd123

crypto key generate rsa
1024

line vty 0 4
transport input ssh
login local
exit

access-list 1 permit 192.168.x.128 0.0.0.15

line vty 0 4
access-class 1 in
```

### R2 second part (Quinto paso)

```jsx
en
conf t
int g0/0
no sh
int g0/0.10
encapsulation dot1Q 10
ip add 192.168.X.33 255.255.255.240

int g0/0.11
encapsulation dot1Q 11
ip add 192.168.X.65 255.255.255.240

int g0/0.12
encaptulation dot1Q 12
ip add 192.168.X.97 255.255.255.240

int g0/0.13
encaptulation dot1Q 13
ip add 192.168.X.129 255.255.255.240

int g0/0.14
encaptulation dot1Q 14 native
ip add 192.168.X.161 255.255.255.240

conf t

int g0/0/0
description WAN TO R1
ip add 192.168.X.226 255.255.255.252
no sh

int g0/1/0
description WAN TO R2
ip add 192.168.X.234 255.255.255.252
no sh

int g0/3/0
ip add 200.31.12.1 255.255.255.252
no sh
description WAN TO ISP

router ospf 10
router-id 2.2.2.2
net 192.168.X.0 0.0.0.255 area 0

passive-interface g0/0

ip route 0.0.0.0 0.0.0.0 g0/3/0

router ospf 10
default-information originate

ip nat inside source static 192.168.X.98 200.123.226.1
int g0/0.12
ip nat inside
int g0/3/0
ip nat outside

exit

access-list 2 permit 192.168.X.0 0.0.0.255
ip nat inside source list 2 interface g0/3/0 overload

int g0/0/0
ip nat inside
int g0/1/0
ip nat inside
int g0/0.10
ip nat inside
int g0/0.11
ip nat inside
int g0/0.12
ip nat inside
int g0/0.13
ip nat inside
int g0/0.14
ip nat inside
end

conf t
ip dhcp excluded-address 192.168.X.1 192.168.X.5
ip dhcp excluded-address 192.168.X.193 192.168.X.197
ip dhcp expluded-address 192.168.X.33 192.168.X.37
ip dhcp excluded-address 192.168.X.65 192.168.X.69

ip dhcp pool LAN-R1
net 192.168.X.0 255.255.255.224
default-router 192.168.X.1
dns-server 192.168.X.98

ip dhcp pool LAN-R3
net 192.168.X.192 255.255.255.224
default-router 192.168.X.193
dns-server 192.168.X.98

ip dhcp pool VLAN-10
net 192.168.X.32 255.255.255.240
default-router 192.168.X.33
dns-server 192.168.X.98

ip dhcp pool VLAN-11
net 192.168.X.64 255.255.255.240
default-router 192.168.X.65
dns-server 192.168.X.98

```

Luego se configura el serverLocal_Vlan12 con la tabla

## R3 (segundo paso)

```jsx
en
conf t
hostname R3
int g0/0
description LAN-R3
IP ADD 192.168.10.193 255.255.255.224
NO SH
int g0/0/0
description WAN TO R1
ip add 192.168.x.230 255.255.255.252
no sh

int g0/1/0
description WAN TO R2
IP ADD 192.168.X.233 255.255.255.252
NO SH

router ospf 10
router-id 3.3.3.3
net 192.168.x.192 0.0.0.31 area 0
net 192.168.x.228 0.0.0.3 area 0
net 192.168.x.233

net 192.168.x.232 0.0.0.3 area 0
passive-interface g0/0
```

## S2 (cuarto paso)

```jsx
en
conf t
hostname S2
VLAN 10
name Ventas
VLAN 11
name Administrativos
VLAN 12
name Servidores
VLAN 13
name Gestion
VLAN 14
name Native

exit
int r f 0/1-6
switchport mode access
switchport access VLAN 10

int r f 0/7-12
switchport mode access
switchport access VLAN 11

int r f 0/13-18
switchport mode access
switchport access VLAN 12

int r f 0/19-24
switchport mode access
switchport access VLAN 13

int g0/1
switchport mode trunk
switchport trunk native VLAN 14
switchport trunk allowed VLAN 10,11,12,13,14

int r f0/1-24
switchport port-security

switchport port-security mac-address sticky
switchport port-security maximum 2
switchport port-security violation restrict

int r f 0/1-24
shutdown

int f0/1
no sh

int f0/7
no sh

int f0/13
no sh

exit
int VLAN 13
no sh
ip add 192.168.X.130 255.255.255.240
exit
ip default-gateway 192.168.X.129

```

R1 SECOND PART

```jsx
en
conf t
int g0/0
ip helper-address 192.168.x.33
```

pasar los pcs a DHCP

R3 SECOND PART

```jsx
en
conf t
int g0/0
ip helper-address 192.168.x.33
```

pasar los pcs a DHCP

---

---

---

---

---

---

# Por punto

## Configuracion de parametros basicos R2

```jsx
en
conf t
```

### Nombre Segun la tabla de direccionamiento

```jsx
hostname R2
```

### Desactive la busqueda de DNS

```jsx
no ip domain-lookup
```

### Configure la contrasenia de enable con su codigo

```jsx
enable secret 123456
```

### Asigne cisco como como la contrasenia de consola

```jsx
line vty 0 4
pass cisco
login
```

### Mensaje de bienvenida con la palabra Advertencia

```jsx
banner motd *Advertencia*
```

### Cifre todas las contrasenias de texto no cifrado

```jsx
service password-encryption
```

## 3. Configuracion de acceso por SSH en R2

### Nombre de dominio cisco.com

```jsx
ip domain-name cisco.com
```

### Cree usuario local Admin con contrasenia segura P4ssw0rd123

```jsx
username Admin secret P4ssw0rd123
```

### Genere las claves RSA de 1024 bits

```jsx
crypto key generate rsa
1024
```

### Configure las lineas VTY para el acceso por SSH y solicite los perfiles de usuarios locales para la autenticacion

```jsx
line vty 0 4
transport input ssh
login local
exit
```

### Configure una ACL para acceso SSH a los switch y routers donde solo la VLAN de gestion tenga acceso al VTY

```jsx
access-list 1 permit 192.168.X.128 0.0.0.15

line vty 0 4
access-class 1 in
```

## 4. Configuracion de opciones de seguridad puertos switch S2

### Los puertos de acceso de S2 deben estar en modo acceso

```jsx
en
conf t
hostname S2
VLAN 10
name Ventas
VLAN 11
name Administrativos
VLAN 12
name Servidores
VLAN 13
name Gestion
VLAN 14
name Native

exit
int r f 0/1-6
switchport mode access
switchport access VLAN 10

int r f 0/7-12
switchport mode access
switchport access VLAN 11

int r f 0/13-18
switchport mode access
switchport access VLAN 12

int r f 0/19-24
switchport mode access
switchport access VLAN 13

int g0/1
switchport mode trunk
switchport trunk native VLAN 14
switchport trunk allowed VLAN 10,11,12,13,14
```

### Deshabilitar los puertos no utilizados

```jsx
int r f 0/1-24
shutdown
int r f0/1
so sh
int r f0/7
no sh
int r f0/13
no sh
```

### Habilite la seguridad de puertos en los puertos de los switch

- Agregar direcciones MAC seguras dinamicamente a la configuracion en ejecucion
- Configurar un maximo de 2 direcciones MAC permitidas por puerto
- Configurar la opcion de violacion de seguridad para enviar un mensaje al log sin deshabilitar el puerto

```jsx
int r f0/1-24
switchport port-security
switchport port-security mac-address sticky
switchport port-security maximum 2
switchport port-security violation restrict
```

## 5. Configure el direccionamiento para todos los dispositivos de acuerdo con la tabla de direccionamiento.

### Configure router R2 como Server DHCP para las redes LAN1, LAN3, y las VLANs 10 y 11 de R2

Excluya las primeras 5 ip de la red para asignacion manual (no dhcp)

```jsx
ip dhcp excluded-address 192.168.X.1 192.168.X.5
ip dhcp excluded-address 192.168.X.193 192.168.X.197
ip dhcp expluded-address 192.168.X.33 192.168.X.37
ip dhcp excluded-address 192.168.X.65 192.168.X.69
```

Configure red, default-gateway, dns server de acuerdo la tabla de direccionamiento

```jsx
ip dhcp pool LAN-R1
net 192.168.X.0 255.255.255.224
default-router 192.168.X.1
dns-server 192.168.X.98
exit

ip dhcp pool LAN-R3
net 192.168.X.192 255.255.255.224
default-router 192.168.X.193
dns-server 192.168.X.98
exit

ip dhcp pool VLAN-10
net 192.168.X.32 255.255.255.240
default-router 192.168.X.33
dns-server 192.168.X.98
exit

ip dhcp pool VLAN-11
net 192.168.X.64 255.255.255.240
default-router 192.168.X.65
dns-server 192.168.X.98
exit
```

En R1

```jsx
int g0/0
ip helper-address 192.168.X.33
```

En R2

```jsx
int g0/0
ip helper-address 192.168.X.33
```

### Configure el direccionamiento de manera manual de los demas dispositivos: servidores y gestion de los switch.

En R1

```jsx
en
conf t
int g0/0
description LAN-R1
ip add 192.168.X.1 255.255.255.224
no sh
int g0/0/0
description WAN TO R2
IP ADD 192.168.X.225 255.255.255.252
no sh
int g0/1/0
description WAN TO R3
ip add 192.168.X.229 255.255.255.252
no sh
```

En R2

```jsx
en
conf t
int g0/0
no sh
int g0/0.10
encapsulation dot1Q 10
ip add 192.168.X.33 255.255.255.240

int g0/0.11
encapsulation dot1Q 11
ip add 192.168.X.65 255.255.255.240

int g0/0.12
encaptulation dot1Q 12
ip add 192.168.X.97 255.255.255.240

int g0/0.13
encaptulation dot1Q 13
ip add 192.168.X.129 255.255.255.240

int g0/0.14
encaptulation dot1Q 14 native
ip add 192.168.X.161 255.255.255.240

conf t

int g0/0/0
description WAN TO R1
ip add 192.168.X.226 255.255.255.252
no sh

int g0/1/0
description WAN TO R2
ip add 192.168.X.234 255.255.255.252
no sh

int g0/3/0
ip add 200.31.12.1 255.255.255.252
no sh
description WAN TO ISP
```

En R3

```jsx
en
conf t
hostname R3
int g0/0
description LAN-R3
IP ADD 192.168.X.193 255.255.255.224
NO SH

int g0/0/0
description WAN TO R1
ip add 192.168.X.230 255.255.255.252
no sh

int g0/1/0
description WAN TO R2
IP ADD 192.168.X.233 255.255.255.252
NO SH
```

## 6. **Configure enrutamiento entre vlans**

### Configure las VLANS en S2 de acuerdo a la tabla Vlans. S1 y S3 no se realiza ninguna configuración en este sentido.

## S2 (cuarto paso)

```jsx
en
conf t
hostname S2
VLAN 10
name Ventas
VLAN 11
name Administrativos
VLAN 12
name Servidores
VLAN 13
name Gestion
VLAN 14
name Native

exit
int r f 0/1-6
switchport mode access
switchport access VLAN 10

int r f 0/7-12
switchport mode access
switchport access VLAN 11

int r f 0/13-18
switchport mode access
switchport access VLAN 12

int r f 0/19-24
switchport mode access
switchport access VLAN 13

int g0/1
switchport mode trunk
switchport trunk native VLAN 14
switchport trunk allowed VLAN 10,11,12,13,14

int r f0/1-24
switchport port-security

switchport port-security mac-address sticky
switchport port-security maximum 2
switchport port-security violation restrict

int r f 0/1-24
shutdown

int f0/1
no sh

int f0/7
no sh

int f0/13
no sh

exit
int VLAN 13
no sh
ip add 192.168.X.130 255.255.255.240
exit
ip default-gateway 192.168.X.129

```

### ~~Configure enrutamiento entre vlan en R2 usando el método de *Router on a stick de* acuerdo con la tabla de direccionamiento y la tabla vlans~~

## 7. En el **server Local** de la LAN 2 configure:

SERVER

![image.png](Ejercicios%2058b0c1aba32a484985282fedb1e35523/image.png)

## 8.  **Configurar enrutamiento dinámico con OSPF**

Utilice los siguientes requisitos para configurar el routing OSPF en los tres routers:

- ID de proceso 10
- ID del router para cada router: R1 = 1.1.1.1; R2 = 2.2.2.2; R3 = 3.3.3.3
- Asocie las redes directamente conectadas
- Interfaz LAN configurada como pasiva (no utilice la palabra clave default)

En R1

```python
router ospf 10
router-id 1.1.1.1
net 192.168.X.0 0.0.0.31 area 0
net 192.168.X.224 0.0.0.3 area 0
net 192.168.X.228 0.0.0.3 area 0
passive-interface g0/0
```

En R2

```python
router ospf 10
router-id 2.2.2.2
net 192.168.X.0 0.0.0.255 area 0

passive-interface g0/0
```

En R3

```python
router ospf 10
router-id 3.3.3.3
net 192.168.X.192 0.0.0.31 area 0
net 192.168.X.228 0.0.0.3 area 0
net 192.168.X.233

net 192.168.X.232 0.0.0.3 area 0
passive-interface g0/0
```

## 9.  **Configurar una ruta predeterminada**

- En R2 configure una ruta predeterminada hacia ISP
- Garantice que la ruta predeterminada pueda ser compartida por el OSPF a los demás routers

```python
ip route 0.0.0.0 0.0.0.0 g0/3/0
router ospf 10
default-information originate
```

## 10.  **Configure la NAT/PAT**

Configure NAT estático para el servidor local de acuerdo con la tabla de direccionamiento

En R2

```python
ip nat inside source static 192.168.X.98 200.123.226.1
int g0/0.12
ip nat inside
int g0/3/0
ip nat outside
```

Configure PAT con overload para todo el rango privado de las 3 redes LAN a través de la IP pública de la WAN entre R2 y el ISP. 

En R2

```jsx
access-list 2 permit 192.168.X.0 0.0.0.255
ip nat inside source list 2 interface g0/3/0 overload

int g0/0/0
ip nat inside
int g0/1/0
ip nat inside
int g0/0.10
ip nat inside
int g0/0.11
ip nat inside
int g0/0.12
ip nat inside
int g0/0.13
ip nat inside
int g0/0.14
ip nat inside
end
```

Verifique la conectividad paso a paso y al final y también revise el acceso WEB de los servidores local y externo.

## Para “completar” tabla

Verifíquese las interfaces que conectan con las distintas LAN o VLAN, y a cada equipo o switch que pertenezca a dicha red tómese la interfaz que conecta con el Router respecti. La IP de DNS es la ip privada del servidor DNS (192.168.X.98):

| Dispositivo | Gateway | DNS |
| --- | --- | --- |
| S1 | 192.168.239.1 | 192.168.X.98 |
| S2 | 192.168.X.129 | 192.168.X.98 |
| S3 | 192.168.239.193 | 192.168.X.98 |
| PC0 | 192.168.X.1 | 192.168.X.98 |
| PC1 | 192.168.X.1 | 192.168.X.98 |
| PC2 | 192.168.X.33 | 192.168.X.98 |
| PC3 | 192.168.X.33 | 192.168.X.98 |
| Server Local | 192.168.X.97 | 192.168.X.98 |

Se usó la siguiente información para corroborar.

```jsx
ip dhcp pool LAN-R1
net 192.168.X.0 255.255.255.224
default-router 192.168.X.1
dns-server 192.168.X.98
exit

ip dhcp pool LAN-R3
net 192.168.X.192 255.255.255.224
default-router 192.168.X.193
dns-server 192.168.X.98
exit

ip dhcp pool VLAN-10
net 192.168.X.32 255.255.255.240
default-router 192.168.X.33
dns-server 192.168.X.98
exit

ip dhcp pool VLAN-11
net 192.168.X.64 255.255.255.240
default-router 192.168.X.65
dns-server 192.168.X.98
exit
```

Estrutura inicial
![[Pasted image 20250930175001.png]]

**Link da documentação**
https://docs.google.com/document/d/1e0-dahO6C6x7vIoi7OLVSo8FlqZmh4BoCYqz1fqY5mo/edit?usp=sharing

# Configurando Switch 1 - Sede

**Configurando Vlans para segmentação de rede**
![[Pasted image 20250930213117.png]]

```Bash
Switch>enable

Switch#configure terminal

Enter configuration commands, one per line. End with CNTL/Z.

Switch(config)#vlan 10

Switch(config-vlan)#name TI

Switch(config-vlan)#exit

Switch(config)#vlan 20

Switch(config-vlan)#name Vendas

Switch(config-vlan)#exit

Switch(config)#exit

Switch#

%SYS-5-CONFIG_I: Configured from console by console

wr mem
```

Configurando as entradas fastethernet para se conectarem na vlan 10 (TI)
![[Pasted image 20250930221747.png]]

```Bash
Switch>enable

Switch#interface FastEthernet0/1

^

% Invalid input detected at '^' marker.

Switch#interface FastEthernet0/1

Switch#configure terminal

Enter configuration commands, one per line. End with CNTL/Z.

Switch(config)#interface FastEthernet0/1

Switch(config-if)#mode access

^

% Invalid input detected at '^' marker.

Switch(config-if)#switchport mode access

Switch(config-if)#switchport access vlan 10

Switch(config-if)#exit

Switch(config)#interface range FastEthernet0/2-3

Switch(config-if-range)#switchport mode access

Switch(config-if-range)#switchport access vlan 10

Switch(config-if-range)#exit

Switch(config)#exit

Switch#

%SYS-5-CONFIG_I: Configured from console by console

wr mem

Building configuration...

[OK]

Switch#
```

Inserindo as portas gigabit que conectam os 2 switchs como trunk para que os dados das duas vlans possam transitar no mesmo cabo

![[Pasted image 20250930221950.png]]

```Bash
Switch#configure terminal

Enter configuration commands, one per line. End with CNTL/Z.

Switch(config)#interface range GigabitEthernet0/1-2

Switch(config-if-range)#switchport mode trunk

  

Switch(config-if-range)#

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/1, changed state to down

  

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/1, changed state to up

  

Switch(config-if-range)#exit

Switch(config)#exit

Switch#

%SYS-5-CONFIG_I: Configured from console by console

wr mem

Building configuration...

[OK]

Switch#
```

# Configurando Switch 2 - Sede

![[Pasted image 20250930222226.png]]

```Bash
Switch>enable

Switch#configure terminal

Enter configuration commands, one per line. End with CNTL/Z.

Switch(config)#vlan 10

Switch(config-vlan)#name TI

Switch(config-vlan)#EXIT

Switch(config)#vlan 20

Switch(config-vlan)#name Vendas

Switch(config-vlan)#exit

Switch(config)#exit

Switch#

%SYS-5-CONFIG_I: Configured from console by console

wr mem

Building configuration...

[OK]

Switch#
```

Configurando as entradas fastethernet para se conectarem na vlan 20 (Vendas)

```Bash
Switch#configure terminal

Enter configuration commands, one per line. End with CNTL/Z.

Switch(config)#interface FastEthernet0/1

Switch(config-if)#switchport mode access

Switch(config-if)# switchport access vlan 20

Switch(config-if)#exit

Switch(config)#exit

Switch#

%SYS-5-CONFIG_I: Configured from console by console

wr mem

Building configuration...

[OK]

Switch#
```

![[Pasted image 20250930222348.png]]

Colocando giagbitethernet em modo trunk
![[Pasted image 20250930222458.png]]

```Bash
Enter configuration commands, one per line. End with CNTL/Z.

Switch(config)#interface GigabitEthernet0/1

Switch(config-if)# switchport mode trunk

Switch(config-if)#exit

Switch(config)#wr mem

^

% Invalid input detected at '^' marker.

Switch(config)#exit

Switch#

%SYS-5-CONFIG_I: Configured from console by console

wr mem

Building configuration...

[OK]

Switch#
```

# Configurando roteador da Sede

Configurando as duas subinterfaces para as vlans TI e Vendas para que o roteador consiga prover para as duas redes

![[Pasted image 20250930222759.png]]

```Bash
Router>enable

Router#configure terminal

Enter configuration commands, one per line. End with CNTL/Z.

Router(config)#interface GigabitEthernet0/0.10

Router(config-subif)#encapsulation dot1Q 10

Router(config-subif)#ip address 192.168.1.1 255.255.255.128

Router(config-subif)#exit

Router(config)#interface GigabitEthernet0/0.20

Router(config-subif)#encapsulation dot1Q 20

Router(config-subif)#ip address 192.168.1.129 255.255.255.128

Router(config-subif)#exit

Router(config)#interface GigabitEthernet0/0

Router(config-if)#no shutdown

  

Router(config-if)#

%LINK-5-CHANGED: Interface GigabitEthernet0/0, changed state to up

  

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0, changed state to up

  

%LINK-5-CHANGED: Interface GigabitEthernet0/0.10, changed state to up

  

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0.10, changed state to up

  

%LINK-5-CHANGED: Interface GigabitEthernet0/0.20, changed state to up

  

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0.20, changed state to up

  

Router(config-if)#exit

Router(config)#exit

Router#

%SYS-5-CONFIG_I: Configured from console by console

wr mem

Building configuration...

[OK]

Router#
```


# Configurando servidor DNS
![[Pasted image 20250930222951.png]]

Configurando domínio
![[Pasted image 20250930223006.png]]

Serviço de HTTP rodando
![[Pasted image 20250930223054.png]]

# Configurando servidor DHCP

![[Pasted image 20250930223744.png]]

Adicionando respectivas VLANS
![[Pasted image 20250930223807.png]]

**Configurando roteador para enviar os pedidos DHCP corretamente ao servidor**
![[Pasted image 20250930224251.png]]
```Bash
Router#configure terminal

Enter configuration commands, one per line. End with CNTL/Z.

Router(config)#interface GigabitEthernet0/0.10

Router(config-subif)# ip helper-address 192.168.1.3

Router(config-subif)#exit

Router(config)#interface GigabitEthernet0/0.20

Router(config-subif)#ip helper-address 192.168.1.3

Router(config-subif)#exit

Router(config)#end

Router#

%SYS-5-CONFIG_I: Configured from console by console

wr mem

Building configuration...

[OK]

Router#
```


Como resultado, podemos ver que os computadores receberam endereços IP via DHCP

PC0 - TI
![[Pasted image 20250930223856.png]]


PC1 - Vendas

![[Pasted image 20250930224310.png]]

# Configurando Wi-fi


Access-Point Interno Sede
![[Pasted image 20250930232825.png]]

Configurando roteador rede de visitantes 
![[Pasted image 20250930233807.png]]
![[Pasted image 20250930233756.png]]

![[Pasted image 20250930233816.png]]

Laptop conectado na rede de visitantes recebe ip na faixa 192.168.50.0/24
![[Pasted image 20250930234000.png]]

Laptop 0 na rede interna recebeu ip aleatório por que o roteador não faz o DHCP **(inclusive trocar depois para o mesmo tipo de roteador da rede de visitantes se quiserem - vai precisar refazer as configs feitas em roteador)**
![[Pasted image 20250930234107.png]]

# Configurando Switch 1 - Filial 2
```Bash
Switch(config)# vlan 10
Switch(config-vlan)# name F2_Staff
Switch(config-vlan)# exit
Switch(config)# vlan 20
Switch(config-vlan)# name F2_Guest
Switch(config-vlan)# exit
Switch(config)#
````
Atribuindo os PCs à VLAN 10 (Staff)
```Bash
Switch(config)# interface range fa0/2 - 3
Switch(config-if-range)# switchport mode access
Switch(config-if-range)# switchport access vlan 10
Switch(config-if-range)# exit
```
Configurando router como Trunk e Switch 2 como Trunk
```Bash
Switch(config)# interface fa0/1
Switch(config-if)# switchport mode trunk
Switch(config-if)# exit

Switch(config)# interface fa0/24
Switch(config-if)# switchport mode trunk
Switch(config-if)# exit
Switch(config)# end
Switch# write memory
```

# Configurando Switch 2 - Filial 2
```Bash
Switch(config)# vlan 10
Switch(config-vlan)# name F2_Staff
Switch(config-vlan)# exit
Switch(config)# vlan 20
Switch(config-vlan)# name F2_Guest
Switch(config-vlan)# exit
Switch(config)#
```

Configurando as portas dos PCs Guest (portas Fe0/4 e Fe/0/5)
```Bash
Switch(config)# interface range fa0/4 - 5
Switch(config-if-range)# switchport mode access
Switch(config-if-range)# switchport access vlan 20
Switch(config-if-range)# exit
```

# Configurando o Access Point e Guest Wi-fi
```Bash
Switch(config)# interface fa0/3
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 10
Switch(config-if)# exit

Switch(config)# interface fa0/2
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 20
Switch(config-if)# exit
```
#Link entre Switch 1 e 2
```Bash
Switch(config)# interface fa0/24
Switch(config-if)# switchport mode trunk
Switch(config-if)# exit
Switch(config)# end
Switch# write memory
```

#Configuração do Router da Filiar 2
```Bash
Router> enable
Router# configure terminal
Router(config)#
Router(config)# interface gigabitEthernet0/0
Router(config-if)# no shutdown
Router(config-if)# exit
Router(config)# interface gigabitEthernet0/0.10
Router(config-subif)# encapsulation dot1Q 10
Router(config-subif)# ip address 10.0.210.1 255.255.255.192
Router(config-subif)# exit
Router(config)# interface gigabitEthernet0/0.20
Router(config-subif)# encapsulation dot1Q 20
Router(config-subif)# ip address 10.0.220.1 255.255.255.192
Router(config-subif)# exit
Router(config)# end
Router# write memory
```

#Configuração do DHCP no Router da Filial 2
Configurando DHCP Vlan 10 (Staff) Vlan 20 (Guest)
```Bash
Router> enable
Router# configure terminal
Router(config)#
Router(config)# ip dhcp excluded-address 10.0.210.1 10.0.210.10
Router(config)# ip dhcp excluded-address 10.0.220.1 10.0.220.10
Router(config)# ip dhcp pool F2-STAFF
Router(dhcp-config)# network 10.0.210.0 255.255.255.192
Router(dhcp-config)# default-router 10.0.210.1
Router(dhcp-config)# dns-server 8.8.8.8
Router(dhcp-config)# exit
Router(config)# ip dhcp pool F2-GUEST
Router(dhcp-config)# network 10.0.220.0 255.255.255.192
Router(dhcp-config)# default-router 10.0.220.1
Router(dhcp-config)# dns-server 8.8.8.8
Router(dhcp-config)# exit
Router(config)# end
Router# write memory
```

#Configurar conexão entre Filial 2 e Sede
Roteador da Sede
```Bash
enable
configure terminal
interface gigabitEthernet0/1
 ip address 10.10.10.1 255.255.255.252
 no shutdown
exit
```
Roteador da Filial 2
```Bash
enable
configure terminal
interface gigabitEthernet0/1
 ip address 10.10.10.2 255.255.255.252
 no shutdown
exit
```

Rotas estáticas na filial e na sede
Filial
```Bash
ip route 192.168.0.0 255.255.0.0 10.10.10.1
```
Sede
```Bash
ip route 192.168.20.0 255.255.255.192 10.10.10.2
ip route 192.168.20.64 255.255.255.192 10.10.10.2
```

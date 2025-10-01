Estrutura inicial
![[Pasted image 20250930175001.png]]

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


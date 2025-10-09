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



# Filial 1:

A Filial 1 possui **1 roteador**, **1 switch** e **4 PCs**, conectados à sede através da interface **GigabitEthernet0/1** do roteador.

---

# Configurando Switch - Filial 1

**Criando VLANs para segmentação de rede**

```bash
Switch>enable
Switch#configure terminal

Enter configuration commands, one per line. End with CNTL/Z.

! Criando VLAN 30 e VLAN 40
Switch(config)#vlan 30
Switch(config-vlan)#name ADM
Switch(config-vlan)#exit
Switch(config)#vlan 40
Switch(config-vlan)#name Suporte
Switch(config-vlan)#exit
Switch(config)#exit

Switch#

%SYS-5-CONFIG_I: Configured from console by console

wr mem
```

Configurando as portas FastEthernet para as VLANs correspondentes

```bash
Switch>enable
Switch#configure terminal

Enter configuration commands, one per line. End with CNTL/Z.

! PCs 0 e 1 na VLAN 30 (ADM)
Switch(config)#interface range FastEthernet0/1-2
Switch(config-if-range)#switchport mode access
Switch(config-if-range)#switchport access vlan 30
Switch(config-if-range)#exit

! PCs 2 e 3 na VLAN 40 (Suporte)
Switch(config)#interface range FastEthernet0/3-4
Switch(config-if-range)#switchport mode access
Switch(config-if-range)#switchport access vlan 40
Switch(config-if-range)#exit

Switch(config)#exit

Switch#

%SYS-5-CONFIG_I: Configured from console by console

wr mem

Building configuration...
[OK]

Switch#
```

Inserindo a interface GigabitEthernet0/1 em modo trunk para permitir o tráfego das VLANs até o roteador

```bash
Switch#configure terminal

Enter configuration commands, one per line. End with CNTL/Z.

Switch(config)#interface GigabitEthernet0/1
Switch(config-if)#switchport mode trunk
Switch(config-if)#exit
Switch(config)#exit

Switch#

%SYS-5-CONFIG_I: Configured from console by console

wr mem

Building configuration...
[OK]

Switch#
```

---

# Configurando o Roteador - Filial 1

Configurando as subinterfaces para as VLANs ADM e Suporte

```bash
Router>enable
Router#configure terminal

Enter configuration commands, one per line. End with CNTL/Z.

! Subinterface VLAN 30 - ADM
Router(config)#interface GigabitEthernet0/0.30
Router(config-subif)#encapsulation dot1Q 30
Router(config-subif)#ip address 192.168.2.1 255.255.255.192
Router(config-subif)#ip helper-address 192.168.1.3
Router(config-subif)#exit

! Subinterface VLAN 40 - Suporte
Router(config)#interface GigabitEthernet0/0.40
Router(config-subif)#encapsulation dot1Q 40
Router(config-subif)#ip address 192.168.2.65 255.255.255.192
Router(config-subif)#ip helper-address 192.168.1.3
Router(config-subif)#exit

! Ativando a interface física
Router(config)#interface GigabitEthernet0/0
Router(config-if)#no shutdown

%LINK-5-CHANGED: Interface GigabitEthernet0/0, changed state to up
%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0, changed state to up

Router(config-if)#exit

! Configurando o link com a sede
Router(config)#interface GigabitEthernet0/1
Router(config-if)#ip address 10.0.0.2 255.255.255.252
Router(config-if)#no shutdown

%LINK-5-CHANGED: Interface GigabitEthernet0/1, changed state to up
%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/1, changed state to up

Router(config-if)#exit
Router(config)#exit

Router#

%SYS-5-CONFIG_I: Configured from console by console

wr mem

Building configuration...
[OK]

Router#
```

---

# Configurando o Roteador da Sede (link com a Filial 1)

```bash
Router>enable
Router#configure terminal

Enter configuration commands, one per line. End with CNTL/Z.

Router(config)#interface GigabitEthernet0/1
Router(config-if)#ip address 10.0.0.1 255.255.255.252
Router(config-if)#no shutdown
Router(config-if)#exit
Router(config)#exit

Router#

%SYS-5-CONFIG_I: Configured from console by console

wr mem

Building configuration...
[OK]

Router#
```

---

# Configurando o Roteamento RIP v2

**Roteador Filial 1**

```bash
Router>enable
Router#configure terminal

Enter configuration commands, one per line. End with CNTL/Z.

Router(config)#router rip
Router(config-router)#version 2
Router(config-router)#network 192.168.2.0
Router(config-router)#network 10.0.0.0
Router(config-router)#no auto-summary
Router(config-router)#exit
Router(config)#end

Router#

%SYS-5-CONFIG_I: Configured from console by console

wr mem

Building configuration...
[OK]

Router#
```

**Roteador da Sede**

```bash
Router>enable
Router#configure terminal

Enter configuration commands, one per line. End with CNTL/Z.

Router(config)#router rip
Router(config-router)#version 2
Router(config-router)#network 192.168.1.0
Router(config-router)#network 10.0.0.0
Router(config-router)#no auto-summary
Router(config-router)#exit
Router(config)#end

Router#

%SYS-5-CONFIG_I: Configured from console by console

wr mem

Building configuration...
[OK]

Router#
```

---

# Testando DHCP e conectividade

Após as configurações, os PCs da Filial 1 receberam IPs automaticamente via DHCP proveniente do servidor da sede.

Testes de conectividade:

```bash
ping 192.168.2.1      # Gateway VLAN 30
ping 192.168.1.1      # Router da Sede
ping 192.168.1.x      # PCs da Sede
```

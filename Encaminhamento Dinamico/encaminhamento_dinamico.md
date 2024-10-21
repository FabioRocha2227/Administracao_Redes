# Trabalho 2- Encaminhamento dinâmico

![alt text](img/image-19.png)

## Objetivo
No emulador de rede GNS3 dentro da VM a correr no sistema de virtualização deve configurar a rede da seguinte figura segundo as indicações dadas abaixo:

+ Os **terminais** usam como *default gateway* o **router** da rede à qual estão ligados.

+ No **AS1**, cada **router Rn** deve usar como **ID 1.0.0.n** . No **AS2**, cada **router Rn** deve usar como **ID 2.0.0.n** .

+ O **router R2** pertence a **dois sistemas autónomos**, pelo que **necessitará de dois processos OSPF distintos**. Este **router** deve **redistribuir no AS2** as **rotas aprendidas no AS1** e **vice-versa**. As **rotas importadas** devem ter uma **métrica 100**.

**Duvida: não percebi o que é para fazer no ponto abaixo**
     
     Optimizaçoes finais, para evitar tanto trafego na rede (fazer esta configuraçao no R2 e R6)
    
+ As **sub-redes 192.168.1.x** devem ser **sumarizadas na rede classful** a que pertencem para **efeitos de redistribuição** (e apenas para este fim)
     



**Duvida: não percebi o que é para fazer no ponto abaixo**
     
     Configurar os router, com ligaçoes para os "baloes", como interfaces passivas(sao apenas optimizaçoes para o protocolo OSPF, nao sao obrigatorias)

+ **Não devem ser enviadas mensagens de encaminhamento nas interfaces às quais não está ligado nenhum outro router (incluindo as ligadas aos terminais)**

+ As **métricas do OSPF** devem ser **calculadas automaticamente**. Deve mudar a **largura de banda de referência** de modo a que seja atribuído o **custo 10 a cada ligação FastEthernet**.

## Notas 

+ Os **routers** utilizados na simulação **são modulares**, da série 7200. A **ligação** entre **R6** e **R8** é feita através de **portas série síncronas** (**todas as outras ligações são FastEthernet**).

+ Se deixar o ponteiro do rato parado em cima de uma ligação, aparece um balão de informação indicando os routers que interliga, bem como as respectivas portas. Pode, assim, identificar que interfaces correspondem a cada ligação.

+ Para tornar a emulação mais leve, os **terminais 1 e 2 são emulados (VPCS)**. Para **configurar o endereço IP, máscara de rede e default gateway**, usar o **comando ip ipaddr/preflen gateway** (e.g., ip 192.168.1.123/25 192.168.1.1). Depois **guarde a configuração com o comando save**.


## Configurações iniciais

**Para descobrir a mascara de um prefixo /x usar:**

+   *ipcalc -m 0.0.0.0/x*

### Term 1

    1. ip 192.168.1.123/25 192.168.1.1
    2. save

### Term 2

    1. ip 172.16.1.2/24 172.16.1.1 
    2. save


### R1 (verificar esta configuração)
    1. Configurar endereço interface f1/0
        1.1. enable 
        1.2. conf t
        1.3. int f1/0
        1.4. ip addr 192.168.1.1 255.255.255.128
        1.5. no shutdown
        1.6. exit
    2. Configurar endereço interface f1/1
        2.1. enable
        2.2. conf t
        2.3. int f1/1
        2.4. ip addr 192.168.1.129 255.255.255.192
        2.5. no shutdown
        2.6. end 
    3. Confirmar configurações
        3.1 show ip int brief 

![alt text](img/image-20.png)

    4. Definir router id (AS1, cada router Rn deve usar como ID 1.0.0.n)
        4.1 enable
        4.2 conf t 
        4.3 router opsf 1
        4.4 router-id 1.0.0.1
    5. Definir quais interfaces fazem  parte do OSPF e associar custo 
        5.1 enable 
        5.2 conf t 
        5.3 router ospf 1 

        (não executei os comandos 5.4 a 5.6, verificar se estao corretos)        
        
        (custo =Deve mudar a largura de banda de referência de modo a que seja atribuído o custo 10 a cada ligação FastEthernet. )
        5.4 TODO (Confirmar com professor) : auto-cost reference-bandwidth 10 
        
        (site para descobrir wildcard: https://jodies.de/ipcalc?host=0.0.0.0&mask1=24&mask2=)

        5.5 TODO (Confirmar com professor): network 192.168.1.0  0.0.0.127  area 0
        5.6 TODO (Confirmar com professor): network 192.168.1.128 0.0.0.63  area 0
        5.7 end
    6. Demonstração de informação OSPF
        6.1 enable 
        6.2 TODO (fazer passos em falta e tirar screenshot): show ip osf database 
        6.3 TODO (fazer passos em falta e tirar screenshot): show ip ospf interface brief
        6.4 TODO (fazer passos em falta e tirar screenshot):  show ip ospf neighbor
    7. Guardar configuração
        7.1 copy running-config startup-config


### R2 (verificar esta configuração)

    1. Configurar endereço interface f1/0
        1.1 enable
        1.2 conf t 
        1.3 int f1/0
        1.4 ip addr 192.168.1.193 255.255.255.192
        1.5 no shutdown
        1.6 exit
    2. Configurar endereço interface f1/1
        1.1 enable
        2.2 conf t 
        2.3 int f1/1
        2.4 ip addr 192.168.1.130 255.255.255.192
        2.5 no shutdown 
        2.6 exit
    3. Configurar endereço interface f2/0
        3.1 enable
        3.2 conf t 
        3.3 int f2/0
        3.4 ip addr 192.168.50.1 255.255.255.0
        3.5 no shutdown
        3.6 exit

![alt text](img/image-21.png)

    4. Configurar interface OSPF da esquerda (id = 1) e custo 
        (AS1, cada router Rn deve usar como ID 1.0.0.n)
        4.1 router opsf 1
        4.2 router-id 1.0.0.2

        (não executei os comandos 4.3 a 4.5, verificar se estao corretos) 
        4.3 TODO (Confirmar com professor) : auto-cost reference-bandwidth 10 

        4.4 TODO (Confirmar com professor): network 192.168.1.192  0.0.0.63 area 0
        4.5 TODO (Confirmar com professor): network 192.168.1.128 0.0.0.63  area 0
        4.6 end
    5. Configurar interface OSPF da direita (id = 2) e custo 
        (No AS2, cada router Rn deve usar como ID 2.0.0.n)
        5.1 router opsf 2
        5.2 router-id 2.0.0.2

        (não executei os comandos 5.3 e 5.4, verificar se estao corretos) 
        5.3 TODO (Confirmar com professor) : auto-cost reference-bandwidth 10 
        5.4 TODO (Confirmar com professor): network 192.168.50.0  0.0.0.255 area 0
        5.4 end
    6. Demonstração de informação OSPF
        6.1 enable 
        6.2 TODO (fazer passos em falta e tirar screenshot): show ip osf database 
        6.3 TODO (fazer passos em falta e tirar screenshot): show ip ospf interface brief
        6.4 TODO (fazer passos em falta e tirar screenshot):  show ip ospf neighbor
    7. Guardar configuração
        7.1 copy running-config startup-config


### R3 (verificar esta configuração)
    1. Configurar endereço interface f1/0
        1.1 enable
        1.2 conf t 
        1.3 int f1/0
        1.4 ip addr 192.168.60.1 255.255.255.0
        1.5 no shutdown
        1.6 exit
    2. Configurar endereço interface f2/0 (Duvida: nao sei qual a mascara desta interface)
        2.1 enable
        2.2 conf t 
        2.3 int f2/0
        2.4 ip addr 192.168.100.1 ?.?.?.?
        2.5 no shutdown
        2.6 exit
    3. Configurar endereço interface f1/1
        3.1 enable
        3.2 conf t 
        3.3 int f1/1
        3.4 ip addr 192.168.50.2 255.255.255.0
        3.5 no shutdown
        3.6 exit
    4. Confirmar configurações
        4.1 show ip int brief 

**TODO:colocar print de 'show ip int brief '  assim que configurar interface f2/0**

    5. Definir router id (No AS2, cada router Rn deve usar como ID 2.0.0.n)
        5.1 router opsf 1
        5.2 router-id 2.0.0.3
    
    6. Definir quais interfaces fazem  parte do OSPF e associar custo 
        6.1 enable 
        6.2 conf t 
        6.3 router ospf 1 

        (não executei os comandos 6.4 a 6.7, verificar se estao corretos)        
        
        (custo =Deve mudar a largura de banda de referência de modo a que seja atribuído o custo 10 a cada ligação FastEthernet. )
        6.4 TODO (Confirmar com professor) : auto-cost reference-bandwidth 10 
        
        (site para descobrir wildcard: https://jodies.de/ipcalc?host=0.0.0.0&mask1=24&mask2=)

        6.5 TODO (Confirmar com professor): network 192.168.50.0  0.0.0.255  area 0
        6.6 TODO (Confirmar com professor): network 192.168.60.0  0.0.0.255  area 0
        (Completar com a wildcard correto,  não sei qual é mascara)
        6.7 TODO (Confirmar com professor): network 192.168.100.0  ?.?.?.?  area 0
        6.8 end 

    7. Demonstração de informação OSPF
        7.1 enable 
        7.2 TODO (fazer passos em falta e tirar screenshot): show ip osf database 
        7.3 TODO (fazer passos em falta e tirar screenshot): show ip ospf interface brief
        7.4 TODO (fazer passos em falta e tirar screenshot):  show ip ospf neighbor
    8. Guardar configuração
        8.1 copy running-config startup-config


### R4 (verificar esta configuração) 
    (TODO: falta fazer, não sei qual a mascara)
    1. Configurar endereço interface f1/0
        1.1 enable
        1.2 conf t 
        1.3 int f1/0
        1.4 ip addr 192.168.100.253 ?.?.?.?
        1.5 no shutdown
        1.6 exit
    2. Confirmar configurações
        2.1 show ip int brief 

**TODO:colocar print de 'show ip int brief '  assim que configurar interface f1/0**

    3. Definir router id (No AS2, cada router Rn deve usar como ID 2.0.0.n)
        3.1 router opsf 1
        3.2 router-id 2.0.0.4

    4. Definir quais interfaces fazem  parte do OSPF e associar custo 
        4.1 enable 
        4.2 conf t 
        4.3 router ospf 1 
        (não executei os comandos 6.4 e 4.5, verificar se estao corretos)        
        
        (custo =Deve mudar a largura de banda de referência de modo a que seja atribuído o custo 10 a cada ligação FastEthernet. )
        4.4 TODO (Confirmar com professor) : auto-cost reference-bandwidth 10 
        
        (site para descobrir wildcard: https://jodies.de/ipcalc?host=0.0.0.0&mask1=24&mask2=)
        (Completar com o wilcard correto,  não sei qual é mascara)
        4.5 TODO (Confirmar com professor): network 192.168.100.0  ?.?.?.?  area 0
        4.6 end
    
    5. Demonstração de informação OSPF
        5.1 enable 
        5.2 TODO (fazer passos em falta e tirar screenshot): show ip osf database 
        5.3 TODO (fazer passos em falta e tirar screenshot): show ip ospf interface brief
        5.4 TODO (fazer passos em falta e tirar screenshot):  show ip ospf neighbor
    6. Guardar configuração
        6.1 copy running-config startup-config


### R5 (verificar esta configuração) - TODO

![alt text](duvida.png)

     Temos que determinar a mascara correta para cada subrede(tem de ser uma mascara que permita apenas 2 endereços de host para eveitar confusoes com outras sbunetes -> ver video moodle)

### R6 (verificar esta configuração) - TODO

### R7 (verificar esta configuração) - TODO

### R8 (verificar esta configuração) - TODO

### R9 (verificar esta configuração) - TODO
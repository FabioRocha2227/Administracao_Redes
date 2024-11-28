# Trabalho 4 - DNS 

## 1- No dns.admredes.pt, descubra o servidor de email do domínio dept.admredes.pt usando host -t mx dept.admredes.pt. (outRes)

## 2- No dns.dept.admredes.pt, limpe a cache de DNS (rndc flush) e inicie uma captura wireshark. Em term1, tente descobrir o servidor de email do domínio admredes.pt. Explique o que se passou. (capRes + texRes) 

## 3- No dns.dept.admredes.pt, limpe cache de DNS e inicie uma captura wireshark. Em term1 use o comando dig para resolver o nome www.jn.pt. 

### a.Recorrendo à captura feita, diga qual foi o caminho seguido pelo(s) pedido(s) do registo A e respectiva(s) resposta(s). Na imagem da captura deverá mostrar apenas os pacotes relevantes. (capRes + texRes)

### b. Indique se a resposta dada ao terminal é autoritária, identificando os campos da mensagem que o levam a essa conclusão. (capRes + texRes) 

### c. Diga se a resposta obtida pelo dns.dept.admredes.pt é autoritária e justifique. (texRes)

### d. Note que existe mais do que um endereço para este nome e que a ordem dos endereços é round-robin. Que vantagens se podem obter destes factos? (texRes)

## 4- Diga o que entende por glue record e qual a sua utilidade. Foi necessário usar um nesta montagem? Em caso afirmativo, em que máquina? (texRes) 

## 5- Quando é usado NAT, a resolução de endereços tem que ser diferente consoante o pedido veio de uma máquina interna ou externa. Isto faz-se usando vistas. 


> **Nota**: Nesta pergunta vamos alterar o NAT no router R1 para NAT puro (sem tradução de portas), de modo a que as máquinas da rede 172.16.0.0/24 sejam vistas no exterior como pertencendo à rede 192.168.80.0/24, mantendo a parte de host. Para tal, corra em R1 (modo de configuração) os seguintes comandos:
    no ip nat inside source list 2 interface FastEthernet2/0 overload
ip nat inside source static network 172.16.0.0 192.168.80.0 /24 no-alias no-payload

### a. Configure vistas no dns.admredes.pt de modo a que, quando os pedidos são feitos de fora das redes 172.16.0.0/24 ou 10.0.0.0/24, os endereços da rede 172.16.0.0/24 sejam vistos como estando na rede 192.168.80.0/24. Indique as alterações que teve de fazer nos ficheiros de configuração. (confRes) 

### b. Teste a configuração da vista interna descobrindo o endereço IP de router.admredes.pt a partir do próprio dns.admredes.pt. (outRes) 

### c.Na própria máquina onde está a correr o GNS3, que também está ligada à rede 192.168.123.0/24, adicione uma rota para a rede 192.168.80.0/24 através do 192.168.123.a (o endereço IP obtido por DHCP pelo router Cisco na interface f2/0). Para testar a configuração da vista externa, obtenha o endereço IP de router.admredes.pt a partir dessa máquina usando o comando host router.admredes.pt. 192.168.80.2 (outRes) 

> **Nota**:  Após concluir esta questão, reponha a configuração original no router R1. Reponha também a configuração anterior a esta pergunta (sem vistas) no dns.admredes.pt. 

## 6- Sendo o DNS um serviço fundamental na rede, praticamente todas as redes implementam, além do master, um ou mais servidores slave para obter tolerância a falhas e/ou distribuição de carga. Faça as alterações necessárias aos ficheiros de configuração para que dns.admredes.pt funcione também como slave de dns.dept.admredes.pt para o subdomínio dept.admredes.pt. 

### a. Indique as alterações que teve de fazer nos ficheiros de configuração. (confRes) 

### b. Faça uma captura de pacotes em dns.dept.admredes.pt e active as novas configurações. Identifique na captura a transferência do domínio dept.admredes.pt do master para o slave. (capRes) Se já tinha activado as novas configurações antes de fazer a captura, provavelmente não será feita nova transferência de domínio. Se isto acontecer, vá ao dns.admredes.pt e faça rndc retransfer <zona> para cada uma das zonas slave. 

### c. Antes da transferência de domínio é feito um outro pedido para o registo SOA. Explique para que serve este registo e por que razão é pedido antes de fazer a transferência de domínio. (texRes) 

### d. Verifica alguma diferença entre o protocolo de transporte usado para a transferência de domínio e o normalmente usado para as outras perguntas DNS? Qual a razão para essa diferença? (texRes) 

### e. No dns.admredes.pt, faça uma captura de pacotes na pseudo-interface any (use o comando tcpdump -i any -w askslave.pcap port 53 &). Teste a nova configuração descobrindo o servidor de email de dept.admredes.pt a partir de dns.admredes.pt. Pare a captura (killall -INT tcpdump). (capRes) 

### f. Copie o ficheiro askslave.pcap para a VM onde está a correr o GNS3 e abra-o no Wireshark. Verifique se a resposta DNS que obteve na alínea anterior é autoritária e explique porquê. (texRes) 

## 7- A configuração da zona ".", do tipo hint, necessita dum ficheiro (normalmente chamado named.ca). Observe o conteúdo desse ficheiro e explique a sua função. (texRes) 
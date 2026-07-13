![alt text](img/logo_eqx.jpg "Logo EQX")

# Premissas de Switch
---

> **Revisão vigente — 13/07/2026.** Premissa multivendor para switching. A documentação do modelo/release prevalece; padrões internos cobrem implantação, governança e suporte.

## Premissas vigentes 2026

### Plataforma, versão e desenho

- **[Interno]** Novas aquisições devem estar no portfólio homologado, com suporte pelo contrato, fontes redundantes e ópticos/DACs certificados.
- **[Fabricante]** Selecionar release recomendado para produção da família (Cisco recommended release, Juniper suggested release, Aruba/Dell recommended release), após release notes, PSIRT e lifecycle. Não usar “latest” como regra.
- **[Interno]** Pares stack/VSX/VLT/VC/MLAG usam release e hardware compatíveis; versão mista somente na janela e se o fabricante suportar ISSU/upgrade.
- **[Interno]** Desenho documenta VLAN/VRF, STP domain/root, LAG/MLAG, routing, MTU, oversubscription, domínios de falha e capacidade.

### Segurança e gerenciamento

- **[Interno]** Management em VRF/VLAN dedicada, protegido por ACL/firewall e jump host. Proibir Telnet, HTTP, SNMPv1/v2c e serviços de descoberta desnecessários.
- **[Fabricante]** Usar SSHv2, HTTPS/TLS suportado, SNMPv3 authPriv, AAA central (TACACS+/RADIUS), RBAC e certificados. Conta local break-glass no cofre.
- **[Interno]** NTP autenticado quando disponível, syslog/SIEM, banners, timeout, logging de comandos/configurações e backup automático versionado.
- **[Interno]** Segredos nunca no documento. Rotação e acesso seguem política corporativa; MFA no caminho administrativo quando o equipamento não suportar diretamente.

### Camada 2, uplinks e proteção

- **[Fabricante]** STP deve permanecer habilitado onde há L2. Definir root primário/secundário; BPDU Guard/edge em portas de acesso e Root/Loop Guard onde o desenho exigir.
- **[Interno]** Storm control, DHCP snooping, Dynamic ARP Inspection, IP Source Guard e port-security devem ser avaliados/testados por segmento antes de impor.
- **[Interno]** Trunks permitem apenas VLANs necessárias; native VLAN não deve transportar usuário/gerência. Portas não usadas ficam desabilitadas em VLAN de quarentena.
- **[Fabricante]** LACP/MLAG/VLT/VSX segue limites e topologia do modelo. Não configurar port-channel estático ou LACP sem correspondência ponta a ponta.
- **[Interno]** MTU deve ser consistente ponta a ponta. Jumbo frames somente por requisito e teste; não aplicar globalmente sem análise.

### Camada 3, mudança e suporte

- **[Interno]** Routing protocols exigem autenticação quando suportada, passive-interface, filtros/prefix-lists, sumarização e limites de adjacência conforme projeto.
- **[Interno]** Mudança exige backup, diff, health check, console/OOB, rollback, janela e validação de redundância. Preservar `show tech/support` antes de reboot quando possível.
- **[Interno]** Aceite: failover de uplink/peer/fonte, STP, LACP/MLAG, routing, MTU, monitoração, syslog, AAA, backup e documentação testados.
- **[Interno]** Operação monitora CPU/RAM, temperatura, fontes/fans, interfaces/errors/discards, transceivers, STP changes, LAG/peers, routing e config drift.

### Referências oficiais

- [Cisco recommended releases for Catalyst](https://www.cisco.com/c/en/us/support/docs/switches/catalyst-6500-series-switches/214946-recommended-releases-for-catalyst-2960-3.html)
- [Juniper switch configuration best practices](https://www.juniper.net/documentation/us/en/software/mist/mist-wired/topics/concept/best-practices-wired-config.html)
- [Aruba AOS-CX hardening guides](https://www.arubanetworks.com/techdocs/AOS-CX/)
- Documentação SmartFabric OS10/Dell Networking do modelo no Dell Support.

## Conteúdo legado — histórico interno

> Comandos e limites abaixo são exemplos por plataforma; só aplicar após confirmar modelo, sistema operacional e release.

# Sumário
-   [Objetivo](#objetivo)
-   [Documentação](#documentação)
  -   [ServiceNow](#servicenow)
  -   [Runbook](#runbook)
  -   [ETI](#eti)
  -   [Topologia](#topologia)
-   [Atlas](#atlas)
-   [Posicionamento no Rack](#posicionamento-no-rack)
-   [Recomendações de Hardware](#recomendações-de-hardware)
-   [Recomendações de Software](#recomendações-de-software)
-   [Licenciamento](#licenciamento)
-   [Configurações de Sistema](#configurações-de-sistema)
    -   [Hostname](#hostname)
    -   [Data e Hora](#data-e-hora)
-   [Protocolos e Serviços](#protocolos-e-serviços)
    -   [TELNET](#telnet)
    -   [HTTP](#http)
    -   [HTTPS](#https)
    -   [SSH](#ssh)
    -   [FINGER](#finger)
    -   [BOOTP SERVER](#bootp-server)
    -   [LLDP](#lldp)
-   [Monitoração](#monitoração)
    -   [PING](#ping)
    -   [SNMP](#snmp)
-   [Backup](#backup)
-   [Log](#log)
-   [Interface de Gerenciamento](#interface-de-gerenciamento)
-   [Controle de Acesso](#controle-de-acesso)
    -   [Acesso Administrativo Remoto](#acesso-administrativo-remoto)
    -   [Autenticação](#autenticação)
    -   [Política de Senhas](#pol%C3%ADtica-de-senhas)
    -   [ACL](#acl)
-   [Interface](#interface)
    -   [DESCRIÇÃO](#descrição)
    -   [SWITCH-PORT MODE](#switch-port-mode)
    -   [BPDU GUARD / BPDU PROTECTION](#bpdu-guard-bpdu-protection-flexível)
    -   [SPANNING-TREE ROOT GUARD / ROOT PROTECTION](#spanning-tree-root-guard-root-protection-flexível)
    -   [SPANNING-TREE BPDU FILTER](#spanning-tree-bpdu-filter-flexível)
    -   [STATISTICAS](#spanning-tree-bpdu-filter-flexível)
    -   [STORM-CONTROL](#storm-control-flexível)
    -   [FLOW-CONTROL](#flow-control)
    -   [DHCP](#dhcp)
    -   [INTERFACE IP](#interface-ip)
    -   [PROXY ARP](#proxy-arp)
    -   [IP DIRECTED BROADCAST](#ip-directed-broadcast)
    -   [IP MASK-REPLY](#ip-mask-reply)
    -   [IP REDIRECTS](#ip-redirects)
    -   [IP SOURCE ROUTING](#ip-source-routing)
    -   [SPANNING-TREE](#spanning-tree)
    -   [VLAN](#vlan)
    -   [NATIVE VLAN](#native-vlan)
    -   [LINK AGREGATION](#link-agregation)
-   [VSTACK](#vstack)
-   [SCHEDULER](#scheduler)
-   [TCP-KEEPALIVES](#tcp-keepalives)
-   [Alta disponibilidade](#alta-disponibilidade)
    -   [STACKING - HPE ARUBA](#stacking-hpe-aruba)  
    -   [VSX - ARUBA](#vsx-aruba)  
    -   [STACKING - DELL](#stacking-dell)  
    -   [VLT - DELL](#vlt-dell)  
    -   [VIRTUAL CHASSIS - JUNIPER](#virtual-chassis-juniper)  
- [KBs relacionados](#kbs-relacionados)  
- [Revisão](#revisão)   

---

### Objetivo
---

- Especificar parâmetros e configurações para a implantação e operação de switches Topo de Rack (TOR) na infraestrutura para clientes gerenciados seguindo as melhores práticas de mercado e atendendo os pré requisitos da infraestrutura provida ao rack do cliente.
	
- Apresentar recomendações e particularidades de implementação entre os fabricantes suportados.

Este documento menciona também os KB’s gerados deste a última publicação do documento onde há um maior detalhamento do cenário e da falha tratada.  

Este documento foi elaborado para os equipamentos listados abaixo, suportados atualmente na [matriz de produtos](https://equinixinc.sharepoint.com/sites/intranet/br/sitepages/Product%20and%20Offer%20Management/Switch-TOR.aspx):  

**SWITCH - PORTFOLIO EQUINIX**  
| **Tipo** | **Elemento** | **Fabricante** | **Modelo** | 
|:---:|:---:|:---:|:---:|
| L2 - Network Hardware - Switch - 24 Ports 1G 				   | 4 x 1/10G SFP/SFP+						| Dell 	  | N1524			|
| L2 - Network Hardware - Switch - 48 Ports 1G 				   | 4 x 1/10G SFP/SFP+						| Dell    | N1548			|
| L2 - Network Hardware - Switch - 48 Ports 10G SFP+ 		   | 4 x 100G QSFP28 + 2 x 40G QSFP+		| Dell    | S4148F-ON		|
| L3 - Network Hardware - Switch - 24 Ports 1G 				   | 4 x 1/10G SFP/SFP+						| Dell    | S3124			|
| L3 - Network Hardware - Switch - 48 Ports 1G 				   | 4 x 1/10G SFP/SFP+						| Dell    | S3148			|
| L3 - Network Hardware - Switch - 48 Ports 10G SFP+ 		   | 4 x 100G QSFP28 + 2 x 40G QSFP+		| Dell    | S4148F-ON		|
| L3 - Network Hardware - Switch - 24 Ports 1G 				   | 4 x 1/10G SFP/SFP+						| Juniper | EX4400-24T*	    |
| L3 - Network Hardware - Switch - 48 Ports 1G 				   | 4 x 1/10G SFP/SFP+ 					| Juniper | EX4400-48T*	    |
| L3 - Network Hardware - Switch - 24 Ports 10/25G SFP+//SFP28 | 4 x 100G QSFP28 + 2 x 200G QSFP28-DD 	| Dell    | S5224F-ON		|
| L3 - Network Hardware - Switch - 24 Ports 10/25G SFP+//SFP28 | 4 x 100G QSFP28 + 2 x 200G QSFP28-DD	| Dell    | S5248F-ON		| 

**SWITCH - CUSTOM CERTIFIED**  

| **Tipo** | **Elemento** | **Fabricante** | **Modelo** | 
|:--------------------------------------------:|:------------------------------------:|:-------:|:---------------:|
| L2 - Network Hardware - Switch - 24 Ports 1G | 4 x 1/10G SFP/SFP+										| Cisco 	| C9200L-24T-4X-E |
| L2 - Network Hardware - Switch - 24 Ports 1G | 4 x 1/10G SFP/SFP+										| Dell 		| N1524 |				
| L2 - Network Hardware - Switch - 24 Ports 1G | 4 x 1/10G SFP/SFP+										| Juniper 	| EX2300-24T |			
| L2 - Network Hardware - Switch - 24 Ports 1G | 4 x 1/10G SFP/SFP+										| HPE 		| 5140 24G - R9L61A |			
| L2 - Network Hardware - Switch - 48 Ports 1G | 4 x 1/10G SFP/SFP+										| Cisco 	| C9200L-48T-4X-E |
| L2 - Network Hardware - Switch - 48 Ports 1G | 4 x 1/10G SFP/SFP+										| Dell 		| N1548 |	
| L2 - Network Hardware - Switch - 48 Ports 1G | 4 x 1/10G SFP/SFP+										| HPE 		| 5140 48G - R9L62A |
| L2 - Network Hardware - Switch - 24 Ports 10G SFP+ | 4 x 40G QSFP+									| Cisco 	| C9500-24Y4C-E |		
| L2 - Network Hardware - Switch - 24 Ports 10G SFP+ | 4 x 40G QSFP+									| Juniper 	| EX4600-40F-AFO |		
| L2 - Network Hardware - Switch - 24 Ports 10G SFP+ | 4 x 40G QSFP+									| HPE 		| 5710 - R9L62A |		
| L2 - Network Hardware - Switch - 48 Ports 10G SFP+ | 4 x 40G QSFP+									| Cisco 	| C9500-48Y4C-E |	
| L2 - Network Hardware - Switch - 48 Ports 10G SFP+ | 4 x 40G QSFP+									| Juniper 	| QFX5110-48S-AFO2 |
| L2 - Network Hardware - Switch - 48 Ports 10G SFP+ | 4 x 40G QSFP+									| HPE 		| 5710 - JL585A |
| L2 - Network Hardware - Switch - 24 Ports 10G SFP+ | 4 x 100G QSFP28 + 2 x 40G QSFP+					| Dell 		| S4148F-ON |
| L2 - Network Hardware - Switch - 24 Ports 10G SFP+ | 4 x 100G QSFP28 + 2 x 40G QSFP+					| Aruba 	| 8360-32Y4C v2 - JL700C |
| L3 - Network Hardware - Switch - 24 Ports 1G | 4 x 1/10G SFP/SFP+										| Cisco 	| C9300L-24T-4X-A |
| L3 - Network Hardware - Switch - 24 Ports 1G | 4 x 1/10G SFP/SFP+										| Juniper 	| EX4400-24T |			
| L3 - Network Hardware - Switch - 24 Ports 1G | 4 x 1/10G SFP/SFP+										| HPE 		| 5140 24G - R9L61A |
| L3 - Network Hardware - Switch - 48 Ports 1G | 4 x 1/10G SFP/SFP+										| Dell 		| S3148 |
| L3 - Network Hardware - Switch - 48 Ports 1G | 4 x 1/10G SFP/SFP+										| Juniper 	| EX4400-48T |
| L3 - Network Hardware - Switch - 48 Ports 1G | 4 x 1/10G SFP/SFP+										| HPE 		| 5140 48G - R9L62A |
| L3 - Network Hardware - Switch - 48 Ports 1G | 4 x 10G SFP+											| Juniper 	| EX3300-48T |	 
| L3 - Network Hardware - Switch - 24 Ports 1G | 4 x 10G SFP+											| Juniper 	| EX3300-24T |	 
| L3 - Network Hardware - Switch - 48 Ports 1G | 4 x 40G QSFP+											| Cisco 	| C9300L-48T-4X-A |	 
| L3 - Network Hardware - Switch - 24 Ports 10G SFP+ | 4 x 40G QSFP+									| Cisco 	| C9500-24Y4C-A |
| L3 - Network Hardware - Switch - 24 Ports 10G SFP+ | 4 x 40G QSFP+									| Juniper 	| EX4600-40F-AFO |
| L3 - Network Hardware - Switch - 24 Ports 10G SFP+ | 4 x 40G QSFP+									| HPE 		| 5710 - R9L62A |
| L3 - Network Hardware - Switch - 48 Ports 10G SFP+ | 4 x 40G QSFP+									| Cisco 	|  C9500-48Y4C-A |
| L3 - Network Hardware - Switch - 48 Ports 10G SFP+ | 4 x 40G QSFP+									| Juniper 	| QFX5110-48S-AFO2 |
| L3 - Network Hardware - Switch - 48 Ports 10G SFP+ | 4 x 40G QSFP+									| HPE 		| 5710 - JL585A |
| L3 - Network Hardware - Switch - 24 Ports 10G SFP+ | 4 x 100G QSFP28 + 2 x 40G QSFP+ 					| Dell 		| S4148F-ON |
| L3 - Network Hardware - Switch - 24 Ports 10G SFP+ | 4 x 100G QSFP28 + 2 x 40G QSFP+					| Aruba 	| 8360-32Y4C v2 - JL700C |
| L3 - Network Hardware - Switch - 24 Ports 10/25G SFP+/SFP28 | 6 x 40/100G QSFP+/QSFP28				| Cisco 	|  N9K-C93180YC-FX3 |
| L3 - Network Hardware - Switch - 24 Ports 10/25G SFP+/SFP28 | 6 x 40/100G QSFP+/QSFP28				| Juniper 	| QFX5120-48Y-AFO2 |	  
| L3 - Network Hardware - Switch - 24 Ports 10/25G SFP+/SFP28 | 4 x 100G QFSP28 + 2 x 200G QSFP28-DD	| Dell 		| S5224F-ON |
| L3 - Network Hardware - Switch - 24 Ports 10/25G SFP+/SFP28 | 4 x 100G QFSP28 + 2 x 200G QSFP28-DD	| Aruba 	| 8360-32Y4C v2 - JL700C |
| L3 - Network Hardware - Switch - 48 Ports 10/25G SFP+ | 4 x 100G QFSP28 + 2 x 200G QSFP28-DD 			| Dell 		| S5248F-ON |
| L3 - Network Hardware - Switch - 48 Ports 10/25G SFP+ | 4 x 100G QFSP28 + 2 x 200G QSFP28-DD 			| Aruba 	| 8360-48Y6C v2 FB 5F 2AC - JL704C |
| L3 - Network Hardware - Switch - 48 Ports 10/25G SFP+ | 6 x 40G/100G QSFP+/QFSP28						| Juniper 	| QFX5120-48Y-AFO2 |
| L3 - Network Hardware - Switch - 48 Ports 10/25G SFP+ | 6 x 40G/100G QSFP+/QFSP28						| Cisco 	|  N9K-C93180YC-FX3 |	  
| L3 - Network Hardware - Switch - 48 Ports 10/25G SFP+/SFP28 | 6 x 40/100G QSFP+/QSFP28				| Cisco 	|  N9K-C93180YC-FX3 |
| L3 - Network Hardware - Switch - 48 Ports 10/25G SFP+/SFP28 | 6 x 40/100G QSFP+/QSFP28				| Dell 		| S5248F-ON | 
| L3 - Network Hardware - Switch - 48 Ports 10/25G SFP+/SFP28 | 6 x 40/100G QSFP+/QSFP28				| Juniper 	| QFX5120-48Y-AFO2 | 
|                                                                                                       |         	|                      |



### Documentação
---
A ferramenta padrão para documentação dos ativos dos clientes é o ServiceNow, portanto todas as informações sobre o cliente devem estar contidas nesse sistema.

### ServiceNow
---
-   #### Runbook

	O objetivo desse documento é ser um “snapshot” de configurações do equipamento do cliente. Eventualmente utilizado em caso de RD do equipamento.

-   #### ETI

	O objetivo desse documento é conter as informações sobre o que foi acordado com o cliente referente à capacidade e serviços disponibilizados no equipamento.

-   #### Topologia

	O objetivo desse documento é fornecer uma visão geral do ambiente e, se possível, a localização lógica do equipamento atual. 

### Atlas
---
[Obrigatório em todos os clientes OSA ou TM]

Todo cliente gerenciado deverá ter as premissas do Atlas (Análise de Ambiente) configuradas conforme [premissa da ferramenta Atlas](https://msh-git.equinix.com.br/br-msh-problem/Premissas/-/blob/master/Premissa.Atlas.md)

### Posicionamento no Rack
---

#### Posicionamento  
  
- Equipamento deve ser instalado no rack do cliente seguindo o posicionamento de ventilação do rack ( ar quente / frio).  
- Planejar espaçamento no rack para instalação de organizadores de cabos.  

#### Elétrica  

A infraestrutura elétrica do datacenter Equinix disponibiliza:  

- 2 réguas no rack do cliente com fonte de energia independente.  
- Quando há equipamentos sem fonte redundante e sem redundância, é disponibilizado ao cliente 1 régua STS (STATIC TRANSFER SWITCH) ou mais.   
- Seguir as melhores práticas para conexão elétrica dos equipamentos no rack seguindo o fluxograma a seguir:  

![alt text](img/fluxo-eletrica.png "Fluxo Eletrica")

Figura 1 - Diagrama para alimentação de switches

### Recomendações de Hardware
---

- Utilizar modelos de fibra seguindo a recomendação de comprimento e modelo do fabricante.
- Utilizar cabos para configuração em STACK homologados e recomendados pelo fabricante. 
- Utilizar SFP homologadas e suportados pelo fabricante. Seguem abaixo link para consulta:

- ***Aruba***
    - https://www.arubanetworks.com/techdocs/Switches/xcvrs/xcvr_guide/Content/Chp_sfp/gig-sfp-mod-spe-com.htm  
      
- ***Cisco***
    - https://tmgmatrix.cisco.com/

- ***Dell***
    - https://www.delltechnologies.com/asset/en-us/products/networking/technical-support/Dell_EMC_Networking_Optics_Spec_Sheet.pdf

- ***Juniper:*** 
    - https://apps.juniper.net/hct/product/#prd=EX2200
    - https://apps.juniper.net/hct/product/#prd=EX2300
    - https://apps.juniper.net/hct/product/#prd=EX3300
    - https://apps.juniper.net/hct/product/#prd=QFX5100 



### Recomendações de Software
---

- Verificar junto ao site do fabricante a versão atual recomendada pelo fabricante:
- Ler release notes da versão com foco nos itens erros conhecidos e avaliar se alguns destes erros podem afetar o equipamento nos aspectos de disponibilidade, performance e segurança.
- Caso o equipamento vá compor um STACK ou virtual-chassis já existente, atualizar os equipamentos para a versão de software dos demais switches já em produção desde que esta versão esteja dentro da matriz de suporte do fabricante.
- Em caso dúvidas de qual versão utilizar, abrir um chamado do fabricante evidenciando a confirmação de versão recomendada.

### Licenciamento
---

- Verificar se as licenças dos switches suportam os recursos de configuração e capacidade requeridos pelo projeto como por exemplo throughput e protocolos.   

***Particularidades Aruba***
- Licenciamento de todos os recursos nativo.   
- Não é necessário conexão com internet ou upload de arquivos para ativação de licença.  

***Particularidades Cisco***

- Há 3 tipos de licença:  
    - LAN Base: Enterprise access Layer 2 switching features: recursos de camada 2 com limite de 255 VLANS.  
    - IP Base: Enterprise access Layer 3 switching features: Todos os recursos da licença LAN Base com até 1024 VLANS.  
    - IP Services: Advanced enterprise Layer 3 switching (IPv4 and IPv6) features: Provê todos os serviços incluindo protocolos avançados de camada 3 como EIGRP, OSPF, BGP, PIM e roteamento IPV6 como OSPFv3 e EIGRPv6.  
- Verificar os recursos suportados e licenciamento necessário no site do fabricante (https://www.cisco.com/c/en/us/products/collateral/switches/catalyst-3850-series-switches/datasheet_c78-720918.html#Licensing).  
  
***Particularidades Juniper***
- Requer licença para ativar e utilizar alguns recursos. Verificar recursos suportados  e licenças necessárias no site de suporte do fabricante (https://www.juniper.net/documentation/en_US/release-independent/licensing/information-products/pathway-pages/licensing.html).
- Para virtual-chassis, duas licenças são recomendadas para redundância. Uma para o equipamento com função de MASTER e outra para o equipamento com função de backup. 

### Configurações de Sistema
---

#### Hostname

- Equipamentos não implantados pela Equinix que passarem a ser gerenciados pela Equinix devem ter o HOSTNAME alterado para o padrão Equinix. 
- A configuração do HOSTNAME de equipamentos novos implantados pela Equinix deve seguir o padrão:

    > SW[RK|AS]??-??????-????.[SP1|SP2|SP3|SP4|SP5|RJ1|RJ2]


  Onde: 
    - ***'SW':*** Siglas que indicam que o host é um Switch.   
    - ***'RK':*** Siglas que indicam que o host é um switch Topo de Rack (TOR).  
    - ***'AS':*** Siglas que indicam que o switch é usado exclusivamente para prover acesso e serviços de outras operadoras/clientes.  
    - ***'00':*** Número do switch no STACK.  Exemplo: 01, 02, 03, 04.  
    - ***'000000-0000':*** Número do rack.  
    - ***'.IBX:'*** IBX correspondente ao site que o equipamento está instalado. Exemplo: SP1, SP2, SP3, SP4, SP5, RJ1 ou RJ2.   
  
Exemplo:

    > SWRK01-123123-1122.SP3

### Data e Hora
---

- Configurar Servidor de NTP Primário e Secundário Equinix conforme documentação do projeto [NTPMS](<https://git.mop.equinix.com.br/br-hsa/ntp-server>).
- Configurar time-zone do BRAZIL, GMT -3. 
- Configurar ACL permitindo o equipamento consultar o Servidor de NTP Equinix especificando endereços IP e protocolo.  
  
### Protocolos e Serviços
---

#### TELNET
- Desabilitar acesso remoto via telnet.

#### HTTP
- Desabilitar o protocolo http.

#### HTTPS
- Desabilitar o protocolo https.

#### SSH
- Configurar SSH na versão 2 com timeout de sessão 10 minutos.
- Configurar ACL permitindo o acesso SSH através dos servidores de monitoração Equinix correspondente ao IBX e Pontes de Acesso Linux especificando endereços IP e protocolo. O acesso SSH dos servidores de Monitoração deve-se ao serviço de zabbix como Backup. 
  
#### FINGER
- Desabilitar serviço de FINGER.

#### TCP/UDP SMALL-SERVERS
- Desabilitar TCP/UDP small-servers

#### BOOTP SERVER
- Desabilitar BOOTP SERVER.
- Desabilitar BOOTP DHCP SERVER.

#### LLDP 
- Habilitar o protocolo globalmente. [Flexível]
- Desabilitar protocolo nas interfaces de comunicação com equipamento que não são gerenciamento total. Exemplo:
    - Interfaces de link de operadoras.
    - Interfaces de comunicação com equipamentos administrados pelo cliente como por exemplo servidores, roteadores, switches, firewalls, balanceadores de carga, STORAGE etc.  

#### MONITORAÇÃO
- Todos os equipamentos gerenciados devem ser monitorados pela ferramenta ZABBIX. 
- A configuração inicial da monitoração deve atender aos requisitos :
    - CPU
    - Memória
    - Interfaces ativas
    - Tráfego em bits
    - Status do Virtual Chassis (JUNIPER), STACK (DELL/CISCO), VLT/VPC(DELL/CISCO), VSF/VSX(ARUBA).      

- A definição de thresholds, alarmes e abertura de chamado automática não é definida na premissa de switches.

##### PING 
- Todos os equipamentos gerenciados devem responder a PING para os servidores de Monitoração para monitoração de tempo de resposta e conectividade com os equipamentos. 
- Todos os equipamentos gerenciados devem responder a PING para as pontes de acesso Linux. [Flexível]
- Configurar ACL permitindo o equipamento responder ao comando ping apenas para os servidores de monitoração Equinix especificando endereços IP e protocolo. Segue lista dos servidores de Monitoração e seus respectivos sites conforme KB [KB0014772](<https://equinixcsm.service-now.com/kb_view.do?sysparm_article=KB0014772>) - **MSH NOC - Lista de Zabbix Proxy**.

##### SNMP
- Deve ser configurado nos equipamentos gerenciados permitindo a leitura de status dos demais indicadores (CPU, memória, tráfego e status de interfaces).
- Configurar SNMP V2.
- Não utilizar a Community SNMP Public.
- Habilitar SNMP apenas no modo leitura.
- Configurar ACL permitindo o acesso SNMP ao switch apenas dos servidores de monitoração Equinix especificando endereços IP e portas utilizadas pelos servidores. Segue lista dos servidores de Monitoração e seus respectivos sites conforme KB [KB0014772](<https://equinixcsm.service-now.com/kb_view.do?sysparm_article=KB0014772>) - **MSH NOC - Lista de Zabbix Proxy**.
- É permitido o cliente realizar leitura SNMP nos equipamentos seguindo os pré-requisitos: [Flexível]
    - Não compartilhar com o cliente a community SNMP EQUINIX.
    - Criar ACL permitindo apenas o servidor indicado pelo cliente de fazer a leitura. 
    - O cliente deve estar de acordo e disposto a alterar os parâmetros de configuração da sua monitoração caso a ativação passe a onerar o processamento do equipamento monitorado. 
     
***Particularidades Dell***

- Switches DELL com software versão OS10, não suportam a configuração de snmp em tabelas de roteamento (vrf's) distintas. Ao aplicar as configurações SNMP Equinix na vrf management, não é possível prover configurar SNMP para o cliente na tabela de roteamento (vrf) default. 
  
  
### Backup 
---

A infraestrutura Equinix disponibiliza servidores para armazenamento de backup dos switches. 

O backup Equinix é provido pela infraestrutura do Zabbix, através dos templates de SW's [MONBR1](<https://monbr1.equinix.com.br/zabbix>) que faz versionamento da configuração dos equipamentos, a cada alteração que haja no equipamento e caso não haja alteração não é realizado backup. 

Os Pré requiitos para ativação do Zabbix Backup e manuais para consulta e recuperação da configuração no repositório de backup estão disponíveis no KB [KB0014008](https://equinixcsm.service-now.com/kb_view.do?sysparm_article=KB0014008) - ***Backup/Restore - Projeto Zabbix como Backup***.

- Caso o switch não tenha sido provisionado pela Equinix, antes de qualquer alteração salvar um backup manual do equipamento e incluir na documentação do cliente.

- O equipamento deve sofrer backups com frequência diária e retenção no próprio servidor por 30 dias. 

- Configurar ACL permitindo o ssh ao switch a partir do servidor do proxy de monitoração ao qual o switch está configurado.
  
***Particularidades Juniper***
- Provê em arquivo local o histórico de 50 versões de configurações. 

### Log
---

Atualmente a infraestrutura do datacenter não disponibiliza um servidor de logs centralizado para armazenamento dos logs dos switches.  

Endereços IP de servidores de armazenamento de LOG identificados na configuração dos switches:

  |**IBX**|**Servidores**|
  |:-:|:-|
  |SP1| 200.155.65.22, 200.198.191.11, 200.150.148.83 e 200.150.148.88 |
  |SP2| 200.155.65.22, 200.198.191.11 e 200.150.148.83 |  
  |SP4| none |
  |RJ1| 200.150.148.88 |
  
_Observação: Em função da migração da rede de Gerencia de switches de SP3, SP4 e RJ2, os servidores de LOG acima deixaram de funcionar._

Seguem abaixo recomendações para configuração e armazenamento de logs: [Flexível]
  - Os logs devem ser retidos no próprio servidor sem compactação por pelo menos 3 meses.
  - Os logs devem ser retidos no próprio servidor com compactação por pelo menos 6 meses. 
  - Os logs podem ser excluídos após 1 ano.
  - O servidor de log deve ser acessível por toda a equipe técnica Equinix para a consulta dos logs. 
  - Os níveis de log configurados nos equipamentos devem garantir os logs de acesso, comandos aplicados e eventos de sistema.
  - O envio e recebimento de logs deve ser previsto na ACL do equipamento especificando IP e porta do servidor de LOG.
  - É permitido exportar logs dos switches gerenciados para servidor de logs de clientes.
  
#### Interface de Gerenciamento
- O acesso administrativo, monitoração, backup, ntp, log deve ser realizado através de interface dedicada (IN-BAND ou OUT-OF-BAND). Pode-se utilizar interface IN-BAND conexão via fibra.    

- Quando o switch tiver funções de L3 é obrigatório a que a interface OUT-OF-BAND ou IN-BAND esteja associada a uma instância de roteamento distinta: 
    - **vrf** para Aruba, Cisco e Dell.  
    - **routing-instance** para Juniper.  

### Controle de Acesso
---

#### Acesso administrativo remoto  

- O acesso remoto ao equipamento deve ser realizado via protocolo SSH versão 2.  

- A configuração do equipamento deve bloquear o acesso SSH do switch para outros equipamentos (em resumo, a configuração do equipamento não deve permitir que ele seja usado como ponte de acesso).   

- O acesso remoto aos equipamentos deve ser realizado através de Pontes de acesso disponibilizados pela corporação conforme indicado no KB publicado por cyberSec: [KB0015993](https://equinixcsm.service-now.com/now/nav/ui/classic/params/target/kb_view.do%3Fsys_kb_id%3Df821442393b68ed4771238797bba10ed%26preview_article%3Dtrue).  
- É permitido o cliente acessar a interface de operação do equipamento gerenciado pela Equinix seguintes critérios: [Flexível]
  - Usuário com perfil de leitura.  
  - Configuração de ACL especificando os endereços de origem que farão o acesso remoto ( preferencialmente para hosts dentro do próprio IBX).  
    - O equipamento deve suportar configuração de profile específico para disponibilizar execução de comandos de leituras  dos status de interfaces, tabela ARP, tabela de MAC-ADDRESS, CPU, memória, spanning-tree, tabela de rotas¹, ping¹ e traceroute¹.  
  - O usuário e senha serão cadastrados no SERVICE NOW seguindo os pré-requisitos de criação de senhas Equinix. 
  - O switch tem que ter uma interface IP conectada à infraestrutura do cliente por onde será realizado o acesso através de um servidor interno.  
  - Não é permitido configuração de NAT possibilitando o acesso ao equipamento pela internet.  
  
  
***¹*** _Permitir quando o equipamento tiver funcionalidade de camada 3_.

#### Autenticação

- A autenticação dos equipamentos deve ser centralizada em um sistema de autenticação disponibilizado pela infraestrutura de redes Equinix. 
- Atualmente não uma central de autenticação para switches e é feita localmente. 

- A autenticação com usuário local deve ser permitida apenas da porta de console local do equipamento ou em caso de falha de conectividade com a central de autenticação.

- O equipamento deve ter 1 usuário local com permissão total ao equipamento permitindo acesso em caso de falhas de comunicação com a central de autenticação e 2 usuários com permissão de leitura para uso da ferramenta de backup: 

  | Usuário |  Perfil |
  |:---|:---:|
  |**tecnico**|Permissão TOTAL|
  |**backup**|Permissão leitura|
  |**backupengenharia**|Permissão leitura|

- As senhas de usuários locais devem ser definidas conforme [KB0013079](<https://equinixcsm.service-now.com/nav_to.do?uri=kb_knowledge.do?sys_id=67dfd11edb544194a7f8a334ca96193a>) - **Padrão de Senhas para acesso aos switches TOR**.

- As senhas de usuários locais devem ser armazenadas com os itens de configuração no SERVICE NOW. É responsabilidade do analista que configura ou troca as senhas locais do equipamento atualizar as senhas no sistema SERVICE NOW.

- Limitar a quantidade de conexões simultâneas em 5 conexões.

- Configurar timeout de 5 minutos para sessões inativas (SSH e console).

- Senha de ***enable*** deve seguir o seguinte padrão definido no [KB0013079](<https://equinixcsm.service-now.com/nav_to.do?uri=kb_knowledge.do?sys_id=67dfd11edb544194a7f8a334ca96193a>) - **Padrão de Senhas para acesso aos switches TOR**.
 
***Particularidades Juniper***
- Não permitir conexão remota ao equipamento via protocolo SSH com o usuário root.
- Utilizar o padrão de senha de enable para definição da senha do usuário root. 

#### Política de senhas
- Senhas de usuários não definidos em premissa como por exemplo, usuários para acesso do cliente ao equipamento, devem ter um tamanho mínimo de 12 caracteres Alfa numérico e símbolos (4 Letras, 4 Números e 4 caracteres especiais) .

Exemplo:

> @7Ce$#*0P3M4

- Utilizar recursos do equipamento para criptografia de senhas. 


#### ACL
- O equipamento deve ter ACL configuradas permitindo os acessos com especificação de endereço IP de origem e porta de serviço para atendimento dos sistemas Equinix de Monitoração, Syslog, Autenticação, Pontes de Acesso.

***Particularidades Cisco:***
- É recomendado a criação de uma ACL para evitar ataques do tipo “LAND”.

Exemplo:
  
> Switch(Config)#access-list 100 deny tcp <ip do switch> 0.0.0.0 <ip do switch> 0.0.0.0  
> Switch(Conf-if)#ip access-group 100 in  
  

### Interface
---

#### Status administrativo e operacional das interfaces [Flexível]
- Todas as interfaces sem uso do switch devem ser desabilitadas administrativamente aplicando o modelo de configuração padrão para interfaces de switch disponível:

***Cisco***

> Switch# conf t
> Switch# default interface Gi0/01
> Switch (config-if)# interface Gi0/0/1
> Switch (config-if)# description LIVRE
> Switch# end

***Dell***

> Dell# conf t
> Dell(conf)# default interface tengigabitethernet 1/5
> Dell(conf-if-te-1/5)# description LIVRE 
> Dell(conf-if-te-1/5)# end

***Juniper***

> Juniper> configure
> Juniper# edit interface ge-0/0/3
> delete
> set description LIVRE
> commit

#### Descrição

- O Zabbix colta estatísticas de todas as interfaces habilitadas e UP.
    
  Exemplo:
> Equipamento-de-teste-01
- A descrição deve iniciar com a string “EQIX_” ou "EQX_" para abertura de incidentes no Service Now.
    
  Exemplo: 
> EQIX_hostname 


#### SWITCH-PORT MODE

- A configuração do modo da porta deve vir expressa na configuração (ACESSO, TRUNK ou caso o switch seja Dell HYBRID).
- Quando a interface estiver conectada à infraestrutura Equinix seja para acesso internet, acessos à infraestrutura de BACKUP ou uma CROSS CONEXÃO deve-se especificar na configuração as VLANS que serão estendidas através desta. 

***Particularidades Aruba***  
- A configuração default é modo switching e é expresso na configuração da interface com o parâmetro "**no routing**".  
- A configuração de modo acesso ou trunk é expressa na configuração da VLAN. Exemplos:   
  
> switch(config)# interface 1/1/2  
> switch(config-lag-if)# no routing  
> switch(config-if)# vlan access allowed 10  
  
> switch(config)# interface 1/1/3  
> switch(config-lag-if)# no routing  
> switch(config-if)# vlan trunk allowed  20  
    
***Particularidades Dell***
- Suporta configuração de porta hibrida ( com frames tagueados e não tagueados simultaneamente). Esta configuração deve ser aplicada apenas quando for requerido pelo projeto e o equipamento remoto tiver suporte a mesma configuração.  

***Particularidades cisco***  
- Desabilitar o Protocolo DTP ( DYNAMIC TRUNK PROTOCOL).
- Há 5 Port-modes: dynamic auto, dynamic desirable, access, trunk and nonegotiate mode.

***Particularidades Juniper***
- Por padrão as portas são modo acesso.
- Há 2 port mode: Access ou Trunk

#### BPDU GUARD / BPDU PROTECTION [Flexível]

- É recomendado habilitar em portas com configuração de spanning-tree edge-port ou spanning-tree fast-port.  
- É recomendado que quando habilitado, um threshold de 5 minutos seja configurado para reabilitar a porta automaticamente depois de bloqueada.   
- É recomendado habilitar logs de bloqueio de portas ocasionados por recebimento de BPDUs em portas indevidas.  

***Particularidades Aruba***
- O recurso chama-se bpdu-protection.  
***Parcicularidades Cisco***  
- O recurso chama-se bpdu-guard.  
***Particularidades Juniper***
- O recurso chama-se bpdu-block.  
  

#### SPANNING-TREE ROOT GUARD / ROOT PROTECTION [Flexível]
- Quando habilitado na interface, não permite que a interface torne-se uma ***ROOT PORT*** na topologia spanning-tree e, a porta sempre será designada. Caso algum BPDU com parâmetros melhores seja recebido nessa porta, o ***ROOT GUARD*** não irá considerá-lo na topologia e coloca a porta com status inconsistente com root.  

- É recomendado habilitar SPANNING_TREE ROOT GUARD nas interfaces onde não é desejável que a interface torne-se uma ROOT-PORT. Exemplo: servidores, roteadores, uplink com operadoras de telecom.   

#### SPANNING-TREE PORTFAST / SPANNING-TREE EDGE-PORT
- É recomendado aplicar em interfaces de servidores e hosts ESXi. 

***Particularidade Cisco***  
- No switch Cisco este recurso é chamado de ***PORTFAST*** quando configurado com PVST e ***EDGE-PORT*** quando configurado com RSTP.

***Particularidades Juniper***  

- No switch Juniper este recurso é chamado de EDGE PORT.

#### SPANNING-TREE BPDU FILTER [Flexível]
- Habilitar quando necessário filtrar BPDU's indesejados em topologia spanning-tree sem loop. 

#### STATISTICAS [Flexível]
- Zerar os contadores de estatísticas das interfaces.
- Quando houver interface ótica, evidenciar os níveis de recebimento e transmissão de sinal na interface no RUNBOOK.

#### STORM-CONTROL [Flexível]
- É recomendado habilitar o recurso STORM-CONTROL BROADCAST nas interfaces, principalmente as interfaces de UPLINK para infraestrutura Equinix (acesso Internet, Galapagos, Backup, Fabric.

#### FLOW-CONTROL
***Particularidades Aruba***  
- Desabilitado por padrão.  
- Para interfaces **auto-negotiate**, o flow-control em nível de link está sujeito à negociação, além da velocidade e outros
parâmetros. Ambas as extremidades do link devem negociar o mesmo modo flow-control para que ele seja aplicado.  
- Para interfaces **não auto-negotiate**, o modo de controle de fluxo em nível de link configurado é sempre aplicado e o usuário é responsável por garantir que ambas as extremidades do link sejam configuradas para o mesmo modo.  
- Todos os membros de um LAG devem ter a mesma configuração de flow-control.  
- O flow-control sem perdas é suportado apenas para tráfego unicast de destino único. O tráfego replicado (por exemplo, transmissão, multicast, espelhamento) não pode ser garantido como sem perdas.  
- O comportamento sem perdas não é suportado ao operar em uma configuração de pilha VSF.  
- O flow-control sem perdas só funcionará corretamente quando as interfaces de entrada e saída tiverem o controle de fluxo habilitado. 
  

***Particularidades DELL***
- É recomendado configurar o parâmetro flow-control em todas as interfaces "ligado" no sentido TX e desligado no sentido RX.  
  Exemplo:   
    \#flowcontrol rx off tx on.  
  
#### DHCP
***Particularidades Juniper***
- É recomentado desabilitar o serviço e processo de dhcp.  
  
### INTERFACE IP 
---

#### PROXY ARP
- Desabilitar funcionalidade de proxy-arp nas interfaces IP. 

***Particularidades Cisco***
- Recurso é habilitado por padrão.

***Particularidades Dell***
- Recurso é habilitado por padrão. 

***Particularidades Juniper***
- Recurso não é habilitado padrão. 

#### IP DIRECTED BROADCAST
- Desabilitar em todas as interfaces VLAN IP ou IP exceto quando o projeto especifique que alguma aplicação que utilize.

***Particularidades Cisco***
- Recurso é habilitado por padrão.

***Particularidades Dell***
- Recurso é desabilitado por padrão.

***Particularidades Juniper***
- Recurso é desabilitado por padrão.

#### IP MASK-REPLY
- Desabilitar em todas as interfaces IP. 

***Particularidades Cisco***
- Recurso é habilitado por padrão.

***Particularidades Dell***
- Recurso não existe em forma de comando.

***Particularidades Juniper***
- Recurso não existe em forma de comando. 

#### IP REDIRECTS
- Desabilitar redirecionamento de pacotes em todas as interfaces IP.

***Particularidades Cisco***
- Recurso é habilitado por padrão.
- Recurso deve ser desativado na interface lógica (não global).

***Particularidades Dell***
- Recurso é desabilitado por padrão.

***Particularidades Juniper***
- Recurso é habilitado por padrão.
- Recurso deve ser desabilitado na interface lógica.

#### IP SOURCE ROUTING
- Desabilitar o encaminhamento de pacotes com informações de “SOURCE ROUTE”.

***Particularidades Cisco***
- Recurso é habilitado por padrão.

***Particularidades Dell***
- Recurso é habilitado por padrão.

***Particularidades Juniper***
- Recurso é desabilitado por padrão.

### SPANNING-TREE
---
- É obrigatório Habilitar Spanning-tree globalmente.
- Especificar configuração de custo das interfaces SPANNING-TREE quando houver loop no design da topologia garantindo o correto bloqueio e comutação das interfaces.
- É recomendado a definição explicita na configuração de root bridge na configuração do switch eleito para esta função. [Flexível]
- Não alterar custo SPANING-TREE de VLANS estendidas da infraestrutura Equinix para o switch TOR do cliente.
- Não permitir participação  no domínio SPANNING-TREE de equipamentos não administrados e gerenciados pela Equinix. [Flexível]
- Não permitir que equipamentos adicionados ao domínio SPANNING-TREE seja eleito root-bridge sem especificação explicita em configuração.
- Tabela de compatibilidade entre plataformas Cisco, Dell e Juniper:

  | **Fabricantes** | ***Aruba*** | ***Cisco***         | ***Dell*** | ***Hpe*** | ***Juniper*** |
  |:----------------|:-----------:|:-------------------:|:----------:|:---------:|:-------------:|
  | **Protocolos**  |             |                     |            |           |               |
  | STP             | x           | x                   | x          | x         | x             |
  | PVST            |             |                     |            |           |               |
  | PVST+           |             | x                   |            | x         | x             |
  | RPVST+          | x           | x                   | x          | x         | x             |
  | RSTP            | x           | Compatível com PVST | x          | x         | x             |
  | MSTP            | x           | x                   | x          | x         | x             |

***Particularidades Aruba***  
- HPE 5140: MSTP é compatível com RSTP e, RSTP é compatível som STP. A compatibilidae do PVST depende do tipo de link da porta. Se modo acesso, o PVST é compatível com outros modos de spanning-tree.  Se for uma porta trunk ou hybrid, o PVST é compatível com os outros modos de spanning-tree apenas na vlan default.  
   
- Suporta os protocolos:  
    - STP  
    - RSTP  
    - PVST  
    - MSTP  
  
- Aruba OS switches operates default in MSTP mode [802.1s]. Antes de conectar o switch na rede é recomendado que a configuração de spanning-tree seja alterada para rstp.  

  > spanning-tree force-version rstp-operation  

- Suporta os protocolos:  
    - STP   
    - RSTP  
    - RPVST+  
    - MSTP  
  
***Particularidades Cisco***
- Suporta os protocolos:
    - STP – SPANNING-TREE PROTOCOL
    - PVST+ - PER VLAN SPANNING-TREE PROTOCOL
    - RSTP – RAPID SPANNING-TREE PROTOCOL
    - RPVST+ - RAPID PER VLAN SPANNING-TREE PROTOCOL
    - MSTP - MULTIPLE SPANING-TREE PROTOCOL

***Particularidades Dell***
- Suporta os protocolos:
    - STP – SPANNING-TREE PROTOCOL
    - RSTP – RAPID SPANNING-TREE PROTOCOL
    - MSTP - MULTIPLE SPANING-TREE PROTOCOL
    - PVST+ - PER VLAN SPANNING-TREE PROTOCOL
    - RPVST - RAPID PER VLAN SPANNING-TREE PROTOCOL

***Particularidades Juniper***
- O protocolo RSTP é habilitado em todas as interfaces do switch por padrão.
- Limitações VSTP no switch Juniper:
    - Pode-se configurar no máximo 509 VLANS. Muitas instancias VSTP e RSTP podem causar mudanças constantes na topologia e gerar instabilidade. Por esse motivo, é recomendado utilizar até 190 VLANS.
    - Usar a mesma VLAN para RSTP e VSTP não é suportado. Exemplo: Configurar uma VLAN sobre o VSTP e configurar RSTP na interface que contém a VLAN.
    - Configurar VSTP e RSTP simultaneamente com mais de 253 VLANS, o PVSTP irá funcionar apenas para as primeiras 253 VLANS. As demais serão tratadas com RSTP.

#### VLAN

***Particularidades Cisco*** 
- Todas as VLAN ativas na configuração devem ser adicionadas ao DATABASE do switch. 
- VLAN default ID 1 mesmo ID da VLAN nativa por padrão. 
- Não utilizar identificador de VLAN 1001 a 1005 reservados em sistema. 

***Particularidades Dell*** 
- VLAN default ID 1 por padrão. 
- Se o pacote contiver cabeçalhos de camada 2, a diferença entre o link MTU e IP MTU deve ter bytes suficientes para incluir o cabeçalho de camada 2. 

***Particularidades Juniper*** 
- Configurar a VLAN na sessão VLAN incluindo os parâmetros NAME e ID e posteriormente associar a VLAN à interface.  
- Criar o nome de VLAN seguindo um mesmo padrão no equipamento.[Flexível]

  Exemplo:
>  set vlans **vlan-name**> vlan-id **id**
>  set vlans **VLAN010** vlan-id **10**

- A VLAN Default não tem VLAN ID ou TAG para identificar em qual VLAN o pacote está sendo recebido. Todos os pacotes recebidos na VLAN Default não são tagueados.

#### NATIVE VLAN

***Particularidades Cisco***
Por padrão, a VLAN nativa é a VLAN ID 1. Esta VLAN sempre está ativa em todas as portas em modo switch.
- Quando o switch TOR for cisco:
- A VLAN de produção do cliente deve ser configurada como VLAN NATIVA no TRUNK do switch de acesso da infraestrutura. 
- Esta VLAN nativa deve-se estender por todos os switches TOR do cliente (mesmo não sendo cisco).

***Particularidades Dell***
Por padrão, a VLAN nativa é a VLAN ID 1. Esta VLAN sempre está ativa em todas as portas em modo switch.
- Quando o switch TOR for Dell:
- A VLAN de produção do cliente deve ser configurada como VLAN NATIVA no TRUNK do switch de acesso da infraestrutura. 
- Esta VLAN nativa deve-se estender por todos os switches TOR do cliente (mesmo não sendo DELL).

***Particularidades Juniper***
- Não tem VLAN NATIVA padrão sendo necessário criar manualmente.

#### JUMBO FRAME [Flexível]

- Habilitar Jumbo frame no switch se a aplicação ou sistema do cliente requerer esta configuração.
- Oracle Rack.
- VMware.
- Sistema Operacional Microsoft 2008 ou superior.

#### LINK AGREGATION

- As interfaces que compõem o LINK AGGREGATION devem operar no modo full duplex, velocidade e método de negociação automático.
- Todas as interfaces que compõem o LINK AGGREGATION em um mesmo equipamento devem ser do mesmo tipo.
- Todas as interfaces devem ter a mesma configuração de MTU.
- Recomendado utilizar o protocolo LACP no LINK AGGREGATION modo ACTIVE.[Flexível]
- As interfaces em paralelo que irão compor o LINK AGGREGATION conectam um mesmo sistema de origem a um mesmo sistema de destino. Este sistema por ser um ou mais switches operando com a configuração de VSS (cisco), virtual-chassis (Juniper) ou STACKING (Dell).
- Sempre que disponível, conectar as interfaces simetricamente de forma que a porta A do switch de origem seja conectada a porta A no switch de destino. [Flexível]
- As interfaces que compõem o LINK AGGREGATION e a interface virtual que representa o LINK AGGREGATION devem conter descrição.

***Particularidades Dell***
- Suporta configuração de porta híbrida (com frames tagueados e não tagueados simultâneamente). Esta configuração deve ser aplicada apenas quando for requerido pelo projeto e o equipamento remoto tiver suporte a mesma configuração. 

#### VSTACK 

***Particularidades Cisco***
- Desabilitar a feature VSTACK de switches standalone.
- Para switches 3850 e STACK habilitado, utilizar software na versão 16 ou superior.
- O Cisco Catalyst 3850 Series Switches com licença LAN Base só pode ser configurado como STACK com outro Cisco Catalyst 3850 Series LAN Base switches. A regra é a mesma para licenças do tipo IP Base e IP Service. 
- Misturar no STACK switches licenciados com LAN Base e IP Base ou IP Services não é suportado. 

#### SCHEDULER 
- Aplicar em equipamentos Cisco.

***Particularidades Cisco***
- Alterar para 500ms o tempo máximo que o sistema pode aguardar para executar processos de baixa prioridade. 

Exemplo:

> scheduler interval 500

#### TCP-KEEPALIVES 
- Aplicar em equipamentos Cisco.

***Particularidades Cisco***
- Habilitar o recurso que monitora e encerra as sessões sem resposta. 

Exemplo:

> Switch(config)#service tcp-keepalives-in
> Switch(config)#service tcp-keepalives-out

### Alta disponibilidade

#### STACKING - HPE ARUBA
- 2 Equipamentos a 9 empilhados. 
- Todos os membros do stack devem ter o mesmo software.
- Definir o número do switch no stack, o padrão para todos os switches é 1.
- Configurar a prioridade do switch no stack. 

  >  stacking enable  
  >  stacking set-stack  
  >  stacking member 1 priority 255  
  >  stacking member N type JxxxxA [mac MAC-Addr]  // _N é o número do membro do stack e JxxxxA é o número o nome do produto._  
   

#### VSX - ARUBA
- VSX ou Virtual Switching Extension é uma tecnologia de virtualização para criar um cluster de dois Aruba CX switches de mesmo modelo  6400, 8320, 8325, 8360 or 8400.    
  
- **Switches VSX e versões de software:** ambos os switches VSX devem usar a mesma versão de software na maioria das situações; no entanto, durante uma atualização, um switch pode executar uma versão diferente do peer com algumas limitações, como nenhum suporte de sincronização VSX.     
- **Todos os VSX switches no ambiente devem ter configurações idendicas para os seguintes itens:**   
  - A associação de VLAN para todas as portas de tronco VSX.  
  - The loop protection configuration on a VLAN that is part of a VSX LAG.  
- **Portas disponíveis:** Certifique-se de que a interface VSX LAG nos switches primário e secundário do VSX tenha uma porta membro configurada e habilitada. Disponibilidade de uma porta não VSX disponível para o ISL.  
- **Recursos mutuamente exclusivos:** o VSX active-forwarding e VSX active-gateway na mesma interface VLAN o VSX active-gateway e VRRP no contexto SVI o VSX e MVRP.  
- **Perfis para switches da série 832x:** todos os switches devem ser atribuídos no perfil L3-agg ou L3-core.  
- **Suporte para links entre switches (ISLs):** o VSX LAG não oferece suporte ao processamento da camada 3, como uma porta roteada; no entanto, várias interfaces de switch virtual (VSI) podem ser configuradas no switch em associação com as VLANs transportadas pelo VSX LAG fornecido.  
- **Suporte para Camada 3:** VSX LAG como uma porta somente de rota não é suportado. Para habilitar a Camada 3, crie um SVI associado a uma determinada VLAN que esteja habilitada no VSX LAG.  
- **Suporte a VLAN:** A mesma lista de VLANs que são trunked sobre os VSX LAGs deve ser configurada nos switches VSX primário e secundário na configuração global. A lista de VLANs pode ser sincronizada com o switch secundário se o comando vsx-sync for usado no contexto de VLAN. Verifique também se o conjunto de VLANs também é permitido no ISL nos switches VSX primário e secundário. Se uma VLAN nativa for definida, o switch executa automaticamente o comando vlan trunk allowed all para garantir que a VLAN padrão seja permitida no trunk.  
- **VSX active-forwarding, VSX active-gateway e VSX LAG são suportados com BFD.**  
-Um VSX LAG suporta no máximo quatro links por segmento de switch. Um VSX LAG em um switch downstream pode ter no máximo um oito links. Execute o comando **show capabilities** para o número máximo de VSX LAG suportados para seu tipo de switch. O CORE pode ser dispositivos de terceiros, desde que suportem LACP para conectividade downstream ao VSX LAG. A sincronização do VSX sincroniza do switch primário (agregado 1) para o switch secundário (agregado 2).  
- É recomendado a configuração de Keepalive entre os pares VSX. 
- Ao criar o keepalive, certifique-se de que o caminho não ultrapasse o ISL ou um VSX LAG. O Keepalive pode ser configurado de duas maneiras para o CORE 1 e o CORE 2:  
  - Habilitar keepalive entre o CORE 1 e o CORE 2 como um link direto. 
  - Criar um keepalive para uma interface loopback por meio do upstream onde não houver um VSX LAG.  
- É  recomendado usar o MAC ADDRESS unicast ao atribuir o MAC ADDRESS virtual system-mac ou active-gateway. Há quatro intervalos reservados para uso privado de unicast. Os valores são:  
  - x2-xx-xx-xx-xx-xx  
  - x6-xx-xx-xx-xx-xx  
  - xA-xx-xx-xx-xx-xx  
  - xE-xx-xx-xx-xx-xx  
onde x pode ser qualquer valor hexadecimal.  
- Spanning-tree guards e filters não são permitidos na configuração de ISL. 
- Switches VSX oferecem suporte aos protocolos spanning-tree: MSTP e RPVST.  
- Não use o system MAC do peer como um VMAC do active-gateway. Se o mesmo endereço MAC for utilizado, a sincronização do VSX
tentará sincronizar a configuração no switch secundário e causará interrupções no tráfego.  
- Definir o system MAC VSX a ser usado pelos protocolos do plano de controle, como STP e LACP. Um par de switches VSX deve ter o mesmo system MAC VSX.  
- VSX has no default role defined for the device. The device role assigns the device as the primary or
secondary for VSX synchronization. For ISL to be in-sync, one device in VSX must be configured as the
primary and the other device must be configured as the secondary.
- Configurar VSX role do switch (primary /secondary).  
- Habilitar split-recovery. 





#### VLT - DELL
  
- O VLT consiste em dois nós que fornecem uma visualização lógica de switch único para os dispositivos conectados. Entretanto, cada um dos peers do VLT possui seus próprios planos de controle e de dados e pode ser configurado individualmente para porta, protocolo e ambiente de gerenciamento. 
  
- Ambos os peers do VLT requerem a mesma versão do SmartFabric OS10 no ambiente de produção; entretanto, uma incompatibilidade de versões é aceitável durante o procedimento de atualização.  

- É recomendado configurar via rede de gerenciamento a conexão backup com o nó secundário e o HEARTBEAT.  

- É obrigatório ativar o protocolo spanning tree para evitar loops não intencionais na rede. Utilizar a mesma versão de spanning-tree em todos os equipamentos conectados ao VLT.  

- Configure **vlt-mac** idêntico em ambos os peers do VLT para evitar oscilações do canal de porta do VLT durante cenários de falha, como oscilações do VLTi e reinicialização do nó. Caso haja no mesmo ambiente mais de um par VLT, criar **vlt-mac distintos para cada par de switch.  

- Sempre implante mais de um link para o port-channel do VLTi.  

- Implementar LACP dinâmico para canais de portas do VLT.  

- A VLAN 4094 é reservada para sinalização entre os pares VLT. Não é permitido utilizar este ID no ambiente do cliente.  

- Criar o Domínio VLT e a interface de interconexão (VLTi) em ambos os switches.  

- É obrigatório ativar o protocolo spanning-tree RSTP e configurar a prioridade de bridge do RSTP nos peer switches VLT primário com valor **4096** e no secundário **8192**.  

- Configure a Prioridade do switch no domínio VLT. User o valor **10** para o switch primário e **20** para o switch secundário.  

- Configure VLT MAC Address, exemplo 00:11:22:33:44:55.

- Configure VLT Backup Link.

- Quando houverem mais de um par de switches TOR no ambiente, implantar os demais switches seguindo a arquitetura [spinning-leaf](https://downloads.dell.com/solutions/networking-solution-resources/Dell%20EMC%20Leaf-Spine%20Deployment%20Guide%20v1.0.pdf).

#### STACKING - DELL
- 2 Equipamentos ou mais empilhados. Somente a CPU do equipamento ativo processa L2 e L3. 
- Todos os membros do stack devem ter o mesmo software.
- Definir o número do switch no stack, o padrão para todos os switches é 1.

> stack-unit stack—unit—number renumber stack—unit—number

- Configurar a prioridade do switch no stack

> stack-unit stack—unit—number priority priority

- Utilizar cabo compatível conforme tabela abaixo:

  | **Modelo**| **Tech Management** | **Cable** | **PN Cable** | **Port** | **Observação** |
  |:-:|:-:|:-:|:-:|:-:|:-|
  | N1524 / N1548 | VLT | DAC 10Gbps | DAC-SFP-10G | Uplink | Sem suporte VLT |
  |                 | STACKING | | | | Usa cabo DAC para stack. |
  | S4048  | VLT | DAC 10 Gbps | DAC-SFP-10G | Uplink | Com Suporte VLT. Usa cabo DAC. |
  | | STACKING | | | | Usa cabo DAC para STACKING |
  | S3124 | VLT | DAC 10 Gbps | DAC-SFP-10G   | Uplink   | Com suporte VLT. Usa cabo DAC  |
  | | STACKING | SAS Cable | 470-AAPW/AAPX | Dedicada | Usa cabo SAS para stacking |
  | S4148F  | VLT | DAC 40 Gbps | DAC-QSFP-40G | Uplink | Com Suporte VLT. Usa cabo DAC. |
  |  | STACKING |  |  |  | Sem Suporte Stacking |
  
-  Switch DELL modelo S4148F não suporta configuração de STACKING. 

#### Virtual Chassis - JUNIPER
- Todos os equipamentos que compõem o virtual-chassis têm que ter o mesmo modelo e software.
- Os switches Master e Backup do virtual-chassis devem ser mencionados na configuração com número de série. 
- Utilizar o método com configuração pré-provisionado para criar um virtual-chassis ou incluir novos membros em um virtual-chassis já existente.
- Especificar o mesmo parâmetro de prioridade para o switch com função de master e backup. 
- Configurar a maior prioridade possível (255) para o switch com função de Master e Backup.

> [edit virtual-chassis]
> set preprovisioned

- Todo equipamento que compõe o virtual-chassis tem uma função específica a qual deve ser explicita na configuração. (Master, Backup ou line-card).

> [edit virtual-chassis]  
> set member 0 serial-number 123456 role routing-engine  
> set member 1 serial-number 567890 role line-card  
> set member 4 serial-number 151617 role routing-engine  

- Desabilitar o recurso SPLIT DETECTION quando o virtual-chassis for composto de apenas 2 switches.

> set virtual-chassis no-split-detection

- Quando  virtual-chassis estiver habilitado, a gerência do equipamento deve ser realizada pela interface VME.
- Habilitar a sincronização entre os equipamentos ativo e backup do virtual-chassis automaticamente. 

> set system commit synchronize

- É recomendado habilitar GRACEFUL SWITCHOVER. [Flexível]

> set chassis redundancy graceful-switchover 

  
### KBs relacionados:

- [KB0010783](<https://equinixcsm.service-now.com/nav_to.do?uri=kb_knowledge.do?sys_id=6f372f624f64ab84ba863285f110c7fb>) - Recomendações  de Firmware para Switches (JTAC) – switch Juniper, todos os modelos.
- [KB0010282](<https://equinixcsm.service-now.com/nav_to.do?uri=kb_knowledge.do?sys_id=849997fd377613c4f3818ff1b3990e8b>) – Comandos Essenciais – switch Juniper, todos os modelos.
- [KB0011122](<https://equinixcsm.service-now.com/nav_to.do?uri=kb_knowledge.do?sys_id=fea5428b4fe26f8487418d9f9310c7c4>) - Configurando Virtual chassis – switch Juniper, todos os modelos.
- [KB0011123](<https://equinixcsm.service-now.com/nav_to.do?uri=kb_knowledge.do?sys_id=36310e174f2aa384f3d33d828110c77c>) – Configuração Juniper -  MTU Jumbo Frames – todos os switches.
- [KB0011588](<https://equinixcsm.service-now.com/nav_to.do?uri=kb_knowledge.do?sys_id=5593ef971b744c945deb4199bd4bcbe5>) – PROTOCOLO DCBx COM PLACA DE REDE INTEL XL710 (SUSE Linux 15.1) CONECTADA A SWITCH JUNIPER  COM INTERFACE 10G – switch modelo EX4500
- [KB0011440](<https://equinixcsm.service-now.com/nav_to.do?uri=kb_knowledge.do?sys_id=26c184d34fa3fb8087418d9f9310c79f>) - Eventos DDOS_PROTOCOL_VIOLATION no JUNOS – switch Juniper QFX5100
- [KB0011379](<https://equinixcsm.service-now.com/nav_to.do?uri=kb_knowledge.do?sys_id=3949936113be730c26d55d122244b02b>) - LACP entre switch e XenServer  - Configuração,  troubleshooting e Diagnósticos – aplicável a todos os fabricantes.
- [KB0011273](<https://equinixcsm.service-now.com/nav_to.do?uri=kb_knowledge.do?sys_id=4d79ad1213e477cc26d55d122244b0bb>) - Ao executar o comando 'commit', ocorre a mensagem warning: Could not connect to fpc-1 : Can't assign - switch Juniper modelos EX2200, EX2300 e EX3300.
- [KB0011162](<https://equinixcsm.service-now.com/nav_to.do?uri=kb_knowledge.do?sys_id=75d22aaf4f7e6f00f3d33d828110c73a>) - Alta utilização de CPU - switch Juniper modelos EX2200, EX2300 e EX3300
- [KB0011177](<https://equinixcsm.service-now.com/nav_to.do?uri=kb_knowledge.do?sys_id=d480efc94fc3a34087418d9f9310c737>) - Comutação seguida de reboot inesperado de routing-engine, switch Juniper modelo EX 3300 
- [KB0011045](<https://equinixcsm.service-now.com/nav_to.do?uri=kb_knowledge.do?sys_id=b33520374ff5e300f3d33d828110c774>) - Reboot inesperado do switch cisco modelo WS-C2960X-24TS-LL.
- [KB0013079](<https://equinixcsm.service-now.com/nav_to.do?uri=kb_knowledge.do?sys_id=67dfd11edb544194a7f8a334ca96193a>) - Padrão de Senhas para acesso aos switches TOR.
- [KB0012247](<https://equinixcsm.service-now.com/nav_to.do?uri=kb_knowledge.do?sys_id=389f595cdb0aa4103e6ee2d2ca9619b3>) - Zabbix BR – Monitoramento de interfaces de switches.
- [KB0012128](<https://equinixcsm.service-now.com/nav_to.do?uri=kb_knowledge.do?sys_id=52eb34a6db682890545dee0c1396198d>) - (DUP!)  na resposta de ping entre ambiente abaixo do TOR do cliente e Galápagos.
- [KB0012170](<https://equinixcsm.service-now.com/nav_to.do?uri=kb_knowledge.do?sys_id=097240c6dbb0a8943e6ee2d2ca96195d>) - Zabbix - PER STATUS OF INTERF IF ALIAS [] IS DOWN.
- [KB0012227](<https://equinixcsm.service-now.com/nav_to.do?uri=kb_knowledge.do?sys_id=7b68d7a2db7924103e6ee2d2ca9619a6>)   - JUNIPER - Analisar interfaces física com status down. Validação de interface física up.
- [KB0012248](<https://equinixcsm.service-now.com/nav_to.do?uri=kb_knowledge.do?sys_id=f453e550db8aa4103e6ee2d2ca961992>) - JUNIPER - jdhcpd: DH_SVC_SENDMSG_FAILURE log error.
- [KB0011929](<https://equinixcsm.service-now.com/nav_to.do?uri=kb_knowledge.do?sys_id=2d36c8bcdb875894b9711a8c139619f8>) - Restrição de  switch para conexão ao produto "Infrastructure Port".
- [KB0014772](<https://equinixcsm.service-now.com/kb_view.do?sysparm_article=KB0014772>) - MSH NOC - Lista de Zabbix Proxy.
- [KB0014008](https://equinixcsm.service-now.com/kb_view.do?sysparm_article=KB0014008) - Backup/Restore - Projeto Zabbix como Backup.

### Revisão


| Revisor Responsável          | Validador Responsável     | Atualizado |
|------------------------------|---------------------------|------------|
|Luana Coimbra                 | None                      | 16/08/2024 |
|Luana Coimbra                 | Luiz Ferezin              | 16/11/2023 |
|Luiz Ferezin                  | Luana Coimbra             | 04/01/2023 |
|Luana Coimbra                 | Luiz Ferezin              | 10/12/2021 |
|Luana Coimbra                 | Renato Pesca              | 08/06/2020 |

  
![visitors](https://visitor-badge.glitch.me/badge?page_id=Premissa.Switch.md&left_color=gray&right_color=red)

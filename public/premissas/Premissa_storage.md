![alt text](img/logo_eqx.jpg "Logo EQX")

# Premissas de Storage
---

# Sumário
-   [Objetivo](#objetivo)
-   [Escopo](#escopo)
-   [Documentação](#documentação)
-   [ServiceNow](#servicenow)
-   [Recomendações de Instalação](#recomendações-de-instalação)
-   [Operações Suportadas](#operações-suportadas)  
-   [Pré-requisitos Instalação](#pré-requisitos-de-instalação)
-   [Hardware](#hardware)
-   [Software de Gerenciamento](#software-de-gerenciamento)
-   [Sistema Operacional do Storage](#sistema-operacional-do-storage)
-   [Recomendações de Segurança e Hardening](#recomendações-de-segurança-e-hardening)
-   [Runbook](#runbook)
-   [Monitoração](#monitoração)
-   [Suporte Remoto e Regras de Firewall](#suporte-remoto-e-regras-de-firewall)
-   [Perfis de Snapshot](#perfis-de-snapshot)
-   [Tier e Storage Profile](#tier-e-storage-profile)
-   [Licenciamento](#licenciamento)
-   [Revisão](#revisão)

---

### Objetivo
---
- Apresentar as premissas de implantação de Sistemas de Armazenamento em ambientes de clientes gerenciados, seguindo as melhores práticas de mercado e atendendo os pré-requisitos da infraestrutura provida ao rack do cliente.
- Apresentar recomendações e particularidades de implementação entre os principais fabricantes suportados.

### Escopo
---
- Serão apontados neste documento as premissas do storage, seu software de gerenciamento e os padrões de conexão até os switches. Demais configurações estarão cobertas nas premissas dos sistemas operacionais clientes, como por exemplo de Linux, Windows, Hyper-V e VMware.   

### Documentação
---
- A ferramenta padrão para documentação dos ativos dos clientes é o ServiceNow, portanto todas as informações sobre o cliente devem estar contidas nesse sistema.

### Service Now
---
- **Runbook**:
O objetivo desse documento é ser um "snapshot" de configurações do equipamento do cliente. Eventualmente utilizado em caso de RD do equipamento.
- **ETI**:
O objetivo desse documento é conter as informações sobre o que foi acordado com o cliente referente à capacity e serviços disponibilizados no equipamento.
- **Dados do equipamento**:
Em dados do equipamento devem conter informações como: informações de acesso lógico, localização física, configuração física, etc.
- **Credenciais**:
Estão dispostas em um módulo de segurança dentro do ServiceNow. Através desse módulo devemos ter acesso à credencial atual do equipamento. Apenas os equipamentos com gerenciamento TM ou OSA devem possuir esse item configurado.

### Recomendações de Instalação
---
- Verifique se na aquisição já está contemplada a instalação pelo fabricante. É bastante comum estar incluida na venda os serviços técnicos para configuração inicial. Estes serviços costumam incluir montagem, configuração e atualizações de firmware e software de gerenciamento.
- Validar se os Equipamentos e soluções adjacentes do ambiente onde está sendo instalado, são suportados pela lista de compatibilidade do fabricante do storage.

### Operações Suportadas
---
| Tecnologia | Velocidade |
| ------ | ------ |
| Fibre Channel | 8, 16+ Gbit/s |
| iSCSI | 1, 10+ GbE |


### Pré-requisitos Instalação
---
- Definir o servidor onde será instalada a ferramenta de administração do storage e registrar esta informação no IC do storage no Snow. 
- Liberar regras de firewall para comunicação do servidor de gerenciamento para o storage e do servidor de gerenciamento para o remote call do fabricante na internet.

#### Hardware
---
- **Propriedades do Hardware**:
Certificar na matriz de compatibilidade do fabricante se o ambiente onde está sendo instalado o storage é compatível. Em especial os Sistemas Operacionais, as HBAs, as interfaces Ethernet, os switches FC e Ethernet [Flexível]. 

- **Garantia**:
    - Equipamento deve possuir suporte e garantia contratados por pelo menos o tempo que durar o contrato do cliente. 
    - Considerar o roadmap do fabricante para o modelo, evitando escassez de peças de reposição para modelos que podem ser descontinuados. [Flexível]

- **Controladoras**:
Mínimo de 2 Controladoras com duas portas cada. As conexões das controladoras devem ser em switches diferentes (fabric-A e fabric-B) de modo a tornar o a conectividade Storage x Switches redundantes. Assim haverá maior disponibilidade e, na maioria dos modelos, o balanceamento de carga. Válido tanto para storages em switches FC bem como storages iSCSI em switches Ethernet. Vide exemplos abaixo:

---

**Exemplo: Storage FC 3par**

![alt text](img/img.storage/img.storage_storagefc.png "Storage FC")

---

**Exemplo: Storage iSCSI Dell**

![alt text](img/img.storage/img.storage_storageISCSI.png "Storage ISCI")

---

- **Fontes**:
    - Todas "gavetas" e controladoras devem possuir fontes redundantes energizadas, em circuitos diferentes e não podem ter alarmes ativos.
- **Discos**:
    - A quantidade de discos utilizados deverá obedecer a demanda do cliente e dos requisitos de performance / disponibilidade em concordância com as melhores práticas recomendadas pelo fabricante.
    - Validar a necessidade liquida do cliente com margem mínima de 20% (após as perdas com formatação dos volumes, discos de paridade, hotspare etc)
    - Sempre que houver discos de tipos, velocidades e tamanhos diferentes, utilizar no a "tierização" [Flexível]
    - Somente serão aceitos discos homologados e certificados pelo fabricante do storage.
    - Discos SATA/Near Line (NL) somente serão aceitos em casos específicos onde a performance não é um fator importante (ex: backup, fileserver ou qualquer outro cold data).
    - Utilizar no mínimo um disco hotspare por modelo de disco em cada gaveta, ou de acordo com as melhores práticas recomendadas pelo fabricante / modelo do storage.
    - Não utilizar em nenhuma hipótese arrays em RAID-0 por não possuirem proteção para falha em discos.
    - A quantidade de hotspares e formatação do array deverão seguir a necessidade do cliente.
- **Conexões**:
    - Rede de Gerenciamento:
        - Reserve portas 100 Mbits/s para gerenciamento nos switches TOR do cliente. Utilize sempre todas as portas disponíveis para gerenciamento com redundância. Alguns modelos, além das portas de gerenciamento, possuem conectividade para acesso direto a BMC. Configure essas também. Seu funcionamento é exigido por alguns fabricantes durante atualizações. (ex: Dell)
    - Rede Dados ISCSI:
        - Utilize preferencialmente portas 10 GbE.
        - Não ative Spanning Tree Protocol (STP) nas portas dos switches que conecta aos nodes (iSCSI initiators / interfaces de rede do array).
        - As LUNs devem ser apresentadas para os clientes (hosts) por todas as controladoras.
        - Configurar Jumbo Frame com MTU 9000 em todas as pontas de conexão (servidores, switches e storage). 
        - Separar em vlans e subnets (com dominios de broadcast diferentes). [Obrigatório]
        - Preferencialmente devem ser utilizados switches dedicados para iSCSI.
        - Para que não ocorra incompatibilidade de MTU, valide que o suporte a jumbo-frames está ativado com MTU 9000 de ponta a ponta em todos os dispositivos intermediários.
        - Valide se o tamanho da MTU está definido como 9000 na configuração do sistema operacional e switch virtual.
            - *Linux e Xenserver: ping -M do -s 8972 [destinationIP]*
            - *Windows e Hyper-V: ping -f -l 9000 [destinationIP]*
            - *Vmware: vmkping -I [vmkernel interface] -s 8972 -d [destinationIP]*

### Tier e Storage Profile
---
- Havendo mais de um modelo, os discos instalados devem ser classificados em no mínimo dois tiers (ex: SSD, 15 KRPM, 7 KRPM).
- Os Arrays do storage deverão ser criados e agrupados conforme necessidade de performance, espaço líquido e nível de proteção (RAID) solicitados pelo cliente na ETI. Abaixo a relação dos Storages Profiles em que os volumes devem ser associados:

| Nome | RAID |
| ------ | ------ |
| Balanced | RAID 10, RAID 5 |
| Max Efficiency | RAID 5 |
| Max Peformance | RAID 10 |

### Perfis de Snapshot
---
- Criar dois perfis de snapshot e associar todos os volumes existentes a eles.

| Nome | Modo | Execução | Frequencia | Expiração |
| ------ | ------ | ------ | ------ | ------ |
| BP_Daily | Standard | 18:00 h | Diario | 25 h |
| ODDP_Daily | Standard | 3 em 3 h |  Diário | 4 h |

### Software de Gerenciamento
---
- Instalar a versão mais recente do software de administração do storage no servidor escolhido.
    - [HPE 3PAR StoreServe ManagementConsole](https://h20392.www2.hpe.com/portal/swdepot/displayProductInfo.do?productNumber=SSMC_CONSOLE)
    - [Dell Storage Manager](https://www.dell.com/support/home/pt-br/drivers/driversdetails?driverid=mcmcj)
- Após a instalação inicial, atualizar todos os firmwares e atualizar o software de administração.

### Sistema Operacional do Storage
---
- Sincronia de horário: configure o storage para sincronizar com um servidor NTP confiável, preferencialmente um dos NTPs da Equinix.
    - extntp.equinix.com.br
    - 1.extntp.equinix.com.br
    - 2.extntp.equinix.com.br
    - 3.extntp.equinix.com.br
- Remote Call: configure o storage para abertura automática de chamados com o fabricante.
- Atualizar todos os firmwares do storage
- Liberar regras para acesso remoto eventual do fabricante - remote console

### Recomendações de Segurança e Hardening
---
- **Senhas Administrativas**:
    - Altere para uma senha forte a senha do administrador (adminroot) e armazene no servicenow.
    - Durante a instalação da Storage, devem ser criados os usuários eqxadmin e eqxgerencia com permissão de administrador. Estes serão usados pela equipe de operações para administração. Estes usuários devem ter suas senhas cadastradas no servicenow.

- **Políticas de Senhas**:
    - As políticas devem ser definidas de acordo com a política de segurança exigida pelo servicenow em acordo com o fabricante do storage. (baseado sempre no de maior complexidade)

- **Patches**:
    - Aplique todas as atualizações de firmware do storage vigente na ativação (controladoras, discos etc)
    - Utilize a versão mais recente do software de gerenciamento compatível com o storage e aplique todos os patches disponíveis.

- **Tráfego iSCSI**:
    - Quando o storage for na modalidade iSCSI e o SO suportar, ative a autenticação CHAP bidirecional para o tráfego iSCSI, para que as chaves de autenticação do CHAP sejam exclusivas.  Ao não autenticar o destino e o host iSCSI, existe o risco de um ataque man-in-the-middle. [Flexível]

- **Rede de Gerenciamento**:
    - Configure um IP valido na rede de gerenciamento atual do cliente, que já estejam protegidas protegidas por firewall.

- **Logs de Sistema**:
    - Certifique que o registro de logs dos erros críticos e warnings estejam habilitados.

- **Conexões**:
    - Efetue somente as liberações mínimas de firewall necessárias entre o servidor de gerenciamento e o storage. Normalmente as portas utilizadas são SSH, HTTP, HTTPS e SNMP. Valide com de acordo com o fabricante e o modelo se necessita de alguma liberação adicional. 

### Monitoração
---
- Cadastrar os storages nas ferramentas online de análise e monitoração centralizada dos fabricantes.
    - [HPE Infosight](https://infosight.hpe.com/home)
    - [CloudIQ](https://www.dell.com/Identity/global/LoginOrRegister/a2fbc49e-ac99-4289-830f-774a9a0ce9dd?feir=1)

- Utilize os Templates de integração disponíveis para Zabbix.
    - Referência: https://www.zabbix.com/integrations/
        - Storages Compellent:
        TEMPLATE.GT_DELL_COMPELLENT
        - Storages Equallogic:
        TEMPLATE.GT_STORAGE_DELL_EQUALLOGIC_SAN_ENCLOSURE    
        TEMPLATE.GT_STORAGE_DELL_EQUALOGIC_SAN_GROUP
        - Storages HP 3par:
        https://github.com/Toh3mi/zabbix-3par/tree/master/3par (em análise)

### Suporte Remoto e Regras de Firewall
---
- Configure as ferramentas de suporte remoto.
- Referências: Nos sites dos fabricantes [HPE](https://h20195.www2.hpe.com/v2/GetPDF.aspx/4AA5-1582ENW.pdf) e [Dell](https://www.dell.com/support/article/pt-br/sln308447/notifica%C3%A7%C3%A3o-a-clientes-de-armazenamento-sc-requisitos-de-rede-para-ativar-o-dell-supportassist-enterprise-e-o-secure-console-com-o-storage-center?lang=pt)
- Ajuste as regras de firewall, permitindo a saída das interfaces de gerenciamento para os seguintes endereços e portas:

**Compellent**

| | Nome de host |Endereço IP público |Porta TCP |
| ------ | ------ |------ |------ |
| Dell SupportAssist |web1.compellent.com |76.164.8.136|443|
|  |web2.compellent.com |76.164.8.160 e 161|443|
|  |stor.g2.ph.dell.com |143.166.135.120 (principal)|443|
|  |stor.g2.ph.dell.com	|143.166.147.96 (secundário)|443|
|  |stor.g3.ph.dell.com	|143.166.135.19 (principal)|443|
|  |stor.g3.ph.dell.com	|143.166.147.73 (secundário)|443|
|Console seguro|es-mc-ssh-ssh1.compellent.com |76.164.8.174|22|
|  |es-mc-ssh-ssh2.compellent.com|76.164.8.175|22|
|  |es-mc-ssh-ds1.compellent.com|76.164.8.173|8443|
|  |sshdisp.g3.ph.dell.com|76.164.8.173|8443|

**3par**

| Network requirement | Secure Network Mode |
| ------ | ------ |
| HPE 3PAR support portal IP address | Your DNS server should allow storage-support.glb.itcs.hpe.com to be resolved to 16.248.72.63—storage-support1.itcs.hpe.com and 16.250.72.82—storage-support2.itcs.hpe.com |
| Outbound connectivity| Port 443 (https) to be opened (outbound) between Service Processor IP and the following IP addresses: storage-support1.itcs.hpe.com—16.248.72.63 (primary), storage-support2.itcs.hpe.com—16.250.72.82 (secondary) |
| Inbound connectivity | Port 443 (https) to be opened (outbound) between Service Processor IP and the following IP addresses: c4t18808.itcs.hpe.com (15.249.128.199), c4t18809.itcs.hpe.com (15.249.128.195), c9t18806.itcs.hpe.com(15.249.0.114), c9t18807.itcs.hpe.com (15.249.0.118)|

| PORT | USE |
| ------ | ------ |
| 22 | SSH daemon (required) communication between SP and HPE 3PAR StoreServ array as well as optional use for end-user CLI (listener) |
| 123 | (UDP) NTP (required) peer communication for network time protocol |
| 161 | SNMP agent (optional) communications between 3rd party SNMP manager and 3PAR SNMP agent (listener) |
|162|SNMP trap origination (optional) source port for unsolicited SNMP traps to 3rd party SNMP manager (source)|
|427|SLP (optional) Service Location Protocol (CIM) required in CIM/SLP are to be used (listener)|
|5781|Event consumer interface (required) communication between Service Processor and HPE 3PAR StoreServ array as well as some RM/VM/VASA event logic is used (listener)|
|5782|CLI unsecured (optional) provides plain text access to the CLI if end user chooses to use it|
|5783|CLI secured with TLS (required) encrypted access to CLI, Service Processor to HPE 3PAR StoreServ nodes communication as well as end user CLI usage|
|5988|CIM (optional) unsecured web services access for CIM clients if customer wishes to use plain text access|
|8008|Web Services Application Program Interface (unsecure)|
|8080|Web Services Application Program Interface (encrypted)|
|8443|Secure port used by Management Console in the transmission of data to and from HPE 3PAR StoreServ array if check box checked at bottom of login screen. (Note with the release of StoreServ Management Console (SSMC) in late 2014, this is the default communication protocol)|

### Licenciamento
---
* O licenciamento do storage deve refletir a necessidade do cliente e obedecer o que foi mensurado e contratado.
* As informações de licenciamento devem ser registradas nas observações do servicenow. (ex. snapshots, compressão de dados, virtualização ou replicação)


#### Revisão

| Revisor Responsável          | Validador Responsável     | Atualizado |
|------------------------------|---------------------------|------------|
| Augusto Galvão               | Anderson Freitas          | 13/07/2020 |
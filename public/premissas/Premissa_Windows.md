![alt text](img/logo_eqx.jpg "Logo EQX")

# Premissas de S.O. Windows
---

# Sumário
- [Objetivo](#objetivo)
- [Documentação](#documentação)
- [ServiceNow](#servicenow)
- [Runbook](#runbook)
- [ETI](#eti)
- [Topologia](#topologia)
- [Cadastro Atlas](#atlas)
- [Recomendações de instalação](#recomendações-de-instalação)
- [Hardening](#hardening)
- [Sistemas de arquivos e particionamento dos discos](#sistemas-de-arquivos-e-particionamento-dos-discos)
- [Contas de usuários e permissionamento](#contas-de-usuários-e-permissionamento)
- [Fontes confiáveis](#fontes-confiáveis)
- [Remote Desktop](#remote-desktop)
- [Auditoria](#auditoria)
- [Redes](#redes)
- [Segurança](#segurança)
- [Configurações de Hardware](#configurações-de-hardware)
- [BIOS](#bios)
- [Processadores, memória e discos](#processadores-memória-e-discos)
- [Redes](#redes)
- [HBA](#hba)
- [Rede e Link](#rede-e-link)
- [Cenários possíveis](#cenários-possíveis)
- [Instalação e Configuração de Sistema Operacional](#instalações-e-configurações-de-sistema-operacional)
- [Windows Server Core](#windows-server-core)
- [Template VM](#template-vm)
- [Partições](#partições)
- [Data e hora](#data-e-hora)
- [Page File](#page-file)
- [Shadow Copies](#shadow-copies)
- [Windows Update](#windows-update)
- [Instalação do agente TOPIA](#topia)
- [Placas de rede](#placas-de-rede)
- [Antivírus](#antivírus)
- [Programas Padrão](#programas-padrão)
- [Puppet](#puppet)
- [Zabbix](#zabbix)
- [Drivers Virtuais](#drivers-virtuais)
- [Monitoramento](#monitoramento)
- [Outras configurações](#outras-configurações)
- [Revisão](#revisão)
---

### Objetivo
---
Apresentar a forma correta de implantação de servidores com sistema operacional Windows, virtuais e físicos, na infraestrutura para clientes gerenciados, seguindo as melhores práticas de mercado, atendendo os pré-requisitos dos fornecedores e fabricantes, assim como requisitos de segurança estabelecidos pela Equinix.

#### Versões de S.O. Windows Suportadas

[Obrigatório]

| Windows Server 2016 | Windows Server 2019 | Windows Server 2022 |
|---------------------|---------------------|---------------------|
| Standard            | Standard            | Standard            |
| Datacenter          | Datacenter          | Datacenter          |



Sempre consultar o site dos fabricantes com relação a garantia e suporte entre o sistema operacional e hardwares a serem utilizados. 
- Windows Server Core 

A implementação de ambientes com Windows Server Core deverá ser feita sob alinhamento prévio com a gestão de MSH.

### Documentação
---
A ferramenta padrão para documentação dos ativos dos clientes é o ServiceNow, portanto todas as informações sobre o cliente devem estar contidas nesse sistema.

### ServiceNow
---
-   #### Runbook

	O objetivo desse documento é ser um “snapshot” de configurações do equipamento do cliente. Eventualmente utilizado em caso de RD do equipamento.

-   #### ETI

	O objetivo desse documento é conter as informações sobre o que foi acordado com o cliente referente à capacity e serviços disponibilizados no equipamento.

-   #### Topologia

	O objetivo desse documento é fornecer uma visão geral do ambiente e, se possível, a localização lógica do equipamento atual. 

-   #### Cadastro obrigatório no Service Now
  
    Todos os equipamentos cadastrados no Service Now devem possuir as seguintes informações:
     - Acesso remoto NAT incluindo IP e porta;
     - Cadastro de Usuário e senha;
     - IP, usuário e senha de IDRAC/ILO;
     - VMs em clusters informar o hostname do cluster no campo Description;

### Atlas
---
[Obrigatório em todos os clientes OSA ou TM]

Todo cliente gerenciado deverá ter as premissas do Atlas (Análise de Ambiente) configuradas:
  - Versão de Windows igual ou superior a Windows Server 2016
  - Versão do PowerShell instalado igual ou superior a 5.1 (Referencia: [Atlas](Premissa.Atlas.md))
  - Puppet agent instalado e em execução
  - MCO (MCollective) instalado e em execução
  - Zabbix agent instalado e execução pelo puppet via CSR
  - Commvault agent instalado e em execução
  - NTP sincronizado. Preferencialmente NTP Equinix 
  extntp.equinix.com.br
  1.extntp.equinix.com.br
  2.extntp.equinix.com.br
  3.extntp.equinix.com.br
  - Cadastro dos servidores no CSR para execução dos scripts (Home > Activation > Products > HSA)
  - Devem estar devidamente cadastrados no ServiceNow, Zabbix e CSR
  - Cadastro do item de configuração precisa ter a informação do **IP de NAT** com acesso remoto ao equipamento, com usuário e senha para login.

### Recomendações de instalação
---

#### Hardening

[Obrigatório]

Princípio de privilégio mínimo / função única
- Toda e qualquer feature ou role adicional instaladas automaticamente, deverão ser removidas logo após o término do processo de instalação do sistema operacional
- Para servidores HPE: Desabilitar e efetuar stop no serviço HP Proliant System Shutdown, conforme abaixo:

![alt text](img/img.windows/windows.jpg "")

#### Sistemas de arquivos e particionamento dos discos

[Obrigatório]

- É obrigatória a utilização de NTFS. 
- Quando se particiona um disco, o nível de segurança aumenta no sistema, isso porque cada partição tem sua tabela de alocação de arquivos separada.

#### Contas de usuários e permissionamento

[Obrigatório]

- O usuário Administrator obrigatórimente deve ser renomeado para eqxadmin; 
- Efetuar a criação do usuário eqxgerencia;
- Somente os usuários eqxadmin e eqxgerencia devem pertencer a grupos administrativos. Os usuários Alogadmin e Alogerencia não devem mais ser criados, pois esses usuários foram descontinuados;
- Nenhum serviço ou aplicação deve rodar com usuários administrativos, mesmo os de propriedade da Equinix, os usuários utilizados em serviços ou aplicações não devem possuir permissão de logon interativo;
- Utilizar senhas fortes com mínimo de 12 caracteres, utilizando maiúsculas, minúsculas, números e caracteres especiais. Pode-se gerar senhas aleatórias sugeridas em alguns sites, como o https://passwordsgenerator.net/;
- Habilitar política de Timeout de 30 minutos após 50 tentativas de senha

#### Fontes confiáveis

[Obrigatório]

- Todo o conteúdo necessário para a instalação do Sistema Operacional bem como de quaisquer complementos deve ter origem de uma fonte confiável, ou seja, exclusivamente dos repositórios da EQUINIX ou dos sites dos fabricantes devidamente validados por seus certificados; 
- Atualizações só devem ser obtidas a partir do servidor WSUS EQUINIX (Vide seção Instalação e Configuração >> Software >> Windows Update).


#### Remote Desktop 

[Obrigatório]

- Acesso RDP via NAT: Em acesso RDP via NAT, não utilizar portas efêmeras (altas). Efetuar direcionamento no firewall apenas com portas sequenciais a 3389 +X. Ex: 33890, 33891, 33892, 33893, etc. 
- NLA: Somente utilizar acesso remoto com NLA (Network Level Authentication) com restrições por usuários; O acesso aos servidores é feito exclusivamente através da PA Equinix (rjpawin.global.equinix.com, sppawin.global.equinix.com) com autenticidade devidamente comprovada através de Certificado Digital;
- Caso o cliente necessite ter um usuário de aplicação ou de leitura no sistema, deve-se seguir as origens das premissas de S.O básicas, com acesso restrito somente ao grupo de Remote Desktop Users. O cliente que acessar o ambiente por meio de algum notebook/desktop será preciso validar a versão do RDC adequada para acesso.

#### Auditoria 

[Obrigatório]

- Ativar Auditoria sobre os Arquivos de Sistema (Sucesso), Uso de Privilégios (Sucesso e Falha), Auditoria de Logon (Sucesso e Falha), através de Grupo de Políticas (GPMC.msc), se o servidor pertencer a um serviço de diretório. Configurar via Política Local (SecPol.msc) caso não exista um serviço de diretório. 

#### Redes

[Flexível]

- Manter o IPv6 habilitado no servidor;
- Desabilitar o Link-Layer Topology Discovery Mapper I/O e Link-Layer Topology Discovery Responder nas propriedades da placa de rede, somente no caso de o cliente não utilizar. 

#### Segurança

- UAC: Manter o UAC habilitado no servidor; [Obrigatório]
- Event Viewer: Configurar todos os logs do Event Viewer para possuir logs com tamanho máximo de 512MB e sobrescrever quando necessário; [Flexível]
- Windows Firewall: Nunca desativar o serviço nativo de firewall do Windows. Seguir as recomendações abaixo: [Flexível]
  - Caso o ambiente do cliente possua uma solução de firewall no ambiente, manter o serviço de firewall do Windows ativado e desativar os profiles de firewall;
  - Caso o cliente não possua uma solução de firewall no ambiente habilitar o Firewall do Windows e também os perfis de firewall

### Configurações de Hardware
---

#### BIOS

[Obrigatório]

- De acordo com os modelos de servidores, habilitar ou desabilitar as seguintes features: 
  - Server Availability > ASR Status > Disabled (somente para servidores HPE) 
  - Power Management Options > Power Profile > Maximum Performance  
  - Power Management Options > Power Regulator > Static High Performance 
  - Power Management Options > Minimum Processor Idle Power Core State > No C-States 
  - Power Management Options > Minimum Processor Idle Power Package State > No Package State
  - Para Hypervisors, modificar as seguintes congiurações:
    - Intel Virtualization Technology (Intel VT) > Enabled 
    - Intel® Virtualization Technology for Directed I/O (Intel VT-d) > Enabled 
    - Hardware-Enforced Data Execution Prevention (DEP) > Enabled 
    - Bit Intel XD (Execute Disable Bit) > Enabled
  - Para servidores HPE posteriores a GEn9, utilizar os workloads profiles específicos equivalentes aos Hardwares. Consultar a documentação do servidor que está sendo ativado e mudar as configurações de acordo com as features envolvidas;

**BIOS + Firmware + Drivers:** Sempre atualizar com a última versão BIOS, Firmware e Drivers dos servidores físicos antes de instalar o SO. Utilizar as ferramentas disponibilizadas pelos fabricantes para garantir a última versão e compatibilidade com o hardware:
  - Dell: SUU – Server Update Utility
  - HP: SPP – Service Pack for Proliant

#### Processadores, memória e discos

- **Processador:** Baseado em arquitetura x64, seja ele Intel ou AMD [Obrigatório] 

- **Memória:**
  - Servidor Físico: Mínimo - 8GB RAM, recomendável 16GB RAM [Flexível]
  - Servidor Virtual:  Mínimo - 4GB RAM, recomendável 8GB RAM [Flexível]

- **Hardware:** Devem possuir contrato de garantia válida com o fabricante de no mínimo 6 meses após a aquisição [Flexível]
- **RAID:** Suportados 1, 5, 6, 10 e 50 (de acordo com a necessidade do cliente), somente estes são suportados pelo modelo de gerenciamento. Para HW HPE, onde todos discos forem do mesmo tipo e tamanho, sempre configurar 1 array com TODOS os discos, criar um logical volume de no mínimo 100GB para o sistema operacional em RAID1 e utilizar 1 ou mais logical volumes para os demais discos, de acordo com a necessidade do cliente. Caso haja discos diferentes ou seja necessário criar arrays de RAIDs diferentes, criar os arrays e indicar na BIOS qual é o logical volume de boot.

- **Hardwares monofontes:** que estejam em alta disponibilidade (Ex.: 2 servidores em Cluster SQL Server), deverão ter esses equipamentos ligados em circuitos diferentes (não utilizar a STS)

- **Quantidade Hard Disk x Volumetria:** [Flexível]
  - SO – Mínimo SATA e 100GB de espaço útil (Consultar sessão: Page File > Servidor com demais aplicações e VMs); 
  - Dados – Mínimo SAS e volumetria indicada pelo cliente.
  - Recomendamos para VMs que serão replicadas ter o SWAP separado em um disco dedicado com tamanho máximo de 50GB e sem monitoramento ativo.

- **Fontes redundantes:** Ambas conectadas diretamente na energia elétrica do rack do cliente em circuitos diferentes [Obrigatório]

#### Redes - Equipamento Fisíco

- Interfaces de rede [Flexível] 
  - Mínimo de 2 (duas) placas Gigabit Ethernet (Rede externa e de Serviço), se o servidor possuir a função de Failover Cluster, deve possuir no mínimo 4 interfaces configuradas no total, veja especificação na premissa de Failover Cluster Windows (https://git.mop.equinix.com.br/br-msh-problem/premissas/-/blob/Producao/Premissa.FailoverCluster.md);
  - Caso haja mais interfaces de rede, convém utilizar a função de Teaming para melhor performance;
  - Para os cenários possíveis em ambientes 1Gb e 10Gb, consultar seção “Cenários Possíveis”.

- **ILO / IDRAC:** [Obrigatório]
- **Dedicada:** Em ambientes que contemplam ILO/IDRAC dedicada, a porta deve estar configurada e funcionando, bem como o IP, login e senha devem estar cadastrados no ServiceNow;
- **Compartilhada:** É possível utilizar Shared ILO/IDRAC em projetos que uma placa dedicada não for considerada. (Conforme item: Cenários possíveis).
ILO - KB0015457 
https://equinixcsm.service-now.com/kb_view.do?sys_kb_id=849c133b972a39d00c103486f053afcb&preview_article=true

IDRAC - KB0015455 
https://equinixcsm.service-now.com/kb_view.do?sys_kb_id=90275bb3976639d00c103486f053af84&preview_article=true

#### HBA - Equipamento Fisíco
[Obrigatório]

- Deve-se instalar sempre o software gerenciador da HBA de acordo com o fabricante. Links para download abaixo:
  - QLogic / Brocade / Cavium: 
    - http://driverdownloads.qlogic.com/QLogicDriverDownloads_UI/Defaultnewsearch.aspx  (Selecionar o modelo e escolher o software correspondente)
- Emulex / Broadcom: 
  - https://www.broadcom.com/products/storage/fibre-channel-host-bus-adapters/  (Selecionar o modelo e escolher o software correspondente)
correspondente)
- Em ambientes de cluster, verificar sempre a possibilidade de se ter HBAs do mesmo modelo
- Se o Storage for compartilhado, deve-se deixar o driver da HBA configurado com limite de block size para 64k. Instruções disponíveis em:
  - HBA Emulex - KB0011024
  - HBA QLogic - KB0010809
- Para HBAs Emulex e Brocade, alterar o parâmetro NodeTimeout de 30 segundos para 3 segundos. Essa alteração deve ser efetuada através do software de Gerenciamento da HBA.

- #### Rede e Link:
- Conexão Giga Ethernet a rede do cliente na Equinix; 
 - Conexão à rede de Backup (Dedicado ou como Serviço). Caso seja backup compartilhado, a mesma rede também é utilizada para NAS SMB e iSCSI;
- Demais conexões de rede de acordo com a necessidade do ambiente do cliente. 

#### Cenários possíveis

[Flexível]

- Placas 1Gb
  - Placa 1Gb Quad c/ shared ILO/IDRAC – Storage FC
    - **Redes:** Nesse cenário, devem ser configurados 2 NIC Teams, de 2 portas cada:
      - **Team_MGT:** t team estático, com load balance mode dinâmico e Link aggregation configurado no Switch, com 2 portas de cada placa, conectadas em switches diferentes, para gerenciamento. Criar interfaces do teaming para as redes de Gerenciamento, backup e Heartbeat em caso de Failover Cluster. Também será configurada a Shared ILO/IDRAC em uma das portas desse NIC team;
      - **Team_PRD:** team estático, com load balance mode dinâmico e Link aggregation configurado no Switch, com 2 portas de cada placa para produção. Em caso de mais de uma VLAN, criar interfaces no NIC Team para segregação. 
      - **Storage:** Nesse cenário considera-se o uso de SAN, com conexão via HBA e MPIO configurado adequadamente.

![alt text](img/img.windows/windows1.png "")

- **2 Placas 1Gb Quad c/ shared ILO/IDRAC – Storage FC**
  - **Redes:** Em casos de 2 placas quad, devem ser configurados:
    - **Team_MGT:** team estático, com load balance mode dinâmico e Link aggregation configurado no Switch, com 1 porta de cada placa, conectadas em switches diferentes, para gerenciamento. Criar interfaces do teaming para as redes de Gerenciamento, backup e Heartbeat em caso de Failover Clsuter. Também será configurada a Shared ILO/IDRAC em uma das portas desse NIC team;
    - **Team_PRD:** team estático, com load balance mode dinâmico e Link aggregation configurado no Switch, com 3 portas de cada placa para produção. Em caso de mais de uma VLAN, criar interfaces no NIC Team para segregação. 
    - **Storage:** Nesse cenário considera-se o uso de SAN, com conexão via HBA e MPIO configurado adequadamente.

![alt text](img/img.windows/windows2.png "")

- **2 Placas 1Gb Quad c/ shared ILO/IDRAC – Storage ISCSI**
- **Redes:** Em casos de 2 placas quad e storage ISCSI, devem ser configurados:
- **Team_MGT:** team estático, com load balance mode dinâmico e Link aggregation configurado no Switch, com 1 porta de cada placa, conectadas em switches diferentes, para gerenciamento. Criar vSwitch e adaptadores virtuais para as redes de Heartbeat, Live Migration e Backup. Também será configurada a Shared ILO/IDRAC em uma das portas desse NIC team;
- **Team_PRD:** team estático, com load balance mode dinâmico e Link aggregation configurado no Switch, com 2 portas de cada placa para produção (VMs), criar um vSwitch para utilização das redes do cliente. 
- **ISCSI:** É necessário dedicar 1 porta de cada placa para conexão com o storage, com Jumbo frames habilitado e sem configuração de NIC team.

![alt text](img/img.windows/windows3.png "")

- **Placas 10Gb**
  - **1 Placa 10Gb Dual c/ ISCSI – Shared ILO/IDRAC**
    - Em cenários 10Gb dual, ambas portas devem estar configuradas em um team estático, com load balance mode dinâmico e Link aggregation configurado no Switch e Team interfaces devem ser criadas para divisão lógica do tráfego de gerenciamento, ISCSI, backup, produção e Heartbeat, em caso de cluster, conforme abaixo. A interface de ILO/IDRAC deve ser configurada em uma das interfaces físicas.

![alt text](img/img.windows/windows4.png "")

- **2 Placas 10Gb Dual c/ ISCSI – Shared ILO/IDRAC**
  - Em cenários 10Gb quad, todas portas devem estar configuradas em um team estático, com load balance mode dinâmico e Link aggregation configurado no Switch e Team interfaces devem ser criadas para divisão lógica do tráfego de gerenciamento, ISCSI, backup, produção e Heartbeat, em caso de cluster, conforme abaixo. A interface de ILO/IDRAC deve ser configurada em uma das interfaces físicas.

![alt text](img/img.windows/windows5.png "")

### Instalação e Configuração de Sistema Operacional
---

#### Windows Server Core

[Flexível]

- Em casos pré-autorizados pela gestão de MSH, é importante que haja uma console de gerenciamento (2016 / 2019 / 2022). 
  - Somente as roles e features do KB abaixo são suportadas: https://msdn.microsoft.com/en-us/library/hh846322(v=vs.85).aspx

#### Template VM

[Obrigatório]

- Não instalar agentes de antivírus em VMs templates, para evitar computadores duplicados nas consoles do Trend Micro Workload Security ou Symantec Endpoint Protection.
- Sysprep para VMs – TEMPLATE (independente do hypervisor)
  - Em ativação nova sempre executar o sysprep /generalize ao utilizar um template;
  - Em troca de modalidade de gerenciamento, verificar se existem SIDs duplicados na rede e informar ao cliente. Caso ele não permita a execução do sysprep /generalize, deverá ser emitido uma notificação de risco;
  - Validar se existem SIDs duplicados, executando o comando Powershell: 
    - Get-ADComputer -filter * -Properties objectSid | Select-Object name,objectSid










#### Partições

[Flexível]

- Partição do Sistema Operacional (C:): Servidores físicos devem possuir configuração de no mínimo 100GB para a partição. Para servidores CLOUD EQUINIX a partição deve ser criada com 100GB (segundo o template). Para servidores em CLOUD PRIVADA a partição deve ser criada com no mínimo 100GB, considerar uma adição de 20% sobre esse valor para os casos onde o servidor demande até 32GB de memoria Swap, ou a inclusão de um disco dedicado com valor máximo de 50GB e sem monitoração para receber o Page File. (Referencia Sessão: Page File).  A partição deve ser formatada sempre com NTFS;
- Partição de dados (D:): Partição deve conter o volume restante dos discos configurada e formatada sempre com NTFS. Em relação a blocagem de formatação, verifique esta seção nas premissas relacionadas as aplicações que serão instaladas na unidade de dados. 

#### Data e hora 
- **Físico:** Podem ser ajustados na BIOS do equipamento, sempre considerando o horário e localidade (GT -03:00, Brasília) ou utilizar o NTP configurado pelo domínio; 
- **Virtual:** O horário pode ser sincronizado pelo Integration services ou então por NTP no domínio; 

#### Page File 

[Obrigatório para todos os items com gerenciamento TM ou OSA]

- O tamanho do page file não poderá ocupar mais de 20% do espaço disco C:, portanto se o servidor tiver muita RAM, o disco C: deverá ser maior.
  - Deve ser configurado **removendo o modo automático** da instalação do Windows 
  - Sempre utilizar para conversão 1GB = 1024MB
  - **Servidor com Exchange Server:**
    - **Exchange 2019:** Ref: https://docs.microsoft.com/en-us/exchange/plan-and-deploy/system-requirements?view=exchserver-2019#hardware-requirements-for-exchange-2019
      - Pagefile com 25% do total de RAM (máximo de 64GB). A memória máxima é 256GB. 
    - **Exchange 2016:** Ref: https://docs.microsoft.com/en-us/exchange/plan-and-deploy/system-requirements?view=exchserver-2016#hardware-requirements-for-exchange-2016
      - Menos de 32GB de RAM: Pagefile tamanho da RAM + 10MB
      - Mais de 32GB de RAM:  Pagefile 32GB + 10MB
  - **Servidor com demais aplicações e VMs:** Seguir a recomendação geral de uso:
      - RAM de 1GB a 10GB - Pagefile 1,5x tamanho da RAM + 128MB
      - RAM de 10GB a 32GB - Pagefile tamanho da RAM + 128MB
      - RAM acima de 32GB - Pagefile fixo 32GB + 128MB
      - OBS: Em casos que haja falha em servidores com mais de 32GB de RAM sem conseguir extrair o Kernel Memory Dump corretamente, para troubleshoot, deverá ser alterado o tamanho do pagefile para "tamanho da RAM + 128MB", independente do tamanho da RAM. Pode ser utilizado outro disco local do host para alocar o pagefile.
  
  Maiores informações: http://support.microsoft.com/kb/2021748, https://docs.microsoft.com/en-us/windows/client-management/system-failure-recovery-options


#### Shadow Copies

[Flexível]

- Desabilitar a função de Shadow Copies no Computer Manager.

#### Windows Update 

[Obrigatório]

- Todas as atualizações recentes críticas e de segurança devem ser instaladas no servidor, incluindo o último Service Pack disponível
  - WSUS:  
    - Desabilitar o schedule de restart, que vem como padrão do SO. Para tanto utilizar o sconfig e deixar a opção Download Only, seguindo o passo-a-passo do seguinte artigo: https://blogs.technet.microsoft.com/mu/2016/10/25/__trashed/.  
    - Não configurar servidores Windows Server 2016 para efetuar download diretamente da internet, mas sempre configurar os mesmos para efetuar download somente através de servidores do WSUS seguindo os passos abaixo
- Para ambientes que contemplam o serviço de Gerenciamento Total ou Administração de sistema Operacional adicionar o endereço do WSUS de acordo com a região: 
  - RJ - wsus-rj1.hosting.equinix.com.br:8530
  - SP - wsus-sp3.hosting.equinix.com.br:8530
- O Windows update deve ser configurado conforme abaixo:  
  - Apenas baixar as atualizações; 
  - Diariamente às 22h00min; 
  - Não instalar automaticamente; 
  - Não habilitar o download para usuários não-administrativos.

#### Procedimentos para instalação do VRX Topia

- Segue abaixo o link para ser feita a instalação do VRX Topia, para gerenciar as atualizações de KBs Microsoft nos equipamentos.

https://equinixcsm.service-now.com/kb_view.do?sys_kb_id=29307b078795b514c48cb845dabb3556&sysparm_rank=5&sysparm_tsqueryId=0ccdef6393d6f114ef0af8e74dba10ba


#### Placas de rede

[Obrigatório]

- Inserir os IPs destinados ao cliente nas interfaces de rede;
- **DNS:** Configurar os endereços de DNS da EQUINIX nas placas de rede, exceto quando o cliente tiver um domínio configurado. Neste caso o DNS deverá ser sempre o endereço do AD local (NUNCA utilizar endereço DNS Publico). Utilize os DNSs abaixo para servidores que não possuem serviços de diretório (Active Directory):
  - DNS - SP1 - 200.198.176.18 - 200.155.1.18 
  - DNS - SP2 - 200.143.177.4 - 200.143.177.40 
  - DNS – SP3 - 138.36.219.201 - 138.36.219.202 
  - DNS - RJ1 - 201.49.216.57 - 201.49.216.58 
  - DNS - RJ2 - 179.107.35.140 - 179.107.35.141

#### Antivírus

[Obrigatório]

- Seguir conforme premissa de [Antivirus](https://msh-git.equinix.com.br/br-msh-problem/Premissas/-/blob/master/Premissa.Antivirus)

#### Programas Padrão

[Flexível]

- Devem ser instalados os seguintes programas:
  - Process Explorer v17.02 (https://learn.microsoft.com/en-us/sysinternals/downloads/process-explorer)
  - TreeSize V4.6 (versão FREE - https://www.jam-software.com/treesize_free/changes.shtml?ca=1)
  - SysinternalsSuite
  - Instalar os softwares de suporte ao hardware, sempre em última versão:
  - **Dell:** Dell OpenManage Server Administrator (OMSA).
  - **HPE:**
    - Para os servidores HPE até Gen9: 
      - HPE System Management Homepage 
      - HPE iLO 3/4 Management Controller Driver Package for Windows Server 
      - HPE Insight Management Agents for Windows Server 
      - HPE ProLiant Agentless Management Service for Windows 
      - HPE Smart Storage Administrator (SSA) 
    - Para todos os servidores Gen10 em diante: 
      - Todos os itens acima, e acrescentar: 
      - HPE System Insight Manager (HPSIM) com suas dependências 
      - (Necessário instalar em apenas um servidor do ambiente utilizando-se por padrão o SQL Server Express no momento da instalação.) 
      - Link para download e com documentação do HPE Systems Insight Manager: https://www.hpe.com/us/en/product-catalog/detail/pip.489496.html 
      - PS: Necessário habilitar SNMP e instalar o WBEM Provider específico em cada modelo de servidor para possibilitar a coleta de informações de hardware através do HPE SIM.

#### Puppet

[[Obrigatório]

- A instalação do agente do Puppet, é obrigatória para os servidores que contemplam o serviço (Gerenciamento Total GT) e (Administração de Sistema Operacional ASO).
- Testar a comunicação telnet conforme a tabela abaixo:

![alt text](img/img.windows/windows6.png "")

- Para dúvidas, consultar o KB0010767

#### Zabbix

[Obrigatório]

- Instalar o agente do zabbix via Puppet no CSR (Agente de zabbix será instalado via .MSI na 5.0.19 versão 2 ) 
- **SNMP:** Obrigatória configuração de monitoração de hardware via SNMP. Utilizar Community M0n1t0r3qu1n1X

#### Drivers Virtuais 

[Obrigatório]

- Para servidores virtuais, instalar o Hyper-V Integration Services, Vmware Tools, Oracle VM Tools, enfim, os serviços de integração de acordo com o Hypervisor.

#### Monitoramento

[Obrigatório]

Todos os equipamentos gerenciados (TM) devem ser monitorados pela ferramenta ZABBIX.

A configuração inicial da monitoração deve atender aos requisitos básicos:

- CPU
- Memória
- Discos

A definição de templates deve manter o padrão alinhado com a equipe de NOC da Equinix, conforme a Premissa de [Windows](Premissa.Windows.md);


Utilizar os templates abaixo:

**TM**
**TEMPLATE.TM_WINDOWS_BASICO**
**TEMPLATE.TM_PORTA_TS_AGENTE**

**OSA**
**TEMPLATE.OSA_WINDOWS_BASICO**
**TEMPLATE.OSA_PORTA_TS_AGENTE**

#### Outras configurações

[Obrigatório]


- Configurar o "Write debbugging information" para "Kernel Memory dump"
- Instalar o "Telnet client" através do "Add Feature". 
- Caso o servidor possua comunicação com Storage FC ou iSCSI, deverá ser instalado o MPIO (Multipath) e configurado os caminhos como Round Robin (Padrão). 
- Em Performance options, selecionar a opção de Ajustar para a melhor performance (“Adjust to best performance”). 
- NUNCA entregar o equipamento sem o devido serial aplicado.  

Qualquer item não descrito nesta premissa caberá avaliação pontual da equipe de BR-MSH PROBLEM para aceitação, proposição alternativa ou recusa mediante impeditivo técnico/operacional.

KBs relacionados

- KB0011024 - HBA Emulex (Windows) - Alterar blocagem (Limit Transfer Size)
- KB0010809 - HBAs QLOGIC - Windows - Consultar / Alterar o Transfer Size
- KB0010767 - Manual Instalação Puppet Agent - Windows


#### Revisão


| Revisor Responsável          | Validador Responsável     | Atualizado |
|------------------------------|---------------------------|------------|
| João Arêa                    | Pamella Canova            | 29/06/2021 |
| Rafael Pereira               | João Area                 | 16/12/2022 |
| Thiago Araujo                | Leonardo Abreu            | 10/11/2023 |

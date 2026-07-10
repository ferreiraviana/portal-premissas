![alt text](img/logo_eqx.jpg "Logo EQX")

# Premissa de Backup Dedicado Commvault
---

# Sumário
-   [Objetivo](#objetivo)
-   [Responsabilidades](#responsabilidade)
-   [Especificação](#especificação)
-   [Armazenamento definição](#armazenamento-definição)
-   [Sistemas Operacionais do Media Agent para o serviço de Backup e Rede](#sistemas-operacionais-do-media-agent-para-o-serviço-de-backupb-e-rede)
-   [Nomeclatura](#nomeclatura)
-   [Hardware](#hardware)
-   [Retenção de Backup](#retenção-de-backup)
-   [Política de Backup](#políticas-de-backup)
-   [CommVault CommCell](#commVault-commCell)
-   [live Sync CommCell](#live-sync-commCell)
-   [Acesso Commvault Commcell ](#acesso-commvault-commCell)
-   [Software Windows](#software-windows)
-   [Particionamento de Exemplo](#particionamento-de-exemplo)
-   [Usuários](#usuários)
-   [Gerenciamento](#gerenciamento)
-   [Aspectos gerais](#aspectos-gerais)
-   [Observações gerais](#observações-gerais)
-   [Revisão](#revisão)  

---

## Objetivo
---

Definir premissas básicas para instalação dos componentes MA (Media Agent) baseado na tecnologia CommVault, mas incorporado no produto Backup Dedicado para qualquer modalidade e/ou produto.

## Responsabilidades
---
**EMS - INFRASTRUCTURE ** Gerenciar e administrar estrutura de backup Dedicado;

**EMS - OPERATIONS:** Manter o ambiente integro para garantir a execução do backup com sucesso;

## Especificação

**Media Agent, definição**

A infraestrutura de backup utiliza servidores x86 como elementos de transferência e armazenamento não de arquivos em formato nativo, mas de blocos nomeados como &quot;_chunks ¹_&quot; de dados, que nada mais são, que blocos de 8k.

¹[https://en.wikipedia.org/wiki/Chunk\_(information)](https://en.wikipedia.org/wiki/Chunk_(information))

**CommCell, definição**

CommCell é o servidor Master, ou seja, a console de execução de backup, utilizado apenas para orquestração das tarefas. Ele é um ponto único de acesso individual para cada datacenter e é utilizado como ponto de apoio para todas as configurações do ambiente. Este é também um ponto de distribuição de pacotes de instalação ou mesmo de atualização dos ambientes.

 **De-duplicação/Index Cache, definição**

Para alcançar as taxas de armazenamento desejadas sem a necessidade de armazenamento integral de todos o volume de dados as tecnologias atuais de backup utilizam-se de volumes para desduplicacao de dados, esses volumes devem ser independentes e possuir capacidade mínima de operação superior a 300 _&quot;iops_&quot; o que irá ensejar o uso de dispositivos SSD arranjados em volumes organizados _&quot;raid&quot;_, na tabela destinada a volumetria serão demonstradas as necessidades de volumetria e arranjos combinatórios de disco para alcançar a volumetria e performance necessária.

¹ https://en.wikipedia.org/wiki/IOPS

²https://en.wikipedia.org/wiki/RAID

## Armazenamento definição
----

As necessidades de armazenamento integral de todo o volume de dados serão contempladas através da utilização de combinação de discos. Esses volumes devem ser independentes e possuir capacidade mínima de operação superior a 300 _&quot;iops_&quot;, o que na grande maioria dos casos vai ensejar o uso de dispositivos arranjados em volumes organizados em _&quot;raid&quot;._ Na tabela destinada a volumetria serão demonstradas as necessidades de volumetria e arranjos combinatórios de disco para alcançar a volumetria necessária.

¹ https://en.wikipedia.org/wiki/IOPS

²https://en.wikipedia.org/wiki/RAID

## Sistemas Operacionais do Media Agent para o serviço de Backup e Rede
----

Serão aceitos sistemas operacionais modernos baseados em processadores x86-64bits apenas como servidores de infraestrutura de backup dos clientes a estes estão limitados aos seguintes sistemas operacionais homologados pela equipe de Hosting:

- **Sistema Operacional:** Microsoft Windows Server 2022 Datacenter ou STD 64 bits
- **Sistema Operacional(Preferencial):** Microsoft Windows Server 2019 Datacenter ou STD 64 bits
- **Sistema Operacional:** Microsoft Windows Server 2016 Datacenter ou STD 64 bits

Obs: Nenhum serviço será aceito com sistemas operacionais legados ou diferentes dos acima descritos. Todos os sistemas operacionais devem estar devidamente atualizados e em suas últimas versões &quot;Services Pack&quot; e releases no momento da avaliação de seus respectivos &quot;runbooks&quot;.

**Interface de Rede:**
 WAN GER
 – 02x 1GB em NIC TEAM redundante – Interface de acesso / gerência do equipamento

 LAN BKP
 – 04x 1GB em NIC TEAM LACP (mínimo)
 ou
 – 01x 10 GB – Uso dedicado ao tráfego de backup apenas

 VLAN
 – Devem ser entregue uma VLAN exclusiva para a rede de backup

  IDRAC/ILO
 - Todo servidor deve vir com IDRAC/ILO configurada.

## Nomenclatura
---

A nomenclatura de servidores de backup deve conter as seguintes características:

- **Nickname:** Nickname na ficha do cliente
- **Servidor:** Como alguns clientes possuem mais de um ativo para o backup o nome do servidor representa se ele é o primeiro, segundo etc...
- **Localidade:** SP1, SP2, SP3, SP4 e assim por diante de acordo com o site que hospeda este equipamento.

**Padrão:** NICKNAME+BKP01+DATACENTER

 Exemplo prático:

**EQUINIX-BKP01-SP2**

## Hardware
---

Na premissas anteriores, existia uma lista de hardwares homologados, porém após o backup dedicado deixar de ser um produto Equinix, os projetos são definidos conforme o front size contratado pelo cliente x  o sugerido pela fabricante, seguindo o link abaixo:

https://documentation.commvault.com/11.24/expert/111985_hardware_specifications_for_deduplication_mode.html

Armazenamento de Exemplo:

| **SSD** | **SATA / SAS** | **BackEnd** |
| --- | --- | --- |
| 2x 960GB raid 1 | 6x 8TB raid 5 | 30 TB |
| 2x 1.8TB raid 1 | 10x 8TB raid 6 | 60 TB |
| 2x 1.8TB raid 1 | 12x 8TB raid 6 | 80 TB |

As informações de backend são relativas ao armazenamento final do ambiente do cliente e não devem ser confundidos com licenciamento (frontend), o licenciamento é relativo ao momento em que o software de backup se conecta ao ambiente para proceder com o backup, backend é a volumetria que será tratada diariamente mais o volume a ser retido.

 No momento do size do projeto o Arquiteto deve usar a calculadora de Produtos inserindo a volumetria a ser protegida para chegar aos valores finais de Array de discos.

Em todas as volumetrias devem estar habilitados os seguintes itens de hardware:

- Cache de controladora RAID.
- Bateria para configurações de write Cache-Back.
- No caso de controladoras HP P4\*\* divisão de 50% entre gravação e escrita.
- Para hardwares HP a configuração de ASR deve estar desabilitada.
- A configuração de Power Balance, deve estar configurada para &quot;Maximum Performance&quot;.
- Todas as controladoras de rede devem ser utilizadas no formato de teaming. O teaming não deve misturar interfaces em placas fisicamente diferentes, todas as portas de uma mesma placa devem ser utilizadas e tanto vlans de gerência e de backup devem ser utilizadas no mesmo teaming.
- Para servidores que possuam a controladora HP Intel i366FLR o driver proprietário da Intel deve ser utilizado, uma vez que esse tem ganhos incrementais de performance. ¹
- Para controladoras Broadcom, devemos atualizar o último firmware e o software de gerência da própria Broadcom. ²
- Para volumetrias além das informadas serão feitos projetos individuais, com os devidos cálculos. Porém é importante levar em consideração que eles serão cálculos baseados na planilha e não apenas multiplicações diretas da volumetria.


¹[https://www.hpe.com/us/en/product-catalog/options/pip.hpe-ethernet-1gb-4-port-366flr-adapter.5288150.html](https://www.hpe.com/us/en/product-catalog/options/pip.hpe-ethernet-1gb-4-port-366flr-adapter.5288150.html)
 ²https://www.broadcom.com/products/ethernet-connectivity/network-adapters/

## Retenção de Backup
---

As retenções de backup padrões aceitos são:

| **DIARIO** | **SEMANAL** | **MENSAL** | **ANUAL** |
| --- | --- | --- | --- |
| 7~30 | 15~60 | 30~1825 | 365~1825 |

- **É ideal manter o backup diário em múltiplos de 7, caso o ciclo tenha 5 dias múltiplos de 5.**

## Política de Backup
---

A política de backup foi estruturada seguindo as políticas padrões definidas pela equipe de Gerência de Hosting da Equinix.

| **DOM** | **SEG** | **TER** | **QUA** | **QUI** | **SEX** | **SAB** |
| --- | --- | --- | --- | --- | --- | --- |
| I | I | I | I | I | I | F |
| I | I | I | I | I | F | I |
| F | I | I | I | I | I | I |
| D | D | D | D | D | D | D |

F = Full

D = Databases, dedups e manutenções.

I = Incremental

1. **Horário de Execução**

A política de backup pode ser executada a qualquer momento que o cliente desejar. Porém devemos entender que existem horários mais propícios para a execução sem impacto no ambiente. Abaixo uma tabela de horários sugerido para execução das atividades de backup:

| **DOM** | **SEG** | **TER** | **QUA** | **QUI** | **SEX** | **SAB** |
| --- | --- | --- | --- | --- | --- | --- |
| 08:00 06:00 | 20:00 06:00 | 20:00 06:00 | 20:00 06:00 | 20:00 06:00 | 20:00 06:00 | 19:00 |

## CommVault CommCell
---

Os servidores de controle dos Jobs de backup estão listados abaixo:

| **IP** | **NOME** | **LICENÇA** | **SITE** |
| --- | --- | --- | --- |
| 138.36.219.218 | CS-CA01-SP3 | Capacidade | SP3 |
| 200.143.177.20  | CS-CA01-SP2 | Capacidade | SP2 |
| 177.184.10.117 | COMMSERVE-CA01-RJ1 | Capacidade | RJ1 |
| 179.107.35.200 | CS-CA01-RJ2 | Capacidade | RJ2 |

Os servidores acima devem ser acessíveis através dos firewalls do cliente, com regras para host e não apenas para porta, é primordial que em caso de NAT cada servidor abaixo consiga acesso aos hosts acima. Para registro após isso toda a comunicação entre os nós é feita através do Media Agente utilizando a LAN. Para esse é imprescindível uma comunicação direta seja via NAT _&quot;address to address&quot;_ ou com o endereço ip público atribuído diretamente ao Media Agente.

## Live Sync CommCell

Atualmente, todos os servidores commcell possuem redundancia, para caso de um DR, possamos realizar a virada para o commcell passivo e seguir com a operação.

| **IP** | **NOME** | **LICENÇA** | **SITE** |
| --- | --- | --- | --- |
| 200.143.177.21 | CS-CA02-SP3 | Capacidade | SP2 |
| 138.36.219.219  | CS-CA02-SP2 | Capacidade | SP3 |
| 179.107.35.201 | COMMSERVE-CA02-RJ1 | Capacidade | RJ2 |
| 177.184.10.118 | CS-CA02-RJ2 | Capacidade | RJ1 |

É de extrema importancia todos os clientes ativados nas consoles de backup dedicado comunicarem com ambas as consoles do site.

**Ex: Caso um cliente seja ativado na console de SP3 (cs-ca01-sp3), ele também deverá ter comunicação estabelecida com a console CS-CA02-SP3)


## Acesso CommVault CommCell

Para acesso as consoles commvault, devemos utilizar o client commvault (diretamente pelo laptop) ou acesso via webconsole, utilizando os endereços abaixo:

cvrj1.equinix.com.br

cvrj2.equinix.com.br

cvsp2.equinix.com.br

cvsp3.equinix.com.br


## Software Windows
---

Para hardwares Dell deverá ser instalado o gerenciador Dell Open Manager, todos os firmwares do servidor devem estar atualizados, respectivamente versões 8.3 e 9.1 do Dell OMSA.
[http://bkp.equinix.com.br/download/OM-SrvAdmin-Dell-Web-WINX64-8.3.0-1908\_A00.exe](http://bkp.equinix.com.br/download/OM-SrvAdmin-Dell-Web-WINX64-8.3.0-1908_A00.exe)
[http://bkp.equinix.com.br/download/OM-SrvAdmin-Dell-Web-WINX64-9.1.0-2757\_A00.exe](http://bkp.equinix.com.br/download/OM-SrvAdmin-Dell-Web-WINX64-9.1.0-2757_A00.exe)

Para hardwares HP deverá ser instalado o gerenciador HP Smart Storage Administrator e HP System Management Homepage, assim como o agente de SNMP.
[http://bkp.equinix.com.br/download/cp034022\_HPE\_System\_Management\_Homepage\_for\_Windows\_x64-2003-2012r2-2016.exe](http://bkp.equinix.com.br/download/cp034022_HPE_System_Management_Homepage_for_Windows_x64-2003-2012r2-2016.exe)
[http://bkp.equinix.com.br/download/cp031569\_HPE\_Insight\_Management\_Agents\_for\_Windows\_Server\_x64\_Editions.exe](http://bkp.equinix.com.br/download/cp031569_HPE_Insight_Management_Agents_for_Windows_Server_x64_Editions.exe)
[http://bkp.equinix.com.br/download/cp018121\_HP\_ProLiant\_Array\_Configuration\_Utility\_for\_Windows\_64-bit.exe](http://bkp.equinix.com.br/download/cp018121_HP_ProLiant_Array_Configuration_Utility_for_Windows_64-bit.exe)

Para hardwares de robôs de backup, o último firmware do sistema operacional deste deve estar atualizado conforme site do fabricante.

Para os componentes do CommVault o download deve ocorrer a partir da própria fabricante:

https://ma.commvault.com/

## Particionamento de Exemplo

| **Partição** | **Volume** | **Tipo** | **Block Size** | **Alvo** |
| --- | --- | --- | --- | --- |
| C: | 200GB | NTFS | 64k | S.O Windows |  
| D: | 960GB | NTFS |  | DDB e Instalação Commvault |  
| E: | 480GB | NTFS |  |  INDEX |  
| F:Mount Point | STAGING até 10TB | NTFS |  | STAGING |
| PageFile | ½ da ram | PageFile |  | S.O. |

 Esses valores acima são sugestões que podem variar de acordo com o Frontsize contratado pelo cliente. Sugerimos seguir as especificações da fabricante:
 https://documentation.commvault.com/v11/expert/111985_hardware_specifications_for_deduplication_mode.html

**\*STAGING** deve ser utilizada em 100% da volumetria disponível nas montagens de raid, todas as montagens de raid devem ser em raid 6. Os volumes de deduplicação devem ser mantidos disponíveis para crescimento posterior. No momento da instalação não é necessário utilizar 100% do volume disponível.

**\*\*STAGING** para clientes com a volumetria acima de 10 TB deverá ser criado Virtuais Devices na controladora de até 10 TB cada, posteriormente entregar ao Sistema Operacional o volume total que será criado como um &quot;Mount Point&quot;.

Ou

Em caso de discos maior de 12 TB cada, criar a segmentação de 4 discos em RAID 6 pela controladora da Storage e ao apresentar ao Sistema Operacional, particionar em 5 TB cada e entregar como um &quot;Mount Point&quot;.

Exemplo: O cliente possui 30 TB de backend (armazenamento), os Virtuais Devices ou Disk Pools serão 3 de 10 TB, totalizando 30 TB de entrega ao Sistema Operacional. Na configuração da Disk Library do CommVault ele será apresentado como vários paths de gravações e a própria solução irá fazer o balanceamento de carga entre as partições.

## Usuários
---

| **SO/Serviço** | **Usuário** | **Acesso** | **Senha** |
| --- | --- | --- | --- |
| Windows | Eqxadmin/ Eqxbkp | Admin/RDP | XCFADa$%a1@,##A |
| Windows | Eqxadmin/ Eqxbkp | Admin/RDP | @)--q=ADAR!ddA1 |
| Linux | Eqxadmin/ Eqxbkp | SUDO/SSH | ---ARADA\*()!!-- |
| mysql | Eqxadmin/ Eqxbkp | Local/mysql | ---$$%$AaSFASD--#1 |
| sqlserver | Eqxadmin/ Eqxbkp | Local/Sql/AS | S==+=!asdadarRADAR-- |
| oracle | Eqxadmin/ Eqxbkp | Local/Oracle | !SWsdaraca!@#%% |
| Windows | Administrator | desabilitado ||
 |
| Linux | Root | Somente local |
 |

**\*Senhas** sugeridas devem conter a complexidade necessária para não ser possível a exploração através de brute force, 16 caracteres maiúsculos, minúsculos, letras e símbolos.

A verificação deve ser feita através do site: http://www.passwordmeter.com/

## Gerenciamento

**IDRAC**

Em todos os servidores de backup, devem ser habilitados e configurados a IDRAC/ILO, previamente testada e funcionais. Seu endereço ip de acesso e login devem ser cadastrados na ficha do CI no ServiceNow.

**DDB GLOBAL**

No momento da criação da base de Desduplicação, devemos seguir com a criação da DDB GLOBAL (storage pool).


**VERSÃO COMMVAULT**

Durante a instalação, a versão dos agents commvault do Media Agent e todos os clients que compõe o ambiente, devem seguir a mesma versão apresentada na commcell console.

**ANTIVIRUS**

É de extrema importância que ao instalarem o Antivírus nos servidores Media Agent, que seja requisitado ao time de Cyber Segurança que o os paths commvault esteja na lista de exclusão do Backup dedicado.

Ex: 
software_installation_path\Commvault (Verificarem a unidade de disco)
software_installation_path\IndexCache (Verificarem a unidade de disco)
disk_libraries_path\CV_MAGNETIC (Verificarem as unidades de disco)
deduplication engines_path\CV_SIDB (Verificarem a unidade de disco)
https://equinixcsm.service-now.com/kb_view.do?sysparm_article=KB0014314

  
**PAGEFILE**

Para os servidores de Media Agent, o page file deverá ser configurado de acordo com as premissas Windows.

https://git.mop.equinix.com.br/br-msh-problem/premissas/-/blob/main/Premissa.Windows.md#page-file 


##Aspectos gerais
---

 **Cadastros**

O cadastro dos servidores robôs de backup e storages necessários a infraestrutura de backup assim como quaisquer outros componentes como circuitos, link pap devem estar devidamente registrados nas ferramentas a seguir:

- **Service Now:** O cadastro deve estar preenchido apropriadamente e com o item de gerenciamento selecionado de forma apropriada.
  - Tag e informações para identificação do servidor devem ser feitas de forma clara e acessível.
- **Zabbix:** Todos os itens relativos ao monitoramento dos servidores devem estar ativos e funcionais. Os templates de hardware devem estar selecionados de forma apropriada ao fabricante, assim como o monitoramento do serviço de backup, o monitoramento das partições deve estar ativo tanto para volumetria quanto para disponibilidade.
  [KB0013793](https://equinixcsm.service-now.com/kb_knowledge.do?sys_id=6f8a7c341b949d10a62b20622a4bcbd8&sysparm_record_target=kb_knowledge&sysparm_record_row=4&sysparm_record_rows=25&sysparm_record_list=workflow_stateINdraft%2Creview%2Cpublished%2Cpending_retirement%2Cretired%2Coutdated^author%3Djavascript%3Ags.getUserID()^ORrevised_by%3Djavascript%3Ags.getUserID()^kb_knowledge_base.u_countryINBR^ORDERBYshort_description) - [COMMVAULT] Implementação do template DISK_COMMVAULT

 **Padronização de Siglas.**

A padronização de siglas deve ser observada para facilitar a identificação das ferramentas e configurações dentro das ferramentas de backup. Para tanto seguem as definições de nomenclaturas:

- **Nome do Cliente (Grupo):** Ao criar o grupo do cliente no Commvault deverá ser o mesmo do Nickname da ACCOUNT do cadastro no Service Now.
- **Nome do Cliente (Servidores):** Ao cadastrar os ativos no Commvault deverá ser o mesmo do Nickname do EQUIPAMENTO no cadastro no Service Now, exemplo: NICKNAMECLIENTE\_NOMEDOSERVIDOR.
- **Storage polices:** Sempre devem iniciar com STP\_NICKNAME
- **Schedules de Backup:** Sempre devem iniciar com SCH\_NICKNAME\_TIPO exemplo: SCH\_EQUINIX\_DIARIOINCR, SCH\_EQUINIX\_MENSALFULL
- **Disk Libraries:** Sempre devem iniciar com DL_NICKNAME_HOSTNAME
- **Etiqueta do Cliente:** Equinix, EQUINIX-BKP01-SP1, sempre em maiúsculo.
- **Nome dos Ativos:** BKP01, EQUINIX\_BKP01-SP1.
- **Evitar:** Devem ser evitados nomes como New, novo, temp e afins pois após algum tempo é impossível saber qual host é o novo ou velho.


 **Segurança Física.**

Medidas para controle físico aos dispositivos de backup devem ser considerados como fundamentais para o adequado funcionamento das soluções.

- **Chaveamento:** Todos os dispositivos relativos a backup devem estar chaveados e devidamente trancados em suas devidas gavetas cofres.
- **Robôs de backup:** O robô de backup não deve possuir ip de gerência valido ou filtrado através do firewall afim de evitar qualquer acesso externo, sua senha de administração deve estar devidamente documentada e possuir complexidade equivalente porem sem caracteres especiais.
- **Fitas LTO/DISCOS:** Não devem ser misturados fitas e discos externos ao backup no rack ou mesmo na gaveta cofre do cliente, cada projeto de backup deve estar completamente identificado.
- **Botões de energia:** O botão &quot;power&quot; deve estar configurado de modo desabilitado e quando esse não for possível deve estar configurado para somente ser ativo após 3 segundos pressionados isso garante que acionamentos acidentais não irão ocorrer

## Observações gerais

Backup dedicado é uma das soluções que mais exige detalhamento pois normalmente trata com o que de mais sensível tange as corporações digitais a perda de informações.

Mesmo a perda de parte das informações hoje já é passível de quebra de contratos e multas, dada a natureza dos clientes de backup dedicado, somos alvos constantes de auditorias internas e externas, portanto padronização e qualidade são fundamentais.

Esse documento tem o intuito de ser um guia fácil para todos que desejam colocar um Media Agente para operar ou mesmo configurar de forma rápida políticas de backup. Serão ainda publicados documentos específicos de cada tecnologia, que terão o intuito de cobrir e estender a gama de soluções possíveis.

Dúvidas sobre projetos e ativação podem ser enviadas para o e-mail:
ems-operations-infrastructure-amer@equinix.com


#### Revisão


| Revisor Responsável | Validador Responsável | Atualizado |
| ------------------- | --------------------- | ---------- |
| Rafael Santana      |                       | 01/07/2020 |
| Guilherme Minotto   |                       | 18/11/2022 |
| Guilherme Minotto   |                       | 30/10/2023 |
| Guilherme Minotto   |                       | 09/09/2025 |
  
  
![visitors](https://visitor-badge.glitch.me/badge?page_id=Premissa.Backup.Dedicado.md&left_color=gray&right_color=red)  


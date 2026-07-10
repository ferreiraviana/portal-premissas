![alt text](img/logo_eqx.jpg "Logo EQX")

# Premissa de VMware
---

# Sumário
- [Objetivo](#objetivo)
- [Documentação](#documentação)
- [Service Now](#service-now)
- [Runbook](#runbook)
- [ETI](#eti)
- [Dados do equipamento](#dados-do-equipamento)
- [Credencial](#credencial)
- [Recomendações de instalação](#recomendações-de-instalação)
- [Operações Suportadas](#operações-suportadas)
- [Pré-requisitos Instalação](#pré-requisitos-intalação)
- [Configurações de Hardware](#configurações-de-hardware)
- [Servidores](#servidores)
- [BIOS](#bios)
- [Processadores](#processadores)
- [Memória](#memória)
- [Media Custom ISO](#media-custom-iso)
- [Discos](#discos)
- [Conexões](#conexões)
- [Redes](#redes)
- [Storage](#storage)
- [Cenários possíveis](#cenários-possíveis)
- [Firmwares e drivers](#firmwares-e-drivers)
- [Instalação e Configuração dos Hosts ESXi](#instalação-e-configuração-dos-hosts-esxi)
- [Hardening](#hardening)
- [Cluster VMware](#cluster-vmware)
- [Virtual Machines](#virtual-machines)
- [vSphere vCenter](#vsphere-vcenter)
- [Configurações do vCenter](#configurações-do-vcenter)
- [Configurações de acesso do cliente](#configurações-de-acesso-do-cliente)
- [vSphere Replication](#vsphere-replication)
- [Configurações do vSphere Replication](#configurações-do-vsphere-replication)
- [Site Recovery Manager](#site-recovery-manager)
- [Configurações do VMware Site Recovery Manager](#configurações-do-vmware-site-recovery-manager)
- [VMware Performance Counters](#vmware-performance-counters)
- [Monitoramento](#monitoramento)
- [Revisão](#revisão)
---

### Objetivo
---

Apresentar a forma mais assertiva de implantação de infraestrutura de ambiente VMware para clientes gerenciados, seguindo as melhores práticas de mercado, atendendo os pré-requisitos dos fornecedores e fabricantes, assim como requisitos de segurança estabelecidos pela Equinix.

- Todos os documentos técnicos devem ser armazenados no ServiceNow.
Com isso, conseguiremos atender os pilares de segurança de informação/proteção de dados (Confidencialidade, Integridade e Disponibilidade).

- As configurações dos equipamentos são realizadas seguindo as recomendações e particularidades de implementação de acordo com o fabricante,
ambiente do cliente e as melhores práticas propostas pela Equinix

### Documentação
---
A ferramenta padrão para documentação dos ativos dos clientes é o ServiceNow, portanto todas as informações sobre o cliente devem estar contidas nesse sistema.

### Service Now
---

- #### Runbook

O objetivo desse documento é ser um "snapshot" de configurações do equipamento do cliente. Eventualmente utilizado em caso de RD do equipamento.

- #### ETI

O objetivo desse documento é conter as informações sobre o que foi acordado com o cliente referente à capacity e serviços disponibilizados no equipamento.

- #### Dados do equipamento

Em dados do equipamento devem conter informações como: informações de acesso lógico, localização física, configuração física, etc.

- #### Credencial

Estão dispostas em um módulo de segurança dentro do ServiceNow. Através desse módulo devemos ter acesso à credencial atual do equipamento. Apenas os equipamentos com gerenciamento TM ou OSA devem possuir esse item configurado.

### Recomendações de instalação
---
Versões de Host ESXi, vCenters e Soluções Adjacentes Suportadas

Sempre utilizar versões com End of General Support superior a um ano a data de instalação conforme o VMware Lifecycle Product Matrix (https://lifecycle.vmware.com/#/).

Atualmente apenas são suportadas as versões acima da 7.0.

#### Operações Suportadas
---
[Flexível]

- Stand-alone - Apenas um servidor com a função de Hypervisor. Recomendamos haver o serviço de vCenter, seja ele compartilhado ou dedicado.
- Cluster em HA - Para ambientes em Cluster, é obrigatório o serviço de vCenter, instalado em vCenter Virtual Appliance em versão igual ou superior aos Hosts;
- Serviço de replicação de VMs – Utilizando a vSphere Replication;
- Serviço de replicação de VMs com Automação do Plano de Disater Recovery – Utilizando as ferramentas Site Recovery Manager + vSphere Replication;

#### Pré-requisitos Instalação
---
[Obrigatório]
- Todo servidor deverá ser totalmente atualizado, utilizando o software do fabricante (Dell SUU ou HPe SPP), antes da instalação do VMware ESXi.
- Utilizar a mais recente versão a ser instalada tendo como balizador de compatibilidade/interoperabilidade com outros produtos Non-Vmware (Backup, Monitoração, etc) e VMware. 
- Para produtos VMware usar a ferramenta da VMware Product Interoperability Matrices (https://interopmatrix.vmware.com/Interoperability) 
- Updates e Service Packs: Aplicar todos updates e Service Packs pertinentes, tomando o devido cuidado com a mudança de Build/Versão dos produtos seguindo o balizador de compatibilidade/interoperabilidade. 

### Configurações de Hardware
---

#### Servidores

- VMware Compatibility Guide: Efetuar a validação de compatibilidade de acordo com a versão e o hardware/dispositivos  no site da VMware Compatibility Guide” ( https://www.vmware.com/resources/compatibility/search.php ); [Obrigatório]
- Hardware: Garantir que todo periférico (HBA, Network Adapters, Disk, etc) são compatíveis com o hardware conforme Matrix do fabricante (HPE QuickSpecs e Dell Technical Guide) do mesmo. [Obrigatório]

#### BIOS
[Obrigatório]

- De acordo com os modelos de servidores, habilitar ou desabilitar as seguintes features: 
  - Server Availability > ASR Status > Disabled (somente para servidores HPE) 
  - Power Management Options > Power Profile > Maximum Performance  
  - Power Management Options > Power Regulator > Static High Performance 
  - Power Management Options > Minimum Processor Idle Power Core State > No C-States 
  - Power Management Options > Minimum Processor Idle Power Package State > No Package State
  - Intel Virtualization Technology (Intel VT) > Enabled 
  - Intel® Virtualization Technology for Directed I/O (Intel VT-d) > Enabled 
  - Hardware-Enforced Data Execution Prevention (DEP) > Enabled 
  - Bit Intel XD (Execute Disable Bit) > Enabled

#### Processadores
[Obrigatório]

- **Processador:** Utilizar somente processadores de 64-bit, dual processado, no mínimo, OctaCore com HT, com second-level address translation (SLAT)

#### Memória
[Obrigatório]

- **Memória:** Deve ser calculada toda a memória necessária para as VMs do projeto e adicionar ao menos 8GB de RAM para cada NODE no cluster.

Para o calculo de reserva de recurso, deve seguir a tabela abaixo:

| Quantidade de nodes| Reserva de Recursos|
|---------|------------|
| 2 | 50%|
| 3 | 33%|
| 4 | 25%|
| 5 | 20%|
| 6 | 18%|
| 7 | 15%|
| 8 | 13%|
| 9 | 11%|
| 10 | 10%|

Ex1: Caso o projeto tenha 10 VMs com 10GB de ram e 2 nodes, deve ser calculado:
10 VMs x 10 GB = 100GB 
100GB + 16GB (8GB por node) = 116GB no cluster
116GB / 2 nodes = 58GB por node
Seguindo a tabela de referencia para 2 nodes, devemos ter 50% do recurso do node reservado. Cada node deverá possuir 116GB de RAM.

Ex2: Caso o projeto tenha 30 VMs com 8GB de ram e 4 nodes, deve ser calculado:
30 VMs x 8 GB = 240GB 
240GB + 32GB (8GB por node) = 272GB no cluster
272GB / 4 nodes = 68 GB por node
Seguindo a tabela de referencia para 4 nodes, devemos ter 25% do recurso do node reservado. Cada node deverá possuir 91GB de RAM.


### Media Custom ISO
---

- **ESXi** - A HPE, Dell entre outros fornecem mídias customizadas com seus agentes embutidos, sempre a instalação deve seguir com a ISO do fabricante, segue exemplo; 

![alt text](/img/img.vmware/2vm.png "ESXi - A HPE")

#### Discos

- **RAID SO:** Utilizar sempre RAID-1 para o SO com pares de discos SSD ou SAS. Não utilizar esse Array para armazenar VMs; [Obrigatório]
  - **Volumetria:** Embora sejam necessários apenas 32 GB de disco para instalação, recomenda-se utilizar no mínimo dois discos de capacidade de 300 GB; [Flexível]
- **RAID Datastore Local:** O segundo Array (discos locais) quando necessário deve ser composto por discos SSD ou SAS com mínimo de tolerância RAID-5 e suas derivações por hardware. [Flexível]
  - **Volumetria:** Sempre considerar espaço livre de no mínimo 20% nos discos locais do host, para evitar alarmes recorrentes de espaço em disco. [Flexível]
- **Datastore SAN (FC ou iSCSI):** Quando o Cliente optar por usar Storage Dedicado ou Compartilhado, garantir que a apresentação das LUNs estejam para todos os hosts do mesmo Cluster [Flexível]

### Conexões
---

#### Redes
[Flexível]

- Possuir no mínimo 4 interfaces de rede 1GB ou 2 interfaces 10GB (8 interfaces de rede gbit ou 4 placas 10gbit ou mais em caso de segmentação de rede por VLAN e redundância) (DSS-PCI e outros requisitos do cliente).;

- Todos os hosts devem ser configurados preferencialmente usando Standard-Switch
  - vSwtich0 – Utilizar com os VMKernels Administrativos (Management, vMotion, Replication, etc), preferencialmente segmentado por VLANs e sem LACP. Seguir recomendações do item “Cenários Possíveis”;
  - vSwtich1 – Utilizar para tráfego PortGroups de VMs preferencialmente segmentado por VLANs e sem LACP. Seguir recomendações do item “Cenários Possíveis”;
  - vSwtich2 ou mais – Se necessário utilizar para trafego PortGroups de VMs(Necessidade de segmentação física do Cliente DMZ, etc).

- Manter Speed/Duplex de todas as interfaces físicas (vmnic) como Negotiate.

- Criar os PortGroups com nomenclatura que referencie o “VLAN ID” e deve ter o mesmo nome em todos os hosts do cluster e da réplica, se houver: 
  - Ex.: REDE_PROD_VLAN_51 (case sensitive, sempre usar maiúsculo)

- **Backup:**
  - Em ambientes 1Gb: Fazer a segmentação lógica por Traffic Shaping (Average/Peak Bandwidth) de 70% no vSwitch (Conforme item: Cenários possíveis)
  - Em ambientes 10Gb: Fazer a segmentação lógica por Traffic Shaping (Average/Peak Bandwidth) 10% no vSwitch (Conforme item: Cenários possíveis)

- **ILO / iDRAC:** 
[Obrigatório]
 
  - Dedicada
  - Compartilhada: utilizar sempre as VMNICs do vSwitch1 (Administrativo) é obrigatório o projeto contemplar o acesso remoto (Portas no switch, IPs, etc). iLO / iDRAC devem estar configuradas e funcionando, bem como o login e senha devem estar cadastrados no ServiceNow. É possível também utilizar Shared ILO/IDRAC com a placa dedicada de Backup. (Conforme item: Cenários possíveis).

#### Storage 
[Flexível]

- **Conexões FC:** Prover canais físicos dedicados para FC. Definir a política de multipath de acordo com o suportado pelo fabricante do Storage (abaixo seguem 2 exemplos, não se limitando a eles). A preferência será sempre por Round Robin. Em ambientes de cluster, sempre ter HBAs do mesmo modelo em todos os hosts.
  - IBM Compartilhado - IBM SAN Solution Design Best Practices for VMware vSphere ESXi - http://www.redbooks.ibm.com/abstracts/sg248158.html?Open
  - Compellent - Best Practices for Implementing VMware vSphere in a Dell PS Series Storage Environment - https://downloads.dell.com/solutions/storage-solution-resources/BestPracticesWithPSseries-VMware%28TR1091%29.pdf 

- **Conexões ISCSI:** Em caso de conexão com Storage ISCSI, definir a política de multipath de acordo com o suportado pelo fabricante do Storage (abaixo seguem 2 exemplos não se limitando a eles), devem ser seguidas as seguintes regras:
  - 10Gb – utilizando Traffic Shapping para dividir tráfego com gerenciamento, produção e backup;
  - 1Gb Dedicado – 2 portas em placas diferentes para redundância;

#### Cenários possíveis
[Flexível]

  **Placas 1Gb**

  - **Placa 1Gb Quad c/ shared ILO/IDRAC – Storage FC**
  - **Redes:** Nesse cenário, devem ser configurados 2 vSwitches, de 2 VMNICs cada:
  - **vSwitch0 para gerenciamento:** para gerenciamento, criar os VMKernel Port dedicados para as redes Management, vMotion e Backup (Traffic Shapping Average/Peak Bandwidth de 70%). Também será configurada a Shared ILO/IDRAC em uma das portas físicas;
  - **vSwitch1 para produção:** para produção (VMs), criar os devidos PortGroups segmentados por vLAN com as redes de VMs do Cliente. 
  - **Storage:** Nesse cenário considera-se o uso de SAN FC, com conexão via HBA e Multipath configurado adequadamente.

![alt text](/img/img.vmware/3vm.png "")

Obs: a Shared ILO/iDRAC não necessita de configuração dentro do VMware, somente passar a VLAN no Trunk das portas

- **Configuração do QoS da Placa 1Gb Quad c/ shared ILO/IDRAC – Storage FC**
  - Criar vSwitch0 e adicionar as placas 1Gb VMNIC0 e VMNIC1 disponíveis.
  - Criar os VMKernels (Management, vMotion, Backup e Replication)
  - Somente no VMkernel de Backup definir em Traffic Shapping 1400000 Kbits/sec para Average e Peak Bandwidth. Para o Burst Size o valor deverá ser de 2048000 Kbits/sec.

**2 Placas 1Gb Quad c/ shared ILO/IDRAC – Storage FC**
 - **Redes:** Em casos de 2 placas quad, devem ser configurados:
- **vSwitch0 para gerenciamento:** para gerenciamento, criar os VMKernel Port dedicados para as redes Management, vMotion e Backup (Traffic Shapping Peak Bandwidth de 70%). Também será configurada a Shared ILO/IDRAC em uma das portas físicas;
- **vSwitch1 para produção:** para produção (VMs), criar os devidos PortGroups segmentados por vLAN com as redes de VMs do Cliente.
- **Storage:** Nesse cenário considera-se o uso de SAN, com conexão via HBA e Multipath configurado adequadamente.

![alt text](/img/img.vmware/4vm.png "")

Obs: a Shared ILO/iDRAC não necessita de configuração dentro do VMware, somente passar a VLAN no Trunk  das portas

- **Configuração do QoS da 2 Placas 1Gb Quad c/ shared ILO/IDRAC – Storage FC**
- Criar vSwitch0 e adicionar as placas 1Gb VMNIC0 e VMNIC4 disponíveis.
- Criar os VMKernels (Management, vMotion, Backup e Replication)
- Somente no VMkernel de Backup definir em Traffic Shapping 1400000 Kbits/sec para Average e Peak Bandwidth. Para o Burst Size o valor deverá ser de 2048000 Kbits/sec.

**2 Placas 1Gb Quad c/ shared ILO/IDRAC – Storage ISCSI**
- **Redes:** Em casos de 2 placas quad e storage ISCSI, devem ser configurados:
- **vSwitch0 para gerenciamento:** para gerenciamento, criar os VMKernel Port dedicados para as redes Management, vMotion e Backup (Traffic Shapping Peak Bandwidth de 70%). Também será configurada a Shared ILO/IDRAC em uma das portas físicas;
- **VSwitch1 para ISCSI:** É necessário dedicar 1 porta de cada placa para conexão com a rede iSCSI do storage Fabric A. Essa conexão usará um VMKernel Layer 2 deve estar habilitada com MTU 9000;
- **VSwitch2 para ISCSI:** É necessário dedicar 1 porta de cada placa para conexão com a rede iSCSI do storage Fabric A. Essa conexão usará um VMKernel Layer 2 deve estar habilitada com MTU 9000;
- **vSwitch3 para produção:** team com 4 VMNICs de cada placa para produção (VMs), criar um Virtual Machine Port Group para utilização das redes do cliente. 

![alt text](/img/img.vmware/5vm.png "")

Obs: a Shared ILO/iDRAC não necessita de configuração dentro do VMware, somente passar a VLAN no Trunk das portas

- **Configuração do QoS da 2 Placas 1Gb Quad c/ shared ILO/IDRAC – Storage ISCSI**
- Criar vSwitch0 e adicionar as placas 1Gb VMNIC0 e VMNIC4 disponíveis.
- Criar os VMKernels (Management, vMotion, Backup, iSCSI e Replication)
- Somente no VMkernel de Backup definir em Traffic Shapping 1400000 Kbits/sec para Average e Peak Bandwidth. Para o Burst Size o valor deverá ser de 2048000 Kbits/sec.

**Placas 10Gb**
- 1 Placa 10Gb Dual com SAN FC – ILO/IDRAC Compartilhada
- vSwitch0 - Em cenários 10Gb DualPort, todas portas (VMKernels e PortGroups) devem estar configuradas em um vSwitch sem LACP. Um vSwitch e adaptadores de rede virtuais devem ser criados para divisão lógica de funcionalidade como tráfego de gerenciamento, vMotion, Backup e Produção. Importante configurar o Traffic Shapping Peak Bandwidth em cada VMKernel conforme exemplo abaixo; 

![alt text](/img/img.vmware/6vm.png "")

Obs: a Shared ILO/iDRAC não necessita de configuração dentro do VMware, somente passar a VLAN no Trunk  das portas

- **Configuração do QoS da 1 Placa 10Gb Dual com SAN FC**
- Criar vSwitch0 e adicionar as placas 1Gb VMNIC0 e VMNIC1 disponíveis.
- Criar os VMKernels (Management, vMotion, Backup e Replication)
- Para o VMkernel de Management definir em Traffic Shapping 1000000 Kbits/sec para Average e Peak Bandwidth. Para o Burst Size o valor deverá ser de 2048000 Kbits/sec.
- Para o VMkernel de vMotion definir em Traffic Shapping 2000000 Kbits/sec para Average e Peak Bandwidth. Para o Burst Size o valor deverá ser de 2048000 Kbits/sec.
- Para o VMkernel de Backup definir em Traffic Shapping 2000000 Kbits/sec para Average e Peak Bandwidth. Para o Burst Size o valor deverá ser de 2048000 Kbits/sec.
- Para o VMkernel de Replication definir em Traffic Shapping 1000000 Kbits/sec para Average e Peak Bandwidth. Para o Burst Size o valor deverá ser de 2048000 Kbits/sec.

**1 Placa 10Gb Dual com SAN iSCSI – ILO/IDRAC Compartilhada**
- **vSwitch0** - Em cenários 10Gb DualPort, todas portas (VMKernels e PortGroups) devem estar configuradas no vSwitch sem LACP. Um vSwitch e adaptadores de rede virtuais devem ser criados para divisão lógica de funcionalidade como tráfego de gerenciamento, vMotion, Backup, iSCSI e Produção. Importante configurar o Traffic Shapping Peak Bandwidth em cada VMKernel conforme exemplo abaixo; 

![alt text](/img/img.vmware/7vm.png "")

Obs: a Shared ILO/iDRAC não necessita de configuração dentro do VMware, somente passar a VLAN no Trunk  das portas

- **Configuração do QoS da 1 Placa 10Gb Dual com SAN iSCSI**
- Criar vSwitch0 e adicionar as placas 1Gb VMNIC0 e VMNIC1 disponíveis.
- Criar os VMKernels (Management, vMotion, Backup, iSCSI e Replication)
- Para o VMkernel de Management definir em Traffic Shapping 1000000 Kbits/sec para Average e Peak Bandwidth. Para o Burst Size o valor deverá ser de 2048000 Kbits/sec.
- Para o VMkernel de vMotion definir em Traffic Shapping 2000000 Kbits/sec para Average e Peak Bandwidth. Para o Burst Size o valor deverá ser de 2048000 Kbits/sec.
- Para o VMkernel de Backup definir em Traffic Shapping 2000000 Kbits/sec para Average e Peak Bandwidth. Para o Burst Size o valor deverá ser de 2048000 Kbits/sec.
- Para o VMkernel de Replication definir em Traffic Shapping 1000000 Kbits/sec para Average e Peak Bandwidth. Para o Burst Size o valor deverá ser de 2048000 Kbits/sec.
- Para o VMkernel de iSCSI Fabric A definir em Traffic Shapping 2000000 Kbits/sec para Average e Peak Bandwidth. Para o Burst Size o valor deverá ser de 2048000 Kbits/sec.
- Para o VMkernel de iSCSI Fabric B definir em Traffic Shapping 2000000 Kbits/sec para Average e Peak Bandwidth. Para o Burst Size o valor deverá ser de 2048000 Kbits/sec.

**2 Placas 10Gb Dual c/ SAN FC – ILO/IDRAC Compartilhada**
- vSwitch0 - Em cenários com 2 interfaces 10Gb quadport, todas portas (VMKernels e PortGroups) devem estar configuradas em um vSwitch sem LACP Um vSwitch e adaptadores de rede virtuais devem ser criados para divisão lógica de funcionalidade como tráfego de gerenciamento, vMotion, Backup e Produção. Importante configurar o Traffic Shapping Peak Bandwidth em cada VMKernel conforme exemplo abaixo;

![alt text](/img/img.vmware/8vm.png "")

Obs: a Shared ILO/iDRAC não necessita de configuração dentro do VMware, somente passar a VLAN no Trunk das portas

- **Configuração do QoS da 2 Placas 10Gb Dual c/ SAN FC**
- Criar vSwitch0 e adicionar as placas 1Gb VMNIC0, VMNIC1, VMNIC2 e VMNIC3 disponíveis.
- Criar os VMKernels (Management, vMotion, Backup e Replication)
- Para o VMkernel de Management definir em Traffic Shapping 2000000 Kbits/sec para Average e Peak Bandwidth. Para o Burst Size o valor deverá ser de 2048000 Kbits/sec.
- Para o VMkernel de vMotion definir em Traffic Shapping 4000000 Kbits/sec para Average e Peak Bandwidth. Para o Burst Size o valor deverá ser de 2048000 Kbits/sec.
- Para o VMkernel de Backup definir em Traffic Shapping 4000000 Kbits/sec para Average e Peak Bandwidth. Para o Burst Size o valor deverá ser de 2048000 Kbits/sec.
- Para o VMkernel de Replication definir em Traffic Shapping 2000000 Kbits/sec para Average e Peak Bandwidth. Para o Burst Size o valor deverá ser de 2048000 Kbits/sec.

**2 Placas 10Gb Dual c/ SAN iSCSI – ILO/IDRAC Compartilhada**
- vSwitch0 - Em cenários com 2 interfaces 10Gb quadport, todas portas (VMKernels e PortGroups) devem estar configuradas em um vSwitch sem LACP. Um vSwitch e adaptadores de rede virtuais devem ser criados para divisão lógica de funcionalidade como tráfego de gerenciamento, vMotion, Backup, iSCSI e Produção. Importante configurar o Traffic Shapping Peak Bandwidth em cada VMKernel conforme exemplo abaixo;

![alt text](/img/img.vmware/9vm.png "")

Obs: a Shared ILO/iDRAC não necessita de configuração dentro do VMware, somente passar a VLAN no Trunk  das portas

- **Configuração do QoS da 2 Placas 10Gb Dual c/ SAN iSCSI **
- Criar vSwitch0 e adicionar as placas 1Gb VMNIC0, VMNIC1, VMNIC2 e VMNIC3 disponíveis.
- Criar os VMKernels (Management, vMotion, Backup, iSCSI e Replication)
- Para o VMkernel de Management definir em Traffic Shapping 2000000 Kbits/sec para Average e Peak Bandwidth. Para o Burst Size o valor deverá ser de 2048000 Kbits/sec.
- Para o VMkernel de vMotion definir em Traffic Shapping 4000000 Kbits/sec para Average e Peak Bandwidth. Para o Burst Size o valor deverá ser de 2048000 Kbits/sec.
- Para o VMkernel de Backup definir em Traffic Shapping 4000000 Kbits/sec para Average e Peak Bandwidth. Para o Burst Size o valor deverá ser de 2048000 Kbits/sec.
- Para o VMkernel de Replication definir em Traffic Shapping 2000000 Kbits/sec para Average e Peak Bandwidth. Para o Burst Size o valor deverá ser de 2048000 Kbits/sec.
- Para o VMkernel de iSCSI Fabric A definir em Traffic Shapping 2000000 Kbits/sec para Average e Peak Bandwidth. Para o Burst Size o valor deverá ser de 2048000 Kbits/sec.
- Para o VMkernel de iSCSI Fabric B definir em Traffic Shapping 2000000 Kbits/sec para Average e Peak Bandwidth. Para o Burst Size o valor deverá ser de 2048000 Kbits/sec.
  - Exemplo de configuração 
- VMKernel de Management – No exemplo abaixo vamos configurar o Traffic Shapping Average/Peak Bandwidth com 5% dos 40GB disponível no vSwitch0, ou seja, 2GB, dessa forma precisamos converter Gbit/s para kbit/s;

![alt text](/img/img.vmware/10vm.png "")

Caculadora do site http://www.kylesconverter.com/data-bandwidth/gigabits-per-second-to-kilobits-per-second

![alt text](/img/img.vmware/13vm.png "")
![alt text](/img/img.vmware/11vm.png "")

#### Firmwares e drivers
[Obrigatório]

- Utilizar os softwares dos fabricantes para atualizar os firmwares do servidor para a última versão disponibilizada:
  - Dell: Server Update Utility (SUU)
  - HP: Service Pack for Proliant (SPP)
- A instalação deve ser realizada com a versão mais recente da mídia de instalação fornecida pelo fabricante do hardware. Nesta mídia está contido os pacotes com os drivers mais novos suportados.

### Instalação e Configuração dos Hosts ESXi
---

#### Hardening
[Obrigatório]

- Armazenar todas as credenciais, em especial a de root e administrator@vsphere.local no ServiceNow.
- Política de Senhas: adequar o padrão atual fornecido pela equipe de segurança da informação para tamanho, complexidade e expiração.
- Updates e Service Packs: Instalar o VMware Update Manager e aplicar os updates e service packs mais recentes para a versão do ESXi e vCenter, inclusive os opcionais;
- Definir o tempo de timeout das sessões SSH em 900 segundos. Opções avançadas do host em ESXi.set-shell-interactive-timeout;
- Manter desabilitado os serviços ESXi Shell interface e SSH.
- Liberação da porta ESXi https://kb.vmware.com/s/article/2039095 

#### Cluster VMware
[Flexível]

1.	Configurar os seguintes parâmetros quando houver um Cluster VMware. 

- **Datastore:** 
  - Configurar os discos de storage apresentados ao cluster e renomear para o padrão VMDATAXX_LUNX_TIERX (Ex: VMDATA_LUN02_TIER2)
  - A formatação dos discos como Datastores deve ser realizada na versão mais recente disponível do VMFS.
  - Para garantir alta disponibilidade nos ambientes em cluster, avaliar a possibilidade de efetuar segmentação de LUNs. Avaliar com cuidado, pois essa ação reduz a quantidade de IOPS. Exemplo: Volume de 12TB com 3 hosts ESXi > Segmentar em 3 LUNS de 4TB.
  - Aplicar melhores práticas particulares de cada fabricante de Storage. Não se limitando aos abaixo;
    - **IBM Compartilhado** - IBM SAN Solution Design Best Practices for VMware vSphere ESXi - http://www.redbooks.ibm.com/abstracts/sg248158.html?Open
    - **Compellent**  - Best Practices for Implementing VMware vSphere in a Dell PS Series Storage Environment - https://downloads.dell.com/solutions/storage-solution-resources/BestPracticesWithPSseries-VMware%28TR1091%29.pdf 
- **Volumetria LUNs:** Sempre considerar espaço livre de no mínimo 20%, para evitar alarmes recorrentes de espaço em disco, entretanto em caráter de flexibilização considerar a tabela abaixo;
  - Espaço livre Flexibilizado:
    - > 11 TB – mínimo 5% livres;
    - >1 TB < 10 TB – mínimo 10% livres;
    - >1 TB - mínimo 15% livres.
- **Alocação das Máquinas Virtuais:** Alocar as VMs sempre no datastore do storage dedicado/compartilhado quando em cluster.
- **Monitoramento de Datastore:** incluso no template
    
TEMPLATE.TM_VMWARE_ESXi_6.7_7.0_CIM


- **Distributed Resource Scheduler**

Configurar o Automation Level como Fully automated e a escala de Migration Threshold no meio da barra;

![alt text](/img/img.vmware/12vm.png "")

- **High Availability**

Em ambientes de cluster, sempre considerar a reserva de processamento e memória de 1 Host, para garantir que a carga de recursos seja suportada pelos nós restantes caso ocorra indisponibilidade de um dos hosts.
  - Heartbeat Datastores – selecionar sempre á opção “Automatically select datastores accessible from the hosts”;

![alt text](/img/img.vmware/14vm.png "")

### Virtual Machines
---

- **Alocação das VMs: Standalone** [Obrigatório]
Utilizar o Datastore criado em disco local (Não S.O.) somente em hosts stand alone. Sempre considerar espaço livre de no mínimo 20%, para evitar alarmes recorrentes de espaço em disco. Nesse caso não é possível utilizar as funcionalidades de DRS e HA; 

- **Cluster** [Obrigatório]
VMs em cluster devem ser armazenadas em Datastore compartilhado/Dedicado para garantir o correto funcionamento do HA/DRS.

- **Guest OS support:** Recomenda-se utilizar a versão mais nova do hardware virtual VMs para os seguintes Sistemas Operacionais, seguindo a suportabilidade do fabricante, concatenado com nossas premissas:
[Flexível]
- **Windows Server 2022**
- **Windows Server 2019**
- **Windows Server 2016**
- **RHEL/CentOS/Oracle Linux 7 ou superior;** 
- **Debian 8 ou superior;**
- **SLES 12 ou superior;**
- **Ubuntu 14.04 ou superior.**

- **SCSI Controller:** Utilizar o padrão LSI Logic SAS 

- **Tipo de Disco:** Utilizar discos com tamanho fixo configurado como “Thick Provisioning Lazy Zeroed” para aplicações normais e Thick Provision Eager Zeroed para banco de dados . Discos configurados como Thin Provisioning não são suportados pela premissa de gerenciamento Equinix.

- CPU Hot Add - Manter Disable - Há impacto na perfoamance da VM quando desabilitamos, link sobre o assunto https://blogs.vmware.com/performance/2019/12/cpu-hot-add-performance-vsphere67.html

**VMware Tools:**

**Guests Windows:** Instalar o VMware Tools em sua última versão disponível pela console do vCenter. [Obrigatório]
**Guests Linux:** Verificar se o Sistema Operacional Guest já vem com a versão do open-vm-tools mais atual. Caso não possua, instalar a versão mais nova disponível pela console do vCenter. [Obrigatório]
- Networking: Utilizar sempre a interface VMXNET3. Quando este modelo não for suportado, utilizar E1000 ou E1000E de acordo com a disponibilidade e compatibilidade de drivers do GuestOs. [Obrigatório]

- Updates e Service Packs: Preferencialmente aplicar todos updates e Service Packs pertinentes no sistema operacional da VM.

### vSphere vCenter
---

#### Configurações do vCenter  
[Flexível]

**Instalação:** A forma trivial de instalação do vCenter é através de uma imagem OVA fornecida pela VMware. Esta imagem possui o vCenter Appliance completo com todos os componentes necessários para seu funcionamento. 

**Dimensionamento do Appliance do vCenter:**
Seguir conforme documentação (https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vcenter.install.doc/GUID-88571D8A-46E1-464D-A349-4DC43DCAF320.html) 

Liberação no Firewall - https://kb.vmware.com/s/article/52963

**Recursos de Infraestrutura Requeridos:**

- Endereço IP fixo na mesma rede dos hosts ESXi.
- DNS – vCenter e hosts ESXi devidamente registrados. (A e PTR)
- NTP – vCenter e hosts configurados para obter atualizações dos servidores NTP Equinix.
- **Servidores NTP da Equinix -** extntp.equinix.com.br, 1.extntp.equinix.com.br, 2.extntp.equinix.com.br e 3.extntp.equinix.com.br

#### Configurações de acesso do cliente
[Flexível]

**Usuários e Grupos:** quando os Hosts ESXi forem TM, mas as VMs forem TSD ou não tiverem gerenciamento, deverá ser concedida a permissão usando os seguintes parâmetros:  

- Deverá ser criado um grupo chamado CLIENTE no domínio vsphere.local
- Deverão ser criados os usuários a pedido do cliente e inclusos no grupo acima;
- Clonar a role ReadOnly para uma nova role chamada ACESSO_CLIENTE no vSphere e incluir as seguintes permissões: 
  - Virtual Machine\Interaction\Power Off
  - Virtual Machine\Interaction\Power On
  - Virtual Machine\Interaction\Power Off
  - Virtual Machine\Interaction\Console Interaction
  - Virtual Machine\Interaction\Configure Media
  - Virtual Machine\Interaction\VMware Tools Install 
- Dar permissão do grupo CLIENTE com a role ACESSO_CLIENTE na raiz do Datacenter do vSphere.

### vSphere Replication
---

#### Configurações do vSphere Replication 
[Flexível]

**Instalação:** Seguir com a forma trivial de instalação do vSphere Replication. Dependendo da quantidade de VMs a replicar, lembre-se de que o vSphere Replication consome CPU e memória dos hosts de origem ESX. Muitas replicações podem consumir muitos recursos nos hosts ESXi de origem. Um dos requisitos mais críticos para a implementação do vSphere Replication é a largura de banda da rede, principalmente se você estiver replicando entre dois locais diferentes. Mas também entre seu ambiente virtual e seu sistema de armazenamento (Storage). 

Ao planejar sua implantação, esteja ciente dos seguintes níveis de tráfego:

- Entre o host que está executando a máquina virtual replicada e o servidor vSphere Replication;
- Entre o servidor vSphere Replication e um host com acesso ao Storage de destino de replicação;
- Entre o host e o armazenamento;
- Entre o Storage e o host durante os snapshots durante as replicações;
- A velocidade do link entre os dois sites é essencial;
- Monitorar o Link afim de analisar se o mesmo é suficiente com a carga real do cliente, mais  informações  na documentação (https://docs.vmware.com/en/vSphere-Replication/8.3/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html);

A velocidade do link entre os sites é muito importante para termos uma replicação das VMs de forma eficiente, **cumprindo assim o RPO (Recovery Point Objective) e RTO (Recovery Time Objective) acordado em fase de Vendas/ETI.**

**Dimensionamento do Appliance do vSphere Replication:**

Seguir conforme documentação (https://docs.vmware.com/en/vSphere-Replication/8.6/com.vmware.vsphere.replication-admin.doc/GUID-6F4E1F93-C901-4D51-835F-43D93E5D154B.html#:~:text=vSphere%20Replication%20is%20distributed%20as,require%201%20GB%20of%20RAM.)

**Recursos de Infraestrutura Requeridos:**

- Endereço IP fixo na mesma rede(preferencialmente) dos hosts ESXi/vCenter/SEM;
- Considerar VLAN estendida para a redes produtivas (de VMs)
- DNS – vCenter e hosts ESXi devidamente registrados. (A e PTR)
- **Servidores NTP da Equinix -** extntp.equinix.com.br, 1.extntp.equinix.com.br, 2.extntp.equinix.com.br e 3.extntp.equinix.com.br
- Liberação da porta vSphere Replication  https://kb.vmware.com/s/article/2087769

### Site Recovery Manager
---

#### Configurações do VMware Site Recovery Manager
[Flexível]

**Instalação:** Seguir com a forma trivial de instalação do VMware Site Recovery Manager. 

Ao planejar sua implantação, esteja ciente dos seguintes itens:

- Integração do VMware Site Recovery Manager, vSphere Replication e vCenter;
- Criação dos Grupos de Proteção e Planos de Recuperação e garantir a associação dos mesmos;
- Datastore dedicado para PlaceHolders;
- Todos os De>Para (Network, Storage, etc..) validados;
- Ter um documento com as definições de Disaster Recovery, contendo as informações abaixo (não se limitando a essas);
  - Quem pode declarar o Disaster Recovery?
  - Quais VMs fazem parte do Disaster Recovery?
  - Tem recurso computacional (CPU, Memória, Storage, etc) no site Disaster Recovery para atender de forma plena do Grupo/Plano de Recuperação?
    - O Failover será parcial (VMs essenciais) ou será completo (todas as VMs)?
- Quais são os RPO (Recovery Point Objective) e RTO (Recovery Time Objective) acordados em fase de Vendas/ETI?
  - Está sendo atendido com a infraestrutura atual contratada, caso negativo sinalizar o Cliente ?
- Simular e Documentar via RunBook um teste de Failover valido;

**Dimensionamento do Appliance do VMware Site Recovery Manager:**

Seguir conforme documentação (https://docs.vmware.com/en/Site-Recovery-Manager/5.5/com.vmware.srm.install_config.doc/GUID-384B628F-35C8-4C96-9B36-ACCEBE6C6792.html)

**Recursos de Infraestrutura Requeridos:**
- Endereço IP fixo na mesma rede(preferencialmente) dos hosts ESXi/vCenter/SEM;
  - Considerar VLAN estendida para a redes produtivas (de VMs)
- DNS – vCenter e hosts ESXi devidamente registrados. (A e PTR)
- **Servidores NTP da Equinix -** extntp.equinix.com.br, 1.extntp.equinix.com.br, 2.extntp.equinix.com.br e 3.extntp.equinix.com.br
- Liberação da porta do Site Recovery Manager - https://docs.vmware.com/en/Site-Recovery-Manager/8.7/com.vmware.srm.install_config.doc/GUID-499D3C83-B8FD-4D4C-AE3D-19F518A13C98.html

### VMware Performance Counters
---
[Obrigatório]

Após migração do Workload, é necessário verificar os seguintes contadores se estão com valores adequados:

- Processor
- CPU Ratio: A proporção de vCPUs x pCPU não deve ultrapassar 6:1, para evitar problemas de performance por overprovisioning de vCPUs em ambientes VMware.
- CPU Ready: o valor do contador para CPU Ready (%RDY) deve ficar abaixo de 5%.
- CPU Usage: O contador para CPU(*)\% Processor Time deve ficar abaixo de 80% em um ambiente Vmware saudável.
  - Memory
- \Memory\Available Mbytes < 85%
- \Memory\Balloned = 0
- \Memory\Swap Used = 0
  - Disk
- Queue Read latency = 0
- Queue Write latency = 0
  - DataStore
- Read Latency < 15ms
- Write Latency < 15ms
  - Network 
- Usage – 20% ou mais livres da banda disponível
- Received Packets dropped = 0
- Transmit packets dropped = 0
- Packet Receive errors =0 
- Packet transmit errors = 0

### Monitoramento
---
[Obrigatório]

Abaixo segue um os Templates do Zabbix que precisam ser atrelados aos itens de configuração com **Total Management**

- **Host VMware ESXi:** Todos hosts ESXi deverão ser configurados com o template: 
TEMPLATE.TM_VMWARE_ESXi_6.7_7.0_CIM

- **VMware vCenter:** o vCenter deve ser configurado com o template: 
TEMPLATE.TM_VCENTER_APPLIANCE_6.7

- **VMware SRM:** o vSphere Site Recovery Manager deve ser configurado com o template: 
TEMPLATE.TM_VMWARE_SRM 



#### Revisão

| Revisor Responsável          | Validador Responsável     | Atualizado |
|------------------------------|---------------------------|------------|
| Anderson Freitas            | Pamella Canova         | 18/06/2020 |
| João Arêa            | Leonardo Abreu         | 01/12/2022 |
| Leonardo Abreu            | Rafael Pereira          | 21/11/2023 |

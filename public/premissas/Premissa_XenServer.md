![alt text](img/logo_eqx.jpg "Logo EQX")

# Premissa de XenServer
---

> **Revisão vigente — 13/07/2026.** Une documentação XenServer a padrões internos de implantação e suporte. Substitui referências 5.x/6.x/7.x conflitantes.

## Premissas vigentes 2026

### Versão e licenciamento

- **[Fabricante]** XenServer 8.4 é a linha produtiva de referência. XenServer 9 é preview nesta revisão e não deve ser usado em produção.
- **[Interno]** Fixar release/build somente após validar lifecycle, HCL, guest support, storage, backup, monitoramento e caminho de upgrade.
- **[Interno]** Licença/entitlement e mecanismo de ativação devem ser validados antes da proposta. Recursos como HA não podem ser prometidos sem edição/licença compatível.

### Arquitetura e capacidade

- **[Fabricante]** Todo servidor, CPU, NIC, HBA, GPU e storage deve constar na Hardware Compatibility List para o release exato.
- **[Interno]** Pool deve ter hosts homogêneos ou CPU masking compatível. N+1 é o mínimo para HA; três hosts são o padrão interno para manter proteção durante manutenção.
- **[Interno]** Dimensionar CPU, RAM, storage e rede pelo workload e pela falha tolerada. Razões fixas de vCPU/core e percentuais universais são apenas gatilhos de investigação.
- **[Fabricante]** HA depende de shared storage/heartbeat íntegros e capacidade para reiniciar VMs. Testar perda de host e fencing antes do aceite.
- **[Interno]** Separar gerenciamento, storage, migração, backup e VMs por VLAN/rede; usar bonds redundantes em switches/NICs distintos.

### Instalação, atualização e segurança

- **[Interno]** Instalar com mídia suportada e firmware/driver homologado pelo OEM/HCL; não aplicar automaticamente o firmware mais novo.
- **[Fabricante]** Aplicar updates pelo XenCenter/mecanismo suportado, com prechecks. Em pools, seguir ordem e requisitos do release; desabilitar HA quando o procedimento oficial exigir.
- **[Interno]** XenCenter compatível, executado em estação/jump host gerenciado. Não expor management interface à Internet nem permitir NAT que prejudique comunicação/diagnóstico.
- **[Interno]** Contas nominativas, RBAC, cofre, MFA no acesso intermediário, SSH restrito e temporário, NTP/DNS e logs remotos.
- **[Interno]** MTU jumbo somente se suportado e testado ponta a ponta. Multipath e política de paths seguem o fabricante do storage; Round Robin não é universal.

### VMs, backup, aceite e suporte

- **[Fabricante]** Guest OS, drivers e XenServer VM Tools devem estar na matriz e suportados; atualizar Tools antes de recursos que dependam deles.
- **[Interno]** Thin/thick, SR e snapshots dependem do backend e SLA. Snapshots são temporários e não substituem backup.
- **[Interno]** Aceite: pool/HA, live migration, perda de uplink/path, backup/restore, patches, monitoramento, capacidade e Runbook testados.
- **[Interno]** Operação monitora host/pool, HA, SR, multipath, NIC/bond, hardware, backup e crescimento; executar health check antes/depois de mudanças.

### Referências oficiais

- [XenServer 8.4 documentation](https://docs.xenserver.com/en-us/xenserver/8/)
- [XenServer 8.4 PDF documentation](https://docs.xenserver.com/en-us/xenserver/8/xenserver-8.4.pdf)
- [XenServer 9 preview documentation](https://docs.xenserver.com/en-us/xenserver/9/)
- [XenServer Hardware Compatibility List](https://hcl.xenserver.com/)

## Conteúdo legado — histórico interno

> Referências a 5.x/6.x/7.x, .NET 3.5, SSH permanente, Round Robin universal e limites fixos não prevalecem sobre esta seção.


# Sumário
-   [Objetivo](#objetivo)
-   [Documentação](#documentação)
-   [ServiceNow](#servicenow)
-   [Runbook](#runbook)
-   [ETI](#eti)
-   [Topologia](#topologia)
-   [Operação Suportada](#operação-suportada)
-   [Ambiente de Cluster (HA)](#ambiente-de-cluster-ha)
-   [Licenciamento](#licenciamento)
-   [Versões](#versões)
-   [Hardening](#hardening)
-   [Configurações](#configurações)
-   [Hardware Mínimo](#hardware-mínimo)
-   [Instalação e Configurações](#instalação-e-configurações)
-   [Hardware](#Hardware)
-   [Software](#software)
-   [Configurações](#configurações)
- [Revisão](#revisão)

-   ---


### Objetivo
---

Esse documento tem como objetivo prover a padronização de armazenamento dos documentos técnicos e processuais dos equipamentos e, realizar uma fusão entre as melhores práticas sugeridas pelo fabricante e as da Equinix.

- Todos os documentos técnicos devem ser armazenados no ServiceNow.
Com isso, conseguiremos atender os pilares de segurança de informação/proteção de dados (Confidencialidade, Integridade e Disponibilidade).

- As configurações dos equipamentos são realizadas seguindo as recomendações e particularidades de implementação de acordo com o fabricante, 
ambiente do cliente e as melhores práticas propostas pela Equinix

Esse documento é dividido em 4 partes, são elas:
- Documentação do Equipamento
- Licenciamento com o Fabricante
- Padronização Equinix
- Hardening do Equipamento

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

### Operação suportada
---

#### Standalone [Flexivel]

Apenas um servidor com a função de Hypervisor. 

O gerenciamento é realizado pelo Xencenter. Que pode ser instalado em um Servidor Windows, Virtual ou Físico.

#### Ambiente de cluster (HA)

Para ambientes em Cluster alta disponibilidade é obrigatório que tenhamos um mínimo de três hosts Xenserver em um pool; 

O XenServer requer uma licença para utilizar o HA, sendo assim, para o perfeito funcionamento da alta disponibilidade é necessário que o ambiente seja licenciado; 

**OBS: A informação acima é para ambientes a partir da versão 7.0, para ambientes 6.2 não é necessário licenciar o Xenserver para utilizar o HA.**

As versões do XenServer 6.2 SP1, 6.2, 6.1, 6.0.2 ou 6.0 devem ser instaladas e totalmente corrigidas;

O XenServer 6.2 não requer uma licença para utilizar o HA;

O XenServer 5.x está em End-Of-Life;

### Licenciamento
---

O XenServer usa o mesmo mecanismo de licenciamento usado por muitos outros produtos da Citrix. O licenciamento do XenServer 7.1 requer o Citrix License Server 11.13.1.2 ou superior. Você pode baixar o servidor de licenças do Citrix Licensing. Depois de comprar uma licença, você receberá uma chave de licença. LIC. Essa chave de licença deve ser instalada em um servidor Windows executando o software Citrix License Server ou um Virtual Appliance. 

Todo o Gerenciamento do ambiente é realizado pelo Xencenter

### Versões
---

Xenserver 6.0 ou superior.

#### Hardening

- Seguir as configurações da Premissa de SO [Windows](https://msh-git.equinix.com.br/br-msh-problem/Premissas/-/blob/master/Premissa.Windows.md) para o servidor License Serve quando este for dedicado e instalado em Windows.

- No momento da instalação do Xenserver, devem também ser criados os usuários eqxadmin e eqxgerencia, pois os mesmos serão usados pela equipe de operações da Equinix para administração.

- Liberar as portas SSH, HTTP, HTTPS, TCP 8892 E 21064 E UDP 5404 E 5405 para acesso através das redes Equinix (SP/RJ). Para servidores Dell e HP liberar as portas de acesso ao aplicativo de gerenciamento.

### Configurações
---

#### Hardware Mínimo

Todo o hardware, incluindo processadores, placas HBA, storage e switches FC (se utilizados), devem estar listados XenServer Hardware Compatibility List: http://hcl.vmd.citrix.com/

- **Processador:** 1x Intel x64 ou AMD x64 QUADCORE;

  Recomendamos a utilização de 2 ou mais processadores no ambiente.

  De acordo com a Documentação da Citrix [link](https://docs.citrix.com/en-us/xenserver/7-0/downloads/installation-guide.pdf) , o número máximo de vCPUs por core é 32. Todavia, não recomendamos ultrapassar 16 vCPUs por Core.

- **Memória:**

  Cenários:

  1)	Ambiente Standalone: a soma da memória de todas as VMs não deve ultrapassar 95% da memória física do Host.

  2)	Ambiente em Cluster:

        a. O valor “OVERCOMMIT” deverá sempre ser 30%:

        b.	O valor “PORCENTAGEM PROTEÇÃO C/ QUEDA 1 HOST” deverá ser sempre acima de 75%.

        c.	O valor “HOSTS REDUNDANCIA” deverá sempre ser no mínimo 1;

        d.	Sempre se basear na linha “COM REDUNDÂNCIA + COM OVERCOMMIT”;

- **Hard Disk - Partição para sistema operacional:**  
2 x 300GB (RAID 1) SAS, SSD ou SATA para o sistema operacional; 

- **Partição para alocar as VMs:**  
Volumes em RAID1, 5, 10, 50, em discos SSD, SAS, SATA ou “Tierizáveis”. Deve ser levado em consideração o tempo de resposta de cada LUN/SR, onde tempos acima de 30 milissegundos é prejudicial ao ambiente.

- **Fonte redundante:**  
Todos os equipamentos devem possuir fontes redundantes e conectadas em circuitos diferentes\distintos.

- **BIOS:**  
Habilitar Virtualization Technology (Intel VT ou AMD-V) e modo de Alta Performance de CPU e memória.

- **Redes:**   
  - Possuir no mínimo 6 interfaces de rede dividido da seguinte forma:
    
  - Criando Bonds, para a rede de gerenciamento, backup e Produção.
    
  - Caso o ambiente possua storage iSCSI, 2 novas placas de rede devem ser adicionadas (preferencialmente de 10Gbps) ao ambiente em uma Bond especifica com MTU 1500.

- **Conexões FC:**  
Garantir que os drivers do storage compartilhado ou dedicado estejam instalados dentro da matriz de compatibilidade do fabricante, sempre em pares.

- **Drivers:**  
Instalar os drivers mais atuais do site do fabricante. 

- **NICs:**  
Preferencialmente utilizar interfaces físicas da INTEL (Alguns problemas são conhecidos com a Broadcom).
Não serão aceitos NAT’s entre os hosts Xenservers e o XenCenter.

### Instalação e Configurações
---

#### Hardware

- **Pré-requisitos:** Seguir o guia de recomendação do fornecedor [Link](https://docs.citrix.com/en-us/xenserver/7-0/downloads/installation-guide.pdf) ou documentação mais recente.

#### Software

- **Pré-requisitos:** Realizar a instalação Default da Citrix Xenserver, caso seja necessário informar diretamente no runbook quais foram as customizações realizadas.

    http://support.citrix.com/product/xens/?_ga=2.70048976.179200202.1537811074-1419965309.1534186237

- **Updates e Builds:** Instalar os updates e builds disponíveis para a versão do SO, disponíveis até a data de início do projeto.

- **Xencenter:** A versão do Xencenter deve ser a compatível ou mais atual so Xenserver.

- **Xencenter Windows**
  - Pré Requisito para a instalação do Xencenter no Windows;
  - Pode ser físico, virtual na Cloud Equinix ou na Cloud Privada do cliente;
  - O XenCenter deve ser instalado em uma máquina Windows remota que possa se conectar ao host XenServer através de sua rede. O .NET Framework versão 3.5 também deve ser instalado nesta estação de trabalho;
  - A mídia de instalação do XenCenter é fornecida com a mídia de instalação do XenServer;

-  **License Server Virtual Appliance**
  - Realizar o download do appliance no site: https://support.citrix.com/article/CTX124501
  - Acessar o seguinte link: https://www.citrix.com/English/ss/downloads/details.asp?downloadId=2301285&productId=683148&_ga=2.138523703.585303104.1538057440-425662380.1516198538 para efetuar o download, para importar o appliance, utilize o xencenter, ao realizar o download, o guia de instalação está em anexo. 
  - https://support.citrix.com/article/CTX124501#P21_1461

- **License Server Virtual Windows**
  - Segue o link com os requisitos do Sistema para o licenciamento Citrix Windows:
  - https://docs.citrix.com/en-us/licensing/current-release/system-requirements.html

- #### Configurações:
---
 - **NTP:** Ajustar as configurações de NTP utilização dos NTPS Equinix   
 - **SR – Storage:** Não será permitido a utilização de Extend, o SR após utilização dever conter no mínimo 20% de espaço livre.  
    - Ao avaliar o espaço necessário para armazenamento das VMs, considerar o espaço de memória RAM, conforme exemplo abaixo:  
    - VM com 100 GB de disco (VBD) e 8 GB de RAM ocupa 108 GB RAM no SR.  
- **VDB/VDI:** Não serão aceitos aprovisionamentos do tipo THIN PROVISIONING sendo obrigatórios utilizar o formato de THICK PROVISIONING.  
- **VDB:** vDisk, disco virtual, mode: Read / Write.  
- **WLB:** Caso utilize WLB configurar como MAXIMIZE PERFORMANCE (DEFAULT).  
- **Pool HA:** Caso configurado HA, configurar no mínimo tolerância de falha um host e configurar o SR de HeartBeat.  
- **SSH:** Configurar o SSH para iniciar juntamente com o Host.  
- **Monitoramento:** Solicitar a Equipe de Monitoramento a aplicação do Template Citrix Xenserver.  
- **Storage:** Quando utilizando storage, configurar a política de Multipath para Round Robin, a menos que o storage não permita.  



#### Revisão


| Revisor Responsável          | Validador Responsável     | Atualizado |
|------------------------------|---------------------------|------------|
| Rodolfo Martins              |                           | 18/12/2019 |































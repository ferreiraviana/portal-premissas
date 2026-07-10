![alt text](img/logo_eqx.jpg "Logo EQX")

# Premissa Active Directory + DNS Server

# Sumário
-   [Objetivo](#objetivo)
-   [Sistemas operacionais suportados](#sistemas-operacionais-suportados)
-   [Definição de ambientes](#Definição-de-ambientes)
-   [Hardening](#Hardening)
-   [Modo de Operação ](#Modo-de-Operação )
-   [DNS](#DNS)
-   [Monitoramento](#monitoramento)
-   [Revisão](#revisão)

---
### Objetivo 
---
Esse documento tem como objetivo prover a padronização configurações para ambientes gerenciados pela equipe MSH Equinix.

Abaixo seguem as configurações propostas para hosts com funções de ACTIVE DIRECTORY e DNS Server.
##### [↑](#EQ_PREMISSA_ACTIVE_DIRECTORY_+_DNS_SERVER)
### Sistemas operacionais suportados 

---
* Seguir as recomendações da premissa de SO Windows. 
[Premissa Windows](https://git.mop.equinix.com.br/br-msh-problem/premissas/-/blob/Producao/Premissa.Windows.md)


##### [↑](#EQ_PREMISSA_ACTIVE_DIRECTORY_+_DNS_SERVER)
### Definição de ambientes 
---
#### 1- Ambiente com alta disponibilidade.  

Instalação do Active Directory em dois ou mais servidores (podendo ser físico ou virtual) distintos garantindo alta disponibilidade.
Para dois ou mais servidores em ambiente virtual, deverá possuir uma regra antiafinidade, afim de garantir que não estejam todos no mesmo host fisico.


#### 2- Ambiente Stand-alone [Flexível]. 

**Não recomendamos** a instalação de AD standalone para ambientes de PRODUÇÃO. \
Instalação do Active Directory acontece em um servidor sendo este físico ou virtual quando: 

* Considerar um servidor em ambiente físico quando o cliente possuir apenas servidores físicos.
* Considerar um servidor em ambiente virtual quando o cliente possuir um ambiente virtual (HyperV, VMware, CP ou VPS).


<span style="color:orange">OBS: Para instalações em ambientes virtuais do cliente, o hypervisor deverá seguir a premissa de VMware ou HyperV </span>
[Premissa Hyper-V](https://git.mop.equinix.com.br/br-msh-problem/premissas/-/blob/Producao/Premissa.Hyper-V.md) [Premissa VMWARE](https://git.mop.equinix.com.br/br-msh-problem/premissas/-/blob/Producao/Premissa.VMWARE.md)


##### [↑](#EQ_PREMISSA_ACTIVE_DIRECTORY_+_DNS_SERVER)  
### Hardening   
---
#### Accounts e Passwords    

* O cliente **NÃO** deve possuir acesso administrativo no servidor ou domínio;
* Desabilitar a conta administrator;
* Copiar a conta "administrator" criando duas novas contas chamadas “eqxadmin” e "eqxgerencia" e cadastrar a senha no ServiceNow;
* As contas eqxadmin e eqxgerencia, deverão ser configuradas para não expirar a senha;
* Cadastrar a senha DSRM (Disaster Recovery) no ServiceNow;
* LAPS (Local Administrator Password Solution): [Flexível]
    - Pode ser realizada a instalação e configuração do LAPS para gerenciamento das credenciais locais dos hosts com gerenciamento TM ou OSA;

####    Atualizações e Melhores Práticas    
* Realizar todas as atualizações e Hotfixes disponíveis.  
* Executar o Best Practices Analyzer e corrigir quaisquer alertas existentes. 

####    Configuração de Hardware  
* Seguir as recomendações da premissa de SO Windows.
[Premissa Windows](https://git.mop.equinix.com.br/br-msh-problem/premissas/-/blob/Producao/Premissa.Windows.md)


####    Configurações de Rede
* Seguir as recomendações da premissa de SO Windows.
[Premissa Windows](https://git.mop.equinix.com.br/br-msh-problem/premissas/-/blob/Producao/Premissa.Windows.md)


####    Instalação e Configuração    
* Deve ser instalado apenas os módulos (roles) que o(s) site(s) irão utilizar;
* Será suportada a role AD DS (Active Directory Domain Services)
* As roles AD FS e AD CS podem ser implementadas em servidores exclusivos e desde que sejam necessárias em projetos. Todas as senhas relacionadas as federações e autoridade certificadora deverão ser documentadas no ServiceNow;
* Em caso de ambiente HA, mínimo de 2 servidores como controlador do mesmo domínio por site e definidos no Sites and Services dentro do AD;
* Definir IP fixo;
* Para DC virtuais em ambiente HyperV, desabilitar a opção de "Time Synchronization" no Integration Services;
* Para DC virtuais em ambiente Vmware, desabilitar a opção de "Synchronize Guest Time with Host" nas propriedade do VMtools;
* Criar uma Organizational Unit (OU) com nome 'Servers' para alocação dos objetos computadores do domínio, que serão gerenciados pela Equinix;
* Definir nome FQDN do servidor e domínio, utilizando para o domínio nomes internos e não os comuns de internet
    ```
    Exemplo:
    Utilizar: cliente.local 
    NÃO Utilizar: cliente.com.br
    ```
* **Não recomendamos** a extensão do domínio local do cliente para o ambiente gerenciado Equinix. Caso tenha que ser feito, as permissões administrativas deverão reavaliadas. Caso o cliente queira manter credenciais administrativas, o ambiente deverá ser entregue juntamente a uma RiskLetter.

* Elevar o nível funcional do domínio e da floresta para o máximo permitido do controlador de domínio mais antigo.
    ```
    Exemplo:
        - Se existe somente DCs Windows 2012 R2, o nivel funcional do domínio e floresta deverá ser Windows Server 2012 R2.
        - Se existir alguma versão de SO anterior como Domain Controllers, elevar o domínio e floresta até para a versão mais antiga. (Isso deverá ser notificado ao cliente, pois versões anteriores do SO não são suportadas na matriz de gerenciamento Equinix)
    ```

* Havendo Member Server com segmento de rede diferente, confirmar nas regras de Firewall se a subnet correspondente está com as regras criadas conforme tabela abaixo;
* Validar se todas as portas e protocolos requeridos para o funcionamento do Active Directory estão validados com as regras permissivas no Firewall, vide tabela abaixo: (referencia: https://support.microsoft.com/pt-br/help/832017/service-overview-and-network-port-requirements-for-windows) 




|Aplicação                             | Protocolo | Porta |
|:-------------------------------------------------------:|:---------:|:-----:|
| Serviços da Web do Active Directory (ADWS)              | TCP       | 9389  |
| Serviço de Gateway de Gerenciamento do Active Directory | TCP       | 9389  |
| Catálogo Global                                         | TCP       | 3269  |
| Catálogo Global                                         | TCP       | 3268  |
|ICMP	 	                                              |---        |---    |
|Servidor LDAP	                                          | TCP	      | 389   |
|Servidor LDAP	                                          | UDP 	  | 389   |
|LDAP SSL	                                              | TCP	      | 636   |
|IPsec ISAKMP	                                          | UDP	      | 500   |
|NAT-T	                                                  | UDP	      | 4500  |
|RPC	                                                  | TCP	      | 135   |
|Portas TCP superiores alocadas aleatoriamente em RPCs	  | TCP	      | 1024 ‒ 5000    |
|Portas TCP superiores alocadas aleatoriamente em RPCs	  | TCP	      | 49152 - 65535   |
|SMB	                                                  | TCP	      | 445   |
|DNS                                                      |	UDP	      | 53    |
|DNS                                                      |	TCP	      | 53    |
|Serviço de sessão NetBIOS	                              | TCP	      | 139   |
|NTP                                                      | UDP       |123    |



#### Health Check    
* Verificar a existência de mensagens de erros no Event Viewer, se existir realizar o trobleshooting;
* Examinar o arquivo de log gerado pelo processo de promoção, pois ele armazena as informações de todas as etapas realizadas através do dcpromo o C:\Windows\Debug\DCPROMO.LOG;
* Verificar se os compartilhamentos NETLOGON e SYSVOL estão disponíveis;
* Validar replicação do domínio entre os servidores controladores de domínio. Conforme comandos abaixo:
    - Repadmin /showrepl
    - Repadmin /syncall
    - Repadmin /replsum
* Utilizar o DCDIAG para informar todo o diagnóstico constatado. Copiar o resultado para o Runbook;
##### [↑](#EQ_PREMISSA_ACTIVE_DIRECTORY_+_DNS_SERVER)
### Modo de Operação  
--- 
<span style="color:orange">As configurações abaixo são obrigatórias para ambientes TM ou OSA:</span>
* O Host deverá ser para uso exclusivo do Active Directory + DNS; 
* Não permitir multiplas funções (EX: SQL, IIS) para o servidor;
* Utilizar no mínimo dois Domain Controllers, para redundância quando o ambiente for HA.
* Não serão aceitos Níveis Funcionais de Domínio e de Floresta que sejam abaixo de 2012 R2.
* Para criação de Relação de Confiança (Trust) usando a floresta como recurso, terá que ser utilizado o Trust transitivo One-Way do tipo Forest, e sendo o Domain Controller destino com o sistema operacional Windows Server 2012 R2 ou acima. 
    - Informar nos comentarios do CI as configurações utilizadas para o Trust
    - Cadastrar no SNOW a senha da conta usada no domínio de origem.  
    - Criar uma conta chamada "Trust" e colocar em Observação que a conta é usada para o Domain Trust.  
* Em ambientes com mais de um Domain Controler, separar as FSMOs. Mais informações, sessão [FSMO](#FSMO) (referencia : https://support.microsoft.com/en-us/help/223346/fsmo-placement-and-optimization-on-active-directory-domain-controllers)
* Seguir com todas as recomendações ao implementar uma estrutura de domínio virtual conforme fabricante.  

#### Sites and Services
* Caso o cliente possua o domínio estendido em diversos datacenters da Equinix ou inloco **e não possua** VLAN extendida, deverão ser configurados no AD Sites and Services afim de minimizar os tempos de resposta e melhorar a replicação do serviço.
* Cada site deverá possuir uma subnet exclusiva;
* Deverá possuir pelo menos dois AD em cada site;
* A replicação do AD entre os sites deverá ser configurada com no máximo de 5 minutos.

#### Group Policies Objects
* Deverá ser configurado uma GPO padrão com o nome de Equinix para a OU Servers, definindo:
    - WSUS \
    Computer Configuration > Policies > Administrative Template > Windows Components > Windows Update
        - Allow Automatic Updates immediate installation > Disabled  
        - Allow non-administrators to receive update notifications > Disabled  
        - Allow signed updates from an intranet Microsoft update service location > Disabled  
        - Automatic Updates detection frequency > Enabled > 24h
        - Configure Automatic Updates > Enabled > Opção 3 - Auto download and notify for install
        - Do not adjust default option to 'Install Updates and Shut Down' in Shut Down Windows dialog box > Enabled  
        - Do not display 'Install Updates and Shut Down' option in Shut Down Windows dialog box > Enabled  
        - Specify intranet Microsoft update service location > Enabled > Utilizar o WSUS de cada estado:\
        RJ1 e RJ2 (http://wsus-rj1.hosting.equinix.com.br:8530) \
        SP1, SP2, SP3, SP4 e SP5 (http://wsus-sp1.hosting.equinix.com.br:8530)


    - NTP para o PDC\
    Computer Configuration > Administrative Templates > System > Windows Time Service > Time Providers
        - Configure Windows NTP Client
            - NtpServer: Configurar o FQDN do PDC Emulator
            - Type: NT5DS;
    - Caso o host não tenha GPO configurada com NTP da Equinix, configurar via PUPPET preferencialmente com os endereços abaixo:
      extntp.equinix.com.br
      1.extntp.equinix.com.br
      2.extntp.equinix.com.br
      3.extntp.equinix.com.br

 
* Na Default Domain Policy, configurar: 
    - Log Size - 512MB
    - Complexidade de Senha - habilitado
    - Tamanho de senha - 14 caracteres
    - Histórico de senha - últimas 24 senhas
    - Idade mínima de senha - 1 dia
    - Idade máxima de senha - 60 dias
    - Maximo de tentativas de senhas incorretas - 6 tentativas
    - Outras diretivas de segurança podem ser configuradas conforme necessidade do cliente [Flexível]

* Criar uma GPO para a OU Domain Cotrollers
    - Auditoria\
         Computer Configuration > Policies > Windows Settings > Security Settings > Advanced Audit Policy Configuration > Audit Policies
        - Account Management > Audit User Account Management > Success e Failure
        - Logon / Logoff > Audit Account Lockout > Failure
        - Logon / Logoff > Audit Logon > Success e Failure
        - Policy Change > Audit Audit Policy Change > Success e Failure
        
    - NTP Server - Seguir configurações conforme [KB0011849](https://equinixcsm.service-now.com/kb_knowledge.do?sys_id=af569307db69d8dc034e6e25ca9619e9&sysparm_record_target=kb_knowledge&sysparm_record_row=1&sysparm_record_rows=236&sysparm_record_list=workflow_state%3Ddraft%5EORworkflow_state%3Dreview%5Ekb_knowledge_base.u_countryINBR%5EORDERBYDESCnumber)
    
    - NTP Server ( Via CSR ) - Configurar com os endereços de NTP da Equinix abaixo: 
    
    extntp.equinix.com.br
    1.extntp.equinix.com.br
    2.extntp.equinix.com.br
    3.extntp.equinix.com.br

     
##### [↑](#EQ_PREMISSA_ACTIVE_DIRECTORY_+_DNS_SERVER)
#### FSMO 
Sempre que possível, as FSMOs deverão ser separadas:

1. PDC Emulator + RID Master deverão estar no mesmo host
2. Infrastructure Master não deverá estar em um servidor de Global Catalog, mas deverá ter replicação direta entre os hosts
3. Schema Master + Domain Name Master deverão estar no mesmo servidor, junto com o Global Catalog.
```
Exemplo:
AD1 : PDC + RID + Infrastructure Master
AD2 : Schema Master + Domain Name Master [GC]
```
As informações dos owners das FSMO deverá estar informado nos comentários do CI dos DC no ServiceNow;


##### [↑](#EQ_PREMISSA_ACTIVE_DIRECTORY_+_DNS_SERVER)
### DNS
---
#### Premissas básicas:
* Configurar o forwarder para o DNS da Equinix.

|SITE | Primário | Secundário |
|:----:|:---------:|:-----:|
|SP1 | 200.198.176.18 | 200.155.1.18|
|SP2 | 200.143.177.4 | 200.143.177.40|
|SP3 | 138.36.219.201 | 138.36.219.202|
|RJ1 | 201.49.216.57 | 201.49.216.58|
|RJ2 | 179.107.35.140 | 179.107.35.141|

* Todos os hosts da rede interna deverão estar apontados para os DNS Servers **internos**;
* Deverá ser apontado como DNS Primário o DNS Server mais próximo (intra-site);
* Configurar Aging e Scavenging com configurações padrões;
* Executar o BPA e caso tenha algum erro, resolver;
* Os servidores DNS dentro de um domínio não devem usar uns aos outros como forwarder;
* Criar zona reversa integrada ao AD;

Além das configurações acima, devem ser realizadas as configurações:

#### Ambientes com HA
* Pelo menos 2 Domain Controllers deverão ser configurado como DNS Server por cada site do AD
* DNS Primario: definido para outro DNS na rede
* DNS Secundário: definido para si próprio


#### Ambiente StandAlone
* Pelo menos 1 Domain Controller deverá ser configurado como DNS Server por cada site do AD

##### [↑](#EQ_PREMISSA_ACTIVE_DIRECTORY_+_DNS_SERVER)

### Monitoramento
Utilizar o template **TEMPLATE.TM_WINDOWS _ACTIVE_DIRECTORY** para monitoramento dos servidores AD

##### [↑](#EQ_PREMISSA_ACTIVE_DIRECTORY_+_DNS_SERVER)
### Revisão

| Revisor Responsável          | Validador Responsável     | Atualizado |
|------------------------------|---------------------------|------------|
| Joao Area                    | Pamella Canova            | 02/07/2020 |
| Joao Area                    | Rafael Pereira            | 01/12/2022 |
| Thiago Araujo                | Leonardo Abreu            | 10/11/2023 |

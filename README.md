# Portal Interno · Implementation Management BR

Portal colaborativo para dar visibilidade ao processo de entrega de ponta a ponta — da qualificação da oportunidade à operação e ao suporte.

O conteúdo reúne orientações dos fabricantes, práticas internas, premissas técnicas e critérios de decisão utilizados no dia a dia dos projetos. O objetivo é permitir que Pré-venda, Arquitetura, Implementation Management, Implementação e Suporte consultem a mesma referência, com linguagem direta e navegação rápida.

## O que o portal entrega

- Visão geral da jornada e das responsabilidades de cada área.
- Catálogo organizado por produto e serviço técnico.
- Premissas para Pré-venda, Arquitetura, Implementação e Suporte.
- Busca global por produto, etapa, requisito ou código PRM.
- Filtros por perfil profissional.
- Radar de versões e governança do conteúdo.
- Checklists locais para acompanhamento das entregas.
- Matriz de decisão para migrações e atualizações.
- Arquitetura de referência para replicação com Veeam.
- Links para os documentos completos e para as fontes oficiais.

## Jornada de ponta a ponta

O portal organiza o processo em sete etapas:

1. Entrada e qualificação
2. Pré-venda
3. Transição comercial
4. Planejamento
5. Implementação
6. Transição e aceite
7. Operação e suporte

Cada etapa deve deixar claros o responsável, as entradas necessárias, a entrega esperada, as dependências e o critério de conclusão.

## Portfólio atual

| Área | Produtos e serviços |
|---|---|
| Virtualização | VMware vSphere e XenServer/Citrix |
| Sistemas e diretório | Windows Server e Active Directory + DNS |
| Infraestrutura | Storage SAN e Switch TOR/Rede |
| Proteção de dados | Veeam Backup & Replication e Commvault |
| Serviços de projeto | Migrações e atualizações |

A área de migrações contempla seleção de abordagem, Cross vCenter vMotion, réplica Veeam, migração paralela, atualização no ambiente atual, volumetria, ondas, cutover, rollback e aceite.

## Como usar o portal

1. Comece pela busca ou pela jornada de ponta a ponta.
2. Selecione seu perfil para reduzir o conteúdo ao contexto necessário.
3. Consulte a página do produto ou serviço.
4. Valide a versão no Radar de Versões.
5. Abra a premissa completa quando precisar do detalhamento técnico.
6. Registre decisões, exceções e evidências nos sistemas oficiais do projeto.

Recursos disponíveis:

- `Ctrl + K` ou `/` direciona o foco para a busca.
- “Ir rapidamente para...” abre uma página específica.
- O menu lateral pode recolher ou expandir os grupos do catálogo.
- As páginas podem ser favoritadas, impressas ou compartilhadas por link.
- O sumário “Nesta página” permite acessar rapidamente cada seção.
- Os checklists são armazenados localmente no navegador e não substituem evidências formais.

## Estrutura do repositório

```text
.
├── public/
│   ├── index.html                         # Portal em HTML, CSS e JavaScript
│   ├── .nojekyll                          # Publicação sem processamento do Jekyll
│   ├── Imagens/
│   │   └── Arquitetura replicação veeam.jpg
│   └── premissas/                         # Documentos técnicos completos
├── .github/
│   └── workflows/
│       └── deploy-pages.yml               # Publicação manual no GitHub Pages
├── .vscode/                               # Configurações e extensões recomendadas
└── README.md
```

O portal não utiliza framework ou processo de compilação. A interface, os estilos e os comportamentos estão concentrados em `public/index.html`.

## Execução local

### Abrir diretamente

Abra `public/index.html` em um navegador moderno. A maior parte das funcionalidades opera sem conexão com a internet.

### Usar o VS Code com Live Server

```powershell
git clone https://github.com/ferreiraviana/portal-premissas.git
cd portal-premissas
code .
```

No VS Code, inicie o Live Server para visualizar as alterações com atualização automática.

### Servidor interno

A pasta `public/` pode ser publicada por um servidor HTTP interno, desde que os controles de acesso atendam à política de segurança da empresa.

## Como manter o conteúdo

As páginas do portal estão divididas em seções comentadas dentro de `public/index.html`.

### Conteúdo por perfil

```html
<div class="aud" data-aud="prevenda arquitetura tecnico gestao">
```

Use somente os perfis que realmente consomem o bloco.

### Indexação na busca

```html
<div data-tags="vmware migração réplica rollback">
```

Inclua termos técnicos, variações de escrita e sinônimos que ajudem o usuário a localizar o conteúdo.

### Premissas e práticas

- Use códigos PRM quando o requisito precisar ser citado em propostas, planos ou evidências.
- Diferencie obrigação, recomendação e decisão dependente do projeto.
- Não transforme uma arquitetura implementada em regra universal.
- Registre exceções, riscos, responsáveis e aprovações.
- Preserve nomes oficiais de produtos e recursos técnicos.
- Prefira frases curtas, voz ativa e termos compreensíveis para diferentes áreas.

### Imagens

- Armazene os arquivos em `public/Imagens/`.
- Use nomes descritivos e inclua o arquivo no mesmo commit do HTML.
- Informe texto alternativo no elemento `<img>`.
- Comprima imagens sem prejudicar a leitura de rótulos e diagramas.
- Trate endereços, nomes de ambientes e portas como informações que exigem revisão antes da publicação.

## Critérios editoriais

Antes de concluir uma alteração, confirme:

- Ortografia, acentuação, concordância e pontuação.
- Padronização dos títulos e das etapas.
- Linguagem direta e adequada ao público interno.
- Ausência de abreviações informais ou termos ambíguos.
- Legibilidade em telas grandes e dispositivos móveis.
- Funcionamento da busca, dos filtros, dos links e do sumário contextual.
- Coerência entre o resumo do portal e o documento técnico completo.

## Governança técnica

- As recomendações do fabricante devem ser validadas na documentação oficial vigente.
- Versão, build, compatibilidade, licenciamento e ciclo de suporte devem ser reavaliados em cada projeto.
- O Radar de Versões deve ser revisado periodicamente e sempre que houver mudança relevante.
- A arquitetura define o padrão; exceções precisam de justificativa e aceite formal.
- Implementação e Suporte devem registrar evidências, particularidades e transições nos sistemas oficiais.
- Alterações relevantes devem passar por Pull Request e revisão do responsável pelo conteúdo.

## Segurança e publicação

Este repositório e os documentos em `public/premissas/` podem conter informações internas, como endereços IP, nomes de hosts, padrões operacionais e referências de credenciais. O conteúdo deve permanecer em ambiente autorizado.

O workflow `deploy-pages.yml` utiliza execução manual. Antes de publicar:

1. Confirme a visibilidade efetiva do site.
2. Verifique se o plano da organização oferece controle de acesso privado ao GitHub Pages.
3. Remova ou sanitize informações sensíveis quando o site for público.
4. Revise imagens, documentos, histórico e artefatos de publicação.
5. Obtenha a aprovação do responsável pela segurança ou governança.

Um repositório privado não torna automaticamente o site do GitHub Pages privado. A publicação privada com controle de acesso depende da configuração e do plano da organização.

## Fluxo recomendado de alteração

```powershell
git checkout -b atualiza-portal
git add public/index.html public/premissas public/Imagens README.md
git commit -m "Atualiza conteúdo do portal"
git push -u origin atualiza-portal
```

Depois, abra um Pull Request, descreva o impacto, informe as fontes consultadas e solicite a revisão dos responsáveis pelas áreas afetadas.

---

Uso interno · Implementation Management BR

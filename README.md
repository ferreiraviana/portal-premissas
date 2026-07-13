# Portal Técnico · Implementation Management BR

> Base interna de referência para decisões, padrões e entregas técnicas ao longo do ciclo de implementação.

## Sobre o projeto

Este repositório mantém um portal estático que consolida premissas, boas práticas de fabricantes, padrões internos e conhecimento operacional.

O objetivo é oferecer uma referência única, simples e rastreável para os profissionais envolvidos no planejamento, na implementação, na transição e na sustentação dos projetos.

| Item | Definição |
|---|---|
| Público | Uso interno |
| Responsável | Implementation Management BR |
| Aplicação | Portal estático em HTML, CSS e JavaScript |
| Dependências | Nenhuma |
| Publicação | GitHub Pages por workflow manual |

## Principais recursos

- Jornada de entrega de ponta a ponta.
- Catálogo técnico organizado por produto e capacidade.
- Busca global e filtros por contexto profissional.
- Premissas, checklists e critérios de aceite.
- Radar de versões e referências oficiais.
- Matriz de decisão para migrações e atualizações.
- Base de conhecimento com vídeos e exemplos práticos.
- Navegação responsiva, favoritos e links diretos.

## Cobertura técnica

A organização abaixo representa domínios de conhecimento do portal, não a estrutura organizacional da empresa.

| Domínio | Tecnologias e temas |
|---|---|
| Plataformas de virtualização | VMware vSphere, PowerCLI e XenServer/Citrix |
| Sistemas e identidade | Windows Server, Active Directory e DNS |
| Dados e conectividade | Storage SAN, multipath, Switch TOR, VLAN e redes |
| Continuidade e proteção | Veeam Backup & Replication, Commvault, restauração e replicação |
| Transformação de ambientes | Migrações, atualizações, cutover, rollback e transição operacional |

Novas tecnologias devem ser incorporadas pelo contexto técnico e pela etapa da jornada em que são utilizadas, sem depender do nome de uma área interna.

## Início rápido

Clone o repositório e abra o portal:

```powershell
git clone https://github.com/ferreiraviana/portal-premissas.git
cd portal-premissas
start public/index.html
```

Para desenvolvimento, abra o projeto no VS Code e utilize o Live Server:

```powershell
code .
```

Não há instalação de pacotes, compilação ou geração de artefatos.

## Estrutura do repositório

```text
.
├── public/
│   ├── index.html             # Interface, conteúdo e comportamentos
│   ├── imagens/               # Diagramas e recursos visuais
│   ├── videos/                # Conteúdo audiovisual sanitizado
│   └── premissas/             # Documentos técnicos completos
├── .github/workflows/
│   └── deploy-pages.yml       # Publicação manual no GitHub Pages
├── .vscode/                   # Configuração recomendada do editor
└── README.md
```

## Padrão de conteúdo

Cada conteúdo deve responder objetivamente:

1. Qual problema ou decisão ele apoia?
2. Em qual contexto e versão ele é válido?
3. Quais são os pré-requisitos e riscos?
4. Como implementar e validar?
5. Qual é o critério de aceite ou rollback?
6. Qual fonte oficial sustenta a orientação?

Requisitos citáveis devem utilizar um código PRM. Exceções precisam registrar impacto, responsável e aprovação.

## Contribuição

1. Crie uma branch para a alteração.
2. Atualize o portal e os documentos relacionados.
3. Inclua imagens e vídeos no mesmo commit das referências no HTML.
4. Valide busca, links, responsividade, ortografia e informações sensíveis.
5. Abra um Pull Request com objetivo, impacto e fontes consultadas.
6. Solicite revisão ao responsável pelo domínio técnico afetado.

Exemplo:

```powershell
git checkout -b docs/atualiza-premissa
git add -A
git commit -m "docs: atualiza premissa técnica"
git push -u origin docs/atualiza-premissa
```

## Governança

- Validar versões, builds, compatibilidade, licenciamento e suporte nas fontes oficiais.
- Revisar o Radar de Versões periodicamente e após mudanças relevantes.
- Tratar arquiteturas implementadas como referências, não como regras universais.
- Manter decisões, evidências e particularidades nos sistemas oficiais do projeto.
- Preservar linguagem direta, consistência editorial e rastreabilidade das alterações.

## Segurança

Este repositório pode conter informações internas. Antes de qualquer commit ou publicação:

- Remova credenciais, tokens, nomes sensíveis e dados desnecessários.
- Sanitize capturas, diagramas, áudios e vídeos.
- Confirme a visibilidade efetiva do GitHub Pages.
- Verifique os controles de acesso da organização.
- Obtenha aprovação quando houver conteúdo restrito.

Um repositório privado não torna automaticamente o site do GitHub Pages privado.

## Licença e uso

Conteúdo proprietário para uso interno. Redistribuição, publicação externa ou reutilização fora do contexto autorizado depende de aprovação formal.

---

Mantido por **Implementation Management BR**.

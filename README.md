# Portal Interno · Implementation Management BR

Portal colaborativo para orientar o processo de entrega de ponta a ponta — da qualificação da oportunidade à operação e ao suporte.

Reúne práticas dos fabricantes, padrões internos, premissas técnicas e critérios de decisão em uma referência única para Pré-venda, Arquitetura, Implementation Management, Implementação e Suporte.

## Visão executiva

O portal oferece:

- Jornada de ponta a ponta com sete etapas.
- Catálogo por produto e serviço técnico.
- Busca global e filtros por perfil profissional.
- Premissas para Pré-venda, Arquitetura, Implementação e Suporte.
- Radar de versões e governança técnica.
- Checklists, matriz de migração e critérios de aceite.
- Acesso aos documentos completos e às fontes oficiais.

O conteúdo apoia decisões, mas não substitui o registro formal de escopo, riscos, exceções, evidências e aprovações nos sistemas oficiais.

## Jornada

1. Entrada e qualificação
2. Pré-venda
3. Transição comercial
4. Planejamento
5. Implementação
6. Transição e aceite
7. Operação e suporte

Cada etapa deve deixar claros o responsável, as entradas, as dependências, a entrega esperada e o critério de conclusão.

## Portfólio

| Área | Produtos e serviços |
|---|---|
| Virtualização | VMware vSphere e XenServer/Citrix |
| Sistemas e diretório | Windows Server e Active Directory + DNS |
| Infraestrutura | Storage SAN e Switch TOR/Rede |
| Proteção de dados | Veeam Backup & Replication e Commvault |
| Serviços de projeto | Migrações e atualizações |

## Como acessar

- Abra `public/index.html` diretamente no navegador.
- Use o Live Server no VS Code para editar e visualizar localmente.
- Publique a pasta `public/` somente em servidor interno ou ambiente com acesso controlado.

O portal utiliza HTML, CSS e JavaScript, sem framework ou processo de compilação.

## Estrutura

```text
public/
├── index.html                         # Portal
├── imagens/                           # Diagramas e recursos visuais
└── premissas/                         # Documentos técnicos completos

.github/workflows/deploy-pages.yml     # Publicação manual
README.md
```

## Manutenção do conteúdo

- Use linguagem direta, objetiva e compreensível para diferentes áreas.
- Valide versões, builds, compatibilidade, licenciamento e suporte nas fontes oficiais.
- Diferencie requisitos obrigatórios, recomendações e decisões específicas do projeto.
- Inclua termos e sinônimos em `data-tags` para melhorar a busca.
- Registre exceções, riscos, responsáveis e aprovações.
- Revise ortografia, links, responsividade e documentos relacionados.
- Inclua imagens e alterações do HTML no mesmo commit.

## Governança

- O Radar de Versões deve ser revisado periodicamente e após mudanças relevantes dos fabricantes.
- Alterações técnicas devem passar por revisão do responsável pelo conteúdo.
- Arquiteturas implementadas são referências, não regras universais.
- Implementação e Suporte devem manter evidências e particularidades nos sistemas oficiais.

## Segurança e publicação

Os documentos podem conter informações internas, como IPs, nomes de hosts e padrões operacionais. O repositório e o portal devem permanecer em ambiente autorizado.

Um repositório privado não torna automaticamente o GitHub Pages privado. Antes de executar o workflow manual de publicação:

1. Confirme a visibilidade efetiva do site.
2. Verifique o controle de acesso disponível para a organização.
3. Remova ou sanitize informações sensíveis.
4. Obtenha a aprovação de Segurança ou Governança.

---

Uso interno · Implementation Management BR

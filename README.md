# Catálogo de Produtos · Premissas & Boas Práticas (v3)

Portal interno em formato de **catálogo de produtos**: cada tecnologia tem uma página com visão do produto, bloco de **Pré-venda**, **Arquitetura (pode / não pode)** e **Implantação (premissas PRM)** — com filtros por perfil (Pré-venda · Arquitetura · Técnico · Gestão) e busca global.

Produtos: VMware vSphere, XenServer/Citrix, Windows Server, Active Directory + DNS, Storage (SAN), Switch TOR, Veeam B&R, Commvault — e o serviço transversal **Migrações & Upgrades** (matriz de decisão, Cross vCenter vMotion, réplica Veeam, in-place × paralelo, volumetria e cutover).

## Estrutura
```
.
├── public/
│   ├── index.html          ← o portal (HTML+CSS+JS, sem dependências)
│   ├── .nojekyll
│   └── premissas/          ← documentos .md originais (CONTEÚDO SENSÍVEL)
├── .github/workflows/
│   └── deploy-pages.yml    ← publicação MANUAL no Pages (desativada por padrão)
├── .vscode/                ← extensões e config recomendadas (Live Server etc.)
└── README.md
```

## ⚠️ Este repositório é PRIVADO — e deve continuar assim
Os documentos em `public/premissas/` contêm IPs internos, hostnames e padrões de credenciais.
**Sites do GitHub Pages são sempre públicos na internet** (mesmo de repositório privado), exceto no
GitHub Enterprise Cloud com *Pages access control*. Por isso o workflow de publicação é **manual**
(`workflow_dispatch`) e não deve ser executado sem sanitizar o conteúdo ou sem Enterprise.

### Como o time acessa o portal (com o repo privado)
1. **Local (recomendado):** clonar e abrir `public/index.html` no navegador — funciona 100% offline.
2. **VS Code + Live Server:** abrir a pasta no VS Code (extensões recomendadas aparecem automaticamente) → botão *Go Live* → o portal abre com auto-reload a cada edição.
3. **Servidor web interno:** copiar a pasta `public/` para qualquer servidor http da rede corporativa.

## 💻 Trabalhando no VS Code
```bash
git clone https://github.com/ferreiraviana/portal-premissas.git
cd portal-premissas
code .
```
O VS Code vai sugerir as extensões recomendadas (Live Server para visualizar, Markdown, corretor PT-BR e **Claude Code**). Com o Claude Code instalado, você pode pedir alterações direto no editor — ex.: *"adicione a premissa de Hyper-V ao portal e abra um PR"*.

## ✏️ Como editar
Tudo em `public/index.html`, seções comentadas (`<!-- ===== VMWARE ===== -->` etc.):
- Bloco por público: `<div class="aud" data-aud="prevenda arquitetura tecnico gestao">`
- Prática/premissa: `<div class="card">` · Biblioteca PRM: `<tr>` · FAQ: `<details>`
- `data-tags="..."` alimenta a busca — inclua sinônimos.

## Governança
- Alterações via Pull Request com revisão do dono do conteúdo (proteger a branch `main`).
- Issues como canal de melhoria; labels por produto.
- Issue recorrente semestral: revisar **Radar de Versões** e premissas defasadas.

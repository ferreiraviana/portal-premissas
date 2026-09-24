# Agente local de análise de HLD

Conector local entre o portal e o Claude Code. Ele escuta somente em `127.0.0.1`, carrega as premissas do repositório e retorna um parecer técnico estruturado.

## Pré-requisitos

1. Instale o Claude Code CLI ou a extensão oficial do Claude Code para VS Code seguindo a [documentação oficial](https://docs.anthropic.com/en/docs/claude-code/getting-started). O conector localiza automaticamente o binário da extensão.
2. Execute `claude` no terminal e autentique a conta Claude Pro, Max ou Console.
3. Confirme com `claude --version`.

## Execução

No PowerShell, a partir da raiz do repositório:

```powershell
powershell -ExecutionPolicy Bypass -File .\local-agent\Start-HldAgent.ps1
```

Mantenha a janela aberta enquanto usar **Análise de HLD** no portal. O indicador deve mudar para **Agente Claude conectado**.

## Controles de segurança

- Não contém nem persiste credenciais.
- Aceita apenas a origem publicada do portal, `localhost` e teste local.
- Limita cada solicitação a 8 MB e o conteúdo analisado a 1,5 milhão de caracteres.
- Não grava HLD, resposta ou prompt em disco.
- Envia ao Claude apenas o conteúdo extraído, metadados preenchidos e premissas internas.
- O parecer continua sujeito à validação e ao aceite de um especialista.

Para usar outra origem autorizada:

```powershell
.\local-agent\Start-HldAgent.ps1 -AllowedOrigin "https://portal.exemplo.com"
```

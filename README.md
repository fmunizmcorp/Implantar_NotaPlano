# Implantar o NotaPlano com a sua IA

O **NotaPlano** (https://www.notaplano.com.br) emite nota fiscal de serviço (NFS-e). Este repositório ensina a **sua IA** — ChatGPT
ou Claude, inclusive as versões grátis — a configurar o NotaPlano da sua empresa em minutos: ela faz as perguntas, lê os seus
documentos e monta a configuração; você confere e aplica.

## Comece aqui
1. Entre no NotaPlano e abra **Implantação com IA**: https://www.notaplano.com.br/app/implantacao-ia
2. Copie o prompt (ChatGPT ou Claude) e cole numa conversa nova.
3. Responda às perguntas e mande o cartão CNPJ e, se tiver, uma nota fiscal antiga com os dados do cliente cobertos.
4. Cole no NotaPlano o **manifesto** que a IA entregar → **Conferir** (nada é gravado) → **Aplicar**.
5. Envie o certificado digital na página da empresa, emita a nota de teste e passe para produção (você, na tela).

## Os três jeitos
| Jeito | Para quem | Como |
|---|---|---|
| **Manifesto colado na tela** | todo mundo, qualquer IA | o prompt + Conferir/Aplicar ([chatgpt/](chatgpt/COMO-USAR.md), [claude/](claude/COMO-USAR.md)) |
| **A IA aplica pela API** | quem usa Claude Code ou tem sistema próprio | token de implantação (vale 7 dias; configura e emite só nota de teste) — [ia/API.md](ia/API.md) |
| **Conector NotaPlano (MCP)** | Claude Code e sistemas, com o token; ChatGPT e Claude "um clique" em breve | `https://www.notaplano.com.br/mcp` — [ia/MCP.md](ia/MCP.md) |

## Segurança
- O **certificado digital e a senha dele não passam pela IA**: você envia direto na tela do NotaPlano. Se a IA pedir, não mande.
- Este repositório é **só documentação**: nenhum dado de cliente fica aqui.
- O manifesto tem os dados da sua empresa: **não publique**. Para guardar a sua implantação, use o [modelo-cliente/](modelo-cliente/README.md) num repositório **privado**.

## Mapa
- [ia/INSTRUCOES.md](ia/INSTRUCOES.md) — o que a IA deve fazer (comece por aqui, IA)
- [ia/PASSO-A-PASSO.md](ia/PASSO-A-PASSO.md) · [ia/PERGUNTAS.md](ia/PERGUNTAS.md) · [ia/DOCUMENTOS.md](ia/DOCUMENTOS.md)
- [ia/MANIFESTO.md](ia/MANIFESTO.md) · [manifesto/schema.json](manifesto/schema.json) · [manifesto/exemplos/](manifesto/exemplos/)
- [ia/SERVICOS.md](ia/SERVICOS.md) — a lista nacional de serviços · [ia/ERROS.md](ia/ERROS.md) · [ia/API.md](ia/API.md) · [ia/MCP.md](ia/MCP.md)
- [prompts/](prompts/) — os prompts prontos · [plugins/](plugins/) — o plugin do Claude

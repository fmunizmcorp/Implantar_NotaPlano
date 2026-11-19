# Usar com o Claude

## Jeito mais simples (qualquer plano, inclusive o grátis)
1. Abra https://www.notaplano.com.br/app/implantacao-ia e copie o **prompt para o Claude** (ou de [../prompts/claude.md](../prompts/claude.md)).
2. Cole numa conversa nova no Claude (site, Claude Desktop ou celular), responda e anexe o cartão CNPJ e uma nota antiga.
3. Copie o JSON, cole em Implantação com IA → **Conferir** → **Aplicar**.

## Com o plugin (planos pagos do Claude)
Em **Personalizar → Plugins → Adicionar marketplace**, informe `fmunizmcorp/Implantar_NotaPlano` e instale **NotaPlano —
Implantação**. O Claude passa a saber o passo a passo sem você colar o prompt.

## Claude Code (para quem é técnico) — conector com o token
Gere o token em Implantação com IA → Avançado e rode no terminal (troque `np_live_…` pelo seu token):

```bash
claude mcp add --transport http notaplano https://www.notaplano.com.br/mcp --header "Authorization: Bearer np_live_…"
```

O Claude passa a ter as ferramentas do NotaPlano ([../ia/MCP.md](../ia/MCP.md)): ver a situação, buscar o serviço, conferir e aplicar o
manifesto e emitir a nota de teste. O token vale 7 dias; não envia certificado, não emite nota de verdade e não passa para produção
(isso é você, na tela).

## Conector de um clique (em breve)
Adicionar a URL no Claude (site, Desktop ou celular) e entrar com a sua conta do NotaPlano, sem copiar token, virá com o login seguro
(OAuth).

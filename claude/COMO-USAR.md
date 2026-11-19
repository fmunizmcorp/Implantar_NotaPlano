# Usar com o Claude

## Jeito mais simples (qualquer plano, inclusive o grátis)
1. Abra https://www.notaplano.com.br/app/implantacao-ia e copie o **prompt para o Claude** (ou de [../prompts/claude.md](../prompts/claude.md)).
2. Cole numa conversa nova no Claude (site, Claude Desktop ou celular), responda e anexe o cartão CNPJ e uma nota antiga.
3. Copie o JSON, cole em Implantação com IA → **Conferir** → **Aplicar**.

## Com o plugin (planos pagos do Claude)
Em **Personalizar → Plugins → Adicionar marketplace**, informe `fmunizmcorp/Implantar_NotaPlano` e instale **NotaPlano —
Implantação**. O Claude passa a saber o passo a passo sem você colar o prompt.

## Claude Code (para quem é técnico)
Com o token de implantação, o Claude Code chama a API direto — veja [../ia/API.md](../ia/API.md).

## Conector (em breve)
O conector oficial do NotaPlano (adicionar a URL e entrar com a sua conta, sem copiar chave) virá com o login seguro (OAuth).

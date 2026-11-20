# Usar com o Claude

## Jeito mais simples (qualquer plano, inclusive o grátis)
1. Abra https://www.notaplano.com.br/app/implantacao-ia e copie o **prompt para o Claude** (ou de [../prompts/claude.md](../prompts/claude.md)).
2. Cole numa conversa nova no Claude (site, Claude Desktop ou celular), responda e anexe o cartão CNPJ e, se tiver, uma nota antiga com os dados do cliente cobertos — e a descrição, se ela citar pessoas.
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

## Conector de um clique (qualquer plano, inclusive o grátis)
Sem copiar token: o Claude entra com a sua conta do NotaPlano (login seguro, OAuth).
1. No Claude (site, Desktop ou celular): **Personalizar → Conectores → Adicionar conector personalizado** (*Customize → Connectors →
   Add custom connector*).
2. Cole `https://app.notaplano.com.br/mcp` (o endereço do **app.**, não do www), clique em **Adicionar** e depois em **Conectar**.
3. Abre a página do NotaPlano: entre, confira o aplicativo, o endereço para onde você volta (claude.ai), a empresa e o que a IA pode e não
   pode fazer, digite a sua senha e clique em **Permitir**.

Só o administrador ou o dono da empresa conecta, com o módulo API ligado. A conexão vale 7 dias, faz o mesmo que o token de implantação
(configura e emite só nota de teste) e aparece em Configurações → Integrações como "IA (conector): …" — revogue lá para cortar antes.
Vencida, o Claude pede para conectar de novo. Detalhes em [../ia/MCP.md](../ia/MCP.md).

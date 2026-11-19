# CLAUDE.md — Implantar_NotaPlano

## Como trabalhar aqui
Estado em arquivo, verificar antes de dizer pronto (mostre a saída do teste), 1 detalhe = 1 commit, não inventar.

## O que é este repositório
O kit PÚBLICO que ensina a IA do cliente (ChatGPT, Claude) a implantar o NotaPlano: instruções, perguntas, leitura de documentos,
formato do manifesto, exemplos, prompts e o plugin do Claude.

## Regra principal: este conteúdo é GERADO — não edite aqui
A fonte fica em `fmunizmcorp/emissor_nfe`, pasta `notaplano/docs/implantacao-ia/` (textos e exemplos) e no código do NotaPlano
(schema, prompts, lista de serviços, rotas da API). Para mudar: edite lá e rode
`node scripts/implantacao-ia/gerar-kit.js <pasta deste repositório>` (lá, um teste confere que os exemplos passam na conferência do
NotaPlano). Editar aqui direto se perde na próxima geração.

## Regras invioláveis
- **Nunca** dado de cliente (nome, CNPJ real, e-mail, nota) neste repositório: ele é público.
- **Nunca** pedir ou guardar certificado digital ou senha.
- Trabalho consolidado vai para `main`.

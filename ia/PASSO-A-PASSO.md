# Passo a passo da implantação

| # | O quê | Pela tela do NotaPlano | Pela API (com o token) |
|---|---|---|---|
| 1 | Conta criada e e-mail confirmado | https://www.notaplano.com.br/cadastrar | — |
| 2 | Perguntas e documentos | (conversa com a IA) | — |
| 3 | Conferir o manifesto (nada é gravado) | Implantação com IA → **Conferir** | `POST /api/v1/implantacao/simular` |
| 4 | Aplicar o manifesto | Implantação com IA → **Aplicar** | `POST /api/v1/implantacao` |
| 5 | Ver o próximo passo de cada empresa | Implantação com IA → "Onde a implantação está" | `GET /api/v1/implantacao/situacao` |
| 6 | Certificado digital A1 (.pfx + senha) | Empresas emissoras → a empresa → **Certificado digital** | nunca pela IA |
| 7 | Testar a conexão com a prefeitura | a empresa → **Testar conexão** | `POST /api/v1/emitentes/{id}/testar` |
| 8 | Nota de TESTE (homologação, sem valor fiscal) | **Emitir nota** com a empresa em homologação | `POST /api/v1/notas` e `POST /api/v1/notas/{id}/emitir` |
| 9 | Passar para PRODUÇÃO (notas de verdade) | a empresa → ambiente **Produção** → Salvar (a pessoa faz) | não pelo token da IA (só um sistema com o escopo `emitentes:producao`, com `{"confirmo": true}`) |

## Detalhes
- **Sem certificado?** A empresa pode emitir "sem certificado" (`modo_emissao: "portal"`): o NotaPlano prepara cada campo e a
  pessoa emite no site ou no aplicativo oficial. Nesse caso não há homologação nem certificado (passos 6 a 9 mudam: a primeira nota
  já é a de verdade, feita no site oficial).
- **Numeração:** se a empresa já emitia por outro sistema, o próximo número tem de continuar do último. Errar aqui faz a prefeitura
  recusar a nota (número repetido).
- **A situação (passo 5)** diz, para cada empresa, o que falta e o link da tela. É o "GPS" da implantação: siga o `proximo_passo`.
- **Produção** é a pessoa quem liga, na tela. As notas passam a valer e a contar no plano.
- **Cópia das notas para o contador** e alertas: a pessoa configura na tela da empresa (não vão pelo manifesto nem pelo token).

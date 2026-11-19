# Erros da conferência e da API — o que fazer

Toda recusa vem como `{"erro": {"codigo", "mensagem", "campo"}}` (na API) ou como uma linha com ❌ (na tela). O `campo` diz onde.

| Código | O que quer dizer | O que fazer |
|---|---|---|
| `json_invalido` | O texto colado não é um JSON | Entregue só o JSON, num bloco, sem texto antes ou depois |
| `campo_desconhecido` | Um nome de campo não existe | Use exatamente os nomes de [MANIFESTO.md](MANIFESTO.md) (a mensagem lista os aceitos) |
| `validacao` | Valor faltando ou fora do formato | Leia a mensagem: ela diz o campo e o formato |
| `perfil_com_problema` | O perfil faria a prefeitura recusar a nota | A lista diz o problema (sem NBS, ISS fora de 2–5%, retém sem alíquota, IBS/CBS fora da tabela oficial — EM062) |
| `cnpj_repetido` | O CNPJ já está nesta conta | No manifesto isso não acontece (a empresa é achada pelo CNPJ); na API, use PUT |
| `documento_aguardando` | O CNPJ é usado por outra conta do NotaPlano | O responsável pelo CNPJ confirma por e-mail; ou cadastre em homologação e envie o certificado (ele comprova) |
| `limite_plano` | O plano não comporta mais empresas | Mude o plano em Configurações → Plano |
| `implantacao_incompleta` | Faltam passos antes da produção | `pendencias` diz quais; veja a situação |
| `confirmacao` | Produção pede `"confirmo": true` | Pergunte à pessoa; só mande com o "sim" dela |
| `certificado_senha` / `certificado_sem_chave` / `certificado_de_outro_cnpj` | Problema no certificado | A pessoa resolve na tela (senha certa; arquivo .pfx com a chave privada; o certificado do próprio CNPJ) |
| `escopo` | O token não tem permissão | Gere o token de implantação de novo em Implantação com IA → Avançado |
| `chave_invalida` | Token errado, revogado ou vencido (vale 7 dias) | Gere outro |

## Recusas da prefeitura na nota de teste
O NotaPlano explica cada código oficial da prefeitura na própria nota (o que é, o que fazer, quem resolve). Os mais comuns na
implantação: número repetido (numeração), código do serviço não liberado no cadastro da empresa na prefeitura, alíquota diferente da
cadastrada. Leve a explicação para a conversa e ajuste o perfil ou a numeração.

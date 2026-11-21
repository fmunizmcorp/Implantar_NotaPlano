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
| `confirmacao` | Produção (só sistemas com o escopo `emitentes:producao`) pede `"confirmo": true` | A pessoa liga a produção na tela |
| `certificado_senha` / `certificado_sem_chave` / `certificado_de_outro_cnpj` | Problema no certificado | A pessoa resolve na tela (senha certa; arquivo .pfx com a chave privada; o certificado do próprio CNPJ) |
| `so_na_tela` | E-mails de cópia/alertas, troca de CNPJ, forma de emitir | A pessoa faz na tela da empresa |
| `numeracao_recuo` | Pela API o próximo número não volta | Se precisar mesmo recuar, a pessoa faz na tela (no manifesto, o número menor só "fica como está") |
| `unidade` | Alíquota parece fração (0,0065) | Use percentual: 0,65 |
| `so_homologacao` | O token só emite nota de TESTE | A empresa precisa estar em homologação, com certificado; nota de verdade é pela tela |
| `certificado_arquivo` / `certificado_ocupado` | Arquivo grande demais ou leitura demorada / muitos certificados ao mesmo tempo | Confira o arquivo .pfx; tente de novo em alguns minutos |
| `escopo` | O token não tem permissão para isso | Algumas coisas são só na tela (produção, cópias de e-mail); para o resto, gere o token de novo em Implantação com IA → Avançado |
| `chave_invalida` | Token errado, revogado ou vencido (vale 7 dias) | Gere outro |

## Recusas da prefeitura na nota de teste
O NotaPlano explica cada código oficial da prefeitura na própria nota (o que é, o que fazer, quem resolve). Os mais comuns na
implantação: número repetido (numeração), código do serviço não liberado no cadastro da empresa na prefeitura, alíquota diferente da
cadastrada. Leve a explicação para a conversa e ajuste o perfil ou a numeração.

## Erros da importação (clientes, notas recorrentes, modelos de nota)
| Código | O que é | O que fazer |
|---|---|---|
| `so_na_tela` | Um campo que a IA não manda (os e-mails de aviso da recorrência) | Tire o campo: a pessoa põe na tela, ao conferir |
| `tomador_junto` | A recorrência veio com `tomador_documento`/`tomador_id` | Mande o cliente junto em `tomador` (CNPJ e nome): você não lê o cadastro dela |
| `lote_grande` | Mais de 500 clientes, 200 recorrências ou 100 modelos | Divida em partes |
| `valor_invalido` | Valor ambíguo ou com casas demais | Use 1500.5, "1500.50" ou "1.500,50" |
| `conta` | O módulo de clientes ou de recorrências está desligado, ou a conta está no modo "só emitir e sair" | A pessoa liga em Configurações → Módulos (na tela) |
| `nada_para_propor` | Nenhuma linha pode entrar | Corrija as linhas recusadas (veja `recusas`) e proponha de novo |
| `muitas_pendentes` | Já há 20 importações esperando a pessoa | Peça para ela aplicar ou descartar as que estão em Importações para conferir |
| linha recusada "pessoa física (CPF)" | Cliente pessoa física não vai pela IA (Termos, item 1.12) | Tire a linha; a pessoa importa na tela (Tomadores → Importar) |

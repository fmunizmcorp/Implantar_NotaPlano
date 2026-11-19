# O manifesto de implantação

Um JSON com as empresas que emitem nota e os perfis fiscais. Nos exemplos, `CNPJ_DA_EMPRESA` é para trocar pelo CNPJ de verdade.
Formato oficial (JSON Schema):
https://www.notaplano.com.br/api/implantacao.schema.json — exemplos prontos em [../manifesto/exemplos/](../manifesto/exemplos/).

```json
{
  "versao": 1,
  "empresas": [ { "cnpj": "…", "nome": "…", "regime_tributario": "nao_optante", "...": "…", "numeracao": { "serie": "1", "proximo": 1 } } ],
  "perfis": [ { "nome": "…", "tributacao_nacional": "040101", "aliq_iss": 2 } ]
}
```

## Regras
- **Campo com nome errado é recusado** (a conferência diz quais existem). Não acrescente campos.
- **A empresa é achada pelo CNPJ e o perfil pelo nome.** Aplicar de novo **altera** em vez de duplicar — dá para corrigir e aplicar
  quantas vezes precisar. Numa empresa já cadastrada, só os campos enviados mudam, e **a numeração nunca volta** (número menor que o
  de hoje: fica o de hoje).
- **A conferência mostra cada campo que muda** e destaca o que entra em toda nota (textos) — a pessoa confere antes de aplicar.
- **Tudo é conferido antes de gravar.** Com qualquer erro, nada é gravado.
- **O certificado nunca vai no manifesto.**

## Empresa (`empresas[]`)
| Campo | Obrigatório | Como preencher |
|---|---|---|
| `cnpj` | sim | 14 dígitos (com ou sem pontuação; o CNPJ alfanumérico vai com as letras) |
| `nome` | sim | Nome curto que aparece no NotaPlano |
| `razao_social` | | Do cartão CNPJ |
| `regime_tributario` | sim (empresa nova) | `"nao_optante"`, `"mei"` ou `"simples"` |
| `municipio` ou `prefeitura_ibge` | sim | `"Cidade/UF"` (ex.: `"Brasília/DF"`) ou o código IBGE de 7 dígitos |
| `im` | quase sempre | Inscrição municipal |
| `cnae`, `cnaes_secundarios` | recomendado | 7 dígitos; servem para sugerir os serviços |
| `endereco` | sim | `{ logradouro, numero, complemento, bairro, cep, cidade, uf }` |
| `email_contato` | | E-mail da empresa |
| `modo_emissao` | | `"a1"` (com certificado, o padrão) ou `"portal"` (sem certificado) |
| `texto_padrao_discriminacao` | | Texto que entra em toda nota (sem dados de cliente) |
| `preferencias` | | `{ email_tomador: true }` (e-mail automático ao cliente). Os e-mails que recebem **cópia** das notas e os **alertas** só se configuram na tela |
| `regime_especial` | | "Nenhum" (o comum), "Ato Cooperado (Cooperativa)", "Estimativa", "Microempresa Municipal", "Notário ou Registrador", "Profissional Autônomo" ou "Sociedade de Profissionais" — com o contador |
| `simples` | só Simples | `{ atividade, regime_ibscbs, regime_apuracao }` — com o contador |
| `condominio_edilicio` | só condomínio | `true` |
| `numeracao` | recomendado | `{ "serie": "1", "proximo": 1 }` — quem já emitia: o último número + 1 (na mesma série). No Emissor Nacional a série vai de 1 a 49999 |
| `ambiente` | não use | A empresa nova nasce em homologação; produção é depois da nota de teste |

## Perfil fiscal (`perfis[]`) — alíquotas em PERCENTUAL (2 = 2%)
| Campo | Como preencher |
|---|---|
| `nome` | Ex.: "Consulta — pessoa física", "Consulta — convênio X" |
| `tributacao_nacional` | Código do serviço, 6 dígitos ([SERVICOS.md](SERVICOS.md)) |
| `nbs` | 9 dígitos — em branco se o serviço tem um só (o NotaPlano completa) |
| `ctrib_mun` | Código municipal — no DF fica em branco (completado) se a empresa ainda não tem |
| `aliq_iss`, `retem_iss` | ISS: alíquota e se o cliente retém (`true`/`false`) |
| `retem_pis`/`aliq_pis`, `retem_cofins`/`aliq_cofins`, `retem_csll`/`aliq_csll`, `retem_irrf`/`aliq_irrf`, `retem_cp`/`aliq_cp` | Retenções federais em percentual: PIS 0,65, COFINS 3, CSLL 1 (nunca 4,65 na CSLL — veja [DOCUMENTOS.md](DOCUMENTOS.md)), IRRF em geral 1, 1,5 ou 4,8, INSS 11 ou 3,5. Fração (0,0065) é recusada |
| `indicador_operacao`, `situacao_tributaria`, `classificacao_tributaria` | IBS/CBS — em branco quando a tabela oficial tem uma resposta só |
| `trib_aprox_federal`, `trib_aprox_estadual`, `trib_aprox_municipal`, `fonte_trib_aproximados` | Tributos aproximados (IBPT) |
| `discriminacao_modelo` | Modelo da descrição (sem dados de cliente) |
| `padrao_pf` | `true` no perfil de pessoa física padrão (só um). Pelo token, só na implantação do zero (conta sem padrão e sem empresa em produção); depois disso, marcar, religar ou mudar o padrão é na tela |
| `ativo` | `false` para desativar um perfil existente |

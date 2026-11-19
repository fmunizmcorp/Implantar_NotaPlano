# O que tirar de cada documento

A pessoa pode mandar fotos, PDFs ou arquivos. Leia, preencha o manifesto e **mostre o que entendeu** para ela confirmar.

## Cartão CNPJ (comprovante de inscrição da Receita Federal)
| No cartão | No manifesto (empresa) |
|---|---|
| Número de inscrição (CNPJ) | `cnpj` |
| Nome empresarial | `razao_social` |
| Nome de fantasia (ou um nome curto que a pessoa preferir) | `nome` |
| Logradouro, número, complemento, bairro, CEP | `endereco.logradouro`, `numero`, `complemento`, `bairro`, `cep` |
| Município e UF | `municipio` ("Cidade/UF") e `endereco.cidade`, `endereco.uf` |
| Atividade econômica principal (CNAE, 7 dígitos) | `cnae` |
| Atividades secundárias | `cnaes_secundarios` |

O cartão **não diz** o regime tributário nem a inscrição municipal: pergunte. O MEI aparece no Portal do Empreendedor; a opção pelo
Simples Nacional, na consulta do Simples (o contador sabe).

## Nota fiscal de serviço antiga (XML ou PDF) — a melhor fonte para o perfil fiscal
Use uma nota **aceita** pela prefeitura, do mesmo tipo de cliente do perfil (ex.: uma de pessoa física, outra de convênio).

| Na nota (XML do padrão nacional) | O que é | No manifesto |
|---|---|---|
| `cTribNac` | Código do serviço (6 dígitos) | perfil `tributacao_nacional` |
| `cTribMun` | Código municipal do serviço | perfil `ctrib_mun` |
| `cNBS` | NBS (9 dígitos) | perfil `nbs` |
| `pAliq` (ou a alíquota impressa) | Alíquota do ISS | perfil `aliq_iss` |
| `tpRetISSQN` = 2 ou 3 | ISS retido pelo tomador | perfil `retem_iss: true` |
| `vRetIRRF`, `vRetCSLL`, `vRetCP`, PIS/COFINS retidos | Retenções federais (calcule o % sobre o valor do serviço) | perfil `retem_*` e `aliq_*` |
| `CST`, `cClassTrib`, `cIndOp` (grupo IBS/CBS) | Classificação da reforma tributária | perfil `situacao_tributaria`, `classificacao_tributaria`, `indicador_operacao` (ou deixe em branco: o NotaPlano completa) |
| `serie` e `nDPS` (número da DPS) | Série e número do último envio | empresa `numeracao.serie` e `numeracao.proximo` = número + 1 |
| `xDescServ` | A descrição do serviço | perfil `discriminacao_modelo` (um modelo, sem dados do cliente) |

Atenção: o número que importa para a numeração é o **da DPS** (ou do RPS), não o número da NFS-e que a prefeitura deu. Se houver
dúvida, pergunte qual foi o último número enviado.

## Cadastro na prefeitura
- **Inscrição municipal** → empresa `im`.
- **Atividades liberadas / código municipal** → perfil `ctrib_mun` (no DF, ISS.net: o código sem zero à esquerda, ex.: 040101 → 401).

## Nunca peça
O certificado digital, a senha dele ou qualquer outra senha.

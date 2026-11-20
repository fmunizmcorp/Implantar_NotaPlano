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
Use uma nota **aceita** pela prefeitura, do mesmo tipo de cliente do perfil (ex.: uma de pessoa física, outra de convênio). **Antes, peça à pessoa
para apagar ou cobrir os dados do cliente** (nome, CPF/CNPJ, endereço, e-mail) — e a descrição, se ela citar pessoas: a configuração só precisa dos campos do serviço e dos
impostos. Se vierem dados de cliente, não os use, não os repita e não os ponha no manifesto.

| Na nota (XML do padrão nacional) | O que é | No manifesto |
|---|---|---|
| `cTribNac` | Código do serviço (6 dígitos) | perfil `tributacao_nacional` |
| `cTribMun` | Código municipal do serviço | perfil `ctrib_mun` |
| `cNBS` | NBS (9 dígitos) | perfil `nbs` |
| `pAliq` (ou a alíquota impressa) | Alíquota do ISS | perfil `aliq_iss` |
| `tpRetISSQN` = 2 | ISS retido pelo tomador | perfil `retem_iss: true` (= 1: não retido. = 3, retido por intermediário: o NotaPlano não emite assim — fale com o suporte) |
| `tpRetPisCofins` + `vRetCSLL` | Quais retenções de PIS/COFINS/CSLL houve — **atenção abaixo** | perfil `retem_pis`/`aliq_pis`, `retem_cofins`/`aliq_cofins`, `retem_csll`/`aliq_csll` |
| `vRetIRRF` | IRRF retido (calcule o % sobre o valor do serviço: em geral 1%, 1,5% ou 4,8%) | perfil `retem_irrf`, `aliq_irrf` |
| `vRetCP` | INSS retido (em geral 11% ou 3,5%) | perfil `retem_cp`, `aliq_cp` |
| `CST`, `cClassTrib`, `cIndOp` (grupo IBS/CBS) | Classificação da reforma tributária | perfil `situacao_tributaria`, `classificacao_tributaria`, `indicador_operacao` (ou deixe em branco: o NotaPlano completa) |
| `serie` e `nDPS` (número da DPS) | Série e número do último envio | empresa `numeracao.serie` e `numeracao.proximo` = número + 1 |
| `xDescServ` | A descrição do serviço | perfil `discriminacao_modelo` (um modelo, sem dados do cliente) |

**Atenção — PIS, COFINS e CSLL na nota nacional:** o campo `vRetCSLL` **pode trazer a soma** de PIS + COFINS + CSLL retidos (é assim
no ISS.net do DF e no NotaPlano). Se `vPis` e `vCofins` vierem preenchidos, a CSLL é só o `vRetCSLL`. Quem diz o que foi retido é o
`tpRetPisCofins`: **3** = os três (PIS 0,65% + COFINS 3% + CSLL 1% = 4,65%, Lei 10.833/2003, art. 31); **4** = PIS e COFINS;
**5** = só PIS; **6** = só COFINS; **7** = COFINS e CSLL; **8** = só CSLL; **9** = PIS e CSLL; **0** = nenhum; **1** = PIS e COFINS
retidos e **2** = PIS e COFINS não retidos (códigos do leiaute mais antigo).
Nunca ponha 4,65 em `aliq_csll`: separe em `aliq_pis` 0,65, `aliq_cofins` 3 e `aliq_csll` 1 (a conferência recusa CSLL acima de 1%).

**Numeração:** o número que importa é o **da DPS** (`nDPS`) ou do RPS, não o número da NFS-e que a prefeitura deu. O próximo é o último
\+ 1, na mesma série. Se havia notas feitas no site ou no aplicativo do Emissor Nacional, elas usam uma série própria do site: o
NotaPlano usa outra série (ex.: 1) e pode começar do 1. Se houver dúvida, pergunte qual foi o último número enviado e em que série.

## Cadastro na prefeitura
- **Inscrição municipal** → empresa `im`.
- **Atividades liberadas / código municipal** → perfil `ctrib_mun` (no DF, ISS.net: o código sem zero à esquerda, ex.: 040101 → 401).

## Nunca peça
O certificado digital, a senha dele ou qualquer outra senha.

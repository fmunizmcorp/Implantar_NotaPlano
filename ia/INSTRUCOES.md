# Instruções para a IA que vai implantar o NotaPlano

Você (ChatGPT, Claude ou outra IA) vai ajudar uma pessoa a configurar o **NotaPlano**, o emissor de nota fiscal de serviço (NFS-e) da
empresa dela, do zero até a primeira nota. A pessoa em geral **não é técnica**. Leia este arquivo inteiro antes de começar.

## O seu papel
- Fale em **português simples**, sem siglas sem explicação. Uma pergunta por vez. Confirme o que entendeu antes de seguir.
- Quando a pessoa mandar um documento (cartão CNPJ, nota fiscal antiga, cadastro na prefeitura), **leia e preencha você** o que
  der — veja [DOCUMENTOS.md](DOCUMENTOS.md) — e mostre um resumo para ela confirmar.
- No fim, entregue o **manifesto de implantação**: um JSON único, no formato de [MANIFESTO.md](MANIFESTO.md)
  (schema: https://www.notaplano.com.br/api/implantacao.schema.json). Ela cola em **Implantação com IA** no NotaPlano
  (https://www.notaplano.com.br/app/implantacao-ia), clica em **Conferir** e depois em **Aplicar**.

## Regras que você NUNCA quebra
1. **Nunca peça o certificado digital nem a senha dele.** O certificado A1 (arquivo .pfx) e a senha são enviados pela própria pessoa
   na tela da empresa no NotaPlano. Se ela tentar mandar para você, diga para não mandar.
2. **Nunca peça a senha do NotaPlano**, de e-mail, do gov.br ou da prefeitura.
3. **Não invente** alíquota, código de serviço, regime tributário, inscrição municipal ou número de nota. Se não tiver certeza,
   pergunte; se a pessoa não souber, peça para confirmar com o contador — e deixe isso claro no resumo.
4. **Alíquotas em percentual:** 2 = 2%; 0,65 = 0,65%. Nunca 0,02 para dizer 2%.
5. Toda empresa nova nasce em **HOMOLOGAÇÃO** (teste, sem valor fiscal). Produção só depois da **nota de teste aceita** e com o
   "sim" da pessoa.
6. O manifesto tem dados da empresa: diga à pessoa para **não publicar** o manifesto em lugar aberto.

## O caminho (resumo — detalhe em [PASSO-A-PASSO.md](PASSO-A-PASSO.md))
1. **Entender o negócio:** o que a empresa faz, para quem emite nota (pessoas físicas, empresas, planos de saúde, governo), em que
   cidade. Perguntas prontas em [PERGUNTAS.md](PERGUNTAS.md).
2. **Cada empresa que emite (cada CNPJ):** nome, CNPJ, endereço, município, inscrição municipal, regime tributário, CNAE, como vai
   emitir (com certificado ou sem certificado) e **a numeração** (se já emitia nota por outro sistema: o número da ÚLTIMA nota; o
   próximo é esse + 1).
3. **Cada tipo de nota (perfil fiscal):** um perfil para cada combinação de serviço × tipo de cliente que muda o imposto. Ex.: "Consulta
   — pessoa física" (sem retenções) e "Consulta — convênio" (com as retenções do convênio).
4. **Montar o manifesto**, mostrar um resumo em linguagem simples e entregar o JSON num bloco só.
5. **Conferir:** se o NotaPlano apontar erros, a pessoa cola a lista aqui; corrija e entregue o JSON inteiro de novo. Os códigos de
   erro estão em [ERROS.md](ERROS.md).
6. **Depois de aplicar**, guie: enviar o certificado na tela da empresa → emitir a nota de teste → passar para produção (no NotaPlano,
   botão na página da empresa; ou pela API com a confirmação dela).

## Regras fiscais que você usa para montar os perfis (na dúvida, contador)
- **Código do serviço** (`tributacao_nacional`): 6 dígitos da lista nacional (ex.: 040101 = Medicina). A lista completa está em
  [SERVICOS.md](SERVICOS.md). Se houver uma nota antiga, o código está nela (`cTribNac`).
- **Deixe em branco o que o NotaPlano completa sozinho pela tabela oficial:** o NBS quando o serviço tem um só, os códigos do IBS/CBS
  (`indicador_operacao`, `situacao_tributaria`, `classificacao_tributaria`) quando a tabela tem uma resposta só, a redução oficial e,
  no DF, o código municipal. A conferência mostra o que foi completado; se o serviço tiver mais de uma opção, ela avisa e você pergunta.
- **ISS:** entre 2% e 5% (Lei Complementar 116, art. 8º-A). Abaixo de 2% só no Simples Nacional/MEI e nos itens 7.02, 7.05 e 16.01.
  No Simples, a alíquota é a da faixa da empresa (o contador sabe; está no extrato do Simples).
- **Pessoa física como cliente:** não retém nada — nem ISS nem tributo federal.
- **ISS retido / retenções federais (PIS, COFINS, CSLL, IRRF, INSS):** dependem do serviço, de quem paga e do contrato. O jeito mais
  seguro: **copiar de uma nota antiga já aceita** para o mesmo tipo de cliente. Sem nota antiga, pergunte ao contador. Marcar "retém"
  exige a alíquota (e vice-versa).
- **MEI:** sem ISS retido e sem retenções federais. **Simples Nacional (ME/EPP):** em geral sem retenção de PIS/COFINS/CSLL/IRRF.
- **Tributos aproximados** (a "Lei da Transparência", 12.741/2012): os percentuais federal, estadual e municipal do serviço e a fonte
  (ex.: IBPT). Peça ao contador ou à tabela do IBPT; sem eles, cada nota fica para revisão antes de enviar.
- **Condomínio edilício:** marque `condominio_edilicio: true` na empresa — o NotaPlano cria o perfil pronto das taxas condominiais.
- **Serviços que o NotaPlano ainda não emite** estão marcados em [SERVICOS.md](SERVICOS.md) (obras, eventos e alguns outros). Avise a
  pessoa antes de seguir.

## Se você consegue chamar APIs
Com o **token de implantação** que a pessoa gera em Implantação com IA → Avançado, você pode ler a situação e aplicar direto:
[API.md](API.md). Mesmo assim, confirme com ela antes de aplicar e **antes de passar para produção**. O token nunca envia
certificado.

<!-- GERADO por fmunizmcorp/emissor_nfe (scripts/implantacao-ia/gerar-kit.js) a partir do código do NotaPlano — não edite aqui -->
# Conector MCP do NotaPlano

- Endereço: `https://www.notaplano.com.br/mcp` (Streamable HTTP, JSON-RPC). `initialize` e `tools/list` sem login; as ferramentas pedem `Authorization: Bearer np_live_…` (o token de implantação, 7 dias). Cada ferramenta chama a API do NotaPlano com o mesmo token — os mesmos escopos e a mesma auditoria.
- Claude Code: `claude mcp add --transport http notaplano https://www.notaplano.com.br/mcp --header "Authorization: Bearer np_live_…"`
- **Conector de um clique (login com a conta, sem token)** — OAuth 2.1 com PKCE e registro dinâmico; o endereço é o do aplicativo: `https://app.notaplano.com.br/mcp`.
  - **Claude** (claude.ai, aplicativo do computador ou do celular): Personalizar → Conectores → Adicionar conector personalizado (*Customize → Connectors → Add custom connector*), cole `https://app.notaplano.com.br/mcp`, clique em Adicionar e depois em Conectar. Funciona até no plano grátis (1 conector).
  - **ChatGPT**: Configurações → Aplicativos e conectores → ligue o **modo desenvolvedor** e crie um conector com `https://app.notaplano.com.br/mcp` e autenticação **OAuth**. Ressalva: hoje o ChatGPT só deixa um conector desses **gravar** (aplicar o manifesto, emitir a nota de teste) nos planos **Business e Enterprise/Edu**, criado pelo administrador do workspace; nos outros planos, siga pelo manifesto colado na tela.
  - Abre a página do NotaPlano: a pessoa entra, confere o aplicativo, o endereço de volta, a empresa e o que a IA pode e não pode, digita a senha e clica em **Permitir**. Só administrador ou dono da empresa, com o módulo API ligado.
  - O acesso é o MESMO do token de implantação (escopos `config:ler`, `config:escrever`, `notas:emitir_teste`, `importacoes:propor`), vale 7 dias e aparece em Configurações → Integrações como "IA (conector): …" (revogável lá). Vencido ou revogado, o /mcp responde 401 e a IA pede para conectar de novo.
  - Descoberta: `https://app.notaplano.com.br/.well-known/oauth-protected-resource/mcp` e `https://app.notaplano.com.br/.well-known/oauth-authorization-server`.
- Nenhuma ferramenta envia certificado. Confirme com a pessoa antes de aplicar o manifesto e antes de passar para produção.

## Ferramentas
### situacao — Situação da implantação
O que falta em cada empresa emissora para emitir (cadastro, prefeitura, inscrição municipal, certificado, numeração, perfil fiscal, nota de teste, produção) e o próximo passo, com o link da tela. Comece por aqui.

Só leitura. Argumentos: `emitente_id` — Opcional: só esta empresa emissora.

### buscar_servico — Buscar serviço na lista nacional
Busca o código do serviço (6 dígitos da lista nacional da NFS-e) pelo nome, ou mostra o detalhe de um código: NBS possíveis e os códigos do IBS/CBS da tabela oficial.

Só leitura. Argumentos: `busca` — Nome do serviço (ex.: consulta médica); `codigo` — Código de 6 dígitos (ex.: 040101).

### servicos_sugeridos — Serviços sugeridos pelo CNAE
Os serviços que combinam com os CNAEs das empresas emissoras já cadastradas (ou de uma).

Só leitura. Argumentos: `emitente_id` — Opcional: só esta empresa emissora.

### ver_empresa — Ver empresa emissora
Os dados de uma empresa emissora: cadastro, regime, endereço, preferências, certificado (só validade) e numeração.

Só leitura. Argumentos: `emitente_id` (obrigatório) — A empresa emissora.

### conferir_manifesto — Conferir o manifesto (não grava)
Confere o manifesto de implantação sem gravar nada: para cada empresa (pelo CNPJ), numeração e perfil (pelo nome), se vai criar ou alterar, o que o NotaPlano completa pela tabela oficial e os erros. Formato: https://www.notaplano.com.br/api/implantacao.schema.json

Só leitura. Argumentos: `manifesto` (obrigatório) — O manifesto: {"versao": 1, "empresas": [...], "perfis": [...]}.

### aplicar_manifesto — Aplicar o manifesto
Aplica o manifesto de implantação: confere tudo antes (com erro, nada é gravado) e grava empresas (novas em HOMOLOGAÇÃO), numeração e perfis. Aplicar de novo altera, não duplica. Mostre o resumo à pessoa e aplique só com o "sim" dela.

Grava (confirme antes). Argumentos: `manifesto` (obrigatório) — O manifesto conferido.

### conferir_importacao — Conferir uma importação (não grava)
Confere, sem gravar, uma importação de clientes, notas recorrentes ou modelos de nota, com as mesmas regras da tela do NotaPlano. Volta quantas linhas entrariam e os erros pelo número da linha. Clientes: só empresa (CNPJ) — os campos documento, nome, razao_social, im, email (use o do setor, ex.: financeiro@), telefone, cep, logradouro, numero, complemento, bairro, cidade, uf, codigo_municipio e perfil (o nome de um perfil fiscal que já existe). Recorrências: nome, emitente_id ou emitente_cnpj, tomador {documento (CNPJ), razao_social}, perfil, valor, dia (1 a 28), inicio, fim, acao (rascunho, emitir ou cobrar_antes), descricao, reajuste_pct (em PERCENTUAL: 4,5 = 4,5%) e reajuste_mes. Modelos: nome, emitente, perfil, tomador (opcional), valor_servicos, descricao, informacoes_complementares.

Só leitura. Argumentos: `tipo` (obrigatório) — O que importar; `itens` (obrigatório) — As linhas (até 500 clientes, 200 recorrências ou 100 modelos).

### propor_importacao — Propor a importação (a pessoa aplica na tela)
Deixa a importação PRONTA para a pessoa conferir: nada entra no cadastro até ela abrir o link "tela" (Implantação com IA → Importações para conferir), ver linha a linha o que entra e o que muda e clicar em Aplicar. As recorrências entram DESLIGADAS (ela liga cada uma na tela). Confira antes com conferir_importacao e proponha só com o "sim" da pessoa. Depois diga a ela: "deixei a importação pronta; abra Implantação com IA → Importações para conferir e clique em Aplicar". Vence em 7 dias.

Grava (confirme antes). Argumentos: `tipo` (obrigatório) — O que importar; `itens` (obrigatório) — As linhas conferidas.

### ver_importacao — Ver uma importação proposta
A situação de uma importação proposta: pendente (esperando a pessoa), aplicada (só o total de linhas processadas — o que entrou, a pessoa vê na tela), descartada ou vencida.

Só leitura. Argumentos: `importacao_id` (obrigatório) — A importação (importacao_id de propor_importacao).

### emitir_nota_teste — Emitir a nota de TESTE (homologação)
Cria e emite uma nota de TESTE, sem valor fiscal, numa empresa que ainda está em HOMOLOGAÇÃO (recusa se a empresa já estiver em produção). Precisa do certificado já enviado na tela. Tomador: os dados em tomador {documento, nome, email, cep, logradouro, numero, bairro, cidade, uf} — use um tomador de teste (a empresa ou alguém que concorde); o cadastro de clientes não é usado, e a nota de teste não vai por e-mail.

Grava (confirme antes). Argumentos: `emitente_id` (obrigatório) — A empresa emissora (em homologação); `perfil_id` (obrigatório) — O perfil fiscal; `tomador` (obrigatório) — Os dados do tomador de teste; `valor_servicos` (obrigatório) — Valor da nota de teste em reais (ex.: 10); `competencia` — Opcional: AAAA-MM-DD (padrão: hoje, em Brasília); `descricao` — Descrição do serviço (opcional).

<!-- GERADO por fmunizmcorp/emissor_nfe (scripts/implantacao-ia/gerar-kit.js) a partir do código do NotaPlano — não edite aqui -->
# API de configuração do NotaPlano (para a IA que chama APIs)

- Endereço: `https://www.notaplano.com.br/api/v1` · documentação completa: https://www.notaplano.com.br/api/docs · OpenAPI: https://www.notaplano.com.br/api/openapi.json
- Autenticação: `Authorization: Bearer np_live_…` — o **token de implantação** que a pessoa gera em Implantação com IA → Avançado (vale 7 dias; escopos: `config:ler`, `config:escrever`, `notas:emitir_teste`). Ele **não** envia certificado.
- Corpo em JSON (`Content-Type: application/json`). Erros: `{"erro": {"codigo", "mensagem", "campo"}}` — veja [ERROS.md](ERROS.md).
- Alíquotas em PERCENTUAL (2 = 2%) nas rotas de configuração. Campo desconhecido é recusado.
- Ordem: `GET /implantacao/situacao` → `POST /implantacao/simular` → `POST /implantacao` → (certificado na tela) → nota de teste → `POST /emitentes/{id}/producao` com `{"confirmo": true}` só depois do "sim" da pessoa.

## Escopos
- `notas:ler` — Ler notas, XML e DANFSe; acompanhar lotes
- `notas:emitir` — Criar, emitir, substituir e desistir de notas; lotes; conferir na prefeitura e registrar a NFS-e oficial achada; fila de emissão; nota espelho do histórico
- `cadastros:ler` — Empresas emissoras e perfis fiscais
- `config:ler` — Ler a configuração: empresa emissora e perfil fiscal em detalhe, certificado (só validade e titular), serviços da tabela nacional e os sugeridos pelo CNAE, a situação da implantação (o próximo passo); testar a conexão com a prefeitura
- `config:escrever` — Configurar: cadastrar e alterar empresas emissoras (a nova nasce sempre em HOMOLOGAÇÃO), numeração, desativar e reativar, perfis fiscais
- `certificados:escrever` — Enviar o certificado digital A1 (.pfx em base64 + senha), guardado cifrado como na tela. Dê este escopo só ao sistema da empresa — nunca a uma IA
- `emitentes:producao` — Passar uma empresa emissora de HOMOLOGAÇÃO para PRODUÇÃO (as notas passam a valer), só com a nota de teste autorizada e "confirmo": true; voltar para homologação ou desativar uma empresa em produção
- `notas:emitir_teste` — Criar e emitir nota de TESTE só em empresa emissora em HOMOLOGAÇÃO (sem valor fiscal) — o escopo do token de implantação; nunca emite de verdade

## Rotas
### GET /eu
Quem sou: empresa, escopos desta chave, empresas emissoras e perfis ativos

Resposta (exemplo):
```json
{
  "empresa": {
    "id": "uuid",
    "nome": "Minha Empresa",
    "status": "ativo"
  },
  "chave": {
    "nome": "ERP",
    "escopos": [
      "notas:ler",
      "notas:emitir"
    ],
    "limite_por_min": 60
  },
  "emitentes": [
    {
      "id": "uuid",
      "nome": "Matriz",
      "cnpj": "CNPJ_DA_EMPRESA"
    }
  ],
  "perfis": [
    {
      "id": "uuid",
      "nome": "Convênio — ISS 2%"
    }
  ]
}
```

### POST /notas
Cria a nota (calcula e confere; não envia). Com notas:emitir_teste, só em empresa em homologação. Tomador: `tomador_id` (cadastrado) e/ou `tomador` (dados). Com os dois, o que vier preenchido em `tomador` completa/corrige o cadastrado para esta nota e atualiza o cadastro (modo completo; `salvar_tomador: false` só a nota); documento diferente do cadastrado → 422 `tomador_documento_diverge`. NFS-e do condomínio (99.05.01): o grupo `condominio` (ver a seção da documentação) no lugar de `valor_servicos`.

Escopo: `notas:emitir`

Corpo (exemplo):
```json
{
  "emitente_id": "uuid",
  "perfil_id": "uuid",
  "tomador": {
    "documento": "CPF_OU_CNPJ",
    "im": "INSCRICAO_MUNICIPAL",
    "email": "contato@empresa.example"
  },
  "valor_servicos": 1500,
  "competencia": "2026-09-30",
  "referencia": "Pedido 123",
  "descricao": "Consultas de setembro"
}
```

Resposta (exemplo):
```json
{
  "nota_id": "uuid",
  "status": "conferida",
  "calculo": {},
  "conferencia": {
    "apto": true,
    "itens": []
  },
  "emitir_em": "/api/v1/notas/{id}/emitir"
}
```

### GET /notas
Lista paginada (50 por página)

Escopo: `notas:ler`

Parâmetros: `status`, `emitente_id`, `de`, `ate`, `documento`, `pagina`

Resposta (exemplo):
```json
{
  "itens": [
    {
      "nota_id": "uuid",
      "status": "emitida",
      "numero_nfse": "4601",
      "chave_acesso": "53001082…",
      "codigo_verificacao": "ABC123",
      "valor_servicos": 1500,
      "competencia": "2026-09-30"
    }
  ],
  "pagina": 1
}
```

### GET /notas/{id}
Detalhe com cálculo, conferência e dados da NFS-e

Escopo: `notas:ler`

Resposta (exemplo):
```json
{
  "nota_id": "uuid",
  "status": "emitida",
  "numero_nfse": "4601",
  "chave_acesso": "53001082…",
  "codigo_verificacao": "ABC123",
  "valor_servicos": 1500,
  "competencia": "2026-09-30"
}
```

### POST /notas/{id}/emitir
Transmite à prefeitura (com notas:emitir_teste, só a nota de empresa em homologação). Com {"fila": true} (ou ?fila=1 / "Prefer: respond-async") a nota vai para a FILA DE EMISSÃO e a resposta é 202 na hora; sem isso emite agora — e, se o servidor estiver no limite de emissões ao mesmo tempo ou a prefeitura fora do ar, também vai para a fila (202 com "motivo"). Se a última tentativa desta nota ficou SEM RESPOSTA da prefeitura e o número se perdeu, a resposta é 409 "conferir_antes": confira na prefeitura e, se a nota não saiu, envie {"confirmar_reenvio": "<confirmar_reenvio_token do 409>"} com uma NOVA Idempotency-Key (a resposta 409 fica guardada na chave anterior). Pela fila, o item termina com "codigo": "conferir_antes" (GET /fila/{id}) e sem o código de confirmação: depois de conferir, chame esta rota SEM fila para receber o 409 com o confirmar_reenvio_token. Sem fila, com a prefeitura lenta a resposta pode levar até ~90 s: use tempo-limite de 100 s ou mais (ou {"fila": true}); se a sua conexão cair antes da resposta, NÃO crie outra nota — consulte GET /notas/{id} (a emissão continua no servidor) antes de repetir

Escopo: `notas:emitir`

Corpo (exemplo):
```json
{
  "fila": true
}
```

Resposta (exemplo):
```json
{
  "ok": true,
  "nota": {
    "nota_id": "uuid",
    "status": "emitida",
    "numero_nfse": "4601",
    "chave_acesso": "53001082…",
    "codigo_verificacao": "ABC123",
    "valor_servicos": 1500,
    "competencia": "2026-09-30"
  }
}
```

### GET /emitentes
Empresas emissoras (CNPJ, IM, ambiente, validade do certificado, numeração)

Escopo: `cadastros:ler`

Resposta (exemplo):
```json
{
  "itens": [
    {
      "id": "uuid",
      "nome": "Matriz",
      "cnpj": "CNPJ_DA_EMPRESA",
      "ambiente": "producao",
      "certificado_validade": "2027-03-01"
    }
  ]
}
```

### GET /emitentes/{id}/numeracao
Numeração da empresa emissora: série, próximo número, faixa liberada pela prefeitura (início, fim, livres), quando foi lida e o alerta (faixa perto do fim)

Escopo: `cadastros:ler`

### POST /emitentes/{id}/numeracao/sincronizar
Relê na prefeitura a série e a faixa liberada e alinha o próximo número (só consulta; nunca recua o número). 422 sem_suporte quando a prefeitura não informa a numeração; 503 se não respondeu. Limite: 10 a cada 15 min por empresa

Escopo: `prefeitura:consultar`

Resposta (exemplo):
```json
{
  "ok": true,
  "serie": "3",
  "faixa_inicio": 3301,
  "faixa_fim": 3800,
  "proximo": 3441,
  "disponiveis": 359,
  "alertas": []
}
```

### GET /perfis
Perfis fiscais (alíquotas e retenções em FRAÇÃO: 0.02 = 2%; o detalhe em GET /perfis/{id} vem em percentual)

Escopo: `cadastros:ler`

Parâmetros: `ativos`

### GET /implantacao/situacao
Situação da implantação, por empresa emissora: cadastro, prefeitura, inscrição municipal, certificado, numeração, perfil fiscal, nota de teste e produção — cada item com o que fazer e o link da tela, e o próximo passo.

Escopo: `config:ler`

Parâmetros: `emitente_id`

Resposta (exemplo):
```json
{
  "empresas": [
    {
      "emitente_id": "uuid",
      "nome": "Matriz",
      "ambiente": "homologacao",
      "pronta": false,
      "itens": [
        {
          "item": "certificado",
          "ok": false,
          "obrigatorio": true,
          "texto": "Envie o certificado digital A1 (.pfx) da empresa NA TELA do NotaPlano…",
          "tela": "https://www.notaplano.com.br/app/emitentes/uuid#certificado"
        }
      ],
      "proximo_passo": {
        "item": "certificado",
        "texto": "…",
        "tela": "…"
      }
    }
  ],
  "proximo_passo": {
    "emitente_id": "uuid",
    "item": "certificado",
    "texto": "…"
  },
  "tudo_pronto": false
}
```

### POST /implantacao/simular
Confere o MANIFESTO de implantação sem gravar nada: para cada empresa (achada pelo CNPJ), numeração e perfil (achado pelo nome), se vai criar ou alterar e o que está errado. JSON Schema público em /api/implantacao.schema.json

Escopo: `config:ler`

Corpo (exemplo):
```json
{
  "versao": 1,
  "empresas": [
    {
      "cnpj": "CNPJ_DA_EMPRESA",
      "nome": "Matriz",
      "regime_tributario": "nao_optante",
      "municipio": "Brasília/DF",
      "numeracao": {
        "serie": "1",
        "proximo": 101
      }
    }
  ],
  "perfis": [
    {
      "nome": "Consulta — pessoa física",
      "tributacao_nacional": "040101",
      "nbs": "123012200",
      "aliq_iss": 2
    }
  ]
}
```

Resposta (exemplo):
```json
{
  "ok": true,
  "itens": [
    {
      "tipo": "empresa",
      "chave": "CPF_OU_CNPJ",
      "acao": "criar",
      "ok": true
    }
  ],
  "avisos": []
}
```

### POST /implantacao
Aplica o manifesto de implantação: confere TUDO antes (com erro, 422 e nada é gravado) e grava na ordem empresas → numeração → perfis, com as regras da tela. Aplicar de novo não duplica (empresa pelo CNPJ, perfil pelo nome). Empresa nova nasce em HOMOLOGAÇÃO; o certificado não vai no manifesto. 409 se parou no meio (diz onde)

Escopo: `config:escrever`

Resposta (exemplo):
```json
{
  "ok": true,
  "aplicado": true,
  "itens": [
    {
      "tipo": "empresa",
      "acao": "criar",
      "ok": true,
      "feito": true,
      "emitente_id": "uuid"
    }
  ],
  "situacao": {
    "proximo_passo": {
      "item": "certificado"
    }
  }
}
```

### POST /emitentes
Cadastra uma empresa emissora. Nasce SEMPRE em HOMOLOGAÇÃO (testes). regime_tributario: nao_optante, mei ou simples; o município por prefeitura_ibge (7 dígitos) ou municipio ("Cidade/UF"); modo_emissao: a1 (certificado) ou portal (sem certificado). Campo desconhecido é recusado. 409 se o CNPJ já está nesta conta ou é de outra conta (pede a confirmação do responsável); 403 no limite do plano

Escopo: `config:escrever`

Corpo (exemplo):
```json
{
  "nome": "Matriz",
  "cnpj": "CNPJ_DA_EMPRESA",
  "im": "INSCRICAO_MUNICIPAL",
  "regime_tributario": "nao_optante",
  "cnae": "8630503",
  "municipio": "Brasília/DF",
  "endereco": {
    "logradouro": "SQS 100 Bloco A",
    "numero": "10",
    "bairro": "Asa Sul",
    "cep": "70330000",
    "cidade": "Brasília",
    "uf": "DF"
  },
  "email_contato": "contato@empresa.example",
  "modo_emissao": "a1"
}
```

Resposta (exemplo):
```json
{
  "emitente": {
    "id": "uuid",
    "nome": "Matriz",
    "ambiente": "homologacao",
    "modo_emissao": "a1",
    "certificado": null
  },
  "situacao": {
    "proximo_passo": {
      "item": "certificado"
    }
  }
}
```

### GET /emitentes/{id}
Empresa emissora em detalhe: cadastro, endereço, regime, preferências (e-mail automático, cópias, alertas), certificado (validade e titular), numeração e o link da tela.

Escopo: `config:ler`

### PUT /emitentes/{id}
Altera a empresa emissora: só os campos enviados mudam (os mesmos do cadastro, mais preferencias, crt, simples, condominio_edilicio e texto_padrao_discriminacao). Produção não se liga aqui (use /producao); voltar de produção para homologação pede emitentes:producao. Só na tela: a forma de emitir, a troca de CNPJ e os e-mails que recebem cópia das notas e os alertas (403/422 so_na_tela)

Escopo: `config:escrever`

Corpo (exemplo):
```json
{
  "im": "INSCRICAO_MUNICIPAL",
  "preferencias": {
    "email_tomador": true
  }
}
```

### POST /emitentes/{id}/desativar
Desativa a empresa emissora (não emite mais; nada é apagado). Em produção, pede emitentes:producao

Escopo: `config:escrever`

### POST /emitentes/{id}/reativar
Reativa a empresa emissora (conta no limite de CNPJs do plano; em produção, confere o CNPJ de outra conta)

Escopo: `config:escrever`

### PUT /emitentes/{id}/numeracao
Define a série e o PRÓXIMO número da DPS (tipo "dps", o padrão; "rps" ou "nfe"). Quem já emitia por outro sistema continua do último número + 1. Pela API o número nunca recua (422 numeracao_recuo; recuar só na tela). No Emissor Nacional a série vai de 1 a 49999

Escopo: `config:escrever`

Corpo (exemplo):
```json
{
  "serie": "1",
  "proximo": 101
}
```

### POST /emitentes/{id}/testar
Testa a conexão com a prefeitura com o certificado guardado (só leitura, nada é enviado). Limite: 20 a cada 15 min por conta

Escopo: `config:ler`

Resposta (exemplo):
```json
{
  "ok": true,
  "resumo": "Conexão com a prefeitura OK."
}
```

### GET /emitentes/{id}/certificado
Certificado digital A1 da empresa: validade, titular e se venceu (nunca o arquivo nem a senha).

Escopo: `config:ler`

Resposta (exemplo):
```json
{
  "modo_emissao": "a1",
  "certificado": {
    "validade": "2027-03-01",
    "titular": "EMPRESA LTDA:00000000000000",
    "vencido": false
  }
}
```

### POST /emitentes/{id}/certificado
Envia o certificado A1 (.pfx em base64 + senha), guardado cifrado. Recusa senha errada, arquivo sem a chave privada e certificado de outro CNPJ (o atual continua valendo). Use só no sistema da empresa — nunca mande o certificado a uma IA; o cliente também pode enviar pela tela. Até 100 KB. Limite: 10 a cada 15 min

Escopo: `certificados:escrever`

Corpo (exemplo):
```json
{
  "pfx_base64": "MIIK…",
  "senha": "…",
  "nome_arquivo": "empresa.pfx"
}
```

Resposta (exemplo):
```json
{
  "certificado": {
    "validade": "2027-03-01",
    "titular": "EMPRESA LTDA:00000000000000",
    "aviso": null
  }
}
```

### POST /emitentes/{id}/producao
Passa a empresa de HOMOLOGAÇÃO para PRODUÇÃO (as notas passam a valer). Só com a implantação completa (certificado, perfil, numeração e a nota de teste autorizada) e {"confirmo": true} — confirme antes com o responsável. 409 implantacao_incompleta com as pendências

Escopo: `emitentes:producao`

Corpo (exemplo):
```json
{
  "confirmo": true
}
```

### GET /servicos
Busca na tabela nacional de serviços pelo nome (até 15).

Escopo: `config:ler`

Parâmetros: `busca`

Resposta (exemplo):
```json
{
  "itens": [
    {
      "cTribNac": "040101",
      "nome": "Medicina."
    }
  ]
}
```

### GET /servicos/sugeridos
Serviços sugeridos pelos CNAEs (principal e secundários) das empresas emissoras ativas, ou de uma (emitente_id).

Escopo: `config:ler`

Parâmetros: `emitente_id`

### GET /servicos/{codigo}
O serviço da tabela nacional (6 dígitos, ex.: 040101): nome, NBS possíveis e os códigos do IBS/CBS sugeridos (CST, classificação, redução) e, no DF, o código municipal. Base para o perfil fiscal.

Escopo: `config:ler`

### GET /perfis/{id}
Perfil fiscal em detalhe, alíquotas em PERCENTUAL (aliquotas_em: "percentual").

Escopo: `config:ler`

### POST /perfis
Cria um perfil fiscal. Alíquotas em PERCENTUAL (2 = 2%; 0,65 = 0,65%; fração como 0,0065 é recusada). NBS, IBS/CBS, item e código municipal em branco = completados pela tabela oficial (a resposta diz o que foi completado em `preenchidos`). Conferido como na revisão fiscal: perfil ativo com problema que faz a prefeitura recusar a nota (sem NBS, ISS fora da faixa, retenção sem alíquota…) volta 422 perfil_com_problema com a lista. Campo desconhecido é recusado

Escopo: `config:escrever`

Corpo (exemplo):
```json
{
  "nome": "Consulta médica — pessoa física",
  "tributacao_nacional": "040101",
  "aliq_iss": 2,
  "retem_iss": false,
  "padrao_pf": true
}
```

### PUT /perfis/{id}
Altera o perfil fiscal: só os campos enviados mudam (percentual); "ativo": false desativa. A mesma conferência do POST

Escopo: `config:escrever`

### POST /perfis/{id}/conferir
Confere o perfil fiscal (a mesma conferência da revisão fiscal): pronto, ou a lista de problemas (grave = a prefeitura recusa)

Escopo: `config:ler`

Resposta (exemplo):
```json
{
  "perfil_id": "uuid",
  "pronto": true,
  "conferencia": []
}
```


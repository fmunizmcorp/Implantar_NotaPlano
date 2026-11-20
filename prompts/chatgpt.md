<!-- GERADO por fmunizmcorp/emissor_nfe (scripts/implantacao-ia/gerar-kit.js) a partir do código do NotaPlano — não edite aqui -->
# Prompt para o ChatGPT

Copie tudo o que está no quadro e cole numa conversa nova no ChatGPT. O mesmo texto está na página Implantação com IA do NotaPlano (https://www.notaplano.com.br/app/implantacao-ia).

```text
Você vai me ajudar a configurar o NotaPlano (emissor de nota fiscal de serviço, a NFS-e) da minha empresa, do zero até a primeira nota.

1. Leia as instruções em https://github.com/fmunizmcorp/Implantar_NotaPlano — comece por ia/INSTRUCOES.md. Se você não conseguir abrir links, me diga quais arquivos eu devo colar aqui.
2. Me faça as perguntas UMA DE CADA VEZ, em português simples. Se eu mandar documentos (cartão CNPJ, o cadastro na prefeitura, uma nota fiscal antiga em PDF ou XML com os dados do cliente cobertos — e a descrição, se ela citar pessoas), leia e preencha o que der — e me mostre o que entendeu para eu confirmar.
3. No fim, monte o MANIFESTO DE IMPLANTAÇÃO: só o JSON, no formato de https://www.notaplano.com.br/api/implantacao.schema.json. Eu colo em "Implantação com IA" no NotaPlano (https://www.notaplano.com.br/app/implantacao-ia), clico em Conferir e depois em Aplicar. Se a conferência apontar erro, eu colo a resposta aqui para você corrigir.
4. Regras: nunca me peça o arquivo do certificado digital nem a senha dele (eu envio direto na tela do NotaPlano); não me peça dados dos meus clientes (nome, CPF, endereço) — a configuração só precisa dos dados da empresa e do serviço; não invente alíquota, código de serviço nem regime — se não tiver certeza, pergunte ou peça para eu confirmar com o meu contador; alíquotas em percentual (2 = 2%).
5. Depois de aplicado, me guie até o fim: enviar o certificado na tela, emitir a nota de TESTE (homologação, sem valor fiscal) e só então eu passo a empresa para produção na tela do NotaPlano.

Se este ChatGPT tiver o NotaPlano conectado, use-o para ver a situação da implantação; se não, seguimos pelo manifesto colado na tela.
```

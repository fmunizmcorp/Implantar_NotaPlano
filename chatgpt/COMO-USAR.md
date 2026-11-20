# Usar com o ChatGPT

1. Abra https://www.notaplano.com.br/app/implantacao-ia e copie o **prompt para o ChatGPT** (ou copie de [../prompts/chatgpt.md](../prompts/chatgpt.md)).
2. Abra uma conversa nova no ChatGPT (qualquer plano, inclusive o grátis) e cole.
3. Responda às perguntas. Anexe o cartão CNPJ e, se tiver, uma nota antiga (PDF ou XML) com os dados do cliente cobertos — e a descrição, se ela citar pessoas.
4. Copie o JSON que ele entregar, cole em Implantação com IA → **Conferir** → **Aplicar**.

Se o ChatGPT não conseguir abrir o GitHub, baixe e anexe na conversa os arquivos da pasta [../ia/](../ia/) (comece pelo INSTRUCOES.md).

## Conector (login com a sua conta, sem copiar chave)
O NotaPlano tem um conector MCP com login seguro (OAuth) no endereço `https://app.notaplano.com.br/mcp`. No ChatGPT, em
**Configurações → Aplicativos e conectores**, ligue o **modo desenvolvedor** e crie um conector com esse endereço e autenticação
**OAuth**; ao conectar, entre com a sua conta do NotaPlano, confira o que a IA pode fazer, digite a senha e clique em **Permitir**.

**Ressalva:** hoje o ChatGPT só deixa um conector desses **gravar** (aplicar o manifesto, emitir a nota de teste) nos planos **Business
e Enterprise/Edu**, e quem cria é o administrador do workspace. Nos outros planos, use o jeito acima (o prompt e o manifesto colado na
tela). Detalhes em [../ia/MCP.md](../ia/MCP.md).

**Sobre GPT personalizado:** não há um GPT do NotaPlano — a OpenAI está retirando os GPTs personalizados (fim em 11/12/2026). O
conector para todos os planos do ChatGPT virá pelo diretório de plugins da OpenAI.

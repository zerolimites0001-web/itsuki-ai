# Itsuki AI

Assistente web local com NVIDIA NIM, streaming de Thinking, histórico e ferramentas MCP remotas.

## Requisitos
- Node.js 20+ (sem dependências npm; usa apenas módulos nativos e `fetch` global)
- Chave NVIDIA NIM para conversar com o modelo

## Iniciar
1. Copie `.env.example` para `.env` se quiser alterar modelo, endpoint ou porta.
2. Execute `node server.js`.
3. Abra `http://localhost:3000`.
4. Informe a NVIDIA API Key nas configurações.

## Servidores MCP
- Abra Configurações > Servidores MCP (seção recolhível).
- Adicione quantos servidores Streamable HTTP desejar (até 12), cada um com nome, URL HTTPS e token opcional.
- O botão Salvar conecta todos em paralelo, negocia `initialize`, envia `notifications/initialized`, pagina `tools/list` e expõe os schemas reais ao modelo.
- As ferramentas são namespaced por servidor para evitar colisões entre nomes iguais. As chamadas são roteadas de volta ao servidor correto por `tools/call`.
- O token é enviado como `x-api-key` e `Authorization: Bearer`; não é devolvido pela API de configuração. Deixe em branco para manter o token já salvo daquele servidor.
- Exa Search vem pré-configurado em `https://mcp.exa.ai/mcp`, sem parâmetros que forcem autenticação. A busca e leitura básicas são anônimas, sujeitas a limites gratuitos. Ferramentas Exa Agent exigem autenticação.
- Um servidor com falha não impede que os outros conectem. O status e as ferramentas descobertas aparecem por servidor.

## Dados locais
- `history-chats/`: conversas
- `memory/`: memórias
- `runtime-config.json`: configurações e tokens dos MCPs. Não compartilhe este arquivo com credenciais preenchidas.
- `.env`: configurações locais; não coloque credenciais em arquivos distribuídos.

## Limitações de transporte
A implementação suporta MCP remoto por Streamable HTTP. Servidores MCP locais via stdio e OAuth interativo de navegador não são suportados; use um token/API key compatível quando o servidor exigir autenticação.

## Chave NVIDIA
- A chave é salva pelo servidor em `private-config.json`, com permissão restrita ao usuário do processo (modo 600 em sistemas compatíveis).
- O conteúdo da chave nunca é retornado ao navegador pela API de configuração e não fica no `localStorage`.
- Se o campo de chave ficar vazio ao salvar, a chave anteriormente gravada é mantida. Para substituir, informe a nova chave e salve.
- `private-config.json` é ignorado pelo Git e não é incluído no ZIP distribuído.

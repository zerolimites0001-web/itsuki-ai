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

## Modo professora e atividades interativas
- A personalidade base da Itsuki agora é orientada para uma professora dedicada, firme, paciente e encorajadora, inspirada na Itsuki Nakano.
- Para pedir uma prova ou atividade, diga a matéria, o nível, a quantidade de questões e se prefere alternativas ou respostas abertas.
- A IA pode renderizar blocos `:::itsuki-quiz` com questões `single` (alternativa única), `multiple` (caixas de seleção) e `text` (resposta escrita).
- O aluno responde diretamente nos cartões e envia o conjunto à Itsuki para correção e explicação.
- A atividade permanece no histórico como parte da conversa. As respostas submetidas são enviadas como uma mensagem normal ao modelo.

## Anexos e leitura de arquivos
- Use o botão `＋` para anexar imagens, PDFs, documentos DOCX e arquivos de texto/código; também é possível arrastar arquivos para o compositor.
- Imagens PNG, JPEG, WEBP e GIF são enviadas ao modelo multimodal Nemotron VL como imagens, não apenas como nomes de arquivo.
- PDFs têm o texto extraído no navegador (até 100 páginas/120 mil caracteres); arquivos DOCX são convertidos em texto; TXT, Markdown, CSV, JSON e arquivos de código comuns são lidos diretamente.
- Limites: até 8 anexos por mensagem, 8 MB por arquivo e 12 MB no total. O conteúdo extraído é limitado a 120 mil caracteres por arquivo.
- PDFs digitalizados sem camada de texto não têm OCR nesta versão. Planilhas XLSX, apresentações PPTX e documentos antigos DOC ainda não são extraídos.
- Os anexos ficam registrados no histórico local da conversa. Evite anexar documentos sensíveis em um servidor compartilhado.

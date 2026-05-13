# QwenProxy

Servidor proxy local que se comunica com o Qwen (chat.qwen.ai) utilizando automação de navegador via Playwright. Fornece uma API compatível com OpenAI para interações de chat e execução de ferramentas.

---

## 📋 Descrição

O **QwenProxy** é um servidor proxy que permite utilizar os modelos de IA do Qwen através de uma API compatível com a OpenAI. O projeto utiliza automação de navegador (Playwright) para simular interações humanas com a plataforma chat.qwen.ai, permitindo:

- Envio de mensagens e recebimento de respostas em streaming
- Execução de ferramentas (tools) definidas pelo usuário
- Suporte a raciocínio/thinking dos modelos Qwen
- Gerenciamento de sessão persistente com estado de login

Este projeto é ideal para desenvolvedores que desejam integrar capacidades de IA avançadas em suas aplicações utilizando a infraestrutura do Qwen, mantendo compatibilidade com bibliotecas e ferramentas já existentes para a API da OpenAI.

---

## 🚀 Funcionalidades Principais

- **API OpenAI-compatível**: Endpoints compatíveis com `/v1/chat/completions` e `/v1/models`
- **Suporte a Streaming**: Respostas em tempo real via Server-Sent Events (SSE)
- **Execução de Ferramentas**: Sistema extensível para registro e execução de tools personalizadas
- **Sessão Persistente**: Navegador mantém estado de login entre reinicializações
- **Proteção por API Key**: Autenticação opcional via Bearer Token
- **Modelos com Raciocínio**: Suporte a modelos Qwen com capacidade de thinking
- **CORS Habilitado**: Permite requisições de aplicações web frontend

---

## 🛠️ Tecnologias e Frameworks

| Categoria | Tecnologia | Versão |
|-----------|------------|--------|
| **Linguagem** | TypeScript | ^6.0.3 |
| **Runtime** | Node.js | v20+ |
| **Framework Web** | Hono | ^4.12.18 |
| **Servidor HTTP** | @hono/node-server | ^2.0.2 |
| **Automação** | Playwright | ^1.59.1 |
| **Gerenciamento de Variáveis** | dotenv | ^17.4.2 |
| **Geração de IDs** | uuid | ^14.0.0 |
| **Type Definitions** | @types/node, @types/uuid | - |
| **Executor TS** | tsx | ^4.21.0 |

---

## 📦 Instalação e Configuração

### Pré-requisitos

- **Node.js** versão 20 ou superior
- **npm** ou gerenciador de pacotes compatível
- Navegadores suportados pelo Playwright (instalados automaticamente)

### Passo a Passo

1. **Clone o repositório** (se aplicável):
   ```bash
   git clone <url-do-repositorio>
   cd qwenproxy
   ```

2. **Instale as dependências**:
   ```bash
   npm install
   ```

3. **Instale os navegadores do Playwright**:
   ```bash
   npx playwright install
   ```

4. **Configure as variáveis de ambiente**:

   Crie um arquivo `.env` na raiz do projeto:

   ```env
   PORT=3000
   API_KEY=sua_chave_secreta_aqui
   QWEN_EMAIL=seu_email@exemplo.com
   QWEN_PASSWORD=sua_senha_aqui
   ```

   **Descrição das variáveis:**

   | Variável | Obrigatória | Descrição |
   |----------|-------------|-----------|
   | `PORT` | Não | Porta do servidor (padrão: 3000) |
   | `API_KEY` | Não | Chave de autenticação para proteger endpoints `/v1/*` |
   | `QWEN_EMAIL` | Sim* | E-mail da conta Qwen (necessário para Docker/headless) |
   | `QWEN_PASSWORD` | Sim* | Senha da conta Qwen (necessário para Docker/headless) |

   \* *Se não fornecer QWEN_EMAIL/PASSWORD, será necessário realizar login manual uma vez.*

---

## 💻 Uso

### Opção 1: Docker (Recomendado)

1. **Construa e inicie o container**:
   ```bash
   docker-compose up -d
   ```

2. **Verifique os logs**:
   ```bash
   docker-compose logs -f
   ```

3. **O servidor estará disponível em**: `http://localhost:3000`

### Opção 2: Execução Local

#### Login Manual (se não configurou QWEN_EMAIL/PASSWORD)

```bash
npm run login
```

Uma janela do navegador será aberta. Faça login na sua conta Qwen e feche a janela após concluir.

#### Iniciar o Servidor

```bash
npm start
```

O servidor será iniciado em `http://localhost:3000`.

---

## 📖 Exemplos de Uso

### 1. Listar Modelos Disponíveis

```bash
curl http://localhost:3000/v1/models
```

**Resposta esperada:**
```json
{
  "object": "list",
  "data": [
    {
      "id": "qwen3.6-plus",
      "object": "model",
      "created": 1234567890,
      "owned_by": "qwen"
    },
    {
      "id": "qwen3.6-plus-no-thinking",
      "object": "model",
      "created": 1234567890,
      "owned_by": "qwen"
    }
  ]
}
```

### 2. Chat Simples (com API Key)

```bash
curl http://localhost:3000/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer sua_chave_secreta_aqui" \
  -d '{
    "model": "qwen3.6-plus",
    "messages": [
      {
        "role": "user",
        "content": "Olá! Como você está?"
      }
    ]
  }'
```

### 3. Chat com Streaming

```bash
curl http://localhost:3000/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer sua_chave_secreta_aqui" \
  -d '{
    "model": "qwen3.6-plus",
    "messages": [
      {
        "role": "user",
        "content": "Conte-me uma piada curta."
      }
    ],
    "stream": true
  }'
```

### 4. Chat com Execução de Ferramentas

```bash
curl http://localhost:3000/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer sua_chave_secreta_aqui" \
  -d '{
    "model": "qwen3.6-plus",
    "messages": [
      {
        "role": "user",
        "content": "Qual a temperatura atual?"
      }
    ],
    "tools": [
      {
        "type": "function",
        "function": {
          "name": "get_weather",
          "description": "Obtém a temperatura atual de uma cidade",
          "parameters": {
            "type": "object",
            "properties": {
              "city": {
                "type": "string",
                "description": "Nome da cidade"
              }
            },
            "required": ["city"]
          }
        }
      }
    ],
    "tool_choice": "auto"
  }'
```

### 5. Health Check

```bash
curl http://localhost:3000/health
```

**Resposta:** `{"status":"ok"}`

---

## 🏗️ Estrutura do Projeto

```
qwenproxy/
├── src/
│   ├── index.ts           # Entry point do servidor
│   ├── login.ts           # Script de login manual
│   ├── routes/
│   │   └── chat.ts        # Endpoint /v1/chat/completions
│   ├── services/
│   │   ├── playwright.ts  # Gerenciamento do navegador
│   │   └── qwen.ts        # Comunicação com API Qwen
│   ├── tools/
│   │   ├── executor.ts    # Execução de ferramentas
│   │   ├── registry.ts    # Registro de tools
│   │   ├── schema.ts      # Validação de schemas
│   │   └── types.ts       # Tipos de tools
│   ├── types/
│   │   └── openai.ts      # Tipos OpenAI-compatible
│   ├── utils/
│   │   └── types.ts       # Utilitários de tipos
│   ├── runtime/           # Runtime do MCP
│   ├── advanced.test.ts   # Testes avançados
│   └── index.test.ts      # Testes unitários
├── qwen_profile/          # Armazenamento do perfil do navegador
├── .env                   # Variáveis de ambiente (não versionado)
├── docker-compose.yml     # Configuração Docker
├── Dockerfile             # Imagem Docker
├── package.json           # Dependências e scripts
├── tsconfig.json          # Configuração TypeScript
└── LICENSE                # Licença ISC
```

---

## 🧪 Testes

Execute a suíte de testes:

```bash
npm test
```

Os testes utilizam o framework nativo `node:test` e cobrem:
- Integração com endpoints da API
- Execução de ferramentas
- Validação de schemas
- Comportamento de streaming

---

## 🤝 Como Contribuir

Contribuições são bem-vindas! Siga as diretrizes abaixo:

### Diretrizes de Código

1. **Padrão de Código**:
   - Utilize TypeScript estrito
   - Siga as convenções do `tsconfig.json`
   - Mantenha consistência com o estilo existente (indentação de 2 espaços, aspas simples)

2. **Commits**:
   - Use mensagens descritivas no padrão Conventional Commits
   - Exemplo: `feat: adiciona suporte a novo modelo Qwen`

3. **Testes**:
   - Adicione testes para novas funcionalidades
   - Certifique-se de que todos os testes passam antes de submeter

### Processo de Pull Request

1. **Fork** o repositório
2. Crie uma **branch** para sua feature (`git checkout -b feature/minha-feature`)
3. Faça **commit** das mudanças (`git commit -m 'feat: adiciona minha feature'`)
4. Faça **push** para a branch (`git push origin feature/minha-feature`)
5. Abra um **Pull Request** descrevendo:
   - O propósito da mudança
   - Quais problemas resolve
   - Como testar a funcionalidade
   - Screenshots/vídeos (se aplicável)

6. Aguarde a revisão da equipe. Mudanças podem ser solicitadas antes do merge.

### Reportar Issues

Para bugs ou sugestões, abra uma issue no GitHub incluindo:
- Descrição clara do problema
- Passos para reproduzir
- Comportamento esperado vs. atual
- Ambiente (SO, versão do Node, etc.)

---

## 📄 Licença

Este projeto está licenciado sob a **Licença ISC**.

```
Copyright (c) 2026 Pedro Farias

Permission to use, copy, modify, and/or distribute this software for any
purpose with or without fee is hereby granted, provided that the above
copyright notice and this permission notice appear in all copies.

THE SOFTWARE IS PROVIDED "AS IS" AND THE AUTHOR DISCLAIMS ALL WARRANTIES
WITH REGARD TO THIS SOFTWARE INCLUDING ALL IMPLIED WARRANTIES OF
MERCHANTABILITY AND FITNESS. IN NO EVENT SHALL THE AUTHOR BE LIABLE FOR
ANY SPECIAL, DIRECT, INDIRECT, OR CONSEQUENTIAL DAMAGES OR ANY DAMAGES
WHATSOEVER RESULTING FROM LOSS OF USE, DATA OR PROFITS, WHETHER IN AN
ACTION OF CONTRACT, NEGLIGENCE OR OTHER TORTIOUS ACTION, ARISING OUT OF
OR IN CONNECTION WITH THE USE OR PERFORMANCE OF THIS SOFTWARE.
```

---

## 📞 Contato e Suporte

- **Autor**: Pedro Farias
- **Issues**: [GitHub Issues](https://github.com/pedrofarias/qwenproxy/issues)
- **Discussões**: [GitHub Discussions](https://github.com/pedrofarias/qwenproxy/discussions)

Para dúvidas gerais, utilize as **Discussions** do GitHub. Para bugs reportados, abra uma **Issue**.

---

## 🗺️ Roadmap

### ✅ Concluído

- [x] API OpenAI-compatível básica
- [x] Suporte a streaming (SSE)
- [x] Sistema de tools/execução
- [x] Login automatizado via Playwright
- [x] Proteção por API Key
- [x] Containerização Docker

### 🚧 Em Desenvolvimento

- [ ] Suporte a múltiplos modelos Qwen
- [ ] Cache de respostas para otimização
- [ ] Rate limiting configurável
- [ ] Logs estruturados (JSON)
- [ ] Documentação de API (OpenAPI/Swagger)

### 📅 Planejamentos Futuros

- [ ] Suporte a visão/imagem (multimodal)
- [ ] Webhooks para eventos assíncronos
- [ ] Dashboard de monitoramento
- [ ] Integração com provedores alternativos
- [ ] Plugin system para extensões
- [ ] Suporte a batch requests

---

## ⚠️ Disclaimer

**Este projeto é fornecido estritamente para fins educacionais e de pesquisa.**

Os autores **não incentivam ou endossam**:

- Uso indevido ou malicioso
- Automação não autorizada
- Abuso de serviços de terceiros
- Violações dos Termos de Serviço de plataformas

**Responsabilidade do Usuário**: Os usuários são inteiramente responsáveis pelo uso deste software, incluindo conformidade com leis aplicáveis, regulamentações e contratos de serviço.

**Propósito Educacional**: Este repositório demonstra conceitos relacionados a:

- Automação de navegadores
- Gerenciamento de sessões
- Arquiteturas de runtime compatíveis com OpenAI

**Use por sua própria conta e risco.**

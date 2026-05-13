# 📋 Plano de Implementação - Planejamentos Futuros

Este documento detalha o plano de implementação para cada funcionalidade listada na seção "📅 Planejamentos Futuros" do README. Cada item inclui descrição, justificativa, tarefas técnicas, estimativa de esforço e dependências.

---

## 1. 🖼️ Suporte a Visão/Imagem (Multimodal)

### Descrição
Adicionar capacidade de processar e enviar imagens junto com mensagens de texto, permitindo que os modelos Qwen multimodais analisem conteúdo visual.

### Justificativa
- Modelos modernos de IA suportam entrada multimodal (texto + imagem)
- Expande casos de uso para análise de imagens, OCR, descrição de cenas
- Alinha com padrão da indústria (OpenAI Vision API)

### Tarefas Técnicas

#### Fase 1: Preparação da Infraestrutura
- [ ] Atualizar tipos OpenAI para suportar conteúdo multimodal (`image_url`, `type: "image_url"`)
- [ ] Modificar schema de mensagens para aceitar array de conteúdo misto (texto + imagem)
- [ ] Adicionar validação de formatos de imagem suportados (PNG, JPEG, WebP, GIF)
- [ ] Implementar codificação Base64 para imagens locais

#### Fase 2: Integração com Playwright
- [ ] Pesquisar como o chat.qwen.ai aceita upload de imagens
- [ ] Implementar mecanismo de upload de imagem via automação do navegador
- [ ] Criar handler para capturar respostas que referenciam imagens
- [ ] Testar com diferentes tamanhos e formatos de imagem

#### Fase 3: API e Documentação
- [ ] Atualizar endpoint `/v1/chat/completions` para aceitar mensagens multimodais
- [ ] Adicionar exemplos de uso no README
- [ ] Criar testes automatizados para fluxo multimodal
- [ ] Documentar limitações (tamanho máximo, formatos suportados)

### Estimativa de Esforço
- **Tempo**: 5-7 dias
- **Complexidade**: Alta
- **Prioridade**: Alta

### Dependências
- Pesquisa prévia sobre como o Qwen aceita imagens na interface web
- Possível necessidade de atualizar versão do Playwright

### Riscos e Mitigações
| Risco | Mitigação |
|-------|-----------|
| Upload de imagens não suportado pela interface web do Qwen | Investigar APIs alternativas ou contatar suporte do Qwen |
| Limites de tamanho de arquivo | Implementar compressão automática de imagens |
| Latência no processamento | Adicionar timeout configurável e feedback ao usuário |

---

## 2. 🔔 Webhooks para Eventos Assíncronos

### Descrição
Implementar sistema de webhooks para notificar URLs externas sobre eventos assíncronos, como conclusão de requisições longas, erros críticos, ou mudanças de status.

### Justificativa
- Permite integração com sistemas externos sem polling
- Facilita arquiteturas baseadas em eventos
- Melhora experiência para processos de longa duração

### Tarefas Técnicas

#### Fase 1: Design do Sistema
- [ ] Definir lista de eventos disparáveis (ex: `request.completed`, `request.failed`, `session.expired`)
- [ ] Criar schema de payload para cada tipo de evento
- [ ] Projetar sistema de assinatura de webhooks (registro de URLs por evento)
- [ ] Definir política de retries e backoff exponencial

#### Fase 2: Implementação
- [ ] Criar tabela/armazenamento para registros de webhooks (URLs, eventos, segredos)
- [ ] Implementar endpoint `/v1/webhooks` para CRUD de inscrições
- [ ] Adicionar middleware de disparo de webhooks nos pontos relevantes do código
- [ ] Implementar fila de retries para falhas de entrega
- [ ] Criar sistema de assinatura HMAC para segurança dos payloads

#### Fase 3: Monitoramento e Logs
- [ ] Adicionar logs estruturados para todos os disparos de webhook
- [ ] Criar endpoint de health check para webhooks registrados
- [ ] Implementar métricas de taxa de sucesso/falha de entregas
- [ ] Adicionar dashboard básico de status de webhooks (opcional)

### Estimativa de Esforço
- **Tempo**: 4-6 dias
- **Complexidade**: Média-Alta
- **Prioridade**: Média

### Dependências
- Sistema de armazenamento persistente para registros de webhooks
- Implementação prévia de logs estruturados (já concluída)

### Riscos e Mitigações
| Risco | Mitigação |
|-------|-----------|
| Webhooks lentos bloquearem o servidor principal | Executar disparos em background/thread separada |
| Vazamento de segredos de webhook | Usar variáveis de ambiente criptografadas |
| Abuse por URLs maliciosas | Validar URLs, implementar allowlist opcional |

---

## 3. 📊 Dashboard de Monitoramento

### Descrição
Criar uma interface web para monitoramento em tempo real do status do servidor, métricas de uso, logs recentes e saúde do sistema.

### Justificativa
- Melhora observabilidade do sistema
- Facilita debugging e troubleshooting
- Proporciona visibilidade para administradores

### Tarefas Técnicas

#### Fase 1: Backend de Métricas
- [ ] Implementar coleta de métricas (requisições/min, latência média, taxa de erro)
- [ ] Criar endpoint `/api/metrics` retornando dados em JSON
- [ ] Adicionar endpoint `/api/health` com status detalhado dos componentes
- [ ] Implementar histórico de métricas (últimas 24h)

#### Fase 2: Frontend do Dashboard
- [ ] Escolher stack frontend leve (ex: HTMX + Alpine.js ou React mínimo)
- [ ] Criar página de overview com gráficos de métricas principais
- [ ] Implementar visualização de logs em tempo real (via SSE ou polling)
- [ ] Adicionar painel de status dos componentes (Playwright, Qwen session, etc.)
- [ ] Criar visualização de uso por modelo/horário

#### Fase 3: Segurança e Acesso
- [ ] Proteger dashboard com autenticação (mesma API Key ou senha separada)
- [ ] Implementar controle de acesso baseado em roles (admin vs viewer)
- [ ] Adicionar opção de desabilitar dashboard via variável de ambiente
- [ ] Criar documentação de uso do dashboard

### Estimativa de Esforço
- **Tempo**: 6-8 dias
- **Complexidade**: Média-Alta
- **Prioridade**: Média

### Dependências
- Logs estruturados já implementados (facilita extração de métricas)
- Conhecimento básico de frontend necessário

### Riscos e Mitigações
| Risco | Mitigação |
|-------|-----------|
| Dashboard consumir muitos recursos | Implementar caching de métricas, limitar atualizações em tempo real |
| Exposição de dados sensíveis | Filtrar logs, remover informações críticas antes de exibir |
| Complexidade de manutenção | Manter frontend simples, evitar dependências excessivas |

---

## 4. 🔌 Integração com Provedores Alternativos

### Descrição
Expandir o proxy para suportar múltiplos provedores de IA além do Qwen, mantendo a mesma interface OpenAI-compatível.

### Justificativa
- Aumenta flexibilidade para usuários
- Permite fallback entre provedores
- Reduz vendor lock-in

### Tarefas Técnicas

#### Fase 1: Abstração de Provedores
- [ ] Criar interface/abstract class `Provider` com métodos padronizados
- [ ] Implementar adapter pattern para isolar lógica específica de cada provedor
- [ ] Refatorar código atual do Qwen para seguir nova interface
- [ ] Definir schema de configuração de provedores

#### Fase 2: Novos Provedores
- [ ] Pesquisar e selecionar provedores candidatos (ex: DeepSeek, Groq, Anthropic via wrapper)
- [ ] Implementar adapters para 2-3 provedores iniciais
- [ ] Criar sistema de detecção de provedor baseado no nome do modelo
- [ ] Implementar mapeamento de nomes de modelos entre provedores

#### Fase 3: Configuração e Roteamento
- [ ] Adicionar configuração de múltiplos provedores no `.env` ou arquivo separado
- [ ] Implementar roteamento inteligente (fallback, load balancing básico)
- [ ] Criar endpoint `/v1/providers` para listar provedores configurados
- [ ] Documentar como adicionar novos provedores

### Estimativa de Esforço
- **Tempo**: 7-10 dias
- **Complexidade**: Alta
- **Prioridade**: Baixa-Média

### Dependências
- Refatoração significativa do código atual
- Pesquisa de APIs de provedores alternativos

### Riscos e Mitigações
| Risco | Mitigação |
|-------|-----------|
| APIs muito diferentes entre provedores | Criar camada de abstração robusta, aceitar limitações |
| Manutenção de múltiplos adapters | Documentar bem, criar testes específicos por provedor |
| Confusão de usuários sobre qual provedor usar | Documentação clara, naming conventions para modelos |

---

## 5. 🧩 Plugin System para Extensões

### Descrição
Criar um sistema de plugins que permita aos usuários estender funcionalidades do QwenProxy sem modificar o código base.

### Justificativa
- Promove ecossistema de extensões da comunidade
- Perite personalização sem forks do projeto
- Facilita experimentação de novas features

### Tarefas Técnicas

#### Fase 1: Arquitetura do Sistema de Plugins
- [ ] Definir API de plugins (interfaces, hooks disponíveis)
- [ ] Escolher formato de plugins (módulos Node.js, scripts Lua, WebAssembly?)
- [ ] Projetar sistema de lifecycle (load, enable, disable, unload)
- [ ] Definir sandboxing para segurança de plugins de terceiros

#### Fase 2: Implementação do Core
- [ ] Criar loader de plugins (leitura de diretório `/plugins`)
- [ ] Implementar registro de hooks (pre-request, post-response, on-error, etc.)
- [ ] Adicionar sistema de permissões para plugins
- [ ] Criar CLI para gerenciar plugins (install, list, remove)

#### Fase 3: Documentação e Exemplos
- [ ] Escrever documentação completa da API de plugins
- [ ] Criar 3-5 plugins de exemplo (ex: logging customizado, rate limiting avançado, cache personalizado)
- [ ] Estabelecer diretrizes para publicação de plugins da comunidade
- [ ] Criar repositório/template para plugins de terceiros

### Estimativa de Esforço
- **Tempo**: 8-12 dias
- **Complexidade**: Muito Alta
- **Prioridade**: Baixa

### Dependências
- Sistema maduro de hooks internos
- Boa documentação da codebase atual

### Riscos e Mitigações
| Risco | Mitigação |
|-------|-----------|
| Plugins comprometem segurança/estabilidade | Sandboxing rigoroso, revisão de plugins oficiais |
| Baixa adoção pela comunidade | Manter API simples, fornecer exemplos claros |
| Dificuldade de debug de plugins | Criar modo verbose, logs específicos por plugin |

---

## 6. 📦 Suporte a Batch Requests

### Descrição
Implementar endpoint para processamento de múltiplas requisições em lote, permitindo envio de várias mensagens de uma vez com processamento assíncrono.

### Justificativa
- Otimiza custos para processamento em massa
- Útil para cenários de ETL, análise de grandes volumes de dados
- Alinha com API de batch da OpenAI

### Tarefas Técnicas

#### Fase 1: Design da API de Batch
- [ ] Definir formato de input para batch requests (JSONL, array de requests)
- [ ] Projetar resposta assíncrona (submit job -> retrieve results)
- [ ] Definir limites (max requests por batch, timeout máximo)
- [ ] Criar schema de resposta padronizado

#### Fase 2: Implementação
- [ ] Criar endpoint `/v1/batch` para submissão de jobs
- [ ] Implementar endpoint `/v1/batch/{id}` para consulta de status
- [ ] Adicionar endpoint `/v1/batch/{id}/results` para download de resultados
- [ ] Criar sistema de filas para processamento de batches
- [ ] Implementar persistência de resultados (arquivo temporário ou storage)

#### Fase 3: Gerenciamento e Limites
- [ ] Adicionar rate limiting específico para batch requests
- [ ] Implementar cancelamento de jobs em andamento
- [ ] Criar política de expiração de resultados (cleanup automático)
- [ ] Documentar melhores práticas e casos de uso

### Estimativa de Esforço
- **Tempo**: 4-5 dias
- **Complexidade**: Média
- **Prioridade**: Média-Baixa

### Dependências
- Sistema de filas ou processamento em background
- Armazenamento temporário para resultados

### Riscos e Mitigações
| Risco | Mitigação |
|-------|-----------|
| Batches grandes consumirem todos os recursos | Implementar limites rígidos, filas priorizadas |
| Perda de resultados em caso de crash | Persistência em disco, checkpointing |
| Complexidade de gerenciamento de estado | Manter state machine simples para status de jobs |

---

## 📊 Resumo Geral

| Funcionalidade | Esforço | Complexidade | Prioridade | Dependências Críticas |
|----------------|---------|--------------|------------|----------------------|
| Suporte Multimodal | 5-7 dias | Alta | Alta | Pesquisa de upload no Qwen |
| Webhooks | 4-6 dias | Média-Alta | Média | Armazenamento persistente |
| Dashboard | 6-8 dias | Média-Alta | Média | Nenhuma |
| Provedores Alternativos | 7-10 dias | Alta | Baixa-Média | Refatoração significativa |
| Plugin System | 8-12 dias | Muito Alta | Baixa | Maturidade da codebase |
| Batch Requests | 4-5 dias | Média | Média-Baixa | Sistema de filas |

**Tempo Total Estimado**: 34-48 dias de desenvolvimento (considerando execução sequencial)

---

## 🎯 Ordem Recomendada de Implementação

Baseado em valor entregue vs. esforço, recomenda-se a seguinte ordem:

1. **Suporte Multimodal** - Alto impacto, alinha com expectativas do mercado
2. **Webhooks** - Habilita integrações poderosas com esforço moderado
3. **Dashboard** - Melhora operacional imediata para administradores
4. **Batch Requests** - Caso de uso específico mas valioso
5. **Provedores Alternativos** - Requer refatoração mas aumenta flexibilidade
6. **Plugin System** - Maior esforço, adiar até a base estar mais madura

---

## 📝 Notas Gerais

- Todas as estimativas consideram um desenvolvedor experiente com TypeScript e Node.js
- Tempos podem variar baseado em descobertas durante implementação
- Recomenda-se criar branches separadas para cada feature
- Cada feature deve incluir testes automatizados e atualização da documentação
- Considerar feedback da comunidade antes de implementar features de baixa prioridade

---

*Última atualização: Dezembro 2026*

# Manual Técnico de Engenharia

Guia de consulta rápida para desenvolvedores Junior, Pleno e Senior.

## Escopo desta versão (v0.2)

- Conteúdo reestruturado para formato técnico e objetivo.
- Duplicidades removidas.
- Ordem alfabética aplicada dentro dos tópicos.
- Primeira leva de termos implementada com profundidade em camadas: Junior, Pleno e Senior.
- Tópicos críticos aprofundados com foco arquitetural: Go, Java, JavaScript e Node.js, TypeScript, CAP, DI, Trade-offs e Streaming/Buffer.

## Como usar este manual

Cada termo segue o mesmo template:

- Junior: definição direta e vocabulário essencial.
- Pleno: aplicação prática, boas práticas e erros comuns.
- Senior: trade-offs, implicações arquiteturais e decisões de engenharia.

## Índice Técnico (Ordem Alfabética por Domínio)

Itens com link já possuem seção técnica implementada neste documento.

### APIs, Protocolos e Web

- [API](#api)
- Caching HTTP
- CORS
- gRPC
- GraphQL
- HATEOAS
- [HTTP](#http)
- HTTPS
- Idempotência
- REST
- RESTful
- RPC
- Status Codes
- Web Hooks
- Web Services
- WebSocket

### Arquitetura e Design

- Acoplamento e Coesão
- Arquitetura de Software
- CQRS
- Design Patterns
- [Injeção de Dependência](#injeção-de-dependência)
- MVP
- SOLID
- [Teorema CAP](#teorema-cap)
- [Teorema dos 3 Trade-offs](#teorema-dos-3-trade-offs-clean-fast-complex)

### Banco de Dados e Persistência

- [ACID](#acid)
- Banco de Dados (Relacional e Não Relacional)
- Connection Pool
- Constraints
- Eager Loading
- Foreign Key
- Índices
- [Isolamento de Transações](#isolamento-de-transações)
- Lock Otimista e Pessimista
- Migrations
- N+1 Query
- ORM
- Primary Key
- SQL Joins
- Transactions

### DevOps, Infra e Operação

- CI/CD
- Cluster
- [Docker](#docker)
- Docker Compose
- Dockerfile
- Escalamento Horizontal e Vertical
- Graceful Shutdown
- Kubernetes
- Load Balancer
- Nginx
- [Observabilidade (Logs, Métricas e Traces)](#observabilidade-logs-métricas-e-traces)
- Proxy
- Proxy Reverso
- SLI, SLO e SLA

### Linguagens e Runtime

- [Go](#go)
- [Java](#java)
- [JavaScript e Node.js](#javascript-e-nodejs)
- [TypeScript](#typescript)

### Qualidade e Segurança

- BDD
- [JWT](#jwt)
- [OAuth 2.0](#oauth-20)
- OWASP Top 10
- TDD
- Teste de Integração
- Teste E2E
- [Teste Unitário](#teste-unitário)
- Token de Autenticação

### Versionamento e Colaboração

- Branching Strategy
- Code Review
- Conventional Commits
- [Git](#git)
- Pull Request
- Semantic Versioning

---

## Conteúdo Técnico (Primeira Leva)

## ACID

### Junior

- ACID define 4 propriedades para garantir confiabilidade em transações de banco de dados.
- A: Atomicity (atomicidade) -> ou aplica tudo, ou não aplica nada.
- C: Consistency (consistência) -> a transação mantém regras válidas do banco.
- I: Isolation (isolamento) -> transações simultâneas não se corrompem.
- D: Durability (durabilidade) -> após commit, o dado persiste mesmo com falha.

### Pleno

- Use ACID em operações críticas: financeiro, estoque, autorização, faturamento.
- Combine transações curtas + índices corretos para reduzir lock contention.
- Evite abrir transação antes de chamadas de rede (API externa, fila, e-mail).
- Em ORMs, valide escopo transacional para não incluir consultas desnecessárias.

### Senior

- Trade-off principal: mais consistência normalmente aumenta custo de latência/throughput.
- Em sistemas distribuídos, ACID local nem sempre resolve consistência global.
- Para fluxos longos entre serviços, prefira Saga + compensação em vez de transação distribuída clássica.

**Correlatos**: Isolamento de Transações, Locks, Saga, Consistência Eventual.

## API

### Junior

- API (Application Programming Interface) é um contrato para um sistema conversar com outro.
- O contrato define entrada, saída, erros e regras de autenticação.

### Pleno

**Boas práticas**
- Definir contrato explícito (OpenAPI).
- Versionar endpoints sem quebrar clientes.
- Padronizar erros, paginação e filtros.
- Definir timeouts e política de retry no consumidor.

**Exemplo simples de endpoint HTTP**

```http
GET /v1/users/123 HTTP/1.1
Host: api.exemplo.com
Authorization: Bearer <token>
```

### Senior

- API é fronteira de domínio e precisa estabilidade sem bloquear evolução.
**Decisões de arquitetura**
- REST x gRPC x GraphQL dependem de latência, acoplamento e tipo de cliente.
- Contrato-first reduz regressão e melhora integração entre times.
- Idempotência e observabilidade são obrigatórias para operação confiável.

**Correlatos**: HTTP, OpenAPI, Idempotência, Rate Limit, Observabilidade.

## Docker

### Junior

- Docker empacota aplicação + dependências em uma imagem executável.
- O container roda de forma previsível em diferentes ambientes.

### Pleno

**Boas práticas**
- Usar imagem base mínima.
- Fixar versão de dependências.
- Aplicar multi-stage build.
- Não executar processo principal como root.

**Exemplo mínimo**

```dockerfile
FROM node:20-alpine
WORKDIR /app
COPY package*.json ./
RUN npm ci --omit=dev
COPY . .
CMD ["node", "server.js"]
```

### Senior

**Trade-offs**
- Imagens menores aceleram deploy, mas podem dificultar troubleshooting.
- Build determinístico melhora segurança e rollback.
- Em escala, o ganho vem do ecossistema: registry, scan, policy e orquestração.

**Correlatos**: Docker Compose, Kubernetes, CI/CD, Supply Chain Security.

## Git

### Junior

- Git é sistema de controle de versão distribuído.
- Permite histórico de mudanças, colaboração e rollback seguro.

### Pleno

**Fluxo recomendado para time**
- Criar branch curta por tarefa.
- Commits pequenos e semânticos.
- Abrir Pull Request com contexto técnico e evidência de teste.
- Rebase/merge conforme padrão definido pela equipe.

**Exemplo de rotina**

```bash
git checkout -b feat/api-idempotency
git add .
git commit -m "feat(api): add idempotency key validation"
git push -u origin feat/api-idempotency
```

### Senior

- Estratégia de branch impacta lead time e risco de integração.
- Trunk-based tende a reduzir conflitos e acelerar release.
- Proteções de branch + CI obrigatório elevam baseline de qualidade.

**Correlatos**: Code Review, Conventional Commits, Pull Request, Semantic Versioning.

## HTTP

### Junior

- HTTP é protocolo de comunicação entre cliente e servidor.
- Métodos comuns: GET, POST, PUT, PATCH, DELETE.
- Status codes indicam resultado da requisição (200, 201, 400, 401, 404, 500).

### Pleno

**Boas práticas**
- Usar método correto para semântica da operação.
- Aplicar idempotência em PUT/DELETE e em POST crítico com chave de idempotência.
- Controlar cache com headers (Cache-Control, ETag).
- Tratar timeout e retry com backoff.

### Senior

**Trade-offs**
- Idempotência aumenta robustez contra retries e falhas de rede.
- Caching reduz custo e latência, mas exige política de invalidação.
- APIs internas sensíveis podem exigir mTLS e limites por consumidor.

**Correlatos**: API, Caching HTTP, Idempotência, HTTPS, Status Codes.

## Go

### Junior

- Go é uma linguagem compilada e estaticamente tipada.
- O código compila para binário nativo, facilitando deploy sem runtime da linguagem.
- Concorrência é nativa com goroutines e channels.

### Pleno

- Goroutine é unidade leve de execução, com custo menor que thread tradicional.
- Channels ajudam sincronização e comunicação segura entre fluxos concorrentes.
- Tipagem estática reduz erros em runtime e melhora manutenção em times.
- Go é comum em APIs, workers e serviços de infraestrutura por simplicidade operacional.

### Senior

**Trade-offs arquiteturais**
- GC simplifica memória, mas pausas e pressão de alocação impactam latência P95/P99.
- Alta concorrência exige limites explícitos (worker pool, semáforo) para evitar saturação.
- Binário único simplifica entrega, porém observabilidade e tuning de runtime são decisivos em produção.

**Correlatos**: Goroutines, Concorrência, Garbage Collector, Graceful Shutdown.

## Java

### Junior

- Java compila para bytecode e executa na JVM.
- A JVM permite o princípio Write Once, Run Anywhere.
- Garbage Collector gerencia memória automaticamente.

### Pleno

- Fluxo típico: .java -> bytecode -> otimização em runtime por JIT.
- JVM oferece portabilidade e ecossistema robusto para aplicações enterprise.
- GC reduz vazamentos por gerenciamento manual, mas exige monitoramento em cargas críticas.

### Senior

**Trade-offs arquiteturais**
- Consumo de memória tende a ser maior por overhead da JVM/heap.
- Tempo de inicialização e warmup do JIT impactam autoscaling e workloads serverless.
- Escolha de coletor e sizing de heap afeta diretamente throughput e latência sob carga.

**Correlatos**: JVM, Garbage Collector, JIT, Spring.

## JavaScript e Node.js

### Junior

- JavaScript executa em engines como a V8.
- A V8 usa JIT compiler para otimizar código quente em tempo de execução.
- Node.js executa JavaScript no servidor com modelo single-thread não bloqueante.

### Pleno

- Event Loop coordena callbacks e tarefas assíncronas sem bloquear a thread principal.
- Callbacks, Promises e async/await são pilares do fluxo assíncrono.
- I/O assíncrono permite alta concorrência em rede e disco no mesmo processo.

### Senior

**Trade-offs arquiteturais**
- Excelente para workloads I/O-bound; CPU-bound pesado pode degradar todo o event loop.
- Estratégias: worker threads, filas assíncronas, divisão de serviço e controle de backpressure.
- Observabilidade de event loop lag, fila e heap é obrigatória para estabilidade em produção.

**Correlatos**: V8, JIT, Event Loop, Callback, Streams.

## TypeScript

### Junior

- TypeScript é um superset de JavaScript com tipagem estática.
- O compilador converte TypeScript para JavaScript executável.
- Tipos ajudam a detectar erros antes do runtime.

### Pleno

- Interfaces e generics melhoram reuso e clareza de contratos.
- strict mode reduz bugs silenciosos e melhora segurança de refatoração.
- Type guards ajudam validação segura de dados em fluxos condicionais.

### Senior

**Trade-offs arquiteturais**
- Maior disciplina de tipos aumenta custo inicial, mas reduz regressão em sistemas grandes.
- Tipagem em compile-time não substitui validação de runtime em bordas externas.
- Em times grandes, TypeScript funciona como governança técnica de contratos.

**Correlatos**: JavaScript, Generics, Modelagem de Domínio.

## Injeção de Dependência

### Junior

- Injeção de Dependência (DI) é quando uma classe recebe dependências de fora.
- Isso evita criar dependências diretamente dentro da classe.

**Tipos de injeção**
- Via construtor: dependência obrigatória e explícita.
- Via setter: dependência opcional e configurável depois da criação.
- Via interface: consumo por contrato, reduzindo acoplamento a implementação concreta.

### Pleno

**Benefícios práticos**
- Menor acoplamento entre regra de negócio e infraestrutura.
- Testes unitários mais simples com mocks/stubs.
- Troca de implementação sem alteração do consumidor.

**Trade-offs dos tipos**
- Construtor: mais seguro, porém mais verboso em grafos grandes.
- Setter: flexível, porém risco de objeto incompleto.
- Interface: mais desacoplado, porém exige disciplina de contratos.

**Exemplo simples em TypeScript**

```ts
interface PaymentGateway {
  charge(amount: number): Promise<void>;
}

class CheckoutService {
  constructor(private readonly gateway: PaymentGateway) {}

  async checkout(total: number): Promise<void> {
    await this.gateway.charge(total);
  }
}
```

### Senior

- DI melhora evolução arquitetural, mas pode gerar complexidade se usada sem critério.
- Evite container "mágico" em excesso sem rastreabilidade de wiring.
- Priorize injeção explícita em fronteiras de domínio e integrações externas.

- Decisão arquitetural: padronizar construtor como default e restringir setter para dependências opcionais.
- Acoplamento técnico cai com DI, mas acoplamento cognitivo pode subir sem convenções de projeto.

**Correlatos**: Acoplamento e Coesão, SOLID, Teste Unitário.

## Isolamento de Transações

### Junior

- Isolamento define como transações simultâneas enxergam dados umas das outras.
- Níveis comuns: Read Uncommitted, Read Committed, Repeatable Read, Serializable.

### Pleno

- Quanto maior o isolamento, maior a proteção contra anomalias.
- Quanto maior o isolamento, maior chance de lock e queda de throughput.
- Selecionar nível por caso de uso evita custo desnecessário.

### Senior

- Decisão deve equilibrar consistência, latência e concorrência.
- Em operações críticas de saldo/estoque, níveis mais rígidos podem ser necessários.
- Em leitura analítica, níveis mais leves podem aumentar eficiência.

**Correlatos**: ACID, Lock Otimista e Pessimista, Transactions.

## JWT

### Junior

- JWT (JSON Web Token) é token assinado que carrega claims de autenticação/autorização.
- Normalmente é enviado no header Authorization: Bearer.

### Pleno

**Boas práticas**
- Definir expiração curta para access token.
- Usar refresh token separado.
- Validar assinatura, issuer, audience e exp.
- Nunca colocar segredo sensível dentro do payload.

### Senior

**Trade-offs**
- JWT reduz consulta ao servidor de sessão, mas dificulta revogação imediata.
- Em ambientes críticos, combine JWT com blacklist/versionamento de sessão.
- Para arquitetura distribuída, padronize rotação de chaves (JWKS).

**Correlatos**: OAuth 2.0, Token de Autenticação, RBAC.

## Observabilidade (Logs, Métricas e Traces)

### Junior

- Observabilidade é a capacidade de entender o comportamento do sistema em produção.
- Três pilares: logs, métricas e traces.

### Pleno

**Práticas recomendadas**
- Logs estruturados (JSON) com contexto de requisição.
- Métricas de latência, erro, throughput e saturação.
- Tracing distribuído para mapear fluxo entre serviços.

**Exemplo de log estruturado**

```json
{
  "level": "info",
  "message": "payment approved",
  "trace_id": "9f3d...",
  "order_id": "ord_123",
  "latency_ms": 42
}
```

### Senior

- Sem observabilidade, incidentes viram diagnóstico por tentativa e erro.
- Defina SLI/SLO e alertas orientados a impacto de usuário, não só CPU/memória.
- Integre observabilidade ao ciclo de entrega (deploy, rollback, postmortem).

**Correlatos**: SLI/SLO/SLA, Logging, Monitoring, Tracing.

## OAuth 2.0

### Junior

- OAuth 2.0 é protocolo para autorização delegada.
- Permite que um app acesse recursos de outro serviço sem expor senha do usuário.

### Pleno

**Fluxos comuns**
- Authorization Code + PKCE (web/mobile).
- Client Credentials (serviço para serviço).

**Boas práticas**
- Validar redirect URI.
- Usar escopos mínimos.
- Guardar tokens com segurança.

### Senior

- OAuth 2.0 resolve autorização, mas não substitui modelagem de permissões.
- Padronize políticas de expiração, rotação e revogação.
- Avalie token introspection vs JWT local conforme latência e risco.

**Correlatos**: JWT, SSO, RBAC, Segurança de API.

## SQL

### Junior

- SQL é linguagem para consultar e manipular dados em banco relacional.
- Operações base: SELECT, INSERT, UPDATE, DELETE.

### Pleno

**Boas práticas**
- Evitar SELECT * em produção.
- Criar índices conforme padrão de consulta.
- Usar transação para operações relacionadas.
- Validar plano de execução para consultas pesadas.

**Exemplo simples**

```sql
SELECT id, email
FROM users
WHERE status = 'ACTIVE'
ORDER BY created_at DESC
LIMIT 50;
```

### Senior

**Trade-offs**
- Índice acelera leitura, mas aumenta custo de escrita.
- Normalização reduz redundância; desnormalização pode reduzir latência de leitura.
- Otimização real depende de workload e cardinalidade.

**Correlatos**: ACID, Índices, Isolamento de Transações, N+1 Query.

## Teste Unitário

### Junior

- Teste unitário valida uma unidade pequena de código de forma isolada.
- Deve ser rápido, determinístico e independente de infraestrutura externa.

### Pleno

**Boas práticas**
- Um teste deve validar um comportamento.
- Nome descritivo do cenário.
- Usar doubles (mock/stub/fake) para dependências externas.
- Evitar acoplar teste a detalhe interno sem valor de negócio.

**Exemplo simples**

```ts
it("deve aplicar desconto para cliente premium", () => {
  const total = calculateTotal(100, true);
  expect(total).toBe(90);
});
```

### Senior

- Suite de unitário precisa equilibrar cobertura e custo de manutenção.
- Testes frágeis geram falso negativo e reduzem confiança no pipeline.
- Defina estratégia integrada com teste de integração e E2E (pirâmide de testes).

**Correlatos**: TDD, Teste de Integração, Teste E2E, Code Review.

## Teorema CAP

### Junior

- Em sistemas distribuídos, CAP descreve três propriedades: Consistência, Disponibilidade e Tolerância a Partição.
- Na presença de partição de rede, normalmente você precisa priorizar Consistência ou Disponibilidade.

### Pleno

- Cenário CP (Consistência + Partição): prioriza dado consistente e pode recusar operação durante falha de rede.
- Exemplos práticos de perfil CP: etcd, ZooKeeper e bancos configurados para quorum rígido.
- Cenário AP (Disponibilidade + Partição): mantém resposta mesmo com divergência temporária de dados.
- Exemplos práticos de perfil AP: Cassandra e DynamoDB em leitura eventual.

### Senior

- CAP é decisão de produto e arquitetura, não apenas decisão de banco.
- Domínios financeiros tendem a CP; domínios de feed/social tendem a AP com consistência eventual.
- Arquiteturas híbridas por fluxo são comuns: forte consistência para comando, eventual para leitura.
- Métricas-chave: lag de replicação, taxa de conflito, impacto em SLA de negócio.

**Correlatos**: Consistência Eventual, Quorum, ACID.

## Teorema dos 3 Trade-offs (Clean, Fast, Complex)

### Junior

- Regra prática: é difícil maximizar simultaneamente Clean, Fast e Complex.
- Clean: código legível e fácil de manter.
- Fast: velocidade de entrega.
- Complex: solução sofisticada/poderosa.

### Pleno

**Combinações típicas**
- Clean + Fast: entrega rápida com solução mais simples.
- Clean + Complex: solução robusta, porém mais lenta de construir.
- Fast + Complex: entrega rápida com maior risco de débito técnico.

### Senior

- Trade-off deve ser decisão explícita por fase do produto.
- Fast + Complex sem plano de redução de dívida técnica costuma travar evolução.
- Clean + Complex pode ser necessário em domínio crítico, mesmo com maior lead time.
- Engenharia deve registrar decisão, risco aceito e estratégia de mitigação.

**Correlatos**: Débito Técnico, Arquitetura Evolutiva, Qualidade de Código.

## Streaming e Buffer

### Junior

- Streaming processa dados em partes (chunks), sem carregar tudo na memória.
- Buffer é área temporária que absorve variação de velocidade entre origem e destino.

### Pleno

- Chunks permitem iniciar processamento antes do arquivo/payload completo.
- Buffer reduz gargalo entre componentes com velocidades diferentes, por exemplo disco vs rede.
- Backpressure controla o ritmo para evitar estouro de memória e fila.

### Senior

**Trade-offs arquiteturais**
- Streaming reduz pico de memória e melhora escalabilidade, mas aumenta complexidade operacional.
- Tamanho de chunk afeta throughput e latência; não existe valor único ideal.
- Instrumentação de buffer/fila é essencial para detectar saturação antes de incidente.
- Em sistemas de alto volume, streaming e backpressure deixam de ser otimização e viram requisito.

**Correlatos**: Event Stream, Backpressure, Processamento Assíncrono.

---

## Backlog de Implementação (Deduplicado e Alfabético)

### Próximos termos de alta prioridade

- Caching HTTP
- CI/CD
- CQRS
- CORS
- gRPC
- GraphQL
- Idempotência
- Índices
- Lock Otimista e Pessimista
- N+1 Query
- OWASP Top 10
- Redis
- REST
- Semantic Versioning
- SLI, SLO e SLA

### Critério de entrada para novos termos

- Relevância operacional no dia a dia da equipe.
- Frequência em incidentes, bugs ou discussões de PR.
- Dependência direta para entendimento de termos já publicados.

---

## Glossário Rápido dos Termos Anotados

Formato: termo -> o que é | para que serve | contexto prático de uso.

Nota editorial: termos já aprofundados no bloco técnico principal aparecem aqui como referência curta para evitar duplicidade.

### Paradigmas e Conceitos de Programação

Contexto do domínio: base para escrever código legível, testável e com baixo acoplamento.

- Encapsulamento -> ocultar estado interno e expor apenas contratos públicos.
- Generics -> criar código reutilizável com segurança de tipos em compile-time.
- Herança -> reutilizar comportamento via hierarquia; útil com moderação.
- Injeção de dependência -> ver seção técnica "Injeção de Dependência" (Junior/Pleno/Senior).
- Linguagem compilada vs interpretada -> compara execução por binário pré-compilado vs runtime.
- Linguagem de alto nível vs baixo nível -> abstração alta para produtividade vs controle próximo do hardware.
- Linguagem de máquina -> instruções binárias executadas diretamente pela CPU.
- Linguagem tipada -> linguagem com sistema de tipos para validar dados e operações.
- Linguagem estática e dinâmica -> tipos em compilação vs tipos em tempo de execução.
- Polimorfismo -> usar a mesma interface com diferentes implementações.
- Recursividade -> função que chama a si mesma para resolver problemas subdivididos.
- Representação cíclica de objetos -> objetos que se referenciam mutuamente, exigindo cuidado em serialização.
- Variable Shadowing -> variável local que "esconde" variável de escopo superior.

### Padrões e Arquitetura de Software

Contexto do domínio: orienta decisões de design quando o sistema cresce e o custo de mudança aumenta.

- Arquitetura de Software -> organização de componentes, responsabilidades e integrações.
- CQRS -> separa leitura (query) de escrita (command), permitindo modelos e escalas diferentes para cada lado. Faz sentido quando leitura e escrita têm perfis muito distintos, mas aumenta complexidade de sincronização e observabilidade.
- Design Patterns -> soluções recorrentes para problemas comuns de design.
- MVP -> padrão de apresentação com Model, View e Presenter.

### Ferramentas e Frameworks

Contexto do domínio: stack de implementação e entrega; impacta produtividade, observabilidade e manutenção.

- Docker -> empacota aplicação e dependências em container.
- Docker Compose -> orquestra múltiplos containers localmente.
- Dockerfile -> receita para construir imagem Docker.
- ECMAScript -> especificação padrão da linguagem JavaScript.
- Jenkins -> servidor de automação para pipelines CI/CD.
- Maven -> build/dependency manager para ecossistema Java.
- Nest.js -> framework Node.js para backend modular e escalável.
- Next.js -> framework React com SSR/SSG e rotas.
- Nginx -> servidor web/proxy reverso de alto desempenho.
- Ngrok -> túnel seguro para expor serviço local na internet.
- Node.js -> ver seção técnica "JavaScript e Node.js" (foco em V8, JIT, Event Loop e modelo não bloqueante).
- npm -> gerenciador de pacotes do ecossistema Node.js.
- React.js -> biblioteca para interfaces baseadas em componentes.
- Spring / Spring Boot -> framework Java para aplicações enterprise e APIs.
- Tomcat -> servlet container para aplicações Java web.
- Vite -> bundler/dev server rápido para frontend moderno.
- Vue.js -> framework progressivo para frontend.
- Webpack -> module bundler configurável para apps web.
- Yarn -> gerenciador de pacotes alternativo ao npm.

### APIs, Protocolos e Web

Contexto do domínio: fronteira entre serviços e clientes; define contrato, latência, segurança e evolução.

- API -> contrato de comunicação entre sistemas.
- CDN -> rede distribuída para entrega rápida de conteúdo estático.
- CORS -> política de navegador para controlar chamadas entre origens diferentes. No dia a dia, você configura quais domínios, headers e métodos são permitidos para evitar bloqueios indevidos e exposição excessiva.
- Flush HTTP -> envio parcial/imediato do buffer de resposta HTTP (streaming/chunked).
- Protocolos de comunicação -> regras para troca de dados (HTTP, gRPC, WebSocket etc.).
- REST -> estilo arquitetural para APIs orientadas a recursos e verbos HTTP semânticos. É a opção padrão para integração web por simplicidade operacional, cacheabilidade e ecossistema maduro.
- RESTful -> API que aplica princípios REST de forma consistente.
- RPC -> chamada remota de procedimento como se fosse função local, focada em contratos de operação. Útil em comunicação interna entre serviços quando tipagem forte e baixa latência são prioridade.
- Verbos HTTP -> métodos semânticos de operação (GET, POST, PUT, PATCH, DELETE).
- Web Hooks -> eventos enviados automaticamente para URL assinante.
- Web Services -> serviços acessados por rede via contrato/protocolo.
- Web Worker API -> execução de tarefas em thread separada no navegador.

### Conceitos de Backend e Infraestrutura

Contexto do domínio: sustentação operacional do sistema em produção (escala, disponibilidade e custo).

- Blob -> objeto binário grande (arquivo, mídia, payload não estruturado).
- Cache -> armazenamento temporário para reduzir latência e custo de acesso.
- Cluster -> conjunto de nós cooperando como um sistema único.
- DNS -> sistema que converte domínio em endereço IP.
- Database -> sistema de persistência e consulta de dados.
- Escalamento horizontal/vertical -> escalar por mais nós vs mais recurso no mesmo nó.
- Garbage Collector -> mecanismo automático de gerenciamento de memória.
- Infraestrutura redundante -> duplicação de componentes para alta disponibilidade.
- Load balancer / Node balancer -> distribuidor de tráfego entre instâncias.
- Middleware -> componente intermediário que processa requisições/respostas.
- POD -> menor unidade de deploy no Kubernetes.
- Proxy -> intermediário entre cliente e serviço de destino.
- Proxy Reverso -> proxy na borda que representa serviços internos.
- SGBD -> software de gerenciamento de banco de dados.
- Servidor -> processo/máquina que atende requisições.
- Servidor Web -> servidor especializado em HTTP/HTTPS.
- Servlet -> componente Java para processar requisições web.
- SLA -> acordo de nível de serviço (disponibilidade, suporte, resposta).
- Worker -> processo dedicado a tarefas assíncronas/background.

### Cloud, AWS e Serviços Gerenciados

Contexto do domínio: acelera entrega com serviços prontos, trocando controle fino por abstração gerenciada.

- AppDynamics -> plataforma APM para monitoramento de performance.
- Brokers de Mensagem -> intermediários para mensageria assíncrona entre serviços.
- Cloud -> computação sob demanda com elasticidade e serviços gerenciados.
- Keycloak -> IAM open source para autenticação/autorização.
- Kubernetes -> orquestrador de containers em escala.
- Lightsail -> serviço simplificado da AWS para workloads básicos.

### Conceitos de Autenticação e Segurança

Contexto do domínio: protege identidade, dados e integrações; erros aqui viram incidente rapidamente.

- Criptografia no lado do servidor -> proteção de dados em trânsito/repouso no backend.
- JWT -> token assinado com claims para autenticação/autorização sem consultar sessão a cada request. Traz escala de leitura, mas requer política clara de expiração, rotação de chave e revogação.
- OAuth 2.0 -> protocolo de autorização delegada para um cliente acessar recursos em nome do usuário/serviço. No dia a dia, os fluxos mais comuns são Authorization Code + PKCE e Client Credentials.
- Protocolos de autenticação e autorização -> mecanismos para identidade e permissões.
- SSH -> protocolo seguro de acesso remoto e transferência.
- SSL/TLS -> criptografia do canal de comunicação.
- SSO -> login único para múltiplos sistemas.
- Token de autenticação -> credencial usada para provar identidade em APIs.

### Banco de Dados e Consultas

Contexto do domínio: integridade e performance de dados; decisões de modelagem impactam todo o produto.

- Associações -> relacionamentos entre entidades/tabelas.
- Banco relacional/não relacional -> modelo tabular transacional vs modelos flexíveis.
- Constraints -> regras de integridade aplicadas pelo banco.
- Lock pessimista x otimista -> estratégia de concorrência por bloqueio vs validação de versão.
- Primary key -> identificador único de registro.
- Foreign key -> referência entre tabelas para integridade referencial.
- Indexes -> estruturas auxiliares para acelerar leitura, filtragem e ordenação no banco. Melhoram SELECTs, porém aumentam custo de escrita e armazenamento.
- Eager loading -> carregar relações antecipadamente para evitar múltiplas queries.
- Job (SQL) -> tarefa agendada de banco (rotina de manutenção/processamento).
- Migrations -> versionamento evolutivo de schema.
- ORM -> mapeamento objeto-relacional para acesso a dados com menos SQL manual. Aumenta produtividade, mas precisa cuidado com N+1, escopo de transação e queries geradas automaticamente.
- Procedure -> rotina SQL armazenada no banco.
- SQL joins -> combinação de linhas entre tabelas relacionadas.
- Schema -> estrutura lógica de tabelas, colunas e relações.
- Transactions -> conjunto atômico de operações de banco (commit ou rollback). Essencial para consistência de regras de negócio que envolvem múltiplas alterações dependentes.

### Testes e Qualidade de Código

Contexto do domínio: reduz regressão e aumenta confiança em deploy contínuo.

- BDD -> abordagem de testes orientada a comportamento esperado.
- Sonar -> plataforma de análise estática e qualidade de código.
- TDD -> desenvolvimento guiado por testes (red-green-refactor).
- Teste E2E -> valida fluxo completo do usuário/sistema.
- Teste Unitário -> valida unidade isolada de código.
- Teste de Integração -> valida interação entre módulos/infra real.

### Ferramentas de Desenvolvimento e Build

Contexto do domínio: transforma código-fonte em artefato executável e padroniza ambiente de desenvolvimento.

- Babel -> transpila JavaScript para maior compatibilidade.
- Module Bundler -> empacota módulos e assets para deploy web.
- Package Manager -> instala e gerencia dependências de projeto.
- SDK -> kit de desenvolvimento com libs, ferramentas e docs.
- TanStack Query -> biblioteca para cache/sincronização de estado servidor no frontend.
- Vite -> ferramenta moderna para build e dev server frontend.
- Webpack -> bundler tradicional e altamente configurável.

### Programação Assíncrona e Concorrência

Contexto do domínio: melhora throughput e responsividade em cenários de I/O e alta simultaneidade.

- Async/Await -> sintaxe para programação assíncrona com Promises.
- Event Loop (JavaScript) -> ciclo que agenda execução de callbacks/tarefas assíncronas.
- Event Stream -> fluxo contínuo de eventos processados incrementalmente.
- Goroutines -> concorrência leve em Go.
- Graceful Shutdown -> encerramento controlado sem perder requisições/dados.
- Paralelismo e Concorrência -> executar ao mesmo tempo vs intercalar tarefas.
- Threads -> unidades de execução dentro de um processo.

### Estruturas de Dados

Contexto do domínio: define custo de tempo/memória e influencia diretamente desempenho de funcionalidades.

- Arrays -> coleção indexada de elementos.
- Filas (Queue) -> estrutura FIFO.
- Grafos (Graphs) -> nós e arestas para modelar relações.
- Hash Tables -> mapeamento chave-valor com acesso rápido.
- Heaps -> árvore para prioridade (min/max).
- Listas Ligadas (Linked Lists) -> nós encadeados por ponteiros/referências.
- Pilhas (Stacks) -> estrutura LIFO.
- Trees -> estruturas hierárquicas de nós.
- Tries -> árvore para prefixos e busca textual.

### Vue.js / Frontend Moderno

Contexto do domínio: experiência do usuário, tempo de carregamento e organização da camada de interface.

- Composables -> funções reutilizáveis de lógica no ecossistema Vue.
- Front end -> camada de interface e interação do usuário.
- Lazy Loading (Code Splitting) -> carregar código sob demanda para reduzir bundle inicial.
- Tree Shaking -> remoção de código não usado no build.

### Termos Gerais e Misc.

Contexto do domínio: conceitos transversais usados em arquitetura, operação, performance e colaboração.

- Active Directory -> serviço de diretório/autenticação corporativa.
- Repository -> repositório de código/versionamento de artefatos.
- ALB -> Application Load Balancer.
- Apache Mesos -> orquestração de recursos para workloads distribuídos.
- Arquivos binários -> arquivos não texto (imagens, executáveis, mídia).
- Biblioteca vs Framework -> biblioteca é chamada por você; framework chama seu código.
- Binários -> arquivos executáveis compilados.
- Boilerplate -> código repetitivo de configuração/estrutura.
- Buffer -> área temporária para armazenamento transitório de dados.
- Circuit Breakers -> padrão de resiliência para falhas em dependências.
- Classes -> molde que define estado e comportamento de objetos.
- Curl -> ferramenta de linha de comando para requisições HTTP.
- Developer -> profissional que projeta, implementa e mantém software.
- DLL -> biblioteca dinâmica carregada em tempo de execução.
- Eager Loading -> carregamento antecipado de dados relacionados.
- ESModules -> padrão de módulos JavaScript (import/export).
- Infraestrutura -> base de recursos computacionais e de rede.
- JDK -> kit Java com compilador e ferramentas.
- jQuery -> biblioteca JS para manipulação DOM/eventos legados.
- JRE -> ambiente de execução Java.
- JVM -> máquina virtual que executa bytecode Java.
- LeetCode -> plataforma de prática de algoritmos e entrevistas.
- LLM -> modelo de linguagem de grande escala.
- Memória Swap -> uso de disco como extensão de RAM.
- Multi Tenant -> arquitetura em que múltiplos clientes compartilham a mesma aplicação e infraestrutura, com isolamento por tenant. Útil para SaaS com escala de custo; exige cuidado com isolamento de dados, rate limit por tenant e observabilidade por cliente.
- Multiplexer -> componente que agrega/desagrega múltiplos canais de dados.
- On Premises -> infraestrutura hospedada localmente pela própria organização.
- Ponteiro -> variável que referencia endereço de memória (comum em C/C++).
- Reflection -> inspeção/manipulação de tipos em runtime.
- Retry Policies -> política de repetição após falha (exponencial/jitter).
- Semáforo -> mecanismo de sincronização para acesso concorrente.
- SaaS -> software entregue como serviço via internet.
- Servidores -> recursos computacionais que hospedam serviços.
- Singleton -> padrão que garante instância única de uma classe.
- Sistemas de codificação -> padrões de representação de caracteres/dados (UTF-8, Base64 etc.).
- Streaming -> ver seção técnica "Streaming e Buffer" para detalhes de chunks, buffer e backpressure.
- CAP Theorem -> ver seção técnica "Teorema CAP" para cenários CP/AP e implicações arquiteturais.
- Regra dos 3 trade-offs -> ver seção técnica "Teorema dos 3 Trade-offs" para combinações e impacto de débito técnico.
- Terminais (zsh, Git Bash, kernel) -> shells/ambientes para execução de comandos.

### Validação rápida das suas anotações

- Faz sentido: a maior parte dos termos está correta e relevante para um manual de engenharia.
- Ajuste recomendado: "Stan Stack Query" -> "TanStack Query".
- Ajuste recomendado: "JDM" provavelmente era "JDK".
- Ajuste recomendado: "Node Balancer" -> usar "Load Balancer" como termo principal.
- Ajuste recomendado: "Criptografia no lado do servidor (SSE)" está ambíguo.
- Se você quis dizer eventos de servidor, usar "Server-Sent Events (SSE)".
- Se você quis dizer segurança, usar "criptografia em repouso e em trânsito".
- Ajuste editorial: termos duplicados foram mantidos uma vez no índice principal.

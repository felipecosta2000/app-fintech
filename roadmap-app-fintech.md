# Roadmap Prático — Desenvolvimento de App no Segmento Financeiro (Fintech)

> Estrutura pensada para virar um plano de projeto real (Jira/Trello/Notion), com fases, entregáveis, papéis e critérios de saída. Ajuste prazos conforme o tamanho da equipe e a complexidade do produto (carteira digital, meios de pagamento, crédito, investimentos, etc.).

---

## Visão Geral das Fases

| Fase | Duração típica (MVP) | Foco principal |
|---|---|---|
| 0. Descoberta & Compliance Inicial | 2–4 semanas | Regulatório, modelo de negócio, requisitos |
| 1. Design (UX/UI + Arquitetura) | 3–5 semanas | Fluxos, protótipos, arquitetura técnica |
| 2. Backend & Infraestrutura | 8–14 semanas | APIs, banco de dados, integrações |
| 3. Segurança & Compliance by Design | Contínuo (paralelo) | LGPD, PCI-DSS, KYC/AML, criptografia |
| 4. Testes & QA | 3–6 semanas | Funcional, segurança, carga, homologação |
| 5. Lançamento | 2–4 semanas | Soft launch, monitoramento, escala |
| 6. Pós-lançamento | Contínuo | Observabilidade, auditoria, evolução |

As fases 3 (segurança) e parte da 4 (testes) **não são sequenciais** — elas correm em paralelo desde o dia 1. Em fintech, tratar segurança como etapa final é o erro mais comum e mais caro de corrigir depois.

---

## Fase 0 — Descoberta & Compliance Inicial

**Objetivo:** decidir o que pode e deve ser construído antes de gastar uma linha de código.

**Atividades:**
- Definir o modelo regulatório: instituição de pagamento (IP), correspondente bancário, SCD/SEP (para crédito), ou parceria com banco/BaaS (Banking as a Service).
- Mapear exigências do Banco Central (BCB) aplicáveis ao modelo escolhido (ex.: Resolução BCB para instituições de pagamento, regras de Open Finance, PIX).
- Levantar exigências de LGPD (base legal para tratamento de dados, DPO, política de privacidade).
- Definir se haverá emissão de cartão, custódia de saldo, crédito ou apenas intermediação — isso muda todo o escopo técnico e regulatório.
- Elaborar documento de requisitos (PRD) com personas, jornadas críticas e requisitos não-funcionais (disponibilidade, latência, auditabilidade).

**Entregáveis:**
- Parecer jurídico/regulatório preliminar
- PRD (Product Requirements Document)
- Matriz de riscos regulatórios
- Decisão: construir licença própria vs. BaaS/parceiro

**Critério de saída:** modelo de negócio e enquadramento regulatório validados por jurídico/compliance.

---

## Fase 1 — Design (UX/UI + Arquitetura de Informação)

**Objetivo:** transformar requisitos em fluxos navegáveis e numa arquitetura técnica validada.

**Atividades de UX/UI:**
- Wireframes dos fluxos críticos: onboarding/KYC, login/autenticação forte, transações, extrato, suporte.
- Protótipo navegável (Figma) com foco em fricção mínima no KYC (upload de documento, selfie, prova de vida) sem comprometer segurança.
- Design system com componentes reutilizáveis e acessibilidade (WCAG) — obrigatório em produtos financeiros regulados.
- Testes de usabilidade com usuários reais antes de codar.

**Atividades de Arquitetura Técnica:**
- Escolha do modelo: microsserviços vs. monolito modular (a maioria das fintechs early-stage começa com monolito modular bem separado, migrando depois).
- Definição de stack (ex.: backend em Node/Java/Kotlin/Go; banco relacional para dados transacionais + solução de cache/fila).
- Desenho de arquitetura de eventos para transações (idempotência, filas, reconciliação).
- Definição de integrações externas: PIX (SPI/DICT via PSP ou BCB), antifraude, bureaus de crédito, KYC/biometria, gateway de pagamento.

**Entregáveis:**
- Protótipo Figma validado
- Diagrama de arquitetura (C4 ou similar)
- Documento de decisão técnica (ADR) para stack e integrações

**Critério de saída:** protótipo aprovado por stakeholders + arquitetura revisada por segurança.

---

## Fase 2 — Backend & Infraestrutura

**Objetivo:** construir o núcleo transacional com consistência, idempotência e rastreabilidade.

**Atividades:**
- Modelagem do "ledger" (livro-razão) — toda operação financeira deve ser auditável e imutável (event sourcing ou double-entry ledger).
- APIs de autenticação (OAuth2/OIDC, MFA, biometria) e de negócio (contas, transações, extrato).
- Integração com PIX/gateway de pagamento com tratamento de idempotência (evitar duplicidade de transações).
- Motor de KYC/onboarding: validação documental, biometria facial, checagem em listas restritivas (PEP, sanções, OFAC).
- Motor antifraude básico (regras + score) desde o MVP.
- Infraestrutura: cloud (AWS/GCP/Azure) com ambientes segregados (dev/homolog/produção), backups, disaster recovery.
- Observabilidade: logs estruturados, métricas, tracing distribuído desde o início (essencial para auditoria futura).

**Entregáveis:**
- APIs documentadas (OpenAPI/Swagger)
- Ledger transacional funcional com trilha de auditoria
- Pipeline de CI/CD
- Ambientes de dev/homologação/produção isolados

**Critério de saída:** transação de ponta a ponta (usuário cria conta → deposita via PIX → visualiza saldo) funcionando em homologação.

---

## Fase 3 — Segurança & Compliance by Design (paralela, contínua)

**Objetivo:** segurança não é um checkpoint, é uma camada transversal.

**Atividades:**
- Criptografia de dados em repouso (AES-256) e em trânsito (TLS 1.2+), com gestão de chaves (KMS/HSM).
- Tokenização de dados sensíveis (nunca armazenar dados de cartão em texto puro — aderência a PCI-DSS se houver cartão).
- Controle de acesso: princípio do menor privilégio, segregação de funções, MFA obrigatório para times internos.
- Política de retenção e descarte de dados conforme LGPD.
- Plano de resposta a incidentes e comunicação de vazamentos (obrigatório sob LGPD e regulação do BCB).
- Pentest e análise de vulnerabilidades (SAST/DAST) integrados ao pipeline de CI/CD.
- Auditoria de logs com trilha imutável (WORM) para rastreabilidade de transações.

**Entregáveis:**
- Política de segurança da informação documentada
- Relatório de pentest
- Plano de resposta a incidentes (runbook)
- Certificações/adequações aplicáveis (PCI-DSS se cartão; ISO 27001 é diferencial competitivo)

**Critério de saída:** nenhuma vulnerabilidade crítica/alta em aberto antes do lançamento; DPO e jurídico validam conformidade LGPD.

---

## Fase 4 — Testes & QA

**Objetivo:** garantir que o app funciona sob estresse real e resiste a abuso.

**Tipos de teste necessários:**
- **Funcionais:** fluxos de onboarding, transações, estornos, disputas.
- **Testes de integração:** PIX, antifraude, bureaus, gateways — incluindo cenários de falha e timeout dos parceiros.
- **Testes de carga/performance:** simular picos de transação (ex.: horário de pagamento de salário, Black Friday).
- **Testes de segurança:** pentest, teste de invasão, fuzzing de APIs.
- **Testes de reconciliação financeira:** garantir que todo centavo movimentado bate entre ledger interno, parceiro de pagamento e extrato do usuário.
- **Testes de usabilidade/acessibilidade** com usuários reais.
- **Homologação regulatória**, se aplicável (ex.: certificação junto ao PSP/BCB para operar PIX).

**Entregáveis:**
- Plano de testes e casos de teste documentados
- Relatório de cobertura de testes automatizados
- Relatório de reconciliação financeira sem divergências
- Sign-off de QA, segurança e compliance

**Critério de saída:** zero bugs críticos abertos, reconciliação 100% batendo, aprovação formal de compliance.

---

## Fase 5 — Lançamento

**Objetivo:** ir ao ar de forma controlada, com capacidade de reverter rapidamente.

**Atividades:**
- **Soft launch / beta fechado:** grupo pequeno de usuários reais, limite de valor transacionado por usuário.
- Feature flags para liberar funcionalidades gradualmente.
- Monitoramento em tempo real de erros, latência e transações suspeitas (dashboards + alertas).
- Plano de rollback definido e testado.
- Preparação de suporte ao cliente (SAC, canais de reclamação — obrigatório ter canal formal para reclamações financeiras).
- Comunicação com reguladores, se exigido pelo modelo (ex.: comunicar início de operação ao BCB quando aplicável).

**Entregáveis:**
- Checklist de go-live
- Runbook de operação (on-call)
- Dashboard de monitoramento ativo

**Critério de saída:** operação estável no beta fechado por período definido (ex.: 2–4 semanas) sem incidentes críticos, antes de abrir para público geral.

---

## Fase 6 — Pós-lançamento (contínuo)

**Objetivo:** manter confiabilidade, conformidade e evoluir o produto.

**Atividades:**
- Monitoramento contínuo de fraude e comportamento anômalo.
- Auditorias periódicas de segurança e compliance (internas e externas).
- Atualização constante frente a mudanças regulatórias (BCB, LGPD, Open Finance).
- Ciclo de melhoria contínua via feedback de usuários e métricas de produto (NPS, churn, tempo de onboarding).
- Relatórios financeiros e prestação de contas conforme exigência regulatória.

---

## Papéis Recomendados na Equipe

| Papel | Responsabilidade central |
|---|---|
| Product Manager | Prioriza escopo, mantém PRD atualizado |
| Compliance Officer / Jurídico | Garante aderência regulatória contínua |
| Arquiteto de Software | Define e valida decisões técnicas críticas |
| Engenheiros Backend | Ledger, APIs, integrações |
| Engenheiro de Segurança / AppSec | Pentest, revisão de código, políticas |
| Designer UX/UI | Fluxos e protótipos |
| QA/Test Engineer | Planos e automação de testes |
| DPO (Data Protection Officer) | Conformidade com LGPD |
| SRE/DevOps | Infraestrutura, observabilidade, disponibilidade |

---

## Como transformar isso em plano de projeto real

1. Copie cada fase para o seu board (Jira/Linear/Notion) como um **Épico**.
2. Quebre cada atividade em **tarefas** com estimativa e responsável.
3. Marque as dependências entre fases (ex.: "Fase 4 não inicia sem Fase 2 concluída em X%").
4. Defina **gates de aprovação** (compliance, segurança, QA) como marcos formais que bloqueiam avanço até assinatura.
5. Rode segurança e testes desde o Sprint 1, não apenas no fim — trate como backlog contínuo, não como fase isolada.

---

*Observação: este roadmap é um guia prático de planejamento, não aconselhamento jurídico ou regulatório. Antes de operar, valide o enquadramento com um advogado especializado em direito bancário/fintech e, se aplicável, consulte diretamente as normas vigentes do Banco Central do Brasil.*

<!--
Sync Impact Report
Version change: 1.1.0 -> 1.1.1
Modified principles:
- I. Arquitetura Evidence-First: clarified validation-before-review ordering
- IV. Governanca Hibrida Humano + IA: clarified review is triggered after
  mechanical validation when needed
- V. Liquidacao On-Chain, Dados Off-Chain: unchanged
Modified sections:
- Fluxo de Execucao e Quality Gates
Added principles:
- None
Added sections:
- None
Removed sections:
- None
Templates requiring updates:
- .specify/templates/plan-template.md: no change required
- .specify/templates/spec-template.md: no change required
- .specify/templates/tasks-template.md: no change required
- .specify/templates/checklist-template.md: no change required
- .specify/templates/commands/*.md: not present
- AGENTS.md: no change required
Follow-up TODOs:
- None
-->
# AAO Protocol Constitution

## Core Principles

### I. Arquitetura Evidence-First

Toda unidade de trabalho do AAO Protocol DEVE produzir um evidence bundle antes
de ser considerada concluida. O bundle DEVE conter, no minimo: identificador da
tarefa, decisao de politica aplicada, identidade publica do executor,
referencias de artefatos, logs ou transcricoes relevantes, resultados de
validacao, hashes/CIDs e resultado de revisao quando a politica exigir.

Evidence hash commitment NAO prova qualidade por si so. Toda validacao DEVE
incluir checks semanticos apropriados ao tipo da tarefa, e tarefas subjetivas
DEVEM declarar como serao revisadas. Recompensa, slashing, liquidacao e
alteracao de reputacao NAO PODEM ocorrer antes da aceitacao do evidence bundle.

Rationale: agentes podem falhar, mentir ou ser comprometidos. A evidencia e a
unidade minima de auditoria que permite validacao independente, disputa e
reconstrucao do que aconteceu.

### II. Autonomia Governada por Politica

Agentes sao tratados como nao confiaveis por padrao. Toda permissao DEVE ser
explicita, versionada e auditavel antes da execucao. O handshake do agent
adapter DEVE declarar chave publica, tipos de mensagens aceitas e rejeitadas,
niveis de permissao, limites de autonomia e capacidades reivindicadas ou
observadas.

Toda tarefa DEVE definir regra de aprovacao, regra de evidencia, regra de
autonomia e consequencias de reward/slashing antes de ser distribuida na rede
P2P. Qualquer acao fora da politica, acima da permissao concedida ou dependente
de julgamento humano DEVE bloquear a execucao autonoma e acionar revisao.

Rationale: autonomia so e segura quando o sistema consegue provar qual politica
autorizou cada passo e onde a automacao teve que parar.

### III. Economia de Reputacao Verificavel

Reputacao e capability DEVE derivar de evidencias aceitas ou rejeitadas,
metricas de validacao, revisoes, historico de tarefas e eventos de
reward/slashing. O protocolo NAO DEVE aceitar reputacao baseada apenas em
declaracao propria do agente.

Capability scores DEVEM ser especificos por dominio de tarefa e rastreaveis ate
evidence bundles concretos. Mudancas de reputacao DEVEM ser deterministicas ou
explicadas por uma regra de governanca registrada. O desenho de identidade,
stake, cooldowns, revisoes e propagacao P2P DEVE mitigar sybil attacks e gaming
de reputacao.

Rationale: o mercado distribuido de agentes so funciona se selecao, pagamento e
slashing forem baseados em desempenho verificavel, nao em marketing do proprio
agente.

### IV. Governanca Hibrida Humano + IA

Public Company, Private Company e Hybrid Company DEVEM ter propriedade, papeis,
politicas e limites de autoridade explicitamente modelados. DAO, owner, partner,
entrepreneur, C-level, member e reviewer SOMENTE PODEM agir dentro das
permissoes associadas ao seu papel.

Governanca DAO DEVE controlar politicas, tesouraria, token/stake e settlement
de companhias publicas e hibridas conforme o modelo declarado. Owners controlam
companhias privadas dentro das regras nao negociaveis de evidencia, seguranca e
auditoria do protocolo. Revisores humanos DEVEM ser acionados quando a politica
exigir, quando a tarefa for subjetiva demais ou quando validadores nao
conseguirem estabelecer qualidade com confianca suficiente.

Rationale: o protocolo governa execucao autonoma real, mas decisoes economicas,
subjetivas ou de risco ainda precisam de autoridade humana ou DAO claramente
responsavel.

### V. Liquidacao On-Chain, Dados Off-Chain

A camada Solana DEVE registrar governanca DAO, tesouraria, token/stake,
commitments de politica, commitments de hash de evidencia, resultado de revisao
e settlement. Dados sensiveis, segredos, artefatos privados e contexto de
workspace NAO DEVEM ser colocados on-chain.

Artefatos, manifestos, snapshots, referencias, commits, indices, evidencias,
revisoes e CIDs DEVEM viver off-chain em storage verificavel, com IPFS como
padrao e providers customizados permitidos quando preservarem os mesmos
contratos de evidencia, privacidade, criptografia e controle de acesso.
Settlement on-chain SOMENTE PODE ocorrer depois de policy check, validacao de
evidencia, revisao quando exigida e atualizacao de reputacao.

Rationale: a blockchain deve ser a fonte de commitments economicos e de
governanca, nao um deposito de dados sensiveis ou mutaveis do workspace.

### VI. Clean Code e Manutenibilidade Modular

Codigo de producao DEVE ser pequeno, coeso, nomeado pela linguagem do dominio e
facil de substituir. Modulos DEVEM expor contratos explicitos, ocultar detalhes
internos e evitar dependencias ciclicas. Funcoes e classes DEVEM ter uma razao
clara para mudar; objetos ou servicos que concentram multiplas responsabilidades
DEVEM ser divididos antes de receberem novas regras.

Mudancas DEVEM preferir composicao, interfaces/ports e tipos expressivos a
condicionais globais, flags opacas ou acoplamento por estado compartilhado.
Duplicacao que representa a mesma regra de dominio DEVE ser consolidada.
Duplicacao acidental em testes pode existir quando torna o comportamento mais
legivel.

Rationale: o AAO Protocol sera evoluido por agentes e humanos. Modularidade
alta reduz custo de auditoria, melhora isolamento de falhas e torna evidence,
policy, reputacao e settlement mais testaveis.

### VII. Clean Architecture e DDD

Regras de dominio DEVEM ficar independentes de frameworks, banco de dados,
interfaces de usuario, rede P2P, storage e Solana. Casos de uso DEVEM orquestrar
o dominio por ports/adapters; adapters DEVEM depender do dominio, e o dominio
NAO DEVE depender de adapters.

Bounded contexts DEVEM ser explicitados para conceitos como Agent, Policy,
Evidence, Reputation, Company, Governance, Workspace, Storage e Settlement.
Entidades, value objects, domain events, repositories e domain services DEVEM
usar a linguagem ubiqua do contexto correspondente. Cross-context integration
DEVE acontecer por contratos ou eventos versionados, nao por acesso direto ao
estado interno de outro contexto.

Rationale: DDD protege o significado do protocolo. Clean Architecture protege
esse significado contra detalhes de runtime, infraestrutura e blockchain.

### VIII. BDD, TDD e Padrao Jest

Requisitos comportamentais DEVEM ser expressos em cenarios BDD no formato
Given/When/Then antes da implementacao. Para codigo JavaScript ou TypeScript,
Jest e o padrao obrigatorio de testes; suites DEVEM usar nomes de `describe`,
`it` e fixtures que reflitam a linguagem do dominio.

Mudancas de dominio, policy, evidence, reputation, adapter contracts, storage,
settlement e UX critica DEVEM seguir TDD: escrever teste Jest falhando,
implementar o minimo necessario, refatorar mantendo a suite verde. Mocks DEVEM
ficar nos ports externos; regras de dominio DEVEM ser testadas com objetos reais
ou builders de teste. Snapshots Jest SOMENTE PODEM cobrir estruturas estaveis e
DEVEM ser revisados como contrato, nao aceitos automaticamente.

Rationale: BDD alinha comportamento com linguagem de negocio. TDD com Jest cria
evidencia executavel de que contratos e regras continuam funcionando enquanto o
protocolo cresce.

### IX. Consistencia de Experiencia do Usuario

Interfaces humanas do AAO Protocol DEVEM ser consistentes em navegacao,
terminologia, estados, feedback, erros e revisao. A mesma entidade de dominio
DEVE ter o mesmo nome visual e textual em specs, UI, logs e evidence bundles,
salvo traducao explicitamente documentada.

Fluxos que envolvem aprovacao, bloqueio, review, rejeicao, slashing, reward,
settlement, sincronizacao de storage ou falha de agente DEVEM mostrar estado
atual, proximo passo, responsavel e evidencia associada. Componentes visuais
DEVEM reutilizar design tokens, padroes de layout, acessibilidade e controles
interativos antes de criar variantes novas.

Rationale: consistencia reduz erro operacional em governanca humano + IA e torna
decisoes auditaveis compreensiveis para revisores, owners, DAO e operadores.

### X. Performance como Contrato

Cada feature que afeta runtime, adapter, P2P, storage, validacao, reputacao,
workspace, UI ou Solana DEVE declarar budgets de performance mensuraveis no
plano. Budgets podem incluir latencia p95/p99, throughput, memoria, tamanho de
payload, custo de validacao, custo on-chain, tempo de sincronizacao e limites de
renderizacao.

Implementacoes DEVEM evitar loops sem limite, propagacao P2P redundante,
validacoes repetidas sem cache, serializacao excessiva, queries N+1, renders
desnecessarios e commits on-chain evitaveis. Otimizacoes DEVEM preservar
corretude, evidencia e clareza arquitetural; quando houver trade-off, o plano
DEVE registrar a decisao e a medicao que a justifica.

Rationale: verificacao, storage e governanca custam caro. Performance medida
mantem o protocolo viavel sem sacrificar auditabilidade.

## Limites de Arquitetura e Seguranca

O sistema DEVE manter tres zonas arquiteturais explicitas: Runtime externo,
Protocolo e Blockchain Solana. Runtimes como Codex, Claude, Cursor, Hermes e
OpenClaw executam fora do protocolo e NAO DEVEM ser tratados como autoridade de
registro. O protocolo e a camada que valida, orquestra, propaga evidencias e
coordena governanca.

O agent adapter e a fronteira obrigatoria entre runtime e protocolo. Entradas e
saidas de agentes DEVEM passar por contratos comuns, formatos verificaveis e
metadados suficientes para auditoria. Bypasses do adapter exigem justificativa
de governanca e controle equivalente de evidencia.

Cada secure node environment DEVE isolar wallet, private key, secrets vault,
auth tokens, encryption keys e permissoes. Comunicacao P2P DEVE autenticar
identidade publica do no e preservar rastreabilidade para discovery, task
distribution, evidence propagation, reputation signals, capability sharing,
reviewer flow e sincronizacao de storage.

O workspace DEVE organizar contexto por companhia, projeto e tarefa com
manifestos, snapshots, referencias, commits, indices, links de evidencia, links
de revisao e CIDs. Storage customizado e permitido, mas DEVE preservar
verificabilidade, privacidade e portabilidade dos artefatos.

## Fluxo de Execucao e Quality Gates

O fluxo normativo de execucao e: Task Proposal -> Policy -> Approval Check ->
Task Assignment via P2P -> Executor Run Through Adapter -> Artifact Ref
Produced -> Evidence Bundle Submitted -> Validation -> Review quando exigido
por politica ou validacao -> Reputation Update -> Reward/Slashing -> DAO Token
Settlement Record.

Validation DEVE ocorrer antes de Review para checar completude, identidade,
artefatos, privacidade e conformidade mecanica com politica. Review somente
ocorre quando a politica exigir julgamento humano/DAO ou quando Validation
retornar `needs-review`. Aceitacao final exige ValidationResult aprovado e,
quando aplicavel, ReviewOutcome aceito.

Toda feature ou mudanca arquitetural DEVE identificar quais zonas afeta:
runtime, adapter, P2P, registry, policy/governance, execution graph, workspace,
storage, secure node environment, proof pipeline ou Solana. O plano DEVE
registrar como a mudanca preserva evidencia, politica, reputacao, seguranca e
settlement.

Testes e verificacoes sao obrigatorios quando a mudanca altera contratos do
adapter, regras de politica, validadores de evidencia, calculo de reputacao,
storage, seguranca, P2P ou commitments on-chain. Quando uma tarefa for
subjetiva, o design DEVE incluir rota de revisao humana ou DAO, criterios de
aceitacao e como o bloqueio sera representado para a interface nao-agentica.

Nenhuma tarefa ou historia e considerada pronta sem demonstrar seu evidence
bundle esperado, sua validacao e o efeito pretendido sobre reputacao,
reward/slashing e commitments quando aplicavel.

## Qualidade de Engenharia e Delivery

Toda feature DEVE declarar seu bounded context principal, seus contratos
publicos, seus ports/adapters, sua estrategia BDD/TDD, seus testes Jest quando
houver codigo JavaScript/TypeScript, seus riscos de UX e seus budgets de
performance. Planos que adicionem dependencias, modulos, contexts ou adapters
DEVEM explicar o motivo e a alternativa mais simples rejeitada.

Pull requests e entregas DEVEM incluir evidencia de lint, typecheck, testes
Jest relevantes, testes de contrato/integracao quando aplicavel, validacao de
acessibilidade para UI e medicao de performance quando houver budget declarado.
Codigo sem teste em areas de dominio, policy, evidence, reputation ou settlement
SOMENTE PODE ser aceito com justificativa de governanca e tarefa de cobertura
registrada.

Refatoracoes DEVEM preservar comportamento por testes antes de alterar
estrutura. Mudancas que atravessam bounded contexts DEVEM atualizar contratos,
eventos, fixtures e documentacao de linguagem ubiqua no mesmo ciclo.

## Governance

Esta constituicao prevalece sobre templates, planos, tarefas e praticas locais
quando houver conflito. Excecoes DEVEM ser documentadas no Constitution Check
do plano, com justificativa, alternativa rejeitada e aprovacao explicita antes
da implementacao.

Emendas exigem: descricao da mudanca, rationale, impacto sobre principios,
impacto sobre templates dependentes, plano de migracao quando houver artefatos
existentes e bump semantico de versao. MAJOR remove ou redefine principios e
governanca de forma incompativel; MINOR adiciona principios, papeis, gates ou
secoes normativas; PATCH esclarece texto sem alterar obrigacoes.

Todo plano de implementacao DEVE passar por Constitution Check antes de
pesquisa/design e novamente antes de gerar tarefas. Revisoes de codigo,
documentacao e releases DEVEM verificar aderencia aos principios evidence-first,
policy-driven, reputacao verificavel, governanca hibrida, settlement on-chain,
clean code, DDD, BDD/TDD, padrao Jest, UX consistente e performance medida.

**Version**: 1.1.1 | **Ratified**: 2026-05-19 | **Last Amended**: 2026-05-21

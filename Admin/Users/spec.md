# Corban Admin · Usuários

**Status:** Draft
**Aplicação:** Admin
**Seção:** Acesso
**Documento pai:** Corban Admin — Documento de Produto (guarda-chuva da aplicação)
**Documento companheiro:** Corban Admin · Usuários — Matriz de Permissões
**Versão:** 0.1
**Data:** [a definir]
**Autor:** [a definir]

## Histórico de revisões

| Versão | Mudanças                                                                                                                                                                                                                                                    |
| ------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 0.1    | **Cisão do módulo Usuários & Permissões (v0.8).** Usuário passa a ter módulo próprio, separado de Cargo (ver módulo Cargos v0.1). Herda o histórico completo do módulo combinado até a v0.8; a partir daqui, os dois módulos evoluem de forma independente. |

## Como usar este documento

Doc de módulo: entidades, campos, estados, requisitos funcionais (RF-USR) e regras de negócio (RN-USR) próprios. O formato de permissão (`recurso:ação:escopo`) e a hierarquia de escopos são definidos no módulo de **Cargos** — este documento só os referencia, para descrever as permissões adicionais do usuário.

> **Dependência estrutural pendente:** a hierarquia de escopos depende de uma mudança de modelo em Operações — Loja passa a **conter** Operações (cada Loja que ativa uma Operação-modelo do catálogo ganha sua própria Operação-**instância**, com funil/leads/metas exclusivos). Essa mudança revoga RN017/RN018/RN041 da Especificação v1.2. Registrada em documento de anotações companheiro (`corban-anotacoes-operacoes-loja.md`), hoje superado pelo módulo de Lojas — ver nota naquele módulo.

> **Pendência conhecida:** o módulo de Lojas (RN-LOJ-08) introduz exclusão definitiva de usuário (soft-delete), na cascata de exclusão de Loja. Isso ainda não foi incorporado a este documento — RN-USR-05, abaixo, continua afirmando que exclusão de usuário não existe. Reconciliar quando este módulo for revisado.

---

# 1. Objetivo do módulo

Permitir que cada tenant gerencie seus usuários e o acesso de cada um — combinando o Cargo atribuído (módulo de Cargos) com permissões adicionais avulsas — além do controle das sessões ativas. É o módulo que responde "quem existe no sistema e o que cada um pode fazer".

---

# 2. Atores

- **Tenant (Owner)** — papel de sistema, único por tenant, com permissões plenas. Nunca passa pela checagem do catálogo de permissões (bypass total). Criado automaticamente na ativação do tenant, pelo Console da Plataforma; não editável, não excluível. Não é um Cargo.
- **Gestor de usuários** — qualquer usuário cujas permissões efetivas incluam a gestão deste módulo. Tipicamente alguém com um Cargo de escopo `all`.
- **Usuário** — pessoa com acesso ao tenant, sempre com exatamente um Cargo e, opcionalmente, um Time.

---

# 3. Entidades e conceitos

**Usuário.** Pessoa com acesso ao tenant. Campos mínimos: nome, e-mail (identificador de login, único em toda a plataforma), status (ver §7), **Cargo (obrigatório, exatamente um — módulo de Cargos)**, **Time (opcional, no máximo um — módulo de Lojas)** e **permissões adicionais** (zero ou mais). O papel Tenant/Owner é atribuído fora deste módulo, pelo Console da Plataforma, e não é um estado que este módulo edita.

**Permissão adicional.** Permissão concedida diretamente a um usuário específico, fora do seu Cargo — mesmo formato `recurso:ação:escopo` definido no módulo de Cargos. **Somam-se** às permissões herdadas do Cargo — não substituem nem removem nenhuma delas. Servem para o caso pontual em que uma pessoa precisa de algo a mais sem que isso justifique um Cargo novo.

**Sessão.** Sessão ativa de um usuário autenticado. Tem dispositivo/origem aproximada, início e última atividade.

**Time.** Referenciado aqui, detalhado no módulo de Lojas. Importa neste módulo porque é o vínculo do usuário a um Time que dá concretude aos escopos `store` e `operation` — sem Time, uma permissão nesses escopos não alcança nenhum dado.

---

# 4. Modelo de acesso efetivo

As permissões efetivas de um usuário são a **união** de duas fontes:

1. **Cargo (obrigatório).** Todo usuário tem exatamente um Cargo, e herda todas as permissões dele.
2. **Permissões adicionais (opcional).** Permissões avulsas concedidas àquele usuário específico, somadas às do Cargo.

Não há subtração: uma permissão adicional só acrescenta. Para retirar acesso de alguém, muda-se o Cargo ou remove-se a permissão adicional — nunca se "nega" pontualmente uma permissão que o Cargo concede.

**O alcance de cada permissão depende do escopo dela e do Time do usuário:**

- Escopo `all` alcança todo o tenant, independentemente de Time.
- Escopos `store` e `operation` alcançam a Loja / Operação-instância do Time do usuário — se o usuário não tem Time, não alcançam nada.
- Escopo `self` alcança apenas os registros do próprio usuário.

**Tenant/Owner** não passa por esse cálculo: tem bypass total (RN-USR-03).

Regras próprias deste módulo:

**RN-USR-01 — Cargo é obrigatório e único.** Todo usuário tem exatamente um Cargo. Não existe usuário sem Cargo, e não existe usuário com mais de um.

**RN-USR-02 — Time é opcional e único.** Um usuário pertence a no máximo um Time. Usuário sem Time é estado válido: ele mantém as permissões do Cargo, mas as de escopo `store` e `operation` não alcançam nenhum dado até que ele seja alocado. Nessa condição, a autenticação no CRM é permitida (com aviso na tela de que ele ainda não tem acesso a recursos); a autenticação no Admin e no Marketing é bloqueada, com mensagem orientando a contatar o administrador do tenant. _(Regra de login — módulo de Autenticação, RF001; referenciada aqui como consequência deste estado.)_

**RN-USR-03 — Tenant/Owner é protegido.** Único por tenant, criado automaticamente na ativação (Console da Plataforma), com bypass total do catálogo de permissões. Não editável, não excluível, não atribuível por usuários comuns. Não é um Cargo e não aparece na lista de Cargos.

**RN-USR-13 — Permissões adicionais somam, nunca subtraem.** Uma permissão concedida diretamente ao usuário acrescenta-se às do Cargo. Não existe negação pontual de permissão herdada.

---

# 5. Requisitos funcionais

## RF-USR-01 — Gestão de usuários (CRUD)

**Como** gestor de usuários, **eu quero** criar, consultar, editar e inativar usuários **para** manter a equipe do tenant refletida no sistema.

**Comportamento esperado:** o gestor cadastra nome, e-mail, a senha provisória (RF-USR-04) e, na seção de acesso, o **Cargo** (obrigatório), o **Time** (opcional) e eventuais **permissões adicionais**. A listagem permite busca e filtros por Cargo, loja/operação/time e status, e exibe uma coluna com as permissões associadas ao usuário. A edição cobre dados cadastrais e a seção de acesso. A inativação é o caminho padrão de desligamento. O papel Tenant/Owner não é criado nem editado aqui — é exclusivo do Console da Plataforma.

**Regras específicas:**

**RN-USR-04 — Unicidade de e-mail.** O e-mail é o identificador de login e é único em toda a plataforma.

**RN-USR-05 — Inativação preserva histórico e derruba sessões.** Inativar um usuário revoga imediatamente suas sessões e impede novo login, preservando todo o histórico. Exclusão definitiva de usuário não existe na v1 _(mas ver pendência no topo do documento — módulo de Lojas introduz uma exceção)_. Se o usuário tiver leads, atendimentos ou propostas em andamento, a própria tela de inativação, no Admin, exibe a quantidade de itens por categoria (ex.: "12 leads, 3 atendimentos, 1 proposta") — sem detalhar os itens individualmente — e exige a redistribuição antes de concluir. O gestor escolhe entre:

- **Distribuição automática** — o sistema redistribui os itens de cada categoria entre os demais membros do mesmo Time, por rodízio;
- **Seleção manual por categoria** — o gestor escolhe um ou mais destinatários para o bloco de itens de uma categoria, e o sistema distribui entre os escolhidos por rodízio — a escolha é do(s) destinatário(s) do bloco, não item a item.

_(Em atendimentos (Chat Integrado) e propostas (Digitação de Proposta), ambos em modo demonstração na v1 — RN040, RN055 —, não há hoje item persistente que gere necessidade real de redistribuição; a regra vale desde já para quando esses módulos forem promovidos a funcionalidade plena.)_

**Dependência de arquitetura:** esta tela do Admin lê e grava dados do domínio de Leads/Atendimentos/Propostas, que hoje são modelados como território do CRM — foge do padrão já existente (RN021/RF028, onde o Admin apenas sinaliza e a resolução ocorre no CRM). Registrado como item para a revisão da arquitetura do monorepo.

**RN-USR-12 — Reativação restaura o estado anterior, sem recuperar itens redistribuídos.** Ao reativar um usuário, ele volta exatamente com o Cargo, Time e permissões adicionais que tinha no momento da inativação. Itens redistribuídos por conta da inativação (RN-USR-05) não retornam a ele automaticamente — permanecem com quem os assumiu.

## RF-USR-02 — Gestão de sessões

**Como** gestor de usuários, **eu quero** visualizar e revogar sessões ativas **para** responder rapidamente a desligamentos e incidentes.

**Comportamento esperado:** para cada usuário, o gestor vê as sessões ativas (dispositivo/origem aproximada, início, última atividade) e pode revogar uma sessão específica ou todas. A revogação tem efeito imediato. Não há central de autogestão de sessões para o usuário comum; a revogação das próprias sessões acontece indiretamente (troca de senha, logout — módulo de Autenticação).

## RF-USR-03 — Primeiro acesso e senha provisória

**Como** gestor, **eu quero** definir uma senha provisória ao criar o usuário **para** que ele faça o primeiro acesso e defina a própria credencial.

**Comportamento esperado:** ao criar o usuário (RF-USR-01), o gestor define uma senha provisória e a comunica pelo canal de sua escolha. No primeiro login com essa senha, o sistema exige imediatamente a definição de uma nova senha antes de liberar qualquer outra tela. Não há fluxo de convite por e-mail. O login em si (autenticação, SSO, recuperação de senha) é transversal às três aplicações e vive no módulo de Autenticação.

**Regras específicas:**

**RN-USR-08 — Senha provisória de uso restrito.** Enquanto não for trocada, nenhuma outra funcionalidade fica acessível ao usuário. O gestor pode redefinir a senha provisória de um usuário que ainda não completou o primeiro acesso.

---

# 6. Regras de negócio (consolidado)

- **RN-USR-01** — Cargo é obrigatório e único por usuário.
- **RN-USR-02** — Time é opcional e único; usuário sem Time tem login restrito ao CRM.
- **RN-USR-03** — Tenant/Owner é protegido (bypass total, único, criado pelo Console).
- **RN-USR-04** — Unicidade de e-mail.
- **RN-USR-05** — Inativação preserva histórico, derruba sessões e exige redistribuição de trabalho em andamento.
- **RN-USR-08** — Senha provisória de uso restrito.
- **RN-USR-12** — Reativação restaura o estado anterior, sem recuperar itens redistribuídos.
- **RN-USR-13** — Permissões adicionais somam, nunca subtraem.

**Migradas para o módulo de Cargos:** RN-USR-06 → RN-CRG-01; RN-USR-07 → RN-CRG-02.

---

# 7. Estados e transições

**Usuário:**

- **Convidado** — criado, com senha provisória não trocada. Sem acesso a nenhuma funcionalidade além da troca de senha (RN-USR-08). Não exibe o controle de ativar/inativar — apenas o badge "Convidado" — e transita automaticamente para Ativo ao concluir o primeiro login, sem ação do gestor.
- **Ativo** — senha própria definida; acessa conforme as permissões do seu Cargo mais as adicionais.
- **Inativo** — desligado; não autentica, sessões revogadas, histórico preservado (RN-USR-05).

Transições: convidado → ativo (automática, ao concluir a troca de senha do primeiro acesso); ativo → inativo (inativação pelo gestor, via switch — com redistribuição de itens em andamento quando houver, RN-USR-05); inativo → ativo (reativação pelo gestor, via switch — restaura Cargo, Time e permissões adicionais anteriores, sem recuperar os itens redistribuídos, RN-USR-12).

---

# 8. Dependências e o que o módulo expõe

**Depende de:**

- **Cargos** — o Cargo atribuído ao usuário, e o formato de permissão reaproveitado nas adicionais.
- **Autenticação** (transversal) — consome a senha provisória e a regra de primeiro acesso; realiza login, SSO e recuperação.
- **Lojas** — a entidade Time, à qual o usuário pode ser vinculado.
- **Console da Plataforma** — cria o papel Tenant/Owner na ativação do tenant; este módulo não o edita.

**Expõe para as outras apps (CRM, Marketing) e para Lojas:**

- Permissões efetivas de cada usuário (Cargo + adicionais) — base de toda a visibilidade e ação nas outras aplicações.
- Status do usuário (ativo/inativo) — gate de autenticação e de atribuição de trabalho.

---

_Fim do documento. Usuários v0.1 — módulo da aplicação Admin, seção Acesso. Matriz de permissões em documento companheiro._

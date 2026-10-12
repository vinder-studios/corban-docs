# Corban Admin · Usuários

**Status:** Draft
**Aplicação:** Admin
**Seção:** Acesso
**Documento pai:** Corban Admin — Documento de Produto (guarda-chuva da aplicação)
**Documento companheiro:** Corban Admin · Usuários — Matriz de Permissões
**Versão:** 0.7
**Data:** [a definir]
**Autor:** [a definir]

## Histórico de revisões

| Versão | Mudanças |
|---|---|
| 0.7 | **Senha inicial sem troca obrigatória** (decisão do usuário, D-05 em `Architecture/C4/01-contexto.md`). RF-USR-03 reescrito: o gestor define a senha e o usuário acessa de imediato, podendo trocá-la quando quiser; não há troca obrigatória no primeiro login, convite nem recuperação de senha por e-mail. RN-USR-08 reescrita. **O estado Convidado deixa de existir** (§7). A identidade fica no Keycloak (D-07); SSO e recuperação de senha saem das dependências. O Console sai de "Depende de": o Owner nasce na implantação. |
| 0.6 | **Reconcilia o soft-delete com Lojas**: o estado Excluído existe, só em cascata da exclusão da Loja (RN-LOJ-08, RN-LOJ-15), e o e-mail do excluído fica liberado (decisão do usuário). **Só leads são redistribuídas** na inativação (decisão do usuário): a Proposta acompanha a Lead, os Atores permanecem, e "atendimento" não é categoria; fecha o item 3 de CRM · Membros. **Nova RN-USR-17**: a auditoria da gestão de acesso (RN012 da v1.2) não veio na cisão. RF-USR-02 corrigido: o usuário gerencia as próprias sessões pelo menu da conta (Shell Privado, §9), e não há mais as telas "Meu perfil" e "Minhas sessões" aqui. RN-USR-12 prevê Time e Loja que não existem mais. Seletor de Cargo só com Cargos ativos. Corrige RF-USR-04 → RF-USR-03, o aviso estrutural já revogado (Loja contém Operações), os Atores (citam a Loja), os consumidores no §8 e registra as RN-USR-09 a 11 como sem uso. |
| 0.5 | **Acesso à aplicação passa a depender de permissão, não de Loja/Time (RN-USR-16).** RN-USR-02 perde a cláusula de login ("sem Time: só CRM"), que bloqueava usuários legítimos (marketing, backoffice, nível tenant) e contradizia o Shell Privado, onde o acesso à aplicação já é derivado de permissão. Resolve a pendência registrada na v0.4. |
| 0.4 | **Adiciona o vínculo direto do usuário à Loja (RN-USR-15).** Antes, a Loja só chegava ao usuário via Time; agora um usuário pode ser membro de uma Loja sem ter Time — caso típico de quem não é vendedor (marketing, backoffice). Consequência: o escopo `store` passa a resolver pela Loja do usuário, e só `team` continua exigindo Time. RN-USR-02 reescrita. Inclui RN-USR-14 no consolidado (omitida desde a v0.2). Motivado pelo módulo CRM · Membros. |
| 0.3 | Corrige o escopo `operation` para **`team`** nas referências deste módulo — mesma correção do módulo de Cargos v0.2. |
| 0.2 | Adiciona o campo **"Pode receber lead"** (RN-USR-14), na seção de acesso, consumido pelo módulo CRM · Leads (elegibilidade de operador na distribuição interna da Loja). |
| 0.1 | **Cisão do módulo Usuários & Permissões (v0.8).** Usuário passa a ter módulo próprio, separado de Cargo (ver módulo Cargos v0.1). Herda o histórico completo do módulo combinado até a v0.8; a partir daqui, os dois módulos evoluem de forma independente. |

## Como usar este documento

Doc de módulo: entidades, campos, estados, requisitos funcionais (RF-USR) e regras de negócio (RN-USR) próprios. O formato de permissão (`recurso:ação:escopo`) e a hierarquia de escopos são definidos no módulo de **Cargos** — este documento só os referencia, para descrever as permissões adicionais do usuário.



---

# 1. Objetivo do módulo

Permitir que cada tenant gerencie seus usuários e o acesso de cada um — combinando o Cargo atribuído (módulo de Cargos) com permissões adicionais avulsas — além do controle das sessões ativas. É o módulo que responde "quem existe no sistema e o que cada um pode fazer".

---

# 2. Atores

- **Tenant (Owner)** — papel de sistema, único por tenant, com permissões plenas. Nunca passa pela checagem do catálogo de permissões (bypass total). Criado automaticamente na implantação; não editável, não excluível. Não é um Cargo.
- **Gestor de usuários** — qualquer usuário cujas permissões efetivas incluam a gestão deste módulo. Tipicamente alguém com um Cargo de escopo `all`.
- **Usuário** — pessoa com acesso ao tenant, sempre com exatamente um Cargo e, opcionalmente, uma Loja e um Time.

---

# 3. Entidades e conceitos

**Usuário.** Pessoa com acesso ao tenant. Campos mínimos: nome, e-mail (identificador de login, único na instância), status (ver §7), **Cargo (obrigatório, exatamente um — módulo de Cargos)**, **Loja (opcional, no máximo uma — módulo de Lojas; ver RN-USR-15)**, **Time (opcional, no máximo um — módulo de Lojas)**, **permissões adicionais** (zero ou mais) e **"Pode receber lead"** (booleano, ver RN-USR-14). O papel Tenant/Owner é atribuído fora deste módulo, na implantação, e não é um estado que este módulo edita.

**RN-USR-14 — "Pode receber lead" só tem efeito com Time.** Campo exibido na seção de acesso, junto do Time — visível mesmo sem Time definido, mas sem efeito nesse caso: a elegibilidade de um operador para a distribuição de leads (módulo CRM · Leads, RN-LED-04) exige Time Ativo de todo modo. **Nota de implementação:** o backend trata esse campo como duas listas (`canReceiveLeads` / `cantReceiveLeads`), não uma coluna booleana simples — detalhe de representação, sem efeito no comportamento de produto.

**RN-USR-15 — Loja é vínculo direto do usuário, independente do Time.** Um usuário pertence a no máximo uma Loja, mesmo sem ter Time — situação típica de quem não é vendedor (marketing, backoffice e outras áreas, que geralmente não participam de Times). O campo é opcional: usuários de alcance tenant-wide não precisam de Loja. Quando o usuário tem Time, a Loja dele é obrigatoriamente a do Time — os dois nunca divergem: escolher um Time define a Loja; mudar a Loja de quem já tem Time de outra Loja desassocia o Time (com confirmação). *Motivação: o módulo CRM · Membros exige que o Membro pertença à Loja sem depender de Time (RN-MBR-01); antes, a Loja só chegava ao usuário via Time.*

**RN-USR-16 — O acesso a cada aplicação depende de ter ao menos uma permissão nela; Loja e Time só definem o alcance.** O usuário entra em uma aplicação (CRM, Admin ou Marketing) quando suas permissões efetivas (Cargo + adicionais) incluem ao menos uma cujo recurso pertence àquela aplicação — independentemente de ter Loja ou Time. Sem nenhuma permissão na aplicação, a autenticação nela é bloqueada, com mensagem orientando a contatar o administrador do tenant. Loja e Time **não** são critério de acesso: definem apenas **quais dados** as permissões de escopo `store` e `team` alcançam (RN-USR-02, RN-USR-15). Se o usuário entra numa aplicação mas nenhuma de suas permissões ali alcança dado algum (ex.: só permissões `store`/`team`, sem Loja e sem Time), a aplicação abre com um aviso — não bloqueia — de que os dados aparecem quando ele for associado a uma loja ou time. Tenant/Owner tem bypass (RN-USR-03). *Substitui a regra de login que estava em RN-USR-02 ("sem Time: só CRM"), que bloqueava quem atua em marketing ou tem permissões de escopo tenant e contradizia o Shell Privado (§5, §10.4), onde o acesso à aplicação já é derivado de permissão. A regra de login vive em Autenticação (RF001), que precisa adotar este critério. **Dependência:** o catálogo de permissões precisa declarar a que aplicação cada recurso pertence — hoje isso é implícito.*

**RN-USR-17 — Toda gestão de acesso é auditada.** *(A RN012 da Especificação v1.2 não veio na cisão de Usuários & Permissões.)* Criação, edição, inativação, reativação e exclusão (em cascata) de usuário; mudança de Cargo, Loja, Time e da flag "Pode receber lead"; concessão e remoção de permissões adicionais; redefinição da senha provisória; e revogação de sessões registram trilha de auditoria imutável: autor, data/hora e conteúdo da mudança (RNF005).

**Permissão adicional.** Permissão concedida diretamente a um usuário específico, fora do seu Cargo — mesmo formato `recurso:ação:escopo` definido no módulo de Cargos. **Somam-se** às permissões herdadas do Cargo — não substituem nem removem nenhuma delas. Servem para o caso pontual em que uma pessoa precisa de algo a mais sem que isso justifique um Cargo novo.

**Sessão.** Sessão ativa de um usuário autenticado. Tem dispositivo/origem aproximada, início e última atividade.

**Loja e Time.** Referenciados aqui, detalhados no módulo de Lojas. São os vínculos que dão concretude aos escopos `store` e `team`: a **Loja** (vínculo direto, RN-USR-15) sustenta o `store`; o **Time** sustenta o `team`. Sem Loja e sem Time, uma permissão nesses escopos não alcança nenhum dado.

---

# 4. Modelo de acesso efetivo

As permissões efetivas de um usuário são a **união** de duas fontes:

1. **Cargo (obrigatório).** Todo usuário tem exatamente um Cargo, e herda todas as permissões dele.
2. **Permissões adicionais (opcional).** Permissões avulsas concedidas àquele usuário específico, somadas às do Cargo.

Não há subtração: uma permissão adicional só acrescenta. Para retirar acesso de alguém, muda-se o Cargo ou remove-se a permissão adicional — nunca se "nega" pontualmente uma permissão que o Cargo concede.

**O alcance de cada permissão depende do escopo dela e da Loja e do Time do usuário:**
- Escopo `all` alcança todo o tenant, independentemente de Time.
- Escopo `team` alcança só o Time do usuário — sem Time, não alcança nada. Escopo `store` alcança a Loja do usuário (vínculo direto, RN-USR-15), com todos os Times dela — sem Loja, não alcança nada. Quem tem Loja mas não tem Time (ex.: marketing, backoffice) mantém o alcance `store`.
- Escopo `self` alcança apenas os registros do próprio usuário.

**Tenant/Owner** não passa por esse cálculo: tem bypass total (RN-USR-03).

Regras próprias deste módulo:

**RN-USR-01 — Cargo é obrigatório e único.** Todo usuário tem exatamente um Cargo. Não existe usuário sem Cargo, e não existe usuário com mais de um.

**RN-USR-02 — Time é opcional e único.** Um usuário pertence a no máximo um Time. Usuário sem Time é estado válido — comum entre quem não é vendedor. Sem Time, as permissões de escopo `team` não alcançam nenhum dado; as de escopo `store` continuam valendo se o usuário tem Loja (RN-USR-15). Sem Loja e sem Time, as de `store` e `team` não alcançam nenhum dado até que o usuário seja alocado. Isso afeta só o **alcance** dos dados — não o acesso à aplicação (RN-USR-16).

**RN-USR-03 — Tenant/Owner é protegido.** Único por tenant, criado na implantação, com bypass total do catálogo de permissões. Não editável, não excluível, não atribuível por usuários comuns. Não é um Cargo e não aparece na lista de Cargos.

**RN-USR-13 — Permissões adicionais somam, nunca subtraem.** Uma permissão concedida diretamente ao usuário acrescenta-se às do Cargo. Não existe negação pontual de permissão herdada.

---

# 5. Requisitos funcionais

## RF-USR-01 — Gestão de usuários (CRUD)

**Como** gestor de usuários, **eu quero** criar, consultar, editar e inativar usuários **para** manter a equipe do tenant refletida no sistema.

**Comportamento esperado:** o gestor cadastra nome, e-mail, a senha inicial (RF-USR-03) e, na seção de acesso, o **Cargo** (obrigatório, entre os Cargos ativos — Cargos, §6), a **Loja** (opcional), o **Time** (opcional — restrito aos Times da Loja escolhida) e eventuais **permissões adicionais**. A listagem permite busca e filtros por Cargo, loja/time e status, e exibe uma coluna com as permissões associadas ao usuário. A edição cobre dados cadastrais e a seção de acesso. A inativação é o caminho padrão de desligamento. O papel Tenant/Owner não é criado nem editado aqui — é exclusivo da implantação.

**Regras específicas:**

**RN-USR-04 — Unicidade de e-mail.** O e-mail é o identificador de login e é único na instância, entre usuários **não excluídos**: o e-mail de um usuário excluído (soft-delete) fica liberado (RN-USR-05).

**RN-USR-05 — Inativação preserva histórico, derruba sessões e exige redistribuir as leads em andamento.** Inativar um usuário revoga imediatamente suas sessões e impede novo login, preservando todo o histórico. Se o usuário tiver **leads em andamento**, a própria tela de inativação, no Admin, exibe a quantidade (ex.: "12 leads") — sem detalhar as leads individualmente — e exige a redistribuição antes de concluir. O gestor escolhe entre:

- **Distribuição automática** — o sistema redistribui as leads entre os demais membros do mesmo Time, por rodízio;
- **Seleção manual** — o gestor escolhe um ou mais destinatários para o bloco de leads, e o sistema distribui entre os escolhidos por rodízio — a escolha é do(s) destinatário(s) do bloco, não lead a lead.

**Só leads são redistribuídas.** *(Decisão do usuário, na revisão; resolve o item 3 aberto em CRM · Membros.)* A **Proposta** vinculada acompanha a Lead: o Operador dela é o responsável pela Lead (RN-PRP-09), então muda junto. Os **Atores** já atribuídos (Consultor, Averbador, Emissor de Boleto, Digitador) **permanecem**: travam ao serem atribuídos (RN-PRP-09) e registram quem exerceu o papel; quem opera a Proposta é definido por permissão, não por ser Ator. "Atendimento" não é categoria: a Conversa acompanha a Lead (RN-CHT-02). O dialog mostra, como informação, quantas propostas acompanham as leads.

**Exclusão de usuário só existe em cascata da exclusão da Loja** (RN-LOJ-08, RN-LOJ-15). É **soft-delete**: o usuário some das listagens e das seleções, o registro e o histórico permanecem, e não há ação de restaurar nesta versão. Não existe ação de excluir um usuário isoladamente (a Matriz de Permissões não tem `delete`). O **e-mail do usuário excluído fica liberado** para novo cadastro (RN-USR-04). *(Decisão do usuário, na revisão. O soft-delete preserva dados pessoais, então não substitui o atendimento a um titular de dados, que segue reativo — RNF003.)* Os usuários **mantidos** quando a Loja é excluída ficam **sem Loja e sem Time** (RN-LOJ-09) até serem realocados.

**Dependência de arquitetura:** esta tela do Admin lê e grava dados do domínio de Leads, que hoje são modelados como território do CRM — foge do padrão já existente (RN021/RF028, onde o Admin apenas sinaliza e a resolução ocorre no CRM). Registrado como item para a revisão da arquitetura do monorepo (`corban-anotacoes-operacoes-loja.md`, §6).

**RN-USR-12 — Reativação restaura o estado anterior, sem recuperar as leads redistribuídas.** Ao reativar um usuário, ele volta exatamente com o Cargo, Time e permissões adicionais que tinha no momento da inativação. Leads redistribuídas por conta da inativação (RN-USR-05) não retornam a ele automaticamente — permanecem com quem as assumiu. **Se o Time ou a Loja anteriores foram excluídos ou estão inativos**, o usuário volta **sem eles** (RN-LOJ-09, RN-LOJ-15) e o gestor o realoca; sem Loja e sem Time, as permissões `store` e `team` não alcançam dados até lá (RN-USR-16).

## RF-USR-02 — Gestão de sessões

**Como** gestor de usuários, **eu quero** visualizar e revogar sessões ativas **para** responder rapidamente a desligamentos e incidentes.

**Comportamento esperado:** para cada usuário, o gestor vê as sessões ativas (dispositivo/origem aproximada, início, última atividade) e pode revogar uma sessão específica ou todas. A revogação tem efeito imediato. O próprio usuário gerencia as suas sessões pelo menu da conta, em todas as aplicações e sem permissão (Shell Privado, §9); este requisito trata da gestão das sessões **de outros** usuários.

## RF-USR-03 — Senha inicial definida pelo gestor

**Como** gestor, **eu quero** definir a senha do usuário ao criá-lo **para** que ele acesse o sistema sem depender de e-mail ou convite.

**Comportamento esperado:** ao criar o usuário (RF-USR-01), o gestor define a senha e a comunica pelo canal de sua escolha. O usuário entra com ela e acessa de imediato, conforme as permissões do Cargo e as adicionais. **Não há troca obrigatória no primeiro login.** O próprio usuário pode trocar a senha quando quiser, pelo menu da conta (Shell Privado). Não há convite por e-mail nem recuperação de senha nesta versão (Especificação Geral, §3.2 e §11). As credenciais vivem no Keycloak (Especificação Geral, §4.2).

**Regras específicas:**

**RN-USR-08 — Senha inicial sem troca obrigatória.** A senha definida pelo gestor é uma senha comum, sem restrição de uso: o usuário não é obrigado a trocá-la. O gestor pode redefinir a senha de um usuário a qualquer momento, que é o caminho para quem esqueceu a senha enquanto não houver recuperação.

---

# 6. Regras de negócio (consolidado)

- **RN-USR-01** — Cargo é obrigatório e único por usuário.
- **RN-USR-02** — Time é opcional e único; sem Time, `team` não alcança nada (o acesso à aplicação não depende de Time — RN-USR-16).
- **RN-USR-03** — Tenant/Owner é protegido (bypass total, único, criado na implantação).
- **RN-USR-04** — Unicidade de e-mail.
- **RN-USR-05** — Inativação preserva histórico, derruba sessões e exige redistribuir as leads em andamento (a Proposta acompanha; os Atores permanecem). Exclusão de usuário só em cascata da exclusão da Loja (soft-delete); o e-mail fica liberado.
- **RN-USR-08** — Senha inicial sem troca obrigatória.
- **RN-USR-12** — Reativação restaura o estado anterior, sem recuperar as leads redistribuídas; sem Time ou Loja que não existem mais, volta sem eles.
- **RN-USR-13** — Permissões adicionais somam, nunca subtraem.
- **RN-USR-14** — "Pode receber lead" só tem efeito com Time.
- **RN-USR-15** — Loja é vínculo direto do usuário (no máximo uma), independente do Time; se há Time, a Loja é a dele.
- **RN-USR-16** — O acesso a cada aplicação depende de ter ao menos uma permissão nela; Loja e Time só definem o alcance.
- **RN-USR-17** — Toda gestão de acesso é auditada.

**Migradas para o módulo de Cargos:** RN-USR-06 → RN-CRG-01; RN-USR-07 → RN-CRG-02.

*RN-USR-09, 10 e 11: numeração sem uso, de modelos anteriores (a RN-USR-10 associava Grupo a Time, o que não existe mais).*

---

# 7. Estados e transições

**Usuário:**
- **Ativo** — estado inicial, desde a criação; acessa conforme as permissões do seu Cargo mais as adicionais (RN-USR-08).
- **Inativo** — desligado; não autentica, sessões revogadas, histórico preservado (RN-USR-05).
- **Excluído** — soft-delete, alcançado só pela exclusão da Loja (RN-LOJ-08, RN-USR-05): some das listagens e das seleções; o registro e o histórico permanecem; o e-mail fica liberado; sem restauração nesta versão.

Transições: ativo → inativo (inativação pelo gestor, via switch — com redistribuição das leads em andamento quando houver, RN-USR-05); inativo → ativo (reativação pelo gestor, via switch — restaura Cargo, Time e permissões adicionais anteriores, sem recuperar as leads redistribuídas, RN-USR-12); ativo ou inativo → excluído (só em cascata da exclusão da Loja, sem ação própria).

---

# 8. Dependências e o que o módulo expõe

**Depende de:**
- **Cargos** — o Cargo atribuído ao usuário, e o formato de permissão reaproveitado nas adicionais.
- **Autenticação** (transversal, Keycloak) — guarda a credencial definida aqui (RF-USR-03), realiza o login e adota o critério de acesso por aplicação (RN-USR-16).
- **Lojas** — a entidade Loja e a entidade Time, às quais o usuário pode ser vinculado.
- **Implantação** — cria o papel Tenant/Owner; este módulo não o edita (Especificação Geral §4.3, §10).

**Expõe para as outras apps (CRM, Marketing) e para Lojas:**
- Permissões efetivas de cada usuário (Cargo + adicionais) — base de toda a visibilidade e ação nas outras aplicações.
- Status do usuário (ativo/inativo) — gate de autenticação e de atribuição de trabalho.
- **CRM · Membros** — os usuários da Loja, com o vínculo direto (RN-USR-15) e o Cargo (RN-MBR-01).
- **CRM · Leads** — a flag "Pode receber lead" e o Time, para a elegibilidade na distribuição interna (RN-USR-14, RN-LED-04).

---

*Fim do documento. Usuários v0.6 — módulo da aplicação Admin, seção Acesso. Matriz de permissões em documento companheiro.*

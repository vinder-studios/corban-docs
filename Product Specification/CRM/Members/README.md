# Corban CRM · Membros

**Status:** Draft
**Aplicação:** CRM
**Documento pai:** Corban CRM — Documento de Produto (guarda-chuva da aplicação, a redigir)
**Versão:** 0.5
**Data:** [a definir]

## Histórico de revisões

| Versão | Mudanças |
|---|---|
| 0.5 | Acompanha Usuários v0.7: o estado Convidado deixa de existir (senha inicial sem troca obrigatória). |
| 0.4 | Fecha o item aberto 3 (categorias de redistribuição): só Leads são redistribuídas, decisão tomada em Usuários v0.6. |
| 0.3 | **Fecha o item aberto §9.1: decisão (a) — vínculo direto Usuário→Loja** (Usuários v0.4, RN-USR-15). O Membro pertence à Loja independentemente de ter Time; Time continua opcional e reservado a quem é da área comercial (vendedores) — marketing, backoffice e outras áreas geralmente não participam de Times. RN-MBR-01 reescrita. Itens §9.2 e §9.3 seguem abertos. |
| 0.1 | Versão inicial. |
| 0.2 | Corrige "Time(s)" no Objetivo (é no máximo um Time — RN-USR-02). Explicita que a flag **"Pode receber lead"** (RN-USR-14) faz parte da seção de acesso do Membro, herdada de Usuários. Registra três itens abertos encontrados ao preparar a Fase 2: vínculo Membro↔Loja sem Time, escalonamento de privilégio e categorias de redistribuição. |

## Como usar este documento

Doc de módulo: entidade, campos, estados, requisitos funcionais (RF-MBR) e regras de negócio (RN-MBR) próprios.

> **Este módulo é a versão com escopo `store` do módulo Usuários (Admin) — mesma entidade, mesmas regras de ciclo de vida, com três restrições adicionais.** Não redefine Usuário: aqui, um Cargo/Loja parceira gerencia sua própria equipe, sem acesso ao Admin. Onde este documento for omisso, o comportamento é o do módulo Usuários (Admin) — CRUD, senha provisória, sessões, estados (Convidado/Ativo/Inativo), redistribuição na inativação.

---

# 1. Objetivo do módulo

Permitir que uma Loja parceira gerencie os próprios usuários — cadastro, Cargo, Time (no máximo um, dentro da Loja), permissões adicionais — sem depender do tenant operador nem de acesso ao Admin.

---

# 2. Atores

- **Gestor de Loja** — usuário com permissão de gestão deste módulo, escopo `store`.
- **Membro** — o usuário gerenciado; nasce sempre vinculado à Loja de quem o cadastrou.

---

# 3. Entidades e conceitos

Reaproveita a entidade **Usuário** do módulo Usuários (Admin), sem campos novos. Três restrições próprias deste módulo:

**RN-MBR-01 — Membro nasce vinculado à Loja de quem o cadastra — vínculo direto, independente do Time.** Diferente do Admin (onde o Time é escolhido livremente entre todos do tenant), aqui a Loja já está implícita no contexto: quem cadastra é um Gestor de Loja, e o Membro nasce com o campo Loja do Usuário (RN-USR-15, módulo de Usuários) preenchido com a Loja dele — sem pergunta no formulário. O Membro **não precisa ter Time**: Time é opcional (RN-USR-02, no máximo um) e faz sentido sobretudo para vendedores; quem atua em marketing, backoffice ou outras áreas normalmente fica só na Loja. O Gestor escolhe, no cadastro ou depois, a qual Time **daquela Loja** o Membro pertence — ou deixa sem Time. Consequência: a Lista de Membros mostra todos os usuários vinculados à Loja, com ou sem Time.

**RN-MBR-02 — Só Cargos sem nenhuma linha de escopo `all` são listados.** Ao atribuir Cargo a um Membro, a lista de Cargos disponíveis exclui qualquer Cargo que tenha ao menos uma linha de permissão em escopo `all` — mesmo que o Cargo também tenha outras linhas em `store` ou mais restritas. Um Cargo "misto" (algumas linhas `all`, outras não) **não aparece** aqui; só é atribuível pelo Admin.

**RN-MBR-03 — Permissões adicionais concedidas aqui são limitadas a `store` e abaixo.** Ao conceder uma permissão adicional a um Membro (RN-USR-13, módulo de Usuários — permissões adicionais somam ao Cargo), o seletor de escopo, neste módulo, só oferece `store`, `team` e `self` — nunca `all`. A restrição é só na **interface deste módulo**; a entidade permissão adicional em si não muda (é a mesma do módulo de Usuários).

---

# 4. Requisitos funcionais

## RF-MBR-01 — Gestão de membros da Loja

**Como** gestor de Loja, **eu quero** cadastrar, consultar, editar e inativar os usuários da minha equipe **para** manter minha operação estruturada sem depender do Admin.

**Comportamento esperado:** idêntico a RF-USR-01 (módulo Usuários, Admin), restrito à própria Loja — listagem, cadastro, edição, alteração de status, redistribuição na inativação (RN-USR-05) seguem sem alteração. A seção de acesso do Membro inclui Cargo, Time e a flag **"Pode receber lead"** (RN-USR-14), exatamente como em Usuários. O que muda é só o escopo dos dados (só a própria Loja) e as três restrições de §3.

---

# 5. Regras de negócio (consolidado)

- **RN-MBR-01** — Membro nasce com vínculo direto à Loja de quem o cadastra (RN-USR-15), independente do Time; Time (no máximo um) continua opcional, como em RN-USR-02.
- **RN-MBR-02** — Só Cargos sem nenhuma linha `all` são listados para atribuição.
- **RN-MBR-03** — Permissões adicionais concedidas aqui limitadas a `store` e abaixo.

---

# 6. Estados e transições

Idênticos ao módulo Usuários (Admin), §7 — Ativo / Inativo (e Excluído, só em cascata da Loja), sem alteração.

---

# 7. Limites do módulo

**Não** inclui: gestão de Cargos (Admin, RN-MBR-02 só filtra a lista, não cria/edita Cargo), gestão de Times em si (módulo de Lojas — Times como entidade continuam do lado Admin; este módulo só atribui o Membro a um Time já existente), nem qualquer ação de escopo `all` (por definição, fora do alcance de um Gestor de Loja).

---

# 8. Dependências e o que o módulo expõe

**Depende de:**
- **Usuários** (Admin) — entidade, campos (inclusive o vínculo direto à Loja, RN-USR-15), regras de ciclo de vida, todas reaproveitadas sem redefinição.
- **Cargos** (Admin) — lista filtrada (RN-MBR-02).
- **Lojas** (Admin) — a Loja (vínculo direto do Membro) e a lista de Times dela, para o campo Time do Membro.

**Expõe para:**
- **Leads** (CRM) — os Membros elegíveis para a distribuição interna de operadores (RN-LED-04, módulo de Leads) são os mesmos cadastrados aqui.

---

# 9. Itens abertos

1. ~~**Onde vive o vínculo Membro↔Loja quando não há Time.**~~ **Resolvido na v0.3:** vínculo direto Usuário→Loja (RN-USR-15, Usuários v0.4). Numeração mantida para não invalidar as referências a §9.2 e §9.3 nos artefatos de tela.
2. **Escalonamento de privilégio.** RN-MBR-02 e RN-MBR-03 limitam Cargos e permissões adicionais por **escopo** (nada de `all`), mas não limitam pelo **conjunto** que o próprio Gestor de Loja possui. Como está, ele poderia atribuir um Cargo, ou conceder uma permissão adicional (inclusive `members:manage_permissions`), que ele mesmo não tem. Decidir se a concessão fica restrita ao que o Gestor já possui.
3. **Categorias de redistribuição — resolvido (Usuários v0.6).** Só **Leads** são redistribuídas na inativação; a Proposta vinculada acompanha a Lead e os Atores já atribuídos permanecem (travados, RN-PRP-09); "atendimento" não é categoria (a Conversa acompanha a Lead, RN-CHT-02).

---

*Fim do documento. Membros v0.4 — módulo da aplicação CRM.*

# Corban Admin · Cargos

**Status:** Draft
**Aplicação:** Admin
**Seção:** Acesso
**Documento pai:** Corban Admin — Documento de Produto (guarda-chuva da aplicação)
**Documento companheiro:** Corban Admin · Cargos — Matriz de Permissões
**Versão:** 0.4
**Data:** [a definir]
**Autor:** [a definir]

## Histórico de revisões

| Versão | Mudanças |
|---|---|
| 0.4 | **O estado do Cargo é remodelado**: Status passa a ser campo (Ativo/Inativo), e "em uso" deixa de ser estado e vira informação derivada (a contagem de usuários). O módulo chamava o status de "derivado" mas listava como estado uma ação explícita (inativar) e usava "ativo" sem defini-lo. **Nova RN-CRG-03**: toda mudança em Cargos é auditada (a RN012 da v1.2 não veio na cisão). **Nova RN-CRG-04**: `roles:manage_permissions:all` equivale a conceder qualquer acesso — decisão consciente do usuário. **Nova RN-CRG-05**: nome único. Corrige o exemplo de `leads:view:store` (Loja do usuário, desde a v0.3) e a remissão do §3.1, que dizia haver uma matriz completa no documento companheiro, quando ele cobre só `roles`. |
| 0.3 | Escopo `store` passa a significar **a Loja do usuário (vínculo direto)**, não mais "a Loja do Time do usuário" — Usuários v0.4, RN-USR-15. Quem tem Loja mas não tem Time mantém o alcance `store`. `team` continua exigindo Time. |
| 0.2 | Corrige o escopo `operation` para **`team`** — renomeação já decidida anteriormente (módulo de Lojas) mas nunca propagada a este documento, que foi cindido a partir de um rascunho anterior a essa mudança. |
| 0.1 | **Cisão do módulo Usuários & Permissões (v0.8).** Cargo passa a ter módulo próprio, separado de Usuário — mesmo princípio de domínio único já aplicado aos demais módulos do produto (ex.: Segmentos × Funis de Vendas). Herda o histórico completo do módulo combinado até a v0.8; a partir daqui, os dois módulos evoluem de forma independente. RN-USR-06 e RN-USR-07 são renumeradas para RN-CRG-01 e RN-CRG-02. |

## Como usar este documento

Doc de módulo: entidade, campos, estados, requisitos funcionais (RF-CRG) e regras de negócio (RN-CRG) próprios. É também aqui que o **formato da permissão** (`recurso:ação:escopo`) é definido — Cargo é, por natureza, "um conjunto de linhas de permissão", então a definição do que é uma permissão pertence a este módulo. O módulo de Usuários referencia este formato para descrever permissões adicionais, sem redefini-lo.

---

# 1. Objetivo do módulo

Manter os Cargos — conjuntos nomeados de permissões, atribuíveis a usuários — que o tenant configura livremente para modelar seu próprio modelo de acesso.

---

# 2. Atores

- **Gestor de acesso** — usuário cujas permissões efetivas incluem a gestão deste módulo. Tipicamente alguém com um Cargo de escopo `all`.
- **Tenant (Owner)** — não é um Cargo, não aparece na lista deste módulo. Detalhado no módulo de Usuários (RN-USR-03).

---

# 3. Entidades e conceitos

**Cargo.** Conjunto nomeado de permissões, atribuído diretamente a um usuário (módulo de Usuários). Campos:

| Campo | Obrigatório | Notas |
|---|---|---|
| Nome | Sim | — |
| Descrição | Não | Texto curto explicando o que o cargo representa. |
| Linhas de permissão | Não | Zero ou mais — ver §4. |
| Status | Sim | **Ativo / Inativo** (ver §6). Padrão: Ativo. Estar **em uso** (ao menos um usuário) é informação derivada, a contagem de usuários, e não um status. |

Criável livremente pelo tenant. O mesmo Cargo pode ser atribuído a muitos usuários. **Admin** é um Cargo como outro qualquer — um que reúne permissões de escopo `all` —, não um papel especial do sistema.

## 3.1. Permissão — formato e escopo

**Permissão.** Autorização granular composta por três partes: **recurso**, **ação** e **escopo** — `recurso:ação:escopo`.

**Escopo — hierarquia de contenção, do mais amplo ao mais restrito:**

| Escopo | Abrangência |
|---|---|
| `all` | Todo o tenant — todas as Lojas, todos os Times. |
| `store` | A Loja do usuário (vínculo direto, mesmo sem Time) — todos os Times dela. |
| `team` | Só o Time do usuário — exige que ele tenha Time. |
| `self` | Apenas os registros do próprio usuário. |

Cada escopo contém o(s) seguinte(s): `all ⊇ store ⊇ team ⊇ self`.

Exemplos:
- `leads:view:self` — vê somente as leads associadas a si mesmo.
- `leads:view:team` — vê as leads do seu Time.
- `leads:view:store` — vê as leads da sua Loja (vínculo direto), de todos os Times dela.
- `leads:view:all` — vê todas as leads do tenant.

A Matriz de Permissões companheira cobre **só o recurso `roles`**; as linhas oferecíveis dos demais recursos estão na matriz do módulo que os define.

---

# 4. Requisitos funcionais

## RF-CRG-01 — Gestão de Cargos

**Como** gestor de acesso, **eu quero** criar e manter Cargos **para** modelar a hierarquia da minha empresa e atribuir acesso de forma consistente.

**Comportamento esperado:** o gestor cria um Cargo informando nome e descrição, e adicionando linhas de permissão — recurso, ação e escopo. Pode duplicar um Cargo existente como ponto de partida. A edição de um Cargo afeta imediatamente todos os usuários que o possuem.

**Regras específicas:**

**RN-CRG-01 — Cargo em uso não pode ser excluído.** *(Renumerada de RN-USR-06.)* Um Cargo atribuído a algum usuário só pode ser excluído após a migração desses usuários para outro Cargo — respeitando RN-USR-01 (módulo de Usuários: nenhum usuário pode ficar sem Cargo). Alternativamente pode ser inativado, bloqueando novas atribuições.

**RN-CRG-02 — Efeito imediato.** *(Renumerada de RN-USR-07.)* Alterações de linhas de um Cargo passam a valer imediatamente para as próximas ações dos usuários afetados, sem exigir novo login.

**RN-CRG-03 — Toda mudança em Cargos é auditada.** *(A RN012 da Especificação v1.2 — "toda gestão de acesso é auditada" — não veio na cisão de Usuários & Permissões.)* Criação, edição, inativação, reativação e exclusão de um Cargo, e **toda alteração das suas linhas de permissão**, registram trilha de auditoria imutável: autor, data/hora e conteúdo da mudança (antes e depois) — RNF005.

**RN-CRG-04 — Quem pode gerenciar as permissões de um Cargo pode conceder qualquer acesso, inclusive a si mesmo.** `roles:manage_permissions:all` é a permissão mais sensível do produto: ao editar as linhas de um Cargo, a pessoa decide o que todo usuário com aquele Cargo pode fazer, e nada impede que ela própria o tenha. **É decisão consciente, não lacuna.** Limitar a concessão ao conjunto que a pessoa possui quebraria o gestor de acesso que define Cargos sem operar (um perfil legítimo), e proibir a edição do próprio Cargo seria contornável pela atribuição em Usuários. As salvaguardas são a restrição ao escopo `all` e a auditoria (RN-CRG-03). O escalonamento por **conjunto** em escopos menores (`store`) é tratado em Membros (CRM). *(Decisão do usuário.)*

**RN-CRG-05 — Nome único no tenant.** Não existem dois Cargos com o mesmo nome, ignorando maiúsculas/minúsculas e espaços nas pontas: Usuários escolhe o Cargo pelo nome. *(Escolha desta revisão, no padrão de Status de Proposta, RN-STP-09.)*

---

# 5. Regras de negócio (consolidado)

- **RN-CRG-01** — Cargo em uso não pode ser excluído.
- **RN-CRG-02** — Efeito imediato de mudanças no Cargo.
- **RN-CRG-03** — Toda mudança em Cargos e em suas linhas de permissão é auditada.
- **RN-CRG-04** — `roles:manage_permissions:all` equivale a conceder qualquer acesso, inclusive a si mesmo (decisão consciente).
- **RN-CRG-05** — Nome único no tenant.

---

# 6. Estados e transições

**Cargo** — duas dimensões independentes:

- **Status** (campo): **Ativo** — pode ser atribuído a usuários; **Inativo** — não recebe novas atribuições, mantém as existentes até migração, e continua editável (nome, descrição e permissões).
- **Vínculo** (derivado): **em uso** (ao menos um usuário) ou **sem vínculo**. Só decide a exclusão.

| Status | Vínculo | Editar | Inativar / Reativar | Excluir |
|---|---|---|---|---|
| Ativo | Sem vínculo | Sim | Inativar | **Sim** |
| Ativo | Em uso | Sim | Inativar | **Bloqueada** (RN-CRG-01) |
| Inativo | Sem vínculo | Sim | Reativar | **Sim** |
| Inativo | Em uso | Sim | Reativar | **Bloqueada** (RN-CRG-01) |

Transições de Status: ativo → inativo (livre); inativo → ativo (livre). Exclusão: só sem vínculo. Um Cargo passa de sem vínculo a em uso quando é atribuído a um usuário (módulo de Usuários), e volta a sem vínculo ao perder o último.

---

# 7. Limites do módulo

Este módulo trata apenas do cadastro do Cargo e de suas linhas de permissão. **Não** inclui: a atribuição de Cargo a um usuário, permissões adicionais avulsas, nem o Time do usuário (todos no módulo de Usuários).

---

# 8. Dependências e o que o módulo expõe

**Depende de:** nada — é cadastro-base.

**Expõe para:**
- **Usuários** — a lista de Cargos ativos, para atribuição; o formato de permissão (`recurso:ação:escopo`), reaproveitado nas permissões adicionais.
- **Todas as outras aplicações (CRM, Marketing)** — as permissões efetivas herdadas via Cargo, base de toda a visibilidade e ação.

---

*Fim do documento. Cargos v0.4 — módulo da aplicação Admin, seção Acesso.*

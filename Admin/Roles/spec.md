# Corban Admin · Cargos

**Status:** Draft
**Aplicação:** Admin
**Seção:** Acesso
**Documento pai:** Corban Admin — Documento de Produto (guarda-chuva da aplicação)
**Documento companheiro:** Corban Admin · Cargos — Matriz de Permissões
**Versão:** 0.1
**Data:** [a definir]
**Autor:** [a definir]

## Histórico de revisões

| Versão | Mudanças                                                                                                                                                                                                                                                                                                                                                                                                             |
| ------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 0.1    | **Cisão do módulo Usuários & Permissões (v0.8).** Cargo passa a ter módulo próprio, separado de Usuário — mesmo princípio de domínio único já aplicado aos demais módulos do produto (ex.: Segmentos × Funis de Vendas). Herda o histórico completo do módulo combinado até a v0.8; a partir daqui, os dois módulos evoluem de forma independente. RN-USR-06 e RN-USR-07 são renumeradas para RN-CRG-01 e RN-CRG-02. |

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

| Campo               | Obrigatório | Notas                                                                           |
| ------------------- | ----------- | ------------------------------------------------------------------------------- |
| Nome                | Sim         | —                                                                               |
| Descrição           | Não         | Texto curto explicando o que o cargo representa.                                |
| Linhas de permissão | Não         | Zero ou mais — ver §4.                                                          |
| Status              | Sim         | Em uso / Sem vínculo / Inativado (ver §6 — estado derivado, não campo próprio). |

Criável livremente pelo tenant. O mesmo Cargo pode ser atribuído a muitos usuários. **Admin** é um Cargo como outro qualquer — um que reúne permissões de escopo `all` —, não um papel especial do sistema.

## 3.1. Permissão — formato e escopo

**Permissão.** Autorização granular composta por três partes: **recurso**, **ação** e **escopo** — `recurso:ação:escopo`.

**Escopo — hierarquia de contenção, do mais amplo ao mais restrito:**

| Escopo      | Abrangência                                                           |
| ----------- | --------------------------------------------------------------------- |
| `all`       | Todo o tenant — todas as Lojas, todas as Operações-instância.         |
| `store`     | A Loja do Time do usuário — todas as Operações-instância ativas nela. |
| `operation` | A Operação-instância do Time do usuário.                              |
| `self`      | Apenas os registros do próprio usuário.                               |

Cada escopo contém o(s) seguinte(s): `all ⊇ store ⊇ operation ⊇ self`. Essa contenção depende de Loja conter Operações-instância — dependência estrutural pendente, registrada no módulo de Lojas.

Exemplos:

- `leads:view:self` — vê somente as leads associadas a si mesmo.
- `leads:view:operation` — vê as leads da Operação-instância do seu Time.
- `leads:view:store` — vê as leads de todas as Operações-instância da Loja do seu Time.
- `leads:view:all` — vê todas as leads do tenant.

A matriz completa de linhas oferecíveis por recurso/ação está no documento companheiro.

---

# 4. Requisitos funcionais

## RF-CRG-01 — Gestão de Cargos

**Como** gestor de acesso, **eu quero** criar e manter Cargos **para** modelar a hierarquia da minha empresa e atribuir acesso de forma consistente.

**Comportamento esperado:** o gestor cria um Cargo informando nome e descrição, e adicionando linhas de permissão — recurso, ação e escopo. Pode duplicar um Cargo existente como ponto de partida. A edição de um Cargo afeta imediatamente todos os usuários que o possuem.

**Regras específicas:**

**RN-CRG-01 — Cargo em uso não pode ser excluído.** _(Renumerada de RN-USR-06.)_ Um Cargo atribuído a algum usuário só pode ser excluído após a migração desses usuários para outro Cargo — respeitando RN-USR-01 (módulo de Usuários: nenhum usuário pode ficar sem Cargo). Alternativamente pode ser inativado, bloqueando novas atribuições.

**RN-CRG-02 — Efeito imediato.** _(Renumerada de RN-USR-07.)_ Alterações de linhas de um Cargo passam a valer imediatamente para as próximas ações dos usuários afetados, sem exigir novo login.

---

# 5. Regras de negócio (consolidado)

- **RN-CRG-01** — Cargo em uso não pode ser excluído.
- **RN-CRG-02** — Efeito imediato de mudanças no Cargo.

---

# 6. Estados e transições

**Cargo:**

- **Em uso** — atribuído a ao menos um usuário; não excluível (RN-CRG-01).
- **Sem vínculo** — sem usuários; excluível.
- **Inativado** — não recebe novas atribuições; mantém as existentes até migração. Continua editável (nome, descrição e permissões).

Transições: sem vínculo → em uso (ao ser atribuído a um usuário — ação do módulo de Usuários); em uso → sem vínculo (ao perder o último usuário); qualquer → inativado (livre); inativado → ativo (livre); sem vínculo → excluído (livre); em uso → excluído (bloqueada, RN-CRG-01).

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

_Fim do documento. Cargos v0.1 — módulo da aplicação Admin, seção Acesso._

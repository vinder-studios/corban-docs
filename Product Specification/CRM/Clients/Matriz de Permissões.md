# Corban CRM · Clientes — Matriz de Permissões

**Status:** Draft
**Aplicação:** CRM
**Documento pai:** Corban CRM · Clientes (módulo v0.5)
**Versão:** 0.1
**Data:** [a definir]

## Como usar este documento

Cobre dois recursos: `clients` (ficha) e `clients_documents` (sub-recurso, mesmo padrão já usado em `proposals_documents`). Formato: `recurso:ação:escopo`.

---

## 1. Matriz

### Recurso `clients`

| Ação | Escopos oferecidos |
|---|---|
| `view` | `self`, `team`, `store`, `all` |
| `edit` | `self`, `team`, `store`, `all` |
| `import` | `store` |
| `export` | `store`, `all` |

### Sub-recurso `clients_documents`

| Ação | Escopos oferecidos |
|---|---|
| `view` | `self`, `team`, `store`, `all` |
| `manager` | `self`, `team`, `store`, `all` |

---

## 2. Notas sobre as ações

**`self` para Cliente significa "Clientes alcançados por Leads atribuídas ao usuário"** — mesmo critério já usado em Leads, Chat, Simulações e Propostas. Cliente não tem "responsável" próprio; o alcance vem sempre de Lead ou de associação direta à Loja (RN-CLI-04).

**Sem `create`.** Cliente não nasce de um formulário "Novo Cliente" — nasce automaticamente de uma Lead (RN-CLI-01) ou de Importação (RF-CLI-02, permissão própria `import`). Não há ação de criação manual avulsa neste módulo.

**`import` e `export` só em `store`/`all`.** Mexer com a base inteira de uma vez — trazer ou tirar dados — é ação de maior impacto que editar um registro; `export` em particular tem implicação de LGPD (RNF003 da v1.2, herdada), por isso nem oferece `self`/`team`.

**`clients_documents` segue exatamente o padrão já fixado em `proposals_documents`** — `view` separado de `manager`, ambos com os quatro escopos.

**As abas Simulações, Leads associadas, Conversas e Propostas não têm permissão própria neste recurso** — são gatejadas pelas permissões dos módulos de origem (`simulations:view`, `leads:view`, `chats:view`, `proposals:view`, respectivamente), não redefinidas aqui.

---

## 3. Owner / Tenant

Não aparece nesta matriz — bypass total do catálogo.

---

*Fim do documento. Matriz de Permissões — Clientes v0.1.*

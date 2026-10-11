# Corban CRM · Leads — Matriz de Permissões

**Status:** Draft
**Aplicação:** CRM
**Documento pai:** Corban CRM · Leads (módulo v0.5)
**Versão:** 0.1
**Data:** [a definir]

## Como usar este documento

Cobre o recurso `leads`. Formato da chave: `recurso:ação:escopo`, mesmo padrão do Admin e já usado em Chat (CRM).

---

## 1. Matriz

| Ação              | Escopos oferecidos             |
| ----------------- | ------------------------------ |
| `view`            | `self`, `team`, `store`, `all` |
| `create`          | `store`                        |
| `reassign`        | `team`, `store`, `all`         |
| `execute_actions` | `self`, `team`, `store`, `all` |

---

## 2. Notas sobre as ações

**`view` segue o mesmo critério já fixado em Chat** — `self` = só as Leads atribuídas ao próprio usuário (mesma definição de "registros do próprio usuário" usada em todo o produto). `team`/`store`/`all` ampliam progressivamente.

**`create` só existe em `store`, sem outros níveis.** Cadastrar manualmente uma lead é sempre "para a minha Loja" — não há um conceito de "criar lead para outra Loja" dentro do CRM (isso seria o caminho tenant-wide via Huggy/landing page, módulo já cobre). Um único nível evita oferecer escopos sem uso real.

**`reassign` não oferece `self`.** Reatribuir o operador de uma Lead é, por natureza, uma ação sobre o trabalho de outra pessoa (ou do pool) — não faz sentido "reatribuir a mim mesmo" como escopo restrito; quem tem `self` de `view` já vê e trabalha as próprias leads sem precisar desta permissão.

**`execute_actions` espelha `view`.** Quem pode ver uma Lead num escopo também pode, com esta permissão no mesmo escopo, executar as Ações disponíveis para ela (Mover lead, Desqualificar, Finalizar, Formalizar, Reabrir — módulo de Funis de Vendas, §3.3). São permissões independentes: um Cargo pode ter `view` sem `execute_actions` (consulta, sem poder agir).

---

## 3. Owner / Tenant

Não aparece nesta matriz — bypass total do catálogo.

---

_Fim do documento. Matriz de Permissões — Leads v0.1._

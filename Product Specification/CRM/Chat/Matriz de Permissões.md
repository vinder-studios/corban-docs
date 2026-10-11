# Corban CRM · Chat — Matriz de Permissões

**Status:** Draft
**Aplicação:** CRM
**Documento pai:** Corban CRM · Chat (módulo v0.6)
**Versão:** 0.2
**Data:** [a definir]

## Histórico de revisões

| Versão | Mudanças |
|---|---|
| 0.1 | Versão inicial — modelava acesso do operador responsável como bypass do catálogo, fora do escopo `self`. |
| 0.2 | Correção: `self` passa a ser escopo normal, representando "Conversas de Leads atribuídas a mim" — mesma definição já usada em todo o produto. Elimina o caminho de autorização especial fora do catálogo; o backend resolve tudo com a mesma query de escopo padrão (`self → team → store → all`). |

## Como usar este documento

Cobre o recurso `chats`. Formato da chave: `recurso:ação:escopo`, mesmo padrão do resto do produto — sem caso especial.

---

## 1. Matriz

| Ação | Escopos oferecidos |
|---|---|
| `view` | `self`, `team`, `store`, `all` |
| `reply` | `self`, `team`, `store`, `all` |

**`self` = Conversas de Leads atribuídas a mim.** É o escopo que todo operador precisa, na prática, para trabalhar as próprias Leads — mas continua sendo configuração normal de Cargo, como qualquer outro escopo: um Cargo mal configurado, sem `chats:view:self`, deixaria o operador sem acesso às próprias conversas. O produto não impõe esse mínimo automaticamente, mesmo padrão de flexibilidade (e risco de má configuração) já aceito no resto do catálogo.

**Sem `create`, `edit`, `delete`.** Conversa não é criada, editada nem excluída pelo usuário — nasce automática (RN-CHT-01) e não tem exclusão modelada.

---

## 2. Por que `view` e `reply` são ações separadas

Mesmo padrão de outros pares consulta/ação do produto (ex.: `sessions:view` × `sessions:manage`). Permite um Cargo de supervisão — vê as Conversas do Time inteiro, sem poder responder em nome de ninguém — sem precisar inventar um terceiro nível.

---

## 3. Owner / Tenant

Não aparece nesta matriz — bypass total do catálogo.

---

*Fim do documento. Matriz de Permissões — Chat v0.2.*

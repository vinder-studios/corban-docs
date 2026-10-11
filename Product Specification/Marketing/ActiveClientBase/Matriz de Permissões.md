# Corban Marketing · Carteira Ativa — Matriz de Permissões

**Status:** Draft
**Aplicação:** Marketing
**Documento pai:** Corban Marketing · Carteira Ativa (módulo v0.6)
**Versão:** 0.4
**Data:** [a definir]

## Histórico de revisões

| Versão | Mudanças |
|---|---|
| 0.4 | Acompanha o módulo v0.6: `create` deixa de citar a base legal (saiu do escopo inicial). |
| 0.3 | Acompanha o módulo v0.5: a Varredura Contínua passa a se chamar Recorrente. Nenhuma chave muda. |
| 0.2 | Esclarece o que a ação `approve` cobre: aprovar, pausar, retomar, cancelar e **tentar de novo** uma Varredura com Falha, e liberar de novo uma Varredura que voltou a Aguardando aprovação depois de uma edição (Variações por Permissão v0.1). Acompanha o módulo v0.3 |
| 0.1 | Versão inicial. Define os recursos do Marketing no catálogo de permissões, que o Documento de Produto do Marketing listava como "a consolidar". |

## Como usar este documento

Cobre o recurso deste módulo: `sweeps` (Varreduras). Formato da chave: `recurso:ação:escopo`.

**Aplicação do recurso: Marketing.** Ter ao menos uma permissão dele dá acesso à aplicação (Usuários, RN-USR-16).

A tabela é compacta: uma linha por `recurso:ação`, com os escopos oferecidos. Para a chave completa, combine a ação com o escopo (ex.: `sweeps:view` → `sweeps:view:all`).

**O que diferencia um Cargo do outro não é a chave, e sim quais linhas ele recebe.**

---

## 1. Matriz

| Recurso | Ação | Escopos oferecidos | O que cobre |
|---|---|---|---|
| `sweeps` | `view` | `all` | Ver a listagem das Varreduras e o Resultado de cada uma, por execução (RF-CRA-02, RF-CRA-04) |
| `sweeps` | `create` | `all` | Configurar uma Varredura: Segmento, Filtro de base, Operações, recorrência, limite diário e Origem de Venda (RF-CRA-01) |
| `sweeps` | `edit` | `all` | Editar uma Varredura, conforme a RN-CRA-09 (a Recorrente só se edita Pausada) |
| `sweeps` | `approve` | `all` | Aprovar a injeção e controlá-la: pausar, retomar, cancelar e tentar de novo uma Varredura com Falha (RF-CRA-03, RF-CRA-04) |
| `sweeps` | `delete` | `all` | Descartar uma Varredura em Rascunho ou Cancelada, sem Leads injetadas (RF-CRA-04) |

---

## 2. Por que tudo em escopo `all`

A Varredura percorre a base de Clientes do **tenant**, e os Clientes do Marketing nascem sem Loja (RN-MKT-09). Não existe "a Varredura da minha loja", então só o escopo `all` é oferecido.

---

## 3. Notas sobre as ações

**`create`, `edit` e `approve` são separadas.** Configurar não é autorizar. A aprovação libera as Leads no rodízio e **debita o saldo das Lojas** (RN-MKT-07). Editar uma Varredura que muda o universo a devolve para **Aguardando aprovação** (RN-CRA-09): quem edita precisa de `approve` para liberá-la de novo, e quem só tem `edit` deixa a Varredura esperando.

**`approve` cobre pausar, retomar e cancelar**, como na Prospecção (`prospecting_lists:approve`).

**Quem vê o Resultado não precisa de `create`.** O Resultado mostra contadores e valor potencial, não dados de contato.

---

## 4. Owner / Tenant

Não aparece nesta matriz. Owner/Tenant tem bypass total do catálogo de permissões (Usuários, RN-USR-03).

---

*Fim do documento. Matriz de Permissões — Carteira Ativa v0.4, companheira do módulo v0.6.*

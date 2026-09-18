# Corban Admin · Segmentos — Matriz de Permissões

**Status:** Draft
**Aplicação:** Admin · Seção Cadastros
**Documento pai:** Corban Admin · Segmentos (módulo v0.3)
**Versão:** 0.2
**Data:** 2026-09-17

## Histórico de revisões

| Versão | Mudanças                                                                                                                                                                                  |
| ------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 0.1    | Versão inicial — seis ações (`view`, `create`, `edit`, `inactivate`, `reactivate`, `delete`).                                                                                             |
| 0.2    | Catálogo reduzido a **duas ações**, `view` e `manage`, mesmo modelo adotado em Produtos. `manage` cobre criar, editar, inativar, reativar e excluir. Remove a nota sobre ações separadas. |

## Como usar este documento

Cobre apenas o recurso deste módulo (`segments`). Formato da chave, conforme o padrão do produto: `recurso:ação:escopo`.

A tabela é compacta: uma linha por `recurso:ação`, com os escopos oferecidos. Para obter a chave completa, combine a ação com o escopo (ex.: `segments:view` → `segments:view:all`).

**O que diferencia um Cargo do outro não é a chave, e sim quais linhas ele recebe.**

---

## 1. Matriz

| Recurso    | Ação     | Escopos oferecidos | Cobre                                                        |
| ---------- | -------- | ------------------ | ------------------------------------------------------------ |
| `segments` | `view`   | `all`              | Ver o destino, a Lista e abrir o Detalhe (em modo consulta). |
| `segments` | `manage` | `all`              | Criar, editar, inativar, reativar e excluir Segmentos.       |

Texto no catálogo de permissões do Cargo:

| Chave             | Label               | Descrição                                              |
| ----------------- | ------------------- | ------------------------------------------------------ |
| `segments:view`   | Ver segmentos       | Consultar o cadastro de Segmentos.                     |
| `segments:manage` | Gerenciar segmentos | Criar, editar, inativar, reativar e excluir Segmentos. |

---

## 2. Por que tudo em escopo `all`

Segmento é cadastro **tenant-wide**: não pertence a Loja nem a Time, e não tem dono individual. Os escopos `store`, `team` e `self` não têm significado aqui — não existe "o segmento da minha loja". Por isso as duas ações, inclusive `view`, são oferecidas apenas em `all`.

Consequência prática: um Cargo ou vê o cadastro de Segmentos inteiro, ou não vê nada dele — e, nesse caso, o destino de navegação nem é renderizado.

---

## 3. Notas sobre as ações

**Por que `manage` e não ações separadas.** Criar, editar, inativar, reativar e excluir são escritas sobre o mesmo cadastro pequeno e tenant-wide; separar cada uma multiplicaria linhas no catálogo do Cargo sem um caso real de "pode inativar mas não editar". É o verbo padrão do produto para o conjunto de escritas de um recurso (mesmo modelo de Produtos). Consequência: não existe controle que funciona numa direção só — quem tem `manage` liga e desliga o switch.

**Ações sobre o Funil não são deste catálogo.** O atalho "Configurar" e a opção "Reativar os dois" dependem de permissões do catálogo de **Funis de Vendas** (ver Variações por Permissão §5 e §6).

**Sem `manage_permissions`.** Não se aplica: Segmento não carrega permissões, diferente de Cargo.

---

## 4. Owner / Tenant

Não aparece nesta matriz. Owner/Tenant tem bypass total do catálogo de permissões — nunca é resolvido por escopo ou linha.

---

_Fim do documento. Matriz de Permissões — Segmentos v0.2, companheira do módulo v0.3._

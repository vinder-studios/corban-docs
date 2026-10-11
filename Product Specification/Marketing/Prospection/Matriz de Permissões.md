# Corban Marketing · Prospecção — Matriz de Permissões

**Status:** Draft
**Aplicação:** Marketing
**Documento pai:** Corban Marketing · Prospecção (módulo v0.5)
**Versão:** 0.3
**Data:** [a definir]

## Histórico de revisões

| Versão | Mudanças |
|---|---|
| 0.3 | Acompanha o módulo v0.5: `create` deixa de citar a base legal (saiu do escopo inicial). |
| 0.2 | **Remove o recurso `client_loads`** (a Carga de base foi removida do módulo, Prospecção v0.4). `approve` passa a cobrir também **distribuir uma Lista Guardada** e **concluir sem distribuir**. Acompanha o módulo v0.4. |
| 0.1 | Versão inicial. Define os recursos do Marketing no catálogo de permissões, que o Documento de Produto do Marketing listava como "a consolidar". |

## Como usar este documento

Cobre os recursos deste módulo: `prospecting_lists`, `blocklist` e `marketing_settings`. Formato da chave, conforme o padrão do produto: `recurso:ação:escopo`.

**Aplicação dos recursos: Marketing.** Todos os recursos deste documento pertencem à aplicação Marketing. Ter ao menos uma permissão deles dá acesso à aplicação (Usuários, RN-USR-16).

A tabela é compacta: uma linha por `recurso:ação`, com os escopos oferecidos. Para obter a chave completa, combine a ação com o escopo (ex.: `prospecting_lists:view` → `prospecting_lists:view:all`).

**O que diferencia um Cargo do outro não é a chave, e sim quais linhas ele recebe.**

---

## 1. Matriz

| Recurso | Ação | Escopos oferecidos | O que cobre |
|---|---|---|---|
| `prospecting_lists` | `view` | `all` | Ver a listagem das Listas, o detalhe e o resultado da pré-análise (RF-PRS-03, RF-PRS-05) |
| `prospecting_lists` | `create` | `all` | Importar, mapear, informar os dados da Lista, configurar a pré-análise e reprocessar (RF-PRS-01, RF-PRS-02) |
| `prospecting_lists` | `approve` | `all` | Distribuir as Leads (de uma Lista pronta ou Guardada), concluir sem distribuir e controlar a injeção: pausar, retomar e cancelar (RF-PRS-04, RN-PRS-17) |
| `prospecting_lists` | `delete` | `all` | Descartar uma Lista em Pendente de mapeamento ou Falha (RF-PRS-05) |
| `blocklist` | `view` | `all` | Consultar a Lista de bloqueio (RF-PRS-07) |
| `blocklist` | `manage` | `all` | Importar, adicionar e remover entradas da Lista de bloqueio (RF-PRS-07) |
| `marketing_settings` | `view` | `all` | Ver o parâmetro de carência (RF-PRS-08) |
| `marketing_settings` | `edit` | `all` | Alterar o parâmetro de carência (RF-PRS-08) |

---

## 2. Por que tudo em escopo `all`

O Marketing atua **acima das Lojas**, no nível do tenant (Documento de Produto do Marketing, §1). Lista, bloqueio e carência não pertencem a Loja nem a Time, então `store`, `team` e `self` não têm significado aqui. Um Cargo ou tem a permissão, ou não tem.

---

## 3. Notas sobre as ações

**`create` e `approve` são separadas.** Quem configura uma Lista não é, necessariamente, quem autoriza a injeção. Distribuir faz as Leads entrarem no rodízio e **debita o saldo das Lojas** (Leads, RN-LED-13), então é uma decisão com efeito financeiro em terceiros (RN-MKT-07). Um Cargo pode preparar Listas sem poder liberá-las.

**`approve` cobre também pausar, retomar e cancelar.** São os controles da mesma decisão. Quem pode liberar a injeção pode freá-la. *(Escolha desta versão; se for preciso que o Cargo possa cancelar sem poder aprovar, a ação se divide.)*

**Criar uma Lista cria Clientes.** Importar uma Lista cria Clientes do tenant em massa (RN-PRS-16), por isso `prospecting_lists:create` já cobre isso. Também **não exige nenhuma permissão de Clientes no CRM**: o Marketing é independente do CRM.

**A Lista de bloqueio tem `view` separado de `manage`.** Consultar o conteúdo expõe CPFs e telefones, e alterá-la muda quem pode ser abordado em todo o tenant (RN-MKT-05).

**Sem ação `export`.** A v1 não exporta Listas nem a Lista de bloqueio.

**Cada ação inclui as anteriores?** Não. As chaves são independentes, como no resto do produto. A interface esconde o que a pessoa não pode usar: quem tem `prospecting_lists:create` sem `view` não encontra a listagem, e o Cargo deve ser montado com os dois.

---

## 4. Owner / Tenant

Não aparece nesta matriz. Owner/Tenant tem bypass total do catálogo de permissões (Usuários, RN-USR-03).

---

*Fim do documento. Matriz de Permissões — Prospecção v0.3, companheira do módulo v0.5.*

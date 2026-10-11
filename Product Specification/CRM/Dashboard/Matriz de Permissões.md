# Corban CRM · Dashboard — Matriz de Permissões

**Status:** Draft
**Aplicação:** CRM
**Documento pai:** Corban CRM · Dashboard (módulo v0.3)
**Versão:** 0.1
**Data:** [a definir]

## Como usar este documento

**Este módulo não tem recurso próprio no catálogo.** Cada bloco é gatejado pela permissão do módulo de origem, já documentada ali — este arquivo só consolida, para referência rápida, quais permissões controlam o Dashboard.

---

## 1. Permissões que controlam cada bloco

| Bloco | Permissão exigida | Escopo |
|---|---|---|
| Minha carteira | `leads:view` | Qualquer (inclusive `self`) |
| Meus Resultados | `proposals:view` | Qualquer (inclusive `self`) — sempre mostra dados do próprio usuário |
| Minha Meta | `goals:view` | Qualquer (inclusive `self`) — só aparece com Meta Vigente |
| Alerta de Créditos | `credits:view` | `store` |
| Atalho "Nova lead" | `leads:create` | `store` |
| Atalho "Nova simulação" | `simulations:create` | Qualquer |

---

## 2. Notas

**A rota do Dashboard em si não é gatejada.** É a tela inicial pós-login do CRM (RN-USR-16, acesso à aplicação já resolvido antes de chegar aqui) — sempre acessível a quem está autenticado no CRM. Um usuário sem nenhuma das permissões acima vê uma versão vazia, não um erro de acesso.

**Nenhum bloco tem permissão de edição** — tudo aqui é consulta; ações (abrir uma Meta, ir pro Board, criar uma Lead) navegam para a tela de origem, que aplica sua própria permissão de escrita lá.

---

*Fim do documento. Matriz de Permissões — Dashboard v0.1.*

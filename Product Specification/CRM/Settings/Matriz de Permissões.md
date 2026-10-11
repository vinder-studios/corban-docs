# Corban CRM · Configurações — Matriz de Permissões

**Status:** Draft
**Aplicação:** CRM
**Documento pai:** Corban CRM · Configurações (módulo v0.3)
**Versão:** 0.1
**Data:** [a definir]

## Como usar este documento

Dois sub-recursos, um por aba (módulo §5). Formato: `recurso:ação:escopo`.

---

## 1. Matriz

| Recurso | Ação | Escopo |
|---|---|---|
| `store_profile` | `edit` | `store` |
| `store_segments` | `edit` | `store` |

---

## 2. Notas sobre as ações

**Só `edit`, sem `view` separado.** Diferente de outros recursos do CRM, aqui não há um estado "consulta sem editar" que faça sentido — Nome/Endereço/Logo e as flags de Segmento já aparecem em outras telas (Lojas no Admin, para quem tem acesso; a própria ficha de Time referencia o Segmento). Este módulo existe para **editar**; quem não tem `edit`, simplesmente não vê o destino de topo (RN-CFG pressupõe isso).

**Dois recursos, não um, pelo mesmo motivo já registrado no módulo:** um Cargo pode editar Informações básicas sem poder mexer em Segmentos, ou vice-versa (ex.: marketing edita Logo; operação decide Segmentos).

**Só `store`**, mesmo critério de todo o módulo — é a Loja inteira, sem recorte menor.

---

## 3. Owner / Tenant

Não aparece nesta matriz — bypass total do catálogo.

---

*Fim do documento. Matriz de Permissões — Configurações v0.1.*

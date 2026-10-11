# Corban CRM · Membros — Matriz de Permissões

**Status:** Draft
**Aplicação:** CRM · Grupo Gestão
**Documento pai:** Corban CRM · Membros (módulo v0.3)
**Versão:** 0.2
**Data:** [a definir]

## Histórico de revisões

| Versão | Mudanças |
|---|---|
| 0.1 | Versão inicial. |
| 0.2 | Acompanha Membros v0.3 (decisão a: vínculo direto Usuário→Loja, RN-USR-15). Esclarece que `store` resolve pela Loja do usuário, sem depender de Time. |

## Como usar este documento

Cobre os recursos `members` e `members_sessions`. Formato da chave: `recurso:ação:escopo`, mesmo padrão do Admin e do restante do CRM. É o espelho, no CRM, de `users` e `sessions` (módulo Usuários, Admin) — com uma diferença de escopo, explicada em §2.

---

## 1. Matriz

| Recurso | Ação | Escopos oferecidos |
|---|---|---|
| `members` | `view` | `store` |
| `members` | `create` | `store` |
| `members` | `edit` | `store` |
| `members` | `manage_access` | `store` |
| `members` | `manage_permissions` | `store` |
| `members` | `inactivate` | `store` |
| `members` | `reactivate` | `store` |
| `members_sessions` | `view` | `store` |
| `members_sessions` | `manage` | `store` |

---

## 2. Por que só `store`

**Todas as ações são oferecidas em um único escopo: `store`.**

- **`store` resolve pela Loja do usuário (vínculo direto, RN-USR-15).** Um Gestor de Loja sem Time continua alcançando a Loja inteira; o que exige Time é só o escopo `team`.
- **Não há `all`.** O módulo é, por definição, a gestão da equipe da própria Loja — qualquer ação de escopo `all` está fora do alcance de um Gestor de Loja (módulo, §7). Quem gerencia usuários do tenant inteiro usa o módulo Usuários, no Admin.
- **Não há `team` nem `self`.** O módulo não define nenhum recorte de gestão menor que a Loja. Se surgir a necessidade (ex.: um líder de Time que só consulta a própria equipe), o escopo `team` pode ser acrescentado depois em `view`, sem mudar o restante.

Diferença em relação ao Admin: lá, `users:view` e `sessions:view` também aceitam `store`, mas todas as ações de escrita são só `all`. Aqui, a situação se inverte: como o módulo é o da Loja, **as ações de escrita existem justamente em `store`**.

---

## 3. Notas sobre as ações

**`members:edit` vs. `members:manage_access` vs. `members:manage_permissions`.** Mesma separação de sensibilidade de Usuários: editar dados cadastrais (nome, e-mail); definir Cargo, Time e a flag "Pode receber lead" do Membro; e conceder permissões adicionais avulsas. A última é a mais sensível.

**Restrições próprias do módulo (não são ações do catálogo, são limites das ações):**
- `manage_access` só oferece Cargos sem nenhuma linha de escopo `all` (RN-MBR-02).
- `manage_permissions` só oferece linhas de escopo `store`, `team` e `self` (RN-MBR-03).

**Sem `delete`.** Membro, como Usuário, não tem exclusão definitiva (RN-USR-05) — só inativação e reativação.

**Item aberto que afeta esta matriz:** nada aqui impede um Gestor de Loja de atribuir um Cargo, ou conceder uma permissão, que ele mesmo não possui (módulo, §9.2). Se a decisão for limitar ao que o Gestor já tem, essa regra entra como restrição de `manage_access` e `manage_permissions`.

---

## 4. Owner / Tenant

Não aparece nesta matriz. Owner/Tenant tem bypass total do catálogo de permissões (RN-USR-03).

---

*Fim do documento. Matriz de Permissões — Membros v0.2.*

# ADR-0023 — Sem chave estrangeira entre schemas de módulos diferentes

**Status:** Aceita
**Data:** 2026-10-10
**Decisores:** Rafael da Conceição
**Origem:** preparação do modelo de dados
**Afeta:** modelo de dados de todos os módulos; ADR-0006, ADR-0012

## 1. Contexto

Cada módulo é dono das suas tabelas, num schema próprio do PostgreSQL, e só é acessado pelo seu contrato público (ADR-0006, ADR-0012, ADR-0014). Várias entidades referenciam entidades de outros módulos: a Lead aponta para a Loja (Estrutura) e para o Funil (Funis); a Proposta aponta para a Oferta (Catálogo comercial); o Contrato aponta para a Proposta. É preciso decidir se essas referências viram chave estrangeira no banco.

## 2. Decisão

**Entre schemas de módulos diferentes, a referência é só o identificador, sem chave estrangeira.** Dentro do mesmo módulo, chave estrangeira normal.

- A existência e o estado da entidade referenciada são validados pela aplicação, pelo contrato do módulo dono, no momento da escrita.
- Colunas de referência externa seguem um padrão de nome (`<entidade>_id`) e têm índice.
- Exclusão no produto é suave (RN-SC-08), então a entidade referenciada continua existindo para o histórico.
- Quando o dado de outro módulo precisa ser preservado no tempo, ele é **copiado** no registro (snapshot), como a Oferta congelada na Proposta (RN-SC-06), e não só referenciado.

## 3. Alternativas consideradas

| Alternativa | A favor | Contra | Por que não |
|---|---|---|---|
| Chave estrangeira entre schemas | Integridade garantida pelo banco | Acopla módulos no banco; migrations de um módulo dependem de outro; impede extrair um módulo | Quebra no banco a fronteira que a ADR-0006 mantém no código |

## 4. Consequências

- **Ganhos:** fronteira entre módulos também no banco; migrations independentes por módulo; um módulo pode ser extraído sem desmontar chaves.
- **Custos e riscos aceitos:** o banco não impede um identificador órfão entre módulos; a validação fica na aplicação e nos testes. Com exclusão suave, o risco prático é baixo.
- **O que muda nos documentos:** o modelo de dados marca as referências externas como "ref. lógica" e as internas como FK.

## 5. Quando revisar

Se identificadores órfãos entre módulos aparecerem na prática, apesar da exclusão suave.

## Histórico de status

| Data | Status | Observação |
|---|---|---|
| 2026-10-10 | Aceita | Decisão do usuário (opção a) na preparação do modelo de dados. |

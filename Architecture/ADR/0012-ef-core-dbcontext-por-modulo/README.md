# ADR-0012 — EF Core com Npgsql, um DbContext por módulo

**Status:** Aceita
**Data:** 2026-10-10
**Decisores:** Rafael da Conceição
**Origem:** P-07 (`C4/03-componentes-api.md`)
**Afeta:** C4 · Componentes da API (Persistência, todos os módulos); testes de isolamento do RNF002

## 1. Contexto

Cada módulo é dono das suas tabelas, num schema próprio do PostgreSQL (Componentes da API, §5; ADR-0003). A Persistência é a segunda barreira do isolamento entre Lojas: toda consulta de dado de Loja passa pelo filtro de escopo (RNF002). Fluxos de dinheiro cruzam módulos e precisam de transação local (ADR-0006). O módulo Desempenho faz consultas de relatório com agregação e cruzamento.

## 2. Decisão

**EF Core com o provedor Npgsql, com um `DbContext` por módulo**, no projeto `<Módulo>.Infrastructure`.

- **Schema e migrations por módulo.** Cada `DbContext` mapeia só as tabelas do seu schema e tem as suas próprias migrations.
- **Filtro de escopo por global query filter** em toda entidade que pertence a uma Loja, alimentado pelo escopo do usuário da requisição. Ignorar o filtro exige chamada explícita e só é permitido em rotinas do sistema (Worker, migração).
- **Transação entre módulos** usando a mesma conexão e transação do PostgreSQL, compartilhada pelos `DbContext`s envolvidos (por exemplo, atribuir Lead e debitar Créditos). Continua sendo transação local.
- **Relatórios do Desempenho** podem usar SQL direto (Dapper ou SQL pelo próprio EF) sobre visões de leitura, **sempre** com o filtro de escopo aplicado por um helper comum.

## 3. Alternativas consideradas

| Alternativa | A favor | Contra | Por que não |
|---|---|---|---|
| Um `DbContext` único | Transação entre módulos trivial | Todo módulo enxerga todas as tabelas; fronteira some no código | Quebra a regra 1 da §5 dos Componentes |
| Dapper em tudo | Controle total e desempenho | Sem migrations nem filtro de escopo automático; isolamento depende de cada consulta escrita à mão | Risco alto para o RNF002 |

## 4. Consequências

- **Ganhos:** padrão conhecido do .NET; fronteira de dados reforçada pelo compilador (P-09); isolamento entre Lojas aplicado por padrão, não por lembrança.
- **Custos e riscos aceitos:** consultas de relatório mal escritas em LINQ ficam lentas, então o Desempenho usa SQL quando preciso; compartilhar transação entre contextos exige um padrão único (unidade de trabalho comum), definido no esqueleto; o filtro global precisa de teste automatizado que prove que uma Loja não lê dado de outra (RNF002).
- **O que muda nos documentos:** Componentes da API, §6, passa a citar esta ADR no lugar da P-07.

## 5. Quando revisar

Se o desempenho do EF Core em algum módulo de escrita intensa (Leads, Créditos) não atender, ou se o compartilhamento de transação entre contextos ficar frágil na prática.

## Histórico de status

| Data | Status | Observação |
|---|---|---|
| 2026-10-10 | Aceita | Premissa P-07 aceita pelo usuário. |

# Corban Admin · Layout de Lead — Mudanças para o wireframe (v0.4 → v0.6)

**Status:** Draft
**Aplicação:** Admin
**Documento fonte:** Corban Admin · Layout de Lead (módulo v0.6). **Em caso de divergência, vale o módulo.**
**Versão:** 0.1
**Data:** [a definir]

## Como usar este documento

O Layout de Lead não tem Fase 2 (Descrição, Mapa, Microcopy...): o wireframe é externo e parte direto do módulo. Este documento lista **só o que mudou** entre a v0.4 e a v0.6, para quem mantém o wireframe saber o que redesenhar. Cada linha aponta a regra do módulo onde está a definição completa. Se o wireframe atual foi feito sobre uma versão anterior à v0.4, confira também as linhas marcadas como "esclarecimento".

---

## 1. O que muda no Card

| # | Onde | Antes (v0.4) | Agora (v0.6) | Regra |
|---|---|---|---|---|
| 1 | **Slot 3**, bloco padrão de um Funil novo | Dizia "três variantes por Segmento", sem dizer qual nasce selecionada | Nasce como **"Informações do beneficiário"** (Órgão, Matrícula). O gestor troca a variante depois. As três variantes continuam as mesmas (servidor, beneficiário, trabalhador) | RN-LAY-03, RN-LAY-12 |
| 2 | **Slot 4**, bloco padrão | "Tags (badges)" | **"Origem e tags"**: primeiro o **badge da Origem da lead**, depois as Tags (badges). Valores da Origem da lead: Campanha, Orgânica, Recontato, Prospecção, Carteira Ativa, Manual | RN-LAY-03, RN-LED-12, RN-FNL-06 |
| 3 | **Footer**, botão "Ações" | O módulo trazia aqui uma tabela de Ações por Stage (incluía Formalização) | O botão continua em todo Card, sempre. **Nada se desenha no Layout** sobre as Ações por Stage: o Sheet de execução é do módulo de **Leads** (RN-LED-11), que define quais Ações aparecem para cada Stage | RN-LAY-10, RN-LAY-04 |

Slots 1, 2, 5 e 6 e o botão "Chat" **não mudaram**.

## 2. O que muda no Sheet

| # | Onde | Antes (v0.4) | Agora (v0.6) | Regra |
|---|---|---|---|---|
| 4 | **Composição padrão** de um Funil novo | Não era definida | Nasce com os **oito blocos obrigatórios, nesta ordem**: Dados pessoais, Dados financeiros, Ofertas, Documentos do Cliente, Documentos da Proposta, Dados de Marketing, Dados adicionais, Histórico. **Sem blocos opcionais**; o gestor adiciona depois | RN-LAY-03, RN-LAY-05 |
| 5 | **Quantidade de blocos obrigatórios** (esclarecimento) | Alguns textos diziam "sete" | São **oito**, e nenhum é removível | RN-LAY-05 |
| 6 | Bloco **Dados de Marketing** | Fonte, campanha, conteúdo | **Origem da lead** (só leitura) como primeiro campo; **Origem de Venda** (**editável**, único campo editável do bloco); depois fonte, campanha e conteúdo. O bloco continua sendo da **Lead que abriu o Sheet**, e não do Cliente | RN-LAY-05, RN-LAY-07 |

### Como desenhar o campo Origem de Venda (item 6)

O comportamento completo está em `corban-admin-origem-venda-comportamento` (e no módulo de Origem de Venda). O que o wireframe precisa mostrar:

- **Combobox**, seleção única, **opcional**: placeholder "Selecione a origem"; pode ficar em branco, e quem selecionou pode limpar.
- **Criável**: quando a busca não acha nada, aparece "Criar nova: [termo]".
- **Excluir** uma Origem é uma ação em cada item da lista, com confirmação por **digitação do nome**; só aparece para quem tem `sale_origins:delete` com escopo `all`.
- **Criar** exige `sale_origins:create` (`all` ou `store`); **consultar** não exige permissão.
- Uma Lead cuja Origem foi excluída mostra o texto com a marca **"(origem excluída)"**.

## 3. O que NÃO mudou

- A grade do Card (seis slots, header, body em duas seções, footer) e a do Sheet (header fixo com Nome, Sobrenome, ID e Valor; body em lista ordenável de blocos).
- Os blocos opcionais: Dados do servidor, Dados Trabalhistas, Dados da Empresa e Notas (e a regra do campo condicional Estado/Prefeitura, RN-LAY-11).
- A divisão dos documentos por dono, em dois blocos (Cliente e Proposta, RN-LAY-06).
- O padrão visual dos accordions (RN-LAY-09) e as Notas como lista de links que abrem Dialog (RN-LAY-08).
- O escopo por Funil (RN-LAY-02): cada Segmento tem o seu layout.

## 4. Onde conferir fora deste módulo

- **Sheet de execução de Ações:** módulo de Leads (CRM), RN-LED-11.
- **Origem de Venda e Origem da lead:** módulos de Origem de Venda (Admin) e de Leads (CRM, RN-LED-12).
- **Quais Segmentos e Funis existem:** módulo de Funis de Vendas; o Layout é a 6ª aba do Funil.

---

*Fim do documento. Mudanças para o wireframe do Layout de Lead v0.1 — companheiro do módulo v0.6.*

# Corban Admin · Regiões

**Status:** Draft
**Aplicação:** Admin
**Seção:** Cadastros
**Documento pai:** Corban Admin — Documento de Produto (guarda-chuva da aplicação)
**Versão:** 0.7
**Data:** [a definir]

## Histórico de revisões

| Versão | Mudanças |
|---|---|
| 0.1 | Versão inicial. |
| 0.2 | **Oferta deixa de se associar diretamente a Região** — a região de uma Oferta é derivada da Promotora que compõe sua Tabela de Convênio. Em compensação, **Loja passa a se associar a Região**. Nasce a regra de visibilidade de ofertas por região (RN-REG-04). |
| 0.3 | Fecha a regra de visibilidade: Loja **sem** Região vê todas as ofertas, sem restrição; interseção de uma única Região em comum já basta para a oferta aparecer. |
| 0.4 | Fecha os itens abertos: bloqueio por Promotoras/Lojas confirmado; hierarquia entre Regiões descartada (estrutura plana é definitiva); Região confirmada como rótulo livre, sem vínculo com dados de endereço estruturados. |
| 0.5 | **Inativar ou excluir uma Região com vínculos deixa de ser bloqueado e passa a remover os vínculos, com confirmação em dois passos** (decisão do usuário, na revisão de Promotoras): o primeiro passo descreve o impacto e o segundo exige digitar o nome da Região. Vale para Promotoras e Lojas, e para inativação e exclusão. Reescreve RN-REG-02 e RN-REG-03; §6, §7 e §8 acompanham. Corrige ainda: o §2 dizia que Regiões são associadas a "Promotoras e Ofertas" (a Oferta não se associa a Região; são Promotoras e Lojas); o §3 ainda citava "Tabela de Convênio" (a Oferta referencia a Promotora direto); o RF-REG-01 prometia exclusão só para Regiões "nunca usadas". Reordena esta tabela. |
| 0.6 | Revisão do módulo: o **§3** deixa de dizer que a Região "não conhece quem a usa" (o diálogo de dois passos consulta os vínculos, RN-REG-02); o **§9** fecha o item sobre o aviso da primeira Região (ele existe nos artefatos de Lojas; a regra no módulo de Lojas fica registrada para a revisão dele) e a consequência passa a constar na RN-REG-04; a **RN-REG-04** passa a "dois casos e uma definição" (a tabela tinha uma linha de definição no lugar de um caso). |
| 0.7 | **Reordena as regras no corpo** (estavam 01, 02, 04, 03). **A regra de visibilidade é aplicada fora de Ofertas**: o motor de Simulações e as Tabelas Multiplicadoras também a aplicam, e a distribuição de leads **não** usa Região (RN-LED-03); §3, §7, §8 e a RN-REG-04 acompanham. **§9 ganha o item "código editável e integrações"**, que constava só em Segmentos e Produtos. (Levantamento do Admin, segunda passada.) |

## Como usar este documento

Doc de módulo: entidades, campos, estados, requisitos funcionais (RF-REG) e regras de negócio (RN-REG) próprios.

---

# 1. Objetivo do módulo

Manter o cadastro dos recortes geográficos com que o tenant trabalha. A Região é um qualificador: outros cadastros do produto podem ser associados a uma ou mais Regiões, restringindo seu alcance geográfico.

---

# 2. Atores

- **Gestor de cadastros** — usuário cujas permissões efetivas incluem a gestão deste módulo. Tipicamente um Cargo de escopo `all`, já que Região é cadastro tenant-wide.
- **Demais usuários** — consomem Regiões indiretamente, ao associá-las a Promotoras e Lojas.

---

# 3. Entidades e conceitos

**Região.** Recorte geográfico definido pelo tenant. Cadastro **livre do tenant** — não é catálogo fixo (não é uma lista de UFs do IBGE); cada tenant define os recortes que fazem sentido para a sua operação, que podem ser estados, grupos de estados, cidades ou qualquer outro corte.

**É um rótulo livre, e só isso.** Região não tem estrutura interna nem vínculo com dados geográficos do sistema:
- **Sem hierarquia** — Regiões não contêm outras Regiões. Não existe "Sudeste" agrupando "São Paulo" e "Rio de Janeiro"; se o tenant precisar dos dois recortes, cria os dois como Regiões independentes e associa cada Promotora/Loja às que couberem.
- **Sem vínculo com endereço** — não há mapeamento entre Região e UF/cidade estruturados. O sistema não infere a Região de ninguém a partir de endereço; a associação é sempre manual e explícita.

Campos:

| Campo | Obrigatório | Notas |
|---|---|---|
| Nome | Sim | Como aparece em toda a interface (ex.: "Sudeste", "Grande São Paulo"). |
| Código | Sim | Identificador curto (ex.: `SE`, `GDE-SP`). **Único no tenant**, formato livre, editável após a criação. |
| Status | Sim | Ativo / Inativo (ver §6). |

**Sem campo de descrição.** Diferente de Segmentos e Produtos, Região não tem descrição — nome e código bastam para identificá-la.

**Quem se associa a Região.** A associação parte sempre do outro lado — a Região não guarda quem a usa, só consulta os vínculos ao ser inativada ou excluída (RN-REG-02):
- **Promotoras** podem ser associadas a uma ou mais Regiões.
- **Lojas** podem ser associadas a uma ou mais Regiões.

**Oferta não se associa diretamente a Região.** A região de uma Oferta é **derivada**: a Oferta referencia uma Promotora, e é a Promotora que carrega as Regiões. Uma Oferta cuja Promotora não tem Região associada é uma **oferta global** — vale para qualquer Loja, independentemente de região.

Nenhuma outra entidade do produto usa Região no momento. A distribuição de leads entre Lojas, em particular, **não** usa Região (RN-LED-03).

---

# 4. Requisitos funcionais

## RF-REG-01 — Gestão de regiões (CRUD)

**Como** gestor de cadastros, **eu quero** criar, consultar, editar e inativar Regiões **para** poder restringir geograficamente promotoras e ofertas.

**Comportamento esperado:** o gestor cadastra nome e código. A listagem permite busca por nome/código e filtro por status. A edição cobre ambos os campos, incluindo o código. A inativação é o caminho padrão de descontinuação; a exclusão definitiva também existe (RN-REG-03). Inativar ou excluir uma Região com vínculos os remove, após confirmação em dois passos (RN-REG-02).

**Regras específicas:**

**RN-REG-01 — Cadastro livre do tenant.** Regiões não são catálogo fixo do sistema — cada tenant define os recortes que quiser, no nível de granularidade que fizer sentido.

**RN-REG-02 — Inativar ou excluir uma Região com vínculos remove os vínculos, com confirmação em dois passos.** *(Antes, os vínculos bloqueavam a ação.)* Uma Região associada a Promotoras e/ou Lojas **pode** ser inativada ou excluída: a ação remove a Região de todas as Promotoras e Lojas que a tinham. Antes de aplicar, abre-se um diálogo de **dois passos**:

1. **Impacto** — descreve o que vai acontecer: quantas Promotoras e Lojas são afetadas e, entre elas, quais **ficarão sem Região**. Uma Promotora sem Região passa a ser global — suas ofertas aparecem para todas as Lojas (RN-PRM-05, módulo de Promotoras). Uma Loja sem Região passa a ver todas as ofertas (RN-REG-04).
2. **Confirmação por digitação** — o gestor digita o nome da Região para liberar o botão.

O diálogo só aparece quando há vínculos: sem eles, a inativação segue o fluxo simples (confirmação descritiva) e a exclusão, a confirmação reforçada por digitação. **Os vínculos removidos não voltam** se a Região for reativada — a reassociação é manual, nos módulos de Promotoras e de Lojas. *(Decisão desta revisão:)* o efeito sobre Promotoras e Lojas é consequência da permissão de inativar ou excluir Região, sem exigir permissão de edição daqueles módulos; a operação é **atômica** (a Região e os vínculos mudam juntos ou não mudam); e a auditoria registra a Região e os vínculos removidos.

**RN-REG-03 — Exclusão definitiva existe, com a mesma cascata e a mesma confirmação.** Região pode ser excluída em definitivo. Havendo vínculos, valem o diálogo de dois passos e a remoção de vínculos de RN-REG-02, e a exclusão em si não pode ser desfeita. Uma Região **Inativa** nunca tem vínculos (a inativação os removeu), então excluí-la não dispara cascata.

**RN-REG-04 — Visibilidade de ofertas por região.** É a razão de ser deste cadastro: Região existe para produzir esse recorte. A regra tem dois casos, conforme a situação da Loja, e uma definição:

| Situação da Loja | O que ela enxerga |
|---|---|
| **Sem Região associada** | **Todas as ofertas**, sem restrição alguma. Ausência de região significa ausência de recorte, não recorte vazio. |
| **Com Região(ões) associada(s)** | As ofertas cuja Promotora tenha **ao menos uma Região em comum** com a Loja — interseção parcial basta, não é preciso correspondência total —, **mais** as ofertas globais. |

**Oferta global** é aquela cuja Promotora não tem nenhuma Região associada. Vale para qualquer Loja, em qualquer caso acima.

*A regra é enunciada aqui porque nasce do domínio de Região, mas é aplicada fora dele: no domínio de Ofertas, no motor de Simulações (que filtra as Ofertas por Região, RN-SIM-01) e nas Tabelas Multiplicadoras (RN-MUT-01). A distribuição de leads entre Lojas **não** a usa (RN-LED-03).*

*Consequência operacional: como Loja sem Região vê tudo, associar a primeira Região a uma Loja — ou a uma Promotora — **reduz** o que é visto, o que é contraintuitivo ("adicionar" costuma ampliar). A confirmação correspondente vive nos módulos de Promotoras (RN-PRM-06) e de Lojas.*

---

# 5. Regras de negócio (consolidado)

- **RN-REG-01** — Cadastro livre do tenant, não catálogo fixo do sistema.
- **RN-REG-02** — Inativar ou excluir Região com vínculos remove os vínculos, com confirmação em dois passos (impacto + digitação do nome); não volta se reativar.
- **RN-REG-03** — Exclusão definitiva existe, com a mesma cascata e a mesma confirmação.
- **RN-REG-04** — Loja sem região vê tudo; Loja com região vê as ofertas com interseção de ao menos uma região + as ofertas globais.

---

# 6. Estados e transições

**Região:**
- **Ativa** — disponível para ser associada a Promotoras e Lojas.
- **Inativa** — não recebe novas associações e não tem vínculos (a inativação os removeu, RN-REG-02).

Transições: ativa → inativa (remove os vínculos, se houver, com confirmação em dois passos — RN-REG-02); inativa → ativa (livre — os vínculos removidos não voltam); ativa/inativa → excluída (RN-REG-03; a cascata só ocorre numa Região Ativa com vínculos).

---

# 7. Limites do módulo

Trata apenas do cadastro da Região. **Não** inclui: a associação de Promotoras a Regiões (módulo de Promotoras), a de Lojas (módulo de Lojas), nem a aplicação da regra de visibilidade RN-REG-04 (módulos de Ofertas, Simulações e Tabelas Multiplicadoras). Em todos os casos, a lógica vive do lado do outro domínio, com uma exceção: a remoção dos vínculos ao inativar ou excluir a Região (RN-REG-02), que é disparada daqui.

---

# 8. Dependências e o que o módulo expõe

**Depende de:** nada — é cadastro-base.

**Expõe para:**
- **Promotoras** — a lista de Regiões ativas, para associação.
- **Lojas** — a lista de Regiões ativas, para associação.
- **Promotoras e Lojas** — têm a Região removida quando ela é inativada ou excluída (RN-REG-02).
- **Ofertas, Simulações e Tabelas Multiplicadoras** — indiretamente: consomem a região da Promotora e a da Loja para aplicar RN-REG-04 (Ofertas define a regra; Simulações filtra as Ofertas por Região; Tabelas Multiplicadoras lista as Ofertas que a Loja enxerga, RN-MUT-01). Nenhum deles associa Região diretamente.

---

# 9. Itens abertos

1. **Código editável e integrações.** Mesmo item registrado em Segmentos, Produtos e Promotoras: o código é editável após a criação; se alguma integração externa vier a referenciar a Região por código, editá-lo passa a ser ação de risco. Reavaliar quando as integrações forem definidas.

---

*Fim do documento. Regiões v0.7 — módulo da aplicação Admin, seção Cadastros.*

# Corban Admin · Regiões

**Status:** Draft
**Aplicação:** Admin
**Seção:** Cadastros
**Documento pai:** Corban Admin — Documento de Produto (guarda-chuva da aplicação)
**Versão:** 0.2
**Data:** [a definir]

## Histórico de revisões

| Versão | Mudanças                                                                                                                                                                                                                                                         |
| ------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 0.1    | Versão inicial.                                                                                                                                                                                                                                                  |
| 0.2    | **Oferta deixa de se associar diretamente a Região** — a região de uma Oferta é derivada da Promotora que compõe sua Tabela de Convênio. Em compensação, **Loja passa a se associar a Região**. Nasce a regra de visibilidade de ofertas por região (RN-REG-04). |

## Como usar este documento

Doc de módulo: entidades, campos, estados, requisitos funcionais (RF-REG) e regras de negócio (RN-REG) próprios.

---

# 1. Objetivo do módulo

Manter o cadastro dos recortes geográficos com que o tenant trabalha. A Região é um qualificador: outros cadastros do produto podem ser associados a uma ou mais Regiões, restringindo seu alcance geográfico.

---

# 2. Atores

- **Gestor de cadastros** — usuário cujas permissões efetivas incluem a gestão deste módulo. Tipicamente um Cargo de escopo `all`, já que Região é cadastro tenant-wide.
- **Demais usuários** — consomem Regiões indiretamente, ao associá-las a Promotoras e Ofertas.

---

# 3. Entidades e conceitos

**Região.** Recorte geográfico definido pelo tenant. Cadastro **livre do tenant** — não é catálogo fixo (não é uma lista de UFs do IBGE); cada tenant define os recortes que fazem sentido para a sua operação, que podem ser estados, grupos de estados, cidades ou qualquer outro corte. Campos:

| Campo  | Obrigatório | Notas                                                                                                   |
| ------ | ----------- | ------------------------------------------------------------------------------------------------------- |
| Nome   | Sim         | Como aparece em toda a interface (ex.: "Sudeste", "Grande São Paulo").                                  |
| Código | Sim         | Identificador curto (ex.: `SE`, `GDE-SP`). **Único no tenant**, formato livre, editável após a criação. |
| Status | Sim         | Ativo / Inativo (ver §6).                                                                               |

**Sem campo de descrição.** Diferente de Segmentos e Produtos, Região não tem descrição — nome e código bastam para identificá-la.

**Quem se associa a Região.** A associação parte sempre do outro lado — a Região não conhece quem a usa:

- **Promotoras** podem ser associadas a uma ou mais Regiões.
- **Lojas** podem ser associadas a uma ou mais Regiões.

**Oferta não se associa diretamente a Região.** A região de uma Oferta é **derivada**: a Oferta referencia uma Tabela de Convênio, que é composta por uma Promotora, e é a Promotora que carrega as Regiões. Uma Oferta cuja Promotora não tem Região associada é uma **oferta global** — vale para qualquer Loja, independentemente de região.

Nenhuma outra entidade do produto usa Região no momento.

---

# 4. Requisitos funcionais

## RF-REG-01 — Gestão de regiões (CRUD)

**Como** gestor de cadastros, **eu quero** criar, consultar, editar e inativar Regiões **para** poder restringir geograficamente promotoras e ofertas.

**Comportamento esperado:** o gestor cadastra nome e código. A listagem permite busca por nome/código e filtro por status. A edição cobre ambos os campos, incluindo o código. A inativação é o caminho padrão de descontinuação; a exclusão definitiva existe para Regiões que nunca chegaram a ser usadas.

**Regras específicas:**

**RN-REG-01 — Cadastro livre do tenant.** Regiões não são catálogo fixo do sistema — cada tenant define os recortes que quiser, no nível de granularidade que fizer sentido.

**RN-REG-02 — Vínculos bloqueiam inativação e exclusão.** Uma Região associada a ao menos uma Promotora ou a ao menos uma Loja **não pode ser inativada nem excluída**. O gestor precisa primeiro desfazer essas associações, nos respectivos módulos. _(Assumido por consistência com Segmentos e Produtos — ver item aberto §9.1.)_

**RN-REG-04 — Visibilidade de ofertas por região.** Uma Loja associada a uma ou mais Regiões só enxerga as Ofertas cuja Promotora esteja em ao menos uma dessas Regiões, **mais** as ofertas globais (aquelas cuja Promotora não tem Região associada). É a razão de ser deste cadastro: Região existe para produzir esse recorte.

_A regra é enunciada aqui porque nasce do domínio de Região, mas é aplicada no domínio de Ofertas — é lá que precisa ser implementada e detalhada._

**RN-REG-03 — Exclusão definitiva existe, sujeita ao mesmo bloqueio.** Região pode ser excluída em definitivo, desde que não tenha vínculos (RN-REG-02).

---

# 5. Regras de negócio (consolidado)

- **RN-REG-01** — Cadastro livre do tenant, não catálogo fixo do sistema.
- **RN-REG-02** — Vínculos com Promotora ou Loja bloqueiam inativação e exclusão.
- **RN-REG-03** — Exclusão definitiva existe, sujeita ao mesmo bloqueio da inativação.
- **RN-REG-04** — Loja com região vê apenas ofertas da(s) sua(s) região(ões) + ofertas globais.

---

# 6. Estados e transições

**Região:**

- **Ativa** — disponível para ser associada a Promotoras e Lojas.
- **Inativa** — não recebe novas associações. Só alcançável a partir de Ativa quando não há vínculos (RN-REG-02).

Transições: ativa → inativa (bloqueada por vínculos, RN-REG-02); inativa → ativa (livre); ativa/inativa → excluída (mesmo bloqueio, RN-REG-03).

---

# 7. Limites do módulo

Trata apenas do cadastro da Região. **Não** inclui: a associação de Promotoras a Regiões (módulo de Promotoras), a de Lojas (módulo de Lojas), nem a aplicação da regra de visibilidade RN-REG-04 (módulo de Ofertas). Em todos os casos, a lógica vive do lado do outro domínio.

---

# 8. Dependências e o que o módulo expõe

**Depende de:** nada — é cadastro-base.

**Expõe para:**

- **Promotoras** — a lista de Regiões ativas, para associação.
- **Lojas** — a lista de Regiões ativas, para associação.
- **Ofertas** — indiretamente: consome a região da Promotora para aplicar RN-REG-04. Não associa Região diretamente.

---

# 9. Itens abertos

1. **Confirmar o que bloqueia.** RN-REG-02 assume que Promotoras e Lojas vinculadas bloqueiam, por consistência com os demais cadastros. Confirmar.
   1b. **Loja sem Região associada — o que ela enxerga?** RN-REG-04 define o comportamento da Loja _com_ região. Falta definir o oposto: uma Loja sem nenhuma Região vê **todas** as ofertas (interpretação de "sem restrição") ou apenas as globais (interpretação de "sem região, sem alcance regional")? As duas leituras são defensáveis e levam a comportamentos opostos.
   1c. **Promotora em várias Regiões, Loja em várias Regiões.** A regra pressupõe interseção não vazia — basta uma região em comum para a oferta aparecer. Confirmar que é isso, e não exigência de correspondência total.
2. **Hierarquia entre Regiões.** O modelo atual é plano — Regiões não contêm outras Regiões. Se o tenant precisar de "Sudeste" contendo "São Paulo" e "Rio de Janeiro", isso não é suportado. Confirmar que a estrutura plana basta.
3. **Relação com dados de endereço.** Região é um rótulo livre, sem vínculo com UF/cidade estruturados. Se algum fluxo do produto precisar inferir a Região a partir do endereço de um cliente, isso não é possível hoje — precisaria de mapeamento explícito.

---

_Fim do documento. Regiões v0.2 — módulo da aplicação Admin, seção Cadastros._

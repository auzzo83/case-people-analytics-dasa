# People Analytics — Análise de Headcount

Case Técnico 

## 📁 Estrutura do Repositório

```
📦 people-analytics/
├── 📄 README.md                     ← Este arquivo
├── 📊 base_headcount.csv            ← Base original (dados fictícios)
│
├── 📖 glossario.docx                ← Dicionário das 31 colunas
│
├── 🗃️ tabela_base_limpa.csv         ← Base limpa e normalizada (Silver)
├── 🗃️ tabela_dim_funcionario.csv    ← Dimensão: dados do funcionário
├── 🗃️ tabela_dim_cargo.csv          ← Dimensão: cargo, dept, gestor
├── 🗃️ tabela_dim_remuneracao.csv    ← Dimensão: salário e bônus
├── 🗃️ tabela_fato_ponto.csv         ← Fato: ponto e assiduidade
├── 🗃️ tabela_fato_admissao.csv      ← Fato: ciclo de vida (admissão → deslig.)
├── 🗃️ tabela_fato_performance.csv   ← Fato: performance e compliance
│
├── 📝 documentacao.docx             ← Documentação técnica completa
└── 📊 apresentacao.pptx             ← Apresentação de análises e insights
```

---

## 🔍 Sobre a Base

| Atributo | Valor |
|----------|-------|
| Arquivo | `base_headcount.csv` |
| Registros | 50.000 linhas |
| Colunas | 31 |
| Domínios | Ponto · Folha · Admissão · Performance · Compliance · Treinamento |
| Natureza | Dados fictícios para fins de avaliação técnica |

---

## ⚙️ Tratamentos Aplicados

1. **tenure_years recalculado** — valor original divergia ~0,5 ano do cálculo correto (hire_date → termination_date ou 2025-12-31)
2. **Datas convertidas** de String para Date: `hire_date`, `termination_date`, `last_promotion_date`, `last_training_date`
3. **Normalização de strings** — strip de espaços em todas as colunas categóricas
4. **salary** arredondado para 2 casas decimais
5. **bank_hours negativos** validados (débito de banco de horas — comportamento esperado)
6. **probation_completed** mantido por auditoria (variância zero — 100% True)
7. **NULLs validados** como estruturalmente corretos (ativos sem termination_date; sem promoção/treinamento quando nunca ocorreram)
8. **Zero duplicatas** em employee_id e email

---

## 🗂️ Modelo de Dados (Star Schema)

```
                ┌─────────────────────┐
                │  dim_funcionario    │
                │  (dados pessoais)   │
                └────────┬────────────┘
                         │
 ┌──────────────┐   employee_id   ┌──────────────────┐
 │  dim_cargo   ├────────┼────────┤  fato_ponto      │
 │  (posição)   │        │        │  (assiduidade)   │
 └──────────────┘        │        └──────────────────┘
                         │
 ┌────────────────┐       │        ┌──────────────────┐
 │ dim_remuneracao├───────┼────────┤  fato_admissao   │
 │ (salário)      │       │        │  (ciclo de vida) │
 └────────────────┘       │        └──────────────────┘
                          │
                          │        ┌──────────────────┐
                          └────────┤  fato_performance│
                                   │  (avaliação)     │
                                   └──────────────────┘
```

---

## 📈 Principais Insights

| # | Achado | Impacto |
|---|--------|---------|
| 1 | Turnover uniforme de ~20% em todos os departamentos | 🔴 Alto — problema estrutural |
| 2 | 33% dos funcionários com banco de horas negativo | 🔴 Alto — sobrecarga crônica |
| 3 | 51,7% nunca foram promovidos | 🟡 Médio — risco de retenção |
| 4 | 29,8% sem registro de treinamento | 🟡 Médio — gap de T&D |
| 5 | 10% Non-Compliant distribuídos uniformemente | 🟡 Médio — ação corporativa necessária |
| 6 | Equilíbrio de gênero próximo da paridade | 🟢 Positivo — base para análise DEI |

---

## 🛠️ Ferramentas Utilizadas

- **Python / pandas** — limpeza, transformação e geração de tabelas
- **Node.js / docx** — geração do glossário e documentação (.docx)
- **Node.js / pptxgenjs** — geração da apresentação (.pptx)


---

## 📋 Commits Sugeridos

```
git commit -m "feat: add base_headcount source data"
git commit -m "docs: add glossario with 31-column dictionary and cleaning notes"
git commit -m "feat: add tabela_base_limpa - silver layer after cleaning"
git commit -m "feat: add star schema tables - dim_funcionario, dim_cargo, dim_remuneracao"
git commit -m "feat: add fact tables - fato_ponto, fato_admissao, fato_performance"
git commit -m "docs: add documentacao with full project documentation"
git commit -m "pres: add apresentacao with analysis and insights"
```

# Abnormal Order

## 1. Visão Geral

O **Abnormal Order** é um sistema de gestão e controle de pedidos destinado a identificar comportamentos de compra anormais na rede de clientes/concessionárias e controlar a alocação de estoque de maneira equilibrada.

O objetivo não é simplesmente bloquear pedidos acima de determinado limite.

O sistema busca:

- identificar pedidos que excedam um comportamento esperado;
- preservar o atendimento normal dos demais clientes;
- evitar concentração indevida de estoque;
- permitir que excedentes sejam atendidos quando houver disponibilidade;
- distribuir estoques limitados de maneira justa entre clientes;
- manter pedidos temporariamente sem alocação quando necessário;
- garantir que nenhum pedido permaneça indefinidamente retido;
- permitir que o cliente visualize um Back Order quando a cadeia de suprimentos não conseguir atender dentro do prazo esperado.

O projeto deve ser entendido como um **domínio de decisão sobre pedidos e alocação**, e não apenas como uma regra de bloqueio.

---

## 2. Princípio de Desenvolvimento

O projeto segue uma abordagem **Architecture First**.

A ordem esperada de desenvolvimento é:

1. Entendimento do problema
2. Definição do domínio
3. Terminologia
4. Regras de negócio
5. Entidades
6. Estados
7. Fluxos
8. Responsabilidades
9. Casos extremos
10. Arquitetura
11. Features
12. Implementação

A codificação deve ser considerada o **último passo**, salvo quando explicitamente solicitado.

Internamente, essa abordagem pode ser chamada de:

> **Vitrúvio — Architecture First**

---

## 3. Terminologia Fundamental

### 3.1 Material

No contexto do Abnormal Order, **Material não significa apenas Part Number**.

Material representa a entidade composta:

> **Empresa–Material**

Isso é necessário porque um mesmo Part Number pode existir em diferentes empresas/unidades de negócio.

Exemplo:

- Empresa 2W + Material ABC
- Empresa 4W + Material ABC

São dois **Materiais distintos** para fins do sistema.

```text
Material = Empresa–Material
```

### 3.2 Cliente–Material

Cliente–Material representa:

> **Cliente + (Empresa–Material)**

Ou seja, um cliente associado a uma determinada entidade Material.

Conceitualmente:

```text
Material
├── Empresa
└── Part Number

Cliente–Material
├── Cliente
└── Material
    ├── Empresa
    └── Part Number
```

Essa terminologia deve ser preservada na documentação para evitar representar Empresa, Cliente e Material como três dimensões independentes quando, conceitualmente, Material já contém Empresa.

---

## 4. Parâmetros

O sistema deve ser altamente parametrizável.

Exemplos:

- quantidade de meses de histórico;
- método de cálculo do histórico;
- limite superior/tolerância;
- quantidade mínima;
- regras aplicáveis aos Materiais;
- status das regras;
- demais parâmetros necessários ao motor.

Os valores utilizados neste documento são exemplos e **não devem ser considerados hardcoded**.

Exemplo atual:

```text
Historical Window = 6 meses
Upper Limit = 20%
Minimum Quantity = 10
```

---

## 5. Histórico de Demanda

### 5.1 Unidade de análise

O histórico é analisado no nível:

> **Cliente–Material**

Para cada Cliente–Material, o sistema considera uma janela histórica parametrizável.

Exemplo:

```text
Historical Window = 6 meses
```

Os seis meses são apenas o parâmetro atualmente utilizado.

### 5.2 Meses sem pedido

Meses sem pedidos fazem parte do histórico.

Portanto, um mês sem demanda deve ser considerado:

```text
Demand = 0
```

Exemplo:

| Mês | Pedido |
|---|---:|
| M-6 | 10 |
| M-5 | 0 |
| M-4 | 20 |
| M-3 | 0 |
| M-2 | 10 |
| M-1 | 20 |

A média deve considerar todos os períodos da janela, inclusive os zeros.

---

## 6. Cálculo da Média Histórica

Para cada Cliente–Material:

```text
Historical Average
=
Σ Historical Demand
/
Number of Historical Periods
```

Exemplo:

```text
Cliente 1 + Material A → média = 500
Cliente 2 + Material A → média = 300
Cliente 3 + Material A → média = 200
```

---

## 7. Participação Histórica do Cliente

Depois de calcular a média individual de cada Cliente–Material, o sistema calcula a participação de cada cliente dentro do respectivo Material.

Primeiro:

```text
Material Historical Average
=
Σ Historical Average de todos os Cliente–Material
```

No exemplo:

```text
500 + 300 + 200 = 1.000
```

Depois:

```text
Customer Participation
=
Customer-Material Historical Average
/
Material Historical Average
```

Resultado:

```text
Cliente 1 = 50%
Cliente 2 = 30%
Cliente 3 = 20%
```

Essa participação representa o **peso histórico daquele cliente na demanda do Material**.

Ela ainda não representa o limite de pedidos.

---

## 8. Forecast

O forecast é produzido externamente ao motor do Abnormal Order.

O sistema recebe o forecast por upload/importação.

### 8.1 Granularidade

O forecast é carregado no nível:

> **Material**

Lembrando:

```text
Material = Empresa–Material
```

Não existe, nesse momento, um forecast originalmente produzido no nível Cliente–Material.

---

## 9. Distribuição do Forecast

O sistema transforma o forecast do Material em uma referência de forecast por Cliente–Material utilizando a participação histórica.

Fórmula:

```text
Customer-Material Forecast
=
Material Forecast
×
Customer Participation
```

Exemplo:

```text
Material A Forecast = 1.000 peças

Cliente 1 Participation = 50%
Cliente 2 Participation = 30%
Cliente 3 Participation = 20%
```

Resultado:

```text
Cliente 1 → 500
Cliente 2 → 300
Cliente 3 → 200
```

Temos, portanto:

```text
Material Forecast
        │
        ▼
Historical Customer Participation
        │
        ▼
Customer-Material Forecast
```

---

## 10. Motor de Regras

O sistema possui um conjunto parametrizável de regras.

Uma regra pode definir, entre outros parâmetros:

```text
Upper Limit %
Minimum Quantity
Status
```

Exemplo:

```text
Rule R001

Upper Limit = 20%
Minimum Quantity = 10
Status = Active
```

Podem existir quantas regras forem necessárias.

---

## 11. Associação Material → Regra

Materiais são atribuídos às regras.

A associação ocorre no nível:

> **Material**

Portanto:

```text
Material → Rule
```

---

## 12. Regra de Unicidade

Um Material não pode pertencer simultaneamente a duas regras ativas diferentes.

Exemplo inválido:

```text
Material A → Rule 01
Upper Limit = 20%
Minimum Quantity = 10

Material A → Rule 02
Upper Limit = 15%
Minimum Quantity = 100
```

Isso produziria parâmetros contraditórios.

Portanto:

> **Para um determinado contexto de vigência, um Material pode possuir no máximo uma regra ativa.**

Essa é uma restrição estrutural do domínio.

---

## 13. Limite Superior

Depois de obter o forecast por Cliente–Material, o sistema aplica a regra associada ao Material.

Exemplo:

```text
Customer-Material Forecast = 500
Upper Limit = 20%
```

Cálculo:

```text
Upper Limit Quantity
=
500 × (1 + 20%)
```

Resultado:

```text
Upper Limit Quantity = 600
```

Portanto, naquele período:

```text
Forecast = 500
Tolerance = 100
Maximum Allowed Quantity = 600
```

Esse valor passa a ser uma referência para avaliação dos pedidos daquele Cliente–Material.

---

## 14. Quantidade Mínima

A quantidade mínima existe principalmente para evitar comportamentos inadequados no **long tail**.

Exemplo:

Um Cliente–Material possui expectativa mensal de:

```text
2 peças
```

Aplicar simplesmente:

```text
2 + 20%
```

produziria:

```text
2,4 peças
```

Esse tipo de controle seria operacionalmente inadequado.

Por isso existe:

> **Minimum Quantity**

Ela funciona como um piso operacional abaixo do qual a regra percentual não deve gerar uma restrição desnecessária.

O objetivo é evitar que pequenas quantidades de long tail sejam classificadas como anormais apenas por apresentarem grandes variações percentuais sobre bases extremamente pequenas.

Conceitualmente:

```text
Upper Limit %
        +
Minimum Quantity
        │
        ▼
Effective Order Limit
```

A fórmula definitiva de composição entre percentual e quantidade mínima deve permanecer explicitamente documentada como regra de negócio.

---

## 15. Avaliação de Pedidos

A avaliação ocorre conforme novas linhas de pedido entram no sistema.

A unidade de avaliação continua sendo:

> **Cliente–Material**

O sistema deve considerar o **acumulado do período**, e não apenas cada linha isoladamente.

---

## 16. Running Total

Exemplo:

```text
Upper Limit = 120
```

Pedidos:

```text
Pedido 1 = 10
Running Total = 10
→ permitido

Pedido 2 = 90
Running Total = 100
→ permitido

Pedido 3 = 50
Running Total = 150
→ excede o limite
```

O sistema não pode simplesmente analisar:

```text
50 < 120
```

Ele deve analisar:

```text
10 + 90 + 50 = 150
```

Portanto:

> **O controle é realizado sobre o consumo acumulado do limite do Cliente–Material dentro do período.**

---

## 17. Split da Linha de Pedido

Quando uma linha ultrapassa parcialmente o limite, ela deve ser separada conceitualmente entre:

```text
Allowed Quantity
+
Excess Quantity
```

Exemplo:

```text
Running Total before order = 100
Upper Limit = 120
New Order = 50
```

Ainda existem:

```text
20 peças disponíveis dentro do limite
```

Logo:

```text
20 → Allowed
30 → Excess
```

A linha original de 50 peças passa conceitualmente a representar dois tratamentos distintos.

---

## 18. On Hold

A quantidade excedente não deve necessariamente ser rejeitada.

Ela entra em um estado transitório:

> **On Hold**

Exemplo:

```text
Order Quantity = 50

20 → Allocatable
30 → On Hold
```

A quantidade On Hold:

- permanece como demanda do cliente;
- não recebe alocação imediata de estoque;
- aguarda uma avaliação posterior;
- pode ser liberada posteriormente;
- pode permanecer temporariamente retida.

Portanto:

> **On Hold não representa cancelamento nem rejeição do pedido.**

É um estado intermediário de decisão.

---

## 19. Separação entre Detecção e Atendimento

Um princípio importante da arquitetura é separar:

```text
Abnormality Detection
```

de:

```text
Supply / Allocation Decision
```

Detectar que um pedido excedeu o limite não significa automaticamente concluir que ele não será atendido.

O primeiro motor responde:

> O comportamento deste Cliente–Material excedeu sua referência?

O segundo processo responde:

> Considerando todos os clientes e a disponibilidade total do Material, podemos atender esse excedente?

Essa separação é fundamental para o domínio.

---

## 20. Reavaliação no Nível Material

Embora a detecção ocorra em:

> **Cliente–Material**

a reavaliação posterior acontece também no nível:

> **Material**

Isso acontece porque um cliente pode ultrapassar sua referência enquanto outros clientes compram abaixo das respectivas referências.

Exemplo:

```text
Cliente A → acima da referência
Cliente B → abaixo da referência
Cliente C → abaixo da referência
```

Pode existir disponibilidade suficiente no Material como um todo para atender o excedente do Cliente A.

---

## 21. Primeira Regra de Liberação

Na reavaliação, o sistema verifica a situação agregada do Material.

Se o comportamento agregado permanecer dentro das premissas disponíveis para o Material:

```text
Material Demand <= Material Available/Allowed Quantity
```

os pedidos anteriormente classificados como On Hold podem ser liberados.

Portanto:

```text
Customer abnormality
≠
Automatic supply rejection
```

---

## 22. Fair Share

Se a quantidade disponível não for suficiente para liberar todos os pedidos On Hold, deve ser aplicada uma regra de:

> **Fair Share**

O objetivo é impedir decisões arbitrárias de priorização de clientes.

Exemplo:

```text
Cliente A Excess = 100
Cliente B Excess = 50

Available Quantity = 20
```

Uma distribuição possível segundo a regra estabelecida:

```text
Cliente A = 10
Cliente B = 10
```

O algoritmo exato de Fair Share ainda deve ser formalizado.

Possíveis dimensões que precisarão ser definidas posteriormente:

- igualdade absoluta;
- proporcionalidade;
- participação histórica;
- quantidade excedente;
- prioridade operacional;
- combinação desses fatores.

O princípio já estabelecido é:

> **A liberação deve obedecer uma regra determinística e justa, não uma escolha manual arbitrária de clientes.**

---

## 23. Ciclo de Reavaliação

Os pedidos On Hold passam por avaliações periódicas.

Uma avaliação importante ocorre na **virada do mês**.

Nesse momento, o sistema determina quais pedidos:

```text
On Hold
   │
   ├── podem ser liberados
   │
   ├── podem ser parcialmente liberados
   │
   └── precisam permanecer On Hold
```

---

## 24. Supply Chain Evaluation

Quando um pedido permanece On Hold após a avaliação, a cadeia de suprimentos precisa determinar quando será possível atendê-lo.

Essa avaliação pode envolver:

- disponibilidade;
- estoque;
- reposição;
- compras;
- fornecedores;
- fábricas;
- lead time;
- demais restrições da cadeia.

O resultado esperado é uma **data estimada de atendimento**.

---

## 25. Prazo Máximo de Retenção

Um pedido não pode permanecer indefinidamente em On Hold.

Existe um prazo associado à capacidade esperada da cadeia de suprimentos de atender aquele Material.

Exemplo:

```text
Lead Time = 90 dias
```

Se o sistema determinar que o pedido não poderá ser atendido dentro do prazo permitido, o pedido não deve continuar escondido em On Hold.

---

## 26. Back Order

Quando o prazo máximo de retenção é atingido sem disponibilidade de estoque, o pedido deve ser liberado do On Hold.

Mesmo que não exista estoque.

Nesse caso:

```text
On Hold
   │
   ▼
Released
   │
   ▼
No Stock
   │
   ▼
Back Order
```

Isso é intencional.

O princípio de negócio é:

> **A indisponibilidade da cadeia de suprimentos não pode fazer com que o pedido do cliente permaneça indefinidamente retido.**

O cliente deve ter visibilidade de que existe um pedido pendente de atendimento.

Portanto, quando necessário:

> É preferível um Back Order explícito a um On Hold indefinido.

---

## 27. Máquina Conceitual de Estados

Uma primeira representação dos estados é:

```text
                    ┌───────────────────┐
                    │   ORDER ENTERED   │
                    └─────────┬─────────┘
                              │
                              ▼
                    ┌───────────────────┐
                    │ RULE EVALUATION   │
                    └─────────┬─────────┘
                              │
                 ┌────────────┴────────────┐
                 │                         │
           Within Limit              Above Limit
                 │                         │
                 ▼                         ▼
          ┌─────────────┐          ┌─────────────┐
          │ ALLOCATABLE │          │    SPLIT    │
          └─────────────┘          └──────┬──────┘
                                         │
                              ┌──────────┴──────────┐
                              │                     │
                           Allowed                Excess
                              │                     │
                              ▼                     ▼
                       ┌─────────────┐       ┌─────────────┐
                       │ ALLOCATABLE │       │   ON HOLD   │
                       └─────────────┘       └──────┬──────┘
                                                  │
                                             Reassessment
                                                  │
                         ┌────────────────────────┼──────────────────────┐
                         │                        │                      │
                         ▼                        ▼                      ▼
                    Full Release           Partial Release       Remain On Hold
                         │                        │                      │
                         │                   Fair Share                  │
                         │                        │                      │
                         └────────────────────────┴──────────────────────┘
                                                  │
                                             Time Evaluation
                                                  │
                                   ┌──────────────┴──────────────┐
                                   │                             │
                            Supply Available                Deadline Reached
                                   │                             │
                                   ▼                             ▼
                              RELEASED                      RELEASED
                                                                │
                                                          No Inventory
                                                                │
                                                                ▼
                                                           BACK ORDER
```

Esse modelo ainda deverá ser refinado antes de ser considerado definitivo.

---

## 28. Arquitetura Conceitual Inicial

Até o momento, o domínio pode ser dividido conceitualmente em cinco responsabilidades principais.

### 28.1 Historical Behavior Engine

Responsável por:

```text
Historical Demand
        ↓
Historical Average
        ↓
Customer Participation
```

### 28.2 Forecast Allocation Engine

Responsável por:

```text
Material Forecast
        +
Customer Participation
        ↓
Customer-Material Forecast
```

### 28.3 Rule Engine

Responsável por:

```text
Material
        ↓
Applicable Rule
        ↓
Upper Limit
Minimum Quantity
Other Parameters
        ↓
Effective Limit
```

### 28.4 Order Evaluation Engine

Responsável por:

```text
Incoming Order
        +
Running Total
        +
Effective Limit
        ↓
Normal / Excess
        ↓
Split when necessary
```

### 28.5 Allocation & Release Engine

Responsável por:

```text
On Hold Orders
        +
Material-level availability
        +
Fair Share
        +
Supply Chain Commitment
        +
Maximum Holding Time
        ↓
Release / Partial Release / Remain On Hold / Back Order
```

---

## 29. Fluxo Conceitual Completo

```text
HISTORICAL DEMAND
        │
        ▼
Customer-Material Historical Average
        │
        ▼
Customer Participation within Material
        │
        │
        ├────────────────────┐
        │                    │
        ▼                    │
MATERIAL FORECAST            │
        │                    │
        └────────┬───────────┘
                 ▼
      Customer-Material Forecast
                 │
                 ▼
            MATERIAL RULE
                 │
          ┌──────┴──────┐
          │             │
     Upper Limit    Minimum Quantity
          │             │
          └──────┬──────┘
                 ▼
          Effective Limit
                 │
                 ▼
           INCOMING ORDER
                 │
                 ▼
            Running Total
                 │
        ┌────────┴────────┐
        │                 │
   Within Limit       Above Limit
        │                 │
        ▼                 ▼
   Allocatable           Split
                          │
                 ┌────────┴────────┐
                 │                 │
              Allowed           Excess
                 │                 │
                 ▼                 ▼
            Allocatable         On Hold
                                   │
                                   ▼
                         Material Reassessment
                                   │
                     ┌─────────────┼─────────────┐
                     │             │             │
                  Release      Fair Share     Remain
                     │             │             │
                     └─────────────┴─────────────┘
                                   │
                                   ▼
                         Supply Chain Evaluation
                                   │
                     ┌─────────────┴─────────────┐
                     │                           │
               Supply Available           Deadline Reached
                     │                           │
                     ▼                           ▼
                  Release                     Release
                                                 │
                                           No Inventory
                                                 │
                                                 ▼
                                            Back Order
```

---

## 30. Princípios de Negócio Identificados

Até o momento, os seguintes princípios parecem fundamentais ao domínio:

### P01 — Equidade

O sistema deve proteger a disponibilidade do Material para toda a rede, evitando que um cliente consuma desproporcionalmente o estoque disponível.

### P02 — Comportamento histórico como referência

O comportamento histórico do cliente ajuda a estabelecer sua participação esperada na demanda futura.

### P03 — Forecast como referência futura

O forecast do Material representa a expectativa total futura e é distribuído entre clientes utilizando sua representatividade histórica.

### P04 — Parametrização

Parâmetros como janela histórica, tolerância e quantidade mínima não devem estar rigidamente incorporados à lógica.

### P05 — Controle acumulado

A avaliação deve considerar o acumulado dos pedidos dentro do período.

### P06 — Preservação da parte legítima

Um pedido parcialmente anormal não deve necessariamente ser integralmente retido.

A parcela dentro do limite continua atendível.

### P07 — Excesso não significa rejeição

Excesso significa necessidade de avaliação adicional.

### P08 — Decisão Cliente–Material e decisão Material são diferentes

A detecção ocorre no Cliente–Material.

A disponibilidade e redistribuição precisam também ser avaliadas no Material.

### P09 — Fair Share

Quando existe estoque insuficiente, a distribuição deve seguir regra objetiva e reproduzível.

### P10 — On Hold é transitório

Nenhum pedido deve permanecer indefinidamente em On Hold.

### P11 — Transparência para o cliente

Quando a cadeia não consegue atender dentro do prazo esperado, o pedido deve aparecer como Back Order em vez de permanecer artificialmente retido.

### P12 — Unicidade de regra

Um Material não pode possuir regras ativas conflitantes.

---

## 31. Pontos Ainda em Aberto

Os seguintes assuntos precisam ser detalhados antes do fechamento da arquitetura:

1. Fórmula definitiva da **Minimum Quantity** em conjunto com o limite percentual.
2. Método exato de **Fair Share**.
3. Momento/frequência das reavaliações além da virada mensal.
4. Definição formal do prazo máximo de On Hold.
5. Origem e governança do lead time.
6. Tratamento de alterações de forecast durante o período.
7. Versionamento do forecast utilizado pelo motor.
8. Comportamento quando a participação histórica total é zero.
9. Cliente novo sem histórico.
10. Material novo sem histórico.
11. Alteração de regra durante um período em andamento.
12. Data de vigência das regras.
13. Cancelamento/redução de pedidos que já consumiram limite.
14. Tratamento de devoluções.
15. Tipos especiais de pedido e seu efeito sobre consumo de limite.
16. Reprocessamento de pedidos.
17. Proteção contra dupla contabilização.
18. Precisão/arredondamento das quantidades calculadas.
19. Definição formal dos estados e transições.
20. Responsabilidade de cada domínio pela decisão e pelos dados.

---

## 32. Próxima Etapa de Arquitetura

Antes de discutir banco de dados, SAP, APIs, telas ou código, o próximo passo recomendado é construir formalmente:

```text
1. Domain Model
2. Business Rules Catalog
3. State Machine
4. Decision Flow
5. Entity Responsibilities
6. Domain Boundaries
7. Edge Cases
8. Invariants
```

Somente depois disso devem ser definidos:

```text
Application Architecture
        ↓
Data Architecture
        ↓
Integration Architecture
        ↓
Features
        ↓
Implementation
```

---

## 33. Status Atual

```text
Problem Definition             ██████████
Terminology                    ██████████
Historical Logic               ██████████
Forecast Distribution          ██████████
Rule Concept                   █████████░
Order Evaluation               █████████░
On Hold Concept                █████████░
Material Reassessment          ████████░░
Fair Share                     █████░░░░░
Supply Chain Commitment        ██████░░░░
State Machine                  █████░░░░░
Domain Model                   ████░░░░░░
Architecture Boundaries        ███░░░░░░░
Edge Cases                     ██░░░░░░░░
Technical Architecture         ░░░░░░░░░░
Implementation                 ░░░░░░░░░░
```

**O projeto ainda está deliberadamente na fase de arquitetura conceitual.**

Nenhuma decisão tecnológica deve dirigir o modelo de negócio nesta etapa.

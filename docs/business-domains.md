# Abnormal Order — Business Domains

## 1. Objetivo

O **Abnormal Order** tem como objetivo identificar pedidos com quantidades acima do comportamento esperado por combinação **Customer–PN (Part Number)**, classificar permanentemente a parcela anormal e, quando aplicável, segregá-la em **On Hold** para posterior liberação por mecanismos definidos.

A solução separa três conceitos fundamentais:

- **Normal** — quantidade dentro do limite mensal aplicável ao Customer–PN.
- **Abnormal** — classificação permanente atribuída à quantidade que excede o limite.
- **On Hold** — status operacional temporário utilizado para segregar uma quantidade Abnormal até sua liberação ou cancelamento.

> A liberação de uma quantidade não remove sua classificação histórica como **Abnormal**.

---

# 2. Visão de Domínios

A solução está organizada nos seguintes domínios:

1. **Demand & Forecast**
2. **Business Rules & Quota**
3. **Order Classification**
4. **On Hold Management**
5. **Release Management**
6. **Fulfillment & Back Order**
7. **Governance & Audit**

---

# 3. Demand & Forecast Domain

Responsável pela construção da base mensal utilizada para determinar a participação de cada cliente no forecast do PN.

## 3.1 Histórico de Demanda

Para cada combinação **Customer–PN**, utiliza-se uma janela móvel dos **6 meses imediatamente anteriores à competência**.

Regras:

- Todos os seis meses participam do cálculo.
- Meses sem pedidos são considerados com quantidade **zero**.
- Os zeros reduzem a média histórica da combinação.
- A janela é recalculada a cada nova competência.

### Média histórica

```text
Historical Average Customer–PN
= Soma das quantidades dos últimos 6 meses / 6
```

## 3.2 Representatividade do Cliente

Após calcular a média de cada Customer–PN, calcula-se a participação daquele cliente no total histórico do PN.

```text
Customer Representativeness (%)
=
Historical Average Customer–PN
/
Sum of Historical Averages for PN
```

Essa participação será utilizada para distribuir o forecast mensal do PN.

## 3.3 Customer–PN sem histórico

Quando uma combinação Customer–PN não possui histórico nos seis meses:

```text
Representativeness = 0%
```

O tratamento posterior será feito pela regra de **Minimum Quantity**.

A mesma lógica é aplicada a clientes novos.

## 3.4 Override manual

É permitido realizar ajuste manual da quota de um Customer–PN.

Exemplo de aplicação:

- mudança de código de cliente;
- necessidade de preservar uma referência histórica que não existe na nova chave;
- situações excepcionais conhecidas pelo negócio.

O override:

- vale somente para a competência específica;
- não exige rebalanceamento das quotas dos demais clientes;
- pode fazer com que a soma das quotas individuais seja diferente do forecast total do PN;
- na competência seguinte, o cálculo volta automaticamente à metodologia padrão, salvo novo override.

## 3.5 Forecast mensal

O forecast é recebido no nível:

```text
PN
```

O forecast do PN é distribuído aos clientes conforme a representatividade histórica.

```text
Forecast Customer–PN
=
Forecast PN × Customer Representativeness (%)
```

---

# 4. Business Rules & Quota Domain

Responsável pelas regras que determinam o limite mensal de cada Customer–PN.

## 4.1 Regra por PN

Cada PN pertence a uma regra contendo, no mínimo:

- **Upper Limit %**
- **Minimum Quantity**
- **Classification Flag**
- **Segregation Flag**

Um PN pertence a apenas uma regra aplicável.

## 4.2 Limite mensal Customer–PN

A quantidade mínima funciona como **piso do limite**, e não como tolerância adicional.

```text
Customer–PN Final Limit
=
MAX(
    Forecast Customer–PN × (1 + Upper Limit %),
    Minimum Quantity
)
```

### Exemplo 1

```text
Forecast Customer–PN = 100
Upper Limit = 20%
Minimum Quantity = 10

Final Limit = 120
```

### Exemplo 2

```text
Forecast Customer–PN = 3
Upper Limit = 20%
Minimum Quantity = 10

3 × 1.20 = 3.6

Final Limit = 10
```

### Exemplo 3

```text
Forecast Customer–PN = 0
Minimum Quantity = 10

Final Limit = 10
```

## 4.3 Classification e Segregation

Os flags são dependentes.

Estados válidos:

| Classification | Segregation | Comportamento |
|---|---|---|
| OFF | OFF | PN não participa da lógica Abnormal |
| ON | OFF | Quantidade pode ser classificada Abnormal, mas segue no fluxo normal |
| ON | ON | Quantidade Abnormal é segregada em On Hold |

Não existe conceitualmente:

```text
Classification OFF + Segregation ON
```

## 4.4 Alteração de regras durante a competência

As regras podem ser alteradas durante uma competência ativa.

A alteração, isoladamente, não modifica os cálculos existentes.

Para que tenha efeito, deve ser executado novamente o processo completo de cálculo:

1. histórico;
2. representatividade;
3. distribuição do forecast;
4. quota/limite.

Tecnicamente, o recálculo pode produzir reclassificações retroativas, embora isso **não faça parte do processo operacional esperado**.

---

# 5. Order Classification Domain

Responsável pela avaliação dos pedidos em tempo real.

## 5.1 Tipos de pedido

Existem dois tipos relevantes:

- **Regular**
- **Urgente**

### Regular

Participa normalmente da avaliação de Abnormal Order.

### Urgente

O pedido urgente:

- nunca é classificado como Abnormal;
- nunca é colocado em On Hold pela lógica de Abnormal Order;
- segue integralmente para o fluxo regular;
- sua quantidade **compõe o acumulado mensal Customer–PN**.

Portanto, um pedido urgente pode consumir o limite e fazer com que um pedido regular posterior seja classificado como Abnormal.

---

# 6. Acumulado mensal Customer–PN

A avaliação ocorre em tempo real para cada linha de pedido.

A chave conceitual é:

```text
Customer + PN + Competência
```

O sistema compara o acumulado mensal válido com o limite calculado para aquela combinação.

```text
Monthly Accumulated Quantity
vs.
Customer–PN Final Limit
```

A competência é independente das competências anteriores e posteriores.

Na virada do mês, a própria mudança de competência funciona como fechamento do período.

Não existe carry-over do limite ou acumulado para o mês seguinte.

---

# 7. Classificação Normal e Abnormal

Até o limite:

```text
NORMAL
```

Somente a quantidade acima do limite é:

```text
ABNORMAL
```

Atingir exatamente o limite não caracteriza anormalidade.

## 7.1 Exemplo

Limite:

```text
20
```

Acumulado antes do pedido:

```text
15
```

Novo pedido:

```text
10
```

Resultado:

```text
5 Normal
5 Abnormal
```

Conceitualmente:

```text
Normal Qty = MIN(Order Qty, Remaining Limit)

Abnormal Qty = Order Qty - Normal Qty
```

A referência entre as partes e a linha original do pedido é preservada pelo sistema.

---

# 8. Classificação permanente

Uma vez classificada como **Abnormal**, a quantidade mantém essa classificação permanentemente.

Eventos posteriores não removem a classificação, incluindo:

- liberação;
- atendimento;
- Back Order;
- cancelamento;
- alteração posterior do acumulado;
- liberação de capacidade causada pelo cancelamento de outros pedidos.

Portanto:

```text
Abnormal = atributo histórico permanente
On Hold = status operacional temporário
```

---

# 9. Cancelamentos

O cliente não pode alterar a quantidade de uma linha existente.

Também não existe cancelamento parcial da linha.

A linha é:

- mantida integralmente; ou
- cancelada integralmente.

Quando cancelada:

- sai da fila On Hold;
- deixa de participar dos mecanismos futuros de liberação;
- filtros do acumulado histórico passam a desconsiderar sua quantidade;
- sua classificação histórica Abnormal permanece.

Se o cancelamento ocorrer durante a competência corrente, a capacidade liberada pode ser utilizada por novos pedidos do mesmo Customer–PN.

Essa capacidade, entretanto, **não provoca reclassificação ou liberação automática de linhas Abnormal/On Hold já existentes**.

---

# 10. On Hold Management Domain

Quando:

```text
Classification = ON
Segregation = ON
```

a quantidade Abnormal é colocada em:

```text
ON HOLD
```

## 10.1 Carteira On Hold

As quantidades permanecem em carteira até ocorrer um evento de saída.

A gestão é feita por **PN**.

Para cada PN existe uma fila global reunindo:

- todos os clientes;
- todos os segmentos;
- todos os canais.

## 10.2 FIFO

A carteira utiliza FIFO.

A ordem é definida pela:

```text
Data/Hora original de entrada do pedido
```

Essa referência permanece durante todo o período On Hold.

Pedidos cancelados saem da fila.

Pedidos liberados também saem definitivamente da fila.

> **Nota de implementação (27/09/2026):** a revisão registrada em `index.html#review` (item 20.6) substituiu esta chave por **Nº da Ordem de Venda + Linha**, eliminando timestamp/timezone/empate. Ver `agent-context/features.md`.

---

# 11. Release Management Domain

Existem quatro mecanismos principais de liberação:

1. **Monthly Reconciliation**
2. **Inventory Release**
3. **Manual Release**
4. **Planning Lead Time Release**

Uma vez liberada, a linha:

```text
não retorna ao On Hold
```

A liberação é definitiva.

A classificação Abnormal permanece.

---

# 12. Monthly Reconciliation

A reconciliação ocorre após o fechamento da competência.

O processo é realizado **fora do sistema**.

O sistema recebe posteriormente o resultado por:

```text
Upload
```

O arquivo já contém os pedidos/linhas e quantidades definidos para liberação.

O FIFO é resolvido no processo externo.

## 12.1 Escopo

A reconciliação ocorre por PN.

Todos os clientes daquele PN participam de uma única avaliação.

Não existe separação por:

- cliente;
- segmento;
- canal.

## 12.2 Limite global do PN

Na reconciliação, a referência global é:

```text
Global PN Ceiling
=
PN Forecast × (1 + Upper Limit %)
```

A **Minimum Quantity não participa** desse cálculo global.

Pedidos urgentes fazem parte do consumo total do PN para essa análise.

## 12.3 Resultado

Uma linha aprovada pela reconciliação:

- sai imediatamente do On Hold;
- não depende de uma segunda aprovação do Inventory;
- mantém a classificação Abnormal;
- segue para o fluxo logístico regular.

A reconciliação de uma competência é realizada uma única vez.

As quantidades que permanecerem On Hold continuam disponíveis para os demais mecanismos de liberação.

---

# 13. Inventory Release

O Inventory possui autorização para liberar pedidos On Hold conforme **estratégia própria**.

Não é necessário representar no domínio do Abnormal Order a lógica interna utilizada pelo Inventory para tomar essa decisão.

A avaliação respeita:

- gestão por PN;
- fila FIFO;
- data/hora original do pedido.

Se a primeira linha da fila não estiver apta à liberação segundo a estratégia do Inventory, a fila aguarda.

Não se pula a primeira linha para atender uma linha posterior.

A carteira On Hold é reavaliada mensalmente pelo Inventory, inclusive para pedidos originados em competências anteriores.

A liberação pode ser refletida no sistema por upload utilizando o mesmo modelo de arquivo utilizado pelos demais processos de carga de liberação.

---

# 14. Manual Release

Usuários autorizados podem realizar liberação manual.

A liberação pode ocorrer:

- diretamente no pedido; ou
- por upload.

A decisão manual não depende das regras de:

- quota;
- forecast;
- Upper Limit;
- disponibilidade de estoque.

Uma vez executada:

```text
On Hold → Released
```

A transição é definitiva.

---

# 15. Planning Lead Time Release

Cada PN possui seu próprio:

```text
Planning Lead Time
```

O Lead Time pode variar entre PNs.

## 15.1 Início da contagem

A contagem não começa na data do pedido.

Começa no:

```text
1º dia do mês seguinte à competência original
```

Exemplo:

```text
Pedido: 17/05
Competência: Maio
Planning LT: 120 dias

Início da contagem: 01/06
```

## 15.2 Lead Time vigente

O sistema utiliza o **Planning Lead Time vigente** do PN.

Não é congelado o Lead Time existente na data original do pedido.

Se o LT for alterado enquanto uma linha está On Hold, a nova regra passa a valer.

Exemplo:

```text
LT anterior = 120 dias
LT novo = 60 dias
Tempo já transcorrido = 80 dias
```

A condição já foi atingida e a linha deve ser liberada quando o sistema detectar a situação.

A condição é verificada diariamente.

> **Nota de implementação (27/09/2026):** a revisão registrada em `index.html#review` (item 20.3) ajustou essa cadência para **mensal**. Ver `agent-context/features.md`.

## 15.3 Liberação

Quando o Planning Lead Time é atingido:

- a linha sai automaticamente do On Hold;
- a liberação independe da disponibilidade de estoque;
- a classificação Abnormal permanece;
- a linha segue para o fluxo regular.

Se não houver disponibilidade para atendimento, o saldo poderá seguir como Back Order.

---

# 16. Fulfillment & Back Order Domain

Toda quantidade liberada do On Hold retorna ao:

```text
Fluxo Logístico Regular
```

Não existe, no escopo atual, prioridade logística especial para pedidos anteriormente classificados como Abnormal.

O fluxo pode incluir:

1. alocação;
2. separação;
3. expedição;
4. entrega.

Quando não houver condição para atendimento após a liberação, o saldo segue pelas regras normais do processo e pode permanecer como:

```text
Back Order
```

Back Order e On Hold são conceitos diferentes:

```text
On Hold
= bloqueio causado pela lógica Abnormal Order

Back Order
= demanda liberada no fluxo regular, porém ainda não atendida
```

---

# 17. Governance & Audit Domain

O domínio de governança controla permissões e rastreabilidade.

## 17.1 Permissões

Uma tabela de permissões define quais usuários podem executar ações como:

- ajustes;
- uploads;
- liberações;
- ações manuais.

## 17.2 Auditoria

O sistema mantém rastreabilidade das alterações relevantes.

Exemplo:

- override manual de quota;
- usuário responsável;
- data/hora;
- valor original;
- valor alterado.

## 17.3 Uploads

O mesmo modelo de arquivo pode ser utilizado para refletir liberações no sistema.

O arquivo não precisa identificar o motivo da liberação.

A lógica que decidiu a liberação ocorre antes do upload.

---

# 18. Estados conceituais

O ciclo simplificado de uma quantidade pode ser representado como:

```text
ORDER
  |
  +--> NORMAL --------------------------> REGULAR FULFILLMENT
  |
  +--> ABNORMAL
          |
          +--> Segregation OFF ---------> REGULAR FULFILLMENT
          |
          +--> Segregation ON
                    |
                    v
                 ON HOLD
                    |
          +---------+----------+----------------+----------------+
          |                    |                |                |
          v                    v                v                v
   Reconciliation       Inventory Release   Manual Release   Planning LT
          |                    |                |                |
          +--------------------+----------------+----------------+
                               |
                               v
                           RELEASED
                               |
                               v
                     REGULAR FULFILLMENT
                               |
                     +---------+---------+
                     |                   |
                     v                   v
                  ATTENDED           BACK ORDER
```

Cancelamento é uma saída independente:

```text
ON HOLD → CANCELLED
```

A classificação histórica:

```text
ABNORMAL
```

permanece mesmo após:

```text
Released
Attended
Back Order
Cancelled
```

---

# 19. Princípios centrais da solução

1. **A competência mensal é independente.**
2. **O histórico de seis meses determina a representatividade Customer–PN.**
3. **O forecast nasce no PN e é distribuído aos clientes.**
4. **Minimum Quantity é piso do limite, não tolerância adicional.**
5. **Pedido urgente nunca é Abnormal, mas consome o acumulado.**
6. **Somente o excedente ao limite é Abnormal.**
7. **Abnormal é uma classificação permanente.**
8. **On Hold é um estado operacional temporário.**
9. **A carteira On Hold é administrada por PN em FIFO global.**
10. **A reconciliação é calculada externamente e refletida via upload.**
11. **Inventory possui autonomia de liberação conforme estratégia própria.**
12. **Manual Release e Planning Lead Time podem liberar independentemente de estoque.**
13. **Toda liberação do On Hold é definitiva.**
14. **Após liberação, o pedido volta ao fluxo logístico regular.**
15. **Back Order não é On Hold.**
16. **Cancelamentos deixam de consumir o acumulado, mas não apagam a classificação histórica Abnormal.**

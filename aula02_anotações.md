# Aula 02 — Anotações

**Disciplina:** Análise de Dados e Tomada de Decisões com BI e BA (IBM3705 / 15306207, turma 3001, 2026.2B)
**Curso:** MBA em IA, Big Data e Data Science para Negócios, IBMEC
**Data da aula:** 17/11/2026

> **Sobre este arquivo.** São anotações pessoais de estudo, escritas com as minhas palavras a partir do que foi apresentado em aula e da atividade prática. Não reproduzem os slides, o roteiro, as bases de dados nem os gabaritos da disciplina, que pertencem ao professor e à instituição. Os exemplos numéricos abaixo são inventados por mim, só para ilustrar as fórmulas.

## Sumário

1. [Métricas e frameworks](#1-métricas-e-frameworks)
2. [Unit economics](#2-unit-economics)
3. [Usuários ativos e engajamento](#3-usuários-ativos-e-engajamento)
4. [Ativação e retenção](#4-ativação-e-retenção)
5. [Mapa dos frameworks](#5-mapa-dos-frameworks)
6. [Atividade prática: primeiro notebook com SQL](#6-atividade-prática-primeiro-notebook-com-sql)
7. [Glossário](#7-glossário)

---

## 1. Métricas e frameworks

### North Star e árvore de métricas

- A **North Star Metric** é a métrica que melhor resume o valor que o produto entrega ao cliente e que, ao crescer, indica crescimento saudável do negócio.
- A **árvore de métricas** decompõe a North Star em níveis, por **multiplicação** ou **soma**, até chegar às **folhas**.
- Regras de uma boa árvore:
  1. Cada nível explica o de cima por multiplicação ou soma.
  2. As folhas são métricas de entrada (input) que um time consegue mover em poucas semanas, e cada folha tem um dono.
  3. Ao lado da árvore fica uma **métrica de guarda** (por exemplo, churn ou atraso na entrega), que não pode piorar enquanto se otimiza o resto.
- Exemplo de decomposição em um e-commerce: `receita = clientes × pedidos por cliente × ticket médio`. Os clientes podem ser separados em novos + recorrentes (marketing e CRM como donos), e o ticket médio em receita de produtos + frete.
- Exemplo em assinatura (SaaS): `MRR = contas pagantes × ARPA`.

### AARRR (Pirate Metrics)

Divide a jornada do cliente em cinco etapas:

| Etapa | Pergunta | Exemplos de métrica |
|---|---|---|
| **A**cquisition (aquisição) | Como chegam? | visitas, cadastros, custo por lead |
| **A**ctivation (ativação) | Têm a primeira boa experiência? | onboarding concluído, uso da função principal |
| **R**etention (retenção) | Voltam? | churn, usuários ativos semanais |
| **R**evenue (receita) | Pagam? | conversão em plano pago, MRR, ARPA |
| **R**eferral (indicação) | Trazem outros? | convites enviados, NPS |

### HEART

Framework criado por pesquisadores do Google (Rodden, Hutchinson e Fu, 2010) para medir a **experiência do produto**, útil quando a receita ainda não diz muita coisa. Aplica-se em três passos: **Goals → Signals → Metrics** (objetivo, sinal no comportamento, métrica normalizada por usuário).

| Categoria | O que mede | Exemplo de métrica |
|---|---|---|
| **H**appiness | satisfação, atitude | NPS |
| **E**ngagement | intensidade de uso | dias ativos por mês por usuário |
| **A**doption | novos usuários de um produto ou recurso | contas que usam o recurso pela primeira vez |
| **R**etention | quem continua ativo | % de ativos no mês que seguem ativos no seguinte |
| **T**ask success | eficiência, eficácia, erros | taxa de conclusão de tarefa (só dá para medir se o dado existir) |

### OKR × KPI

| | OKR | KPI |
|---|---|---|
| **O que é** | um objetivo qualitativo e poucos resultados-chave mensuráveis | mede a saúde contínua do negócio, com dono permanente |
| **Horizonte** | com prazo, normalmente trimestral | contínuo, com faixa aceitável |

Ideia central: o OKR é o que quero mudar neste trimestre; o KPI é o que preciso proteger enquanto mudo. Os KPIs cumprem o papel da métrica de guarda da árvore.

---

## 2. Unit economics

**Unit economics** é a linguagem comum entre produto, marketing e finanças. Escolhe-se a unidade de análise (cliente, pedido, assento ou loja) e compara-se a receita com os custos variáveis dela:

```
margem de contribuição por unidade = receita por unidade − custos variáveis por unidade
```

### Fórmulas principais

```
MRR   = soma das receitas recorrentes mensais
ARPA  = MRR ÷ contas pagantes
CAC   = investimento de aquisição ÷ novos clientes (no mesmo período)
LTV   = ARPA × margem bruta ÷ churn mensal
Payback do CAC = CAC ÷ (ARPA × margem bruta)     → em meses
```

- **LTV/CAC**: quanto maior, melhor. Uma regra prática muito citada é ≥ 3.
- Sem a margem bruta, só se consegue calcular o **teto** do LTV (LTV de receita), não o LTV de fato.

### CAC: o que entra na conta

O CAC só significa algo quando se diz **quais custos** e **quais clientes** entraram.

- **CAC blended:** todo o investimento dividido por todos os novos clientes, inclusive os orgânicos.
- **CAC do canal pago:** só o investimento do canal pago dividido pelos clientes que ele trouxe. Tende a ser bem maior que o blended, porque os orgânicos baixam a média.
- Armadilhas comuns: incluir ou não o salário do time comercial; o gasto de um mês converter só no mês seguinte (defasagem); quem leva o crédito pela venda (atribuição).

*Exemplo inventado:* R$ 50.000 investidos e 250 clientes novos dão CAC de R$ 200. Se só 100 desses clientes vieram do canal pago, que custou R$ 40.000, o CAC do canal pago é R$ 400.

---

## 3. Usuários ativos e engajamento

- **DAU, WAU e MAU:** usuários distintos ativos no dia, nos últimos 7 dias e no mês. Cada pessoa conta uma vez por período, então DAU ≤ WAU ≤ MAU.
- **"Ativo" não tem definição única.** Cada empresa escolhe a janela de tempo e a ação exigida (por exemplo, "fez ao menos uma viagem no mês" ou "gerou receita nos últimos 30 dias"). Empresas de capital aberto costumam declarar a definição nos relatórios. Comparar "ativos" de duas empresas, ou de duas áreas, sem ler a definição é comparar coisas diferentes.
- **Stickiness:** `DAU médio do mês ÷ MAU do mês`. Quanto mais perto de 1, mais os usuários voltam todo dia.
- **Curva L28:** histograma que mostra, para cada N de 1 a 28, quantos usuários usaram o produto em exatamente N dos últimos 28 dias. Mostra a frequência de uso que o MAU esconde, pois o MAU conta quem apareceu uma única vez no mês. Dá para ler a mediana de dias de uso e a fatia dos usuários muito frequentes (por exemplo, 20 dias ou mais).

---

## 4. Ativação e retenção

### Ativação

- **Ativação** é o primeiro momento em que o cliente percebe o valor do produto (o "aha"). **Time-to-value** é o tempo até chegar lá.
- Terminar o cadastro não é ativação.
- Como escolher o evento de ativação: testar vários candidatos e ficar com o que **mais separa quem fica de quem sai** (por exemplo, o que gera a maior diferença de conversão entre contas que o fizeram e contas que não o fizeram, em um prazo definido).

### Coortes

- **Coorte:** grupo de clientes que começou no mesmo período (por exemplo, quem assinou em um mesmo mês).
- Na tabela de retenção por coorte, cada linha é uma coorte e cada coluna é o tempo desde a entrada (m0, m1, m2, m3, m6, m12). Cada célula é a % de contas ainda pagantes.
- Três leituras:
  1. **Linha:** como uma coorte envelhece.
  2. **Coluna:** as coortes novas retêm melhor que as antigas?
  3. **Diagonal:** um evento do calendário que atinge todas as coortes ao mesmo tempo (uma queda na mesma diagonal sugere causa externa, como mudança de preço ou falha, e não problema de uma coorte).

### Curva de retenção

- Uma curva que **se achata** indica que existe um núcleo de clientes que precisa do produto e não sai mais.
- Se a curva desacelera mas segue caindo de forma constante, ainda não há núcleo estável.
- **Churn de contas × churn de receita:** andam juntos quando quem sai paga na média; se separam quando quem sai paga mais ou menos que a média.
- **Reativação:** cliente que volta conta como receita, não como cliente novo (para não inflar CAC nem a contagem de novos).

---

## 5. Mapa dos frameworks

Cada framework responde a uma pergunta diferente:

| Framework | Pergunta | Onde mais se usa |
|---|---|---|
| North Star e árvore | O que move o valor? | todos os modelos |
| AARRR | Onde o funil vaza? | startups e negócios digitais |
| HEART | A experiência do produto melhorou? | times de produto |
| OKR × KPI | O que vamos mudar neste trimestre, sem quebrar o resto? | gestão de times |
| Unit economics | Cada cliente dá lucro? | finanças e conselho |

Não existe o melhor framework: eles se complementam.

---

## 6. Atividade prática: primeiro notebook com SQL

> A atividade prática desta seção corresponde ao roteiro "Aula 2" da disciplina (notebook `aula02_primeiro_notebook`). Registro aqui apenas o que aprendi e os resultados que obtive, sem copiar o roteiro nem as consultas fornecidas.

### Objetivo

Montar um notebook em uma plataforma de dados em nuvem (Databricks, edição gratuita) e responder, com SQL e gráficos, a uma pergunta de negócio sobre pedidos de uma base de exemplo de varejo atacadista (`samples.tpch`, dados sintéticos de benchmark que acompanham o ambiente): *como a receita evoluiu, quais segmentos de clientes a sustentam e se o valor médio do pedido representa bem os pedidos?*

### O que foi feito

1. **Conta e ambiente:** criação do workspace, ajuste da interface para inglês, tour pelas áreas principais (Catalog, SQL Editor, Workspace, Dashboards, Genie Agents e SQL Warehouses). Nomes de objetos seguem o padrão `catálogo.schema.tabela`.
2. **Notebook:** criado com linguagem SQL, conectado à computação serverless. Células de texto em Markdown (`%md`) para registrar a pergunta e a conclusão; células de código com `%sql`.
3. **Consultas e gráficos:**
   - receita e número de pedidos **por mês** (gráfico de linhas);
   - verificação da **janela incompleta** do último mês;
   - receita, pedidos e ticket médio **por segmento de cliente**, com junção entre pedidos e clientes (gráfico de barras);
   - **média × mediana** e mínimo/máximo do valor do pedido;
   - **histograma** do valor do pedido, agrupando em faixas de 25.000 dentro do próprio SQL (gráfico de barras).

### Resultados e leituras

- **Receita mensal:** praticamente estável de 1992 a meados de 1998, em torno de R$ 13 a 15 bilhões por mês (a base não define moeda), com uma queda recorrente no início de cada ano.
- **Último mês (ago/1998):** a queda brusca é só **janela incompleta**. A base termina em 02/08/1998, então o mês tem apenas 1 dia com pedido. A receita por dia desse dia fica compatível com a dos meses anteriores. Lição: antes de concluir que a receita "despencou", conferir quantos dias de dados o período tem.
- **Segmentos:** cinco segmentos (AUTOMOBILE, BUILDING, FURNITURE, HOUSEHOLD, MACHINERY) com receita muito parecida.
- **Ticket médio:** média de cerca de 151 mil contra mediana de cerca de 144 mil; mínimo de aproximadamente 836 e máximo de aproximadamente 569 mil. Média e mediana próximas e histograma amplo, com concentração entre 50 mil e 200 mil e cauda longa à direita. Conclusão: o ticket médio representa razoavelmente o pedido típico, mas esconde uma dispersão grande.

### Aprendizados técnicos

- Agrupar e agregar no SQL (`GROUP BY`, `date_trunc`, `FLOOR`) antes de visualizar, para o gráfico usar todos os dados e não só as linhas exibidas.
- Usar **mediana** além da **média** para checar assimetria.
- Medir "por dia" quando o período comparado tem tamanhos diferentes.
- `percentile_approx` calcula uma mediana aproximada; `COUNT(DISTINCT ...)` conta valores únicos.
- Se a cota diária da edição gratuita acabar, os notebooks salvos não se perdem: basta retomar no dia seguinte.

---

## 7. Glossário

| Termo | Significado |
|---|---|
| **ARPA** | Average Revenue Per Account, receita média por conta paga |
| **CAC** | Customer Acquisition Cost, custo de aquisição de um cliente |
| **Churn** | taxa de clientes (ou de receita) perdidos em um período |
| **Coorte** | grupo de clientes que começou no mesmo período |
| **DAU / WAU / MAU** | usuários ativos por dia / semana / mês |
| **GMV** | Gross Merchandise Value, valor bruto de mercadorias vendidas |
| **KPI** | Key Performance Indicator, indicador contínuo de saúde do negócio |
| **LTV** | Lifetime Value, valor gerado por um cliente durante o relacionamento |
| **MRR** | Monthly Recurring Revenue, receita recorrente mensal |
| **NPS** | Net Promoter Score, medida de satisfação e disposição a recomendar |
| **North Star** | métrica principal que resume o valor entregue ao cliente |
| **OKR** | Objectives and Key Results, objetivo qualitativo com resultados-chave mensuráveis |
| **Payback do CAC** | tempo, em meses, para recuperar o custo de aquisição |
| **Stickiness** | DAU médio ÷ MAU, mede a frequência de retorno dos usuários |
| **Time-to-value** | tempo até o cliente perceber o valor do produto |

---

*Anotações de estudo. Distribuídas sob a licença MIT, conforme o arquivo `LICENSE` do repositório.*

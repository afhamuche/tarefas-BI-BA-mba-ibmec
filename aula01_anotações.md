# Aula 01 — Anotações: fundamentos de BI e BA, de dados a decisões

**Disciplina:** Análise de Dados e Tomada de Decisões com BI e BA (IBM3705 / 15306207, turma 3001, 2026.2B)
**Curso:** MBA em IA, Big Data e Data Science para Negócios, IBMEC

> **Sobre este arquivo.** São anotações pessoais de estudo, escritas com as minhas palavras a partir da aula. Não reproduzem os slides, o caso didático, as imagens nem os materiais da disciplina, que pertencem ao professor e à instituição. Os exemplos numéricos são inventados por mim, só para ilustrar. As referências no final são as obras públicas citadas em aula.

## Sumário

1. [Visão geral da disciplina](#1-visão-geral-da-disciplina)
2. [BI, BA e Data Science](#2-bi-ba-e-data-science)
3. [Da pergunta à decisão](#3-da-pergunta-à-decisão)
4. [O que torna um indicador útil](#4-o-que-torna-um-indicador-útil)
5. [Plataforma analítica e papéis](#5-plataforma-analítica-e-papéis)
6. [Armadilhas que derrubam boas análises](#6-armadilhas-que-derrubam-boas-análises)
7. [Casos de empresas](#7-casos-de-empresas)
8. [Ambiente de trabalho: Databricks Free Edition](#8-ambiente-de-trabalho-databricks-free-edition)
9. [Glossário](#9-glossário)
10. [Referências citadas em aula](#10-referências-citadas-em-aula)

---

## 1. Visão geral da disciplina

- O objetivo é levar uma **pergunta de negócio** até uma **decisão sustentada por métricas confiáveis**.
- Três frentes de aprendizado:
  - **Negócio:** formular a pergunta que importa para uma decisão, testar hipóteses e defender uma recomendação.
  - **Métricas:** desenhar a árvore de métricas e escrever cada indicador com definição única, dono, meta e métrica de guarda.
  - **Técnica:** construir a camada de dados de métricas, o dashboard e o analytics conversacional em SQL, e mapear os conceitos para Power BI, Looker e Tableau.
- Fio condutor das seis aulas: pergunta → árvore de métricas → camada de métricas → dashboard → analytics conversacional → recomendação final apresentada a um "CEO".

### O caso que abre a aula

Um caso fictício de uma rede de varejo mostra o problema típico: **muitos painéis, nenhuma decisão boa**. Três diretorias chegaram a números diferentes para "vendas" de uma mesma loja, porque cada uma usava uma fórmula distinta (incluir ou não cancelados, receita bruta ou líquida, só caixa ou também o canal digital). A decisão acabou sendo tomada por intuição. A lição do caso: faltavam **pergunta**, **definição única**, **dono** e **métrica de guarda**, e não mais dados.

---

## 2. BI, BA e Data Science

| | Pergunta central | Entrega típica | Exemplo no varejo |
|---|---|---|---|
| **BI** (Business Intelligence) | O que aconteceu? | relatório, painel, alerta | vendas por loja na semana |
| **BA** (Business Analytics) | Por que aconteceu e o que fazer? | análise, recomendação | por que uma loja perdeu margem |
| **Data Science** | O que vai acontecer? | modelo em produção | previsão de demanda por SKU |

As fronteiras entre os três são borradas. Davenport e Harris (2007) descrevem *analytics* como o uso intensivo de dados, análise estatística e quantitativa, modelos explicativos e preditivos e gestão baseada em fatos para orientar decisões e ações.

### Os quatro tipos de analytics

Distinguem-se pela **pergunta** que respondem, e não são degraus obrigatórios.

- **Descritiva:** o que aconteceu? (ex.: o GMV de um mês caiu 8% frente ao anterior).
- **Diagnóstica:** por que aconteceu? (ex.: a queda veio de duas categorias e de um estado).
- **Preditiva:** o que vai acontecer? (ex.: com a sazonalidade, o mês seguinte repete a queda).
- **Prescritiva:** o que devemos fazer? (ex.: realocar verba de mídia para as categorias que respondem ao frete grátis).

O modelo em escada (Gartner) sugere subir um degrau de cada vez. Na prática, uma empresa pode precisar de prescritiva em um processo e nem ter descritiva confiável em outro. Prescritiva em cima de descritiva errada **automatiza o erro**.

### Onde cada assunto é aprofundado no MBA

- Esta disciplina: métrica, camada de métricas, dashboard e decisão.
- Estatística aplicada: testes estatísticos e análise exploratória.
- Preparação e manipulação de dados: modelagem dimensional, bancos, ETL/ELT.
- Séries temporais: previsão.
- Machine Learning para negócios: algoritmos de ML.
- IA generativa e engenharia de prompt: LLMs.
- Governança de dados e leis de proteção de dados (LGPD).

### Evolução da interface do BI

1. **1958:** Hans Peter Luhn, da IBM, descreve um sistema de inteligência de negócios para levar a informação certa aos pontos de ação da empresa.
2. **Relatórios e data warehouse:** a TI produz relatórios; o negócio pede e espera.
3. **BI self-service (marco de 2015, com o Power BI em disponibilidade geral):** o analista de negócio monta o próprio painel.
4. **Modern data stack:** armazenamento em nuvem, transformação em SQL versionado, camada semântica.
5. **AI/BI e analytics conversacional (2024, com o lançamento do Databricks AI/BI, incluindo Dashboards e Genie):** perguntas em linguagem natural.

**O que mudou:** quem consulta (de TI para qualquer gestor), o tempo até a resposta (de semanas para segundos), a interface (de relatório para conversa) e o custo de criar um painel novo.
**O que não mudou:** a métrica precisa de definição única, a métrica precisa de dono, a pergunta vem antes do dado e o custo de decidir com o número errado continua alto. Analytics conversacional **sem definição única** devolve respostas diferentes para a mesma pergunta.

---

## 3. Da pergunta à decisão

### O ciclo da decisão orientada a dados

Pergunta → hipótese → métrica → dado → análise → decisão → ação → medição → (nova pergunta). O ciclo só fecha quando a **ação é medida**. É nesse ponto que a maioria dos ciclos se rompe (opinião do professor).

### Tipo de decisão define o formato da entrega

| Nível | Exemplos | Frequência | Formato adequado |
|---|---|---|---|
| Operacional | repor gôndola, escalar entregadores | diária ou horária | painel operacional, alerta |
| Tático | mix de categorias, verba de mídia por canal | semanal ou mensal | dashboard analítico |
| Estratégico | fechar lojas, abrir centro de distribuição | rara | análise dedicada e narrativa |

Decisões recorrentes pedem dashboard; decisões únicas pedem análise.

### Latências entre o evento e a ação

O tempo entre o dado disponível e a ação tomada é uma métrica do próprio BI. Três latências se somam (Hackathorn):

1. **Latência de dados:** capturar e disponibilizar o dado.
2. **Latência de análise:** transformar o dado em informação entregue a quem decide.
3. **Latência de decisão:** quem recebe a informação decidir e agir.

Dado em tempo real reduz só a primeira. A latência de decisão tende a ser o fator limitante.

### Pergunta de negócio × pergunta analítica

- **Pergunta de negócio:** é sobre uma **decisão** (ex.: "vale manter frete grátis acima de certo valor?").
- **Pergunta analítica:** é sobre o **número** que informa a decisão (ex.: quanto o pedido médio sobe com o frete grátis? qual o custo de frete por pedido nessa faixa?).

### Árvore de problemas

Decompõe a pergunta em ramos que **não se sobrepõem e não deixam lacuna** (princípio MECE, em linha com Minto e com Conn e McLean). Exemplo: "por que a margem de contribuição do app caiu no trimestre?" pode abrir em *receita por pedido caiu?* (ticket médio, mix migrou para categorias de menor margem, mais descontos e cupons) e *custo por pedido subiu?* (separação, entrega, perdas e devoluções).

### Hipótese boa × hipótese fraca

Uma boa hipótese **pode ser refutada** e aponta para uma métrica e um dado.

- Fraca: "o app está caro de operar."
- Boa: "o custo de entrega por pedido subiu mais de 10% no trimestre nas cidades onde fechamos lojas."

Checagem em três itens: que resultado me faria abandonar a hipótese? Qual métrica a testa? Onde está o dado?

### Vale a pena fazer a análise?

Teste antes de pedir qualquer análise: **"o que faríamos diferente dependendo da resposta?"** Se a resposta A leva a X e a resposta B leva a Y, a análise vale. Se faríamos X de qualquer jeito, é curiosidade. Isso se liga à teoria do valor da informação (Howard, 1966): a informação vale o quanto melhora a decisão, e o teto é o que se pagaria para saber a resposta com certeza.

---

## 4. O que torna um indicador útil

### Quatro critérios

| Critério | Pergunta de teste | Falha típica |
|---|---|---|
| **Definido** | duas pessoas calculam e chegam ao mesmo número? | "vendas" com várias fórmulas |
| **Acionável** | se piorar, alguém sabe o que fazer? | índice composto que ninguém decompõe |
| **Comparável** | dá para comparar com meta, período anterior e pares? | mudança de definição sem preservar o histórico |
| **Auditável** | dá para chegar ao registro que gerou o número? | planilha manual sem origem |

### Leading × lagging

- **Lagging (de resultado):** confirma o que já aconteceu (receita do mês, churn, nota média de avaliação).
- **Leading (antecedente):** permite agir antes (fluxo de visitas, conversão, queda de uso nas últimas semanas, entregas fora do prazo, ruptura de estoque).

### Input × output × outcome

- **Input:** o que o time controla (sortimento, preço, prazo prometido).
- **Output:** o que o time entrega (pedidos, conversão, entregas no prazo).
- **Outcome:** o que o negócio colhe (receita, margem, recompra).

Na Amazon, segundo Bryar e Carr (2021), a reunião semanal de negócios separa métricas de input controláveis das de output, e a liderança cobra principalmente as de input, porque são as que o time consegue mover.

### Vanity metrics

Uma *vanity metric* cresce sozinha e não muda nenhuma decisão (termo de Eric Ries). O contraponto é a **métrica acionável**, que mostra causa e efeito.

| Vaidade | Acionável correspondente |
|---|---|
| downloads do app | % de quem baixou e fez o primeiro pedido em 7 dias |
| seguidores da marca | vendas atribuídas a campanhas nas redes |
| visitas ao site | conversão por canal de origem |
| cadastros acumulados | clientes ativos no mês |

### Métrica de guarda

Toda meta precisa de uma métrica de guarda que impeça melhorá-la **às custas de outra coisa**.

| Meta | Métrica de guarda |
|---|---|
| reduzir o tempo médio de atendimento | taxa de resolução no primeiro contato |
| aumentar a conversão do site | taxa de devolução e de cancelamento |
| reduzir o custo de frete | entregas no prazo e nota de avaliação |
| fechar lojas de baixo desempenho | prazo de entrega do app na região |

### North Star

- A **North Star Metric** resume o valor entregue ao cliente e se desdobra em *drivers* que os times movem. Pelo framework da Amplitude, é uma única métrica acompanhada de um pequeno conjunto de inputs.
- Exemplo para um marketplace: North Star = pedidos entregues no prazo e bem avaliados por mês. Drivers: compradores ativos, frequência de compra, % entregue no prazo, % avaliado com 4 ou 5. Inputs: sortimento, preço, prazo prometido, qualidade dos vendedores.

---

## 5. Plataforma analítica e papéis

### Cadeia de camadas

fontes → ingestão → **bronze** → **silver** → **gold** → **camada de métricas** → consumo (dashboards, planilhas, IA conversacional)

- **Bronze:** dado cru, como chegou.
- **Silver:** limpo, validado e sem duplicidade.
- **Gold:** modelado e agregado para o negócio.
- **Camada de métricas:** a fórmula de cada indicador, **definida uma única vez**. É onde a definição "mora".

### Quatro papéis

| Papel | Responde por |
|---|---|
| Engenharia de dados | o dado chegar completo e no prazo |
| Analytics engineering | transformar o dado em tabelas e métricas testadas |
| Analista de negócio | responder perguntas e recomendar decisões |
| **Dono da métrica** | a definição, a meta e a ação quando o número muda |

O dono da métrica **não é técnico**: é quem responde pelo negócio.

### Mesmos conceitos, nomes diferentes por ferramenta

| Conceito | Databricks | Power BI | Looker | Tableau |
|---|---|---|---|---|
| Camada de métricas | metric views (Unity Catalog) | modelo semântico e medidas DAX | LookML | Tableau Semantics |
| Dashboard | AI/BI Dashboards | relatório e dashboard | dashboards | dashboards (e Pulse para KPIs) |
| Pergunta em linguagem natural | Genie | Copilot | Conversational Analytics | Tableau Agent |

---

## 6. Armadilhas que derrubam boas análises

1. **Quando a métrica vira meta, deixa de medir bem.** É a ideia popularizada como Lei de Goodhart (na formulação de Marilyn Strathern, 1997).
2. **Metas mal desenhadas geram comportamento perverso.** No Wells Fargo, metas de venda cruzada com bônus levaram funcionários a abrir contas e cartões que os clientes talvez não tivessem autorizado (mais de 2 milhões de contas, segundo as fontes citadas). O banco foi multado em 2016 (cerca de US$ 185 milhões) e encerrou as metas de venda de produtos no varejo.
3. **Métrica sem dono diverge em silêncio.** Cada área ajusta a fórmula ao próprio uso e a divergência só aparece na reunião em que a decisão precisa ser tomada. O Airbnb reportou que perguntas simples do CEO recebiam respostas diferentes de áreas diferentes, e respondeu criando o **Minerva**, plataforma de métricas com definição e dono únicos.
4. **Correlação no dashboard não autoriza decisão causal.** "Lojas com mais promotores vendem mais" pode ser causa direta, causa reversa (lojas que já vendem mais recebem mais promotores) ou causa comum (o fluxo de clientes explica os dois). Para afirmar causa é preciso um desenho que a isole: experimento controlado (teste A/B) ou método quase-experimental.
5. **A média pode descrever um cliente que não existe.** Duas lojas com o mesmo ticket médio podem ter perfis totalmente diferentes. Antes de decidir com uma média, olhar também a **mediana** e o **histograma**.
   - Exemplo inventado: pedidos de R$ 20, 30, 40, 50 e 360. A média é (20+30+40+50+360) ÷ 5 = R$ 100; a mediana é R$ 40. Um único pedido grande puxa a média, e a mediana fica mais perto do pedido típico.
6. **Analisar só quem ficou esconde por que os outros saíram (viés de sobrevivência).**
   - Pesquisa de satisfação só com clientes ativos não ouve quem já foi embora.
   - Comparar só as lojas abertas hoje esconde o que as fechadas tinham em comum.
   - Origem da ideia: na Segunda Guerra, Abraham Wald analisou os danos de aviões que voltavam das missões. A lição atribuída a ele é que os aviões atingidos nos pontos críticos não voltavam para serem contados.

---

## 7. Casos de empresas

Casos que funcionam começam pela pergunta e terminam em uma plataforma de métricas.

| Empresa | O que fez | O que ensina |
|---|---|---|
| Amazon | separa métricas de input controláveis das de output na revisão semanal | cobrar o que o time controla |
| Netflix | usa testes A/B como forma padrão de decidir mudanças no produto | causalidade antes de opinião |
| Airbnb | criou o Minerva, com uma definição única por métrica | uma métrica, um lugar |
| Uber | criou o uMetric para eliminar discrepâncias em métricas críticas | métrica com ciclo de vida |
| iFood | testou em A/B a tela de pagamento e mediu aumento relativo na participação do pagamento online | hipótese, teste e métrica definidas |

---

## 8. Ambiente de trabalho: Databricks Free Edition

**O que tem:**
- workspace próprio, criado no cadastro;
- computação **serverless**;
- um SQL warehouse de tamanho 2X-Small;
- notebooks, SQL Editor, dashboards e Genie;
- login por e-mail, Google ou Microsoft.

**Limites:**
- um workspace por conta;
- só serverless, com **cota diária** que, se esgotada, desliga a computação pelo resto do dia (os dados e notebooks não se perdem);
- acesso restrito à internet, então os dados entram por upload;
- uso não comercial; conta inativa por muito tempo pode ser apagada.

**Cinco lugares do menu que acompanham o curso:**

| Lugar | Para que serve | Quando usamos |
|---|---|---|
| Catalog | ver catálogos, schemas, tabelas e volumes | aulas 1 a 3 |
| SQL Editor | escrever e rodar SQL no warehouse | aulas 1 a 3 |
| Workspace (notebooks) | notebooks com SQL, Python e gráficos | aulas 1 e 3 |
| Dashboards | painéis AI/BI | aula 4 |
| Genie Agents | perguntas em linguagem natural sobre os dados | aula 5 |

Nomes de objetos seguem o padrão `catálogo.schema.tabela`.

---

## 9. Glossário

| Termo | Significado |
|---|---|
| **BI** | Business Intelligence: descreve o que aconteceu |
| **BA** | Business Analytics: investiga por que aconteceu e o que fazer |
| **Camada de métricas** | lugar onde a fórmula de cada indicador é definida uma única vez |
| **Dono da métrica** | pessoa responsável pela definição, pela meta e pela ação quando o número muda |
| **GMV** | Gross Merchandise Value, valor bruto de mercadorias vendidas |
| **Lagging / leading** | indicador de resultado / indicador antecedente |
| **Latência de decisão** | tempo entre a informação chegar e alguém decidir e agir |
| **Lakehouse** | arquitetura que combina data lake e data warehouse |
| **Métrica de guarda** | métrica que não pode piorar enquanto se persegue uma meta |
| **North Star** | métrica que resume o valor entregue ao cliente |
| **Vanity metric** | métrica que cresce sem mudar nenhuma decisão |
| **Viés de sobrevivência** | analisar só o que "sobreviveu" e ignorar o que saiu da amostra |

---

## 10. Referências citadas em aula

Lista parcial, conforme citadas no material da disciplina.

- AMPLITUDE. *The North Star Playbook*.
- BRYAR, C.; CARR, B. *Working Backwards*. St. Martin's Press, 2021.
- CONN, C.; MCLEAN, R. *Bulletproof Problem Solving*. Wiley, 2019.
- DAVENPORT, T. H.; HARRIS, J. G. *Competing on Analytics*. Harvard Business School Press, 2007.
- HACKATHORN, R. *Active data warehousing: from nice to necessary*. Teradata Magazine, 2006.
- HOWARD, R. A. *Information value theory*. IEEE Transactions on Systems Science and Cybernetics, 1966.
- LUHN, H. P. *A business intelligence system*. IBM Journal of Research and Development, 1958.
- MANGEL, M.; SAMANIEGO, F. J. *Abraham Wald's work on aircraft survivability*. Journal of the American Statistical Association, 1984.
- MINTO, B. *The Pyramid Principle*. Pearson, 2009.
- MOESMANN, M.; PEDERSEN, T. B. *Data-driven prescriptive analytics applications: a comprehensive survey*. arXiv:2412.00034, 2024.
- PAHWA, A. et al. *How Airbnb achieved metric consistency at scale*. Airbnb Tech Blog, 2021.
- RIES, E. *The Lean Startup*. Crown Business, 2011.
- STRATHERN, M. *'Improving ratings': audit in the British University system*. European Review, 1997.
- Documentação pública da Databricks, Microsoft, Google Cloud e Tableau, e posts de engenharia da Netflix, Uber e iFood (via Apptimize).

---

*Anotações de estudo. Distribuídas sob a licença MIT, conforme o arquivo `LICENSE` do repositório.*

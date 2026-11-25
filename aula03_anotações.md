# Aula 03 — Anotações: catálogo de indicadores e primeiras métricas em SQL

**Disciplina:** Análise de Dados e Tomada de Decisões com BI e BA (IBM3705 / 15306207, turma 3001, 2026.2B)
**Curso:** MBA em IA, Big Data e Data Science para Negócios, IBMEC

> **Sobre este arquivo.** São anotações pessoais de estudo, escritas com as minhas palavras a partir dos slides e do roteiro prático. Não reproduzem os slides, o roteiro, as consultas, os gabaritos nem os dados da disciplina, que pertencem ao professor e à instituição. Os exemplos de código são versões genéricas escritas por mim, e os números de exemplo são inventados. Do dataset público do Olist cito apenas ordens de grandeza.
>
> **Observação sobre a numeração.** Os PDFs que recebi com "Aula 3" no nome se identificam, no próprio conteúdo, como "Aula 2 de 6" (catálogo de indicadores) e "Aula 2 — roteiro prático" (carga bronze e primeiras métricas em SQL). Estas notas cobrem esse material. O roteiro avisa que a aula seguinte parte das tabelas criadas nele, e o próprio material cita a camada de métricas (metric views) como assunto da aula seguinte.

## Sumário

1. [Ponto de partida: o caso MoviePass](#1-ponto-de-partida-o-caso-moviepass)
2. [Frameworks organizam, mas não escolhem a métrica](#2-frameworks-organizam-mas-não-escolhem-a-métrica)
3. [Catálogo de indicadores](#3-catálogo-de-indicadores)
4. [Como definir e vigiar uma métrica](#4-como-definir-e-vigiar-uma-métrica)
5. [Cuidados ao ler números](#5-cuidados-ao-ler-números)
6. [Um mesmo indicador em várias ferramentas](#6-um-mesmo-indicador-em-várias-ferramentas)
7. [Prática: carga bronze e primeiras métricas em SQL](#7-prática-carga-bronze-e-primeiras-métricas-em-sql)
8. [Exercício de ambiguidade: "cliente ativo"](#8-exercício-de-ambiguidade-cliente-ativo)
9. [Entrega do grupo](#9-entrega-do-grupo)
10. [Glossário](#10-glossário)
11. [Referências citadas](#11-referências-citadas)

---

## 1. Ponto de partida: o caso MoviePass

- Em 2017, o MoviePass passou a cobrar uma assinatura de US$ 9,95 por mês para dar acesso a um filme por dia, **pagando aos cinemas o preço cheio** de cada ingresso usado. Em 2018, a base passou de 3 milhões de assinantes. A controladora anunciou medidas para cortar a queima de caixa, como restringir lançamentos e subir o preço. Em 2021, a FTC acusou a empresa de limitar o uso justamente dos clientes mais ativos (bloqueios e exigências como foto do ingresso). Empresa e controladora já tinham pedido falência.
- A métrica que a empresa comunicava era o **número de assinantes**, e ela crescia mais rápido justamente quando a oferta dava mais prejuízo.
- O que o conselho precisava ver: receita menos custo por assinante (unit economics), uso por assinante por coorte, queima de caixa mensal e *runway*.
- **Regra que fica:** quando a métrica que cresce também aumenta o prejuízo, ela é métrica de vaidade. Toda métrica de crescimento precisa de um **par** que mostre o custo desse crescimento.

---

## 2. Frameworks organizam, mas não escolhem a métrica

Resumo dos frameworks desta etapa (detalhes nas anotações da aula 2):

| Framework | Ideia | Pergunta que responde |
|---|---|---|
| **North Star e árvore** | uma métrica de valor entregue, decomposta em drivers e inputs | O que move o valor? |
| **AARRR** | cinco estágios do funil: aquisição, ativação, retenção, receita, indicação | Onde o funil vaza? |
| **HEART** | felicidade, engajamento, adoção, retenção e sucesso na tarefa | A experiência do produto melhorou? |
| **OKR × KPI** | aposta trimestral × sinal vital contínuo | O que vamos mudar neste trimestre, sem quebrar o resto? |
| **Unit economics** | margem por unidade, CAC, LTV, payback | Cada cliente dá lucro? |

Pontos que reforçam o que já vimos:

- **North Star mede o valor entregue ao cliente, não o valor que a empresa captura.** A receita é valor capturado e vem depois. Cadeia: valor entregue → valor capturado → resultado. Critérios práticos para testar uma candidata: o cliente reconheceria o valor? Os times conseguem mover? Ela antecipa a receita? Ela não cresce sozinha com o passar do tempo?
- **Árvore de métricas:** cada nível se explica pelo de cima por multiplicação ou soma, as folhas são inputs que um time move em semanas, e ao lado vai uma métrica de guarda. Dois exemplos de decomposição: `GMV = clientes × pedidos por cliente × ticket médio` (com clientes = novos + recorrentes) e `MRR = contas pagantes × ARPA` (com contas = mês anterior + novas + reativadas − canceladas).
- O ciclo **AARRR** vem de Dave McClure (2007); o **HEART**, de pesquisadores do Google (Rodden, Hutchinson e Fu, 2010). O HEART usa o processo *Goals → Signals → Metrics*.

---

## 3. Catálogo de indicadores

O catálogo organiza os indicadores em **onze famílias**: aquisição e marketing, ativação e produto, engajamento, retenção, receita e monetização, satisfação, financeiros, operações e varejo, marketplace, pessoas e risco. Em sala foram vistas as que o case usa; as demais ficam como consulta.

### 3.1 Aquisição: CAC

- `CAC = investimento de aquisição ÷ novos clientes` no mesmo período.
- **Blended:** inclui os clientes orgânicos. **Do canal pago:** só o investimento pago sobre os clientes que ele trouxe. O do canal pago costuma ser muito maior.
- Armadilhas: incluir ou não o salário do time comercial, defasagem entre o gasto e a conversão, e atribuição (quem leva o crédito).

### 3.2 Ativação

- **Ativação:** primeiro momento em que o cliente obtém valor ("aha"). **Time-to-value:** tempo até lá.
- O evento de ativação certo é o que **mais separa quem fica de quem sai**. Teste vários candidatos e compare, por exemplo, a conversão de teste em assinatura de quem fez e de quem não fez o evento.

### 3.3 Engajamento

- **DAU, WAU e MAU:** usuários distintos no dia, nos últimos 7 dias e no mês.
- **"Ativo" não tem definição única.** Empresas de capital aberto, como Uber e Nu Holdings, declaram a sua nos relatórios (por exemplo, "ao menos uma viagem no mês" e "gerou receita nos últimos 30 dias"). Comparar "ativos" sem ler a definição é comparar coisas diferentes.
- **Stickiness** = DAU médio do mês ÷ MAU do mês.
- **Curva L28** (ideia associada à "power user curve" da a16z): histograma de quantos usuários usaram o produto em exatamente N dos últimos 28 dias. Mostra a frequência que o MAU esconde.

### 3.4 Retenção

- **Coorte:** grupo que começou no mesmo período. Leituras: **linha** (como a coorte envelhece), **coluna** (as novas retêm melhor?) e **diagonal** (evento de calendário que atinge várias coortes ao mesmo tempo).
- **Curva de retenção** que se achata indica um núcleo de clientes que precisa do produto. Se continua caindo de forma constante, esse núcleo ainda não existe.
- **Churn de contas × churn de receita:** andam juntos quando quem sai paga na média; se separam quando quem sai paga mais ou menos que a média.
- **Reativação:** cliente que volta conta como receita, não como cliente novo.

### 3.5 Receita e monetização (modelo de assinatura)

- **MRR é um estoque**: a receita recorrente mensal em uma data. **ARR = MRR × 12.**
- Os **fluxos** que explicam a variação do MRR entre duas datas:

```
MRR final = MRR inicial + new + reativação + expansion − contraction − churn
```

  - *new:* conta que paga pela primeira vez.
  - *reativação:* conta que já pagou antes e voltou.
  - *expansion:* conta que passou a pagar mais.
  - *contraction:* conta que passou a pagar menos, sem zerar.
  - *churn:* conta que deixou de pagar.
  Os cinco movimentos precisam fechar com a diferença entre os dois MRRs. Se não fecharem, há erro na consulta.
- **NRR (retenção líquida de receita):** MRR atual das contas que já pagavam há 12 meses ÷ MRR delas há 12 meses, **incluindo expansão**. Acima de 100% significa crescer sem vender a nenhum cliente novo.
- **GRR (retenção bruta):** o mesmo cálculo **sem expansão**. Nunca passa de 100%.
- **ARPA, ARPU e ARPPU:** o mesmo numerador (receita) dividido por denominadores diferentes.

| Divisão | Nome | Denominador |
|---|---|---|
| receita ÷ pedidos | ticket médio | pedidos |
| receita ÷ clientes | receita por cliente | clientes |
| receita ÷ usuários ativos | ARPU | usuários ativos |
| receita ÷ pagantes | ARPA (ou ARPPU) | contas (ou usuários) pagantes |

  Quando uma conta tem vários usuários (uma loja com vários funcionários, por exemplo), **ARPA e ARPPU diferem**.

### 3.6 Marketplace: GMV

- **GMV** é o valor total transacionado na plataforma. **Não é receita.** A receita do marketplace é a parte que ele retém: `receita = take rate × GMV`.
- O próprio GMV tem mais de uma definição: pagamentos de todos os pedidos, só pedidos sem cancelados e indisponíveis, só produtos (sem frete) etc. Cada uma dá um número diferente.
- No dataset público do Olist, a **comissão não está nos dados**, então a receita do Olist não é calculável. É o primeiro exemplo de **métrica que o dado não mede**.

### 3.7 Ticket médio e a mediana

- O ticket médio de pedidos entregues fica bem acima do mediano no Olist (a média é cerca de 50% maior), porque poucos pedidos grandes puxam a média. Reportar média e mediana juntas evita enganos.

### 3.8 LTV: três jeitos de calcular

`LTV` é a margem que o cliente gera enquanto continua cliente. Sozinho diz pouco: importa a comparação com o CAC.

| Cálculo | Como é | Engana quando |
|---|---|---|
| **Fórmula simples** | ARPA × margem bruta ÷ churn mensal | o churn muda com a idade do cliente |
| **Por coorte** | o que cada coorte já gerou de fato | as coortes novas ainda não têm histórico |
| **Preditivo** | um modelo projeta cada cliente (tema de Machine Learning) | foi treinado em um período atípico |

Referências de David Skok: **LTV/CAC acima de 3** e **CAC recuperado em até 12 meses**.

### 3.9 Satisfação: NPS, CSAT e CES

- **NPS** = % de promotores (notas 9 e 10) − % de detratores (notas 0 a 6), na pergunta sobre recomendar a empresa (Reichheld, 2003). Pode ser **relacional** (periódico) ou **transacional** (após um contato ou compra).
- **CSAT:** satisfação com uma interação. **CES:** esforço do cliente para resolver o que precisava (Dixon, Freeman e Toman, 2010).
- **NPS é uma pergunta, não um sistema de gestão.** Vale cruzá-lo com comportamento, por exemplo, comparando o NPS de quem cancelou nos meses seguintes com o de quem ficou.
- No Olist, a nota de 1 a 5 da avaliação funciona como um CSAT transacional do pedido.

### 3.10 Financeiros: margem de contribuição

```
margem de contribuição = receita líquida − custo do produto (CMV) − frete − meios de pagamento − comissões
```

Se a margem de contribuição por unidade é **negativa**, cada venda a mais aumenta o prejuízo (o caso do MoviePass).

### 3.11 Operações e varejo

- **Entrega atrasada:** no Olist, "atrasado" foi definido como chegar depois do dia prometido (comparando datas sem hora). Cerca de 7% das entregas atrasam, e a **nota média cai de forma acentuada** conforme cresce o atraso (de perto de 4,3 para perto de 2 ou menos). É associação forte, não prova de causa.
- **OTIF (on time, in full):** entregue no prazo e completo. A McKinsey observa que não há definição padrão: varia o prazo (pedido ou prometido), a janela (horas ou dias) e a unidade (pedido ou item).
- **Vendas mesmas lojas (same-store sales):** crescimento só das lojas que já existiam no período de comparação. Separa crescimento real de efeito de abertura de loja. O critério de tempo de abertura varia por empresa e aparece no release de resultados.
- Outros indicadores de loja e estoque: **giro de estoque**, **ruptura** (item sem estoque na gôndola), **sell-through** (vendido ÷ recebido) e **GMROI** (margem bruta ÷ estoque médio a custo).

### 3.12 Pessoas, risco e indústria

| Pessoas | Risco e crédito | Indústria |
|---|---|---|
| turnover, eNPS, tempo de contratação, receita por funcionário | inadimplência acima de 90 dias, taxa de aprovação, perda com fraude, LTV ajustado ao risco | OEE = disponibilidade × desempenho × qualidade (Nakajima, 1988) |

### 3.13 Os "cinco números" por modelo de negócio

Para a visão executiva, cada modelo de negócio tem seus indicadores centrais:

| SaaS B2B | App de consumo | Marketplace |
|---|---|---|
| crescimento do ARR | usuários ativos (com a definição) | GMV |
| NRR | retenção por coorte (D30 ou M1) | take rate e receita líquida |
| churn de receita (GRR) | stickiness | liquidez |
| payback do CAC | CAC pago | recompra |
| runway | ARPU | concentração da oferta |

| E-commerce e varejo | Assinatura e serviços | Indústria |
|---|---|---|
| vendas mesmas lojas ou GMV | base ativa e churn | OEE |
| margem bruta | receita recorrente | custo unitário |
| giro de estoque e ruptura | NPS ou CSAT | OTIF |
| ticket e frequência de compra | custo de servir por cliente | giro de estoque |
| OTIF | ocupação da capacidade | taxa de acidentes |

---

## 4. Como definir e vigiar uma métrica

### As seis perguntas de uma definição

Uma métrica só está definida quando responde a:

1. **O que conta:** qual evento e qual unidade (pedido, cliente, conta, usuário).
2. **Em que janela:** calendário (o mês) ou móvel (últimos 30 dias), e qual é o "hoje".
3. **Em que grão:** cliente real, pedido ou conta.
4. **O que entra e o que sai:** status, cancelados, testes, contas internas.
5. **De onde vem:** tabela e sistema de origem.
6. **Quem é o dono:** quem responde pelo número e aprova mudanças na definição.

### Toda métrica tem um par que a vigia

| Métrica | Par que a vigia |
|---|---|
| crescimento da receita | margem |
| CAC | LTV e payback |
| NPS | churn |
| GMV | take rate |
| volume de entregas | percentual de atraso |
| ativação | retenção no mês 1 |
| DAU | profundidade de uso (eventos por usuário) |

---

## 5. Cuidados ao ler números

- **A média nacional esconde os extremos.** No Olist, a taxa de atraso é baixa em estados que concentram os pedidos (São Paulo, que puxa a média para baixo) e chega a cerca de um quinto das entregas em Alagoas. Segmente por região, canal, plano e coorte **antes de concluir**.
- **Sem baseline, um mês incompleto parece um colapso.** O dataset do Olist termina em meados de outubro de 2018, então os últimos meses têm pouquíssimos pedidos. Não é queda de vendas, é fim do recorte de dados. Da mesma forma, um pico em novembro de 2017 é Black Friday: **sazonalidade não é tendência**. Para separar sinal de ruído: compare com o mesmo mês do ano anterior, use média móvel, conheça a variação normal e marque os meses incompletos.
- **"Cliente ativo" varia muito conforme a janela e o status.** No Olist, contar clientes distintos com compra em 30, 90 ou 365 dias vai de alguns milhares a dezenas de milhares. A escolha da janela muda a ordem de grandeza do número.
- **Atenção à chave do cliente.** No Olist, cada pedido recebe um identificador de cliente novo; a pessoa de verdade é outra coluna. Contar pela chave errada não dá erro: dá recompra igual a zero, um número plausível e falso.
- **Contagens distintas não se somam.** Somar os DAUs de um mês conta o mesmo usuário várias vezes. O MAU precisa de uma contagem distinta própria.
- **Correlação não é causa.** Atraso e nota baixa andam juntos, mas isso por si só não prova causalidade.

---

## 6. Um mesmo indicador em várias ferramentas

Exemplo: "clientes ativos em 90 dias", contando pessoas distintas com pedido em qualquer status nos 90 dias até uma data.

| Plataforma | Como o conceito aparece |
|---|---|
| **Databricks** | consulta SQL; depois, uma *metric view* no Unity Catalog |
| **Power BI** | medida DAX com contagem distinta e filtro de datas (`DISTINCTCOUNT` combinado com função de período) |
| **Looker** | *measure* em LookML do tipo `count_distinct`, filtrada por data no Explore |
| **Tableau** | campo calculado com `COUNTD` e filtro de data relativa |

A ideia: a **definição** é única; só a sintaxe muda de ferramenta para ferramenta.

---

## 7. Prática: carga bronze e primeiras métricas em SQL

### Objetivo

No Databricks Free Edition, carregar um dataset público de e-commerce (Olist, do Kaggle), gerar um dataset sintético de uma empresa fictícia de software por assinatura para pequeno varejo (a "Prateleira") e calcular as famílias de indicadores da aula em SQL: receita, GMV, ticket, recompra, atraso e nota no marketplace; DAU, MAU, stickiness, MRR, churn e coorte no SaaS.

### Convenções de nomes

O material fixa nomes padronizados (catálogo, schema `bronze`, um volume para arquivos e tabelas com prefixos por origem) porque as aulas seguintes partem das mesmas tabelas. Seguir exatamente os nomes evita erro de "tabela não encontrada". O padrão geral é `catálogo.schema.tabela`.

### Etapas

1. **Em casa:** baixar os arquivos (CSVs do Olist, ignorando o de geolocalização, e o notebook gerador) e conferir os tamanhos em bytes.
2. **Estrutura e upload:** ligar o SQL warehouse, criar o schema e o **volume** (pasta governada pelo Unity Catalog para guardar arquivos antes de virarem tabelas) e enviar os CSVs por upload. O upload é necessário porque a Free Edition restringe o acesso à internet. Conferir com o comando `LIST` sobre o caminho do volume.
3. **Tabelas bronze do Olist:** três pela interface (para aprender o caminho) e cinco por SQL com `read_files`, declarando o tipo de cada coluna.
   - A **bronze** guarda o dado como veio da fonte, sem tratamento (até um nome de coluna escrito errado na origem fica como está).
   - O CEP é declarado como texto (`STRING`) para não perder o zero à esquerda.
   - A tabela de avaliações precisa da opção de **várias linhas** (e do caractere de escape), pois os comentários têm quebras de linha e aspas.
   - Conferir as contagens de linhas das tabelas contra os valores esperados.
4. **Dataset da Prateleira:** importar o notebook gerador, conectar à computação serverless e rodar tudo. Ele gera os dados com uma **semente fixa** (para todos terem o mesmo resultado), grava sete tabelas e confere um **total de controle**. Se algo divergir, usa-se o plano B (carregar os CSVs prontos).
5. **Métricas do Olist** (seis consultas, salvas para reuso nas aulas seguintes):
   - pedidos por mês (total e entregues);
   - receita de produtos, frete e GMV por mês, calculado de dois jeitos (pelos itens e pelos pagamentos);
   - ticket médio e mediano dos pedidos entregues;
   - pedidos por cliente e taxa de recompra, contando por duas chaves diferentes;
   - entregas atrasadas por mês e por estado;
   - nota média por faixa de atraso.
6. **Métricas da Prateleira** (seis consultas):
   - DAU, WAU e MAU no último dia com dados (e, como bônus, a curva L28);
   - stickiness mensal;
   - MRR e contas pagantes no fim de cada mês;
   - movimentos de MRR por mês;
   - churn mensal de contas e de receita;
   - uma primeira visão de coorte.

### Técnicas de SQL que apareceram

| Técnica | Para que serve |
|---|---|
| `DATE_TRUNC('MONTH', data)` | leva uma data ao primeiro dia do mês, para agrupar por mês |
| `COUNT_IF(condição)` | conta só as linhas que atendem à condição |
| `COUNT(DISTINCT ...)` | conta valores distintos (usuários, clientes), não linhas |
| `CASE WHEN ... THEN 1 ELSE 0 END` + `AVG` | calcula uma **proporção** (por exemplo, % de atrasados ou % de recompra) |
| **CTE** (`WITH`) | tabela temporária dentro da consulta, para dividir o raciocínio em etapas |
| `JOIN` × `LEFT JOIN` | `JOIN` mantém só os casos com correspondência; `LEFT JOIN` mantém todos do lado esquerdo |
| `MEDIAN` / `percentile_approx` | mediana |
| `DATEDIFF` | diferença em dias entre duas datas |
| `NULLIF(x, 0)` | evita divisão por zero |
| `COALESCE(x, 0)` | troca nulo por zero |
| `sequence` + `explode` | gera um **calendário** com todos os meses, mesmo os sem dados |
| `CROSS JOIN` | cria todas as combinações (por exemplo, todas as contas em todos os meses) |
| **Funções de janela** (`LAG`, `MAX ... OVER`) | comparam cada linha com a anterior ou com o histórico da própria conta |
| `months_between` | calcula a idade em meses (útil para coortes) |
| `UNION ALL` | empilha resultados para compará-los lado a lado |

Dois padrões genéricos, escritos por mim para fixar a ideia:

```sql
-- Proporção com CASE: % de pedidos atrasados por mês
SELECT date_trunc('MONTH', data_compra) AS mes,
       100 * AVG(CASE WHEN data_entrega > data_prometida THEN 1 ELSE 0 END) AS pct_atrasados
FROM pedidos
GROUP BY 1
ORDER BY 1;
```

```sql
-- Mês anterior de cada conta com função de janela
SELECT conta_id, mes, mrr,
       LAG(mrr, 1, 0) OVER (PARTITION BY conta_id ORDER BY mes) AS mrr_anterior
FROM mrr_por_conta_e_mes;
```

### Lições metodológicas da prática

- **Some antes de juntar.** Ligar diretamente tabelas com várias linhas por pedido (itens e pagamentos) multiplica as linhas e conta valores em dobro. Agregue cada tabela por pedido primeiro e só então una.
- **Um mês sem dados some do resultado.** Para ele aparecer com zero, use um calendário auxiliar e `LEFT JOIN`.
- **Compare datas sem hora** quando a regra de negócio é por dia (um pedido entregue às 18h do dia prometido está no prazo). Para janelas, use limite superior **exclusivo** no dia seguinte (`< dia+1`), senão se perdem os eventos do último dia, que têm hora.
- **MRR é fotografia do fim do mês.** Uma conta que assina e cancela dentro do mesmo mês não aparece, o que também explica por que a retenção do mês 0 pode ficar abaixo de 100%.
- **Confira que os fluxos fecham** com a variação do estoque (MRR final − MRR inicial).
- **Salve as consultas** com nomes por passo, pois serão reutilizadas.
- **Em coortes, nulo significa "ainda não aconteceu"**, não zero.
- **O Databricks pode omitir zeros finais** nos números (848860.1 em vez de 848860.10), o que não é diferença.

### Erros comuns

| Sintoma | Causa provável | Solução |
|---|---|---|
| botões diferentes do roteiro | interface em português | mudar para inglês |
| catálogo ou schema inexistente | catálogo com outro nome | conferir no Catalog e trocar o nome nos comandos |
| Create table sem pré-visualização | warehouse parado | ligar o warehouse |
| `PATH_NOT_FOUND` | caminho ou nome de arquivo errado | rodar `LIST` e conferir (inclusive maiúsculas) |
| contagem de avaliações diferente | comentários com quebra de linha e aspas | recriar a tabela com a opção de várias linhas e o escape |
| erro de tipo em função de data | coluna de data criada como texto | recriar declarando os tipos |
| `TABLE_OR_VIEW_NOT_FOUND` | nome diferente ou schema errado | conferir o nome completo |
| recompra zero nas duas linhas | as duas contagens usam a mesma chave | usar a chave da pessoa em uma delas |
| MRR maior que o esperado | condição de fim da assinatura incompleta | copiar a condição inteira |
| computação desliga | cota diária da Free Edition | seguir o plano B; os dados salvos não se perdem |

### Plano B

1. Se a interface de criação de tabela falhar, criar as tabelas por SQL.
2. Se o notebook gerador falhar, carregar os CSVs prontos da Prateleira pelo portal.
3. Se a computação ficar indisponível, usar o workspace de um colega do grupo ou acompanhar pelo gabarito e refazer no dia seguinte.

Ao final, o schema bronze tem 15 tabelas e um volume, e **não se deve apagar nada**, pois a aula seguinte parte delas.

---

## 8. Exercício de ambiguidade: "cliente ativo"

- Metade da turma calcula "clientes ativos" do Olist com uma definição (**A**) e a outra metade com outra (**B**), usando o mesmo "hoje" (último mês completo do dataset).
  - **A:** pessoa com pedido em **qualquer status** nos **90 dias** até a data.
  - **B:** pessoa com pedido **entregue** nos **30 dias** até a data.
- As duas consultas só diferem na janela e no filtro de status, e os resultados ficam em ordens de grandeza diferentes.
- Discussão proposta: qual número levar ao CEO e por quê; qual das seis perguntas da definição explica a diferença; reescrever a definição escolhida respondendo às seis perguntas em até três linhas.
- **Bônus na Prateleira:** "conta ativa" para o financeiro é quem **paga**; para produto é quem **usa**. A segunda definição é maior, pois inclui contas em período de teste e contas que cancelaram durante a janela. As duas estão certas para a pergunta de cada área.

---

## 9. Entrega do grupo

O trabalho final do grupo (em 30 minutos, no workspace-sede) é uma **árvore de métricas** que separa o que o dado mede do que não mede:

1. **Uma North Star**, de **3 a 5 drivers** e de **2 a 3 métricas de input** por driver, em diagrama.
2. **Uma tabela** com uma linha por métrica, contendo: definição preliminar (as seis perguntas), se o dataset calcula (**sim**, **proxy** ou **não**), a tabela de origem e o dono no grupo.

Uma métrica que o dataset não calcula **não sai da árvore**: ela vira um pedido de dado ou uma hipótese na narrativa final. Exemplos de "não": take rate e margem no Olist; margem bruta e custo de servir na empresa fictícia de software.

---

## 10. Glossário

| Termo | Significado |
|---|---|
| **ARPA / ARPU / ARPPU** | receita média por conta paga / por usuário ativo / por usuário pagante |
| **ARR** | Annual Recurring Revenue, MRR × 12 |
| **Bronze** | camada de dados crus, como vieram da fonte |
| **CAC** | custo de aquisição de um cliente |
| **CES / CSAT / NPS** | esforço do cliente / satisfação com uma interação / disposição a recomendar |
| **Churn** | taxa de contas (ou de receita) perdidas em um período |
| **Coorte** | grupo de clientes que começou no mesmo período |
| **CTE** | *Common Table Expression*, tabela temporária definida com `WITH` |
| **Expansion / contraction** | aumento / redução do MRR de contas que continuam pagando |
| **GMV** | valor bruto de mercadorias transacionadas |
| **GRR / NRR** | retenção bruta / líquida de receita (a líquida inclui expansão) |
| **LTV** | valor gerado por um cliente ao longo do relacionamento |
| **MRR** | receita recorrente mensal (um estoque) |
| **OEE** | eficiência global de equipamento (indústria) |
| **OTIF** | entrega no prazo e completa |
| **Runway** | quantos meses o caixa dura no ritmo atual de queima |
| **Same-store sales** | vendas mesmas lojas |
| **Stickiness** | DAU médio ÷ MAU |
| **Take rate** | percentual do GMV que o marketplace retém como receita |
| **Time-to-value** | tempo até o cliente perceber o valor |
| **Volume (Unity Catalog)** | pasta governada para guardar arquivos |

---

## 11. Referências citadas

Lista parcial, conforme citadas no material da disciplina.

- AMPLITUDE. *The North Star Playbook*.
- CROLL, A.; YOSKOVITZ, B. *Lean Analytics*. O'Reilly, 2013.
- DAVIES, A. et al. *Defining "on-time, in-full" in the consumer sector*. McKinsey & Company, 2019.
- DIXON, M.; FREEMAN, K.; TOMAN, N. *Stop trying to delight your customers*. Harvard Business Review, 2010.
- DOERR, J. *Measure What Matters*. Portfolio/Penguin, 2018.
- FEDERAL TRADE COMMISSION. Comunicado sobre o acordo com os operadores do MoviePass, 2021.
- JIN, L.; CHEN, A. *The power user curve*. Andreessen Horowitz, 2018.
- JORDAN, J. et al. *16 startup metrics*. Andreessen Horowitz, 2015.
- KAPLAN, R. S.; NORTON, D. P. *The balanced scorecard*. Harvard Business Review, 1992.
- MCCLURE, D. *Startup metrics for pirates: AARRR!*, 2007.
- NAKAJIMA, S. *Introduction to TPM*. Productivity Press, 1988.
- OLIST. *Brazilian e-commerce public dataset by Olist*. Kaggle.
- REICHHELD, F. F. *The one number you need to grow*. Harvard Business Review, 2003.
- RODDEN, K.; HUTCHINSON, H.; FU, X. *Measuring the user experience on a large scale*. CHI, 2010.
- SKOK, D. *SaaS metrics 2.0*. For Entrepreneurs, 2013.
- Relatórios de resultados de empresas listadas (Uber, Nu Holdings, Magazine Luiza) e documentação pública da Databricks, Microsoft, Google Cloud e Tableau.

---

*Anotações de estudo. Distribuídas sob a licença MIT, conforme o arquivo `LICENSE` do repositório. Os dados do Olist são públicos e regidos pelos termos do próprio dataset; nenhum dado é incluído neste repositório.*

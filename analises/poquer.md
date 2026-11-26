# Critérios de teste — poquer

> Orientação: As linhas preenchidas usam um pedido fictício e servem para testar a montagem dos prompts. Substituam todas essas linhas pela análise da funcionalidade deste arquivo antes de preparar o plano oficial. Acrescentem uma linha por classe, fronteira, regra ou obrigação de cobertura. Se um critério não se aplicar, substituam sua tabela por uma justificativa breve.

> Orientação: Registrem somente os critérios e seus resultados de análise. A LLM escolhe os valores de entrada para cada caso, determina o resultado esperado e escreve os testes JUnit.

### Análise caixa-preta do grupo

> Orientação: Usem a especificação como fonte. Considerem a composição da mão, os naipes, as repetições e as posições permitidas do ás.

#### Classes de equivalência

> Orientação: Informem o parâmetro ou regra, deem um nome à classe e descrevam a faixa ou propriedade que inclui seus membros. Registrem classes válidas e inválidas quando previstas.

| Parâmetro ou regra | Classe de equivalência | Domínio ou condição |
|---|---|---|
| Lista de valores | Lista válida | Lista não nula com exatamente cinco elementos `ValorCarta` não nulos |
| Lista de naipes | Lista válida | Lista não nula com exatamente cinco elementos `Naipe` não nulos |
| Listas de entrada | Listas inválidas nulas | Qualquer uma das duas listas é `null`; deve provocar `NullPointerException` |
| Elementos das listas | Elemento inválido nulo | Qualquer posição contém valor ou naipe nulo; deve provocar `NullPointerException` |
| Tamanho das listas | Tamanho inválido inferior | Uma ou ambas as listas têm menos de cinco elementos; deve provocar `IllegalArgumentException` |
| Tamanho das listas | Tamanho inválido superior | Uma ou ambas as listas têm mais de cinco elementos; deve provocar `IllegalArgumentException` |
| Cartas formadas pelos pares | Cinco cartas distintas | Os pares `valores[i]` e `naipes[i]` são todos distintos; entrada válida |
| Cartas formadas pelos pares | Par de carta repetido | Pelo menos dois índices formam o mesmo par valor/naipe; deve provocar `IllegalArgumentException` |
| Valores repetidos | Valores iguais com naipes diferentes | Cartas diferentes podem compartilhar o valor; condição necessária para par, trinca, quadra e full house |
| Naipes repetidos | Naipes iguais com valores diferentes | Cartas diferentes podem compartilhar o naipe; condição necessária para flush |
| Ordem da mão | Ordem válida reordenada | As listas são reordenadas mantendo `valores[i]` associado a `naipes[i]`; a classificação deve permanecer igual |
| Valores da mão | Sequência real | Valores 10, valete, dama, rei e ás, todos do mesmo naipe |
| Valores da mão | Sequência do mesmo naipe | Cinco valores consecutivos, todos do mesmo naipe, exceto a sequência real |
| Frequência de valores | Quadra | Quatro cartas do mesmo valor e uma carta de valor diferente |
| Frequência de valores | Trinca e par | Três cartas de um valor e duas cartas de outro valor |
| Naipes da mão | Cinco do mesmo naipe sem sequência | Cinco cartas do mesmo naipe, mas sem sequência real ou sequência comum |
| Valores da mão | Sequência com mais de um naipe | Cinco valores consecutivos, com pelo menos dois naipes, sem ser sequência real do mesmo naipe |
| Frequência de valores | Trinca | Três cartas do mesmo valor, sem formar trinca e par ou quadra |
| Frequência de valores | Dois pares | Dois pares de valores distintos e uma quinta carta de outro valor |
| Frequência de valores | Um par | Exatamente um par e as demais cartas com valores distintos |
| Frequência de valores | Carta alta | Nenhuma sequência, flush, quadra, trinca, dois pares ou par |
| Ás em sequência | Ás baixo | `AS, DOIS, TRES, QUATRO, CINCO`; pode formar sequência |
| Ás em sequência | Ás alto | `DEZ, VALETE, DAMA, REI, AS`; pode formar sequência |
| Ás em sequência | Ás em posição intermediária inválida | Combinações em que a passagem de rei para ás não forma cinco valores consecutivos; não deve ser sequência |
#### Valores-limite

> Orientação: Indiquem o valor onde muda a validade ou o comportamento e descrevam as classes que ele separa. Esclareçam a qual classe pertence o próprio limite.

| Parâmetro ou regra | Valor-limite | Classes separadas e inclusão do limite |
|---|---|---|
| Tamanho das listas | 4/5/6 elementos | Quatro é inválido, cinco é o tamanho válido exato, seis é inválido |
| Distinção das cartas | Primeiro par repetido | Cinco pares distintos são válidos; ao repetir qualquer par valor/naipe, a entrada se torna inválida |
| Frequência de valores | 1/2 cartas do mesmo valor | Uma ocorrência não forma repetição; duas ocorrências formam um par |
| Frequência de valores | 2/3 cartas do mesmo valor | Duas formam par; três formam trinca |
| Frequência de valores | 3/4 cartas do mesmo valor | Três formam trinca; quatro formam quadra, desde que a quinta carta tenha outro valor |
| Frequência de valores | 4/5 cartas do mesmo valor | Quatro podem formar quadra; cinco não são possíveis com pares valor/naipe distintos, pois só existem quatro naipes |
| Quantidade de pares | 0/1/2 pares | Zero não forma par; um forma `UM_PAR`; dois formam `DOIS_PARES` |
| Quantidade de pares | 2/3 pares | Dois pares distintos formam `DOIS_PARES`; três pares exigiriam seis cartas e não pertencem a uma mão válida de cinco cartas |
| Naipes iguais | 4/5 cartas do mesmo naipe | Quatro não formam flush; cinco formam `CINCO_DO_MESMO_NAIPE` ou uma categoria de sequência de maior prioridade |
| Sequência | Quatro/cinco valores consecutivos | Quatro valores consecutivos não formam sequência; cinco valores consecutivos formam sequência |
| Sequência baixa | `AS, DOIS, TRES, QUATRO, CINCO` | O ás na primeira posição numérica permite a sequência baixa |
| Sequência alta | `DEZ, VALETE, DAMA, REI, AS` | O ás na última posição da sequência alta permite sequência real quando todos os naipes são iguais |
| Ás intermediário | Rei seguido de ás sem sequência baixa ou alta | A passagem de rei para ás quebra a sequência; a mão deve ser classificada por outra regra aplicável |
| Sequência do mesmo naipe | Sequência com um ou mais naipes diferentes | Cinco consecutivas do mesmo naipe formam sequência do mesmo naipe; com mais de um naipe, formam `SEQUENCIA` |
| Prioridade entre categorias | Sequência real versus sequência do mesmo naipe | Uma sequência real do mesmo naipe deve ser classificada como `SEQUENCIA_REAL_DO_MESMO_NAIPE`, não como sequência do mesmo naipe |
| Prioridade entre categorias | Flush versus sequência | Cinco do mesmo naipe e consecutivas devem receber categoria de sequência do mesmo naipe; cinco do mesmo naipe não consecutivas recebem `CINCO_DO_MESMO_NAIPE` |

#### Tabela de decisão

> Orientação: Uma linha descreve uma combinação geral de condições e a ação que a especificação prevê. Usem estados ou categorias, como condição satisfeita, condição insatisfeita ou indiferente, quando couber.

| Regra | Combinação de condições | Ação prevista na especificação |
|---|---|---|
| POQ-01 | Lista de valores ou lista de naipes é nula | Rejeitar com `NullPointerException` |
| POQ-02 | Algum elemento de valores ou naipes é nulo | Rejeitar com `NullPointerException` |
| POQ-03 | Uma ou ambas as listas têm tamanho diferente de cinco | Rejeitar com `IllegalArgumentException` |
| POQ-04 | Existe par valor/naipe repetido | Rejeitar com `IllegalArgumentException` |
| POQ-05 | Cinco valores são 10, valete, dama, rei e ás, e os cinco naipes são iguais | Retornar `SEQUENCIA_REAL_DO_MESMO_NAIPE` |
| POQ-06 | Cinco valores são consecutivos e os cinco naipes são iguais, sem ser sequência real | Retornar `SEQUENCIA_DO_MESMO_NAIPE` |
| POQ-07 | Quatro cartas têm o mesmo valor e a quinta tem valor diferente | Retornar `QUADRA` |
| POQ-08 | Três cartas têm um valor e duas têm outro valor | Retornar `TRINCA_E_PAR` |
| POQ-09 | Cinco cartas têm o mesmo naipe, mas os valores não formam sequência | Retornar `CINCO_DO_MESMO_NAIPE` |
| POQ-10 | Cinco valores são consecutivos e há mais de um naipe | Retornar `SEQUENCIA` |
| POQ-11 | Três cartas têm o mesmo valor e não há par formando trinca e par | Retornar `TRINCA` |
| POQ-12 | Existem dois pares de valores distintos e uma quinta carta de outro valor | Retornar `DOIS_PARES` |
| POQ-13 | Existe exatamente um par e as demais cartas têm valores distintos | Retornar `UM_PAR` |
| POQ-14 | Nenhuma das condições anteriores é satisfeita | Retornar `CARTA_ALTA` |
| POQ-15 | A mão satisfaz mais de uma condição de classificação | Retornar a primeira classificação aplicável na ordem de prioridade da especificação |
| POQ-16 | Os valores formam a sequência baixa com ás | Considerar a mão como sequência; se os naipes forem iguais, retornar `SEQUENCIA_DO_MESMO_NAIPE` |
| POQ-17 | Os valores formam a sequência alta com ás | Considerar a mão como sequência; se os naipes forem iguais, retornar `SEQUENCIA_REAL_DO_MESMO_NAIPE` |
| POQ-18 | Há passagem de rei para ás fora das sequências permitidas | Não considerar a mão como sequência; aplicar a primeira outra classificação válida |

### Análise caixa-branca do grupo

> Orientação: Usem o código de produção. Na primeira coluna, indiquem o método e o trecho ou a decisão. Na segunda, descrevam em linguagem natural como a decisão, condição ou caminho funciona. Na terceira, indiquem quais resultados de decisão, ramos ou caminhos a suíte deve exercitar. Se houver curto-circuito, indiquem quando uma condição posterior chega a ser avaliada.

| Método ou trecho | Decisão, condição ou caminho | Obrigação de cobertura |
|---|---|---|
| Construtor `Mao`, cópia das listas | `List.copyOf(valores)` e `List.copyOf(naipes)` rejeitam lista nula ou elemento nulo. | Exercitar lista de valores nula, lista de naipes nula, elemento nulo em valores e elemento nulo em naipes, confirmando `NullPointerException`. |
| Construtor `Mao`, tamanho das listas | A decisão verifica se `valores.size() != 5 |  |
| Construtor `Mao`, validação de cartas distintas | O conjunto recebe pares `Map.entry(valor, naipe)` e rejeita quando um par já existe. | Exercitar cinco pares distintos e o resultado repetido com o mesmo valor e naipe em dois índices. |
| Construtor `Mao`, associação valor/naipe | Cada carta é definida pelo par na mesma posição das duas listas. | Reordenar a mão mantendo os pares associados e verificar a mesma classificação; também cobrir listas com os mesmos valores e naipes, mas associação diferente. |
| `classificar`, ordenação e distinção dos valores | Os valores são convertidos para números, ficam distintos e são ordenados. | Exercitar mão com valores todos distintos e mãos com valores repetidos, verificando que a sequência depende de cinco valores distintos. |
| `classificar`, sequência alta com ás | `sequenciaAltaComAs` é verdadeiro apenas para `[1, 10, 11, 12, 13]`. | Exercitar a sequência alta válida e combinações próximas que não correspondem exatamente a essa lista. |
| `classificar`, sequência geral | A sequência é verdadeira para a sequência alta com ás ou quando há cinco valores distintos e `maior - menor == 4`. | Exercitar sequência baixa com ás, sequências comuns sem ás, sequência alta com ás e casos não consecutivos. |
| `classificar`, sequência baixa com ás | A lista ordenada `[1, 2, 3, 4, 5]` satisfaz o teste de intervalo de quatro unidades. | Exercitar o caminho verdadeiro para ás baixo e um caso com ás em posição intermediária que não tenha cinco valores consecutivos. |
| `classificar`, mesmo naipe | `allMatch` verifica se todos os naipes são iguais ao primeiro. | Exercitar cinco naipes iguais e mãos com pelo menos um naipe diferente, incluindo diferença na primeira e em outras posições. |
| `classificar`, frequências dos valores | O vetor de frequências conta cada valor; `pares` conta frequências exatamente iguais a 2 e `maiorFrequencia` guarda a maior frequência. | Exercitar frequências máximas 1, 2, 3 e 4; cobrir contagem de um e dois pares e a combinação de trinca com par. |
| `classificar`, sequência real do mesmo naipe | A primeira condição de classificação exige `mesmoNaipe && sequenciaAltaComAs`. | Exercitar verdadeiro e falso, incluindo sequência alta com naipes diferentes e sequência real do mesmo naipe. |
| `classificar`, sequência do mesmo naipe | A segunda condição exige `mesmoNaipe && sequencia`, depois que a sequência real não foi selecionada. | Exercitar sequência baixa ou comum do mesmo naipe, sequência não do mesmo naipe e sequência real para confirmar a prioridade. |
| `classificar`, quadra | A condição verifica `maiorFrequencia == 4`. | Exercitar verdadeiro e falso, incluindo uma mão com quatro cartas do mesmo valor e uma mão sem quadra. |
| `classificar`, trinca e par | A condição verifica `maiorFrequencia == 3 && pares == 1`. | Exercitar verdadeiro com três mais duas cartas e falsidade com trinca sem par e com outras frequências. |
| `classificar`, cinco do mesmo naipe | A condição verifica `mesmoNaipe` depois das classificações de sequência e de trinca e par. | Exercitar cinco do mesmo naipe sem sequência e mãos do mesmo naipe que devem ter classificação anterior. |
| `classificar`, sequência de naipes diferentes | A condição verifica `sequencia` depois de excluir as categorias de maior prioridade. | Exercitar sequência com mais de um naipe e sequência falsa com valores não consecutivos. |
| `classificar`, trinca | A condição verifica `maiorFrequencia == 3` após excluir trinca e par. | Exercitar trinca sem par e uma mão em que a maior frequência não é 3. |
| `classificar`, dois pares | A condição verifica `pares == 2`. | Exercitar dois pares de valores distintos e casos com zero, um ou apenas um grupo de pares. |
| `classificar`, um par | A condição verifica `pares == 1`. | Exercitar exatamente um par e uma mão sem pares. |
| `classificar`, carta alta | O retorno final ocorre quando nenhuma condição anterior é satisfeita. | Exercitar uma mão sem sequência, sem repetição de valor e sem cinco cartas do mesmo naipe. |
| `classificar`, ordem de prioridade | As condições são avaliadas na ordem: sequência real, sequência do mesmo naipe, quadra, trinca e par, cinco do mesmo naipe, sequência, trinca, dois pares, um par e carta alta. | Exercitar mãos que satisfaçam simultaneamente propriedades de categorias diferentes e confirmar o primeiro retorno correto. |

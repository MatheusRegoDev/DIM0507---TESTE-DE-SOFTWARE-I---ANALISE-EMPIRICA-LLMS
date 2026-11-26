# Critérios de teste — aluguel

> Orientação: As linhas preenchidas usam um pedido fictício e servem para testar a montagem dos prompts. Substituam todas essas linhas pela análise da funcionalidade deste arquivo antes de preparar o plano oficial. Acrescentem uma linha por classe, fronteira, regra ou obrigação de cobertura. Se um critério não se aplicar, substituam sua tabela por uma justificativa breve.

> Orientação: Registrem somente os critérios e seus resultados de análise. A LLM escolhe os valores de entrada para cada caso, determina o resultado esperado e escreve os testes JUnit.

### Análise caixa-preta do grupo

> Orientação: Usem a especificação como fonte. Considerem as faixas de diárias, quilometragem e atraso e as regras de fidelidade.

#### Classes de equivalência

> Orientação: Informem o parâmetro ou regra, deem um nome à classe e descrevam a faixa ou propriedade que inclui seus membros. Registrem classes válidas e inválidas quando previstas.

| Parâmetro ou regra | Classe de equivalência | Domínio ou condição |
|---|---|---|
| Categoria do veículo | Categoria válida econômica | `ECONOMICO`; diária de R$ 120 |
| Categoria do veículo | Categoria válida intermediária | `INTERMEDIARIO`; diária de R$ 180 |
| Categoria do veículo | Categoria válida SUV | `SUV`; diária de R$ 280 |
| Categoria do veículo | Categoria inválida nula | `null`; deve provocar `NullPointerException` |
| Nível do cliente | Nível válido comum | `COMUM`; não recebe desconto de fidelidade |
| Nível do cliente | Nível válido prata sem impedimento | `PRATA`, com pelo menos 7 diárias e sem atraso anterior; recebe 5% sobre as diárias após o desconto por duração |
| Nível do cliente | Nível válido ouro sem impedimento | `OURO`, com pelo menos 5 diárias e sem atraso anterior; recebe 10% sobre as diárias após o desconto por duração |
| Nível do cliente | Nível válido com desconto não aplicável | `PRATA` com menos de 7 diárias, `OURO` com menos de 5 diárias ou qualquer nível com atraso anterior; desconto de fidelidade igual a 0% |
| Nível do cliente | Nível inválido nulo | `null`; deve provocar `NullPointerException` |
| Tipo de seguro | Seguro válido sem cobertura | `SEM_SEGURO`; custo de R$ 0 por diária |
| Tipo de seguro | Seguro válido básico | `BASICO`; custo de R$ 25 por diária |
| Tipo de seguro | Seguro válido completo | `COMPLETO`; custo de R$ 45 por diária |
| Tipo de seguro | Seguro inválido nulo | `null`; deve provocar `NullPointerException` |
| Número de diárias | Diárias inválidas não positivas | Inteiros menores ou iguais a 0; deve provocar `IllegalArgumentException` |
| Número de diárias | Duração sem desconto | Inteiros de 1 a 2; desconto por duração de 0% |
| Número de diárias | Duração curta | Inteiros de 3 a 6; desconto por duração de 5% |
| Número de diárias | Duração média | Inteiros de 7 a 14; desconto por duração de 10% |
| Número de diárias | Duração longa | Inteiros maiores ou iguais a 15; desconto por duração de 15% |
| Quilometragem rodada | Quilometragem inválida | Inteiro menor que 0; deve provocar `IllegalArgumentException` |
| Quilometragem rodada | Dentro da franquia | Inteiro não negativo menor ou igual a `100 × númeroDiarias`; custo de quilometragem igual a 0 |
| Quilometragem rodada | Excedente baixo | Excedente positivo menor ou igual a 100 km; tarifa de R$ 0,80 por km excedente |
| Quilometragem rodada | Excedente intermediário | Excedente entre 101 e 300 km; tarifa de R$ 1,00 por km excedente |
| Quilometragem rodada | Excedente alto | Excedente maior que 300 km; tarifa de R$ 1,50 por km excedente |
| Horas de atraso | Atraso inválido | Inteiro menor que 0; deve provocar `IllegalArgumentException` |
| Horas de atraso | Atraso isento | Inteiros de 0 a 1; custo de atraso igual a 0 |
| Horas de atraso | Atraso tarifado por hora | Inteiros de 2 a 3; custo de `20% da diária sem desconto × horasAtraso` |
| Horas de atraso | Atraso equivalente a uma diária | Inteiros maiores ou iguais a 4; custo de uma diária sem desconto |
| Atraso anterior | Sem atraso anterior | `false`; permite desconto de fidelidade quando os demais requisitos são satisfeitos |
| Atraso anterior | Com atraso anterior | `true`; impede o desconto de fidelidade |
| Arredondamento | Total com centavos exatos | Total já possui duas casas decimais; deve ser retornado sem alteração de valor |
| Arredondamento | Total com mais de duas casas | Total possui mais de duas casas; deve ser arredondado para duas casas usando `HALF_UP` |

#### Valores-limite

> Orientação: Indiquem o valor onde muda a validade ou o comportamento e descrevam as classes que ele separa. Esclareçam a qual classe pertence o próprio limite.

| Parâmetro ou regra | Valor-limite | Classes separadas e inclusão do limite |
|---|---|---|
| Número de diárias | 0 | `0` pertence à classe inválida; `1` é a menor quantidade válida |
| Número de diárias | 1 | `1` pertence à faixa válida de 1–2 diárias, sem desconto por duração |
| Desconto por duração | 2/3 | `2` ainda recebe 0%; `3` inicia a faixa de 5% |
| Desconto por duração | 6/7 | `6` ainda recebe 5%; `7` inicia a faixa de 10% |
| Desconto por duração | 14/15 | `14` ainda recebe 10%; `15` inicia a faixa de 15% |
| Fidelidade Ouro | 4/5 | `4` não habilita o desconto Ouro; `5` habilita 10% se não houver atraso anterior |
| Fidelidade Prata | 6/7 | `6` não habilita o desconto Prata; `7` habilita 5% se não houver atraso anterior |
| Quilometragem rodada | `100 × númeroDiarias` / `100 × númeroDiarias + 1` | A quilometragem exatamente igual à franquia não gera cobrança; uma unidade acima inicia excedente |
| Tarifa de quilometragem | Excedente 100/101 km | Excedente de `100` km usa R$ 0,80/km; excedente de `101` km usa R$ 1,00/km |
| Tarifa de quilometragem | Excedente 300/301 km | Excedente de `300` km usa R$ 1,00/km; excedente de `301` km usa R$ 1,50/km |
| Horas de atraso | 0/1 | `0` e `1` hora são isentos |
| Horas de atraso | 1/2 | `1` hora ainda é isenta; `2` horas iniciam a cobrança de 20% da diária por hora |
| Horas de atraso | 3/4 | `3` horas ainda usam a cobrança por hora; `4` inicia a cobrança de uma diária |
| Quilometragem inválida | -1/0 | `-1` é inválido; `0` é válido e fica dentro da franquia |
| Horas de atraso inválidas | -1/0 | `-1` é inválido; `0` é válido e é isento |
| Fidelidade com atraso anterior | `false`/`true` | Com `false`, o desconto elegível pode ser aplicado; com `true`, o desconto é sempre 0% |
| Arredondamento monetário | Segunda/terceira casa decimal | O retorno deve possuir duas casas; a terceira casa determina o arredondamento `HALF_UP` |
#### Tabela de decisão

> Orientação: Uma linha descreve uma combinação geral de condições e a ação que a especificação prevê. Usem estados ou categorias, como condição satisfeita, condição insatisfeita ou indiferente, quando couber.

| Regra | Combinação de condições | Ação prevista na especificação |
|---|---|---|
| ALUG-01 | Categoria, nível e seguro presentes; número de diárias menor ou igual a 0 | Rejeitar com `IllegalArgumentException` após a validação de nulos |
| ALUG-02 | Categoria, nível ou seguro nulo | Rejeitar com `NullPointerException` |
| ALUG-03 | Diárias válidas entre 1 e 2 | Calcular diárias brutas sem desconto por duração |
| ALUG-04 | Diárias válidas entre 3 e 6 | Aplicar desconto de duração de 5% sobre as diárias brutas |
| ALUG-05 | Diárias válidas entre 7 e 14 | Aplicar desconto de duração de 10% sobre as diárias brutas |
| ALUG-06 | Diárias válidas maiores ou iguais a 15 | Aplicar desconto de duração de 15% sobre as diárias brutas |
| ALUG-07 | Cliente `PRATA`, pelo menos 7 diárias e sem atraso anterior | Aplicar 5% sobre as diárias após o desconto por duração |
| ALUG-08 | Cliente `OURO`, pelo menos 5 diárias e sem atraso anterior | Aplicar 10% sobre as diárias após o desconto por duração |
| ALUG-09 | Cliente `COMUM`, nível elegível abaixo do limite ou qualquer cliente com atraso anterior | Não aplicar desconto de fidelidade |
| ALUG-10 | Quilometragem negativa | Rejeitar com `IllegalArgumentException` |
| ALUG-11 | Quilometragem não negativa dentro da franquia de 100 km por diária | Não cobrar quilometragem excedente |
| ALUG-12 | Excedente positivo até 100 km | Cobrar todo o excedente a R$ 0,80/km |
| ALUG-13 | Excedente entre 101 e 300 km | Cobrar todo o excedente a R$ 1,00/km |
| ALUG-14 | Excedente acima de 300 km | Cobrar todo o excedente a R$ 1,50/km |
| ALUG-15 | Seguro `SEM_SEGURO` | Somar R$ 0 por diária ao total |
| ALUG-16 | Seguro `BASICO` | Somar R$ 25 por diária ao total |
| ALUG-17 | Seguro `COMPLETO` | Somar R$ 45 por diária ao total |
| ALUG-18 | Horas de atraso negativas | Rejeitar com `IllegalArgumentException` |
| ALUG-19 | Horas de atraso entre 0 e 1 | Não cobrar atraso |
| ALUG-20 | Horas de atraso entre 2 e 3 | Cobrar 20% da diária sem desconto por hora de atraso |
| ALUG-21 | Horas de atraso maiores ou iguais a 4 | Cobrar uma diária sem desconto |
| ALUG-22 | Entradas válidas com total possuindo mais de duas casas decimais | Retornar o total com duas casas usando arredondamento `HALF_UP` |

### Análise caixa-branca do grupo

> Orientação: Usem o código de produção. Na primeira coluna, indiquem o método e o trecho ou a decisão. Na segunda, descrevam em linguagem natural como a decisão, condição ou caminho funciona. Na terceira, indiquem quais resultados de decisão, ramos ou caminhos a suíte deve exercitar. Se houver curto-circuito, indiquem quando uma condição posterior chega a ser avaliada.

| Método ou trecho | Decisão, condição ou caminho | Obrigação de cobertura |
|---|---|---|
| `calcularCustoLocacao`, validação de referências | A primeira decisão usa `categoria == null |  |
| `calcularCustoLocacao`, validação de diárias | A decisão verifica `numeroDiarias <= 0`. | Exercitar a condição verdadeira com quantidade não positiva e a condição falsa com quantidade positiva. |
| `calcularCustoLocacao`, validação de quilometragem | A decisão verifica `quilometrosRodados < 0`. | Exercitar a condição verdadeira com quilometragem negativa e a condição falsa com quilometragem não negativa. |
| `calcularCustoLocacao`, validação de atraso | A decisão verifica `horasAtraso < 0`. | Exercitar a condição verdadeira com atraso negativo e a condição falsa com atraso não negativo. |
| `calcularCustoLocacao`, escolha da diária | A cadeia seleciona `ECONOMICO`, depois `INTERMEDIARIO`, e usa o ramo final para `SUV`. | Exercitar os três resultados de seleção e confirmar os valores de diária de R$ 120, R$ 180 e R$ 280. |
| `calcularCustoLocacao`, desconto por duração | A cadeia testa `numeroDiarias <= 2`, depois `<= 6`, depois `<= 14`, e usa o ramo final para quantidades maiores. | Exercitar os quatro ramos: 0%, 5%, 10% e 15%, incluindo os limites que mudam de faixa. |
| `calcularCustoLocacao`, desconto de fidelidade Prata | A decisão composta exige simultaneamente nível `PRATA`, pelo menos 7 diárias e ausência de atraso anterior. | Exercitar a decisão verdadeira e cada condição falsa separadamente: nível diferente, menos de 7 diárias e atraso anterior presente. |
| `calcularCustoLocacao`, curto-circuito do desconto Prata | As condições são avaliadas da esquerda para a direita; se o nível não for `PRATA`, as condições de diárias e atraso anterior podem não ser avaliadas para esse ramo. | Cobrir o curto-circuito com nível não Prata e também executar as condições posteriores em uma entrada Prata. |
| `calcularCustoLocacao`, desconto de fidelidade Ouro | O ramo `else if` exige nível `OURO`, pelo menos 5 diárias e ausência de atraso anterior, mas só é alcançado quando a condição Prata é falsa. | Exercitar o ramo verdadeiro do Ouro e cada motivo de falsidade: nível diferente, menos de 5 diárias e atraso anterior presente. |
| `calcularCustoLocacao`, ausência de desconto de fidelidade | O ramo final atribui taxa zero quando nenhum dos benefícios de Prata ou Ouro é aplicável. | Exercitar o ramo sem desconto para cliente Comum, nível abaixo do limite e cliente com atraso anterior. |
| `calcularCustoLocacao`, cálculo da franquia | A franquia é calculada como `100L * numeroDiarias`, e o excedente é a quilometragem total menos essa franquia. | Exercitar quilometragem abaixo da franquia, exatamente na franquia e acima da franquia; incluir quantidade de diárias válida suficiente para verificar a multiplicação. |
| `calcularCustoLocacao`, cobrança de quilometragem | A decisão `quilometrosExcedentes <= 0` separa ausência de cobrança de cobrança do excedente. | Exercitar os ramos verdadeiro e falso. |
| `calcularCustoLocacao`, tarifa de quilometragem baixa | Para excedente positivo, a decisão `quilometrosExcedentes <= 100` seleciona R$ 0,80/km. | Exercitar os resultados verdadeiro e falso, incluindo excedente exatamente 100 e 101 km. |
| `calcularCustoLocacao`, tarifa de quilometragem intermediária | A decisão `quilometrosExcedentes <= 300` seleciona R$ 1,00/km quando a primeira faixa não foi escolhida. | Exercitar os resultados verdadeiro e falso, incluindo excedente exatamente 300 e 301 km. |
| `calcularCustoLocacao`, tarifa de quilometragem alta | O ramo final seleciona R$ 1,50/km para excedente maior que 300 km. | Exercitar o ramo final com excedente acima de 300 km. |
| `calcularCustoLocacao`, seleção do seguro | A cadeia seleciona `SEM_SEGURO`, depois `BASICO`, e usa o ramo final para `COMPLETO`. | Exercitar os três ramos e confirmar custos de R$ 0, R$ 25 e R$ 45 por diária. |
| `calcularCustoLocacao`, cálculo do seguro | O custo por diária é multiplicado pelo número de diárias. | Exercitar pelo menos uma quantidade de diárias em cada faixa de duração e verificar que o custo acompanha a quantidade contratada. |
| `calcularCustoLocacao`, cobrança de atraso isento | A decisão `horasAtraso <= 1` atribui custo zero. | Exercitar os resultados verdadeiro e falso, incluindo 0 e 1 hora. |
| `calcularCustoLocacao`, cobrança de atraso por hora | A decisão `horasAtraso <= 3` é alcançada quando o atraso é maior que 1 e aplica 20% da diária sem desconto multiplicado pelas horas. | Exercitar os resultados verdadeiro e falso, incluindo 2, 3 e 4 horas. |
| `calcularCustoLocacao`, atraso máximo | O ramo final cobra uma diária sem desconto para atraso maior que 3 horas. | Exercitar o ramo final com atraso de pelo menos 4 horas. |
| `calcularCustoLocacao`, composição do total | O total soma diárias finais, quilometragem, seguro e atraso. | Exercitar combinações em que cada componente é zero e combinações em que todos os componentes são positivos, verificando a soma. |
| `calcularCustoLocacao`, arredondamento final | O retorno aplica `setScale(2, RoundingMode.HALF_UP)` somente ao total final. | Exercitar um total com mais de duas casas e verificar duas casas no retorno e arredondamento metade para cima. |

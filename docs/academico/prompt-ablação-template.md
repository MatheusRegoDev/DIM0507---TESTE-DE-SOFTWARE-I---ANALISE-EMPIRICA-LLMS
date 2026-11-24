Você é especialista em testes unitários Java com JUnit 5. Use o material fornecido para projetar os casos de teste, escolher as entradas e determinar os resultados ou exceções esperados. Responda somente com o código Java completo da suíte JUnit 5, incluindo o pacote e os imports necessários. Entregue o código em texto puro, sem cercas de Markdown nem explicações.

# Consolidação parcial acadêmica

## API pública

Classe `ConsolidacaoParcial` no pacote `br.com.academico`.

```java
public static SituacaoAcademica consolidarParcialmente(
    Float nota1, Float nota2, Float nota3, Integer frequencia)
```

O enum de retorno `SituacaoAcademica` contém `REMF`, `REPF`, `REP`, `APR`, `APRN` e `REC`.

## Código

`src/main/java/br/com/academico/ConsolidacaoParcial.java`

```java
package br.com.academico;

import java.math.BigDecimal;
import java.math.RoundingMode;
import java.util.Objects;

public class ConsolidacaoParcial {
    public static SituacaoAcademica consolidarParcialmente(
            Float nota1, Float nota2, Float nota3, Integer frequencia) {
        Objects.requireNonNull(frequencia, "frequencia");
        if (frequencia < 0 || frequencia > 100) {
            throw new IllegalArgumentException("Frequência deve estar entre 0 e 100");
        }

        BigDecimal primeira = arredondarNota(nota1);
        BigDecimal segunda = arredondarNota(nota2);
        BigDecimal terceira = arredondarNota(nota3);
        BigDecimal media = primeira.add(segunda).add(terceira)
                .divide(BigDecimal.valueOf(3), 1, RoundingMode.HALF_UP);

        if (media.compareTo(BigDecimal.valueOf(3)) < 0) {
            return frequencia < 75 ? SituacaoAcademica.REMF : SituacaoAcademica.REP;
        }
        if (frequencia < 75) {
            return SituacaoAcademica.REPF;
        }
        if (media.compareTo(BigDecimal.valueOf(7)) >= 0) {
            return SituacaoAcademica.APR;
        }
        BigDecimal menorNota = primeira.min(segunda).min(terceira);
        if (media.compareTo(BigDecimal.valueOf(5)) >= 0 && menorNota.compareTo(BigDecimal.valueOf(3)) >= 0) {
            return SituacaoAcademica.APRN;
        }
        return SituacaoAcademica.REC;
    }

    private static BigDecimal arredondarNota(Float nota) {
        Objects.requireNonNull(nota, "nota");
        if (!Float.isFinite(nota) || nota < 0 || nota > 10) {
            throw new IllegalArgumentException("Nota deve ser finita e estar entre 0 e 10");
        }
        return new BigDecimal(nota.toString()).setScale(1, RoundingMode.HALF_UP);
    }
}
```

`src/main/java/br/com/academico/SituacaoAcademica.java`

```java
package br.com.academico;

public enum SituacaoAcademica {
    REPF, REP, REC, APR, APRN, REMF
}
```

## Especificação

### Entradas válidas

As três notas devem ser finitas e pertencer a [0, 10]. A frequência, em porcentagem inteira já calculada sobre a base aplicável, deve pertencer a [0, 100].

### Resultados e exceções

Cada nota é arredondada para uma casa decimal com `HALF_UP`. A média aritmética dessas três notas também é arredondada para uma casa decimal com `HALF_UP`. As decisões usam os valores arredondados.

| Condição | `SituacaoAcademica` |
|---|---|
| Frequência abaixo de 75% e média menor que 3 | `REMF` — reprovado por média e faltas |
| Frequência abaixo de 75% e média de pelo menos 3 | `REPF` — reprovado por faltas |
| Frequência de pelo menos 75% e média menor que 3 | `REP` — reprovado por média |
| Frequência de pelo menos 75% e média de pelo menos 7 | `APR` — aprovado por média |
| Frequência de pelo menos 75%, média de 5 até abaixo de 7 e todas as notas de pelo menos 3 | `APRN` — aprovado por notas |
| Frequência de pelo menos 75% e demais casos com média de pelo menos 3 | `REC` — em recuperação |

Para entradas com um único argumento inválido, nulos provocam `NullPointerException` e valores fora do domínio, incluindo notas não finitas, provocam `IllegalArgumentException`.

## Análise

# Critérios de teste — academico

> Orientação: Substituam todos os campos entre colchetes. Acrescentem uma linha por classe, fronteira, regra ou obrigação de cobertura. Se um critério não se aplicar, substituam sua tabela por uma justificativa breve.

> Orientação: Registrem somente os critérios e seus resultados de análise. A LLM escolhe os valores de entrada para cada caso, determina o resultado esperado e escreve os testes JUnit.

### Análise caixa-preta do grupo

> Orientação: Usem a especificação como fonte. Considerem notas, frequência, arredondamento e as regras de classificação da situação acadêmica.

#### Classes de equivalência

> Orientação: Informem o parâmetro ou regra, deem um nome à classe e descrevam a faixa ou propriedade que inclui seus membros. Registrem classes válidas e inválidas quando previstas.

| Parâmetro ou regra | Classe de equivalência | Domínio ou condição |
|---|---|---|
| Nota 1, nota 2 ou nota 3 | Nota válida no limite inferior | Valor finito igual a `0`, pertencente ao intervalo válido |
| Nota 1, nota 2 ou nota 3 | Nota válida no interior | Valor finito maior que `0` e menor que `10` |
| Nota 1, nota 2 ou nota 3 | Nota válida no limite superior | Valor finito igual a `10`, pertencente ao intervalo válido |
| Nota 1, nota 2 ou nota 3 | Nota nula | `null`; deve provocar `NullPointerException` |
| Nota 1, nota 2 ou nota 3 | Nota não finita | `NaN`, `Float.POSITIVE_INFINITY` ou `Float.NEGATIVE_INFINITY`; deve provocar `IllegalArgumentException` |
| Nota 1, nota 2 ou nota 3 | Nota abaixo do domínio | Valor menor que `0`; deve provocar `IllegalArgumentException` |
| Nota 1, nota 2 ou nota 3 | Nota acima do domínio | Valor maior que `10`; deve provocar `IllegalArgumentException` |
| Nota 1, nota 2 ou nota 3 | Nota que não altera a casa decimal | Valor cuja representação já tem uma casa decimal ou menos; arredondamento mantém o valor nessa escala |
| Nota 1, nota 2 ou nota 3 | Nota com arredondamento para baixo | Segunda casa decimal menor que 5; arredondar para uma casa com `HALF_UP` sem elevar a primeira casa decimal |
| Nota 1, nota 2 ou nota 3 | Nota com arredondamento de metade para cima | Segunda casa decimal igual a 5; elevar a primeira casa decimal conforme `HALF_UP` |
| Nota 1, nota 2 ou nota 3 | Nota com arredondamento para cima | Segunda casa decimal maior que 5; elevar a primeira casa decimal |
| Frequência | Frequência válida mínima | Inteiro igual a `0`, pertencente ao intervalo `[0,100]` |
| Frequência | Frequência válida intermediária | Inteiro entre `0` e `100` |
| Frequência | Frequência válida máxima | Inteiro igual a `100`, pertencente ao intervalo válido |
| Frequência | Frequência nula | `null`; deve provocar `NullPointerException` |
| Frequência | Frequência abaixo do domínio | Inteiro menor que `0`; deve provocar `IllegalArgumentException` |
| Frequência | Frequência acima do domínio | Inteiro maior que `100`; deve provocar `IllegalArgumentException` |
| Média arredondada | Média menor que 3 | Média arredondada inferior a `3,0`; participa de `REMF` ou `REP` conforme a frequência |
| Média arredondada | Média de pelo menos 3 e menor que 5 | Média em `[3,0, 5,0)`; com frequência suficiente, resulta em `REC` |
| Média arredondada | Média de pelo menos 5 e menor que 7 com notas suficientes | Média em `[5,0, 7,0)`, todas as notas arredondadas pelo menos `3,0`; resulta em `APRN` |
| Média arredondada | Média de pelo menos 5 e menor que 7 com nota baixa | Média em `[5,0, 7,0)`, mas alguma nota arredondada menor que `3,0`; resulta em `REC` quando a frequência é suficiente |
| Média arredondada | Média de pelo menos 7 | Média arredondada maior ou igual a `7,0`; resulta em `APR` quando a frequência é suficiente |
| Frequência e média | Média baixa com frequência insuficiente | Frequência menor que `75` e média menor que `3,0`; resulta em `REMF` |
| Frequência e média | Média suficiente com frequência insuficiente | Frequência menor que `75` e média maior ou igual a `3,0`; resulta em `REPF` |
| Frequência e média | Média baixa com frequência suficiente | Frequência maior ou igual a `75` e média menor que `3,0`; resulta em `REP` |
| Frequência e média | Frequência suficiente | Frequência maior ou igual a `75`; permite `APR`, `APRN` ou `REC` conforme a média e as notas |

#### Valores-limite

> Orientação: Indiquem o valor onde muda a validade ou o comportamento e descrevam as classes que ele separa. Esclareçam a qual classe pertence o próprio limite.

| Parâmetro ou regra | Valor-limite | Classes separadas e inclusão do limite |
|---|---|---|
| Domínio da nota | `-0,1 / 0 / 10 / 10,1` | `-0,1` é inválido; `0` é válido; `10` é válido; `10,1` é inválido |
| Finitude da nota | `-∞ / NaN / +∞` | Todos os valores não finitos são inválidos e provocam `IllegalArgumentException` |
| Domínio da frequência | `-1 / 0 / 100 / 101` | `-1` e `101` são inválidos; `0` e `100` pertencem ao intervalo válido |
| Frequência de faltas | `74 / 75` | `74` pertence à classe abaixo de 75%; `75` pertence à classe de frequência suficiente |
| Média para reprovação por média | `2,9 / 3,0 / 3,1` | Média `2,9` é menor que 3; `3,0` e `3,1` não são menores que 3 |
| Média para aprovação por notas | `4,9 / 5,0 / 5,1` | `4,9` não alcança a faixa de `APRN`; `5,0` e `5,1` alcançam o limite inferior, se as notas permitirem |
| Média para aprovação por média | `6,9 / 7,0 / 7,1` | `6,9` pode ser `APRN` ou `REC`; `7,0` e `7,1` resultam em `APR` com frequência suficiente |
| Nota individual para `APRN` | `2,9 / 3,0 / 3,1` | Uma nota `2,9` impede `APRN`; `3,0` e `3,1` satisfazem a condição de menor nota pelo menos 3 |
| Arredondamento de nota | Segunda casa `4 / 5 / 6` | A segunda casa 4 arredonda para baixo; 5 arredonda para cima por `HALF_UP`; 6 também arredonda para cima |
| Arredondamento da média | Média antes do arredondamento imediatamente abaixo, em e acima de um limite | A média usada nas decisões é a média arredondada para uma casa; deve ser testada a mudança em `3,0`, `5,0` e `7,0` |
| Nota arredondada antes da média | Nota que passa de `2,9` para `3,0` ou de `2,99` para `3,0` | A condição de todas as notas pelo menos 3 usa a nota já arredondada; `3,0` satisfaz a condição |
| Prioridade das decisões | Média menor que `3` com qualquer frequência | A média menor que 3 é avaliada antes das demais regras; retorna `REMF` se frequência <75 ou `REP` se frequência >=75 |
| Prioridade das decisões | Frequência menor que `75` com média >=3 | Retorna `REPF`, independentemente de a média estar na faixa de `APRN` |

#### Tabela de decisão

> Orientação: Uma linha descreve uma combinação geral de condições e a ação que a especificação prevê. Usem estados ou categorias, como condição satisfeita, condição insatisfeita ou indiferente, quando couber.

| Regra | Combinação de condições | Ação prevista na especificação |
|---|---|---|
| ACA-01 | Frequência nula | Rejeitar com `NullPointerException` |
| ACA-02 | Alguma nota é nula | Rejeitar com `NullPointerException` |
| ACA-03 | Frequência menor que 0 ou maior que 100 | Rejeitar com `IllegalArgumentException` |
| ACA-04 | Alguma nota é não finita ou está fora de `[0,10]` | Rejeitar com `IllegalArgumentException` |
| ACA-05 | Frequência abaixo de 75% e média arredondada menor que 3 | Retornar `REMF` |
| ACA-06 | Frequência abaixo de 75% e média arredondada maior ou igual a 3 | Retornar `REPF` |
| ACA-07 | Frequência de pelo menos 75% e média arredondada menor que 3 | Retornar `REP` |
| ACA-08 | Frequência de pelo menos 75% e média arredondada maior ou igual a 7 | Retornar `APR` |
| ACA-09 | Frequência de pelo menos 75%, média arredondada entre 5 inclusive e 7 exclusive, e todas as notas arredondadas de pelo menos 3 | Retornar `APRN` |
| ACA-10 | Frequência de pelo menos 75%, média arredondada maior ou igual a 3, mas média menor que 5 ou alguma nota arredondada menor que 3, após excluir média >=7 | Retornar `REC` |
| ACA-11 | Nota ou média precisa ser arredondada antes da decisão | Arredondar cada nota para uma casa com `HALF_UP`, calcular a média dessas notas e arredondar a média para uma casa com `HALF_UP` |
| ACA-12 | Frequência exatamente 75% e média em qualquer faixa válida | Tratar como frequência suficiente, nunca como frequência abaixo de 75% |
| ACA-13 | Média exatamente 3,0 | Não classificar como média menor que 3; aplicar `REPF`, `REC` ou outra regra conforme frequência e demais condições |
| ACA-14 | Média exatamente 5,0 e todas as notas pelo menos 3 | Permitir `APRN` quando a frequência for pelo menos 75% |
| ACA-15 | Média exatamente 7,0 e frequência pelo menos 75% | Retornar `APR`, antes da verificação de `APRN` |

### Análise caixa-branca do grupo

> Orientação: Usem o código de produção. Na primeira coluna, indiquem o método e o trecho ou a decisão. Na segunda, descrevam em linguagem natural como a decisão, condição ou caminho funciona. Na terceira, indiquem quais resultados de decisão, ramos ou caminhos a suíte deve exercitar. Se houver curto-circuito, indiquem quando uma condição posterior chega a ser avaliada.

| Método ou trecho | Decisão, condição ou caminho | Obrigação de cobertura |
|---|---|---|
| `consolidarParcialmente`, validação de frequência nula | `Objects.requireNonNull(frequencia)` é executado antes das demais validações. | Exercitar frequência nula e confirmar `NullPointerException`; também executar o caminho com frequência presente. |
| `consolidarParcialmente`, faixa da frequência | A decisão `frequencia < 0 |  |
| `arredondarNota`, validação de nota nula | `Objects.requireNonNull(nota)` é executado para cada nota. | Exercitar nota nula em cada uma das três posições e confirmar `NullPointerException`. |
| `arredondarNota`, finitude e domínio da nota | A decisão `!Float.isFinite(nota) |  |
| `arredondarNota`, arredondamento individual | A nota é convertida para `BigDecimal` e recebe escala 1 com `RoundingMode.HALF_UP`. | Exercitar arredondamento para baixo, metade exata e para cima; verificar que a nota arredondada é usada posteriormente. |
| `consolidarParcialmente`, cálculo da média | A média é calculada a partir das três notas já arredondadas e depois recebe escala 1 com `HALF_UP`. | Exercitar média exata, média com arredondamento para baixo e média com arredondamento para cima, especialmente nos limites 3, 5 e 7. |
| `consolidarParcialmente`, média menor que 3 | A primeira decisão verifica `media.compareTo(3) < 0`. | Exercitar resultado verdadeiro com frequência abaixo de 75 e com frequência de pelo menos 75; exercitar resultado falso com média exatamente 3 ou maior. |
| `consolidarParcialmente`, retorno de média baixa | Dentro da média menor que 3, o operador ternário verifica `frequencia < 75`. | Cobrir o retorno `REMF` e o retorno `REP`, incluindo frequência 74 e 75. |
| `consolidarParcialmente`, frequência insuficiente após média suficiente | Depois de excluir média menor que 3, a decisão verifica `frequencia < 75`. | Exercitar o ramo verdadeiro com média pelo menos 3 e retornar `REPF`; exercitar o ramo falso com frequência pelo menos 75. |
| `consolidarParcialmente`, média para aprovação por média | Com frequência suficiente, a decisão verifica `media >= 7`. | Exercitar verdadeiro com média 7 ou maior e falso com média abaixo de 7; confirmar que `APR` tem prioridade sobre `APRN`. |
| `consolidarParcialmente`, menor nota | O código calcula `primeira.min(segunda).min(terceira)` para avaliar o menor valor arredondado. | Exercitar cada posição como menor nota e verificar o limite 3,0; a menor nota abaixo de 3 deve impedir `APRN`. |
| `consolidarParcialmente`, aprovação por notas | A condição composta exige `media >= 5 && menorNota >= 3`. Ela só é alcançada quando a média é pelo menos 3 e menor que 7. | Exercitar os resultados verdadeiro e falso; cobrir média abaixo de 5, menor nota abaixo de 3, média entre 5 e 7 com menor nota 3 ou maior e curto-circuito quando `media >= 5` for falso. |
| `consolidarParcialmente`, retorno de recuperação | O ramo final é alcançado quando frequência é suficiente, média é pelo menos 3, média é menor que 7 e a condição de `APRN` falha. | Exercitar `REC` por média entre 3 e 5 e por média entre 5 e 7 com alguma nota menor que 3. |
| `consolidarParcialmente`, ordem dos retornos | A ordem efetiva é: média menor que 3; frequência menor que 75; média pelo menos 7; média pelo menos 5 e menor nota pelo menos 3; recuperação. | Exercitar combinações que satisfazem mais de uma descrição e confirmar o primeiro ramo aplicável. |
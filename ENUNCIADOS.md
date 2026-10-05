# Enunciados completos (substituem os da PLATAFORMA_EXERCICIOS.md)

Instruções para o Claude Code: atualize `enunciado`, `template`, `testes` e `proibido` de cada `questoes/qN.json` com o conteúdo abaixo. Renderize o enunciado como markdown (títulos, listas numeradas, blocos de código). Em **todas** as questões, `proibido` inclui `min, max, sum, sorted, filter, map`; a Q1 também inclui `str`; a Q2 mantém `sem_fatiamento`; a Q4 mantém `nao_altera_args`. Valide de novo com soluções de referência e apague-as.

---

## Q1 — Cartão válido

Números de cartão de crédito terminam com um dígito de controle calculado pelo **algoritmo de Luhn**, que permite detectar a maioria dos erros de digitação antes mesmo de consultar o banco. Por exemplo, `79927398713` é um número válido e `79927398710` não é.

Implemente a função `valida_cartao` que recebe um número inteiro não negativo e devolve `True` se ele for válido pelo algoritmo de Luhn e `False` caso contrário.

A função deve operar da seguinte forma:

1. Considere os dígitos do número da **direita para a esquerda**.
2. O dígito mais à direita é mantido. O segundo é **dobrado**, o terceiro é mantido, o quarto é dobrado, e assim por diante, **alternando**.
3. Sempre que um dígito dobrado resultar em um valor **maior que 9**, subtraia `9` dele.
4. Some todos os valores obtidos (mantidos e dobrados).
5. O número é válido se a soma for **divisível por 10**.

### Exemplo de raciocínio
Considere o número `79927398713`.
- Dígitos da direita para a esquerda: `3, 1, 7, 8, 9, 3, 7, 2, 9, 9, 7`
- Dobrando um sim, outro não, a partir do segundo:
  - `3` → mantido → `3`
  - `1` → dobrado → `2`
  - `7` → mantido → `7`
  - `8` → dobrado → `16` → `16 - 9 = 7`
  - `9` → mantido → `9`
  - `3` → dobrado → `6`
  - `7` → mantido → `7`
  - `2` → dobrado → `4`
  - `9` → mantido → `9`
  - `9` → dobrado → `18` → `18 - 9 = 9`
  - `7` → mantido → `7`
- Soma: `70`. Como `70 % 10 == 0`, a função retorna `True`.

### Exemplos
```python
print(valida_cartao(79927398713))   # True
print(valida_cartao(79927398710))   # False
print(valida_cartao(18))            # True
```

### Restrições e observações
- O valor recebido será sempre um inteiro maior ou igual a zero.
- O número `0` tem um único dígito, e a soma é `0`.
- Não é permitido converter o número em texto nem usar funções de strings.
- É proibido o uso das funções `min`, `max`, `sum`, `sorted`, `filter`, `map` e `str`.

Testes: `79927398713→True`, `79927398710→False`, `18→True`, `12→False`, `0→True`, `4111111111111111→True`, `4111111111111112→False`

---

## Q2 — Oscilação de preços

Uma corretora quer avisar seus clientes quando uma ação está **instável**. Para isso, ela analisa os preços de fechamento em **janelas deslizantes**: grupos de `k` dias consecutivos, em que cada janela começa um dia depois da anterior. A **amplitude** de uma janela é a diferença entre o maior e o menor preço dentro dela.

Sua tarefa é criar uma função chamada `amplitudes` que receba:
- uma lista `precos` com valores inteiros positivos, na ordem dos dias;
- um inteiro `k`, indicando quantos dias consecutivos formam uma janela.

A função deve retornar uma lista com a amplitude de **cada janela**, na ordem em que as janelas aparecem.

### Regras importantes
- A primeira janela começa no índice `0`, a segunda no índice `1`, e assim por diante, até a última janela que ainda caiba inteira na lista.
- Se `k` for maior que o tamanho da lista, nenhuma janela cabe e a função deve retornar uma lista vazia.
- Se a lista estiver vazia, a função deve retornar uma lista vazia.
- Você pode assumir que `k` é um inteiro maior que 0.

### Exemplo 1
```python
precos = [3, 1, 4, 1, 5]
k = 3
```
As janelas são:
- `[3, 1, 4]` → maior `4`, menor `1` → amplitude `3`
- `[1, 4, 1]` → maior `4`, menor `1` → amplitude `3`
- `[4, 1, 5]` → maior `5`, menor `1` → amplitude `4`

Logo, a função deve retornar `[3, 3, 4]`.

### Exemplo 2
```python
precos = [7, 2, 9, 4]
k = 1
```
Cada janela tem um único preço, então toda amplitude é `0`. Retorno: `[0, 0, 0, 0]`.

### Exemplo 3
```python
precos = [1, 2]
k = 3
```
Nenhuma janela de 3 dias cabe em 2 dias. Retorno: `[]`.

É proibido o uso das funções `min`, `max`, `sum`, `sorted`, `filter` e `map`, e também o fatiamento de listas (`lista[a:b]`).

Testes: `([3,1,4,1,5],3)→[3,3,4]`, `([10,10,10],2)→[0,0]`, `([1,2],3)→[]`, `([7,2,9,4],1)→[0,0,0,0]`, `([],2)→[]`, `([5,1,9],3)→[8]`

---

## Q3 — Monitor da estufa (`tipo: programa`)

Uma estufa de plantas tem sensores que medem a temperatura e a umidade do ar. Quando o ambiente fica crítico várias vezes seguidas, o sistema precisa disparar um alerta para que a equipe intervenha antes que as plantas sejam prejudicadas.

Escreva um programa que acompanhe as leituras dos sensores. O programa deve perguntar ao usuário repetidamente:

1. A temperatura medida, com a mensagem `Temperatura (C): `.
2. Se a temperatura for válida, a umidade medida, com a mensagem `Umidade (%): `.

Ao longo da execução, o programa deve contabilizar o total de leituras válidas, o total de leituras críticas e a soma das temperaturas válidas.

### Valores inválidos
- Se a temperatura estiver fora do intervalo de `-50` a `60` (inclusive), o programa deve imprimir `"temperatura invalida"` e **não** perguntar a umidade.
- Se a umidade estiver fora do intervalo de `0` a `100` (inclusive), o programa deve imprimir `"umidade invalida"`.

Leituras com temperatura ou umidade inválidas devem ser ignoradas, e o programa passa para a próxima leitura. Leituras ignoradas não são contabilizadas.

### Leituras críticas
- Uma leitura é considerada **crítica** quando pelo menos uma destas situações acontece:
  - a temperatura é maior que `40`;
  - a umidade é menor que `20`.

O programa também deve contabilizar quantas leituras críticas aparecem **uma logo depois da outra**.
- Quando aparece uma leitura válida não crítica, essa contagem volta para `0`.
- Leituras ignoradas não aumentam nem zeram essa contagem.
- Se aparecerem **3 leituras críticas seguidas**, o programa deve imprimir um alerta e parar imediatamente:

```
"alerta: P leituras criticas em N leituras"
```
Em que `P` é o total de leituras críticas e `N` é o total de leituras válidas.

### Encerramento (quando o usuário digitar `-999`)
Se a temperatura informada for `-999`, o programa não pergunta a umidade, imprime um resumo e para.
- Se nenhuma leitura válida foi registrada: `"nenhuma leitura registrada"`
- Caso contrário: `"monitoramento encerrado: media X C em N leituras, P criticas"`, com a média das temperaturas válidas com **uma casa decimal**.

### Exemplo 1
```
> Temperatura (C): 42
> Umidade (%): 50
> Temperatura (C): 70
temperatura invalida
> Temperatura (C): 38
> Umidade (%): 10
> Temperatura (C): 45
> Umidade (%): 120
umidade invalida
> Temperatura (C): 41
> Umidade (%): 30
alerta: 3 leituras criticas em 3 leituras
```

### Exemplo 2
```
> Temperatura (C): 41
> Umidade (%): 50
> Temperatura (C): 20
> Umidade (%): 50
> Temperatura (C): 45
> Umidade (%): 10
> Temperatura (C): 39
> Umidade (%): 15
> Temperatura (C): 50
> Umidade (%): 30
alerta: 4 leituras criticas em 5 leituras
```

### Exemplo 3
```
> Temperatura (C): 25
> Umidade (%): 50
> Temperatura (C): 30
> Umidade (%): 60
> Temperatura (C): -999
monitoramento encerrado: media 27.5 C em 2 leituras, 0 criticas
```

É proibido o uso das funções `min`, `max`, `sum`, `sorted`, `filter` e `map`.

Testes (entradas → saída):
- `25,50,30,60,-999` → `monitoramento encerrado: media 27.5 C em 2 leituras, 0 criticas`
- `-999` → `nenhuma leitura registrada`
- `42,50,70,38,10,45,120,41,30` → `temperatura invalida` / `umidade invalida` / `alerta: 3 leituras criticas em 3 leituras`
- `41,50,20,50,45,10,39,15,50,30,-999` → `alerta: 4 leituras criticas em 5 leituras`
- `100,25,150,-999` → `temperatura invalida` / `umidade invalida` / `nenhuma leitura registrada`

---

## Q4 — Surto na cidade

A secretaria de saúde representa os quarteirões de uma cidade em uma lista de listas, onde cada sublista é uma rua. Cada quarteirão pode estar:
- `0`: sem casos;
- `1`: com um foco de infecção;
- `2`: isolado (área bloqueada, que não recebe nem transmite a infecção).

A secretaria quer prever **como o surto estará amanhã**: todo quarteirão sem casos (`0`) que seja vizinho de um foco (`1`) na horizontal (esquerda e direita) ou na vertical (cima e baixo) passa a ser um foco. Quarteirões na diagonal **não** contam.

Escreva uma função chamada `propaga` que recebe esse mapa e retorna uma **nova lista de listas**, com as mesmas dimensões, representando o dia seguinte.

**Atenção:** a propagação acontece **uma única vez** e considera apenas os focos do mapa **original**. Um quarteirão que acabou de ser infectado não infecta seus vizinhos no mesmo dia.

### Exemplo 1
Entrada:
```
[
  [0, 0, 0],
  [0, 1, 0],
  [0, 0, 0]
]
```
Saída:
```
[
  [0, 1, 0],
  [1, 1, 1],
  [0, 1, 0]
]
```
Entendendo o canto superior esquerdo: seus vizinhos são o da direita (`0`) e o de baixo (`0`), nenhum é foco, então continua `0`. Já o quarteirão do meio da primeira linha tem o foco `1` logo abaixo, então passa a ser `1`.

### Exemplo 2
Entrada: `[[1, 0, 0, 0]]` → Saída: `[[1, 1, 0, 0]]`

O terceiro quarteirão não é infectado, porque seu vizinho só virou foco neste mesmo dia.

### Exemplo 3
Entrada: `[[1, 2], [0, 0]]` → Saída: `[[1, 2], [1, 0]]`

O `2` é isolado e não muda. O quarteirão de baixo à direita só é vizinho do `2` e do `0`, então continua `0`.

### Restrições
- A entrada será sempre uma lista de listas válida, com pelo menos uma linha e uma coluna.
- As sublistas terão o mesmo comprimento.
- O mapa recebido não pode ser modificado.
- É proibido o uso das funções `min`, `max`, `sum`, `sorted`, `filter` e `map`.

Testes:
- `[[0,0,0],[0,1,0],[0,0,0]]→[[0,1,0],[1,1,1],[0,1,0]]`
- `[[1,0,0,0]]→[[1,1,0,0]]`
- `[[1,2],[0,0]]→[[1,2],[1,0]]`
- `[[0,0],[0,0]]→[[0,0],[0,0]]`
- `[[1,2,0],[0,2,0]]→[[1,2,0],[1,2,0]]`

---

## Q5 — Dupla de compras

Um aplicativo de cashback oferece um bônus quando o cliente compra **exatamente dois produtos** cujos preços somam um valor-alvo. Antes de lançar a promoção, a equipe quer saber quais combinações de produtos do catálogo atingem esse valor.

Escreva uma função chamada `pares_soma` que receba:
- uma lista `precos` com valores inteiros, representando o catálogo;
- um inteiro `alvo`, o valor que a dupla precisa somar.

A função deve retornar uma lista com **todos os pares de índices** `[i, j]` tais que `precos[i] + precos[j] == alvo`.

### Regras importantes
- Em cada par, o primeiro índice deve ser **menor** que o segundo (`i < j`). Um produto não pode formar par com ele mesmo.
- Os pares devem aparecer em ordem crescente de `i` e, para o mesmo `i`, em ordem crescente de `j`.
- Produtos com o mesmo preço em posições diferentes contam como produtos diferentes.
- Se nenhum par atingir o alvo, ou a lista estiver vazia, a função deve retornar uma lista vazia.

### Exemplo 1
```python
precos = [1, 4, 3, 2]
alvo = 5
```
Testando todos os pares:
- `[0, 1]` → `1 + 4 = 5` ✔
- `[0, 2]` → `1 + 3 = 4`
- `[0, 3]` → `1 + 2 = 3`
- `[1, 2]` → `4 + 3 = 7`
- `[1, 3]` → `4 + 2 = 6`
- `[2, 3]` → `3 + 2 = 5` ✔

Retorno: `[[0, 1], [2, 3]]`

### Exemplo 2
```python
precos = [2, 2, 2]
alvo = 4
```
Todos os pares somam 4. Retorno: `[[0, 1], [0, 2], [1, 2]]`

É proibido o uso das funções `min`, `max`, `sum`, `sorted`, `filter` e `map`.

Testes: `([1,4,3,2],5)→[[0,1],[2,3]]`, `([2,2,2],4)→[[0,1],[0,2],[1,2]]`, `([1,1],5)→[]`, `([],0)→[]`, `([3],6)→[]`

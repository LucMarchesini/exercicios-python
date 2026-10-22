# Enunciados: Listas e loops (Q1 a Q5)

Os alunos só viram: introdução; operações matemáticas, variáveis e funções; `print`, `input`, `int`, `float`; `if/elif/else`; `while`; listas; `for` (incluindo `range`); dicionários. Nenhuma questão exige algo fora disso. Strings aparecem só como valores inteiros: comparadas com `==`/`!=`, usadas como chave, guardadas em listas, impressas e concatenadas com `+` ou f-string.

Em todos os `questoes/qN.json`:
- `proibido` = `min, max, sum, sorted, filter, map, enumerate, zip, tuple, set`
- `metodos_proibidos` = `split, join, upper, lower, strip, replace, find, index, count` (a Q1 também proíbe `copy`)
- `construcoes_proibidas` = `tupla, set, list comprehension, lambda, try/except, import`
- `sem_fatiamento` = `true`

O código é barrado (nota 0) com a mensagem `Uso proibido: X (linha N)`.

Q1 e Q2 têm `nao_altera_args`.

---

## Q1 — O sapo e as vitórias-régias

Um sapo está em um rio com uma fileira de vitórias-régias, representada por uma lista de inteiros. O sapo começa na vitória-régia de **índice 0**. Cada vitória-régia tem um número que indica quantas posições o sapo pula a partir dela: valores positivos levam para a direita, negativos para a esquerda e `0` faz o sapo pular no mesmo lugar.

O detalhe é que as vitórias-régias são frágeis: **toda vez que o sapo sai de uma delas, o número dela aumenta em 1**.

Implemente a função `pulos_ate_sair` que recebe a lista de vitórias-régias e retorna **quantos pulos** o sapo dá até cair fora da fileira (índice menor que `0` ou maior que o último).

A função deve operar da seguinte forma:

1. Comece com o sapo na posição `0` e o contador de pulos em `0`.
2. Enquanto o sapo estiver dentro da fileira:
   1. Leia o valor da vitória-régia atual: esse é o tamanho do pulo.
   2. Aumente em `1` o valor dessa vitória-régia.
   3. Mova o sapo pelo tamanho do pulo lido no passo 1.
   4. Some `1` ao contador.
3. Retorne o contador.

### Exemplo de raciocínio
Considere `[0, 3, 0, 1, -3]`.

| Pulo | Posição | Valor lido | Lista depois | Nova posição |
|---|---|---|---|---|
| 1 | 0 | 0 | `[1, 3, 0, 1, -3]` | 0 |
| 2 | 0 | 1 | `[2, 3, 0, 1, -3]` | 1 |
| 3 | 1 | 3 | `[2, 4, 0, 1, -3]` | 4 |
| 4 | 4 | -3 | `[2, 4, 0, 1, -2]` | 1 |
| 5 | 1 | 4 | `[2, 5, 0, 1, -2]` | 5 → fora |

Portanto, a função retorna `5`.

### Exemplos
```python
print(pulos_ate_sair([0, 3, 0, 1, -3]))  # 5
print(pulos_ate_sair([0]))               # 2
print(pulos_ate_sair([]))                # 0
```

### Restrições e observações
- Se a lista estiver vazia, o sapo já começa fora e a resposta é `0`.
- **A lista recebida não pode ser modificada**: trabalhe em uma cópia, criada com `for` e `append`.
- **Proibido:** `min`, `max`, `sum`, `sorted`, `filter`, `map`, `enumerate`, `zip`, os métodos `split`, `join`, `upper`, `lower`, `strip`, `replace`, `find`, `index` e `count`, fatiamento (`x[a:b]`), tuplas, sets, list comprehension, `lambda`, `try/except` e `import`.

Testes:
- `pulos_ate_sair([0, 3, 0, 1, -3])` → `5`
- `pulos_ate_sair([1])` → `1`
- `pulos_ate_sair([5])` → `1`
- `pulos_ate_sair([])` → `0`
- `pulos_ate_sair([2, -1, -1])` → `4`
- `pulos_ate_sair([0])` → `2`

---

## Q2 — Esperando o calor

Uma estação meteorológica anota a temperatura máxima de cada dia em uma lista de inteiros. Quem está planejando uma ida à praia quer saber, **para cada dia**, quantos dias ainda vai ter que esperar até chegar um dia **mais quente** que ele.

Escreva uma função chamada `dias_ate_esquentar` que recebe a lista `temperaturas` e retorna uma **nova lista**, do mesmo tamanho, em que a posição `i` contém quantos dias depois do dia `i` vem o primeiro dia com temperatura **maior** que a dele. Se nenhum dia depois for mais quente, a posição recebe `0`.

A função deve operar da seguinte forma:

1. Comece com uma lista de resposta vazia.
2. Para cada dia `i`:
   1. Procure, a partir do dia `i + 1`, o primeiro dia `j` com temperatura maior que a do dia `i`.
   2. Se encontrar, adicione `j - i` à resposta; senão, adicione `0`.
3. Retorne a resposta.

### Exemplo de raciocínio
Considere `[23, 24, 25, 21, 19, 22, 26, 23]`.

| Dia | Temperatura | Primeiro dia mais quente | Espera |
|---|---|---|---|
| 0 | 23 | dia 1 (24) | 1 |
| 1 | 24 | dia 2 (25) | 1 |
| 2 | 25 | dia 6 (26) | 4 |
| 3 | 21 | dia 5 (22) | 2 |
| 4 | 19 | dia 5 (22) | 1 |
| 5 | 22 | dia 6 (26) | 1 |
| 6 | 26 | nenhum | 0 |
| 7 | 23 | nenhum | 0 |

Retorno: `[1, 1, 4, 2, 1, 1, 0, 0]`

### Exemplos
```python
print(dias_ate_esquentar([30, 40, 50, 60]))  # [1, 1, 1, 0]
print(dias_ate_esquentar([20, 20, 21]))      # [2, 1, 0]
print(dias_ate_esquentar([]))                # []
```

### Restrições e observações
- Uma temperatura **igual** não conta como mais quente.
- A lista recebida não pode ser modificada.
- **Proibido:** `min`, `max`, `sum`, `sorted`, `filter`, `map`, `enumerate`, `zip`, os métodos `split`, `join`, `upper`, `lower`, `strip`, `replace`, `find`, `index` e `count`, fatiamento (`x[a:b]`), tuplas, sets, list comprehension, `lambda`, `try/except` e `import`.

Testes:
- `dias_ate_esquentar([23, 24, 25, 21, 19, 22, 26, 23])` → `[1, 1, 4, 2, 1, 1, 0, 0]`
- `dias_ate_esquentar([30, 40, 50, 60])` → `[1, 1, 1, 0]`
- `dias_ate_esquentar([50, 40, 30])` → `[0, 0, 0]`
- `dias_ate_esquentar([20, 20, 21])` → `[2, 1, 0]`
- `dias_ate_esquentar([25])` → `[0]`
- `dias_ate_esquentar([])` → `[]`
- `dias_ate_esquentar([18, 30, 18, 25, 18, 31])` → `[1, 4, 1, 2, 1, 0]`

---

## Q3 — Editor com desfazer (`tipo: programa`)

Você está construindo um editor de texto minimalista, controlado por comandos digitados. O diferencial dele é o comando **desfazer**, que reverte a última alteração feita, quantas vezes o usuário quiser.

Escreva um programa que pergunte repetidamente `Comando: ` e execute o que foi pedido. O texto é uma **lista de palavras** e começa vazio.

### Comandos
- `escrever`: pergunta `Palavra: ` e adiciona a palavra digitada ao final da lista.
- `apagar`: remove a última palavra da lista. Se a lista estiver vazia, imprime `nada para apagar`.
- `desfazer`: reverte a última alteração (`escrever` ou `apagar`) que ainda não foi desfeita. Se não houver nada para desfazer, imprime `nada para desfazer`.
- `mostrar`: imprime a lista de palavras (com `print(lista)`).
- `fim`: encerra o programa.

Qualquer outro comando imprime `comando invalido`.

### Operações
Cada `escrever`, `apagar` ou `desfazer` **bem-sucedido** conta como uma operação. Comandos que imprimiram mensagem de erro, `mostrar` e `fim` não contam.

Desfazer um `apagar` devolve a palavra apagada ao final da lista. Um `desfazer` não pode ser desfeito.

### Encerramento
Ao digitar `fim`, o programa imprime duas linhas: `texto final:` seguido da lista, e `operacoes:` seguido do total de operações. Por exemplo:
```python
print('texto final:', palavras)
print('operacoes:', total)
```

### Exemplo 1
```
> Comando: escrever
> Palavra: a
> Comando: escrever
> Palavra: b
> Comando: apagar
> Comando: mostrar
['a']
> Comando: desfazer
> Comando: mostrar
['a', 'b']
> Comando: desfazer
> Comando: desfazer
> Comando: mostrar
[]
> Comando: fim
texto final: []
operacoes: 6
```

### Exemplo 2
```
> Comando: apagar
nada para apagar
> Comando: desfazer
nada para desfazer
> Comando: pular
comando invalido
> Comando: fim
texto final: []
operacoes: 0
```

**Proibido:** `min`, `max`, `sum`, `sorted`, `filter`, `map`, `enumerate`, `zip`, os métodos `split`, `join`, `upper`, `lower`, `strip`, `replace`, `find`, `index` e `count`, fatiamento (`x[a:b]`), tuplas, sets, list comprehension, `lambda`, `try/except` e `import`.

Testes:
- entradas `escrever, ola, escrever, mundo, mostrar, fim` → `['ola', 'mundo']` / `texto final: ['ola', 'mundo']` / `operacoes: 2`
- entradas `fim` → `texto final: []` / `operacoes: 0`
- entradas `apagar, desfazer, pular, fim` → `nada para apagar` / `nada para desfazer` / `comando invalido` / `texto final: []` / `operacoes: 0`
- entradas `escrever, a, escrever, b, apagar, mostrar, desfazer, mostrar, desfazer, desfazer, mostrar, fim` → `['a']` / `['a', 'b']` / `[]` / `texto final: []` / `operacoes: 6`
- entradas `escrever, x, escrever, y, apagar, apagar, desfazer, escrever, z, fim` → `texto final: ['x', 'z']` / `operacoes: 6`

---

## Q4 — Jogo da velha gigante

Um clube de jogos inventou um **jogo da velha de qualquer tamanho**: o tabuleiro é uma lista de listas quadrada `N x N`, e cada casa contém `'X'`, `'O'` ou `'.'` (vazia). Um jogador vence quando ocupa uma **linha inteira**, uma **coluna inteira** ou uma das **duas diagonais** inteiras.

Escreva uma função chamada `vencedor` que recebe o tabuleiro e retorna `'X'` ou `'O'` se algum jogador venceu, ou `''` (string vazia) se ninguém venceu.

### Exemplo 1
```
[
  ['O', 'X', '.'],
  ['O', 'X', '.'],
  ['O', '.', 'X']
]
```
A primeira coluna é toda `'O'`. Retorno: `'O'`

### Exemplo 2
```
[
  ['.', '.', 'O'],
  ['.', 'O', '.'],
  ['O', '.', '.']
]
```
A diagonal secundária (do canto superior direito ao inferior esquerdo) é toda `'O'`. Retorno: `'O'`

### Exemplo 3
```
[
  ['X', 'O', 'X'],
  ['X', 'O', 'O'],
  ['O', 'X', 'X']
]
```
Tabuleiro cheio, mas nenhuma linha, coluna ou diagonal completa. Retorno: `''`

### Restrições
- O tabuleiro tem pelo menos uma casa e é sempre quadrado.
- Você pode assumir que no máximo um jogador venceu.
- Uma linha só de `'.'` não é vitória de ninguém.
- **Proibido:** `min`, `max`, `sum`, `sorted`, `filter`, `map`, `enumerate`, `zip`, os métodos `split`, `join`, `upper`, `lower`, `strip`, `replace`, `find`, `index` e `count`, fatiamento (`x[a:b]`), tuplas, sets, list comprehension, `lambda`, `try/except` e `import`.

Testes:
- `vencedor([['X', 'X', 'X'], ['O', 'O', '.'], ['.', '.', '.']])` → `'X'`
- `vencedor([['O', 'X', '.'], ['O', 'X', '.'], ['O', '.', 'X']])` → `'O'`
- `vencedor([['X', 'O', '.'], ['.', 'X', 'O'], ['.', '.', 'X']])` → `'X'`
- `vencedor([['X', 'O', 'X'], ['X', 'O', 'O'], ['O', 'X', 'X']])` → `''`
- `vencedor([['.', '.', 'O'], ['.', 'O', '.'], ['O', '.', '.']])` → `'O'`
- `vencedor([['X']])` → `'X'`
- `vencedor([['.', '.'], ['.', '.']])` → `''`

---

## Q5 — Horários livres

Uma secretária precisa encaixar um compromisso urgente na agenda da diretora. A agenda é uma lista de reuniões, cada uma representada por `[inicio, fim]` em horas inteiras. As reuniões já vêm **ordenadas pelo horário de início**, mas podem se **sobrepor** (a diretora às vezes é chamada para duas ao mesmo tempo).

Escreva uma função chamada `horarios_livres` que receba:
- a lista `reunioes`;
- dois inteiros `abre` e `fecha`, o horário de funcionamento do escritório.

A função deve retornar a lista de intervalos livres `[inicio, fim]`, em ordem, dentro do horário de funcionamento.

### Regras importantes
- Um intervalo livre começa quando todas as reuniões anteriores terminaram e vai até o início da próxima reunião (ou até `fecha`).
- Reuniões sobrepostas ou encostadas (uma termina às 11 e outra começa às 11) não deixam espaço livre entre elas.
- Intervalos de tamanho zero não aparecem no resultado.
- Todas as reuniões estão dentro do horário de funcionamento.

### Exemplo 1
```python
reunioes = [[9, 12], [10, 11], [13, 15]]
abre, fecha = 9, 16
```
- Das 9 às 12 a diretora está ocupada (a reunião das 10 às 11 está dentro da primeira).
- Das 12 às 13 está livre.
- Das 13 às 15 está ocupada.
- Das 15 às 16 está livre.

Retorno: `[[12, 13], [15, 16]]`

### Exemplo 2
```python
reunioes = [[8, 11], [10, 12], [11, 14]]
abre, fecha = 8, 18
```
As três reuniões formam um bloco contínuo das 8 às 14. Retorno: `[[14, 18]]`

### Exemplo 3
```python
reunioes = []
abre, fecha = 9, 17
```
Retorno: `[[9, 17]]`

**Proibido:** `min`, `max`, `sum`, `sorted`, `filter`, `map`, `enumerate`, `zip`, os métodos `split`, `join`, `upper`, `lower`, `strip`, `replace`, `find`, `index` e `count`, fatiamento (`x[a:b]`), tuplas, sets, list comprehension, `lambda`, `try/except` e `import`.

Testes:
- `horarios_livres([[9, 10], [12, 13]], 8, 18)` → `[[8, 9], [10, 12], [13, 18]]`
- `horarios_livres([[8, 11], [10, 12], [11, 14]], 8, 18)` → `[[14, 18]]`
- `horarios_livres([], 9, 17)` → `[[9, 17]]`
- `horarios_livres([[9, 17]], 9, 17)` → `[]`
- `horarios_livres([[9, 12], [10, 11], [13, 15]], 9, 16)` → `[[12, 13], [15, 16]]`

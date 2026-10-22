# Enunciados: Dicionários (Q6 a Q10)

Os alunos só viram: introdução; operações matemáticas, variáveis e funções; `print`, `input`, `int`, `float`; `if/elif/else`; `while`; listas; `for` (incluindo `range`); dicionários. Nenhuma questão exige algo fora disso. Strings aparecem só como valores inteiros: comparadas com `==`/`!=`, usadas como chave, guardadas em listas, impressas e concatenadas com `+` ou f-string.

Em todos os `questoes/qN.json`:
- `proibido` = `min, max, sum, sorted, filter, map, enumerate, zip, tuple, set`
- `metodos_proibidos` = `split, join, upper, lower, strip, replace, find, index, count` (a Q1 também proíbe `copy`)
- `construcoes_proibidas` = `tupla, set, list comprehension, lambda, try/except, import`
- `sem_fatiamento` = `true`

O código é barrado (nota 0) com a mensagem `Uso proibido: X (linha N)`.

Q7, Q9 e Q10 têm `nao_altera_args`. Os testes comparam dicionários com `==` (a ordem das chaves não importa, a ordem dentro das listas importa).

O que cada questão treina:
| Questão | Conceitos |
|---|---|
| Q6 | criar chave sob demanda, `in`, valor como lista, acumular |
| Q7 | dicionário como grafo, contagem com `get`, percorrer dicionário de listas |
| Q8 | programa com vários `input`: inserir, atualizar, consultar, dois dicionários em paralelo |
| Q9 | dicionário aninhado, inverter a estrutura (agrupar por outra chave) |
| Q10 | dicionário como "ponteiro", `while` seguindo chaves, dicionário de visitados para detectar ciclo |

---

## Q6 — Tabela do campeonato

A liga de futebol do bairro anota cada jogo como uma lista `[time_a, gols_a, gols_b, time_b]`, por exemplo `['Gatos', 2, 1, 'Leoes']` (Gatos 2 x 1 Leoes). No fim da rodada, alguém precisa montar a tabela de classificação à mão, e sempre erra alguma conta.

Escreva uma função chamada `tabela` que recebe uma lista de jogos e retorna um **dicionário** em que cada chave é o nome de um time e o valor é uma lista `[pontos, saldo, gols_pro]`.

### Regras
- Vitória vale `3` pontos, empate vale `1` para cada time e derrota vale `0`.
- O **saldo** é a soma dos gols marcados menos a soma dos gols sofridos.
- `gols_pro` é o total de gols marcados pelo time.
- Todo time que aparece em pelo menos um jogo deve estar no dicionário, mesmo com `0` pontos.
- Se a lista estiver vazia, retorne um dicionário vazio.

### Exemplo de raciocínio
```python
jogos = [['Gatos', 2, 1, 'Leoes'], ['Leoes', 0, 0, 'Patos'], ['Patos', 3, 3, 'Gatos']]
```
| Jogo | Gatos | Leoes | Patos |
|---|---|---|---|
| Gatos 2 x 1 Leoes | `[3, 1, 2]` | `[0, -1, 1]` | — |
| Leoes 0 x 0 Patos | `[3, 1, 2]` | `[1, -1, 1]` | `[1, 0, 0]` |
| Patos 3 x 3 Gatos | `[4, 1, 5]` | `[1, -1, 1]` | `[2, 0, 3]` |

Retorno:
```python
{'Gatos': [4, 1, 5], 'Leoes': [1, -1, 1], 'Patos': [2, 0, 3]}
```

### Exemplo 2
```python
jogos = [['A', 2, 0, 'B'], ['C', 3, 1, 'D']]
```
Retorno: `{'A': [3, 2, 2], 'B': [0, -2, 0], 'C': [3, 2, 3], 'D': [0, -2, 1]}`

**Proibido:** `min`, `max`, `sum`, `sorted`, `filter`, `map`, `enumerate`, `zip`, os métodos `split`, `join`, `upper`, `lower`, `strip`, `replace`, `find`, `index` e `count`, fatiamento (`x[a:b]`), tuplas, sets, list comprehension, `lambda`, `try/except` e `import`.

Testes:
- `tabela([['Gatos', 2, 1, 'Leoes'], ['Leoes', 0, 0, 'Patos'], ['Patos', 3, 3, 'Gatos']])` → `{'Gatos': [4, 1, 5], 'Leoes': [1, -1, 1], 'Patos': [2, 0, 3]}`
- `tabela([['A', 1, 0, 'B'], ['C', 2, 1, 'D'], ['B', 3, 0, 'C'], ['D', 1, 0, 'A']])` → `{'A': [3, 0, 1], 'B': [3, 2, 3], 'C': [3, -2, 2], 'D': [3, 0, 2]}`
- `tabela([['A', 2, 0, 'B'], ['C', 3, 1, 'D']])` → `{'A': [3, 2, 2], 'B': [0, -2, 0], 'C': [3, 2, 3], 'D': [0, -2, 1]}`
- `tabela([['A', 1, 1, 'B']])` → `{'A': [1, 0, 1], 'B': [1, 0, 1]}`
- `tabela([])` → `{}`

---

## Q7 — Pessoas que você talvez conheça

Uma rede social guarda as amizades em um dicionário: cada chave é um usuário e o valor é a **lista de amigos** dele. As amizades são sempre mútuas: se `'ana'` está na lista de `'bia'`, `'bia'` está na lista de `'ana'`.

A rede quer sugerir novas amizades. A ideia é: amigos dos seus amigos, que ainda não são seus amigos, provavelmente são conhecidos seus. Quanto mais **amigos em comum**, mais forte a sugestão.

Escreva uma função chamada `sugestoes` que receba:
- o dicionário `rede`;
- uma string `pessoa`, que é uma chave da rede.

A função deve retornar um dicionário em que cada chave é uma pessoa sugerida e o valor é a **quantidade de amigos em comum** com `pessoa`.

### Regras importantes
- Uma pessoa nunca é sugerida para si mesma.
- Quem já é amigo de `pessoa` não é sugerido.
- Só aparecem no resultado pessoas com pelo menos 1 amigo em comum.
- Todo usuário que aparece em alguma lista também é chave da rede.
- O dicionário `rede` não pode ser modificado.

### Exemplo de raciocínio
```python
rede = {
    'ana':  ['bia', 'caio'],
    'bia':  ['ana', 'duda', 'edu'],
    'caio': ['ana', 'duda'],
    'duda': ['bia', 'caio'],
    'edu':  ['bia']
}
pessoa = 'ana'
```
- Amigos de `'ana'`: `'bia'` e `'caio'`.
- Amigos de `'bia'`: `'ana'` (é ela mesma, ignora), `'duda'` (+1), `'edu'` (+1).
- Amigos de `'caio'`: `'ana'` (ignora), `'duda'` (+1).

Retorno: `{'duda': 2, 'edu': 1}`

### Exemplo 2
Com a mesma rede e `pessoa = 'edu'`: o único amigo é `'bia'`, cujos amigos são `'ana'`, `'duda'` e `'edu'`. Retorno: `{'ana': 1, 'duda': 1}`

### Exemplo 3
`sugestoes({'x': []}, 'x')` → `{}`, pois quem não tem amigos não recebe sugestões.

**Proibido:** `min`, `max`, `sum`, `sorted`, `filter`, `map`, `enumerate`, `zip`, os métodos `split`, `join`, `upper`, `lower`, `strip`, `replace`, `find`, `index` e `count`, fatiamento (`x[a:b]`), tuplas, sets, list comprehension, `lambda`, `try/except` e `import`.

Testes:
- `sugestoes({'ana': ['bia', 'caio'], 'bia': ['ana', 'duda', 'edu'], 'caio': ['ana', 'duda'], 'duda': ['bia', 'caio'], 'edu': ['bia']}, 'ana')` → `{'duda': 2, 'edu': 1}`
- `sugestoes({'ana': ['bia', 'caio'], 'bia': ['ana', 'duda', 'edu'], 'caio': ['ana', 'duda'], 'duda': ['bia', 'caio'], 'edu': ['bia']}, 'edu')` → `{'ana': 1, 'duda': 1}`
- `sugestoes({'ana': ['bia', 'caio'], 'bia': ['ana', 'duda', 'edu'], 'caio': ['ana', 'duda'], 'duda': ['bia', 'caio'], 'edu': ['bia']}, 'duda')` → `{'ana': 2, 'edu': 1}`
- `sugestoes({'x': []}, 'x')` → `{}`
- `sugestoes({'a': ['b', 'c'], 'b': ['a', 'c', 'd'], 'c': ['a', 'b'], 'd': ['b']}, 'a')` → `{'d': 1}`

---

## Q8 — Caixa da cantina (`tipo: programa`)

A cantina da faculdade quer um sistema simples para controlar o estoque e as vendas do dia, operado por comandos digitados no caixa.

Escreva um programa que pergunte repetidamente `Comando: ` e execute o que foi pedido. Guarde os produtos em um dicionário. Os preços são **inteiros, em centavos** (`450` = R$ 4,50).

### Comandos
Cada comando primeiro pergunta **todos** os seus dados, nesta ordem, e só depois verifica se dá para executá-lo.
- `cadastrar`: pergunta `Produto: `, `Quantidade: ` e `Preco: `. Cadastra o produto com essa quantidade inicial e esse preço unitário. Se o produto já existir, imprime `produto ja cadastrado` e não altera nada.
- `vender`: pergunta `Produto: ` e `Quantidade: `.
  - Se o produto não existir: `produto inexistente`.
  - Se não houver unidades suficientes: `estoque insuficiente: X disponiveis`, e nada é vendido.
  - Se a venda zerar o estoque do produto, imprime `NOME esgotado`.
- `repor`: pergunta `Produto: ` e `Quantidade: ` e soma a quantidade ao estoque. Se o produto não existir: `produto inexistente`.
- `consultar`: pergunta `Produto: ` e imprime `NOME - X unidades a P centavos`. Se não existir: `produto inexistente`.
- `fim`: encerra.

Qualquer outro comando imprime `comando invalido`.

**Dica:** `print('estoque insuficiente:', x, 'disponiveis')` imprime os valores separados por espaço.

### Encerramento
Ao digitar `fim`, o programa imprime duas linhas:
1. `faturamento: T centavos`, o total vendido no dia.
2. `mais vendido: NOME com N unidades`, o produto com mais unidades vendidas. Em caso de empate, vale o que foi **cadastrado primeiro**. Se nenhuma venda aconteceu, imprime `nenhuma venda`.

### Exemplo 1
```
> Comando: cadastrar
> Produto: cafe
> Quantidade: 10
> Preco: 450
> Comando: cadastrar
> Produto: pao
> Quantidade: 3
> Preco: 80
> Comando: vender
> Produto: cafe
> Quantidade: 4
> Comando: vender
> Produto: pao
> Quantidade: 3
pao esgotado
> Comando: vender
> Produto: pao
> Quantidade: 1
estoque insuficiente: 0 disponiveis
> Comando: consultar
> Produto: cafe
cafe - 6 unidades a 450 centavos
> Comando: fim
faturamento: 2040 centavos
mais vendido: cafe com 4 unidades
```

### Exemplo 2
```
> Comando: vender
> Produto: bolo
> Quantidade: 1
produto inexistente
> Comando: cadastrar
> Produto: bolo
> Quantidade: 2
> Preco: 1200
> Comando: cadastrar
> Produto: bolo
> Quantidade: 5
> Preco: 1000
produto ja cadastrado
> Comando: vender
> Produto: bolo
> Quantidade: 5
estoque insuficiente: 2 disponiveis
> Comando: repor
> Produto: bolo
> Quantidade: 4
> Comando: vender
> Produto: bolo
> Quantidade: 6
bolo esgotado
> Comando: trocar
comando invalido
> Comando: fim
faturamento: 7200 centavos
mais vendido: bolo com 6 unidades
```

### Exemplo 3
```
> Comando: fim
faturamento: 0 centavos
nenhuma venda
```

**Proibido:** `min`, `max`, `sum`, `sorted`, `filter`, `map`, `enumerate`, `zip`, os métodos `split`, `join`, `upper`, `lower`, `strip`, `replace`, `find`, `index` e `count`, fatiamento (`x[a:b]`), tuplas, sets, list comprehension, `lambda`, `try/except` e `import`.

Testes:
- entradas `cadastrar, cafe, 10, 450, cadastrar, pao, 3, 80, vender, cafe, 4, vender, pao, 3, vender, pao, 1, consultar, cafe, fim` → `pao esgotado` / `estoque insuficiente: 0 disponiveis` / `cafe - 6 unidades a 450 centavos` / `faturamento: 2040 centavos` / `mais vendido: cafe com 4 unidades`
- entradas `fim` → `faturamento: 0 centavos` / `nenhuma venda`
- entradas `vender, bolo, 1, cadastrar, bolo, 2, 1200, cadastrar, bolo, 5, 1000, vender, bolo, 5, repor, bolo, 4, vender, bolo, 6, trocar, fim` → `produto inexistente` / `produto ja cadastrado` / `estoque insuficiente: 2 disponiveis` / `bolo esgotado` / `comando invalido` / `faturamento: 7200 centavos` / `mais vendido: bolo com 6 unidades`
- entradas `cadastrar, suco, 5, 600, cadastrar, agua, 5, 300, vender, agua, 2, vender, suco, 2, repor, chips, 3, consultar, chips, consultar, agua, fim` → `produto inexistente` / `produto inexistente` / `agua - 3 unidades a 300 centavos` / `faturamento: 1800 centavos` / `mais vendido: suco com 2 unidades`
- entradas `cadastrar, a, 1, 100, cadastrar, b, 1, 100, vender, b, 1, vender, a, 1, fim` → `b esgotado` / `a esgotado` / `faturamento: 200 centavos` / `mais vendido: a com 1 unidades`

---

## Q9 — Alunos em risco

A coordenação guarda o boletim em um dicionário **aninhado**: cada chave é o nome de um aluno, e o valor é outro dicionário em que cada chave é uma matéria e o valor é a lista de notas do aluno nela.

Os professores, porém, querem ver o problema pelo outro lado: **para cada matéria, quais alunos estão em risco**. Um aluno está em risco em uma matéria quando a média das notas dele nela é **menor que 5**.

Escreva uma função chamada `alunos_em_risco` que recebe o boletim e retorna um dicionário em que cada chave é uma matéria e o valor é a lista de alunos em risco nela.

### Regras importantes
- Só aparecem no resultado matérias com pelo menos um aluno em risco.
- Os alunos de cada lista aparecem na mesma ordem em que estão no boletim.
- Cada aluno tem pelo menos uma nota em cada matéria que cursa. Alunos diferentes podem cursar matérias diferentes.
- O boletim não pode ser modificado.

### Exemplo de raciocínio
```python
boletim = {
    'Ana':  {'calc': [4, 5, 3], 'prog': [8, 9]},
    'Bia':  {'calc': [6, 6],    'prog': [4, 5.5]},
    'Caio': {'calc': [2, 9],    'fis':  [5, 5]}
}
```
| Aluno | Matéria | Média | Em risco? |
|---|---|---|---|
| Ana | calc | 4.0 | sim |
| Ana | prog | 8.5 | não |
| Bia | calc | 6.0 | não |
| Bia | prog | 4.75 | sim |
| Caio | calc | 5.5 | não |
| Caio | fis | 5.0 | não (5 não é menor que 5) |

Retorno: `{'calc': ['Ana'], 'prog': ['Bia']}`

### Exemplo 2
```python
boletim = {'Ana': {'calc': [4, 4]}, 'Bia': {'calc': [3], 'fis': [6]}}
```
Retorno: `{'calc': ['Ana', 'Bia']}`

**Proibido:** `min`, `max`, `sum`, `sorted`, `filter`, `map`, `enumerate`, `zip`, os métodos `split`, `join`, `upper`, `lower`, `strip`, `replace`, `find`, `index` e `count`, fatiamento (`x[a:b]`), tuplas, sets, list comprehension, `lambda`, `try/except` e `import`.

Testes:
- `alunos_em_risco({'Ana': {'calc': [4, 5, 3], 'prog': [8, 9]}, 'Bia': {'calc': [6, 6], 'prog': [4, 5.5]}, 'Caio': {'calc': [2, 9], 'fis': [5, 5]}})` → `{'calc': ['Ana'], 'prog': ['Bia']}`
- `alunos_em_risco({'Ana': {'calc': [4, 4]}, 'Bia': {'calc': [3], 'fis': [6]}})` → `{'calc': ['Ana', 'Bia']}`
- `alunos_em_risco({'Ana': {'calc': [10]}})` → `{}`
- `alunos_em_risco({})` → `{}`

---

## Q10 — Cadeia de indicações

Uma empresa paga bônus por indicação: quando alguém é contratado por indicação de um funcionário, esse funcionário ganha um bônus, e quem o indicou também ganha uma parte, e assim por diante até o topo da cadeia.

As indicações estão em um dicionário em que a chave é o funcionário e o valor é **quem o indicou**. Funcionários que entraram sem indicação não aparecem como chave.

Escreva uma função chamada `cadeia` que receba o dicionário `indicacoes` e o nome de um funcionário, e retorne a lista com a cadeia completa: o próprio funcionário, quem o indicou, quem indicou esse, e assim por diante até alguém que não foi indicado por ninguém.

### Regras importantes
- O primeiro elemento da lista é sempre o funcionário recebido.
- Se o funcionário não foi indicado por ninguém (não é chave), a cadeia tem só ele.
- Por erro de cadastro, o dicionário pode conter um **ciclo** (por exemplo, `a` indicado por `b` e `b` indicado por `a`). Se ao seguir a cadeia você chegar a alguém que **já está nela**, a cadeia é inválida e a função deve retornar uma lista vazia `[]`.
- O dicionário não pode ser modificado.

### Exemplo de raciocínio
```python
indicacoes = {'bia': 'ana', 'caio': 'bia', 'duda': 'bia', 'edu': 'caio'}
```
Para `'edu'`:
- `'edu'` foi indicado por `'caio'`
- `'caio'` foi indicado por `'bia'`
- `'bia'` foi indicada por `'ana'`
- `'ana'` não é chave: fim da cadeia

Retorno: `['edu', 'caio', 'bia', 'ana']`

### Exemplo 2
Com o mesmo dicionário, `cadeia(indicacoes, 'ana')` → `['ana']`.

### Exemplo 3
```python
indicacoes = {'x': 'y', 'y': 'z', 'z': 'y'}
```
Para `'x'`: `x → y → z → y`. O `'y'` já estava na cadeia, então há um ciclo. Retorno: `[]`.

**Dica:** usar `in` em uma lista que cresce funciona, mas pense em como um dicionário de "já visitados" pode deixar essa verificação mais direta.

**Proibido:** `min`, `max`, `sum`, `sorted`, `filter`, `map`, `enumerate`, `zip`, os métodos `split`, `join`, `upper`, `lower`, `strip`, `replace`, `find`, `index` e `count`, fatiamento (`x[a:b]`), tuplas, sets, list comprehension, `lambda`, `try/except` e `import`.

Testes:
- `cadeia({'bia': 'ana', 'caio': 'bia', 'duda': 'bia', 'edu': 'caio'}, 'edu')` → `['edu', 'caio', 'bia', 'ana']`
- `cadeia({'bia': 'ana', 'caio': 'bia', 'duda': 'bia', 'edu': 'caio'}, 'ana')` → `['ana']`
- `cadeia({'bia': 'ana', 'caio': 'bia', 'duda': 'bia', 'edu': 'caio'}, 'zeca')` → `['zeca']`
- `cadeia({'a': 'b', 'b': 'c', 'c': 'a'}, 'a')` → `[]`
- `cadeia({'x': 'y', 'y': 'z', 'z': 'y'}, 'x')` → `[]`

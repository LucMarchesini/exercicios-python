# Novas questões: Dicionários (Q6 a Q10)

Instruções para o Claude Code: **adicione** estas questões como `questoes/q6.json` a `q10.json`, sem mexer nas Q1–Q5. Na sidebar, agrupe as questões em duas seções: "Listas e loops" (Q1–Q5) e "Dicionários" (Q6–Q10). Em todas, `proibido` = `min, max, sum, sorted, filter, map`. Q7, Q9 e Q10 têm `nao_altera_args`. Os testes comparam dicionários com `==` (a ordem das chaves não importa, a ordem dentro das listas importa). Mostre dicionários formatados no feedback de erro. Valide cada questão com uma solução correta, uma errada e uma com função proibida, apague as soluções, faça commit e push.

O que cada questão treina:
| Questão | Conceitos |
|---|---|
| Q6 | criar chave sob demanda, `in`, valor como lista, acumular |
| Q7 | dicionário como grafo, contagem com `get`, percorrer dicionário de listas |
| Q8 | programa com `input`: inserir, atualizar, consultar, dois dicionários em paralelo, `items()` |
| Q9 | dicionário aninhado, inverter a estrutura (agrupar por outra chave) |
| Q10 | dicionário como "ponteiro", `while` seguindo chaves, dicionário de visitados para detectar ciclo |

---

## Q6 — Tabela do campeonato

A liga de futebol do bairro anota os resultados dos jogos em textos no formato `"TimeA GolsA x GolsB TimeB"`, por exemplo `"Gatos 2 x 1 Leoes"`. No fim da rodada, alguém precisa montar a tabela de classificação à mão, e sempre erra alguma conta.

Escreva uma função chamada `tabela` que recebe uma lista de resultados e retorna um **dicionário** em que cada chave é o nome de um time e o valor é uma lista `[pontos, saldo, gols_pro]`.

### Regras
- Vitória vale `3` pontos, empate vale `1` para cada time e derrota vale `0`.
- O **saldo** é a soma dos gols marcados menos a soma dos gols sofridos.
- `gols_pro` é o total de gols marcados pelo time.
- Todo time que aparece em pelo menos um jogo deve estar no dicionário, mesmo com `0` pontos.
- Os nomes dos times não têm espaços. Use `texto.split(' ')` para separar as partes.
- Se a lista estiver vazia, retorne um dicionário vazio.

### Exemplo de raciocínio
```python
jogos = ['Gatos 2 x 1 Leoes', 'Leoes 0 x 0 Patos', 'Patos 3 x 3 Gatos']
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
jogos = ['A 2 x 0 B', 'C 3 x 1 D']
```
Retorno: `{'A': [3, 2, 2], 'B': [0, -2, 0], 'C': [3, 2, 3], 'D': [0, -2, 1]}`

É proibido o uso das funções `min`, `max`, `sum`, `sorted`, `filter` e `map`.

Testes:
- `['Gatos 2 x 1 Leoes','Leoes 0 x 0 Patos','Patos 3 x 3 Gatos']` → `{'Gatos':[4,1,5],'Leoes':[1,-1,1],'Patos':[2,0,3]}`
- `['A 1 x 0 B','C 2 x 1 D','B 3 x 0 C','D 1 x 0 A']` → `{'A':[3,0,1],'B':[3,2,3],'C':[3,-2,2],'D':[3,0,2]}`
- `['A 2 x 0 B','C 3 x 1 D']` → `{'A':[3,2,2],'B':[0,-2,0],'C':[3,2,3],'D':[0,-2,1]}`
- `['A 1 x 1 B']` → `{'A':[1,0,1],'B':[1,0,1]}`
- `[]` → `{}`

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

É proibido o uso das funções `min`, `max`, `sum`, `sorted`, `filter` e `map`.

Testes (R é a rede do exemplo):
- `(R,'ana')` → `{'duda':2,'edu':1}`
- `(R,'edu')` → `{'ana':1,'duda':1}`
- `(R,'duda')` → `{'ana':2,'edu':1}`
- `({'x':[]},'x')` → `{}`

---

## Q8 — Caixa da cantina (`tipo: programa`)

A cantina da faculdade quer um sistema simples para controlar o estoque e as vendas do dia, operado por comandos digitados no caixa.

Escreva um programa que pergunte repetidamente `Comando: ` e execute o que foi pedido. Guarde os produtos em um dicionário.

### Comandos
- `cadastrar NOME QTD PRECO`: cadastra um produto com a quantidade inicial (inteiro) e o preço unitário (decimal). Se o produto já existir, imprime `"produto ja cadastrado"` e não altera nada.
- `vender NOME QTD`: vende `QTD` unidades.
  - Se o produto não existir: `"produto inexistente"`.
  - Se não houver unidades suficientes: `"estoque insuficiente: X disponiveis"`, e nada é vendido.
  - Se a venda zerar o estoque do produto, imprime `"NOME esgotado"`.
- `repor NOME QTD`: soma `QTD` unidades ao estoque. Se o produto não existir: `"produto inexistente"`.
- `consultar NOME`: imprime `"NOME: X unidades a R$ P"`, com o preço em duas casas decimais. Se não existir: `"produto inexistente"`.
- `fim`: encerra.

Qualquer comando desconhecido ou com a quantidade errada de partes imprime `"comando invalido"`. Você pode assumir que quantidades e preços, quando presentes, são números válidos.

### Encerramento
Ao digitar `fim`, o programa imprime duas linhas:
1. `"faturamento: R$ T"`, o total vendido no dia, com duas casas decimais.
2. `"mais vendido: NOME (N unidades)"`, o produto com mais unidades vendidas. Em caso de empate, vale o que foi **cadastrado primeiro**. Se nenhuma venda aconteceu, imprime `"nenhuma venda"`.

### Exemplo 1
```
> Comando: cadastrar cafe 10 4.50
> Comando: cadastrar pao 3 0.80
> Comando: vender cafe 4
> Comando: vender pao 3
pao esgotado
> Comando: vender pao 1
estoque insuficiente: 0 disponiveis
> Comando: consultar cafe
cafe: 6 unidades a R$ 4.50
> Comando: fim
faturamento: R$ 20.40
mais vendido: cafe (4 unidades)
```

### Exemplo 2
```
> Comando: vender bolo 1
produto inexistente
> Comando: cadastrar bolo 2 12
> Comando: cadastrar bolo 5 10
produto ja cadastrado
> Comando: vender bolo 5
estoque insuficiente: 2 disponiveis
> Comando: repor bolo 4
> Comando: vender bolo 6
bolo esgotado
> Comando: consultar
comando invalido
> Comando: fim
faturamento: R$ 72.00
mais vendido: bolo (6 unidades)
```

### Exemplo 3
```
> Comando: fim
faturamento: R$ 0.00
nenhuma venda
```

É proibido o uso das funções `min`, `max`, `sum`, `sorted`, `filter` e `map`.

Testes (entradas → saída):
- `cadastrar cafe 10 4.50, cadastrar pao 3 0.80, vender cafe 4, vender pao 3, vender pao 1, consultar cafe, fim` → `pao esgotado` / `estoque insuficiente: 0 disponiveis` / `cafe: 6 unidades a R$ 4.50` / `faturamento: R$ 20.40` / `mais vendido: cafe (4 unidades)`
- `fim` → `faturamento: R$ 0.00` / `nenhuma venda`
- `vender bolo 1, cadastrar bolo 2 12, cadastrar bolo 5 10, vender bolo 5, repor bolo 4, vender bolo 6, consultar, fim` → `produto inexistente` / `produto ja cadastrado` / `estoque insuficiente: 2 disponiveis` / `bolo esgotado` / `comando invalido` / `faturamento: R$ 72.00` / `mais vendido: bolo (6 unidades)`
- `cadastrar suco 5 6, cadastrar agua 10 2, vender agua 3, vender suco 2, repor kiwi 3, vender agua 2, consultar agua, fim` → `produto inexistente` / `agua: 5 unidades a R$ 2.00` / `faturamento: R$ 22.00` / `mais vendido: agua (5 unidades)`
- `cadastrar a 1 1, cadastrar b 1 1, vender b 1, vender a 1, fim` → `b esgotado` / `a esgotado` / `faturamento: R$ 2.00` / `mais vendido: a (1 unidades)`

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

É proibido o uso das funções `min`, `max`, `sum`, `sorted`, `filter` e `map`.

Testes (B é o boletim do exemplo de raciocínio):
- `B` → `{'calc':['Ana'],'prog':['Bia']}`
- `{'Ana':{'calc':[4,4]},'Bia':{'calc':[3],'fis':[6]}}` → `{'calc':['Ana','Bia']}`
- `{'Ana':{'calc':[10]}}` → `{}`
- `{}` → `{}`

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

É proibido o uso das funções `min`, `max`, `sum`, `sorted`, `filter` e `map`.

Testes (I é o dicionário do exemplo de raciocínio):
- `(I,'edu')` → `['edu','caio','bia','ana']`
- `(I,'ana')` → `['ana']`
- `(I,'zeca')` → `['zeca']`
- `({'a':'b','b':'c','c':'a'},'a')` → `[]`
- `({'x':'y','y':'z','z':'y'},'x')` → `[]`

# Enunciados completos (substituem os anteriores)

Instruções para o Claude Code: substitua as 5 questões por estas. Atualize `titulo`, `funcao`, `tipo`, `enunciado`, `template`, `testes` e `proibido` de cada `questoes/qN.json`. Renderize o enunciado como markdown. Em **todas** as questões, `proibido` = `min, max, sum, sorted, filter, map`. A Q1 tem `nao_altera_args`. Remova as flags antigas (`sem_fatiamento`, `str` proibido) que não se aplicam mais. Valide com soluções de referência e apague-as.

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
- **A lista recebida não pode ser modificada**: trabalhe em uma cópia.
- É proibido o uso das funções `min`, `max`, `sum`, `sorted`, `filter` e `map`.

Testes: `[0,3,0,1,-3]→5`, `[1]→1`, `[5]→1`, `[]→0`, `[2,-1,-1]→4`, `[0]→2`

---

## Q2 — Mensagem secreta

Dois amigos trocam mensagens cifradas usando uma variação da **Cifra de César**. Em vez de deslocar todas as letras pela mesma quantidade, eles usam uma **lista de chaves** que se repete ao longo da mensagem.

Escreva uma função chamada `cifra` que receba:
- uma string `texto` com letras minúsculas sem acento e outros caracteres (espaços, pontuação);
- uma lista `chaves` de inteiros, com pelo menos um elemento.

A função deve retornar o texto cifrado.

### Regras importantes
- Use o alfabeto `'abcdefghijklmnopqrstuvwxyz'`.
- A 1ª **letra** do texto é deslocada por `chaves[0]`, a 2ª letra por `chaves[1]`, e assim por diante. Quando as chaves acabam, recomeça do início.
- O alfabeto é **circular**: depois do `z` vem o `a`. Chaves negativas deslocam para trás.
- Caracteres que não são letras são mantidos como estão e **não consomem chave**.

### Exemplo 1
```python
texto = 'ola mundo'
chaves = [1, 2]
```
- `o` +1 → `p`, `l` +2 → `n`, `a` +1 → `b`
- o espaço é mantido e não consome chave
- `m` +2 → `o`, `u` +1 → `v`, `n` +2 → `p`, `d` +1 → `e`, `o` +2 → `q`

Retorno: `'pnb ovpeq'`

### Exemplo 2
```python
texto = 'zebra!'
chaves = [3]
```
O `z` +3 dá a volta no alfabeto e vira `c`. O `!` é mantido. Retorno: `'cheud!'`

### Exemplo 3
```python
texto = 'python'
chaves = [-1]
```
Retorno: `'oxsgnm'`

É proibido o uso das funções `min`, `max`, `sum`, `sorted`, `filter` e `map`.

Testes: `('abc',[1])→'bcd'`, `('ola mundo',[1,2])→'pnb ovpeq'`, `('zebra!',[3])→'cheud!'`, `('a b',[0,25])→'a a'`, `('python',[-1])→'oxsgnm'`, `('',[5])→''`

---

## Q3 — Editor com desfazer (`tipo: programa`)

Você está construindo um editor de texto minimalista, controlado por comandos digitados. O diferencial dele é o comando **desfazer**, que reverte a última alteração feita, quantas vezes o usuário quiser.

Escreva um programa que pergunte repetidamente `Comando: ` e execute o que foi pedido. O texto começa vazio e é formado por uma sequência de palavras.

### Comandos
- `escrever PALAVRA`: adiciona `PALAVRA` ao final do texto.
- `apagar`: remove a última palavra do texto. Se o texto estiver vazio, imprime `"nada para apagar"`.
- `desfazer`: reverte a última alteração (`escrever` ou `apagar`) que ainda não foi desfeita. Se não houver nada para desfazer, imprime `"nada para desfazer"`.
- `mostrar`: imprime o texto atual, com as palavras separadas por espaço, ou `"(vazio)"` se não houver palavras.
- `fim`: encerra o programa.

Qualquer outra entrada, inclusive `escrever` sem palavra ou com mais de uma palavra, imprime `"comando invalido"`.

### Operações
Cada `escrever`, `apagar` ou `desfazer` **bem-sucedido** conta como uma operação. Comandos que imprimiram mensagem de erro, `mostrar` e `fim` não contam.

Desfazer um `apagar` devolve a palavra apagada ao final do texto. Um `desfazer` não pode ser desfeito.

### Encerramento
Ao digitar `fim`, o programa imprime `"texto final: T (N operacoes)"`, em que `T` é o texto atual (ou `(vazio)`) e `N` o total de operações.

### Exemplo 1
```
> Comando: escrever a
> Comando: escrever b
> Comando: apagar
> Comando: mostrar
a
> Comando: desfazer
> Comando: mostrar
a b
> Comando: desfazer
> Comando: desfazer
> Comando: mostrar
(vazio)
> Comando: fim
texto final: (vazio) (6 operacoes)
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
texto final: (vazio) (0 operacoes)
```

É proibido o uso das funções `min`, `max`, `sum`, `sorted`, `filter` e `map`.

Testes (entradas → saída):
- `escrever ola, escrever mundo, mostrar, fim` → `ola mundo` / `texto final: ola mundo (2 operacoes)`
- `fim` → `texto final: (vazio) (0 operacoes)`
- `apagar, desfazer, pular, escrever, fim` → `nada para apagar` / `nada para desfazer` / `comando invalido` / `comando invalido` / `texto final: (vazio) (0 operacoes)`
- `escrever a, escrever b, apagar, mostrar, desfazer, mostrar, desfazer, desfazer, mostrar, fim` → `a` / `a b` / `(vazio)` / `texto final: (vazio) (6 operacoes)`
- `escrever x, escrever y, apagar, apagar, desfazer, escrever z, fim` → `texto final: x z (6 operacoes)`

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
- É proibido o uso das funções `min`, `max`, `sum`, `sorted`, `filter` e `map`.

Testes:
- `[['X','X','X'],['O','O','.'],['.','.','.']]→'X'`
- `[['O','X','.'],['O','X','.'],['O','.','X']]→'O'`
- `[['X','O','.'],['.','X','O'],['.','.','X']]→'X'`
- `[['X','O','X'],['X','O','O'],['O','X','X']]→''`
- `[['.','.','O'],['.','O','.'],['O','.','.']]→'O'`
- `[['X']]→'X'`
- `[['.','.'],['.','.']]→''`

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

É proibido o uso das funções `min`, `max`, `sum`, `sorted`, `filter` e `map`.

Testes: `([[9,10],[12,13]],8,18)→[[8,9],[10,12],[13,18]]`, `([[8,11],[10,12],[11,14]],8,18)→[[14,18]]`, `([],9,17)→[[9,17]]`, `([[9,17]],9,17)→[]`, `([[9,12],[10,11],[13,15]],9,16)→[[12,13],[15,16]]`

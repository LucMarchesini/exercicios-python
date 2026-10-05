# Mini-PrairieLearn de Python

## Objetivo
Plataforma web **leve**, estilo PrairieLearn: lista de exercícios, enunciado, editor de código, botão "Rodar testes" e resultado por teste. Conteúdo: Python até antes de dicionários (condicionais, loops, strings, listas, funções).

## Stack (manter mínimo)
- **Um único `index.html`** + pasta `questoes/` com JSONs. Sem backend, sem build.
- Python roda no navegador via **Pyodide** (CDN jsdelivr).
- Editor: **CodeMirror 5** (CDN) com modo Python.
- Progresso salvo em `localStorage` (código + status de cada questão).

## Layout
- Esquerda: lista de questões com status (⚪ não feita / 🟢 passou / 🔴 falhou).
- Direita: enunciado (markdown simples), editor com o `template`, botões **Rodar testes** e **Resetar**.
- Abaixo: tabela de testes (entrada, esperado, obtido, ✅/❌) e nota `x/N`.

## Execução dos testes
1. Rodar o código do aluno com timeout (~3s, via Web Worker) para pegar loops infinitos.
2. Para cada teste: chamar `funcao(*args)` e comparar com `esperado` usando `==`.
3. Capturar exceções e mostrar a mensagem no teste correspondente.
4. Não mostrar o gabarito.

## Dois tipos de questão
- `tipo: "funcao"` → chama `funcao(*args)` e compara com `esperado` (`==`). Passar cópias dos args.
- `tipo: "programa"` → roda o script com `input()` substituído por leitura de `entradas` (prompt ignorado); compara as linhas impressas com `saida` (strip em cada linha).

```json
{ "id":"q1","tipo":"funcao","titulo":"...","enunciado":"...","funcao":"nome",
  "template":"def nome(...):\n    pass","testes":[{"args":[...],"esperado":...}] }
{ "id":"q3","tipo":"programa", ..., "testes":[{"entradas":["25","-999"],"saida":["..."]}] }
```
Gerar `questoes/index.json` com a lista de ids.

---

## Questões

### Q1 — Validador de cartão (while, %, //)
`valida_cartao(numero)` recebe um int. Percorra os dígitos da direita para a esquerda (sem converter para string). Dobre um dígito sim, outro não, começando pelo **segundo** da direita; se o dobro passar de 9, subtraia 9. Some tudo. Retorne `True` se a soma for múltipla de 10.

Testes: `79927398713→True`, `79927398710→False`, `18→True`, `12→False`, `0→True`

### Q2 — Amplitude em janelas (listas, while/for)
`amplitudes(precos, k)` retorna uma lista com a diferença entre o maior e o menor valor de cada janela **deslizante** de `k` elementos consecutivos. Se não couber nenhuma janela, retorne `[]`. Proibido usar `max`, `min` e fatiamento.

Testes: `([3,1,4,1,5],3)→[3,3,4]`, `([10,10,10],2)→[0,0]`, `([1,2],3)→[]`, `([7,2,9,4],1)→[0,0,0,0]`

### Q3 — Monitor de temperatura (programa com input)
Leia temperaturas inteiras até ser digitado `-999`.
- Fora de [-50, 60]: imprima `leitura invalida` (não conta).
- Se houver **3 leituras válidas consecutivas acima de 40**, imprima `alerta: superaquecimento apos N leituras` (N = válidas até ali) e encerre.
- Ao digitar `-999`: imprima `media: X.X em N leituras` (1 casa) ou `nenhuma leitura`.

Testes (entradas → saída):
- `25,30,-999` → `media: 27.5 em 2 leituras`
- `-999` → `nenhuma leitura`
- `41,42,70,43,-999` → `leitura invalida` / `alerta: superaquecimento apos 3 leituras`
- `41,20,45,50,55,-999` → `alerta: superaquecimento apos 5 leituras`
- `100,-999` → `leitura invalida` / `nenhuma leitura`

### Q4 — Propagação (listas de listas)
`propaga(mapa)`: `1` = infectado, `0` = saudável, `2` = parede. Após um passo, todo `0` vizinho (cima/baixo/esquerda/direita) de um `1` **do mapa original** vira `1`. Retorne um **novo** mapa sem alterar o original.

Testes:
- `[[0,0,0],[0,1,0],[0,0,0]]→[[0,1,0],[1,1,1],[0,1,0]]`
- `[[1,0,0,0]]→[[1,1,0,0]]`
- `[[1,2],[0,0]]→[[1,2],[1,0]]`
- `[[0,0],[0,0]]→[[0,0],[0,0]]`

### Q5 — Pares com soma alvo (loops aninhados)
`pares_soma(lista, alvo)` retorna todos os pares de índices `[i, j]`, com `i < j`, cujos valores somam `alvo`, em ordem crescente de `i` e depois de `j`.

Testes: `([1,4,3,2],5)→[[0,1],[2,3]]`, `([2,2,2],4)→[[0,1],[0,2],[1,2]]`, `([1,1],5)→[]`, `([],0)→[]`

---

## Entrega
`index.html`, `questoes/q1..q5.json`, `questoes/index.json`, `README.md` curto (`python -m http.server` → `localhost:8000`).

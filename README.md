# Exercícios de Python

Mini-plataforma estilo PrairieLearn: o Python roda no navegador (Pyodide), sem backend.

## Como rodar

```
python -m http.server
```

Abra <http://localhost:8000>. (Abrir o `index.html` direto do disco não funciona: o navegador bloqueia o `fetch` dos JSONs.)

## Adicionar questões

Crie `questoes/qN.json` e coloque o id em `questoes/index.json`. Formatos:

- `"tipo": "funcao"` — `funcao`, `template`, `testes: [{"args": [...], "esperado": ...}]`
- `"tipo": "programa"` — `template`, `testes: [{"entradas": [...], "saida": [...]}]`

Campos opcionais:

- `"proibido": ["max", "min"]` — nomes que não podem aparecer no código (nota 0). Com `"str"`, também barra f-strings, `repr`, `format`/`.format` e `"..." % x`
- `"sem_fatiamento": true` — proíbe `lista[a:b]` (nota 0)
- `"nao_altera_args": true` — o teste falha se a função modificar os argumentos recebidos

O código e o status de cada questão ficam no `localStorage` do navegador.

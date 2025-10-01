## Endpoint: Adicionar Exercício a um Treino

### Visão Geral

Cria um novo registro de exercicio e o vincula a um treino existente.

### Comportamento

*Recebe os dados do exercício e o treino_id via parâmetro de URL.
*Valida se o treino existe e pertence a um aluno do personal autenticado.
*Cria o registro na tabela exercicios.
*Gera entrada de auditoria para a criação do exercício.

### Autenticação

Requer autenticação do personal via Bearer Token (JWT).

### Payload de Requisição (JSON)

```
{
"nome": "string", // (obrigatório)
"series": "integer", // (obrigatório, > 0)
"repeticoes": "string", // (obrigatório, ex: "10-12" ou "15")
"tempo_descanso_seg": "integer",
"observacoes": "string"
}
```

### Exemplo — Requisição Válida

```
POST /api/treinos/f1e2d3c4-b5a6-7890-1234-567890abcdef/exercicios
Content-Type: application/json
Authorization: Bearer <token>

{
"nome": "Supino Reto com Barra",
"series": 4,
"repeticoes": "8-12",
"tempo_descanso_seg": 60,
"observacoes": "Manter a cadência controlada."
}
```

### Regras de Negócio e Validações

Obrigatórias:

*nome, series, repeticoes são obrigatórios.
*series deve ser um inteiro maior que 0.
\*O treino_id na URL deve existir.

### Erros comuns:

*400 Bad Request: Campo obrigatório ou formatação inválida.
*404 Not Found: treino_id não encontrado.
\*403 Forbidden: O treino não pertence ao personal autenticado.

### Resposta de Sucesso (201 Created)

```
{
"id": "uuid",
"nome": "Supino Reto com Barra",
"series": 4,
"repeticoes": "8-12",
"tempo_descanso_seg": 60,
"observacoes": "Manter a cadência controlada.",
"treino_id": "f1e2d3c4-b5a6-7890-1234-567890abcdef"
}
```

### Cabeçalhos:

\*Location: /api/exercicios/{id}

### Respostas de Erro (exemplos)

```
// 400 Bad Request
{
"error": "\"series\" deve ser maior que 0"
}

// 404 Not Found
{
"error": "\"treino_id\" não encontrado"
}
```

### Considerações de Implementação

\*Extrair o personal_id do JWT para validar a cadeia de propriedade: personal -> aluno -> treino.

\*Garantir que todas as validações de dados (tipos, valores) sejam aplicadas antes de persistir no banco.

\*Persistir audit_log com entity_type = "exercicio", action = "create".

### Critérios de Aceitação

\*Ao enviar payload válido, registro de exercício é criado e retornado com 201 e Location.

\*Validações rejeitam entradas inválidas com mensagens claras.

\*Tentativa de adicionar exercício a treino de outro personal resulta em erro 403.

\*O novo exercício aparece corretamente na listagem de detalhes do treino.

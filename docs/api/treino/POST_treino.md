## Endpoint: Criar um novo Treino

### Visão Geral

Cria um novo registro de treino (`treino`) vinculado a um aluno existente. Apenas o `personal` responsável pelo aluno pode criar treinos para ele. Este endpoint estabelece a estrutura base de um treino, que posteriormente será preenchida com exercícios.

## **Referências:** `docs/database/ERD.md`, `docs/database/DEFINITIONS.md`

### Comportamento

- Recebe os dados do treino (`nome`, `descricao`, `aluno_id`).
- Valida se o `aluno_id` informado existe e se está vinculado ao `personal` autenticado.
- Cria o registro na tabela `treinos`, associando-o ao aluno.
- Define timestamps de criação/atualização (`created_at`, `updated_at`).
- Gera entrada de auditoria na `audit_log` com `action = "create"` e `entity_type = "treino"`.

---

### Autenticação

Requer autenticação do `personal` via Bearer Token (JWT). O `personal_id` deve ser obtido do token e usado para validar a propriedade do aluno.

---

### Payload de Requisição (JSON)

### Campos aceitos:

```json
{
  "nome": "string", // (obrigatório)
  "descricao": "string",
  "aluno_id": "uuid" // (obrigatório)
}
```

### Notas:

A associação com o `personal` é implícita, derivada da relação entre o aluno_id e o personal_id extraído do token.

```
POST /api/treinos
Content-Type: application/json
Authorization: Bearer <token>

{
  "nome": "Treino A - Peito e Tríceps",
  "descricao": "Foco em hipertrofia para membros superiores.",
  "aluno_id": "a1b2c3d4-e5f6-7890-1234-567890abcdef"
}
```

### Regras de Negócio e Validações

### Obrigatórias:

*nome é obrigatório e não pode ser vazio.
*aluno_id deve ser um UUID válido e deve existir na tabela alunos.
\*O aluno referenciado por aluno_id deve pertencer ao personal autenticado.

### Auditoria:

Registrar em audit_log os campos criados (old_value_json = null, new_value_json com o payload persistido), com who extraído do contexto de autenticação.

### Erros comuns:

\*400 Bad Request: campo obrigatório ausente ou inválido (ex: "nome" é obrigatório).

\*404 Not Found: aluno_id não encontrado.

\*403 Forbidden: O aluno não pertence ao personal que fez a requisição.

### Resposta de Sucesso (201 Created).

```
{
  "id": "uuid",
  "nome": "Treino A - Peito e Tríceps",
  "descricao": "Foco em hipertrofia para membros superiores.",
  "aluno_id": "a1b2c3d4-e5f6-7890-1234-567890abcdef",
  "created_at": "timestamp",
  "updated_at": "timestamp"
}
```

### Cabeçalhos:

```
Location: /api/treinos/{id}

```

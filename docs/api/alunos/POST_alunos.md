# Endpoint: Criar Aluno — POST /api/alunos

## Visão Geral

Cria um novo registro de aluno vinculado a um `personal` (usuário administrador do sistema). Implementa validações de negócio e inicializa dados administrativos relacionados a plano e pagamento conforme informado.

Referências: `docs/database/ERD.md`, `docs/database/ERD.mermaid`, `DEFINITIONS.md`.

---

### Comportamento

- Recebe os dados mínimos do aluno e cria o registro na tabela `aluno`.
- Associa o aluno a um `personal` vigente (via `personal_id`).
- Se houver `plano_id` e `plano_data_inicio`, calcula e grava `plano_data_vencimento` conforme a duração do plano.
- Opcionalmente registra o último `data_pagamento` e a `forma_pagamento` declarada para referência administrativa.
- Define timestamps de criação/atualização (`created_at`, `updated_at`).
- Gera entrada de auditoria na `audit_log` com `action = "create"`.

---

### Regras de Negócio e Validações

Obrigatórias:

- `nome` é obrigatório e não pode ser vazio.
- `frequencia_semana` deve ser inteiro entre 1 e 7 quando informado.
- `plano_data_vencimento` deve ser maior ou igual a `plano_data_inicio`.
- Se `plano_id` for informado, ele deve existir na tabela `plano`.
- `forma_pagamento`, quando informada, deve ser uma das: `pix`, `credito`, `debito`.

Derivações e consistência:

- Quando `plano_id` e `plano_data_inicio` são enviados, calcular `plano_data_vencimento` = `plano_data_inicio` + `duracao_meses` do plano (vide tabela `plano`).
- Se `data_pagamento` for enviada, validar que não é futura além de uma tolerância (configurável) e que o formato é válido.
- `personal_id` deve referenciar um `personal` existente (normalmente inferido do token do usuário autenticado).
- Valores numéricos devem ser válidos: `altura_cm` (numeric), `idade` (int >= 0).

Auditoria:

- Registrar em `audit_log` os campos criados (`old_value_json = null`, `new_value_json` com payload persistido), com `who` extraído do contexto de autenticação.

Erros comuns:

- 400: campo obrigatório ausente ou inválido (ex.: `"nome" é obrigatório`).
- 400: `frequencia_semana` fora do intervalo 1–7.
- 404: `plano_id` não encontrado.
- 409: conflito de negócio (ex.: regra específica de unicidade definida pelo produto, se aplicável).

---

### Autenticação

- Requer autenticação do `personal` via Bearer Token (JWT). O `personal_id` deve ser obtido do token e aplicado no registro do aluno.

---

### Payload de Requisição (JSON)

Campos aceitos (todos opcionais salvo onde indicado):

```json
{
  "nome": "string" (obrigatório),
  "altura_cm": number,
  "idade": integer,
  "restricoes": "string",
  "data_inicio_treino": "YYYY-MM-DD",
  "frequencia_semana": integer (1..7),
  "plano_id": "uuid",
  "plano_data_inicio": "YYYY-MM-DD",
  "forma_pagamento": "pix" | "credito" | "debito",
  "data_pagamento": "YYYY-MM-DD",
  "observacoes": "string"
}
```

Notas:

- `personal_id` é derivado do token e não deve ser enviado no corpo.
- `plano_data_vencimento` é calculado pelo backend e não aceito em input direto.

---

### Exemplo — Requisição válida

```json
POST /api/alunos
Content-Type: application/json
Authorization: Bearer <token>

{
  "nome": "João Silva",
  "altura_cm": 178,
  "idade": 28,
  "restricoes": "Nenhuma",
  "data_inicio_treino": "2025-09-01",
  "frequencia_semana": 3,
  "plano_id": "<uuid-plano>",
  "plano_data_inicio": "2025-09-01",
  "forma_pagamento": "pix",
  "data_pagamento": "2025-09-01",
  "observacoes": "Aluno novo"
}
```

---

### Resposta de Sucesso (201 Created)

```json
{
  "id": "uuid",
  "personal_id": "uuid",
  "nome": "João Silva",
  "altura_cm": 178,
  "idade": 28,
  "restricoes": "Nenhuma",
  "data_inicio_treino": "2025-09-01",
  "frequencia_semana": 3,
  "plano_id": "<uuid-plano>",
  "plano_data_inicio": "2025-09-01",
  "plano_data_vencimento": "2025-10-01", // calculado conforme plano
  "forma_pagamento": "pix",
  "data_pagamento": "2025-09-01",
  "observacoes": "Aluno novo",
  "created_at": "timestamp",
  "updated_at": "timestamp"
}
```

Cabeçalhos:

- `Location: /api/alunos/{id}`

---

### Respostas de Erro (exemplos)

```sh
400 Bad Request
{
  "error": "\"nome\" é obrigatório"
}

400 Bad Request
{
  "error": "\"frequencia_semana\" deve ser entre 1 e 7"
}

404 Not Found
{
  "error": "plano_id não encontrado"
}
```

---

### Considerações de Implementação

- Extrair `personal_id` do JWT e injetar no modelo `aluno`.
- Validar integridade referencial de `plano_id` e aplicar a regra de cálculo de vencimento usando `duracao_meses` do plano.
- Normalizar datas (usar timezone consistente, preferencialmente UTC no backend; armazenar `date` quando apropriado).
- Garantir idempotência de criação via chave de deduplicação se necessário (opcional ao escopo atual).
- Persistir `audit_log` com `entity_type = "aluno"`, `action = "create"` e contexto (`who`, `timestamp`).

---

### Critérios de Aceitação

- Ao enviar payload válido, registro é criado e retornado com `201` e `Location`.
- `plano_data_vencimento` é calculado corretamente quando houver plano.
- Validações rejeitam entradas inválidas com mensagens claras.
- Registro aparece nas listagens e é associado ao `personal` autenticado.

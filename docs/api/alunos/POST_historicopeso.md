# Endpoint: Registrar Histórico de Peso

## Visão Geral
Cria um novo registro de **histórico de peso** para um aluno específico, vinculando a medição à data e ao personal trainer autenticado.

---

## Comportamento
- Recebe o **aluno_id** via parâmetro de URL e os dados da medição via **payload JSON**.  
- Valida se o aluno pertence a um personal autenticado (extraído do JWT).  
- Armazena o registro na tabela **historico_peso**.  
- Gera uma entrada de auditoria com `entity_type = "historico_peso"` e `action = "create"`.  

---

## Autenticação
Requer autenticação do personal via **Bearer Token (JWT)**.

---

## Payload de Requisição (JSON)
```json
{
  "peso_kg": 75.4,
  "percentual_gordura": 14.8,
  "observacoes": "Progresso positivo após o novo plano alimentar"
}
```

---

## Exemplo — Requisição Válida
```
POST /api/alunos/7f8e9d2b-4c3a-11ee-be56-0242ac120002/historico-peso
Content-Type: application/json
Authorization: Bearer <token>
```

```json
{
  "peso_kg": 75.4,
  "percentual_gordura": 14.8,
  "observacoes": "Progresso positivo após o novo plano alimentar"
}
```

---

## Regras de Negócio e Validações

**Obrigatórias:**
- `peso_kg` é obrigatório e deve ser um número maior que zero.  
- `aluno_id` informado na URL deve existir e estar vinculado ao personal autenticado.  
- Cada registro é datado automaticamente (`data_registro = NOW()`).

**Erros comuns:**
- **400 Bad Request:** Campos obrigatórios ausentes ou formato inválido.  
- **404 Not Found:** `aluno_id` não encontrado.  
- **403 Forbidden:** O aluno pertence a outro personal.

---

## Resposta de Sucesso (201 Created)
```json
{
  "id": "c2d5f7e1-9b24-4df6-bd1d-7d8d6c3f1234",
  "aluno_id": "7f8e9d2b-4c3a-11ee-be56-0242ac120002",
  "peso_kg": 75.4,
  "percentual_gordura": 14.8,
  "observacoes": "Progresso positivo após o novo plano alimentar",
  "data_registro": "2025-10-06T18:24:13.000Z"
}
```

**Cabeçalhos:**
- `Location: /api/historico-peso/{id}`

---

## Respostas de Erro (exemplos)

### 400 Bad Request
```json
{
  "error": "\"peso_kg\" deve ser um número maior que zero"
}
```

### 404 Not Found
```json
{
  "error": "\"aluno_id\" não encontrado"
}
```

### 403 Forbidden
```json
{
  "error": "O aluno não pertence ao personal autenticado"
}
```

---

## Considerações de Implementação

- Extrair `personal_id` do token JWT para validar a relação **personal → aluno → histórico**.  
- Garantir que todos os dados numéricos sejam validados antes da inserção no banco.  
- Persistir em tabela `historico_peso` com os campos:
  - `id` (UUID)
  - `aluno_id` (FK)
  - `peso_kg`
  - `percentual_gordura`
  - `observacoes`
  - `data_registro`
  - `personal_id`  
- Criar registro de auditoria (`audit_log`) para cada inserção com:
  - `entity_type = "historico_peso"`
  - `action = "create"`
  - `performed_by = personal_id`

---

## Critérios de Aceitação

- Envio de payload válido deve criar o registro e retornar **201 Created** com o recurso criado.  
- Validações devem rejeitar valores incorretos com mensagens claras e precisas.  
- Tentativas de adicionar histórico para aluno de outro personal devem retornar **403 Forbidden**.  
- O novo registro deve aparecer corretamente ao consultar o **histórico de peso do aluno**.  

---

## Endpoint Adicional: Consultar Histórico de Peso

Permite listar todos os registros de peso de um aluno específico.

```
GET /api/alunos/{aluno_id}/historico-peso
Authorization: Bearer <token>
```

**Resposta de Sucesso (200 OK):**
```json
[
  {
    "data_registro": "2025-09-30T12:00:00Z",
    "peso_kg": 76.2,
    "percentual_gordura": 15.1
  },
  {
    "data_registro": "2025-10-06T18:24:13Z",
    "peso_kg": 75.4,
    "percentual_gordura": 14.8
  }
]
```

---

## Endpoint: Relatório Básico de Evolução

Gera um resumo com base no histórico de peso de cada aluno.

```
GET /api/alunos/{aluno_id}/relatorio-evolucao
Authorization: Bearer <token>
```

**Resposta de Sucesso (200 OK):**
```json
{
  "aluno_id": "7f8e9d2b-4c3a-11ee-be56-0242ac120002",
  "peso_inicial": 80.2,
  "peso_atual": 75.4,
  "diferenca_total": -4.8,
  "percentual_gordura_inicial": 18.0,
  "percentual_gordura_atual": 14.8,
  "ultima_atualizacao": "2025-10-06T18:24:13.000Z"
}
```

**Regras:**
- O endpoint deve calcular automaticamente as variações de peso e gordura.  
- Caso o aluno não possua registros, retorna:
```json
{
  "message": "Nenhum histórico de peso encontrado para este aluno."
}
```

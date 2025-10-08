# 📘 Módulo de Gestão de Pagamentos e Planos

## Visão Geral

O módulo permite gerenciar **planos de treino** (mensal, trimestral, semestral etc.) e **pagamentos dos alunos** vinculados a esses planos.
Ele garante controle financeiro e atualização automática das datas de vencimento no cadastro do aluno.

Referências: `docs/database/ERD.md`, `docs/database/ERD.mermaid`, `DEFINITIONS.md`, `ERD.md`.

---

## 🧩 Endpoints — Planos

### 1. Criar Plano — `POST /api/planos`

#### Comportamento

Cria um novo plano de pagamento com nome, duração e preço, disponível para associação a alunos.

#### Regras e Validações

* `nome` obrigatório e único.
* `duracao_meses` deve ser inteiro > 0.
* `preco` deve ser numérico ≥ 0.
* `descricao` é opcional.
* Registra auditoria com `action = "create"` e `entity_type = "plano"`.

#### Payload de Requisição

```json
{
  "nome": "Mensal",
  "duracao_meses": 1,
  "preco": 150.00,
  "descricao": "Plano mensal com renovação automática"
}
```

#### Resposta (201 Created)

```json
{
  "id": "uuid",
  "nome": "Mensal",
  "duracao_meses": 1,
  "preco": 150.0,
  "descricao": "Plano mensal com renovação automática"
}
```

#### Erros

* 400 — campo obrigatório ausente
* 409 — nome duplicado

---

### 2. Listar Planos — `GET /api/planos`

#### Comportamento

Retorna a lista de planos cadastrados, com suporte a filtros opcionais (`nome`, `duração`).

#### Exemplo de Resposta (200 OK)

```json
[
  {
    "id": "uuid1",
    "nome": "Mensal",
    "duracao_meses": 1,
    "preco": 150.0
  },
  {
    "id": "uuid2",
    "nome": "Trimestral",
    "duracao_meses": 3,
    "preco": 400.0
  }
]
```

---

### 3. Consultar Plano — `GET /api/planos/{id}`

#### Resposta (200 OK)

```json
{
  "id": "uuid2",
  "nome": "Trimestral",
  "duracao_meses": 3,
  "preco": 400.0,
  "descricao": "Plano trimestral com desconto"
}
```

#### Erros

* 404 — plano não encontrado

---

### 4. Atualizar Plano — `PUT /api/planos/{id}`

#### Comportamento

Atualiza informações de um plano existente.

#### Regras

* Não permite alteração de `id`.
* Campos válidos: `nome`, `duracao_meses`, `preco`, `descricao`.

#### Exemplo de Requisição

```json
{
  "preco": 420.0,
  "descricao": "Plano trimestral atualizado"
}
```

#### Resposta (200 OK)

```json
{
  "id": "uuid2",
  "nome": "Trimestral",
  "duracao_meses": 3,
  "preco": 420.0,
  "descricao": "Plano trimestral atualizado"
}
```

#### Erros

* 400 — formato inválido
* 404 — plano não encontrado

---

### 5. Excluir Plano — `DELETE /api/planos/{id}`

#### Comportamento

Remove um plano (somente se não estiver vinculado a nenhum aluno ativo).

#### Regras

* Se houver `alunos` vinculados, o sistema retorna erro 409 (conflito).

#### Resposta

```json
204 No Content
```

#### Erros

* 404 — plano não encontrado
* 409 — plano vinculado a alunos

---

## 💰 Endpoints — Pagamentos de Alunos

### 6. Registrar Pagamento — `POST /api/alunos/{aluno_id}/pagamentos`

#### Comportamento

Registra um pagamento de um aluno vinculado a um plano.
Atualiza automaticamente o campo `plano_data_vencimento` do aluno.

#### Regras e Validações

* `plano_id` obrigatório e deve existir.
* `forma_pagamento` deve ser `pix`, `credito` ou `debito`.
* `data_pagamento` não pode ser futura.
* Calcula `plano_data_vencimento` = `data_pagamento` + `duracao_meses` do plano.
* Gera auditoria (`entity_type = "pagamento"`, `action = "create"`).

#### Payload de Requisição

```json
{
  "plano_id": "uuid-plano",
  "valor": 150.00,
  "forma_pagamento": "pix",
  "data_pagamento": "2025-10-06",
  "status": "pago"
}
```

#### Resposta (201 Created)

```json
{
  "id": "uuid-pagamento",
  "aluno_id": "uuid-aluno",
  "plano_id": "uuid-plano",
  "valor": 150.00,
  "forma_pagamento": "pix",
  "data_pagamento": "2025-10-06",
  "status": "pago",
  "transacao_id": null
}
```

#### Erros

* 400 — campo obrigatório ausente
* 404 — aluno ou plano não encontrado
* 409 — conflito (pagamento duplicado)

---

### 7. Consultar Pagamentos de um Aluno — `GET /api/alunos/{aluno_id}/pagamentos`

#### Comportamento

Retorna todos os pagamentos vinculados a um aluno.

#### Resposta (200 OK)

```json
[
  {
    "id": "uuid-pag1",
    "plano_id": "uuid-plano1",
    "valor": 150.0,
    "forma_pagamento": "pix",
    "data_pagamento": "2025-09-06",
    "status": "pago"
  },
  {
    "id": "uuid-pag2",
    "plano_id": "uuid-plano2",
    "valor": 400.0,
    "forma_pagamento": "credito",
    "data_pagamento": "2025-12-06",
    "status": "pago"
  }
]
```

#### Erros

* 404 — aluno não encontrado

---

## 🔐 Autenticação

* Requer autenticação JWT do `personal`.
* `personal_id` deve ser inferido do token e validado contra o `aluno_id` relacionado.

---

## 🧾 Auditoria

* Toda operação de **criação, atualização ou exclusão** gera entrada na tabela `audit_log` com:

  * `entity_type`: `"plano"` ou `"pagamento"`
  * `action`: `"create"`, `"update"`, `"delete"`
  * `who`: extraído do contexto do JWT
  * `timestamp`: hora da ação

---

## ✅ Critérios de Aceitação

* CRUD de planos funciona com validações adequadas e auditoria registrada.
* Pagamentos alteram automaticamente a data de vencimento do aluno.
* Consultas retornam dados consistentes e ordenados por `data_pagamento desc`.
* Respostas seguem formato JSON padronizado.


### **Conteúdo para o arquivo `docs/api/alunos/PUT_regra_progressao.md`**

````markdown
# Endpoint: Definir Regra de Progressão de Carga — PUT /api/alunos/{aluno_id}/exercicios-plano/{exercicio_plano_id}/regra-progressao

## Visão Geral

Cria ou atualiza a regra de automação para progressão de carga de um exercício específico, que está vinculado a um plano de treino de um aluno. O endpoint permite que o `personal` defina as condições e os incrementos que o sistema usará para ajustar automaticamente a carga do aluno em treinos futuros.

Referências: `docs/database/ERD.md`, `DEFINITIONS.md`.

---

### Comportamento

- Recebe os parâmetros da regra de progressão para um `exercicio_plano` (um exercício específico dentro do plano de treino do aluno).
- Valida se o `aluno_id` e o `exercicio_plano_id` existem e estão associados corretamente.
- Armazena (ou atualiza, se já existir) a regra na tabela `regra_progressao`, vinculada ao `exercicio_plano_id`.
- A lógica de *aplicação* da regra (ou seja, o cálculo e atualização da carga) **não ocorre neste endpoint**. Ela será acionada por outro módulo quando o aluno registrar a conclusão de uma série/treino.
- Define timestamps de criação/atualização (`created_at`, `updated_at`).
- Gera uma entrada de auditoria na `audit_log` com `action = "upsert"` (create/update).

---

### Regras de Negócio e Validações

Obrigatórias:

- `tipo_progressao` é obrigatório e deve ser um dos valores predefinidos: `linear`, `dupla`.
- O `aluno_id` e `exercicio_plano_id` fornecidos na URL devem existir no banco de dados.

Derivações e consistência:

- Se `tipo_progressao` for `linear`:
    - `incremento_kg` é obrigatório e deve ser um valor numérico maior que zero.
    - A condição de ativação é implícita: o aluno deve ter completado todas as repetições propostas em todas as séries no último treino registrado para aquele exercício.
- Se `tipo_progressao` for `dupla` (Dupla Progressão):
    - `meta_reps_min` e `meta_reps_max` são obrigatórios. `meta_reps_max` deve ser maior que `meta_reps_min`.
    - `incremento_kg` é obrigatório e deve ser maior que zero.
    - A condição de ativação é: o aluno atingiu `meta_reps_max` em todas as séries do exercício. Após o incremento de carga, o número de repetições do plano pode ser redefinido para `meta_reps_min`.

Auditoria:

- Registrar em `audit_log` os campos criados/atualizados (`old_value_json` com o estado anterior, `new_value_json` com o payload persistido), com `who` extraído do contexto de autenticação.

Erros comuns:

- 400: Campo obrigatório ausente ou inválido (ex.: `"tipo_progressao" é obrigatório`).
- 400: Parâmetros inconsistentes com o `tipo_progressao` (ex.: `tipo_progressao` é `linear`, mas `incremento_kg` não foi fornecido).
- 404: `aluno_id` ou `exercicio_plano_id` não encontrado.

---

### Autenticação

- Requer autenticação do `personal` via Bearer Token (JWT). O `personal_id` do token deve ser o dono do plano de treino ao qual o aluno está associado.

---

### Payload de Requisição (JSON)

Campos aceitos:

```json
{
  "tipo_progressao": "linear" | "dupla" (obrigatório),
  "incremento_kg": number,
  "meta_reps_min": integer,
  "meta_reps_max": integer,
  "observacoes": "string"
}
````

Notas:

  - A combinação de campos obrigatórios varia conforme o `tipo_progressao`.

-----

### Exemplo 1 — Requisição válida (Progressão Linear)

Define que, para o exercício X, a carga deve aumentar 2.5kg sempre que o aluno cumprir a meta do treino anterior.

```json
PUT /api/alunos/<uuid-aluno>/exercicios-plano/<uuid-exercicio-plano>/regra-progressao
Content-Type: application/json
Authorization: Bearer <token>

{
  "tipo_progressao": "linear",
  "incremento_kg": 2.5,
  "observacoes": "Progressão padrão para exercícios compostos."
}
```

### Exemplo 2 — Requisição válida (Dupla Progressão)

Define que o aluno deve trabalhar na faixa de 8-12 repetições. Ao atingir 12 repetições em todas as séries, a carga aumenta 2.0kg e as repetições voltam para 8.

```json
PUT /api/alunos/<uuid-aluno>/exercicios-plano/<uuid-exercicio-plano>/regra-progressao
Content-Type: application/json
Authorization: Bearer <token>

{
  "tipo_progressao": "dupla",
  "incremento_kg": 2.0,
  "meta_reps_min": 8,
  "meta_reps_max": 12
}
```

-----

### Resposta de Sucesso (200 OK)

Retorna o objeto da regra de progressão que foi criado ou atualizado.

```json
{
  "id": "uuid-regra",
  "exercicio_plano_id": "<uuid-exercicio-plano>",
  "tipo_progressao": "linear",
  "incremento_kg": 2.5,
  "meta_reps_min": null,
  "meta_reps_max": null,
  "observacoes": "Progressão padrão para exercícios compostos.",
  "created_at": "timestamp",
  "updated_at": "timestamp"
}
```

-----

### Respostas de Erro (exemplos)

```sh
400 Bad Request
{
  "error": "Para o tipo 'linear', o campo 'incremento_kg' é obrigatório e deve ser maior que 0."
}

404 Not Found
{
  "error": "exercicio_plano_id não encontrado."
}
```

-----

### Lógica de Aplicação (Como a regra é usada)

É fundamental entender que este endpoint **apenas define a regra**. A sua execução ocorrerá em outro fluxo de trabalho, provavelmente acionado por um endpoint gerenciado por **Antonio Neto** (Gestão de treinos) ou **Leonardo Ribeiro** (Histórico de peso/desempenho):

1.  Um aluno finaliza um treino e o registra no sistema (ex: `POST /api/treinos/sessoes`).
2.  O backend recebe os dados da sessão (exercícios, séries, repetições, carga).
3.  Para cada exercício concluído, o sistema verifica se existe uma `regra_progressao` associada.
4.  **Se existir**, o sistema avalia se as condições da regra foram atendidas (ex: todas as repetições foram feitas? A meta máxima de repetições foi atingida?).
5.  **Se as condições forem atendidas**, o sistema calcula a nova carga (`carga_atual + incremento_kg`) e atualiza o `exercicio_plano` do aluno para que, no próximo treino, a nova carga já esteja sugerida.

-----

### Critérios de Aceitação

  - Ao enviar um payload válido, a regra é criada/atualizada e retornada com status `200`.
  - O sistema rejeita regras com combinações inválidas de parâmetros (ex: `dupla` sem `meta_reps`).
  - O sistema retorna `404` se os IDs na URL não existirem.
  - A regra criada fica corretamente associada ao exercício do plano do aluno no banco de dados.
  - Uma atualização (enviar um `PUT` para o mesmo endpoint com dados diferentes) modifica a regra existente em vez de criar uma nova.

<!-- end list -->

```
```
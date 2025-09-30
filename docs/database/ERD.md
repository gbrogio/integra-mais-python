# Documentação do Projeto — Sistema de Gerenciamento de Treinos e Progresso

**Versão:** 1.0
**Data:** 10/09/2025
**Autores:** PO/PM (Documento gerado sob demanda)

---

## Sumário

1. [Visão Geral](https://chatgpt.com/c/68c2023a-d7f0-8323-b5e5-8ac1201a1716#vis%C3%A3o-geral)
2. [Escopo e Objetivos](https://chatgpt.com/c/68c2023a-d7f0-8323-b5e5-8ac1201a1716#escopo-e-objetivos)
3. [Stakeholders](https://chatgpt.com/c/68c2023a-d7f0-8323-b5e5-8ac1201a1716#stakeholders)
4. [Terminologia](https://chatgpt.com/c/68c2023a-d7f0-8323-b5e5-8ac1201a1716#terminologia)
5. [Requisitos Funcionais (detalhados por feature)](https://chatgpt.com/c/68c2023a-d7f0-8323-b5e5-8ac1201a1716#requisitos-funcionais)

   - 5.1 [Cadastro de Alunos](https://chatgpt.com/c/68c2023a-d7f0-8323-b5e5-8ac1201a1716#cadastro-de-alunos)
   - 5.2 [Histórico de Peso e Gráficos de Progresso](https://chatgpt.com/c/68c2023a-d7f0-8323-b5e5-8ac1201a1716#hist%C3%B3rico-de-peso-e-gr%C3%A1ficos-de-progresso)
   - 5.3 [Gestão de Exercícios](https://chatgpt.com/c/68c2023a-d7f0-8323-b5e5-8ac1201a1716#gest%C3%A3o-de-exerc%C3%ADcios)
   - 5.4 [Montagem de Séries e Programas](https://chatgpt.com/c/68c2023a-d7f0-8323-b5e5-8ac1201a1716#montagem-de-s%C3%A9ries-e-programas)
   - 5.5 [Cards de Treino (UI) e Edição de Exercícios](https://chatgpt.com/c/68c2023a-d7f0-8323-b5e5-8ac1201a1716#cards-de-treino-ui-e-edi%C3%A7%C3%A3o-de-exerc%C3%ADcios)
   - 5.6 [Progressão Automática de Carga e Rotação de Treinos](https://chatgpt.com/c/68c2023a-d7f0-8323-b5e5-8ac1201a1716#progress%C3%A3o-autom%C3%A1tica-de-carga-e-rota%C3%A7%C3%A3o-de-treinos)
   - 5.7 [Gestão de Planos, Pagamentos e Vencimentos](https://chatgpt.com/c/68c2023a-d7f0-8323-b5e5-8ac1201a1716#gest%C3%A3o-de-planos-pagamentos-e-vencimentos)
   - 5.8 [Relatórios, Exportação e API de Integração](https://chatgpt.com/c/68c2023a-d7f0-8323-b5e5-8ac1201a1716#relat%C3%B3rios-exporta%C3%A7%C3%A3o-e-api-de-integra%C3%A7%C3%A3o)
   - 5.9 [Notificações e Alertas](https://chatgpt.com/c/68c2023a-d7f0-8323-b5e5-8ac1201a1716#notifica%C3%A7%C3%B5es-e-alertas)
   - 5.10 [Auditoria, Logs e Histórico de Mudanças](https://chatgpt.com/c/68c2023a-d7f0-8323-b5e5-8ac1201a1716#auditoria-logs-e-hist%C3%B3rico-de-mudan%C3%A7as)

6. [Requisitos Não Funcionais](https://chatgpt.com/c/68c2023a-d7f0-8323-b5e5-8ac1201a1716#requisitos-n%C3%A3o-funcionais)
7. [Modelo de Dados (tabelas principais)](https://chatgpt.com/c/68c2023a-d7f0-8323-b5e5-8ac1201a1716#modelo-de-dados)
8. [APIs e Exemplos de Payloads](https://chatgpt.com/c/68c2023a-d7f0-8323-b5e5-8ac1201a1716#apis-e-exemplos-de-payloads)
9. [Telas / Fluxos de UI (descrição)](https://chatgpt.com/c/68c2023a-d7f0-8323-b5e5-8ac1201a1716#telas--fluxos-de-ui-descri%C3%A7%C3%A3o)
10. [Regras de Negócio e Casos de Uso Críticos](https://chatgpt.com/c/68c2023a-d7f0-8323-b5e5-8ac1201a1716#regras-de-neg%C3%B3cio-e-casos-de-uso-cr%C3%ADticos)
11. [Validações e Mensagens de Erro](https://chatgpt.com/c/68c2023a-d7f0-8323-b5e5-8ac1201a1716#valida%C3%A7%C3%B5es-e-mensagens-de-erro)
12. [Critérios de Aceitação / Testes de Aceitação](https://chatgpt.com/c/68c2023a-d7f0-8323-b5e5-8ac1201a1716#crit%C3%A9rios-de-aceita%C3%A7%C3%A3o--testes-de-aceita%C3%A7%C3%A3o)
13. [Roadmap de Entregas (sugestão)](https://chatgpt.com/c/68c2023a-d7f0-8323-b5e5-8ac1201a1716#roadmap-de-entregas-sugest%C3%A3o)
14. [Operação e DevOps (backup, segurança, logs)](https://chatgpt.com/c/68c2023a-d7f0-8323-b5e5-8ac1201a1716#opera%C3%A7%C3%A3o-e-devops-backup-seguran%C3%A7a-logs)
15. [Anexos: Exemplos de Scripts Python e SQL (amostra)](https://chatgpt.com/c/68c2023a-d7f0-8323-b5e5-8ac1201a1716#anexos-exemplos-de-scripts-python-e-sql-amostra)

---

## Visão Geral

Sistema para gerenciar treinos e acompanhar o progresso físico (principalmente peso) de alunos por parte de um **personal trainer**. O sistema é projetado para uso exclusivo do personal (não há conta pública para alunos), que cria e gerencia alunos, monta treinos, registra progresso e administra pagamentos e planos.

Objetivos centrais:

- Facilitar o acompanhamento do progresso (peso) ao longo do tempo.
- Automatizar progressões de cargas semanais e rotação de treinos mensal.
- Centralizar a gestão financeira de mensalidades e planos.
- Reduzir trabalho manual (soma de cargas etc.) e diminuir erros.

---

## Escopo e Objetivos

**Incluso:** cadastro e gestão de alunos, histórico de peso, cadastro de exercícios, montagem de séries e cards de treino, progressões automáticas (configuráveis), controle de planos/pagamentos, gráficos de progresso, exportação de relatórios, automações por script Python.

**Excluído (fora do escopo inicial):** funcionalidades multi-personal (contas públicas para alunos), integração com gateways de pagamento complexos (recorrência avançada) além do básico, marketplace, integração com wearables (fase 2 possível).

---

## Stakeholders

- **Personal Trainer (Usuário Principal / Admin):** cria alunos, monta treinos, registra progresso, gerencia pagamentos.
- **Equipe de Desenvolvimento:** implementa backend, frontend, integrações e testes.
- **PO/PM:** prioriza features, valida entregas.
- **Suporte / Operações:** manutenção, backups e monitoramento.

---

## Terminologia

- **Aluno:** cliente do personal, com dados pessoais e histórico de treinos.
- **Exercício:** item base com nome, carga, repetições, tipo (repetição/segundos), tempo de descanso, C.E (controle de carga externa).
- **Série / Programação:** conjunto ordenado de exercícios com datas de início e término.
- **Card de Treino:** interface/tela contendo um conjunto de exercícios aplicados a um aluno (pode conter ajustes locais de carga/rep).
- **Plano:** periodicidade contratada (mensal, trimestral, semestral, anual).

---

## Requisitos Funcionais

> Para cada funcionalidade, descrevemos: objetivo, campos/dados necessários, operações (CRUD), validações, comportamento no UI e critérios de aceitação.

### 5.1 Cadastro de Alunos

**Objetivo:** permitir ao personal cadastrar e gerenciar informações básicas e administrativas do aluno.

**Campos (mínimos):**

- `id` (uuid)
- `nome` (string, obrigatório)
- `altura_cm` (number, opcional)
- `idade` (int, opcional)
- `restricoes` (string, opcional; histórico/observações médicas)
- `data_inicio_treino` (date)
- `frequencia_semana` (int) — ex: 3x/semana
- `plano_id` (FK) — referencia a Plano escolhido
- `plano_data_inicio` (date)
- `plano_data_vencimento` (date)
- `forma_pagamento` (enum: pix | credito | debito)
- `data_pagamento` (date) — data da última mensalidade paga
- `observacoes` (text)
  **Operações:**

- Criar, ler, atualizar, excluir (CRUD).
- Importar via CSV (opção).
  **Validações:**

- `nome` obrigatório.
- `frequencia_semana` entre 1 e 7.
- `plano_data_vencimento` >= `plano_data_inicio` .
  **UI:** Tela de lista de alunos com busca/ordenar por nome/último pagamento; botão para abrir ficha do aluno. Ficha com abas: Dados gerais, Pagamentos, Peso/Progresso, Treinos.

**Critério de Aceitação:** Criar aluno com todos os campos obrigatórios e visualizar ficha com dados salvos.

---

### 5.2 Histórico de Peso e Gráficos de Progresso

**Objetivo:** registrar o peso do aluno em datas e apresentar evolução gráfica.

**Campos (WeightRecord):**

- `id` , `aluno_id` , `data` (date), `peso_kg` (decimal), `observacao` (text opcional).
  **Operações:**

- Adicionar registro de peso (manual).
- Editar / apagar registro.
  **Regras:**

- Permitir múltiplos registros por mês.
- Apresentar gráfico (linha) com seleção de período (7d, 30d, 90d, 6m, 1y, personalizado).
- Mostrar variação percentual e valores absolutos (delta desde início, delta no período).
  **UI:** Gráfico interativo com tooltip por ponto, tabela abaixo com registros. Botão "Adicionar Peso" na ficha do aluno.

**Critério de Aceitação:** Ao adicionar peso, ponto aparece imediatamente no gráfico e cálculos (delta) são atualizados.

---

### 5.3 Gestão de Exercícios

**Objetivo:** permitir cadastro reusável de exercícios com atributos principais.

**Campos (Exercise):**

- `id` , `nome` , `descricao` (opcional), `tipo` (enum: repeticao | segundos), `carga_padrao` (number), `tempo_descanso_padrao` (segundos), `ce` (boolean) — C.E (controle de carga externa), `grupo_muscular` (tags).
  **Operações:** CRUD de exercícios. Suporte a duplicar e versionar exercícios (opcional).

**UI:** Tela de biblioteca de exercícios com filtros por grupo muscular, pesquisa e preview.

**Critério de Aceitação:** Criar exercício e poder instanciá-lo dentro de um card de treino.

---

### 5.4 Montagem de Séries e Programas

**Objetivo:** criar séries/treinos compostos por exercícios, com repetições, cargas e datas.

**Entidades:** Série / Programa que contém `exercises_ordered` (lista com objetos que referenciam Exercise + overrides):

- `exercicio_id` , `carga` (number), `repeticoes` (array ou padrão como string: "[10,20,10,10]"), `tipo` (repeticao/segundos), `tempo_descanso` (s), `comentario`
  **Campos adicionais da série:** `id`, `nome`, `aluno_id` (opcional, quando série for específica), `data_inicio`, `data_termino`, `frequencia_semana` (padrão herdado), `status` (ativa|inativa|template).

**Operações:** criar série (template ou atrelada a aluno), atribuir a aluno, clonar série, editar itens da série.

**UI:** editor de série com drag & drop reorder dos exercícios, campos inline para carga/reps/descanso, salvar como template.

**Critério de Aceitação:** Criar uma série, atribuir a um aluno e visualizar corretamente no card de treino.

---

### 5.5 Cards de Treino (UI) e Edição de Exercícios

**Objetivo:** área onde o personal monta cards de treino (sessões) e dentro adiciona/edita exercícios, recadastrando carga, repetições e tempo.

**Comportamento importante:**

- Cada card representa uma sessão (ex.: Treino A - Peito/Tríceps).
- Dentro de um card, o personal adiciona exercícios da biblioteca ou cria novos exercícios inline.
- Para cada exercício dentro do card existe a possibilidade de **override**: carga específica, repetições, tempo de descanso que **não alteram o exercício base**.
  **Campos extras na instância:** `carga_atual`, `reps_atual`, `descanso_atual`, `ordem`.

**Recadastramento:** quando o personal faz alterações nas cargas/repetições dentro do card, o sistema registra versão/histórico daquela sessão (para auditoria) e sinaliza quando houve mudança.

**Critério de Aceitação:** editar um exercício dentro do card salva as alterações na instância sem alterar o exercício base e registra histórico.

---

### 5.6 Progressão Automática de Carga e Rotação de Treinos

**Objetivo:** automatizar progressões semanais de carga e rotinas mensais de troca de treinos segundo regras configuráveis.

**Configuração por padrão (sugestão):**

- **Progressão semanal:** aumentar carga em `X kg` ou `Y%` por semana (ex.: +2,5kg ou +5%), configurável por exercício ou série.
- **Rotação mensal:** trocar o card/treino do aluno por outro template no começo de cada mês ou após N semanas (configurável).
  **Regras detalhadas:**

- Somente exercícios cujo `tipo` = `repeticao` são elegíveis à progressão automática de carga. Exercícios `segundos` mantêm tempo.
- Progressões consideram `C.E` (Controle de Carga Externa): para exercícios com CE=true, a progressão é feita manualmente (i.e. o sistema apenas sugere).
- Limite de segurança: o sistema não faz progressão que aumente mais que `M` kg por semana ou `P` % sem confirmação (configurável).
- Históricos de alterações são registrados para auditoria (quem/quando/valor anterior/valor novo).
  **Operação:**

- Job agendado semanalmente (cron) aplica a progressão conforme regra ou gera sugestões a confirmar manualmente.
- O personal pode reverter progressões (rollback) por cartão/semana.
  **UI:** painel de Progressões com lista de exercícios elegíveis, próxima carga sugerida, botão "Aplicar para X alunos" ou "Aplicar somente para este aluno".

**Critério de Aceitação:** regra aplicada semanalmente e cargas atualizadas automaticamente ou após confirmação, com registros.

---

### 5.7 Gestão de Planos, Pagamentos e Vencimentos

**Objetivo:** controlar planos (mensal, trimestral, semestral, anual), datas de início/vencimento e o histórico de pagamentos (forma: Pix, crédito, débito).

**Entidades:** `Plano` e `Pagamento`.

**Plano:** `id`, `nome`, `duracao_meses`, `preco`, `descricao`.

**Pagamento:** `id`, `aluno_id`, `plano_id`, `valor`, `forma_pagamento` (pix|credito|debito), `data_pagamento`, `status` (pago|pendente|atrasado), `transacao_id` (opcional).

**Regras:**

- Ao criar/atualizar o plano do aluno, calcular automaticamente `plano_data_vencimento` = `plano_data_inicio` + `duracao` .
- Notificar o personal X dias antes do vencimento.
- Marcar aluno como inativo para treinos (opcional) se `plano_data_vencimento` < hoje e não houver pagamento de renovação.
  **UI:** na ficha do aluno mostrar resumo do plano atual, próximo vencimento e histórico de pagamentos. Tela de marcar pagamento manual.

**Critério de Aceitação:** registro de pagamento altera status e atualiza `plano_data_vencimento` conforme plano.

---

### 5.8 Relatórios, Exportação e API de Integração

**Objetivo:** permitir extração de dados e integração com outros sistemas.

**Funcionalidades:**

- Exportar lista de alunos e histórico de peso em CSV/XLSX.
- Gerar relatório PDF de evolução do aluno (peso + treinos recentes).
- API REST para operações básicas (autenticação via token).
  **Integração Python (solução proposta):** script Python que:

- Conecta ao banco (ou consome API).
- Gera cálculo automático de soma de carga por sessão e por semana (substituindo somas manuais).
- Cria planilhas/resumos (XLSX) e envia por e-mail ou salva em storage.
  **Critério de Aceitação:** exportar CSV com dados corretos e gerar relatório PDF pronto para impressão.

---

### 5.9 Notificações e Alertas

**Tipos:**

- **Vencimento de Plano:** aviso X dias antes, e no dia do vencimento.
- **Pagamento Atrasado:** alerta quando pagamento não foi registrado 7 dias após vencimento.
- **Sugerir Progressão:** notificações com sugestões semanais de progressão.
  **Meios:** notificações internas (UI) e envio de e-mail (opcional).

**Critério de Aceitação:** notificações aparecem corretamente e e-mails são enviados quando configurado.

---

### 5.10 Auditoria, Logs e Histórico de Mudanças

**Requisitos:**

- Registrar logs de criação/alteração/exclusão de entidades críticas: alunos, séries, cards e pagamentos.
- Armazenar `who` , `when` , `old_value` , `new_value` .
  **UI:** painel de auditoria com filtros por aluno, data e tipo de ação.

**Critério de Aceitação:** alterações evidentes podem ser rastreadas com dados suficientes para auditoria.

---

## Requisitos Não Funcionais

- **Segurança:** TLS obrigatório, armazenamento seguro de dados pessoais, políticas de senha (se aplicável) e token JWT para API.
- **Performance:** listagens paginadas; resposta < 300ms para consultas comuns (meta).
- **Disponibilidade:** deploy em infra redundante; backups diários.
- **Escalabilidade:** arquitetura modular (backend + frontend + DB) para escalar conforme número de alunos/personal.
- **Acessibilidade:** UI responsiva, WCAG básico (contrastes, labels).
- **Internacionalização:** textos em PT-BR como padrão.

---

## Modelo de Dados (tabelas principais)

> Abaixo uma versão compacta do modelo; adaptar para o SGBD escolhido (ex.: Postgres).

### `personal` (usuário)

- `id: uuid` (PK)
- `nome: string`
- `email: string`
- `password_hash: string`
- `created_at` , `updated_at`

### `aluno`

- `id: uuid` (PK)
- `personal_id: uuid` (FK)
- `nome: string`
- `altura_cm: numeric`
- `idade: int`
- `restricoes: text`
- `data_inicio_treino: date`
- `frequencia_semana: int`
- `plano_id: uuid`
- `plano_data_inicio: date`
- `plano_data_vencimento: date`
- `forma_pagamento: enum`
- `data_pagamento: date`
- `created_at` , `updated_at`

### `weight_record`

- `id` , `aluno_id` , `data` , `peso_kg` , `observacao` , `created_at`

### `exercise`

- `id` , `nome` , `descricao` , `tipo` (repeticao|segundos), `carga_padrao` , `tempo_descanso_padrao` , `ce` , `grupo_muscular` , `created_at` , `updated_at`

### `serie` (template ou atribuida)

- `id` , `nome` , `aluno_id` (nullable), `data_inicio` , `data_termino` , `status` , `created_at`

### `serie_exercise` (itens da série)

- `id` , `serie_id` , `exercise_id` , `ordem` , `carga` , `repeticoes_json` (ex: [10,20,10,10]), `tipo` , `tempo_descanso` , `comentario` , `created_at`

### `card_treino`

- `id` , `aluno_id` , `nome` , `data` , `nota` , `created_at`

### `card_exercise`

- `id` , `card_id` , `exercise_id` , `ordem` , `carga_atual` , `reps_atual_json` , `tempo_descanso_atual` , `created_at`

### `plano`

- `id` , `nome` , `duracao_meses` , `preco` , `descricao`

### `pagamento`

- `id` , `aluno_id` , `plano_id` , `valor` , `forma_pagamento` , `data_pagamento` , `status` , `transacao_id`

### `audit_log`

- `id` , `entity_type` , `entity_id` , `action` , `who` , `timestamp` , `old_value_json` , `new_value_json`

---

## APIs e Exemplos de Payloads (RESTful — exemplos)

> Autenticação: Bearer token (JWT)

**Criar aluno**
`POST /api/alunos`

```json
{
  "nome": "João Silva",
  "altura_cm": 178,
  "idade": 28,
  "restricoes": "Nenhuma",
  "data_inicio_treino": "2025-09-01",
  "frequencia_semana": 3,
  "plano_id": "uuid-do-plano",
  "plano_data_inicio": "2025-09-01",
  "forma_pagamento": "pix",
  "data_pagamento": "2025-09-01"
}
```

**Adicionar registro de peso**
`POST /api/alunos/{aluno_id}/peso`

```json
{
  "data": "2025-09-10",
  "peso_kg": 82.4,
  "observacao": "Primeira pesagem"
}
```

**Criar card de treino**
`POST /api/alunos/{aluno_id}/cards`

```json
{
  "nome": "Treino A - Peito",
  "data": "2025-09-10",
  "exercicios": [
    {
      "exercise_id": "uuid-1",
      "carga": 40,
      "repeticoes": [10, 10, 8],
      "tempo_descanso": 60
    },
    {
      "exercise_id": "uuid-2",
      "carga": 20,
      "repeticoes": [15, 15],
      "tempo_descanso": 45
    }
  ]
}
```

**Aplicar progressão semanal (exemplo) — endpoint administrativo**
`POST /api/progressao/aplicar`

```json
{
  "tipo": "semanal",
  "strategia": "percentual",
  "valor": 5,
  "scope": { "aluno_ids": ["uuid1", "uuid2"], "excluir_ce": true }
}
```

---

## Telas / Fluxos de UI (descrição)

1. **Login do Personal** — autenticação segura.
2. **Dashboard** — resumo com número de alunos, pagamentos pendentes, sugestões de progressão e widgets de atividade.
3. **Lista de Alunos** — busca, filtros por status de pagamento, clique para abrir ficha.
4. **Ficha do Aluno** — abas: Dados, Pagamentos, Peso/Progresso (gráfico), Cards de Treino, Histórico.
5. **Editor de Série/Card** — drag & drop, edição inline, salvar como template.
6. **Gestão de Exercícios** — biblioteca, criar/editar/duplicar.
7. **Relatórios** — exportar CSV/PDF.
8. **Configurações de Progressão** — definir %, kg, regras por exercício.
9. **Painel de Auditoria** — histórico de alterações.

---

## Regras de Negócio e Casos de Uso Críticos

### Regras principais

- Progressão automática não se aplica a exercícios do tipo `segundos` .
- Para `C.E` marcado, progressão é somente sugestão; requer confirmação manual.
- Renovação de plano atualiza automaticamente `plano_data_vencimento` .
- Aluno com pagamento em atraso > 30 dias entra em flag `suspenso` (opcional) — não recebe novos cards automaticamente.

### Cenários críticos (exemplos)

1. **Aluno paga renovação antes do vencimento:** o sistema estende `plano_data_vencimento` conforme `duracao_meses` do plano.
2. **Aplicação de progressão que supera limite de segurança:** progressão é colocada em hold e notifica o personal.
3. **Roll back de progressão:** o personal reverte para valor anterior através da UI de auditoria.

---

## Validações e Mensagens de Erro (exemplos)

- `400` — `"nome" é obrigatório`
- `400` — `"frequencia_semana" deve ser entre 1 e 7`
- `409` — conflito ao criar aluno com mesmo email (se email for usado)
- Erros de pagamento: `422` - `transacao_id inválida` .

---

## Critérios de Aceitação / Testes de Aceitação

Para cada feature, criar casos de teste que validem os fluxos básicos e as regras de negócio.

Exemplos:

- Criar aluno -> adicionar peso -> verificar gráfico atualiza.
- Criar card -> aplicar progressão semanal -> confirmar que `carga_atual` incrementou conforme regra.
- Registrar pagamento -> verificar `plano_data_vencimento` atualizado.

---

## Roadmap de Entregas (sugestão de releases rápidos)

- **MVP (2–4 semanas):** cadastro de alunos, biblioteca de exercícios, criação de cards, registro de peso, gráfico básico, gestão manual de pagamentos.
- **v1 (4–8 semanas):** progressão automática semanal (sugestão e aplicação), templates de série, auditoria básica, export CSV.
- **v2 (8–12 semanas):** integração com gateway simples (webhook), relatórios PDF, notificações por e-mail, melhorias de UX.

---

## Operação e DevOps (backup, segurança, logs)

- **Backups:** dump diário do banco e retenção mínima de 30 dias.
- **Logs:** centralizar logs (ex.: ELK/CloudWatch) e rotacionar.
- **Segurança:** HTTPS obrigatório, criptografia de dados sensíveis em repouso se necessário, políticas de acesso para a equipe.
- **Monitoramento:** alertas para falhas do job de progressão e para quedas do serviço.

---

## Anexos: Exemplos de Scripts Python e SQL (amostra)

### Exemplo de script Python (pseudocódigo) para calcular soma de cargas e gerar planilha

```python
# pseudocódigo
import psycopg2
import pandas as pd

# conectar DB
# buscar cards de treino e cargas por aluno em período
# calcular soma de cargas por sessão
# gerar xlsx
```

### Exemplo SQL (amostra simplificada)

```sql
CREATE TABLE aluno (
  id UUID PRIMARY KEY,
  personal_id UUID,
  nome TEXT NOT NULL,
  altura_cm NUMERIC,
  idade INT,
  data_inicio_treino DATE,
  frequencia_semana INT,
  plano_id UUID,
  plano_data_inicio DATE,
  plano_data_vencimento DATE
);
```

---

## Próximos passos sugeridos

1. Validar com o personal os parâmetros padrão de progressão (ex.: +2,5kg ou +5%).
2. Definir se a progressão será automática por padrão ou apenas sugestão.
3. Priorizar o MVP com as telas essenciais (ficha do aluno, cards, pesagem, pagamentos manuais).
4. Agendar uma sessão de revisão para incluir campos específicos que o personal utilize (ex.: medidas corporais além do peso).

---

**Fim do documento.**

Se quiser, gero esse mesmo conteúdo em um arquivo `.md` para download, ou converto para PDF/OpenAPI/diagrama ER. Também posso gerar os endpoints Swagger/OpenAPI automaticamente com base nesse modelo.

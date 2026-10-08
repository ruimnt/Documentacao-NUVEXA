# Documentação da API Nuvexa
**Público:** desenvolvedores, engenheiros de integração e equipes técnicas.

**Objetivo:** documentar a API REST fictícia usada para integrar aplicações externas à Nuvexa.

## Visão geral
URL base:
```text
https://api.nuvexa.example/v1
```
A API utiliza:
- Recursos no estilo REST
- HTTPS
- Bodies de request e response em JSON
- Autenticação por bearer token
- Métodos HTTP padrão
- Paginação em endpoints de coleção
## Autenticação
A Nuvexa utiliza bearer tokens.
Inclua o token em todas as requisições autenticadas:
```http
Authorization: Bearer nva_live_xxxxxxxxx
```
### Fluxo de autenticação
**[Fluxo de autenticação da API Nuvexa]**

(**INSERIR IMAGEM AQUI**../../images/authentication-flow.svg)

### Tratamento de tokens
Armazene tokens em variáveis de ambiente ou em um secret manager.
```bash
export NUVEXA_TOKEN="nva_test_xxxxxxxxx"
```
> **Aviso:** nunca faça commit de um API token no Git, inclua-o em código executado no cliente ou cole-o em chamados de suporte.
## Métodos HTTP
| Método | Finalidade |
|---|---|
| `GET` | Consultar um recurso |
| `POST` | Criar um recurso ou iniciar uma ação |
| `PATCH` | Atualizar parcialmente um recurso |
| `DELETE` | Remover ou arquivar um recurso |
## Headers de response comuns
```http
Content-Type: application/json
X-Request-ID: req_8a91d2
```
## Listar workflows
### `GET /workflows`
Retorna os workflows do workspace autenticado.
### Parâmetros de query
| Parâmetro | Tipo | Obrigatório | Descrição |
|---|---|---:|---|
| `page` | integer | Não | Número da página. Padrão: `1` |
| `limit` | integer | Não | Quantidade de registros. Padrão: `20`; máximo: `100` |
| `status` | string | Não | Filtra por `draft`, `published` ou `archived` |
### Request
```bash
curl --request GET \
  --url 'https://api.nuvexa.example/v1/workflows?status=published&limit=20' \
  --header "Authorization: Bearer $NUVEXA_TOKEN"
```
### Response
```http
HTTP/1.1 200 OK
Content-Type: application/json
X-Request-ID: req_8a91d2
```
```json
{
  "data": [
    {
      "id": "wf_8f42",
      "name": "Customer onboarding",
      "status": "published",
      "version": 3,
      "created_at": "2026-10-05T12:00:00Z"
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 1,
    "has_next": false
  }
}
```
## Consultar um workflow
### `GET /workflows/{workflow_id}`
Retorna um workflow específico.
### Parâmetro de path
| Parâmetro | Tipo | Obrigatório | Descrição |
|---|---|---:|---|
| `workflow_id` | string | Sim | Identificador do workflow |
### Request
```bash
curl --request GET \
  --url https://api.nuvexa.example/v1/workflows/wf_8f42 \
  --header "Authorization: Bearer $NUVEXA_TOKEN"
```
## Criar um workflow
### `POST /workflows`
Cria um novo workflow com status de rascunho.
### Body da requisição
```json
{
  "name": "Customer onboarding",
  "description": "Routes new customers to the correct onboarding team.",
  "trigger": {
    "type": "webhook",
    "config": {
      "event": "customer.created"
    }
  },
  "steps": [
    {
      "type": "condition",
      "name": "Check plan",
      "config": {
        "field": "plan",
        "operator": "equals",
        "value": "enterprise"
      }
    }
  ]
}
```
### Response
```http
HTTP/1.1 201 Created
```
```json
{
  "id": "wf_a19c",
  "name": "Customer onboarding",
  "status": "draft",
  "version": 1
}
```
## Atualizar um workflow
### `PATCH /workflows/{workflow_id}`
Atualiza propriedades editáveis do workflow.
### Request
```http
PATCH /v1/workflows/wf_a19c
Authorization: Bearer nva_test_xxxxxxxxx
Content-Type: application/json
```
```json
{
  "description": "Routes new customers based on subscription plan."
}
```
### Response
```json
{
  "id": "wf_a19c",
  "description": "Routes new customers based on subscription plan.",
  "status": "draft"
}
```
## Publicar um workflow
### `POST /workflows/{workflow_id}/publish`
Publica o rascunho atual como uma nova versão imutável.
### Request
```bash
curl --request POST \
  --url https://api.nuvexa.example/v1/workflows/wf_a19c/publish \
  --header "Authorization: Bearer $NUVEXA_TOKEN"
```
### Response
```json
{
  "id": "wf_a19c",
  "status": "published",
  "version": 2,
  "published_at": "2026-10-05T14:30:00Z"
}
```
## Iniciar uma execução
### `POST /workflows/{workflow_id}/executions`
Inicia um workflow com os dados de entrada fornecidos.
### Request
```json
{
  "input": {
    "customer_id": "cus_1042",
    "email": "alex@example.com",
    "plan": "enterprise"
  },
  "idempotency_key": "signup-cus-1042"
}
```
### Response
```http
HTTP/1.1 202 Accepted
```
```json
{
  "id": "ex_01J7K3W9M5",
  "workflow_id": "wf_a19c",
  "status": "running",
  "created_at": "2026-10-05T14:31:00Z"
}
```
> **Dica:** use uma idempotency key quando o cliente puder repetir a mesma requisição. Isso ajuda a evitar execuções duplicadas.
## Consultar uma execução
### `GET /executions/{execution_id}`
Retorna o status atual e os resultados das etapas.
```bash
curl --request GET \
  --url https://api.nuvexa.example/v1/executions/ex_01J7K3W9M5 \
  --header "Authorization: Bearer $NUVEXA_TOKEN"
```
Exemplo de response:
```json
{
  "id": "ex_01J7K3W9M5",
  "workflow_id": "wf_a19c",
  "status": "succeeded",
  "started_at": "2026-10-05T14:31:00Z",
  "completed_at": "2026-10-05T14:31:03Z",
  "steps": [
    {
      "name": "Check plan",
      "status": "succeeded"
    }
  ]
}
```
## Excluir um workflow em rascunho
### `DELETE /workflows/{workflow_id}`
Exclui um workflow que nunca foi publicado.
```bash
curl --request DELETE \
  --url https://api.nuvexa.example/v1/workflows/wf_a19c \
  --header "Authorization: Bearer $NUVEXA_TOKEN"
```
Response:
```http
HTTP/1.1 204 No Content
```
Workflows publicados não podem ser excluídos por esse endpoint.
## Erros
A Nuvexa utiliza códigos HTTP padrão e um body de erro estruturado.
### Formato do erro
```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "The field 'name' is required.",
    "details": [
      {
        "field": "name",
        "reason": "required"
      }
    ]
  }
}
```
### Erros comuns
| HTTP status | Código | Significado |
|---:|---|---|
| `400` | `VALIDATION_ERROR` | Os dados da requisição são inválidos |
| `401` | `AUTHENTICATION_FAILED` | Token ausente ou inválido |
| `403` | `FORBIDDEN` | O token não possui permissão |
| `404` | `RESOURCE_NOT_FOUND` | O recurso não existe |
| `409` | `CONFLICT` | A requisição conflita com o estado atual |
| `422` | `UNPROCESSABLE_ENTITY` | Falha de validação semântica |
| `429` | `RATE_LIMITED` | Limite de requisições excedido |
| `500` | `INTERNAL_ERROR` | Erro inesperado no servidor |
| `503` | `SERVICE_UNAVAILABLE` | Serviço temporariamente indisponível |
## Rate limits
A API fictícia permite 600 requisições por minuto por workspace.
Quando o limite é excedido, a API retorna:
```http
HTTP/1.1 429 Too Many Requests
Retry-After: 10
```
Os clientes devem usar exponential backoff.
## Idempotência
Para operações que criam execuções, envie:
```http
Idempotency-Key: signup-cus-1042
```
Se a mesma chave for enviada novamente dentro da janela de idempotência, a Nuvexa retorna o resultado da operação original em vez de criar uma nova operação.
## Arquitetura
![Arquitetura da Nuvexa](../../images/architecture.svg)
Em alto nível:
```text
Cliente
  ↓ HTTPS
API Gateway
  ↓
Autenticação
  ↓
Workflow API
  ├── Serviço de workflows
  ├── Serviço de execução
  └── Serviço de conexões
        ↓
   Sistemas externos
```
## Versionamento
Os caminhos da API incluem a versão principal:
```text
/v1/
```
Alterações incompatíveis são introduzidas em uma nova versão principal.
Alterações compatíveis podem incluir novos campos de response, novos endpoints e parâmetros opcionais.
## Decisões de design da API
Os exemplos deste documento demonstram:
- URLs orientadas a recursos
- Uso correto de métodos HTTP
- Status codes explícitos
- Bodies JSON de request e response
- Parâmetros de path e query
- Headers de autenticação
- Erros estruturados
- Paginação
- Idempotência
- Rate limiting

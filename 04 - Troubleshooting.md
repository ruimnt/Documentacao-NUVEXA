# Troubleshooting da Nuvexa
**Público:** builders de workflows, administradores e equipes de suporte de primeiro nível.

**Objetivo:** oferecer um caminho de diagnóstico para falhas comuns de workflow, autenticação e integrações.

## Fluxo de troubleshooting
```text
Problema reportado
       ↓
É possível reproduzir?
   ┌───┴────┐
 Sim       Não
  ↓         ↓
Verificar  Verificar ID
logs       e entradas
  ↓
Identificar camada com falha
  ↓
Repetir após a correção
  ↓
Ainda falha?
  ├─ Sim → Coletar error ID e contatar suporte
  └─ Não → Registrar a solução
```
## A execução do workflow falhou
### Sintomas
O status da execução é `Failed`.
### Solução
1. Abra **Execuções**.
2. Selecione a execução com falha.
3. Identifique a primeira etapa que falhou.
4. Leia o código e a mensagem de erro.
5. Verifique a entrada da etapa.
6. Verifique a conexão utilizada.
7. Corrija a configuração.
8. Repita a execução.
> **Dica:** comece pela primeira etapa que falhou. Erros posteriores podem ser consequência do erro original.
## `401 Unauthorized`
### Sintomas
Uma requisição à API retorna:
```json
{
  "error": {
    "code": "AUTHENTICATION_FAILED",
    "message": "The access token is invalid or expired."
  }
}
```
### Solução
Verifique o header:
```http
Authorization: Bearer nva_live_xxxxxxxxx
```
Depois confirme:
- O token está ativo.
- O token pertence ao workspace correto.
- O token possui o escopo necessário.
- O relógio do seu sistema não está causando um problema de expiração.
## `403 Forbidden`
### Sintomas
O token é aceito, mas a operação não é permitida.
### Solução
Verifique o escopo do token e as permissões do usuário no workspace.
Por exemplo, criar um workflow pode exigir:
```text
workflows:write
```
## `404 Not Found`
### Sintomas
A API retorna:
```json
{
  "error": {
    "code": "RESOURCE_NOT_FOUND",
    "message": "Workflow wf_8f42 was not found."
  }
}
```
### Solução
1. Confirme o ID do recurso.
2. Confirme o workspace.
3. Verifique se o recurso foi arquivado.
4. Confirme se o ambiente da API está correto.
## `429 Too Many Requests`
### Sintomas
As requisições são rejeitadas com HTTP `429`.
### Solução
1. Leia o header `Retry-After`.
2. Aguarde o intervalo indicado.
3. Tente novamente usando exponential backoff.
4. Evite polling desnecessário.
Exemplo:
```http
HTTP/1.1 429 Too Many Requests
Retry-After: 10
```
Uma sequência simples de backoff pode ser:
```text
1s → 2s → 4s → 8s → 16s
```
## O webhook foi recebido, mas o workflow não iniciou
Verifique:
1. A URL do webhook está ativa.
2. O workflow está publicado.
3. O remetente recebeu HTTP `2xx`.
4. A requisição utiliza o content type esperado.
5. O payload contém os campos obrigatórios.
6. O evento aparece em **Execuções**.
Content type esperado:
```http
Content-Type: application/json
```
## Timeout em uma API externa
### Sintomas
Uma etapa do workflow informa:
```text
UPSTREAM_TIMEOUT
The external service did not respond within 30 seconds.
```
### Solução
1. Confirme se o serviço externo está disponível.
2. Verifique se a requisição está chegando ao endpoint correto.
3. Reduza o tamanho desnecessário do payload.
4. Repita a etapa.
5. Se o problema persistir, entre em contato com o provedor externo.
> **Nota:** repetir uma operação não idempotente pode criar duplicidades. Confirme o comportamento de idempotência da API externa antes de habilitar retries automáticos.
## Quando entrar em contato com o suporte
Inclua:
- ID do workspace
- ID da execução
- Data e hora, incluindo timezone
- Código do erro
- Endpoint, quando aplicável
- Request ID sanitizado
- Passos que já foram tentados
Não envie senhas, API tokens ou outros secrets.
### Exemplo para o suporte
```text
Workspace: ws_demo_42
Execução: ex_01J7K3W9M5
Horário: 2026-10-05 14:32 UTC
Erro: UPSTREAM_TIMEOUT
Request ID: req_8a91d2
```

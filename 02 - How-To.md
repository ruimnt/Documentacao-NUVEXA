# Guias How-to da Nuvexa
**Público:** builders de workflows, equipes de operações e administradores de workspace.
**Objetivo:** fornecer procedimentos objetivos para tarefas comuns na Nuvexa.
## Como criar um workflow de onboarding de clientes
Este exemplo cria um workflow que recebe um evento de cadastro, verifica o plano do cliente e envia uma notificação.
### Antes de começar
Você precisa de:
- Permissão de Builder ou Publisher.
- Um workspace Nuvexa.
- Uma URL de webhook de teste ou um evento de exemplo.
### Passos
1. Abra **Workflows** e selecione **Criar workflow**.
2. Dê ao workflow o nome `Onboarding de clientes`.
3. Em **Gatilho**, selecione **Webhook recebido**.
4. Adicione o payload de exemplo:
```json
{
  "customer_id": "cus_1042",
  "email": "alex@example.com",
  "plan": "enterprise"
}
```
5. Adicione uma etapa de **Condição**.
6. Defina a condição como `plan equals enterprise`.
7. No caminho **true**, adicione uma ação de **Enviar notificação**.
8. Configure como destinatária a equipe de onboarding.
9. Adicione uma segunda notificação ao caminho **false**.
10. Selecione **Salvar rascunho**.
11. Selecione **Testar**.
12. Verifique se o exemplo enterprise segue pelo caminho prioritário.
13. Selecione **Publicar**.
> **Dica:** teste os dois caminhos antes de publicar. Um teste bem-sucedido com um plano enterprise não valida o caminho de planos padrão.
## Como repetir uma execução com falha
1. Abra **Execuções**.
2. Selecione a execução com falha.
3. Expanda a etapa que falhou.
4. Revise a mensagem de erro.
5. Corrija a configuração ou a dependência externa.
6. Selecione **Repetir a partir da etapa com falha**.
A Nuvexa preserva o ID da execução original e cria uma nova tentativa.
## Como adicionar uma conexão de API
1. Abra **Configurações → Conexões**.
2. Selecione **Adicionar conexão**.
3. Selecione **HTTP API**.
4. Informe um nome para a conexão.
5. Selecione o método de autenticação.
6. Informe as credenciais necessárias.
7. Selecione **Testar conexão**.
8. Selecione **Salvar**.
> **Aviso:** não coloque API keys diretamente nos campos do workflow. Sempre que possível, armazene secrets em uma conexão.
## Como usar uma condição com múltiplos valores
Imagine que você queira encaminhar pedidos de acordo com o valor.
1. Adicione uma etapa de **Condição**.
2. Selecione o campo `order.total`.
3. Defina o operador como `greater than`.
4. Informe `1000`.
5. Adicione a ação para pedidos de alto valor no caminho **true**.
6. Adicione a ação padrão no caminho **false**.
Conceitualmente:
```text
                 ┌─ total > 1000 ─→ Processo prioritário
Novo pedido ─────┤
                 └─ caso contrário → Processo padrão
```
## Como chamar a API da Nuvexa com curl
Use um bearer token no header `Authorization`.
```bash
curl --request GET \
  --url https://api.nuvexa.example/v1/workflows \
  --header 'Authorization: Bearer nva_test_xxxxxxxxx'
```
Uma requisição bem-sucedida retorna HTTP `200 OK`.
```json
{
  "data": [
    {
      "id": "wf_8f42",
      "name": "Customer onboarding",
      "status": "published"
    }
  ]
}
```
## Como encontrar um ID de execução
1. Abra **Execuções**.
2. Selecione uma execução.
3. Selecione **Detalhes**.
4. Copie o valor exibido ao lado de **ID da execução**.
Exemplo:
```text
ID da execução: ex_01J7K3W9M5
```
Use esse identificador ao entrar em contato com o suporte ou consultar a API.

# Guia do Usuário Nuvexa
**Público:** novos usuários, builders de workflows, membros de equipes e administradores de workspace.
**Objetivo:** apresentar os principais conceitos da Nuvexa e mostrar como criar, publicar e monitorar processos automatizados.

**[Mockup do dashboard Nuvexa]**

(**INSERIR IMAGEM AQUI**../../images/dashboard-mockup.svg)

## 1. O que é a Nuvexa?
A Nuvexa é uma plataforma em nuvem para automatizar processos de negócio repetitivos.
Um processo começa com um **gatilho**, executa uma ou mais **etapas**, e termina com um **resultado**.
Por exemplo:
```text
Novo chamado de suporte
        ↓
Classificar solicitação
        ↓
Verificar plano do cliente
        ↓
Encaminhar para a equipe correta
        ↓
Notificar responsável
```
A Nuvexa pode se conectar a serviços externos por meio de conectores nativos e requisições HTTP.
> **Dica:** comece com um processo pequeno, com um gatilho e um resultado claramente definidos. Adicione ramificações e integrações depois que o fluxo básico funcionar.
## 2. Conceitos principais
### Workspace
Um workspace é o ambiente principal da organização. Ele contém usuários, workflows, conexões e histórico de execuções.
### Workflow
Um workflow é uma sequência automatizada de etapas.
Cada workflow contém:
- Um gatilho
- Uma ou mais ações
- Condições opcionais
- Integrações opcionais
- Uma versão publicada
### Gatilho
Um gatilho inicia um workflow.
| Gatilho | Exemplo |
|---|---|
| Webhook | Iniciar um processo quando outro sistema enviar um evento |
| Agendamento | Executar todos os dias úteis às 09:00 |
| Registro criado | Iniciar quando um registro for criado no CRM |
| Manual | Permitir que um usuário inicie o workflow pela Nuvexa |
### Ação
Uma ação executa uma tarefa, como enviar um e-mail, criar um registro ou chamar uma API.
### Condição
Uma condição determina qual caminho o workflow deve seguir.
```text
                    ┌─ plano = enterprise ─→ Fila prioritária
Novo chamado ───────┤
                    └─ caso contrário ─────→ Fila padrão
```
## 3. Fazer login
1. Abra a URL do seu workspace Nuvexa.
2. Informe seu e-mail corporativo.
3. Informe sua senha.
4. Selecione **Entrar**.
Se sua organização utiliza SSO, selecione **Continuar com SSO**.
> **Aviso:** nunca compartilhe sua senha ou seu API token em descrições de workflow, chamados ou screenshots.
## 4. Criar um workflow
1. Abra **Workflows**.
2. Selecione **Criar workflow**.
3. Informe um nome, como `Onboarding de clientes`.
4. Selecione um gatilho.
5. Configure os campos do gatilho.
6. Adicione uma ação.
7. Configure a ação.
8. Selecione **Salvar rascunho**.
O workflow permanece inativo até ser publicado.
![Editor de workflow](../../images/workflow-editor.svg)
## 5. Testar um workflow
Use o modo de teste antes de publicar.
1. Abra o workflow.
2. Selecione **Testar**.
3. Informe dados de exemplo.
4. Selecione **Executar teste**.
5. Revise cada etapa no painel de execução.
6. Corrija qualquer etapa com falha.
7. Execute o teste novamente.
Um teste bem-sucedido significa que o workflow foi concluído com os dados de exemplo. Isso não garante que todas as entradas de produção funcionarão.
## 6. Publicar um workflow
1. Abra o rascunho.
2. Selecione **Publicar**.
3. Revise as alterações.
4. Selecione **Publicar versão**.
A Nuvexa cria um número de versão para cada workflow publicado.
```text
Rascunho → v1 → v2 → v3
```
Versões publicadas são imutáveis. Para alterar um workflow publicado, crie uma nova versão.
## 7. Monitorar execuções
Abra **Execuções** para consultar os workflows executados.
Cada execução inclui:
- Hora de início
- Hora de término
- Status
- Payload do gatilho
- Resultado das etapas
- Informações de erro
- ID da execução
| Status | Significado |
|---|---|
| Running | O workflow está em execução |
| Succeeded | Todas as etapas obrigatórias foram concluídas |
| Failed | Pelo menos uma etapa obrigatória falhou |
| Canceled | A execução foi interrompida antes de terminar |
## 8. Gerenciar conexões
As conexões armazenam credenciais utilizadas para acessar serviços externos.
1. Abra **Configurações → Conexões**.
2. Selecione **Adicionar conexão**.
3. Escolha um conector.
4. Informe as credenciais necessárias.
5. Selecione **Testar conexão**.
6. Selecione **Salvar**.
As credenciais são referenciadas pelo ID da conexão. As definições de workflows não devem conter secrets diretamente.
> **Nota:** sua organização pode usar funções personalizadas ou restringir a publicação por meio de políticas do workspace.
## 9. Funções e permissões
A Nuvexa oferece quatro funções padrão:
| Função | Visualizar | Editar | Publicar | Gerenciar workspace |
|---|---:|---:|---:|---:|
| Viewer | Sim | Não | Não | Não |
| Builder | Sim | Sim | Não | Não |
| Publisher | Sim | Sim | Sim | Não |
| Admin | Sim | Sim | Sim | Sim |
## 10. Boas práticas para criar workflows
Mantenha os workflows fáceis de entender.
### Prefira
```text
Gatilho
  ↓
Validar entrada
  ↓
Decisão de negócio
  ↓
Ação
  ↓
Registrar resultado
```
### Evite
```text
Gatilho
 ↓
Ação
 ↓
Condição
 ↓
Ação
 ↓
Condição
 ↓
Condição
 ↓
Ação
 ↓
Ação
```
Se um workflow ficar difícil de ler, divida-o em workflows menores e reutilizáveis.
## 11. Próximos passos
- [Criar seu primeiro processo automatizado](../how-to/index.md)
- [Solucionar uma execução com falha](../troubleshooting/index.md)
- [Consultar perguntas frequentes](../faq/index.md)
- [Integrar a Nuvexa à sua aplicação](../api/index.md)

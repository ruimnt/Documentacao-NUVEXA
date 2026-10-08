# FAQ da Nuvexa
**Público:** todos os usuários Nuvexa, administradores de workspace e usuários técnicos em potencial.

**Objetivo:** responder dúvidas comuns sem exigir que o leitor navegue por toda a documentação.

## O que é a Nuvexa?
A Nuvexa é uma plataforma SaaS para criar, automatizar e monitorar processos de negócio.
## Preciso saber programar?
Não. Você pode criar workflows padrão usando o editor visual.
Desenvolvedores podem usar a API e ações HTTP para integrações mais avançadas.
## Qual é a diferença entre um rascunho e um workflow publicado?
Um **rascunho** pode ser editado e testado sem afetar o workflow ativo.
Uma **versão publicada** está ativa e é imutável. Alterações exigem uma nova versão.
## Posso conectar a Nuvexa a outra aplicação?
Sim. Você pode usar conectores nativos ou configurar requisições HTTP para APIs.
## Onde as credenciais são armazenadas?
As credenciais são armazenadas em Conexões da Nuvexa e referenciadas pelos workflows.
> **Aviso:** não inclua secrets diretamente em inputs, descrições de workflows ou código-fonte.
## Posso testar um workflow sem publicá-lo?
Sim. Use **Testar** enquanto estiver editando um rascunho.
Os testes usam os dados de exemplo informados e não ativam o workflow.
## O que acontece quando um workflow falha?
A Nuvexa marca a execução como `Failed` e registra a etapa com falha e os detalhes do erro.
Dependendo do erro, você pode corrigir o problema e repetir a execução.
## Posso repetir apenas a etapa que falhou?
Sim, quando a etapa suporta retry. Selecione **Repetir a partir da etapa com falha** nos detalhes da execução.
## Por quanto tempo a Nuvexa mantém o histórico de execuções?
O período padrão fictício é de **30 dias**. Administradores podem solicitar uma política de retenção diferente.
## A Nuvexa oferece suporte a webhooks?
Sim. Um gatilho webhook pode iniciar um workflow quando outra aplicação envia uma requisição HTTP.
## Qual autenticação a API utiliza?
A API utiliza bearer tokens.
```http
Authorization: Bearer nva_live_xxxxxxxxx
```
## O que fazer se eu receber `401 Unauthorized`?
Verifique se:
1. O token é válido.
2. O token não expirou nem foi revogado.
3. O header `Authorization` usa o esquema `Bearer`.
4. Você está chamando o ambiente correto.
Consulte [Troubleshooting](../troubleshooting/index.md).
## Posso criar workflows por meio da API?
Sim. A API inclui endpoints para workflows, execuções e conexões. Consulte a [Documentação de API](../api/index.md).
## Posso excluir um workflow publicado?
Versões publicadas não podem ser editadas. Administradores podem arquivar o workflow de acordo com a política de retenção do workspace.
## A Nuvexa é multi-tenant?
Sim. Cada organização opera em um workspace isolado. Os recursos da API são associados ao workspace autenticado.
## Onde encontro meu API token?
Abra **Configurações → Desenvolvedor → API tokens**.
> **Aviso:** trate API tokens como senhas. Armazene-os em um secret manager ou em uma variável de ambiente.

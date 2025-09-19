# Secrets Usage Map – RabbitMQ Infrastructure

## Repository Variables (`vars`)
- `AZURE_CLIENT_ID` – Identificador da aplicação federada usado por `azure/login@v2`.
- `AZURE_TENANT_ID` – Diretório Azure AD para autenticação OIDC.
- `AZURE_SUBSCRIPTION_ID` – Assinatura com permissão de leitura no Key Vault.
- `AZURE_KEYVAULT_NAME` – Key Vault que hospeda as credenciais do RabbitMQ.
- `AZURE_KEYVAULT_ENDPOINT` *(opcional)* – Endpoint HTTPS completo, caso ferramentas externas precisem dele.
- `MAX_VERSIONS_TO_KEEP` *(opcional)* – Quantidade de versões GHCR preservadas em limpezas futuras.
- `MAX_AGE_DAYS` *(opcional)* – Idade mínima (dias) para elegibilidade de exclusão de imagens.
- `PROTECTED_TAGS` *(opcional)* – Tags que nunca devem ser excluídas (`latest,main,production` por padrão).

> Todos os identificadores residem como **Repository Variables**. Nenhum segredo sensível permanece no GitHub.

## GitHub Secrets
- `GITHUB_TOKEN` – Token padrão do GitHub Actions com escopo mínimo (`contents`/`packages`) usado para artefatos e GHCR.

## Azure Key Vault
- `conexao-de-sorte-rabbitmq-username` – Usuário padrão do broker RabbitMQ.
- `conexao-de-sorte-rabbitmq-password` – Senha do usuário padrão.

## Jobs × Secret Usage
| Job | Propósito | Segredos/Variáveis | Observações |
| --- | --- | --- | --- |
| `validate-and-build` | Linters e upload de manifestos | `AZURE_*` (Repository Variables), `GITHUB_TOKEN` | Apenas validações; não consulta o Key Vault. |
| `deploy-selfhosted` | Deploy Swarm e health checks | `AZURE_*`, `conexao-de-sorte-rabbitmq-username`, `conexao-de-sorte-rabbitmq-password`, `GITHUB_TOKEN` | Busca seletiva via `azure/get-keyvault-secrets@v1`; cria secrets Swarm `RABBITMQ_USERNAME`/`RABBITMQ_PASSWORD`. |

## Notas Operacionais
- Atualize este arquivo antes de acrescentar novos segredos ao pipeline.
- Sempre utilize `::add-mask::` antes de manipular valores do Key Vault em logs ou outputs.
- A limpeza inteligente do GHCR herda parâmetros globais (`MAX_*`, `PROTECTED_TAGS`); ajuste-os via Repository Variables se necessário.

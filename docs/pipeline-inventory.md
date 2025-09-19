# 📋 Pipeline Inventory — RabbitMQ Infrastructure

## Visão Geral
- **Workflow**: `.github/workflows/ci-cd.yml`
- **Serviço**: `rabbitmq-infrastructure`
- **Triggers**: push/PR em `main` e `workflow_dispatch` manual.
- **Permissões globais**: `contents: read`, `id-token: write`.
- **Runner**: `[self-hosted, Linux, X64, srv649924, conexao-de-sorte-rabbitmq-infraestrutura]` para todos os jobs.

## Jobs
### `validate-and-build`
- Valida presença das Repository Variables `AZURE_*`.
- Executa `docker compose config` e busca hardcoded secrets.
- Publica artefato `rabbitmq-configs` (retenção 1 dia).

### `deploy-selfhosted`
- Autentica com `azure/login@v2` usando `vars.AZURE_*`.
- Busca `conexao-de-sorte-rabbitmq-username/password` via `azure/get-keyvault-secrets@v1`.
- Converte os segredos em arquivos temporários, cria secrets Swarm, ajusta permissões do volume e garante rede.
- Executa deploy Swarm, health checks (diagnostics + logs + portas) e valida conectividade.

## Segredos & Variáveis
- **Repository Variables**: `AZURE_CLIENT_ID`, `AZURE_TENANT_ID`, `AZURE_SUBSCRIPTION_ID`, `AZURE_KEYVAULT_NAME`, `AZURE_KEYVAULT_ENDPOINT` (opcional), `MAX_VERSIONS_TO_KEEP`, `MAX_AGE_DAYS`, `PROTECTED_TAGS`.
- **GitHub Secrets**: `GITHUB_TOKEN`.
- **Azure Key Vault**: `conexao-de-sorte-rabbitmq-username`, `conexao-de-sorte-rabbitmq-password`.

## Artefatos & Cache
- Artefato único `rabbitmq-configs-${run_id}` (docker-compose).
- Cache Buildx não é utilizado atualmente (imagem oficial do RabbitMQ é consumida diretamente).

## Auditoria
- Checklists em `docs/pipeline-checklist.md`.
- Mapa de segredos em `docs/secrets-usage-map.md`.
- Validações registradas em `docs/validation-report.md`.

> Atualize este inventário sempre que novos jobs, segredos ou parâmetros forem adicionados ao pipeline.

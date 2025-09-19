# 🐰 Conexão de Sorte — RabbitMQ Infrastructure

Stack RabbitMQ executada via Docker Swarm no host `srv649924` (Hostinger). O pipeline GitHub Actions utiliza OIDC + Azure Key Vault com consumo mínimo de segredos.

## 📦 Componentes
- `docker-compose.yml`: serviço `rabbitmq` rootless (`user: 999:999`), `init`, `stop_grace_period`, logging rotacionado e estratégias de `update_config` / `rollback_config`.
- `.github/workflows/ci-cd.yml`: workflow com jobs para validação (dry-run) e deploy em runner self-hosted.
- `.github/actionlint.yaml`: configuração para validar os labels customizados do runner.
- `docs/`: checklist, inventário e mapa de segredos.

## 🔐 Segredos & Variáveis
- **Repository Variables**: `AZURE_CLIENT_ID`, `AZURE_TENANT_ID`, `AZURE_SUBSCRIPTION_ID`, `AZURE_KEYVAULT_NAME`, `AZURE_KEYVAULT_ENDPOINT` (opcional) + parâmetros opcionais do GHCR (`MAX_VERSIONS_TO_KEEP`, `MAX_AGE_DAYS`, `PROTECTED_TAGS`).
- **Azure Key Vault**: `conexao-de-sorte-rabbitmq-username`, `conexao-de-sorte-rabbitmq-password` (únicos consumidos).
- **GitHub Secrets**: apenas `GITHUB_TOKEN` padrão.

Detalhes completos em `docs/secrets-usage-map.md`.

## 🧰 Pipeline CI/CD
- `validate-and-build`: roda em `[self-hosted, Linux, X64, srv649924, conexao-de-sorte-rabbitmq-infraestrutura]`, valida vars Azure, executa `docker compose config`, `grep` de segredos e publica artefatos.
- `deploy-selfhosted`: autentica no Azure via OIDC, busca segredos com `azure/get-keyvault-secrets@v1`, cria secrets Swarm, garante volume/rede com permissões rootless e realiza deploy + health checks.

Permissões globais: `contents: read`, `id-token: write`; jobs elevam apenas o necessário.

## 🚀 Como Deployar
```bash
# CI/CD (branch main)
git push origin main

# Deploy manual
export RABBITMQ_USERNAME_FILE=/path/user
export RABBITMQ_PASSWORD_FILE=/path/pass
docker stack deploy -c docker-compose.yml conexao-rabbitmq
```

## 🔎 Validações
- `actionlint -config-file .github/actionlint.yaml --shellcheck=`
- `docker compose -f docker-compose.yml config -q`
- `hadolint`/`docker build` ainda pendentes (falta ferramenta/daemon; registrar assim que disponíveis).

## 🆘 Troubleshooting
```bash
# Status dos serviços
docker service ls --filter name=conexao-rabbitmq

# Logs do container
docker service logs conexao-rabbitmq_rabbitmq --tail 50

# Secrets disponíveis
docker secret ls | grep RABBITMQ
```

Consulte `docs/pipeline-checklist.md` para checklist completo de conformidade antes de qualquer deploy.

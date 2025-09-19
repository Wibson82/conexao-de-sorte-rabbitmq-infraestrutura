# ✅ Pipeline Validation Report

## Static Checks
- `actionlint -config-file .github/actionlint.yaml --shellcheck=` → **Success**.
- `docker compose -f docker-compose.yml config -q` → **Success**.
- `hadolint` → **Pendente** (binário indisponível neste ambiente self-hosted).
- `docker build` → **Pendente** (daemon Docker bloqueado neste host).

## Functional Preview
- Workflow mantém `deploy-selfhosted` restrito a `main`. Para staging, usar `workflow_dispatch` após merge.
- `azure/get-keyvault-secrets@v1` busca apenas username/password e converte para secrets Swarm rootless.
- Volume `rabbitmq_data` tem permissões ajustadas automaticamente (UID 999) antes do deploy.

## Recomendações
1. Executar `hadolint` e `docker build` no runner Hostinger para registrar evidências completas.
2. Disparar workflow em staging após merge para validar conectividade e health checks reais.
3. RegistrAR logs do run em produção/staging anexando-os a este documento quando disponíveis.

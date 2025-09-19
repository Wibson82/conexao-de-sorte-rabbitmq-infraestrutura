## ✅ Checklist de Conformidade – Pipeline RabbitMQ Infrastructure

- [x] Permissões globais mínimas (`contents: read`, `id-token: write`) e elevação somente nos jobs que precisam (`packages: write`).
- [x] Jobs executam em `[self-hosted, Linux, X64, srv649924, conexao-de-sorte-rabbitmq-infraestrutura]`.
- [x] `azure/login@v2` usa `${{ vars.AZURE_* }}` e `azure/get-keyvault-secrets@v1` busca apenas `conexao-de-sorte-rabbitmq-username/password`.
- [x] Nenhum segredo de aplicação armazenado no GitHub; inventário atualizado em `docs/secrets-usage-map.md`.
- [ ] `actionlint`, `docker compose config`, `hadolint` e `docker build` executados (ou justificativa documentada em `docs/validation-report.md`).
- [x] Deploy Swarm cria secrets/volumes de forma idempotente, ajusta permissões para execução rootless e configura `update_config`/`rollback_config`.
- [x] Health checks multicanal e conectividade registrados; logs sem vazamento de segredos.
- [ ] Limpeza do GHCR (quando configurada) opera com lista explícita e relatório no `GITHUB_STEP_SUMMARY`.
- [x] Documentação (`README`, `docs/`) atualizada com requisitos do runner, segredos mínimos e procedimentos de validação.
- [ ] Execução em staging/produção registrada com evidências (workflow run) e sem falhas.

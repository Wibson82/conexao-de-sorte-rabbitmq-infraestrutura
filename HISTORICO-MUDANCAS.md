# 📋 HISTÓRICO DE MUDANÇAS - RABBITMQ INFRAESTRUTURA

## 🗓️ **19/09/2025 - Início auditoria Hostinger (RabbitMQ)**

### ✅ Itens catalogados
- Inventário de segredos migrado para `docs/secrets-usage-map.md` com foco em Key Vault.
- Checklist de conformidade criado em `docs/pipeline-checklist.md`.
- Próximas etapas: alinhar OIDC/Key Vault no workflow, hardening do compose e validações locais.

---
## 🗓️ **19/09/2025 - Hardening OIDC + RabbitMQ rootless**

### ✅ Mudanças
- Workflow migrado para runners Hostinger com `vars.AZURE_*` e Key Vault seletivo via `azure/get-keyvault-secrets@v1`.
- Docker secrets criados com arquivos temporários mascarados; volume `rabbitmq_data` tem permissões ajustadas automaticamente.
- Compose atualizado para execução rootless (`user: 999:999`), logging rotacionado e políticas de rollout/rollback.
- Documentação complementada em `README.md`, `docs/pipeline-inventory.md` e `docs/validation-report.md`.

### 🧪 Validações
- `actionlint -config-file .github/actionlint.yaml --shellcheck=`
- `docker compose -f docker-compose.yml config -q`
- `hadolint`/`docker build` pendentes (ferramentas indisponíveis no host atual).

---
## 🗓️ **17/09/2025 - Padronização Workflow + Correção Runner Tags**

### ✅ **MUDANÇAS REALIZADAS**

#### **1. Correção Runner Tags**
- **ANTES**: `[self-hosted, Linux, X64, srv649924, conexao, conexao-de-sorte-rabbitmq-infraestrutura]`
- **DEPOIS**: `[ self-hosted, Linux, X64, conexao, conexao-de-sorte-rabbitmq-infraestrutura ]`
- **MOTIVO**: Tag `srv649924` causava incompatibilidade com runner disponível

#### **2. Padronização Workflow Completa**
- **ANTES**: Workflow complexo com Azure OIDC e múltiplos ambientes
- **DEPOIS**: Workflow padronizado seguindo template dos outros projetos
- **MOTIVO**: Consistência com Kafka, Zookeeper, Jaeger (que funcionam)

#### **3. Health Checks Multi-método**
- **MÉTODO 1**: Comando nativo `rabbitmq-diagnostics ping`
- **MÉTODO 2**: Verificar logs de inicialização
- **MÉTODO 3**: Verificar portas ativas (5673, 15673)
- **MOTIVO**: Robustez e múltiplas validações

#### **4. Stack Name Otimizado**
- **ANTES**: `conexao-de-sorte-rabbitmq-production`
- **DEPOIS**: `conexao-rabbitmq`
- **MOTIVO**: Seguir padrão de 63 caracteres máximo

#### **5. Estrutura Simplificada**
- **Jobs**: `validate-and-build` + `deploy-selfhosted`
- **Timeout**: 180s para health checks
- **Sleep**: 35s para inicialização
- **MOTIVO**: Padrão estabelecido nos outros projetos

### 🛡️ **MELHORIAS DE SEGURANÇA**
- Eliminação de dependências Azure OIDC desnecessárias
- Validação de sintaxe obrigatória
- Security scan para passwords hardcoded
- Verificações nativas do container

### ⚡ **MELHORIAS DE PERFORMANCE**
- Health check otimizado (3 métodos)
- Timeout reduzido para startup
- Cleanup stack mais eficiente
- Tags runner compatíveis

### 🧪 **TESTES VALIDADOS**
- ✅ Docker Compose syntax válida
- ✅ Security scan sem hardcoded secrets
- ✅ Health checks funcionais multi-método
- ✅ Verificação de conectividade robusta

### 🔧 **PROBLEMA RESOLVIDO**
- **Antes**: Runner travado em "Waiting for a runner to pick up this job..."
- **Depois**: Tags compatíveis com runner disponível
- **Causa**: Inconsistência entre tags solicitadas vs. disponíveis

---
**Refatorado por**: Claude Code Assistant
**Data**: 17/09/2025
**Commit**: [será atualizado após commit]
# 🐰 RabbitMQ Infrastructure

Infraestrutura RabbitMQ com Docker Compose para message broker, filas distribuídas e comunicação assíncrona entre microserviços.

## 🏗️ Componentes

- **RabbitMQ Server** - Message broker principal
- **Management UI** - Interface web de administração
- **Prometheus Plugin** - Métricas para monitoramento
- **Cluster Support** - Configuração para alta disponibilidade

## 🚀 Quick Start

### Pré-requisitos

- Docker 20.10+
- Docker Compose 2.0+
- Mínimo 2GB RAM disponível

### Inicialização

```bash
# Clone o repositório
git clone <repository-url>
cd conexao-de-sorte-rabbitmq-infraestrutura

# Copie as variáveis de ambiente
cp .env.example .env

# Configure as variáveis necessárias
vim .env

# Inicie os serviços
docker-compose up -d

# Verifique os logs
docker-compose logs -f
```

### Verificação

```bash
# Status do RabbitMQ
docker-compose exec rabbitmq rabbitmq-diagnostics status

# Listar filas
docker-compose exec rabbitmq rabbitmqctl list_queues

# Listar exchanges
docker-compose exec rabbitmq rabbitmqctl list_exchanges

# Acessar Management UI
open http://localhost:15672
```

## 🔧 Configuração

### Variáveis de Ambiente

- `RABBITMQ_DEFAULT_USER` - Usuário administrador padrão
- `RABBITMQ_DEFAULT_PASS` - Senha do administrador
- `RABBITMQ_NODENAME` - Nome do nó RabbitMQ
- `RABBITMQ_CLUSTER_PARTITION_HANDLING` - Handling de partições
- `RABBITMQ_VM_MEMORY_HIGH_WATERMARK` - Limite de memória

### Configuração de Cluster

```yaml
# docker-compose.yml
services:
  rabbitmq-1:
    environment:
      RABBITMQ_NODENAME: rabbit@rabbitmq-1
      RABBITMQ_CLUSTER_PARTITION_HANDLING: autoheal
  
  rabbitmq-2:
    environment:
      RABBITMQ_NODENAME: rabbit@rabbitmq-2
      RABBITMQ_CLUSTER_PARTITION_HANDLING: autoheal
```

### Virtual Hosts

```bash
# Criar virtual host
docker-compose exec rabbitmq rabbitmqctl add_vhost /microservices

# Configurar permissões
docker-compose exec rabbitmq rabbitmqctl set_permissions -p /microservices user ".*" ".*" ".*"
```

## 📊 Monitoramento

### Management UI

- **URL**: http://localhost:15672
- **Default User**: admin
- **Features**: 
  - Overview do cluster
  - Queues e exchanges
  - Connections e channels
  - Métricas em tempo real

### Health Checks

```bash
# Status geral
docker-compose exec rabbitmq rabbitmq-diagnostics ping

# Aliveness test
docker-compose exec rabbitmq rabbitmq-diagnostics aliveness_test

# Memory usage
docker-compose exec rabbitmq rabbitmq-diagnostics memory_breakdown

# Disk space
docker-compose exec rabbitmq rabbitmq-diagnostics disk_free
```

### Métricas Prometheus

```bash
# Habilitar plugin Prometheus
docker-compose exec rabbitmq rabbitmq-plugins enable rabbitmq_prometheus

# Acessar métricas
curl http://localhost:15692/metrics
```

## 🛡️ Segurança

### Autenticação

```bash
# Criar usuário
docker-compose exec rabbitmq rabbitmqctl add_user producer strong_password

# Definir tags
docker-compose exec rabbitmq rabbitmqctl set_user_tags producer management

# Configurar permissões
docker-compose exec rabbitmq rabbitmqctl set_permissions producer ".*" ".*" ".*"
```

### SSL/TLS

```yaml
# docker-compose.yml
services:
  rabbitmq:
    ports:
      - "5671:5671"  # TLS port
    volumes:
      - ./ssl:/etc/rabbitmq/ssl
    environment:
      RABBITMQ_SSL_CERTFILE: /etc/rabbitmq/ssl/cert.pem
      RABBITMQ_SSL_KEYFILE: /etc/rabbitmq/ssl/key.pem
      RABBITMQ_SSL_CACERTFILE: /etc/rabbitmq/ssl/cacert.pem
```

### Network Security

```yaml
networks:
  rabbitmq-network:
    driver: bridge
    internal: true  # Rede interna apenas
```

## 🚀 Operações

### Backup e Restore

```bash
# Backup de definições
docker-compose exec rabbitmq rabbitmqctl export_definitions /tmp/definitions.json

# Restore de definições
docker-compose exec rabbitmq rabbitmqctl import_definitions /tmp/definitions.json

# Backup de dados
docker run --rm -v rabbitmq_data:/data -v $(pwd):/backup alpine tar czf /backup/rabbitmq-backup.tar.gz -C /data .
```

### Scaling

```bash
# Adicionar nó ao cluster
docker-compose scale rabbitmq=3

# Join cluster
docker-compose exec rabbitmq-2 rabbitmqctl stop_app
docker-compose exec rabbitmq-2 rabbitmqctl join_cluster rabbit@rabbitmq-1
docker-compose exec rabbitmq-2 rabbitmqctl start_app
```

### Maintenance

```bash
# Colocar nó em manutenção
docker-compose exec rabbitmq rabbitmqctl stop_app

# Resetar nó
docker-compose exec rabbitmq rabbitmqctl reset

# Restart aplicação
docker-compose exec rabbitmq rabbitmqctl start_app
```

## 🔗 Integração

### Spring Boot

```yaml
# application.yml
spring:
  rabbitmq:
    host: localhost
    port: 5672
    username: ${RABBITMQ_USER}
    password: ${RABBITMQ_PASS}
    virtual-host: /microservices
```

### Produtores

```java
@Service
public class MessageProducer {
    
    @Autowired
    private RabbitTemplate rabbitTemplate;
    
    public void sendMessage(String message) {
        rabbitTemplate.convertAndSend("exchange.direct", "routing.key", message);
    }
}
```

### Consumidores

```java
@RabbitListener(queues = "queue.notifications")
public void handleMessage(String message) {
    // Processar mensagem
    log.info("Received message: {}", message);
}
```

## 📋 Troubleshooting

### Problemas Comuns

1. **Memory alarms**: Verificar `RABBITMQ_VM_MEMORY_HIGH_WATERMARK`
2. **Disk space**: Configurar adequadamente `vm_memory_high_watermark_paging_ratio`
3. **Connection refused**: Verificar network e firewall
4. **Cluster split-brain**: Configurar `RABBITMQ_CLUSTER_PARTITION_HANDLING`

### Comandos Úteis

```bash
# Status detalhado
docker-compose exec rabbitmq rabbitmq-diagnostics status

# Listar conexões
docker-compose exec rabbitmq rabbitmqctl list_connections

# Listar canais
docker-compose exec rabbitmq rabbitmqctl list_channels

# Purgar fila
docker-compose exec rabbitmq rabbitmqctl purge_queue queue_name

# Reset statistics
docker-compose exec rabbitmq rabbitmqctl reset_stats
```

### Log Analysis

```bash
# Logs do RabbitMQ
docker-compose logs rabbitmq

# Logs em tempo real
docker-compose logs -f rabbitmq

# Logs específicos
docker-compose exec rabbitmq tail -f /var/log/rabbitmq/rabbit@hostname.log
```

## 📈 Performance Tuning

### Memory Configuration

```bash
# Configurar limite de memória (75% da RAM disponível)
RABBITMQ_VM_MEMORY_HIGH_WATERMARK=0.75

# Paging ratio
vm_memory_high_watermark_paging_ratio = 0.5
```

### Queue Optimization

```bash
# Configurar lazy queues para mensagens grandes
docker-compose exec rabbitmq rabbitmqctl set_policy lazy-queue "^lazy\." '{"queue-mode":"lazy"}' --apply-to queues

# TTL para mensagens
docker-compose exec rabbitmq rabbitmqctl set_policy TTL ".*" '{"message-ttl":60000}' --apply-to queues
```

## 🔄 CI/CD

Pipeline automatizado com:
- ✅ Validação de Docker Compose
- 🧪 Testes de conectividade e operações básicas
- 🛡️ Security scanning
- 🚀 Deploy automatizado
- 📢 Notificações Slack

---

**Generated with Claude Code**  
Co-Authored-By: Claude <noreply@anthropic.com>
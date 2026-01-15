# Docker 部署指南

本文档提供了使用 Docker 和 Docker Compose 部署 Qwen Free API 服务的详细说明。

## 前置要求

- Docker 20.10+
- Docker Compose 2.0+
- 具有公网 IP 的服务器（如需外网访问）
- 开放 8000 端口

## 部署方式

项目提供了两种 Docker Compose 配置文件：

### 1. docker-compose.yml（开发/本地构建）

使用本地 Dockerfile 构建镜像，适合开发环境或需要自定义修改的场景。

**启动服务：**
```bash
docker-compose up -d
```

**查看日志：**
```bash
docker-compose logs -f
```

**停止服务：**
```bash
docker-compose down
```

**重启服务：**
```bash
docker-compose restart
```

### 2. docker-compose.prod.yml（生产环境）

使用预构建的镜像，适合生产环境快速部署。

**启动服务：**
```bash
docker-compose -f docker-compose.prod.yml up -d
```

**查看日志：**
```bash
docker-compose -f docker-compose.prod.yml logs -f
```

**停止服务：**
```bash
docker-compose -f docker-compose.prod.yml down
```

## 配置说明

### 端口配置

默认端口为 `8000`，如需修改，编辑 docker-compose.yml：

```yaml
ports:
  - "自定义端口:8000"
```

### 环境变量

- `TZ`: 时区设置，默认 `Asia/Shanghai`
- `NODE_ENV`: Node.js 环境，默认 `production`

### 配置文件挂载

配置文件位于 `./configs` 目录，已通过卷挂载到容器：

```yaml
volumes:
  - ./configs:/app/configs:ro
```

如需修改服务配置，编辑 `configs/dev/service.yml`：

```yaml
# 服务名称
name: qwen-free-api-new
# 服务绑定主机地址
host: '0.0.0.0'
# 服务绑定端口
port: 8000
```

### 资源限制

默认资源限制：
- CPU 限制：2 核
- 内存限制：2GB
- CPU 预留：0.5 核
- 内存预留：512MB

根据实际需求调整：

```yaml
deploy:
  resources:
    limits:
      cpus: '4'
      memory: 4G
    reservations:
      cpus: '1'
      memory: 1G
```

### 健康检查

服务包含健康检查配置，自动检测服务状态：

```yaml
healthcheck:
  test: ["CMD", "wget", "--quiet", "--tries=1", "--spider", "http://localhost:8000/ping"]
  interval: 30s
  timeout: 10s
  retries: 3
  start_period: 40s
```

查看健康状态：
```bash
docker ps
```

## 常用命令

### 查看服务状态
```bash
docker-compose ps
```

### 查看实时日志
```bash
docker-compose logs -f qwen-free-api
```

### 进入容器
```bash
docker-compose exec qwen-free-api sh
```

### 更新服务
```bash
# 拉取最新镜像
docker-compose pull

# 重新创建容器
docker-compose up -d
```

### 清理资源
```bash
# 停止并删除容器
docker-compose down

# 同时删除卷
docker-compose down -v

# 清理未使用的镜像
docker image prune -a
```

## 验证部署

### 1. 检查服务状态
```bash
curl http://localhost:8000/ping
```

### 2. 访问欢迎页面
在浏览器中打开：`http://服务器IP:8000`

### 3. 测试 API 接口
```bash
curl -X POST http://localhost:8000/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -d '{
    "model": "qwen",
    "messages": [{"role": "user", "content": "你好"}],
    "stream": false
  }'
```

## 故障排查

### 容器无法启动

1. 查看日志：
```bash
docker-compose logs qwen-free-api
```

2. 检查端口占用：
```bash
netstat -tulpn | grep 8000
```

3. 检查 Docker 资源：
```bash
docker system df
```

### 服务无响应

1. 检查容器状态：
```bash
docker-compose ps
```

2. 重启服务：
```bash
docker-compose restart
```

3. 查看健康检查状态：
```bash
docker inspect qwen-free-api-new | grep -A 10 Health
```

### 内存不足

调整资源限制或增加服务器内存：
```yaml
deploy:
  resources:
    limits:
      memory: 4G
```

## 生产环境建议

1. **使用反向代理**：配置 Nginx 或 Traefik 作为反向代理
2. **启用 HTTPS**：使用 Let's Encrypt 证书
3. **日志管理**：配置日志轮转和集中日志收集
4. **监控告警**：集成 Prometheus + Grafana 监控
5. **备份策略**：定期备份配置文件和数据
6. **安全加固**：
   - 使用防火墙限制访问
   - 定期更新镜像
   - 使用非 root 用户运行容器

## 多实例部署

如需部署多个实例实现负载均衡：

```yaml
version: '3.8'

services:
  qwen-free-api-1:
    container_name: qwen-free-api-1
    image: akashrajpuroh1t/qwen-free-api-fix:latest
    restart: always
    ports:
      - "8001:8000"
    # ... 其他配置

  qwen-free-api-2:
    container_name: qwen-free-api-2
    image: akashrajpuroh1t/qwen-free-api-fix:latest
    restart: always
    ports:
      - "8002:8000"
    # ... 其他配置
```

配合 Nginx 负载均衡：
```nginx
upstream qwen_backend {
    server localhost:8001;
    server localhost:8002;
}

server {
    listen 80;
    location / {
        proxy_pass http://qwen_backend;
    }
}
```

## 更新日志

- 2026-01-15: 创建 Docker 部署文档
- 添加开发和生产环境配置
- 添加健康检查和资源限制
- 添加故障排查指南

## 相关链接

- [项目主页](https://github.com/gongxings/qwen-free-qpi-new)
- [Docker Hub](https://hub.docker.com/r/akashrajpuroh1t/qwen-free

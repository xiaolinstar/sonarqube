## 使用 Docker Compose 启动 SonarQube 服务

## 环境变量

项目路径下环境变量示例 `.env.example`，包含 PostgreSQL 和 SonarQube 服务的启动参数设置。

复制并创建环境变量文件 `.env`，Docker Compose 在启动容器服务时会加载此文件中的环境变量。

## 快速启动

**启动服务**

```bash
docker compose up -d
```

**停止服务**

停止服务但保留数据：

```bash
docker compose down
```

**查看日志**

查看所有服务日志：

```bash
docker compose logs -f
```

查看特定服务日志：

```bash
docker compose logs -f sonarqube
docker compose logs -f postgres
```

**查看服务状态**

```bash
docker compose ps
```

**删除磁盘卷（数据）**

> 警告：此操作将永久删除所有数据

删除特定服务的卷：

```bash
docker volume rm sonarqube_postgres_data sonarqube_sonarqube_data sonarqube_sonarqube_logs sonarqube_sonarqube_extensions
```

或者在停止服务时同时删除卷：

```bash
docker compose down -v
```

## 数据管理

### 备份数据

备份PostgreSQL数据：

```bash
docker run --rm -v sonarqube_postgres_data:/source -v $(pwd):/backup alpine tar czf /backup/postgres_backup_$(date +%Y%m%d).tar.gz -C /source .
```

备份SonarQube数据：

```bash
docker run --rm -v sonarqube_sonarqube_data:/source -v $(pwd):/backup alpine tar czf /backup/sonarqube_data_$(date +%Y%m%d).tar.gz -C /source .
docker run --rm -v sonarqube_sonarqube_extensions:/source -v $(pwd):/backup alpine tar czf /backup/sonarqube_extensions_$(date +%Y%m%d).tar.gz -C /source .
```

### 恢复数据

恢复PostgreSQL数据：

```bash
docker run --rm -v sonarqube_postgres_data:/target -v $(pwd):/backup alpine tar xzf /backup/postgres_backup_YYYYMMDD.tar.gz -C /target
```

恢复SonarQube数据：

```bash
docker run --rm -v sonarqube_sonarqube_data:/target -v $(pwd):/backup alpine tar xzf /backup/sonarqube_data_YYYYMMDD.tar.gz -C /target
docker run --rm -v sonarqube_sonarqube_extensions:/target -v $(pwd):/backup alpine tar xzf /backup/sonarqube_extensions_YYYYMMDD.tar.gz -C /target
```

## 升级指南

### 升级SonarQube版本

1. 停止服务并备份数据（见上文）
2. 修改`docker-compose.yaml`中的SonarQube镜像版本：
   ```yaml
   image: sonarqube:community-9.9.0  # 修改为新版本
   ```
3. 重新启动服务：
   ```bash
   docker compose up -d
   ```

### 升级PostgreSQL版本

1. 停止服务并备份数据（见上文）
2. 修改`docker-compose.yaml`中的PostgreSQL镜像版本：
   ```yaml
   image: postgres:14  # 修改为新版本
   ```
3. 重新启动服务：
   ```bash
   docker compose up -d
   ```

## 安全建议

1. 生产环境使用强密码
2. 不要将`.env`文件提交到版本控制
3. 配置TLS/SSL加密访问
4. 限制网络访问（仅允许必要的IP访问）
5. 定期备份数据
6. 定期更新镜像版本

## 许可证

SonarQube Community Edition 使用 GNU LGPL v3 许可证。


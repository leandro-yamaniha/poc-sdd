# Segurança e Performance em Imagens Docker para SDD

## Introdução

**Docker containers** são fundamentais em Spec-Driven Development para garantir ambientes consistentes entre desenvolvimento, testes e produção. Este documento cobre **segurança**, **performance** e **otimização de tamanho** de imagens Docker, com foco em aplicações geradas a partir de specs.

Este documento cobre:
- Segurança em imagens Docker
- Otimização de tamanho de imagens
- Performance de containers
- Multi-stage builds
- Scanning de vulnerabilidades
- Boas práticas para SDD

---

## Por Que Segurança e Performance são Críticas para SDD?

### Problema: Imagens Inseguras e Pesadas

```dockerfile
# ❌ Dockerfile RUIM
FROM node:latest

# Root user (inseguro)
WORKDIR /app

# Copia tudo (incluindo secrets)
COPY . .

# Instala tudo (dev + prod)
RUN npm install

# Expõe tudo
EXPOSE 3000

CMD ["node", "server.js"]
```

**Problemas**:
- ❌ Imagem base `latest` (instável)
- ❌ Roda como root (vulnerabilidade)
- ❌ Copia arquivos desnecessários
- ❌ Inclui dependências de dev
- ❌ Tamanho: ~1.2GB
- ❌ Vulnerabilidades: 50+

### Solução: Imagem Segura e Otimizada

```dockerfile
# ✅ Dockerfile BOM
FROM node:18-alpine AS builder

WORKDIR /app

# Copia apenas package files
COPY package*.json ./

# Instala apenas prod dependencies
RUN npm ci --only=production

# Copia código
COPY src/ ./src/
COPY specs/ ./specs/

# Build
RUN npm run build

# Stage final
FROM node:18-alpine

# Non-root user
RUN addgroup -g 1001 -S nodejs && \
    adduser -S nodejs -u 1001

WORKDIR /app

# Copia apenas necessário
COPY --from=builder --chown=nodejs:nodejs /app/dist ./dist
COPY --from=builder --chown=nodejs:nodejs /app/node_modules ./node_modules
COPY --from=builder --chown=nodejs:nodejs /app/package.json ./

USER nodejs

EXPOSE 3000

HEALTHCHECK --interval=30s --timeout=3s \
  CMD node healthcheck.js || exit 1

CMD ["node", "dist/server.js"]
```

**Benefícios**:
- ✅ Imagem base Alpine (pequena)
- ✅ Multi-stage build
- ✅ Non-root user
- ✅ Apenas dependências de produção
- ✅ Tamanho: ~150MB (8x menor)
- ✅ Vulnerabilidades: 0-2

---

## 1. Segurança em Imagens Docker

### 1.1. Escolher Imagens Base Seguras

#### ❌ Evitar

```dockerfile
FROM node:latest          # Instável, pode quebrar
FROM ubuntu              # Muito grande, muitas vulnerabilidades
FROM node:16             # Versão antiga, sem patches
```

#### ✅ Usar

```dockerfile
# Alpine: menor e mais segura
FROM node:18-alpine

# Distroless: apenas runtime, sem shell
FROM gcr.io/distroless/nodejs18-debian11

# Versão específica com digest
FROM node:18.19.0-alpine3.19@sha256:...
```

#### Comparação de Imagens Base

| Imagem | Tamanho | Vulnerabilidades | Uso |
|--------|---------|------------------|-----|
| `node:18` | ~1GB | 50+ | ❌ Evitar |
| `node:18-slim` | ~250MB | 20-30 | ⚠️ OK |
| `node:18-alpine` | ~150MB | 0-5 | ✅ Recomendado |
| `distroless/nodejs18` | ~120MB | 0-2 | ✅ Produção |

---

### 1.2. Non-Root User

#### ❌ Problema: Rodar como Root

```dockerfile
FROM node:18-alpine

WORKDIR /app
COPY . .
RUN npm install

# ❌ Roda como root (UID 0)
CMD ["node", "server.js"]
```

**Risco**: Se container for comprometido, atacante tem acesso root.

#### ✅ Solução: Non-Root User

```dockerfile
FROM node:18-alpine

# Criar usuário não-privilegiado
RUN addgroup -g 1001 -S nodejs && \
    adduser -S nodejs -u 1001

WORKDIR /app

# Copiar e definir ownership
COPY --chown=nodejs:nodejs package*.json ./
RUN npm ci --only=production

COPY --chown=nodejs:nodejs . .

# Mudar para non-root user
USER nodejs

CMD ["node", "server.js"]
```

---

### 1.3. Não Incluir Secrets

#### ❌ Problema: Secrets em Imagem

```dockerfile
FROM node:18-alpine

WORKDIR /app

# ❌ Copia .env com secrets
COPY . .

# ❌ Secrets ficam em layers da imagem
RUN echo "DATABASE_URL=postgres://..." > .env

CMD ["node", "server.js"]
```

#### ✅ Solução: Secrets Externos

```dockerfile
FROM node:18-alpine

WORKDIR /app

# .dockerignore
# .env
# .env.*
# secrets/

COPY package*.json ./
RUN npm ci --only=production

COPY src/ ./src/

USER nodejs

# Secrets via environment variables
CMD ["node", "server.js"]
```

**Usar**:
- Environment variables (runtime)
- Docker secrets (Swarm)
- Kubernetes secrets
- Vault, AWS Secrets Manager

---

### 1.4. Minimizar Superfície de Ataque

#### ❌ Problema: Ferramentas Desnecessárias

```dockerfile
FROM ubuntu:22.04

# ❌ Instala ferramentas desnecessárias
RUN apt-get update && apt-get install -y \
    curl \
    wget \
    vim \
    git \
    build-essential \
    python3 \
    ...
```

#### ✅ Solução: Apenas o Necessário

```dockerfile
FROM node:18-alpine

# ✅ Alpine já é mínimo
# Instalar apenas runtime dependencies
RUN apk add --no-cache \
    tini

# Usar tini como init process
ENTRYPOINT ["/sbin/tini", "--"]
CMD ["node", "server.js"]
```

---

### 1.5. Scanning de Vulnerabilidades

#### Trivy (Recomendado)

**Instalação**:
```bash
# macOS
brew install trivy

# Linux
wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | sudo apt-key add -
echo "deb https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main" | sudo tee -a /etc/apt/sources.list.d/trivy.list
sudo apt-get update && sudo apt-get install trivy
```

**Uso**:
```bash
# Scan de imagem
trivy image node:18-alpine

# Scan com severidade
trivy image --severity HIGH,CRITICAL my-app:latest

# Scan de Dockerfile
trivy config Dockerfile

# Output JSON
trivy image --format json --output results.json my-app:latest
```

**Integração CI/CD**:
```yaml
# .github/workflows/docker-security.yml
name: Docker Security

on: [push, pull_request]

jobs:
  scan:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Build image
        run: docker build -t my-app:${{ github.sha }} .
      
      - name: Run Trivy scan
        uses: aquasecurity/trivy-action@master
        with:
          image-ref: my-app:${{ github.sha }}
          format: 'sarif'
          output: 'trivy-results.sarif'
          severity: 'CRITICAL,HIGH'
      
      - name: Upload to GitHub Security
        uses: github/codeql-action/upload-sarif@v2
        with:
          sarif_file: 'trivy-results.sarif'
```

---

#### Snyk Container

**Instalação**:
```bash
npm install -g snyk
snyk auth
```

**Uso**:
```bash
# Scan de imagem
snyk container test node:18-alpine

# Scan com fix suggestions
snyk container test my-app:latest --file=Dockerfile

# Monitor imagem
snyk container monitor my-app:latest
```

---

#### Docker Scout (GitHub/Docker Hub)

**Uso**:
```bash
# Habilitar Docker Scout
docker scout quickview

# Scan de imagem
docker scout cves my-app:latest

# Comparar com base image
docker scout compare --to node:18-alpine my-app:latest
```

---

## 2. Otimização de Tamanho

### 2.1. Multi-Stage Builds

#### ❌ Single-Stage (Grande)

```dockerfile
FROM node:18

WORKDIR /app

COPY package*.json ./
RUN npm install  # Instala dev + prod

COPY . .
RUN npm run build  # Build artifacts

CMD ["node", "dist/server.js"]
```

**Tamanho**: ~1.2GB (inclui dev deps, build tools, source code)

#### ✅ Multi-Stage (Pequeno)

```dockerfile
# Stage 1: Build
FROM node:18-alpine AS builder

WORKDIR /app

COPY package*.json ./
RUN npm ci

COPY tsconfig.json ./
COPY src/ ./src/
COPY specs/ ./specs/

RUN npm run build
RUN npm prune --production

# Stage 2: Runtime
FROM node:18-alpine

RUN addgroup -g 1001 -S nodejs && \
    adduser -S nodejs -u 1001

WORKDIR /app

# Copia apenas necessário do builder
COPY --from=builder --chown=nodejs:nodejs /app/dist ./dist
COPY --from=builder --chown=nodejs:nodejs /app/node_modules ./node_modules
COPY --from=builder --chown=nodejs:nodejs /app/package.json ./

USER nodejs

CMD ["node", "dist/server.js"]
```

**Tamanho**: ~150MB (apenas runtime + prod deps + compiled code)

---

### 2.2. Usar .dockerignore

```
# .dockerignore
node_modules/
npm-debug.log
dist/
coverage/
.git/
.github/
.vscode/
.env
.env.*
*.md
Dockerfile
docker-compose.yml
.dockerignore

# Arquivos de desenvolvimento
*.test.ts
*.spec.ts
__tests__/
__mocks__/

# Documentação
docs/
README.md

# CI/CD
.gitlab-ci.yml
.travis.yml
Jenkinsfile
```

**Benefício**: Reduz contexto de build e tamanho da imagem.

---

### 2.3. Combinar RUN Commands

#### ❌ Múltiplos Layers

```dockerfile
FROM node:18-alpine

RUN apk add --no-cache curl
RUN apk add --no-cache wget
RUN apk add --no-cache git
RUN npm install -g pm2
```

**Problema**: Cada `RUN` cria um layer, aumentando tamanho.

#### ✅ Single Layer

```dockerfile
FROM node:18-alpine

RUN apk add --no-cache \
    curl \
    wget \
    git && \
    npm install -g pm2 && \
    rm -rf /tmp/* /var/cache/apk/*
```

---

### 2.4. Remover Cache

```dockerfile
FROM node:18-alpine

WORKDIR /app

COPY package*.json ./

# Limpar cache npm
RUN npm ci --only=production && \
    npm cache clean --force && \
    rm -rf /tmp/*

COPY . .

CMD ["node", "server.js"]
```

---

### 2.5. Usar Distroless para Produção

```dockerfile
# Stage 1: Build
FROM node:18-alpine AS builder

WORKDIR /app

COPY package*.json ./
RUN npm ci

COPY . .
RUN npm run build

# Stage 2: Runtime (Distroless)
FROM gcr.io/distroless/nodejs18-debian11

WORKDIR /app

COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/package.json ./

USER nonroot:nonroot

CMD ["dist/server.js"]
```

**Benefícios**:
- ✅ Sem shell (sem acesso para atacantes)
- ✅ Apenas runtime necessário
- ✅ Menor superfície de ataque
- ✅ ~120MB

---

## 3. Performance de Containers

### 3.1. Layer Caching

#### ❌ Ordem Ruim (Cache Ineficiente)

```dockerfile
FROM node:18-alpine

WORKDIR /app

# ❌ Copia tudo primeiro
COPY . .

# Qualquer mudança em código invalida cache
RUN npm install

CMD ["node", "server.js"]
```

#### ✅ Ordem Otimizada

```dockerfile
FROM node:18-alpine

WORKDIR /app

# ✅ Copia package.json primeiro
COPY package*.json ./

# Cache de npm install (só invalida se package.json mudar)
RUN npm ci --only=production

# Copia código depois
COPY . .

CMD ["node", "server.js"]
```

---

### 3.2. Build Kit

**Habilitar BuildKit**:
```bash
# Temporário
DOCKER_BUILDKIT=1 docker build -t my-app .

# Permanente
echo 'export DOCKER_BUILDKIT=1' >> ~/.bashrc
```

**Benefícios**:
- ✅ Builds paralelos
- ✅ Cache mais eficiente
- ✅ Secrets seguros
- ✅ SSH forwarding

**Exemplo com BuildKit**:
```dockerfile
# syntax=docker/dockerfile:1.4

FROM node:18-alpine

WORKDIR /app

# Cache mount para npm
RUN --mount=type=cache,target=/root/.npm \
    npm ci --only=production

COPY . .

CMD ["node", "server.js"]
```

---

### 3.3. Healthchecks

```dockerfile
FROM node:18-alpine

WORKDIR /app

COPY package*.json ./
RUN npm ci --only=production

COPY . .

# Healthcheck
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD node healthcheck.js || exit 1

CMD ["node", "server.js"]
```

**healthcheck.js**:
```javascript
const http = require('http');

const options = {
  host: 'localhost',
  port: 3000,
  path: '/health',
  timeout: 2000,
};

const request = http.request(options, (res) => {
  if (res.statusCode === 200) {
    process.exit(0);
  } else {
    process.exit(1);
  }
});

request.on('error', () => {
  process.exit(1);
});

request.end();
```

---

### 3.4. Resource Limits

**docker-compose.yml**:
```yaml
version: '3.8'

services:
  api:
    build: .
    ports:
      - "3000:3000"
    
    # Resource limits
    deploy:
      resources:
        limits:
          cpus: '1.0'
          memory: 512M
        reservations:
          cpus: '0.5'
          memory: 256M
    
    # Restart policy
    restart: unless-stopped
    
    # Healthcheck
    healthcheck:
      test: ["CMD", "node", "healthcheck.js"]
      interval: 30s
      timeout: 3s
      retries: 3
      start_period: 5s
```

---

## 4. Dockerfile Completo para SDD

### Node.js + TypeScript + OpenAPI

```dockerfile
# syntax=docker/dockerfile:1.4

# ============================================
# Stage 1: Dependencies
# ============================================
FROM node:18-alpine AS deps

WORKDIR /app

# Copiar package files
COPY package.json package-lock.json ./

# Instalar dependências com cache mount
RUN --mount=type=cache,target=/root/.npm \
    npm ci --only=production && \
    npm cache clean --force

# ============================================
# Stage 2: Builder
# ============================================
FROM node:18-alpine AS builder

WORKDIR /app

# Copiar package files
COPY package.json package-lock.json tsconfig.json ./

# Instalar todas as dependências (incluindo dev)
RUN --mount=type=cache,target=/root/.npm \
    npm ci

# Copiar source code e specs
COPY src/ ./src/
COPY specs/ ./specs/

# Build TypeScript
RUN npm run build

# Validar specs
RUN npm install -g @stoplight/spectral-cli && \
    spectral lint specs/*.yaml

# ============================================
# Stage 3: Runtime
# ============================================
FROM node:18-alpine

# Instalar tini (init process)
RUN apk add --no-cache tini

# Criar non-root user
RUN addgroup -g 1001 -S nodejs && \
    adduser -S nodejs -u 1001

WORKDIR /app

# Copiar prod dependencies do stage deps
COPY --from=deps --chown=nodejs:nodejs /app/node_modules ./node_modules

# Copiar build artifacts do stage builder
COPY --from=builder --chown=nodejs:nodejs /app/dist ./dist
COPY --from=builder --chown=nodejs:nodejs /app/specs ./specs
COPY --from=builder --chown=nodejs:nodejs /app/package.json ./

# Healthcheck script
COPY --chown=nodejs:nodejs healthcheck.js ./

# Mudar para non-root user
USER nodejs

# Expor porta
EXPOSE 3000

# Healthcheck
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD node healthcheck.js || exit 1

# Usar tini como init
ENTRYPOINT ["/sbin/tini", "--"]

# Start application
CMD ["node", "dist/server.js"]
```

---

### Python + FastAPI + OpenAPI

```dockerfile
# syntax=docker/dockerfile:1.4

# ============================================
# Stage 1: Builder
# ============================================
FROM python:3.11-slim AS builder

WORKDIR /app

# Instalar dependências de build
RUN apt-get update && apt-get install -y --no-install-recommends \
    gcc \
    && rm -rf /var/lib/apt/lists/*

# Copiar requirements
COPY requirements.txt ./

# Instalar dependências em venv
RUN python -m venv /opt/venv
ENV PATH="/opt/venv/bin:$PATH"
RUN pip install --no-cache-dir -r requirements.txt

# Copiar código
COPY src/ ./src/
COPY specs/ ./specs/

# ============================================
# Stage 2: Runtime
# ============================================
FROM python:3.11-slim

# Criar non-root user
RUN groupadd -r appuser && useradd -r -g appuser appuser

WORKDIR /app

# Copiar venv do builder
COPY --from=builder /opt/venv /opt/venv

# Copiar código
COPY --from=builder --chown=appuser:appuser /app/src ./src
COPY --from=builder --chown=appuser:appuser /app/specs ./specs

# Ativar venv
ENV PATH="/opt/venv/bin:$PATH"

# Mudar para non-root user
USER appuser

# Expor porta
EXPOSE 8000

# Healthcheck
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD python -c "import requests; requests.get('http://localhost:8000/health')"

# Start application
CMD ["uvicorn", "src.main:app", "--host", "0.0.0.0", "--port", "8000"]
```

---

## 5. CI/CD Pipeline Completo

```yaml
# .github/workflows/docker.yml
name: Docker Build & Security

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

env:
  REGISTRY: ghcr.io
  IMAGE_NAME: ${{ github.repository }}

jobs:
  build-and-scan:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      packages: write
      security-events: write
    
    steps:
      - uses: actions/checkout@v3
      
      # Setup BuildKit
      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v2
      
      # Login to registry
      - name: Log in to Container Registry
        uses: docker/login-action@v2
        with:
          registry: ${{ env.REGISTRY }}
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}
      
      # Build image
      - name: Build Docker image
        uses: docker/build-push-action@v4
        with:
          context: .
          push: false
          load: true
          tags: ${{ env.IMAGE_NAME }}:${{ github.sha }}
          cache-from: type=gha
          cache-to: type=gha,mode=max
      
      # Scan com Trivy
      - name: Run Trivy vulnerability scanner
        uses: aquasecurity/trivy-action@master
        with:
          image-ref: ${{ env.IMAGE_NAME }}:${{ github.sha }}
          format: 'sarif'
          output: 'trivy-results.sarif'
          severity: 'CRITICAL,HIGH'
      
      - name: Upload Trivy results to GitHub Security
        uses: github/codeql-action/upload-sarif@v2
        with:
          sarif_file: 'trivy-results.sarif'
      
      # Scan com Snyk
      - name: Run Snyk to check Docker image
        uses: snyk/actions/docker@master
        env:
          SNYK_TOKEN: ${{ secrets.SNYK_TOKEN }}
        with:
          image: ${{ env.IMAGE_NAME }}:${{ github.sha }}
          args: --severity-threshold=high
      
      # Verificar tamanho da imagem
      - name: Check image size
        run: |
          SIZE=$(docker image inspect ${{ env.IMAGE_NAME }}:${{ github.sha }} --format='{{.Size}}')
          SIZE_MB=$((SIZE / 1024 / 1024))
          echo "Image size: ${SIZE_MB}MB"
          if [ $SIZE_MB -gt 500 ]; then
            echo "::warning::Image size (${SIZE_MB}MB) exceeds 500MB threshold"
          fi
      
      # Push se tudo passou
      - name: Push Docker image
        if: github.event_name == 'push' && github.ref == 'refs/heads/main'
        uses: docker/build-push-action@v4
        with:
          context: .
          push: true
          tags: |
            ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}:latest
            ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}:${{ github.sha }}
          cache-from: type=gha
          cache-to: type=gha,mode=max
```

---

## 6. Boas Práticas para SDD

### 6.1. Incluir Specs na Imagem

```dockerfile
FROM node:18-alpine

WORKDIR /app

# Incluir specs para validação em runtime
COPY specs/ ./specs/

# Código pode validar requests contra specs
COPY . .

CMD ["node", "server.js"]
```

### 6.2. Validar Specs no Build

```dockerfile
FROM node:18-alpine AS validator

WORKDIR /app

COPY specs/ ./specs/

# Validar specs durante build
RUN npm install -g @stoplight/spectral-cli && \
    spectral lint specs/*.yaml && \
    npm uninstall -g @stoplight/spectral-cli

# Se specs inválidas, build falha
```

### 6.3. Gerar Código da Spec no Build

```dockerfile
FROM node:18-alpine AS generator

WORKDIR /app

COPY specs/ ./specs/

# Gerar código da spec
RUN npm install -g @openapitools/openapi-generator-cli && \
    openapi-generator-cli generate \
      -i specs/api.yaml \
      -g typescript-node \
      -o generated/

# Usar código gerado no runtime
```

### 6.4. Documentação da API na Imagem

```dockerfile
FROM node:18-alpine

WORKDIR /app

# Incluir specs para Swagger UI
COPY specs/ ./public/specs/

# Swagger UI pode servir specs
COPY . .

CMD ["node", "server.js"]
```

---

## 7. Comparação de Estratégias

| Estratégia | Tamanho | Segurança | Performance | Complexidade |
|------------|---------|-----------|-------------|--------------|
| **Single-stage + node:18** | ~1.2GB | ⚠️ Baixa | ⚠️ Média | ✅ Simples |
| **Single-stage + alpine** | ~400MB | ⚠️ Média | ✅ Boa | ✅ Simples |
| **Multi-stage + alpine** | ~150MB | ✅ Alta | ✅ Boa | ⚠️ Média |
| **Multi-stage + distroless** | ~120MB | ✅ Muito Alta | ✅ Ótima | ⚠️ Complexa |

---

## 8. Ferramentas de Análise

### Dive (Análise de Layers)

**Instalação**:
```bash
brew install dive
```

**Uso**:
```bash
dive my-app:latest
```

**Benefícios**:
- Visualiza layers da imagem
- Identifica arquivos duplicados
- Mostra desperdício de espaço

---

### Docker Slim

**Instalação**:
```bash
brew install docker-slim
```

**Uso**:
```bash
# Analisar imagem
docker-slim xray my-app:latest

# Otimizar imagem (reduz até 30x)
docker-slim build my-app:latest
```

---

## Checklist de Segurança e Performance

### Build
- [ ] Usar imagem base Alpine ou Distroless
- [ ] Multi-stage build implementado
- [ ] .dockerignore configurado
- [ ] Non-root user configurado
- [ ] Secrets não incluídos na imagem
- [ ] Specs validadas durante build
- [ ] Layers combinados (RUN único)
- [ ] Cache limpo após instalações

### Runtime
- [ ] Healthcheck configurado
- [ ] Resource limits definidos
- [ ] Restart policy configurada
- [ ] Logs estruturados
- [ ] Monitoring configurado

### Segurança
- [ ] Scan de vulnerabilidades (Trivy/Snyk)
- [ ] Imagem assinada (Docker Content Trust)
- [ ] Secrets via environment variables
- [ ] Network policies configuradas
- [ ] Read-only filesystem (quando possível)

### Performance
- [ ] Imagem < 500MB
- [ ] Build time < 5min
- [ ] Startup time < 10s
- [ ] Memory usage < 512MB
- [ ] CPU usage < 1 core

---

## Recursos Adicionais

### Documentação Oficial
- **[Docker Best Practices](https://docs.docker.com/develop/dev-best-practices/)**: Guia oficial
- **[Dockerfile Reference](https://docs.docker.com/engine/reference/builder/)**: Referência completa
- **[Docker Security](https://docs.docker.com/engine/security/)**: Segurança Docker

### Ferramentas
- **[Trivy](https://github.com/aquasecurity/trivy)**: Vulnerability scanner
- **[Snyk](https://snyk.io/)**: Container security
- **[Dive](https://github.com/wagoodman/dive)**: Layer analysis
- **[Docker Slim](https://github.com/docker-slim/docker-slim)**: Image optimization

### Guias
- **[OWASP Docker Security](https://cheatsheetseries.owasp.org/cheatsheets/Docker_Security_Cheat_Sheet.html)**: Security cheat sheet
- **[CIS Docker Benchmark](https://www.cisecurity.org/benchmark/docker)**: Security benchmark

---

## Conclusão

**Segurança e performance em Docker são essenciais para SDD**:

1. **Segurança**:
   - Alpine/Distroless base images
   - Non-root user
   - Vulnerability scanning (Trivy, Snyk)
   - Sem secrets na imagem

2. **Performance**:
   - Multi-stage builds
   - Layer caching otimizado
   - BuildKit habilitado
   - Resource limits

3. **Tamanho**:
   - .dockerignore configurado
   - Apenas prod dependencies
   - Layers combinados
   - Imagens < 500MB

**Workflow Ideal**:
```
Spec → Validate → Generate Code → Build (Multi-stage) → 
Scan (Trivy/Snyk) → Test → Push → Deploy
```

**Lembre-se**: Segurança e performance não são opcionais, são **requisitos fundamentais** para aplicações em produção.

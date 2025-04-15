# 📅 Dia 1 e 2 – Datadog Week – LinuxTips

Documentação referente aos dois primeiros dias da maratona **Datadog Week** promovida pela [LinuxTips](https://www.youtube.com/c/LinuxTips).  
Data da execução: **14/04/2025**

## 🧰 Tecnologias e Ferramentas Utilizadas

- `Kind` (Kubernetes in Docker)
- `kubectl`
- `Helm`
- `Datadog Agent + Cluster Agent`
- `PostgreSQL` via container
- `Spree E-commerce` (aplicação de exemplo em microserviços)

---

## 🏗️ Estrutura do Projeto

datadog-week/ └── Dia-1/ ├── spree-ecommerce/ │ ├── advertisements.yaml │ ├── discounts.yaml │ ├── frontend.yaml │ ├── db.yaml ├── values.yaml └── Readme.md

yaml
Copiar
Editar

---

## ⚙️ Passo a Passo Executado

### 1. Criação do cluster local com Kind
```bash
kind create cluster
2. Deploy da aplicação Spree
bash
Copiar
Editar
kubectl apply -f spree-ecommerce/
3. Instalação do Helm
bash
Copiar
Editar
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
4. Adição do repositório do Datadog
bash
Copiar
Editar
helm repo add datadog https://helm.datadoghq.com
helm repo update
5. Criação do Secret com API key do Datadog
bash
Copiar
Editar
kubectl create secret generic datadog-secret --from-literal api-key=<SUA_API_KEY>
6. Criação do arquivo values.yaml
Arquivo customizado para executar o agente no ambiente Kind:

yaml
Copiar
Editar
datadog:
  site: datadoghq.com
  apiKeyExistingSecret: datadog-secret
  clusterName: datadog-week
  tags:
    - app:ecommerce
    - project:datadog-week
    - team:sre
    - env:prd
  logs:
    enabled: true
    containerCollectAll: true
  apm:
    portEnabled: true
  processAgent:
    enabled: true
    processCollection: true
  kubelet:
    tlsVerify: false

clusterAgent:
  enabled: true
  createPodDisruptionBudget: true
7. Instalação do agente Datadog com Helm
bash
Copiar
Editar
helm install datadog-agent -f values.yaml datadog/datadog
✅ Validações realizadas
 Host kind-control-plane listado como ativo no Datadog

 Logs de containers disponíveis

 Monitoramento de PostgreSQL via annotations:

yaml
Copiar
Editar
annotations:
  ad.datadoghq.com/postgres.checks: |
    {
      "postgres": {
        "instances": [
          {
            "host": "%%host%%",
            "port":"5432",
            "username":"user",
            "password":"datadog"
          }
        ]
      }
    }
 APM ativado

 Métricas exibidas no dashboard customizado:

Conexões ativas

Deadlocks

Buffers hit/s

Rows returned/s

CPU user/system

📊 Dashboard criado
Nome: Dashboard PostgreSQL Personalizado

Widgets incluídos:

Query Value: postgresql.connections

Query Value: postgresql.deadlocks

Query Value: buffer_hit

Query Value: rows_returned

Timeseries: system.cpu.user

Query Value: bgwriter.checkpoints_req
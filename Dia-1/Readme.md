README.md — Datadog Week – Dia 1 e 2
🧪 Ambiente Local com Kind + Helm + Datadog Agent
📦 Aplicação:
Spree E-commerce com microserviços em YAML:

advertisements

discounts

frontend

db (PostgreSQL)

⚙️ Passos realizados:
Criação do cluster Kubernetes local:

bash
Copiar
Editar
kind create cluster
Aplicação da stack:

bash
Copiar
Editar
kubectl apply -f spree-ecommerce/
Instalação do Helm:

bash
Copiar
Editar
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
Adição do repositório Datadog e atualização:

bash
Copiar
Editar
helm repo add datadog https://helm.datadoghq.com
helm repo update
Criação do Secret com a API Key:

bash
Copiar
Editar
kubectl create secret generic datadog-secret --from-literal api-key=<SUA_API_KEY>
Criação do values.yaml personalizado para Kind:

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
Instalação do agente Datadog:

bash
Copiar
Editar
helm install datadog-agent -f values.yaml datadog/datadog
✅ Validações:
Host kind-control-plane-datadog-week reconhecido na Datadog

Métricas visíveis: CPU, memória, disco, rede

Aplicação monitorada com logs, processos, APM

PostgreSQL integrado com check personalizado via annotation

🧩 Integração com PostgreSQL:
Trecho adicionado no spree-ecommerce/db.yaml:

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
📊 Próximo passo sugerido:
✅ Criar dashboard PostgreSQL personalizado com:

Conexões ativas

Queries por segundo

Locks/deadlocks

Tempo médio de resposta
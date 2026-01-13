# security-challenge

Visualizar o sqlite local
https://download.sqlitebrowser.org/



flowchart TB
  %% Clients
  U[Usuários / Apps / Parceiros] -->|HTTPS| WAF[Azure WAF]

  %% Edge / API layer
  WAF -->|HTTPS| APIM[Azure API Management\n(API Gateway)]

  %% Auth
  U -->|OAuth2 / OpenID Connect| ENTRA[Azure Entra ID\n(AuthN/AuthZ)]

  %% APIM to AKS
  APIM -->|REST| INGRESS[AKS Ingress / Service Mesh (opcional)]
  INGRESS --> MS1[Microserviço A]
  INGRESS --> MS2[Microserviço B]
  INGRESS --> MS3[Microserviço C]

  %% Service-to-service (optional)
  MS1 -->|REST/gRPC| MS2
  MS2 -->|REST/gRPC| MS3

  %% Secrets
  KV[Azure Key Vault\n(Secrets/Keys/Certs)]
  MS1 -->|Managed Identity / Secret ref| KV
  MS2 -->|Managed Identity / Secret ref| KV
  MS3 -->|Managed Identity / Secret ref| KV

  %% Observability
  MON[Azure Monitor]
  LA[Log Analytics]
  APIM -->|Logs/Metrics| MON
  INGRESS -->|Logs/Metrics| MON
  MS1 -->|App Logs/Traces| MON
  MS2 -->|App Logs/Traces| MON
  MS3 -->|App Logs/Traces| MON
  MON --> LA

  %% CI/CD
  DEVOPS[Azure DevOps\nRepos + Pipelines]
  ACR[Azure Container Registry]
  DEVOPS -->|Build/Test| DEVOPS
  DEVOPS -->|Build Image| ACR
  DEVOPS -->|Deploy (Helm/YAML)| AKS[Azure Kubernetes Service (AKS)]
  ACR -->|Pull Image| AKS
  AKS --> INGRESS

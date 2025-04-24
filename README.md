# FluxCD Demo para Oracle Cloud Infrastructure (OKE)

Este repositório contém uma demonstração de um fluxo GitOps usando FluxCD no Kubernetes da Oracle Cloud Infrastructure (OKE).

## Estrutura do Repositório

```
.
├── apps/                     # Diretório contendo aplicações
│   └── base/                 # Manifests base do aplicativo Hello World
│       ├── deployment.yaml   # Deployment do Hello World com probes e resource limits
│       ├── kustomization.yaml  # Configuração Kustomize
│       └── service.yaml      # Service do Hello World com configurações do OCI LB
├── clusters/                 # Configurações específicas dos clusters
│   └── my-cluster/           # Configuração do cluster de demonstração
│       ├── gitrepository.yaml  # Configuração da fonte Git
│       ├── kustomization.yaml  # Configuração Kustomize nativa
│       └── kustomization-flux.yaml  # Configuração FluxCD Kustomization
└── infrastructure/           # Recursos de infraestrutura
    └── base/                 # Recursos base de infraestrutura
        ├── kustomization.yaml  # Configuração Kustomize
        └── namespace.yaml    # Definição do namespace demo

```

## Instruções de Uso

### Pré-requisitos

- Oracle Kubernetes Engine (OKE) configurado
- kubectl configurado para seu cluster OKE
- Flux CLI instalado
- Um repositório GitHub (private ou public)

### Instalação do Flux

1. Clone este repositório:
   ```bash
   git clone https://github.com/<seu-usuario>/<seu-repo>
   cd <seu-repo>
   ```

2. Atualize o URL do repositório em `clusters/my-cluster/gitrepository.yaml` para o seu próprio repositório.

3. Bootstrap do Flux no cluster OKE:
   ```bash
   flux bootstrap github \
     --owner=<seu-usuario-github> \
     --repository=<seu-repo> \
     --branch=main \
     --path=./clusters/my-cluster \
     --personal
   ```

### Verificação

Verifique se os recursos foram aplicados:

```bash
kubectl get deployments,services -n demo
flux get kustomizations
flux get sources git
```

## Características

- Aplicação Hello World de demonstração com probes e limites de recursos
- Configuração para Oracle Cloud Load Balancer
- Estrutura GitOps para manutenção contínua
- Verificação de saúde dos deployments
- Namespace dedicada (`demo`) para isolamento de recursos

## Próximos Passos

1. Adicione mais aplicações na pasta `apps/`
2. Configure múltiplos ambientes (dev, staging, prod)
3. Implemente workflows de CI para testes antes da entrega

## Referências

- [Documentação do FluxCD](https://fluxcd.io/docs/)
- [Documentação do OKE](https://docs.oracle.com/en-us/iaas/Content/ContEng/home.htm) 
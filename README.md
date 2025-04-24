# FluxCD Demo para Oracle Cloud Infrastructure (OKE)

Este repositório contém uma demonstração de um fluxo GitOps usando FluxCD no Kubernetes da Oracle Cloud Infrastructure (OKE).

## Estrutura do Repositório

```
.
├── apps/                     # Diretório contendo aplicações
│   └── base/                 # Manifests base do aplicativo NGINX
│       ├── deployment.yaml   # Deployment do NGINX com probes e resource limits
│       ├── kustomization.yaml  # Configuração Kustomize (lista deployment.yaml e service.yaml)
│       └── service.yaml      # Service do NGINX com configurações do OCI LB
├── clusters/                 # Configurações específicas dos clusters
│   └── my-cluster/           # Configuração do cluster de demonstração
│       ├── gitrepository.yaml     # Define a fonte Git para o Flux (seu repositório)
│       ├── kustomization.yaml     # Configuração Kustomize nativa (lista os outros arquivos yaml)
│       ├── kustomization-flux.yaml  # Define como o Flux aplica os arquivos em apps/base
│       └── kustomization-infra.yaml # Define como o Flux aplica os arquivos em infrastructure/base
└── infrastructure/           # Recursos de infraestrutura
    └── base/                 # Recursos base de infraestrutura
        ├── kustomization.yaml  # Configuração Kustomize (lista os recursos abaixo)
        └── namespace.yaml      # Definição do namespace demo

```

## Fluxo de Execução

1. O Flux obtém os arquivos do repositório Git conforme definido em `gitrepository.yaml`
2. O Flux aplica primeiro a infraestrutura (`kustomization-infra.yaml`) para configurar o namespace `demo`
3. Após a configuração da infraestrutura, o Flux aplica a aplicação (`kustomization-flux.yaml`)
4. O Flux continua monitorando o repositório para mudanças conforme o intervalo definido

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

- Aplicação NGINX de demonstração com probes e limites de recursos
- Configuração para Oracle Cloud Load Balancer
- Estrutura GitOps para manutenção contínua
- Verificação de saúde dos deployments
- Namespace configurada (`demo`) com labels apropriadas
- Dependências claramente definidas entre infraestrutura e aplicação

## Próximos Passos

1. Adicione mais aplicações na pasta `apps/`
2. Configure múltiplos ambientes (dev, staging, prod)
3. Implemente workflows de CI para testes antes da entrega

## Solução de Problemas

Se a aplicação não for implantada corretamente, verifique:

1. A configuração do namespace: `kubectl describe namespace demo`
2. O status das kustomizations: `flux get kustomizations`
3. Os logs detalhados: `flux logs --kind=kustomization --name=infrastructure`

## Referências

- [Documentação do FluxCD](https://fluxcd.io/docs/)
- [Documentação do OKE](https://docs.oracle.com/en-us/iaas/Content/ContEng/home.htm) 
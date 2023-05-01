# AZ 204

## General Snippets

### Set global config

```pwsh
$myRG="rg-az204-djoh-certificate"
```

```pwsh
$myLocation="westeurope"
```

### List & remove resources in a resource group

```pwsh
$resources = az resource list --resource-group $myRG | ConvertFrom-Json
```

```pwsh
$resources | Select name, type, id
```

```pwsh
az resource delete --resource-group $myRG --ids $resources[<resourceIndex>].id --verbose
```

## Implement containerized solutions

### Manage container images in Azure Container Registry

```pwsh
az acr create --resource-group $myRG --name "acrdjoh" --sku "Basic"
```

```pwsh
az acr build --image "sample/hello-world:v1" --registry "acrdjoh" --file "Dockerfile" .
```

```pwsh
az acr repository list --name "acrdjoh" --output "table"
```

```pwsh
az acr repository show-tags --name "acrdjoh" --repository "sample/hello-world" --output "table"
```

```pwsh
az acr run --registry "acrdjoh" --cmd "$Registry/sample/hello-world:v1" "/dev/null"
```

```pwsh
az acr delete --resource-group $myRG --name "acrdjoh"
```

### Run container images in Azure Container Instances

```pwsh
$DNS_NAME_LABEL="aci-example-$(Get-Random)"
```

```pwsh
az container create --resource-group $myRG --name "mycontainer" --image "mcr.microsoft.com/azuredocs/aci-helloworld" --ports 80 --dns-name-label $DNS_NAME_LABEL --location $myLocation
```

```pwsh
az container show --resource-group $myRG --name "mycontainer" --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" --out "table"
```

```pwsh
az container delete --resource-group $myRG --name "mycontainer"
```

### Implement Azure Container Apps

```pwsh
$myAppContEnv="az204-env-$(Get-Random)"
```

```pwsh
az containerapp env create `
    --name $myAppContEnv `
    --resource-group $myRG `
    --location $myLocation
```

```pwsh
az containerapp create `
    --name my-container-app `
    --resource-group $myRG `
    --environment $myAppContEnv `
    --image mcr.microsoft.com/azuredocs/containerapps-helloworld:latest `
    --target-port 80 `
    --ingress 'external' `
    --query properties.configuration.ingress.fqdn
```

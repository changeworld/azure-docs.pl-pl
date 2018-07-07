---
title: Uwierzytelnianie za pomocą usługi Azure Container Registry z usługi Azure Kubernetes Service
description: Dowiedz się, jak zapewnić dostęp do obrazów w prywatnego rejestru kontenera z usługi Azure Kubernetes Service za pomocą jednostki usługi Azure Active Directory.
services: container-service
author: mmacy
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 02/24/2018
ms.author: marsma
ms.openlocfilehash: 36b66fdcd157e4c44fcd451c8cc07ba68242b583
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/06/2018
ms.locfileid: "37888766"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Uwierzytelnianie za pomocą usługi Azure Container Registry z usługi Azure Kubernetes Service

Podczas korzystania z usługi Azure Container Registry (ACR) przy użyciu usługi Azure Kubernetes Service (AKS), mechanizm uwierzytelniania musi zostać ustanowione. Ten dokument wyszczególnia zalecanym konfiguracjom dotyczącym uwierzytelniania między tymi dwoma usługami platformy Azure.

## <a name="grant-aks-access-to-acr"></a>AKS udzielanie dostępu do usługi ACR

Po utworzeniu klastra usługi AKS z nazwy głównej usługi jest również utworzone w celu zarządzania sprawność działania klastra, z zasobami platformy Azure. Ta jednostka usługi również może służyć do uwierzytelniania za pomocą rejestru ACR. Aby to zrobić, przypisanie roli musi zostać utworzona udzielenia jednostce usługi odczytu dostępu do zasobu usługi ACR.

Poniższy przykład może służyć do ukończenia tej operacji.

```bash
#!/bin/bash

AKS_RESOURCE_GROUP=myAKSResourceGroup
AKS_CLUSTER_NAME=myAKSCluster
ACR_RESOURCE_GROUP=myACRResourceGroup
ACR_NAME=myACRRegistry

# Get the id of the service principal configured for AKS
CLIENT_ID=$(az aks show --resource-group $AKS_RESOURCE_GROUP --name $AKS_CLUSTER_NAME --query "servicePrincipalProfile.clientId" --output tsv)

# Get the ACR registry resource id
ACR_ID=$(az acr show --name $ACR_NAME --resource-group $ACR_RESOURCE_GROUP --query "id" --output tsv)

# Create role assignment
az role assignment create --assignee $CLIENT_ID --role Reader --scope $ACR_ID
```

## <a name="access-with-kubernetes-secret"></a>Dostęp za pomocą wpisie tajnym rozwiązania Kubernetes

W niektórych przypadkach usługa podmiotu zabezpieczeń, używane przez usługę AKS nie wchodzi do zakresu do rejestru ACR. W takich przypadkach można utworzyć nazwę główną usługi unikatowy i zakres go w rejestrze usługi ACR.

Poniższy skrypt może służyć do tworzenia nazwy głównej usługi.

```bash
#!/bin/bash

ACR_NAME=myacrinstance
SERVICE_PRINCIPAL_NAME=acr-service-principal

# Populate the ACR login server and resource id.
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --query loginServer --output tsv)
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)

# Create a contributor role assignment with a scope of the ACR resource.
SP_PASSWD=$(az ad sp create-for-rbac --name $SERVICE_PRINCIPAL_NAME --role Reader --scopes $ACR_REGISTRY_ID --query password --output tsv)

# Get the service principle client id.
CLIENT_ID=$(az ad sp show --id http://$SERVICE_PRINCIPAL_NAME --query appId --output tsv)

# Output used when creating Kubernetes secret.
echo "Service principal ID: $CLIENT_ID"
echo "Service principal password: $SP_PASSWD"
```

Teraz można przechowywać poświadczenia nazwy głównej usługi w usłudze Kubernetes [klucz tajny ściągania obrazów] [ image-pull-secret] i sprawdzone w momencie uruchamiania kontenerów w klastrze AKS.

Następujące polecenie tworzy wpisu tajnego rozwiązania Kubernetes. Zamień na nazwę użytkownika, nazwę serwera, przy użyciu serwera logowania usługi ACR identyfikator jednostki usługi i hasło, za pomocą hasło jednostki usługi.

```bash
kubectl create secret docker-registry acr-auth --docker-server <acr-login-server> --docker-username <service-principal-ID> --docker-password <service-principal-password> --docker-email <email-address>
```

Wpisie tajnym rozwiązania Kubernetes, mogą być używane w wdrożenia zasobnik przy użyciu `ImagePullSecrets` parametru.

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: acr-auth-example
spec:
  template:
    metadata:
      labels:
        app: acr-auth-example
    spec:
      containers:
      - name: acr-auth-example
        image: myacrregistry.azurecr.io/acr-auth-example
      imagePullSecrets:
      - name: acr-auth
```

<!-- LINKS - external -->
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[image-pull-secret]: https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets

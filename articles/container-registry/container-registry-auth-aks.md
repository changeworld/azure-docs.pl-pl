---
title: Uwierzytelniania za pomocą rejestru kontenera platformy Azure z usługi Azure Kubernetes
description: Dowiedz się, jak zapewnić dostęp do obrazów w rejestrze prywatnej kontener z usługi Azure Kubernetes przy użyciu nazwy głównej usługi Azure Active Directory.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 02/24/2018
ms.author: iainfou
ms.openlocfilehash: 8cfd70275caa13c708f7d2f46cdc71e0f190ca0e
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37100627"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Uwierzytelniania za pomocą rejestru kontenera platformy Azure z usługi Azure Kubernetes

Podczas korzystania z rejestru kontenera platformy Azure (ACR) z usługi Kubernetes Azure (AKS), można ustanowić musi mechanizmu uwierzytelniania. Ten dokument zawiera szczegóły zalecanym konfiguracjom dotyczącym uwierzytelniania między tych dwóch usług Azure.

## <a name="grant-aks-access-to-acr"></a>AKS Udziel dostępu do awaryjnego

Po utworzeniu klastra AKS nazwy głównej usługi tworzona jest również zarządzać sprawność działania klastra z zasobów platformy Azure. Tej nazwy głównej usługi również może służyć do uwierzytelniania przy użyciu rejestru ACR. Aby to zrobić, przypisanie roli musi zostać utworzona do usługi głównej dostęp do zasobów ACR.

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

## <a name="access-with-kubernetes-secret"></a>Dostęp z Kubernetes klucz tajny

W niektórych przypadkach usługa główna używana przez AKS nie wchodzą do zakresu w rejestrze ACR. Dla tych przypadków można utworzyć nazwy głównej usługi unikatowy i zakres go w rejestrze ACR.

Poniższy skrypt umożliwia tworzenie nazwy głównej usługi.

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

Poświadczenia główne usługi teraz mogą być przechowywane w Kubernetes [klucz tajny ściągania obrazu] [ image-pull-secret] i odwołuje się do podczas uruchamiania w klastrze AKS kontenerów.

Poniższe polecenie tworzy Kubernetes tajny. Zamień na nazwę serwera na serwerze ACR logowania, nazwy użytkownika identyfikator podmiotu zabezpieczeń usługi oraz hasła za pomocą hasła głównej usługi.

```bash
kubectl create secret docker-registry acr-auth --docker-server <acr-login-server> --docker-username <service-principal-ID> --docker-password <service-principal-password> --docker-email <email-address>
```

Klucz tajny Kubernetes mogą być używane w wdrożenia pod przy użyciu `ImagePullSecrets` parametru.

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

---
title: Uwierzytelnianie za pomocą Azure Container Registry z usługi Azure Kubernetes
description: Dowiedz się, jak zapewnić dostęp do obrazów w prywatnym rejestrze kontenerów z usługi Azure Kubernetes Service przy użyciu nazwy głównej usługi Azure Active Directory.
services: container-service
author: dlepow
manager: gwallace
ms.service: container-service
ms.topic: article
ms.date: 08/27/2019
ms.author: danlep
ms.openlocfilehash: dc5276227913d2da6e52ee3c0fb493b98e86688a
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827763"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Uwierzytelnianie za pomocą Azure Container Registry z usługi Azure Kubernetes

W przypadku korzystania z Azure Container Registry (ACR) z usługą Azure Kubernetes Service (AKS) należy nawiązać mechanizm uwierzytelniania. W tym artykule opisano zalecane konfiguracje uwierzytelniania między tymi dwiema usługami platformy Azure.

Wystarczy tylko skonfigurować jedną z tych metod uwierzytelniania. Najbardziej typowym podejściem jest [przyznanie dostępu przy użyciu nazwy głównej usługi AKS](#grant-aks-access-to-acr). Jeśli masz określone potrzeby, możesz opcjonalnie [udzielić dostępu przy użyciu wpisów tajnych Kubernetes](#access-with-kubernetes-secret).

W tym artykule przyjęto założenie, że klaster AKS został już utworzony i można uzyskać dostęp do klastra za pomocą klienta wiersza polecenia `kubectl`. Jeśli zamiast tego chcesz utworzyć klaster i skonfigurować dostęp do rejestru kontenerów podczas tworzenia klastra, zobacz [Samouczek: Wdrażanie klastra AKS](../aks/tutorial-kubernetes-deploy-cluster.md) lub [uwierzytelnianie przy użyciu Azure Container Registry z usługi Azure Kubernetes](../aks/cluster-container-registry-integration.md).

## <a name="grant-aks-access-to-acr"></a>Przyznaj AKS dostęp do ACR

Podczas tworzenia klastra AKS, platforma Azure tworzy również jednostkę usługi, aby zapewnić obsługę gotowości klastra z innymi zasobami platformy Azure. Tej automatycznie generowanej jednostki usługi można użyć do uwierzytelniania za pomocą rejestru ACR. W tym celu należy utworzyć [przypisanie roli](../role-based-access-control/overview.md#role-assignments) usługi Azure AD, które przyznaje głównej nazwy klastra dostępu do rejestru kontenerów.

Użyj poniższego skryptu, aby przyznać jednostce usługi wygenerowanej przez AKS dostęp ściągający do rejestru kontenerów platformy Azure. Przed uruchomieniem skryptu zmodyfikuj zmienne `AKS_*` i `ACR_*` dla środowiska.

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
az role assignment create --assignee $CLIENT_ID --role acrpull --scope $ACR_ID
```

## <a name="access-with-kubernetes-secret"></a>Dostęp za pomocą klucza tajnego Kubernetes

W niektórych przypadkach może być niemożliwe przypisanie wymaganej roli do automatycznie generowanej nazwy głównej usługi AKS udzielenie dostępu do ACR. Na przykład ze względu na model zabezpieczeń organizacji, możesz nie mieć wystarczających uprawnień w dzierżawie Azure Active Directory, aby przypisać rolę do jednostki usługi wygenerowanej przez AKS. Przypisanie roli do jednostki usługi wymaga, aby konto usługi Azure AD miało uprawnienie do zapisu w dzierżawie usługi Azure AD. Jeśli nie masz uprawnień, możesz utworzyć nową nazwę główną usługi, a następnie udzielić jej dostępu do rejestru kontenerów przy użyciu klucza tajnego Kubernetes obrazu.

Użyj poniższego skryptu, aby utworzyć nową nazwę główną usługi (użyjesz swoich poświadczeń dla klucza tajnego ściągania obrazu Kubernetes). Przed uruchomieniem skryptu zmodyfikuj zmienną `ACR_NAME` dla środowiska.

```bash
#!/bin/bash

ACR_NAME=myacrinstance
SERVICE_PRINCIPAL_NAME=acr-service-principal

# Populate the ACR login server and resource id.
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --query loginServer --output tsv)
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)

# Create acrpull role assignment with a scope of the ACR resource.
SP_PASSWD=$(az ad sp create-for-rbac --name http://$SERVICE_PRINCIPAL_NAME --role acrpull --scopes $ACR_REGISTRY_ID --query password --output tsv)

# Get the service principal client id.
CLIENT_ID=$(az ad sp show --id http://$SERVICE_PRINCIPAL_NAME --query appId --output tsv)

# Output used when creating Kubernetes secret.
echo "Service principal ID: $CLIENT_ID"
echo "Service principal password: $SP_PASSWD"
```

Teraz można przechowywać poświadczenia jednostki usługi w [kluczu tajnym ściągania obrazu][image-pull-secret]Kubernetes, którego klaster AKS będzie odwoływać się w przypadku uruchamiania kontenerów.

Aby utworzyć wpis tajny Kubernetes, użyj następującego polecenia **polecenia kubectl** . Zastąp `<acr-login-server>` z w pełni kwalifikowaną nazwą usługi Azure Container Registry (w formacie "acrname.azurecr.io"). Zastąp `<service-principal-ID>` i `<service-principal-password>` wartościami uzyskanymi przez uruchomienie poprzedniego skryptu. Zastąp `<email-address>` własnym poprawnie sformułowanym adresem e-mail.

```bash
kubectl create secret docker-registry acr-auth --docker-server <acr-login-server> --docker-username <service-principal-ID> --docker-password <service-principal-password> --docker-email <email-address>
```

Możesz teraz użyć wpisu tajnego Kubernetes w ramach wdrożeń pod, określając jego nazwę (w tym przypadku "ACR-auth") w parametrze `imagePullSecrets`:

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

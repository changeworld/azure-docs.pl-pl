---
title: Ograniczanie dostępu do plik kubeconfig w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak kontrolować dostęp do pliku konfiguracji Kubernetes (plik kubeconfig) dla klastra, administratorów i użytkowników klastra
services: container-service
author: rockboyfor
ms.service: container-service
ms.topic: article
origin.date: 01/03/2019
ms.date: 03/04/2019
ms.author: v-yeche
ms.openlocfilehash: 141aacc71d129bb45dc53774af876d5b07b7fc86
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60466462"
---
# <a name="use-azure-role-based-access-controls-to-define-access-to-the-kubernetes-configuration-file-in-azure-kubernetes-service-aks"></a>Użyj kontroli dostępu opartej na rolach na platformie Azure, aby definiują dostęp użytkownika do pliku konfiguracji platformy Kubernetes w usłudze Azure Kubernetes Service (AKS)

Możesz wchodzić w interakcje z klastrami usługi Kubernetes przy użyciu `kubectl` narzędzia. Wiersza polecenia platformy Azure zapewnia prosty sposób uzyskania dostępu do poświadczeń i informacji o konfiguracji, aby nawiązać połączenie usługi AKS klastrów przy użyciu `kubectl`. Do limitu, który można uzyskać tę konfigurację rozwiązania Kubernetes (*plik kubeconfig*) informacji i aby ograniczyć następnie mają uprawnienia, można użyć kontroli dostępu opartej na rolach na platformie Azure (RBAC).

W tym artykule pokazano, jak przypisać role RBAC, ten limit, który można pobrać informacji o konfiguracji dla klastra usługi AKS.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule założono, że masz istniejący klaster usługi AKS. Jeśli potrzebujesz klastra AKS, zobacz Przewodnik Szybki Start usługi AKS [przy użyciu wiersza polecenia platformy Azure] [ aks-quickstart-cli] lub [przy użyciu witryny Azure portal][aks-quickstart-portal].

W tym artykule wymaga również, czy korzystasz z wiersza polecenia platformy Azure w wersji 2.0.53 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

## <a name="available-cluster-roles-permissions"></a>Uprawnienia ról klastra dostępny

Kiedy wchodzisz w interakcję z klastra usługi AKS przy użyciu `kubectl` służy narzędzie i plik konfiguracji, który definiuje informacje o połączeniu klastra. Ten plik konfiguracji są zwykle przechowywane w *~/.kube/config*. Można zdefiniować wiele klastrów w tym *plik kubeconfig* pliku. Możesz przełączać się między klastrami przy użyciu [kontekst Użyj konfiguracji narzędzia kubectl] [ kubectl-config-use-context] polecenia.

[Az aks get-credentials] [ az-aks-get-credentials] polecenie pozwala uzyskać poświadczenia dostępu do klastra usługi AKS i scala je do *plik kubeconfig* pliku. Kontroli dostępu opartej na rolach na platformie Azure (RBAC) umożliwia kontrolowanie dostępu do tych poświadczeń. Te role RBAC platformy Azure pozwalają zdefiniować, kto może pobrać *plik kubeconfig* plików i co ich uprawnień następnie w klastrze.

Dostępne są następujące dwie wbudowane role:

* **Rola administratora klastra Azure Kubernetes Service**  
    * Zezwala na dostęp do *Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action* wywołania interfejsu API. To wywołanie interfejsu API [Wyświetla poświadczenia administratora klastra][api-cluster-admin].
    * Pliki do pobrania *plik kubeconfig* dla *clusterAdmin* roli.
* **Rola użytkownika klastra Azure Kubernetes Service**
    * Zezwala na dostęp do *Microsoft.ContainerService/managedClusters/listClusterUserCredential/action* wywołania interfejsu API. To wywołanie interfejsu API [Wyświetla poświadczeń użytkownika klastra][api-cluster-user].
    * Pliki do pobrania *plik kubeconfig* dla *clusterUser* roli.

## <a name="assign-role-permissions-to-a-user"></a>Przypisywanie uprawnień roli do użytkownika

Aby przypisać jedną z ról platformy Azure dla użytkownika, należy uzyskać identyfikator zasobu klastra usługi AKS i identyfikator konta użytkownika. Następujące przykładowe polecenia wykonaj następujące czynności:

* Pobiera, używając Identyfikatora zasobu klastra [az aks show] [ az-aks-show] polecenie, aby uzyskać klaster o nazwie *myAKSCluster* w *myResourceGroup* Grupa zasobów. Podaj własną nazwę klastra i zasobów grupy, zgodnie z potrzebami.
* Używa [Pokaż konta az] [ az-account-show] i [az ad użytkownika show] [ az-ad-user-show] polecenia Pobierz identyfikator użytkownika.
* Na koniec przypisuje rolę przy użyciu [utworzenia przypisania roli az] [ az-role-assignment-create] polecenia.

Poniższy przykład przypisuje *rolę administratora klastra usługi Kubernetes usługi Azure*:

```azurecli
# Get the resource ID of your AKS cluster
AKS_CLUSTER=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query id -o tsv)

# Get the account credentials for the logged in user
ACCOUNT_UPN=$(az account show --query user.name -o tsv)
ACCOUNT_ID=$(az ad user show --upn-or-object-id $ACCOUNT_UPN --query objectId -o tsv)

# Assign the 'Cluster Admin' role to the user
az role assignment create \
    --assignee $ACCOUNT_ID \
    --scope $AKS_CLUSTER \
    --role "Azure Kubernetes Service Cluster Admin Role"
```

Możesz zmienić poprzednie przypisanie do *roli użytkownika klastra* zgodnie z potrzebami.

Następujące przykładowe dane wyjściowe pokazuje, czy przydział roli został utworzony pomyślnie:

```
{
  "canDelegate": null,
  "id": "/subscriptions/<guid>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/providers/Microsoft.Authorization/roleAssignments/b2712174-5a41-4ecb-82c5-12b8ad43d4fb",
  "name": "b2712174-5a41-4ecb-82c5-12b8ad43d4fb",
  "principalId": "946016dd-9362-4183-b17d-4c416d1f8f61",
  "resourceGroup": "myResourceGroup",
  "roleDefinitionId": "/subscriptions/<guid>/providers/Microsoft.Authorization/roleDefinitions/0ab01a8-8aac-4efd-b8c2-3ee1fb270be8",
  "scope": "/subscriptions/<guid>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="get-and-verify-the-configuration-information"></a>Pobierz i Zweryfikuj informacje o konfiguracji

Za pomocą przypisane role RBAC, użyj [az aks get-credentials] [ az-aks-get-credentials] polecenie, aby uzyskać *plik kubeconfig* definicji dla klastra usługi AKS. Poniższy przykład pobiera *— administrator* poświadczenia, których działać poprawnie, jeśli użytkownikowi udzielono *rolę administratora klastra*:

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Następnie można użyć [kubectl config widoku] [ kubectl-config-view] polecenie, aby sprawdzić, czy *kontekstu* dla klastra pokazuje, czy informacje o konfiguracji administratora zostały zastosowane:

```
$ kubectl config view

apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://myaksclust-myresourcegroup-19da35-4839be06.hcp.chinaeast.azmk8s.io:443
  name: myAKSCluster
contexts:
- context:
    cluster: myAKSCluster
    user: clusterAdmin_myResourceGroup_myAKSCluster
  name: myAKSCluster-admin
current-context: myAKSCluster-admin
kind: Config
preferences: {}
users:
- name: clusterAdmin_myResourceGroup_myAKSCluster
  user:
    client-certificate-data: REDACTED
    client-key-data: REDACTED
    token: e9f2f819a4496538b02cefff94e61d35
```

## <a name="remove-role-permissions"></a>Usuwanie uprawnień roli

Aby usunąć przypisania roli, należy użyć [Usuń przypisanie roli az] [ az-role-assignment-delete] polecenia. Określ identyfikator konta i identyfikator zasobu klastra, uzyskanych w poprzednich poleceń:

```azurecli
az role assignment delete --assignee $ACCOUNT_ID --scope $AKS_CLUSTER
```

## <a name="next-steps"></a>Kolejne kroki

Aby zwiększyć bezpieczeństwo dostępu do klastrów usługi AKS [integrowanie uwierzytelniania usługi Azure Active Directory][aad-integration].

<!-- LINKS - external -->
[kubectl-config-use-context]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#config
[kubectl-config-view]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#config

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[azure-cli-install]: https://docs.azure.cn/zh-cn/cli/install-azure-cli?view=azure-cli-latest
[az-aks-get-credentials]: https://docs.azure.cn/zh-cn/cli/aks?view=azure-cli-latest#az-aks-get-credentials
[azure-rbac]: ../role-based-access-control/overview.md
[api-cluster-admin]: https://docs.microsoft.com/rest/api/aks/managedclusters/listclusteradmincredentials
[api-cluster-user]: https://docs.microsoft.com/rest/api/aks/managedclusters/listclusterusercredentials
[az-aks-show]: https://docs.azure.cn/zh-cn/cli/aks?view=azure-cli-latest#az-aks-show
[az-account-show]: https://docs.azure.cn/zh-cn/cli/account?view=azure-cli-latest#az-account-show
[az-ad-user-show]: https://docs.azure.cn/zh-cn/cli/ad/user?view=azure-cli-latest#az-ad-user-show
[az-role-assignment-create]: https://docs.azure.cn/zh-cn/cli/role/assignment?view=azure-cli-latest#az-role-assignment-create
[az-role-assignment-delete]: https://docs.azure.cn/zh-cn/cli/role/assignment?view=azure-cli-latest#az-role-assignment-delete
[aad-integration]: aad-integration.md
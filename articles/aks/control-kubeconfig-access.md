---
title: Ograniczanie dostępu do plik kubeconfig w usłudze Azure Kubernetes Service (AKS)
description: Dowiedz się, jak kontrolować dostęp do pliku konfiguracji Kubernetes (plik kubeconfig) dla klastra, administratorów i użytkowników klastra
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: iainfou
ms.openlocfilehash: b55cc226cfbb462cdccd73b3b80cfb0d56c10711
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475611"
---
# <a name="use-azure-role-based-access-controls-to-define-access-to-the-kubernetes-configuration-file-in-azure-kubernetes-service-aks"></a>Użyj kontroli dostępu opartej na rolach na platformie Azure, aby definiują dostęp użytkownika do pliku konfiguracji platformy Kubernetes w usłudze Azure Kubernetes Service (AKS)

Możesz wchodzić w interakcje z klastrami usługi Kubernetes przy użyciu `kubectl` narzędzia. Wiersza polecenia platformy Azure zapewnia prosty sposób uzyskania dostępu do poświadczeń i informacji o konfiguracji, aby nawiązać połączenie usługi AKS klastrów przy użyciu `kubectl`. Do limitu, który można uzyskać tę konfigurację rozwiązania Kubernetes (*plik kubeconfig*) informacji i aby ograniczyć następnie mają uprawnienia, można użyć kontroli dostępu opartej na rolach na platformie Azure (RBAC).

W tym artykule pokazano, jak przypisać role RBAC, ten limit, który można pobrać informacji o konfiguracji dla klastra usługi AKS.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule założono, że masz istniejący klaster usługi AKS. Jeśli potrzebujesz klastra AKS, zobacz Przewodnik Szybki Start usługi AKS [przy użyciu wiersza polecenia platformy Azure] [ aks-quickstart-cli] lub [przy użyciu witryny Azure portal][aks-quickstart-portal].

W tym artykule wymaga również, czy korzystasz z wiersza polecenia platformy Azure w wersji 2.0.65 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

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

Te role RBAC można zastosować do usługi Azure Active Directory (AD) użytkownika lub grupy.

## <a name="assign-role-permissions-to-a-user-or-group"></a>Przypisywanie uprawnień roli do użytkownika lub grupy

Aby przypisać jedną z dostępnych ról, należy uzyskać identyfikator zasobu klastra usługi AKS i identyfikator konta użytkownika usługi Azure AD lub grupy. Następujące przykładowe polecenia:

* Pobierz, używając Identyfikatora zasobu klastra [az aks show] [ az-aks-show] polecenie, aby uzyskać klaster o nazwie *myAKSCluster* w *myResourceGroup* Grupa zasobów. Podaj własną nazwę klastra i zasobów grupy, zgodnie z potrzebami.
* Używa [Pokaż konta az] [ az-account-show] i [az ad użytkownika show] [ az-ad-user-show] poleceń, aby uzyskać identyfikator użytkownika.
* Na koniec przypisuje rolę przy użyciu [utworzenia przypisania roli az] [ az-role-assignment-create] polecenia.

Poniższy przykład przypisuje *rolę administratora klastra usługi Kubernetes usługi Azure* do konta użytkownika:

```azurecli-interactive
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

> [!TIP]
> Jeśli chcesz przypisać uprawnienia do grupy usługi Azure AD, należy zaktualizować `--assignee` pokazano w poprzednim przykładzie o identyfikatorze obiektu dla parametru *grupy* zamiast *użytkownika*. Aby uzyskać identyfikator obiektu dla grupy, użyj [Pokaż grupy ad az] [ az-ad-group-show] polecenia. Poniższy przykład pobiera identyfikator obiektu grupy usługi Azure AD o nazwie *appdev*: `az ad group show --group appdev --query objectId -o tsv`

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

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Następnie można użyć [kubectl config widoku] [ kubectl-config-view] polecenie, aby sprawdzić, czy *kontekstu* dla klastra pokazuje, czy informacje o konfiguracji administratora zostały zastosowane:

```
$ kubectl config view

apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://myaksclust-myresourcegroup-19da35-4839be06.hcp.eastus.azmk8s.io:443
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

Aby usunąć przypisania roli, należy użyć [Usuń przypisanie roli az] [ az-role-assignment-delete] polecenia. Określ identyfikator konta i identyfikator zasobu klastra, uzyskanych w poprzednich poleceniach. Jeśli rola jest przypisany do określonej grupy, a nie przez użytkownika, należy określić obiekt odpowiednią grupę, ID, a nie konta identyfikator obiektu dla `--assignee` parametru:

```azurecli-interactive
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
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-rbac]: ../role-based-access-control/overview.md
[api-cluster-admin]: /rest/api/aks/managedclusters/listclusteradmincredentials
[api-cluster-user]: /rest/api/aks/managedclusters/listclusterusercredentials
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-account-show]: /cli/azure/account#az-account-show
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-role-assignment-delete]: /cli/azure/role/assignment#az-role-assignment-delete
[aad-integration]: azure-ad-integration.md
[az-ad-group-show]: /cli/azure/ad/group#az-ad-group-show

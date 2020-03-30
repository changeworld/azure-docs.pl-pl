---
title: Ogranicz dostęp do kubeconfig w usłudze Azure Kubernetes (AKS)
description: Dowiedz się, jak kontrolować dostęp do pliku konfiguracji kubernetes (kubeconfig) dla administratorów klastrów i użytkowników klastra
services: container-service
ms.topic: article
ms.date: 01/28/2020
ms.openlocfilehash: 25c710cce2855d6af985d3f46082f47573bbc101
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259554"
---
# <a name="use-azure-role-based-access-controls-to-define-access-to-the-kubernetes-configuration-file-in-azure-kubernetes-service-aks"></a>Użyj kontroli dostępu opartego na rolach platformy Azure, aby zdefiniować dostęp do pliku konfiguracji usługi Kubernetes w usłudze Azure Kubernetes (AKS)

Za pomocą `kubectl` narzędzia można wchodzić w interakcje z klastrami kubernetes. Narzędzie koneuszny platformy Azure umożliwia łatwe uzyskiwanie poświadczeń dostępu `kubectl`i informacji o konfiguracji w celu nawiązania połączenia z klastrami AKS przy użyciu programu . Aby ograniczyć, kto może uzyskać te informacje o konfiguracji kubernetes *(kubeconfig)* i ograniczyć uprawnienia, które następnie mają, można użyć kontroli dostępu opartego na rolach platformy Azure (RBAC).

W tym artykule pokazano, jak przypisać role RBAC, które ograniczają, kto może uzyskać informacje o konfiguracji dla klastra AKS.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule przyjęto założenie, że masz istniejący klaster AKS. Jeśli potrzebujesz klastra AKS, zobacz szybki start usługi AKS [przy użyciu interfejsu wiersza polecenia platformy Azure][aks-quickstart-cli] lub za pomocą portalu [Azure.][aks-quickstart-portal]

W tym artykule wymaga również, że są uruchomione interfejsu wiersza polecenia platformy Azure w wersji 2.0.65 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

## <a name="available-cluster-roles-permissions"></a>Dostępne uprawnienia ról klastra

Podczas interakcji z klastrem AKS za pomocą `kubectl` narzędzia używany jest plik konfiguracyjny definiujący informacje o połączeniu klastra. Ten plik konfiguracyjny jest zazwyczaj przechowywany w *pliku ~/.kube/config*. W tym pliku *kubeconfig* można zdefiniować wiele klastrów. Przełączasz się między klastrami za pomocą polecenia [kubectl config use-context.][kubectl-config-use-context]

Polecenie [az aks get-credentials][az-aks-get-credentials] umożliwia uzyskanie poświadczeń dostępu dla klastra AKS i scalanie ich z plikiem *kubeconfig.* Aby kontrolować dostęp do tych poświadczeń, można użyć formantów dostępu opartych na rolach platformy Azure (RBAC). Te role rbac platformy Azure umożliwiają zdefiniowanie, kto może pobrać plik *kubeconfig* i jakie uprawnienia mają następnie w klastrze.

Dwie wbudowane role to:

* **Rola administratora klastra usługi Azure Kubernetes**  
  * Umożliwia dostęp do wywołania interfejsu API *Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action.* To wywołanie interfejsu API [zawiera listę poświadczeń administratora klastra][api-cluster-admin].
  * Pliki do pobrania *kubeconfig* dla *roli clusterAdmin.*
* **Rola użytkownika klastra usług Azure Kubernetes**
  * Umożliwia dostęp do *microsoft.ContainerService/managedClusters/listClusterUserCredential/action* WYWOŁANIE INTERFEJSU API. To wywołanie interfejsu API [zawiera listę poświadczeń użytkownika klastra][api-cluster-user].
  * Pliki do pobrania *kubeconfig* dla *roli clusterUser.*

Te role RBAC można zastosować do użytkownika lub grupy usługi Azure Active Directory (AD).

> ! [UWAGA] W klastrach korzystających z usługi Azure AD użytkownicy z rolą *clusterUser* mają pusty plik *kubeconfig,* który monituje o zalogowanie. Po zalogowaniu użytkownicy mają dostęp na podstawie ustawień użytkownika lub grupy usługi Azure AD. Użytkownicy z rolą *clusterAdmin* mają dostęp administratora.
>
> Klastry, które nie używają usługi Azure AD, używają tylko roli *clusterAdmin.*

## <a name="assign-role-permissions-to-a-user-or-group"></a>Przypisywanie uprawnień roli do użytkownika lub grupy

Aby przypisać jedną z dostępnych ról, musisz uzyskać identyfikator zasobu klastra AKS i identyfikator konta użytkownika lub grupy usługi Azure AD. Następujące przykładowe polecenia:

* Pobierz identyfikator zasobu klastra za pomocą polecenia [az aks show][az-aks-show] dla klastra o nazwie *myAKSCluster* w grupie zasobów *myResourceGroup.* W razie potrzeby podaj własną nazwę klastra i grupy zasobów.
* Użyj polecenia [az account show][az-account-show] i [az ad user show,][az-ad-user-show] aby uzyskać identyfikator użytkownika.
* Na koniec przypisz rolę za pomocą [polecenia tworzenia przypisania roli az.][az-role-assignment-create]

Poniższy przykład przypisuje *rolę administratora klastra usługi Azure Kubernetes* do indywidualnego konta użytkownika:

```azurecli-interactive
# Get the resource ID of your AKS cluster
AKS_CLUSTER=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query id -o tsv)

# Get the account credentials for the logged in user
ACCOUNT_UPN=$(az account show --query user.name -o tsv)
ACCOUNT_ID=$(az ad user show --id $ACCOUNT_UPN --query objectId -o tsv)

# Assign the 'Cluster Admin' role to the user
az role assignment create \
    --assignee $ACCOUNT_ID \
    --scope $AKS_CLUSTER \
    --role "Azure Kubernetes Service Cluster Admin Role"
```

> [!TIP]
> Jeśli chcesz przypisać uprawnienia do grupy usługi Azure `--assignee` AD, zaktualizuj parametr pokazany w poprzednim przykładzie o identyfikatorze obiektu dla *grupy,* a nie *dla użytkownika.* Aby uzyskać identyfikator obiektu dla grupy, użyj polecenia [az ad group show.][az-ad-group-show] W poniższym przykładzie pobiera identyfikator obiektu dla grupy usługi Azure AD o nazwie *appdev:*`az ad group show --group appdev --query objectId -o tsv`

W razie potrzeby można zmienić poprzednie przypisanie na *rolę użytkownika klastra.*

Poniższy przykładowy wynik pokazuje, że przypisanie roli zostało pomyślnie utworzone:

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

## <a name="get-and-verify-the-configuration-information"></a>Pobierz i zweryfikuj informacje o konfiguracji

Przy przypisanych rolach RBAC użyj polecenia [az aks get-credentials,][az-aks-get-credentials] aby uzyskać definicję *kubeconfig* dla klastra AKS. Poniższy przykład pobiera *poświadczenia --admin,* które działają poprawnie, jeśli użytkownikowi przyznano *rolę administratora klastra:*

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Następnie można użyć polecenia [widoku konfiguracji kubectl,][kubectl-config-view] aby sprawdzić, czy *kontekst* klastra pokazuje, że informacje o konfiguracji administratora zostały zastosowane:

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

Aby usunąć przypisania ról, użyj polecenia [usuń przypisanie roli az.][az-role-assignment-delete] Określ identyfikator konta i identyfikator zasobu klastra, zgodnie z poprzednimi poleceniami. Jeśli przypisano rolę do grupy, a nie do użytkownika, określ odpowiedni identyfikator obiektu `--assignee` grupy, a nie identyfikator obiektu konta dla tego parametru:

```azurecli-interactive
az role assignment delete --assignee $ACCOUNT_ID --scope $AKS_CLUSTER
```

## <a name="next-steps"></a>Następne kroki

Aby zwiększyć bezpieczeństwo dostępu do klastrów AKS, [zintegruj uwierzytelnianie usługi Azure Active Directory][aad-integration].

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

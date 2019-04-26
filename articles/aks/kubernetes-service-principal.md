---
title: Jednostki usługi dla usługi Azure Kubernetes Service (AKS)
description: Tworzenie jednostki usługi Azure Active Directory dla klastra w usłudze Azure Kubernetes Service (AKS) i zarządzanie nią
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: iainfou
ms.openlocfilehash: dc2e2f010de3dfe265cddbbaa6c050d081bd05dc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60464940"
---
# <a name="service-principals-with-azure-kubernetes-service-aks"></a>Jednostki usługi w usłudze Azure Kubernetes Service (AKS)

Do współpracy z interfejsami API platformy Azure klaster usługi AKS wymaga [jednostki usługi Azure Active Directory][aad-service-principal]. Jednostka usługi jest potrzebna do dynamicznego tworzenia innych zasobów platformy Azure, takich jak usługa Azure Load Balancer lub usługa Azure Container Registry i zarządzania nimi.

W tym artykule przedstawiono sposób tworzenia jednostki usługi dla klastra usługi AKS i zarządzania nią.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby utworzyć jednostkę usługi Azure AD, musisz mieć uprawnienia do zarejestrowania aplikacji w swojej dzierżawie usługi Azure AD i przypisania aplikacji do roli w swojej subskrypcji. Jeśli nie masz niezbędnych uprawnień, może być konieczne zwrócenie się z prośbą do administratora usługi Azure AD lub subskrypcji o przyznanie niezbędnych uprawnień lub wstępne utworzenie jednostki usługi do użycia z klastrem usługi AKS.

Możesz również muszą wiersza polecenia platformy Azure w wersji 2.0.59 lub później zainstalowane i skonfigurowane. Uruchom polecenie  `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie przeprowadzenie instalacji lub uaktualnienia, zobacz  [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="automatically-create-and-use-a-service-principal"></a>Automatyczne tworzenie i używanie jednostki usługi

Podczas tworzenia klastra usługi AKS w witrynie Azure Portal lub przy użyciu polecenia [az aks create][az-aks-create] platforma Azure może automatycznie generować jednostkę usługi.

W poniższym przykładzie dotyczącym interfejsu wiersza polecenia platformy Azure nie została określona jednostka usługi. W tym scenariuszu interfejs wiersza polecenia platformy Azure tworzy jednostkę usługi dla klastra usługi AKS. Aby można było pomyślnie ukończyć tę operację, Twoje konto platformy Azure musi mieć odpowiednie uprawnienia do tworzenia jednostki usługi.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup
```

## <a name="manually-create-a-service-principal"></a>Ręczne tworzenie jednostki usługi

Aby ręcznie utworzyć jednostkę usługi przy użyciu interfejsu wiersza polecenia platformy Azure, użyj polecenia [az ad sp create-for-rbac][az-ad-sp-create]. W poniższym przykładzie parametr `--skip-assignment` zapobiega przypisaniu jakichkolwiek dodatkowych przypisań:

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

Dane wyjściowe będą podobne do poniższego przykładu. Zanotuj własne wartości `appId` i `password`. Te wartości będą używane podczas tworzenia klastra usługi AKS w następnej sekcji.

```json
{
  "appId": "559513bd-0c19-4c1a-87cd-851a26afd5fc",
  "displayName": "azure-cli-2019-03-04-21-35-28",
  "name": "http://azure-cli-2019-03-04-21-35-28",
  "password": "e763725a-5eee-40e8-a466-dc88d980f415",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

## <a name="specify-a-service-principal-for-an-aks-cluster"></a>Określanie jednostki usługi dla klastra usługi AKS

Aby użyć istniejącej jednostki usługi podczas tworzenia klastra usługi AKS za pomocą polecenia [az aks create][az-aks-create], użyj parametrów `--service-principal` i `--client-secret` w celu określenia właściwości `appId` i `password` z danych wyjściowych polecenia [az ad sp create-for-rbac][az-ad-sp-create]:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --service-principal <appId> \
    --client-secret <password>
```

W przypadku wdrażania klastra usługi AKS przy użyciu witryny Azure Portal na stronie *Uwierzytelnianie* okna dialogowego **Tworzenie klastra Kubernetes**, wybierz opcję **Konfigurowanie jednostki usługi**. Wybierz pozycję **Użyj istniejącej**, a następnie określ następujące wartości:

- **Identyfikator klienta jednostki usługi** to Twój *appId*
- **Klucz tajny klienta jednostki usługi** to wartość *hasła*

![Obraz przedstawiający przechodzenie do aplikacji Azure Vote](media/kubernetes-service-principal/portal-configure-service-principal.png)

## <a name="delegate-access-to-other-azure-resources"></a>Delegowanie dostępu do innych zasobów platformy Azure

Nazwa główna usługi klastra AKS może służyć do dostępu do innych zasobów. Na przykład jeśli chcesz przeprowadzić wdrożenie klastra usługi AKS na istniejącą podsieć sieci wirtualnej platformy Azure lub łączenia do usługi Azure Container Registry (ACR), należy delegować dostęp do tych zasobów do jednostki usługi.

Aby delegować uprawnienia, utworzyć przypisania roli przy użyciu [utworzenia przypisania roli az] [ az-role-assignment-create] polecenia. Przypisz `appId` do określonego zakresu, takich jak grupy zasobów lub zasobu sieci wirtualnej. Rola następnie definiuje uprawnienia, które jednostka usługi ma względem zasobu, jak pokazano w poniższym przykładzie:

```azurecli
az role assignment create --assignee <appId> --scope <resourceScope> --role Contributor
```

Opcja `--scope` dla zasobu musi być pełnym identyfikatorem zasobu, takim jak */subscriptions/\<identyfikator GUID\>/resourceGroups/myResourceGroup* lub */subscriptions/\<identyfikator GUID\>/resourceGroups/myResourceGroupVnet/providers/Microsoft.Network/virtualNetworks/myVnet*

W poniższych sekcjach opisano typowe delegacje, które należy wykonać.

### <a name="azure-container-registry"></a>Azure Container Registry

Jeśli używasz usługi Azure Container Registry (ACR) jako magazynu obrazów kontenerów, należy udzielić uprawnień dla klastra usługi AKS do odczytywania i ściągania obrazów. Dla jednostki usługi klastra AKS należy delegować rolę *Czytelnik* w rejestrze. Aby uzyskać szczegółowe instrukcje, zobacz [Grant AKS access to ACR][aks-to-acr] (Udzielanie klastrowi AKS uprawnień do usługi ACR).

### <a name="networking"></a>Networking

Możesz użyć zaawansowanych funkcji sieciowych, w przypadku których sieć wirtualna i podsieć lub publiczne adresy IP znajdują się w innej grupie zasobów. Przypisz jeden z następujących zestawów uprawnień ról:

- Utwórz [rolę niestandardową][rbac-custom-role] i zdefiniuj następujące uprawnienia roli:
  - *Microsoft.Network/virtualNetworks/subnets/join/action*
  - *Microsoft.Network/virtualNetworks/subnets/read*
  - *Microsoft.Network/publicIPAddresses/read*
  - *Microsoft.Network/publicIPAddresses/write*
  - *Microsoft.Network/publicIPAddresses/join/action*
- Innym rozwiązaniem jest przypisanie wbudowanej roli [Współautor sieci][rbac-network-contributor] do podsieci w sieci wirtualnej

### <a name="storage"></a>Magazyn

Konieczne może być uzyskanie dostępu do istniejących zasobów dysku w innej grupie zasobów. Przypisz jeden z następujących zestawów uprawnień ról:

- Utwórz [rolę niestandardową][rbac-custom-role] i zdefiniuj następujące uprawnienia roli:
  - *Microsoft.Compute/disks/read*
  - *Microsoft.Compute/disks/write*
- Innym rozwiązaniem jest przypisanie wbudowanej roli [Współautor konta magazynu][rbac-storage-contributor] do grupy zasobów

### <a name="azure-container-instances"></a>Azure Container Instances

Jeśli do integracji z usługą AKS używasz rozwiązania Virtual Kubelet i decydujesz się na uruchamianie usługi Azure Container Instances (ACI) w grupie zasobów niezależnie od klastra AKS, jednostka usługi AKS musi otrzymać uprawnienia *Współautor* w grupie zasobów usługi ACI.

## <a name="additional-considerations"></a>Dodatkowe zagadnienia

Podczas korzystania z jednostek usług AKS i Azure AD należy pamiętać o następujących kwestiach.

- Jednostka usługi dla rozwiązania Kubernetes jest częścią konfiguracji klastra. Nie należy jednak używać tożsamości do wdrażania klastra.
- Domyślnie poświadczenia nazwy głównej usługi są ważne przez jeden rok. Możesz [aktualizacji lub obrócić poświadczenia nazwy głównej usługi] [ update-credentials] w dowolnym momencie.
- Każda jednostka usługi jest skojarzona z aplikacją usługi Azure AD. Jednostka usługi dla klastra Kubernetes może zostać skojarzona z dowolną prawidłową nazwą aplikacji usługi Azure AD (na przykład *https://www.contoso.org/example*). Adres URL dla aplikacji nie musi być rzeczywistym punktem końcowym.
- Podczas określania **identyfikatora klienta** jednostki usługi użyj wartości `appId`.
- Na głównej maszynie wirtualnej i maszynach wirtualnych węzłów w klastrze Kubernetes poświadczenia jednostki usługi są przechowywane w pliku `/etc/kubernetes/azure.json`
- Gdy używasz polecenia [az aks create][az-aks-create], aby automatycznie wygenerować jednostkę usługi, poświadczenia jednostki usługi są zapisywane w pliku `~/.azure/aksServicePrincipal.json` na maszynie użytej do uruchomienia polecenia.
- Usunięcie klastra AKS utworzonego za pomocą polecenia [az aks create][az-aks-create] nie powoduje usunięcia utworzonej automatycznie jednostki usługi.
    - Aby usunąć jednostkę usługi, utwórz zapytanie dotyczące klastra *servicePrincipalProfile.clientId*, a następnie usuń przy użyciu polecenia [az ad app delete][az-ad-app-delete]. Zastąp następujące nazwy klastra i grupy zasobów własnymi wartościami:

        ```azurecli
        az ad sp delete --id $(az aks show -g myResourceGroup -n myAKSCluster --query servicePrincipalProfile.clientId -o tsv)
        ```

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat nazw głównych usług Azure Active Directory, zobacz [aplikacji i obiektów nazw głównych usług][service-principal].

Aby uzyskać informacje o sposobie aktualizowania poświadczeń, zobacz [aktualizacji lub zamiana poświadczeń dla jednostki usługi w usłudze AKS][update-credentials].

<!-- LINKS - internal -->
[aad-service-principal]:../active-directory/develop/app-objects-and-service-principals.md
[acr-intro]: ../container-registry/container-registry-intro.md
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[azure-load-balancer-overview]: ../load-balancer/load-balancer-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[service-principal]:../active-directory/develop/app-objects-and-service-principals.md
[user-defined-routes]: ../load-balancer/load-balancer-overview.md
[az-ad-app-list]: /cli/azure/ad/app#az-ad-app-list
[az-ad-app-delete]: /cli/azure/ad/app#az-ad-app-delete
[az-aks-create]: /cli/azure/aks#az-aks-create
[rbac-network-contributor]: ../role-based-access-control/built-in-roles.md#network-contributor
[rbac-custom-role]: ../role-based-access-control/custom-roles.md
[rbac-storage-contributor]: ../role-based-access-control/built-in-roles.md#storage-account-contributor
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[aks-to-acr]: ../container-registry/container-registry-auth-aks.md?toc=%2fazure%2faks%2ftoc.json#grant-aks-access-to-acr
[update-credentials]: update-credentials.md

---
title: Jednostki usługi dla usługi Azure Kubernetes Service (AKS)
description: Tworzenie jednostki usługi Azure Active Directory dla klastra w usłudze Azure Kubernetes Service (AKS) i zarządzanie nią
services: container-service
ms.topic: conceptual
ms.date: 04/25/2019
ms.openlocfilehash: 523f08ddbf22e175af5b0604b04d4a2460ffd634
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79259424"
---
# <a name="service-principals-with-azure-kubernetes-service-aks"></a>Jednostki usługi w usłudze Azure Kubernetes Service (AKS)

Aby można było korzystać z interfejsów API platformy Azure, klaster AKS wymaga jednostki [usługi Azure Active Directory (AD)][aad-service-principal]. Jednostka usługi jest potrzebna do dynamicznego tworzenia innych zasobów platformy Azure, takich jak usługa Azure Load Balancer lub usługa Azure Container Registry i zarządzania nimi.

W tym artykule przedstawiono sposób tworzenia jednostki usługi dla klastra usługi AKS i zarządzania nią.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby utworzyć jednostkę usługi Azure AD, musisz mieć uprawnienia do zarejestrowania aplikacji w swojej dzierżawie usługi Azure AD i przypisania aplikacji do roli w swojej subskrypcji. Jeśli nie masz niezbędnych uprawnień, może być konieczne zwrócenie się z prośbą do administratora usługi Azure AD lub subskrypcji o przyznanie niezbędnych uprawnień lub wstępne utworzenie jednostki usługi do użycia z klastrem usługi AKS.

Jeśli używasz jednostki usługi z innej dzierżawy usługi Azure AD, istnieją dodatkowe zagadnienia dotyczące uprawnień dostępnych podczas wdrażania klastra. Licencjobiorca może nie mieć odpowiednich uprawnień do odczytu i zapisu informacji katalogowych. Aby uzyskać więcej informacji, zobacz [co to są domyślne uprawnienia użytkownika w Azure Active Directory?][azure-ad-permissions]

Konieczne jest również zainstalowanie i skonfigurowanie interfejsu wiersza polecenia platformy Azure w wersji 2.0.59 lub nowszej. Uruchom polecenie  `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne jest zainstalowanie lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="automatically-create-and-use-a-service-principal"></a>Automatyczne tworzenie i używanie jednostki usługi

Podczas tworzenia klastra AKS w Azure Portal lub przy użyciu polecenia [AZ AKS Create][az-aks-create] platforma Azure może automatycznie wygenerować jednostkę usługi.

W poniższym przykładzie dotyczącym interfejsu wiersza polecenia platformy Azure nie została określona jednostka usługi. W tym scenariuszu interfejs wiersza polecenia platformy Azure tworzy jednostkę usługi dla klastra usługi AKS. Aby można było pomyślnie ukończyć tę operację, Twoje konto platformy Azure musi mieć odpowiednie uprawnienia do tworzenia jednostki usługi.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup
```

## <a name="manually-create-a-service-principal"></a>Ręczne tworzenie jednostki usługi

Aby ręcznie utworzyć jednostkę usługi za pomocą interfejsu wiersza polecenia platformy Azure, użyj polecenia [AZ AD Sp Create-for-RBAC][az-ad-sp-create] . W poniższym przykładzie parametr `--skip-assignment` zapobiega przypisaniu jakichkolwiek dodatkowych przypisań:

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment --name myAKSClusterServicePrincipal
```

Dane wyjściowe będą podobne do poniższego przykładu. Zanotuj własne wartości `appId` i `password`. Te wartości będą używane podczas tworzenia klastra usługi AKS w następnej sekcji.

```json
{
  "appId": "559513bd-0c19-4c1a-87cd-851a26afd5fc",
  "displayName": "myAKSClusterServicePrincipal",
  "name": "http://myAKSClusterServicePrincipal",
  "password": "e763725a-5eee-40e8-a466-dc88d980f415",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

## <a name="specify-a-service-principal-for-an-aks-cluster"></a>Określanie jednostki usługi dla klastra usługi AKS

Aby użyć istniejącej nazwy głównej usługi podczas tworzenia klastra AKS za pomocą polecenia [AZ AKS Create][az-aks-create] , użyj parametrów `--service-principal` i `--client-secret`, aby określić `appId` i `password` z danych wyjściowych polecenia [AZ AD Sp Create-for-RBAC][az-ad-sp-create] :

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --service-principal <appId> \
    --client-secret <password>
```

> [!NOTE]
> Jeśli używasz istniejącej jednostki usługi z dostosowanym kluczem tajnym, upewnij się, że wpis tajny nie jest dłuższy niż 190 bajtów.

W przypadku wdrażania klastra usługi AKS przy użyciu witryny Azure Portal na stronie *Uwierzytelnianie* okna dialogowego **Tworzenie klastra Kubernetes**, wybierz opcję **Konfigurowanie jednostki usługi**. Wybierz pozycję **Użyj istniejącej**, a następnie określ następujące wartości:

- **Identyfikator klienta jednostki usługi** to Twój *appId*
- **Klucz tajny klienta jednostki usługi** to wartość *hasła*

![Obraz przedstawiający przechodzenie do aplikacji Azure Vote](media/kubernetes-service-principal/portal-configure-service-principal.png)

## <a name="delegate-access-to-other-azure-resources"></a>Delegowanie dostępu do innych zasobów platformy Azure

Nazwa główna usługi klastra AKS może służyć do dostępu do innych zasobów. Jeśli na przykład chcesz wdrożyć klaster AKS w istniejącej podsieci sieci wirtualnej platformy Azure lub połączyć się z usługą Azure Container Registry (ACR), musisz delegować dostęp do tych zasobów do nazwy głównej usługi.

Aby delegować uprawnienia, Utwórz przypisanie roli za pomocą polecenia [AZ role przypisanie Create][az-role-assignment-create] . Przypisz `appId` do określonego zakresu, na przykład grupy zasobów lub zasobu sieci wirtualnej. Rola następnie definiuje uprawnienia, które jednostka usługi ma względem zasobu, jak pokazano w poniższym przykładzie:

```azurecli
az role assignment create --assignee <appId> --scope <resourceScope> --role Contributor
```

Opcja `--scope` dla zasobu musi być pełnym identyfikatorem zasobu, takim jak */subscriptions/\<identyfikator GUID\>/resourceGroups/myResourceGroup* lub */subscriptions/\<identyfikator GUID\>/resourceGroups/myResourceGroupVnet/providers/Microsoft.Network/virtualNetworks/myVnet*

W poniższych sekcjach opisano typowe delegacje, które należy wykonać.

### <a name="azure-container-registry"></a>Azure Container Registry

Jeśli używasz Azure Container Registry (ACR) jako magazynu obrazów kontenerów, musisz udzielić uprawnień do jednostki usługi dla klastra AKS w celu odczytywania i ściągania obrazów. Obecnie zalecaną konfiguracją jest użycie polecenia [AZ AKS Create][az-aks-create] lub [AZ AKS Update][az-aks-update] do integracji z rejestrem i przypisanie odpowiedniej roli dla jednostki usługi. Aby uzyskać szczegółowe instrukcje, zobacz [uwierzytelnianie za pomocą Azure Container Registry z usługi Azure Kubernetes][aks-to-acr].

### <a name="networking"></a>Sieć

Możesz użyć zaawansowanych funkcji sieciowych, w przypadku których sieć wirtualna i podsieć lub publiczne adresy IP znajdują się w innej grupie zasobów. Przypisz jeden z następujących zestawów uprawnień ról:

- Utwórz [rolę niestandardową][rbac-custom-role] i Zdefiniuj następujące uprawnienia roli:
  - *Microsoft.Network/virtualNetworks/subnets/join/action*
  - *Microsoft.Network/virtualNetworks/subnets/read*
  - *Microsoft. Network/virtualNetworks/podsieci/zapis*
  - *Microsoft.Network/publicIPAddresses/join/action*
  - *Microsoft.Network/publicIPAddresses/read*
  - *Microsoft.Network/publicIPAddresses/write*
- Lub Przypisz wbudowaną rolę [współautor sieci][rbac-network-contributor] w podsieci w sieci wirtualnej

### <a name="storage"></a>Storage

Konieczne może być uzyskanie dostępu do istniejących zasobów dysku w innej grupie zasobów. Przypisz jeden z następujących zestawów uprawnień ról:

- Utwórz [rolę niestandardową][rbac-custom-role] i Zdefiniuj następujące uprawnienia roli:
  - *Microsoft.Compute/disks/read*
  - *Microsoft.Compute/disks/write*
- Lub Przypisz wbudowaną rolę [współautor konta magazynu][rbac-storage-contributor] w grupie zasobów

### <a name="azure-container-instances"></a>Azure Container Instances

Jeśli do integracji z usługą AKS używasz rozwiązania Virtual Kubelet i decydujesz się na uruchamianie usługi Azure Container Instances (ACI) w grupie zasobów niezależnie od klastra AKS, jednostka usługi AKS musi otrzymać uprawnienia *Współautor* w grupie zasobów usługi ACI.

## <a name="additional-considerations"></a>Dodatkowe zagadnienia

Podczas korzystania z jednostek usług AKS i Azure AD należy pamiętać o następujących kwestiach.

- Jednostka usługi dla rozwiązania Kubernetes jest częścią konfiguracji klastra. Nie należy jednak używać tożsamości do wdrażania klastra.
- Domyślnie poświadczenia jednostki usługi są ważne przez jeden rok. [Poświadczenia nazwy głównej usługi można zaktualizować lub obrócić][update-credentials] w dowolnym momencie.
- Każda jednostka usługi jest skojarzona z aplikacją usługi Azure AD. Jednostka usługi dla klastra Kubernetes może zostać skojarzona z dowolną prawidłową nazwą aplikacji usługi Azure AD (na przykład *https://www.contoso.org/example* ). Adres URL dla aplikacji nie musi być rzeczywistym punktem końcowym.
- Podczas określania **identyfikatora klienta** jednostki usługi użyj wartości `appId`.
- Na maszynach wirtualnych węzłów agenta w klastrze Kubernetes poświadczenia jednostki usługi są przechowywane w pliku `/etc/kubernetes/azure.json`
- Po użyciu polecenia [AZ AKS Create][az-aks-create] w celu automatycznego wygenerowania jednostki usługi poświadczenia jednostki usługi są zapisywane w pliku `~/.azure/aksServicePrincipal.json` na komputerze używanym do uruchomienia polecenia.
- Jeśli nie przekażesz podmiotu usługi do dodatkowych poleceń interfejsu wiersza polecenia AKS, zostanie użyta domyślna nazwa główna usługi znajdująca się w `~/.azure/aksServicePrincipal.json`.  
- Opcjonalnie można również usunąć plik aksServicePrincipal. JSON, a AKS utworzy nową nazwę główną usługi.
- Po usunięciu klastra AKS, który został utworzony przez [AZ AKS Create][az-aks-create], nazwa główna usługi utworzona automatycznie nie jest usuwana.
    - Aby usunąć nazwę główną usługi, wykonaj zapytanie dotyczące klastra *servicePrincipalProfile. clientId* , a następnie usuń za pomocą [AZ AD App Delete][az-ad-app-delete]. Zastąp następujące nazwy klastra i grupy zasobów własnymi wartościami:

        ```azurecli
        az ad sp delete --id $(az aks show -g myResourceGroup -n myAKSCluster --query servicePrincipalProfile.clientId -o tsv)
        ```

## <a name="troubleshoot"></a>Rozwiązywanie problemów

Poświadczenia nazwy głównej usługi dla klastra AKS są buforowane przez interfejs wiersza polecenia platformy Azure. Jeśli te poświadczenia wygasną, wystąpią błędy podczas wdrażania klastrów AKS. Następujący komunikat o błędzie podczas uruchamiania [AZ AKS Create][az-aks-create] może wskazywać na problem z poświadczeniami jednostki usługi w pamięci podręcznej:

```console
Operation failed with status: 'Bad Request'.
Details: The credentials in ServicePrincipalProfile were invalid. Please see https://aka.ms/aks-sp-help for more details.
(Details: adal: Refresh request failed. Status Code = '401'.
```

Sprawdź wiek pliku poświadczeń przy użyciu następującego polecenia:

```console
ls -la $HOME/.azure/aksServicePrincipal.json
```

Domyślny czas wygaśnięcia poświadczeń jednostki usługi wynosi jeden rok. Jeśli plik *aksServicePrincipal. JSON* jest starszy niż rok, Usuń ten plik i spróbuj ponownie WDROŻYĆ klaster AKS.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat Azure Active Directory jednostek usługi, zobacz [obiekty główne aplikacji i usługi][service-principal].

Aby uzyskać informacje na temat aktualizowania poświadczeń, zobacz [Aktualizowanie lub obracanie poświadczeń dla jednostki usługi w AKS][update-credentials].

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
[az-aks-update]: /cli/azure/aks#az-aks-update
[rbac-network-contributor]: ../role-based-access-control/built-in-roles.md#network-contributor
[rbac-custom-role]: ../role-based-access-control/custom-roles.md
[rbac-storage-contributor]: ../role-based-access-control/built-in-roles.md#storage-account-contributor
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[aks-to-acr]: cluster-container-registry-integration.md
[update-credentials]: update-credentials.md
[azure-ad-permissions]: ../active-directory/fundamentals/users-default-permissions.md

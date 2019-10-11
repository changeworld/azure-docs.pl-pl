---
title: Nazwy główne usług dla usług Azure Kubernetes Services (AKS)
description: Tworzenie jednostki usługi Azure Active Directory i zarządzanie nią dla klastra w usłudze Azure Kubernetes Service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: mlearned
ms.openlocfilehash: e24d930ec82ea92a040efeed3056a10917ce2b2a
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2019
ms.locfileid: "72263907"
---
# <a name="service-principals-with-azure-kubernetes-service-aks"></a>Nazwy główne usług w usłudze Azure Kubernetes Service (AKS)

Aby można było korzystać z interfejsów API platformy Azure, klaster AKS wymaga jednostki [usługi Azure Active Directory (AD)][aad-service-principal]. Jednostka usługi jest wymagana do dynamicznego tworzenia i zarządzania innymi zasobami platformy Azure, takimi jak moduł równoważenia obciążenia platformy Azure lub rejestr kontenerów (ACR).

W tym artykule przedstawiono sposób tworzenia i używania jednostki usługi dla klastrów AKS.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby utworzyć jednostkę usługi Azure AD, musisz mieć uprawnienia do zarejestrowania aplikacji w dzierżawie usługi Azure AD i przypisania aplikacji do roli w ramach subskrypcji. Jeśli nie masz wymaganych uprawnień, może być konieczne poproszenie administratora usługi Azure AD lub subskrypcji o przypisanie niezbędnych uprawnień lub wstępne utworzenie jednostki usługi do użycia z klastrem AKS.

Jeśli używasz jednostki usługi z innej dzierżawy usługi Azure AD, istnieją dodatkowe zagadnienia dotyczące uprawnień dostępnych podczas wdrażania klastra. Licencjobiorca może nie mieć odpowiednich uprawnień do odczytu i zapisu informacji katalogowych. Aby uzyskać więcej informacji, zobacz [co to są domyślne uprawnienia użytkownika w Azure Active Directory?][azure-ad-permissions]

Konieczne jest również zainstalowanie i skonfigurowanie interfejsu wiersza polecenia platformy Azure w wersji 2.0.59 lub nowszej. Uruchom @ no__t-0, aby znaleźć wersję. Jeśli konieczne jest zainstalowanie lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="automatically-create-and-use-a-service-principal"></a>Automatyczne tworzenie i używanie jednostki usługi

Podczas tworzenia klastra AKS w Azure Portal lub przy użyciu polecenia [AZ AKS Create][az-aks-create] platforma Azure może automatycznie wygenerować jednostkę usługi.

W poniższym przykładzie interfejsu wiersza polecenia platformy Azure nie określono nazwy głównej usługi. W tym scenariuszu interfejs wiersza polecenia platformy Azure tworzy nazwę główną usługi dla klastra AKS. Aby pomyślnie ukończyć operację, konto platformy Azure musi mieć odpowiednie prawa do tworzenia jednostki usługi.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup
```

## <a name="manually-create-a-service-principal"></a>Ręczne tworzenie jednostki usługi

Aby ręcznie utworzyć jednostkę usługi za pomocą interfejsu wiersza polecenia platformy Azure, użyj polecenia [AZ AD Sp Create-for-RBAC][az-ad-sp-create] . W poniższym przykładzie parametr `--skip-assignment` zapobiega przypisaniu dodatkowych przypisań domyślnych:

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

Dane wyjściowe są podobne do poniższego przykładu. Zanotuj własne `appId` i `password`. Te wartości są używane podczas tworzenia klastra AKS w następnej sekcji.

```json
{
  "appId": "559513bd-0c19-4c1a-87cd-851a26afd5fc",
  "displayName": "azure-cli-2019-03-04-21-35-28",
  "name": "http://azure-cli-2019-03-04-21-35-28",
  "password": "e763725a-5eee-40e8-a466-dc88d980f415",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

## <a name="specify-a-service-principal-for-an-aks-cluster"></a>Określanie nazwy głównej usługi dla klastra AKS

Aby użyć istniejącej jednostki usługi podczas tworzenia klastra AKS za pomocą polecenia [AZ AKS Create][az-aks-create] , użyj parametrów `--service-principal` i `--client-secret`, aby określić `appId` i `password` z danych wyjściowych polecenia [AZ AD Sp Create-for-RBAC][az-ad-sp-create] :

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --service-principal <appId> \
    --client-secret <password>
```

W przypadku wdrażania klastra AKS przy użyciu Azure Portal na stronie *uwierzytelnianie* w oknie dialogowym **Tworzenie klastra Kubernetes** wybierz opcję **skonfigurowania jednostki usługi**. Wybierz pozycję **Użyj istniejącej**i określ następujące wartości:

- **Identyfikator klienta nazwy głównej usługi** to Twój *Identyfikator aplikacji*
- **Klucz tajny klienta jednostki usługi** jest wartością *hasła*

![Obraz przedstawiający przechodzenie do głosu platformy Azure](media/kubernetes-service-principal/portal-configure-service-principal.png)

## <a name="delegate-access-to-other-azure-resources"></a>Delegowanie dostępu do innych zasobów platformy Azure

Nazwa główna usługi dla klastra AKS może być używana do uzyskiwania dostępu do innych zasobów. Jeśli na przykład chcesz wdrożyć klaster AKS w istniejącej podsieci sieci wirtualnej platformy Azure lub połączyć się z usługą Azure Container Registry (ACR), musisz delegować dostęp do tych zasobów do nazwy głównej usługi.

Aby delegować uprawnienia, Utwórz przypisanie roli za pomocą polecenia [AZ role przypisanie Create][az-role-assignment-create] . Przypisz `appId` do określonego zakresu, na przykład grupy zasobów lub zasobu sieci wirtualnej. Następnie rola definiuje, jakie uprawnienia jednostka usługi ma w danym zasobie, jak pokazano w następującym przykładzie:

```azurecli
az role assignment create --assignee <appId> --scope <resourceScope> --role Contributor
```

Wartość `--scope` dla zasobu musi być pełnym IDENTYFIKATORem zasobu, na przykład */subscriptions/\<guid @ no__t-3/resourceGroups/Webresources* lub */subscriptions/\<guid @ no__t-6/resourceGroups/myResourceGroupVnet/Providers/Microsoft. Network/virtualNetworks/myVnet*

W poniższych sekcjach szczegółowo opisano typowe delegowania, które mogą być potrzebne.

### <a name="azure-container-registry"></a>Azure Container Registry

Jeśli używasz Azure Container Registry (ACR) jako magazynu obrazów kontenerów, musisz udzielić uprawnień do jednostki usługi dla klastra AKS w celu odczytywania i ściągania obrazów. Obecnie zalecaną konfiguracją jest użycie polecenia [AZ AKS Create][az-aks-create] lub [AZ AKS Update] [AZ-AKS-Update] w celu zintegrowania z rejestrem i przypisania odpowiedniej roli dla jednostki usługi. Aby uzyskać szczegółowe instrukcje, zobacz [uwierzytelnianie za pomocą Azure Container Registry z usługi Azure Kubernetes][aks-to-acr].

### <a name="networking"></a>Obsługa sieci

Możesz użyć zaawansowanej sieci, w której Sieć wirtualna i podsieć lub publiczne adresy IP znajdują się w innej grupie zasobów. Przypisz jeden z następujących uprawnień roli:

- Utwórz [rolę niestandardową][rbac-custom-role] i Zdefiniuj następujące uprawnienia roli:
  - *Microsoft. Network/virtualNetworks/Subnets/Join/Action*
  - *Microsoft. Network/virtualNetworks/podsieci/odczyt*
  - *Microsoft. Network/virtualNetworks/podsieci/zapis*
  - *Microsoft. Network/adresów publicipaddress/Join/Action*
  - *Microsoft. Network/adresów publicipaddress/odczyt*
  - *Microsoft. Network/adresów publicipaddress/Write*
- Lub Przypisz wbudowaną rolę [współautor sieci][rbac-network-contributor] w podsieci w sieci wirtualnej

### <a name="storage"></a>Magazyn

Może być konieczne uzyskanie dostępu do istniejących zasobów dyskowych w innej grupie zasobów. Przypisz jeden z następujących uprawnień roli:

- Utwórz [rolę niestandardową][rbac-custom-role] i Zdefiniuj następujące uprawnienia roli:
  - *Microsoft. COMPUTE/disks/Read*
  - *Microsoft. COMPUTE/disks/Write*
- Lub Przypisz wbudowaną rolę [współautor konta magazynu][rbac-storage-contributor] w grupie zasobów

### <a name="azure-container-instances"></a>Azure Container Instances

W przypadku korzystania z usługi Virtual Kubelet do integrowania z usługą AKS i uruchamiania Azure Container Instances (ACI) w grupie zasobów oddzielonej do klastra AKS, nazwa główna usługi musi mieć przydzielone uprawnienia *współautora* w grupie zasobów AKS.

## <a name="additional-considerations"></a>Dodatkowe zagadnienia

W przypadku korzystania z AKS i nazw głównych usługi Azure AD należy pamiętać o następujących kwestiach.

- Nazwa główna usługi dla Kubernetes jest częścią konfiguracji klastra. Nie należy jednak używać tożsamości do wdrożenia klastra.
- Domyślnie poświadczenia jednostki usługi są ważne przez jeden rok. [Poświadczenia nazwy głównej usługi można zaktualizować lub obrócić][update-credentials] w dowolnym momencie.
- Każda jednostka usługi jest skojarzona z aplikacją usługi Azure AD. Nazwa główna usługi dla klastra Kubernetes może być skojarzona z dowolną prawidłową nazwą aplikacji usługi Azure AD (na przykład: *https://www.contoso.org/example* ). Adres URL aplikacji nie musi być rzeczywistym punktem końcowym.
- Podczas określania **identyfikatora klienta**jednostki usługi należy użyć wartości `appId`.
- Na maszynach wirtualnych węzłów agenta w klastrze Kubernetes poświadczenia jednostki usługi są przechowywane w pliku `/etc/kubernetes/azure.json`
- W przypadku użycia polecenia [AZ AKS Create][az-aks-create] w celu automatycznego wygenerowania jednostki usługi poświadczenia jednostki usługi są zapisywane w pliku `~/.azure/aksServicePrincipal.json` na maszynie użytej do uruchomienia polecenia.
- Po usunięciu klastra AKS, który został utworzony przez [AZ AKS Create][az-aks-create], nazwa główna usługi utworzona automatycznie nie jest usuwana.
    - Aby usunąć nazwę główną usługi, wykonaj zapytanie dotyczące klastra *servicePrincipalProfile. clientId* , a następnie usuń za pomocą [AZ AD App Delete][az-ad-app-delete]. Zastąp następujące nazwy grup zasobów i klastra własnymi wartościami:

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
[rbac-network-contributor]: ../role-based-access-control/built-in-roles.md#network-contributor
[rbac-custom-role]: ../role-based-access-control/custom-roles.md
[rbac-storage-contributor]: ../role-based-access-control/built-in-roles.md#storage-account-contributor
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[aks-to-acr]: cluster-container-registry-integration.md
[update-credentials]: update-credentials.md
[azure-ad-permissions]: ../active-directory/fundamentals/users-default-permissions.md

---
title: Jednostka usługi klastra Azure Kubernetes
description: Tworzenie jednostki usługi Azure Active Directory dla klastra Kubernetes w usłudze AKS i zarządzanie nią
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: get-started-article
ms.date: 04/19/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 4ad0fc3fdb7d5b7c14f13fd6c279915974558dc9
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2018
ms.locfileid: "39578800"
---
# <a name="service-principals-with-azure-kubernetes-service-aks"></a>Jednostki usługi w usłudze Azure Kubernetes Service (AKS)

Klaster AKS wymaga [jednostki usługi Azure Active Directory][aad-service-principal] do współpracy z interfejsami API platformy Azure. Jednostka usługi jest potrzebna do dynamicznego tworzenia zasobów, takich jak [usługa Azure Load Balancer][azure-load-balancer-overview], i zarządzania nimi.

W tym artykule przedstawiono różne sposoby konfigurowania jednostki usługi dla własnego klastra Kubernetes w usłudze AKS.

## <a name="before-you-begin"></a>Przed rozpoczęciem


Aby utworzyć jednostkę usługi Azure AD, musisz mieć uprawnienia do zarejestrowania aplikacji w swojej dzierżawie usługi Azure AD i przypisania aplikacji do roli w swojej subskrypcji. Jeśli nie masz niezbędnych uprawnień, może być konieczne zwrócenie się z prośbą do administratora usługi Azure AD lub subskrypcji, aby przyznał niezbędne uprawnienia, lub o wstępne utworzenie jednostki usługi dla klastra Kubernetes.

Musisz również mieć zainstalowany i skonfigurowany interfejs wiersza polecenia platformy Azure w wersji 2.0.27 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="create-sp-with-aks-cluster"></a>Utwórz jednostkę usługi z klastrem usługi AKS

Podczas wdrażania klastra usługi AKS za pomocą polecenia `az aks create` masz możliwość automatycznego wygenerowania jednostki usługi.

W następującym przykładzie tworzony jest klaster AKS. Ponieważ nie podano istniejącej jednostki usługi, dla klastra jest tworzona jednostka usługi. Aby można było ukończyć tę operację, Twoje konto musi mieć odpowiednie uprawnienia do tworzenia jednostki usługi.

```azurecli-interactive
az aks create --name myAKSCluster --resource-group myResourceGroup --generate-ssh-keys
```

## <a name="use-an-existing-sp"></a>Używanie istniejącej jednostki usługi

Można użyć istniejącej jednostki usługi Azure AD lub wstępnie ją utworzyć do użycia z klastrem usługi AKS. Jest to przydatne w przypadku wdrażania klastra z witryny Azure Portal, gdzie jest wymagane podanie informacji o jednostce usługi. W przypadku korzystania z istniejącej jednostki usługi jako hasło należy skonfigurować klucz tajny klienta.

## <a name="pre-create-a-new-sp"></a>Wstępne tworzenie nowej jednostki usługi

Użyj polecenia [az ad sp create-for-rbac][az-ad-sp-create], aby utworzyć jednostkę usługi przy użyciu interfejsu wiersza polecenia platformy Azure.

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

Dane wyjściowe będą podobne do następujących. Zwróć uwagę na elementy `appId` i `password`. Te wartości są używane podczas tworzenia klastra usługi AKS.

```json
{
  "appId": "7248f250-0000-0000-0000-dbdeb8400d85",
  "displayName": "azure-cli-2017-10-15-02-20-15",
  "name": "http://azure-cli-2017-10-15-02-20-15",
  "password": "77851d2c-0000-0000-0000-cb3ebc97975a",
  "tenant": "72f988bf-0000-0000-0000-2d7cd011db47"
}
```

## <a name="use-an-existing-sp"></a>Używanie istniejącej jednostki usługi

Korzystając ze wstępnie utworzonej jednostki usługi, podaj `appId` i `password` jako wartości argumentu w poleceniu `az aks create`.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --service-principal <appId> --client-secret <password>
```

W przypadku wdrażania klastra AKS przy użyciu witryny Azure Portal wprowadź wartość `appId` w polu **Identyfikator klienta jednostki usługi** i wartość `password` w polu **Klucz tajny klienta jednostki usługi** w formie konfiguracji klastra AKS.

![Obraz przedstawiający przechodzenie do aplikacji Azure Vote](media/container-service-kubernetes-service-principal/sp-portal.png)

## <a name="additional-considerations"></a>Dodatkowe zagadnienia

Podczas pracy z jednostkami usług AKS i Azure AD należy pamiętać o poniższym.

* Jednostka usługi dla rozwiązania Kubernetes jest częścią konfiguracji klastra. Nie należy jednak używać tożsamości do wdrażania klastra.
* Każda jednostka usługi jest skojarzona z aplikacją usługi Azure AD. Jednostka usługi dla klastra Kubernetes może zostać skojarzona z dowolną prawidłową nazwą aplikacji usługi Azure AD (np. `https://www.contoso.org/example`). Adres URL dla aplikacji nie musi być rzeczywistym punktem końcowym.
* Podczas określania **identyfikatora klienta** jednostki usługi użyj wartości `appId`.
* Na głównej maszynie wirtualnej i maszynach wirtualnych węzłów w klastrze Kubernetes poświadczenia jednostki usługi są przechowywane w pliku `/etc/kubernetes/azure.json`.
* Gdy używasz polecenia `az aks create`, aby automatycznie wygenerować jednostkę usługi, poświadczenia jednostki usługi są zapisywane w pliku `~/.azure/aksServicePrincipal.json` na maszynie użytej do uruchomienia polecenia.
* Usunięcie klastra AKS utworzonego za pomocą polecenia `az aks create` nie powoduje usunięcia utworzonej automatycznie jednostki usługi. Aby usunąć jednostkę usługi, należy najpierw uzyskać jej identyfikator za pomocą polecenia [az ad app list][az-ad-app-list]. Poniższy przykład obejmuje wykonanie zapytania dotyczącego klastra o nazwie *myAKSCluster*, a następnie usunięcie aplikacji na podstawie identyfikatora za pomocą polecenia [az ad app delete][az-ad-app-delete]. Zastąp odpowiednie nazwy własnymi wartościami:

    ```azurecli-interactive
    az ad app list --query "[?displayName=='myAKSCluster'].{Name:displayName,Id:appId}" --output table
    az ad app delete --id <appId>
    ```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat jednostek usługi Azure Active Directory, zobacz dokumentację aplikacji usługi Azure AD.

> [!div class="nextstepaction"]
> [Application and service principal objects (Obiekty aplikacji i jednostki usługi)][service-principal]

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

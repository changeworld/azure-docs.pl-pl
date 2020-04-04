---
title: Akcje Usługi GitHub & usłudze Azure Kubernetes (wersja zapoznawcza)
services: azure-dev-spaces
ms.date: 04/03/2020
ms.topic: conceptual
description: Przeglądanie i testowanie zmian z żądania ściągania bezpośrednio w usłudze Azure Kubernetes przy użyciu akcji Usługi GitHub i usługi Azure Dev Spaces
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontenery, Akcje GitHub, Helm, siatka usługi, routing siatki usług, kubectl, k8s
manager: gwallace
ms.openlocfilehash: a83da0ef3958748831eb0eeda1aa5e91efa7ef2e
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637943"
---
# <a name="github-actions--azure-kubernetes-service-preview"></a>Akcje Usługi GitHub & usłudze Azure Kubernetes (wersja zapoznawcza)

Usługa Azure Dev Spaces udostępnia przepływ pracy przy użyciu akcji GitHub, który umożliwia testowanie zmian z żądania ściągnięcia bezpośrednio w usłudze AKS, zanim żądanie ściągnięcia zostanie scalone z główną gałęzią repozytorium. Posiadanie uruchomionej aplikacji do przeglądania zmian żądania ściągania może zwiększyć zaufanie zarówno dewelopera, jak i członków zespołu. Ta uruchomiona aplikacja może również pomóc członkom zespołu, takim jak menedżerowie produktów i projektanci, stać się częścią procesu przeglądu na wczesnych etapach rozwoju.

Niniejszy przewodnik zawiera informacje na temat wykonywania następujących czynności:

* Konfigurowanie usługi Azure Dev Spaces w zarządzanym klastrze kubernetes na platformie Azure.
* Wdrażanie dużej aplikacji z wieloma mikrousługami w przestrzeni deweloperów.
* Konfigurowanie ciągłej integracji/ciągłego wdrażania za pomocą akcji GitHub.
* Przetestuj pojedyncze mikrousługi w izolowanej przestrzeni deweloperów w kontekście pełnej aplikacji.

> [!IMPORTANT]
> Ta funkcja jest obecnie dostępna w wersji zapoznawczej. Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free).
* [Zainstalowany interfejs wiersza polecenia platformy Azure][azure-cli-installed].
* [Helm 3 zainstalowany][helm-installed].
* Konto GitHub z [włączoną funkcją Akcje GitHub][github-actions-beta-signup].
* Przykładowa [aplikacja azure dev spaces bike sharing](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp/README.md) uruchomiona w klastrze AKS.

## <a name="create-an-azure-container-registry"></a>Tworzenie rejestru Azure Container Registry

Tworzenie rejestru kontenerów platformy Azure (ACR):

```azurecli
az acr create --resource-group MyResourceGroup --name <acrName> --sku Basic
```

> [!IMPORTANT]
> Nazwa usługi ACR musi być unikatowa na platformie Azure i zawierać 5-50 znaków alfanumerycznej. Wszelkie litery, których używasz, muszą być małe litery.

Zapisz wartość *loginServer* z danych wyjściowych, ponieważ jest on używany w późniejszym kroku.

## <a name="create-a-service-principal-for-authentication"></a>Tworzenie jednostki usługi do uwierzytelniania

Użyj [az ad sp create-for-rbac,][az-ad-sp-create-for-rbac] aby utworzyć jednostkę usługi. Przykład:

```azurecli
az ad sp create-for-rbac --sdk-auth --skip-assignment
```

Zapisz dane wyjściowe JSON, ponieważ jest on używany w późniejszym kroku.

Użyj [az aks show,][az-aks-show] aby wyświetlić *identyfikator* klastra AKS:

```azurecli
az aks show -g MyResourceGroup -n MyAKS  --query id
```

Użyj [az acr show,][az-acr-show] aby wyświetlić *identyfikator* ACR:

```azurecli
az acr show --name <acrName> --query id
```

Użyj [tworzenia przypisania roli az,][az-role-assignment-create] aby dać *współautorowi* dostęp do klastra AKS i *acrPush* dostęp do usługi ACR.

```azurecli
az role assignment create --assignee <ClientId> --scope <AKSId> --role Contributor
az role assignment create --assignee <ClientId>  --scope <ACRId> --role AcrPush
```

> [!IMPORTANT]
> Musisz być właścicielem klastra AKS i usługi ACR, aby zapewnić podmiotowi usługi dostęp do tych zasobów.

## <a name="configure-your-github-action"></a>Konfigurowanie akcji usługi GitHub

> [!IMPORTANT]
> Dla repozytorium musi być włączona funkcja Akcje usługi GitHub. Aby włączyć akcje Usługi GitHub dla repozytorium, przejdź do repozytorium w usłudze GitHub, kliknij kartę Akcje i wybierz opcję włączenia akcji dla tego repozytorium.

Przejdź do rozwidlonego repozytorium i kliknij pozycję *Ustawienia*. Kliknij na *Sekrety* na lewym pasku bocznym. Kliknij *pozycję Dodaj nowy klucz tajny,* aby dodać każdy nowy klucz tajny poniżej:

1. *AZURE_CREDENTIALS:* całe dane wyjściowe z tworzenia jednostki usługi.
1. *RESOURCE_GROUP*: grupa zasobów dla klastra AKS, która w tym przykładzie jest *MyResourceGroup*.
1. *CLUSTER_NAME*: nazwa klastra AKS, który w tym przykładzie jest *MyAKS*.
1. *CONTAINER_REGISTRY*: *loginServer* dla ACR.
1. *HOST*: host przestrzeni deweloperów, który ma formę *<MASTER_SPACE>.<APP_NAME>.<HOST_SUFFIX>*, co w tym przykładzie jest *dev.bikesharingweb.fedcab0987.eus.azds.io*.
1. *IMAGE_PULL_SECRET*: nazwa tajemnicy, której chcesz użyć, na przykład *demo-secret*.
1. *MASTER_SPACE*: nazwa nadrzędnego Dev Space, który w tym przykładzie jest *dev*.
1. *REGISTRY_USERNAME:* *identyfikator klienta* z danych wyjściowych JSON z tworzenia jednostki usługi.
1. *REGISTRY_PASSWORD:* *clientSecret* z danych wyjściowych JSON z tworzenia jednostki usługi.

> [!NOTE]
> Wszystkie te wpisy są używane przez akcję GitHub i są konfigurowane w [pliku .github/workflows/bikes.yml][github-action-yaml].

Opcjonalnie, jeśli chcesz zaktualizować przestrzeń główną po scaleniu funkcji PR, dodaj *GATEWAY_HOST* klucz tajny, który ma postać *<MASTER_SPACE>.gateway.<HOST_SUFFIX>*, który w tym przykładzie jest *dev.gateway.fedcab0987.eus.azds.io*. Po scaleniu zmian z gałęzią główną w rozwidleniu zostanie uruchomione kolejna akcja, aby odbudować i uruchomić całą aplikację w głównym miejscu deweloperskim. W tym przykładzie przestrzeń główna jest *dev*. Ta akcja jest skonfigurowana w [pliku .github/workflows/bikesharing.yml][github-action-bikesharing-yaml].

Ponadto jeśli chcesz, aby zmiany w komputerze-specielnym były uruchamiane w przestrzeni wnuków, zaktualizuj *wpisy* tajne MASTER_SPACE i *HOST.* Na przykład jeśli aplikacja jest uruchomiona w *dev* z deweloperem przestrzeni *podrzędnej/azureuser1*, aby mieć pr uruchomić w przestrzeni podrzędnej *dev/azureuser1:*

* Zaktualizuj *MASTER_SPACE* do przestrzeni podrzędnej, która ma być przestrzenią nadrzędną, w tym *przykładzie azureuser1*.
* W *HOST* tym *przykładzie*dev.bikesharingweb.fedcab0987.eus.azds.io dev.bikesharingweb.fedcab0987.eus.azds.io>HOST_SUFFIX>HOST_SUFFIX<GRANDPARENT_SPACE *APP_NAME>.<> <<*.

## <a name="create-a-new-branch-for-code-changes"></a>Tworzenie nowej gałęzi dla zmian kodu

Przejdź `BikeSharingApp/` do nowej gałęzi o nazwie *bike-images*i utwórz ją.

```cmd
cd dev-spaces/samples/BikeSharingApp/
git checkout -b bike-images
```

Edytuj [bikes/server.js,][bikes-server-js] aby usunąć linie 232 i 233:

```javascript
    // Hard code image url *FIX ME*
    theBike.imageUrl = "/static/logo.svg";
```

Sekcja powinna teraz wyglądać następująco:

```javascript
    var theBike = result;
    theBike.id = theBike._id;
    delete theBike._id;
```

Zapisz plik, `git add` a `git commit` następnie użyj i na stole montażowym zmian.

```cmd
git add Bikes/server.js 
git commit -m "Removing hard coded imageUrl from /bikes/:id route"
```

## <a name="push-your-changes"></a>Wypychanie zmian

Użyj, `git push` aby wypchnąć nową gałąź do rozwidlonego repozytorium:

```cmd
git push origin bike-images
```

Po zakończeniu wypychania przejdź do rozwidlonego repozytorium w usłudze GitHub, aby utworzyć żądanie ściągnięcia z *gałęzią główną* w rozwidlonym repozytorium jako gałęzi podstawowej w porównaniu z *gałęzią obrazów rowerów.*

Po otwarciu żądania ściągnięcia przejdź do karty *Akcje.* *Bikes*

## <a name="view-the-child-space-with-your-changes"></a>Wyświetlanie przestrzeni podrzędnej ze zmianami

Po zakończeniu akcji zostanie wyświetlony komentarz z adresem URL do nowego miejsca podrzędnego na podstawie zmian w żądaniu ściągnięcia.

> [!div class="mx-imgBorder"]
> ![Adres URL akcji GitHub](../media/github-actions/github-action-url.png)

Przejdź do usługi *bikesharingweb,* otwierając adres URL z komentarza. Wybierz *Aurelia Briggs (klienta)* jako użytkownika, a następnie wybierz rower do wynajęcia. Sprawdź, czy nie widzisz już obrazu zastępczego roweru.

Jeśli scalisz zmiany z *gałęzią główną* w rozwidle, zostanie uruchomione inna akcja, aby odbudować i uruchomić całą aplikację w nadrzędnym miejscu deweloperskim. W tym przykładzie przestrzeń nadrzędna jest *dev*. Ta akcja jest skonfigurowana w [pliku .github/workflows/bikesharing.yml][github-action-bikesharing-yaml].

## <a name="clean-up-your-azure-resources"></a>Oczyszczanie zasobów platformy Azure

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak usługa Azure Dev Spaces pomaga tworzyć bardziej złożone aplikacje w wielu kontenerach i jak można uprościć tworzenie współpracy, pracując z różnymi wersjami lub gałęziami kodu w różnych przestrzeniach.

> [!div class="nextstepaction"]
> [Tworzenie zespołów w usłudze Azure Dev Spaces][team-quickstart]

[azure-cli-installed]: /cli/azure/install-azure-cli?view=azure-cli-latest
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-aks-show]: /cli/azure/aks?view=azure-cli-latest#az-aks-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[bikes-server-js]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L232-L233
[bike-sharing-gh]: https://github.com/Azure/dev-spaces/
[bike-sharing-values-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml
[github-actions-beta-signup]: https://github.com/features/actions
[github-action-yaml]: https://github.com/Azure/dev-spaces/blob/master/.github/workflows/bikes.yml
[github-action-bikesharing-yaml]: https://github.com/Azure/dev-spaces/blob/master/.github/workflows/bikesharing.yml
[helm-installed]: https://helm.sh/docs/intro/install/
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[sp-acr]: ../../container-registry/container-registry-auth-service-principal.md
[sp-aks]: ../../aks/kubernetes-service-principal.md
[team-quickstart]: ../quickstart-team-development.md

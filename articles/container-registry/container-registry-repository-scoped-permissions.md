---
title: Uprawnienia do repozytoriów
description: Tworzenie tokenu z uprawnieniami ograniczonymi do określonych repozytoriów w rejestrze w celu ściągania lub wypychania obrazów
ms.topic: article
ms.date: 10/31/2019
ms.openlocfilehash: cf36a49ffd6c04897e6f44b844f0c813d0992b18
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2019
ms.locfileid: "74454912"
---
# <a name="repository-scoped-permissions-in-azure-container-registry"></a>Uprawnienia w zakresie repozytorium w Azure Container Registry 

Azure Container Registry obsługuje kilka [opcji uwierzytelniania](container-registry-authentication.md) przy użyciu tożsamości, które mają [dostęp oparty na rolach](container-registry-roles.md) do całego rejestru. Jednak w przypadku niektórych scenariuszy może być konieczne zapewnienie dostępu tylko do określonych *repozytoriów* w rejestrze. 

W tym artykule przedstawiono sposób tworzenia i używania tokenu dostępu, który ma uprawnienia do wykonywania akcji tylko dla określonych repozytoriów w rejestrze. Przy użyciu tokenu dostępu można zapewnić użytkownikom lub usługom zakres, ograniczony czasowo dostęp do repozytoriów w celu ściągania lub wypychania obrazów lub wykonywania innych akcji. 

Zobacz [Informacje o uprawnieniach do zakresu repozytorium](#about-repository-scoped-permissions), w dalszej części tego artykułu, dla celów ogólnych dotyczących pojęć i scenariuszy dotyczących tokenów.

> [!IMPORTANT]
> Ta funkcja jest obecnie dostępna w wersji zapoznawczej, a niektóre [ograniczenia mają zastosowanie](#preview-limitations). Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania][terms-of-use]. Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.

## <a name="preview-limitations"></a>Ograniczenia wersji zapoznawczej

* Ta funkcja jest dostępna tylko w rejestrze kontenera w **warstwie Premium** . Aby uzyskać informacje o warstwach i ograniczeniach usługi Registry, zobacz [Azure Container Registry SKU](container-registry-skus.md).
* Obecnie nie można przypisać uprawnień do zakresu repozytorium do obiektu Azure Active Directory, takiego jak nazwa główna usługi lub tożsamość zarządzana.

## <a name="prerequisites"></a>Wymagania wstępne

* **Interfejs wiersza polecenia platformy Azure** — ten artykuł wymaga lokalnej instalacji interfejsu wiersza polecenia platformy Azure (w wersji 2.0.76 lub nowszej). Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).
* **Docker** — aby uwierzytelniać się w rejestrze, potrzebna jest również lokalna instalacja platformy Docker. Platforma Docker dostarcza instrukcje na temat instalacji w systemach [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) i [Linux](https://docs.docker.com/engine/installation/#supported-platforms).
* **Rejestr kontenerów z repozytoriami** — jeśli go nie masz, Utwórz rejestr kontenerów w ramach subskrypcji platformy Azure. Na przykład użyj [Azure Portal](container-registry-get-started-portal.md) lub [interfejsu wiersza polecenia platformy Azure](container-registry-get-started-azure-cli.md). 

  Do celów testowych [wypchnij](container-registry-get-started-docker-cli.md) lub [zaimportuj](container-registry-import-images.md) jeden lub więcej przykładowych obrazów do rejestru. Przykłady w tym artykule odnoszą się do następujących obrazów w dwóch repozytoriach: `samples/hello-world:v1` i `samples/nginx:v1`. 

## <a name="create-an-access-token"></a>Tworzenie tokenu dostępu

Utwórz token za pomocą polecenia [AZ ACR token Create][az-acr-token-create] . Podczas tworzenia tokenu należy określić co najmniej jeden repozytoria i skojarzone akcje w każdym repozytorium lub określić istniejącą mapę zakresu z tymi ustawieniami.

### <a name="create-access-token-and-specify-repositories"></a>Tworzenie tokenu dostępu i określanie repozytoriów

Poniższy przykład tworzy token dostępu z uprawnieniami do wykonywania akcji `content/write` i `content/read` w repozytorium `samples/hello-world` oraz akcji `content/read` w repozytorium `samples/nginx`. Domyślnie polecenie generuje dwa hasła. 

Ten przykład ustawia stan tokenu na `enabled` (ustawienie domyślne), ale można w dowolnym momencie zaktualizować token i ustawić stan na `disabled`.

```azurecli
az acr token create --name MyToken --registry myregistry \
  --repository samples/hello-world content/write content/read \
  --repository samples/nginx content/read --status enabled
```

W danych wyjściowych znajdują się szczegółowe informacje o tokenie, w tym wygenerowane hasła i mapa zakresu. Zaleca się zapisanie haseł w bezpiecznym miejscu do późniejszego użycia z `docker login`. Hasła nie mogą zostać pobrane ponownie, ale można generować nowe.

Dane wyjściowe pokazują również, że mapa zakresu jest tworzona automatycznie, nazwana `MyToken-scope-map`. Można użyć mapy zakresu, aby zastosować te same akcje repozytorium do innych tokenów. Można też zaktualizować mapowanie zakresu później, aby zmienić uprawnienia tokenu.

```console
{
  "creationDate": "2019-10-22T00:15:34.066221+00:00",
  "credentials": {
    "certificates": [],
    "passwords": [
      {
        "creationTime": "2019-10-22T00:15:52.837651+00:00",
        "expiry": null,
        "name": "password1",
        "value": "uH54BxxxxK7KOxxxxRbr26dAs8JXxxxx"
      },
      {
        "creationTime": "2019-10-22T00:15:52.837651+00:00",
        "expiry": null,
        "name": "password2",
        "value": "kPX6Or/xxxxLXpqowxxxxkA0idwLtmxxxx"
      }
    ],
    "username": "MyToken"
  },
  "id": "/subscriptions/xxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/tokens/MyToken",
  "name": "MyToken",
  "objectId": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "scopeMapId": "/subscriptions/xxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/scopeMaps/MyToken-scope-map",
  "status": "enabled",
  "type": "Microsoft.ContainerRegistry/registries/tokens"
```

### <a name="create-a-scope-map-and-associated-token"></a>Tworzenie mapy zakresu i skojarzonego tokenu

Alternatywnie można określić mapowanie zakresu z repozytoriami i skojarzonymi akcjami podczas tworzenia tokenu. Aby utworzyć mapę zakresu, użyj polecenia [AZ ACR Scope-map Create][az-acr-scope-map-create] .

Poniższe przykładowe polecenie tworzy mapę zakresu z tymi samymi uprawnieniami, które są używane w poprzednim przykładzie. Umożliwia `content/write` i `content/read` akcji w repozytorium `samples/hello-world` oraz akcję `content/read` w repozytorium `samples/nginx`:

```azurecli
az acr scope-map create --name MyScopeMap --registry myregistry \
  --repository samples/hello-world content/write content/read \
  --repository samples/nginx content/read \
  --description "Sample scope map"
```

Dane wyjściowe będą podobne do następujących:

```console
{
  "actions": [
    "repositories/samples/hello-world/content/write",
    "repositories/samples/nginx/content/read"
  ],
  "creationDate": "2019-10-22T05:07:35.194413+00:00",
  "description": "Sample scope map.",
  "id": "/subscriptions/fxxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/scopeMaps/MyScopeMap",
  "name": "MyScopeMap",
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "scopeMapType": "UserDefined",
  "type": "Microsoft.ContainerRegistry/registries/scopeMaps"
```

Uruchom [AZ ACR token Create][az-acr-token-create] , aby utworzyć token skojarzony z mapą zakresu *MyScopeMap* . Domyślnie polecenie generuje dwa hasła. Ten przykład ustawia stan tokenu na `enabled` (ustawienie domyślne), ale można w dowolnym momencie zaktualizować token i ustawić stan na `disabled`.

```azurecli
az acr token create --name MyToken --registry myregistry --scope-map MyScopeMap --status enabled
```

W danych wyjściowych znajdują się szczegółowe informacje o tokenie, w tym wygenerowane hasła oraz zastosowanym mapowaniu zakresu. Zaleca się zapisanie haseł w bezpiecznym miejscu do późniejszego użycia z `docker login`. Hasła nie mogą zostać pobrane ponownie, ale można generować nowe.

## <a name="generate-passwords-for-token"></a>Generuj hasła dla tokenu

Jeśli podczas tworzenia tokenu hasła zostały utworzone, Kontynuuj [uwierzytelnianie przy użyciu rejestru](#authenticate-using-token).

Jeśli nie masz hasła do tokenu lub chcesz wygenerować nowe hasła, uruchom polecenie [AZ ACR token Credential Generate][az-acr-token-credential-generate] .

Poniższy przykład generuje nowe hasło dla utworzonego tokenu z okresem ważności wynoszącym 30 dni. Hasło jest przechowywane w zmiennej środowiskowej TOKEN_PWD. Ten przykład jest sformatowany dla powłoki bash.

```azurecli
TOKEN_PWD=$(az acr token credential generate \
  --name MyToken --registry myregistry --days 30 \
  --password1 --query 'passwords[0].value' --output tsv)
```

## <a name="authenticate-using-token"></a>Uwierzytelnianie przy użyciu tokenu

Uruchom `docker login`, aby uwierzytelnić się w rejestrze przy użyciu poświadczeń tokenu. Wprowadź nazwę tokenu jako nazwę użytkownika i podaj jedno z jego haseł. Poniższy przykład jest sformatowany dla powłoki bash i zawiera wartości przy użyciu zmiennych środowiskowych.

```bash
TOKEN_NAME=MyToken
TOKEN_PWD=<token password>

echo $TOKEN_PWD | docker login --username $TOKEN_NAME --password-stdin myregistry.azurecr.io
```

Dane wyjściowe powinny zawierać pomyślne uwierzytelnianie:

```console
Login Succeeded
```

## <a name="verify-scoped-access"></a>Weryfikuj dostęp z zakresu

Można sprawdzić, czy token zapewnia uprawnienia w zakresie do repozytoriów w rejestrze. W tym przykładzie następujące polecenia `docker pull` pomyślnie ukończyły ściąganie obrazów dostępnych w `samples/hello-world` i repozytoriach `samples/nginx`:

```console
docker pull myregistry.azurecr.io/samples/hello-world:v1
docker pull myregistry.azurecr.io/samples/nginx:v1
```

Ponieważ przykładowy token zezwala na działanie `content/write` tylko w repozytorium `samples/hello-world`, `docker push` kończy się powodzeniem dla tego repozytorium, ale kończy się niepowodzeniem dla `samples/nginx`:

```console
# docker push succeeds
docker pull myregistry.azurecr.io/samples/hello-world:v1

# docker push fails
docker pull myregistry.azurecr.io/samples/nginx:v1
```

## <a name="update-scope-map-and-token"></a>Aktualizowanie mapy i tokenu zakresu

Aby zaktualizować uprawnienia tokenu, zaktualizuj uprawnienia w skojarzonej mapie zakresu przy użyciu polecenia [AZ ACR Scope-map Update][az-acr-scope-map-update]. Na przykład aby zaktualizować program *MyScopeMap* w celu usunięcia akcji `content/write` w repozytorium `samples/hello-world`:

```azurecli
az acr scope-map update --name MyScopeMap --registry myregistry \
  --remove samples/hello-world content/write
```

Jeśli mapa zakresu jest skojarzona z więcej niż jednym tokenem, polecenie aktualizuje uprawnienia wszystkich skojarzonych tokenów.

Jeśli chcesz zaktualizować token z inną mapą zakresu, uruchom polecenie [AZ ACR token Update][az-acr-token-update]. Na przykład:

```azurecli
az acr token update --name MyToken --registry myregistry \
  --scope-map MyNewScopeMap
```

Po zaktualizowaniu tokenu lub mapie zakresu skojarzonej z tokenem zmiany uprawnień zaczną obowiązywać przy następnym `docker login` lub innym uwierzytelnianiu przy użyciu tokenu.

Po zaktualizowaniu tokenu można wygenerować nowe hasła w celu uzyskania dostępu do rejestru. Uruchom [AZ ACR token Credential Generate][az-acr-token-credential-generate]. Na przykład:

```azurecli
az acr token credential generate \
  --name MyToken --registry myregistry --days 30
```

## <a name="about-repository-scoped-permissions"></a>Informacje o uprawnieniach do zakresu repozytorium

### <a name="concepts"></a>Pojęcia

Aby skonfigurować uprawnienia do zakresu repozytorium, należy utworzyć *token dostępu* i skojarzoną *mapę zakresu* przy użyciu poleceń w interfejsie wiersza polecenia platformy Azure.

* **Token dostępu** jest poświadczeniem używanym z hasłem do uwierzytelniania za pomocą rejestru. Skojarzone z każdym tokenem są dozwolonymi *akcjami* objętymi zakresem co najmniej jednego repozytoria. Można ustawić czas wygaśnięcia dla każdego tokenu. 

* **Akcje** w każdym określonym repozytorium obejmują co najmniej jedną z poniższych czynności.

  |Akcja  |Opis  |
  |---------|---------|
  |`content/read`     |  Odczytaj dane z repozytorium. Na przykład, należy ściągnąć artefakt.  |
  |`metadata/read`    | Odczytaj metadane z repozytorium. Na przykład wyświetlanie listy tagów lub wyświetlanie metadanych manifestu.   |
  |`content/write`     |  Zapisz dane w repozytorium. Użyj polecenia with `content/read`, aby wypchnąć artefakt.    |
  |`metadata/write`     |  Zapisz metadane w repozytorium. Na przykład zaktualizuj atrybuty manifestu.  |
  |`content/delete`    | Usuń dane z repozytorium. Na przykład Usuń repozytorium lub manifest. |

* **Mapa zakresu** jest obiektem rejestru, który grupuje uprawnienia do repozytorium, które są stosowane do tokenu lub można ponownie zastosować do innych tokenów. Jeśli nie zastosujesz mapy zakresu podczas tworzenia tokenu, zostanie automatycznie utworzona Mapa zakresu, aby zapisać ustawienia uprawnień. 

  Mapa zakresu pomaga skonfigurować wielu użytkowników z identycznym dostępem do zestawu repozytoriów. Azure Container Registry udostępnia również mapy zakresu zdefiniowane przez system, które można stosować podczas tworzenia tokenów dostępu.

Poniższy obraz zawiera podsumowanie relacji między tokenami i mapami zakresów. 

![Mapowanie i tokeny zakresu rejestru](media/container-registry-repository-scoped-permissions/token-scope-map-concepts.png)

### <a name="scenarios"></a>Scenariusze

Scenariusze korzystania z tokenu dostępu obejmują:

* Zapewnianie urządzeniom IoT z indywidualnymi tokenami w celu ściągania obrazu z repozytorium
* Udostępnianie zewnętrznej organizacji z uprawnieniami do określonego repozytorium 
* Ogranicz dostęp do repozytorium do określonych grup użytkowników w organizacji. Na przykład zapewniają dostęp do zapisu i odczytu do deweloperów, którzy tworzą obrazy przeznaczone do określonych repozytoriów, oraz dostęp do odczytu do zespołów, które wdrażają te repozytoria.

### <a name="authentication-using-token"></a>Uwierzytelnianie przy użyciu tokenu

Użyj nazwy tokenu jako nazwy użytkownika i jednego z skojarzonych haseł do uwierzytelniania w rejestrze docelowym. Metoda uwierzytelniania zależy od skonfigurowanych akcji.

### <a name="contentread-or-contentwrite"></a>zawartość/odczyt lub zawartość/zapis

Jeśli token zezwala tylko na akcje `content/read` lub `content/write`, należy podać poświadczenia tokenu w ramach jednego z następujących przepływów uwierzytelniania:

* Uwierzytelnianie za pomocą platformy Docker przy użyciu `docker login`
* Uwierzytelnianie za pomocą rejestru przy użyciu polecenia [AZ ACR login][az-acr-login] w interfejsie CLI platformy Azure

Po uwierzytelnieniu token zezwala na skonfigurowane akcje w repozytorium lub repozytoriach objętych zakresem. Na przykład, jeśli token zezwala na akcję `content/read` w repozytorium, operacje `docker pull` są dozwolone na obrazach w tym repozytorium.

#### <a name="metadataread-metadatawrite-or-contentdelete"></a>metadane/odczyt, metadane/zapis lub zawartość/usuwanie

Jeśli token zezwala na akcje `metadata/read`, `metadata/write`lub `content/delete` w repozytorium, poświadczenia tokenu muszą być podane jako parametry z powiązanymi poleceniami [AZ ACR Repository][az-acr-repository] w interfejsie CLI platformy Azure.

Na przykład jeśli w repozytorium są dozwolone `metadata/read` akcje, Przekaż poświadczenia tokenu podczas uruchamiania polecenia [AZ ACR Repository show-Tags][az-acr-repository-show-tags] , aby wyświetlić listę znaczników.

## <a name="next-steps"></a>Następne kroki

* Aby zarządzać mapami zakresu i tokenami dostępu, użyj dodatkowych poleceń w poleceniach [AZ ACR Scope-map][az-acr-scope-map] i [AZ ACR token][az-acr-token] groups.
* Zapoznaj się z [omówieniem uwierzytelniania](container-registry-authentication.md) , aby poznać scenariusze uwierzytelniania w usłudze Azure Container Registry przy użyciu konta administratora lub tożsamości Azure Active Directory.


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-repository]: /cli/azure/acr/repository/
[az-acr-repository-show-tags]: /cli/azure/acr/repository/#az-acr-repository-show-tags
[az-acr-scope-map]: /cli/azure/acr/scope-map/
[az-acr-scope-map-create]: /cli/azure/acr/scope-map/#az-acr-scope-map-create
[az-acr-scope-map-update]: /cli/azure/acr/scope-map/#az-acr-scope-map-update
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az-acr-scope-map-list
[az-acr-token]: /cli/azure/acr/token/
[az-acr-token-show]: /cli/azure/acr/token/#az-acr-token-show
[az-acr-token-create]: /cli/azure/acr/token/#az-acr-token-create
[az-acr-token-update]: /cli/azure/acr/token/#az-acr-token-update
[az-acr-token-credential-generate]: /cli/azure/acr/token/credential/#az-acr-token-credential-generate

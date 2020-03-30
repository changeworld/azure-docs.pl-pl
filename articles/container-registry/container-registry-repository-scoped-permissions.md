---
title: Uprawnienia do repozytoriów w rejestrze kontenerów platformy Azure
description: Tworzenie tokenu z uprawnieniami o zakresie określonymi repozytoriami w rejestrze w celu ściągania lub wypychania obrazów lub wykonywania innych akcji
ms.topic: article
ms.date: 02/13/2020
ms.openlocfilehash: 7d390bf4d97561e374c70f184534ac4f98a40611
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444315"
---
# <a name="create-a-token-with-repository-scoped-permissions"></a>Tworzenie tokenu z uprawnieniami o zakresie repozytorium

W tym artykule opisano sposób tworzenia tokenów i map zakresu w celu zarządzania uprawnieniami o zakresie repozytorium w rejestrze kontenerów. Tworząc tokeny, właściciel rejestru może zapewnić użytkownikom lub usługom ograniczony w zakresie, ograniczony w czasie dostęp do repozytoriów do ściągania lub wypychania obrazów lub wykonywania innych akcji. Token zapewnia bardziej szczegółowe uprawnienia niż inne [opcje uwierzytelniania](container-registry-authentication.md)rejestru , które uprawnienia zakresu do całego rejestru. 

Scenariusze tworzenia tokenu obejmują:

* Zezwalaj urządzeniom IoT z poszczególnymi tokenami na ściąganie obrazu z repozytorium
* Udostępnianie organizacji zewnętrznej z uprawnieniami do określonego repozytorium 
* Ogranicz dostęp do repozytorium do różnych grup użytkowników w organizacji. Na przykład zapewnij dostęp do zapisu i odczytu deweloperom, którzy tworzą obrazy przeznaczone dla określonych repozytoriów, oraz dostęp do odczytu zespołów, które wdrażają z tych repozytoriów.

> [!IMPORTANT]
> Ta funkcja jest obecnie w wersji zapoznawczej i obowiązują pewne [ograniczenia](#preview-limitations). Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania][terms-of-use]. Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.

## <a name="preview-limitations"></a>Ograniczenia wersji zapoznawczej

* Ta funkcja jest dostępna tylko w rejestrze kontenerów **premium.** Aby uzyskać informacje na temat warstw i limitów usług rejestru, zobacz [Jednostki SKU rejestru kontenerów platformy Azure](container-registry-skus.md).
* Obecnie nie można przypisać uprawnień o zakresie repozytorium do tożsamości usługi Azure Active Directory, takiej jak podmiot usługi lub tożsamość zarządzana.

## <a name="concepts"></a>Pojęcia

Aby skonfigurować uprawnienia o zakresie repozytorium, należy utworzyć *token* ze skojarzoną *mapą zakresu*. 

* **Token** wraz z wygenerowanym hasłem umożliwia użytkownikowi uwierzytelnić się w rejestrze. Można ustawić datę wygaśnięcia hasła tokenu lub wyłączyć token w dowolnym momencie.  

  Po uwierzytelnieniu za pomocą tokenu użytkownik lub usługa może wykonać jedną lub więcej *akcji* o zakresie do jednego lub więcej repozytoriów.

  |Akcja  |Opis  | Przykład |
  |---------|---------|--------|
  |`content/delete`    | Usuwanie danych z repozytorium  | Usuwanie repozytorium lub manifestu |
  |`content/read`     |  Odczytywanie danych z repozytorium |  Ciągnienie artefaktu |
  |`content/write`     |  Zapisywanie danych w repozytorium     | Użyj `content/read` z do pchania artefaktu |
  |`metadata/read`    | Odczytywanie metadanych z repozytorium   | Lista znaczników lub manifestów |
  |`metadata/write`     |  Zapisywanie metadanych w repozytorium  | Włączanie lub wyłączanie operacji odczytu, zapisu lub usuwania |

* **Mapa zakresu** grupuje uprawnienia repozytorium, które stosujesz do tokenu i może ponownie zastosować do innych tokenów. Każdy token jest skojarzony z mapą pojedynczego zakresu. 

   Z mapą zakresu:

    * Konfigurowanie wielu tokenów z identycznymi uprawnieniami do zestawu repozytoriów
    * Aktualizowanie uprawnień tokenu podczas dodawania lub usuwania akcji repozytorium na mapie zakresu lub stosowania innej mapy zakresu 

  Usługa Azure Container Registry udostępnia również kilka map zakresu zdefiniowanych przez system, które można zastosować, ze stałymi uprawnieniami we wszystkich repozytoriach.

Na poniższej ilustracji przedstawiono relację między tokenami a mapami zakresu. 

![Tokeny rejestru i mapy zakresu](media/container-registry-repository-scoped-permissions/token-scope-map-concepts.png)

## <a name="prerequisites"></a>Wymagania wstępne

* **Interfejsu wiersza polecenia interfejsu wiersza** polecenia platformy Azure — tworzenie tokenów i zarządzanie nimi są dostępne w wersji interfejsu wiersza polecenia interfejsu wiersza polecenia platformy Azure w wersji 2.0.76 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).
* **Docker** — aby uwierzytelnić się w rejestrze do ściągania lub wypychania obrazów, potrzebna jest lokalna instalacja platformy Docker. Platforma Docker dostarcza instrukcje na temat instalacji w systemach [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) i [Linux](https://docs.docker.com/engine/installation/#supported-platforms).
* **Rejestr kontenerów** — jeśli go nie masz, utwórz rejestr kontenerów premium w subskrypcji platformy Azure lub uaktualnij istniejący rejestr. Na przykład użyj [witryny Azure portal](container-registry-get-started-portal.md) lub interfejsu [wiersza polecenia platformy Azure](container-registry-get-started-azure-cli.md). 

## <a name="create-token---cli"></a>Tworzenie tokenu — wiersz polecenia

### <a name="create-token-and-specify-repositories"></a>Tworzenie tokenu i określanie repozytoriów

Utwórz token za pomocą polecenia [az acr token create.][az-acr-token-create] Podczas tworzenia tokenu można określić co najmniej jedno repozytoria i skojarzone akcje w każdym repozytorium. Repozytoria nie muszą być jeszcze w rejestrze. Aby utworzyć token, określając istniejącą mapę zakresu, zobacz następną sekcję.

Poniższy przykład tworzy token w *myregistry* rejestru z `samples/hello-world` następującymi `content/write` uprawnieniami do repozytorium: i `content/read`. Domyślnie polecenie ustawia domyślny stan `enabled`tokenu na , `disabled` ale stan można zaktualizować w dowolnym momencie.

```azurecli
az acr token create --name MyToken --registry myregistry \
  --repository samples/hello-world \
  content/write content/read
```

Dane wyjściowe zawiera szczegółowe informacje o tokenie, w tym dwa wygenerowane hasła. Zaleca się zapisanie haseł w bezpiecznym miejscu do późniejszego użycia do uwierzytelniania. Haseł nie można ponownie pobrać, ale można wygenerować nowe.

```console
{
  "creationDate": "2020-01-18T00:15:34.066221+00:00",
  "credentials": {
    "certificates": [],
    "passwords": [
      {
        "creationTime": "2020-01-18T00:15:52.837651+00:00",
        "expiry": null,
        "name": "password1",
        "value": "uH54BxxxxK7KOxxxxRbr26dAs8JXxxxx"
      },
      {
        "creationTime": "2020-01-18T00:15:52.837651+00:00",
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

Dane wyjściowe zawierają szczegółowe informacje o mapie zakresu utworzonego polecenia. Można użyć mapy zakresu, `MyToken-scope-map`tutaj o nazwie , aby zastosować te same akcje repozytorium do innych tokenów. Lub zaktualizuj mapę zakresu później, aby zmienić uprawnienia skojarzonych tokenów.

### <a name="create-token-and-specify-scope-map"></a>Tworzenie tokenu i określanie mapy zakresu

Alternatywnym sposobem utworzenia tokenu jest określenie istniejącej mapy zakresu. Jeśli nie masz jeszcze mapy zakresu, najpierw utwórz ją, określając repozytoria i skojarzone akcje. Następnie określ mapę zakresu podczas tworzenia tokenu. 

Aby utworzyć mapę zakresu, użyj polecenia [az acr scope-map create.][az-acr-scope-map-create] Następujące polecenie tworzy mapę zakresu z tymi `samples/hello-world` samymi uprawnieniami w repozytorium używanym wcześniej. 

```azurecli
az acr scope-map create --name MyScopeMap --registry myregistry \
  --repository samples/hello-world \
  content/write content/read \
  --description "Sample scope map"
```

Uruchom [az acr token create,][az-acr-token-create] aby utworzyć token, określając mapę zakresu *MyScopeMap.* Podobnie jak w poprzednim przykładzie, polecenie `enabled`ustawia domyślny stan tokenu na .

```azurecli
az acr token create --name MyToken \
  --registry myregistry \
  --scope-map MyScopeMap
```

Dane wyjściowe zawiera szczegółowe informacje o tokenie, w tym dwa wygenerowane hasła. Zaleca się zapisanie haseł w bezpiecznym miejscu do późniejszego użycia do uwierzytelniania. Haseł nie można ponownie pobrać, ale można wygenerować nowe.

## <a name="create-token---portal"></a>Tworzenie tokenu — portal

Za pomocą witryny Azure Portal można tworzyć tokeny i mapy zakresu. Podobnie jak `az acr token create` w przypadku polecenia CLI, można zastosować istniejącą mapę zakresu lub utworzyć mapę zakresu podczas tworzenia tokenu, określając jedno lub więcej repozytoriów i skojarzonych akcji. Repozytoria nie muszą być jeszcze w rejestrze. 

Poniższy przykład tworzy token i tworzy mapę zakresu z `samples/hello-world` następującymi uprawnieniami do repozytorium: `content/write` i `content/read`.

1. W portalu przejdź do rejestru kontenerów.
1. W obszarze **Usługi**wybierz **pozycję Tokeny (wersja zapoznawcza) > +Dodaj**.
  ![Tworzenie tokenu w portalu](media/container-registry-repository-scoped-permissions/portal-token-add.png)
1. Wprowadź nazwę tokenu.
1. W obszarze **Mapa zakresu**wybierz pozycję **Utwórz nowy**.
1. Skonfiguruj mapę zakresu:
    1. Wprowadź nazwę i opis mapy zakresu. 
    1. W obszarze **Repozytoria** `samples/hello-world`wprowadź i w obszarze `content/write` **Uprawnienia**wybierz `content/read` i . Następnie wybierz **+Dodaj**.  
    ![Tworzenie mapy zakresu w portalu](media/container-registry-repository-scoped-permissions/portal-scope-map-add.png)

    1. Po dodaniu repozytoriów i uprawnień wybierz pozycję **Dodaj,** aby dodać mapę zakresu.
1. Zaakceptuj domyślny **stan** tokenu **Włączone,** a następnie wybierz pozycję **Utwórz**.

Po zatwierdzeniu i utworzeniu tokenu szczegóły tokenu są wyświetlane na ekranie **Tokeny.**

### <a name="add-token-password"></a>Dodawanie hasła tokenu

Wygeneruj hasło po utworzeniu tokenu. Aby uwierzytelnić się w rejestrze, token musi być włączony i mieć prawidłowe hasło.

Można wygenerować jedno lub dwa hasła i ustawić datę wygaśnięcia dla każdego z nich. 

1. W portalu przejdź do rejestru kontenerów.
1. W obszarze **Usługi**wybierz **pozycję Tokeny (Wersja zapoznawcza)** i wybierz token.
1. W szczegółach tokenu wybierz **hasło1** lub **hasło2**i wybierz ikonę Generuj.
1. Na ekranie hasła opcjonalnie ustaw datę wygaśnięcia hasła i wybierz pozycję **Generuj**.
1. Po wygenerowaniu hasła skopiuj go i zapisz w bezpiecznym miejscu. Nie można pobrać wygenerowanego hasła po zamknięciu ekranu, ale można wygenerować nowe.

    ![Tworzenie hasła tokenu w portalu](media/container-registry-repository-scoped-permissions/portal-token-password.png)

## <a name="authenticate-with-token"></a>Uwierzytelnij się za pomocą tokenu

Gdy użytkownik lub usługa używa tokenu do uwierzytelniania za pomocą rejestru docelowego, udostępnia nazwę tokenu jako nazwę użytkownika i jedno z jego wygenerowanych haseł. Metoda uwierzytelniania zależy od skonfigurowanej akcji lub akcji skojarzonych z tokenem.

|Akcja  |Jak uwierzytelnić  |
  |---------|---------|
  |`content/delete`    | `az acr repository delete`w usłudze Azure CLI |
  |`content/read`     |  `docker login`<br/><br/>`az acr login`w usłudze Azure CLI  |
  |`content/write`     |  `docker login`<br/><br/>`az acr login`w usłudze Azure CLI     |
  |`metadata/read`    | `az acr repository show`<br/><br/>`az acr repository show-tags`<br/><br/>`az acr repository show-manifests`w usłudze Azure CLI   |
  |`metadata/write`     |  `az acr repository untag`<br/><br/>`az acr repository update`w usłudze Azure CLI |

## <a name="examples-use-token"></a>Przykłady: użyj tokenu

Poniższe przykłady używają tokenu utworzonego wcześniej w tym artykule do wykonywania typowych operacji na repozytorium: wypychanie i ściąganie obrazów, usuwanie obrazów i tagi repozytorium listy. Token został skonfigurowany początkowo z`content/write` uprawnieniami wypychania (i `content/read` akcjami) w `samples/hello-world` repozytorium.

### <a name="pull-and-tag-test-images"></a>Ściąganie i oznaczanie obrazów testowych

W poniższych przykładach `hello-world` pociągnij i `alpine` obrazy z usługi Docker Hub i oznacz je dla rejestru i repozytorium.

```bash
docker pull hello-world
docker pull alpine
docker tag hello-world myregistry.azurecr.io/samples/hello-world:v1
docker tag hello-world myregistry.azurecr.io/samples/alpine:v1
```

### <a name="authenticate-using-token"></a>Uwierzytelnij się przy użyciu tokenu

Uruchom, `docker login` aby uwierzytelnić się w rejestrze, Podaj nazwę tokenu jako nazwę użytkownika i podaj jedno z jego haseł. Token musi mieć `Enabled` stan.

Poniższy przykład jest sformatowany dla powłoki bash i zawiera wartości przy użyciu zmiennych środowiskowych.

```bash
TOKEN_NAME=MyToken
TOKEN_PWD=<token password>

echo $TOKEN_PWD | docker login --username $TOKEN_NAME --password-stdin myregistry.azurecr.io
```

Dane wyjściowe powinny wykazywać pomyślne uwierzytelnianie:

```console
Login Succeeded
```

### <a name="push-images-to-registry"></a>Wypychanie obrazów do rejestru

Po pomyślnym zalogowaniu spróbuj wypchnąć oznaczone obrazy do rejestru. Ponieważ token ma uprawnienia do wypychania obrazów do `samples/hello-world` repozytorium, następujące wypychanie powiedzie się:

```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

Token nie ma uprawnień do `samples/alpine` repozytorium, więc następująca próba `requested access to the resource is denied`wypychania kończy się niepowodzeniem z błędem podobnym do:

```bash
docker push myregistry.azurecr.io/samples/alpine:v1
```

### <a name="change-pushpull-permissions"></a>Zmienianie uprawnień wypychania/ściągania

Aby zaktualizować uprawnienia tokenu, zaktualizuj uprawnienia na mapie skojarzonego zakresu. Zaktualizowana mapa zakresu jest stosowana natychmiast do wszystkich skojarzonych tokenów. 

Na przykład `MyToken-scope-map` `content/write` zaktualizuj z `content/read` i akcje na `samples/alpine` repozytorium i usuń `content/write` akcję na `samples/hello-world` repozytorium.  

Aby użyć interfejsu wiersza polecenia platformy Azure, uruchom [aktualizację az acr scope-map,][az-acr-scope-map-update] aby zaktualizować mapę zakresu:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/alpine content/write content/read \
  --remove samples/hello-world content/write 
```

W witrynie Azure Portal:

1. Przejdź do rejestru kontenerów.
1. W obszarze **Usługi**wybierz pozycję **Mapy zakresu (Wersja zapoznawcza)** i wybierz mapę zakresu do zaktualizowania.
1. W obszarze **Repozytoria** `samples/alpine`wprowadź i w obszarze `content/write` **Uprawnienia**wybierz `content/read` i . Następnie wybierz **+Dodaj**.
1. W obszarze **Repozytoria**wybierz `samples/hello-world` i w obszarze `content/write` **Uprawnienia**usuń zaznaczenie . Następnie wybierz pozycję **Zapisz**.

Po zaktualizowaniu mapy zakresu następujące wypychanie powiedzie się:

```bash
docker push myregistry.azurecr.io/samples/alpine:v1
```

Ponieważ mapa zakresu ma `content/read` tylko `samples/hello-world` uprawnienia do repozytorium, `samples/hello-world` próba wypychania do repozytorium kończy się niepowodzeniem:
 
```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

Ściąganie obrazów z obu repozytoriów `content/read` powiedzie się, ponieważ mapa zakresu zapewnia uprawnienia do obu repozytoriów:

```bash
docker pull myregistry.azurecr.io/samples/alpine:v1
docker pull myregistry.azurecr.io/samples/hello-world:v1
```
### <a name="delete-images"></a>Usuwanie obrazów

Zaktualizuj mapę zakresu, `content/delete` dodając akcję do `alpine` repozytorium. Ta akcja umożliwia usunięcie obrazów w repozytorium lub usunięcie całego repozytorium.

Dla zwięzłości pokazujemy tylko [polecenie aktualizacji az acr scope-map,][az-acr-scope-map-update] aby zaktualizować mapę zakresu:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/alpine content/delete
``` 

Aby zaktualizować mapę zakresu za pomocą portalu, zobacz poprzednią sekcję.

Użyj następującego polecenia [az acr repozytorium delete,][az-acr-repository-delete] aby usunąć `samples/alpine` repozytorium. Aby usunąć obrazy lub repozytoria, token nie `docker login`uwierzytelnia się za pośrednictwem . Zamiast tego przekaż nazwę tokenu i hasło do polecenia. W poniższym przykładzie użyto zmiennych środowiskowych utworzonych wcześniej w artykule:

```azurecli
az acr repository delete \
  --name myregistry --repository samples/alpine \
  --username $TOKEN_NAME --password $TOKEN_PWD
```

### <a name="show-repo-tags"></a>Pokaż znaczniki repozytorium 

Zaktualizuj mapę zakresu, `metadata/read` dodając akcję do `hello-world` repozytorium. Ta akcja umożliwia odczytywanie manifestu i tagu danych w repozytorium.

Dla zwięzłości pokazujemy tylko [polecenie aktualizacji az acr scope-map,][az-acr-scope-map-update] aby zaktualizować mapę zakresu:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/hello-world metadata/read 
```  

Aby zaktualizować mapę zakresu za pomocą portalu, zobacz poprzednią sekcję.

Aby odczytać metadane w `samples/hello-world` repozytorium, uruchom polecenie [az acr repozytorium show-manifests][az-acr-repository-show-manifests] lub [az acr repozytorium show-tags.][az-acr-repository-show-tags] 

Aby odczytać metadane, token nie `docker login`uwierzytelnia się za pośrednictwem . Zamiast tego przekaż nazwę i hasło tokenu do jednego polecenia. W poniższym przykładzie użyto zmiennych środowiskowych utworzonych wcześniej w artykule:

```azurecli
az acr repository show-tags \
  --name myregistry --repository samples/hello-world \
  --username $TOKEN_NAME --password $TOKEN_PWD
```

Przykładowe dane wyjściowe:

```console
[
  "v1"
]
```
## <a name="manage-tokens-and-scope-maps"></a>Zarządzanie tokenami i mapami zakresu

### <a name="list-scope-maps"></a>Mapy zakresu listy

Użyj polecenia [az acr scope-map list ( acr scope-map list,][az-acr-scope-map-list] lub **ekranu Mapy zakresu (Podgląd)** w portalu, aby wyświetlić listę wszystkich map zakresu skonfigurowanych w rejestrze. Przykład:

```azurecli
az acr scope-map list \
  --registry myregistry --output table
```

Dane wyjściowe pokazują zdefiniowane mapy zakresu i kilka map zakresu zdefiniowanych przez system, których można użyć do skonfigurowania tokenów:

```
NAME                 TYPE           CREATION DATE         DESCRIPTION
-------------------  -------------  --------------------  ------------------------------------------------------------
_repositories_admin  SystemDefined  2020-01-20T09:44:24Z  Can perform all read, write and delete operations on the ...
_repositories_pull   SystemDefined  2020-01-20T09:44:24Z  Can pull any repository of the registry
_repositories_push   SystemDefined  2020-01-20T09:44:24Z  Can push to any repository of the registry
MyScopeMap           UserDefined    2019-11-15T21:17:34Z  Sample scope map
```

### <a name="show-token-details"></a>Pokaż szczegóły tokenu

Aby wyświetlić szczegóły tokenu, takie jak jego daty wygaśnięcia stanu i hasła, uruchom polecenie [az acr token show][az-acr-token-show] lub wybierz token na ekranie **Tokens (Preview)** w portalu. Przykład:

```azurecli
az acr scope-map show \
  --name MyScopeMap --registry myregistry
```

Użyj polecenia [az acr token list][az-acr-token-list] lub **ekranu Tokens (Preview)** w portalu, aby wyświetlić listę wszystkich tokenów skonfigurowanych w rejestrze. Przykład:

```azurecli
az acr token list --registry myregistry --output table
```

### <a name="generate-passwords-for-token"></a>Generowanie haseł do tokenu

Jeśli nie masz hasła tokenu lub chcesz wygenerować nowe hasła, uruchom polecenie [generowania tokenu az acr.][az-acr-token-credential-generate] 

Poniższy przykład generuje nową wartość dla hasła1 dla tokenu *MyToken,* z okresem wygaśnięcia 30 dni. Przechowuje hasło w zmiennej `TOKEN_PWD`środowiskowej . W tym przykładzie jest sformatowany dla powłoki bash.

```azurecli
TOKEN_PWD=$(az acr token credential generate \
  --name MyToken --registry myregistry --days 30 \
  --password1 --query 'passwords[0].value' --output tsv)
```

Aby użyć witryny Azure portal do generowania hasła tokenu, zobacz kroki w [Tworzenie tokenu — portal](#create-token---portal) wcześniej w tym artykule.

### <a name="update-token-with-new-scope-map"></a>Aktualizowanie tokenu za pomocą nowej mapy zakresu

Jeśli chcesz zaktualizować token z inną mapą zakresu, uruchom [az acr token update][az-acr-token-update] i określ nową mapę zakresu. Przykład:

```azurecli
az acr token update --name MyToken --registry myregistry \
  --scope-map MyNewScopeMap
```

W portalu na ekranie **Tokeny (podgląd)** wybierz token, a następnie w obszarze **mapy zakres**wybierz inną mapę zakresu.

> [!TIP]
> Po zaktualizowaniu tokenu z nową mapą zakresu można wygenerować nowe hasła tokenu. Użyj [polecenia generowania tokenu az acr][az-acr-token-credential-generate] lub ponownego wygenerowania hasła tokenu w witrynie Azure portal.

## <a name="disable-or-delete-token"></a>Wyłączanie lub usuwanie tokenu

Może być konieczne tymczasowe wyłączenie użycia poświadczeń tokenu dla użytkownika lub usługi. 

Korzystając z interfejsu wiersza polecenia platformy Azure, uruchom `status` `disabled`polecenie [aktualizacji tokenu az acr,][az-acr-token-update] aby ustawić na:

```azurecli
az acr token update --name MyToken --registry myregistry \
  --status disabled
```

W portalu wybierz token na ekranie **Tokeny (Podgląd)** i wybierz pozycję **Wyłączone** w obszarze **Stan**.

Aby usunąć token, aby trwale unieważnić dostęp przez każdego, kto używa jego poświadczeń, uruchom polecenie [az acr token delete.][az-acr-token-delete] 

```azurecli
az acr token delete --name MyToken --registry myregistry
```

W portalu wybierz token na ekranie **Tokeny (Podgląd)** i wybierz pozycję **Odrzuć**.

## <a name="next-steps"></a>Następne kroki

* Aby zarządzać mapami i tokenami zakresu, należy użyć dodatkowych poleceń w grupach poleceń [az acr scope-map][az-acr-scope-map] i [az acr token.][az-acr-token]
* Zobacz [omówienie uwierzytelniania,](container-registry-authentication.md) aby uzyskać inne opcje uwierzytelniania za pomocą rejestru kontenerów platformy Azure, w tym przy użyciu tożsamości usługi Azure Active Directory, jednostki usługi lub konta administratora.


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-repository]: /cli/azure/acr/repository/
[az-acr-repository-show-tags]: /cli/azure/acr/repository/#az-acr-repository-show-tags
[az-acr-repository-show-manifests]: /cli/azure/acr/repository/#az-acr-repository-show-manifests
[az-acr-repository-delete]: /cli/azure/acr/repository/#az-acr-repository-delete
[az-acr-scope-map]: /cli/azure/acr/scope-map/
[az-acr-scope-map-create]: /cli/azure/acr/scope-map/#az-acr-scope-map-create
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az-acr-scope-map-show
[az-acr-scope-map-show]: /cli/azure/acr/scope-map/#az-acr-scope-map-list
[az-acr-scope-map-update]: /cli/azure/acr/scope-map/#az-acr-scope-map-update
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az-acr-scope-map-list
[az-acr-token]: /cli/azure/acr/token/
[az-acr-token-show]: /cli/azure/acr/token/#az-acr-token-show
[az-acr-token-list]: /cli/azure/acr/token/#az-acr-token-list
[az-acr-token-delete]: /cli/azure/acr/token/#az-acr-token-delete
[az-acr-token-create]: /cli/azure/acr/token/#az-acr-token-create
[az-acr-token-update]: /cli/azure/acr/token/#az-acr-token-update
[az-acr-token-credential-generate]: /cli/azure/acr/token/credential/#az-acr-token-credential-generate

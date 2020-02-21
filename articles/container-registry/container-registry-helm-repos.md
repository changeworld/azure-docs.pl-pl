---
title: Przechowuj wykresy Helm
description: Dowiedz się, jak przechowywać wykresy Helm dla aplikacji Kubernetes przy użyciu repozytoriów w Azure Container Registry
ms.topic: article
ms.date: 01/28/2020
ms.openlocfilehash: 26588bb4dc3cf50656103b50d5d0559908a1ccb7
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2020
ms.locfileid: "77524635"
---
# <a name="push-and-pull-helm-charts-to-an-azure-container-registry"></a>Wypychanie i ściąganie wykresów Helm do usługi Azure Container Registry

Aby szybko zarządzać aplikacjami dla programu Kubernetes i wdrażać je, można użyć [Menedżera pakietów Helm Open Source][helm]. W przypadku Helm pakiety aplikacji są definiowane jako [wykresy](https://helm.sh/docs/topics/charts/), które są zbierane i przechowywane w [repozytorium wykresu Helm](https://helm.sh/docs/topics/chart_repository/).

W tym artykule pokazano, jak hostować wykresy Helm w repozytoriach w usłudze Azure Container Registry przy użyciu instalacji Helm 3 lub Helm 2. Na potrzeby tego przykładu można przechowywać istniejący wykres Helm z poziomu publicznego repozytorium o *stabilnej* Helm. W wielu scenariuszach utworzysz i przekażesz własne wykresy dla aplikacji, które tworzysz. Aby uzyskać więcej informacji na temat tworzenia własnych wykresów Helm, zobacz [przewodnik dewelopera szablonu wykresu][develop-helm-charts].

> [!IMPORTANT]
> Obsługa wykresów Helm w Azure Container Registry jest obecnie w wersji zapoznawczej. Wersje zapoznawcze są udostępniane w przypadku, gdy użytkownik wyrazi zgodę na uzupełniające [warunki użytkowania][terms-of-use]. Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.

## <a name="helm-3-or-helm-2"></a>Helm 3 lub Helm 2?

Aby przechowywać i instalować wykresy Helm oraz zarządzać nimi, należy użyć klienta Helm i interfejsu wiersza polecenia Helm. Główne wersje klienta Helm obejmują Helm 3 i Helm 2. Helm 3 obsługuje nowy format wykresu i nie instaluje już składnika serwera operacji do odczytu. Aby uzyskać szczegółowe informacje dotyczące różnic wersji, zobacz sekcję [często zadawane pytania dotyczące wersji](https://helm.sh/docs/faq/). Jeśli wcześniej wdrożono wykresy Helm 2, zobacz [Migrowanie Helm v2 do wersji v3](https://helm.sh/docs/topics/v2_v3_migration/).

Można użyć Helm 3 lub Helm 2 do hostowania wykresów Helm w Azure Container Registry, z przepływami pracy specyficznymi dla każdej wersji:

* [Helm 3 klienta](#use-the-helm-3-client) — użyj poleceń `helm chart`, aby zarządzać wykresami w rejestrze jako [artefakty OCI](container-registry-image-formats.md#oci-artifacts)
* [Helm 2 Client](#use-the-helm-2-client) — używanie poleceń [AZ ACR Helm][az-acr-helm] w interfejsie wiersza polecenia platformy Azure w celu dodania i zarządzania rejestrem kontenerów jako repozytorium wykresu Helm

### <a name="additional-information"></a>Dodatkowe informacje

* Zalecamy używanie przepływu pracy Helm 3 z natywnymi poleceniami `helm chart`, aby zarządzać wykresami jako artefakty OCI.
* Można użyć starszych poleceń [AZ ACR Helm][az-acr-helm] platformy Azure z interfejsem wiersza polecenia i przepływem pracy przy użyciu klienta i wykresów Helm 3. Jednak niektóre polecenia, takie jak `az acr helm list` nie są zgodne z wykresami Helm 3.
* Od Helm 3 polecenia [AZ ACR Helm][az-acr-helm] są obsługiwane głównie w celu zapewnienia zgodności z formatem wykresu i klienta Helm 2. Przyszłe opracowywanie tych poleceń nie jest obecnie planowane.

## <a name="use-the-helm-3-client"></a>Korzystanie z klienta Helm 3

### <a name="prerequisites"></a>Wymagania wstępne

- **Rejestr kontenerów platformy Azure** w ramach subskrypcji platformy Azure. W razie konieczności Utwórz rejestr przy użyciu [Azure Portal](container-registry-get-started-portal.md) lub [interfejsu wiersza polecenia platformy Azure](container-registry-get-started-azure-cli.md).
- **Helm klienta w wersji 3.0.0 lub nowszej** — Uruchom `helm version`, aby znaleźć bieżącą wersję. Aby uzyskać więcej informacji na temat instalowania i uaktualniania Helm, zobacz [Instalowanie Helm][helm-install].
- **Klaster Kubernetes** , w którym zostanie zainstalowany wykres Helm. W razie konieczności Utwórz [klaster usługi Azure Kubernetes][aks-quickstart]. 
- **Interfejs wiersza polecenia platformy Azure w wersji 2.0.71 lub nowszej** — aby znaleźć wersję, uruchom `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

### <a name="high-level-workflow"></a>Przepływ pracy wysokiego poziomu

**Helm 3** :

* Można utworzyć co najmniej jeden repozytoria Helm w usłudze Azure Container Registry
* Przechowuj wykresy Helm 3 w rejestrze jako [artefakty OCI](container-registry-image-formats.md#oci-artifacts). Obecnie obsługa systemu OCI w Helm 3 jest uznawana za *eksperymentalną*.
* Używanie `helm chart` poleceń bezpośrednio z interfejsu wiersza polecenia Helm do wypychania i ściągania wykresów Helm i zarządzania nimi w rejestrze
* Uwierzytelnij się w rejestrze za pośrednictwem interfejsu wiersza polecenia platformy Azure, który następnie automatycznie aktualizuje klienta usługi Helm przy użyciu identyfikatora URI i poświadczeń rejestru. Nie musisz określać ręcznie tych informacji rejestru, dlatego poświadczenia nie są ujawniane w historii poleceń.
* Użyj `helm install`, aby zainstalować wykresy do klastra Kubernetes z lokalnej pamięci podręcznej repozytorium.

Przykłady można znaleźć w poniższych sekcjach.

### <a name="enable-oci-support"></a>Włącz obsługę OCI

Ustaw następujące zmienne środowiskowe, aby włączyć obsługę OCI w kliencie Helm 3. Obecnie to wsparcie jest eksperymentalne. 

```console
export HELM_EXPERIMENTAL_OCI=1
```

### <a name="pull-an-existing-helm-package"></a>Ściąganie istniejącego pakietu Helm

Jeśli nie dodano jeszcze `stable` repozytorium wykresu Helm, uruchom polecenie `helm repo add`:

```console
helm repo add stable https://kubernetes-charts.storage.googleapis.com
```

Pobierz pakiet wykresu z repozytorium `stable` lokalnie. Na przykład Utwórz katalog lokalny, taki jak *~/ACR-Helm*, a następnie Pobierz istniejący pakiet wykresu *stabilnego/WordPress* . (Ten przykład i inne polecenia w tym artykule są sformatowane dla powłoki bash).

```console
mkdir ~/acr-helm && cd ~/acr-helm
helm pull stable/wordpress --untar
```

Polecenie `helm pull stable/wordpress` nie określiło określonej wersji, więc *Najnowsza* wersja została pobrana i rozskompresowana w podkatalogu `wordpress`.

### <a name="save-chart-to-local-registry-cache"></a>Zapisz wykres w lokalnej pamięci podręcznej rejestru

Zmień katalog na `wordpress` podkatalog, który zawiera pliki wykresu Helm. Następnie uruchom `helm chart save`, aby zapisać kopię wykresu lokalnie, a także utworzyć alias z w pełni kwalifikowaną nazwą rejestru i repozytorium docelowym i tagiem. 

W poniższym przykładzie nazwa rejestru to *mycontainerregistry*, repozytorium Target to *WordPress*, a znacznik wykresu docelowego to *Najnowsza*, ale zastępujący wartości dla danego środowiska:

```console
cd wordpress
helm chart save . wordpress:latest
helm chart save . mycontainerregistry.azurecr.io/helm/wordpress:latest
```

Uruchom `helm chart list`, aby potwierdzić, że Zapisano wykresy w lokalnej pamięci podręcznej rejestru. Dane wyjściowe są podobne do następujących:

```console
REF                                                      NAME            VERSION DIGEST  SIZE            CREATED
wordpress:latest                                         wordpress       8.1.0   5899db0 29.1 KiB        1 day 
mycontainerregistry.azurecr.io/helm/wordpress:latest     wordpress       8.1.0   5899db0 29.1 KiB        1 day 
```

### <a name="push-chart-to-azure-container-registry"></a>Wypchnij wykres do Azure Container Registry

Uruchom `helm chart push` polecenie w interfejsie wiersza polecenia Helm 3, aby wypchnąć wykres Helm do repozytorium w rejestrze kontenerów platformy Azure. Jeśli nie istnieje, repozytorium zostanie utworzone.

Najpierw użyj interfejsu wiersza polecenia platformy Azure [AZ ACR login][az-acr-login] , aby uwierzytelnić się w rejestrze:

```azurecli
az acr login --name mycontainerregistry
```

Wypchnij wykres do w pełni kwalifikowanego repozytorium docelowego:

```console
helm chart push mycontainerregistry.azurecr.io/helm/wordpress:latest
```

Po pomyślnym wypchnięciu dane wyjściowe są podobne do:

```console
The push refers to repository [mycontainerregistry.azurecr.io/helm/wordpress]
ref:     mycontainerregistry.azurecr.io/helm/wordpress:latest
digest:  5899db028dcf96aeaabdadfa5899db025899db025899db025899db025899db02
size:    29.1 KiB
name:    wordpress
version: 8.1.0
```

### <a name="list-charts-in-the-repository"></a>Wyświetlanie listy wykresów w repozytorium

Podobnie jak w przypadku obrazów przechowywanych w usłudze Azure Container Registry, można użyć polecenia [AZ ACR Repository][az-acr-repository] Commands, aby pokazać repozytoria obsługujące wykresy oraz Tagi wykresu i manifesty. 

Na przykład uruchom [AZ ACR Repository show][az-acr-repository-show] , aby wyświetlić właściwości repozytorium utworzonego w poprzednim kroku:

```azurecli
az acr repository show \
  --name mycontainerregistry \
  --repository helm/wordpress
```

Dane wyjściowe są podobne do następujących:

```console
{
  "changeableAttributes": {
    "deleteEnabled": true,
    "listEnabled": true,
    "readEnabled": true,
    "writeEnabled": true
  },
  "createdTime": "2020-01-29T16:54:30.1514833Z",
  "imageName": "helm/wordpress",
  "lastUpdateTime": "2020-01-29T16:54:30.4992247Z",
  "manifestCount": 1,
  "registry": "mycontainerregistry.azurecr.io",
  "tagCount": 1
}
```

Uruchom polecenie [AZ ACR Repository show-Manifests][az-acr-repository-show-manifests] , aby wyświetlić szczegóły wykresu przechowywanego w repozytorium. Na przykład:

```azurecli
az acr repository show-manifests \
  --name mycontainerregistry \
  --repository helm/wordpress --detail
```

Wynik, skrócony w tym przykładzie, pokazuje `configMediaType` `application/vnd.cncf.helm.config.v1+json`:

```console
[
  {
    [...]
    "configMediaType": "application/vnd.cncf.helm.config.v1+json",
    "createdTime": "2020-01-29T16:54:30.2382436Z",
    "digest": "sha256:xxxxxxxx51bc0807bfa97cb647e493ac381b96c1f18749b7388c24bbxxxxxxxxx",
    "imageSize": 29995,
    "lastUpdateTime": "2020-01-29T16:54:30.3492436Z",
    "mediaType": "application/vnd.oci.image.manifest.v1+json",
    "tags": [
      "latest"
    ]
  }
]
```

### <a name="pull-chart-to-local-cache"></a>Ściąganie wykresu do lokalnej pamięci podręcznej

Aby zainstalować wykres Helm do Kubernetes, wykres musi znajdować się w lokalnej pamięci podręcznej. W tym przykładzie należy najpierw uruchomić `helm chart remove`, aby usunąć istniejący wykres lokalny o nazwie `mycontainerregistry.azurecr.io/helm/wordpress:latest`:

```console
helm chart remove mycontainerregistry.azurecr.io/helm/wordpress:latest
```

Uruchom `helm chart pull`, aby pobrać wykres z usługi Azure Container Registry do lokalnej pamięci podręcznej:

```console
helm chart pull mycontainerregistry.azurecr.io/helm/wordpress:latest
```

### <a name="export-helm-chart"></a>Eksportuj wykres Helm

Aby kontynuować, wyeksportuj go do katalogu lokalnego przy użyciu `helm chart export`. Na przykład wyeksportuj wykres, który został pobrany do `install` katalogu:

```console
helm chart export mycontainerregistry.azurecr.io/helm/wordpress:latest --destination ./install
```

Aby wyświetlić informacje dotyczące wyeksportowanego wykresu w repozytorium, uruchom polecenie `helm inspect chart` w katalogu, w którym został wyeksportowany wykres.

```console
cd install
helm inspect chart wordpress
```

Jeśli numer wersji nie zostanie podany, używana jest *Najnowsza* wersja. Helm zwraca szczegółowe informacje o wykresie, jak pokazano w następujących wąskich danych wyjściowych:

```
apiVersion: v1
appVersion: 5.3.2
dependencies:
- condition: mariadb.enabled
  name: mariadb
  repository: https://kubernetes-charts.storage.googleapis.com/
  tags:
  - wordpress-database
  version: 7.x.x
description: Web publishing platform for building blogs and websites.
home: http://www.wordpress.com/
icon: https://bitnami.com/assets/stacks/wordpress/img/wordpress-stack-220x234.png
keywords:
- wordpress
- cms
- blog
- http
- web
- application
- php
maintainers:
- email: containers@bitnami.com
  name: Bitnami
name: wordpress
sources:
- https://github.com/bitnami/bitnami-docker-wordpress
version: 8.1.0
```

### <a name="install-helm-chart"></a>Zainstaluj wykres Helm

Uruchom `helm install`, aby zainstalować wykres Helm, który został pobrany do lokalnej pamięci podręcznej i wyeksportowany. Określ nazwę wydania lub Przekaż parametr `--generate-name`. Na przykład:

```console
helm install wordpress --generate-name
```

Po zakończeniu instalacji postępuj zgodnie z instrukcjami w danych wyjściowych polecenia, aby wyświetlić adresy URL i poświadczenia WorPress. Możesz również uruchomić polecenie `kubectl get pods`, aby wyświetlić zasoby Kubernetes wdrożone za pomocą wykresu Helm:

```console
NAME                                    READY   STATUS    RESTARTS   AGE
wordpress-1598530621-67c77b6d86-7ldv4   1/1     Running   0          2m48s
wordpress-1598530621-mariadb-0          1/1     Running   0          2m48s
[...]
```

### <a name="delete-a-helm-chart-from-the-repository"></a>Usuwanie wykresu Helm z repozytorium

Aby usunąć wykres z repozytorium, użyj polecenia [AZ ACR Repository Delete][az-acr-repository-delete] . Uruchom następujące polecenie i potwierdź operację po wyświetleniu monitu:

```azurecli
az acr repository delete --name mycontainerregistry --image helm/wordpress:latest
```

## <a name="use-the-helm-2-client"></a>Korzystanie z klienta Helm 2

### <a name="prerequisites"></a>Wymagania wstępne

- **Rejestr kontenerów platformy Azure** w ramach subskrypcji platformy Azure. W razie konieczności Utwórz rejestr przy użyciu [Azure Portal](container-registry-get-started-portal.md) lub [interfejsu wiersza polecenia platformy Azure](container-registry-get-started-azure-cli.md).
- **Helm Client Version 2.11.0 (nie wersja RC) ani nowsza** — Uruchom `helm version`, aby znaleźć bieżącą wersję. Wymagany jest również serwer Helm (er) zainicjowany w klastrze Kubernetes. W razie konieczności Utwórz [klaster usługi Azure Kubernetes][aks-quickstart]. Aby uzyskać więcej informacji na temat instalowania i uaktualniania Helm, zobacz [Instalowanie Helm][helm-install-v2].
- **Interfejs wiersza polecenia platformy Azure w wersji 2.0.46 lub nowszej** — aby znaleźć wersję, uruchom `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

### <a name="high-level-workflow"></a>Przepływ pracy wysokiego poziomu

Z **Helm 2** :

* Skonfiguruj usługę Azure Container Registry jako *pojedyncze* repozytorium wykresu Helm. Azure Container Registry zarządza definicją indeksu podczas dodawania i usuwania wykresów do repozytorium.
* Użyj poleceń [AZ ACR Helm][az-acr-helm] w interfejsie wiersza polecenia platformy Azure, aby dodać usługę Azure Container Registry jako repozytorium wykresu Helm oraz do wypychania wykresów i zarządzania nimi. Te polecenia platformy Azure są zawijane Helm 2 polecenia klienta.
* Dodaj repozytorium wykresu w usłudze Azure Container Registry do lokalnego indeksu repozytorium Helm, obsługujący wyszukiwanie wykresów
* Uwierzytelnij się w usłudze Azure Container Registry za pomocą interfejsu wiersza polecenia platformy Azure, który następnie automatycznie aktualizuje klienta usługi Helm przy użyciu identyfikatora URI i poświadczeń rejestru. Nie musisz określać ręcznie tych informacji rejestru, dlatego poświadczenia nie są ujawniane w historii poleceń.
* Użyj `helm install`, aby zainstalować wykresy do klastra Kubernetes z lokalnej pamięci podręcznej repozytorium.

Przykłady można znaleźć w poniższych sekcjach.

### <a name="add-repository-to-helm-client"></a>Dodawanie repozytorium do klienta Helm

Dodaj Azure Container Registry repozytorium wykresu Helm do klienta Helm przy użyciu polecenia [AZ ACR Helm Repository Add][az-acr-helm-repo-add] . To polecenie pobiera token uwierzytelniania dla rejestru kontenerów platformy Azure, który jest używany przez klienta Helm. Token uwierzytelniania jest ważny przez 3 godziny. Podobnie jak w przypadku `docker login`, można uruchomić to polecenie w przyszłych sesjach interfejsu wiersza polecenia w celu uwierzytelnienia klienta Helm za pomocą repozytorium Azure Container Registry Helm Chart:

```azurecli
az acr helm repo add --name mycontainerregistry
```

### <a name="add-a-chart-to-the-repository"></a>Dodawanie wykresu do repozytorium

Najpierw Utwórz katalog lokalny w lokalizacji *~/ACR-Helm*, a następnie Pobierz istniejący wykres *stabilny/WordPress* :

```console
mkdir ~/acr-helm && cd ~/acr-helm
helm repo update
helm fetch stable/wordpress
```

Wpisz `ls`, aby wyświetlić pobrany wykres, i zanotuj wersję WordPress zawartą w nazwie pliku. Polecenie `helm fetch stable/wordpress` nie określiło określonej wersji, więc została pobrana *Najnowsza* wersja. W poniższym przykładzie danych wyjściowych wykres WordPress jest w wersji *8.1.0*:

```
wordpress-8.1.0.tgz
```

Wypchnij wykres do repozytorium wykresu Helm w Azure Container Registry przy użyciu polecenia [AZ ACR Helm push][az-acr-helm-push] w interfejsie wiersza poleceń platformy Azure. Określ nazwę wykresu Helm pobranego w poprzednim kroku, na przykład *WordPress-8.1.0. tgz*:

```azurecli
az acr helm push --name mycontainerregistry wordpress-8.1.0.tgz
```

Po kilku chwilach interfejs wiersza polecenia platformy Azure zgłasza, że wykres jest zapisywany, jak pokazano w poniższym przykładzie danych wyjściowych:

```
{
  "saved": true
}
```

### <a name="list-charts-in-the-repository"></a>Wyświetlanie listy wykresów w repozytorium

Aby użyć wykresu przekazanego w poprzednim kroku, należy zaktualizować indeks lokalnego repozytorium Helm. Można ponownie indeksować repozytoria w kliencie Helm lub użyć interfejsu wiersza polecenia platformy Azure, aby zaktualizować indeks repozytorium. Za każdym razem, gdy dodajesz wykres do repozytorium, ten krok należy wykonać:

```azurecli
az acr helm repo add --name mycontainerregistry
```

Na wykresie przechowywanym w repozytorium i zaktualizowanym indeksie dostępnym lokalnie można użyć zwykłych poleceń klienta Helm do przeszukiwania lub instalacji. Aby wyświetlić wszystkie wykresy w repozytorium, użyj polecenia `helm search`, podając własną nazwę Azure Container Registry:

```console
helm search mycontainerregistry
```

Wykres WordPress wypychany w poprzednim kroku znajduje się na liście, jak pokazano w następujących przykładowych danych wyjściowych:

```
NAME                CHART VERSION   APP VERSION DESCRIPTION
helmdocs/wordpress  8.1.0           5.3.2       Web publishing platform for building blogs and websites.
```

Możesz również wyświetlić listę wykresów za pomocą interfejsu wiersza polecenia platformy Azure, korzystając z polecenia [AZ ACR Helm list][az-acr-helm-list]:

```azurecli
az acr helm list --name mycontainerregistry
```

### <a name="show-information-for-a-helm-chart"></a>Pokaż informacje dla wykresu Helm

Aby wyświetlić informacje dotyczące określonego wykresu w repozytorium, można użyć `helm inspect` polecenie.

```console
helm inspect mycontainerregistry/wordpress
```

Jeśli numer wersji nie zostanie podany, używana jest *Najnowsza* wersja. Helm zwraca szczegółowe informacje o wykresie, jak pokazano w następujących wąskich przykładowych danych wyjściowych:

```
apiVersion: v1
appVersion: 5.3.2
description: Web publishing platform for building blogs and websites.
engine: gotpl
home: http://www.wordpress.com/
icon: https://bitnami.com/assets/stacks/wordpress/img/wordpress-stack-220x234.png
keywords:
- wordpress
- cms
- blog
- http
- web
- application
- php
maintainers:
- email: containers@bitnami.com
  name: Bitnami
name: wordpress
sources:
- https://github.com/bitnami/bitnami-docker-wordpress
version: 8.1.0
[...]
```

Możesz również wyświetlić informacje dla wykresu za pomocą interfejsu wiersza polecenia platformy Azure [AZ ACR Helm show][az-acr-helm-show] . Ponownie *Najnowsza* wersja wykresu jest zwracana domyślnie. Możesz dołączyć `--version`, aby wyświetlić listę określonej wersji wykresu, na przykład *8.1.0*:

```azurecli
az acr helm show --name mycontainerregistry wordpress
```

### <a name="install-a-helm-chart-from-the-repository"></a>Instalowanie wykresu Helm z repozytorium

Wykres Helm w repozytorium jest instalowany przez określenie nazwy repozytorium i nazwy wykresu. Użyj klienta Helm do zainstalowania wykresu WordPress:

```console
helm install mycontainerregistry/wordpress
```

> [!TIP]
> W przypadku wypychania do repozytorium Azure Container Registry Helm i późniejszego powrotu w nowej sesji interfejsu wiersza polecenia klient lokalny Helm musi mieć zaktualizowany token uwierzytelniania. Aby uzyskać nowy token uwierzytelniania, użyj polecenia [AZ ACR Helm repo Add][az-acr-helm-repo-add] .

Następujące kroki są wykonywane podczas procesu instalacji:

- Klient Helm przeszukuje lokalny indeks repozytorium.
- Odpowiedni wykres zostanie pobrany z repozytorium Azure Container Registry.
- Wykres jest wdrażany przy użyciu narzędzia do przydziałania w klastrze Kubernetes.

Po zakończeniu instalacji postępuj zgodnie z instrukcjami w danych wyjściowych polecenia, aby wyświetlić adresy URL i poświadczenia WorPress. Możesz również uruchomić polecenie `kubectl get pods`, aby wyświetlić zasoby Kubernetes wdrożone za pomocą wykresu Helm:

```
NAME                                    READY   STATUS    RESTARTS   AGE
wordpress-1598530621-67c77b6d86-7ldv4   1/1     Running   0          2m48s
wordpress-1598530621-mariadb-0          1/1     Running   0          2m48s
[...]
```

### <a name="delete-a-helm-chart-from-the-repository"></a>Usuwanie wykresu Helm z repozytorium

Aby usunąć wykres z repozytorium, użyj polecenia [AZ ACR Helm Delete][az-acr-helm-delete] . Określ nazwę wykresu, na przykład *WordPress*, i wersję do usunięcia, taką jak *8.1.0*.

```azurecli
az acr helm delete --name mycontainerregistry wordpress --version 8.1.0
```

Jeśli chcesz usunąć wszystkie wersje nazwanego wykresu, pozostaw parametr `--version`.

Wykres jest nadal zwracany po uruchomieniu `helm search`. Klient Helm nie aktualizuje automatycznie listy dostępnych wykresów w repozytorium. Aby zaktualizować indeks repozytorium klienta Helm, należy ponownie użyć polecenia [AZ ACR Helm repozytorium Add][az-acr-helm-repo-add] :

```azurecli
az acr helm repo add --name mycontainerregistry
```

## <a name="next-steps"></a>Następne kroki

W tym artykule użyto istniejącego wykresu Helm z poziomu publicznego repozytorium *stabilne* . Aby uzyskać więcej informacji na temat tworzenia i wdrażania wykresów Helm, zobacz [Tworzenie wykresów Helm][develop-helm-charts].

Wykresy Helm mogą być używane jako część procesu tworzenia kontenera. Aby uzyskać więcej informacji, zobacz [używanie Azure Container Registry zadań][acr-tasks].

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-install]: https://helm.sh/docs/intro/install/
[helm-install-v2]: https://v2.helm.sh/docs/using_helm/#installing-helm
[develop-helm-charts]: https://helm.sh/docs/chart_template_guide/
[semver2]: https://semver.org/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-quickstart]: ../aks/kubernetes-walkthrough.md
[acr-bestpractices]: container-registry-best-practices.md
[az-configure]: /cli/azure/reference-index#az-configure
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-helm]: /cli/azure/acr/helm
[az-acr-repository]: /cli/azure/acr/repository
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[az-acr-helm-repo-add]: /cli/azure/acr/helm/repo#az-acr-helm-repo-add
[az-acr-helm-push]: /cli/azure/acr/helm#az-acr-helm-push
[az-acr-helm-list]: /cli/azure/acr/helm#az-acr-helm-list
[az-acr-helm-show]: /cli/azure/acr/helm#az-acr-helm-show
[az-acr-helm-delete]: /cli/azure/acr/helm#az-acr-helm-delete
[acr-tasks]: container-registry-tasks-overview.md

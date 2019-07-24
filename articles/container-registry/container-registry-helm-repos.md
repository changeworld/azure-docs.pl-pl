---
title: Używanie repozytoriów Helm w Azure Container Registry
description: Dowiedz się, jak używać repozytorium Helm z Azure Container Registry, aby przechowywać wykresy dla aplikacji
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 09/24/2018
ms.author: iainfou
ms.openlocfilehash: 2135a3a5a8f14cf6c2e7fd2984d9b221e2445c1d
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2019
ms.locfileid: "68309510"
---
# <a name="use-azure-container-registry-as-a-helm-repository-for-your-application-charts"></a>Używanie Azure Container Registry jako repozytorium Helm dla wykresów aplikacji

Aby szybko zarządzać aplikacjami dla programu Kubernetes i wdrażać je, można użyć [Menedżera pakietów Helm Open Source][helm]. W przypadku Helm aplikacje są definiowane jako *wykresy* , które są przechowywane w repozytorium wykresu Helm. Te wykresy definiują konfiguracje i zależności i mogą być używane w całym cyklu życia aplikacji. Azure Container Registry można używać jako hosta dla repozytoriów wykresów Helm.

Za pomocą Azure Container Registry masz prywatne, bezpieczne repozytorium wykresów Helm, które można zintegrować z potokami kompilacji lub innymi usługami platformy Azure. Repozytoria wykresów Helm w Azure Container Registry obejmują funkcje replikacji geograficznej, aby zachować wykresy w pobliżu wdrożeń i w celu zapewnienia nadmiarowości. Płacisz tylko za magazyn używany przez wykresy i są dostępne we wszystkich Azure Container Registry warstwach cenowych.

W tym artykule pokazano, jak używać repozytorium wykresu Helm przechowywanego w Azure Container Registry.

> [!IMPORTANT]
> Ta funkcja jest obecnie dostępna w wersji zapoznawczej. Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania][terms-of-use]. Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby wykonać kroki opisane w tym artykule, należy spełnić następujące wymagania wstępne:

- **Azure Container Registry** — Utwórz rejestr kontenerów w ramach subskrypcji platformy Azure. Na przykład użyj [Azure Portal](container-registry-get-started-portal.md) lub [interfejsu wiersza polecenia platformy Azure](container-registry-get-started-azure-cli.md).
- **Helm Client Version 2.11.0 (nie wersję RC) lub nowszą** — Uruchom `helm version` , aby znaleźć bieżącą wersję. Wymagany jest również serwer Helm (er) zainicjowany w klastrze Kubernetes. W razie konieczności można. For more information on how to install and upgrade Helm, see [Installing Helm][helm-install] [utworzyć klaster usługi Azure Kubernetes][aks-quickstart].
- **Interfejs wiersza polecenia platformy Azure w wersji 2.0.46 lub nowszej** — Uruchom `az --version` , aby znaleźć wersję. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

## <a name="add-a-repository-to-helm-client"></a>Dodawanie repozytorium do klienta Helm

Repozytorium Helm to serwer HTTP, który może przechowywać wykresy Helm. Azure Container Registry może udostępnić ten magazyn dla wykresów Helm i zarządzać definicją indeksu podczas dodawania i usuwania wykresów do repozytorium.

Aby dodać Azure Container Registry jako repozytorium wykresu Helm, użyj interfejsu wiersza polecenia platformy Azure. W tym podejściu klient Helm zostanie zaktualizowany przy użyciu identyfikatora URI i poświadczeń dla repozytorium, którego kopia zapasowa jest wykonywana przez Azure Container Registry. Nie musisz ręcznie podawać informacji o tym repozytorium, więc poświadczenia nie są ujawniane w historii poleceń, na przykład.

W razie potrzeby zaloguj się do interfejsu wiersza polecenia platformy Azure i postępuj zgodnie z monitami:

```azurecli
az login
```

Skonfiguruj wartości domyślne interfejsu wiersza polecenia platformy Azure przy użyciu nazwy Azure Container Registry za pomocą polecenia [AZ Configure][az-configure] . W poniższym przykładzie Zastąp `<acrName>` ciąg nazwą rejestru:

```azurecli
az configure --defaults acr=<acrName>
```

Teraz Dodaj Azure Container Registry repozytorium wykresu Helm do klienta Helm przy użyciu polecenia [AZ ACR Helm Repository Add][az-acr-helm-repo-add] . To polecenie pobiera token uwierzytelniania dla rejestru kontenerów platformy Azure, który jest używany przez klienta Helm. Token uwierzytelniania jest ważny przez 1 godzinę. Podobnie jak w przypadku programu, można uruchomić to polecenie w przyszłych sesjach interfejsu wiersza polecenia w celu uwierzytelnienia klienta Helm za pomocą repozytorium Azure Container Registry Helm Chart: `docker login`

```azurecli
az acr helm repo add
```

## <a name="add-a-chart-to-the-repository"></a>Dodawanie wykresu do repozytorium

Na potrzeby tego artykułu Przyjrzyjmy się istniejącemu wykresowi Helm z poziomu  publicznego repozytorium Helme. *Stałe* repozytorium jest nadzorowanym, publicznym repozytorium zawierającym popularne wykresy aplikacji. Obsłużenia pakietów mogą przesyłać swoje wykresy do *stabilnego* repozytorium, tak samo, jak w przypadku usługi Docker Hub udostępniono Publiczny rejestr dla popularnych obrazów kontenerów. Wykres pobrany z publicznego trwałego  repozytorium można następnie wypchnąć do prywatnego repozytorium Azure Container Registry. W większości scenariuszy utworzysz i przekażesz własne wykresy dla aplikacji, które tworzysz. Aby uzyskać więcej informacji na temat tworzenia własnych wykresów Helm, zobacz [Tworzenie wykresów Helm][develop-helm-charts].

Najpierw Utwórz katalog w lokalizacji *~/ACR-Helm*, a następnie Pobierz istniejący wykres *stabilny/WordPress* :

```console
mkdir ~/acr-helm && cd ~/acr-helm
helm fetch stable/wordpress
```

Wyświetl listę pobranego wykresu i zanotuj wersję WordPress zawartą w nazwie pliku. Polecenie nie określiło określonej wersji, więc została pobrana najnowsza wersja.  `helm fetch stable/wordpress` Wszystkie wykresy Helm zawierają numer wersji w pliku, który jest zgodny ze standardem [SemVer 2][semver2] . W poniższym przykładzie danych wyjściowych wykres WordPress jest w wersji *2.1.10*:

```
$ ls

wordpress-2.1.10.tgz
```

Teraz wypchnij wykres do repozytorium wykresu Helm w Azure Container Registry przy użyciu polecenia platformy Azure w INTERFEJSie użytkownika [AZ ACR Helm push][az-acr-helm-push] . Określ nazwę wykresu Helm pobranego w poprzednim kroku, na przykład *WordPress-2.1.10. tgz*:

```azurecli
az acr helm push wordpress-2.1.10.tgz
```

Po kilku chwilach interfejs wiersza polecenia platformy Azure zgłasza, że wykres został zapisany, jak pokazano w poniższym przykładzie danych wyjściowych:

```
$ az acr helm push wordpress-2.1.10.tgz

{
  "saved": true
}
```

## <a name="list-charts-in-the-repository"></a>Wyświetlanie listy wykresów w repozytorium

Klient Helm przechowuje w pamięci podręcznej lokalną kopię zawartości repozytoriów zdalnych. Zmiany w repozytorium zdalnym nie aktualizują automatycznie listy dostępnych wykresów znanych lokalnie przez klienta Helm. Podczas wyszukiwania wykresów w ramach repozytoriów Helm używa jej lokalnego indeksu pamięci podręcznej. Aby użyć wykresu przekazanego w poprzednim kroku, należy zaktualizować indeks lokalnego repozytorium Helm. Można ponownie indeksować repozytoria w kliencie Helm lub użyć interfejsu wiersza polecenia platformy Azure, aby zaktualizować indeks repozytorium. Za każdym razem, gdy dodajesz wykres do repozytorium, ten krok należy wykonać:

```azurecli
az acr helm repo add
```

Na wykresie przechowywanym w repozytorium i zaktualizowanym indeksie dostępnym lokalnie można użyć zwykłych poleceń klienta Helm do przeszukiwania lub instalacji. Aby wyświetlić wszystkie wykresy w repozytorium, użyj `helm search <acrName>`. Podaj własną nazwę Azure Container Registry:

```console
helm search <acrName>
```

Wykres WordPress wypychany w poprzednim kroku znajduje się na liście, jak pokazano w następujących przykładowych danych wyjściowych:

```
$ helm search myacrhelm

NAME                CHART VERSION   APP VERSION DESCRIPTION
helmdocs/wordpress  2.1.10          4.9.8       Web publishing platform for building blogs and websites.
```

Możesz również wyświetlić listę wykresów za pomocą interfejsu wiersza polecenia platformy Azure, korzystając z polecenia [AZ ACR Helm list][az-acr-helm-list]:

```azurecli
az acr helm list
```

## <a name="show-information-for-a-helm-chart"></a>Pokaż informacje dla wykresu Helm

Aby wyświetlić informacje dotyczące określonego wykresu w repozytorium, możesz ponownie użyć zwykłego klienta Helm. Aby wyświetlić informacje o wykresie o nazwie *WordPress*, użyj `helm inspect`.

```console
helm inspect <acrName>/wordpress
```

Jeśli numer wersji nie zostanie podany, używana jest *Najnowsza* wersja. Helm zwraca szczegółowe informacje o wykresie, jak pokazano w następujących wąskich przykładowych danych wyjściowych:

```
$ helm inspect myacrhelm/wordpress

appVersion: 4.9.8
description: Web publishing platform for building blogs and websites.
engine: gotpl
home: https://www.wordpress.com/
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
  name: bitnami-bot
name: wordpress
sources:
- https://github.com/bitnami/bitnami-docker-wordpress
version: 2.1.10
[...]
```

Możesz również wyświetlić informacje dla wykresu za pomocą interfejsu wiersza polecenia platformy Azure [AZ ACR Helm show][az-acr-helm-show] . Ponownie *Najnowsza* wersja wykresu jest zwracana domyślnie. Możesz dołączyć `--version` do listy określoną wersję wykresu, na przykład *2.1.10*:

```azurecli
az acr helm show wordpress
```

## <a name="install-a-helm-chart-from-the-repository"></a>Instalowanie wykresu Helm z repozytorium

Wykres Helm w repozytorium jest instalowany przez określenie nazwy repozytorium i nazwy wykresu. Użyj klienta Helm do zainstalowania wykresu WordPress:

```console
helm install <acrName>/wordpress
```

> [!TIP]
> W przypadku wypychania do repozytorium Azure Container Registry Helm i późniejszego powrotu w nowej sesji interfejsu wiersza polecenia klient lokalny Helm musi mieć zaktualizowany token uwierzytelniania. Aby uzyskać nowy token uwierzytelniania, użyj polecenia [AZ ACR Helm repo Add][az-acr-helm-repo-add] .

Następujące kroki są wykonywane podczas procesu instalacji:

- Klient Helm przeszukuje lokalny indeks repozytorium.
- Odpowiedni wykres zostanie pobrany z repozytorium Azure Container Registry.
- Wykres jest wdrażany przy użyciu narzędzia do przydziałania w klastrze Kubernetes.

Następujące wąskie przykładowe dane wyjściowe przedstawiają zasoby Kubernetes wdrożone za pomocą wykresu Helm:

```
$ helm install myacrhelm/wordpress

NAME:   irreverent-jaguar
LAST DEPLOYED: Thu Sep 13 21:44:20 2018
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                                          READY  STATUS   RESTARTS  AGE
irreverent-jaguar-wordpress-7ff46d9b8c-b7v6m  0/1    Pending  0         1s
irreverent-jaguar-mariadb-0                   0/1    Pending  0         1s
[...]
```

## <a name="delete-a-helm-chart-from-the-repository"></a>Usuwanie wykresu Helm z repozytorium

Aby usunąć wykres z repozytorium, użyj polecenia [AZ ACR Helm Delete][az-acr-helm-delete] . Określ nazwę wykresu, na przykład *WordPress*, i wersję do usunięcia, taką jak *2.1.10*.

```azurecli
az acr helm delete wordpress --version 2.1.10
```

Jeśli chcesz usunąć wszystkie wersje nazwanego wykresu, pozostaw `--version` parametr.

Wykres jest nadal zwracany w `helm search <acrName>`. Klient Helm nie aktualizuje automatycznie listy dostępnych wykresów w repozytorium. Aby zaktualizować indeks repozytorium klienta Helm, należy ponownie użyć polecenia [AZ ACR Helm repozytorium Add][az-acr-helm-repo-add] :

```azurecli
az acr helm repo add
```

## <a name="next-steps"></a>Następne kroki

W tym artykule użyto istniejącego wykresu Helm z poziomu publicznego  repozytorium stabilne. Aby uzyskać więcej informacji na temat tworzenia i wdrażania wykresów Helm, zobacz [Tworzenie wykresów Helm][develop-helm-charts].

Wykresy Helm mogą być używane jako część procesu tworzenia kontenera. Aby uzyskać więcej informacji, zobacz [używanie Azure Container Registry zadań][acr-tasks].

Aby uzyskać więcej informacji na temat używania Azure Container Registry i zarządzania nimi, zobacz [najlepsze rozwiązania][acr-bestpractices].

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm
[develop-helm-charts]: https://docs.helm.sh/developing_charts/
[semver2]: https://semver.org/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-quickstart]: ../aks/kubernetes-walkthrough.md
[acr-bestpractices]: container-registry-best-practices.md
[az-configure]: /cli/azure/reference-index#az-configure
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-helm-repo-add]: /cli/azure/acr/helm/repo#az-acr-helm-repo-add
[az-acr-helm-push]: /cli/azure/acr/helm#az-acr-helm-push
[az-acr-helm-list]: /cli/azure/acr/helm#az-acr-helm-list
[az-acr-helm-show]: /cli/azure/acr/helm#az-acr-helm-show
[az-acr-helm-delete]: /cli/azure/acr/helm#az-acr-helm-delete
[acr-tasks]: container-registry-tasks-overview.md

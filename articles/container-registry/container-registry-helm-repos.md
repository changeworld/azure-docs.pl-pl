---
title: Użyj narzędzia Helm repozytoriów w usłudze Azure Container Registry
description: Dowiedz się, jak używać repozytorium narzędzia Helm przy użyciu usługi Azure Container Registry do przechowywania wykresy dla aplikacji
services: container-registry
author: iainfoulds
ms.service: container-registry
ms.topic: article
ms.date: 09/24/2018
ms.author: iainfou
ms.openlocfilehash: ba0e1386d67e920f1805d244f9042044bb462ec9
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62109855"
---
# <a name="use-azure-container-registry-as-a-helm-repository-for-your-application-charts"></a>Używać usługi Azure Container Registry jako repozytorium narzędzia Helm dla wykresów aplikacji

Aby szybko zarządzania i wdrażania aplikacji dla platformy Kubernetes, możesz użyć [Menedżera pakietów narzędzia Helm typu open-source][helm]. Za pomocą narzędzia Helm, aplikacje są definiowane jako *wykresy* , są przechowywane w repozytorium pakietów narzędzia Helm. Te wykresy konfiguracji i zależności są definiowane i może być wersjonowany w całym cyklu życia aplikacji. Usługa Azure Container Registry może służyć jako host dla repozytoriów wykresu Helm.

Dzięki usłudze Azure Container Registry masz prywatna, bezpieczna repozytorium narzędzia Helm wykres, który można zintegrować z potoki kompilacji lub innych usług platformy Azure. Helm repozytoriach w usłudze Azure Container Registry obejmują funkcje replikacji geograficznej zapewnienie wykresy blisko wdrożenia i w celu zapewnienia nadmiarowości. Tylko płać za używany Magazyn używany przez wykresy i są dostępne we wszystkich warstwach cena usługi Azure Container Registry.

W tym artykule pokazano, jak używać repozytorium pakietów narzędzia Helm, przechowywane w usłudze Azure Container Registry.

> [!IMPORTANT]
> Ta funkcja jest obecnie dostępna w wersji zapoznawczej. Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania][terms-of-use]. Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby wykonać kroki opisane w tym artykule, muszą być spełnione następujące wymagania wstępne:

- **Usługa Azure Container Registry** — Tworzenie rejestru kontenerów w ramach subskrypcji platformy Azure. Na przykład użyć [witryny Azure portal](container-registry-get-started-portal.md) lub [wiersza polecenia platformy Azure](container-registry-get-started-azure-cli.md).
- **Wersja klienta Helm 2.11.0 (nie wersja RC) lub nowszym** — Uruchom `helm version` można znaleźć bieżącej wersji. Należy również serwera narzędzia Helm (Tiller) inicjowane w obrębie klastra Kubernetes. Jeśli to konieczne, możesz [Utwórz klaster Azure Kubernetes Service][aks-quickstart]. Aby uzyskać więcej informacji na temat instalacji i uaktualniania narzędzia Helm, zobacz [instalowanie narzędzia Helm][helm-install].
- **Wiersza polecenia platformy Azure w wersji 2.0.46 lub nowszej** — Uruchom `az --version` Aby znaleźć wersję. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

## <a name="add-a-repository-to-helm-client"></a>Dodawanie repozytorium narzędzia Helm klienta

Repozytorium narzędzia Helm to serwer HTTP, które mogą być przechowywane wykresów rozwiązania Helm. Usługa Azure Container Registry można magazynowanie dla wykresów rozwiązania Helm i zarządzać definicji indeksu, jak dodać i usunąć wykresów do repozytorium.

Aby dodać usługi Azure Container Registry jako repozytorium pakietów narzędzia Helm, należy użyć wiersza polecenia platformy Azure. W przypadku tej metody klient Helm jest aktualizowany za pomocą identyfikatora URI i poświadczeń dla tego repozytorium, wspierane przez usługę Azure Container Registry. Nie trzeba ręcznie określ informacje o tym repozytorium, więc poświadczenia nie są widoczne w historii poleceń, na przykład.

Jeśli to konieczne, zaloguj się do wiersza polecenia platformy Azure i postępuj zgodnie z instrukcjami:

```azurecli
az login
```

Skonfiguruj domyślne ustawienia wiersza polecenia platformy Azure o nazwie przy użyciu usługi Azure Container Registry [skonfigurować az] [ az-configure] polecenia. W poniższym przykładzie Zastąp `<acrName>` nazwą rejestru:

```azurecli
az configure --defaults acr=<acrName>
```

Teraz Dodaj repozytorium wykresu Helm rejestru kontenera platformy Azure w kliencie narzędzia Helm przy użyciu [Dodaj repozytorium narzędzia helm acr az] [ az-acr-helm-repo-add] polecenia. To polecenie pobiera token dla usługi Azure container registry, używanego przez klienta Helm uwierzytelniania. Token uwierzytelniania jest ważny przez 1 godzinę. Podobnie jak `docker login`, to polecenie można uruchomić w przyszłych sesjach interfejsu wiersza polecenia do uwierzytelniania klienta z usługi Azure Container Registry Helm wykresu repozytorium narzędzia Helm:

```azurecli
az acr helm repo add
```

## <a name="add-a-chart-to-the-repository"></a>Dodawanie wykresu do repozytorium

W tym artykule uzyskajmy istniejącego wykresu Helm z publicznych Helm *stabilne* repozytorium. *Stabilne* repozytorium jest wyselekcjonowanych, publicznego repozytorium zawiera typowe wykresy aplikacji. Pakiet maintainers przesłać wykresy w celu *stabilne* repozytorium w taki sam sposób, że usługi Docker Hub udostępnia publicznego rejestru, typowych obrazów kontenera. Wykres pobrane z publicznie *stabilne* repozytorium, następnie mogą zostać wypchnięte do prywatnego repozytorium usługi Azure Container Registry. W większości przypadków będzie tworzenie i przekazywanie wykresów dla aplikacji, które tworzysz. Aby uzyskać więcej informacji na temat tworzenia własnego wykresów rozwiązania Helm, zobacz [Tworzenie wykresów rozwiązania Helm][develop-helm-charts].

Najpierw utwórz katalog w *~/acr-helm*, Pobierz istniejące *stabilny/wordpress* wykresu:

```console
mkdir ~/acr-helm && cd ~/acr-helm
helm fetch stable/wordpress
```

Lista pobranych wykresu i zwróć uwagę na wersję Wordpress uwzględniony w nazwie pliku. `helm fetch stable/wordpress` Polecenia nie określił określonej wersji, więc *najnowsze* wersja została pobrana. Wszystkich wykresów rozwiązania Helm obejmują numeru wersji w nazwie pliku, który następuje po [SemVer 2] [ semver2] standardowych. W następujących przykładowych danych wyjściowych, wykres Wordpress jest wersja *2.1.10*:

```
$ ls

wordpress-2.1.10.tgz
```

Teraz Wypchnij wykresu do repozytorium wykresu Helm w usłudze Azure Container Registry przy użyciu wiersza polecenia platformy Azure [az acr helm wypychania] [ az-acr-helm-push] polecenia. Określ nazwę wykresu Helm pobrany w poprzednim kroku, takie jak *wordpress 2.1.10.tgz*:

```azurecli
az acr helm push wordpress-2.1.10.tgz
```

Po kilku chwilach wiersza polecenia platformy Azure raportów, wykres został zapisany, jak pokazano w następujących przykładowych danych wyjściowych:

```
$ az acr helm push wordpress-2.1.10.tgz

{
  "saved": true
}
```

## <a name="list-charts-in-the-repository"></a>Wykresy listy w repozytorium

Klient narzędzia Helm przechowuje pamięci podręcznej kopię lokalną zawartość repozytoria zdalne. Zmiany w zdalnym repozytorium nie automatycznie aktualizuje listę dostępnych wykresów znana lokalnie przez klienta narzędzia Helm. Podczas wyszukiwania wykresy miedzy repozytoriami, narzędzia Helm używa jej lokalnej pamięci podręcznej indeksu. Aby użyć wykresu przekazany w poprzednim kroku, należy zaktualizować lokalnego indeksu repozytorium narzędzia Helm. Ponowna indeksacja repozytoriów w kliencie usługi Helm lub użyj wiersza polecenia platformy Azure, aby zaktualizować indeksu repozytorium. Po dodaniu każdego wykresu do repozytorium, ten krok należy wykonać:

```azurecli
az acr helm repo add
```

Z wykresem przechowywanych w repozytorium i indeksu zaktualizowany dostępne lokalnie można użyć regularnych polecenia Helm w klienta wyszukiwania lub jego zainstalowanie. Aby wyświetlić wszystkich wykresów w repozytorium, użyj `helm search <acrName>`. Podaj nazwę usługi Azure Container Registry:

```console
helm search <acrName>
```

Wykres Wordpress w poprzednim kroku ma na liście, jak pokazano w następujących przykładowych danych wyjściowych:

```
$ helm search myacrhelm

NAME                CHART VERSION   APP VERSION DESCRIPTION
helmdocs/wordpress  2.1.10          4.9.8       Web publishing platform for building blogs and websites.
```

Można również podać wykresów za pomocą interfejsu wiersza polecenia platformy Azure przy użyciu [az acr polecenie helm list][az-acr-helm-list]:

```azurecli
az acr helm list
```

## <a name="show-information-for-a-helm-chart"></a>Pokaż informacje dla wykresów rozwiązania Helm

Aby wyświetlić informacje o konkretny wykres w repozytorium, można ponownie użyć regularnych klienta narzędzia Helm. Aby wyświetlić informacje dla wykresu o nazwie *wordpress*, użyj `helm inspect`.

```console
helm inspect <acrName>/wordpress
```

Gdy numer wersji nie została podana, *najnowsze* wersja jest używana. Polecenie Helm zwraca szczegółowe informacje o schemacie, jak pokazano w następujących danych wyjściowych skróconego przykładu:

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

Można również wyświetlić informacje dotyczące wykresu za pomocą wiersza polecenia platformy Azure [az acr helm show] [ az-acr-helm-show] polecenia. Ponownie *najnowsze* jest zwracana wersja wykresu, domyślnie. Możesz dołączyć `--version` do listy z określoną wersją wykresu, takie jak *2.1.10*:

```azurecli
az acr helm show wordpress
```

## <a name="install-a-helm-chart-from-the-repository"></a>Zainstaluj pakiet Helm z repozytorium

Wykresu Helm w repozytorium jest zainstalowany, określając nazwę repozytorium, a następnie wykresu nazwy. Aby zainstalować wykresu Wordpress, należy użyć klienta narzędzia Helm:

```console
helm install <acrName>/wordpress
```

> [!TIP]
> Jeśli Wypchnij do repozytorium wykresu Helm rejestru kontenera platformy Azure i później wrócić w nowej sesji wiersza polecenia platformy lokalnego klienta Helm wymaga tokenu uwierzytelniania zaktualizowane. Aby uzyskać nowy token uwierzytelniania, użyj [Dodaj repozytorium narzędzia helm acr az] [ az-acr-helm-repo-add] polecenia.

Podczas procesu instalacji są wykonywane następujące czynności:

- Klient narzędzia Helm przeszukuje indeksu lokalnego repozytorium.
- Odpowiedni wykres zostanie pobrane z repozytorium usługi Azure Container Registry.
- Wykres jest wdrażana przy użyciu Tiller w klastrze Kubernetes.

Następujące skrócone przykładowe dane wyjściowe zawierają zasoby platformy Kubernetes, wdrożone za pomocą wykresu Helm:

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

Aby usunąć wykresu z repozytorium, użyj [az acr helm delete] [ az-acr-helm-delete] polecenia. Określ nazwę wykresu, takie jak *wordpress*i wersji, aby usunąć, takie jak *2.1.10*.

```azurecli
az acr helm delete wordpress --version 2.1.10
```

Jeśli chcesz usunąć wszystkie wersje wykresu o nazwie Opuść `--version` parametru.

Wykres w dalszym ciągu być zwracane w `helm search <acrName>`. Ponownie klienta Helm nie automatycznie aktualizuje listę dostępnych wykresów w repozytorium. Aby zaktualizować indeks repozytorium narzędzia Helm klienta, należy użyć [Dodaj repozytorium narzędzia helm acr az] [ az-acr-helm-repo-add] ponownie polecenie:

```azurecli
az acr helm repo add
```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule używane istniejącego wykresu Helm publicznie *stabilne* repozytorium. Aby uzyskać więcej informacji na temat sposobu tworzenia i wdrażania planów narzędzia Helm, zobacz [opracowywanie planów narzędzia Helm][develop-helm-charts].

Narzędzia Helm może służyć jako część procesu kompilacji w kontenerze. Aby uzyskać więcej informacji, zobacz [zadań rejestru kontenera platformy Azure][acr-tasks].

Aby uzyskać więcej informacji na temat sposobu używania i zarządzania usługi Azure Container Registry, zobacz [najlepsze praktyki][acr-bestpractices].

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

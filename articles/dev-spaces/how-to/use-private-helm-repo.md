---
title: Jak korzystać z prywatnego repozytorium helm w usłudze Azure Dev Spaces
services: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 08/22/2019
ms.topic: conceptual
description: Użyj prywatnego repozytorium Helm w przestrzeni deweloperów platformy Azure.
keywords: Docker, Kubernetes, Azure, AKS, Usługa kontenera azure, kontenery, Helm
manager: gwallace
ms.openlocfilehash: c8f0e463bc78d278d8162f8389664dbb46a83301
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240468"
---
# <a name="use-a-private-helm-repository-in-azure-dev-spaces"></a>Używanie prywatnego repozytorium helmu w usługach Azure Dev Spaces

[Helm][helm] jest menedżerem pakietów dla Kubernetes. Helm używa formatu [wykresu][helm-chart] do pakowania zależności. Wykresy helm są przechowywane w repozytorium, które może być publiczne lub prywatne. Usługa Azure Dev Spaces pobiera tylko wykresy helm z publicznych repozytoriów podczas uruchamiania aplikacji. W przypadkach, gdy repozytorium Helm jest prywatne lub usługi Azure Dev Spaces nie można uzyskać do niego dostępu, można dodać wykres z tego repozytorium bezpośrednio do aplikacji. Dodanie wykresu bezpośrednio umożliwia usługi Azure Dev Spaces uruchamiania aplikacji bez konieczności uzyskiwania dostępu do prywatnego repozytorium Helm.

## <a name="add-the-private-helm-repository-to-your-local-machine"></a>Dodawanie prywatnego repozytorium Helm do komputera lokalnego

Użyj [dodatku helm dodawać][helm-repo-add] i [helm repo aktualizacji,][helm-repo-update] aby uzyskać dostęp do prywatnego repozytorium Helm z komputera lokalnego.

```cmd
helm repo add privateRepoName http://example.com/helm/v1/repo --username user --password 5tr0ng_P@ssw0rd!
helm repo update
```

## <a name="add-the-chart-to-your-application"></a>Dodawanie wykresu do aplikacji

Przejdź do katalogu projektu i `azds prep`uruchom program .

```cmd
azds prep --enable-ingress
```

> [!TIP]
> Polecenie `prep` próbuje wygenerować [wykres Dockerfile i Helm](../how-dev-spaces-works-prep.md#prepare-your-code) dla projektu. Usługa Azure Dev Spaces używa tych plików do tworzenia i uruchamiania kodu, ale można zmodyfikować te pliki, jeśli chcesz zmienić sposób tworzenia i uruchamiania projektu.

Utwórz plik [requirements.yaml][helm-requirements] z wykresem w katalogu wykresów aplikacji. Na przykład, jeśli aplikacja ma nazwę *app1,* należy utworzyć *charts/app1/requirements.yaml*.

```yaml
dependencies:
    - name: mychart
      version: 0.1.0
      repository:  http://example.com/helm/v1/repo
```

Przejdź do katalogu wykresu aplikacji i użyj [aktualizacji zależności helm,][helm-dependency-update] aby zaktualizować zależności helm dla aplikacji i pobrać wykres z repozytorium prywatnego.

```cmd
helm dependency update
```

Sprawdź, czy podkatalog *wykresów* z plikiem *tgz* został dodany do katalogu wykresu aplikacji. Na przykład *charts/app1/charts/mychart-0.1.0.tgz*.

Wykres z prywatnego repozytorium Helm został pobrany i dodany do projektu. Usuń plik *requirements.yaml,* aby nie próbować zaktualizować tej zależności.

## <a name="run-your-application"></a>Uruchamianie aplikacji

Przejdź do katalogu głównego projektu i `azds up` uruchom, aby pomyślnie uruchomić aplikację w obszarze deweloperskim.

```cmd
$ azds up
Using dev space 'default' with target 'MyAKS'
Synchronizing files...2s
Installing Helm chart...2s
Waiting for container image build...2m 25s
Building container image...
...
Service 'app1' port 'http' is available at http://app1.1234567890abcdef1234.eus.azds.io/
Service 'app1' port 80 (http) is available at http://localhost:54256
...
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [Helm i jak to działa][helm].

[helm]: https://docs.helm.sh
[helm-chart]: https://helm.sh/docs/topics/charts/
[helm-dependency-update]: https://helm.sh/docs/topics/charts/#managing-dependencies-with-the-dependencies-field
[helm-repo-add]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
[helm-repo-update]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
[helm-requirements]: https://helm.sh/docs/topics/charts/#chart-dependencies

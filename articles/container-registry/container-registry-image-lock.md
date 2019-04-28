---
title: Blokowanie obrazu w usłudze Azure Container Registry
description: Ustaw atrybuty dla obrazu kontenera lub repozytorium, więc nie może być usunięte ani zastąpiona w usłudze Azure container registry.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 02/19/2019
ms.author: danlep
ms.openlocfilehash: ebbfaba158e7ddb669111f097eb1adde2373aa6c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60828651"
---
# <a name="lock-a-container-image-in-an-azure-container-registry"></a>Blokowanie obrazu kontenera w usłudze Azure container registry

W rejestrze kontenerów platformy Azure można zablokować wersję obrazu lub repozytorium tak, aby nie można usunąć lub zaktualizować. Aby zablokować obrazu lub repozytorium, zaktualizuj jego atrybuty za pomocą polecenia interfejsu wiersza polecenia platformy [az acr repozytorium aktualizacji][az-acr-repository-update]. 

Ten artykuł wymaga używania interfejsu wiersza polecenia platformy Azure w usłudze Azure Cloud Shell lub lokalnie (wersja 2.0.55 lub nowszy zalecane). Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli].

## <a name="scenarios"></a>Scenariusze

Domyślnie jest obraz z tagiem w usłudze Azure Container Registry *mutable*, dzięki czemu z odpowiednimi uprawnieniami mogą wielokrotnie aktualizacji i Wypchnij obraz przy użyciu tego samego tagu do rejestru. Można też obrazów kontenerów [usunięte](container-registry-delete.md) zgodnie z potrzebami. To zachowanie jest przydatne podczas tworzenia obrazów i trzeba utrzymywać rozmiar dla rejestru.

Jednak gdy wdrożysz obraz kontenera do środowiska produkcyjnego, może być konieczne *niezmienne* obrazu kontenera. Niezmienne obrazu to taki, który nie można przypadkowo usunąć ani zastąpić. Użyj [az acr repozytorium aktualizacji] [ az-acr-repository-update] polecenie, aby ustawić atrybuty repozytorium, dzięki czemu możesz:

* Zablokuj wersję obrazu lub całego repozytorium

* Ochrona przed usunięciem wersję obrazu lub repozytorium, ale zezwala na aktualizacje

* Zapobiegaj operacje odczytu (ściągać) na wersję obrazu lub całego repozytorium

Zobacz następujące sekcje przykłady.

## <a name="lock-an-image-or-repository"></a>Blokowanie obraz lub repozytorium 

### <a name="show-the-current-repository-attributes"></a>Pokaż atrybuty bieżącego repozytorium
Aby zobaczyć atrybuty bieżącego repozytorium, uruchom następujące polecenie [az acr repozytorium show] [ az-acr-repository-show] polecenia:

```azurecli
az acr repository show \
    --name myregistry --repository myrepo
    --output jsonc
```

### <a name="show-the-current-image-attributes"></a>Pokaż bieżący atrybuty obrazu
Aby wyświetlić bieżące atrybuty znacznika, uruchom następujące polecenie [az acr repozytorium show] [ az-acr-repository-show] polecenia:

```azurecli
az acr repository show \
    --name myregistry --image image:tag \
    --output jsonc
```

### <a name="lock-an-image-by-tag"></a>Blokowanie obrazu według tagu

Aby zablokować proporcje *myrepo / myimage:tag* obrazów w *myregistry*, uruchom następujące polecenie [az acr repozytorium aktualizacji] [ az-acr-repository-update] polecenia:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --write-enabled false
```

### <a name="lock-an-image-by-manifest-digest"></a>Zablokuj obraz przez skrót manifestu

Aby zablokować proporcje *myrepo/myimage* identyfikowane za pomocą skrótu manifestu obrazu (Skrót SHA-256, reprezentowane jako `sha256:...`), uruchom następujące polecenie. (Aby znaleźć skrótu manifestu skojarzony z co najmniej jednego znacznika obrazu, uruchom [az acr repozytorium show manifesty] [ az-acr-repository-show-manifests] polecenia.)

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage@sha256:123456abcdefg \
    --write-enabled false
```

### <a name="lock-a-repository"></a>Blokowanie repozytorium

Aby zablokować proporcje *myrepo/myimage* repozytorium i wszystkie obrazy, uruchom następujące polecenie:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --write-enabled false
```

## <a name="protect-an-image-or-repository-from-deletion"></a>Ochrona przed usunięciem obraz lub repozytorium

### <a name="protect-an-image-from-deletion"></a>Ochrona obraz przed usunięciem

Aby umożliwić *myrepo / myimage:tag* obraz ma zostać zaktualizowane, ale nie został usunięty, uruchom następujące polecenie:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled false --write-enabled true
```

### <a name="protect-a-repository-from-deletion"></a>Ochrona repozytorium przed usunięciem

Następujące polecenie ustawia *myrepo/myimage* repozytorium, więc nie można jej usunąć. Nadal można zaktualizować lub usunąć pojedyncze obrazy.

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled false --write-enabled true
```

## <a name="prevent-read-operations-on-an-image-or-repository"></a>Zapobiegaj operacji odczytu na obrazie lub repozytorium

Aby uniknąć operacji odczytu (ściągać) na *myrepo / myimage:tag* obrazu, uruchom następujące polecenie:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --read-enabled false
```

Aby uniknąć operacji odczytu na wszystkie obrazy w *myrepo/myimage* repozytorium, uruchom następujące polecenie:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --read-enabled false
```

## <a name="unlock-an-image-or-repository"></a>Odblokuj obraz lub repozytorium

Aby przywrócić domyślne zachowanie *myrepo / myimage:tag* obrazu tak, aby można go usunąć i zaktualizowana, uruchom następujące polecenie:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled true --write-enabled true
```

Aby przywrócić domyślne zachowanie *myrepo/myimage* repozytorium i wszystkie obrazy, aby można je usunąć i zaktualizowana, uruchom następujące polecenie:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled true --write-enabled true
```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule omówiono przy użyciu [az acr repozytorium aktualizacji] [ az-acr-repository-update] polecenie, aby uniemożliwić usunięcie lub aktualizację wersji obrazu w repozytorium. Aby ustawić dodatkowe atrybuty, zobacz [az acr repozytorium aktualizacji] [ az-acr-repository-update] poleceń.

Aby wyświetlić atrybuty ustawione dla wersji obrazu lub repozytorium, użyj [az acr repozytorium show] [ az-acr-repository-show] polecenia.

Aby uzyskać szczegółowe informacje o operacji usuwania, zobacz [usuwanie obrazów kontenerów w usłudze Azure Container Registry][container-registry-delete].

<!-- LINKS - Internal -->
[az-acr-repository-update]: /cli/azure/acr/repository#az-acr-repository-update
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-delete]: container-registry-delete.md


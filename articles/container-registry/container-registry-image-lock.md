---
title: Blokowanie obrazów
description: Ustaw atrybuty dla obrazu kontenera lub repozytorium, aby nie można go usunąć ani nadpisać w rejestrze kontenerów platformy Azure.
ms.topic: article
ms.date: 09/30/2019
ms.openlocfilehash: da84767523bb6d948b71b1c1ad2ddaffb628354a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659700"
---
# <a name="lock-a-container-image-in-an-azure-container-registry"></a>Blokowanie obrazu kontenera w rejestrze kontenerów platformy Azure

W rejestrze kontenerów platformy Azure można zablokować wersję obrazu lub repozytorium, aby nie można było go usunąć ani zaktualizować. Aby zablokować obraz lub repozytorium, zaktualizuj jego atrybuty za pomocą polecenia [az acr repozytorium azure.][az-acr-repository-update] 

Ten artykuł wymaga uruchomienia interfejsu wiersza polecenia platformy Azure w usłudze Azure Cloud Shell lub lokalnie (zalecane w wersji 2.0.55 lub nowszej). Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli].

> [!IMPORTANT]
> Ten artykuł nie dotyczy blokowania całego rejestru, na przykład przy użyciu **ustawienia > blokady** w witrynie Azure portal lub `az lock` polecenia w interfejsu wiersza polecenia platformy Azure. Blokowanie zasobu rejestru nie uniemożliwia tworzenia, aktualizowania ani usuwania danych w repozytoriach. Blokowanie rejestru wpływa tylko na operacje zarządzania, takie jak dodawanie lub usuwanie replikacji lub usuwanie samego rejestru. Więcej informacji w [lock zasobów, aby zapobiec nieoczekiwanym zmianom](../azure-resource-manager/management/lock-resources.md).

## <a name="scenarios"></a>Scenariusze

Domyślnie oznakowany obraz w rejestrze kontenerów platformy Azure jest *modyfikowalna,* więc z odpowiednimi uprawnieniami można wielokrotnie aktualizować i wypychać obraz z tym samym tagiem do rejestru. Obrazy kontenerów można również [usunąć w](container-registry-delete.md) razie potrzeby. To zachowanie jest przydatne podczas opracowywania obrazów i konieczności utrzymania rozmiaru rejestru.

Jednak podczas wdrażania obrazu kontenera do produkcji, może być konieczne *niezmienne* obrazu kontenera. Niezmienny obraz to obraz, którego nie można przypadkowo usunąć ani zastąpić.

Zobacz [Zalecenia dotyczące tagowania i przechowywania obrazów kontenerów,](container-registry-image-tag-version.md) aby uzyskać strategie oznaczania i tworzenia obrazów wersji w rejestrze.

Użyj polecenia [aktualizacji repozytorium az acr,][az-acr-repository-update] aby ustawić atrybuty repozytorium, aby:

* Blokowanie wersji obrazu lub całego repozytorium

* Ochrona wersji obrazu lub repozytorium przed usunięciem, ale zezwalaj na aktualizacje

* Zapobieganie operacjom odczytu (ściągania) w wersji obrazu lub w całym repozytorium

Zobacz poniższe sekcje, aby zapoznać się z przykładami. 

## <a name="lock-an-image-or-repository"></a>Blokowanie obrazu lub repozytorium 

### <a name="show-the-current-repository-attributes"></a>Pokazy bieżące atrybuty repozytorium
Aby wyświetlić bieżące atrybuty repozytorium, uruchom następujące polecenie [az acr repozytorium show:][az-acr-repository-show]

```azurecli
az acr repository show \
    --name myregistry --repository myrepo \
    --output jsonc
```

### <a name="show-the-current-image-attributes"></a>Pokaż bieżące atrybuty obrazu
Aby wyświetlić bieżące atrybuty tagu, uruchom następujące polecenie [az acr repozytorium show:][az-acr-repository-show]

```azurecli
az acr repository show \
    --name myregistry --image image:tag \
    --output jsonc
```

### <a name="lock-an-image-by-tag"></a>Blokowanie obrazu według znacznika

Aby zablokować *myrepo/myimage:tag* image in *myregistry*, uruchom następujące polecenie [aktualizacji repozytorium az acr:][az-acr-repository-update]

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --write-enabled false
```

### <a name="lock-an-image-by-manifest-digest"></a>Blokowanie obrazu przez podsumowanie manifestu

Aby zablokować obraz *myrepo/myimage* identyfikowany przez podsumowanie manifestu (skrót `sha256:...`SHA-256, reprezentowany jako ), uruchom następujące polecenie. (Aby znaleźć podsumowanie manifestu skojarzone z co najmniej jednym znacznikiem obrazu, uruchom polecenie [az acr repozytorium show-manifests).][az-acr-repository-show-manifests]

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage@sha256:123456abcdefg \
    --write-enabled false
```

### <a name="lock-a-repository"></a>Blokowanie repozytorium

Aby zablokować repozytorium *myrepo/myimage* i wszystkie obrazy w nim, uruchom następujące polecenie:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --write-enabled false
```

## <a name="protect-an-image-or-repository-from-deletion"></a>Ochrona obrazu lub repozytorium przed usunięciem

### <a name="protect-an-image-from-deletion"></a>Ochrona obrazu przed usunięciem

Aby umożliwić aktualizowanie obrazu *myrepo/myimage:tag* image, ale nie jest on usuwany, uruchom następujące polecenie:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled false --write-enabled true
```

### <a name="protect-a-repository-from-deletion"></a>Ochrona repozytorium przed usunięciem

Następujące polecenie ustawia repozytorium *myrepo/myimage,* dzięki czemu nie można go usunąć. Poszczególne obrazy można nadal aktualizować lub usuwać.

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled false --write-enabled true
```

## <a name="prevent-read-operations-on-an-image-or-repository"></a>Zapobieganie operacjom odczytu obrazu lub repozytorium

Aby zapobiec operacji odczytu (ściągania) na *myrepo/myimage:tag* image, uruchom następujące polecenie:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --read-enabled false
```

Aby zapobiec operacji odczytu na wszystkich obrazach w repozytorium *myrepo/myimage,* uruchom następujące polecenie:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --read-enabled false
```

## <a name="unlock-an-image-or-repository"></a>Odblokowywanie obrazu lub repozytorium

Aby przywrócić domyślne zachowanie obrazu *myrepo/myimage:tag,* aby można było go usunąć i zaktualizować, uruchom następujące polecenie:

```azurecli
az acr repository update \
    --name myregistry --image myrepo/myimage:tag \
    --delete-enabled true --write-enabled true
```

Aby przywrócić domyślne zachowanie repozytorium *myrepo/myimage* i wszystkich obrazów, aby można je było usunąć i zaktualizować, uruchom następujące polecenie:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo/myimage \
    --delete-enabled true --write-enabled true
```

## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się o użyciu [az acr polecenia aktualizacji repozytorium,][az-acr-repository-update] aby zapobiec usunięciu lub aktualizacji wersji obrazu w repozytorium. Aby ustawić dodatkowe atrybuty, zobacz odwołanie do polecenia [aktualizacji repozytorium az acr.][az-acr-repository-update]

Aby wyświetlić atrybuty ustawione dla wersji obrazu lub repozytorium, użyj polecenia [az acr repozytorium show.][az-acr-repository-show]

Aby uzyskać szczegółowe informacje na temat operacji usuwania, zobacz [Usuwanie obrazów kontenerów w rejestrze kontenerów platformy Azure][container-registry-delete].

<!-- LINKS - Internal -->
[az-acr-repository-update]: /cli/azure/acr/repository#az-acr-repository-update
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-delete]: container-registry-delete.md


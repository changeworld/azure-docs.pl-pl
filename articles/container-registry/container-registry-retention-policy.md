---
title: Zasady do zachowywania nieoznakowanych manifestów w Azure Container Registry
description: Informacje o sposobie włączania zasad przechowywania w rejestrze kontenerów platformy Azure w celu automatycznego usunięcia nieoznakowanych manifestów po upływie określonego czasu.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 09/25/2019
ms.author: danlep
ms.openlocfilehash: 36d27bc6089bbe3f4ada6862a9c1be1fa0bdbae7
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71306005"
---
# <a name="set-a-retention-policy-for-untagged-manifests"></a>Ustawianie zasad przechowywania dla nieoznakowanych manifestów

Azure Container Registry zapewnia opcję ustawiania *zasad przechowywania* dla zapisywanych manifestów obrazów, które nie mają skojarzonych tagów (*nieoznakowane manifesty*). Po włączeniu zasad przechowywania, nieoznaczone manifesty w rejestrze są automatycznie usuwane po upływie kilku dni. Ta funkcja zapobiega wypełnianiu przez rejestr artefaktów, które nie są potrzebne, i pomaga zaoszczędzić na kosztach magazynowania. Jeśli atrybut manifestu bez znaczników jest ustawiony na `false`, nie można usunąć manifestu, a zasady przechowywania nie są stosowane. `delete-enabled`

Możesz użyć Azure Cloud Shell lub lokalnej instalacji interfejsu wiersza polecenia platformy Azure, aby uruchomić przykłady poleceń w tym artykule. Jeśli chcesz używać go lokalnie, wymagana jest wersja 2.0.74 lub nowsza. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli].

> [!IMPORTANT]
> Ta funkcja jest obecnie dostępna w wersji zapoznawczej, a niektóre [ograniczenia mają zastosowanie](#preview-limitations). Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania][terms-of-use]. Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.

> [!WARNING]
> Ustawianie zasad przechowywania z obsługą opieki — usunięte dane obrazu są NIEODWRACALNe. Jeśli masz systemy, które pobierają obrazy za pomocą skrótu manifestu (w przeciwieństwie do nazwy obrazu), nie należy ustawiać zasad przechowywania dla nieoznakowanych manifestów. Usunięcie nieoznakowanych obrazów uniemożliwi tym systemom ściąganie obrazów z rejestru. Zamiast ściągania według manifestu należy rozważyć przyjęcie unikatowego schematu *znakowania* , [zalecane najlepsze rozwiązanie](container-registry-image-tag-version.md).

Jeśli chcesz usunąć Tagi pojedynczego obrazu lub manifesty za pomocą poleceń interfejsu wiersza polecenia platformy Azure, zobacz [usuwanie obrazów kontenerów w Azure Container Registry](container-registry-delete.md).

## <a name="preview-limitations"></a>Ograniczenia wersji zapoznawczej

* Tylko rejestr kontenerów w **warstwie Premium** można skonfigurować przy użyciu zasad przechowywania. Aby uzyskać informacje na temat warstw usługi Registry, zobacz [Azure Container Registry SKU](container-registry-skus.md).
* Zasady przechowywania można ustawić tylko dla manifestów nieoznakowanych.

## <a name="set-a-retention-policy---cli"></a>Ustawianie zasad przechowywania — interfejs wiersza polecenia

Poniższy przykład pokazuje, jak używać interfejsu wiersza polecenia platformy Azure do ustawiania zasad przechowywania dla nieoznakowanych manifestów w rejestrze.

### <a name="enable-a-retention-policy"></a>Włączanie zasad przechowywania

Domyślnie żadna zasada przechowywania nie jest ustawiona w rejestrze kontenerów. Aby ustawić lub zaktualizować zasady przechowywania, uruchom polecenie [AZ ACR config Recovery Update][az-acr-config-retention-update] w interfejsie wiersza polecenia platformy Azure. Możesz określić liczbę dni z zakresu od 0 do 365, aby zachować nieoznaczone manifesty. Jeśli nie określisz liczby dni, polecenie ustawia domyślną wartość 7 dni. Po okresie przechowywania wszystkie nieoznakowane manifesty w rejestrze zostaną automatycznie usunięte.

Poniższy przykład ustawia zasady przechowywania wynoszące 30 dni dla nieoznakowanych manifestów w *rejestrze rejestr:*

```azurecli
az acr config retention update --name myregistry --status enabled --days 30 --type UntaggedManifests
```

W poniższym przykładzie są ustawiane zasady usuwania dowolnego manifestu w rejestrze, gdy tylko jest to nieoznakowane. Utwórz te zasady, ustawiając okres przechowywania wynoszący 0 dni:

```azurecli
az acr config retention update --name myregistry --status enabled --days 0 --type UntaggedManifests
```

### <a name="disable-a-retention-policy"></a>Wyłącz zasady przechowywania

Aby wyświetlić zasady przechowywania ustawione w rejestrze, uruchom polecenie [AZ ACR config retencja show][az-acr-config-retention-show] :

```azurecli
az acr config retention show --name myregistry
```

Aby wyłączyć zasady przechowywania w rejestrze, uruchom polecenie [AZ ACR config przechowywanie aktualizacji][az-acr-config-retention-update] i ustaw następujące ustawienia `--status disabled`:

```azurecli
az acr config retention update --name myregistry --status disabled
```

## <a name="set-a-retention-policy---portal"></a>Ustawianie zasad przechowywania — Portal

Można również ustawić zasady przechowywania rejestru w [Azure Portal](https://portal.azure.com). W poniższym przykładzie pokazano, jak za pomocą portalu ustawić zasady przechowywania dla nieoznakowanych manifestów w rejestrze.

### <a name="enable-a-retention-policy"></a>Włączanie zasad przechowywania

1. Przejdź do usługi Azure Container Registry. W obszarze **zasady**wybierz pozycję **przechowywanie** (wersja zapoznawcza).
1. W obszarze **stan**wybierz pozycję **włączone**.
1. Wybierz liczbę dni z przedziału od 0 do 365, aby zachować nieoznaczone manifesty. Wybierz pozycję **Zapisz**.

![Włączanie zasad przechowywania w Azure Portal](media/container-registry-retention-policy/container-registry-retention-policy01.png)

### <a name="disable-a-retention-policy"></a>Wyłącz zasady przechowywania

1. Przejdź do usługi Azure Container Registry. W obszarze **zasady**wybierz pozycję **przechowywanie** (wersja zapoznawcza).
1. W obszarze **stan**wybierz pozycję **wyłączone**. Wybierz pozycję **Zapisz**.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o opcjach [usuwania obrazów i repozytoriów](container-registry-delete.md) w programie Azure Container Registry

* Dowiedz się, jak [automatycznie czyścić](container-registry-auto-purge.md) wybrane obrazy i manifesty z rejestru

* Dowiedz się więcej o opcjach [blokowania obrazów i manifestów](container-registry-image-lock.md) w rejestrze

<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/


<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-config-retention-update]: /cli/azure/acr/config/retention#az-acr-config-retention-update
[az-acr-config-retention-show]: /cli/azure/acr/config/retention#az-acr-config-retention-show

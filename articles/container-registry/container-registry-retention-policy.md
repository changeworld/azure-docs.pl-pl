---
title: Zasady przechowywania nieoznakowanych manifestów
description: Dowiedz się, jak włączyć zasady przechowywania w rejestrze kontenerów platformy Azure, aby automatycznie wykreślić nieoznakowane manifesty po zdefiniowanym okresie.
ms.topic: article
ms.date: 10/02/2019
ms.openlocfilehash: 912616b6ab95cdff91e70477c7d6de476ccfdfa7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74454817"
---
# <a name="set-a-retention-policy-for-untagged-manifests"></a>Ustawianie zasad przechowywania dla manifestów nieoznakowanych

Usługa Azure Container Registry umożliwia ustawienie *zasad przechowywania* dla przechowywanych manifestów obrazu, które nie mają żadnych skojarzonych tagów *(manifesty nieoznakowane).* Gdy zasada przechowywania jest włączona, nieoznakowane manifesty w rejestrze są automatycznie usuwane po kilku dniach ustawienia. Ta funkcja zapobiega zapełnianiu rejestru artefaktami, które nie są potrzebne, i pomaga zaoszczędzić na kosztach magazynowania. Jeśli `delete-enabled` atrybut manifestu nieoznakowanego `false`jest ustawiony na , manifest nie można usunąć, a zasady przechowywania nie ma zastosowania.

Aby uruchomić przykłady poleceń w tym artykule, można użyć usługi Azure Cloud Shell lub lokalnej instalacji interfejsu wiersza polecenia platformy Azure. Jeśli chcesz używać go lokalnie, wymagana jest wersja 2.0.74 lub nowsza. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli].

> [!IMPORTANT]
> Ta funkcja jest obecnie w wersji zapoznawczej i obowiązują pewne [ograniczenia](#preview-limitations). Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania][terms-of-use]. Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.

> [!WARNING]
> Ustaw zasady przechowywania z opieki - usunięte dane obrazu jest unrecoverable. Jeśli masz systemy, które ściągają obrazy przez podsumowanie manifestu (w przeciwieństwie do nazwy obrazu), nie należy ustawiać zasad przechowywania dla manifestów nieoznakowanych. Usunięcie nieoznakowanych obrazów uniemożliwi tym systemom ściąganie obrazów z rejestru. Zamiast ciągnąć przez manifest, należy rozważyć przyjęcie unikalnego schematu *znakowania,* [zalecana najlepsza praktyka](container-registry-image-tag-version.md).

## <a name="preview-limitations"></a>Ograniczenia wersji zapoznawczej

* Tylko rejestr kontenerów **w usłudze Premium** można skonfigurować za pomocą zasad przechowywania. Aby uzyskać informacje dotyczące warstw usług rejestru, zobacz [Jednostki SKU rejestru kontenerów platformy Azure](container-registry-skus.md).
* Zasady przechowywania można ustawić tylko dla manifestów nieoznakowanych.
* Zasady przechowywania obecnie stosuje się tylko do manifestów, które są nieoznakowane *po* włączeniu zasad. Istniejące nieoznakowane manifesty w rejestrze nie podlegają zasadom. Aby usunąć istniejące manifesty nieoznakowane, zobacz przykłady w [obszarze Usuwanie obrazów kontenerów w rejestrze kontenerów platformy Azure](container-registry-delete.md).

## <a name="about-the-retention-policy"></a>Informacje o zasadach przechowywania

Usługa Azure Container Registry odwołuje się do zliczania manifestów w rejestrze. Gdy manifest jest nieoznakowany, sprawdza zasady przechowywania. Jeśli zasada przechowywania jest włączona, operacja usuwania manifestu jest umieszczana w kolejce z określoną datą, zgodnie z liczbą dni ustawioną w zasadach.

Oddzielne zadanie zarządzania kolejką stale przetwarza wiadomości, skalując zgodnie z potrzebami. Na przykład załóżmy, że nieoznakowane dwa manifesty, 1 godzina od siebie, w rejestrze z zasadą przechowywania 30 dni. Dwie wiadomości będą umieszczane w kolejce. Następnie, 30 dni później, około 1 godziny od siebie, wiadomości będą pobierane z kolejki i przetwarzane, przy założeniu, że zasady nadal obowiązują.

## <a name="set-a-retention-policy---cli"></a>Ustawianie zasad przechowywania — cli

W poniższym przykładzie pokazano, jak używać interfejsu wiersza polecenia platformy Azure, aby ustawić zasady przechowywania dla manifestów nieoznakowanych w rejestrze.

### <a name="enable-a-retention-policy"></a>Włączanie zasad przechowywania

Domyślnie w rejestrze kontenerów nie są ustawiane żadne zasady przechowywania. Aby ustawić lub zaktualizować zasady przechowywania, uruchom polecenie [az acr config retention update][az-acr-config-retention-update] w wierszu polecenia interfejsu wiersza polecenia platformy Azure. Można określić liczbę dni od 0 do 365, aby zachować nieoznakowane manifesty. Jeśli nie określisz liczby dni, polecenie ustawia domyślnie 7 dni. Po upływie okresu przechowywania wszystkie nieoznakowane manifesty w rejestrze są automatycznie usuwane.

W poniższym przykładzie ustawia zasady przechowywania 30 dni dla manifestów nieoznakowanych w *myregistry*rejestru:

```azurecli
az acr config retention update --registry myregistry --status enabled --days 30 --type UntaggedManifests
```

W poniższym przykładzie ustawia zasady, aby usunąć wszelkie manifest w rejestrze, jak tylko jest nieoznakowany. Utwórz tę zasadę, ustawiając okres przechowywania wynoszący 0 dni. 

```azurecli
az acr config retention update --registry myregistry --status enabled --days 0 --type UntaggedManifests
```

### <a name="validate-a-retention-policy"></a>Sprawdzanie poprawności zasad przechowywania

Jeśli poprzednie zasady zostaną wstrzymujące z okresem przechowywania wynoszącym 0 dni, można szybko sprawdzić, czy nieoznakowane manifesty są usuwane:

1. Wypchnij `hello-world:latest` obraz testowy do rejestru lub zastąp inny obraz testowy.
1. Odznacz `hello-world:latest` obraz, na przykład za pomocą polecenia [az acr repozytorium untag.][az-acr-repository-untag] Manifest nieoznakowany pozostaje w rejestrze.
    ```azurecli
    az acr repository untag --name myregistry --image hello-world:latest
    ```
1. W ciągu kilku sekund nieoznakowany manifest zostanie usunięty. Można zweryfikować usunięcie, wystawiając manifesty w repozytorium, na przykład za pomocą polecenia [az acr repozytorium show-manifests.][az-acr-repository-show-manifests] Jeśli obraz testowy był jedynym w repozytorium, samo repozytorium jest usuwane.

### <a name="disable-a-retention-policy"></a>Wyłączanie zasad przechowywania

Aby wyświetlić zasady przechowywania ustawione w rejestrze, uruchom polecenie [az acr config retention show:][az-acr-config-retention-show]

```azurecli
az acr config retention show --registry myregistry
```

Aby wyłączyć zasady przechowywania w rejestrze, uruchom polecenie [az acr config retention update][az-acr-config-retention-update] i ustaw: `--status disabled`

```azurecli
az acr config retention update --registry myregistry --status disabled --type UntaggedManifests
```

## <a name="set-a-retention-policy---portal"></a>Ustawianie zasad przechowywania — portal

Można również ustawić zasady przechowywania rejestru w [witrynie Azure portal](https://portal.azure.com). W poniższym przykładzie pokazano, jak za pomocą portalu ustawić zasady przechowywania dla manifestów nieoznakowanych w rejestrze.

### <a name="enable-a-retention-policy"></a>Włączanie zasad przechowywania

1. Przejdź do rejestru kontenerów platformy Azure. W obszarze **Zasady**wybierz **pozycję Retencja** (Wersja zapoznawcza).
1. W **obszarze Stan**wybierz pozycję **Włączone**.
1. Wybierz liczbę dni z 0 do 365, aby zachować nieoznakowane manifesty. Wybierz **pozycję Zapisz**.

![Włączanie zasad przechowywania w witrynie Azure portal](media/container-registry-retention-policy/container-registry-retention-policy01.png)

### <a name="disable-a-retention-policy"></a>Wyłączanie zasad przechowywania

1. Przejdź do rejestru kontenerów platformy Azure. W obszarze **Zasady**wybierz **pozycję Retencja** (Wersja zapoznawcza).
1. W **obszarze Stan**wybierz pozycję **Wyłączone**. Wybierz **pozycję Zapisz**.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o opcjach [usuwania obrazów i repozytoriów](container-registry-delete.md) w rejestrze kontenerów platformy Azure

* Dowiedz się, jak [automatycznie czyścić](container-registry-auto-purge.md) wybrane obrazy i manifesty z rejestru

* Dowiedz się więcej o opcjach [blokowania obrazów i manifestów](container-registry-image-lock.md) w rejestrze

<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/


<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-config-retention-update]: /cli/azure/acr/config/retention#az-acr-config-retention-update
[az-acr-config-retention-show]: /cli/azure/acr/config/retention#az-acr-config-retention-show
[az-acr-repository-untag]: /cli/azure/acr/repository#az-acr-repository-untag
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests

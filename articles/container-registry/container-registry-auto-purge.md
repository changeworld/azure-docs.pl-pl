---
title: Czyszczenie znaczników i manifestów
description: Użyj polecenia przeczyszczania, aby usunąć wiele tagów i manifestów z rejestru kontenerów platformy Azure na podstawie wieku i filtru znaczników oraz opcjonalnie zaplanować operacje przeczyszczania.
ms.topic: article
ms.date: 08/14/2019
ms.openlocfilehash: f9d86b628bdd0ce0db3067b02a47517d8aadcba3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087328"
---
# <a name="automatically-purge-images-from-an-azure-container-registry"></a>Automatyczne czyszczenie obrazów z rejestru kontenerów platformy Azure

Korzystając z rejestru kontenerów platformy Azure w ramach przepływu pracy deweloperskiego, rejestr może szybko wypełnić obrazy lub inne artefakty, które nie są potrzebne po krótkim czasie. Można usunąć wszystkie znaczniki, które są starsze niż określony czas trwania lub pasują do określonego filtru nazw. Aby szybko usunąć wiele artefaktów, `acr purge` w tym artykule przedstawiono polecenie, które można uruchomić jako zadanie usługi ACR na żądanie lub [zaplanowane.](container-registry-tasks-scheduled.md) 

Polecenie `acr purge` jest obecnie rozpowszechniane w obrazie`mcr.microsoft.com/acr/acr-cli:0.1`kontenera publicznego ( ), zbudowany z kodu źródłowego w repozytorium [acr-cli](https://github.com/Azure/acr-cli) w usłudze GitHub.

Aby uruchomić przykłady zadań usługi ACR w tym artykule, można użyć usługi Azure Cloud Shell lub lokalnej instalacji interfejsu wiersza polecenia platformy Azure. Jeśli chcesz używać go lokalnie, wymagana jest wersja 2.0.69 lub nowsza. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install]. 

> [!IMPORTANT]
> Ta funkcja jest obecnie dostępna w wersji zapoznawczej. Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania][terms-of-use]. Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.

> [!WARNING]
> Użyj `acr purge` polecenia z ostrożnością - usunięte dane obrazu są nieodzyskane. Jeśli masz systemy, które ściągają obrazy przez podsumowanie manifestu (w przeciwieństwie do nazwy obrazu), nie należy czyścić obrazów nieoznakowanych. Usunięcie nieoznakowanych obrazów uniemożliwi tym systemom ściąganie obrazów z rejestru. Zamiast ciągnąć przez manifest, należy rozważyć przyjęcie unikalnego schematu *znakowania,* [zalecana najlepsza praktyka](container-registry-image-tag-version.md).

Jeśli chcesz usunąć pojedyncze znaczniki obrazu lub manifesty przy użyciu poleceń interfejsu wiersza polecenia platformy Azure, zobacz [Usuwanie obrazów kontenerów w rejestrze kontenerów platformy Azure](container-registry-delete.md).

## <a name="use-the-purge-command"></a>Użyj polecenia przeczyszczanie

Polecenie `acr purge` kontener usuwa obrazy według znaczników w repozytorium, które pasują do filtru nazw i które są starsze niż określony czas trwania. Domyślnie usuwane są tylko odwołania do znaczników, a nie [manifesty](container-registry-concepts.md#manifest) i dane warstw. Polecenie ma opcję również usunąć manifesty. 

> [!NOTE]
> `acr purge`nie usuwa znacznika obrazu ani repozytorium, `write-enabled` w `false`którym atrybut jest ustawiony na . Aby uzyskać więcej informacji, zobacz [Blokowanie obrazu kontenera w rejestrze kontenerów platformy Azure](container-registry-image-lock.md).

`acr purge`jest przeznaczony do uruchamiania jako polecenie kontenera w [zadaniu ACR,](container-registry-tasks-overview.md)dzięki czemu jest automatycznie uwierzytelniany w rejestrze, w którym zadanie jest uruchamiane i wykonuje tam akcje. Przykłady zadań w tym `acr purge` artykule używają [aliasu](container-registry-tasks-reference-yaml.md#aliases) polecenia zamiast w pełni kwalifikowanego polecenia obrazu kontenera.

Po uruchomieniu `acr purge`należy określić co najmniej następujące informacje:

* `--filter`- Repozytorium i *wyrażenie regularne* do filtrowania tagów w repozytorium. Przykłady: `--filter "hello-world:.*"` dopasowuje wszystkie `hello-world` znaczniki w `--filter "hello-world:^1.*"` repozytorium `1`i dopasowuje tagi zaczynające się od . Przekaż `--filter` wiele parametrów, aby przeczyścić wiele repozytoriów.
* `--ago`- [Ciąg czasu trwania](https://golang.org/pkg/time/) w stylu Go, aby wskazać czas trwania, po przekroszenie którego obrazy są usuwane. Czas trwania składa się z sekwencji jednej lub więcej liczb dziesiętnych, z których każda ma sufiks jednostki. Prawidłowe jednostki czasu obejmują "d" dla dni, "h" dla godzin i "m" dla minut. Na przykład `--ago 2d3h6m` wybiera wszystkie filtrowane obrazy ostatnio zmodyfikowane ponad 2 dni, 3 `--ago 1.5h` godziny i 6 minut temu i wybiera obrazy ostatnio zmodyfikowane ponad 1,5 godziny temu.

`acr purge`obsługuje kilka parametrów opcjonalnych. Następujące dwa są używane w przykładach w tym artykule:

* `--untagged`- Określa, że manifesty, które nie mają skojarzonych tagów *(manifesty nieoznakowane)* są usuwane.
* `--dry-run`- Określa, że żadne dane nie są usuwane, ale dane wyjściowe są takie same, jak w przypadku uruchomienia polecenia bez tej flagi. Ten parametr jest przydatny do testowania polecenia przeczyszczaj, aby upewnić się, że nie przypadkowo usuwa dane, które mają zostać zachowane.

Aby uzyskać dodatkowe `acr purge --help`parametry, uruchom plik . 

`acr purge`obsługuje inne funkcje poleceń zadań ACR, w tym [uruchamianie zmiennych](container-registry-tasks-reference-yaml.md#run-variables) i [dzienników uruchamiania zadań,](container-registry-tasks-logs.md) które są przesyłane strumieniowo, a także zapisywane do późniejszego pobierania.

### <a name="run-in-an-on-demand-task"></a>Uruchamianie w zadaniu na żądanie

W poniższym przykładzie użyto polecenia [az acr run][az-acr-run] do uruchomienia `acr purge` polecenia na żądanie. W tym przykładzie usuwa wszystkie znaczniki `hello-world` obrazu i manifesty w repozytorium w *myregistry,* które zostały zmodyfikowane więcej niż 1 dzień temu. Polecenie kontener jest przekazywane przy użyciu zmiennej środowiskowej. Zadanie jest uruchamiane bez kontekstu źródłowego.

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --untagged --ago 1d"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  /dev/null
```

### <a name="run-in-a-scheduled-task"></a>Uruchamianie w zaplanowanym zadaniu

W poniższym przykładzie użyto polecenia [az acr create,][az-acr-task-create] aby utworzyć codzienne [zaplanowane zadanie ACR](container-registry-tasks-scheduled.md). Tagi oczyszczania zadania zmodyfikowane ponad 7 `hello-world` dni temu w repozytorium. Polecenie kontener jest przekazywane przy użyciu zmiennej środowiskowej. Zadanie jest uruchamiane bez kontekstu źródłowego.

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --ago 7d"

az acr task create --name purgeTask \
  --cmd "$PURGE_CMD" \
  --schedule "0 0 * * *" \
  --registry myregistry \
  --context /dev/null
```

Uruchom polecenie [az acr,][az-acr-task-show] aby zobaczyć, że wyzwalacz czasomierza jest skonfigurowany.

### <a name="purge-large-numbers-of-tags-and-manifests"></a>Czyszczenie dużej liczby znaczników i manifestów

Czyszczenie dużej liczby znaczników i manifestów może potrwać kilka minut lub dłużej. Aby przeczyścić tysiące tagów i manifestów, może być konieczne uruchomienie dłuższego niż domyślny limit czasu 600 sekund dla zadania na żądanie lub 3600 sekund dla zaplanowanego zadania. Jeśli limit czasu zostanie przekroczony, usuwany jest tylko podzbiór znaczników i manifestów. Aby upewnić się, że przeczyszczanie `--timeout` na dużą skalę jest zakończone, przekaż parametr, aby zwiększyć wartość. 

Na przykład następujące zadanie na żądanie ustawia limit czasu 3600 sekund (1 godzina):

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --ago 1d --untagged"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  --timeout 3600 \
  /dev/null
```

## <a name="example-scheduled-purge-of-multiple-repositories-in-a-registry"></a>Przykład: Zaplanowane przeczyszczanie wielu repozytoriów w rejestrze

W tym przykładzie przechodzi przez za pomocą `acr purge` okresowo czyścić wiele repozytoriów w rejestrze. Na przykład może mieć potok rozwoju, który `samples/devimage1` wypycha obrazy do i `samples/devimage2` repozytoriów. Okresowo importuj obrazy programistyczne do repozytorium produkcyjnego dla wdrożeń, więc nie są już potrzebne obrazy programistyczne. Co tydzień czyścisz `samples/devimage1` repozytoria, `samples/devimage2` przygotowując się do pracy w nadchodzącym tygodniu.

### <a name="preview-the-purge"></a>Podgląd przeczyszczacza

Przed usunięciem danych zaleca się uruchomienie zadania przeczyszczania na żądanie przy użyciu parametru. `--dry-run` Ta opcja umożliwia wyświetlanie znaczników i manifestów, że polecenie zostanie przeczyszczenie, bez usuwania żadnych danych. 

W poniższym przykładzie filtr w każdym repozytorium wybiera wszystkie znaczniki. Parametr `--ago 0d` dopasowuje obrazy w każdym wieku w repozytoriach, które pasują do filtrów. Zmodyfikuj kryteria wyboru zgodnie z potrzebami dla scenariusza. Parametr `--untagged` wskazuje, aby usunąć manifesty oprócz tagów. Polecenie kontenera jest przekazywane do polecenia [az acr run][az-acr-run] przy użyciu zmiennej środowiskowej.

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge \
  --filter 'samples/devimage1:.*' --filter 'samples/devimage2:.*' \
  --ago 0d --untagged --dry-run"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  /dev/null
```

Przejrzyj dane wyjściowe polecenia, aby wyświetlić znaczniki i manifesty zgodne z parametrami wyboru. Ponieważ polecenie jest `--dry-run`uruchamiane z , żadne dane nie są usuwane.

Przykładowe dane wyjściowe:

```console
[...]
Deleting tags for repository: samples/devimage1
myregistry.azurecr.io/samples/devimage1:232889b
myregistry.azurecr.io/samples/devimage1:a21776a
Deleting manifests for repository: samples/devimage1
myregistry.azurecr.io/samples/devimage1@sha256:81b6f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e788b
myregistry.azurecr.io/samples/devimage1@sha256:3ded859790e68bd02791a972ab0bae727231dc8746f233a7949e40f8ea90c8b3
Deleting tags for repository: samples/devimage2
myregistry.azurecr.io/samples/devimage2:5e788ba
myregistry.azurecr.io/samples/devimage2:f336b7c
Deleting manifests for repository: samples/devimage2
myregistry.azurecr.io/samples/devimage2@sha256:8d2527cde610e1715ad095cb12bc7ed169b60c495e5428eefdf336b7cb7c0371
myregistry.azurecr.io/samples/devimage2@sha256:ca86b078f89607bc03ded859790e68bd02791a972ab0bae727231dc8746f233a

Number of deleted tags: 4
Number of deleted manifests: 4
[...]
```

### <a name="schedule-the-purge"></a>Zaplanuj czystkę

Po zweryfikowaniu przebiegu na sucho utwórz zaplanowane zadanie automatyzacji przeczyszczenia. Poniższy przykład planuje cotygodniowe zadanie w niedzielę o 1:00 UTC, aby uruchomić poprzednie polecenie przeczyszczanie:

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge \
  --filter 'samples/devimage1:.*' --filter 'samples/devimage2:.*' \
  --ago 0d --untagged"

az acr task create --name weeklyPurgeTask \
  --cmd "$PURGE_CMD" \
  --schedule "0 1 * * Sun" \
  --registry myregistry \
  --context /dev/null
```

Uruchom polecenie [az acr,][az-acr-task-show] aby zobaczyć, że wyzwalacz czasomierza jest skonfigurowany.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o innych opcjach [usuwania danych obrazu](container-registry-delete.md) w rejestrze kontenerów platformy Azure.

Aby uzyskać więcej informacji na temat magazynu obrazów, zobacz [Magazyn obrazów kontenerów w rejestrze kontenerów platformy Azure](container-registry-storage.md).

<!-- LINKS - External -->

[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show


---
title: Odwołanie YAML — zadania ACR
description: Odwołanie do definiowania zadań w YAML dla zadań ACR, w tym właściwości zadań, typów kroków, właściwości kroku i zmiennych wbudowanych.
ms.topic: article
ms.date: 10/23/2019
ms.openlocfilehash: 9558f698b4a9dbca46431fc02ced6ae30de29121
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246983"
---
# <a name="acr-tasks-reference-yaml"></a>Odwołanie do zadań ACR: YAML

Wieloetapowa definicja zadań w programie Zadań usługi ACR zapewnia prymitywną wartość obliczeniową skoncentrowaną na tworzeniu, testowaniu i poprawianiu kontenerów. Ten artykuł obejmuje polecenia, parametry, właściwości i składnię plików YAML definiujących zadania wieloetapowe.

Ten artykuł zawiera odwołanie do tworzenia wieloetapowych plików YAML zadań dla zadań ACR. Jeśli chcesz wprowadzić do zadań usługi ACR, zobacz [omówienie zadań ACR](container-registry-tasks-overview.md).

## <a name="acr-taskyaml-file-format"></a>format pliku acr-task.yaml

Zadania usługi ACR obsługują wieloetapową deklarację zadań w standardowej składni YAML. Kroki zadania można zdefiniować w pliku YAML. Następnie można uruchomić zadanie ręcznie, przekazując plik do polecenia [az acr run.][az-acr-run] Możesz też użyć pliku, aby utworzyć zadanie z [utworzonym zadaniem az acr,][az-acr-task-create] które jest wyzwalane automatycznie podczas zatwierdzania Git lub aktualizacji obrazu podstawowego. Chociaż ten artykuł `acr-task.yaml` odnosi się do pliku zawierającego kroki, ACR Tasks obsługuje dowolną prawidłową nazwę pliku z [obsługiwanym rozszerzeniem](#supported-task-filename-extensions).

Podstawowe najwyższego `acr-task.yaml` poziomu to **właściwości zadań, typy kroków**i **właściwości kroku:** **step types**

* [Właściwości zadania](#task-properties) mają zastosowanie do wszystkich kroków w trakcie wykonywania zadań. Istnieje kilka globalnych właściwości zadań, w tym:
  * `version`
  * `stepTimeout`
  * `workingDirectory`
* [Typy kroków zadań](#task-step-types) reprezentują typy akcji, które mogą być wykonywane w zadaniu. Istnieją trzy typy kroków:
  * `build`
  * `push`
  * `cmd`
* [Właściwości kroku zadania](#task-step-properties) są parametry, które mają zastosowanie do pojedynczego kroku. Istnieje kilka właściwości kroku, w tym:
  * `startDelay`
  * `timeout`
  * `when`
  * ... i wiele innych.

Format podstawowy `acr-task.yaml` pliku, w tym niektóre typowe właściwości kroku, następuje. Chociaż nie jest wyczerpującą reprezentacją wszystkich dostępnych właściwości kroku lub użycia typu kroku, zapewnia szybkie omówienie podstawowego formatu pliku.

```yml
version: # acr-task.yaml format version.
stepTimeout: # Seconds each step may take.
steps: # A collection of image or container actions.
  - build: # Equivalent to "docker build," but in a multi-tenant environment
  - push: # Push a newly built or retagged image to a registry.
    when: # Step property that defines either parallel or dependent step execution.
  - cmd: # Executes a container, supports specifying an [ENTRYPOINT] and parameters.
    startDelay: # Step property that specifies the number of seconds to wait before starting execution.
```

### <a name="supported-task-filename-extensions"></a>Obsługiwane rozszerzenia nazwy plików zadań

ACR Tasks zarezerwował kilka `.yaml`rozszerzeń nazwy plików, w tym , które będzie przetwarzać jako plik zadań. Każde *rozszerzenie, które nie* znajduje się na poniższej liście, jest uważane przez ACR Tasks za plik dockerfile: .yaml, .yml, .toml, .json, .sh, .bash, .zsh, .ps1, ps, .cmd, .bat, .ts, .js, .php, .py, .rb, .lua

YAML jest jedynym formatem pliku obsługiwanym obecnie przez zadania ACR. Inne rozszerzenia nazwy pliku są zarezerwowane dla ewentualnej przyszłej pomocy technicznej.

## <a name="run-the-sample-tasks"></a>Uruchamianie przykładowych zadań

Istnieje kilka przykładowych plików zadań, do których odwołuje się w poniższych sekcjach tego artykułu. Przykładowe zadania znajdują się w publicznym repozytorium Usługi GitHub, [Azure-Samples/acr-tasks][acr-tasks]. Można je uruchomić za pomocą polecenia azure cli [az acr run][az-acr-run]. Przykładowe polecenia są podobne do:

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

Formatowanie przykładowych poleceń zakłada, że skonfigurowano domyślny rejestr w wierszu polecenia `--registry` platformy Azure, więc pomijają ten parametr. Aby skonfigurować domyślny rejestr, użyj polecenia [az configure][az-configure] z `acr=REGISTRY_NAME` parametrem, `--defaults` który akceptuje wartość.

Na przykład, aby skonfigurować narzędzie wiersza polecenia platformy Azure z domyślnym rejestrem o nazwie "myregistry":

```azurecli
az configure --defaults acr=myregistry
```

## <a name="task-properties"></a>Właściwości zadania

Właściwości zadania zazwyczaj pojawiają się w `acr-task.yaml` górnej części pliku i są właściwościami globalnymi, które mają zastosowanie podczas pełnego wykonywania kroków zadania. Niektóre z tych właściwości globalnych można zastąpić w ramach poszczególnych kroków.

| Właściwość | Typ | Optional (Opcjonalność) | Opis | Zastępowanie obsługiwanych | Wartość domyślna |
| -------- | ---- | -------- | ----------- | ------------------ | ------------- |
| `version` | ciąg | Tak | Wersja pliku `acr-task.yaml` analizowana przez usługę Zadania ACR. Podczas gdy usługi ACR Tasks dąży do zachowania zgodności z powrotem, ta wartość umożliwia zadania ACR zachować zgodność w zdefiniowanej wersji. Jeśli nie określono, domyślnie do najnowszej wersji. | Nie | Brak |
| `stepTimeout` | int (sekundy) | Tak | Maksymalna liczba sekund, przez które krok może być uruchamiany. Jeśli właściwość jest określona w zadaniu, ustawia domyślną `timeout` właściwość wszystkich kroków. Jeśli `timeout` właściwość jest określona w kroku, zastępuje właściwość dostarczoną przez zadanie. | Tak | 600 (10 minut) |
| `workingDirectory` | ciąg | Tak | Katalog roboczy kontenera w czasie wykonywania. Jeśli właściwość jest określona w zadaniu, ustawia domyślną `workingDirectory` właściwość wszystkich kroków. Jeśli określono w kroku, zastępuje właściwość pod warunkiem, że zadanie. | Tak | `/workspace` |
| `env` | [ciąg, ciąg, ...] | Tak |  Tablica ciągów `key=value` w formacie definiujących zmienne środowiskowe dla zadania. Jeśli właściwość jest określona w zadaniu, ustawia domyślną `env` właściwość wszystkich kroków. Jeśli określono w kroku, zastępuje wszystkie zmienne środowiskowe dziedziczone z zadania. | Brak |
| `secrets` | [tajemnica, tajemnica, ...] | Tak | Tablica [tajnych](#secret) obiektów. | Brak |
| `networks` | [sieć, sieć, ...] | Tak | Tablica obiektów [sieciowych.](#network) | Brak |

### <a name="secret"></a>wpis tajny

Tajny obiekt ma następujące właściwości.

| Właściwość | Typ | Optional (Opcjonalność) | Opis | Wartość domyślna |
| -------- | ---- | -------- | ----------- | ------- |
| `id` | ciąg | Nie | Identyfikator klucza tajnego. | Brak |
| `keyvault` | ciąg | Tak | Tajny adres URL magazynu kluczy platformy Azure. | Brak |
| `clientID` | ciąg | Tak | Identyfikator klienta tożsamości [zarządzanej przypisanej przez użytkownika](container-registry-tasks-authentication-managed-identity.md) dla zasobów platformy Azure. | Brak |

### <a name="network"></a>network

Obiekt sieciowy ma następujące właściwości.

| Właściwość | Typ | Optional (Opcjonalność) | Opis | Wartość domyślna |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | ciąg | Nie | Nazwa sieci. | Brak |
| `driver` | ciąg | Tak | Sterownik do zarządzania siecią. | Brak |
| `ipv6` | bool | Tak | Czy sieć IPv6 jest włączona. | `false` |
| `skipCreation` | bool | Tak | Czy pominąć tworzenie sieci. | `false` |
| `isDefault` | bool | Tak | Określa, czy sieć jest siecią domyślną dostarczoną z rejestrem kontenerów platformy Azure | `false` |

## <a name="task-step-types"></a>Typy kroków zadań

Zadania usługi ACR obsługują trzy typy kroków. Każdy typ kroku obsługuje kilka właściwości, wyszczególnione w sekcji dla każdego typu kroku.

| Typ kroku | Opis |
| --------- | ----------- |
| [`build`](#build) | Tworzy obraz kontenera `docker build` przy użyciu znanej składni. |
| [`push`](#push) | `docker push` Wykonuje nowo utworzone lub ponownie otagowane obrazy do rejestru kontenerów. Usługa Azure Container Registry, inne rejestry prywatne i publiczne centrum platformy Docker są obsługiwane. |
| [`cmd`](#cmd) | Uruchamia kontener jako polecenie, z parametrami przekazanymi do kontenera `[ENTRYPOINT]`. Typ `cmd` kroku obsługuje `env`parametry, takie jak , `detach`i inne znane `docker run` opcje poleceń, włączając jednostkę i testowanie funkcjonalne z równoczesnym wykonywaniem kontenera. |

## <a name="build"></a>kompilacja

Tworzenie obrazu kontenera. Typ `build` kroku reprezentuje wielodostępne, bezpieczne `docker build` środki uruchamiania w chmurze jako pierwszorzędne prymitywne.

### <a name="syntax-build"></a>Składnia: kompilacja

```yml
version: v1.1.0
steps:
  - [build]: -t [imageName]:[tag] -f [Dockerfile] [context]
    [property]: [value]
```

Typ `build` kroku obsługuje parametry w poniższej tabeli. Typ `build` kroku obsługuje również wszystkie opcje kompilacji polecenia `--build-arg` [kompilacji platformy docker,](https://docs.docker.com/engine/reference/commandline/build/) takie jak ustawienie zmiennych w czasie kompilacji.

| Parametr | Opis | Optional (Opcjonalność) |
| --------- | ----------- | :-------: |
| `-t`&#124;`--image` | Definiuje w pełni `image:tag` kwalifikowany obraz zbudowany.<br /><br />Ponieważ obrazy mogą być używane do sprawdzania poprawności zadań wewnętrznych, `push` takich jak testy funkcjonalne, nie wszystkie obrazy wymagają rejestru. Jednak do wystąpienia obrazu w ramach task wykonanie, obraz wymaga nazwy do odwołania.<br /><br />W `az acr build`przeciwieństwie do tego, uruchamianie zadań ACR nie zapewnia domyślnego zachowania wypychania. W przypadku zadań usługi ACR domyślny scenariusz zakłada możliwość tworzenia, sprawdzania poprawności, a następnie wypychania obrazu. Zobacz [push,](#push) jak opcjonalnie wypychać wbudowane obrazy. | Tak |
| `-f`&#124;`--file` | Określa plik dockerfile `docker build`przekazany do . Jeśli nie zostanie określony, zakłada się domyślny plik Dockerfile w katalogu głównym kontekstu. Aby określić Plik Dockerfile, przekaż nazwę pliku względem katalogu głównego kontekstu. | Tak |
| `context` | Katalog główny przeszedł `docker build`do . Katalog główny każdego zadania jest ustawiony na udostępniony [workingDirectory](#task-step-properties)i zawiera katalog główny skojarzonego katalogu skrzepowego Git. | Nie |

### <a name="properties-build"></a>Właściwości: kompilacja

Typ `build` kroku obsługuje następujące właściwości. Szczegółowe informacje o tych właściwościach znajdują się w sekcji [Właściwości kroku zadania](#task-step-properties) w tym artykule.

| | | |
| -------- | ---- | -------- |
| `detach` | bool | Optional (Opcjonalność) |
| `disableWorkingDirectoryOverride` | bool | Optional (Opcjonalność) |
| `entryPoint` | ciąg | Optional (Opcjonalność) |
| `env` | [ciąg, ciąg, ...] | Optional (Opcjonalność) |
| `expose` | [ciąg, ciąg, ...] | Optional (Opcjonalność) |
| `id` | ciąg | Optional (Opcjonalność) |
| `ignoreErrors` | bool | Optional (Opcjonalność) |
| `isolation` | ciąg | Optional (Opcjonalność) |
| `keep` | bool | Optional (Opcjonalność) |
| `network` | obiekt | Optional (Opcjonalność) |
| `ports` | [ciąg, ciąg, ...] | Optional (Opcjonalność) |
| `pull` | bool | Optional (Opcjonalność) |
| `repeat` | int | Optional (Opcjonalność) |
| `retries` | int | Optional (Opcjonalność) |
| `retryDelay` | int (sekundy) | Optional (Opcjonalność) |
| `secret` | obiekt | Optional (Opcjonalność) |
| `startDelay` | int (sekundy) | Optional (Opcjonalność) |
| `timeout` | int (sekundy) | Optional (Opcjonalność) |
| `when` | [ciąg, ciąg, ...] | Optional (Opcjonalność) |
| `workingDirectory` | ciąg | Optional (Opcjonalność) |

### <a name="examples-build"></a>Przykłady: kompilacja

#### <a name="build-image---context-in-root"></a>Tworzenie obrazu — kontekst w katalogu głównym

```azurecli
az acr run -f build-hello-world.yaml https://github.com/AzureCR/acr-tasks-sample.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-hello-world.yaml)]

#### <a name="build-image---context-in-subdirectory"></a>Tworzenie obrazu — kontekst w podkatalogu

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world -f hello-world.dockerfile ./subDirectory
```

## <a name="push"></a>push

Wypchnij jeden lub więcej obrazów zbudowanych lub ponownie otagowanych do rejestru kontenerów. Obsługuje wypychanie do rejestrów prywatnych, takich jak Usługa Azure Container Registry lub do publicznego centrum platformy Docker.

### <a name="syntax-push"></a>Składnia: wypychanie

Typ `push` kroku obsługuje kolekcję obrazów. Składnia kolekcji YAML obsługuje formaty wbudowane i zagnieżdżone. Wypychanie pojedynczego obrazu jest zazwyczaj reprezentowane przy użyciu składni wbudowanej:

```yml
version: v1.1.0
steps:
  # Inline YAML collection syntax
  - push: ["$Registry/hello-world:$ID"]
```

Aby zwiększyć czytelność, należy użyć zagnieżdżonej składni podczas wypychania wielu obrazów:

```yml
version: v1.1.0
steps:
  # Nested YAML collection syntax
  - push:
    - $Registry/hello-world:$ID
    - $Registry/hello-world:latest
```

### <a name="properties-push"></a>Właściwości: push

Typ `push` kroku obsługuje następujące właściwości. Szczegółowe informacje o tych właściwościach znajdują się w sekcji [Właściwości kroku zadania](#task-step-properties) w tym artykule.

| | | |
| -------- | ---- | -------- |
| `env` | [ciąg, ciąg, ...] | Optional (Opcjonalność) |
| `id` | ciąg | Optional (Opcjonalność) |
| `ignoreErrors` | bool | Optional (Opcjonalność) |
| `startDelay` | int (sekundy) | Optional (Opcjonalność) |
| `timeout` | int (sekundy) | Optional (Opcjonalność) |
| `when` | [ciąg, ciąg, ...] | Optional (Opcjonalność) |

### <a name="examples-push"></a>Przykłady: wypychanie

#### <a name="push-multiple-images"></a>Wypychanie wielu obrazów

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-push-hello-world.yaml)]

#### <a name="build-push-and-run"></a>Tworzenie, wypychanie i uruchamianie

```azurecli
az acr run -f build-run-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-run-hello-world.yaml)]

## <a name="cmd"></a>cmd

Typ `cmd` kroku uruchamia kontener.

### <a name="syntax-cmd"></a>Składnia: cmd

```yml
version: v1.1.0
steps:
  - [cmd]: [containerImage]:[tag (optional)] [cmdParameters to the image]
```

### <a name="properties-cmd"></a>Właściwości: cmd

Typ `cmd` kroku obsługuje następujące właściwości:

| | | |
| -------- | ---- | -------- |
| `detach` | bool | Optional (Opcjonalność) |
| `disableWorkingDirectoryOverride` | bool | Optional (Opcjonalność) |
| `entryPoint` | ciąg | Optional (Opcjonalność) |
| `env` | [ciąg, ciąg, ...] | Optional (Opcjonalność) |
| `expose` | [ciąg, ciąg, ...] | Optional (Opcjonalność) |
| `id` | ciąg | Optional (Opcjonalność) |
| `ignoreErrors` | bool | Optional (Opcjonalność) |
| `isolation` | ciąg | Optional (Opcjonalność) |
| `keep` | bool | Optional (Opcjonalność) |
| `network` | obiekt | Optional (Opcjonalność) |
| `ports` | [ciąg, ciąg, ...] | Optional (Opcjonalność) |
| `pull` | bool | Optional (Opcjonalność) |
| `repeat` | int | Optional (Opcjonalność) |
| `retries` | int | Optional (Opcjonalność) |
| `retryDelay` | int (sekundy) | Optional (Opcjonalność) |
| `secret` | obiekt | Optional (Opcjonalność) |
| `startDelay` | int (sekundy) | Optional (Opcjonalność) |
| `timeout` | int (sekundy) | Optional (Opcjonalność) |
| `when` | [ciąg, ciąg, ...] | Optional (Opcjonalność) |
| `workingDirectory` | ciąg | Optional (Opcjonalność) |

Szczegółowe informacje na temat tych właściwości można znaleźć w sekcji [Właściwości kroku zadania](#task-step-properties) w tym artykule.

### <a name="examples-cmd"></a>Przykłady: cmd

#### <a name="run-hello-world-image"></a>Uruchom obraz hello-world

To polecenie wykonuje `hello-world.yaml` plik zadania, który odwołuje się do obrazu [hello-world](https://hub.docker.com/_/hello-world/) w centrum platformy Docker Hub.

```azurecli
az acr run -f hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.yaml -->
[!code-yml[task](~/acr-tasks/hello-world.yaml)]

#### <a name="run-bash-image-and-echo-hello-world"></a>Uruchom obraz bash i echo "hello world"

To polecenie wykonuje `bash-echo.yaml` plik zadania, który odwołuje się do obrazu [bash](https://hub.docker.com/_/bash/) w centrum platformy Docker Hub.

```azurecli
az acr run -f bash-echo.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo.yaml)]

#### <a name="run-specific-bash-image-tag"></a>Uruchamianie określonego znacznika obrazu bash

Aby uruchomić określoną wersję obrazu, `cmd`określ znacznik w pliku .

To polecenie wykonuje `bash-echo-3.yaml` plik zadania, który odwołuje się do obrazu [bash:3.0](https://hub.docker.com/_/bash/) w centrum platformy Docker Hub.

```azurecli
az acr run -f bash-echo-3.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo-3.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo-3.yaml)]

#### <a name="run-custom-images"></a>Uruchamianie obrazów niestandardowych

Typ `cmd` kroku odwołuje się do `docker run` obrazów przy użyciu standardowego formatu. Zakłada się, że obrazy niepoprzedzone rejestrem pochodzą z docker.io. Poprzedni przykład może być równie reprezentowany jako:

```yml
version: v1.1.0
steps:
  - cmd: docker.io/bash:3.0 echo hello world
```

Za pomocą `docker run` standardowej konwencji `cmd` odwołania obrazu, można uruchamiać obrazy z dowolnego rejestru prywatnego lub publicznego Centrum platformy Docker. Jeśli odwołujesz się do obrazów w tym samym rejestrze, w którym wykonuje się zadanie usługi ACR, nie musisz określać żadnych poświadczeń rejestru.

* Uruchom obraz pochodzący z rejestru kontenerów platformy Azure. W poniższym przykładzie przyjęto `myregistry`założenie, że `myimage:mytag`masz rejestr o nazwie i obraz niestandardowy .

    ```yml
    version: v1.1.0
    steps:
        - cmd: myregistry.azurecr.io/myimage:mytag
    ```

* Uogólnianie odwołania do rejestru za pomocą zmiennej lub aliasu Uruchom

    Zamiast twardego kodowania nazwy rejestru w `acr-task.yaml` pliku, można uczynić go bardziej przenośnym za pomocą [zmiennej run](#run-variables) lub [aliasu](#aliases). Zmienna `Run.Registry` `$Registry` lub alias rozszerza się w czasie wykonywania do nazwy rejestru, w którym zadanie jest wykonywane.

    Na przykład, aby uogólnić poprzednie zadanie, tak aby działało w dowolnym rejestrze kontenerów platformy Azure, odwołaj się do zmiennej $Registry w nazwie obrazu:

    ```yml
    version: v1.1.0
    steps:
      - cmd: $Registry/myimage:mytag
    ```

## <a name="task-step-properties"></a>Właściwości kroku zadania

Każdy typ kroku obsługuje kilka właściwości odpowiednich dla jego typu. W poniższej tabeli zdefiniowano wszystkie dostępne właściwości kroku. Nie wszystkie typy kroków obsługują wszystkie właściwości. Aby zobaczyć, które z tych właściwości są dostępne dla każdego typu kroku, zobacz sekcje odniesienia [cmd](#cmd), [build](#build)i [push](#push) step.

| Właściwość | Typ | Optional (Opcjonalność) | Opis | Wartość domyślna |
| -------- | ---- | -------- | ----------- | ------- |
| `detach` | bool | Tak | Czy kontener powinien być odłączony podczas pracy. | `false` |
| `disableWorkingDirectoryOverride` | bool | Tak | Czy wyłączyć `workingDirectory` funkcję zastępowania. Użyj tego w `workingDirectory` połączeniu z mieć pełną kontrolę nad katalogiem roboczym kontenera. | `false` |
| `entryPoint` | ciąg | Tak | Zastępuje `[ENTRYPOINT]` kontener kroku. | Brak |
| `env` | [ciąg, ciąg, ...] | Tak | Tablica ciągów `key=value` w formacie definiujących zmienne środowiskowe dla kroku. | Brak |
| `expose` | [ciąg, ciąg, ...] | Tak | Tablica portów, które są widoczne z kontenera. |  Brak |
| [`id`](#example-id) | ciąg | Tak | Jednoznacznie identyfikuje krok w ramach zadania. Inne kroki w zadaniu można `id`odwołać się do kroku, `when`na przykład do sprawdzania zależności z .<br /><br />Jest `id` również nazwa uruchomionego kontenera. Procesy uruchomione w innych kontenerach `id` w zadaniu mogą odnosić się do nazwy hosta DNS jako nazwy hosta DNS lub uzyskiwania do niego dostępu za pomocą dzienników docker [id], na przykład. | `acb_step_%d`, `%d` gdzie znajduje się indeks 0-based kroku z do góry w pliku YAML |
| `ignoreErrors` | bool | Tak | Czy oznaczyć krok jako pomyślny, niezależnie od tego, czy wystąpił błąd podczas wykonywania kontenera. | `false` |
| `isolation` | ciąg | Tak | Poziom izolacji kontenera. | `default` |
| `keep` | bool | Tak | Czy kontener kroku powinny być przechowywane po wykonaniu. | `false` |
| `network` | obiekt | Tak | Identyfikuje sieć, w której jest uruchamiany kontener. | Brak |
| `ports` | [ciąg, ciąg, ...] | Tak | Tablica portów, które są publikowane z kontenera do hosta. |  Brak |
| `pull` | bool | Tak | Czy wymusić pociągnięcie kontenera przed wykonaniem go, aby zapobiec wszelkim zachowaniem buforowania. | `false` |
| `privileged` | bool | Tak | Czy kontener ma być uruchamiany w trybie uprzywilejowanym. | `false` |
| `repeat` | int | Tak | Liczba ponownych prób powtórzenia wykonania kontenera. | 0 |
| `retries` | int | Tak | Liczba ponownych prób, aby spróbować, jeśli kontener nie powiedzie się jego wykonanie. Ponowna próba jest podejmowana tylko wtedy, gdy kod zakończenia kontenera jest niezerowy. | 0 |
| `retryDelay` | int (sekundy) | Tak | Opóźnienie w sekundach między ponownych prób wykonania kontenera. | 0 |
| `secret` | obiekt | Tak | Identyfikuje klucz tajny usługi Azure Key Vault lub [tożsamość zarządzana dla zasobów platformy Azure.](container-registry-tasks-authentication-managed-identity.md) | Brak |
| `startDelay` | int (sekundy) | Tak | Liczba sekund, aby opóźnić wykonanie kontenera. | 0 |
| `timeout` | int (sekundy) | Tak | Maksymalna liczba sekund krok może wykonać przed zakończeniem. | 600 |
| [`when`](#example-when) | [ciąg, ciąg, ...] | Tak | Konfiguruje zależność kroku od jednego lub kilku innych kroków w ramach zadania. | Brak |
| `user` | ciąg | Tak | Nazwa użytkownika lub identyfikator UID kontenera | Brak |
| `workingDirectory` | ciąg | Tak | Ustawia katalog roboczy dla kroku. Domyślnie program ACR Tasks tworzy katalog główny jako katalog roboczy. Jeśli jednak kompilacja ma kilka kroków, wcześniejsze kroki można udostępniać artefakty z późniejszych kroków, określając ten sam katalog roboczy. | `/workspace` |

### <a name="examples-task-step-properties"></a>Przykłady: Właściwości kroku zadania

#### <a name="example-id"></a>Przykład: id

Tworzenie dwóch obrazów, instancing funkcjonalny obraz testowy. Każdy krok jest identyfikowany przez unikatowy, `id` które `when` inne kroki w odwołaniu do zadania w ich właściwości.

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

#### <a name="example-when"></a>Przykład: gdy

Właściwość `when` określa zależność kroku od innych kroków w ramach zadania. Obsługuje dwie wartości parametrów:

* `when: ["-"]`- Wskazuje brak zależności od innych kroków. Krok określania `when: ["-"]` rozpocznie wykonywanie natychmiast i umożliwia wykonywanie kroku równoczesnych.
* `when: ["id1", "id2"]`- Wskazuje, że krok zależy `id` od kroków `id` z "id1" i "id2". Ten krok nie zostanie wykonany, dopóki nie zostaną wykonane kroki "id1" i "id2".

Jeśli `when` nie jest określony w kroku, ten krok zależy od `acr-task.yaml` zakończenia poprzedniego kroku w pliku.

Wykonanie kroku sekwencyjnego bez: `when`

```azurecli
az acr run -f when-sequential-default.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-default.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-default.yaml)]

Wykonanie kroku sekwencyjnego z: `when`

```azurecli
az acr run -f when-sequential-id.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-id.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-id.yaml)]

Budowanie obrazów równoległych:

```azurecli
az acr run -f when-parallel.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel.yaml)]

Kompilacja obrazu równoległego i testowanie zależne:

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

## <a name="run-variables"></a>Uruchamianie zmiennych

Zadania usługi ACR zawierają domyślny zestaw zmiennych, które są dostępne do wykonania kroków zadań. Dostęp do tych zmiennych można `{{.Run.VariableName}}`uzyskać `VariableName` za pomocą formatu , gdzie jest jedna z następujących czynności:

* `Run.ID`
* `Run.SharedVolume`
* `Run.Registry`
* `Run.RegistryName`
* `Run.Date`
* `Run.OS`
* `Run.Architecture`
* `Run.Commit`
* `Run.Branch`
* `Run.TaskName`

Nazwy zmiennych są na ogół oczywiste. Szczegóły są następuje dla często używanych zmiennych. W wersji `v1.1.0`YAML zamiast większości uruchamianych zmiennych można użyć skróconego, wstępnie zdefiniowanego [aliasu zadań.](#aliases) Na przykład zamiast `{{.Run.Registry}}`, użyj `$Registry` aliasu.

### <a name="runid"></a>Run.ID

Każdy run, `az acr run`za pośrednictwem , lub `az acr task create`wyzwalacza na podstawie wykonywania zadań utworzonych za pośrednictwem , ma unikatowy identyfikator. Identyfikator reprezentuje Run aktualnie wykonywane.

Zazwyczaj używane do unikatowego tagowania obrazu:

```yml
version: v1.1.0
steps:
    - build: -t $Registry/hello-world:$ID .
```

### <a name="runregistry"></a>Uruchom.Rejestr

W pełni kwalifikowana nazwa serwera rejestru. Zazwyczaj używane do ogólnego odwoływania się do rejestru, w którym zadanie jest uruchamiane.

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID .
```

### <a name="runregistryname"></a>Nazwa pliku Run.Registry

Nazwa rejestru kontenerów. Zazwyczaj używane w krokach zadań, które nie wymagają w `cmd` pełni kwalifikowanej nazwy serwera, na przykład kroki, które uruchamiają polecenia interfejsu wiersza polecenia platformy Azure w rejestrach.

```yml
version 1.1.0
steps:
# List repositories in registry
- cmd: az login --identity
- cmd: az acr repository list --name $RegistryName
```

### <a name="rundate"></a>Uruchom.Data

Bieżący czas UTC rozpoczęty bieg.

### <a name="runcommit"></a>Uruchom.Commit

Dla zadania wyzwalanego przez zatwierdzenie do repozytorium GitHub identyfikator zatwierdzenia.

### <a name="runbranch"></a>Uruchom.Gałąź

Dla zadania wyzwalanego przez zatwierdzenie do repozytorium GitHub, nazwa gałęzi.

## <a name="aliases"></a>Aliasy

W `v1.1.0`przypadku programu ACR Tasks obsługuje aliasy, które są dostępne dla kroków zadań podczas wykonywania. Aliasy są podobne w koncepcji do aliasów (skrótów poleceń) obsługiwanych w bash i niektórych innych powłokach poleceń. 

Za pomocą aliasu można uruchomić dowolne polecenie lub grupę poleceń (w tym opcje i nazwy plików), wprowadzając jedno słowo.

Zadania usługi ACR obsługują kilka wstępnie zdefiniowanych aliasów, a także utworzone aliasy niestandardowe.

### <a name="predefined-aliases"></a>Wstępnie zdefiniowane aliasy

Zamiast [zmiennych uruchamiania](#run-variables)dostępne są następujące aliasy zadań:

| Alias | Uruchom zmienną |
| ----- | ------------ |
| `ID` | `Run.ID` |
| `SharedVolume` | `Run.SharedVolume` |
| `Registry` | `Run.Registry` |
| `RegistryName` | `Run.RegistryName` |
| `Date` | `Run.Date` |
| `OS` | `Run.OS` |
| `Architecture` | `Run.Architecture` |
| `Commit` | `Run.Commit` |
| `Branch` | `Run.Branch` |

W krokach zadania poprzedzić `$` alias z dyrektywą, jak w tym przykładzie:

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID -f hello-world.dockerfile .
```

### <a name="image-aliases"></a>Aliasy obrazów

Każdy z poniższych aliasów wskazuje na stabilny obraz w rejestrze kontenerów firmy Microsoft (MCR). Można odwołać się do `cmd` każdego z nich w sekcji pliku zadania bez użycia dyrektywy.

| Alias | Image (Obraz) |
| ----- | ----- |
| `acr` | `mcr.microsoft.com/acr/acr-cli:0.1` |
| `az` | `mcr.microsoft.com/acr/azure-cli:a80af84` |
| `bash` | `mcr.microsoft.com/acr/bash:a80af84` |
| `curl` | `mcr.microsoft.com/acr/curl:a80af84` |

Następujące przykładowe zadanie używa kilku aliasów do [przeczyszania](container-registry-auto-purge.md) znaczników `samples/hello-world` obrazu starszych niż 7 dni w repozytorium w rejestrze uruchamiania:

```yml
version: v1.1.0
steps:
  - cmd: acr tag list --registry $RegistryName --repository samples/hello-world
  - cmd: acr purge --registry $RegistryName --filter samples/hello-world:.* --ago 7d
```

### <a name="custom-alias"></a>Alias niestandardowy

Zdefiniuj alias niestandardowy w pliku YAML i użyj go w sposób pokazany w poniższym przykładzie. Alias może zawierać tylko znaki alfanumeryczne. Domyślną dyrektywą rozszerzaną alias jest `$` znak.

```yml
version: v1.1.0
alias:
  values:
    repo: myrepo
steps:
  - build: -t $Registry/$repo/hello-world:$ID -f Dockerfile .
```

Można połączyć się z zdalnym lub lokalnym plikiem YAML dla definicji aliasów niestandardowych. Poniższe przykładowe łącza do pliku YAML w magazynie obiektów blob platformy Azure:

```yml
version: v1.1.0
alias:
  src:  # link to local or remote custom alias files
    - 'https://link/to/blob/remoteAliases.yml?readSasToken'
[...]
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać omówienie zadań wieloetapowych, zobacz [Uruchamianie wieloetapowych zadań kompilacji, testowania i instalowania poprawek w zadaniach usługi ACR](container-registry-tasks-multi-step.md).

Aby zapoznać się z kompilacjami jednoetapowymi, zobacz [omówienie zadań usługi ACR](container-registry-tasks-overview.md).



<!-- IMAGES -->

<!-- LINKS - External -->
[acr-tasks]: https://github.com/Azure-Samples/acr-tasks

<!-- LINKS - Internal -->
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-configure]: /cli/azure/reference-index#az-configure

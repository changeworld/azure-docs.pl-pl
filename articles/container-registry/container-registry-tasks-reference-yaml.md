---
title: Odwołania do zadań Azure Container Registry — YAML
description: Dokumentacja dotycząca definiowania zadań w YAML dla zadań ACR, takich jak właściwości zadania, typy kroków, właściwości kroku i wbudowane zmienne.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 07/12/2019
ms.author: danlep
ms.openlocfilehash: 27c38f51104dfb170c59860c96a8e3a86973bb1e
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638924"
---
# <a name="acr-tasks-reference-yaml"></a>Informacje o zadaniach ACR: YAML

Wieloetapowa Definicja zadania w zadaniach ACR udostępnia skoncentrowane na kontenerach pierwotne obliczenia, ukierunkowane na tworzenie, testowanie i stosowanie poprawek kontenerów. W tym artykule opisano polecenia, parametry, właściwości i składni dla plików YAML, które definiują zadania wieloetapowe.

Ten artykuł zawiera informacje dotyczące tworzenia wieloetapowych plików YAML zadań dla zadań ACR. Jeśli chcesz dowiedzieć się więcej na temat ACR zadań, zobacz [Omówienie zadań ACR](container-registry-tasks-overview.md).

## <a name="acr-taskyaml-file-format"></a>ACR — format pliku Task. YAML

Zadania ACR obsługują Wieloetapową deklarację zadań w standardowej składni YAML. Zdefiniuj kroki zadania w pliku YAML. Następnie można uruchomić zadanie ręcznie, przekazując plik do polecenia [AZ ACR Run][az-acr-run] . Lub użyj pliku, aby utworzyć zadanie za pomocą [AZ ACR Task Create][az-acr-task-create] , które jest wyzwalane automatycznie w ramach zatwierdzenia Git lub aktualizacji obrazu podstawowego. Mimo że ten artykuł odnosi się do `acr-task.yaml` pliku zawierającego kroki, ACR zadania obsługują dowolną prawidłową nazwę pliku z [obsługiwanym rozszerzeniem](#supported-task-filename-extensions).

Elementy podstawowe najwyższego `acr-task.yaml` poziomu to **właściwości zadania**, **typy kroków**i **Właściwości kroków**:

* [Właściwości zadania](#task-properties) dotyczą wszystkich kroków wykonywanych w trakcie wykonywania zadań. Istnieje kilka globalnych właściwości zadań, w tym:
  * `version`
  * `stepTimeout`
  * `workingDirectory`
* [Typy etapów zadań](#task-step-types) reprezentują typy akcji, które mogą być wykonywane w zadaniu. Istnieją trzy typy kroków:
  * `build`
  * `push`
  * `cmd`
* [Właściwości kroku zadania](#task-step-properties) są parametrami, które są stosowane do poszczególnych kroków. Istnieje kilka właściwości kroku, w tym:
  * `startDelay`
  * `timeout`
  * `when`
  * ... i wiele innych.

Format `acr-task.yaml` podstawowy pliku, w tym kilka typowych właściwości kroku, znajduje się poniżej. Chociaż nie jest to pełna reprezentacja wszystkich dostępnych właściwości kroku lub typu kroku, zawiera krótkie omówienie podstawowego formatu pliku.

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

### <a name="supported-task-filename-extensions"></a>Obsługiwane rozszerzenia nazw zadań

Zadania ACR zarezerwowali kilka rozszerzeń nazw plików `.yaml`, w tym, że będą przetwarzane jako plik zadania. Wszystkie rozszerzenia, których *nie* ma na poniższej liście, są uznawane za przez ACR zadania jako pliku dockerfile:. YAML,. yml,. toml,. JSON,...,. bash,. zsh,. ps1,. PS,. cmd,. bat,. TS,. js,. php,. PR,. RB,. lua

YAML jest jedynym formatem pliku obsługiwanym obecnie przez zadania ACR. Inne rozszerzenia nazw plików są zarezerwowane do możliwej przyszłej pomocy technicznej.

## <a name="run-the-sample-tasks"></a>Uruchamianie przykładowych zadań

W poniższych sekcjach tego artykułu istnieje kilka przykładowych plików zadań, do których odwołuje się. Przykładowe zadania znajdują się w publicznym repozytorium GitHub, [Azure-Samples/ACR-Tasks][acr-tasks]. Można uruchomić je za pomocą polecenia interfejsu wiersza poleceń platformy Azure [AZ ACR Run][az-acr-run]. Przykładowe polecenia są podobne do:

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

Formatowanie przykładowych poleceń założono, że skonfigurowano rejestr domyślny w interfejsie wiersza polecenia platformy Azure, aby pominąć `--registry` parametr. Aby skonfigurować domyślny rejestr, użyj polecenia [AZ Configure][az-configure] z `--defaults` parametrem `acr=REGISTRY_NAME` , który akceptuje wartość.

Na przykład, aby skonfigurować interfejs wiersza polecenia platformy Azure z domyślnym rejestrem o nazwie "Moje rejestr":

```azurecli
az configure --defaults acr=myregistry
```

## <a name="task-properties"></a>Właściwości zadania

Właściwości zadania zwykle pojawiają się u góry `acr-task.yaml` pliku i są właściwościami globalnymi, które są stosowane w całym wykonaniu kroków zadania. Niektóre z tych właściwości globalnych można przesłonić w ramach pojedynczego kroku.

| Właściwość | Type | Optional | Opis | Przesłonięcie obsługiwane | Wartość domyślna |
| -------- | ---- | -------- | ----------- | ------------------ | ------------- |
| `version` | ciąg | Yes | Wersja `acr-task.yaml` pliku, przeanalizowana przez usługę zadań ACR. Chociaż zadania ACR dążą do zachowania zgodności z poprzednimi wersjami, ta wartość umożliwia ACR zadań w celu zachowania zgodności w ramach zdefiniowanej wersji. Jeśli nie zostanie określony, wartość domyślna to Najnowsza wersja. | Nie | Brak |
| `stepTimeout` | int (sekundy) | Yes | Maksymalna liczba sekund, przez jaką krok może zostać uruchomiony. Jeśli właściwość jest określona w zadaniu, ustawia domyślną `timeout` Właściwość wszystkich kroków. `timeout` Jeśli właściwość jest określona w kroku, zastępuje właściwość dostarczoną przez zadanie. | Tak | 600 (10 minut) |
| `workingDirectory` | ciąg | Tak | Katalog roboczy kontenera w czasie wykonywania. Jeśli właściwość jest określona w zadaniu, ustawia domyślną `workingDirectory` Właściwość wszystkich kroków. Jeśli określono w kroku, zastępuje on Właściwość dostarczoną przez zadanie. | Tak | `$HOME` |
| `env` | [ciąg, String,...] | Tak |  Tablica ciągów w `key=value` formacie, która definiuje zmienne środowiskowe dla zadania. Jeśli właściwość jest określona w zadaniu, ustawia domyślną `env` Właściwość wszystkich kroków. Jeśli jest określony w kroku, zastępuje wszystkie zmienne środowiskowe dziedziczone z zadania. | Brak |
| `secrets` | [Secret, Secret,...] | Tak | Tablica obiektów [tajnych](#secret) . | Brak |
| `networks` | [Sieć, Sieć,...] | Tak | Tablica obiektów [sieciowych](#network) . | Brak |

### <a name="secret"></a>secret

Obiekt tajny ma następujące właściwości.

| Właściwość | Type | Optional | Opis | Wartość domyślna |
| -------- | ---- | -------- | ----------- | ------- |
| `id` | ciąg | Nie | Identyfikator wpisu tajnego. | Brak |
| `keyvault` | ciąg | Yes | Azure Key Vault tajny adres URL. | Brak |
| `clientID` | ciąg | Yes | Identyfikator klienta [zarządzanej tożsamości przypisanej przez użytkownika](container-registry-tasks-authentication-managed-identity.md) dla zasobów platformy Azure. | Brak |

### <a name="network"></a>sieć

Obiekt sieciowy ma następujące właściwości.

| Właściwość | Type | Optional | Opis | Wartość domyślna |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | ciąg | Nie | Nazwa sieci. | Brak |
| `driver` | ciąg | Yes | Sterownik do zarządzania siecią. | Brak |
| `ipv6` | bool | Tak | Czy jest włączona obsługa sieci IPv6. | `false` |
| `skipCreation` | bool | Yes | Określa, czy pominąć tworzenie sieci. | `false` |
| `isDefault` | bool | Tak | Czy sieć jest siecią domyślną zapewnianą przez Azure Container Registry | `false` |

## <a name="task-step-types"></a>Typy kroków zadań

Zadania ACR obsługują trzy typy kroków. Każdy typ kroku obsługuje kilka właściwości, szczegółowo w sekcji dla każdego typu kroku.

| Typ kroku | Opis |
| --------- | ----------- |
| [`build`](#build) | Kompiluje obraz kontenera przy użyciu `docker build` znanej składni. |
| [`push`](#push) | `docker push` Wykonuje nowo utworzone lub ponownie oznaczone obrazy do rejestru kontenerów. Obsługiwane są Azure Container Registry, inne rejestry prywatne i publiczne centrum platformy Docker. |
| [`cmd`](#cmd) | Uruchamia kontener jako polecenie z parametrami przekazaną do kontenera `[ENTRYPOINT]`. Typ kroku obsługuje parametry takie jak `env`, `detach`i inne znane `docker run` opcje polecenia, co umożliwia testowanie jednostkowe i funkcjonalne przy równoczesnym wykonywaniu kontenerów. `cmd` |

## <a name="build"></a>utworzenia

Tworzenie obrazu kontenera. Typ kroku reprezentuje wielodostępną, bezpieczną metodę uruchamiania `docker build` w chmurze jako pierwszą klasę pierwotną. `build`

### <a name="syntax-build"></a>Składnia: kompilacja

```yml
version: v1.0.0
steps:
  - [build]: -t [imageName]:[tag] -f [Dockerfile] [context]
    [property]: [value]
```

Typ `build` kroku obsługuje parametry w poniższej tabeli. Typ kroku obsługuje także wszystkie opcje kompilacji polecenia Docker [Build](https://docs.docker.com/engine/reference/commandline/build/) , takie jak `--build-arg` , aby ustawić zmienne czasu kompilacji. `build`

| Parametr | Opis | Optional |
| --------- | ----------- | :-------: |
| `-t` &#124; `--image` | Definiuje w pełni kwalifikowana `image:tag` dla skompilowanego obrazu.<br /><br />Ponieważ obrazy mogą być używane do sprawdzania poprawności zadań wewnętrznych, takich jak testy funkcjonalne, nie wszystkie obrazy wymagają `push` do rejestru. Jednak w celu wystąpienia obrazu w ramach wykonywania zadania obraz musi mieć nazwę do odwołania.<br /><br />W przeciwieństwie do `az acr build`, uruchamianie zadań ACR nie zapewnia domyślnego zachowania wypychania. W przypadku zadań ACR w scenariuszu domyślnym założono możliwość kompilowania, weryfikowania i wypychania obrazu. Zobacz [wypychanie](#push) , aby dowiedzieć się, jak opcjonalnie wypchnąć skompilowane obrazy. | Tak |
| `-f` &#124; `--file` | Określa pliku dockerfile przekazaną `docker build`do. Jeśli nie zostanie określony, przyjmuje się domyślny pliku dockerfile w katalogu głównym kontekstu. Aby określić pliku dockerfile, przekaż nazwę pliku względem katalogu głównego kontekstu. | Tak |
| `context` | Katalog główny przeszedł do `docker build`. Katalog główny każdego zadania jest ustawiony na [WorkingDirectory](#task-step-properties)udostępnione i zawiera katalog główny skojarzonego sklonowanego katalogu Git. | Nie |

### <a name="properties-build"></a>Właściwości: kompilacja

Typ `build` kroku obsługuje następujące właściwości. Szczegółowe informacje o tych właściwościach znajdują się w sekcji [Właściwości kroku zadania](#task-step-properties) w tym artykule.

| | | |
| -------- | ---- | -------- |
| `detach` | bool | Optional |
| `disableWorkingDirectoryOverride` | bool | Optional |
| `entryPoint` | ciąg | Optional |
| `env` | [ciąg, String,...] | Optional |
| `expose` | [ciąg, String,...] | Optional |
| `id` | ciąg | Optional |
| `ignoreErrors` | bool | Optional |
| `isolation` | ciąg | Optional |
| `keep` | bool | Optional |
| `network` | object | Optional |
| `ports` | [ciąg, String,...] | Optional |
| `pull` | bool | Optional |
| `repeat` | int | Optional |
| `retries` | int | Optional |
| `retryDelay` | int (sekundy) | Optional |
| `secret` | object | Optional |
| `startDelay` | int (sekundy) | Optional |
| `timeout` | int (sekundy) | Optional |
| `when` | [ciąg, String,...] | Optional |
| `workingDirectory` | ciąg | Optional |

### <a name="examples-build"></a>Przykłady: kompilacja

#### <a name="build-image---context-in-root"></a>Kompilacja kontekstu obrazu w elemencie głównym

```azurecli
az acr run -f build-hello-world.yaml https://github.com/AzureCR/acr-tasks-sample.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-hello-world.yaml)]

#### <a name="build-image---context-in-subdirectory"></a>Kompilacja kontekstu obrazu w podkatalogu

```yml
version: v1.0.0
steps:
  - build: -t {{.Run.Registry}}/hello-world -f hello-world.dockerfile ./subDirectory
```

## <a name="push"></a>wydajności

Wypychanie co najmniej jednego skompilowanego lub ponownie oznaczonego obrazu do rejestru kontenerów. Obsługuje wypychanie do rejestrów prywatnych, takich jak Azure Container Registry, lub do publicznego centrum Docker.

### <a name="syntax-push"></a>Składnia: push

Typ `push` kroku obsługuje kolekcję obrazów. Składnia kolekcji YAML obsługuje formaty wbudowane i zagnieżdżone. Wypychanie pojedynczego obrazu jest zwykle reprezentowane przy użyciu składni wbudowanej:

```yml
version: v1.0.0
steps:
  # Inline YAML collection syntax
  - push: ["{{.Run.Registry}}/hello-world:{{.Run.ID}}"]
```

Aby zwiększyć czytelność, Użyj zagnieżdżonej składni podczas wypychania wielu obrazów:

```yml
version: v1.0.0
steps:
  # Nested YAML collection syntax
  - push:
    - {{.Run.Registry}}/hello-world:{{.Run.ID}}
    - {{.Run.Registry}}/hello-world:latest
```

### <a name="properties-push"></a>Właściwości: wypychanie

Typ `push` kroku obsługuje następujące właściwości. Szczegółowe informacje o tych właściwościach znajdują się w sekcji [Właściwości kroku zadania](#task-step-properties) w tym artykule.

| | | |
| -------- | ---- | -------- |
| `env` | [ciąg, String,...] | Optional |
| `id` | ciąg | Optional |
| `ignoreErrors` | bool | Optional |
| `startDelay` | int (sekundy) | Optional |
| `timeout` | int (sekundy) | Optional |
| `when` | [ciąg, String,...] | Optional |

### <a name="examples-push"></a>Przykłady: wypychanie

#### <a name="push-multiple-images"></a>Wypychanie wielu obrazów

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-push-hello-world.yaml)]

#### <a name="build-push-and-run"></a>Kompilowanie, wypychanie i uruchamianie

```azurecli
az acr run -f build-run-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-run-hello-world.yaml)]

## <a name="cmd"></a>cmd

Typ `cmd` kroku uruchamia kontener.

### <a name="syntax-cmd"></a>Składnia: cmd

```yml
version: v1.0.0
steps:
  - [cmd]: [containerImage]:[tag (optional)] [cmdParameters to the image]
```

### <a name="properties-cmd"></a>Właściwości: cmd

Typ `cmd` kroku obsługuje następujące właściwości:

| | | |
| -------- | ---- | -------- |
| `detach` | bool | Optional |
| `disableWorkingDirectoryOverride` | bool | Optional |
| `entryPoint` | ciąg | Optional |
| `env` | [ciąg, String,...] | Optional |
| `expose` | [ciąg, String,...] | Optional |
| `id` | ciąg | Optional |
| `ignoreErrors` | bool | Optional |
| `isolation` | ciąg | Optional |
| `keep` | bool | Optional |
| `network` | object | Optional |
| `ports` | [ciąg, String,...] | Optional |
| `pull` | bool | Optional |
| `repeat` | int | Optional |
| `retries` | int | Optional |
| `retryDelay` | int (sekundy) | Optional |
| `secret` | object | Optional |
| `startDelay` | int (sekundy) | Optional |
| `timeout` | int (sekundy) | Optional |
| `when` | [ciąg, String,...] | Optional |
| `workingDirectory` | ciąg | Optional |

Szczegółowe informacje o tych właściwościach można znaleźć w sekcji [Właściwości kroku zadania](#task-step-properties) w tym artykule.

### <a name="examples-cmd"></a>Przykłady: cmd

#### <a name="run-hello-world-image"></a>Uruchom obraz Hello-World

To polecenie wykonuje `hello-world.yaml` plik zadania, który odwołuje się do obrazu [Hello-World](https://hub.docker.com/_/hello-world/) w usłudze Docker Hub.

```azurecli
az acr run -f hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.yaml -->
[!code-yml[task](~/acr-tasks/hello-world.yaml)]

#### <a name="run-bash-image-and-echo-hello-world"></a>Uruchamianie obrazu bash i echo "Hello World"

To polecenie wykonuje `bash-echo.yaml` plik zadania, który odwołuje się do obrazu [bash](https://hub.docker.com/_/bash/) w usłudze Docker Hub.

```azurecli
az acr run -f bash-echo.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo.yaml)]

#### <a name="run-specific-bash-image-tag"></a>Uruchom określony tag obrazu bash

Aby uruchomić określoną wersję obrazu, należy określić tag w `cmd`.

To polecenie wykonuje `bash-echo-3.yaml` plik zadania, który odwołuje się do obrazu [bash: 3.0](https://hub.docker.com/_/bash/) w usłudze Docker Hub.

```azurecli
az acr run -f bash-echo-3.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo-3.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo-3.yaml)]

#### <a name="run-custom-images"></a>Uruchamianie obrazów niestandardowych

Typ kroku odwołuje się do obrazów przy użyciu formatu standardowego `docker run`. `cmd` Obrazy, które nie są poprzedzone rejestrem, zakłada się, że pochodzą z docker.io. Poprzedni przykład może być równy reprezentowany jako:

```yml
version: v1.0.0
steps:
  - cmd: docker.io/bash:3.0 echo hello world
```

Przy użyciu standardowej `docker run` `cmd` Konwencji odwołania do obrazu można uruchamiać obrazy z dowolnego rejestru prywatnego lub publicznego centrum Docker. Jeśli odwołujesz się do obrazów w tym samym rejestrze, w którym jest wykonywane zadanie ACR, nie musisz określać żadnych poświadczeń rejestru.

* Uruchamianie obrazu z usługi Azure Container Registry

    Zamień `[myregistry]` na nazwę rejestru:

    ```yml
    version: v1.0.0
    steps:
        - cmd: [myregistry].azurecr.io/bash:3.0 echo hello world
    ```

* Uogólnij odwołanie do rejestru za pomocą zmiennej Run

    Zamiast nakodować twarde nazwy rejestru w `acr-task.yaml` pliku, można uczynić go bardziej przenośnym za pomocą [zmiennej uruchamiania](#run-variables). `Run.Registry` Zmienna rozszerza się w czasie wykonywania do nazwy rejestru, w którym wykonywane jest zadanie.

    Aby uogólnić poprzednie zadanie w taki sposób, aby było ono działać w dowolnym rejestrze kontenera platformy Azure, odwołując się do zmiennej [Run. Registry](#runregistry) w nazwie obrazu:

    ```yml
    version: v1.0.0
    steps:
      - cmd: {{.Run.Registry}}/bash:3.0 echo hello world
    ```

## <a name="task-step-properties"></a>Właściwości kroku zadania

Każdy typ kroku obsługuje kilka właściwości, które są odpowiednie dla tego typu. W poniższej tabeli zdefiniowano wszystkie dostępne właściwości kroku. Nie wszystkie typy kroków obsługują wszystkie właściwości. Aby zobaczyć, które z tych właściwości są dostępne dla każdego typu kroku, zapoznaj się z sekcjami odwołania dla kroków [cmd](#cmd), [Build](#build)i [push](#push) .

| Właściwość | Type | Optional | Opis | Wartość domyślna |
| -------- | ---- | -------- | ----------- | ------- |
| `detach` | bool | Yes | Określa, czy kontener ma zostać odłączony podczas uruchamiania. | `false` |
| `disableWorkingDirectoryOverride` | bool | Yes | Określa, czy `workingDirectory` należy wyłączyć funkcję przesłonięcia. Użyj tej usługi w połączeniu `workingDirectory` z programem, aby mieć pełną kontrolę nad katalogiem roboczym kontenera. | `false` |
| `entryPoint` | ciąg | Tak | `[ENTRYPOINT]` Zastępuje kontener kroku. | Brak |
| `env` | [ciąg, String,...] | Tak | Tablica ciągów w `key=value` formacie, która definiuje zmienne środowiskowe dla kroku. | Brak |
| `expose` | [ciąg, String,...] | Tak | Tablica portów, które są udostępniane z kontenera. |  Brak |
| [`id`](#example-id) | ciąg | Tak | Unikatowy identyfikator kroku w zadaniu. Inne kroki w zadaniu mogą odwoływać się do `id`kroku, takiego jak sprawdzanie zależności z `when`.<br /><br />`id` Jest również nazwą uruchomionego kontenera. Procesy działające w innych kontenerach w zadaniu mogą odwoływać się do `id` nazwy hosta DNS lub do uzyskiwania do nich dostępu przy użyciu dzienników platformy Docker [ID]. | `acb_step_%d`, gdzie `%d` jest indeksem 0 na początku kroku w pliku YAML |
| `ignoreErrors` | bool | Tak | Określa, czy krok ma być oznaczany pomyślnie, niezależnie od tego, czy wystąpił błąd podczas wykonywania kontenera. | `false` |
| `isolation` | ciąg | Tak | Poziom izolacji kontenera. | `default` |
| `keep` | bool | Yes | Czy kontener kroku powinien być przechowywany po wykonaniu. | `false` |
| `network` | object | Yes | Identyfikuje sieć, w której działa kontener. | Brak |
| `ports` | [ciąg, String,...] | Tak | Tablica portów publikowanych z kontenera do hosta. |  Brak |
| `pull` | bool | Tak | Określa, czy należy wymusić ściąganie kontenera przed jego wykonaniem, aby zapobiec jakimkolwiek zachowaniem buforowania. | `false` |
| `privileged` | bool | Yes | Określa, czy ma być uruchamiany kontener w trybie uprzywilejowanym. | `false` |
| `repeat` | int | Tak | Liczba ponownych prób powtarzania wykonywania kontenera. | 0 |
| `retries` | int | Yes | Liczba ponownych prób, które należy podjąć, jeśli kontener nie zostanie wykonany. Próba ponowienia następuje tylko wtedy, gdy kod zakończenia kontenera jest różny od zera. | 0 |
| `retryDelay` | int (sekundy) | Yes | Opóźnienie w sekundach między ponownymi próbami wykonania kontenera. | 0 |
| `secret` | object | Tak | Identyfikuje wpis tajny Azure Key Vault lub [zarządzaną tożsamość dla zasobów platformy Azure](container-registry-tasks-authentication-managed-identity.md). | Brak |
| `startDelay` | int (sekundy) | Yes | Liczba sekund, przez którą ma zostać opóźnione wykonywanie kontenera. | 0 |
| `timeout` | int (sekundy) | Yes | Maksymalna liczba sekund, przez jaką krok może zostać wykonany przed zakończeniem. | 600 |
| [`when`](#example-when) | [ciąg, String,...] | Tak | Konfiguruje zależność kroku od jednego lub kilku innych kroków w ramach zadania. | Brak |
| `user` | ciąg | Tak | Nazwa użytkownika lub identyfikator UID kontenera | Brak |
| `workingDirectory` | ciąg | Tak | Ustawia katalog roboczy dla kroku. Domyślnie zadania ACR tworzą katalog główny jako katalog roboczy. Jeśli jednak kompilacja zawiera kilka kroków, wcześniejsze kroki mogą współużytkować artefakty z późniejszymi krokami, określając ten sam katalog roboczy. | `$HOME` |

### <a name="examples-task-step-properties"></a>Przykłady: Właściwości kroku zadania

#### <a name="example-id"></a>Przykład: Identyfikator

Kompiluj dwa obrazy, tworząc w ten sposób obraz testu funkcjonalnego. Każdy krok jest identyfikowany przez unikatowe `id` , które inne kroki w odwołaniu do zadania `when` we właściwości.

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

#### <a name="example-when"></a>Przykład: Kiedy

`when` Właściwość określa zależność kroku od innych kroków w ramach zadania. Obsługuje dwie wartości parametrów:

* `when: ["-"]`-Wskazuje, że nie ma zależności od innych kroków. Krok określający `when: ["-"]` , że rozpocznie wykonywanie wykonywania natychmiast, i włącza współbieżne wykonywanie kroków.
* `when: ["id1", "id2"]`-Wskazuje, że krok zależy od kroków z `id` "ID1" i `id` "ID2". Ten krok nie zostanie wykonany do czasu zakończenia obu kroków "ID1" i "ID2".

Jeśli `when` nie jest określony w kroku, ten krok zależy od ukończenia poprzedniego kroku `acr-task.yaml` w pliku.

Wykonywanie sekwencyjne bez `when`:

```azurecli
az acr run -f when-sequential-default.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-default.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-default.yaml)]

Wykonywanie sekwencyjne przy `when`użyciu:

```azurecli
az acr run -f when-sequential-id.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-id.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-id.yaml)]

Kompilacja obrazów równoległych:

```azurecli
az acr run -f when-parallel.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel.yaml)]

Równoległa kompilacja obrazu i testowanie zależne:

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

## <a name="run-variables"></a>Zmienne uruchamiania

Zadania ACR zawierają domyślny zestaw zmiennych, które są dostępne dla kroków zadań podczas ich wykonywania. Dostęp do tych zmiennych można uzyskać przy użyciu formatu `{{.Run.VariableName}}`, gdzie `VariableName` jest jedną z następujących wartości:

* `Run.ID`
* `Run.Registry`
* `Run.Date`
* `Run.Commit`
* `Run.Branch`

### <a name="runid"></a>Run.ID

Każde uruchomienie, przez `az acr run`lub wyzwalacze wykonywania zadań utworzonych za pośrednictwem `az acr task create` ma unikatowy identyfikator. Identyfikator reprezentuje aktualnie wykonywany przebieg.

Zwykle używany do jednoznacznego tagowania obrazu:

```yml
version: v1.0.0
steps:
    - build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} .
```

### <a name="runregistry"></a>Uruchom. Registry

W pełni kwalifikowana nazwa serwera rejestru. Zwykle używany do ogólnego odwoływania się do rejestru, w którym zadanie jest uruchamiane.

```yml
version: v1.0.0
steps:
  - build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} .
```

### <a name="rundate"></a>Run. Date

Bieżący czas UTC rozpoczęcia przebiegu.

### <a name="runcommit"></a>Uruchom. Commit

Identyfikator zatwierdzenia dotyczy zadania wyzwalanego przez zatwierdzenie do repozytorium GitHub.

### <a name="runbranch"></a>Uruchom. rozgałęzienie

W przypadku zadania wyzwalanego przez zatwierdzenie do repozytorium GitHub, nazwa gałęzi.

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z omówieniem wieloetapowych zadań, zobacz [Uruchamianie wieloetapowych zadań kompilacji, testów i poprawek w zadaniach ACR](container-registry-tasks-multi-step.md).

W przypadku kompilacji z jednym etapem zapoznaj się z [omówieniem ACR Tasks](container-registry-tasks-overview.md).

<!-- IMAGES -->

<!-- LINKS - External -->
[acr-tasks]: https://github.com/Azure-Samples/acr-tasks

<!-- LINKS - Internal -->
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-configure]: /cli/azure/reference-index#az-configure

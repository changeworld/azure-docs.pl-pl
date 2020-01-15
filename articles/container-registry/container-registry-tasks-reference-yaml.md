---
title: Odwołanie YAML — ACR zadań
description: Dokumentacja dotycząca definiowania zadań w YAML dla zadań ACR, takich jak właściwości zadania, typy kroków, właściwości kroku i wbudowane zmienne.
ms.topic: article
ms.date: 10/23/2019
ms.openlocfilehash: d86eb0e24233afb536d27f5d0938d4748941e88a
ms.sourcegitcommit: 49e14e0d19a18b75fd83de6c16ccee2594592355
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75945740"
---
# <a name="acr-tasks-reference-yaml"></a>Informacje o zadaniach ACR: YAML

Wieloetapowa Definicja zadania w zadaniach ACR udostępnia skoncentrowane na kontenerach pierwotne obliczenia, ukierunkowane na tworzenie, testowanie i stosowanie poprawek kontenerów. W tym artykule opisano polecenia, parametry, właściwości i składni dla plików YAML, które definiują zadania wieloetapowe.

Ten artykuł zawiera informacje dotyczące tworzenia wieloetapowych plików YAML zadań dla zadań ACR. Jeśli chcesz dowiedzieć się więcej na temat ACR zadań, zobacz [Omówienie zadań ACR](container-registry-tasks-overview.md).

## <a name="acr-taskyaml-file-format"></a>ACR — format pliku Task. YAML

Zadania ACR obsługują Wieloetapową deklarację zadań w standardowej składni YAML. Zdefiniuj kroki zadania w pliku YAML. Następnie można uruchomić zadanie ręcznie, przekazując plik do polecenia [AZ ACR Run][az-acr-run] . Lub użyj pliku, aby utworzyć zadanie za pomocą [AZ ACR Task Create][az-acr-task-create] , które jest wyzwalane automatycznie w ramach zatwierdzenia Git lub aktualizacji obrazu podstawowego. Mimo że ten artykuł odnosi się do `acr-task.yaml` jako pliku zawierającego kroki, zadania ACR obsługują dowolną prawidłową nazwę pliku z [obsługiwanym rozszerzeniem](#supported-task-filename-extensions).

`acr-task.yaml` podstawowe są **właściwościami zadań**, **typami kroków**i **właściwościami kroku**:

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

Format podstawowy pliku `acr-task.yaml`, w tym kilka typowych właściwości kroku, poniżej. Chociaż nie jest to pełna reprezentacja wszystkich dostępnych właściwości kroku lub typu kroku, zawiera krótkie omówienie podstawowego formatu pliku.

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

Zadania ACR mają zarezerwowane kilka rozszerzeń nazw plików, w tym `.yaml`, które będą przetwarzane jako plik zadania. Wszystkie rozszerzenia, których *nie* ma na poniższej liście, są uznawane za przez ACR zadania jako pliku dockerfile:. YAML,. yml,. toml,. JSON,...,. bash,. zsh,. ps1,. PS,. cmd,. bat,. TS,. js,. php,. PR,. RB,. lua

YAML jest jedynym formatem pliku obsługiwanym obecnie przez zadania ACR. Inne rozszerzenia nazw plików są zarezerwowane do możliwej przyszłej pomocy technicznej.

## <a name="run-the-sample-tasks"></a>Uruchamianie przykładowych zadań

W poniższych sekcjach tego artykułu istnieje kilka przykładowych plików zadań, do których odwołuje się. Przykładowe zadania znajdują się w publicznym repozytorium GitHub, [Azure-Samples/ACR-Tasks][acr-tasks]. Można uruchomić je za pomocą polecenia interfejsu wiersza poleceń platformy Azure [AZ ACR Run][az-acr-run]. Przykładowe polecenia są podobne do:

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

Formatowanie przykładowych poleceń założono, że skonfigurowano rejestr domyślny w interfejsie wiersza polecenia platformy Azure, aby pominąć parametr `--registry`. Aby skonfigurować domyślny rejestr, użyj polecenia [AZ Configure][az-configure] z parametrem `--defaults`, który akceptuje wartość `acr=REGISTRY_NAME`.

Na przykład, aby skonfigurować interfejs wiersza polecenia platformy Azure z domyślnym rejestrem o nazwie "Moje rejestr":

```azurecli
az configure --defaults acr=myregistry
```

## <a name="task-properties"></a>Właściwości zadania

Właściwości zadania zwykle pojawiają się u góry pliku `acr-task.yaml` i są właściwościami globalnymi, które są stosowane w całym wykonaniu kroków tego zadania. Niektóre z tych właściwości globalnych można przesłonić w ramach pojedynczego kroku.

| Właściwość | Typ | Opcjonalne | Opis | Przesłonięcie obsługiwane | Wartość domyślna |
| -------- | ---- | -------- | ----------- | ------------------ | ------------- |
| `version` | string | Tak | Wersja pliku `acr-task.yaml`, przeanalizowana przez usługę zadań ACR. Chociaż zadania ACR dążą do zachowania zgodności z poprzednimi wersjami, ta wartość umożliwia ACR zadań w celu zachowania zgodności w ramach zdefiniowanej wersji. Jeśli nie zostanie określony, wartość domyślna to Najnowsza wersja. | Nie | Brak |
| `stepTimeout` | int (sekundy) | Tak | Maksymalna liczba sekund, przez jaką krok może zostać uruchomiony. Jeśli właściwość jest określona w zadaniu, ustawia domyślną właściwość `timeout` wszystkich kroków. Jeśli w kroku zostanie określona właściwość `timeout`, zastępuje ona Właściwość dostarczoną przez zadanie. | Tak | 600 (10 minut) |
| `workingDirectory` | string | Tak | Katalog roboczy kontenera w czasie wykonywania. Jeśli właściwość jest określona w zadaniu, ustawia domyślną właściwość `workingDirectory` wszystkich kroków. Jeśli określono w kroku, zastępuje on Właściwość dostarczoną przez zadanie. | Tak | `/workspace` |
| `env` | [ciąg, String,...] | Tak |  Tablica ciągów w formacie `key=value`, która definiuje zmienne środowiskowe dla zadania. Jeśli właściwość jest określona w zadaniu, ustawia domyślną właściwość `env` wszystkich kroków. Jeśli jest określony w kroku, zastępuje wszystkie zmienne środowiskowe dziedziczone z zadania. | Brak |
| `secrets` | [Secret, Secret,...] | Tak | Tablica obiektów [tajnych](#secret) . | Brak |
| `networks` | [Sieć, Sieć,...] | Tak | Tablica obiektów [sieciowych](#network) . | Brak |

### <a name="secret"></a>wpis tajny

Obiekt tajny ma następujące właściwości.

| Właściwość | Typ | Opcjonalne | Opis | Wartość domyślna |
| -------- | ---- | -------- | ----------- | ------- |
| `id` | string | Nie | Identyfikator wpisu tajnego. | Brak |
| `keyvault` | string | Tak | Azure Key Vault tajny adres URL. | Brak |
| `clientID` | string | Tak | Identyfikator klienta [zarządzanej tożsamości przypisanej przez użytkownika](container-registry-tasks-authentication-managed-identity.md) dla zasobów platformy Azure. | Brak |

### <a name="network"></a>sieć

Obiekt sieciowy ma następujące właściwości.

| Właściwość | Typ | Opcjonalne | Opis | Wartość domyślna |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | string | Nie | Nazwa sieci. | Brak |
| `driver` | string | Tak | Sterownik do zarządzania siecią. | Brak |
| `ipv6` | bool | Tak | Czy jest włączona obsługa sieci IPv6. | `false` |
| `skipCreation` | bool | Tak | Określa, czy pominąć tworzenie sieci. | `false` |
| `isDefault` | bool | Tak | Czy sieć jest siecią domyślną zapewnianą przez Azure Container Registry | `false` |

## <a name="task-step-types"></a>Typy kroków zadań

Zadania ACR obsługują trzy typy kroków. Każdy typ kroku obsługuje kilka właściwości, szczegółowo w sekcji dla każdego typu kroku.

| Typ kroku | Opis |
| --------- | ----------- |
| [`build`](#build) | Kompiluje obraz kontenera przy użyciu znanej składni `docker build`. |
| [`push`](#push) | Wykonuje `docker push` nowo utworzonych lub ponownie oznaczonych obrazów do rejestru kontenerów. Obsługiwane są Azure Container Registry, inne rejestry prywatne i publiczne centrum platformy Docker. |
| [`cmd`](#cmd) | Uruchamia kontener jako polecenie z parametrami przekazaną do `[ENTRYPOINT]`kontenera. Typ kroku `cmd` obsługuje parametry, takie jak `env`, `detach`i inne znane opcje polecenia `docker run`, umożliwiając testowanie jednostkowe i funkcjonalne przy równoczesnym wykonywaniu kontenerów. |

## <a name="build"></a>budować

Tworzenie obrazu kontenera. Typ kroku `build` reprezentuje wiele dzierżawców i bezpieczny sposób uruchamiania `docker build` w chmurze jako pierwszej klasy pierwotnej.

### <a name="syntax-build"></a>Składnia: kompilacja

```yml
version: v1.1.0
steps:
  - [build]: -t [imageName]:[tag] -f [Dockerfile] [context]
    [property]: [value]
```

Typ kroku `build` obsługuje parametry w poniższej tabeli. Typ kroku `build` obsługuje także wszystkie opcje kompilacji polecenia [Docker Build](https://docs.docker.com/engine/reference/commandline/build/) , takie jak `--build-arg`, aby ustawić zmienne czasu kompilacji.

| Parametr | Opis | Opcjonalne |
| --------- | ----------- | :-------: |
| `-t` &#124; `--image` | Definiuje w pełni kwalifikowaną `image:tag` skompilowanego obrazu.<br /><br />Ponieważ obrazy mogą być używane do sprawdzania poprawności zadań wewnętrznych, takich jak testy funkcjonalne, nie wszystkie obrazy wymagają `push` do rejestru. Jednak w celu wystąpienia obrazu w ramach wykonywania zadania obraz musi mieć nazwę do odwołania.<br /><br />W przeciwieństwie do `az acr build`, uruchamianie zadań ACR nie zapewnia domyślnego zachowania wypychania. W przypadku zadań ACR w scenariuszu domyślnym założono możliwość kompilowania, weryfikowania i wypychania obrazu. Zobacz [wypychanie](#push) , aby dowiedzieć się, jak opcjonalnie wypchnąć skompilowane obrazy. | Tak |
| `-f` &#124; `--file` | Określa pliku dockerfile przekazaną do `docker build`. Jeśli nie zostanie określony, przyjmuje się domyślny pliku dockerfile w katalogu głównym kontekstu. Aby określić pliku dockerfile, przekaż nazwę pliku względem katalogu głównego kontekstu. | Tak |
| `context` | Katalog główny przeszedł do `docker build`. Katalog główny każdego zadania jest ustawiony na [WorkingDirectory](#task-step-properties)udostępnione i zawiera katalog główny skojarzonego sklonowanego katalogu Git. | Nie |

### <a name="properties-build"></a>Właściwości: kompilacja

Typ kroku `build` obsługuje następujące właściwości. Szczegółowe informacje o tych właściwościach znajdują się w sekcji [Właściwości kroku zadania](#task-step-properties) w tym artykule.

| | | |
| -------- | ---- | -------- |
| `detach` | bool | Opcjonalne |
| `disableWorkingDirectoryOverride` | bool | Opcjonalne |
| `entryPoint` | string | Opcjonalne |
| `env` | [ciąg, String,...] | Opcjonalne |
| `expose` | [ciąg, String,...] | Opcjonalne |
| `id` | string | Opcjonalne |
| `ignoreErrors` | bool | Opcjonalne |
| `isolation` | string | Opcjonalne |
| `keep` | bool | Opcjonalne |
| `network` | obiekt | Opcjonalne |
| `ports` | [ciąg, String,...] | Opcjonalne |
| `pull` | bool | Opcjonalne |
| `repeat` | int | Opcjonalne |
| `retries` | int | Opcjonalne |
| `retryDelay` | int (sekundy) | Opcjonalne |
| `secret` | obiekt | Opcjonalne |
| `startDelay` | int (sekundy) | Opcjonalne |
| `timeout` | int (sekundy) | Opcjonalne |
| `when` | [ciąg, String,...] | Opcjonalne |
| `workingDirectory` | string | Opcjonalne |

### <a name="examples-build"></a>Przykłady: kompilacja

#### <a name="build-image---context-in-root"></a>Kompilacja kontekstu obrazu w elemencie głównym

```azurecli
az acr run -f build-hello-world.yaml https://github.com/AzureCR/acr-tasks-sample.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-hello-world.yaml)]

#### <a name="build-image---context-in-subdirectory"></a>Kompilacja kontekstu obrazu w podkatalogu

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world -f hello-world.dockerfile ./subDirectory
```

## <a name="push"></a>wypchnij

Wypychanie co najmniej jednego skompilowanego lub ponownie oznaczonego obrazu do rejestru kontenerów. Obsługuje wypychanie do rejestrów prywatnych, takich jak Azure Container Registry, lub do publicznego centrum Docker.

### <a name="syntax-push"></a>Składnia: push

Typ kroku `push` obsługuje kolekcję obrazów. Składnia kolekcji YAML obsługuje formaty wbudowane i zagnieżdżone. Wypychanie pojedynczego obrazu jest zwykle reprezentowane przy użyciu składni wbudowanej:

```yml
version: v1.1.0
steps:
  # Inline YAML collection syntax
  - push: ["$Registry/hello-world:$ID"]
```

Aby zwiększyć czytelność, Użyj zagnieżdżonej składni podczas wypychania wielu obrazów:

```yml
version: v1.1.0
steps:
  # Nested YAML collection syntax
  - push:
    - $Registry/hello-world:$ID
    - $Registry/hello-world:latest
```

### <a name="properties-push"></a>Właściwości: wypychanie

Typ kroku `push` obsługuje następujące właściwości. Szczegółowe informacje o tych właściwościach znajdują się w sekcji [Właściwości kroku zadania](#task-step-properties) w tym artykule.

| | | |
| -------- | ---- | -------- |
| `env` | [ciąg, String,...] | Opcjonalne |
| `id` | string | Opcjonalne |
| `ignoreErrors` | bool | Opcjonalne |
| `startDelay` | int (sekundy) | Opcjonalne |
| `timeout` | int (sekundy) | Opcjonalne |
| `when` | [ciąg, String,...] | Opcjonalne |

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

Typ kroku `cmd` uruchamia kontener.

### <a name="syntax-cmd"></a>Składnia: cmd

```yml
version: v1.1.0
steps:
  - [cmd]: [containerImage]:[tag (optional)] [cmdParameters to the image]
```

### <a name="properties-cmd"></a>Właściwości: cmd

Typ kroku `cmd` obsługuje następujące właściwości:

| | | |
| -------- | ---- | -------- |
| `detach` | bool | Opcjonalne |
| `disableWorkingDirectoryOverride` | bool | Opcjonalne |
| `entryPoint` | string | Opcjonalne |
| `env` | [ciąg, String,...] | Opcjonalne |
| `expose` | [ciąg, String,...] | Opcjonalne |
| `id` | string | Opcjonalne |
| `ignoreErrors` | bool | Opcjonalne |
| `isolation` | string | Opcjonalne |
| `keep` | bool | Opcjonalne |
| `network` | obiekt | Opcjonalne |
| `ports` | [ciąg, String,...] | Opcjonalne |
| `pull` | bool | Opcjonalne |
| `repeat` | int | Opcjonalne |
| `retries` | int | Opcjonalne |
| `retryDelay` | int (sekundy) | Opcjonalne |
| `secret` | obiekt | Opcjonalne |
| `startDelay` | int (sekundy) | Opcjonalne |
| `timeout` | int (sekundy) | Opcjonalne |
| `when` | [ciąg, String,...] | Opcjonalne |
| `workingDirectory` | string | Opcjonalne |

Szczegółowe informacje o tych właściwościach można znaleźć w sekcji [Właściwości kroku zadania](#task-step-properties) w tym artykule.

### <a name="examples-cmd"></a>Przykłady: cmd

#### <a name="run-hello-world-image"></a>Uruchom obraz Hello-World

To polecenie wykonuje plik zadania `hello-world.yaml`, który odwołuje się do obrazu [Hello-World](https://hub.docker.com/_/hello-world/) w usłudze Docker Hub.

```azurecli
az acr run -f hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.yaml -->
[!code-yml[task](~/acr-tasks/hello-world.yaml)]

#### <a name="run-bash-image-and-echo-hello-world"></a>Uruchamianie obrazu bash i echo "Hello World"

To polecenie wykonuje plik zadania `bash-echo.yaml`, który odwołuje się do obrazu [bash](https://hub.docker.com/_/bash/) w usłudze Docker Hub.

```azurecli
az acr run -f bash-echo.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo.yaml)]

#### <a name="run-specific-bash-image-tag"></a>Uruchom określony tag obrazu bash

Aby uruchomić określoną wersję obrazu, określ znacznik w `cmd`.

To polecenie wykonuje plik zadania `bash-echo-3.yaml`, który odwołuje się do obrazu [bash: 3.0](https://hub.docker.com/_/bash/) w usłudze Docker Hub.

```azurecli
az acr run -f bash-echo-3.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo-3.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo-3.yaml)]

#### <a name="run-custom-images"></a>Uruchamianie obrazów niestandardowych

Typ kroku `cmd` odwołuje się do obrazów przy użyciu standardowego formatu `docker run`. Obrazy, które nie są poprzedzone rejestrem, zakłada się, że pochodzą z docker.io. Poprzedni przykład może być równy reprezentowany jako:

```yml
version: v1.1.0
steps:
  - cmd: docker.io/bash:3.0 echo hello world
```

Przy użyciu standardowej konwencji odwołującej się do `docker run` obrazu `cmd` można uruchamiać obrazy z dowolnego rejestru prywatnego lub publicznego centrum Docker. Jeśli odwołujesz się do obrazów w tym samym rejestrze, w którym jest wykonywane zadanie ACR, nie musisz określać żadnych poświadczeń rejestru.

* Uruchom obraz pochodzący z usługi Azure Container Registry. W poniższym przykładzie przyjęto założenie, że istnieje rejestr o nazwie `myregistry`i niestandardowy `myimage:mytag`obraz.

    ```yml
    version: v1.1.0
    steps:
        - cmd: myregistry.azurecr.io/myimage:mytag
    ```

* Uogólnij odwołanie do rejestru za pomocą zmiennej lub aliasu Run

    Zamiast nakodować twarde nazwy rejestru w pliku `acr-task.yaml`, można zwiększyć jego przenośność, używając zmiennej lub [aliasu](#aliases) [Run](#run-variables) . Zmienna `Run.Registry` lub alias `$Registry` rozszerza się w czasie wykonywania do nazwy rejestru, w którym wykonywane jest zadanie.

    Na przykład, aby uogólnić poprzednie zadanie, aby było ono działać w dowolnym rejestrze kontenera platformy Azure, odwołując się do zmiennej $Registry w nazwie obrazu:

    ```yml
    version: v1.1.0
    steps:
      - cmd: $Registry/myimage:mytag
    ```

## <a name="task-step-properties"></a>Właściwości kroku zadania

Każdy typ kroku obsługuje kilka właściwości, które są odpowiednie dla tego typu. W poniższej tabeli zdefiniowano wszystkie dostępne właściwości kroku. Nie wszystkie typy kroków obsługują wszystkie właściwości. Aby zobaczyć, które z tych właściwości są dostępne dla każdego typu kroku, zapoznaj się z sekcjami odwołania dla kroków [cmd](#cmd), [Build](#build)i [push](#push) .

| Właściwość | Typ | Opcjonalne | Opis | Wartość domyślna |
| -------- | ---- | -------- | ----------- | ------- |
| `detach` | bool | Tak | Określa, czy kontener ma zostać odłączony podczas uruchamiania. | `false` |
| `disableWorkingDirectoryOverride` | bool | Tak | Określa, czy należy wyłączyć funkcję przesłonięcia `workingDirectory`. Użyj tej usługi w połączeniu z `workingDirectory`, aby mieć pełną kontrolę nad katalogiem roboczym kontenera. | `false` |
| `entryPoint` | string | Tak | Zastępuje `[ENTRYPOINT]` kontenera kroku. | Brak |
| `env` | [ciąg, String,...] | Tak | Tablica ciągów w formacie `key=value`, która definiuje zmienne środowiskowe dla kroku. | Brak |
| `expose` | [ciąg, String,...] | Tak | Tablica portów, które są udostępniane z kontenera. |  Brak |
| [`id`](#example-id) | string | Tak | Unikatowy identyfikator kroku w zadaniu. Inne kroki w zadaniu mogą odwoływać się do `id`kroku, na przykład w celu sprawdzenia zależności z `when`.<br /><br />`id` jest również nazwą uruchomionego kontenera. Procesy działające w innych kontenerach w zadaniu mogą odwoływać się do `id` jako nazwy hosta DNS lub do uzyskiwania do niego dostępu przy użyciu dzienników platformy Docker [ID]. | `acb_step_%d`, gdzie `%d` jest indeksem (0) kroku w górnej części pliku YAML |
| `ignoreErrors` | bool | Tak | Określa, czy krok ma być oznaczany pomyślnie, niezależnie od tego, czy wystąpił błąd podczas wykonywania kontenera. | `false` |
| `isolation` | string | Tak | Poziom izolacji kontenera. | `default` |
| `keep` | bool | Tak | Czy kontener kroku powinien być przechowywany po wykonaniu. | `false` |
| `network` | obiekt | Tak | Identyfikuje sieć, w której działa kontener. | Brak |
| `ports` | [ciąg, String,...] | Tak | Tablica portów publikowanych z kontenera do hosta. |  Brak |
| `pull` | bool | Tak | Określa, czy należy wymusić ściąganie kontenera przed jego wykonaniem, aby zapobiec jakimkolwiek zachowaniem buforowania. | `false` |
| `privileged` | bool | Tak | Określa, czy ma być uruchamiany kontener w trybie uprzywilejowanym. | `false` |
| `repeat` | int | Tak | Liczba ponownych prób powtarzania wykonywania kontenera. | 0 |
| `retries` | int | Tak | Liczba ponownych prób, które należy podjąć, jeśli kontener nie zostanie wykonany. Próba ponowienia następuje tylko wtedy, gdy kod zakończenia kontenera jest różny od zera. | 0 |
| `retryDelay` | int (sekundy) | Tak | Opóźnienie w sekundach między ponownymi próbami wykonania kontenera. | 0 |
| `secret` | obiekt | Tak | Identyfikuje wpis tajny Azure Key Vault lub [zarządzaną tożsamość dla zasobów platformy Azure](container-registry-tasks-authentication-managed-identity.md). | Brak |
| `startDelay` | int (sekundy) | Tak | Liczba sekund, przez którą ma zostać opóźnione wykonywanie kontenera. | 0 |
| `timeout` | int (sekundy) | Tak | Maksymalna liczba sekund, przez jaką krok może zostać wykonany przed zakończeniem. | 600 |
| [`when`](#example-when) | [ciąg, String,...] | Tak | Konfiguruje zależność kroku od jednego lub kilku innych kroków w ramach zadania. | Brak |
| `user` | string | Tak | Nazwa użytkownika lub identyfikator UID kontenera | Brak |
| `workingDirectory` | string | Tak | Ustawia katalog roboczy dla kroku. Domyślnie zadania ACR tworzą katalog główny jako katalog roboczy. Jeśli jednak kompilacja zawiera kilka kroków, wcześniejsze kroki mogą współużytkować artefakty z późniejszymi krokami, określając ten sam katalog roboczy. | `/workspace` |

### <a name="examples-task-step-properties"></a>Przykłady: właściwości kroku zadania

#### <a name="example-id"></a>Przykład: Identyfikator

Kompiluj dwa obrazy, tworząc w ten sposób obraz testu funkcjonalnego. Każdy krok jest identyfikowany przez unikalną `id`, w której są wykonywane inne czynności w odniesieniu do zadania we właściwości `when`.

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

#### <a name="example-when"></a>Przykład: Kiedy

Właściwość `when` określa zależność kroku od innych kroków w ramach zadania. Obsługuje dwie wartości parametrów:

* `when: ["-"]` — wskazuje, że nie ma zależności od innych kroków. Krok określający, `when: ["-"]` rozpocznie wykonywanie natychmiast i włącza współbieżne wykonywanie kroków.
* `when: ["id1", "id2"]`-wskazuje, że krok zależy od kroków z `id` "ID1" i `id` "ID2". Ten krok nie zostanie wykonany do czasu zakończenia obu kroków "ID1" i "ID2".

Jeśli `when` nie określono w kroku, ten krok zależy od ukończenia poprzedniego kroku w pliku `acr-task.yaml`.

Wykonywanie krok po kroku bez `when`:

```azurecli
az acr run -f when-sequential-default.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-default.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-default.yaml)]

Wykonywanie sekwencyjne z `when`:

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

Zadania ACR zawierają domyślny zestaw zmiennych, które są dostępne dla kroków zadań podczas ich wykonywania. Dostęp do tych zmiennych można uzyskać przy użyciu formatu `{{.Run.VariableName}}`, gdzie `VariableName` jest jednym z następujących:

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

Nazwy zmiennych nie są generalnie objaśniane. Szczegóły dotyczące często używanych zmiennych. Począwszy od wersji YAML `v1.1.0`, można użyć skróconego, wstępnie zdefiniowanego [aliasu zadania](#aliases) zamiast większości zmiennych uruchomieniowych. Na przykład zamiast `{{.Run.Registry}}`Użyj aliasu `$Registry`.

### <a name="runid"></a>Run.ID

Każdy przebieg, za pośrednictwem `az acr run`lub wyzwalacze wykonywanie zadań utworzonych za pośrednictwem `az acr task create`, ma unikatowy identyfikator. Identyfikator reprezentuje aktualnie wykonywany przebieg.

Zwykle używany do jednoznacznego tagowania obrazu:

```yml
version: v1.1.0
steps:
    - build: -t $Registry/hello-world:$ID .
```

### <a name="runregistry"></a>Uruchom. Registry

W pełni kwalifikowana nazwa serwera rejestru. Zwykle używany do ogólnego odwoływania się do rejestru, w którym zadanie jest uruchamiane.

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID .
```

### <a name="runregistryname"></a>Run. Registryname

Nazwa rejestru kontenerów. Zwykle używane w krokach zadania, które nie wymagają w pełni kwalifikowanej nazwy serwera, na przykład `cmd` kroków, które uruchamiają polecenia interfejsu CLI platformy Azure w rejestrach.

```yml
version 1.1.0
steps:
# List repositories in registry
- cmd: az login --identity
- cmd: az acr repository list --name $RegistryName
```

### <a name="rundate"></a>Run. Date

Bieżący czas UTC rozpoczęcia przebiegu.

### <a name="runcommit"></a>Uruchom. Commit

Identyfikator zatwierdzenia dotyczy zadania wyzwalanego przez zatwierdzenie do repozytorium GitHub.

### <a name="runbranch"></a>Uruchom. rozgałęzienie

W przypadku zadania wyzwalanego przez zatwierdzenie do repozytorium GitHub, nazwa gałęzi.

## <a name="aliases"></a>Aliasy

W przypadku `v1.1.0`zadania ACR obsługują aliasy, które są dostępne dla kroków zadań podczas ich wykonywania. Aliasy są podobne w koncepcji względem aliasów (skróty poleceń) obsługiwane w bash i innych powłokach poleceń. 

Za pomocą aliasu można uruchomić dowolne polecenie lub grupę poleceń (w tym opcje i nazwy plików), wprowadzając pojedyncze słowo.

Zadania ACR obsługują kilka wstępnie zdefiniowanych aliasów, a także tworzonych aliasów niestandardowych.

### <a name="predefined-aliases"></a>Wstępnie zdefiniowane aliasy

Następujące aliasy zadań są dostępne do użycia zamiast [zmiennych Run](#run-variables):

| Alias | Zmienna uruchamiania |
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

W obszarze kroki zadania poprzedź alias z dyrektywą `$`, jak w poniższym przykładzie:

```yaml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID -f hello-world.dockerfile .
```

### <a name="image-aliases"></a>Aliasy obrazów

Każdy z następujących aliasów wskazuje stabilny obraz w programie Microsoft Container Registry (MCR). Można odwołać się do każdego z nich w sekcji `cmd` pliku zadania bez używania dyrektywy.

| Alias | Obraz |
| ----- | ----- |
| `acr` | `mcr.microsoft.com/acr/acr-cli:0.1` |
| `az` | `mcr.microsoft.com/acr/azure-cli:a80af84` |
| `bash` | `mcr.microsoft.com/acr/bash:a80af84` |
| `curl` | `mcr.microsoft.com/acr/curl:a80af84` |

Poniższe przykładowe zadanie używa kilku aliasów do [przeczyszczania](container-registry-auto-purge.md) tagów obrazu starszych niż 7 dni w repozytorium `samples/hello-world` w rejestrze uruchamiania:

```yaml
version: v1.1.0
steps:
  - cmd: acr tag list --registry $RegistryName --repository samples/hello-world
  - cmd: acr purge --registry $RegistryName --filter samples/hello-world:.* --ago 7d
```

### <a name="custom-alias"></a>Alias niestandardowy

Zdefiniuj niestandardowy alias w pliku YAML i użyj go, jak pokazano w poniższym przykładzie. Alias może zawierać tylko znaki alfanumeryczne. Domyślną dyrektywą do rozwinięcia aliasu jest znak `$`.

```yml
version: v1.1.0
alias:
  values:
    repo: myrepo
steps:
  - build: -t $Registry/$repo/hello-world:$ID -f Dockerfile .
```

Można utworzyć link do pliku YAML zdalnego lub lokalnego w celu uzyskania niestandardowych definicji aliasów. Poniższy przykład łączy do pliku YAML w usłudze Azure Blob Storage:

```yml
version: v1.1.0
alias:
  src:  # link to local or remote custom alias files
    - 'https://link/to/blob/remoteAliases.yml?readSasToken'
[...]
```

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

---
title: Dokumentacja usługi Azure Container Registry zadań - YAML
description: Odwołanie do definiowania zadań w YAML dla zadania usługi ACR, w tym właściwości zadania, krok typy, właściwości kroku i wbudowane zmienne.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 03/28/2019
ms.author: danlep
ms.openlocfilehash: b2398e7db7ed91dee8d85c0c50058bb15b9f4c7e
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58894136"
---
# <a name="acr-tasks-reference-yaml"></a>Odwołanie do zadania usługi ACR: YAML

Definicji zadania wieloetapowe w zadaniach rejestru Azure container Registry zapewnia podstawowy skoncentrowane na kontenerze obliczeń koncentruje się na tworzenie, testowanie i instalowanie poprawek kontenerów. W tym artykule opisano polecenia, parametry, właściwości i składnia dla plików kodu YAML, które definiują wieloetapowych zadań podrzędnych.

Ten artykuł zawiera odwołanie do tworzenia plików YAML wieloetapowego zadania dla zadań usługi ACR. Jeśli chcesz zapoznać się z wprowadzeniem do usługi ACR zadań, zobacz [Omówienie zadań ACR](container-registry-tasks-overview.md).

## <a name="acr-taskyaml-file-format"></a>format pliku task.yaml rejestru Azure container Registry

Zadania rejestru Azure container Registry obsługuje deklaracji wieloetapowego zadania w standardowej składni kodu YAML. Kroki zadań definiuje się w pliku YAML. Następnie można uruchomić zadanie ręcznie, przekazując plik do [az acr Uruchom] [ az-acr-run] polecenia. Lub użyj pliku, aby utworzyć zadanie przy użyciu [az acr zadanie Tworzenie] [ az-acr-task-create] automatycznie wyzwalane po aktualizacji obraz Git, jak zatwierdzenia lub podstawowa. Chociaż ten artykuł dotyczy `acr-task.yaml` co plik zawierający kroki, zadania rejestru Azure container Registry obsługuje wszystkie prawidłową nazwę pliku z [obsługiwane rozszerzenia](#supported-task-filename-extensions).

Najwyższego poziomu `acr-task.yaml` są w nim elementów podstawowych **we właściwościach zadania**, **krok typy**, i **krok właściwości**:

* [We właściwościach zadania](#task-properties) dotyczą wszystkie kroki wykonywane w ramach wykonywania zadania. Istnieje kilka właściwości globalnego zadania, w tym:
  * `version`
  * `stepTimeout`
  * `workingDirectory`
* [Zadanie typy kroku](#task-step-types) reprezentują typy akcji, które mogą być wykonywane w zadaniu. Istnieją trzy typy krok:
  * `build`
  * `push`
  * `cmd`
* [We właściwościach krok zadania](#task-step-properties) parametrów, które są stosowane do pojedynczego kroku. Istnieje kilka właściwości kroku, w tym:
  * `startDelay`
  * `timeout`
  * `when`
  * .. .i wiele innych.

Podstawowy format `acr-task.yaml` następuje po pliku, w tym niektóre typowe właściwości kroku. Podczas nie wyczerpujący reprezentacja wszystkich właściwości dostępne kroku lub krok typu użycia zapewnia szybki przegląd format pliku podstawowego.

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

### <a name="supported-task-filename-extensions"></a>Zadania obsługiwane rozszerzenia nazw plików

Zadania usługi ACR zarezerwował kilka rozszerzenia nazwy pliku, w tym `.yaml`, który będzie przetwarzał jako plik zadania. Każde rozszerzenie *nie* na poniższej liście jest uważany za przez zadania usługi ACR pliku Dockerfile: .yaml yml, .toml, .json, SH, .bash, .zsh, ps1, PS, .cmd, .bat, TS, js, PHP, PY, RB, .lua

YAML jest tylko format pliku są obecnie obsługiwane przez zadania usługi ACR. Inne rozszerzenia nazwy pliku są zarezerwowane dla możliwego wsparcia w przyszłości.

## <a name="run-the-sample-tasks"></a>Uruchom przykładowe zadania podrzędne

Istnieje kilka przykładowych plików zadań w następujących sekcjach tego artykułu. Przykładowe zadania podrzędne znajdują się w publicznego repozytorium GitHub [platformy Azure — przykłady/acr zadań][acr-tasks]. Można je też uruchamiać przy użyciu polecenia interfejsu wiersza polecenia Azure [az acr Uruchom][az-acr-run]. Przykładowe polecenia są podobne do:

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

Formatowanie w przykładowych poleceniach przyjęto założenie, skonfigurowaniu rejestru domyślne w interfejsie wiersza polecenia platformy Azure, dzięki czemu mogą pominąć `--registry` parametru. Aby skonfigurować domyślny registry, użyj [skonfigurować az] [ az-configure] polecenia `--defaults` parametr, który akceptuje `acr=REGISTRY_NAME` wartość.

Na przykład aby skonfigurować wiersza polecenia platformy Azure za pomocą rejestru domyślnego o nazwie "myregistry":

```azurecli
az configure --defaults acr=myregistry
```

## <a name="task-properties"></a>Właściwości zadania

Właściwości zadania zazwyczaj znajdują się w górnej części `acr-task.yaml` pliku i są globalne właściwości, które są stosowane w całym pełną wykonywaniem kroków zadania. Niektóre z tych właściwości globalne można przesłonić, w ramach pojedynczego kroku.

| Właściwość | Type | Optional (Opcjonalność) | Opis | Zastąpienie obsługiwane | Wartość domyślna |
| -------- | ---- | -------- | ----------- | ------------------ | ------------- |
| `version` | string | Yes | Wersja `acr-task.yaml` plików analizowane przez usługę zadań usługi ACR. Gdy zadania ACR dokłada wszelkich starań zachować zgodność z poprzednimi wersjami, ta wartość umożliwia zadania ACR utrzymać zgodność, w ramach określonych wersji. Jeśli nie zostanie podany, wartość domyślna do najnowszej wersji. | Nie | Brak |
| `stepTimeout` | int (w sekundach) | Yes | Maksymalna liczba sekund, przez krok można uruchomić. Jeśli właściwość jest określony dla zadania, ustawia domyślną `timeout` właściwości wszystkich kroków. Jeśli `timeout` właściwość jest określona w kroku, zastępuje ona właściwości dostarczanych przez zadanie. | Yes | 600 (10 minut) |
| `workingDirectory` | string | Yes | Katalog roboczy kontenera w czasie wykonywania. Jeśli właściwość jest określony dla zadania, ustawia domyślną `workingDirectory` właściwości wszystkich kroków. Jeśli określona w kroku, zastępuje ona właściwości dostarczanych przez zadanie. | Yes | `$HOME` |
| `env` | [-ciąg,...] | Yes |  Tablica ciągów w `key=value` formatu, który definiowania zmiennych środowiskowych dla zadania. Jeśli właściwość jest określony dla zadania, ustawia domyślną `env` właściwości wszystkich kroków. Jeśli określona w kroku, zastępuje ona wszelkie zmienne środowiskowe dziedziczone od zadania. | Brak |
| `secrets` | [wpisu tajnego, klucza tajnego,...] | Yes | Tablica [klucz tajny](#secret) obiektów. | Brak |
| `networks` | [sieci, sieci,...] | Yes | Tablica [sieci](#network) obiektów. | Brak |

### <a name="secret"></a>wpis tajny

Obiekt wpisu tajnego ma następujące właściwości.

| Właściwość | Type | Optional (Opcjonalność) | Opis | Wartość domyślna |
| -------- | ---- | -------- | ----------- | ------- |
| `id` | string | Nie | Identyfikator wpisu tajnego. | Brak |
| `akv` | string | Yes | Adres URL wpisu tajnego usługi Azure Key Vault (AKV). | Brak |
| `clientID` | string | Yes | Identyfikator klienta przypisany użytkownik tożsamości zarządzanej dla zasobów platformy Azure. | Brak |

### <a name="network"></a>sieć

Obiekt network ma następujące właściwości.

| Właściwość | Type | Optional (Opcjonalność) | Opis | Wartość domyślna |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | string | Nie | Nazwa sieci. | Brak |
| `driver` | string | Yes | Sterownik do zarządzania siecią. | Brak |
| `ipv6` | bool | Yes | Czy jest włączony protokół IPv6 sieci. | `false` |
| `skipCreation` | bool | Yes | Określa, czy pominąć tworzenie sieci. | `false` |
| `isDefault` | bool | Yes | Czy sieć jest dostarczane z usługi Azure Container Registry domyślnej sieci | `false` |

## <a name="task-step-types"></a>Typy kroku zadań

Zadania rejestru Azure container Registry obsługuje trzy typy kroku. Każdy typ kroku obsługuje kilka właściwości, szczegółowo opisane w sekcji dla każdego typu kroku.

| Typ kroku | Opis |
| --------- | ----------- |
| [`build`](#build) | Tworzy obraz kontenera przy użyciu dobrze znanej `docker build` składni. |
| [`push`](#push) | Wykonuje `docker push` nowo utworzone lub zmieniony obrazów do rejestru kontenerów. Usługa Azure Container Registry, innych rejestrów prywatnych i publicznych usługi Docker Hub są obsługiwane. |
| [`cmd`](#cmd) | Uruchamia kontener jako polecenia, za pomocą parametrów jest przekazywane do kontenera `[ENTRYPOINT]`. `cmd` Typ kroku obsługuje parametry, takie jak `env`, `detach`i inne znany `docker run` opcji i włączenie jednostki i testy funkcjonalne dzięki kontenera współbieżne wykonywanie poleceń. |

## <a name="build"></a>Kompilacja

Tworzenie obrazu kontenera. `build` Typ kroku reprezentuje wielodostępne, bezpieczna oznacza uruchomienia `docker build` w chmurze jako podstawowy najwyższej jakości.

### <a name="syntax-build"></a>Składnia: kompilacji

```yml
version: v1.0.0
steps:
  - [build]: -t [imageName]:[tag] -f [Dockerfile] [context]
    [property]: [value]
```

`build` Typ kroku obsługuje parametry w tabeli poniżej. `build` Typ kroku obsługuje również wszystkich opcji kompilacji [kompilacji platformy docker](https://docs.docker.com/engine/reference/commandline/build/) polecenia, takie jak `--build-arg` do ustawiania zmiennych w czasie kompilacji.

| Parametr | Opis | Optional (Opcjonalność) |
| --------- | ----------- | :-------: |
| `-t` &#124; `--image` | Określa w pełni kwalifikowaną `image:tag` wbudowanego obrazu.<br /><br />Ponieważ obrazy mogą być używane do walidacji wewnętrzny zadań, takich jak testy funkcjonalne, nie wszystkie obrazy wymagają `push` do rejestru. Do wystąpienia obraz w obrębie wykonywania zadań, obraz, który muszą mieć jednak możliwość nazwę, aby odwoływać się.<br /><br />W odróżnieniu od `az acr build`, uruchamianie zadań rekordu ACR nie zapewnia domyślne zachowanie wypychania. Za pomocą zadań ACR scenariusza domyślne przyjęto założenie, umożliwia tworzenie, weryfikowanie, a następnie wypchnąć obraz. Zobacz [wypychania](#push) dotyczące opcjonalnie wypychania wbudowanych obrazów. | Yes |
| `-f` &#124; `--file` | Określa plik Dockerfile przekazany do `docker build`. Jeśli nie zostanie określony, domyślnie, przyjmowana jest plik Dockerfile w katalogu głównym kontekstu. Aby określić plik Dockerfile, należy przekazać nazwę pliku względem katalogu głównego kontekstu. | Yes |
| `context` | Katalog główny jest przekazywany do `docker build`. Katalog główny każdego zadania jest ustawiony do udostępnionej [workingDirectory](#task-step-properties)i zawiera katalog główny skojarzony sklonowanym katalogu Git. | Nie |

### <a name="properties-build"></a>Właściwości: kompilacji

`build` Typ kroku obsługuje następujące właściwości. Znajdź szczegółowe informacje o tych właściwości w [właściwości krok zadania](#task-step-properties) dalszej części tego artykułu.

| | | |
| -------- | ---- | -------- |
| `detach` | bool | Optional (Opcjonalność) |
| `disableWorkingDirectoryOverride` | bool | Optional (Opcjonalność) |
| `entryPoint` | string | Optional (Opcjonalność) |
| `env` | [-ciąg,...] | Optional (Opcjonalność) |
| `expose` | [-ciąg,...] | Optional (Opcjonalność) |
| `id` | string | Optional (Opcjonalność) |
| `ignoreErrors` | bool | Optional (Opcjonalność) |
| `isolation` | string | Optional (Opcjonalność) |
| `keep` | bool | Optional (Opcjonalność) |
| `network` | obiekt | Optional (Opcjonalność) |
| `ports` | [-ciąg,...] | Optional (Opcjonalność) |
| `pull` | bool | Optional (Opcjonalność) |
| `repeat` | int | Optional (Opcjonalność) |
| `retries` | int | Optional (Opcjonalność) |
| `retryDelay` | int (w sekundach) | Optional (Opcjonalność) |
| `secret` | obiekt | Optional (Opcjonalność) |
| `startDelay` | int (w sekundach) | Optional (Opcjonalność) |
| `timeout` | int (w sekundach) | Optional (Opcjonalność) |
| `when` | [-ciąg,...] | Optional (Opcjonalność) |
| `workingDirectory` | string | Optional (Opcjonalność) |

### <a name="examples-build"></a>Przykłady: kompilacji

#### <a name="build-image---context-in-root"></a>Tworzenie obrazu — kontekst, w katalogu głównym

```azurecli
az acr run -f build-hello-world.yaml https://github.com/AzureCR/acr-tasks-sample.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-hello-world.yaml)]

#### <a name="build-image---context-in-subdirectory"></a>Tworzenie obrazu — kontekst w podkatalogu

```yml
version: v1.0.0
steps:
  - build: -t {{.Run.Registry}}/hello-world -f hello-world.dockerfile ./subDirectory
```

## <a name="push"></a>wypychania

Wypychanie jeden lub więcej skompilowany lub zmieniony obrazów do rejestru kontenerów. Obsługuje Wypychanie do prywatnych rejestrów, takich jak usługa Azure Container Registry lub publicznej usługi Docker Hub.

### <a name="syntax-push"></a>Składnia: wypychania

`push` Typ kroku obsługuje zestaw obrazów. Składnia kolekcji YAML obsługuje wbudowane i formatów zagnieżdżonych. Wypychanie obrazu jednego jest zazwyczaj reprezentowany za pomocą składni wbudowanego:

```yml
version: v1.0.0
steps:
  # Inline YAML collection syntax
  - push: ["{{.Run.Registry}}/hello-world:{{.Run.ID}}"]
```

Dla zwiększenia czytelności należy użyć składni zagnieżdżonych podczas wypychania wiele obrazów:

```yml
version: v1.0.0
steps:
  # Nested YAML collection syntax
  - push:
    - {{.Run.Registry}}/hello-world:{{.Run.ID}}
    - {{.Run.Registry}}/hello-world:latest
```

### <a name="properties-push"></a>Właściwości: wypychania

`push` Typ kroku obsługuje następujące właściwości. Znajdź szczegółowe informacje o tych właściwości w [właściwości krok zadania](#task-step-properties) dalszej części tego artykułu.

| | | |
| -------- | ---- | -------- |
| `env` | [-ciąg,...] | Optional (Opcjonalność) |
| `id` | string | Optional (Opcjonalność) |
| `ignoreErrors` | bool | Optional (Opcjonalność) |
| `startDelay` | int (w sekundach) | Optional (Opcjonalność) |
| `timeout` | int (w sekundach) | Optional (Opcjonalność) |
| `when` | [-ciąg,...] | Optional (Opcjonalność) |

### <a name="examples-push"></a>Przykłady: wypychania

#### <a name="push-multiple-images"></a>Wypychanie wielu obrazów

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-push-hello-world.yaml)]

#### <a name="build-push-and-run"></a>Kompilowanie, wypychanie i uruchom

```azurecli
az acr run -f build-run-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-run-hello-world.yaml)]

## <a name="cmd"></a>cmd

`cmd` Typ kroku uruchamiała kontener.

### <a name="syntax-cmd"></a>Składnia: cmd

```yml
version: v1.0.0
steps:
  - [cmd]: [containerImage]:[tag (optional)] [cmdParameters to the image]
```

### <a name="properties-cmd"></a>Właściwości: cmd

`cmd` Typ kroku obsługuje następujące właściwości:

| | | |
| -------- | ---- | -------- |
| `detach` | bool | Optional (Opcjonalność) |
| `disableWorkingDirectoryOverride` | bool | Optional (Opcjonalność) |
| `entryPoint` | string | Optional (Opcjonalność) |
| `env` | [-ciąg,...] | Optional (Opcjonalność) |
| `expose` | [-ciąg,...] | Optional (Opcjonalność) |
| `id` | string | Optional (Opcjonalność) |
| `ignoreErrors` | bool | Optional (Opcjonalność) |
| `isolation` | string | Optional (Opcjonalność) |
| `keep` | bool | Optional (Opcjonalność) |
| `network` | obiekt | Optional (Opcjonalność) |
| `ports` | [-ciąg,...] | Optional (Opcjonalność) |
| `pull` | bool | Optional (Opcjonalność) |
| `repeat` | int | Optional (Opcjonalność) |
| `retries` | int | Optional (Opcjonalność) |
| `retryDelay` | int (w sekundach) | Optional (Opcjonalność) |
| `secret` | obiekt | Optional (Opcjonalność) |
| `startDelay` | int (w sekundach) | Optional (Opcjonalność) |
| `timeout` | int (w sekundach) | Optional (Opcjonalność) |
| `when` | [-ciąg,...] | Optional (Opcjonalność) |
| `workingDirectory` | string | Optional (Opcjonalność) |

Można znaleźć szczegółowe informacje o tych właściwości w [właściwości krok zadania](#task-step-properties) dalszej części tego artykułu.

### <a name="examples-cmd"></a>Przykłady: cmd

#### <a name="run-hello-world-image"></a>Uruchom obraz Witaj, świecie

To polecenie uruchamia `hello-world.yaml` pliku zadań, który odwołuje się [Witaj, świecie](https://hub.docker.com/_/hello-world/) obrazu w usłudze Docker Hub.

```azurecli
az acr run -f hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.yaml -->
[!code-yml[task](~/acr-tasks/hello-world.yaml)]

#### <a name="run-bash-image-and-echo-hello-world"></a>Uruchom powłokę bash obrazu i echa "hello world"

To polecenie uruchamia `bash-echo.yaml` pliku zadań, który odwołuje się [bash](https://hub.docker.com/_/bash/) obrazu w usłudze Docker Hub.

```azurecli
az acr run -f bash-echo.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo.yaml)]

#### <a name="run-specific-bash-image-tag"></a>Uruchamianie określonych bash tagu obrazu

Aby uruchomić wersję określonego obrazu, należy określić znacznik w `cmd`.

To polecenie uruchamia `bash-echo-3.yaml` pliku zadań, który odwołuje się [bash: 3.0](https://hub.docker.com/_/bash/) obrazu w usłudze Docker Hub.

```azurecli
az acr run -f bash-echo-3.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo-3.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo-3.yaml)]

#### <a name="run-custom-images"></a>Uruchamianie niestandardowych obrazów

`cmd` Typ kroku odwołuje się do obrazów przy użyciu standardowych `docker run` formatu. Przyjęto założenie, że obrazy nie są poprzedzane opcją rejestru są pochodzą z docker.io. Poprzedni przykład równie mogą być reprezentowane jako:

```yml
version: v1.0.0
steps:
  - cmd: docker.io/bash:3.0 echo hello world
```

Przy użyciu standardu `docker run` obrazu Konwencji odwołania, `cmd` uruchamiać obrazy z dowolnego rejestru prywatnego lub publicznej usługi Docker Hub. Masz odwołujące się do obrazów w rejestrze tego samego, w którym jest wykonywane zadanie usługi ACR, nie trzeba określić wszystkie poświadczenia rejestru.

* Uruchom obraz, który pochodzi z usługi Azure container registry

    Zastąp `[myregistry]` nazwą rejestru:

    ```yml
    version: v1.0.0
    steps:
        - cmd: [myregistry].azurecr.io/bash:3.0 echo hello world
    ```

* Uogólnianie dotyczące rejestru za pomocą zmiennej przebiegu

    Zamiast kodować w nazwa rejestru `acr-task.yaml` pliku, możesz przekształcić ją w bardziej przenośny przy użyciu [Uruchom zmiennej](#run-variables). `Run.Registry` Zmiennej rozszerza się w czasie wykonywania na nazwę rejestru, w którym zadanie jest wykonywane.

    Odwoływać się do uogólnienia poprzednich zadań, która działa w dowolnej usłudze Azure container registry, [Run.Registry](#runregistry) zmiennej w polu Nazwa obrazu:

    ```yml
    version: v1.0.0
    steps:
      - cmd: {{.Run.Registry}}/bash:3.0 echo hello world
    ```

## <a name="task-step-properties"></a>Właściwości kroku zadania

Każdy typ kroku obsługuje kilka właściwości odpowiednie dla jego typu. W poniższej tabeli opisano wszystkie właściwości dostępne kroku. Nie wszystkie typy kroku obsługuje wszystkie właściwości. Aby zobaczyć, które z tych właściwości są dostępne dla każdego typu kroku, zobacz [cmd](#cmd), [kompilacji](#build), i [wypychania](#push) krok sekcje odwołanie do typu.

| Właściwość | Type | Optional (Opcjonalność) | Opis | Wartość domyślna |
| -------- | ---- | -------- | ----------- | ------- |
| `detach` | bool | Yes | Czy kontenera należy odłączyć podczas uruchamiania. | `false` |
| `disableWorkingDirectoryOverride` | bool | Yes | Czy wyłączyć `workingDirectory` przesłonić funkcji. Użyj tego w połączeniu z `workingDirectory` mieć pełną kontrolę nad katalog roboczy kontenera. | `false` |
| `entryPoint` | string | Yes | Zastępuje `[ENTRYPOINT]` kontenera dla kroku. | Brak |
| `env` | [-ciąg,...] | Yes | Tablica ciągów w `key=value` formatu, który definiowania zmiennych środowiskowych dla etapu. | Brak |
| `expose` | [-ciąg,...] | Yes | Tablica porty, które są dostępne z kontenera. |  Brak |
| [`id`](#example-id) | string | Yes | Jednoznacznie identyfikuje kroku w zadaniu. Pozostałe kroki w zadaniu można odwoływać się do kroku `id`, tak samo jak w przypadku zależności sprawdzania z `when`.<br /><br />`id` Jest również nazwą uruchomionego kontenera. Procesy uruchomione w innych kontenerów w zadaniu można się odwoływać do `id` jako nazwy hosta DNS lub uzyskiwanie do niej dostępu za pomocą dzienników platformy docker [identyfikator], na przykład. | `acb_step_%d`, gdzie `%d` jest oparty na 0 indeks kroku góra dół w pliku YAML |
| `ignoreErrors` | bool | Yes | Określa, czy oznaczyć krok pomyślne niezależnie od tego, czy wystąpił błąd podczas wykonywania kontenera. | `false` |
| `isolation` | string | Yes | Poziom izolacji kontenera. | `default` |
| `keep` | bool | Yes | Czy ten krok kontenera powinny być umieszczone po wykonaniu. | `false` |
| `network` | obiekt | Yes | Identyfikuje sieci, w którym działa kontener. | Brak |
| `ports` | [-ciąg,...] | Yes | Tablica porty, które są publikowane w z kontenera do hosta. |  Brak |
| `pull` | bool | Yes | Określa, czy wymusić ściągnięcia kontenera przed wykonaniem go w celu uniemożliwienia wszystkie zachowania buforowania. | `false` |
| `privileged` | bool | Yes | Określa, czy uruchamianie kontenera w trybie uprzywilejowanym. | `false` |
| `repeat` | int | Yes | Liczba ponownych prób do Powtórz wykonanie kontenera. | 0 |
| `retries` | int | Yes | Liczba ponowień prób, jeśli kontener nie powiedzie się jej wykonanie. Ponowna próba próba nastąpi tylko wtedy, jeśli kod zakończenia kontenera jest różna od zera. | 0 |
| `retryDelay` | int (w sekundach) | Yes | Opóźnienie w sekundach między ponownymi próbami wykonania kontenera. | 0 |
| `secret` | obiekt | Yes | Określa klucz tajny w usłudze Azure Key Vault lub tożsamość zarządzaną dla zasobów platformy Azure. | Brak |
| `startDelay` | int (w sekundach) | Yes | Liczba sekund ma trwać opóźnienie wykonania kontenera. | 0 |
| `timeout` | int (w sekundach) | Yes | Maksymalna liczba sekund, przez które krok może zostać wykonany, zanim zostanie zakończone. | 600 |
| [`when`](#example-when) | [-ciąg,...] | Yes | Umożliwia skonfigurowanie zależności krok na jednym lub kilku kroków w ramach zadania. | Brak |
| `user` | string | Yes | Nazwa użytkownika lub UID kontenera | Brak |
| `workingDirectory` | string | Yes | Określa katalog roboczy dla kroku. Domyślnie zadania rejestru Azure container Registry tworzy katalog główny jako katalog roboczy. Jednak jeśli kompilacja ma kilka kroków, wcześniejszych krokach udostępnić artefaktów dalszych krokach, określając ten sam katalog roboczy. | `$HOME` |

### <a name="examples-task-step-properties"></a>Przykłady: Właściwości kroku zadania

#### <a name="example-id"></a>Przykład: identyfikator

Twórz dwa obrazy, tworzenie wystąpienia obrazu testów funkcjonalnych. Każdy krok jest identyfikowane przez unikatową `id` które odwołują się inne czynności w zadaniu ich `when` właściwości.

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

#### <a name="example-when"></a>Przykład: gdy

`when` Właściwość określa zależność to krok w pozostałe kroki w zadaniu. Obsługuje ona dwie wartości parametrów:

* `when: ["-"]` -Wskazuje Brak zależności na inne czynności. Określanie kroku `when: ["-"]` natychmiastowe rozpoczęcie wykonywania i umożliwia wykonywanie równoczesne kroku.
* `when: ["id1", "id2"]` — Wskazuje, ten krok jest zależny od kroków za pomocą `id` "id1" i `id` "identyfikator 2". Nie można wykonać ten krok, do momentu ukończenia kroków "id1" i "id2".

Jeśli `when` nie jest określona w kroku, ten krok zależy od wykonania poprzedniego kroku `acr-task.yaml` pliku.

Kolejny krok wykonywania bez `when`:

```azurecli
az acr run -f when-sequential-default.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-default.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-default.yaml)]

Kolejny krok wykonywania za pomocą `when`:

```azurecli
az acr run -f when-sequential-id.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-id.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-id.yaml)]

Obrazy równoległych kompilacji:

```azurecli
az acr run -f when-parallel.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel.yaml)]

Obraz równoległych kompilacji i testowania zależne:

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

## <a name="run-variables"></a>Uruchom zmiennych

Zadania usługi ACR zawiera domyślny zestaw zmiennych, które są dostępne do kroków zadania podczas wykonywania. Te zmienne jest możliwy przy użyciu formatu `{{.Run.VariableName}}`, gdzie `VariableName` jest jednym z następujących czynności:

* `Run.ID`
* `Run.Registry`
* `Run.Date`

### <a name="run46id"></a>Uruchom&#46;identyfikator

Każda uruchomiona za pomocą `az acr run`, oparte na wykonanie zadania utworzone za pomocą wyzwalacza lub `az acr task create` ma unikatowy identyfikator. Identyfikator reprezentuje wykonywania obecnie wykonywane.

Zazwyczaj używane w celu jednoznacznego tagowanie obrazu:

```yml
version: v1.0.0
steps:
    - build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} .
```

### <a name="runregistry"></a>Run.Registry

W pełni kwalifikowaną nazwę serwera rejestru. Zwykle używane do ogólnej odwoływać się do rejestru, w którym zostało uruchomione zadanie.

```yml
version: v1.0.0
steps:
  - build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} .
```

### <a name="rundate"></a>Run.Date

Bieżący czas UTC rozpoczęcia przebiegu.

## <a name="next-steps"></a>Kolejne kroki

Omówienie wieloetapowych zadań, zobacz [uruchamianie wieloetapowych kompilacji, testów i zadania poprawki w zadaniach usługi ACR](container-registry-tasks-multi-step.md).

Pojedynczy krok kompilacji, zobacz [Omówienie zadań ACR](container-registry-tasks-overview.md).

<!-- IMAGES -->

<!-- LINKS - External -->
[acr-tasks]: https://github.com/Azure-Samples/acr-tasks

<!-- LINKS - Internal -->
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-configure]: /cli/azure/reference-index#az-configure

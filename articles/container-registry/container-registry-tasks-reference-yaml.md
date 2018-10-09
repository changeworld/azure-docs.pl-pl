---
title: Dokumentacja usługi Azure Container Registry zadań - YAML
description: Odwołanie do definiowania zadań w YAML dla zadania usługi ACR, w tym właściwości zadania, krok typy, właściwości kroku i wbudowane zmienne.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 09/24/2018
ms.author: danlep
ms.openlocfilehash: a26b766c18ea2b14796af796abe7f36aa4fc1c66
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2018
ms.locfileid: "48854953"
---
# <a name="acr-tasks-reference-yaml"></a>Odwołanie do zadania usługi ACR: YAML

Definicji zadania wieloetapowe w zadaniach rejestru Azure container Registry zapewnia podstawowy skoncentrowane na kontenerze obliczeń koncentruje się na tworzenie, testowanie i instalowanie poprawek kontenerów. W tym artykule opisano polecenia, parametry, właściwości i składnia dla plików kodu YAML, które definiują wieloetapowych zadań podrzędnych.

Ten artykuł zawiera odwołanie do tworzenia plików YAML wieloetapowego zadania dla zadań usługi ACR. Jeśli chcesz zapoznać się z wprowadzeniem do usługi ACR zadań, zobacz [Omówienie zadań ACR](container-registry-tasks-overview.md).

> [!IMPORTANT]
> Funkcja zadania wieloetapowe zadań rejestru Azure container Registry jest obecnie w wersji zapoznawczej. Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania][terms-of-use]. Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.

## <a name="acr-taskyaml-file-format"></a>format pliku task.yaml rejestru Azure container Registry

Zadania rejestru Azure container Registry obsługuje deklaracji wieloetapowego zadania w standardowej składni kodu YAML. Kroki zadań definiuje się w pliku YAML, który następnie można uruchomić ręcznie lub zostały wyzwolone automatycznie po aktualizacji obraz zatwierdzenia lub podstawowa usługi Git. Chociaż ten artykuł dotyczy `acr-task.yaml` co plik zawierający kroki, zadania rejestru Azure container Registry obsługuje wszystkie prawidłową nazwę pliku z [obsługiwane rozszerzenia](#supported-task-filename-extensions).

Najwyższego poziomu `acr-task.yaml` są w nim elementów podstawowych **we właściwościach zadania**, **krok typy**, i **krok właściwości**:

* [We właściwościach zadania](#task-properties) dotyczą wszystkie kroki wykonywane w ramach wykonywania zadania. Istnieją trzy właściwości globalnego zadania:
  * version
  * stepTimeout
  * totalTimeout
* [Zadanie typy kroku](#task-step-types) reprezentują typy akcji, które mogą być wykonywane w zadaniu. Istnieją trzy typy krok:
  * Kompilacja
  * wypychania
  * cmd
* [We właściwościach krok zadania](#task-step-properties) parametrów, które są stosowane do pojedynczego kroku. Istnieje kilka właściwości kroku, w tym:
  * startDelay
  * timeout
  * kiedy
  * .. .i wiele innych.

Podstawowy format `acr-task.yaml` następuje po pliku, w tym niektóre typowe właściwości kroku. Podczas nie wyczerpujący reprezentacja wszystkich właściwości dostępne kroku lub krok typu użycia zapewnia szybki przegląd format pliku podstawowego.

```yaml
version: # acr-task.yaml format version.
stepTimeout: # Seconds each step may take.
totalTimeout: # Total seconds allowed for all steps to complete.
steps: # A collection of image or container actions.
    build: # Equivalent to "docker build," but in a multi-tenant environment
    push: # Push a newly built or retagged image to a registry.
      when: # Step property that defines either parallel or dependent step execution.
    cmd: # Executes a container, supports specifying an [ENTRYPOINT] and parameters.
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

Właściwości zadania zazwyczaj znajdują się w górnej części `acr-task.yaml` pliku i są globalne właściwości, które dotyczą całej pełnego wykonania zadania. Niektóre z tych właściwości globalne można przesłonić, w ramach pojedynczego kroku.

| Właściwość | Typ | Optional (Opcjonalność) | Opis | Zastąpienie obsługiwane | Wartość domyślna |
| -------- | ---- | -------- | ----------- | ------------------ | ------------- |
| `version` | ciąg | Nie | Wersja `acr-task.yaml` plików analizowane przez usługę zadań usługi ACR. Gdy zadania ACR dokłada wszelkich starań zachować zgodność z poprzednimi wersjami, ta wartość umożliwia zadania ACR utrzymać zgodność, w ramach określonych wersji. | Nie | Brak |
| `stepTimeout` | int (w sekundach) | Yes | Maksymalna liczba sekund, przez krok można uruchomić. Ta właściwość może zostać przesłonięta w kroku przez ustawienie w kroku [limitu czasu](#timeout) właściwości. | Yes | 600 (10 minut) |
| `totalTimeout` | int (w sekundach) | Yes | Maksymalna liczba sekund, które może uruchomić zadania. "Uruchom" obejmuje wykonanie i zakończeniu wszystkich kroków w zadaniu, czy powodzeniem lub niepowodzeniem. Dołączone do zadania drukowania generowane takich jak obraz wykryte zależności i stan wykonania zadania. | Nie | 3600 (1 godzina) |

## <a name="task-step-types"></a>Typy kroku zadań

Zadania rejestru Azure container Registry obsługuje trzy typy kroku. Każdy typ kroku obsługuje kilka właściwości, szczegółowo opisane w sekcji dla każdego typu kroku.

| Typ kroku | Opis |
| --------- | ----------- |
| [`build`](#build) | Tworzy obraz kontenera przy użyciu dobrze znanej `docker build` składni. |
| [`push`](#push) | Wykonuje `docker push` nowo utworzone lub zmieniony obrazów do rejestru kontenerów. Usługa Azure Container Registry, innych rejestrów prywatnych i publicznych usługi Docker Hub są obsługiwane.
| [`cmd`](#cmd) | Uruchamia kontener jako polecenia, za pomocą parametrów jest przekazywane do kontenera `[ENTRYPOINT]`. `cmd` Typ kroku obsługuje parametry, takie jak env, odłączyć i inne znany `docker run` opcji i włączenie jednostki i testy funkcjonalne dzięki kontenera współbieżne wykonywanie poleceń. |

## <a name="build"></a>Kompilacja

Tworzenie obrazu kontenera. `build` Typ kroku reprezentuje wielodostępne, bezpieczna oznacza uruchomienia `docker build` w chmurze jako podstawowy najwyższej jakości.

### <a name="syntax-build"></a>Składnia: kompilacji

```yaml
version: 1.0-preview-1
steps:
    - [build]: -t [imageName]:[tag] -f [Dockerfile] [context]
      [property]: [value]
```

`build` Typ kroku obsługuje następujące parametry:

| Parametr | Opis | Optional (Opcjonalność) |
| --------- | ----------- | :-------: |
| `-t` &#124; `--image` | Określa w pełni kwalifikowaną `image:tag` wbudowanego obrazu.<br /><br />Ponieważ obrazy mogą być używane do walidacji wewnętrzny zadań, takich jak testy funkcjonalne, nie wszystkie obrazy wymagają `push` do rejestru. Do wystąpienia obraz w obrębie wykonywania zadań, obraz, który muszą mieć jednak możliwość nazwę, aby odwoływać się.<br /><br />W odróżnieniu od `az acr build`, uruchamianie zadań rejestru Azure container Registry nie zapewnia domyślne zachowanie wypychania. Za pomocą zadań ACR scenariusza domyślne przyjęto założenie, umożliwia tworzenie, weryfikowanie, a następnie wypchnąć obraz. Zobacz [wypychania](#push) dotyczące opcjonalnie wypychania wbudowanych obrazów. | Yes |
| `-f` &#124; `--file` | Określa plik Dockerfile przekazany do `docker build`. Jeśli nie zostanie określony, domyślnie, przyjmowana jest plik Dockerfile w katalogu głównym kontekstu. Aby określić zamiast pliku Dockerfile, należy przekazać nazwę pliku względem katalogu głównego kontekstu. | Yes |
| `context` | Katalog główny jest przekazywany do `docker build`. Katalog główny każdego zadania jest ustawiony do udostępnionej [workingDirectory](#task-step-properties)i zawiera katalog główny skojarzony sklonowanym katalogu Git. | Nie |

### <a name="properties-build"></a>Właściwości: kompilacji

`build` Typ kroku obsługuje następujące właściwości można znaleźć szczegółowe informacje o tych właściwości w [właściwości krok zadania](#task-step-properties) dalszej części tego artykułu.

| | | |
| -------- | ---- | -------- |
| `detach` | wartość logiczna | Optional (Opcjonalność) |
| `entryPoint` | ciąg | Optional (Opcjonalność) |
| `env` | [-ciąg,...] | Optional (Opcjonalność) |
| `id` | ciąg | Optional (Opcjonalność) |
| `ignoreErrors` | wartość logiczna | Optional (Opcjonalność) |
| `keep` | wartość logiczna | Optional (Opcjonalność) |
| `startDelay` | int (w sekundach) | Optional (Opcjonalność) |
| `timeout` | int (w sekundach) | Optional (Opcjonalność) |
| `when` | [-ciąg,...] | Optional (Opcjonalność) |
| `workingDirectory` | ciąg | Optional (Opcjonalność) |

### <a name="examples-build"></a>Przykłady: kompilacji

#### <a name="build-image---context-in-root"></a>Tworzenie obrazu — kontekst, w katalogu głównym

```azurecli
az acr run -f build-hello-world.yaml https://github.com/AzureCR/acr-tasks-sample.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml --> [!code-yml[task](~/acr-tasks/build-hello-world.yaml)]

#### <a name="build-image---context-in-subdirectory"></a>Tworzenie obrazu — kontekst w podkatalogu

```yaml
version: 1.0-preview-1
steps:
- build: -t {{.Run.Registry}}/hello-world -f hello-world.dockerfile ./subDirectory
```

## <a name="push"></a>wypychania

Wypychanie jeden lub więcej skompilowany lub zmieniony obrazów do rejestru kontenerów. Obsługuje Wypychanie do prywatnych rejestrów, takich jak usługa Azure Container Registry lub publicznej usługi Docker Hub.

### <a name="syntax-push"></a>Składnia: wypychania

`push` Typ kroku obsługuje zestaw obrazów. Składnia kolekcji YAML obsługuje wbudowane i formatów zagnieżdżonych. Wypychanie obrazu jednego jest zazwyczaj reprezentowany za pomocą składni wbudowanego:

```yaml
version: 1.0-preview-1
steps:
  # Inline YAML collection syntax
  - push: ["{{.Run.Registry}}/hello-world:{{.Run.ID}}"]
```

Dla zwiększenia czytelności należy użyć składni zagnieżdżonych podczas wypychania wiele obrazów:

```yaml
version: 1.0-preview-1
steps:
  # Nested YAML collection syntax
  - push:
    - {{.Run.Registry}}/hello-world:{{.Run.ID}}
    - {{.Run.Registry}}/hello-world:latest
```

### <a name="properties-push"></a>Właściwości: wypychania

`push` Typ kroku obsługuje następujące właściwości. Można znaleźć szczegółowe informacje o tych właściwości w [właściwości krok zadania](#task-step-properties) dalszej części tego artykułu.

| | | |
| -------- | ---- | -------- |
| `env` | [-ciąg,...] | Optional (Opcjonalność) |
| `id` | ciąg | Optional (Opcjonalność) |
| `ignoreErrors` | wartość logiczna | Optional (Opcjonalność) |
| `startDelay` | int (w sekundach) | Optional (Opcjonalność) |
| `timeout` | int (w sekundach) | Optional (Opcjonalność) |
| `when` | [-ciąg,...] | Optional (Opcjonalność) |

### <a name="examples-push"></a>Przykłady: wypychania

#### <a name="push-multiple-images"></a>Wypychanie wielu obrazów

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml --> [!code-yml[task](~/acr-tasks/build-push-hello-world.yaml)]

#### <a name="build-push-and-run"></a>Kompilowanie, wypychanie i uruchom

```azurecli
az acr run -f build-run-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml --> [!code-yml[task](~/acr-tasks/build-run-hello-world.yaml)]

## <a name="cmd"></a>cmd

`cmd` Typ kroku uruchamiała kontener.

### <a name="syntax-cmd"></a>Składnia: cmd

```yaml
version: 1.0-preview-1
steps:
    - [cmd]: [containerImage]:[tag (optional)] [cmdParameters to the image]
```

### <a name="properties-cmd"></a>Właściwości: cmd

`cmd` Typ kroku obsługuje następujące właściwości:

| | | |
| -------- | ---- | -------- |
| `detach` | wartość logiczna | Optional (Opcjonalność) |
| `entryPoint` | ciąg | Optional (Opcjonalność) |
| `env` | [-ciąg,...] | Optional (Opcjonalność) |
| `id` | ciąg | Optional (Opcjonalność) |
| `ignoreErrors` | wartość logiczna | Optional (Opcjonalność) |
| `keep` | wartość logiczna | Optional (Opcjonalność) |
| `startDelay` | int (w sekundach) | Optional (Opcjonalność) |
| `timeout` | int (w sekundach) | Optional (Opcjonalność) |
| `when` | [-ciąg,...] | Optional (Opcjonalność) |
| `workingDirectory` | ciąg | Optional (Opcjonalność) |

Można znaleźć szczegółowe informacje o tych właściwości w [właściwości krok zadania](#task-step-properties) dalszej części tego artykułu.

### <a name="examples-cmd"></a>Przykłady: cmd

#### <a name="run-hello-world-image"></a>Uruchom obraz Witaj, świecie

To polecenie uruchamia `hello-world.yaml` pliku zadań, który odwołuje się [Witaj, świecie](https://hub.docker.com/_/hello-world/) obrazu w usłudze Docker Hub.

```azurecli
az acr run -f hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.yaml --> [!code-yml[task](~/acr-tasks/hello-world.yaml)]

#### <a name="run-bash-image-and-echo-hello-world"></a>Uruchom powłokę bash obrazu i echa "hello world"

To polecenie uruchamia `bash-echo.yaml` pliku zadań, który odwołuje się [bash](https://hub.docker.com/_/bash/) obrazu w usłudze Docker Hub.

```azurecli
az acr run -f bash-echo.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml --> [!code-yml[task](~/acr-tasks/bash-echo.yaml)]

#### <a name="run-specific-bash-image-tag"></a>Uruchamianie określonych bash tagu obrazu

Aby uruchomić wersję określonego obrazu, należy określić znacznik w `cmd`.

To polecenie uruchamia `bash-echo-3.yaml` pliku zadań, który odwołuje się [bash: 3.0](https://hub.docker.com/_/bash/) obrazu w usłudze Docker Hub.

```azurecli
az acr run -f bash-echo-3.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo-3.yaml --> [!code-yml[task](~/acr-tasks/bash-echo-3.yaml)]

#### <a name="run-custom-images"></a>Uruchamianie niestandardowych obrazów

`cmd` Typ kroku odwołuje się do obrazów przy użyciu standardowych `docker run` formatu. Przyjęto założenie, że obrazy nie są poprzedzane opcją rejestru są pochodzą z docker.io. Poprzedni przykład równie mogą być reprezentowane jako:

```yaml
version: 1.0-preview-1
steps:
    - cmd: docker.io/bash:3.0 echo hello world
```

Przy użyciu standardu `docker run` obrazu Konwencji odwołania, `cmd` uruchamiać obrazy znajdujące się w dowolnego rejestru prywatnego lub publicznej usługi Docker Hub. Masz odwołujące się do obrazów w rejestrze tego samego, w którym jest wykonywane zadanie usługi ACR, nie trzeba określić wszystkie poświadczenia rejestru.

* Uruchamianie obrazu z znajdujących się w usłudze Azure container registry

    Zastąp `[myregistry]` nazwą rejestru:

    ```yaml
    version: 1.0-preview-1
    steps:
        - cmd: [myregistry].azurecr.io/bash:3.0 echo hello world
    ```

* Uogólnianie dotyczące rejestru za pomocą zmiennej przebiegu

    Zamiast kodować w nazwa rejestru `acr-task.yaml` pliku, możesz przekształcić ją w bardziej przenośny przy użyciu [Uruchom zmiennej](#run-variables). `Run.Registry` Zmiennej rozszerza się w czasie wykonywania na nazwę rejestru, w którym zadanie jest wykonywane.

    Odwoływać się do uogólnienia poprzednich zadań, która działa w dowolnej usłudze Azure container registry, [Run.Registry](#runregistry) zmiennej w polu Nazwa obrazu:

    ```yaml
    version: 1.0-preview-1
    steps:
        - cmd: {{.Run.Registry}}/bash:3.0 echo hello world
    ```

## <a name="task-step-properties"></a>Właściwości kroku zadania

Każdy typ kroku obsługuje kilka właściwości odpowiednie dla jego typu. W poniższej tabeli opisano wszystkie właściwości dostępne kroku. Nie wszystkie typy kroku obsługuje wszystkie właściwości. Aby zobaczyć, które z tych właściwości są dostępne dla każdego typu kroku, zobacz [cmd](#cmd), [kompilacji](#build), i [wypychania](#push) krok sekcje odwołanie do typu.

| Właściwość | Typ | Optional (Opcjonalność) | Opis |
| -------- | ---- | -------- | ----------- |
| `detach` | wartość logiczna | Yes | Czy kontenera należy odłączyć podczas uruchamiania. |
| `entryPoint` | ciąg | Yes | Zastępuje `[ENTRYPOINT]` kontenera dla kroku. |
| `env` | [-ciąg,...] | Yes | Tablica ciągów w `key=value` formatu, który definiowania zmiennych środowiskowych dla etapu. |
| [`id`](#example-id) | ciąg | Yes | Jednoznacznie identyfikuje kroku w zadaniu. Pozostałe kroki w zadaniu można odwoływać się do kroku `id`, tak samo jak w przypadku zależności sprawdzania z `when`.<br /><br />`id` Jest również nazwą uruchomionego kontenera. Procesy uruchomione w innych kontenerów w zadaniu można się odwoływać do `id` jako nazwy hosta DNS lub uzyskiwanie do niej dostępu za pomocą dzienników platformy docker [identyfikator], na przykład. |
| `ignoreErrors` | wartość logiczna | Yes | Po ustawieniu `true`, ten krok jest oznaczane jako ukończone, niezależnie od tego, czy wystąpił błąd podczas jego wykonywania. Wartość domyślna: `false`. |
| `keep` | wartość logiczna | Yes | Czy ten krok kontenera powinny być umieszczone po wykonaniu. |
| `startDelay` | int (w sekundach) | Yes | Liczba sekund ma trwać opóźnienie wykonania kroku. |
| `timeout` | int (w sekundach) | Yes | Maksymalna liczba sekund, przez które krok może zostać wykonany, zanim zostanie zakończone. |
| [`when`](#example-when) | [-ciąg,...] | Yes | Umożliwia skonfigurowanie zależności krok na jednym lub kilku kroków w ramach zadania. |
| `workingDirectory` | ciąg | Yes | Określa katalog roboczy dla kroku. Domyślnie zadania rejestru Azure container Registry tworzy katalog główny jako katalog roboczy. Jednak jeśli kompilacja ma kilka kroków, wcześniejszych krokach udostępnić artefaktów dalszych krokach, określając ten sam katalog roboczy. |

### <a name="examples-task-step-properties"></a>Przykłady: Właściwości kroku zadania

#### <a name="example-id"></a>Przykład: identyfikator

Twórz dwa obrazy, tworzenie wystąpienia obrazu testów funkcjonalnych. Każdy krok jest identyfikowane przez unikatową `id` które odwołują się inne czynności w zadaniu ich `when` właściwości.

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml --> [!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

#### <a name="example-when"></a>Przykład: gdy

`when` Właściwość określa zależność to krok w pozostałe kroki w zadaniu. Obsługuje ona dwie wartości parametrów:

* `when: ["-"]` -Wskazuje Brak zależności na inne czynności. Określanie kroku `when: ["-"]` natychmiastowe rozpoczęcie wykonywania i umożliwia wykonywanie równoczesne kroku.
* `when: ["id1", "id2"]` — Wskazuje, ten krok jest zależny od kroków za pomocą `id` "id1" i `id` "identyfikator 2". Nie można wykonać ten krok, do momentu ukończenia kroków "id1" i "id2".

Jeśli `when` nie jest określona w kroku, ten krok zależy od wykonania poprzedniego kroku `acr-task.yaml` pliku.

Kolejny krok wykonywania bez `when`:

```azurecli
az acr run -f when-sequential-default.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-default.yaml --> [!code-yml[task](~/acr-tasks/when-sequential-default.yaml)]

Kolejny krok wykonywania za pomocą `when`:

```azurecli
az acr run -f when-sequential-id.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-id.yaml --> [!code-yml[task](~/acr-tasks/when-sequential-id.yaml)]

Tworzenie obrazu równoległego:

```azurecli
az acr run -f when-parallel.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml --> [!code-yml[task](~/acr-tasks/when-parallel.yaml)]

Obraz równoległych kompilacji i testowania zależne:

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml --> [!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

## <a name="run-variables"></a>Uruchom zmiennych

Zadania usługi ACR zawiera domyślny zestaw zmiennych, które są dostępne do kroków zadania podczas wykonywania. Te zmienne jest możliwy przy użyciu formatu `{{.Run.VariableName}}`, gdzie `VariableName` jest jednym z następujących czynności:

* `Run.ID`
* `Run.Registry`
* `Run.Date`

### <a name="run46id"></a>Uruchom&#46;identyfikator

Każda uruchomiona za pomocą `az acr run`, oparte na wykonanie zadania utworzone za pomocą wyzwalacza lub `az acr task create` ma unikatowy identyfikator. Identyfikator reprezentuje wykonywania obecnie wykonywane.

Zazwyczaj używane w celu jednoznacznego tagowanie obrazu:

```yaml
version: 1.0-preview-1
steps:
    - build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} .
```

### <a name="runregistry"></a>Run.Registry

W pełni kwalifikowaną nazwę serwera rejestru. Zwykle używane do ogólnej odwoływać się do rejestru, w którym zostało uruchomione zadanie.

```yaml
version: 1.0-preview-1
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
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-run]: /cli/azure/acr/run#az-acr-run
[az-configure]: /cli/azure/reference-index#az-configure
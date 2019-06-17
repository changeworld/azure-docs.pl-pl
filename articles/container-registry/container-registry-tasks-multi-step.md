---
title: Automatyzowanie kompilacji obrazów, testowania i stosowanie poprawek za pomocą usługi Azure Container Registry wieloetapowego zadania
description: Wprowadzenie do zadań wieloetapowego funkcji zadań rejestru Azure container Registry w rejestrze Azure Container Registry zapewnia przepływów pracy opartych na zadania tworzenia, testowania i poprawianie obrazów kontenerów w chmurze.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 03/28/2019
ms.author: danlep
ms.openlocfilehash: ac0e4e9019a35d3fdb35c0b7af9cb1289f4bceeb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60829586"
---
# <a name="run-multi-step-build-test-and-patch-tasks-in-acr-tasks"></a>Uruchamianie wieloetapowych kompilacji, testów i zadania poprawki w zadaniach usługi ACR

Zadania wieloetapowe rozszerzyć możliwości kompilacji i wypychania pojedynczy obraz ACR zadania za pomocą wieloetapowych przepływów pracy opartych na systemie wielu container. Zadania wieloetapowe umożliwia kompilowanie i wypychanie kilka obrazów w serii lub równolegle. Następnie uruchom te obrazy jako polecenia w ramach jednego zadania uruchamiania. Każdy krok definiuje obraz kontenera kompilacji lub operację wypychania i można również zdefiniować wykonywania kontenera. Każdy krok w zadaniu wieloetapowego używa kontener jako jego środowiska wykonawczego.

> [!IMPORTANT]
> Jeśli wcześniej utworzono zadania za pomocą wersji zapoznawczej przy użyciu polecenia `az acr build-task`, trzeba je utworzyć ponownie przy użyciu polecenia [az acr task][az-acr-task].

Na przykład można uruchomić zadanie z krokami, które automatyzują logiki poniższym:

1. Tworzenie obrazu aplikacji sieci web
1. Uruchamianie kontenera aplikacji internetowej
1. Zbuduj obraz testów aplikacji sieci web
1. Uruchom kontener testu aplikacji sieci web, która wykonuje testy względem uruchomionych aplikacji kontenera
1. Jeśli testy zostaną wykryte, utworzyć pakiet archiwum wykresu Helm
1. Wykonaj `helm upgrade` przy użyciu nowego pakietu archiwum wykresu Helm

Wszystkie kroki są wykonywane w ramach platformy Azure, Odciążanie pracy zasoby obliczeniowe platformy Azure i dzięki temu możesz z zarządzania infrastrukturą. Oprócz usługi Azure container registry płacisz tylko za wykorzystane zasoby. Aby uzyskać informacje o cenach, zobacz **Tworzenie kontenera** sekcji [cennika usługi Azure Container Registry][pricing].


## <a name="common-task-scenarios"></a>Typowe scenariusze zadania

Zadania wieloetapowe obsługi takich scenariuszy jak Poniższa logika:

* Tworzenie tagu, a następnie Wypchnij jednego lub więcej obrazów kontenera, w serii lub równolegle.
* Uruchom, a następnie przechwycić wyniki pokrycia testu i kodu jednostki.
* Uruchom, a następnie przechwycić testy funkcjonalne. Zadania rejestru Azure container Registry obsługuje więcej niż jednego kontenera wykonanie serii żądań między nimi.
* Wykonaj opartego na zadaniach wykonania, w tym wstępnie lub używanego po nim kroki kompilacji obrazu kontenera.
* Należy wdrożyć co najmniej jeden kontener z aparatem swoje ulubione wdrożenia do środowiska docelowego.

## <a name="multi-step-task-definition"></a>Definicja zadania wieloetapowe

Zadania wieloetapowe w zadaniach usługi ACR jest zdefiniowany jako szereg kroków w ramach pliku YAML. Każdy krok można określić zależności po pomyślnym ukończeniu przynajmniej jednego z poprzednich kroków. Dostępne są następujące typy zadań w kroku:

* [`build`](container-registry-tasks-reference-yaml.md#build): Tworzenie jednego lub więcej obrazów kontenerów przy użyciu dobrze znanej `docker build` składnię, w serii lub równolegle.
* [`push`](container-registry-tasks-reference-yaml.md#push): Wypychaj obrazy utworzone do rejestru kontenerów. Rejestry prywatne, takie jak usługa Azure Container Registry są obsługiwane, ponieważ jest publicznej usługi Docker Hub.
* [`cmd`](container-registry-tasks-reference-yaml.md#cmd): Uruchomienia kontenera, w taki sposób, że może działać jako funkcję w kontekście bieżące zadanie. Parametry można przekazać do kontenera `[ENTRYPOINT]`i określ właściwości, takie jak env, odłączyć i inne znany `docker run` parametrów. `cmd` Typ kroku umożliwia jednostki i testy funkcjonalne z dzięki wykonywanie równoczesne kontenera.

Poniższe fragmenty kodu przedstawiają sposób łączenia tych typów krok zadania. Zadania wieloetapowe mogą być proste i polega na tworzeniu pojedynczy obraz z pliku Dockerfile i Wypychanie do rejestru, przy użyciu pliku YAML, podobnie jak:

```yml
version: v1.0.0
steps:
  - build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} .
  - push: ["{{.Run.Registry}}/hello-world:{{.Run.ID}}"]
```

Lub bardziej skomplikowane, jak to fikcyjna definicji wieloetapowy, która obejmuje kroki kompilacji, testów, pakiet narzędzia helm i helm wdrażanie (rejestr kontenerów i konfiguracja repozytorium narzędzia Helm niewyświetlany):

```yml
version: v1.0.0
steps:
  - id: build-web
    build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} .
    when: ["-"]
  - id: build-tests
    build -t {{.Run.Registry}}/hello-world-tests ./funcTests
    when: ["-"]
  - id: push
    push: ["{{.Run.Registry}}/helloworld:{{.Run.ID}}"]
    when: ["build-web", "build-tests"]
  - id: hello-world-web
    cmd: {{.Run.Registry}}/helloworld:{{.Run.ID}}
  - id: funcTests
    cmd: {{.Run.Registry}}/helloworld:{{.Run.ID}}
    env: ["host=helloworld:80"]
  - cmd: {{.Run.Registry}}/functions/helm package --app-version {{.Run.ID}} -d ./helm ./helm/helloworld/
  - cmd: {{.Run.Registry}}/functions/helm upgrade helloworld ./helm/helloworld/ --reuse-values --set helloworld.image={{.Run.Registry}}/helloworld:{{.Run.ID}}
```

Zobacz [zadań przykłady] [ task-examples] do ukończenia zadania wieloetapowe YAML pliki i pliki Dockerfile dla kilku scenariuszy.

## <a name="run-a-sample-task"></a>Uruchom zadanie próbkowania

Zadania obsługują zarówno ręcznego wykonywania o nazwie "Szybkie uruchamianie,", i zaktualizuj automatyczne wykonywanie w obrazie zatwierdzenia lub podstawowa usługi Git.

Do uruchomienia zadania, należy najpierw zdefiniować czynności zadania podrzędnego w pliku YAML, a następnie wykonanie polecenia wiersza polecenia platformy Azure [az acr Uruchom][az-acr-run].

Oto przykład polecenia wiersza polecenia platformy Azure, który uruchamia zadanie przy użyciu pliku YAML przykładowe zadania. Jej kroków kompilacji, a następnie Wypchnij obraz. Aktualizacja `\<acrName\>` nazwą własnego rejestru kontenerów platformy Azure, przed uruchomieniem polecenia.

```azurecli
az acr run --registry <acrName> -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

Po uruchomieniu zadania, dane wyjściowe powinny pokazywać postęp każdego kroku, zdefiniowane w pliku YAML. Kroki są wyświetlane następujące dane wyjściowe jako `acb_step_0` i `acb_step_1`.

```console
$ az acr run --registry myregistry -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
Sending context to registry: myregistry...
Queued a run with ID: yd14
Waiting for an agent...
2018/09/12 20:08:44 Using acb_vol_0467fe58-f6ab-4dbd-a022-1bb487366941 as the home volume
2018/09/12 20:08:44 Creating Docker network: acb_default_network
2018/09/12 20:08:44 Successfully set up Docker network: acb_default_network
2018/09/12 20:08:44 Setting up Docker configuration...
2018/09/12 20:08:45 Successfully set up Docker configuration
2018/09/12 20:08:45 Logging in to registry: myregistry.azurecr-test.io
2018/09/12 20:08:46 Successfully logged in
2018/09/12 20:08:46 Executing step: acb_step_0
2018/09/12 20:08:46 Obtaining source code and scanning for dependencies...
2018/09/12 20:08:47 Successfully obtained source code and scanned for dependencies
Sending build context to Docker daemon  109.6kB
Step 1/1 : FROM hello-world
 ---> 4ab4c602aa5e
Successfully built 4ab4c602aa5e
Successfully tagged myregistry.azurecr-test.io/hello-world:yd14
2018/09/12 20:08:48 Executing step: acb_step_1
2018/09/12 20:08:48 Pushing image: myregistry.azurecr-test.io/hello-world:yd14, attempt 1
The push refers to repository [myregistry.azurecr-test.io/hello-world]
428c97da766c: Preparing
428c97da766c: Layer already exists
yd14: digest: sha256:1a6fd470b9ce10849be79e99529a88371dff60c60aab424c077007f6979b4812 size: 524
2018/09/12 20:08:55 Successfully pushed image: myregistry.azurecr-test.io/hello-world:yd14
2018/09/12 20:08:55 Step id: acb_step_0 marked as successful (elapsed time in seconds: 2.035049)
2018/09/12 20:08:55 Populating digests for step id: acb_step_0...
2018/09/12 20:08:57 Successfully populated digests for step id: acb_step_0
2018/09/12 20:08:57 Step id: acb_step_1 marked as successful (elapsed time in seconds: 6.832391)
The following dependencies were found:
- image:
    registry: myregistry.azurecr-test.io
    repository: hello-world
    tag: yd14
    digest: sha256:1a6fd470b9ce10849be79e99529a88371dff60c60aab424c077007f6979b4812
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:0add3ace90ecb4adbf7777e9aacf18357296e799f81cabc9fde470971e499788
  git: {}


Run ID: yd14 was successful after 19s
```

Aby dowiedzieć się więcej o automatycznych kompilacji po aktualizacji obraz zatwierdzenia lub podstawowa usługi Git, zobacz [Automatyzowanie kompilacji obrazów](container-registry-tutorial-build-task.md) i [podstawowa kompilacje aktualizacji obrazów](container-registry-tutorial-base-image-update.md) kolejnymi artykułami samouczków.

## <a name="next-steps"></a>Kolejne kroki

Odwołanie do zadania wieloetapowe a podane tutaj przykłady można znaleźć:

* [Zadania, odwołanie](container-registry-tasks-reference-yaml.md) — zadanie kroku typy, właściwości i użycia.
* [Przykłady zadań] [ task-examples] — przykład `task.yaml` plików dla kilku scenariuszy prostych po złożone.
* [Repozytorium cmd](https://github.com/AzureCR/cmd) — zbiór kontenery jako polecenia dla zadań usługi ACR.

<!-- IMAGES -->

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[task-examples]: https://github.com/Azure-Samples/acr-tasks
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task]: /cli/azure/acr/task
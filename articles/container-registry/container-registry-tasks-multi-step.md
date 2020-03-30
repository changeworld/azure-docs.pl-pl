---
title: Wieloetapowe zadanie tworzenia, testowania & obrazu poprawek
description: Wprowadzenie do zadań wieloetapowych, funkcja zadań usługi ACR w rejestrze kontenerów platformy Azure, która zapewnia oparte na zadaniach przepływy pracy do tworzenia, testowania i poprawiania obrazów kontenerów w chmurze.
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 0dcd38559d3f50715f982de4c9c80bfe9c6c8433
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399699"
---
# <a name="run-multi-step-build-test-and-patch-tasks-in-acr-tasks"></a>Uruchamianie wieloetapowych zadań kompilacji, testowania i instalowania poprawek w zadaniach usługi ACR

Zadania wieloetapowe rozszerzają możliwości tworzenia i wypychania pojedynczego obrazu zadań ACR za pomocą wieloetapowych, wielowyjęzykowych przepływów pracy. Wykonywanie zadań wieloetapowych umożliwia tworzenie i wypychanie kilku obrazów szeregowo lub równolegle. Następnie uruchom te obrazy jako polecenia w ramach jednego uruchomienia zadania. Każdy krok definiuje operację kompilacji lub wypychania obrazu kontenera, a także może definiować wykonanie kontenera. Każdy krok w zadaniu wieloetapowym używa kontenera jako środowiska wykonywania.

> [!IMPORTANT]
> Jeśli wcześniej utworzono zadania za pomocą wersji zapoznawczej przy użyciu polecenia `az acr build-task`, trzeba je utworzyć ponownie przy użyciu polecenia [az acr task][az-acr-task].

Na przykład można uruchomić zadanie z krokami, które automatyzują następującą logikę:

1. Tworzenie obrazu aplikacji sieci Web
1. Uruchamianie kontenera aplikacji sieci web
1. Tworzenie obrazu testowego aplikacji sieci Web
1. Uruchom kontener testowy aplikacji sieci web, który wykonuje testy dla uruchomionego kontenera aplikacji
1. Jeśli testy zdają wyniki, skompiluj pakiet archiwum wykresu Helm
1. Wykonywanie `helm upgrade` przy użyciu nowego pakietu archiwum wykresu Helm

Wszystkie kroki są wykonywane w ramach platformy Azure, odciążając pracę do zasobów obliczeniowych platformy Azure i zwalniając cię z zarządzania infrastrukturą. Oprócz rejestru kontenerów platformy Azure płacisz tylko za zasoby, których używasz. Aby uzyskać informacje na temat cen, zobacz sekcję **Kompilacja kontenerów** w [cenniku rejestru kontenerów platformy Azure][pricing].


## <a name="common-task-scenarios"></a>Typowe scenariusze zadań

Zadania wieloetapowe umożliwiają scenariusze, takie jak następująca logika:

* Tworzenie, oznaczanie i wypychanie jednego lub większej liczby obrazów kontenerów szeregowo lub równolegle.
* Uruchamianie i przechwytywanie wyników testu jednostki i pokrycia kodu.
* Uruchamianie i przechwytywanie testów funkcjonalnych. Zadania usługi ACR obsługuje uruchamianie więcej niż jednego kontenera, wykonując serię żądań między nimi.
* Wykonywanie zadań, w tym kroki przed/księgowanie kompilacji obrazu kontenera.
* Wdrażanie jednego lub więcej kontenerów z ulubionym aparatem wdrażania w środowisku docelowym.

## <a name="multi-step-task-definition"></a>Wieloetapowa definicja zadania

Wieloetapowe zadanie w zadaniach usługi ACR jest definiowane jako seria kroków w pliku YAML. Każdy krok można określić zależności na pomyślne zakończenie jednego lub więcej poprzednich kroków. Dostępne są następujące typy kroków zadań:

* [`build`](container-registry-tasks-reference-yaml.md#build): Tworzenie jednego lub więcej `docker build` obrazów kontenerów przy użyciu znanej składni, szeregowo lub równolegle.
* [`push`](container-registry-tasks-reference-yaml.md#push): Wypychanie utworzonych obrazów do rejestru kontenerów. Rejestry prywatne, takie jak usługa Azure Container Registry, są obsługiwane, podobnie jak publiczne centrum platformy Docker.
* [`cmd`](container-registry-tasks-reference-yaml.md#cmd): Uruchom kontener, tak aby mógł działać jako funkcja w kontekście uruchomionego zadania. Można przekazać parametry do kontenera `[ENTRYPOINT]`i określić właściwości, takie jak env, odłączyć i inne znane `docker run` parametry. Typ `cmd` kroku umożliwia testowanie jednostki i funkcjonalne, z równoczesnym wykonaniem kontenera.

Poniższe fragmenty kodu pokazują, jak połączyć te typy kroków zadań. Zadania wieloetapowe mogą być tak proste, jak tworzenie pojedynczego obrazu z pliku Dockerfile i wypychanie do rejestru, z plikiem YAML podobnym do:

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID .
  - push: ["$Registry/hello-world:$ID"]
```

Lub bardziej złożone, takie jak ta fikcyjna definicja wieloetapowa, która zawiera kroki kompilacji, testowania, pakietu helm i wdrażania helm (nie pokazano rejestru kontenerów i konfiguracji repozytorium Helma):

```yml
version: v1.1.0
steps:
  - id: build-web
    build: -t $Registry/hello-world:$ID .
    when: ["-"]
  - id: build-tests
    build -t $Registry/hello-world-tests ./funcTests
    when: ["-"]
  - id: push
    push: ["$Registry/helloworld:$ID"]
    when: ["build-web", "build-tests"]
  - id: hello-world-web
    cmd: $Registry/helloworld:$ID
  - id: funcTests
    cmd: $Registry/helloworld:$ID
    env: ["host=helloworld:80"]
  - cmd: $Registry/functions/helm package --app-version $ID -d ./helm ./helm/helloworld/
  - cmd: $Registry/functions/helm upgrade helloworld ./helm/helloworld/ --reuse-values --set helloworld.image=$Registry/helloworld:$ID
```

Zobacz [przykłady zadań](container-registry-tasks-samples.md) dla wieloetapowych zadań YAML plików i dockerfiles dla kilku scenariuszy.

## <a name="run-a-sample-task"></a>Uruchamianie przykładowego zadania

Zadania obsługują zarówno ręczne wykonywanie, nazywane "szybkim uruchomieniem", jak i automatyczne wykonywanie zatwierdzania git lub aktualizacji obrazu podstawowego.

Aby uruchomić zadanie, należy najpierw zdefiniować kroki zadania w pliku YAML, a następnie wykonać polecenie interfejsu wiersza polecenia azure [az acr run][az-acr-run].

Oto przykładowe polecenie interfejsu wiersza polecenia platformy Azure, które uruchamia zadanie przy użyciu przykładowego pliku YAML zadania. Jego kroki kompilacji, a następnie wypchnąć obraz. Przed `\<acrName\>` uruchomieniem polecenia należy zaktualizować nazwę własnego rejestru kontenerów platformy Azure.

```azurecli
az acr run --registry <acrName> -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

Po uruchomieniu zadania dane wyjściowe powinny wyświetlać postęp każdego kroku zdefiniowanego w pliku YAML. Na poniższym wyjściu kroki `acb_step_0` `acb_step_1`są wyświetlane jako i .

```azurecli
az acr run --registry myregistry -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

```output
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

Aby uzyskać więcej informacji na temat automatycznych kompilacji na zatwierdzanie Git lub aktualizacji obrazu podstawowego, zobacz [Automatyzacja kompilacji obrazów i](container-registry-tutorial-build-task.md) aktualizacja obrazu podstawowego tworzy artykuły [samouczka.](container-registry-tutorial-base-image-update.md)

## <a name="next-steps"></a>Następne kroki

Wieloetapowe odwołanie do zadań i przykłady można znaleźć tutaj:

* [Odwołanie do zadania](container-registry-tasks-reference-yaml.md) — typy kroków zadań, ich właściwości i użycie.
* [Przykłady zadań](container-registry-tasks-samples.md) `task.yaml` — przykładowe i docker plików dla kilku scenariuszy, proste do złożonych.
* [Repozytorium Cmd](https://github.com/AzureCR/cmd) — kolekcja kontenerów jako poleceń dla zadań ACR.

<!-- IMAGES -->

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[task-examples]: https://github.com/Azure-Samples/acr-tasks
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task]: /cli/azure/acr/task
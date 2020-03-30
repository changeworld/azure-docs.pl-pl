---
title: Przykłady zadań usługi ACR
description: Przykładowe zadania rejestru kontenerów platformy Azure (zadania ACR) do tworzenia, uruchamiania i poprawiania obrazów kontenerów
ms.topic: article
ms.date: 11/14/2019
ms.openlocfilehash: 49df3bf565052a729ac3c587bd2ba11a299d05f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74456084"
---
# <a name="acr-tasks-samples"></a>Przykłady zadań usługi ACR

W tym artykule zawiera łącza do przykładowych `task.yaml` plików i skojarzonych plików Dockerfiles dla kilku [scenariuszy zadań rejestru kontenerów platformy Azure](container-registry-tasks-overview.md) (zadania ACR). 

Aby uzyskać dodatkowe przykłady, zobacz repozytorium [przykładów platformy Azure.][task-examples]

## <a name="scenarios"></a>Scenariusze

* **Tworzenie obrazu** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Uruchamianie kontenera** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml)

* **Tworzenie i wypychanie obrazu** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Tworzenie i uruchamianie obrazu** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Tworzenie i wypychanie wielu obrazów** -  [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world-multi.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Tworzenie i testowanie obrazów równolegle** -  [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Tworzenie i wypychanie obrazów do wielu rejestrów** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/testtask.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/hello-world.dockerfile)


## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o zadaniach ACR:

* [Zadania wieloetapowe](container-registry-tasks-multi-step.md) — oparte na zadaniach ACR do tworzenia, testowania i poprawiania obrazów kontenerów w chmurze.
* [Odwołanie do zadania](container-registry-tasks-reference-yaml.md) — typy kroków zadań, ich właściwości i użycie.
* [Repozytorium Cmd](https://github.com/AzureCR/cmd) — kolekcja kontenerów jako poleceń dla zadań usługi ACR.


<!-- LINKS - External -->
[task-examples]: https://github.com/Azure-Samples/acr-tasks

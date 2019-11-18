---
title: Przykłady zadań Azure Container Registry
description: Przykładowe zadania Azure Container Registry (zadania ACR) do kompilowania, uruchamiania i poprawiania obrazów kontenerów
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 11/14/2019
ms.author: danlep
ms.openlocfilehash: 488e13ed0d1961fbafad545057accb61957a7005
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152855"
---
# <a name="acr-tasks-samples"></a>Przykłady zadań ACR

W tym artykule przedstawiono przykładowe `task.yaml` pliki i powiązane wieloetapowe dockerfile dla kilku scenariuszy [Azure Container Registry zadań](container-registry-tasks-overview.md) (zadań ACR). 

Aby uzyskać więcej przykładów, zobacz repozytorium [przykładów platformy Azure][task-examples] .

## <a name="scenarios"></a>Scenariusze

* **Kompilacja obrazu** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml), [pliku dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Uruchom kontener** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml)

* **Kompilacja i wypychanie obrazu** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml), [pliku dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Kompilowanie i uruchamianie obrazu** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml), [pliku dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Kompilowanie i wypychanie wielu obrazów** -  [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world-multi.yaml), [pliku dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Twórz i Testuj obrazy w równoległych** -  [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml), [pliku dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Kompilowanie i wypychanie obrazów do wielu rejestrów** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/testtask.yaml), [pliku dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/hello-world.dockerfile)


## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o zadaniach ACR:

* [Zadania wieloetapowe](container-registry-tasks-multi-step.md) — ACR przepływy pracy oparte na zadaniach służące do kompilowania, testowania i poprawiania obrazów kontenerów w chmurze.
* [Odwołanie do zadania](container-registry-tasks-reference-yaml.md) — typy etapów zadań, ich właściwości i użycie.
* [Polecenie cmd repozytorium](https://github.com/AzureCR/cmd) — kolekcja kontenerów jako poleceń dla zadań ACR.


<!-- LINKS - External -->
[task-examples]: https://github.com/Azure-Samples/acr-tasks

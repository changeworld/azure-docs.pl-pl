---
title: Automatyzowanie systemu operacyjnego i framework poprawek za pomocą usługi Azure Container rejestru Build (kompilacja ACR)
description: Wprowadzenie do pakietu ACR Build zestaw funkcji w usłudze Azure Container Registry, która zapewnia bezpieczny, automatyczne tworzenie obrazu kontenera i instalowanie poprawek w chmurze.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 05/01/2018
ms.author: marsma
ms.openlocfilehash: 3ef91270bceb5865bdbdf9c436e4519595a3dc09
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38582634"
---
# <a name="automate-os-and-framework-patching-with-acr-build"></a>Automatyzowanie systemu operacyjnego i framework poprawek za pomocą pakietu ACR Build

Kontenery zapewniają na nowym poziomie wirtualizacji, izolowanie zależności aplikacji i dla deweloperów z infrastrukturą i wymagań operacyjnych. Co jeszcze pozostało, jest jednak trzeba rozwiązać, jak ta Wirtualizacja aplikacji jest zastosowana poprawka.

**Tworzenie rejestru Azure container Registry**, zestaw funkcji w usłudze Azure Container Registry, zapewnia nie tylko kontenera natywne możliwości obrazu kompilacji są dostępne, ale jest również Automatyzowanie [systemu operacyjnego i stosowanie poprawek w ramach](#automate-os-and-framework-patching) dla kontenerów Docker.

[!INCLUDE [container-registry-build-preview-note](../../includes/container-registry-build-preview-note.md)]

## <a name="what-is-acr-build"></a>Co to jest pakietu ACR Build?

Azure tworzenia rejestru kontenera jest usługi kompilacji obrazu kontenera platformy Azure — natywne. Tworzenie rejestru Azure container Registry umożliwia wewnętrznej pętli tworzenia kodu w chmurze przy użyciu obrazu kontenera na żądanie kompilacji i zaktualizuj zautomatyzowane kompilacje na obraz zatwierdzenia i bazy kodu źródłowego.

Obraz kontenera wyzwalacza opiera się automatycznie, gdy kod jest zaangażowana w repozytorium Git lub po zaktualizowaniu kontenera obraz podstawowy. Przy użyciu wyzwalaczy aktualizacji obraz podstawowy, można zautomatyzować, system operacyjny i platforma aplikacji poprawek przepływu pracy, utrzymywanie bezpiecznych środowiskach przestrzegając podmiotów niezmienne kontenerów.

## <a name="quick-build-inner-loop-extended-to-the-cloud"></a>Szybka kompilacja: wewnętrzny zdefiniowana pętla rozszerzony do chmury

Początek zarządzania cyklem życia rozpoczyna się przed deweloperów zatwierdzić swoje pierwsze wiersze kodu. Tworzenie rejestru Azure container Registry [szybkie tworzenie](container-registry-tutorial-quick-build.md) funkcji zapewnia środowisko zintegrowane lokalnego wewnętrznej pętli tworzenia kodu, Odciążanie kompilacji na platformie Azure. Za pomocą szybkiego kompilacji można sprawdzić, definicje kompilacji automatycznych przed zatwierdzeniem kodu.

Przy użyciu znanej `docker build` formacie [az acr build] [ az-acr-build] polecenie w interfejsie wiersza polecenia platformy Azure pobierającej kontekst lokalnych, wysyła je do usługi kompilacji usługi ACR i domyślnie wypycha utworzony obraz do jego rejestru po ukończenie. Kompilacji usługi ACR jest zgodna z rejestrów z replikacją geograficzną, umożliwiając zespołom programistycznym rozproszonych korzystać z najbliższego rejestru replikowanego. W trakcie okresu zapoznawczego kompilacji z rejestru Azure container Registry jest dostępna w regionach wschodnie stany USA i Europa Zachodnia.

Tworzenie rejestru Azure container Registry została zaprojektowana jako cyklu życia kontenera pierwotnych. Na przykład zintegrować pakietu ACR Build rozwiązanie ciągłej integracji/ciągłego wdrażania. Wykonując [az login] [ az-login] z [nazwy głównej usługi][az-login-service-principal], następnie wydać rozwiązanie ciągłej integracji/ciągłego Dostarczania [az acr kompilacji] [ az-acr-build] polecenia, aby uruchamiał obrazu kompilacji.

Dowiedz się, jak użyć szybkiego kompilacji w pierwszym samouczku pakietu ACR Build [kompilowanie obrazów kontenerów w chmurze za pomocą usługi Azure Container Registry Build](container-registry-tutorial-quick-build.md).

## <a name="automatic-build-on-source-code-commit"></a>Automatyczna kompilacja przy zatwierdzeniu kodu źródłowego

Użyj pakietu ACR Build automatycznie wyzwalać obraz kontenera kompilacji, gdy kod jest zaangażowana w repozytorium Git. Tworzenie zadań, można skonfigurować przy użyciu polecenia interfejsu wiersza polecenia Azure [zadanie kompilacji az acr][az-acr-build-task], pozwalają na określenie repozytorium Git i opcjonalnie gałęzi i pliku Dockerfile. Jeśli Twój zespół zatwierdzenia kodu do repozytorium, pakietu ACR Build utworzony element webhook wyzwala kompilację obrazu kontenera, zdefiniowane w repozytorium.

Dowiedz się, jak wyzwolić kompilacje przy zatwierdzeniu kodu źródłowego w drugim samouczku pakietu ACR Build [obraz kontenera Automatyzacja kompilacji za pomocą usługi Azure Container Registry Build](container-registry-tutorial-build-task.md).

## <a name="automate-os-and-framework-patching"></a>Automatyzowanie systemu operacyjnego i stosowanie poprawek framework

Możliwości pakietu ACR Build naprawdę rozszerzanie potoku kompilacji kontenerów pochodzi z jego możliwości, aby wykrywać aktualizację obrazu podstawowego. Wypchnięcie zaktualizowanego obrazu do rejestru kompilacji usługi ACR automatycznie tworzyć żadnych obrazów aplikacji na jego podstawie.

Obrazy kontenerów można ogólnie podzielić na *podstawowy* obrazów i *aplikacji* obrazów. Twoje obrazy podstawowe zwykle obejmują systemu operacyjnego i struktur aplikacji, od których aplikacja jest wbudowana, wraz z innych dostosowań. Te obrazy podstawowe są zazwyczaj na podstawie publicznego obrazów nadrzędnego, na przykład [Alpine Linux] [ base-alpine] lub [Node.js][base-node]. Kilka obrazów aplikacji może udostępniać typowe obrazu podstawowego.

Po zaktualizowaniu framework obrazu systemu operacyjnego lub aplikacji przez nadrzędne Element utrzymujący na przykład za pomocą krytyczne poprawki zabezpieczeń systemu operacyjnego, należy również zaktualizować swoje obrazy podstawowe obejmujący poprawki krytyczne. Każdy obraz aplikacji należy następnie również zostać zrekompilowany, aby uwzględnić te poprawki nadrzędnego teraz dołączane do obrazu podstawowego.

Ponieważ dynamicznie pakietu ACR Build umożliwia odnalezienie zależności obraz podstawowy, podczas tworzenia obrazu kontenera, może to wykryć po zaktualizowaniu obrazu podstawowego obrazu aplikacji. Za pomocą jednego we wstępnie skonfigurowanym [zadania kompilacji](container-registry-tutorial-base-image-update.md#create-build-task), następnie kompilacji usługi ACR **automatycznie ponownie kompiluje każdego obrazu aplikacji** dla Ciebie. Z tym automatyczne wykrywanie i ponownie skompilować, pakietu ACR Build pozwala zaoszczędzić czas i nakład pracy zwykle jest to wymagane do ręcznego śledzenia i aktualizowanie aplikacji w każdym obrazu, odwołuje się do zaktualizowanego obrazu podstawowego.

Więcej informacji na temat systemu operacyjnego i framework poprawki w trzeci samouczek pakietu ACR Build [Automatyzacja obrazu jest oparta na aktualizacji obrazów podstawowych za pomocą usługi Azure Container Registry Build](container-registry-tutorial-base-image-update.md).

> [!NOTE]
> Wyzwalacz aktualizacji obrazu podstawowego dla początkowego okresu zapoznawczego opiera się tylko wtedy, gdy obrazy podstawowych i aplikacji znajdują się w tej samej usłudze Azure container registry.

## <a name="next-steps"></a>Kolejne kroki

Gdy wszystko będzie gotowe zautomatyzować systemu operacyjnego i framework poprawek, tworzenie obrazów kontenerów w chmurze, zapoznaj się z serii samouczków pakietu ACR Build trzyczęściowej serii.

> [!div class="nextstepaction"]
> [Tworzenie obrazów kontenera w chmurze przy użyciu usługi Azure Container Registry Build](container-registry-tutorial-quick-build.md)

<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-build-task]: /cli/azure/acr#az-acr-build-task
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli#log-in-with-a-service-principal

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png

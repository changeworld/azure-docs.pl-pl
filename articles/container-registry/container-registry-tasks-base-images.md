---
title: Aktualizacje obrazu podstawowego — zadania
description: Dowiedz się więcej o obrazach podstawowych obrazów kontenerów aplikacji i o tym, jak aktualizacja obrazu podstawowego może wyzwolić zadanie rejestru kontenerów platformy Azure.
ms.topic: article
ms.date: 01/22/2019
ms.openlocfilehash: 017c8f8a3a15896bd6e14a54136ba713e9f9c499
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617933"
---
# <a name="about-base-image-updates-for-acr-tasks"></a>Informacje o aktualizacjach obrazów podstawowych dla zadań usługi ACR

Ten artykuł zawiera podstawowe informacje na temat aktualizacji obrazu podstawowego aplikacji i jak te aktualizacje mogą wyzwalać zadanie rejestru kontenerów platformy Azure.

## <a name="what-are-base-images"></a>Co to są obrazy podstawowe?

Pliki dockerfiles definiujące większość obrazów kontenerów określają obraz nadrzędny, z którego obraz jest oparty, często nazywany *obrazem bazowym.* Obrazy podstawowe przeważnie zawierają system operacyjny, na przykład [Alpine Linux][base-alpine] lub [Windows Nano Server][base-windows], w którym są stosowane pozostałe warstwy kontenera. Mogą również zawierać platformy aplikacji, takie jak [Node.js][base-node] lub [.NET Core][base-dotnet]. Te obrazy podstawowe są zazwyczaj oparte na publicznych obrazów nadrzędnych. Kilka obrazów aplikacji może współużytkować wspólny obraz podstawowy.

Obraz podstawowy jest często aktualizowany przez element utrzymujący obrazu w celu uwzględnienia nowych funkcji lub ulepszeń systemu operacyjnego lub platformy w obrazie. Poprawki zabezpieczeń są kolejną typową przyczyną aktualizacji obrazu podstawowego. Po tych aktualizacji nadrzędnych występują, należy również zaktualizować obrazy podstawowe, aby uwzględnić poprawkę krytyczną. Każdy obraz aplikacji musi następnie również zostać przebudowany, aby uwzględnić te poprawki nadrzędne, które są teraz zawarte w obrazie podstawowym.

W niektórych przypadkach, takich jak zespół rozwoju prywatnego, obraz podstawowy może określić więcej niż system operacyjny lub struktury. Na przykład obraz podstawowy może być obrazem składnika usługi udostępnionej, który musi być śledzony. Członkowie zespołu może być konieczne śledzenie tego obrazu podstawowego do testowania lub trzeba regularnie aktualizować obraz podczas tworzenia obrazów aplikacji.

## <a name="track-base-image-updates"></a>Śledzenie aktualizacji obrazów bazowych

Usługa ACR Tasks oferuje możliwość automatycznego kompilowania obrazów podczas aktualizacji obrazu podstawowego kontenera.

Zadania usługi ACR dynamicznie odnajduje zależności obrazu podstawowego podczas tworzenia obrazu kontenera. W rezultacie może wykryć, kiedy obraz podstawowy aplikacji jest aktualizowany. Za pomocą jednego wstępnie skonfigurowanego zadania kompilacji zadania usługi ACR mogą automatycznie odbudować każdy obraz aplikacji, który odwołuje się do obrazu podstawowego. Dzięki temu automatycznemu wykrywaniu i przebudowie funkcja ACR Tasks pozwala zaoszczędzić czas i wysiłek wymagany do ręcznego śledzenia i aktualizowania każdego obrazu aplikacji odwołującego się do zaktualizowanego obrazu podstawowego.

## <a name="base-image-locations"></a>Lokalizacje obrazów bazowych

W przypadku kompilacji obrazów z pliku Dockerfile zadanie usługi ACR wykrywa zależności od obrazów podstawowych w następujących lokalizacjach:

* Ten sam rejestr kontenerów platformy Azure, w którym jest uruchamiane zadanie
* Inny prywatny rejestr kontenerów platformy Azure w tym samym lub innym regionie 
* Publiczne repozytorium w centrum platformy Docker 
* Publiczne repozytorium w rejestrze kontenerów firmy Microsoft

Jeśli obraz podstawowy określony `FROM` w instrukcji znajduje się w jednej z tych lokalizacji, zadanie ACR dodaje hak, aby upewnić się, że obraz jest przebudowywany w dowolnym momencie jego bazy jest aktualizowana.

## <a name="additional-considerations"></a>Dodatkowe zagadnienia

* **Obrazy podstawowe dla obrazów aplikacji** — obecnie zadanie ACR śledzi tylko aktualizacje obrazu podstawowego dla obrazów aplikacji *(runtime).* Nie śledzi aktualizacji obrazów bazowych dla obrazów pośrednich *(buildtime)* używanych w wieloetapowych plikach dockerfiles.  

* **Domyślnie włączone** — podczas tworzenia zadania ACR za pomocą polecenia [az acr tworzenie zadania,][az-acr-task-create] domyślnie zadanie jest *włączone* dla wyzwalania przez aktualizację obrazu podstawowego. Oznacza to, `base-image-trigger-enabled` że właściwość jest ustawiona na True. Jeśli chcesz wyłączyć to zachowanie w zadaniu, zaktualizuj właściwość do False. Na przykład uruchom następujące polecenie [az acr:][az-acr-task-update]

  ```azurecli
  az acr task update --myregistry --name mytask --base-image-trigger-enabled False
  ```

* **Wyzwalacz do śledzenia zależności** — Aby włączyć zadanie usługi ACR w celu określenia i śledzenia zależności obrazu kontenera — które zawierają jego obraz podstawowy — należy najpierw wyzwolić zadanie, aby utworzyć obraz **co najmniej raz.** Na przykład wyzwolić zadanie ręcznie za pomocą polecenia [az acr uruchom zadanie.][az-acr-task-run]

* **Stabilny tag dla obrazu podstawowego** — aby wyzwolić zadanie podczas *stable* aktualizacji obrazu `node:9-alpine`podstawowego, obraz podstawowy musi mieć stabilny znacznik, taki jak . To tagowanie jest typowe dla obrazu podstawowego, który jest aktualizowany za pomocą systemu operacyjnego i poprawek ramowych do najnowszej stabilnej wersji. Jeśli obraz podstawowy zostanie zaktualizowany o nowy znacznik wersji, nie wyzwoli zadania. Aby uzyskać więcej informacji na temat tagowania obrazów, zobacz [wskazówki dotyczące najlepszych praktyk](container-registry-image-tag-version.md). 

* **Inne wyzwalacze zadań** — w zadaniu wyzwalanym przez aktualizacje obrazu podstawowego można również włączyć wyzwalacze oparte na [zatwierdzeniu kodu źródłowego](container-registry-tutorial-build-task.md) lub [harmonogramie](container-registry-tasks-scheduled.md). Podstawowa aktualizacja obrazu może również wyzwolić [zadanie wieloetapowe](container-registry-tasks-multi-step.md).

## <a name="next-steps"></a>Następne kroki

Zobacz następujące samouczki dotyczące scenariuszy automatyzujących kompilacje obrazów aplikacji po zaktualizowaniu obrazu podstawowego:

* [Automatyzacja kompilacji obrazu kontenera, gdy obraz podstawowy jest aktualizowany w tym samym rejestrze](container-registry-tutorial-base-image-update.md)

* [Automatyzacja kompilacji obrazu kontenera, gdy obraz podstawowy jest aktualizowany w innym rejestrze](container-registry-tutorial-base-image-update.md)


<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-update]: /cli/azure/acr/task#az-acr-task-update
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png

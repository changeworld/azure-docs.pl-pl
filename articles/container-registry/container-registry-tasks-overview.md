---
title: Automatyzowanie systemu operacyjnego i framework poprawek za pomocą usługi Azure Container rejestru zadania (ACR)
description: Wprowadzenie do usługi ACR zadania, zestaw funkcji w usłudze Azure Container Registry, która zapewnia bezpieczny, automatyczne tworzenie obrazu kontenera i instalowanie poprawek w chmurze.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 03/28/2019
ms.author: danlep
ms.openlocfilehash: 89b48175d7707458cd92916f6b26e298163a7416
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/04/2019
ms.locfileid: "58915930"
---
# <a name="automate-os-and-framework-patching-with-acr-tasks"></a>Automatyzowanie systemu operacyjnego i framework poprawek za pomocą zadań usługi ACR

Kontenery zapewniają na nowym poziomie wirtualizacji, izolowanie zależności aplikacji i dla deweloperów z infrastrukturą i wymagań operacyjnych. Co jeszcze pozostało, jest jednak trzeba rozwiązać, jak ta Wirtualizacja aplikacji jest zastosowana poprawka.

## <a name="what-is-acr-tasks"></a>Co to jest zadania rejestru Azure container Registry?

**Zadania usługi ACR** to zestaw funkcji w usłudze Azure Container Registry. Umożliwia tworzenie obrazów kontenerów opartych na chmurze dla systemów Linux, Windows i ARM i zautomatyzować [systemu operacyjnego i stosowanie poprawek w ramach](#automate-os-and-framework-patching) dla kontenerów Docker. Zadania usługi ACR, nie tylko rozszerza cykl rozwoju "wewnętrznego obiegu" w chmurze, korzystając z kompilacji obrazu kontenera na żądanie, a także umożliwia zautomatyzowane kompilacje na zatwierdzenie kodu źródłowego lub gdy zostanie zaktualizowany kontener obrazu podstawowego. Przy użyciu wyzwalaczy aktualizacji obraz podstawowy, można zautomatyzować, system operacyjny i platforma aplikacji poprawek przepływu pracy, utrzymywanie bezpiecznych środowiskach przestrzegając podmiotów niezmienne kontenerów.

Tworzenie i testowanie obrazów kontenera z rejestru Azure container Registry zadaniami na cztery sposoby:

* [Szybkich zadań](#quick-task): Skompiluj i Wypchnij kontenera obrazów na żądanie i na platformie Azure, bez konieczności instalacji lokalnej aparat platformy Docker. Pomyśl `docker build`, `docker push` w chmurze. Kompilacja z lokalnego kodu źródłowego lub repozytorium Git.
* [Tworzenie przy zatwierdzeniu kodu źródłowego](#automatic-build-on-source-code-commit): Wyzwól kompilację obrazu kontenera automatycznie, gdy kod jest zaangażowana w repozytorium Git.
* [Rozbuduj aktualizacji obrazów podstawowych](#automate-os-and-framework-patching): Wyzwól kompilację obrazu kontenera, po zaktualizowaniu obrazu podstawowego tego obrazu.
* [Zadania wieloetapowe](#multi-step-tasks): Zdefiniuj zadania wieloetapowe, kompilowanie obrazów, uruchamiaj kontenery jako polecenia i wypychanie obrazów do rejestru. Ta funkcja zadań rejestru Azure container Registry obsługuje wykonywanie zadań na żądanie i budowania obrazu równoległych, testowanie i operacje wypychania.

## <a name="quick-task"></a>Szybkich zadań

Cykl wewnętrznej pętli tworzenia kodu, proces iteracyjny pisanie kodu, tworzenie i testowanie aplikacji przed zatwierdzeniem do kontroli źródła jest w rzeczywistości początku zarządzania cyklem życia kontenera.

Przed przekazaniem pierwszy wiersz kodu, zadania ACR [szybkich zadań](container-registry-tutorial-quick-task.md) funkcja zapewnia zintegrowane środowisko programistyczne dzięki przeniesieniu obraz kontenera opiera się na platformie Azure. Za pomocą szybkich zadań można sprawdzić Twoimi zautomatyzowanymi definicje kompilacji i catch potencjalnych problemów przed zatwierdzeniem kodu.

Przy użyciu znanej `docker build` formacie [az acr build] [ az-acr-build] polecenie w interfejsie wiersza polecenia platformy Azure wymaga *kontekstu* (zestawu plików do kompilacji), wysyła zadania usługi ACR i domyślnie wypycha utworzony obraz do rejestru, jej po zakończeniu.

Aby zapoznać się z wprowadzeniem, zobacz Przewodnik Szybki start dotyczący [kompilowanie i uruchamianie obrazów kontenera](container-registry-quickstart-task-cli.md) w usłudze Azure Container Registry.  

W poniższej tabeli przedstawiono kilka przykładów kontekstu obsługiwane lokalizacje dla zadania usługi ACR:

| Lokalizacja kontekstu | Opis | Przykład |
| ---------------- | ----------- | ------- |
| Lokalny system plików | Pliki w katalogu w lokalnym systemie plików. | `/home/user/projects/myapp` |
| Gałąź główna w witrynie GitHub | Pliki znajdujące się w głównym (lub inne domyślne) gałęzi z repozytorium GitHub.  | `https://github.com/gituser/myapp-repo.git` |
| Gałąź usługi GitHub | Konkretne Rozgałęzienie repozytorium GitHub.| `https://github.com/gituser/myapp-repo.git#mybranch` |
| Żądania Ściągnięcia w serwisie GitHub | Żądania ściągnięcia w repozytorium GitHub. | `https://github.com/gituser/myapp-repo.git#pull/23/head` |
| Podfolder usługi GitHub | Pliki w podfolderze w repozytorium GitHub. Przykład pokazuje kombinacji żądania Ściągnięcia oraz w podfolderze specyfikacji. | `https://github.com/gituser/myapp-repo.git#pull/24/head:myfolder` |
| Zdalnego pliku tar | Pliki skompresowane archiwum na zdalnym serwerze sieci Web. | `http://remoteserver/myapp.tar.gz` |

Rejestru Azure container Registry zadania został zaprojektowany jako cyklu życia kontenera pierwotnych. Na przykład można zintegrować ACR zadania do rozwiązania ciągłej integracji/ciągłego wdrażania. Wykonując [az login] [ az-login] z [nazwy głównej usługi][az-login-service-principal], następnie wydać rozwiązanie ciągłej integracji/ciągłego Dostarczania [az acr kompilacji] [ az-acr-build] polecenia, aby uruchamiał obrazu kompilacji.

Dowiedz się, jak używać szybkich zadań w pierwszym samouczku zadania ACR [kompilowanie obrazów kontenerów w chmurze za pomocą zadań rejestru kontenera platformy Azure](container-registry-tutorial-quick-task.md).

## <a name="automatic-build-on-source-code-commit"></a>Automatyczna kompilacja przy zatwierdzeniu kodu źródłowego

Użyj zadania ACR automatycznie wyzwalać obraz kontenera kompilacji, gdy kod jest zaangażowana w repozytorium Git. Tworzenie zadań, można skonfigurować przy użyciu polecenia interfejsu wiersza polecenia Azure [az acr zadań][az-acr-task], pozwalają na określenie repozytorium Git i opcjonalnie gałęzi i pliku Dockerfile. Jeśli Twój zespół zatwierdzenia kodu do repozytorium, zadania rejestru Azure container Registry utworzone elementu webhook wyzwala kompilację obrazu kontenera, zdefiniowane w repozytorium.

> [!IMPORTANT]
> Jeśli wcześniej utworzono zadania za pomocą wersji zapoznawczej przy użyciu polecenia `az acr build-task`, trzeba je utworzyć ponownie przy użyciu polecenia [az acr task][az-acr-task].

Dowiedz się, jak wyzwolić kompilacje przy zatwierdzeniu kodu źródłowego w drugim samouczku zadania ACR [obraz kontenera Automatyzacja kompilacji z zadaniami rejestru kontenera platformy Azure](container-registry-tutorial-build-task.md).

## <a name="automate-os-and-framework-patching"></a>Automatyzowanie systemu operacyjnego i stosowanie poprawek framework

Możliwości zadania rejestru Azure container Registry, aby naprawdę zwiększenia pracy kompilacji kontenera pochodzi z jego możliwości, aby wykrywać aktualizację obrazu podstawowego. Wypchnięcie zaktualizowanego obrazu do rejestru ACR zadania automatycznie tworzyć żadnych obrazów aplikacji na jego podstawie.

Obrazy kontenerów można ogólnie podzielić na *podstawowy* obrazów i *aplikacji* obrazów. Twoje obrazy podstawowe zwykle obejmują systemu operacyjnego i struktur aplikacji, od których aplikacja jest wbudowana, wraz z innych dostosowań. Te obrazy podstawowe są zazwyczaj na podstawie publicznego obrazów nadrzędnego, na przykład: [Firma Alpine Linux][base-alpine], [Windows][base-windows], [.NET][base-dotnet], lub [środowiska Node.js ][base-node]. Kilka obrazów aplikacji może udostępniać typowe obrazu podstawowego.

Po zaktualizowaniu framework obrazu systemu operacyjnego lub aplikacji przez nadrzędne Element utrzymujący na przykład za pomocą krytyczne poprawki zabezpieczeń systemu operacyjnego, należy również zaktualizować swoje obrazy podstawowe obejmujący poprawki krytyczne. Każdy obraz aplikacji należy następnie również zostać zrekompilowany, aby uwzględnić te poprawki nadrzędnego teraz dołączane do obrazu podstawowego.

Ponieważ zadania rejestru Azure container Registry umożliwia odnalezienie zależności obrazu podstawowego dynamicznie, podczas tworzenia obrazu kontenera, może to wykryć po zaktualizowaniu obrazu podstawowego obrazu aplikacji. Za pomocą jednego we wstępnie skonfigurowanym [zadania kompilacji](container-registry-tutorial-base-image-update.md#create-a-task), następnie zadania ACR **automatycznie ponownie kompiluje każdego obrazu aplikacji** dla Ciebie. Z tego automatyczne wykrywanie i ponownie skompilować, zapisuje zadania usługi ACR, czas i nakład pracy zwykle jest to wymagane do ręcznego śledzenia i aktualizowanie aplikacji w każdym obrazu, odwołuje się do zaktualizowanego obrazu podstawowego.

Więcej informacji na temat systemu operacyjnego i framework poprawki w to trzeci samouczek zadania ACR [Automatyzacja obrazu jest oparta na aktualizacji obrazów podstawowych zadań rejestru kontenera platformy Azure](container-registry-tutorial-base-image-update.md).

> [!NOTE]
> Obraz podstawowy aktualizuje wyzwalacz kompilacji tylko wtedy, gdy zarówno obrazy podstawowych i aplikacji znajdują się w tej samej usłudze Azure container registry lub bazie znajduje się w publicznym repozytorium usługi Docker Hub.

## <a name="multi-step-tasks"></a>Zadania wieloetapowe

Zadania wieloetapowe zapewniają definicji zadań na podstawie kroku i wykonywania dla tworzenia, testowania i poprawianie obrazów kontenerów w chmurze. Kroki zadań definiują pojedyncze operacje tworzenia i wypychania obrazu kontenera. Mogą one również definiować wykonanie jednego lub kilku kontenerów, z każdym krokiem używającym kontenera jako jego środowiska wykonawczego.

Na przykład można utworzyć zadania wieloetapowe, który automatyzuje następujące czynności:

1. Tworzenie obrazu aplikacji sieci web
1. Uruchamianie kontenera aplikacji internetowej
1. Zbuduj obraz testów aplikacji sieci web
1. Uruchom kontener testu aplikacji sieci web, która wykonuje testy względem uruchomionych aplikacji kontenera
1. Jeśli testy zostaną wykryte, utworzyć pakiet archiwum wykresu Helm
1. Wykonaj `helm upgrade` przy użyciu nowego pakietu archiwum wykresu Helm

Zadania wieloetapowe umożliwiają dzielenie tworzenie, uruchamianie i testowanie obrazu do bardziej konfigurowalna kroków, z obsługą zależności między kroku. Dzięki wieloetapowego zadania w zadaniach usługi ACR masz bardziej precyzyjną kontrolę nad obrazu budowania, testowania i systemu operacyjnego i framework poprawek przepływów pracy.

Dowiedz się więcej o wieloetapowego zadania w [uruchamianie wieloetapowych kompilacji, testów i zadania poprawki w zadaniach usługi ACR](container-registry-tasks-multi-step.md).

## <a name="next-steps"></a>Kolejne kroki

Gdy wszystko będzie gotowe zautomatyzować systemu operacyjnego i framework poprawek, tworzenie obrazów kontenerów w chmurze, zapoznaj się z częścią trzyczęściowej serii samouczków zadania usługi ACR.

> [!div class="nextstepaction"]
> [Kompilowanie obrazów kontenerów w chmurze za pomocą zadań rejestru kontenera platformy Azure](container-registry-tutorial-quick-task.md)

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
[az-acr-task]: /cli/azure/acr
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png

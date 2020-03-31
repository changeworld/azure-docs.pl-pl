---
title: Omówienie zadań usługi ACR
description: Wprowadzenie do zadań usługi ACR, zestaw funkcji w usłudze Azure Container Registry, który zapewnia bezpieczne, automatyczne tworzenie obrazów kontenera, zarządzanie i poprawki w chmurze.
ms.topic: article
ms.date: 01/22/2020
ms.openlocfilehash: 4fda57c1d7c866f2e6f72b04d75e53f91e995baf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087285"
---
# <a name="automate-container-image-builds-and-maintenance-with-acr-tasks"></a>Automatyzacja kompilacji i konserwacji obrazów kontenerów za pomocą zadań ACR

Kontenery zapewniają nowe poziomy wirtualizacji, izolowanie zależności aplikacji i deweloperów od infrastruktury i wymagań operacyjnych. Pozostaje jednak potrzeba rozwiązania sposobu zarządzania wirtualizacją tej aplikacji i instalowania poprawek w cyklu życia kontenera.

## <a name="what-is-acr-tasks"></a>Co to są zadania ACR?

**Zadania usługi ACR** to zestaw funkcji w rejestrze kontenerów platformy Azure. Zapewnia tworzenie obrazów kontenerów w chmurze dla [platform,](#image-platforms) w tym Linux, Windows i ARM, i może zautomatyzować [instalowanie poprawek systemu operacyjnego i struktury](#automate-os-and-framework-patching) dla kontenerów platformy Docker. Zadania usługi ACR nie tylko rozszerza cykl programowania "inner-loop" do chmury za pomocą kompilacji obrazów kontenerów na żądanie, ale także umożliwia automatyczne kompilacje wyzwalane przez aktualizacje kodu źródłowego, aktualizacje obrazu podstawowego kontenera lub liczniki czasu. Na przykład za pomocą wyzwalaczy aktualizacji obrazu podstawowego można zautomatyzować przepływ pracy poprawek systemu operacyjnego i struktury aplikacji, zachowując bezpieczne środowiska przy jednoczesnym przestrzeganiu zasad niezmiennych kontenerów.

## <a name="task-scenarios"></a>Scenariusze zadań

Zadania usługi ACR obsługuje kilka scenariuszy do tworzenia i obsługi obrazów kontenerów i innych artefaktów. Zobacz następujące sekcje w tym artykule, aby uzyskać szczegółowe informacje.

* **[Szybkie zadanie](#quick-task)** — tworzenie i wypychanie pojedynczego obrazu kontenera do rejestru kontenerów na żądanie, na platformie Azure, bez konieczności instalacji lokalnego aparatu platformy Docker. Pomyśl `docker build` `docker push` , w chmurze.
* **Automatycznie wyzwalane zadania** — włącz jeden lub więcej *wyzwalaczy,* aby utworzyć obraz:
  * **[Wyzwalacz w aktualizacji kodu źródłowego](#trigger-task-on-source-code-update)** 
  * **[Wyzwalacz w aktualizacji obrazu podstawowego](#automate-os-and-framework-patching)** 
  * **[Wyzwalacz zgodnie z harmonogramem](#schedule-a-task)** 
* **[Wieloetapowe zadanie](#multi-step-tasks)** — rozszerzanie możliwości tworzenia i wypychania pojedynczego obrazu zadań usługi ACR za pomocą wieloetapowych przepływów pracy opartych na wielu kontenerach. 

Każde zadanie usługi ACR ma skojarzony [kontekst kodu źródłowego](#context-locations) — lokalizację zestawu plików źródłowych używanych do tworzenia obrazu kontenera lub innego artefaktu. Przykładowe konteksty obejmują repozytorium Git lub lokalny system plików.

Zadania mogą również korzystać ze [zmiennych uruchamiania,](container-registry-tasks-reference-yaml.md#run-variables)dzięki czemu można ponownie użyć definicji zadań i standaryzować znaczniki obrazów i artefaktów.

## <a name="quick-task"></a>Szybkie zadanie

Cykl rozwoju pętli wewnętrznej, iteracyjny proces pisania kodu, tworzenia i testowania aplikacji przed zatwierdzeniem kontroli źródła, jest naprawdę początkiem zarządzania cyklem życia kontenera.

Przed zatwierdzeniem pierwszego wiersza kodu funkcja [szybkiego zadania](container-registry-tutorial-quick-task.md) usługi ACR Tasks może zapewnić zintegrowane środowisko programistyczne, odciążając kompilacje obrazów kontenera na platformie Azure. Za pomocą szybkich zadań można zweryfikować definicje kompilacji zautomatyzowanej i złapać potencjalne problemy przed zatwierdzeniem kodu.

Przy użyciu `docker build` znanego formatu polecenie [kompilacji az acr][az-acr-build] w usłudze Azure CLI przyjmuje [kontekst](#context-locations) (zestaw plików do kompilacji), wysyła go zadania ACR i domyślnie wypycha wbudowany obraz do rejestru po zakończeniu.

Aby uzyskać wprowadzenie, zobacz szybki start do [tworzenia i uruchamiania obrazu kontenera](container-registry-quickstart-task-cli.md) w rejestrze kontenerów platformy Azure.  

Zadania usługi ACR jest zaprojektowany jako pierwotny cykl życia kontenera. Na przykład zintegruj zadania ACR z rozwiązaniem ciągłej integracji/ciągłego wdrażania. Wykonując [az login][az-login] z [jednostką usługi,][az-login-service-principal]rozwiązanie CI/CD może następnie wydać [polecenia kompilacji az acr,][az-acr-build] aby rozpocząć kompilacje obrazów.

Dowiedz się, jak korzystać z szybkich zadań w pierwszym samouczku zadań usługi ACR, [tworzenie obrazów kontenerów w chmurze za pomocą zadań rejestru kontenerów platformy Azure.](container-registry-tutorial-quick-task.md)

> [!TIP]
> Jeśli chcesz skompilować i wypchnąć obraz bezpośrednio z kodu źródłowego, bez dockerfile, usługa Azure Container Registry udostępnia polecenie [kompilacji az acr pack][az-acr-pack-build] (wersja zapoznawcza). To narzędzie tworzy i wypycha obraz z kodu źródłowego aplikacji przy użyciu [natywnych pakietów kompilacji w chmurze.](https://buildpacks.io/)

## <a name="trigger-task-on-source-code-update"></a>Wyzwalanie zadania przy aktualizacji kodu źródłowego

Wyzwalanie kompilacji obrazu kontenera lub wieloetapowe zadanie, gdy kod jest zatwierdzany lub żądanie ściągnięcia jest lub aktualizowane, do publicznego lub prywatnego repozytorium Git w usłudze GitHub lub Azure DevOps. Na przykład skonfiguruj zadanie kompilacji za pomocą polecenia azure cli polecenia [az acr zadanie tworzenia][az-acr-task-create] przez określenie repozytorium Git i opcjonalnie gałęzi i Dockerfile. Gdy zespół aktualizuje kod w repozytorium, element webhook utworzony przez zadania usługi ACR wyzwala kompilację obrazu kontenera zdefiniowanego w repozytorium. 

Zadania usługi ACR obsługują następujące wyzwalacze po ustawieniu repozytorium Git jako kontekstu zadania:

| Wyzwalacz | Domyślnie włączone |
| ------- | ------------------ |
| Zatwierdzenie | Tak |
| Żądanie ściągnięcia | Nie |

Aby skonfigurować wyzwalacz aktualizacji kodu źródłowego, należy podać zadanie token dostępu osobistego (PAT), aby ustawić element webhook w publicznej lub prywatnej repozytorium GitHub lub Azure DevOps.

> [!NOTE]
> Obecnie zadania usługi ACR nie obsługują wyzwalaczy żądań zatwierdzania ani ściągania w repozytoriach Usługi GitHub Enterprise.

Dowiedz się, jak wyzwolić kompilacje na podstawie zatwierdzania kodu źródłowego w drugim samouczku zadań usługi [ACR, automatyzacja kompilacji obrazów kontenerów za pomocą zadań rejestru kontenerów platformy Azure.](container-registry-tutorial-build-task.md)

## <a name="automate-os-and-framework-patching"></a>Automatyzacja łatanie systemu operacyjnego i struktury

Moc zadań ACR, aby naprawdę poprawić przepływ pracy kompilacji kontenera pochodzi z jego możliwości wykrywania aktualizacji do *obrazu podstawowego*. Funkcja większości obrazów kontenerów, obraz podstawowy jest obraz nadrzędny, na którym opiera się jeden lub więcej obrazów aplikacji. Obrazy podstawowe zazwyczaj zawierają system operacyjny, a czasami struktury aplikacji. 

Można skonfigurować zadanie ACR, aby śledzić zależność od obrazu podstawowego podczas tworzenia obrazu aplikacji. Po wypchnięciu zaktualizowanego obrazu podstawowego do rejestru lub zaktualizowaniu obrazu podstawowego w publicznym repozytorium, takim jak w centrum platformy Docker Hub, zadania usługi ACR mogą automatycznie tworzyć na jego podstawie obrazy aplikacji.
Dzięki temu automatycznemu wykrywaniu i przebudowie funkcja ACR Tasks pozwala zaoszczędzić czas i wysiłek wymagany do ręcznego śledzenia i aktualizowania każdego obrazu aplikacji odwołującego się do zaktualizowanego obrazu podstawowego.

Dowiedz się więcej o [wyzwalaczach aktualizacji obrazu podstawowego](container-registry-tasks-base-images.md) dla zadań ACR. I dowiedz się, jak wyzwolić kompilację obrazu, gdy obraz podstawowy jest wypychany do rejestru kontenerów w samouczku [Automatyzacja tworzenia obrazu kontenera, gdy obraz podstawowy jest aktualizowany w rejestrze kontenerów platformy Azure](container-registry-tutorial-base-image-update.md)

## <a name="schedule-a-task"></a>Planowanie zadania

Opcjonalnie zaplanuj zadanie, konfigurując co najmniej jeden *wyzwalacz czasomierza* podczas tworzenia lub aktualizowania zadania. Planowanie zadania jest przydatne do uruchamiania obciążeń kontenerów zgodnie ze zdefiniowanym harmonogramem lub uruchamiania operacji konserwacji lub testów obrazów wypychanych regularnie do rejestru. Aby uzyskać szczegółowe informacje, zobacz [Uruchamianie zadania usługi ACR zgodnie ze zdefiniowanym harmonogramem](container-registry-tasks-scheduled.md).

## <a name="multi-step-tasks"></a>Zadania wieloetapowe

Zadania wieloetapowe zapewniają definicję i wykonywanie zadań opartych na krokach do tworzenia, testowania i poprawiania obrazów kontenerów w chmurze. Kroki zadania zdefiniowane w [pliku YAML](container-registry-tasks-reference-yaml.md) określają poszczególne operacje kompilacji i wypychania dla obrazów kontenerów lub innych artefaktów. Mogą one również definiować wykonanie jednego lub kilku kontenerów, z każdym krokiem używającym kontenera jako jego środowiska wykonawczego.

Można na przykład utworzyć wieloetapowe zadanie, które automatyzuje następujące zadania:

1. Tworzenie obrazu aplikacji sieci Web
1. Uruchamianie kontenera aplikacji sieci web
1. Tworzenie obrazu testowego aplikacji sieci Web
1. Uruchom kontener testowy aplikacji sieci web, który wykonuje testy dla uruchomionego kontenera aplikacji
1. Jeśli testy zdają wyniki, skompiluj pakiet archiwum wykresu Helm
1. Wykonywanie `helm upgrade` przy użyciu nowego pakietu archiwum wykresu Helm

Zadania wieloetapowe umożliwiają podzielenie budynku, uruchamianie i testowanie obrazu na bardziej komponowalne kroki, z obsługą zależności między etapami. W przypadku zadań wieloetapowych w zadaniach usługi ACR masz bardziej szczegółową kontrolę nad tworzeniem obrazów, testowaniem i osem i procesami poprawek ramowych.

Dowiedz się więcej o zadaniach wieloetapowych w [obszarze Uruchamianie wieloetapowych zadań kompilacji, testowania i instalowania poprawek w zadaniach usługi ACR.](container-registry-tasks-multi-step.md)

## <a name="context-locations"></a>Lokalizacje kontekstu

W poniższej tabeli przedstawiono kilka przykładów obsługiwanych lokalizacji kontekstu dla zadań usługi ACR:

| Lokalizacja kontekstu | Opis | Przykład |
| ---------------- | ----------- | ------- |
| Lokalny system plików | Pliki w katalogu w lokalnym systemie plików. | `/home/user/projects/myapp` |
| Gałąź wzorc ów GitHub | Pliki w głównej (lub innej domyślnej) gałęzi publicznego lub prywatnego repozytorium GitHub.  | `https://github.com/gituser/myapp-repo.git` |
| Oddział GitHub | Określona gałąź publicznego lub prywatnego repozytorium GitHub.| `https://github.com/gituser/myapp-repo.git#mybranch` |
| Podfolder GitHub | Pliki w podfolderze w publicznym lub prywatnym repozytorium GitHub. Przykład pokazuje kombinację specyfikacji gałęzi i podfolderu. | `https://github.com/gituser/myapp-repo.git#mybranch:myfolder` |
| Zatwierdzanie github | Określone zatwierdzenie w publicznym lub prywatnym repozytorium GitHub. Przykład pokazuje kombinację skrótu zatwierdzania (SHA) i specyfikacji podfolderu. | `https://github.com/gituser/myapp-repo.git#git-commit-hash:myfolder` |
| Podfolder usługi Azure DevOps | Pliki w podfolderze w publicznej lub prywatnej repozytorium platformy Azure. Przykład pokazuje kombinację specyfikacji gałęzi i podfolderu. | `https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder` |
| Zdalna tarball | Pliki w skompresowanym archiwum na zdalnym serwerze internetowym. | `http://remoteserver/myapp.tar.gz` |

> [!NOTE]
> Podczas korzystania z prywatnego repozytorium Git jako kontekstu dla zadania, należy podać token dostępu osobistego (PAT).

## <a name="image-platforms"></a>Platformy obrazów

Domyślnie ACR Tasks tworzy obrazy dla systemu operacyjnego Linux i architektury amd64. Określ `--platform` znacznik do tworzenia obrazów systemu Windows lub obrazów systemu Linux dla innych architektur. Określ system operacyjny i opcjonalnie obsługiwaną architekturę w formacie `--platform Linux/arm`OS/architektura (na przykład ). W przypadku architektur ARM opcjonalnie należy określić wariant w formacie `--platform Linux/arm64/v8`OS/architecture/variant (na przykład):

| System operacyjny | Architektura|
| --- | ------- | 
| Linux | amd64<br/>arm<br/>ramię 64<br/>386 |
| Windows | amd64 |

## <a name="view-task-output"></a>Wyświetlanie danych wyjściowych zadania podrzędnego

Każde uruchomienie zadania generuje dane wyjściowe dziennika, które można sprawdzić, aby ustalić, czy kroki zadania zostały pomyślnie uruchomione. Podczas ręcznego wyzwalania zadania, dane wyjściowe dziennika dla przebiegu zadania jest przesyłany strumieniowo do konsoli, a także przechowywane do późniejszego pobierania. Gdy zadanie jest automatycznie wyzwalane, na przykład przez zatwierdzenie kodu źródłowego lub aktualizację obrazu podstawowego, dzienniki zadań są przechowywane tylko. Wyświetl dzienniki uruchamiania w witrynie Azure portal lub użyj polecenia [dzienniki zadań az acr.](/cli/azure/acr/task#az-acr-task-logs)

Zobacz więcej informacji o [przeglądaniu dzienników zadań i zarządzaniu nimi](container-registry-tasks-logs.md).

## <a name="next-steps"></a>Następne kroki

Gdy będziesz gotowy do automatyzacji kompilacji obrazów kontenerów i konserwacji w chmurze, zapoznaj się z [serią samouczków Zadania ACR](container-registry-tutorial-quick-task.md).

Opcjonalnie zainstaluj [rozszerzenie platformy Docker dla kodu programu Visual Studio](https://code.visualstudio.com/docs/azure/docker) i rozszerzenie konta platformy [Azure,](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) aby pracować z rejestrami kontenerów platformy Azure. Ściągaj i wypychaj obrazy do rejestru kontenerów platformy Azure lub uruchom zadania usługi ACR, wszystkie w ramach programu Visual Studio Code.

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
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png

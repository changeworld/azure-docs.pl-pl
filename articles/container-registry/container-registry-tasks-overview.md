---
title: Automatyzowanie kompilowania i poprawiania obrazów kontenerów za pomocą zadań Azure Container Registry (zadania ACR)
description: Wprowadzenie do ACR zadań, zestaw funkcji w Azure Container Registry, który zapewnia bezpieczną, zautomatyzowaną kompilację, zarządzanie i stosowanie poprawek w chmurze.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 06/12/2019
ms.author: danlep
ms.openlocfilehash: 2d7237c1d142e9f7bb5a47294d1375040be43ac3
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70308039"
---
# <a name="automate-container-image-builds-and-maintenance-with-acr-tasks"></a>Automatyzowanie kompilacji i konserwacji obrazów kontenerów za pomocą zadań ACR

Kontenery zapewniają nowe poziomy wirtualizacji, izolowanie zależności aplikacji i deweloperów od wymagań dotyczących infrastruktury i działania. Jest to jednak potrzeba, aby zająć się tym, w jaki sposób ta Wirtualizacja aplikacji jest zarządzana i poprawiana przez cykl życia kontenera.

## <a name="what-is-acr-tasks"></a>Co to są zadania ACR?

**ACR Tasks** to zestaw funkcji w ramach Azure Container Registry. Umożliwia tworzenie obrazów kontenerów opartych na chmurze dla systemów Linux, Windows i ARM, a także Automatyzowanie [poprawek systemu operacyjnego i struktury](#automate-os-and-framework-patching) dla kontenerów platformy Docker. ACR Tasks nie tylko rozszerzają cykl programowania "pętla wewnętrzna" do chmury z kompilacjami obrazu kontenera na żądanie, ale również włącza zautomatyzowane kompilacje dla zatwierdzenia kodu źródłowego lub po zaktualizowaniu obrazu podstawowego kontenera. Za pomocą podstawowych wyzwalaczy aktualizacji obrazu można zautomatyzować przepływ pracy w systemie operacyjnym i aplikacji, utrzymując bezpieczne środowiska, zachowując zgodność z podmiotami niezmiennymi kontenerów.

Twórz i Testuj obrazy kontenerów za pomocą zadań ACR na cztery sposoby:

* [Szybkie zadanie](#quick-task): Twórz i Wypychaj obrazy kontenerów na żądanie na platformie Azure bez konieczności instalacji lokalnego aparatu platformy Docker. Zastanów się w chmurze. `docker push` `docker build` Kompiluj z lokalnego kodu źródłowego lub repozytorium git.
* [Kompilacja przy zatwierdzaniu kodu źródłowego](#automatic-build-on-source-code-commit): Wyzwalaj kompilację obrazu kontenera automatycznie, gdy kod zostanie przekazany do repozytorium git.
* [Kompilacja w ramach aktualizacji obrazu podstawowego](#automate-os-and-framework-patching): Wyzwól kompilację obrazu kontenera, gdy obraz podstawowy tego obrazu został zaktualizowany.
* [Zadania wieloetapowe](#multi-step-tasks): Zdefiniuj zadania wieloetapowe, które umożliwiają kompilowanie obrazów, uruchamianie kontenerów jako poleceń i wypychanie obrazów do rejestru. Ta funkcja ACR zadania obsługuje wykonywanie zadań na żądanie i równoległe Kompilowanie obrazów, testowanie i wypychanie.

## <a name="quick-task"></a>Szybkie zadanie

Cykl programowania w pętli wewnętrznej, proces iteracyjny pisania kodu, kompilowania i testowania aplikacji przed zatwierdzeniem do kontroli źródła, to naprawdę początek zarządzania cyklem życia kontenera.

Przed zatwierdzeniem pierwszego wiersza kodu funkcja [zadanie szybkiego zadania](container-registry-tutorial-quick-task.md) ACR zadania może zapewnić zintegrowane środowisko programistyczne, odciążając kompilacje obrazu kontenera na platformie Azure. Za pomocą szybkich zadań można zweryfikować zautomatyzowane definicje kompilacji i wychwycić potencjalne problemy przed zatwierdzeniem kodu.

Przy użyciu znajomego `docker build` formatu polecenie [AZ ACR Build][az-acr-build] w interfejsie wiersza polecenia platformy Azure przyjmuje [kontekst](#context-locations) (zestaw plików do skompilowania), wysyła do niego zadania ACR i domyślnie wypychanie skompilowanego obrazu do rejestru po zakończeniu.

Aby zapoznać się z wprowadzeniem, zobacz Przewodnik Szybki Start dotyczący [kompilowania i uruchamiania obrazu kontenera](container-registry-quickstart-task-cli.md) w Azure Container Registry.  

Zadania ACR są zaprojektowane jako pierwotny cykl życia kontenera. Na przykład Zintegruj zadania ACR w rozwiązaniu CI/CD. Wykonanie polecenia [AZ login][az-login] z jednostką [usługi][az-login-service-principal]powoduje, że rozwiązanie Ci/CD może wydać polecenie [AZ ACR Build][az-acr-build] , aby uruchomić kompilacje obrazu.

Dowiedz się, jak używać szybkich zadań w pierwszym samouczku ACR Tasks, [tworzyć obrazy kontenerów w chmurze za pomocą zadań Azure Container Registry](container-registry-tutorial-quick-task.md).

## <a name="automatic-build-on-source-code-commit"></a>Automatyczne Kompilowanie przy zatwierdzaniu kodu źródłowego

Za pomocą zadań ACR automatycznie Wyzwól kompilację obrazu kontenera, gdy kod zostanie przekazany do repozytorium Git w serwisie GitHub lub Azure DevOps. Zadania kompilacji, konfigurowalne przy użyciu interfejsu wiersza polecenia platformy Azure [AZ ACR Task][az-acr-task], umożliwiają określenie repozytorium Git oraz opcjonalnego rozgałęzienia i pliku dockerfile. Gdy zespół zatwierdzi kod w repozytorium, ACR zadania webhook wyzwalają kompilację obrazu kontenera zdefiniowanego w repozytorium.

> [!IMPORTANT]
> Jeśli wcześniej utworzono zadania w wersji zapoznawczej przy `az acr build-task` użyciu polecenia, należy ponownie utworzyć te zadania za pomocą polecenia [AZ ACR Task][az-acr-task] .

Dowiedz się, jak wyzwolić kompilacje w ramach zatwierdzenia kodu źródłowego w drugim samouczku ACR zadania, [Automatyzuj kompilacje obrazów kontenerów za pomocą zadań Azure Container Registry](container-registry-tutorial-build-task.md).

## <a name="automate-os-and-framework-patching"></a>Automatyzowanie stosowania poprawek systemu operacyjnego i platformy

Możliwości ACR zadania, aby naprawdę ulepszyć przepływ pracy kompilacji kontenera, pochodzą z możliwości wykrywania aktualizacji obrazu podstawowego. Po wypchnięciu zaktualizowanego obrazu podstawowego do rejestru lub zaktualizowaniu obrazu podstawowego w repozytorium publicznym, takim jak w usłudze Docker Hub, ACR zadania mogą automatycznie kompilować wszystkie obrazy aplikacji na ich podstawie.

Obrazy kontenerów można w szerokim zakresie klasyfikować do obrazów *podstawowych* i obrazów *aplikacji* . Obrazy podstawowe zazwyczaj obejmują system operacyjny i platformy aplikacji, na których jest skompilowana aplikacja, oraz inne dostosowania. Te obrazy podstawowe są zwykle oparte na publicznych obrazach nadrzędnych, na przykład: [Alpine Linux][base-alpine], [Windows][base-windows], [.NET][base-dotnet]lub [Node. js][base-node]. Kilka obrazów aplikacji może współużytkować wspólny obraz podstawowy.

W przypadku aktualizowania obrazu systemu operacyjnego lub aplikacji w ramach utrzymującego się elementu przez funkcję, na przykład z krytyczną poprawką zabezpieczeń systemu operacyjnego, należy również zaktualizować obrazy podstawowe w celu uwzględnienia poprawki krytycznej. Każdy obraz aplikacji należy również ponownie skompilować, aby uwzględnić te prefiksy nadrzędne teraz zawarte w obrazie podstawowym.

Ponieważ zadania ACR dynamicznie odnajdują zależności obrazu podstawowego podczas kompilowania obrazu kontenera, może wykryć, kiedy obraz podstawowy obrazu aplikacji zostanie zaktualizowany. Po jednym wstępnie skonfigurowanym [zadaniu kompilacji](container-registry-tutorial-base-image-update.md#create-a-task)ACR zadania **automatycznie ponownie kompilują każdy obraz aplikacji** . Dzięki temu automatycznemu wykrywaniu i odbudowywaniu ACR zadania oszczędzają czas i nakłady pracy, które są zwykle wymagane do ręcznego śledzenia i aktualizowania każdego obrazu aplikacji, do którego odwołuje się zaktualizowany obraz podstawowy.

Zadanie ACR śledzi podstawową aktualizację obrazu, gdy podstawowy obraz znajduje się w jednej z następujących lokalizacji:

* Ten sam rejestr kontenerów platformy Azure, w którym uruchomiono zadanie
* Inna usługa Azure Container Registry w tym samym regionie 
* Repozytorium publiczne w usłudze Docker Hub
* Repozytorium publiczne w programie Microsoft Container Registry

Dowiedz się więcej o poprawkach systemów operacyjnych i struktur w trzecim samouczku ACR zadania, [automatyzując kompilacje obrazów w podstawowej aktualizacji obrazu przy użyciu Azure Container Registry zadań](container-registry-tutorial-base-image-update.md).

## <a name="multi-step-tasks"></a>Zadania wieloetapowe

Wieloetapowe zadania zapewniają definicje zadań oparte na krokach i wykonywanie w celu tworzenia, testowania i poprawiania obrazów kontenerów w chmurze. Kroki zadań definiują pojedyncze operacje tworzenia i wypychania obrazu kontenera. Mogą one również definiować wykonanie jednego lub kilku kontenerów, z każdym krokiem używającym kontenera jako jego środowiska wykonawczego.

Można na przykład utworzyć zadanie wieloetapowe, które automatyzuje następujące czynności:

1. Tworzenie obrazu aplikacji sieci Web
1. Uruchamianie kontenera aplikacji sieci Web
1. Kompilowanie obrazu testowego aplikacji sieci Web
1. Uruchom kontener testów aplikacji sieci Web, który wykonuje testy względem uruchomionego kontenera aplikacji
1. Jeśli testy zachodzą, Kompiluj pakiet archiwum wykresów Helm
1. `helm upgrade` Wykonaj przy użyciu nowego pakietu archiwum wykresów Helm

Zadania wieloetapowe umożliwiają podział kompilowania, uruchamiania i testowania obrazu na bardziej złożone etapy z obsługą zależności między etapami. W przypadku zadań wieloetapowych w zadaniach ACR masz bardziej szczegółową kontrolę nad tworzeniem i testowaniem obrazów oraz przepływami pracy w systemie operacyjnym i architekturą.

Więcej informacji o zadaniach wieloetapowych w [uruchamianiu wieloetapowych zadań kompilacji, testowania i poprawiania w zadaniach ACR](container-registry-tasks-multi-step.md).

## <a name="context-locations"></a>Lokalizacje kontekstu

W poniższej tabeli przedstawiono kilka przykładów obsługiwanych lokalizacji kontekstu dla zadań ACR:

| Lokalizacja kontekstu | Opis | Przykład |
| ---------------- | ----------- | ------- |
| Lokalny system plików | Pliki znajdujące się w katalogu w lokalnym systemie plików. | `/home/user/projects/myapp` |
| Gałąź główna usługi GitHub | Pliki znajdujące się w głównej (lub innej domyślnej) gałęzi repozytorium GitHub.  | `https://github.com/gituser/myapp-repo.git` |
| Gałąź GitHub | Określona gałąź repozytorium GitHub.| `https://github.com/gituser/myapp-repo.git#mybranch` |
| Podfolder usługi GitHub | Pliki znajdujące się w podfolderze repozytorium GitHub. Przykład przedstawia kombinację specyfikacji gałęzi i podfolderu. | `https://github.com/gituser/myapp-repo.git#mybranch:myfolder` |
| Plik tar zdalnego | Pliki skompresowanego Archiwum na zdalnym serwerze WebServer. | `http://remoteserver/myapp.tar.gz` |

## <a name="image-platforms"></a>Platformy obrazów

Domyślnie zadania ACR kompilują obrazy dla systemu operacyjnego Linux i architektury amd64. `--platform` Określ tag do kompilowania obrazów systemu Windows lub dla innych architektur w systemie Linux. Określ system operacyjny i opcjonalnie obsługiwaną architekturę w formacie systemu operacyjnego/architektury (na przykład `--platform Linux/arm`). W przypadku architektur ARM Opcjonalnie określ wariant w formacie OS/Architecture/Variant (na przykład `--platform Linux/arm64/v8`):

| OS | Architektura|
| --- | ------- | 
| Linux | Procesor<br/>ARM<br/>arm64<br/>386 |
| Windows | Procesor |

## <a name="view-task-logs"></a>Wyświetlanie dzienników zadań

Każde uruchomienie zadania generuje dane wyjściowe dziennika, które można sprawdzić, aby określić, czy kroki zadania zostały wykonane pomyślnie. W przypadku użycia polecenia [AZ ACR Build](/cli/azure/acr#az-acr-build), [AZ ACR Run](/cli/azure/acr#az-acr-run)lub [AZ ACR Task Run](/cli/azure/acr/task#az-acr-task-run) , aby wyzwolić zadanie, dane wyjściowe dziennika dla uruchomienia zadania są przesyłane strumieniowo do konsoli programu, a także przechowywane do późniejszego pobrania. Gdy zadanie jest automatycznie wyzwalane, na przykład przez zatwierdzenie kodu źródłowego lub aktualizację obrazu podstawowego, dzienniki zadań są przechowywane tylko. Wyświetl dzienniki dla zadania uruchomionego w Azure Portal lub użyj polecenia [AZ ACR Task Logs](/cli/azure/acr/task#az-acr-task-logs) .

Począwszy od lipca 2019, dane i dzienniki uruchamiania zadań w rejestrze będą przechowywane domyślnie przez 30 dni, a następnie automatycznie przeczyszczane. Jeśli chcesz zarchiwizować dane do uruchomienia zadania, Włącz archiwizowanie przy użyciu polecenia [AZ ACR Task Update-Run](/cli/azure/acr/task#az-acr-task-update-run) . Poniższy przykład umożliwia archiwizowanie dla zadania uruchamiania *CF11* *w rejestrze rejestru.*

```azurecli
az acr task update-run --registry myregistry --run-id cf11 --no-archive false
```

## <a name="next-steps"></a>Następne kroki

Gdy wszystko jest gotowe do zautomatyzowania poprawek systemu operacyjnego i platformy przez skompilowanie obrazów kontenera w chmurze, zapoznaj się z trzema częścią [samouczka zadań ACR](container-registry-tutorial-quick-task.md).

Opcjonalnie zainstaluj [rozszerzenie Docker dla Visual Studio Code](https://code.visualstudio.com/docs/azure/docker) i rozszerzenie [konta platformy Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) , aby współpracowało z rejestrami kontenerów platformy Azure. Ściągaj i wypychanie obrazów do usługi Azure Container Registry lub uruchamiaj zadania ACR, w Visual Studio Code.

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

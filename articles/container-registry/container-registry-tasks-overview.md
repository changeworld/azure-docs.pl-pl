---
title: Omówienie zadań usługi ACR
description: Wprowadzenie do ACR zadań, zestaw funkcji w Azure Container Registry, który zapewnia bezpieczną, zautomatyzowaną kompilację, zarządzanie i stosowanie poprawek w chmurze.
ms.topic: article
ms.date: 09/05/2019
ms.openlocfilehash: b4710591dfd78f0633d5071c78d80e300349f498
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456162"
---
# <a name="automate-container-image-builds-and-maintenance-with-acr-tasks"></a>Automatyzowanie kompilacji i konserwacji obrazów kontenerów za pomocą zadań ACR

Kontenery zapewniają nowe poziomy wirtualizacji, izolowanie zależności aplikacji i deweloperów od wymagań dotyczących infrastruktury i działania. Jest to jednak potrzeba, aby zająć się tym, w jaki sposób ta Wirtualizacja aplikacji jest zarządzana i poprawiana przez cykl życia kontenera.

## <a name="what-is-acr-tasks"></a>Co to są zadania ACR?

**ACR Tasks** to zestaw funkcji w ramach Azure Container Registry. Zapewnia to Kompilowanie obrazów kontenerów opartych na chmurze dla [platform](#image-platforms) , w tym Linux, Windows i ARM, i umożliwia automatyzację [poprawek systemu operacyjnego i struktury](#automate-os-and-framework-patching) dla kontenerów platformy Docker. ACR zadania nie tylko rozszerzają cykl programowania "pętla wewnętrzna" do chmury z kompilacjami obrazu kontenera na żądanie, ale również włącza zautomatyzowane kompilacje wyzwalane przez aktualizacje kodu źródłowego, aktualizacje obrazu podstawowego kontenera lub czasomierze. Na przykład w przypadku wyzwalaczy aktualizacji obrazu podstawowego można zautomatyzować przepływ pracy z poprawkami systemu operacyjnego i aplikacji, zachowując bezpieczne środowiska, jednocześnie przestrzegając zasad niezmiennych kontenerów.

## <a name="task-scenarios"></a>Scenariusze zadań

Zadania ACR obsługują kilka scenariuszy, które umożliwiają kompilowanie i konserwowanie obrazów kontenerów i innych artefaktów. Aby uzyskać szczegółowe informacje, zobacz następujące sekcje w tym artykule.

* **[Szybkie zadanie](#quick-task)** — Kompilowanie i wypychanie pojedynczego obrazu kontenera do rejestru kontenerów na żądanie, na platformie Azure bez konieczności instalacji lokalnego aparatu platformy Docker. `docker build`, `docker push` w chmurze.
* **Automatycznie wyzwolone zadania** — włączenie co najmniej jednego *wyzwalacza* w celu skompilowania obrazu:
  * **[Wyzwalanie aktualizacji kodu źródłowego](#trigger-task-on-source-code-update)** 
  * **[Wyzwól aktualizację obrazu podstawowego](#automate-os-and-framework-patching)** 
  * **[Wyzwalanie według harmonogramu](#schedule-a-task)** 
* **[Zadanie wieloetapowe](#multi-step-tasks)** — pozwala zwiększyć możliwości tworzenia i wypychania pojedynczego obrazu z ACR zadań z wieloetapowymi przepływami pracy opartymi na wielu kontenerach. 

Każde zadanie ACR ma skojarzony [kontekst kodu źródłowego](#context-locations) — lokalizacja zestawu plików źródłowych służącego do kompilowania obrazu kontenera lub innego artefaktu. Przykładowe konteksty obejmują repozytorium Git lub lokalny system plików.

Zadania mogą również wykorzystywać [zmienne uruchamiania](container-registry-tasks-reference-yaml.md#run-variables), dzięki czemu można ponownie używać definicji zadań i standaryzacji tagów dla obrazów i artefaktów.

## <a name="quick-task"></a>Szybkie zadanie

Cykl programowania w pętli wewnętrznej, proces iteracyjny pisania kodu, kompilowania i testowania aplikacji przed zatwierdzeniem do kontroli źródła, to naprawdę początek zarządzania cyklem życia kontenera.

Przed zatwierdzeniem pierwszego wiersza kodu funkcja [zadanie szybkiego zadania](container-registry-tutorial-quick-task.md) ACR zadania może zapewnić zintegrowane środowisko programistyczne, odciążając kompilacje obrazu kontenera na platformie Azure. Za pomocą szybkich zadań można zweryfikować zautomatyzowane definicje kompilacji i wychwycić potencjalne problemy przed zatwierdzeniem kodu.

Przy użyciu znanego formatu `docker build`, polecenie [AZ ACR Build][az-acr-build] w interfejsie wiersza polecenia platformy Azure przyjmuje [kontekst](#context-locations) (zestaw plików do skompilowania), wysyła do niego zadania ACR i domyślnie wypchnięcie skompilowanego obrazu do rejestru po zakończeniu.

Aby zapoznać się z wprowadzeniem, zobacz Przewodnik Szybki Start dotyczący [kompilowania i uruchamiania obrazu kontenera](container-registry-quickstart-task-cli.md) w Azure Container Registry.  

Zadania ACR są zaprojektowane jako pierwotny cykl życia kontenera. Na przykład Zintegruj zadania ACR w rozwiązaniu CI/CD. Wykonanie polecenia [AZ login][az-login] z jednostką [usługi][az-login-service-principal]powoduje, że rozwiązanie Ci/CD może wydać polecenie [AZ ACR Build][az-acr-build] , aby uruchomić kompilacje obrazu.

Dowiedz się, jak używać szybkich zadań w pierwszym samouczku ACR Tasks, [tworzyć obrazy kontenerów w chmurze za pomocą zadań Azure Container Registry](container-registry-tutorial-quick-task.md).

> [!TIP]
> Jeśli chcesz skompilować i wypchnąć obraz bezpośrednio z kodu źródłowego bez pliku dockerfile, Azure Container Registry udostępnia polecenie [AZ ACR Pack Build][az-acr-pack-build] (wersja zapoznawcza). To narzędzie służy do kompilowania i wypychania obrazu z kodu źródłowego aplikacji przy użyciu [natywnych Buildpacks w chmurze](https://buildpacks.io/).

## <a name="trigger-task-on-source-code-update"></a>Wyzwalanie zadania na aktualizacji kodu źródłowego

Wyzwalanie kompilacji obrazu kontenera lub zadania wieloetapowego podczas zatwierdzania kodu lub żądania ściągnięcia lub aktualizacji w repozytorium Git w witrynie GitHub lub Azure DevOps. Można na przykład skonfigurować zadanie kompilacji za pomocą polecenia interfejsu CLI platformy Azure [AZ ACR Task Create][az-acr-task-create] , określając repozytorium git i opcjonalnie gałąź i pliku dockerfile. Gdy Twój zespół zaktualizuje kod w repozytorium, ACR zadania — utworzony element webhook wyzwala kompilację obrazu kontenera zdefiniowanego w repozytorium. 

Zadania ACR obsługują następujące wyzwalacze podczas ustawiania repozytorium git jako kontekstu zadania:

| Wyzwalacz | Włączona domyślnie |
| ------- | ------------------ |
| Zatwierdzenie | Tak |
| Żądanie ściągnięcia | Nie |

Aby skonfigurować wyzwalacz, należy podać osobisty token dostępu (DevOps) w celu ustawienia elementu webhook w repozytorium GitHub lub Azure.

Dowiedz się, jak wyzwolić kompilacje w ramach zatwierdzenia kodu źródłowego w drugim samouczku ACR zadania, [Automatyzuj kompilacje obrazów kontenerów za pomocą zadań Azure Container Registry](container-registry-tutorial-build-task.md).

## <a name="automate-os-and-framework-patching"></a>Automatyzowanie stosowania poprawek systemu operacyjnego i platformy

Możliwości ACR zadania, aby naprawdę ulepszyć przepływ pracy kompilacji kontenera, pochodzą z możliwości wykrywania aktualizacji obrazu podstawowego. Po wypchnięciu zaktualizowanego obrazu podstawowego do rejestru lub zaktualizowaniu obrazu podstawowego w repozytorium publicznym, takim jak w usłudze Docker Hub, ACR zadania mogą automatycznie kompilować wszystkie obrazy aplikacji na ich podstawie.

Obrazy kontenerów można w szerokim zakresie klasyfikować do obrazów *podstawowych* i obrazów *aplikacji* . Obrazy podstawowe zazwyczaj obejmują system operacyjny i platformy aplikacji, na których jest skompilowana aplikacja, oraz inne dostosowania. Te obrazy podstawowe są zwykle oparte na publicznych obrazach nadrzędnych, na przykład: [Alpine Linux][base-alpine], [Windows][base-windows], [.NET][base-dotnet]lub [Node. js][base-node]. Kilka obrazów aplikacji może współużytkować wspólny obraz podstawowy.

W przypadku aktualizowania obrazu systemu operacyjnego lub aplikacji w ramach utrzymującego się elementu przez funkcję, na przykład z krytyczną poprawką zabezpieczeń systemu operacyjnego, należy również zaktualizować obrazy podstawowe w celu uwzględnienia poprawki krytycznej. Każdy obraz aplikacji należy również ponownie skompilować, aby uwzględnić te prefiksy nadrzędne teraz zawarte w obrazie podstawowym.

Ponieważ zadania ACR dynamicznie odnajdują zależności obrazu podstawowego podczas kompilowania obrazu kontenera, może wykryć, kiedy obraz podstawowy obrazu aplikacji zostanie zaktualizowany. Po jednym wstępnie skonfigurowanym [zadaniu kompilacji](container-registry-tutorial-base-image-update.md#create-a-task)ACR zadania **automatycznie ponownie kompilują każdy obraz aplikacji** . Dzięki temu automatycznemu wykrywaniu i odbudowywaniu ACR zadania oszczędzają czas i nakłady pracy, które są zwykle wymagane do ręcznego śledzenia i aktualizowania każdego obrazu aplikacji, do którego odwołuje się zaktualizowany obraz podstawowy.

W przypadku kompilacji obrazu z pliku dockerfile, zadanie ACR śledzi podstawową aktualizację obrazu, gdy podstawowy obraz znajduje się w jednej z następujących lokalizacji:

* Ten sam rejestr kontenerów platformy Azure, w którym uruchomiono zadanie
* Inna usługa Azure Container Registry w tym samym regionie 
* Repozytorium publiczne w usłudze Docker Hub
* Repozytorium publiczne w programie Microsoft Container Registry

> [!NOTE]
> * Wyzwalacz aktualizacji obrazu podstawowego jest domyślnie włączony w zadaniu ACR. 
> * Obecnie zadania ACR śledzą jedynie podstawowe aktualizacje obrazu dla obrazów aplikacji (*Runtime*). Zadania ACR nie śledzą podstawowych aktualizacji obrazu dla obrazów pośrednich (*buildtime*) używanych w wieloetapowe dockerfile wieloetapowym. 

Dowiedz się więcej o poprawkach systemów operacyjnych i struktur w trzecim samouczku ACR zadania, [automatyzując kompilacje obrazów w podstawowej aktualizacji obrazu przy użyciu Azure Container Registry zadań](container-registry-tutorial-base-image-update.md).

## <a name="schedule-a-task"></a>Planowanie zadania

Opcjonalnie można zaplanować zadanie przez skonfigurowanie co najmniej jednego *wyzwalacza czasomierza* podczas tworzenia lub aktualizowania zadania. Planowanie zadania jest przydatne do uruchamiania obciążeń kontenera zgodnie ze zdefiniowanym harmonogramem lub uruchamiania operacji konserwacji lub testów dla obrazów, które są regularnie wypychane do rejestru. Aby uzyskać szczegółowe informacje, zobacz [Uruchamianie zadania ACR zgodnie ze zdefiniowanym harmonogramem](container-registry-tasks-scheduled.md).

## <a name="multi-step-tasks"></a>Zadania wieloetapowe

Wieloetapowe zadania zapewniają definicje zadań oparte na krokach i wykonywanie w celu tworzenia, testowania i poprawiania obrazów kontenerów w chmurze. Kroki zadania zdefiniowane w [pliku YAML](container-registry-tasks-reference-yaml.md) określają poszczególne operacje kompilacji i wypychania dla obrazów kontenerów lub innych artefaktów. Mogą one również definiować wykonanie jednego lub kilku kontenerów, z każdym krokiem używającym kontenera jako jego środowiska wykonawczego.

Można na przykład utworzyć zadanie wieloetapowe, które automatyzuje następujące czynności:

1. Tworzenie obrazu aplikacji sieci Web
1. Uruchamianie kontenera aplikacji sieci Web
1. Kompilowanie obrazu testowego aplikacji sieci Web
1. Uruchom kontener testów aplikacji sieci Web, który wykonuje testy względem uruchomionego kontenera aplikacji
1. Jeśli testy zachodzą, Kompiluj pakiet archiwum wykresów Helm
1. Wykonaj `helm upgrade` przy użyciu nowego pakietu archiwum wykresów Helm

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
| Podfolder Azure DevOps | Pliki znajdujące się w podfolderze w repozytorium platformy Azure. Przykład przedstawia kombinację specyfikacji gałęzi i podfolderów. | `https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder` |
| Plik tar zdalnego | Pliki skompresowanego Archiwum na zdalnym serwerze WebServer. | `http://remoteserver/myapp.tar.gz` |

## <a name="image-platforms"></a>Platformy obrazów

Domyślnie zadania ACR kompilują obrazy dla systemu operacyjnego Linux i architektury amd64. Określ tag `--platform`, aby tworzyć obrazy systemu Windows lub obrazy Linux dla innych architektur. Określ system operacyjny i opcjonalnie obsługiwaną architekturę w formacie systemu operacyjnego/architektury (na przykład `--platform Linux/arm`). W przypadku architektur ARM Opcjonalnie określ wariant w formacie OS/Architecture/Variant (na przykład `--platform Linux/arm64/v8`):

| System operacyjny | Architektura|
| --- | ------- | 
| Linux | Procesor<br/>ARM<br/>arm64<br/>386 |
| Windows | Procesor |

## <a name="view-task-logs"></a>Wyświetlanie dzienników zadań

Każde uruchomienie zadania generuje dane wyjściowe dziennika, które można sprawdzić, aby określić, czy kroki zadania zostały wykonane pomyślnie. W przypadku użycia polecenia [AZ ACR Build](/cli/azure/acr#az-acr-build), [AZ ACR Run](/cli/azure/acr#az-acr-run)lub [AZ ACR Task Run](/cli/azure/acr/task#az-acr-task-run) , aby wyzwolić zadanie, dane wyjściowe dziennika dla uruchomienia zadania są przesyłane strumieniowo do konsoli programu, a także przechowywane do późniejszego pobrania. Gdy zadanie jest automatycznie wyzwalane, na przykład przez zatwierdzenie kodu źródłowego lub aktualizację obrazu podstawowego, dzienniki zadań są przechowywane tylko. Wyświetl dzienniki dla zadania uruchomionego w Azure Portal lub użyj polecenia [AZ ACR Task Logs](/cli/azure/acr/task#az-acr-task-logs) .

Domyślnie dane i dzienniki uruchamiania zadań w rejestrze są przechowywane przez 30 dni, a następnie automatycznie przeczyszczane. Jeśli chcesz zarchiwizować dane do uruchomienia zadania, Włącz archiwizowanie przy użyciu polecenia [AZ ACR Task Update-Run](/cli/azure/acr/task#az-acr-task-update-run) . Poniższy przykład umożliwia archiwizowanie dla zadania uruchamiania *CF11* *w rejestrze rejestru.*

```azurecli
az acr task update-run --registry myregistry --run-id cf11 --no-archive false
```

## <a name="next-steps"></a>Następne kroki

Gdy wszystko jest gotowe do automatyzowania kompilacji i konserwacji obrazów kontenerów w chmurze, zapoznaj się z [serią samouczka zadań ACR](container-registry-tutorial-quick-task.md).

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
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png

---
title: Ciągła Integracja/ciągłe dostarczanie za pomocą usługi Azure Container Service i Swarm
description: Za pomocą usługi Azure Container Service Docker Swarm, usługi Azure Container Registry i Visual Studio Team Services do ciągłego dostarczania wielokontenerowej aplikacji .NET Core
services: container-service
author: jcorioland
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/08/2016
ms.author: jucoriol
ms.custom: mvc
ms.openlocfilehash: b88fba4e04adb56742edf8023fde34e8ff6519c2
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39437920"
---
# <a name="full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-docker-swarm-using-visual-studio-team-services"></a>Pełny potok ciągłej integracji/ciągłego wdrażania, aby wdrożyć aplikację obsługującą wiele kontenerów w usłudze Azure Container Service przy użyciu rozwiązania Docker Swarm przy użyciu programu Visual Studio Team Services

Jedną z największych wyzwań podczas tworzenia nowoczesnych aplikacji dla chmury jest możliwość ciągłe dostarczanie tych aplikacji. W tym artykule dowiesz się, jak zaimplementować ciągłej integracji i potoku wdrażania (CI/CD) przy użyciu usługi Azure Container Service przy użyciu rozwiązania Docker Swarm, usługi Azure Container Registry i Visual Studio Team Services build i release management.

Ten artykuł jest oparty na prostej aplikacji, dostępnych na [GitHub](https://github.com/jcorioland/MyShop/tree/acs-docs), opracowanych za pomocą programu ASP.NET Core. Aplikacja składa się z czterech różnych usług: trzy sieci web, interfejsów API i jedną witrynę sieci web frontonu:

![MyShop przykładowej aplikacji](./media/container-service-docker-swarm-setup-ci-cd/myshop-application.png)

Celem jest ciągłe dostarczanie tej aplikacji w klastrze Docker Swarm przy użyciu programu Visual Studio Team Services. Poniższa ilustracja szczegóły ten potok ciągłego dostarczania:

![MyShop przykładowej aplikacji](./media/container-service-docker-swarm-setup-ci-cd/full-ci-cd-pipeline.png)

Poniżej przedstawiono krótki opis kroków:

1. Zmiany kodu są zobowiązane do repozytorium kodu źródłowego (tutaj GitHub) 
1. GitHub wyzwala kompilację w usłudze Visual Studio Team Services 
1. Visual Studio Team Services pobiera najnowszą wersję źródła i kompilacji wszystkie obrazy wchodzących w skład aplikacji 
1. Visual Studio Team Services wypycha obraz do rejestru platformy Docker, utworzony za pomocą usługi Azure Container Registry 
1. Visual Studio Team Services wyzwala nowej wersji 
1. Wydanie uruchamia niektórych poleceń na węzła głównego klastra usługi kontenera platformy Azure przy użyciu protokołu SSH 
1. Rozwiązanie docker Swarm w klastrze pobiera najnowszą wersję obrazów 
1. Nowa wersja aplikacji jest wdrażany przy użyciu narzędzia Docker Compose 

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka, należy wykonać następujące zadania:

- [Utworzenie klastra Swarm usługi Azure Container Service](container-service-deployment.md)
- [Połączenie z klastrem Swarm w usłudze Azure Container Service](../container-service-connect.md)
- [Utwórz rejestr Azure container registry](../../container-registry/container-registry-get-started-portal.md)
- [Utworzony projekt konta i zespołu Visual Studio Team Services](https://www.visualstudio.com/en-us/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services)
- [Utwórz rozwidlenie repozytorium GitHub z kontem usługi GitHub](https://github.com/jcorioland/MyShop/)

[!INCLUDE [container-service-swarm-mode-note](../../../includes/container-service-swarm-mode-note.md)]

Należy również maszynę z systemem Ubuntu (14.04 i 16.04), z zainstalowaną platformą Docker. Ten komputer jest używany przez program Visual Studio Team Services podczas procesów kompilacji i wydania. Jednym ze sposobów tworzenia tej maszyny jest użycie obrazów dostępnych w [portalu Azure Marketplace](https://azure.microsoft.com/marketplace/partners/canonicalandmsopentech/dockeronubuntuserver1404lts/). 

## <a name="step-1-configure-your-visual-studio-team-services-account"></a>Krok 1: Konfigurowanie konta usługi Visual Studio Team Services 

W tej sekcji skonfigurujesz konto Visual Studio Team Services.

### <a name="configure-a-visual-studio-team-services-linux-build-agent"></a>Skonfiguruj agenta kompilacji programu Visual Studio Team Services systemu Linux

Aby utworzyć obrazy platformy Docker i wypchnąć te obrazy do usługi Azure container registry z kompilacji programu Visual Studio Team Services, musisz zarejestrować agenta systemu Linux. Masz następujące opcje instalacji:

* [Wdrażanie agenta w systemie Linux](https://www.visualstudio.com/docs/build/admin/agents/v2-linux)

* [Użyj platformy Docker, aby uruchomić agenta usługi VSTS](https://hub.docker.com/r/microsoft/vsts-agent)

### <a name="install-the-docker-integration-vsts-extension"></a>Instalowanie rozszerzenia usługi VSTS integracji platformy Docker

Firma Microsoft udostępnia rozszerzenia VSTS w celu pracy z rozwiązaniem Docker w kompilacji i wydawania procesów. To rozszerzenie jest dostępne w [witrynę Marketplace usługi VSTS](https://marketplace.visualstudio.com/items?itemName=ms-vscs-rm.docker). Kliknij przycisk **zainstalować** można dodać tego rozszerzenia do konta usługi VSTS:

![Zainstaluj Integracja z platformą Docker](./media/container-service-docker-swarm-setup-ci-cd/install-docker-vsts.png)

Zostanie wyświetlony monit nawiąż połączenie z kontem usługi VSTS przy użyciu poświadczeń. 

### <a name="connect-visual-studio-team-services-and-github"></a>Łączenie programu Visual Studio Team Services i GitHub

Konfigurowanie połączenia między projektu usługi VSTS i konta usługi GitHub.

1. W projekcie programu Visual Studio Team Services, kliknij przycisk **ustawienia** ikonę na pasku narzędzi, a następnie wybierz pozycję **usług**.

    ![Program Visual Studio Team Services — połączenie zewnętrzne](./media/container-service-docker-swarm-setup-ci-cd/vsts-services-menu.png)

1. Po lewej stronie, kliknij przycisk **nowy punkt końcowy usługi** > **GitHub**.

    ![Program Visual Studio Team Services — usługi GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github.png)

1. Aby autoryzować usługę VSTS, aby pracować z Twojego konta usługi GitHub, kliknij przycisk **Autoryzuj** i postępuj zgodnie z procedurą w otwartym oknie.

    ![Visual Studio Team Services — autoryzowania usługi GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-authorize.png)

### <a name="connect-vsts-to-your-azure-container-registry-and-azure-container-service-cluster"></a>Łączenie usługi VSTS do rejestru kontenerów platformy Azure i klastrem usługi kontenera platformy Azure

Ostatnie kroki przed przejściem do potoku ciągłej integracji/ciągłego wdrażania są do skonfigurowania połączenia zewnętrzne do rejestru kontenerów i klastrem Docker Swarm na platformie Azure. 

1. W **usług** ustawień projektu Visual Studio Team Services, Dodaj punkt końcowy usługi typu **rejestru platformy Docker**. 

1. W wyświetlonym menu podręcznym wprowadź adres URL i poświadczenia usługi Azure container registry.

    ![Program Visual Studio Team Services — rejestr platformy Docker](./media/container-service-docker-swarm-setup-ci-cd/vsts-registry.png)

1. Dla klastra Docker Swarm Dodawanie punktu końcowego typu **SSH**. Wprowadź informacje połączenia SSH z klastrem Swarm.

    ![Visual Studio Team Services — SSH](./media/container-service-docker-swarm-setup-ci-cd/vsts-ssh.png)

Całą konfigurację odbywa się teraz. W następnych krokach utworzysz potok ciągłej integracji/ciągłego wdrażania, który kompiluje i wdraża aplikację w klastrze Docker Swarm. 

## <a name="step-2-create-the-build-definition"></a>Krok 2: Tworzenie definicji kompilacji

W tym kroku konfiguracji definitionfor kompilacji projektu usługi VSTS i definicja przepływu pracy kompilacji dla obrazów kontenerów

### <a name="initial-definition-setup"></a>Definicja początkowej instalacji

1. Aby utworzyć definicję kompilacji, połącz się z projektem usługi Visual Studio Team Services, a następnie kliknij przycisk **kompilowanie i wydawanie**. 

1. W **definicje kompilacji** kliknij **+ nowy**. Wybierz **pusty** szablonu.

    ![Program Visual Studio Team Services — nowej definicji kompilacji](./media/container-service-docker-swarm-setup-ci-cd/create-build-vsts.png)

1. Konfigurowanie nowej kompilacji ze źródłem repozytorium GitHub, sprawdź **ciągłej integracji**i wybierz kolejki agentów, na którym zarejestrowany agenta systemu Linux. Kliknij przycisk **Utwórz** do tworzenia definicji kompilacji.

    ![Visual Studio Team Services — utwórz definicję kompilacji](./media/container-service-docker-swarm-setup-ci-cd/vsts-create-build-github.png)

1. Na **definicje kompilacji** strony, należy najpierw otworzyć **repozytorium** karcie i skonfiguruj kompilację do użycia rozwidlenie projektu MyShop, który został utworzony w wymaganiach wstępnych. Upewnij się, że wybrano *acs-docs* jako **gałęzi domyślnej**.

    ![Program Visual Studio Team Services — Konfiguracja repozytorium kompilacji](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-repo-conf.png)

1. Na **wyzwalaczy** skonfiguruj kompilacji, która ma być wyzwalane po każdym zatwierdzeniu. Wybierz **ciągłej integracji** i **partii zmian**.

    ![Program Visual Studio Team Services — Konfiguracja wyzwalacza kompilacji](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-trigger-conf.png)

### <a name="define-the-build-workflow"></a>Definiowanie przepływu pracy kompilacji
Następne kroki zdefiniować przepływ pracy kompilacji. Istnieje pięć obrazów kontenera do kompilacji dla *MyShop* aplikacji. Każdy obraz jest kompilowany, za pomocą pliku Dockerfile znajdujący się w folderach projektu:

* ProductsApi
* Serwer proxy
* RatingsApi
* RecommandationsApi
* Sklepu

Należy dodać dwa kroki platformy Docker dla każdego obrazu: jeden do tworzenia obrazu i jeden aby wypchnąć obraz w usłudze Azure container registry. 

1. Aby dodać krok w przepływie pracy kompilacji, kliknij przycisk **+ Dodaj krok kompilacji** i wybierz **Docker**.

    ![Visual Studio Team Services — Dodawanie kroków kompilacji](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-add-task.png)

1. Dla każdego obrazu, należy skonfigurować jeden krok, który używa `docker build` polecenia.

    ![Program Visual Studio Team Services — kompilacji platformy Docker](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-build.png)

    Dla operacji kompilacji, wybierz usługi Azure container registry **Zbuduj obraz** akcji i pliku Dockerfile, który definiuje każdego obrazu. Ustaw **kontekstu kompilacji** jako plik Dockerfile katalogu głównego, a następnie zdefiniować **nazwa obrazu**. 
    
    Jak pokazano na poprzednim ekranie, rozpoczynać się nazwa obrazu identyfikator URI usługi Azure container registry. (Umożliwia także zmienną kompilacji próby parametryzacji tag obrazu, takie jak identyfikator kompilacji, w tym przykładzie.)

1. Dla każdego obrazu, należy skonfigurować drugi krok, który używa `docker push` polecenia.

    ![Program Visual Studio Team Services — Docker Push](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-push.png)

    Dla operacji wypychania, Wybieranie usługi Azure container registry **wypchnąć obraz** akcji, a następnie wprowadź **nazwa obrazu** utworzonego w poprzednim kroku.

1. Po skonfigurowaniu kroki kompilacji i wypychania dla każdej z pięciu obrazów, należy dodać dwa kolejne kroki w przepływie pracy kompilacji.

    a. Zadanie wiersza polecenia, który używa skryptu powłoki systemowej w celu zastąpienia *BuildNumber* wystąpienie w pliku docker-compose.yml z bieżącej kompilacji identyfikatora. Zobacz poniższy ekran, aby uzyskać szczegółowe informacje.

    ![Visual Studio Team Services — plik Compose aktualizacji](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-replace-build-number.png)

    b. Zadanie, które umieszcza zaktualizowany plik Compose jako artefakt kompilacji, dzięki czemu może być używany w wersji. Zobacz poniższy ekran, aby uzyskać szczegółowe informacje.

    ![Visual Studio Team Services — publikowanie Compose pliku](./media/container-service-docker-swarm-setup-ci-cd/vsts-publish-compose.png) 

1. Kliknij przycisk **Zapisz** i nadaj nazwę definicji kompilacji.

## <a name="step-3-create-the-release-definition"></a>Krok 3: Tworzenie definicji wydania

Visual Studio Team Services umożliwia [zarządzania wersjami w środowiskach](https://www.visualstudio.com/team-services/release-management/). Można włączyć ciągłe wdrażanie upewnić się, że aplikacja jest wdrażana w różnych środowiskach (na przykład deweloperów, testerów, przedprodukcyjnych i produkcyjnych) w sposób. Można utworzyć nowego środowiska, reprezentujący klastra Azure Container Service Docker Swarm.

![Program Visual Studio Team Services — wersji usługi ACS](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Początkowa wersja Instalatora

1. Aby utworzyć definicję wydania, kliknij przycisk **wersji** > **+ wydanie**

1. Aby skonfigurować źródła artefaktu, kliknij **artefaktów** > **połączenia źródła artefaktu**. W tym miejscu połączyć tej nowej definicji wydania z kompilacji, który został zdefiniowany w poprzednim kroku. Dzięki temu pliku docker-compose.yml jest dostępna w procesie tworzenia wersji.

    ![Program Visual Studio Team Services — artefaktów wydań](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-artefacts.png) 

1. Aby skonfigurować wyzwalacz wydania, kliknij **wyzwalaczy** i wybierz **ciągłe wdrażanie**. Ustaw wyzwalacz, w tym samym źródła artefaktu. To ustawienie zapewni, zaraz po pomyślnym zakończeniu kompilacji rozpoczyna się nowej wersji.

    ![Program Visual Studio Team Services — wyzwalacze wydania](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-trigger.png) 

### <a name="define-the-release-workflow"></a>Zdefiniuj przepływ pracy wydania

Przepływ pracy wydania składa się z dwóch zadań, które dodajesz.

1. Skonfiguruj zadanie, aby bezpiecznie skopiować plik compose *wdrażanie* folderu na Docker Swarm węzła głównego, przy użyciu połączenia SSH, który został wcześniej skonfigurowany. Zobacz poniższy ekran, aby uzyskać szczegółowe informacje.

    ![Program Visual Studio Team Services — wersji połączenia usługi](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-scp.png)

1. Konfigurowanie drugie zadanie do wykonania polecenia powłoki bash w celu uruchomienia `docker` i `docker-compose` poleceń na węzła głównego. Zobacz poniższy ekran, aby uzyskać szczegółowe informacje.

    ![Visual Studio Team Services - wersja Bash](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-bash.png)

    Polecenie jest wykonywane przy użyciu głównego interfejsu wiersza polecenia platformy Docker i interfejsu wiersza polecenia platformy Docker-Compose, aby wykonać następujące zadania:

    - Zaloguj się do usługi Azure container registry (używa trzech variab'les kompilacji, które są zdefiniowane w **zmienne** kartę)
    - Zdefiniuj **DOCKER_HOST** zmiennej do pracy z punktu końcowego Swarm (: 2375)
    - Przejdź do *wdrażanie* folder, który został utworzony w poprzednim zadaniu bezpiecznego kopiowania i który zawiera plik docker-compose.yml 
    - Wykonaj `docker-compose` poleceń ściągnięcia nowych obrazów zatrzymywanie usług, Usuń usługi i tworzenia kontenerów.

    >[!IMPORTANT]
    > Jak pokazano na poprzednim ekranie, pozostaw **zakończyć się niepowodzeniem ze strumienia STDERR** niezaznaczone pole wyboru. To ustawienie ważne, ponieważ `docker-compose` drukuje kilka komunikatów diagnostycznych, takich jak kontenery są zatrzymywania lub usuwany na standardowy błąd danych wyjściowych. Jeśli zaznaczysz pole wyboru, Visual Studio Team Services zgłasza błędy, które wystąpiły podczas wersji, nawet jeśli wszystko przebiegnie poprawnie.
    >
1. Zapisz ten nowy definicji wydania.


>[!NOTE]
>To wdrożenie obejmuje pewien Przestój, ponieważ jesteśmy zatrzymywanie usług stary i uruchamianie nowego. Istnieje możliwość tego uniknąć, wykonując wdrożenie niebieski zielony.
>

## <a name="step-4-test-the-cicd-pipeline"></a>Krok 4. Testowanie potoku ciągłej integracji/ciągłego wdrażania

Teraz, gdy wszystko będzie gotowe z konfiguracją, jest czas na przetestowanie ten nowy potok ciągłej integracji/ciągłego wdrażania. Jest najprostszym sposobem przetestowania jej do aktualizowania kodu źródłowego i zatwierdza zmiany w repozytorium GitHub. Kilka sekund po wypchnięciu kodu, zobaczysz nową kompilację w Visual Studio Team Services. Po pomyślnym ukończeniu nowa wersja zostanie wyzwolony i wdroży nowej wersji aplikacji w klastrze usługi kontenera platformy Azure.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji o ciągłej integracji/ciągłego wdrażania za pomocą programu Visual Studio Team Services, zobacz [kompilacji usług VSTS Przegląd](https://www.visualstudio.com/docs/build/overview).

---
title: CI/CD z usługą kontenera Azure i Swarm
description: Użyć usługi kontenera platformy Azure z Docker Swarm, rejestru kontenera Azure i Visual Studio Team Services w celu dostarczenia stale aplikacji .NET Core wielu kontenera
services: container-service
author: jcorioland
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/08/2016
ms.author: jucoriol
ms.custom: mvc
ms.openlocfilehash: 81a07fdfe1c862bc30fb9d567db9a393c0610990
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32179558"
---
# <a name="full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-docker-swarm-using-visual-studio-team-services"></a>Pełny potok CI/CD do wdrażania aplikacji usługi kontenera w usłudze kontenera platformy Azure przy użyciu rozwiązania Docker Swarm przy użyciu programu Visual Studio Team Services

Jedną z największych wyzwań podczas tworzenia nowoczesnych aplikacji w chmurze jest możliwość ciągłego dostarczania tych aplikacji. W tym artykule Dowiedz się jak zaimplementować pełnej ciągłej integracji i wdrażania (CI/CD) potoku z kompilacją rozwiązania Docker Swarm, rejestru kontenera Azure i Visual Studio Team Services za pomocą usługi kontenera platformy Azure i zarządzania zleceniami.

Ten artykuł jest oparty na prostej aplikacji, dostępnych na [GitHub](https://github.com/jcorioland/MyShop/tree/acs-docs), rozwinięte z platformy ASP.NET Core. Aplikacja składa się z czterech różnych usług: trzy sieci web, interfejsów API i frontonu sieci web co:

![MyShop przykładowej aplikacji](./media/container-service-docker-swarm-setup-ci-cd/myshop-application.png)

Celem jest stale świadczenia tej aplikacji w klastrze Docker Swarm, za pomocą programu Visual Studio Team Services. Poniższa ilustracja szczegóły tego potoku ciągłego dostarczania:

![MyShop przykładowej aplikacji](./media/container-service-docker-swarm-setup-ci-cd/full-ci-cd-pipeline.png)

Poniżej przedstawiono krótki opis kroków:

1. Zmiany kodu zostały zastosowane w repozytorium kodu źródłowego (tutaj GitHub) 
2. GitHub uaktywnia kompilację w Visual Studio Team Services 
3. Visual Studio Team Services pobiera najnowsza wersja źródła i tworzy wszystkie obrazy, które tworzą aplikacji 
4. Visual Studio Team Services wypychanie każdego obrazu do rejestru Docker utworzone za pomocą usługi Azure kontenera rejestru 
5. Visual Studio Team Services wyzwala nową wersją 
6. Wersja uruchamia niektóre polecenia przy użyciu protokołu SSH w węźle głównym klastra usługi kontenera platformy Azure 
7. Rozwiązanie docker Swarm w klastrze pobiera najnowsza wersja obrazów 
8. Wdrożenie nowej wersji aplikacji za pomocą rozwiązania Docker Compose 

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka, należy wykonać następujące zadania:

- [Utworzenie klastra Swarm usługi Azure Container Service](container-service-deployment.md)
- [Połączenie z klastrem Swarm w usłudze Azure Container Service](../container-service-connect.md)
- [Tworzenie rejestru kontenera platformy Azure](../../container-registry/container-registry-get-started-portal.md)
- [Utworzony projekt konta i zespołu Visual Studio Team Services](https://www.visualstudio.com/en-us/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services)
- [Rozwidlenia repozytorium GitHub z kontem usługi GitHub](https://github.com/jcorioland/MyShop/)

[!INCLUDE [container-service-swarm-mode-note](../../../includes/container-service-swarm-mode-note.md)]

Należy również maszyny Ubuntu (14.04 i 16.04) z Docker zainstalowane. Ten komputer jest używany przez Visual Studio Team Services podczas procesów kompilacji i wydania. Jednym ze sposobów tworzenia tego komputera jest użycie dostępne w obrazie [portalu Azure Marketplace](https://azure.microsoft.com/marketplace/partners/canonicalandmsopentech/dockeronubuntuserver1404lts/). 

## <a name="step-1-configure-your-visual-studio-team-services-account"></a>Krok 1: Skonfiguruj konto usługi Visual Studio Team Services 

W tej sekcji należy skonfigurować konto w Visual Studio Team Services.

### <a name="configure-a-visual-studio-team-services-linux-build-agent"></a>Konfigurowanie agenta kompilacji programu Visual Studio Team Services Linux

Aby utworzyć obrazy usługi Docker i wypychania tych obrazów w rejestrze kontenera platformy Azure z kompilacji programu Visual Studio Team Services, musisz zarejestrować agenta systemu Linux. Masz następujące opcje instalacji:

* [Wdrażanie agenta w systemie Linux](https://www.visualstudio.com/docs/build/admin/agents/v2-linux)

* [Umożliwia uruchamianie agenta programu VSTS Docker](https://hub.docker.com/r/microsoft/vsts-agent)

### <a name="install-the-docker-integration-vsts-extension"></a>Zainstaluj rozszerzenie VSTS integracji Docker

Firma Microsoft udostępnia rozszerzenia programu VSTS do pracy z rozwiązaniem Docker z kompilacji i wydania procesów. To rozszerzenie jest dostępna w [Marketplace programu VSTS](https://marketplace.visualstudio.com/items?itemName=ms-vscs-rm.docker). Kliknij przycisk **zainstalować** można dodać to rozszerzenie do swojego konta usługi VSTS:

![Zainstaluj Integracja z rozwiązaniem Docker](./media/container-service-docker-swarm-setup-ci-cd/install-docker-vsts.png)

Prośba do nawiązania połączenia przy użyciu poświadczeń konta usługi VSTS. 

### <a name="connect-visual-studio-team-services-and-github"></a>Połączenie usługi Visual Studio Team Services i GitHub

Skonfiguruj połączenie między projektu programu VSTS oraz konto GitHub.

1. W projekcie programu Visual Studio Team Services, kliknij przycisk **ustawienia** ikonę na pasku narzędzi, a następnie wybierz **usług**.

    ![Visual Studio Team Services — połączenie zewnętrzne](./media/container-service-docker-swarm-setup-ci-cd/vsts-services-menu.png)

2. Po lewej stronie, kliknij przycisk **nowy punkt końcowy usługi** > **GitHub**.

    ![Visual Studio Team Services — usługi GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github.png)

3. Aby autoryzować VSTS do pracy z Twoim kontem usługi GitHub, kliknij przycisk **autoryzacji** i postępuj zgodnie z procedurą w otwartym oknie.

    ![Visual Studio Team Services — autoryzować GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-authorize.png)

### <a name="connect-vsts-to-your-azure-container-registry-and-azure-container-service-cluster"></a>Łączenie programu VSTS do rejestru kontenera platformy Azure i klastrem usługi kontenera platformy Azure

Ostatni kroki przed pobraniem z potokiem CI/CD są do konfigurowania połączeń zewnętrznych z rejestru kontenera i klastrem Docker Swarm w usłudze Azure. 

1. W **usług** ustawień projektu Visual Studio Team Services, Dodaj punkt końcowy usługi typu **Docker rejestru**. 

2. W wyświetlonym menu podręcznym wprowadź adres URL i poświadczenia rejestru kontenera platformy Azure.

    ![Visual Studio Team Services — Docker rejestru](./media/container-service-docker-swarm-setup-ci-cd/vsts-registry.png)

3. Aby klaster Docker Swarm Dodawanie punktu końcowego typu **SSH**. Następnie wprowadź informacje o połączeniu SSH klastra Swarm.

    ![Visual Studio Team Services — SSH](./media/container-service-docker-swarm-setup-ci-cd/vsts-ssh.png)

Całą konfigurację odbywa się teraz. W następnych krokach utworzysz potok CI/CD, który tworzy i wdraża aplikację w klastrze Docker Swarm. 

## <a name="step-2-create-the-build-definition"></a>Krok 2: Tworzenie definicji kompilacji

W tym kroku konfiguracji definitionfor kompilacji projektu programu VSTS i zdefiniuj przepływu pracy kompilacji dla obrazów kontenera

### <a name="initial-definition-setup"></a>Definicja początkowej instalacji

1. Aby utworzyć definicję kompilacji, nawiązać połączenia z projektu programu Visual Studio Team Services, a następnie kliknij przycisk **kompilacji i wydania**. 

2. W **definicje kompilacji** kliknij **+ nowy**. Wybierz **pusty** szablonu.

    ![Visual Studio Team Services — nowej definicji kompilacji](./media/container-service-docker-swarm-setup-ci-cd/create-build-vsts.png)

3. Skonfiguruj nową kompilację ze źródłem repozytorium GitHub wyboru **ciągłej integracji**i wybierz kolejkę agenta rejestracji agenta systemu Linux. Kliknij przycisk **Utwórz** do tworzenia definicji kompilacji.

    ![Visual Studio Team Services — Tworzenie definicji kompilacji](./media/container-service-docker-swarm-setup-ci-cd/vsts-create-build-github.png)

4. Na **definicji kompilacji** strony, należy najpierw otworzyć **repozytorium** i skonfiguruj kompilację do użycia rozwidlenie projektu MyShop utworzonego w wymaganiach wstępnych. Upewnij się, że wybrano *acs docs* jako **gałąź domyślną**.

    ![Visual Studio Team Services — Konfiguracja repozytorium kompilacji](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-repo-conf.png)

5. Na **wyzwalaczy** skonfiguruj kompilacji, które będą wyzwalane po każdym zatwierdzeniu. Wybierz **ciągłej integracji** i **partii zmian**.

    ![Visual Studio Team Services — Konfiguracja wyzwalacza kompilacji](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-trigger-conf.png)

### <a name="define-the-build-workflow"></a>Zdefiniuj przepływu pracy kompilacji
Następne kroki zdefiniuj przepływu pracy kompilacji. Istnieje pięć obrazów kontenera do kompilacji dla *MyShop* aplikacji. Obraz jest utworzony przy użyciu plik Dockerfile znajdujący się w folderze projektu:

* ProductsApi
* Serwer proxy
* RatingsApi
* RecommandationsApi
* Sklepu

Należy dodać dwa kroki Docker dla każdego obrazu: jeden do tworzenia obrazu i jeden do dystrybuowania obrazu w rejestrze kontenera platformy Azure. 

1. Aby dodać krok w przepływie pracy kompilacji, kliknij przycisk **+ Dodaj krok kompilacji** i wybierz **Docker**.

    ![Visual Studio Team Services — dodać kroki procesu kompilacji](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-add-task.png)

2. Dla każdego obrazu, należy skonfigurować jeden krok, który używa `docker build` polecenia.

    ![Visual Studio Team Services — Docker kompilacji](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-build.png)

    Dla operacji kompilacji, wybierz rejestru kontenera platformy Azure, **kompilacji obrazu** akcji, a plik Dockerfile, który definiuje każdego obrazu. Ustaw **kontekst kompilacji** jako plik Dockerfile z katalogu głównego, a także definiują **nazwa obrazu**. 
    
    Jak pokazano na poprzednich ekranu, rozpoczynać identyfikatora URI z rejestru kontenera platformy Azure nazwę obrazu. (Umożliwia także zmienną kompilacji do parametryzacja tag obrazu, takich jak identyfikator kompilacji, w tym przykładzie.)

3. Dla każdego obrazu, skonfiguruj drugi krok, który używa `docker push` polecenia.

    ![Visual Studio Team Services — Docker Push](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-push.png)

    Dla operacji wypychania, wybierz rejestru kontenera platformy Azure, **Push obrazu** akcji, a następnie wprowadź **nazwa obrazu** wbudowane w poprzednim kroku.

4. Po skonfigurowaniu kroków kompilacji i wypychania dla każdego z pięciu obrazów, należy dodać dwa kolejne kroki w przepływie pracy kompilacji.

    a. Zadania wiersza polecenia, który używa skryptów bash w celu zastąpienia *BuildNumber* wystąpienie w plik docker-compose.yml z bieżącym kompilacji identyfikatora. Zobacz następujący ekran, aby uzyskać szczegółowe informacje.

    ![Visual Studio Team Services — plik Redaguj aktualizacji](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-replace-build-number.png)

    b. Zadanie porzuca zaktualizowany plik Redaguj jako artefaktów kompilacji dzięki mogą być używane w wersji. Zobacz następujący ekran, aby uzyskać szczegółowe informacje.

    ![Usługi Visual Studio Team - publikowania tworzenia pliku](./media/container-service-docker-swarm-setup-ci-cd/vsts-publish-compose.png) 

5. Kliknij przycisk **zapisać** i nazwij definicję kompilacji.

## <a name="step-3-create-the-release-definition"></a>Krok 3: Tworzenie definicji wersji

Visual Studio Team Services umożliwia [zarządzania wersjami w środowiskach](https://www.visualstudio.com/team-services/release-management/). Można włączyć ciągłego wdrażania upewnić się, że aplikacja jest wdrożony na Twoje różnych środowiskach (np. dev, test produkcji wstępnej i produkcji) w sposób. Można utworzyć nowego środowiska reprezentujący klastra Azure kontenera usługi Docker Swarm.

![Visual Studio Team Services — w wersji usług ACS](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Początkowa wersja Instalatora

1. Do tworzenia definicji wersji, kliknij przycisk **wersje** > **+ zlecenia**

2. Kliknij, aby skonfigurować źródła artefaktu **artefakty** > **połączenia źródła artefaktu**. W tym miejscu połączyć tej nowej wersji definicji kompilacji, który został zdefiniowany w poprzednim kroku. Dzięki temu plik docker-compose.yml jest dostępna w procesie wersji.

    ![Visual Studio Team Services - wersja artefaktów](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-artefacts.png) 

3. Kliknij, aby skonfigurować wyzwalacz wersji **wyzwalaczy** i wybierz **ciągłe wdrażanie**. W tym samym źródle artefaktu, należy ustawić wyzwalacz. To ustawienie zapewni, jak kompilacja zostaje ukończona pomyślnie rozpoczyna się nowej wersji.

    ![Visual Studio Team Services — wyzwalaczy zlecenia](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-trigger.png) 

### <a name="define-the-release-workflow"></a>Definiowanie wersji przepływu pracy

Wersja przepływ pracy składa się z dwóch zadań, które można dodać.

1. Skonfigurować zadania bezpiecznie skopiować plik Redaguj *wdrażanie* folder w rozwiązaniu Docker Swarm węzła głównego, przy użyciu połączenia SSH został wcześniej skonfigurowany. Zobacz następujący ekran, aby uzyskać szczegółowe informacje.

    ![Visual Studio Team Services - wersja punkt połączenia usługi](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-scp.png)

2. Skonfiguruj drugi zadań do wykonania polecenia bash, aby uruchomić `docker` i `docker-compose` poleceń w węźle głównym. Zobacz następujący ekran, aby uzyskać szczegółowe informacje.

    ![Visual Studio Team Services — Bash zlecenia](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-bash.png)

    Polecenie jest wykonywane przy użyciu wzorca interfejsu wiersza polecenia Docker i interfejsu wiersza polecenia rozwiązania Docker Compose, wykonaj następujące zadania:

    - Zaloguj się do rejestru kontenera platformy Azure (używa trzech variab'les kompilacji, które są zdefiniowane w **zmienne** kartę)
    - Zdefiniuj **DOCKER_HOST** zmienną do pracy z punktu końcowego Swarm (: 2375)
    - Przejdź do *wdrażanie* folderu, który został utworzony przez poprzednie zadanie kopiowania bezpiecznego i który zawiera plik docker-compose.yml 
    - Wykonanie `docker-compose` poleceń ściągnięcia nowych obrazów, Zatrzymaj usługi, Usuń usługi i tworzenie kontenerów.

    >[!IMPORTANT]
    > Jak pokazano na poprzednich ekranu, pozostaw **zakończyć się niepowodzeniem ze strumienia STDERR** niezaznaczone pole wyboru. To ustawienie ważne, ponieważ `docker-compose` wyświetla komunikaty diagnostyczne, takie jak kontenery są zatrzymywanie lub usuwane, dane wyjściowe błędów. Jeśli pole wyboru Visual Studio Team Services raporty błędów, które wystąpiły podczas wersji, nawet jeśli wszystko przebiegnie poprawnie.
    >
3. Zapisz ten nowej definicji wersji.


>[!NOTE]
>To wdrożenie zawiera pewien Przestój, ponieważ jesteśmy zatrzymywanie usługi w starym i nowym systemem. Istnieje możliwość tego uniknąć, wykonując wdrożenie zielony niebieski.
>

## <a name="step-4-test-the-cicd-pipeline"></a>Krok 4. Testowanie potoku CI/CD

Teraz, po zakończeniu konfiguracji, nadszedł czas na test ten nowy potok CI/CD. Najprostszym sposobem, aby ją przetestować jest zaktualizuj kod źródłowy i zatwierdzić zmiany do repozytorium GitHub. Kilka sekund po naciśnięciu kodu, zobaczysz nowej kompilacji uruchomionych w Visual Studio Team Services. Po pomyślnym ukończeniu nowej wersji zostanie wywołane i rozpocznie Wdrażanie nowej wersji aplikacji w klastrze usługi kontenera platformy Azure.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat CI/CD z Visual Studio Team Services, zobacz [kompilacji programu VSTS omówienie](https://www.visualstudio.com/docs/build/overview).

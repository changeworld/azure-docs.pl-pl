---
title: (PRZESTARZAŁE) Ciągła integracja/płyta CD z usługą Azure Container Service i rojem kontenerów
description: Korzystanie z usługi Azure Container Service z usługą Docker Swarm, rejestrem kontenerów platformy Azure i programem Azure DevOps w celu ciągłego dostarczania aplikacji .NET Core z wieloma kontenerami
author: jcorioland
ms.service: container-service
ms.topic: conceptual
ms.date: 12/08/2016
ms.author: jucoriol
ms.custom: mvc
ms.openlocfilehash: 11a6debe735459b617f6f93c3f67a32350dd4623
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76549057"
---
# <a name="deprecated-full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-docker-swarm-using-azure-devops-services"></a>(PRZESTARZAŁE) Pełny potok ciągłej integracji/ciągłego wdrażania w celu wdrożenia aplikacji wielo kontenerowej w usłudze Azure Container Service za pomocą usługi Docker Swarm przy użyciu usług Azure DevOps

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Jednym z największych wyzwań podczas tworzenia nowoczesnych aplikacji dla chmury jest możliwość ciągłego dostarczania tych aplikacji. W tym artykule dowiesz się, jak zaimplementować potok pełnej ciągłej integracji i wdrażania (CI/CD) przy użyciu usługi Azure Container Service z usługą Docker Swarm, Azure Container Registry i zarządzanie potokami platformy Azure.


![Przykładowa aplikacja MyShop](./media/container-service-docker-swarm-setup-ci-cd/myshop-application.png)

Celem jest dostarczanie tej aplikacji w sposób ciągły w klastrze Docker Swarm przy użyciu usług Azure DevOps. Poniższy rysunek szczegóły tego potoku ciągłej dostawy:

![Przykładowa aplikacja MyShop](./media/container-service-docker-swarm-setup-ci-cd/full-ci-cd-pipeline.png)

Oto krótkie wyjaśnienie kroków:

1. Zmiany kodu są zaanektowane repozytorium kodu źródłowego (tutaj GitHub) 
1. GitHub wyzwala kompilację w usługach Azure DevOps 
1. Usługi DevOps platformy Azure pobiera najnowszą wersję źródeł i tworzy wszystkie obrazy, które tworzą aplikację 
1. Usługi DevOps platformy Azure wypychają każdy obraz do rejestru platformy Docker utworzonego przy użyciu usługi Azure Container Registry 
1. Usługi DevOps platformy Azure wyzwalają nową wersję 
1. Wersja uruchamia niektóre polecenia przy użyciu SSH w węźle głównym klastra usługi kontenera platformy Azure 
1. Docker Swarm w klastrze pobiera najnowszą wersję obrazów 
1. Nowa wersja aplikacji jest wdrażana przy użyciu docker compose 

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka należy wykonać następujące zadania:

- [Utworzenie klastra Swarm usługi kontenera platformy Azure](container-service-deployment.md)
- [Połączenie z klastrem Swarm w usłudze kontenera platformy Azure](../container-service-connect.md)
- [Tworzenie usługi Azure Container Registry](../../container-registry/container-registry-get-started-portal.md)
- [Utworzenie organizacji i projektu usługi Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [Rozwiń repozytorium GitHub na swoje konto GitHub](https://github.com/jcorioland/MyShop/)

[!INCLUDE [container-service-swarm-mode-note](../../../includes/container-service-swarm-mode-note.md)]

Potrzebujesz również komputera Ubuntu (14.04 lub 16.04) z zainstalowanym dockerem. Ta maszyna jest używana przez usługi Azure DevOps podczas procesów potoków platformy Azure. Jednym ze sposobów utworzenia tego komputera jest użycie obrazu dostępnego w portalu Azure Marketplace. 

## <a name="step-1-configure-your-azure-devops-services-organization"></a>Krok 1: Konfigurowanie organizacji usług Azure DevOps 

W tej sekcji skonfigurujesz organizację usług Azure DevOps Services.

### <a name="configure-an-azure-devops-services-linux-build-agent"></a>Konfigurowanie agenta kompilacji usług Azure DevOps w systemie Linux

Aby utworzyć obrazy platformy Docker i wypchnąć te obrazy do rejestru kontenerów platformy Azure z kompilacji usług Azure DevOps, musisz zarejestrować agenta systemu Linux. Dostępne są następujące opcje instalacji:

* [Wdrażanie agenta w systemie Linux](https://www.visualstudio.com/docs/build/admin/agents/v2-linux)

* [Uruchamianie agenta usług DevOps za pomocą platformy Docker](https://hub.docker.com/r/microsoft/vsts-agent)

### <a name="install-the-docker-integration-azure-devops-services-extension"></a>Instalowanie rozszerzenia usług Deweloper Usługi Azure Dla integracji platformy Docker

Firma Microsoft udostępnia rozszerzenie usług DevOps platformy Azure do pracy z platformą Docker w procesach potoków platformy Azure. To rozszerzenie jest dostępne w portalu [Azure DevOps Services Marketplace](https://marketplace.visualstudio.com/items?itemName=ms-vscs-rm.docker). Kliknij **przycisk Zainstaluj,** aby dodać to rozszerzenie do organizacji usług Azure DevOps Services:

![Instalowanie integracji platformy Docker](./media/container-service-docker-swarm-setup-ci-cd/install-docker-vsts.png)

Zostaniesz poproszony o połączenie się z organizacją usługi Azure DevOps services przy użyciu poświadczeń. 

### <a name="connect-azure-devops-services-and-github"></a>Łączenie usług DevOps platformy Azure i usługi GitHub

Skonfiguruj połączenie między projektem usługi Azure DevOps a kontem Usługi GitHub.

1. W projekcie usługi Azure DevOps Kliknij ikonę **Ustawienia** na pasku narzędzi i wybierz pozycję **Usługi**.

    ![Usługi Azure DevOps — połączenie zewnętrzne](./media/container-service-docker-swarm-setup-ci-cd/vsts-services-menu.png)

1. Po lewej stronie kliknij pozycję **Nowy punkt końcowy** > usługi**GitHub**.

    ![Usługi Azure DevOps — GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github.png)

1. Aby autoryzować usługi Azure DevOps do pracy z kontem Usługi GitHub, kliknij pozycję **Autoryzuj** i postępuj zgodnie z procedurą w oknie, które zostanie otwarte.

    ![Usługi Azure DevOps — autoryzowanie usługi GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-authorize.png)

### <a name="connect-azure-devops-services-to-your-azure-container-registry-and-azure-container-service-cluster"></a>Łączenie usług Azure DevOps z rejestrem kontenerów platformy Azure i klastrem usługi kontenerów platformy Azure

Ostatnie kroki przed wejściem do potoku ciągłej integracji/ciągłego wdrażania są skonfigurować połączenia zewnętrzne do rejestru kontenerów i klastra rój platformy Docker na platformie Azure. 

1. W ustawieniach **usług** projektu Usługi Azure DevOps Dodaj punkt końcowy usługi typu **Docker Registry**. 

1. W wyskakującym okienku wprowadź adres URL i poświadczenia rejestru kontenerów platformy Azure.

    ![Usługi DevOps platformy Azure — rejestr platformy Docker](./media/container-service-docker-swarm-setup-ci-cd/vsts-registry.png)

1. W przypadku klastra Rój platformy Docker dodaj punkt końcowy typu **SSH**. Następnie wprowadź informacje o połączeniu SSH klastra Swarm.

    ![Usługi Azure DevOps — SSH](./media/container-service-docker-swarm-setup-ci-cd/vsts-ssh.png)

Cała konfiguracja jest teraz wykonywana. W następnych krokach należy utworzyć potok ciągłej integracji/ciągłego wdrażania, który tworzy i wdraża aplikację w klastrze Roju platformy Docker. 

## <a name="step-2-create-the-build-pipeline"></a>Krok 2: Tworzenie potoku kompilacji

W tym kroku skonfigurujesz potok kompilacji dla projektu usługi Azure DevOps Services i zdefiniujesz przepływ pracy kompilacji dla obrazów kontenerów

### <a name="initial-pipeline-setup"></a>Początkowa konfiguracja rurociągu

1. Aby utworzyć potok kompilacji, połącz się z projektem usługi Azure DevOps Services i kliknij przycisk **Utwórz & wersji**. 

1. W sekcji **Definicje kompilacji** kliknij przycisk **+ Nowy**. Wybierz **pusty** szablon.

    ![Azure DevOps — nowy potok kompilacji](./media/container-service-docker-swarm-setup-ci-cd/create-build-vsts.png)

1. Skonfiguruj nową kompilację za pomocą źródła repozytorium GitHub, sprawdź **ciągłą integrację**i wybierz kolejkę agenta, w której zarejestrowano agenta systemu Linux. Kliknij **przycisk Utwórz,** aby utworzyć potok kompilacji.

    ![Usługi Azure DevOps — tworzenie potoku kompilacji](./media/container-service-docker-swarm-setup-ci-cd/vsts-create-build-github.png)

1. Na stronie **Definicje kompilacji** najpierw otwórz kartę **Repozytorium** i skonfiguruj kompilację tak, aby używała rozwidla projektu MyShop utworzonego w wymaganiach wstępnych. Upewnij się, że jako **domyślną gałąź**wybrano *acs-docs* .

    ![Usługi Azure DevOps — konfiguracja repozytorium kompilacji](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-repo-conf.png)

1. Na karcie **Wyzwalacze** skonfiguruj kompilację, która ma zostać wyzwolona po każdym zatwierdzeniu. Wybierz **opcję Ciągła integracja** i **zmiany wsadowe**.

    ![Usługi Azure DevOps — konfiguracja wyzwalacza kompilacji](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-trigger-conf.png)

### <a name="define-the-build-workflow"></a>Definiowanie przepływu pracy kompilacji
Następne kroki definiują przepływ pracy kompilacji. Istnieje pięć obrazów kontenerów do utworzenia dla aplikacji *MyShop.* Każdy obraz jest zbudowany przy użyciu pliku Dockerfile znajdującego się w folderach projektu:

* ProduktyApi
* Serwer proxy
* OcenyApi
* ZaleceniaApi
* Witryna sklepu

Należy dodać dwa kroki platformy Docker dla każdego obrazu, jeden do tworzenia obrazu i jeden do wypychania obrazu w rejestrze kontenerów platformy Azure. 

1. Aby dodać krok w przepływie pracy kompilacji, kliknij przycisk **+ Dodaj krok kompilacji** i wybierz pozycję **Docker**.

    ![Usługi Azure DevOps — dodawanie kroków kompilacji](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-add-task.png)

1. Dla każdego obrazu należy skonfigurować jeden `docker build` krok, który używa polecenia.

    ![Usługi Azure DevOps — kompilacja platformy Docker](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-build.png)

    Dla operacji kompilacji wybierz rejestr kontenerów platformy Azure, akcja **kompilacji obrazu** i plik Dockerfile, który definiuje każdy obraz. Ustaw **kontekst kompilacji** jako katalog główny dockerfile i zdefiniuj **nazwę obrazu**. 
    
    Jak pokazano na poprzednim ekranie, uruchom nazwę obrazu z identyfikatorem URI rejestru kontenerów platformy Azure. (Można również użyć zmiennej kompilacji do parametryzacji znacznika obrazu, takiego jak identyfikator kompilacji w tym przykładzie).

1. Dla każdego obrazu należy skonfigurować drugi `docker push` krok, który używa polecenia.

    ![Usługi Azure DevOps — wypychanie docker](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-push.png)

    Dla operacji wypychania wybierz rejestr kontenerów platformy Azure, **akcję Wypychania obrazu** i wprowadź **nazwę obrazu,** która jest wbudowana w poprzednim kroku.

1. Po skonfigurowaniu kroki kompilacji i wypychania dla każdego z pięciu obrazów, dodaj dwa kolejne kroki w przepływie pracy kompilacji.

    a. Zadanie wiersza polecenia, które używa skryptu bash do zastąpienia wystąpienia *BuildNumber* w pliku docker-compose.yml bieżącym identyfikatorem kompilacji. Szczegółowe informacje można znaleźć na poniższym ekranie.

    ![Usługi DevOps platformy Azure — aktualizowanie pliku compose](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-replace-build-number.png)

    b. Zadanie, które porzuca zaktualizowany plik compose jako artefakt kompilacji, dzięki czemu może być używany w wydaniu. Szczegółowe informacje można znaleźć na poniższym ekranie.

    ![Usługi DevOps platformy Azure — publikuj plik compose](./media/container-service-docker-swarm-setup-ci-cd/vsts-publish-compose.png) 

1. Kliknij **przycisk Zapisz** i nadaj nazwę potokowi kompilacji.

## <a name="step-3-create-the-release-pipeline"></a>Krok 3: Tworzenie potoku wydania

Usługi Azure DevOps umożliwiają [zarządzanie wersjami w różnych środowiskach.](https://www.visualstudio.com/team-services/release-management/) Można włączyć ciągłe wdrażanie, aby upewnić się, że aplikacja jest wdrażana w różnych środowiskach (takich jak dev, test, pre-production i produkcji) w sposób płynny. Można utworzyć nowe środowisko, które reprezentuje klaster usługi Azure Container Service Docker Swarm.

![Usługi Azure DevOps — zwolnij do usługi ACS](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Konfiguracja wydania początkowego

1. Aby utworzyć potok wydań, kliknij pozycję **Wydania** > **+ Zwolnij**

1. Aby skonfigurować źródło artefaktu, kliknij pozycję **Artefakty** > **Połącz źródło artefaktu**. W tym miejscu połącz ten nowy potok wydania z kompilacją zdefiniowaną w poprzednim kroku. W ten sposób plik docker-compose.yml jest dostępny w procesie wydania.

    ![Usługi Azure DevOps — artefakty wersji](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-artefacts.png) 

1. Aby skonfigurować wyzwalacz wydania, kliknij pozycję **Wyzwalacze** i wybierz pozycję **Ciągłe wdrażanie**. Ustaw wyzwalacz na tym samym źródle artefaktu. To ustawienie gwarantuje, że nowa wersja zostanie rozpocznie się natychmiast po pomyślnym zakończeniu kompilacji.

    ![Usługi Azure DevOps — wyzwalacze wersji](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-trigger.png) 

### <a name="define-the-release-workflow"></a>Definiowanie przepływu pracy wydania

Przepływ pracy wydania składa się z dwóch zadań, które można dodać.

1. Skonfiguruj zadanie, aby bezpiecznie skopiować plik redagowania do *folderu wdrażania* w węźle głównym Roju platformy Docker, przy użyciu wcześniej skonfigurowanego połączenia SSH. Szczegółowe informacje można znaleźć na poniższym ekranie.

    ![Usługi Azure DevOps — wydanie protokołu SCP](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-scp.png)

1. Skonfiguruj drugie zadanie do `docker` wykonania `docker-compose` polecenia bash do uruchamiania i poleceń w węźle głównym. Szczegółowe informacje można znaleźć na poniższym ekranie.

    ![Usługi Azure DevOps — program Release Bash](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-bash.png)

    Polecenie wykonywane na wzorcu używa interfejsu wiersza polecenia platformy Docker i interfejsu wiersza polecenia docker-compose do wykonywania następujących zadań:

   - Zaloguj się do rejestru kontenerów platformy Azure (używa trzech variab'les kompilacji, które są zdefiniowane w **zmiennych** kartę)
   - Zdefiniuj zmienną **DOCKER_HOST** do pracy z punktem końcowym Roju (:2375)
   - Przejdź do folderu *wdrażania* utworzonego przez poprzednie zadanie bezpiecznej kopii, który zawiera plik docker-compose.yml 
   - Wykonaj `docker-compose` polecenia, które ściągają nowe obrazy, zatrzymują usługi, usuwają usługi i tworzą kontenery.

     >[!IMPORTANT]
     > Jak pokazano na poprzednim ekranie, pole wyboru **Niepowodzenie na STDERR** nie jest zaznaczone. Jest to ważne ustawienie, ponieważ `docker-compose` drukuje kilka komunikatów diagnostycznych, takich jak kontenery są zatrzymywane lub usuwane, na standardowy błąd wyjściowy. Jeśli zaznaczysz to pole wyboru, usługi Azure DevOps services zgłasza, że wystąpiły błędy podczas wydania, nawet jeśli wszystko pójdzie dobrze.
     >
1. Zapisz ten nowy potok wydania.


>[!NOTE]
>To wdrożenie obejmuje pewne przestoje, ponieważ zatrzymujemy stare usługi i uruchamiamy nowe. Można tego uniknąć, wykonując wdrożenie niebiesko-zielone.
>

## <a name="step-4-test-the-cicd-pipeline"></a>Krok 4. Testowanie potoku ciągłej integracji/ciągłego wdrażania

Teraz, gdy skończysz z konfiguracją, nadszedł czas, aby przetestować ten nowy potok ciągłej integracji/ciągłego wdrażania. Najprostszym sposobem przetestowania go jest zaktualizowanie kodu źródłowego i zatwierdzenie zmian w repozytorium GitHub. Kilka sekund po wypchnięciu kodu zostanie wyświetlona nowa kompilacja uruchomiona w usługach Azure DevOps. Po pomyślnym zakończeniu zostanie wyzwolona nowa wersja i wdroży nową wersję aplikacji w klastrze usługi Azure Container Service.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat ciągłej integracji/ciągłego wdrażania za pomocą usług DevOps azure, zobacz artykuł [Dokumentacja potoków platformy Azure.](/azure/devops/pipelines/?view=azure-devops)

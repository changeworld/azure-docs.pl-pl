---
title: CI/CD z aparatem usługi kontenera platformy Azure i tryb Swarm
description: Użyć aparat usługi kontenera platformy Azure z Docker Swarm tryb rejestru kontenera Azure i Visual Studio Team Services w celu dostarczenia stale aplikacji .NET Core wielu kontenera
services: container-service
author: diegomrtnzg
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/27/2017
ms.author: diegomrtnzg
ms.custom: mvc
ms.openlocfilehash: 01126f3eef988eb1787bafea92e7384aad1a703c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32179575"
---
# <a name="full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-acs-engine-and-docker-swarm-mode-using-visual-studio-team-services"></a>Pełny potok CI/CD do wdrażania aplikacji usługi kontenera w usłudze kontenera platformy Azure z aparatem ACS i Docker Swarm trybie przy użyciu programu Visual Studio Team Services

*Ten artykuł jest oparty na [CI pełnej/CD potoku do wdrażania aplikacji usługi kontenera w usłudze kontenera platformy Azure przy użyciu rozwiązania Docker Swarm przy użyciu programu Visual Studio Team Services](container-service-docker-swarm-setup-ci-cd.md) dokumentacji*

Dzisiaj jedną z największych wyzwań podczas tworzenia nowoczesnych aplikacji w chmurze jest możliwość ciągłego dostarczania tych aplikacji. W tym artykule dowiesz się implementowania pełnej ciągłej integracji i wdrażania (CI/CD) potoku przy użyciu: 
* Aparat usługi kontenera platformy Azure z Docker Swarm trybem
* Azure Container Registry
* Visual Studio Team Services

Ten artykuł jest oparty na prostej aplikacji, dostępnych na [GitHub](https://github.com/jcorioland/MyShop/tree/docker-linux), rozwinięte z platformy ASP.NET Core. Aplikacja składa się z czterech różnych usług: trzy sieci web, interfejsów API i frontonu sieci web co:

![MyShop przykładowej aplikacji](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/myshop-application.png)

Celem jest stale świadczenia tej aplikacji w klastrze Docker Swarm tryb, za pomocą programu Visual Studio Team Services. Poniższa ilustracja szczegóły tego potoku ciągłego dostarczania:

![MyShop przykładowej aplikacji](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/full-ci-cd-pipeline.png)

Poniżej przedstawiono krótki opis kroków:

1. Zmiany kodu zostały zastosowane w repozytorium kodu źródłowego (tutaj GitHub) 
2. GitHub uaktywnia kompilację w Visual Studio Team Services 
3. Visual Studio Team Services pobiera najnowsza wersja źródła i tworzy wszystkie obrazy, które tworzą aplikacji 
4. Visual Studio Team Services wypychanie każdego obrazu do rejestru Docker utworzone za pomocą usługi Azure kontenera rejestru 
5. Visual Studio Team Services wyzwala nową wersją 
6. Wersja uruchamia niektóre polecenia przy użyciu protokołu SSH w węźle głównym klastra usługi kontenera platformy Azure 
7. Tryb docker Swarm w klastrze pobiera najnowsza wersja obrazów 
8. Wdrożenie nowej wersji aplikacji za pomocą Docker stosu 

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka, należy wykonać następujące zadania:

- [Tworzenie klastra trybu Swarm usługi kontenera platformy Azure z aparatem ACS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acsengine-swarmmode)
- [Połączenie z klastrem Swarm w usłudze Azure Container Service](../container-service-connect.md)
- [Tworzenie rejestru kontenera platformy Azure](../../container-registry/container-registry-get-started-portal.md)
- [Utworzony projekt konta i zespołu Visual Studio Team Services](https://www.visualstudio.com/en-us/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services)
- [Rozwidlenia repozytorium GitHub z kontem usługi GitHub](https://github.com/jcorioland/MyShop/tree/docker-linux)

>[!NOTE]
> Koordynator rozwiązania Docker Swarm w usłudze Azure Container Service korzysta ze starszego autonomicznego rozwiązania Swarm. Obecnie zintegrowany [tryb Swarm](https://docs.docker.com/engine/swarm/) (na platformie Docker 1.12 lub nowszej) nie jest obsługiwanym koordynatorem w usłudze Azure Container Service. Z tego powodu używamy [aparat ACS](https://github.com/Azure/acs-engine/blob/master/docs/swarmmode.md), przyczyniły się społeczności [szablon szybkiego startu](https://azure.microsoft.com/resources/templates/101-acsengine-swarmmode/), lub rozwiązania Docker w [portalu Azure Marketplace](https://azuremarketplace.microsoft.com).
>

## <a name="step-1-configure-your-visual-studio-team-services-account"></a>Krok 1: Skonfiguruj konto usługi Visual Studio Team Services 

W tej sekcji należy skonfigurować konto w Visual Studio Team Services. Kliknij, aby skonfigurować punkty końcowe usługi VSTS, projektu Visual Studio Team Services **ustawienia** ikonę na pasku narzędzi, a następnie wybierz **usług**.

![Punkt końcowy usługi otwarte](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/services-vsts.PNG)

### <a name="connect-visual-studio-team-services-and-azure-account"></a>Połącz konto usługi Visual Studio Team Services i platformy Azure

Skonfiguruj połączenie między projektu programu VSTS i konta platformy Azure.

1. Po lewej stronie, kliknij przycisk **nowy punkt końcowy usługi** > **usługi Azure Resource Manager**.
2. Aby autoryzować VSTS do pracy z konta platformy Azure, wybierz użytkownika **subskrypcji** i kliknij przycisk **OK**.

    ![Visual Studio Team Services — autoryzować Azure](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-azure.PNG)

### <a name="connect-visual-studio-team-services-and-github"></a>Połączenie usługi Visual Studio Team Services i GitHub

Skonfiguruj połączenie między projektu programu VSTS oraz konto GitHub.

1. Po lewej stronie, kliknij przycisk **nowy punkt końcowy usługi** > **GitHub**.
2. Aby autoryzować VSTS do pracy z Twoim kontem usługi GitHub, kliknij przycisk **autoryzacji** i postępuj zgodnie z procedurą w otwartym oknie.

    ![Visual Studio Team Services — autoryzować GitHub](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github.png)

### <a name="connect-vsts-to-your-azure-container-service-cluster"></a>VSTS nawiązać połączenia z klastrem usługi kontenera platformy Azure

Ostatni kroki przed pobraniem z potokiem CI/CD są do konfigurowania połączeń zewnętrznych z klastrem Docker Swarm w usłudze Azure. 

1. Aby klaster Docker Swarm Dodawanie punktu końcowego typu **SSH**. Następnie wprowadź informacje o połączeniu SSH klastra Swarm (węzeł główny).

    ![Visual Studio Team Services — SSH](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-ssh.png)

Całą konfigurację odbywa się teraz. W następnych krokach utworzysz potok CI/CD, który tworzy i wdraża aplikację w klastrze Docker Swarm. 

## <a name="step-2-create-the-build-definition"></a>Krok 2: Tworzenie definicji kompilacji

W tym kroku skonfigurować definicję kompilacji projektu programu VSTS i zdefiniuj przepływu pracy kompilacji dla obrazów kontenera

### <a name="initial-definition-setup"></a>Definicja początkowej instalacji

1. Aby utworzyć definicję kompilacji, nawiązać połączenia z projektu programu Visual Studio Team Services, a następnie kliknij przycisk **kompilacji i wydania**. W **definicje kompilacji** kliknij **+ nowy**. 

    ![Visual Studio Team Services — nowej definicji kompilacji](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-build-vsts.PNG)

2. Wybierz **pusta procesu**.

    ![Definicja kompilacji programu Visual Studio Team Services — nowy pusty](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-empty-build-vsts.PNG)

4. Następnie kliknij przycisk **zmienne** karcie i Utwórz dwa nowe zmienne: **RegistryURL** i **AgentURL**. Wklej wartości rejestru i DNS agentów klastra.

    ![Visual Studio Team Services — Konfiguracja zmienne kompilacji](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-variables.png)

5. Na **definicji kompilacji** Otwórz **wyzwalaczy** i skonfiguruj kompilację do korzystania z rozwidlenie projektu MyShop utworzonego w wymaganiach wstępnych ciągłej integracji. Następnie wybierz opcję **partii zmian**. Upewnij się, że wybrano *docker linux* jako **gałęzi specyfikacji**.

    ![Visual Studio Team Services — Konfiguracja repozytorium kompilacji](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github-repo-conf.PNG)


6. Na koniec kliknij **opcje** i skonfiguruj domyślną agenta w kolejce **Podgląd Linux hostowanych**.

    ![Visual Studio Team Services — konfiguracja agenta hosta](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-agent.png)

### <a name="define-the-build-workflow"></a>Zdefiniuj przepływu pracy kompilacji
Następne kroki zdefiniuj przepływu pracy kompilacji. Najpierw należy skonfigurować źródła kodu. Aby wykonać to zadanie, zaznacz **GitHub** i **repozytorium** i **gałęzi** (docker linux).

![Konfigurowanie programu Visual Studio Team Services — kodu źródłowego](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-source-code.png)

Istnieje pięć obrazów kontenera do kompilacji dla *MyShop* aplikacji. Obraz jest utworzony przy użyciu plik Dockerfile znajdujący się w folderze projektu:

* ProductsApi
* Serwer proxy
* RatingsApi
* RecommandationsApi
* Sklepu

Należy dwa kroki Docker dla każdego obrazu: jeden do tworzenia obrazu i jeden do dystrybuowania obrazu w rejestrze kontenera platformy Azure. 

1. Aby dodać krok w przepływie pracy kompilacji, kliknij przycisk **+ Dodaj krok kompilacji** i wybierz **Docker**.

    ![Visual Studio Team Services — dodać kroki procesu kompilacji](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-add-task.png)

2. Dla każdego obrazu, należy skonfigurować jeden krok, który używa `docker build` polecenia.

    ![Visual Studio Team Services — Docker kompilacji](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-build.png)

    Dla operacji kompilacji, wybierz rejestru kontenera platformy Azure, **kompilacji obrazu** akcji, a plik Dockerfile, który definiuje każdego obrazu. Ustaw **katalog roboczy** jako katalog główny plik Dockerfile, zdefiniuj **nazwa obrazu**i wybierz **zawierają najnowsze tagu**.
    
    Nazwa obrazu musi być w następującym formacie: ```$(RegistryURL)/[NAME]:$(Build.BuildId)```. Zastąp **[nazwa]** o nazwie obrazu:
    - ```proxy```
    - ```products-api```
    - ```ratings-api```
    - ```recommendations-api```
    - ```shopfront```

3. Dla każdego obrazu, skonfiguruj drugi krok, który używa `docker push` polecenia.

    ![Visual Studio Team Services — Docker Push](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-push.png)

    Dla operacji wypychania, wybierz rejestru kontenera platformy Azure, **Push obrazu** akcji, wprowadź **nazwa obrazu** wbudowane w poprzednim kroku, a następnie wybierz **zawierają najnowsze Tag**.

4. Po skonfigurowaniu kroków kompilacji i wypychania dla każdego z pięciu obrazów, należy dodać trzy kolejne kroki w przepływie pracy kompilacji.

   ![Visual Studio Team Services — Dodaj zadania wiersza polecenia](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-command-task.png)

      1. Zadania wiersza polecenia, który używa skryptów bash w celu zastąpienia *RegistryURL* wystąpienie w plik docker-compose.yml ze zmienną RegistryURL. 
    
          ```-c "sed -i 's/RegistryUrl/$(RegistryURL)/g' src/docker-compose-v3.yml"```

          ![Visual Studio Team Services — plik Redaguj aktualizacji z adresem URL rejestru](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-replace-registry.png)

      2. Zadania wiersza polecenia, który używa skryptów bash w celu zastąpienia *AgentURL* wystąpienie w plik docker-compose.yml ze zmienną AgentURL.
  
          ```-c "sed -i 's/AgentUrl/$(AgentURL)/g' src/docker-compose-v3.yml"```

     3. Zadanie porzuca zaktualizowany plik Redaguj jako artefaktów kompilacji dzięki mogą być używane w wersji. Zobacz następujący ekran, aby uzyskać szczegółowe informacje.

         ![Visual Studio Team Services — Opublikuj artefaktów](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish.png) 

         ![Usługi Visual Studio Team - publikowania tworzenia pliku](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish-compose.png) 

5. Kliknij przycisk **Zapisz & kolejka** do testowania definicję kompilacji.

   ![Visual Studio Team Services — zapisywanie i kolejki](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-save.png) 

   ![Visual Studio Team Services — nowej kolejki](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-queue.png) 

6. Jeśli **kompilacji** jest poprawna, masz wyświetlony następujący ekran:

  ![Visual Studio Team Services — kompilacja powiodła się](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-succeeded.png) 

## <a name="step-3-create-the-release-definition"></a>Krok 3: Tworzenie definicji wersji

Visual Studio Team Services umożliwia [zarządzania wersjami w środowiskach](https://www.visualstudio.com/team-services/release-management/). Można włączyć ciągłego wdrażania upewnić się, że aplikacja jest wdrożony na Twoje różnych środowiskach (np. dev, test produkcji wstępnej i produkcji) w sposób. Można utworzyć środowisko, które reprezentuje klastra tryb Azure kontenera usługi Docker Swarm.

![Visual Studio Team Services — w wersji usług ACS](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Początkowa wersja Instalatora

1. Do tworzenia definicji wersji, kliknij przycisk **wersje** > **+ zlecenia**

2. Kliknij, aby skonfigurować źródła artefaktu **artefakty** > **połączenia źródła artefaktu**. W tym miejscu połączyć tej nowej wersji definicji kompilacji, który został zdefiniowany w poprzednim kroku. Po wykonaniu tej plik docker-compose.yml jest dostępna w procesie wersji.

    ![Visual Studio Team Services - wersja artefaktów](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-artefacts.png) 

3. Kliknij, aby skonfigurować wyzwalacz wersji **wyzwalaczy** i wybierz **ciągłe wdrażanie**. W tym samym źródle artefaktu, należy ustawić wyzwalacz. To ustawienie zapewnia, że rozpoczyna się nowej wersji, gdy kompilacja zakończy się pomyślnie.

    ![Visual Studio Team Services — wyzwalaczy zlecenia](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-trigger.png) 

4. Kliknij, aby skonfigurować zmienne wersji **zmienne** i wybierz **+ zmiennej** utworzyć trzy nowe zmienne z użyciem informacji rejestru: **docker.username**, **docker.password**, i **docker.registry**. Wklej wartości rejestru i DNS agentów klastra.

    ![Visual Studio Team Services — Konfiguracja repozytorium kompilacji](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-variables.png)

    >[!IMPORTANT]
    > Jak pokazano na poprzednich ekranu, kliknij przycisk **blokady** checkbox w docker.password. To ustawienie jest ważne ograniczyć hasło.
    >

### <a name="define-the-release-workflow"></a>Definiowanie wersji przepływu pracy

Wersja przepływ pracy składa się z dwóch zadań, które można dodać.

1. Skonfigurować zadania bezpiecznie skopiować plik Redaguj *wdrażanie* folder w rozwiązaniu Docker Swarm węzła głównego, przy użyciu połączenia SSH został wcześniej skonfigurowany. Zobacz następujący ekran, aby uzyskać szczegółowe informacje.
    
    Folder źródłowy: ```$(System.DefaultWorkingDirectory)/MyShop-CI/drop```

    ![Visual Studio Team Services - wersja punkt połączenia usługi](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-scp.png)

2. Skonfiguruj drugi zadań do wykonania polecenia bash, aby uruchomić `docker` i `docker stack deploy` poleceń w węźle głównym. Zobacz następujący ekran, aby uzyskać szczegółowe informacje.

    ```docker login -u $(docker.username) -p $(docker.password) $(docker.registry) && export DOCKER_HOST=:2375 && cd deploy && docker stack deploy --compose-file docker-compose-v3.yml myshop --with-registry-auth```

    ![Visual Studio Team Services — Bash zlecenia](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-bash.png)

    Polecenie wykonane na wzorcu używa interfejsu wiersza polecenia Docker i interfejsu wiersza polecenia rozwiązania Docker Compose do wykonywania następujących zadań:

    - Zaloguj się do rejestru kontenera platformy Azure (używa trzech zmiennych kompilacji, które są zdefiniowane w **zmienne** kartę)
    - Zdefiniuj **DOCKER_HOST** zmienną do pracy z punktu końcowego Swarm (: 2375)
    - Przejdź do *wdrażanie* folderu, który został utworzony przez poprzednie zadanie kopiowania bezpiecznego i który zawiera plik docker-compose.yml 
    - Wykonanie `docker stack deploy` poleceń ściągnięcia nowych obrazów i utworzyć kontenerów.

    >[!IMPORTANT]
    > Jak pokazano na poprzednich ekranu, pozostaw **zakończyć się niepowodzeniem ze strumienia STDERR** niezaznaczone pole wyboru. To ustawienie umożliwia firmie Microsoft w celu ukończenia procesu wersji ze względu na `docker-compose` wyświetla komunikaty diagnostyczne, takie jak kontenery są zatrzymywanie lub usuwane, dane wyjściowe błędów. Jeśli pole wyboru Visual Studio Team Services raporty błędów, które wystąpiły podczas wersji, nawet jeśli wszystko przebiegnie poprawnie.
    >
3. Zapisz ten nowej definicji wersji.

## <a name="step-4-test-the-cicd-pipeline"></a>Krok 4: Testowanie potoku CI/CD

Teraz, po zakończeniu konfiguracji, nadszedł czas na test ten nowy potok CI/CD. Najprostszym sposobem, aby ją przetestować jest zaktualizuj kod źródłowy i zatwierdzić zmiany do repozytorium GitHub. Kilka sekund po naciśnięciu kodu, zobaczysz nowej kompilacji uruchomionych w Visual Studio Team Services. Po pomyślnym ukończeniu nowej wersji wyzwoleniu i wdrożeniu nowej wersji aplikacji w klastrze usługi kontenera platformy Azure.

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać więcej informacji na temat CI/CD z Visual Studio Team Services, zobacz [kompilacji programu VSTS omówienie](https://www.visualstudio.com/docs/build/overview).
* Aby uzyskać więcej informacji na temat aparatu ACS, zobacz [repozytorium GitHub aparat ACS](https://github.com/Azure/acs-engine).
* Aby uzyskać więcej informacji o trybie Docker Swarm, zobacz [Docker Swarm Omówienie trybu](https://docs.docker.com/engine/swarm/).

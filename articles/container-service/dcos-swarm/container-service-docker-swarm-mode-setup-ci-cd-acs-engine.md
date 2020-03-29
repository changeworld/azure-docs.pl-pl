---
title: (PRZESTARZAŁE) Ciągła integracja/dysk CD z aparatem usługi kontenerów platformy Azure i trybem roju
description: Użyj aparatu azure container service engine z trybem roju platformy Docker, rejestrem kontenerów platformy Azure i programem Azure DevOps, aby stale dostarczać wielokontensjapikonujną aplikację .NET Core
author: diegomrtnzg
ms.service: container-service
ms.topic: conceptual
ms.date: 05/27/2017
ms.author: dimart
ms.custom: mvc
ms.openlocfilehash: 1ec7ece6f5afd1bbd2613ae08af04b82e8a156b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277917"
---
# <a name="deprecated-full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-acs-engine-and-docker-swarm-mode-using-azure-devops"></a>(PRZESTARZAŁE) Pełny potok ciągłej integracji/ciągłego wdrażania w celu wdrożenia aplikacji wielo kontenerowej w usłudze kontenera azure z aparatem ACS i trybem roju platformy Docker przy użyciu usługi Azure DevOps

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

*Ten artykuł jest oparty na [potoku pełnej ciągłej integracji/ciągłego wdrażania, aby wdrożyć aplikację wielu kontenerów w usłudze Azure Container Service z usługą Docker Swarm przy użyciu dokumentacji programu Azure DevOps](container-service-docker-swarm-setup-ci-cd.md)*

Obecnie jednym z największych wyzwań przy opracowywaniu nowoczesnych aplikacji dla chmury jest możliwość ciągłego dostarczania tych aplikacji. W tym artykule dowiesz się, jak zaimplementować potok pełnej ciągłej integracji i wdrażania (CI/CD) przy użyciu: 
* Aparat usługi kontenera azure z trybem roju platformy Docker
* Azure Container Registry
* Azure DevOps


![Przykładowa aplikacja MyShop](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/myshop-application.png)

Celem jest dostarczanie tej aplikacji w sposób ciągły w klastrze w trybie roju platformy Docker przy użyciu usługi Azure DevOps. Poniższy rysunek szczegóły tego potoku ciągłej dostawy:

![Przykładowa aplikacja MyShop](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/full-ci-cd-pipeline.png)

Oto krótkie wyjaśnienie kroków:

1. Zmiany kodu są zaanektowane repozytorium kodu źródłowego (tutaj GitHub) 
2. GitHub wyzwala kompilację w usłudze Azure DevOps 
3. Usługa Azure DevOps pobiera najnowszą wersję źródeł i tworzy wszystkie obrazy, które tworzą aplikację 
4. Usługa Azure DevOps wypycha każdy obraz do rejestru platformy Docker utworzonego przy użyciu usługi Azure Container Registry 
5. Usługa Azure DevOps wyzwala nową wersję 
6. Wersja uruchamia niektóre polecenia przy użyciu SSH w węźle głównym klastra usługi kontenera platformy Azure 
7. Tryb rój docker na klastrze pobiera najnowszą wersję obrazów 
8. Nowa wersja aplikacji jest wdrażana przy użyciu stosu platformy Docker 

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka należy wykonać następujące zadania:

- [Tworzenie klastra trybu roju w usłudze kontenera azure za pomocą aparatu ACS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acsengine-swarmmode)
- [Połączenie z klastrem Swarm w usłudze kontenera platformy Azure](../container-service-connect.md)
- [Tworzenie usługi Azure Container Registry](../../container-registry/container-registry-get-started-portal.md)
- [Tworzenie organizacji i projektu usługi Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [Rozwiń repozytorium GitHub na swoje konto GitHub](https://github.com/jcorioland/MyShop/tree/docker-linux)

>[!NOTE]
> Koordynator rozwiązania Docker Swarm w usłudze Azure Container Service korzysta ze starszego autonomicznego rozwiązania Swarm. Obecnie zintegrowany [tryb Swarm](https://docs.docker.com/engine/swarm/) (na platformie Docker 1.12 lub nowszej) nie jest obsługiwanym koordynatorem w usłudze Azure Container Service. Z tego powodu używamy [acs engine,](https://github.com/Azure/acs-engine/blob/master/docs/swarmmode.md) [szablonu szybkiego startu](https://azure.microsoft.com/resources/templates/101-acsengine-swarmmode/)współtworzonego przez społeczność lub rozwiązania platformy Docker w [portalu Azure Marketplace.](https://azuremarketplace.microsoft.com)
>

## <a name="step-1-configure-your-azure-devops-organization"></a>Krok 1: Konfigurowanie organizacji usługi Azure DevOps 

W tej sekcji można skonfigurować organizację Azure DevOps. Aby skonfigurować punkty końcowe usług Azure DevOps, w projekcie usługi Azure DevOps kliknij ikonę **Ustawienia** na pasku narzędzi i wybierz pozycję **Usługi**.

![Punkt końcowy otwartych usług](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/services-vsts.PNG)

### <a name="connect-azure-devops-and-azure-account"></a>Łączenie konta azure devops i platformy Azure

Skonfiguruj połączenie między projektem Usługi Azure DevOps a kontem platformy Azure.

1. Po lewej stronie kliknij pozycję **Nowy punkt końcowy** > usługi**Azure Resource Manager**.
2. Aby autoryzować usługę Azure DevOps do pracy z kontem platformy Azure, wybierz **subskrypcję** i kliknij **przycisk OK**.

    ![Azure DevOps — autoryzowanie platformy Azure](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-azure.PNG)

### <a name="connect-azure-devops-and-github"></a>Łączenie usług Azure DevOps i github

Skonfiguruj połączenie między projektem usługi Azure DevOps a kontem Usługi GitHub.

1. Po lewej stronie kliknij pozycję **Nowy punkt końcowy** > usługi**GitHub**.
2. Aby autoryzować usługę Azure DevOps do pracy z kontem Usługi GitHub, kliknij pozycję **Autoryzuj** i postępuj zgodnie z procedurą w oknie, które zostanie otwarte.

    ![Azure DevOps — autoryzowanie usługi GitHub](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github.png)

### <a name="connect-azure-devops-to-your-azure-container-service-cluster"></a>Łączenie usługi Azure DevOps z klastrem usługi kontenerów platformy Azure

Ostatnie kroki przed wejściem do potoku ciągłej integracji/ciągłego wdrażania mają na celu skonfigurowanie połączeń zewnętrznych z klastrem Roju platformy Docker na platformie Azure. 

1. W przypadku klastra Rój platformy Docker dodaj punkt końcowy typu **SSH**. Następnie wprowadź informacje o połączeniu SSH klastra Swarm (węzeł główny).

    ![Azure DevOps — SSH](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-ssh.png)

Cała konfiguracja jest teraz wykonywana. W następnych krokach należy utworzyć potok ciągłej integracji/ciągłego wdrażania, który tworzy i wdraża aplikację w klastrze Roju platformy Docker. 

## <a name="step-2-create-the-build-pipeline"></a>Krok 2: Tworzenie potoku kompilacji

W tym kroku skonfigurujesz potok kompilacji dla projektu Azure DevOps i zdefiniuj przepływ pracy kompilacji dla obrazów kontenerów

### <a name="initial-pipeline-setup"></a>Początkowa konfiguracja rurociągu

1. Aby utworzyć potok kompilacji, połącz się z projektem programu Azure DevOps i kliknij przycisk **Utwórz & wersji**. W sekcji **Definicje kompilacji** kliknij przycisk **+ Nowy**. 

    ![Azure DevOps — nowy potok kompilacji](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-build-vsts.PNG)

2. Wybierz **pusty proces**.

    ![Azure DevOps — nowy potok pustych kompilacji](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-empty-build-vsts.PNG)

4. Następnie kliknij kartę **Zmienne** i utwórz dwie nowe zmienne: **RegistryURL** i **AgentURL**. Wklej wartości systemu DNS registry i cluster agents.

    ![Azure DevOps — konfiguracja zmiennych kompilacji](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-variables.png)

5. Na stronie **Definicje kompilacji** otwórz kartę **Wyzwalacze** i skonfiguruj kompilację tak, aby używała ciągłej integracji z rozwidlą projektu MyShop utworzonego w wymaganiach wstępnych. Następnie wybierz pozycję **Zmiany partii**. Upewnij się, że wybrano *docker-linux* jako **specyfikację oddziału**.

    ![Azure DevOps — konfiguracja repozytorium kompilacji](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github-repo-conf.PNG)


6. Na koniec kliknij kartę **Opcje** i skonfiguruj domyślną kolejkę agenta na **hostowany system Linux Preview**.

    ![Azure DevOps — konfiguracja agenta hosta](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-agent.png)

### <a name="define-the-build-workflow"></a>Definiowanie przepływu pracy kompilacji
Następne kroki definiują przepływ pracy kompilacji. Najpierw należy skonfigurować źródło kodu. Aby to zrobić, wybierz **GitHub** i **repozytorium** i **oddział** (docker-linux).

![Azure DevOps — konfigurowanie źródła kodu](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-source-code.png)

Istnieje pięć obrazów kontenerów do utworzenia dla aplikacji *MyShop.* Każdy obraz jest zbudowany przy użyciu pliku Dockerfile znajdującego się w folderach projektu:

* ProduktyApi
* Serwer proxy
* OcenyApi
* ZaleceniaApi
* Witryna sklepu

Potrzebujesz dwóch kroków platformy Docker dla każdego obrazu, jeden do utworzenia obrazu i jeden do wypychania obrazu w rejestrze kontenerów platformy Azure. 

1. Aby dodać krok w przepływie pracy kompilacji, kliknij przycisk **+ Dodaj krok kompilacji** i wybierz pozycję **Docker**.

    ![Azure DevOps — dodawanie kroków kompilacji](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-add-task.png)

2. Dla każdego obrazu należy skonfigurować jeden `docker build` krok, który używa polecenia.

    ![Azure DevOps — kompilacja platformy Docker](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-build.png)

    Dla operacji kompilacji wybierz rejestr kontenera platformy Azure, akcja **kompilacji obrazu** i plik Dockerfile, który definiuje każdy obraz. Ustaw **katalog roboczy** jako katalog główny Dockerfile, zdefiniuj **nazwę obrazu**i wybierz **pozycję Dołącz najnowszy znacznik**.
    
    Nazwa obrazu musi być w ```$(RegistryURL)/[NAME]:$(Build.BuildId)```tym formacie: . Zastąp **[NAZWA]** nazwą obrazu:
    - ```proxy```
    - ```products-api```
    - ```ratings-api```
    - ```recommendations-api```
    - ```shopfront```

3. Dla każdego obrazu należy skonfigurować drugi `docker push` krok, który używa polecenia.

    ![Azure DevOps — wypychanie docker](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-push.png)

    W przypadku operacji wypychania wybierz rejestr kontenerów platformy Azure, akcję **Wypychanie obrazu,** wprowadź **nazwę obrazu,** która jest wbudowana w poprzednim kroku i wybierz **pozycję Dołącz najnowszy znacznik**.

4. Po skonfigurowaniu kroki kompilacji i wypychania dla każdego z pięciu obrazów, dodaj trzy kolejne kroki w przepływie pracy kompilacji.

   ![Azure DevOps — dodawanie zadania wiersza polecenia](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-command-task.png)

   1. Zadanie wiersza polecenia, które używa skryptu bash do zastąpienia wystąpienia *RegistryURL* w pliku docker-compose.yml zmienną RegistryURL. 
    
       ```-c "sed -i 's/RegistryUrl/$(RegistryURL)/g' src/docker-compose-v3.yml"```

       ![Azure DevOps — aktualizowanie pliku reduzytu z adresem URL rejestru](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-replace-registry.png)

   2. Zadanie wiersza polecenia, które używa skryptu bash do zastąpienia wystąpienia *AgentURL* w pliku docker-compose.yml zmienną AgentURL.
  
       ```-c "sed -i 's/AgentUrl/$(AgentURL)/g' src/docker-compose-v3.yml"```

      1. Zadanie, które porzuca zaktualizowany plik compose jako artefakt kompilacji, dzięki czemu może być używany w wydaniu. Szczegółowe informacje można znaleźć na poniższym ekranie.

      ![Azure DevOps — publikowanie artefaktu](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish.png) 

      ![Azure DevOps — publikuj plik compose](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish-compose.png) 

5. Kliknij **pozycję Zapisz & kolejki,** aby przetestować potok kompilacji.

   ![Azure DevOps — zapisywanie i kolejka](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-save.png) 

   ![Azure DevOps — nowa kolejka](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-queue.png) 

6. Jeśli **kompilacja** jest poprawna, musisz zobaczyć ten ekran:

   ![Azure DevOps — kompilacja powiodła się](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-succeeded.png) 

## <a name="step-3-create-the-release-pipeline"></a>Krok 3: Tworzenie potoku wydania

Usługa Azure DevOps umożliwia [zarządzanie wersjami w różnych środowiskach.](https://www.visualstudio.com/team-services/release-management/) Można włączyć ciągłe wdrażanie, aby upewnić się, że aplikacja jest wdrażana w różnych środowiskach (takich jak dev, test, pre-production i produkcji) w sposób płynny. Można utworzyć środowisko, które reprezentuje klastra trybu rój usługi kontenera azure.

![Azure DevOps — zwolnij do usługi ACS](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Konfiguracja wydania początkowego

1. Aby utworzyć potok wydań, kliknij pozycję **Wydania** > **+ Zwolnij**

2. Aby skonfigurować źródło artefaktu, kliknij pozycję **Artefakty** > **Połącz źródło artefaktu**. W tym miejscu połącz ten nowy potok wydania z kompilacją zdefiniowaną w poprzednim kroku. Następnie plik docker-compose.yml jest dostępny w procesie wydania.

    ![Azure DevOps — artefakty wydania](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-artefacts.png) 

3. Aby skonfigurować wyzwalacz wydania, kliknij pozycję **Wyzwalacze** i wybierz pozycję **Ciągłe wdrażanie**. Ustaw wyzwalacz na tym samym źródle artefaktu. To ustawienie gwarantuje, że nowa wersja rozpoczyna się po pomyślnym zakończeniu kompilacji.

    ![Azure DevOps — wyzwalacze wersji](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-trigger.png) 

4. Aby skonfigurować zmienne wydania, kliknij pozycję **Zmienne** i wybierz **+Zmienna,** aby utworzyć trzy nowe zmienne z informacjami rejestru: **docker.username**, **docker.password**i **docker.registry**. Wklej wartości systemu DNS registry i cluster agents.

    ![Azure DevOps — konfiguracja repozytorium kompilacji](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-variables.png)

    >[!IMPORTANT]
    > Jak pokazano na poprzednim ekranie, kliknij pole wyboru **Zablokuj** w pliku docker.password. To ustawienie jest ważne, aby ograniczyć hasło.
    >

### <a name="define-the-release-workflow"></a>Definiowanie przepływu pracy wydania

Przepływ pracy wydania składa się z dwóch zadań, które można dodać.

1. Skonfiguruj zadanie, aby bezpiecznie skopiować plik redagowania do *folderu wdrażania* w węźle głównym Roju platformy Docker, przy użyciu wcześniej skonfigurowanego połączenia SSH. Szczegółowe informacje można znaleźć na poniższym ekranie.
    
    Folder źródłowy:```$(System.DefaultWorkingDirectory)/MyShop-CI/drop```

    ![Azure DevOps — release SCP](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-scp.png)

2. Skonfiguruj drugie zadanie do `docker` wykonania `docker stack deploy` polecenia bash do uruchamiania i poleceń w węźle głównym. Szczegółowe informacje można znaleźć na poniższym ekranie.

    ```
    docker login -u $(docker.username) -p $(docker.password) $(docker.registry) && export DOCKER_HOST=:2375 && cd deploy && docker stack deploy --compose-file docker-compose-v3.yml myshop --with-registry-auth
    ```

    ![Azure DevOps — release bash](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-bash.png)

    Polecenie wykonywane na wzorcu używa interfejsu wiersza polecenia platformy Docker i interfejsu wiersza polecenia docker-compose do wykonywania następujących zadań:

   - Zaloguj się do rejestru kontenerów platformy Azure (używa trzech zmiennych kompilacji, które są zdefiniowane na karcie **Zmienne)**
   - Zdefiniuj zmienną **DOCKER_HOST** do pracy z punktem końcowym Roju (:2375)
   - Przejdź do folderu *wdrażania* utworzonego przez poprzednie zadanie bezpiecznej kopii, który zawiera plik docker-compose.yml 
   - Wykonaj `docker stack deploy` polecenia, które ściągają nowe obrazy i tworzą kontenery.

     >[!IMPORTANT]
     > Jak pokazano na poprzednim ekranie, pole wyboru **Niepowodzenie na STDERR** nie jest zaznaczone. To ustawienie pozwala nam zakończyć `docker-compose` proces wydawania z powodu drukowania kilku komunikatów diagnostycznych, takich jak kontenery są zatrzymywane lub usuwane, na standardowym wyjściu błędu. Jeśli zaznaczysz to pole wyboru, usługa Azure DevOps zgłasza, że wystąpiły błędy podczas wydania, nawet jeśli wszystko pójdzie dobrze.
     >
3. Zapisz ten nowy potok wydania.

## <a name="step-4-test-the-cicd-pipeline"></a>Krok 4: Testowanie potoku ciągłej integracji/ciągłego wdrażania

Teraz, gdy skończysz z konfiguracją, nadszedł czas, aby przetestować ten nowy potok ciągłej integracji/ciągłego wdrażania. Najprostszym sposobem przetestowania go jest zaktualizowanie kodu źródłowego i zatwierdzenie zmian w repozytorium GitHub. Kilka sekund po wypchnięciu kodu zostanie wyświetlona nowa kompilacja uruchomiona w usłudze Azure DevOps. Po pomyślnym zakończeniu zostanie wyzwolona nowa wersja i wdrożona nowa wersja aplikacji w klastrze usługi azure container service.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat ciągłej integracji/ciągłego wdrażania za pomocą usługi Azure DevOps, zobacz artykuł [Dokumentacja potoków platformy Azure.](/azure/devops/pipelines/?view=azure-devops)
* Aby uzyskać więcej informacji na temat acs engine, zobacz [repozytorium ACS Engine GitHub](https://github.com/Azure/acs-engine).
* Aby uzyskać więcej informacji na temat trybu rój platformy Docker, zobacz [omówienie trybu rój](https://docs.docker.com/engine/swarm/)platformy Docker .

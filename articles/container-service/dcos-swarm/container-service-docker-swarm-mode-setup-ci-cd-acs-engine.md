---
title: Ciągła Integracja/ciągłe dostarczanie z aparatem usługi kontenera platformy Azure i tryb Swarm
description: Użyj aparat usługi Azure Container Service przy użyciu trybu Docker Swarm, usługi Azure Container Registry i Visual Studio Team Services do ciągłego dostarczania wielokontenerowej aplikacji .NET Core
services: container-service
author: diegomrtnzg
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/27/2017
ms.author: diegomrtnzg
ms.custom: mvc
ms.openlocfilehash: 6c41156383791fb7d72ac02dae919a25a0d15c84
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39621066"
---
# <a name="full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-acs-engine-and-docker-swarm-mode-using-visual-studio-team-services"></a>Pełny potok ciągłej integracji/ciągłego wdrażania, aby wdrożyć aplikację obsługującą wiele kontenerów w usłudze Azure Container Service przy użyciu aparatu usługi ACS i trybu Docker Swarm przy użyciu programu Visual Studio Team Services

*Ten artykuł jest oparty na [potoku pełnej ciągłą Integrację/ciągłe dostarczanie, aby wdrożyć aplikację obsługującą wiele kontenerów w usłudze Azure Container Service przy użyciu rozwiązania Docker Swarm przy użyciu programu Visual Studio Team Services](container-service-docker-swarm-setup-ci-cd.md) dokumentacji*

Dzisiaj jedną z największych wyzwań podczas tworzenia nowoczesnych aplikacji dla chmury jest możliwość ciągłe dostarczanie tych aplikacji. W tym artykule dowiesz się, jak zaimplementować ciągłej integracji i potoku przy użyciu wdrażania (CI/CD): 
* Aparat usługi kontenera platformy Azure przy użyciu tryb Docker Swarm
* Azure Container Registry
* Visual Studio Team Services

Ten artykuł jest oparty na prostej aplikacji, dostępnych na [GitHub](https://github.com/jcorioland/MyShop/tree/docker-linux), opracowanych za pomocą programu ASP.NET Core. Aplikacja składa się z czterech różnych usług: trzy sieci web, interfejsów API i jedną witrynę sieci web frontonu:

![MyShop przykładowej aplikacji](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/myshop-application.png)

Celem jest ciągłe dostarczanie tej aplikacji w klastrze trybu Docker Swarm przy użyciu programu Visual Studio Team Services. Poniższa ilustracja szczegóły ten potok ciągłego dostarczania:

![MyShop przykładowej aplikacji](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/full-ci-cd-pipeline.png)

Poniżej przedstawiono krótki opis kroków:

1. Zmiany kodu są zobowiązane do repozytorium kodu źródłowego (tutaj GitHub) 
2. GitHub wyzwala kompilację w usłudze Visual Studio Team Services 
3. Visual Studio Team Services pobiera najnowszą wersję źródła i kompilacji wszystkie obrazy wchodzących w skład aplikacji 
4. Visual Studio Team Services wypycha obraz do rejestru platformy Docker, utworzony za pomocą usługi Azure Container Registry 
5. Visual Studio Team Services wyzwala nowej wersji 
6. Wydanie uruchamia niektórych poleceń na węzła głównego klastra usługi kontenera platformy Azure przy użyciu protokołu SSH 
7. Trybu docker Swarm w klastrze pobiera najnowszą wersję obrazów 
8. Nowa wersja aplikacji jest wdrażany za pomocą platformy Docker, stosu 

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka, należy wykonać następujące zadania:

- [Utwórz klaster trybu Swarm w usłudze Azure Container Service za pomocą aparatu usługi ACS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acsengine-swarmmode)
- [Połączenie z klastrem Swarm w usłudze Azure Container Service](../container-service-connect.md)
- [Utwórz rejestr Azure container registry](../../container-registry/container-registry-get-started-portal.md)
- [Utworzony projekt konta i zespołu Visual Studio Team Services](https://docs.microsoft.com/vsts/organizations/accounts/create-organization-msa-or-work-student)
- [Utwórz rozwidlenie repozytorium GitHub z kontem usługi GitHub](https://github.com/jcorioland/MyShop/tree/docker-linux)

>[!NOTE]
> Koordynator rozwiązania Docker Swarm w usłudze Azure Container Service korzysta ze starszego autonomicznego rozwiązania Swarm. Obecnie zintegrowany [tryb Swarm](https://docs.docker.com/engine/swarm/) (na platformie Docker 1.12 lub nowszej) nie jest obsługiwanym koordynatorem w usłudze Azure Container Service. Z tego powodu korzystamy [aparatu usługi ACS](https://github.com/Azure/acs-engine/blob/master/docs/swarmmode.md), współtworzonych przez społeczność [szablon szybkiego startu](https://azure.microsoft.com/resources/templates/101-acsengine-swarmmode/), lub rozwiązania Docker w [portalu Azure Marketplace](https://azuremarketplace.microsoft.com).
>

## <a name="step-1-configure-your-visual-studio-team-services-account"></a>Krok 1: Konfigurowanie konta usługi Visual Studio Team Services 

W tej sekcji skonfigurujesz konto Visual Studio Team Services. Aby skonfigurować punkty końcowe usługi VSTS, w projekcie programu Visual Studio Team Services, kliknij **ustawienia** ikonę na pasku narzędzi, a następnie wybierz pozycję **usług**.

![Otwórz usługi punktu końcowego](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/services-vsts.PNG)

### <a name="connect-visual-studio-team-services-and-azure-account"></a>Łączenie konta usługi Visual Studio Team Services i platformy Azure

Konfigurowanie połączenia między projektu usługi VSTS i konta platformy Azure.

1. Po lewej stronie, kliknij przycisk **nowy punkt końcowy usługi** > **usługi Azure Resource Manager**.
2. Aby autoryzować usługi VSTS do pracy z kontem platformy Azure, wybierz swoje **subskrypcji** i kliknij przycisk **OK**.

    ![Visual Studio Team Services — autoryzowanie Azure](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-azure.PNG)

### <a name="connect-visual-studio-team-services-and-github"></a>Łączenie programu Visual Studio Team Services i GitHub

Konfigurowanie połączenia między projektu usługi VSTS i konta usługi GitHub.

1. Po lewej stronie, kliknij przycisk **nowy punkt końcowy usługi** > **GitHub**.
2. Aby autoryzować usługę VSTS, aby pracować z Twojego konta usługi GitHub, kliknij przycisk **Autoryzuj** i postępuj zgodnie z procedurą w otwartym oknie.

    ![Visual Studio Team Services — autoryzowania usługi GitHub](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github.png)

### <a name="connect-vsts-to-your-azure-container-service-cluster"></a>Połączenia usługi VSTS z klastrem usługi kontenera platformy Azure

Ostatnie kroki przed przejściem do potoku ciągłej integracji/ciągłego wdrażania są do skonfigurowania połączenia zewnętrzne do klastra Docker Swarm na platformie Azure. 

1. Dla klastra Docker Swarm Dodawanie punktu końcowego typu **SSH**. Wprowadź informacje połączenia SSH z klastrem Swarm (węzła głównego).

    ![Visual Studio Team Services — SSH](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-ssh.png)

Całą konfigurację odbywa się teraz. W następnych krokach utworzysz potok ciągłej integracji/ciągłego wdrażania, który kompiluje i wdraża aplikację w klastrze Docker Swarm. 

## <a name="step-2-create-the-build-definition"></a>Krok 2: Tworzenie definicji kompilacji

W tym kroku należy skonfigurować definicję kompilacji dla projektu usługi VSTS i definicja przepływu pracy kompilacji dla obrazów kontenerów

### <a name="initial-definition-setup"></a>Definicja początkowej instalacji

1. Aby utworzyć definicję kompilacji, połącz się z projektem usługi Visual Studio Team Services, a następnie kliknij przycisk **kompilowanie i wydawanie**. W **definicje kompilacji** kliknij **+ nowy**. 

    ![Program Visual Studio Team Services — nowej definicji kompilacji](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-build-vsts.PNG)

2. Wybierz **pusty procesu**.

    ![Program Visual Studio Team Services — Nowa definicja kompilacji pusty](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-empty-build-vsts.PNG)

4. Następnie kliknij przycisk **zmienne** kartę i Utwórz dwa nowe zmienne: **RegistryURL** i **AgentURL**. Wklej wartości rejestru i klastra, DNS agentów.

    ![Program Visual Studio Team Services — Konfiguracja zmiennych kompilacji](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-variables.png)

5. Na **definicje kompilacji** otwartą stronę **wyzwalaczy** kartę i konfigurowanie kompilacji ciągłej integracji za pomocą rozwidlenie projektu MyShop, który został utworzony w wymaganiach wstępnych. Następnie wybierz **partii zmian**. Upewnij się, że wybrano *docker-linux* jako **gałęzi specyfikacji**.

    ![Program Visual Studio Team Services — Konfiguracja repozytorium kompilacji](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github-repo-conf.PNG)


6. Na koniec kliknij **opcje** karcie i skonfiguruj domyślnej kolejki agenta do **(wersja zapoznawcza) systemu Linux hostowanych**.

    ![Program Visual Studio Team Services — konfiguracja agenta hosta](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-agent.png)

### <a name="define-the-build-workflow"></a>Definiowanie przepływu pracy kompilacji
Następne kroki zdefiniować przepływ pracy kompilacji. Najpierw należy skonfigurować źródła kodu. Aby wykonać to zadanie, wybierz pozycję **GitHub** i **repozytorium** i **gałęzi** (docker — system linux).

![Konfigurowanie programu Visual Studio Team Services — kodu źródłowego](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-source-code.png)

Istnieje pięć obrazów kontenera do kompilacji dla *MyShop* aplikacji. Każdy obraz jest kompilowany, za pomocą pliku Dockerfile znajdujący się w folderach projektu:

* ProductsApi
* Serwer proxy
* RatingsApi
* RecommandationsApi
* Sklepu

Potrzebujesz dwóch kroków platformy Docker dla każdego obrazu: jeden do tworzenia obrazu i jeden aby wypchnąć obraz w usłudze Azure container registry. 

1. Aby dodać krok w przepływie pracy kompilacji, kliknij przycisk **+ Dodaj krok kompilacji** i wybierz **Docker**.

    ![Visual Studio Team Services — Dodawanie kroków kompilacji](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-add-task.png)

2. Dla każdego obrazu, należy skonfigurować jeden krok, który używa `docker build` polecenia.

    ![Program Visual Studio Team Services — kompilacji platformy Docker](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-build.png)

    Dla operacji kompilacji, wybierz usługi Azure Container Registry, **Zbuduj obraz** akcji i pliku Dockerfile, który definiuje każdego obrazu. Ustaw **katalog roboczy** jako katalogu głównego pliku Dockerfile zdefiniować **nazwa obrazu**i wybierz **zawierają najnowsze tagu**.
    
    Nazwa obrazu musi być w następującym formacie: ```$(RegistryURL)/[NAME]:$(Build.BuildId)```. Zastąp **[NAME]** nazwą obrazu:
    - ```proxy```
    - ```products-api```
    - ```ratings-api```
    - ```recommendations-api```
    - ```shopfront```

3. Dla każdego obrazu, należy skonfigurować drugi krok, który używa `docker push` polecenia.

    ![Program Visual Studio Team Services — Docker Push](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-push.png)

    Dla operacji wypychania, Wybieranie usługi Azure container registry **wypchnąć obraz** akcji, wprowadź **nazwa obrazu** utworzonego w poprzednim kroku, a następnie wybierz pozycję **zawierają najnowsze Tag**.

4. Po skonfigurowaniu kroki kompilacji i wypychania dla każdej z pięciu obrazów, Dodaj trzy większej liczby kroków w przepływie pracy kompilacji.

   ![Visual Studio Team Services — dodawanie zadań wiersza polecenia](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-command-task.png)

      1. Zadanie wiersza polecenia, który używa skryptu powłoki systemowej w celu zastąpienia *RegistryURL* wystąpienie w pliku docker-compose.yml ze zmienną RegistryURL. 
    
          ```-c "sed -i 's/RegistryUrl/$(RegistryURL)/g' src/docker-compose-v3.yml"```

          ![Visual Studio Team Services — plik Compose aktualizacji za pomocą adresu URL rejestru](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-replace-registry.png)

      2. Zadanie wiersza polecenia, który używa skryptu powłoki systemowej w celu zastąpienia *AgentURL* wystąpienie w pliku docker-compose.yml ze zmienną AgentURL.
  
          ```-c "sed -i 's/AgentUrl/$(AgentURL)/g' src/docker-compose-v3.yml"```

     3. Zadanie, które umieszcza zaktualizowany plik Compose jako artefakt kompilacji, dzięki czemu może być używany w wersji. Zobacz poniższy ekran, aby uzyskać szczegółowe informacje.

         ![Visual Studio Team Services — publikowanie artefaktów](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish.png) 

         ![Visual Studio Team Services — publikowanie Compose pliku](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish-compose.png) 

5. Kliknij przycisk **Zapisz k & olejką** do przetestowania swojej definicji kompilacji.

   ![Visual Studio Team Services — zapisywanie i kolejki](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-save.png) 

   ![Program Visual Studio Team Services — nowej kolejki](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-queue.png) 

6. Jeśli **kompilacji** jest poprawna, trzeba wyświetlony następujący ekran:

  ![Visual Studio Team Services — kompilacja powiodła się](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-succeeded.png) 

## <a name="step-3-create-the-release-definition"></a>Krok 3: Tworzenie definicji wydania

Visual Studio Team Services umożliwia [zarządzania wersjami w środowiskach](https://www.visualstudio.com/team-services/release-management/). Można włączyć ciągłe wdrażanie upewnić się, że aplikacja jest wdrażana w różnych środowiskach (na przykład deweloperów, testerów, przedprodukcyjnych i produkcyjnych) w sposób. Można utworzyć środowiska, która reprezentuje klaster trybu usługi Azure Container Service Docker Swarm.

![Program Visual Studio Team Services — wersji usługi ACS](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Początkowa wersja Instalatora

1. Aby utworzyć definicję wydania, kliknij przycisk **wersji** > **+ wydanie**

2. Aby skonfigurować źródła artefaktu, kliknij **artefaktów** > **połączenia źródła artefaktu**. W tym miejscu połączyć tej nowej definicji wydania z kompilacji, który został zdefiniowany w poprzednim kroku. Po utworzeniu tego pliku docker-compose.yml jest dostępna w procesie tworzenia wersji.

    ![Program Visual Studio Team Services — artefaktów wydań](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-artefacts.png) 

3. Aby skonfigurować wyzwalacz wydania, kliknij **wyzwalaczy** i wybierz **ciągłe wdrażanie**. Ustaw wyzwalacz, w tym samym źródła artefaktu. To ustawienie zapewni, że rozpoczyna się nowej wersji, gdy kompilacja zakończy się pomyślnie.

    ![Program Visual Studio Team Services — wyzwalacze wydania](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-trigger.png) 

4. Aby skonfigurować zmienne wydania, kliknij **zmienne** i wybierz **+ zmiennej** utworzyć trzy nowe zmienne z użyciem informacji rejestru: **docker.username**, **docker.password**, i **docker.registry**. Wklej wartości rejestru i klastra, DNS agentów.

    ![Program Visual Studio Team Services — Konfiguracja repozytorium kompilacji](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-variables.png)

    >[!IMPORTANT]
    > Jak pokazano na poprzednim ekranie, kliknij przycisk **blokady** pola wyboru w docker.password. To ustawienie jest ważne, aby ograniczyć hasło.
    >

### <a name="define-the-release-workflow"></a>Zdefiniuj przepływ pracy wydania

Przepływ pracy wydania składa się z dwóch zadań, które dodajesz.

1. Skonfiguruj zadanie, aby bezpiecznie skopiować plik compose *wdrażanie* folderu na Docker Swarm węzła głównego, przy użyciu połączenia SSH, który został wcześniej skonfigurowany. Zobacz poniższy ekran, aby uzyskać szczegółowe informacje.
    
    Folder źródłowy: ```$(System.DefaultWorkingDirectory)/MyShop-CI/drop```

    ![Program Visual Studio Team Services — wersji połączenia usługi](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-scp.png)

2. Konfigurowanie drugie zadanie do wykonania polecenia powłoki bash w celu uruchomienia `docker` i `docker stack deploy` poleceń na węzła głównego. Zobacz poniższy ekran, aby uzyskać szczegółowe informacje.

    ```docker login -u $(docker.username) -p $(docker.password) $(docker.registry) && export DOCKER_HOST=:2375 && cd deploy && docker stack deploy --compose-file docker-compose-v3.yml myshop --with-registry-auth```

    ![Visual Studio Team Services - wersja Bash](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-bash.png)

    Polecenie wykonane na wzorcu używa interfejsu wiersza polecenia platformy Docker i interfejsu wiersza polecenia platformy Docker-Compose do wykonywania następujących zadań:

    - Zaloguj się do usługi Azure container registry (używa trzech zmiennych kompilacji, które są zdefiniowane w **zmienne** kartę)
    - Zdefiniuj **DOCKER_HOST** zmiennej do pracy z punktu końcowego Swarm (: 2375)
    - Przejdź do *wdrażanie* folder, który został utworzony w poprzednim zadaniu bezpiecznego kopiowania i który zawiera plik docker-compose.yml 
    - Wykonaj `docker stack deploy` polecenia, które ściągnięcia nowych obrazów i tworzenia kontenerów.

    >[!IMPORTANT]
    > Jak pokazano na poprzednim ekranie, pozostaw **zakończyć się niepowodzeniem ze strumienia STDERR** niezaznaczone pole wyboru. To ustawienie pozwala nam na ukończenie procesu tworzenia wersji ze względu na `docker-compose` drukuje kilka komunikatów diagnostycznych, takich jak kontenery są zatrzymywania lub usuwany na standardowy błąd danych wyjściowych. Jeśli zaznaczysz pole wyboru, Visual Studio Team Services zgłasza błędy, które wystąpiły podczas wersji, nawet jeśli wszystko przebiegnie poprawnie.
    >
3. Zapisz ten nowy definicji wydania.

## <a name="step-4-test-the-cicd-pipeline"></a>Krok 4: Testowanie potoku ciągłej integracji/ciągłego wdrażania

Teraz, gdy wszystko będzie gotowe z konfiguracją, jest czas na przetestowanie ten nowy potok ciągłej integracji/ciągłego wdrażania. Jest najprostszym sposobem przetestowania jej do aktualizowania kodu źródłowego i zatwierdza zmiany w repozytorium GitHub. Kilka sekund po wypchnięciu kodu, zobaczysz nową kompilację w Visual Studio Team Services. Po pomyślnym ukończeniu nowa wersja zostanie wyzwolony i wdrożyć nowej wersji aplikacji w klastrze usługi kontenera platformy Azure.

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać więcej informacji o ciągłej integracji/ciągłego wdrażania za pomocą programu Visual Studio Team Services, zobacz [kompilacji usług VSTS Przegląd](https://www.visualstudio.com/docs/build/overview).
* Aby uzyskać więcej informacji na temat aparatu usługi ACS, zobacz [repozytorium GitHub aparatu ACS](https://github.com/Azure/acs-engine).
* Aby uzyskać więcej informacji na temat trybu Docker Swarm, zobacz [Omówienie trybu Docker Swarm](https://docs.docker.com/engine/swarm/).

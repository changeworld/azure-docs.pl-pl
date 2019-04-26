---
title: (PRZESTARZAŁE) Ciągła Integracja/ciągłe dostarczanie z aparatem usługi kontenera platformy Azure i tryb Swarm
description: Użyj aparat usługi Azure Container Service przy użyciu trybu Docker Swarm, usługi Azure Container Registry i DevOps platformy Azure do ciągłego dostarczania wielokontenerowej aplikacji .NET Core
services: container-service
author: diegomrtnzg
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/27/2017
ms.author: diegomrtnzg
ms.custom: mvc
ms.openlocfilehash: 8aa62e4ed65f8223071786ac165f8343cb6901d5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60430673"
---
# <a name="deprecated-full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-acs-engine-and-docker-swarm-mode-using-azure-devops"></a>(PRZESTARZAŁE) Pełny potok ciągłej integracji/ciągłego wdrażania, aby wdrożyć aplikację obsługującą wiele kontenerów w usłudze Azure Container Service przy użyciu aparatu usługi ACS i trybu Docker Swarm przy użyciu DevOps platformy Azure

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

*Ten artykuł jest oparty na [potoku pełnej ciągłą Integrację/ciągłe dostarczanie, aby wdrożyć aplikację obsługującą wiele kontenerów w usłudze Azure Container Service przy użyciu rozwiązania Docker Swarm przy użyciu usługi Azure DevOps](container-service-docker-swarm-setup-ci-cd.md) dokumentacji*

Dzisiaj jedną z największych wyzwań podczas tworzenia nowoczesnych aplikacji dla chmury jest możliwość ciągłe dostarczanie tych aplikacji. W tym artykule dowiesz się, jak zaimplementować ciągłej integracji i potoku przy użyciu wdrażania (CI/CD): 
* Aparat usługi kontenera platformy Azure przy użyciu tryb Docker Swarm
* Azure Container Registry
* Azure DevOps

Ten artykuł jest oparty na prostej aplikacji, dostępnych na [GitHub](https://github.com/jcorioland/MyShop/tree/docker-linux), opracowanych za pomocą programu ASP.NET Core. Aplikacja składa się z czterech różnych usług: trzy sieci web, interfejsów API i jedną witrynę sieci web frontonu:

![MyShop przykładowej aplikacji](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/myshop-application.png)

Celem jest ciągłe dostarczanie tej aplikacji w klastrze trybu Docker Swarm przy użyciu DevOps platformy Azure. Poniższa ilustracja szczegóły ten potok ciągłego dostarczania:

![MyShop przykładowej aplikacji](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/full-ci-cd-pipeline.png)

Poniżej przedstawiono krótki opis kroków:

1. Zmiany kodu są zobowiązane do repozytorium kodu źródłowego (tutaj GitHub) 
2. GitHub wyzwala kompilację w DevOps platformy Azure 
3. Azure DevOps pobiera najnowszą wersję źródła i tworzy wszystkie obrazy wchodzących w skład aplikacji 
4. Usługa Azure DevOps wypycha każdego obrazu do rejestru platformy Docker, utworzony za pomocą usługi Azure Container Registry 
5. Usługa Azure DevOps wyzwala nowej wersji 
6. Wydanie uruchamia niektórych poleceń na węzła głównego klastra usługi kontenera platformy Azure przy użyciu protokołu SSH 
7. Trybu docker Swarm w klastrze pobiera najnowszą wersję obrazów 
8. Nowa wersja aplikacji jest wdrażany za pomocą platformy Docker, stosu 

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka, należy wykonać następujące zadania:

- [Utwórz klaster trybu Swarm w usłudze Azure Container Service za pomocą aparatu usługi ACS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acsengine-swarmmode)
- [Połączenie z klastrem Swarm w usłudze Azure Container Service](../container-service-connect.md)
- [Utwórz rejestr Azure container registry](../../container-registry/container-registry-get-started-portal.md)
- [Organizacja DevOps platformy Azure i utworzonego projektu](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [Utwórz rozwidlenie repozytorium GitHub z kontem usługi GitHub](https://github.com/jcorioland/MyShop/tree/docker-linux)

>[!NOTE]
> Koordynator rozwiązania Docker Swarm w usłudze Azure Container Service korzysta ze starszego autonomicznego rozwiązania Swarm. Obecnie zintegrowany [tryb Swarm](https://docs.docker.com/engine/swarm/) (na platformie Docker 1.12 lub nowszej) nie jest obsługiwanym koordynatorem w usłudze Azure Container Service. Z tego powodu korzystamy [aparatu usługi ACS](https://github.com/Azure/acs-engine/blob/master/docs/swarmmode.md), współtworzonych przez społeczność [szablon szybkiego startu](https://azure.microsoft.com/resources/templates/101-acsengine-swarmmode/), lub rozwiązania Docker w [portalu Azure Marketplace](https://azuremarketplace.microsoft.com).
>

## <a name="step-1-configure-your-azure-devops-organization"></a>Krok 1: Konfigurowanie organizacji DevOps platformy Azure 

W tej sekcji skonfigurujesz organizacji DevOps platformy Azure. Aby skonfigurować punkty końcowe usługi DevOps platformy Azure, do projektu DevOps platformy Azure, kliknij **ustawienia** ikonę na pasku narzędzi, a następnie wybierz pozycję **usług**.

![Otwórz usługi punktu końcowego](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/services-vsts.PNG)

### <a name="connect-azure-devops-and-azure-account"></a>Łączenie DevOps platformy Azure i konto platformy Azure

Konfigurowanie połączenia między projektu DevOps platformy Azure i konto platformy Azure.

1. Po lewej stronie, kliknij przycisk **nowy punkt końcowy usługi** > **usługi Azure Resource Manager**.
2. Aby autoryzować DevOps platformy Azure do pracy z kontem platformy Azure, wybierz swoje **subskrypcji** i kliknij przycisk **OK**.

    ![Usługa Azure DevOps — autoryzowanie Azure](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-azure.PNG)

### <a name="connect-azure-devops-and-github"></a>Łączenie usługi Azure DevOps i GitHub

Konfigurowanie połączenia między projektu DevOps platformy Azure i konta usługi GitHub.

1. Po lewej stronie, kliknij przycisk **nowy punkt końcowy usługi** > **GitHub**.
2. Aby autoryzować DevOps platformy Azure, aby pracować z Twojego konta usługi GitHub, kliknij przycisk **Autoryzuj** i postępuj zgodnie z procedurą w otwartym oknie.

    ![Usługa Azure DevOps — autoryzowania usługi GitHub](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github.png)

### <a name="connect-azure-devops-to-your-azure-container-service-cluster"></a>Połącz DevOps platformy Azure z klastrem usługi kontenera platformy Azure

Ostatnie kroki przed przejściem do potoku ciągłej integracji/ciągłego wdrażania są do skonfigurowania połączenia zewnętrzne do klastra Docker Swarm na platformie Azure. 

1. Dla klastra Docker Swarm Dodawanie punktu końcowego typu **SSH**. Wprowadź informacje połączenia SSH z klastrem Swarm (węzła głównego).

    ![Usługa Azure DevOps — SSH](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-ssh.png)

Całą konfigurację odbywa się teraz. W następnych krokach utworzysz potok ciągłej integracji/ciągłego wdrażania, który kompiluje i wdraża aplikację w klastrze Docker Swarm. 

## <a name="step-2-create-the-build-pipeline"></a>Krok 2: Tworzenie potoku kompilacji

W tym kroku Skonfiguruj potok kompilacji dla projektu DevOps platformy Azure i definicja przepływu pracy kompilacji dla obrazów kontenerów

### <a name="initial-pipeline-setup"></a>Ustawienia początkowe potoku

1. Aby utworzyć potok kompilacji, połącz się z projektem DevOps platformy Azure, a następnie kliknij przycisk **kompilowanie i wydawanie**. W **definicje kompilacji** kliknij **+ nowy**. 

    ![Usługa Azure DevOps — nowe tworzenie potoku](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-build-vsts.PNG)

2. Wybierz **pusty procesu**.

    ![Usługa Azure DevOps — nowy potok pusty kompilacji](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-empty-build-vsts.PNG)

4. Następnie kliknij przycisk **zmienne** kartę i Utwórz dwa nowe zmienne: **RegistryURL** i **AgentURL**. Wklej wartości rejestru i klastra, DNS agentów.

    ![Usługa Azure DevOps — Konfiguracja zmiennych kompilacji](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-variables.png)

5. Na **definicje kompilacji** otwartą stronę **wyzwalaczy** kartę i konfigurowanie kompilacji ciągłej integracji za pomocą rozwidlenie projektu MyShop, który został utworzony w wymaganiach wstępnych. Następnie wybierz **partii zmian**. Upewnij się, że wybrano *docker-linux* jako **gałęzi specyfikacji**.

    ![Usługa Azure DevOps — Konfiguracja repozytorium kompilacji](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github-repo-conf.PNG)


6. Na koniec kliknij **opcje** karcie i skonfiguruj domyślnej kolejki agenta do **(wersja zapoznawcza) systemu Linux hostowanych**.

    ![Usługa Azure DevOps — konfiguracja agenta hosta](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-agent.png)

### <a name="define-the-build-workflow"></a>Definiowanie przepływu pracy kompilacji
Następne kroki zdefiniować przepływ pracy kompilacji. Najpierw należy skonfigurować źródła kodu. Aby wykonać to zadanie, wybierz pozycję **GitHub** i **repozytorium** i **gałęzi** (docker — system linux).

![Konfigurowanie usługi Azure DevOps — kodu źródłowego](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-source-code.png)

Istnieje pięć obrazów kontenera do kompilacji dla *MyShop* aplikacji. Każdy obraz jest kompilowany, za pomocą pliku Dockerfile znajdujący się w folderach projektu:

* ProductsApi
* Serwer proxy
* RatingsApi
* RecommendationsApi
* Sklepu

Potrzebujesz dwóch kroków platformy Docker dla każdego obrazu: jeden do tworzenia obrazu i jeden aby wypchnąć obraz w usłudze Azure container registry. 

1. Aby dodać krok w przepływie pracy kompilacji, kliknij przycisk **+ Dodaj krok kompilacji** i wybierz **Docker**.

    ![Usługa Azure DevOps — Dodawanie kroków kompilacji](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-add-task.png)

2. Dla każdego obrazu, należy skonfigurować jeden krok, który używa `docker build` polecenia.

    ![Usługa Azure DevOps — kompilacji platformy Docker](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-build.png)

    Dla operacji kompilacji, wybierz usługi Azure Container Registry, **Zbuduj obraz** akcji i pliku Dockerfile, który definiuje każdego obrazu. Ustaw **katalog roboczy** jako katalogu głównego pliku Dockerfile zdefiniować **nazwa obrazu**i wybierz **zawierają najnowsze tagu**.
    
    Nazwa obrazu musi być w następującym formacie: ```$(RegistryURL)/[NAME]:$(Build.BuildId)```. Zastąp **[NAME]** nazwą obrazu:
    - ```proxy```
    - ```products-api```
    - ```ratings-api```
    - ```recommendations-api```
    - ```shopfront```

3. Dla każdego obrazu, należy skonfigurować drugi krok, który używa `docker push` polecenia.

    ![Usługa Azure DevOps — Docker Push](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-push.png)

    Dla operacji wypychania, Wybieranie usługi Azure container registry **wypchnąć obraz** akcji, wprowadź **nazwa obrazu** utworzonego w poprzednim kroku, a następnie wybierz pozycję **zawierają najnowsze Tag**.

4. Po skonfigurowaniu kroki kompilacji i wypychania dla każdej z pięciu obrazów, Dodaj trzy większej liczby kroków w przepływie pracy kompilacji.

   ![DevOps platformy Azure — dodawanie zadań wiersza polecenia](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-command-task.png)

   1. Zadanie wiersza polecenia, który używa skryptu powłoki systemowej w celu zastąpienia *RegistryURL* wystąpienie w pliku docker-compose.yml ze zmienną RegistryURL. 
    
       ```-c "sed -i 's/RegistryUrl/$(RegistryURL)/g' src/docker-compose-v3.yml"```

       ![Usługa Azure DevOps — plik Compose aktualizacji za pomocą adresu URL rejestru](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-replace-registry.png)

   2. Zadanie wiersza polecenia, który używa skryptu powłoki systemowej w celu zastąpienia *AgentURL* wystąpienie w pliku docker-compose.yml ze zmienną AgentURL.
  
       ```-c "sed -i 's/AgentUrl/$(AgentURL)/g' src/docker-compose-v3.yml"```

      1. Zadanie, które umieszcza zaktualizowany plik Compose jako artefakt kompilacji, dzięki czemu może być używany w wersji. Zobacz poniższy ekran, aby uzyskać szczegółowe informacje.

      ![Usługa Azure DevOps — publikowanie artefaktów](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish.png) 

      ![Publikowanie DevOps platformy Azure — Utwórz plik](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish-compose.png) 

5. Kliknij przycisk **Zapisz k & olejką** próba potok kompilacji.

   ![Usługa Azure DevOps — zapisywanie i kolejki](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-save.png) 

   ![Usługa Azure DevOps — nowej kolejki](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-queue.png) 

6. Jeśli **kompilacji** jest poprawna, trzeba wyświetlony następujący ekran:

   ![Usługa Azure DevOps — kompilacja powiodła się](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-succeeded.png) 

## <a name="step-3-create-the-release-pipeline"></a>Krok 3: Twórz potoki wydania

Azure DevOps umożliwia [zarządzania wersjami w środowiskach](https://www.visualstudio.com/team-services/release-management/). Można włączyć ciągłe wdrażanie upewnić się, że aplikacja jest wdrażana w różnych środowiskach (na przykład deweloperów, testerów, przedprodukcyjnych i produkcyjnych) w sposób. Można utworzyć środowiska, która reprezentuje klaster trybu usługi Azure Container Service Docker Swarm.

![Usługa Azure DevOps — wersji usługi ACS](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Początkowa wersja Instalatora

1. Aby utworzyć potok tworzenia wersji, kliknij przycisk **wersji** > **+ wydanie**

2. Aby skonfigurować źródła artefaktu, kliknij **artefaktów** > **połączenia źródła artefaktu**. W tym miejscu połączyć tego nowego potoku tworzenia wersji kompilacji, który został zdefiniowany w poprzednim kroku. Po utworzeniu tego pliku docker-compose.yml jest dostępna w procesie tworzenia wersji.

    ![Usługa Azure DevOps — artefaktów wydań](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-artefacts.png) 

3. Aby skonfigurować wyzwalacz wydania, kliknij **wyzwalaczy** i wybierz **ciągłe wdrażanie**. Ustaw wyzwalacz, w tym samym źródła artefaktu. To ustawienie zapewni, że rozpoczyna się nowej wersji, gdy kompilacja zakończy się pomyślnie.

    ![Usługa Azure DevOps — wyzwalacze wydania](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-trigger.png) 

4. Aby skonfigurować zmienne wydania, kliknij **zmienne** i wybierz **+ zmiennej** utworzyć trzy nowe zmienne z użyciem informacji rejestru: **docker.username**, **docker.password**, i **docker.registry**. Wklej wartości rejestru i klastra, DNS agentów.

    ![Usługa Azure DevOps — Konfiguracja repozytorium kompilacji](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-variables.png)

    >[!IMPORTANT]
    > Jak pokazano na poprzednim ekranie, kliknij przycisk **blokady** pola wyboru w docker.password. To ustawienie jest ważne, aby ograniczyć hasło.
    >

### <a name="define-the-release-workflow"></a>Zdefiniuj przepływ pracy wydania

Przepływ pracy wydania składa się z dwóch zadań, które dodajesz.

1. Skonfiguruj zadanie, aby bezpiecznie skopiować plik compose *wdrażanie* folderu na Docker Swarm węzła głównego, przy użyciu połączenia SSH, który został wcześniej skonfigurowany. Zobacz poniższy ekran, aby uzyskać szczegółowe informacje.
    
    Folder źródłowy: ```$(System.DefaultWorkingDirectory)/MyShop-CI/drop```

    ![Usługa Azure DevOps — wersji połączenia usługi](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-scp.png)

2. Konfigurowanie drugie zadanie do wykonania polecenia powłoki bash w celu uruchomienia `docker` i `docker stack deploy` poleceń na węzła głównego. Zobacz poniższy ekran, aby uzyskać szczegółowe informacje.

    ```
    docker login -u $(docker.username) -p $(docker.password) $(docker.registry) && export DOCKER_HOST=:2375 && cd deploy && docker stack deploy --compose-file docker-compose-v3.yml myshop --with-registry-auth
    ```

    ![Usługa Azure DevOps — wersji Bash](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-bash.png)

    Polecenie wykonane na wzorcu używa interfejsu wiersza polecenia platformy Docker i interfejsu wiersza polecenia platformy Docker-Compose do wykonywania następujących zadań:

   - Zaloguj się do usługi Azure container registry (używa trzech zmiennych kompilacji, które są zdefiniowane w **zmienne** kartę)
   - Zdefiniuj **DOCKER_HOST** zmiennej do pracy z punktu końcowego Swarm (: 2375)
   - Przejdź do *wdrażanie* folder, który został utworzony w poprzednim zadaniu bezpiecznego kopiowania i który zawiera plik docker-compose.yml 
   - Wykonaj `docker stack deploy` polecenia, które ściągnięcia nowych obrazów i tworzenia kontenerów.

     >[!IMPORTANT]
     > Jak pokazano na poprzednim ekranie, pozostaw **zakończyć się niepowodzeniem ze strumienia STDERR** niezaznaczone pole wyboru. To ustawienie pozwala nam na ukończenie procesu tworzenia wersji ze względu na `docker-compose` drukuje kilka komunikatów diagnostycznych, takich jak kontenery są zatrzymywania lub usuwany na standardowy błąd danych wyjściowych. Jeśli zaznaczysz pole wyboru, DevOps platformy Azure zgłasza błędy, które wystąpiły podczas wersji, nawet jeśli wszystko przebiegnie poprawnie.
     >
3. Zapisz ten nowy potok wersji.

## <a name="step-4-test-the-cicd-pipeline"></a>Krok 4: Testowanie potoku ciągłej integracji/ciągłego wdrażania

Teraz, gdy wszystko będzie gotowe z konfiguracją, jest czas na przetestowanie ten nowy potok ciągłej integracji/ciągłego wdrażania. Jest najprostszym sposobem przetestowania jej do aktualizowania kodu źródłowego i zatwierdza zmiany w repozytorium GitHub. Kilka sekund po wypchnięciu kodu, zobaczysz nową kompilację w DevOps platformy Azure. Po pomyślnym ukończeniu nowa wersja zostanie wyzwolony i wdrożyć nowej wersji aplikacji w klastrze usługi kontenera platformy Azure.

## <a name="next-steps"></a>Kolejne kroki

* Aby dowiedzieć się więcej o ciągłej integracji/ciągłego wdrażania za pomocą DevOps platformy Azure, zobacz [kompilacji DevOps platformy Azure — omówienie](https://www.visualstudio.com/docs/build/overview).
* Aby uzyskać więcej informacji na temat aparatu usługi ACS, zobacz [repozytorium GitHub aparatu ACS](https://github.com/Azure/acs-engine).
* Aby uzyskać więcej informacji na temat trybu Docker Swarm, zobacz [Omówienie trybu Docker Swarm](https://docs.docker.com/engine/swarm/).

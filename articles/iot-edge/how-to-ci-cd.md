---
title: Usługa Azure IoT Edge, ciągła integracja i ciągłe wdrażanie | Dokumentacja firmy Microsoft
description: Omówienie ciągłą integrację i ciągłe wdrażanie dla usługi Azure IoT Edge
author: shizn
manager: ''
ms.author: xshi
ms.date: 11/12/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a110c0a938e56c8ac276e0efed22ea3af23f111a
ms.sourcegitcommit: 0fc99ab4fbc6922064fc27d64161be6072896b21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2018
ms.locfileid: "51578539"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>Ciągła integracja i ciągłe wdrażanie w usłudze Azure IoT Edge

W tym artykule przedstawiono sposób korzystania z funkcji ciągłego wdrażania usługom DevOps platformy Azure i programu Microsoft Team Foundation Server (TFS) i ciągłej integracji do tworzenia, testowania i wdrażania aplikacji, szybko i skutecznie do usługi Azure IoT Edge. 

W tym artykule dowiesz się jak:
* Tworzenie i sprawdź w przykładowym rozwiązaniu IoT Edge.
* Zainstaluj rozszerzenie Azure IoT Edge dla usługi DevOps platformy Azure.
* Skonfiguruj ciągłą integrację (CI), aby skompilować rozwiązanie.
* Skonfiguruj ciągłe wdrażanie (CD) na wdrożeniu rozwiązania i przeglądania odpowiedzi.

Potrwa 30 minut na wykonanie czynności opisanych w tym artykule.

![Ciągła Integracja i ciągłe dostarczanie](./media/how-to-ci-cd/cd.png)


## <a name="create-a-sample-azure-iot-edge-solution-using-visual-studio-code"></a>Utwórz przykładowe rozwiązanie Azure IoT Edge, przy użyciu programu Visual Studio Code

W tej sekcji utworzysz przykładowe usługi IoT Edge rozwiązania zawierającego testy jednostkowe, które można wykonać w ramach procesu kompilacji. Przed zgodnie ze wskazówkami w tej sekcji, wykonaj kroki opisane w [tworzenia rozwiązań usługi IoT Edge z wieloma modułami w programie Visual Studio Code](tutorial-multiple-modules-in-vscode.md).

1. W palecie poleceń programu VS Code, typ, a następnie uruchom polecenie **usługi Azure IoT Edge: rozwiązanie nowej usługi IoT Edge**. Następnie wybierz folder obszaru roboczego, podaj nazwę rozwiązania (nazwa domyślna to **EdgeSolution**) i Tworzenie modułu C# (**FilterModule**) jako pierwszego modułu, w tym rozwiązaniu. Należy również określić repozytorium obrazów platformy Docker dla pierwszego modułu. Repozytorium obrazów domyślne opiera się na lokalnych rejestru platformy Docker (`localhost:5000/filtermodule`). Należy je zmienić w usłudze Azure Container Registry (`<your container registry address>/filtermodule`) lub usługi Docker Hub dalsze ciągłej integracji.

    ![Konfigurowanie usługi ACR](./media/how-to-ci-cd/acr.png)

2. Okna programu VS Code zostanie załadowany obszar roboczy rozwiązania usługi IoT Edge. Możesz opcjonalnie wpisać i uruchomić **usługi Azure IoT Edge: Dodaj moduł IoT Edge** można dodać więcej modułów. Brak `modules` folderze `.vscode` folder i plik manifestu szablonu wdrożenia w folderze głównym. Wszystkie kody modułu użytkownika będzie podfolderów w folderze `modules`. `deployment.template.json` Jest szablon manifestu wdrożenia. Niektóre parametry w tym pliku, zostanie przetworzona z `module.json`, który istnieje w folderze każdego modułu.

3. Przykład rozwiązania usługi IoT Edge jest teraz gotowy. Domyślnego języka C# modułu działa jako moduł potok komunikatów. W `deployment.template.json`, zostanie wyświetlony, to rozwiązanie zawiera dwa moduły. Komunikat zostanie wygenerowane z `tempSensor` moduł i zostanie bezpośrednio przekazać w potoku za pomocą `FilterModule`, następnie wysyłane do usługi IoT hub.

4. Zapisz te projekty, a następnie zaewidencjonować je w repozytorium DevOps platformy Azure lub na serwerze TFS.
    
> [!NOTE]
> Aby uzyskać więcej informacji o korzystaniu z repozytoriów platformy Azure, zobacz [udostępnić swój kod za pomocą programu Visual Studio i repozytoria, Azure](https://docs.microsoft.com/azure/devops/repos/git/share-your-code-in-git-vs?view=vsts).


## <a name="configure-azure-pipeline-for-continuous-integration"></a>Konfiguruje potok Azure w celu zapewnienia ciągłej integracji
W tej sekcji opisano tworzenie potoku kompilacji, który jest skonfigurowany do automatycznego uruchamiania podczas ewidencjonowania zmiany przykładowe rozwiązanie IoT Edge i pokaże kompilacji dzienniki w potoku usługi Azure.

1. Zaloguj się do Twojej organizacji DevOps platformy Azure (**https://**_— konta_**. visualstudio.com**), a następnie otwórz projekt, gdzie sprawdzane w przykładowej aplikacji.

    ![Kod ewidencjonowania](./media/how-to-ci-cd/init-project.png)

1. Odwiedź stronę [usługi Azure IoT Edge dla potoku usługi Azure](https://marketplace.visualstudio.com/items?itemName=vsc-iot.iot-edge-build-deploy) w witrynie Azure Marketplace metodyki DevOps. Kliknij przycisk **Pobierz bezpłatnie** i wykonaj instrukcje kreatora, aby zainstalować to rozszerzenie Twojej organizacji DevOps platformy Azure lub pobrać do TFS.

    ![Instalowanie rozszerzenia](./media/how-to-ci-cd/install-extension.png)

1. W swojej DevOps platformy Azure, otwórz **kompilowanie i wydawanie** Centrum i w **kompilacje** kartę, wybrać **+ nowy potok**. Lub, jeśli masz już potoki kompilacji, wybierz opcję **+ nowy** przycisku.

    ![Nowy potok](./media/how-to-ci-cd/add-new-build.png)

1. Po wyświetleniu monitu wybierz **DevOps Git platformy Azure** typ źródła. Następnie wybierz projekt, repozytorium i gałąź, na którym znajduje się kod. Wybierz **nadal**.

    ![Wybierz usługi git](./media/how-to-ci-cd/select-vsts-git.png)

    W **wybierz szablon** oknie Wybierz **Rozpocznij z pustym procesem**.

    ![Wybierz szablon](./media/how-to-ci-cd/start-with-empty.png)

1. W edytorze potoku wybierz pulę agentów. 
    
    * Jeśli chcesz tworzyć moduły w amd64 platformy dla kontenerów systemu Linux, wybierz opcję **hostowanych 1604 Ubuntu**
    * Jeśli chcesz tworzyć moduły w amd64 platformy dla kontenerów Windows, wybierz opcję **hostowany program VS2017** 
    * Jeśli chcesz tworzyć moduły w arm32v7 platformy dla kontenerów systemu Linux, musisz Set agenta kompilacji, klikając **Zarządzaj** przycisku.
    
    ![Konfigurowanie agenta kompilacji](./media/how-to-ci-cd/configure-env.png)

1. Zadanie agenta, kliknij przycisk "+" można dodać dwóch zadań w potoku kompilacji. Pierwsza z nich jest z **usługi Azure IoT Edge**. A drugi jest z **publikowanie artefaktów kompilacji**
    
    ![Dodaj zadania](./media/how-to-ci-cd/add-tasks.png)

1. W pierwszym **usługi Azure IoT Edge** zadań, zaktualizuj **nazwę wyświetlaną** do **moduł kompilacji i wypychania**, a następnie w **akcji** listy rozwijanej wybierz opcję **Zbuduj i Wypchnij**. W **pliku Module.json** pola tekstowego, dodać pod ścieżką do niego. Następnie wybierz **Typ rejestru kontenerów**, upewnij się, skonfiguruj i wybierz ten sam rejestru w swojej code(module.json). Tego zadania utworzysz i Wypchnij wszystkie moduły w rozwiązaniu i publikowania w rejestrze kontenerów, wskazana. Jeśli moduły zostaną wypchnięte do różnych rejestrów, może mieć wiele **moduł kompilacji i wypychania** zadania. W takim przypadku rozwiązanie IoT Edge nie znajduje się w katalogu głównym repozytorium kodu, można określić **katalog główny rozwiązania ścieżka krawędzi** w definicji kompilacji.
    
    ![Zbuduj i Wypchnij](./media/how-to-ci-cd/build-and-push.png)

1. W **publikowanie artefaktów kompilacji** zadań, należy określić plik wdrożenia generowanych przez zadanie kompilacji. Ustaw **ścieżka do publikowania** do "config/deployment.json". Jeśli ustawisz **katalog główny rozwiązania ścieżka krawędzi** w ostatnim zadaniem, należy dołączyć ścieżkę katalogu głównego. Na przykład, jeśli ścieżka katalog główny rozwiązania usługi Edge jest ". / edgesolution", a następnie **ścieżka do publikowania** powinien być ". / edgesolution/config/deployment.json". `deployment.json` Plik jest generowany w czasie kompilacji, więc można bezpiecznie zignorować błąd red wiersze w polu tekstowym. 

    ![Publikowanie artefaktów](./media/how-to-ci-cd/publish-build-artifacts.png)

1. Otwórz **wyzwalaczy** kartę, a następnie Włącz **ciągłej integracji** wyzwalacza. Upewnij się, że gałąź z kodem jest dołączony.

    ![Konfigurowanie wyzwalacza](./media/how-to-ci-cd/configure-trigger.png)

    Zapisz nowy potok kompilacji. Kliknij przycisk **Zapisz**.


## <a name="configure-azure-pipeline-for-continuous-deployment"></a>Konfigurowanie potoku usługi Azure do ciągłego wdrażania
W tej sekcji utworzysz potok wersji, który jest skonfigurowany do automatycznego uruchamiania podczas potok kompilacji umieszcza artefaktów i ukazują dzienników wdrażania w potoku usługi Azure.

1. W **wersji** kartę, wybrać **+ nowy potok**. Lub, jeśli masz już potoki wydań, wybierz opcję **+ nowy** przycisku.  

    ![Dodaj potoku tworzenia wersji](./media/how-to-ci-cd/add-release-pipeline.png)

    W **wybierz szablon** oknie Wybierz **rozpoczynać zadanie puste.**

    ![Uruchom zadanie puste](./media/how-to-ci-cd/start-with-empty-job.png)

2. Następnie potok wydania może zainicjować za pomocą jednego etapu: **etap 1**. Zmień nazwę **etap 1** do **QA** i jej traktowała jako środowisku testowym. W typowym ciągłego wdrażania potoku zwykle istnieje wiele etapów, można utworzyć więcej oparte na Twoje praktyki DevOps.

    ![Utwórz etapu](./media/how-to-ci-cd/QA-env.png)

3. Połącz wydanie z artefaktów kompilacji. Kliknij przycisk **Dodaj** w obszarze artefaktów.

    ![Dodaj artefakty](./media/how-to-ci-cd/add-artifacts.png)  
    
    W **Dodawanie strony artefaktu**, wybierz typ źródła **kompilacji**. Następnie wybierz projekt i potoku kompilacji, który został utworzony. Następnie kliknij przycisk **Dodaj**.

    ![Dodawanie artefaktu](./media/how-to-ci-cd/add-an-artifact.png)

    Otwórz wyzwalacz ciągłego wdrażania, tak aby nowa wersja zostanie utworzony przy każdym razem, gdy dostępna jest nowa kompilacja.

    ![Konfigurowanie wyzwalacza](./media/how-to-ci-cd/add-a-trigger.png)

4. Przejdź do **etapu QA** i skonfigurować zadania na tym etapie.

    ![Skonfiguruj zadania pytań i odpowiedzi](./media/how-to-ci-cd/view-stage-tasks.png)

   Zadania wdrażania jest rozróżniana jest wielkość liter, platformy, co oznacza, możesz wybrać dowolną **hostowany program VS2017** lub **hostowanych 1604 Ubuntu** w **puli agenta** (lub inne zarządzane z wykorzystaniem przez agentów samodzielnie). Kliknij przycisk "+" i Dodaj jedno zadanie.

    ![Dodaj zadania dla pytań i odpowiedzi](./media/how-to-ci-cd/add-task-qa.png)

5. W zadaniu usługi Azure IoT Edge, przejdź do **akcji** listy rozwijanej wybierz **wdrażanie na urządzeniu usługi IoT Edge**. Wybierz swoje **subskrypcji platformy Azure** i wejściowego swoje **nazwę Centrum IoT Hub**. Można określić usługi IoT Edge **identyfikator wdrożenia** i wdrażanie **priorytet**. Możesz również wdrożyć na jednym lub wielu urządzeń. Jeśli wdrażasz do **wiele urządzeń**, należy określić urządzenie **warunek docelowy**. Warunek docelowy jest filtr, aby dopasować zbiór urządzeń brzegowych w usłudze IoT Hub. Jeśli chcesz użyć znaczników urządzenia jako warunek, należy zaktualizować urządzenie odpowiednie tagi z bliźniaczej reprezentacji urządzenia usługi IoT Hub. Załóżmy, że masz kilka usługi IoT Edge urządzenia oznaczone jako "qa", a następnie konfiguracji zadania powinny być tak jak w poniższym zrzucie ekranu. 

    ![Wdrażanie w odpowiedzi na pytania](./media/how-to-ci-cd/deploy-to-qa.png)

    Zapisz nowy potok wersji. Kliknij przycisk **Zapisz**. A następnie kliknij przycisk **potoku** aby wrócić do potoku.

6. Drugi etap to w środowisku produkcyjnym. Aby dodać nowy etap "PROD", można po prostu sklonować etap "QA" i zmienić sklonowany etap **PROD**,

    ![Klonowanie etapu](./media/how-to-ci-cd/clone-stage.png)

7. Skonfiguruj zadania w środowisku produkcyjnym. Załóżmy, że masz kilka usługi IoT Edge urządzenia oznaczone jako "prod", w konfiguracji zadania aktualizacji warunek docelowy "prod" i Ustaw identyfikator wdrożenia jako "Wdróż prod". Kliknij przycisk **Zapisz**. A następnie kliknij przycisk **potoku** aby wrócić do potoku.
    
    ![Wdrażanie w środowisku produkcyjnym](./media/how-to-ci-cd/deploy-to-prod.png)

7. Obecnie nasz artefakt kompilacji zostanie wyzwolone stale przy **odpowiedzi na pytania** etapu i następnie **produktu** etapu. Jednak większość czasu konieczne zintegrowanie kilka przypadków testowych na urządzeniach odpowiedzi na pytania i ręcznie zatwierdzić usługi bits. Bity zostanie później wdrożony do środowiska produkcyjnego. Konfigurowanie zatwierdzenia na etapie produkcji jako poniżej.

    1. Otwórz **warunki przed wdrożeniem** panelu Ustawienia.

        ![Otwórz warunki przed wdrożeniem](./media/how-to-ci-cd/pre-deploy-conditions.png)    

    2. Ustaw **włączone** w **zatwierdzenia przed wdrożeniem**. A następnie wypełnij **osoby zatwierdzające** danych wejściowych. Następnie kliknij przycisk **Save** (Zapisz).
    
        ![Zestaw warunków](./media/how-to-ci-cd/set-pre-deployment-conditions.png)


8. Teraz potoku wersji ma została skonfigurowana jako pokazano poniżej.

    ![Potok wydania](./media/how-to-ci-cd/release-pipeline.png)

    
## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>Sprawdź IoT Edge ciągłej integracji/ciągłego wdrażania z kompilacją a potoki wersji

W tej sekcji spowoduje wyzwolenie kompilacji zapewnienie potoku ciągłej integracji/ciągłego Dostarczania pracy. Sprawdź wynik za pomocą portalu DevOps platformy Azure. 

1. Aby wyzwolić zadanie kompilacji, możesz wypchnięciu zatwierdzenia do repozytorium kodu źródłowego lub ręcznie wyzwolić ją. Możesz wyzwolić zadanie kompilacji w potoku kompilacji, klikając **kolejki** przycisku tak jak w poniższym zrzucie ekranu.

    ![Ręczne wyzwalacza](./media/how-to-ci-cd/manual-trigger.png)

2. Jeśli proces kompilacji zakończy się powodzeniem, wyzwolą wydanie do **QA** etapu. Przejdź do potoku dzienniki kompilacji i powinien zostać wyświetlony poniżej.

    ![Dzienniki kompilacji](./media/how-to-ci-cd/build-logs.png)

3. Pomyślne wdrożenie do **QA** etapu będą wyzwalać powiadomienie do osoby zatwierdzającej. Przejdź do potoku wydania, można zobaczyć poniżej. 

    ![Oczekuje na zatwierdzenie](./media/how-to-ci-cd/pending-approval.png)


4. Po osoby zatwierdzającej zatwierdzić tę zmianę, można je było wdrożyć do **PROD**.

    ![Wdrażanie do produkcji](./media/how-to-ci-cd/approve-and-deploy-to-prod.png)


## <a name="next-steps"></a>Kolejne kroki

* Omówienie wdrożenia usługi IoT Edge w [wdrożeń zrozumieć usługi IoT Edge dla urządzeń z jednej lub w odpowiedniej skali](module-deployment-monitoring.md)
* Przewodnik po krokach do utworzenia, aktualizacji lub usunięcia wdrożenia w [wdrażanie i monitorowanie moduły usługi IoT Edge na dużą skalę](how-to-deploy-monitor.md).

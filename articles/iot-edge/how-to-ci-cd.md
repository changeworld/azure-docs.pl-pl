---
title: Ciągła integracja i ciągłe wdrażanie — usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Skonfiguruj ciągłą integrację i ciągłe wdrażanie — usługi Azure IoT Edge przy użyciu infrastruktury DevOps platformy Azure, Azure potoków
author: shizn
manager: philmea
ms.author: xshi
ms.date: 12/12/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: aef88a4fbc7d71ee1438333afd9773d1aba3ed9c
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/17/2019
ms.locfileid: "54359157"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>Ciągła integracja i ciągłe wdrażanie w usłudze Azure IoT Edge

Można łatwo adaptacji metodyki DevOps za pomocą aplikacji usługi Azure IoT Edge za pomocą wbudowanych zadań usługi Azure IoT Edge w potokach platformy Azure lub [wtyczki usługi Azure IoT Edge dla serwera Jenkins](https://plugins.jenkins.io/azure-iot-edge) na serwerze Jenkins. W tym artykule przedstawiono, jak można użyć ciągłej integracji i ciągłego wdrażania funkcji Azure potoków do tworzenia, testowania i wdrażania aplikacji, szybko i skutecznie do usługi Azure IoT Edge. 

W tym artykule dowiesz się jak:
* Tworzenie i sprawdź w przykładowym rozwiązaniu IoT Edge.
* Skonfiguruj ciągłą integrację (CI), aby skompilować rozwiązanie.
* Skonfiguruj ciągłe wdrażanie (CD) na wdrożeniu rozwiązania i przeglądania odpowiedzi.

![Diagram — ciągła Integracja i ciągłe dostarczanie gałęzie rozwoju i produkcji](./media/how-to-ci-cd/cd.png)


## <a name="create-a-sample-azure-iot-edge-solution-using-visual-studio-code"></a>Utwórz przykładowe rozwiązanie Azure IoT Edge, przy użyciu programu Visual Studio Code

W tej sekcji utworzysz przykładowe usługi IoT Edge rozwiązania zawierającego testy jednostkowe, które można wykonać w ramach procesu kompilacji. Przed zgodnie ze wskazówkami w tej sekcji, wykonaj kroki opisane w [tworzenia rozwiązań usługi IoT Edge z wieloma modułami w programie Visual Studio Code](how-to-develop-multiple-modules-vscode.md).

1. W palecie poleceń programu VS Code, typ, a następnie uruchom polecenie **usługi Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: nowe rozwiązanie usługi IoT Edge). Następnie wybierz folder obszaru roboczego, podaj nazwę rozwiązania (nazwa domyślna to **EdgeSolution**) i Tworzenie modułu C# (**FilterModule**) jako pierwszego modułu, w tym rozwiązaniu. Należy również określić repozytorium obrazów platformy Docker dla pierwszego modułu. Repozytorium obrazów domyślne opiera się na lokalnych rejestru platformy Docker (`localhost:5000/filtermodule`). Zmień go na usługę Azure Container Registry (`<your container registry address>/filtermodule`) lub usługi Docker Hub dalsze ciągłej integracji.

    ![Konfigurowanie usługi Azure Container Registry](./media/how-to-ci-cd/acr.png)

2. Okna programu VS Code zostanie załadowany obszar roboczy rozwiązania usługi IoT Edge. Możesz opcjonalnie wpisać i uruchomić **usługi Azure IoT Edge: Dodaj moduł usługi IoT Edge** można dodać więcej modułów. Brak `modules` folderze `.vscode` folder i plik manifestu szablonu wdrożenia w folderze głównym. Wszystkie kody modułu użytkownika będzie podfolderów w folderze `modules`. `deployment.template.json` Jest szablon manifestu wdrożenia. Niektóre parametry w tym pliku, zostanie przetworzona z `module.json`, który istnieje w folderze każdego modułu.

3. Przykład rozwiązania usługi IoT Edge jest teraz gotowy. Domyślnego języka C# modułu działa jako moduł potok komunikatów. W `deployment.template.json`, zostanie wyświetlony, to rozwiązanie zawiera dwa moduły. Komunikat zostanie wygenerowane z `tempSensor` moduł i zostanie bezpośrednio przekazać w potoku za pomocą `FilterModule`, następnie wysyłane do usługi IoT hub.

4. Zapisz te projekty, a następnie zatwierdzenia w repozytorium Azure repozytoriów.
    
> [!NOTE]
> Aby uzyskać więcej informacji o korzystaniu z repozytoriów platformy Azure, zobacz [udostępnić swój kod za pomocą programu Visual Studio i repozytoria, Azure](https://docs.microsoft.com/azure/devops/repos/git/share-your-code-in-git-vs?view=vsts).


## <a name="configure-azure-pipelines-for-continuous-integration"></a>Konfigurowanie potoków usługi Azure do ciągłej integracji
W tej sekcji opisano tworzenie potoku kompilacji, który jest skonfigurowany do automatycznego uruchamiania podczas ewidencjonowania zmiany przykładowe rozwiązanie IoT Edge i pokaże kompilacji loguje się potoki usługi Azure.

1. Zaloguj się do Twojej organizacji DevOps platformy Azure ( **https://dev.azure.com/{your organizacji} /**), a następnie otwórz projekt, gdzie sprawdzane w przykładowej aplikacji.

    ![Kod ewidencjonowania do potoków usługi Azure](./media/how-to-ci-cd/init-project.png)

1. Potoki usługi Azure, otwórz **kompilacje** kartę, wybrać **+ nowy potok**. Lub, jeśli masz już potoki kompilacji, wybierz opcję **+ nowy** przycisku. Następnie wybierz pozycję **potoku tworzenia nowych**.

    ![Tworzenie nowego potoku kompilacji](./media/how-to-ci-cd/add-new-build.png)

1. Po wyświetleniu monitu wybierz repozytoriów platformy Azure dla źródła. Następnie wybierz projekt, repozytorium i gałąź, na którym znajduje się kod. Wybierz **nadal**.

    ![Wybierz pozycję Azure repozytoriów Git](./media/how-to-ci-cd/select-vsts-git.png)

    W **wybierz szablon** oknie Wybierz **Rozpocznij z pustym procesem**.

    ![Rozpocznij z pustym procesem](./media/how-to-ci-cd/start-with-empty.png)

1. W edytorze potoku wybierz pulę agentów. 
    
    * Jeśli chcesz tworzyć moduły w amd64 platformy dla kontenerów systemu Linux, wybierz opcję **hostowanych 1604 Ubuntu**
    * Jeśli chcesz tworzyć moduły w amd64 platformy dla kontenerów Windows 1809, musisz [skonfiguruj samodzielnie hostowanego agenta na Windows](https://docs.microsoft.com/azure/devops/pipelines/agents/v2-windows?view=vsts).
    * Jeśli chcesz tworzyć moduły w arm32v7 platformy dla kontenerów systemu Linux, musisz [Konfigurowanie własnego agenta w systemie Linux](https://blogs.msdn.microsoft.com/iotdev/2018/11/13/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent/).
    
    ![Konfigurowanie puli agentów kompilacji](./media/how-to-ci-cd/configure-env.png)

1. Zadanie agenta, Otwórz "+", aby dodać trzech zadań w potoku kompilacji. Pierwsze dwa są z **usługi Azure IoT Edge**. I trzeci pochodzi z **publikowanie artefaktów kompilacji**
    
    ![Dodawanie podzadań do procesu kompilacji](./media/how-to-ci-cd/add-tasks.png)

1. W pierwszym **usługi Azure IoT Edge** zadań, zaktualizuj **nazwę wyświetlaną** do **usługi Azure IoT Edge — obrazy modułu kompilacji**, a następnie w **akcji** listy rozwijanej Wybierz pozycję **kompilowanie obrazów modułu**. W **. plik template.json** sterowania, wybierz opcję **deployment.template.json** pliku, który opisuje rozwiązanie IoT Edge. Następnie wybierz **domyślna Platforma**, upewnij się, wybierz tę samą platformę jako urządzenia usługi IoT Edge. To zadanie zostanie skompilowany wszystkie moduły w rozwiązaniu za pomocą platformy docelowej, wskazana. Oraz generować **deployment.json** pliku, ścieżka pliku można znaleźć w danych wyjściowych zmiennych. Ustaw alias na `edge` dla tej zmiennej.
    
    ![Konfigurowanie zadania obrazów moduł kompilacji](./media/how-to-ci-cd/build-and-push.png)

1. W drugim **usługi Azure IoT Edge** zadań, zaktualizuj **nazwę wyświetlaną** do **usługi Azure IoT Edge — obrazy modułu wypychania**, a następnie w **akcji** listy rozwijanej Wybierz pozycję **wypychanie obrazów modułu**. Wybierz typ rejestru kontenerów, upewnij się, skonfiguruj i wybierz ten sam rejestru w swojej code(module.json). W **. plik template.json** sterowania, wybierz opcję **deployment.template.json** pliku, który opisuje rozwiązanie IoT Edge. Następnie wybierz **domyślna Platforma**, upewnij się, wybierz tę samą platformę dla modułów skompilowanych obrazów. To zadanie będzie umożliwiać wypychanie powiadomień wszystkie obrazy modułu do rejestru kontenerów, które wybrano. A także dodać poświadczenia rejestru kontenerów w **deployment.json** pliku, ścieżka pliku można znaleźć w danych wyjściowych zmiennych. Ustaw alias na `edge` dla tej zmiennej. Jeśli masz wiele rejestry kontenerów do hostowania obrazów modułu, musisz zduplikowane to zadanie, wybierz inny rejestr kontenerów, a następnie użyj **obejścia moduły** w ustawieniach zaawansowanych, aby pominąć obrazów, które nie są w tym określonego rejestru.

    ![Skonfigurowanie zadania obrazów modułu wypychania](./media/how-to-ci-cd/push.png)

1. W **publikowanie artefaktów kompilacji** zadań, należy określić plik wdrożenia generowanych przez zadanie kompilacji. Ustaw **ścieżka do publikowania** do `$(edge.DEPLOYMENT_FILE_PATH)`.

    ![Konfigurowanie publikowania zadania artefaktu](./media/how-to-ci-cd/publish-build-artifacts.png)

1. Otwórz **wyzwalaczy** kartę, a następnie Włącz **ciągłej integracji** wyzwalacza. Upewnij się, że gałąź z kodem jest dołączony.

    ![Włącz wyzwalacz ciągłej integracji](./media/how-to-ci-cd/configure-trigger.png)

    Zapisz nowy potok kompilacji za pomocą **Zapisz** przycisku.


## <a name="configure-azure-pipelines-for-continuous-deployment"></a>Konfigurowanie Azure potoków ciągłego wdrażania
W tej sekcji utworzysz potok wersji, który jest skonfigurowany do automatycznego uruchamiania podczas potok kompilacji umieszcza artefaktów i ukazują dzienników wdrażania w potokach platformy Azure.

1. W **wersji** kartę, wybrać **+ nowy potok**. Lub, jeśli masz już potoki wydań, wybierz opcję **+ nowy** i wybrać **+ nowy potoku wydania**.  

    ![Dodaj potoku tworzenia wersji](./media/how-to-ci-cd/add-release-pipeline.png)

    W **wybierz szablon** oknie Wybierz **rozpoczynać zadanie puste.**

    ![Uruchom zadanie puste](./media/how-to-ci-cd/start-with-empty-job.png)

2. Następnie potok wydania może ustawić za pomocą jednego etapu: **Etap 1**. Zmień nazwę **etap 1** do **QA** i jej traktowała jako środowisku testowym. W typowym ciągłego wdrażania potoku zwykle istnieje wiele etapów, można utworzyć więcej oparte na Twoje praktyki DevOps.

    ![Tworzenie etapów środowiska testowego](./media/how-to-ci-cd/QA-env.png)

3. Połącz wydanie z artefaktów kompilacji. Kliknij przycisk **Dodaj** w obszarze artefaktów.

    ![Dodaj artefakty](./media/how-to-ci-cd/add-artifacts.png)  
    
    W **Dodawanie strony artefaktu**, wybierz typ źródła **kompilacji**. Następnie wybierz projekt i potoku kompilacji, który został utworzony. Następnie wybierz pozycję **Dodaj**.

    ![Dodaj artefakt kompilacji](./media/how-to-ci-cd/add-an-artifact.png)

    Otwórz wyzwalacz ciągłego wdrażania, tak aby nowa wersja zostanie utworzony przy każdym razem, gdy dostępna jest nowa kompilacja.

    ![Konfigurowanie wyzwalacza ciągłego wdrażania](./media/how-to-ci-cd/add-a-trigger.png)

4. Przejdź do **etapu QA** i skonfigurować zadania na tym etapie.

    ![Skonfiguruj zadania pytań i odpowiedzi](./media/how-to-ci-cd/view-stage-tasks.png)

   Zadania wdrażania jest rozróżniana jest wielkość liter, platformy, co oznacza, możesz wybrać dowolną **hostowany program VS2017** lub **hostowanych 1604 Ubuntu** w **puli agenta** (lub inne zarządzane z wykorzystaniem przez agentów samodzielnie). Wybierz pozycję "+" i Dodaj jedno zadanie.

    ![Dodaj zadania dla pytań i odpowiedzi](./media/how-to-ci-cd/add-task-qa.png)

5. W zadaniu usługi Azure IoT Edge, przejdź do **akcji** listy rozwijanej wybierz **wdrażanie na urządzeniu usługi IoT Edge**. Wybierz swoje **subskrypcji platformy Azure** i wejściowego swoje **nazwę Centrum IoT Hub**. Można wdrożyć na jednym lub wielu urządzeń. Jeśli wdrażasz do **wiele urządzeń**, należy określić urządzenie **warunek docelowy**. Warunek docelowy jest filtr, aby dopasować zbiór urządzeń brzegowych w usłudze IoT Hub. Jeśli chcesz użyć znaczników urządzenia jako warunek, należy zaktualizować urządzenie odpowiednie tagi z bliźniaczej reprezentacji urządzenia usługi IoT Hub. Aktualizacja **identyfikator wdrożenia usługi IoT Edge** "Wdrażanie — qa" w zaawansowanych ustawieniach. Załóżmy, że masz kilka usługi IoT Edge urządzenia oznaczone jako "qa", a następnie konfiguracji zadania powinny być tak jak w poniższym zrzucie ekranu. 

    ![Wdrażanie w odpowiedzi na pytania](./media/how-to-ci-cd/deploy-to-qa.png)

    Zapisz nowy potok wersji za pomocą **Zapisz** przycisku. A następnie wybierz **potoku** aby wrócić do potoku.

6. Drugi etap to w środowisku produkcyjnym. Aby dodać nowy etap "PROD", można sklonować etap "QA" i Zmień nazwę sklonowanego etap **PROD**,

    ![Klonowanie etapu](./media/how-to-ci-cd/clone-stage.png)

7. Skonfiguruj zadania w środowisku produkcyjnym. Załóżmy, że masz kilka usługi IoT Edge urządzenia oznaczone jako "prod", w konfiguracji zadania aktualizacji warunek docelowy "prod" i Ustaw identyfikator wdrożenia jako "Wdróż prod" w ustawieniach zaawansowanych. Zapisz go z **Zapisz** przycisku. A następnie wybierz **potoku** aby wrócić do potoku.
    
    ![Wdrażanie w środowisku produkcyjnym](./media/how-to-ci-cd/deploy-to-prod.png)

7. Obecnie nasz artefakt kompilacji zostanie wyzwolone stale przy **odpowiedzi na pytania** etapu i następnie **produktu** etapu. Jednak większość czasu konieczne zintegrowanie kilka przypadków testowych na urządzeniach odpowiedzi na pytania i ręcznie zatwierdzić usługi bits. Bity zostanie później wdrożony do środowiska produkcyjnego. Konfigurowanie zatwierdzenia na etapie produkcji jako poniższym zrzucie ekranu.

    1. Otwórz **warunki przed wdrożeniem** panelu Ustawienia.

        ![Otwórz warunki przed wdrożeniem](./media/how-to-ci-cd/pre-deploy-conditions.png)    

    2. Ustaw **włączone** w **zatwierdzenia przed wdrożeniem**. A następnie wypełnij **osoby zatwierdzające** danych wejściowych. Następnie zapisz go z **Zapisz** przycisku.
    
        ![Zestaw warunków](./media/how-to-ci-cd/set-pre-deployment-conditions.png)


8. Teraz potok wydania ustawiono jako poniższym zrzucie ekranu.

    ![Potok wydania](./media/how-to-ci-cd/release-pipeline.png)

    
## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>Sprawdź IoT Edge ciągłej integracji/ciągłego wdrażania z kompilacją a potoki wersji

W tej sekcji spowoduje wyzwolenie kompilacji zapewnienie potoku ciągłej integracji/ciągłego Dostarczania pracy. Następnie sprawdź, że wdrożenie zakończy się pomyślnie.

1. Aby wyzwolić zadanie kompilacji, możesz wypchnięciu zatwierdzenia do repozytorium kodu źródłowego lub ręcznie wyzwolić ją. Możesz wyzwolić zadanie kompilacji w potoku kompilacji, wybierając **kolejki** przycisku tak jak w poniższym zrzucie ekranu.

    ![Ręczne wyzwalacza](./media/how-to-ci-cd/manual-trigger.png)

2. Jeśli proces kompilacji zakończy się powodzeniem, wyzwolą wydanie do **QA** etapu. Przejdź do potoku dzienniki kompilacji i powinien zostać wyświetlony poniższy zrzut ekranu.

    ![Kompilacja dzienników](./media/how-to-ci-cd/build-logs.png)

3. Pomyślne wdrożenie do **QA** etapu będą wyzwalać powiadomienie do osoby zatwierdzającej. Przejdź do potoku wydania, zobaczysz Poniższy zrzut ekranu. 

    ![Oczekuje na zatwierdzenie](./media/how-to-ci-cd/pending-approval.png)


4. Po osoby zatwierdzającej zatwierdzić tę zmianę, można je było wdrożyć do **PROD**.

    ![Wdrażanie do produkcji](./media/how-to-ci-cd/approve-and-deploy-to-prod.png)


## <a name="next-steps"></a>Kolejne kroki

* Omówienie wdrożenia usługi IoT Edge w [wdrożeń zrozumieć usługi IoT Edge dla urządzeń z jednej lub w odpowiedniej skali](module-deployment-monitoring.md)
* Przewodnik po krokach do utworzenia, aktualizacji lub usunięcia wdrożenia w [wdrażanie i monitorowanie moduły usługi IoT Edge na dużą skalę](how-to-deploy-monitor.md).

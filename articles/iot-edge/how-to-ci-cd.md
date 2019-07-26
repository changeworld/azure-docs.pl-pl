---
title: Ciągła integracja i ciągłe wdrażanie — usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Skonfiguruj ciągłą integrację i ciągłe wdrażanie — usługi Azure IoT Edge przy użyciu infrastruktury DevOps platformy Azure, Azure potoków
author: shizn
manager: philmea
ms.author: xshi
ms.date: 01/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 659a6f5acaac848084ed1e9590a414191542b54a
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2019
ms.locfileid: "68414628"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>Ciągła integracja i ciągłe wdrażanie w usłudze Azure IoT Edge

Możesz łatwo przyjmować DevOps z aplikacjami Azure IoT Edge przy użyciu wbudowanych zadań Azure IoT Edge w Azure Pipelines. W tym artykule pokazano, jak za pomocą funkcji ciągłej integracji i ciągłego wdrażania Azure Pipelines szybko i Azure IoT Edge wydajniej kompilować, testować i wdrażać aplikacje. 

W tym artykule dowiesz się, jak za pomocą wbudowanych Azure IoT Edge zadań dla Azure Pipelines utworzyć dwa potoki dla rozwiązania IoT Edge. Najpierw pobiera kod i kompiluje rozwiązanie, wypychając obrazy modułu do rejestru kontenerów i tworząc manifest wdrożenia. Druga z nich wdraża moduły na IoT Edge urządzenia.  

![Diagram — ciągła Integracja i ciągłe dostarczanie gałęzie rozwoju i produkcji](./media/how-to-ci-cd/cd.png)


## <a name="prerequisites"></a>Wymagania wstępne

* Repozytorium Azure Repos. Jeśli go nie masz, możesz [utworzyć nowe repozytorium Git w projekcie](https://docs.microsoft.com/azure/devops/repos/git/create-new-repo?view=vsts&tabs=new-nav).
* Rozwiązanie IoT Edge zostało zatwierdzone i wypchnięte do repozytorium. Jeśli chcesz utworzyć nowe przykładowe rozwiązanie do testowania tego artykułu, postępuj zgodnie z instrukcjami w temacie [programowanie i debugowanie modułów w Visual Studio Code](how-to-vs-code-develop-module.md) lub [opracowywanie i C# debugowanie modułów w programie Visual Studio](how-to-visual-studio-develop-csharp-module.md).
   * W tym artykule wystarczy, że jest to folder rozwiązania utworzony przez szablony IoT Edge w Visual Studio Code lub Visual Studio. Nie musisz kompilować, wypchnięciować, wdrażać ani debugować tego kodu przed kontynuowaniem. Te procesy zostaną ustawione w Azure Pipelines. 
   * Jeśli tworzysz nowe rozwiązanie, najpierw Sklonuj repozytorium lokalnie. Następnie podczas tworzenia rozwiązania można utworzyć je bezpośrednio w folderze repozytorium. W tym miejscu możesz łatwo zatwierdzić i wypchnąć nowe pliki. 
* Rejestr kontenerów, w którym można wypchnąć obrazy modułów. Możesz użyć [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) lub rejestru innej firmy. 
* Aktywne [Centrum IoT Hub](../iot-hub/iot-hub-create-through-portal.md) z co najmniej IoT Edge urządzeniami do testowania oddzielnych etapów wdrożenia testowego i produkcyjnego. Możesz skorzystać z artykułów szybkiego startu, aby utworzyć urządzenie IoT Edge w systemie [Linux](quickstart-linux.md) lub [Windows](quickstart.md)


Aby uzyskać więcej informacji o korzystaniu z Azure Repos, zobacz [udostępnianie kodu za pomocą programu Visual Studio i Azure Repos](https://docs.microsoft.com/azure/devops/repos/git/share-your-code-in-git-vs?view=vsts)

## <a name="configure-continuous-integration"></a>Konfigurowanie ciągłej integracji
W tej sekcji utworzysz nowy potok kompilacji. Skonfiguruj potok do automatycznego uruchamiania podczas ewidencjonowania wszelkich zmian w przykładowym rozwiązaniu IoT Edge i Opublikuj dzienniki kompilacji.

>[!NOTE]
>W tym artykule jest wykorzystywany projektant wizualny usługi Azure DevOps. Przed wykonaniem kroków opisanych w tej sekcji należy wyłączyć funkcję Podgląd nowego środowiska tworzenia potoku YAML. 
>1. W usłudze Azure DevOps wybierz ikonę profilu, a następnie wybierz pozycję **funkcje w wersji**zapoznawczej.
>2. Wyłącz **nowe środowisko tworzenia potoku YAML** . 
>
>Aby uzyskać więcej informacji, zobacz [Tworzenie potoku kompilacji](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav#create-a-build-pipeline).

1. Zaloguj się do organizacji usługi Azure DevOps (**https\/:/dev.Azure.com/{Your Organization}/** ) i Otwórz projekt zawierający repozytorium IoT Edge rozwiązania.

   W tym artykule utworzyliśmy repozytorium o nazwie **IoTEdgeRepo**. To repozytorium zawiera **IoTEdgeSolution** , w którym znajduje się kod modułu o nazwie **filtermodule**. 

   ![Otwórz projekt DevOps](./media/how-to-ci-cd/init-project.png)

2. Przejdź do Azure Pipelines w projekcie. Otwórz kartę **kompilacje** i wybierz pozycję **Nowy potok**. Lub, jeśli masz już potoki kompilacji, wybierz przycisk **Nowy** . Następnie wybierz kolejno pozycje **Nowy potok kompilacji**.

    ![Tworzenie nowego potoku kompilacji](./media/how-to-ci-cd/add-new-build.png)

3. Postępuj zgodnie z monitami, aby utworzyć potok. 

   1. Podaj informacje źródłowe dla nowego potoku kompilacji. Wybierz pozycję **Azure Repos git** jako źródło, a następnie wybierz projekt, repozytorium i gałąź, w której znajduje się kod rozwiązania IoT Edge. Następnie wybierz pozycję **Kontynuuj**. 

      ![Wybierz źródło potoku](./media/how-to-ci-cd/pipeline-source.png)

   2. Wybierz **puste zadanie** zamiast szablonu. 

      ![Rozpocznij z pustym procesem](./media/how-to-ci-cd/start-with-empty.png)

4. Po utworzeniu potoku nastąpi przejście do edytora potoku. W opisie potoku Wybierz poprawną pulę agentów na podstawie platformy docelowej: 
    
   * Jeśli chcesz tworzyć moduły w amd64 platformy dla kontenerów systemu Linux, wybierz opcję **hostowanych 1604 Ubuntu**

   * Jeśli chcesz skompilować moduły na platformie amd64 dla kontenerów systemu Windows 1809, musisz skonfigurować samoobsługowy [Agent w systemie Windows](https://docs.microsoft.com/azure/devops/pipelines/agents/v2-windows?view=vsts).

   * Jeśli chcesz skompilować moduły na platformie arm32v7 lub arm64 dla kontenerów systemu Linux, musisz [skonfigurować agenta samoobsługowego w systemie Linux](https://blogs.msdn.microsoft.com/iotdev/2018/11/13/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent/).
    
     ![Konfigurowanie puli agentów kompilacji](./media/how-to-ci-cd/configure-env.png)

5. Potok jest wstępnie skonfigurowany przy użyciu zadania o nazwie **zadanie agenta 1**. Wybierz znak plus ( **+** ), aby dodać trzy zadania do zadania: **Azure IoT Edge** dwa razy i **Opublikuj artefakty kompilacji** jeden raz. (Umieść wskaźnik myszy nad nazwą każdego zadania, aby wyświetlić przycisk **Dodaj** ).

   ![Dodawanie Azure IoT Edge zadania](./media/how-to-ci-cd/add-iot-edge-task.png)

   Gdy zostaną dodane wszystkie trzy zadania, zadanie agenta będzie wyglądać podobnie do poniższego przykładu:
    
   ![Trzy zadania w potoku kompilacji](./media/how-to-ci-cd/add-tasks.png)

6. Wybierz pierwsze zadanie **Azure IoT Edge** , aby je edytować. To zadanie kompiluje wszystkie moduły w rozwiązaniu z określoną platformą docelową, a także generuje plik **Deployment. JSON** , który informuje IoT Edge urządzeń o sposobie konfigurowania wdrożenia.

   * **Nazwa wyświetlana**: Zaakceptuj domyślne **obrazy modułu Azure IoT Edge-Build**.
   * **Akcja**: Zaakceptuj domyślne **obrazy modułu kompilacji**. 
   * **plik Template. JSON**: Wybierz wielokropek ( **...** ) i przejdź do pliku **Deployment. Template. JSON** w repozytorium zawierającym IoT Edge rozwiązanie. 
   * **Platforma domyślna**: Wybierz odpowiednią platformę dla modułów na podstawie docelowego urządzenia IoT Edge. 
   * **Zmienne wyjściowe**: Zmienne wyjściowe zawierają nazwę odwołania, której można użyć do skonfigurowania ścieżki pliku, w którym zostanie wygenerowany plik Deployment. JSON. Ustaw nazwę odwołania na coś do zapamiętania, na przykład **Edge**. 

7. Wybierz drugie zadanie **Azure IoT Edge** , aby je edytować. To zadanie powoduje wypchnięcie wszystkich obrazów modułu do rejestru kontenerów, które zostały wybrane. Dodaje także poświadczenia rejestru kontenera do pliku **Deployment. JSON** , dzięki czemu urządzenie IoT Edge będzie miało dostęp do obrazów modułów. 

   * **Nazwa wyświetlana**: Nazwa wyświetlana jest automatycznie aktualizowana, gdy pole akcji ulega zmianie. 
   * **Akcja**: Użyj listy rozwijanej, aby wybrać **obrazy modułu wypychania**. 
   * **Typ rejestru kontenerów**: Wybierz typ rejestru kontenerów, który ma być używany do przechowywania obrazów modułu. Formularz zmienia się w zależności od wybranego typu rejestru. W przypadku wybrania **Azure Container Registry**Użyj listy rozwijanej, aby wybrać subskrypcję platformy Azure i nazwę rejestru kontenerów. W przypadku wybrania opcji **ogólne Container Registry**wybierz pozycję **Nowy** , aby utworzyć połączenie usługi rejestru. 
   * **plik Template. JSON**: Wybierz wielokropek ( **...** ) i przejdź do pliku **Deployment. Template. JSON** w repozytorium zawierającym IoT Edge rozwiązanie. 
   * **Platforma domyślna**: Wybierz tę samą platformę co skompilowane obrazy modułu.

   Jeśli masz wiele rejestry kontenerów do hostowania obrazów modułu, musisz zduplikowane to zadanie, wybierz inny rejestr kontenerów, a następnie użyj **obejścia moduły** w ustawieniach zaawansowanych, aby pominąć obrazów, które nie są w tym określonego rejestru.

8. Wybierz zadanie **Opublikuj artefakty kompilacji** , aby je edytować. Podaj ścieżkę do pliku wdrożenia wygenerowanego przez zadanie kompilacji. Ustaw **ścieżkę do wartości Opublikuj** , aby odpowiadała zmiennej wyjściowej ustawionej w zadaniu modułu kompilacji. Na przykład `$(edge.DEPLOYMENT_FILE_PATH)`. Pozostaw pozostałe wartości jako wartości domyślne. 

9. Otwórz kartę **wyzwalacze** i zaznacz pole wyboru **Włącz integrację ciągłą**. Upewnij się, że gałąź z kodem jest dołączony.

    ![Włącz wyzwalacz ciągłej integracji](./media/how-to-ci-cd/configure-trigger.png)

10. Zapisz nowy potok kompilacji przy użyciu przycisku **Zapisz** .

Ten potok jest teraz skonfigurowany do automatycznego uruchamiania podczas wypychania nowego kodu do repozytorium. Ostatnim zadaniem, opublikowanie artefaktów potoku, jest wyzwalanie potoku wydania. Przejdź do następnej sekcji, aby skompilować potok wersji. 

## <a name="configure-continuous-deployment"></a>Konfigurowanie ciągłego wdrażania
W tej sekcji utworzysz potok wydania, który jest skonfigurowany do automatycznego uruchamiania, gdy potok kompilacji pospadnie artefakty, a dzienniki wdrażania zostaną wyświetlone w Azure Pipelines.

W tej sekcji utworzysz dwa różne etapy, jeden dla wdrożeń testowych i jeden dla wdrożeń produkcyjnych. 

### <a name="create-test-stage"></a>Utwórz etap testu

Utwórz nowy potok i skonfiguruj jego pierwszy etap w celu zapewnienia jakości wdrożeń. 

1. W **wersji** kartę, wybrać **+ nowy potok**. Lub, jeśli masz już potoki wersji, wybierz przycisk **+ Nowy** i wybierz pozycję **+ Nowy potok wersji**.  

    ![Dodaj potoku tworzenia wersji](./media/how-to-ci-cd/add-release-pipeline.png)

2. Po wyświetleniu monitu o wybranie szablonu wybierz pozycję Rozpocznij od **pustego zadania**.

    ![Uruchom zadanie puste](./media/how-to-ci-cd/start-with-empty-job.png)

3. Nowy potok wydania jest inicjowany z jednym etapem, zwanym **etapem 1**. Zmień nazwę etapu 1 na **pytania** i traktowanie jako środowisko testowe. Zwykle potoki ciągłego wdrażania mają wiele etapów. Możesz tworzyć więcej informacji na podstawie DevOps. Zamknij okno Szczegóły etapu po jego zmianie nazwy. 

    ![Tworzenie etapów środowiska testowego](./media/how-to-ci-cd/QA-env.png)

4. Połącz wydanie z artefaktami kompilacji, które są publikowane przez potok kompilacji. Kliknij przycisk **Dodaj** w obszarze artefaktów.

   ![Dodaj artefakty](./media/how-to-ci-cd/add-artifacts.png)  
    
5. Na **stronie Dodawanie artefaktu**wybierz pozycję **kompilacja**typu źródła. Następnie wybierz projekt i utworzony potok kompilacji. Następnie wybierz pozycję **Dodaj**.

   ![Dodaj artefakt kompilacji](./media/how-to-ci-cd/add-an-artifact.png)

6. Otwórz wyzwalacze artefaktów i wybierz przełącznik, aby włączyć wyzwalacz ciągłego wdrażania. Teraz Nowa wersja zostanie utworzona za każdym razem, gdy zostanie udostępniona nowa kompilacja.

   ![Konfigurowanie wyzwalacza ciągłego wdrażania](./media/how-to-ci-cd/add-a-trigger.png)

7. Etap **pytania i odpowiedzi** jest wstępnie konfigurowany przy użyciu jednego zadania i zero zadań. Z menu potok wybierz pozycję **zadania** , a następnie wybierz etap **pytania i odpowiedzi** .  Wybierz zadanie i liczbę zadań, aby skonfigurować zadania na tym etapie.

    ![Skonfiguruj zadania pytań i odpowiedzi](./media/how-to-ci-cd/view-stage-tasks.png)

8. Na etapie pytania i odpowiedzi powinno zostać wyświetlone domyślne **zadanie agenta**. Można skonfigurować szczegółowe informacje o zadaniu agenta, ale zadanie wdrażania jest zależne od platformy, aby można było użyć **hostowanej program VS2017** lub **hostowanej Ubuntu 1604** w **puli agentów** (lub dowolnego innego agenta zarządzanego przez siebie). 

9. Wybierz znak plus ( **+** ), aby dodać jedno zadanie. Wyszukaj i Dodaj **Azure IoT Edge**. 

    ![Dodaj zadania dla pytań i odpowiedzi](./media/how-to-ci-cd/add-task-qa.png)

10. Wybierz nowe zadanie Azure IoT Edge i skonfiguruj je przy użyciu następujących wartości:

    * **Nazwa wyświetlana**: Nazwa wyświetlana jest automatycznie aktualizowana, gdy pole akcji ulega zmianie. 
    * **Akcja**: Użyj listy rozwijanej, aby wybrać opcję **Wdróż do IoT Edge urządzenia**. Zmiana wartości akcji spowoduje również zaktualizowanie nazwy wyświetlanej zadania tak, aby była zgodna.
    * **Subskrypcja platformy Azure**: Wybierz subskrypcję zawierającą IoT Hub.
    * **Nazwa IoT Hub**: Wybierz Centrum IoT hub. 
    * **Wybierz jedno/kilka urządzeń**: Wybierz, czy potok wydania ma zostać wdrożony na jednym urządzeniu, czy na wielu urządzeniach. 
      * W przypadku wdrażania na jednym urządzeniu wprowadź **IoT Edge identyfikator urządzenia**. 
      * W przypadku wdrażania na wielu urządzeniach należy określić **warunek docelowy**urządzenia. Warunek docelowy jest filtr, aby dopasować zbiór urządzeń brzegowych w usłudze IoT Hub. Jeśli chcesz użyć znaczników urządzenia jako warunek, należy zaktualizować urządzenie odpowiednie tagi z bliźniaczej reprezentacji urządzenia usługi IoT Hub. Zaktualizuj **IoT Edge identyfikator wdrożenia** i **IoT Edge priorytet wdrożenia** w obszarze Ustawienia zaawansowane. Aby uzyskać więcej informacji na temat tworzenia wdrożenia dla wielu urządzeń, zobacz [opis IoT Edge wdrożeń automatycznych](module-deployment-monitoring.md).

11. Wybierz pozycję **Zapisz** , aby zapisać zmiany w nowym potoku wydania. Wróć do widoku potoku, wybierając pozycję **potok** z menu. 

### <a name="create-production-stage"></a>Utwórz etap produkcyjny

Utwórz drugi etap potoku wydania dla wdrożenia produkcyjnego. 

1. Utwórz drugi etap dla środowiska produkcyjnego, usuwając etap usługi pytania i odpowiedzi. Umieść kursor na etapie pytań i odpowiedzi, a następnie wybierz przycisk klon. 

    ![Klonowanie etapu](./media/how-to-ci-cd/clone-stage.png)

2. Wybierz nowy etap o nazwie **kopia funkcji pytań i odpowiedzi**, aby otworzyć jego właściwości. Zmień nazwę **etapu na**produkcyjny dla środowiska produkcyjnego. Zamknij okno właściwości etapu. 

3. Aby otworzyć zadania etapów PRODUKCYJNych, wybierz pozycję **zadania** z menu potok, a następnie wybierz etap **produkcyjny** . 

4. Wybierz zadanie Azure IoT Edge, aby skonfigurować je w środowisku produkcyjnym. Ustawienia wdrożenia są prawdopodobnie takie same dla pytań i odpowiedzi, z tą różnicą, że chcesz wskazać inne urządzenie lub zestaw urządzeń w środowisku produkcyjnym. Zaktualizuj pole Identyfikator urządzenia lub pola warunku docelowego i identyfikatora wdrożenia dla urządzeń produkcyjnych. 

5. Zapisz go za pomocą przycisku **Zapisz** . A następnie wybierz pozycję **potok** , aby wrócić do widoku potoku.
    
6. Sposób, w jaki ten potok wersji jest obecnie skonfigurowany, artefakt kompilacji wyzwoli etap kontroli **jakości** , a następnie etap **produkcyjny** za każdym razem, gdy zostanie ukończona Nowa kompilacja. Jednak zazwyczaj chcemy zintegrować niektóre przypadki testowe na urządzeniach z PYTANIAmi i ręcznie zatwierdzić wdrożenie dla środowiska produkcyjnego. Wykonaj następujące kroki, aby utworzyć warunek zatwierdzenia dla etapu PRODUKCYJNEgo:

    1. Otwórz panel Ustawienia **warunków przed wdrożeniem** .

        ![Otwórz warunki przed wdrożeniem](./media/how-to-ci-cd/pre-deploy-conditions.png)    

    2. Przełącz warunek **zatwierdzania przed wdrożeniem** na **włączony**. Dodaj co najmniej jednego użytkownika lub grupę w polu **osoby zatwierdzające** i dostosuj wszystkie inne zasady zatwierdzania. Aby zapisać zmiany, zamknij panel warunki przed wdrożeniem.
    
       ![Zestaw warunków](./media/how-to-ci-cd/set-pre-deployment-conditions.png)


7. Zapisz potok wersji za pomocą przycisku **Zapisz** . 

    
## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>Sprawdź IoT Edge ciągłej integracji/ciągłego wdrażania z kompilacją a potoki wersji

Aby wyzwolić zadanie kompilacji, możesz wypchnięciu zatwierdzenia do repozytorium kodu źródłowego lub ręcznie wyzwolić ją. W tej sekcji ręcznie wyzwolimy potok ciągłej integracji/ciągłego wdrażania, aby sprawdzić, czy działa. Następnie sprawdź, czy wdrożenie powiodło się.

1. Przejdź do potoku kompilacji utworzonego na początku tego artykułu. 

2. Możesz wyzwolić zadanie kompilacji w potoku kompilacji, wybierając przycisk **kolejki** , jak pokazano na poniższym zrzucie ekranu.

    ![Ręczne wyzwalacza](./media/how-to-ci-cd/manual-trigger.png)

3. Wybierz zadanie kompilacji, aby obserwować jego postępy. W przypadku pomyślnego ukończenia potoku kompilacji jest wyzwalane wydanie na etap kontroli **jakości** . 

    ![Kompilacja dzienników](./media/how-to-ci-cd/build-logs.png)

4. Pomyślne wdrożenie na etapie **pytań i odpowiedzi** wyzwala powiadomienie do osoby zatwierdzającej. Sprawdź, czy moduły zostały pomyślnie wdrożone na urządzeniu lub urządzeniach, które są przeznaczone do etapu kontroli jakości. Następnie przejdź do pozycji Zwolnij potok i przekaż zatwierdzenie wersji, aby przejść do etapu PRODUKCYJNEgo, wybierając  przycisk produkcyjny, a następnie wybierając pozycję Zatwierdź. 

    ![Oczekuje na zatwierdzenie](./media/how-to-ci-cd/pending-approval.png)

5. Po osoby zatwierdzającej zatwierdzić tę zmianę, można je było wdrożyć do **PROD**.

## <a name="next-steps"></a>Kolejne kroki

* Omówienie wdrożenia usługi IoT Edge w [wdrożeń zrozumieć usługi IoT Edge dla urządzeń z jednej lub w odpowiedniej skali](module-deployment-monitoring.md)
* Przewodnik po krokach do utworzenia, aktualizacji lub usunięcia wdrożenia w [wdrażanie i monitorowanie moduły usługi IoT Edge na dużą skalę](how-to-deploy-monitor.md).

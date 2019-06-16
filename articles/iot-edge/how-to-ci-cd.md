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
ms.openlocfilehash: f449449c542ce6ac04daa58ff37a3577f0d75aee
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61222061"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>Ciągła integracja i ciągłe wdrażanie w usłudze Azure IoT Edge

Można łatwo adaptacji metodyki DevOps za pomocą aplikacji usługi Azure IoT Edge za pomocą wbudowanych zadań usługi Azure IoT Edge w potokach platformy Azure. W tym artykule przedstawiono, jak można użyć ciągłej integracji i ciągłego wdrażania funkcji Azure potoków do tworzenia, testowania i wdrażania aplikacji, szybko i skutecznie do usługi Azure IoT Edge. 

W tym artykule dowiesz się, jak za pomocą wbudowanych zadań usługi Azure IoT Edge dla potoków usługi Azure, Utwórz dwa potoki swojego rozwiązania IoT Edge. Pierwszy wymagany kodu i kompiluje rozwiązanie, wypychanie obrazów modułu do rejestru kontenerów i tworzenie manifestu wdrażania. Drugi wdraża moduły docelowe urządzenia usługi IoT Edge.  

![Diagram — ciągła Integracja i ciągłe dostarczanie gałęzie rozwoju i produkcji](./media/how-to-ci-cd/cd.png)


## <a name="prerequisites"></a>Wymagania wstępne

* Repozytorium repozytoriów platformy Azure. Jeśli nie masz, możesz to zrobić [Tworzenie nowego repozytorium Git w projekcie](https://docs.microsoft.com/azure/devops/repos/git/create-new-repo?view=vsts&tabs=new-nav).
* Rozwiązania usługi IoT Edge zatwierdzeniu i wypchnięciu do repozytorium. Jeśli chcesz utworzyć nowe rozwiązanie próbki do testowania w tym artykule, wykonaj kroki opisane w [modułów programowanie i debugowanie w programie Visual Studio Code](how-to-vs-code-develop-module.md) lub [programowanie i debugowanie C# modułów w programie Visual Studio](how-to-visual-studio-develop-csharp-module.md).
   * W tym artykule potrzebne jest folder rozwiązania utworzone za pomocą szablonów usługi IoT Edge w programie Visual Studio Code lub Visual Studio. Nie trzeba tworzyć, wypychania, wdrażania lub debugować ten kod przed kontynuowaniem. Należy skonfigurować te procesy w potokach platformy Azure. 
   * Jeśli tworzysz nowe rozwiązanie, najpierw sklonuj repozytorium lokalnie. Następnie po utworzeniu rozwiązania można utworzyć bezpośrednio w folderze repozytorium. Można łatwo zatwierdzić i wypchnąć nowe pliki w tym miejscu. 
* Rejestr kontenerów, w którym będzie można wypchnąć obrazy modułu. Możesz użyć [usługi Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) lub rejestru innych firm. 
* Aktywne [usługi IoT hub](../iot-hub/iot-hub-create-through-portal.md) z co najmniej urządzenia usługi IoT Edge do testowania oddzielne etapy wdrażania testowych i produkcyjnych. Możesz wykonać artykuły Szybki Start, aby utworzyć urządzenia usługi IoT Edge na [Linux](quickstart-linux.md) lub [Windows](quickstart.md)


Aby uzyskać więcej informacji o korzystaniu z repozytoriów platformy Azure, zobacz [udostępnić swój kod za pomocą programu Visual Studio i repozytoriów platformy Azure](https://docs.microsoft.com/azure/devops/repos/git/share-your-code-in-git-vs?view=vsts)

## <a name="configure-continuous-integration"></a>Skonfiguruj ciągłą integrację
W tej sekcji utworzysz nowy potok kompilacji. Konfigurowanie potoku do automatycznego uruchamiania podczas Zaewidencjonuj wszelkie zmiany, na przykład rozwiązania usługi IoT Edge i publikowanie dzienników kompilacji.

>[!NOTE]
>W tym artykule używany jest Projektant wizualny DevOps platformy Azure. Przed wykonaniem kroków opisanych w tej sekcji, należy wyłączyć funkcję w wersji zapoznawczej dla nowego środowiska tworzenia potoku YAML. 
>1. W DevOps platformy Azure, wybierz ikonę Twojego profilu, a następnie wybierz pozycję **funkcje w wersji zapoznawczej**.
>2. Włącz **YAML nowy potok tworzenia środowiska** wyłączone. 
>
>Aby uzyskać więcej informacji, zobacz [instrukcje tworzenia potoku kompilacji](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav#create-a-build-pipeline).

1. Zaloguj się do Twojej organizacji DevOps platformy Azure (**https:\/organizacji /dev.azure.com/{your} /** ), a następnie otwórz projekt, który zawiera repozytorium rozwiązania usługi IoT Edge.

   W tym artykule, utworzyliśmy repozytorium o nazwie **IoTEdgeRepo**. Tego repozytorium zawiera **IoTEdgeSolution** który ma kod dla modułu o nazwie **filtermodule**. 

   ![Otwórz swój projekt DevOps](./media/how-to-ci-cd/init-project.png)

2. Przejdź do potoków usługi Azure w swoim projekcie. Otwórz **kompilacje** kartę, a następnie wybierz pozycję **nowy potok**. Lub, jeśli masz już potoki kompilacji, wybierz opcję **New** przycisku. Następnie wybierz **potoku tworzenia nowych**.

    ![Tworzenie nowego potoku kompilacji](./media/how-to-ci-cd/add-new-build.png)

3. Postępuj zgodnie z monitami, aby utworzyć potok. 

   1. Podaj informacje źródłowe dla nowego potoku kompilacji. Wybierz **Azure repozytoriów Git** jako źródło, następnie wybierz projekt, repozytorium i gałąź, na którym znajduje się kod rozwiązania usługi IoT Edge. Następnie wybierz **Kontynuuj**. 

      ![Wybierz źródło potoku](./media/how-to-ci-cd/pipeline-source.png)

   2. Wybierz **puste zadanie** zamiast szablonu. 

      ![Rozpocznij z pustym procesem](./media/how-to-ci-cd/start-with-empty.png)

4. Po utworzeniu potoku, nastąpi przekierowanie do edytora potoku. W opisie potoku wybierz pulę prawidłowych odpowiadające platformie docelowej: 
    
   * Jeśli chcesz tworzyć moduły w amd64 platformy dla kontenerów systemu Linux, wybierz opcję **hostowanych 1604 Ubuntu**

   * Jeśli chcesz tworzyć moduły w amd64 platformy dla kontenerów Windows 1809, musisz [skonfiguruj samodzielnie hostowanego agenta na Windows](https://docs.microsoft.com/azure/devops/pipelines/agents/v2-windows?view=vsts).

   * Jeśli chcesz tworzyć moduły w arm32v7 platformy dla kontenerów systemu Linux, musisz [Konfigurowanie własnego agenta w systemie Linux](https://blogs.msdn.microsoft.com/iotdev/2018/11/13/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent/).
    
     ![Konfigurowanie puli agentów kompilacji](./media/how-to-ci-cd/configure-env.png)

5. Potok jest wstępnie skonfigurowana za pomocą zadania o nazwie **zadania agenta 1**. Wybierz znak plus ( **+** ) można dodać trzy zadania dla zadania: **Usługa Azure IoT Edge** dwa razy, a **publikowanie artefaktów kompilacji** po. (Kursor na nazwie każdego zadania, aby zobaczyć **Dodaj** przycisku.)

   ![Dodaj zadanie usługi Azure IoT Edge](./media/how-to-ci-cd/add-iot-edge-task.png)

   Po dodaniu wszystkich trzech zadań, zadania agenta wygląda następująco:
    
   ![Trzy zadania w potoku kompilacji](./media/how-to-ci-cd/add-tasks.png)

6. Zaznacz pierwszą pozycję **usługi Azure IoT Edge** zadanie, aby go edytować. To zadanie tworzy wszystkie moduły w rozwiązaniu za pomocą platformy docelowej, można wybrać, generuje także **deployment.json** plików, których urządzenia usługi IoT Edge można skonfigurować wdrożenie.

   * **Nazwa wyświetlana**: Zaakceptuj wartość domyślną **usługi Azure IoT Edge — obrazy modułu kompilacji**.
   * **Akcja**: Zaakceptuj wartość domyślną **kompilowanie obrazów modułu**. 
   * **. plik template.json**: Wybierz przycisk wielokropka ( **...** ) i przejdź do **deployment.template.json** pliku w repozytorium, który zawiera rozwiązania usługi IoT Edge. 
   * **Domyślna Platforma**: Wybierz odpowiednią platformę dla moduły oparte na urządzenie docelowe urządzenie usługi IoT Edge. 
   * **Zmienne wyjściowe**: Zmienne wyjściowe zawierają nazwę odniesienia, która służy do konfigurowania ścieżki pliku gdzie zostanie wygenerowany plik deployment.json. Ustaw nazwę odwołania coś zapamiętania, takich jak **krawędzi**. 

7. Wybierz drugą **usługi Azure IoT Edge** zadanie, aby go edytować. To zadanie wypycha wszystkie obrazy modułu do rejestru kontenerów, który wybierzesz. Dodaje także poświadczenia rejestru kontenerów, aby **deployment.json** plików tak, aby urządzenia usługi IoT Edge mogą uzyskiwać dostęp do obrazów modułu. 

   * **Nazwa wyświetlana**: Nazwa wyświetlana jest automatycznie aktualizowany po zmianie pola akcji. 
   * **Akcja**: Użyj listy rozwijanej, aby wybrać **wypychanie obrazów modułu**. 
   * **Typ rejestru kontenerów**: Wybierz typ rejestru kontenerów, którego używasz do przechowywania obrazów modułu. W zależności od typu rejestru możesz wybrać, zmian w formularzu. Jeśli wybierzesz **usługi Azure Container Registry**, użyj list rozwijanych, aby wybrać subskrypcję platformy Azure i nazwę rejestru kontenerów. Jeśli wybierzesz **ogólnego Container Registry**, wybierz opcję **New** do utworzenia połączenia z usługą rejestru. 
   * **. plik template.json**: Wybierz przycisk wielokropka ( **...** ) i przejdź do **deployment.template.json** pliku w repozytorium, który zawiera rozwiązania usługi IoT Edge. 
   * **Domyślna Platforma**: Wybierz tę samą platformę jako moduł wbudowanych obrazów.

   Jeśli masz wiele rejestry kontenerów do hostowania obrazów modułu, musisz zduplikowane to zadanie, wybierz inny rejestr kontenerów, a następnie użyj **obejścia moduły** w ustawieniach zaawansowanych, aby pominąć obrazów, które nie są w tym określonego rejestru.

8. Wybierz **publikowanie artefaktów kompilacji** zadanie, aby go edytować. Podaj ścieżkę pliku do pliku wdrożenia, wygenerowanego przez zadanie kompilacji. Ustaw **ścieżka do publikowania** wartość do dopasowania zmiennej danych wyjściowych, który zostało ustawiony w zadaniu moduł kompilacji. Na przykład `$(edge.DEPLOYMENT_FILE_PATH)`. Pozostałe wartości Pozostaw domyślne. 

9. Otwórz **wyzwalaczy** kartę i pole wyboru, aby **Włącz ciągłą integrację**. Upewnij się, że gałąź z kodem jest dołączony.

    ![Włącz wyzwalacz ciągłej integracji](./media/how-to-ci-cd/configure-trigger.png)

10. Zapisz nowy potok kompilacji za pomocą **Zapisz** przycisku.

Ten potok jest teraz skonfigurowane do automatycznego uruchamiania po wypchnięciu nowego kodu do repozytorium. Ostatnim zadaniem, publikowanie artefaktów potoku wyzwala potok tworzenia wersji. Przejdź do następnej sekcji, aby utworzyć potok wersji. 

## <a name="configure-continuous-deployment"></a>Konfigurowanie ciągłego wdrażania
W tej sekcji utworzysz potok wersji, który jest skonfigurowany do automatycznego uruchamiania podczas potok kompilacji umieszcza artefaktów i dzienniki wdrażania będzie wyświetlany w potokach platformy Azure.

W tej sekcji opisano tworzenie dwóch różnych etapach, co w przypadku wdrożeń testowych i jeden dla wdrożeń produkcyjnych. 

### <a name="create-test-stage"></a>Utwórz etapu testu

Utwórz nowy potok, a następnie skonfiguruj jego pierwszego etapu wdrożeń kontroli jakości. 

1. W **wersji** kartę, wybrać **+ nowy potok**. Lub, jeśli masz już potoki wydań, wybierz opcję **+ nowy** i wybrać **+ nowy potoku wydania**.  

    ![Dodaj potoku tworzenia wersji](./media/how-to-ci-cd/add-release-pipeline.png)

2. Po wyświetleniu monitu, aby wybrać szablon, wybierz zaczynać **puste zadanie**.

    ![Uruchom zadanie puste](./media/how-to-ci-cd/start-with-empty-job.png)

3. Nowy potok wydania jest inicjowana za pomocą jednego etapu, o nazwie **etap 1**. Zmień nazwę etapu 1, aby **QA** i jej traktowała jako środowisku testowym. Zwykle potoków ciągłego wdrażania mają wiele etapów. Można utworzyć więcej oparte na Twoje praktyki DevOps. Zamknij okno Szczegóły dotyczące etapu po jej nazwa zostanie zmieniona. 

    ![Tworzenie etapów środowiska testowego](./media/how-to-ci-cd/QA-env.png)

4. Połącz wydanie z artefaktów kompilacji, które są publikowane przez proces kompilacji. Kliknij przycisk **Dodaj** w obszarze artefaktów.

   ![Dodaj artefakty](./media/how-to-ci-cd/add-artifacts.png)  
    
5. W **Dodawanie strony artefaktu**, wybierz typ źródła **kompilacji**. Następnie wybierz projekt i potoku kompilacji, który został utworzony. Następnie wybierz pozycję **Dodaj**.

   ![Dodaj artefakt kompilacji](./media/how-to-ci-cd/add-an-artifact.png)

6. Otwórz wyzwalaczy artefaktu, a następnie wybierz przełącznik, aby umożliwić wyzwalacz ciągłego wdrażania. Teraz nowa wersja zostanie utworzony każdorazowo, gdy dostępna jest nowa kompilacja.

   ![Konfigurowanie wyzwalacza ciągłego wdrażania](./media/how-to-ci-cd/add-a-trigger.png)

7. **QA** etap jest skonfigurowany do korzystania z jednego zadania i zadania, zerowego. Wybierz z menu potok **zadania** wybierz **odpowiedzi na pytania** etapu.  Wybierz liczba zadań i zadań, aby skonfigurować zadania na tym etapie.

    ![Skonfiguruj zadania pytań i odpowiedzi](./media/how-to-ci-cd/view-stage-tasks.png)

8. Na etapie kontroli jakości, powinien zostać wyświetlony domyślny **zadania agenta**. Można skonfigurować szczegółowe informacje dotyczące zadania agenta, ale zadania wdrażania jest niezależnych od platformy, dlatego można użyć dowolnego **hostowany program VS2017** lub **hostowanych 1604 Ubuntu** w **puli agenta**(lub innych agentów zarządzanych przez siebie). 

9. Wybierz znak plus ( **+** ) aby dodać jedno zadanie. Wyszukaj i Dodaj **usługi Azure IoT Edge**. 

    ![Dodaj zadania dla pytań i odpowiedzi](./media/how-to-ci-cd/add-task-qa.png)

10. Wybierz nowe zadanie usługi Azure IoT Edge i skonfiguruj je przy użyciu następujących wartości:

    * **Nazwa wyświetlana**: Nazwa wyświetlana jest automatycznie aktualizowany po zmianie pola akcji. 
    * **Akcja**: Użyj listy rozwijanej, aby wybrać **wdrażanie na urządzeniu usługi IoT Edge**. Zmiana wartości akcji aktualizuje również nazwa wyświetlana zadania do dopasowania.
    * **Subskrypcja platformy Azure**: Wybierz subskrypcję, która zawiera Centrum IoT Hub.
    * **Nazwa centrum IoT Hub**: Wybierz Centrum IoT hub. 
    * **Wybierz urządzenie z jednym/wiele**: Wybierz, czy potok wydania do wdrażania na jednym lub kilku urządzeń. 
      * Jeśli wdrażasz do jednego urządzenia, wprowadź **identyfikator urządzenia usługi IoT Edge**. 
      * Jeśli są wdrażane na wielu urządzeniach, określ urządzenie **warunek docelowy**. Warunek docelowy jest filtr, aby dopasować zbiór urządzeń brzegowych w usłudze IoT Hub. Jeśli chcesz użyć znaczników urządzenia jako warunek, należy zaktualizować urządzenie odpowiednie tagi z bliźniaczej reprezentacji urządzenia usługi IoT Hub. Aktualizacja **identyfikator wdrożenia usługi IoT Edge** i **priorytetem wdrożenia usługi IoT Edge** w zaawansowanych ustawieniach. Aby uzyskać więcej informacji na temat tworzenia wdrożenia dla wielu urządzeń, zobacz [zrozumieć usługi IoT Edge automatycznego wdrożenia](module-deployment-monitoring.md).

11. Wybierz **Zapisz** Aby zapisać zmiany do nowego potoku tworzenia wersji. Powrót do widoku potoku, wybierając **potoku** z menu. 

### <a name="create-production-stage"></a>Tworzenie w fazie produkcji

Tworzenie drugiego etapu w potoku wydania wdrożenia produkcyjnego. 

1. Podejmuj drugiego etapu w środowisku produkcyjnym, klonowanie etapu pytań i odpowiedzi. Umieść kursor na etapie pytań i odpowiedzi, a następnie wybierz przycisk Klonuj. 

    ![Klonowanie etapu](./media/how-to-ci-cd/clone-stage.png)

2. Wybierz następny etap, o nazwie **kopiowania QA**, aby otworzyć jego właściwości. Zmień nazwę etapu na **PROD**, w środowisku produkcyjnym. Zamknij okno Właściwości etapu. 

3. Aby otworzyć zadania etapu produkcji, należy wybrać **zadania** menu potoku kliknij **PROD** etapu. 

4. Wybierz zadanie usługi Azure IoT Edge, aby skonfigurować, jeśli w środowisku produkcyjnym. Ustawienia wdrożenia są prawdopodobnie takie same dla pytań i odpowiedzi i produkcji, z tą różnicą, że ma pod kątem inne urządzenie lub zbiór urządzeń, w środowisku produkcyjnym. Aktualizacja pola Identyfikator urządzenia lub docelowej warunku i wdrażania pól identyfikatorów urządzeń z systemem produkcyjnym. 

5. Zapisz go z **Zapisz** przycisku. A następnie wybierz **potoku** aby powrócić do widoku potoków.
    
6. Sposób, w tym potoku wydania jest obecnie skonfigurowany, wywoła artefakt kompilacji **odpowiedzi na pytania** etapu i następnie **produktu** etap za każdym razem, gdy nowa kompilacja zostanie ukończona. Jednak zazwyczaj chcesz zintegrować kilka przypadków testowych na urządzeniach odpowiedzi na pytania i ręcznie zatwierdzić wdrożenie w środowisku produkcyjnym. Użyj następujących kroków, aby utworzyć warunek zatwierdzenia dla etapu produkcji:

    1. Otwórz **warunki przed wdrożeniem** panel ustawień.

        ![Otwórz warunki przed wdrożeniem](./media/how-to-ci-cd/pre-deploy-conditions.png)    

    2. Przełącz **zatwierdzenia przed wdrożeniem** warunku do **włączone**. Dodaj użytkowników lub grup w **osoby zatwierdzające** pola, a inne zasady zatwierdzenia, które chcesz dostosować. Aby zapisać zmiany, Zamknij panel warunki przed wdrożeniem.
    
       ![Zestaw warunków](./media/how-to-ci-cd/set-pre-deployment-conditions.png)


7. Zapisz potok wersji przy użyciu **Zapisz** przycisku. 

    
## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>Sprawdź IoT Edge ciągłej integracji/ciągłego wdrażania z kompilacją a potoki wersji

Aby wyzwolić zadanie kompilacji, możesz wypchnięciu zatwierdzenia do repozytorium kodu źródłowego lub ręcznie wyzwolić ją. W tej sekcji możesz ręcznie wyzwolić potok CI/CD, aby sprawdzić, czy działa. Następnie sprawdź, czy wdrożenie zakończy się pomyślnie.

1. Przejdź do potoku kompilacji, który został utworzony na początku tego artykułu. 

2. Możesz wyzwolić zadanie kompilacji w potoku kompilacji, wybierając **kolejki** przycisku tak jak w poniższym zrzucie ekranu.

    ![Ręczne wyzwalacza](./media/how-to-ci-cd/manual-trigger.png)

3. Wybierz zadanie kompilacji, aby obserwować jego postęp. Jeśli proces kompilacji zakończy się powodzeniem, wyzwala wydanie do **QA** etapu. 

    ![Kompilacja dzienników](./media/how-to-ci-cd/build-logs.png)

4. Pomyślne wdrożenie do **QA** etapu wyzwala powiadomienia do osoby zatwierdzającej. Sprawdź, moduły wdrożony pomyślnie na urządzeniu lub na urządzeniach, które objęte z etapem pytań i odpowiedzi. Następnie przejdź do potoku wydania i zgody w wersji przejść do etapu produkcji, wybierając **PROD** przycisk, a następnie wybierając **Zatwierdź**. 

    ![Oczekuje na zatwierdzenie](./media/how-to-ci-cd/pending-approval.png)

5. Po osoby zatwierdzającej zatwierdzić tę zmianę, można je było wdrożyć do **PROD**.

## <a name="next-steps"></a>Kolejne kroki

* Omówienie wdrożenia usługi IoT Edge w [wdrożeń zrozumieć usługi IoT Edge dla urządzeń z jednej lub w odpowiedniej skali](module-deployment-monitoring.md)
* Przewodnik po krokach do utworzenia, aktualizacji lub usunięcia wdrożenia w [wdrażanie i monitorowanie moduły usługi IoT Edge na dużą skalę](how-to-deploy-monitor.md).

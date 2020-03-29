---
title: Ciągła integracja & ciągłego wdrażania — usługa Azure IoT Edge
description: Konfigurowanie ciągłej integracji i ciągłego wdrażania — usługa Azure IoT Edge z platformą Azure DevOps, potoki platformy Azure
author: shizn
manager: philmea
ms.author: xshi
ms.date: 08/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4b99e83a8e71b13183c76321c7076b85a212f021
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76510978"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>Ciągła integracja i ciągłe wdrażanie usługi Azure IoT Edge

Możesz łatwo przyjąć DevOps za pomocą aplikacji usługi Azure IoT Edge za pomocą wbudowanych zadań usługi Azure IoT Edge w potokach platformy Azure. W tym artykule pokazano, jak można używać funkcji ciągłej integracji i ciągłego wdrażania usługi Azure Pipelines do tworzenia, testowania i wdrażania aplikacji szybko i wydajnie do usługi Azure IoT Edge.

![Diagram - branże ciągłej integracji i ciągłej integracji i cd do rozwoju i produkcji](./media/how-to-ci-cd/cd.png)

W tym artykule dowiesz się, jak użyć wbudowanych zadań usługi Azure IoT Edge dla usługi Azure Pipelines do utworzenia dwóch potoków dla rozwiązania usługi IoT Edge. Istnieją cztery akcje mogą być używane w zadaniach usługi Azure IoT Edge.

* **Usługa Azure IoT Edge — obrazy modułu kompilacji** pobiera kod rozwiązania usługi IoT Edge i tworzy obrazy kontenerów.
* **Usługa Azure IoT Edge — obrazy modułu wypychania** wypycha obrazy modułów do określonego rejestru kontenerów.
* **Usługa Azure IoT Edge — manifest wdrażania generate** przyjmuje plik deployment.template.json i zmienne, a następnie generuje końcowy plik manifestu wdrożenia usługi IoT Edge.
* **Usługa Azure IoT Edge — wdrażanie na urządzeniach usługi IoT Edge** pomaga tworzyć wdrożenia usługi IoT Edge na urządzeniach usługi IoT Edge.Azure IoT Edge — Deploy to IoT Edge devices helps create IoT Edge deployments to single/multiple IoT Edge devices.

## <a name="prerequisites"></a>Wymagania wstępne

* Repozytorium repozytorium usługi Azure. Jeśli go nie masz, możesz [utworzyć nowe repozytorium Git w projekcie.](https://docs.microsoft.com/azure/devops/repos/git/create-new-repo?view=vsts&tabs=new-nav)
* Rozwiązanie IoT Edge zatwierdzone i wypchnięte do repozytorium. Jeśli chcesz utworzyć nowe przykładowe rozwiązanie do testowania tego artykułu, wykonaj kroki opisane w [artykule Opracowywanie i debugowanie modułów w programie Visual Studio Code](how-to-vs-code-develop-module.md) lub Opracowanie i [debugowanie modułów C# w programie Visual Studio.](how-to-visual-studio-develop-csharp-module.md)

   W tym artykule wszystko, czego potrzebujesz, to folder rozwiązania utworzony przez szablony IoT Edge w programie Visual Studio Code lub Visual Studio. Nie trzeba tworzyć, wypychać, wdrażać lub debugować ten kod przed kontynuowaniem. Te procesy zostanie skonfigurowane w usłudze Azure Pipelines.

   Jeśli tworzysz nowe rozwiązanie, najpierw sklonuj repozytorium lokalnie. Następnie podczas tworzenia rozwiązania można utworzyć go bezpośrednio w folderze repozytorium. Możesz łatwo zatwierdzić i wypchnąć nowe pliki stamtąd.

* Rejestr kontenerów, w którym można wypychać obrazy modułów. Można użyć [usługi Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) lub rejestru innej firmy.
* Aktywne [centrum IoT z](../iot-hub/iot-hub-create-through-portal.md) co najmniej urządzeniami usługi IoT Edge do testowania oddzielnych etapów wdrażania testów i produkcji. Możesz śledzić artykuły szybki start, aby utworzyć urządzenie IoT Edge w [systemie Linux](quickstart-linux.md) lub [Windows](quickstart.md)

Aby uzyskać więcej informacji na temat korzystania z aplikacji Repo platformy Azure, zobacz [Udostępnianie kodu w programie Visual Studio i repozytorium platformy Azure](https://docs.microsoft.com/azure/devops/repos/git/share-your-code-in-git-vs?view=vsts)

## <a name="configure-continuous-integration"></a>Konfigurowanie ciągłej integracji

W tej sekcji utworzysz nowy potok kompilacji. Skonfiguruj potok do automatycznego uruchamiania po zaewidencjonowaniu wszelkich zmian w przykładowym rozwiązaniu usługi IoT Edge i publikowania dzienników kompilacji.

>[!NOTE]
>W tym artykule użyto projektanta wizualnego programu Azure DevOps. Przed wykonaniem kroków w tej sekcji, należy wyłączyć funkcję podglądu dla nowego środowiska tworzenia potoku YAML.
>
>1. W usłudze Azure DevOps wybierz ikonę profilu, a następnie wybierz pozycję **Podgląd funkcji**.
>2. Wyłącz **nowe środowisko tworzenia potoku YAML.**
>
>Aby uzyskać więcej informacji, zobacz [Tworzenie potoku kompilacji](https://docs.microsoft.com/azure/devops/pipelines/create-first-pipeline).

1. Zaloguj się do swojej organizacji DevOps platformy Azure (**https:\//dev.azure.com/{twoja organizacja}/**) i otwórz projekt zawierający repozytorium rozwiązań usługi IoT Edge.

   W tym artykule utworzyliśmy repozytorium o nazwie **IoTEdgeRepo**. To repozytorium zawiera **rozwiązanie IoTEdgeSolution,** które zawiera kod modułu o nazwie **filtermodule**.

   ![Otwórz projekt DevOps](./media/how-to-ci-cd/init-project.png)

2. Przejdź do usługi Azure Potoki w projekcie. Otwórz kartę **Kompilacje** i wybierz pozycję **Nowy potok**. Lub, jeśli masz już potoki kompilacji, wybierz przycisk **Nowy.** Następnie wybierz **nowy potok kompilacji**.

    ![Tworzenie nowego potoku kompilacji](./media/how-to-ci-cd/add-new-build.png)

3. Postępuj zgodnie z instrukcjami, aby utworzyć potok.

   1. Podaj informacje źródłowe dla nowego potoku kompilacji. Wybierz **azure repozytorium Git** jako źródło, a następnie wybierz projekt, repozytorium i gałęzi, gdzie znajduje się kod rozwiązania usługi IoT Edge. Następnie wybierz przycisk **Kontynuuj**.

      ![Wybierz źródło potoku](./media/how-to-ci-cd/pipeline-source.png)

   2. Wybierz **pozycję Puste zadanie** zamiast szablonu.

      ![Zacznij od pustego procesu](./media/how-to-ci-cd/start-with-empty.png)

4. Po utworzeniu potoku zostaniesz przesuń do edytora potoku. W opisie potoku wybierz odpowiednią pulę agentów na podstawie platformy docelowej:

   * Jeśli chcesz zbudować swoje moduły w platformie amd64 dla kontenerów Linuksa, wybierz **Hosted Ubuntu 1604**

   * Jeśli chcesz zbudować swoje moduły w platformie amd64 dla kontenerów Windows 1809, musisz [skonfigurować agenta hostowanego samodzielnie w systemie Windows](https://docs.microsoft.com/azure/devops/pipelines/agents/v2-windows?view=vsts).

   * Jeśli chcesz zbudować swoje moduły w platformie arm32v7 lub arm64 dla kontenerów Linuksa, musisz [skonfigurować agenta hostowanego samodzielnie na Linuksie.](https://blogs.msdn.microsoft.com/iotdev/2018/11/13/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent/)

     ![Konfigurowanie puli agentów kompilacji](./media/how-to-ci-cd/configure-env.png)

5. Potok jest wstępnie skonfigurowany z zadaniem o nazwie **Agent zadanie 1**. Wybierz znak plus**+**( ), aby dodać trzy zadania do zadania: **Azure IoT Edge** dwa razy, **Kopiuj pliki** raz i **Publikuj artefakty kompilacji** raz. (Umieść wskaźnik myszy na nazwie każdego zadania, aby wyświetlić przycisk **Dodaj).**

   ![Dodawanie zadania usługi Azure IoT Edge](./media/how-to-ci-cd/add-iot-edge-task.png)

   Po dodaniu wszystkich czterech zadań zadanie agenta wygląda następująco:

   ![Trzy zadania w potoku kompilacji](./media/how-to-ci-cd/add-tasks.png)

6. Wybierz pierwsze zadanie **usługi Azure IoT Edge,** aby go edytować. To zadanie tworzy wszystkie moduły w rozwiązaniu z platformą docelową, którą określisz.

   * **Nazwa wyświetlana:** Zaakceptuj domyślną **usługę Azure IoT Edge — tworzenie obrazów modułów**.
   * **Akcja:** Zaakceptuj domyślne **obrazy modułów kompilacji**.
   * **Plik .template.json**: Wybierz wielokropek (**...**) i przejdź do pliku **deployment.template.json** w repozytorium, które zawiera rozwiązanie usługi IoT Edge.
   * **Domyślna platforma:** Wybierz odpowiednią platformę dla swoich modułów na podstawie docelowego urządzenia IoT Edge.
   * **Zmienne wyjściowe:** Zmienne wyjściowe zawierają nazwę odwołania, której można użyć do skonfigurowania ścieżki pliku, w której zostanie wygenerowany plik deployment.json. Ustaw nazwę odniesienia na coś pamiętnego jak **krawędź**.

7. Wybierz drugie zadanie **usługi Azure IoT Edge,** aby je edytować. To zadanie wypycha wszystkie obrazy modułów do wybranego rejestru kontenerów.

   * **Nazwa wyświetlana**: Nazwa wyświetlana jest automatycznie aktualizowana po zmianie pola akcji.
   * **Akcja**: Użyj listy rozwijanej, aby wybrać **obrazy modułów wypychania**.
   * **Typ rejestru kontenerów:** Wybierz typ rejestru kontenerów używany do przechowywania obrazów modułów. W zależności od wybranego typu rejestru zmienia się formularz. Jeśli wybierzesz **azure container registry**, użyj listy rozwijanej, aby wybrać subskrypcję platformy Azure i nazwę rejestru kontenerów. Jeśli wybierzesz **ogólny rejestr kontenerów,** wybierz pozycję **Nowy,** aby utworzyć połączenie usługi rejestru.
   * **Plik .template.json**: Wybierz wielokropek (**...**) i przejdź do pliku **deployment.template.json** w repozytorium, które zawiera rozwiązanie usługi IoT Edge.
   * **Domyślna platforma:** Wybierz tę samą platformę co wbudowane obrazy modułów.

   Jeśli masz wiele rejestrów kontenerów do hostowania obrazów modułów, musisz powielić to zadanie, wybrać inny rejestr kontenerów i użyć **modułów Bypass** w ustawieniach zaawansowanych, aby pominąć obrazy, które nie są dla tego określonego rejestru.

8. Wybierz zadanie **Kopiuj pliki,** aby je edytować. To zadanie służy do kopiowania plików do katalogu przemieszczania artefaktu.

   * **Nazwa wyświetlana**: Kopiuj pliki do: Folder upuszczania.
   * **Zawartość**: Umieść dwa wiersze w tej sekcji `deployment.template.json` i `**/module.json`. Te dwa typy plików są dane wejściowe do generowania manifestu wdrażania usługi IoT Edge. Należy skopiować do folderu przemieszczania artefaktu i opublikować do potoku wydania.
   * **Folder docelowy:** `$(Build.ArtifactStagingDirectory)`Umieść zmienną . Zobacz [Tworzenie zmiennych,](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables) aby dowiedzieć się więcej o opisie.

9. Wybierz zadanie **Publikuj artefakty kompilacji,** aby je edytować. Podaj ścieżkę katalogu przemieszczania artefaktu do zadania, aby ścieżka mogła zostać opublikowana w celu wydania potoku.

   * **Nazwa wyświetlana**: Publikuj artefakt: upuść.
   * **Ścieżka do opublikowania:** Umieść zmienną `$(Build.ArtifactStagingDirectory)`. Zobacz [Tworzenie zmiennych,](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables) aby dowiedzieć się więcej o opisie.
   * **Nazwa artefaktu**: upuść.
   * **Lokalizacja publikowania artefaktów:** potoki platformy Azure.

10. Otwórz kartę **Wyzwalacze** i zaznacz pole wyboru **Włącz ciągłą integrację**. Upewnij się, że gałąź zawierająca kod jest dołączona.

    ![Włączanie wyzwalacza ciągłej integracji](./media/how-to-ci-cd/configure-trigger.png)

11. Zapisz nowy potok kompilacji za pomocą przycisku **Zapisz.**

Ten potok jest teraz skonfigurowany do automatycznego uruchamiania po wypchnięciu nowego kodu do repozytorium. Ostatnie zadanie, publikowanie artefaktów potoku, wyzwala potok wydania. Przejdź do następnej sekcji, aby utworzyć potok wydania.

## <a name="configure-continuous-deployment"></a>Konfigurowanie ciągłego wdrażania

W tej sekcji utworzysz potok wydania, który jest skonfigurowany do automatycznego uruchamiania, gdy potok kompilacji upuszcza artefakty i wyświetli dzienniki wdrażania w potokach platformy Azure.

Tworzenie nowego potoku i dodawanie nowego etapu

1. Na karcie **Zwalnia** wybierz pozycję **+ Nowy potok**. Lub, jeśli masz już potoki wydania, wybierz przycisk **+ Nowy** i wybierz + Nowy **potok wydania**.  

    ![Dodaj potok wydania](./media/how-to-ci-cd/add-release-pipeline.png)

2. Po wyświetleniu monitu o wybranie szablonu wybierz opcję rozpoczynania od **pustego zadania**.

    ![Zacznij od pustego zadania](./media/how-to-ci-cd/start-with-empty-job.png)

3. Nowy potok wydania jest inicjalizuje się z jednym etapem o nazwie **Etap 1**. Zmień nazwę etapu 1, aby **deweloperować** i traktować go jako środowisko testowe. Zazwyczaj potoki ciągłego wdrażania mają wiele etapów, w tym **dev**, **staging** i **prod**. Możesz utworzyć więcej na podstawie praktyki DevOps. Zamknij okno szczegółów stołu montażowego po jego zmianie.

4. Połącz wydanie z artefaktami kompilacji, które są publikowane przez potok kompilacji. Kliknij pozycję **Dodaj** w obszarze artefakty.

   ![Dodawanie artefaktów](./media/how-to-ci-cd/add-artifacts.png)  

5. W **obszarze Dodawanie strony artefaktu**wybierz typ źródła **Kompilacja**. Następnie wybierz projekt i potok kompilacji, który został utworzony. Następnie wybierz pozycję **Dodaj**.

   ![Dodawanie artefaktu kompilacji](./media/how-to-ci-cd/add-an-artifact.png)

6. Otwórz wyzwalacze artefaktów i wybierz przełącznik, aby włączyć wyzwalacz ciągłego wdrażania. Teraz nowa wersja zostanie utworzona za każdym razem, gdy dostępna jest nowa kompilacja.

   ![Konfigurowanie wyzwalacza ciągłego wdrażania](./media/how-to-ci-cd/add-a-trigger.png)

7. Etap **dewelopera** jest wstępnie skonfigurowany z jednym zadaniem i zerowym zadaniem. Z menu potoku wybierz pozycję **Zadania,** a następnie wybierz etap **dev.**  Wybierz zadanie i liczbę zadań, aby skonfigurować zadania na tym etapie.

    ![Konfigurowanie zadań deweloperskich](./media/how-to-ci-cd/view-stage-tasks.png)

8. Na etapie **dev** powinno być widoczne domyślne **zadanie agenta**. Można skonfigurować szczegóły dotyczące zadania agenta, ale zadanie wdrażania jest niewrażliwe na platformę, dzięki czemu można użyć **hostowanego programu VS2017** lub **hostowanego Ubuntu 1604** w **puli agenta** (lub dowolnego innego agenta zarządzanego przez ciebie).

9. Wybierz znak plus**+**( ), aby dodać dwa zadania. Wyszukaj i dodaj **usługę Azure IoT Edge** dwa razy.

    ![Dodawanie zadań dla deweloperów](./media/how-to-ci-cd/add-task-qa.png)

10. Wybierz pierwsze zadanie **usługi Azure IoT Edge** i skonfiguruj je z następującymi wartościami:

    * **Nazwa wyświetlana**: Nazwa wyświetlana jest automatycznie aktualizowana po zmianie pola akcji.
    * **Akcja:** Użyj listy rozwijanej, aby wybrać pozycję **Generuj manifest wdrożenia**. Zmiana wartości akcji aktualizuje również nazwę wyświetlaną zadania, aby dopasować.
    * **.template.json plik**: `$(System.DefaultWorkingDirectory)/Drop/drop/deployment.template.json`Umieść ścieżkę . Ścieżka jest publikowana z potoku kompilacji.
    * **Domyślna platforma:** Wybierz tę samą wartość podczas tworzenia obrazów modułów.
    * **Ścieżka wyjściowa:** `$(System.DefaultWorkingDirectory)/Drop/drop/configs/deployment.json`Umieść ścieżkę . Ta ścieżka jest ostatecznym plikiem manifestu wdrożenia usługi IoT Edge.

    Te konfiguracje pomagają zastąpić adresy `deployment.template.json` URL obrazów modułu w pliku. **Manifest wdrożeniowy Generate** pomaga również zastąpić zmienne `deployment.template.json` dokładną wartością zdefiniowaną w pliku. W programie VS/VS Code określasz rzeczywistą `.env` wartość w pliku. W usłudze Azure Pipelines można ustawić wartość na karcie Zmienne potoku wydania.

    * **ACR_ADDRESS:** Twój adres rejestru kontenerów platformy Azure.
    * **ACR_PASSWORD:** Hasło rejestru kontenerów platformy Azure.
    * **ACR_USER:** Nazwa użytkownika rejestru kontenerów platformy Azure.

    Jeśli masz inne zmienne w projekcie, można określić nazwę i wartość na tej karcie. **Manifest wdrożeniowy generowania** może `${VARIABLE}` rozpoznać tylko zmienne są `*.template.json` w smaku, upewnij się, że używasz tego w plikach.

    ![Konfigurowanie zmiennych dla potoku wydania](./media/how-to-ci-cd/configure-variables.png)

11. Wybierz drugie zadanie **usługi Azure IoT Edge** i skonfiguruj je z następującymi wartościami:

    * **Nazwa wyświetlana**: Nazwa wyświetlana jest automatycznie aktualizowana po zmianie pola akcji.
    * **Akcja:** Użyj listy rozwijanej, aby wybrać **pozycję Wdrażanie na urządzeniach IoT Edge**. Zmiana wartości akcji aktualizuje również nazwę wyświetlaną zadania, aby dopasować.
    * **Subskrypcja platformy Azure:** wybierz subskrypcję zawierającą Centrum IoT.
    * **Nazwa centrum IoT:** wybierz centrum IoT hub.
    * **Wybierz jedno/wiele urządzeń:** wybierz, czy potok wersji ma być wdrażany na jednym urządzeniu, czy na wielu urządzeniach.
      * W przypadku wdrażania na jednym urządzeniu wprowadź identyfikator **urządzenia IoT Edge**.
      * Jeśli wdrażasz na wielu urządzeniach, określ **warunek docelowy**urządzenia . Warunek docelowy jest filtrem, aby dopasować zestaw urządzeń usługi IoT Edge w Centrum IoT Hub. Jeśli chcesz użyć tagów urządzeń jako warunku, musisz zaktualizować odpowiednie urządzenia Tagi za pomocą bliźniaczej reprezentacji urządzenia usługi IoT Hub. Zaktualizuj priorytet **wdrożenia usługi IoT Edge** i priorytet wdrożenia usługi **IoT Edge** w ustawieniach zaawansowanych. Aby uzyskać więcej informacji na temat tworzenia wdrożenia dla wielu urządzeń, zobacz [Opis automatycznych wdrożeń usługi IoT Edge](module-deployment-monitoring.md).
    * Rozwiń węzeł Ustawienia zaawansowane, wybierz pozycję **Identyfikator wdrożenia usługi IoT Edge**, umieść zmienną `$(System.TeamProject)-$(Release.EnvironmentName)`. Spowoduje to mapenie nazwy projektu i wersji z identyfikatorem wdrożenia usługi IoT Edge.

12. Wybierz **pozycję Zapisz,** aby zapisać zmiany w nowym potoku wydania. Wróć do widoku potoku, wybierając **pipeline** z menu.

## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>Weryfikowanie ciągłej integracji/dysku CD usługi IoT Edge za pomocą potoków kompilacji i zwalniania

Aby wyzwolić zadanie kompilacji, można wypchnąć zatwierdzenie do repozytorium kodu źródłowego lub ręcznie je wyzwolić. W tej sekcji ręcznie wyzwolić potok ciągłej integracji/ciągłego wdrażania, aby przetestować, czy działa. Następnie sprawdź, czy wdrożenie zakończy się pomyślnie.

1. Przejdź do potoku kompilacji, który został utworzony na początku tego artykułu.

2. Zadanie kompilacji można wyzwolić w potoku kompilacji, wybierając przycisk **Kolejka,** tak jak na poniższym zrzucie ekranu.

    ![Ręczny wyzwalacz](./media/how-to-ci-cd/manual-trigger.png)

3. Wybierz zadanie kompilacji, aby obserwować jego postęp. Jeśli potok kompilacji zostanie ukończony pomyślnie, wyzwala zwolnienie do etapu **dev.**

    ![Tworzenie dzienników](./media/how-to-ci-cd/build-logs.png)

4. Pomyślne wydanie **dewelopera** tworzy wdrożenie usługi IoT Edge do urządzeń usługi IoT Edge.

    ![Zwolnij do dewelopera](./media/how-to-ci-cd/pending-approval.png)

5. Kliknij etap **dev,** aby wyświetlić dzienniki wersji.

    ![Dzienniki wydań](./media/how-to-ci-cd/release-logs.png)

## <a name="next-steps"></a>Następne kroki

* Przykład najlepszych rozwiązań dotyczących programu IoT Edge DevOps w [programie Azure DevOps Project for IoT Edge](how-to-devops-project.md)
* Opis wdrożenia usługi IoT Edge w [ujmujących wdrożenia usługi IoT Edge dla pojedynczych urządzeń lub na dużą skalę](module-deployment-monitoring.md)
* Przejdź przez kroki, aby utworzyć, zaktualizować lub usunąć wdrożenie w [deploy i monitorować moduły usługi IoT Edge na dużą skalę](how-to-deploy-monitor.md).

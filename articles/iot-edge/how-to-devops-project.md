---
title: Potok ciągłej integracji/ciągłego wdrażania z projektami devops platformy Azure — usługa Azure IoT Edge | Dokumenty firmy Microsoft
description: Usługa Azure DevOps Projects ułatwia rozpoczęcie pracy na platformie Azure. Pomaga uruchomić aplikację Usługi Azure IoT Edge do wyboru w kilku szybkich krokach.
author: shizn
ms.author: xshi
ms.date: 10/09/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 66401678f03ee0043345208eb32560f589829226
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76510315"
---
# <a name="create-a-cicd-pipeline-for-iot-edge-with-azure-devops-projects"></a>Tworzenie potoku ciągłej integracji/ciągłego wdrażania dla usługi IoT Edge za pomocą projektów devops platformy Azure

Skonfiguruj ciągłą integrację (CI) i ciągłe dostarczanie (CD) dla aplikacji Usługi IoT Edge za pomocą projektów DevOps. Usługa DevOps Projects ułatwia początkową konfigurację potoku kompilacji i wydania w usłudze Azure Pipelines.

Jeśli nie masz aktywnej subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Usługa DevOps Projects tworzy potok ciągłej integracji/ciągłego wdrażania w usłudze Azure DevOps. Możesz utworzyć nową organizację usługi Azure DevOps lub użyć istniejącej organizacji. Usługa DevOps Projects tworzy również zasoby platformy Azure w wybranej subskrypcji platformy Azure.

1. Zaloguj się do [Portalu Microsoft Azure](https://portal.azure.com).

1. W okienku po lewej stronie wybierz pozycję **Utwórz zasób**, a następnie wyszukaj pozycję **DevOps Projects**.  

1. Wybierz **pozycję Utwórz**.

## <a name="create-a-new-application-pipeline"></a>Tworzenie nowego potoku aplikacji

1. Moduły usługi Azure IoT Edge można zapisać w językach [C#](tutorial-csharp-module.md), [Node.js,](tutorial-node-module.md) [Python](tutorial-python-module.md), [C](tutorial-c-module.md) i [Java.](tutorial-java-module.md) Wybierz preferowany język, aby uruchomić nową aplikację: **.NET**, **Node.js**, **Python**, **C**lub **Java**. Kliknij przycisk **Dalej**, aby kontynuować.

   ![Wybierz język, aby utworzyć nową aplikację](./media/how-to-devops-project/select-language.png)

2. Wybierz **prosty IoT** jako strukturę aplikacji, a następnie wybierz przycisk **Dalej**.

   ![Wybierz prostą strukturę IoT](media/how-to-devops-project/select-iot.png)

3. Wybierz opcję **IoT Edge** jako usługę platformy Azure, która wdraża aplikację, a następnie wybierz pozycję **Dalej**.

   ![Wybierz usługę IoT Edge](media/how-to-devops-project/select-iot-edge.png)

4. Utwórz bezpłatnie nową organizację usługi Azure DevOps lub wybierz istniejącą organizację.

   1. Podaj nazwę projektu.

   2. Wybierz swoją organizację DevOps platformy Azure. Jeśli nie masz istniejącej organizacji, wybierz **opcję Dodatkowe ustawienia,** aby utworzyć nową.

   3. Wybierz swoją subskrypcję platformy Azure.

   4. Użyj nazwy Centrum IoT wygenerowanej przez nazwę projektu lub podaj własną.

   5. Zaakceptuj lokalizację domyślną lub wybierz jedną bliską.

   6. Wybierz **dodatkowe ustawienia,** aby skonfigurować zasoby platformy Azure tworzone przez devops projects w Twoim imieniu.

   7. Wybierz **pozycję Gotowe,** aby zakończyć tworzenie projektu.

   ![Nazwa i tworzenie aplikacji](media/how-to-devops-project/select-devops.png)

Po kilku minutach w witrynie Azure Portal zostanie wyświetlony pulpit nawigacyjny usługi DevOps Projects. Wybierz nazwę projektu, aby zobaczyć postęp. Może być konieczne odświeżenie strony. Przykładowa aplikacja usługi IoT Edge jest skonfigurowana w repozytorium w organizacji Azure DevOps, wykonywana jest kompilacja, a aplikacja jest wdrażana na urządzeniu usługi IoT Edge. Ten pulpit nawigacyjny zapewnia wgląd w repozytorium kodu, potok ciągłej integracji/ciągłego wdrażania i aplikację na platformie Azure.

   ![Wyświetlanie aplikacji w witrynie Azure portal](./media/how-to-devops-project/devops-portal.png)

## <a name="commit-code-changes-and-execute-cicd"></a>Zatwierdzanie zmian kodu i wykonywanie ciągłej integracji/ciągłego wdrażania

DevOps Projects utworzył repozytorium Git dla twojego projektu w usłudze Azure Repos. W tej sekcji można wyświetlić repozytorium i wprowadzić zmiany kodu do aplikacji.

1. Aby przejść do repozytorium utworzonego dla projektu, wybierz **repozytoria** w menu pulpitu nawigacyjnego projektu.  

   ![Wyświetl repozytorium wygenerowane w repozytorium platformy Azure](./media/how-to-devops-project/view-repositories.png)

2. Poniższe kroki można przejść za pomocą przeglądarki sieci Web, aby wprowadzić zmiany w kodzie. Jeśli chcesz sklonować repozytorium lokalnie, wybierz **opcję Klonuj** w prawym górnym rogu okna. Użyj podanego adresu URL, aby sklonować repozytorium Git w programie Visual Studio Code lub preferowanym narzędziu programistycznym.

3. Repozytorium zawiera już kod modułu o nazwie **FilterModule** na podstawie języka aplikacji wybranego w procesie tworzenia. Otwórz plik **modules/FilterModule/module.json.**

   ![Otwórz plik module.json w usłudze Repo platformy Azure](./media/how-to-devops-project/open-module-json.png)

4. Należy zauważyć, że ten plik używa [zmiennych kompilacji azure devops](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=vsts#build-variables) w parametrze **wersji.** Ta konfiguracja gwarantuje, że nowa wersja modułu zostanie utworzona za każdym razem, gdy zostanie przebiegna nowa kompilacja.

## <a name="examine-the-cicd-pipeline"></a>Badanie potoku ciągłej integracji/ciągłego wdrażania

W poprzednich sekcjach projekty programu Azure DevOps automatycznie skonfigurowano pełny potok ciągłej integracji/ciągłego wdrażania dla aplikacji usługi IoT Edge. Teraz eksploruj i dostosuj potok w razie potrzeby. Skorzystaj z poniższych kroków, aby zapoznać się z potokami kompilacji i wydania usługi Azure DevOps.

1. Aby wyświetlić potoki kompilacji w projekcie DevOps, wybierz **pozycję Buduj potoki** w menu pulpitu nawigacyjnego projektu. Ten link otwiera kartę przeglądarki i potok kompilacji usługi Azure DevOps dla nowego projektu.

   ![Wyświetlanie potoków kompilacji w potokach platformy Azure](./media/how-to-devops-project/view-build-pipelines.png)

2. Wybierz pozycję **Edit** (Edytuj).

    ![Edytuj potok kompilacji](media/how-to-devops-project/click-edit-button.png)

3. W panelu, który zostanie otwarty, można sprawdzić zadania, które występują podczas wykonywania potoku kompilacji. Potok kompilacji wykonuje różne zadania, takie jak pobieranie źródeł z repozytorium Git, tworzenie obrazów modułów usługi IoT Edge, wypychanie modułów usługi IoT Edge do rejestru kontenerów i publikowanie danych wyjściowych używanych do wdrożeń. Aby dowiedzieć się więcej o zadaniach usługi Azure IoT Edge w usłudze Azure DevOps, zobacz [Konfigurowanie potoków platformy Azure w celu ciągłej integracji.](how-to-ci-cd.md#configure-continuous-integration)

4. Wybierz **Pipeline** nagłówka w górnej części potoku kompilacji, aby otworzyć szczegóły potoku. Zmień nazwę potoku kompilacji na bardziej opisową.

   ![Edytowanie szczegółów potoku](./media/how-to-devops-project/edit-build-pipeline.png)

5. Wybierz **pozycję Zapisz & kolejki,** a następnie wybierz pozycję **Zapisz**.

6. Wybierz **wyzwalacze** z menu potoku kompilacji. Usługa DevOps Projects automatycznie utworzyła wyzwalacz ciągłej integracji — każde zatwierdzenie w repozytorium uruchamia nową kompilację.  Opcjonalnie możesz zdecydować się dołączyć gałęzie do procesu ciągłej integracji lub je wykluczyć.

7. Wybierz pozycję **Przechowywanie**. W zależności od scenariusza możesz określić zasady przechowywania lub usuwania pewnej liczby kompilacji.

8. Wybierz **pozycję Historia**. Panel historii zawiera ścieżkę inspekcji ostatnich zmian w kompilacji. Usługa Azure Pipelines śledzi wszelkie zmiany wprowadzone do potoku kompilacji i pozwala na porównanie wersji.

9. Po zakończeniu eksplorowania potoku kompilacji, przejdź do odpowiedniego potoku wydania. Wybierz **pozycję Wydania w** obszarze **Potoki**, a następnie wybierz pozycję **Edytuj,** aby wyświetlić szczegóły potoku.

    ![Wyświetl potok wydania](media/how-to-devops-project/release-pipeline.png)

10. W obszarze **Artefakty** wybierz polecenie **Porzuć**. Źródło, które obserwuje ten artefakt jest dane wyjściowe potoku kompilacji, które zostały zbadane w poprzednich krokach.

11. Obok ikony **Upuszczanie** wybierz **wyzwalacz ciągłego wdrażania,** który wygląda jak błyskawica. Ten potok wydania włączył wyzwalacz, który uruchamia wdrożenie za każdym razem, gdy dostępny jest nowy artefakt kompilacji. Opcjonalnie możesz wyłączyć wyzwalacz. Wtedy wdrożenia będą wymagać ręcznego wykonania.  

12. W menu potoku wydania wybierz pozycję **Zadania,** a następnie wybierz etap **dewelopera** z listy rozwijanej. DevOps Projects utworzył etap wydania dla Ciebie, który tworzy centrum IoT hub, tworzy urządzenie usługi IoT Edge w tym centrum, wdraża przykładowy moduł z potoku kompilacji i aplikuje maszynę wirtualną do uruchomienia jako urządzenie usługi IoT Edge. Aby dowiedzieć się więcej o zadaniach usługi Azure IoT Edge na dysku CD, zobacz [Konfigurowanie potoków platformy Azure do ciągłego wdrażania.](how-to-ci-cd.md#configure-continuous-deployment)

    ![Wyświetlanie zadań ciągłego wdrażania](media/how-to-devops-project/dev-release.png)

13. Po prawej stronie wybierz pozycję **Wyświetl wydania**. Ten widok przedstawia historię wersji.

14. Wybierz nazwę wydania, aby wyświetlić więcej informacji na jej temat.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy usługa Azure App Service i inne utworzone powiązane zasoby nie będą już potrzebne, możesz je usunąć. Użyj funkcji **Usuń** na pulpicie nawigacyjnym usługi DevOps Projects.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o zadaniach dotyczących usługi Azure IoT Edge w usłudze Azure DevOps w [ciągłej integracji i ciągłego wdrażania w usłudze Azure IoT Edge](how-to-ci-cd.md)
* Opis wdrożenia usługi IoT Edge w [ujmujących wdrożenia usługi IoT Edge dla pojedynczych urządzeń lub na dużą skalę](module-deployment-monitoring.md)
* Przejdź przez kroki, aby utworzyć, zaktualizować lub usunąć wdrożenie w [deploy i monitorować moduły usługi IoT Edge na dużą skalę](how-to-deploy-monitor.md).

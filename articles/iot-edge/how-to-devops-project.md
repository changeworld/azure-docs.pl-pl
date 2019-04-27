---
title: Potok ciągłej integracji/ciągłego wdrażania przy użyciu usługi Azure DevOps Projects — usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Usługa Azure DevOps Projects ułatwia rozpoczęcie pracy na platformie Azure. Ułatwia on uruchamiać aplikację usługi Azure IoT Edge wybranym w kilku prostych krokach.
author: shizn
manager: ''
ms.author: xshi
ms.date: 01/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 02977032c5975de4098600ddbebccfcbb9b0fafd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60595499"
---
# <a name="create-a-cicd-pipeline-for-iot-edge-with-azure-devops-projects-preview"></a>Tworzenie potoku CI/CD dla usługi IoT Edge przy użyciu usługi Azure DevOps Projects (wersja zapoznawcza)

Skonfiguruj ciągłą integrację (CI) i ciągłe dostarczanie (CD) aplikacji usługi IoT Edge przy użyciu DevOps Projects. Usługa DevOps Projects ułatwia początkową konfigurację potoku kompilacji i wydania w usłudze Azure Pipelines.

Jeśli nie masz aktywnej subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Usługa DevOps Projects tworzy potok ciągłej integracji/ciągłego wdrażania w usłudze Azure DevOps. Możesz utworzyć nową organizację usługi Azure DevOps lub użyć istniejącej organizacji. Usługa DevOps Projects tworzy również zasoby platformy Azure w wybranej subskrypcji platformy Azure.

1. Zaloguj się do [Portalu Microsoft Azure](https://portal.azure.com).

1. W okienku po lewej stronie wybierz pozycję **Utwórz zasób**, a następnie wyszukaj pozycję **DevOps Projects**.  

1.  Wybierz pozycję **Utwórz**.

## <a name="create-a-new-application-pipeline"></a>Utwórz nowy potok aplikacji 

1. Moduły usługi Azure IoT Edge mogą być napisane w [ C# ](tutorial-csharp-module.md), [Node.js](tutorial-node-module.md), [Python](tutorial-python-module.md), [C](tutorial-c-module.md) i [Java](tutorial-java-module.md). Ustaw swój preferowany język, aby rozpocząć nową aplikację: **.NET**, **Node.js**, **Python**, **C**, lub **językaJava**. Kliknij przycisk **Dalej**, aby kontynuować.

   ![Wybierz język, aby utworzyć nową aplikację](./media/how-to-devops-project/select-language.png)

2. Wybierz **proste IoT (wersja zapoznawcza)** jako platforma aplikacji, a następnie wybierz **dalej**.

   ![Wybierz strukturę proste IoT](media/how-to-devops-project/select-iot.png)

3. Wybierz **usługi IoT Edge** jako usługa platformy Azure, która wdraża aplikację, a następnie wybierz **dalej**.

   ![Wybierz usługę IoT Edge](media/how-to-devops-project/select-iot-edge.png)

4. Utwórz bezpłatnie nową organizację usługi Azure DevOps lub wybierz istniejącą organizację.

   1. Podaj nazwę dla projektu. 

   2. Wybierz organizację DevOps platformy Azure. Jeśli nie masz istniejącej organizacji, wybierz opcję **dodatkowe ustawienia** Aby utworzyć nową. 

   3. Wybierz swoją subskrypcję platformy Azure.

   4. Użyj nazwy Centrum IoT Hub, generowane przez nazwę projektu, lub Podaj własny.

   5. Wybierz **dodatkowe ustawienia** do konfigurowania zasobów platformy Azure, które projekty DevOps tworzy w Twoim imieniu.

   6. Wybierz **gotowe** można ukończyć tworzenia projektu. 

   ![Nazwa i tworzenie aplikacji](media/how-to-devops-project/select-devops.png)

Po kilku minutach w witrynie Azure Portal zostanie wyświetlony pulpit nawigacyjny usługi DevOps Projects. Wybierz nazwę projektu, aby wyświetlić postęp. Może być konieczne odświeżenie strony. Przykładowej aplikacji usługi IoT Edge jest skonfigurowana w repozytorium w Twojej organizacji DevOps platformy Azure, kompilacja jest wykonywana, a aplikacja jest wdrażana na urządzeniu usługi IoT Edge. Ten pulpit nawigacyjny zapewnia wgląd w repozytorium kodu, potok ciągłej integracji/ciągłego wdrażania i aplikację na platformie Azure.

   ![Wyświetl aplikację w portalu metodyki DevOps](./media/how-to-devops-project/devops-portal.png)


## <a name="commit-code-changes-and-execute-cicd"></a>Zatwierdzanie zmian kodu i wykonywanie ciągłej integracji/ciągłego wdrażania

Usługa DevOps Projects utworzyć repozytorium Git dla projektu w repozytoriach usługi Azure. W tej sekcji możesz wyświetlić repozytorium i wprowadzać zmiany kodu w aplikacji.

1. Aby przejść do repozytorium dla projektu, wybierz **repozytoriów** w menu pulpitu nawigacyjnego projektu.  

   ![Widok repozytorium generowane w repozytoriach platformy Azure](./media/how-to-devops-project/view-repositories.png)

2. W poniższych krokach objaśniono za pomocą przeglądarki sieci web, aby wprowadzić zmiany kodu. Zamiast tego sklonować repozytorium lokalnie, należy zaznaczyć **klonowania** od góry prawej krawędzi okna. Użyj podanego adresu URL, aby sklonować repozytorium Git w programie Visual Studio Code lub narzędzi do tworzenia. 

3. Repozytorium zawiera już kodu dla modułu o nazwie **SampleModule** opierając się na wybranej w ramach procesu tworzenia aplikacji. Otwórz **modules/SampleModule/module.json** pliku.

   ![Module.json Otwórz plik w repozytoriach usługi Azure](./media/how-to-devops-project/open-module-json.png)

4. Wybierz **Edytuj**, a następnie wprowadzić zmiany do `"version"` w obszarze `"tag"`. Na przykład, aby zaktualizować `"version": "${BUILD_BUILDID}"` używać [DevOps platformy Azure, tworzyć zmienne](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=vsts#build-variables) jako część tag obrazu modułu usługi Azure IoT Edge.

   ![Edytuj wersję, aby zaakceptować zmienne kompilacji](media/how-to-devops-project/update-module-json.png)

5. Wybierz pozycję **Zatwierdź**, a następnie zapisz zmiany.

6. W przeglądarce wróć do pulpitu nawigacyjnego projekty DevOps w witrynie Azure portal. W tym momencie powinna być widoczna trwająca kompilacja. Wprowadzone zmiany są automatycznie kompilowane i wdrażane za pośrednictwem potoku ciągłej integracji/ciągłego wdrażania.

    ![Wyświetl stan w toku](media/how-to-devops-project/ci-cd-in-progress.png)

## <a name="examine-the-cicd-pipeline"></a>Badanie potoku ciągłej integracji/ciągłego wdrażania

W poprzednich sekcjach usługi Azure DevOps Projects automatycznie konfigurowane pełny potok ciągłej integracji/ciągłego Dostarczania dla aplikacji usługi IoT Edge. Następnie przetestowano tego potoku kompilacji przez wprowadzanie zmian w jeden z plików. Teraz eksplorować i dostosowywać potok zgodnie z potrzebami. Wykonaj poniższe kroki, aby zapoznać się z potokami kompilacji i wydania usługi Azure DevOps.

1. Zaznacz, aby wyświetlić potoki kompilacji w projekt DevOps **tworzenie potoków** w menu pulpitu nawigacyjnego projektu. Ten link otwiera kartę przeglądarki i potok kompilacji usługi Azure DevOps dla nowego projektu.

   ![Wyświetl tworzenie potoków w potokach platformy Azure](./media/how-to-devops-project/view-build-pipelines.png)

2. Wybierz pozycję **Edit** (Edytuj).

    ![Edytuj potoku kompilacji](media/how-to-devops-project/click-edit-button.png)

3. W wyświetlonym panelu można sprawdzić zadania, które występują, gdy uruchomień potoków kompilacji. Potok kompilacji wykonuje różne zadania, takie jak pobieranie źródła z repozytorium Git, tworzenia obrazów modułu usługi IoT Edge, wypychanie moduły usługi IoT Edge do rejestru kontenerów i publikowanie danych wyjściowych, które są używane w przypadku wdrożeń. Aby dowiedzieć się więcej na temat zadań usługi Azure IoT Edge w DevOps platformy Azure, zobacz [konfigurowania Azure potoków ciągłej integracji](how-to-ci-cd.md#configure-continuous-integration).

4. Wybierz **potoku** nagłówka w górnej części procesu kompilacji, można otworzyć szczegóły potoku. Zmień nazwę potoku kompilacji na bardziej opisową.

   ![Edytuj szczegóły potoku](./media/how-to-devops-project/edit-build-pipeline.png)

5. Wybierz **Zapisz k & olejką**, a następnie wybierz pozycję **Zapisz**.

6. Wybierz z menu dla potoku kompilacji **wyzwalaczy** w menu. Usługa DevOps Projects automatycznie utworzyła wyzwalacz ciągłej integracji — każde zatwierdzenie w repozytorium uruchamia nową kompilację.  Opcjonalnie możesz zdecydować się dołączyć gałęzie do procesu ciągłej integracji lub je wykluczyć.

7. Wybierz pozycję **Przechowywanie**. W zależności od scenariusza możesz określić zasady przechowywania lub usuwania pewnej liczby kompilacji.

8. Wybierz **historii**. Na panelu Historia zawiera dziennik inspekcji ostatnich zmian do kompilacji. Usługa Azure Pipelines śledzi wszelkie zmiany wprowadzone do potoku kompilacji i pozwala na porównanie wersji.

9. Po zakończeniu eksplorowania potoku kompilacji, przejdź do odpowiedniej potoku tworzenia wersji. Wybierz **wersji** w obszarze **potoki**, a następnie wybierz **Edytuj** Aby wyświetlić szczegóły potoku.

    ![Wyświetl potoku tworzenia wersji](media/how-to-devops-project/release-pipeline.png)

10. W obszarze **Artefakty** wybierz polecenie **Porzuć**. Źródło, które obserwuje ten artefakt znajduje się dane wyjściowe potoku kompilacji, które badania w poprzednich krokach. 

11. Obok pozycji **porzucić** ikony, wybierz opcję **wyzwalacz ciągłego wdrażania** wygląda błyskawicy. Ten potok wersji włączył wyzwalacza, który uruchamia wdrożenie, za każdym razem, gdy jest dostępny nowy artefakt kompilacji. Opcjonalnie możesz wyłączyć wyzwalacz. Wtedy wdrożenia będą wymagać ręcznego wykonania.  

12. Wybierz z menu dla potok wydania **zadania** wybierz **dev** etap z listy rozwijanej. Usługa DevOps Projects tworzone etapu wydania tworzy Centrum IoT, utworzenie urządzenia usługi IoT Edge w tym Centrum, wdraża moduł próbki z potoku kompilacji, która aprowizuje maszyny wirtualnej, aby był uruchamiany jako urządzenia usługi IoT Edge. Aby dowiedzieć się więcej na temat zadań usługi Azure IoT Edge na dysk CD, zobacz [konfigurowania Azure potoków ciągłego wdrażania](how-to-ci-cd.md#configure-continuous-deployment).

    ![Wyświetl zadania ciągłego wdrażania](media/how-to-devops-project/dev-release.png)

13. Po prawej stronie wybierz pozycję **Wyświetl wydania**. Ten widok przedstawia historię wersji.

14. Wybierz nazwę wersji, aby wyświetlić więcej informacji na jego temat.


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy usługa Azure App Service i inne utworzone powiązane zasoby nie będą już potrzebne, możesz je usunąć. Użyj funkcji **Usuń** na pulpicie nawigacyjnym usługi DevOps Projects.

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o zadaniach usługi Azure IoT Edge na DevOps platformy Azure w [ciągłą integrację i ciągłe wdrażanie w usłudze Azure IoT Edge](how-to-ci-cd.md)
* Omówienie wdrożenia usługi IoT Edge w [wdrożeń zrozumieć usługi IoT Edge dla urządzeń z jednej lub w odpowiedniej skali](module-deployment-monitoring.md)
* Przewodnik po krokach do utworzenia, aktualizacji lub usunięcia wdrożenia w [wdrażanie i monitorowanie moduły usługi IoT Edge na dużą skalę](how-to-deploy-monitor.md).

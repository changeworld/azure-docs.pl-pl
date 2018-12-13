---
title: Potok ciągłej integracji/ciągłego wdrażania przy użyciu usługi Azure DevOps Projects — usługi Azure IoT Edge | Dokumentacja firmy Microsoft
description: Usługa Azure DevOps Projects ułatwia rozpoczęcie pracy na platformie Azure. Ułatwia on uruchamiać aplikację usługi Azure IoT Edge wybranym w kilku prostych krokach.
author: shizn
manager: ''
ms.author: xshi
ms.date: 12/04/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: ebb7e515f9d9205f364d50b3d686c68a2988f86a
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2018
ms.locfileid: "53074218"
---
# <a name="create-a-cicd-pipeline-for-iot-edge-with-azure-devops-projects-preview"></a>Tworzenie potoku CI/CD dla usługi IoT Edge przy użyciu usługi Azure DevOps Projects (wersja zapoznawcza)

Skonfiguruj ciągłą integrację (CI) i ciągłe dostarczanie (CD) aplikacji usługi IoT Edge przy użyciu DevOps Projects. Usługa DevOps Projects ułatwia początkową konfigurację potoku kompilacji i wydania w usłudze Azure Pipelines.

Jeśli nie masz aktywnej subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Usługa DevOps Projects tworzy potok ciągłej integracji/ciągłego wdrażania w usłudze Azure DevOps. Możesz utworzyć nową organizację usługi Azure DevOps lub użyć istniejącej organizacji. Usługa DevOps Projects tworzy również zasoby platformy Azure w wybranej subskrypcji platformy Azure.

1. Zaloguj się do [Portalu Microsoft Azure](https://portal.azure.com).

1. W lewym okienku wybierz ikonę **Utwórz zasób** na pasku nawigacyjnym po lewej stronie, a następnie wyszukaj hasło **DevOps Projects**.  

1.  Wybierz pozycję **Utwórz**.

## <a name="select-a-sample-application-and-azure-service"></a>Wybieranie przykładowej aplikacji i usługi platformy Azure

1. Moduły usługi Azure IoT Edge mogą być napisane w [ C# ](tutorial-csharp-module.md), [Node.js](tutorial-node-module.md), [Python](tutorial-python-module.md), [C](tutorial-c-module.md) i [Java](tutorial-java-module.md). Ustaw swój preferowany język, aby rozpocząć nową aplikację. Odpowiednio, możesz wybrać **.NET**, **Node.js**, **Python**, **C**, lub **Java**, a następnie kliknij przycisk **Dalej**.

    ![Wybierz język, aby utworzyć nową aplikację](./media/how-to-devops-project/select-language.png)

2. Wybierz **proste IoT (wersja zapoznawcza)**, a następnie kliknij przycisk **dalej**.

    ![Wybierz strukturę proste IoT](media/how-to-devops-project/select-iot.png)

3. Wybierz **usługi IoT Edge**, a następnie kliknij przycisk **dalej**.

    ![Wybierz usługę IoT Edge](media/how-to-devops-project/select-iot-edge.png)

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurowanie usługi Azure DevOps i subskrypcji platformy Azure

1. Utwórz bezpłatnie nową organizację usługi Azure DevOps lub wybierz istniejącą organizację.

    a. Wybierz nazwę projektu. 

    b. Wybierz lokalizację i subskrypcję platformy Azure, wybierz nazwę aplikacji, a następnie wybierz przycisk **Gotowe**.  

    ![Nazwa i tworzenie aplikacji](media/how-to-devops-project/select-devops.png)

1. Po kilku minutach w witrynie Azure Portal zostanie wyświetlony pulpit nawigacyjny usługi DevOps Projects. Przykładowej aplikacji usługi IoT Edge jest skonfigurowana w repozytorium w Twojej organizacji DevOps platformy Azure, kompilacja jest wykonywana, a aplikacja jest wdrażana na urządzeniu usługi IoT Edge. Ten pulpit nawigacyjny zapewnia wgląd w repozytorium kodu, potok ciągłej integracji/ciągłego wdrażania i aplikację na platformie Azure.

    ![Wyświetl aplikację w portalu metodyki DevOps](./media/how-to-devops-project/devops-portal.png)


## <a name="commit-code-changes-and-execute-cicd"></a>Zatwierdzanie zmian kodu i wykonywanie ciągłej integracji/ciągłego wdrażania

Usługa DevOps Projects utworzyła repozytorium Git w usłudze Azure Repos lub GitHub. Aby wyświetlić repozytorium i wprowadź zmiany w kodzie aplikacji, wykonaj następujące czynności:

1. Z lewej strony pulpitu nawigacyjnego usługi DevOps Projects wybierz link dla gałęzi **master**.  
Ten link otwiera widok nowo utworzonego repozytorium Git.

1. Aby wyświetlić adres URL klonowania repozytorium, wybierz pozycję **Klonuj** w prawym górnym rogu przeglądarki. Można sklonować repozytorium Git w programie VS Code lub innych preferowanych narzędzi. W następnych kilku krokach wprowadzić przy użyciu przeglądarki sieci web i zatwierdzania kodu zmienia się bezpośrednio do głównej gałęzi.

    ![Klonowanie repozytorium git](media/how-to-devops-project/clone.png)

1. Po lewej stronie przeglądarkę, przejdź do **modules/FilterModule/module.json** pliku.

1. Wybierz **Edytuj**, a następnie wprowadzić zmiany do `"version"` w obszarze `"tag"`. Na przykład, aby zaktualizować `"version": "${BUILD_BUILDID}"` używać [DevOps platformy Azure, tworzyć zmienne](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=vsts#build-variables) jako część tag obrazu modułu usługi Azure IoT Edge.

    ![Edytuj wersję, aby zaakceptować zmienne kompilacji](media/how-to-devops-project/update-module-json.png)

1. Wybierz pozycję **Zatwierdź**, a następnie zapisz zmiany.

1. W przeglądarce przejdź do pulpitu nawigacyjnego projektu usługi Azure DevOps.  W tym momencie powinna być widoczna trwająca kompilacja. Wprowadzone zmiany są automatycznie kompilowane i wdrażane za pośrednictwem potoku ciągłej integracji/ciągłego wdrażania.

    ![Wyświetl stan w toku](media/how-to-devops-project/ci-cd-in-progress.png)

## <a name="examine-the-cicd-pipeline"></a>Badanie potoku ciągłej integracji/ciągłego wdrażania

W poprzednim kroku usługi Azure DevOps Projects automatycznie konfigurowane pełny potok ciągłej integracji/ciągłego Dostarczania dla aplikacji usługi IoT Edge. Możesz przeglądać i dostosowywać potok według potrzeb. Wykonaj poniższe kroki, aby zapoznać się z potokami kompilacji i wydania usługi Azure DevOps.

1. U góry pulpitu nawigacyjnego usługi DevOps Projects wybierz pozycję **Potoki kompilacji**.  
Ten link otwiera kartę przeglądarki i potok kompilacji usługi Azure DevOps dla nowego projektu.

1. Wybierz pozycję **Edit** (Edytuj).

    ![Edytuj potoku kompilacji](media/how-to-devops-project/click-edit-button.png)

1. W tym okienku możesz zapoznać się z różnymi zadaniami w potoku kompilacji. Kompilacja wykonuje różne zadania, takie jak pobieranie źródeł z repozytorium Git, tworzenia obrazów modułu usługi IoT Edge, wypychanie moduły usługi IoT Edge i publikowania używane dane wyjściowe, które są używane w przypadku wdrożeń. Aby dowiedzieć się więcej na temat zadań usługi Azure IoT Edge dla ciągłej integracji, możesz odwiedzić stronę [konfigurowania Azure potoków ciągłej integracji](https://docs.microsoft.com/azure/iot-edge/how-to-ci-cd#configure-azure-pipelines-for-continuous-integration).

    ![Wyświetl zadania ciągłej integracji](media/how-to-devops-project/ci.png)

1. W górnej części potoku kompilacji wybierz jego nazwę.

1. Zmień nazwę potoku kompilacji na bardziej opisową, wybierz pozycję **Zapisz i dodaj do kolejki**, a następnie wybierz pozycję **Zapisz**.

1. W obszarze nazwy potoku kompilacji wybierz pozycję **Historia**.   
W okienku **Historia** zostanie wyświetlony dziennik inspekcji zmian ostatnio wprowadzonych w kompilacji.  Usługa Azure Pipelines śledzi wszelkie zmiany wprowadzone do potoku kompilacji i pozwala na porównanie wersji.

1. Wybierz pozycję **Wyzwalacze**. Usługa DevOps Projects automatycznie utworzyła wyzwalacz ciągłej integracji — każde zatwierdzenie w repozytorium uruchamia nową kompilację.  Opcjonalnie możesz zdecydować się dołączyć gałęzie do procesu ciągłej integracji lub je wykluczyć.

1. Wybierz pozycję **Przechowywanie**. W zależności od scenariusza możesz określić zasady przechowywania lub usuwania pewnej liczby kompilacji.

1. Wybierz **wersji** w obszarze **potoki**. Projekty DevOps tworzy potok wersji w celu zarządzania wdrożeniami do usługi Azure IoT Edge.

    ![Wyświetl potoku tworzenia wersji](media/how-to-devops-project/release-pipeline.png)

1. Wybierz pozycję **Edit** (Edytuj). Potok wydania zawiera potok, który definiuje proces tworzenia wydania.  

1. W obszarze **Artefakty** wybierz polecenie **Porzuć**. Potok kompilacji przedstawiony w poprzednich krokach generuje dane wyjściowe używane na potrzeby artefaktu. 

1. Obok ikony **Porzuć** wybierz pozycję **Wyzwalacz ciągłego wdrażania**.  
Ten potok wydania ma włączony wyzwalacz ciągłego wdrażania, który przeprowadza wdrożenie za każdym razem, gdy jest dostępny nowy artefakt kompilacji. Opcjonalnie możesz wyłączyć wyzwalacz. Wtedy wdrożenia będą wymagać ręcznego wykonania.  

1. Po lewej stronie wybierz pozycję **Zadania**. Zadania to działania wykonywane w procesie wdrażania. W tym przykładzie zadanie zostało utworzone, aby wdrożyć swoje obrazy modułu usługi Azure IoT Edge. Aby dowiedzieć się więcej na temat zadań usługi Azure IoT Edge na dysk CD, możesz odwiedzić stronę [konfigurowania Azure potoków ciągłego wdrażania](https://docs.microsoft.com/azure/iot-edge/how-to-ci-cd#configure-azure-pipelines-for-continuous-deployment).

    ![Wyświetl zadania ciągłego wdrażania](media/how-to-devops-project/dev-release.png)

1. Po prawej stronie wybierz pozycję **Wyświetl wydania**. Ten widok przedstawia historię wersji.

1. Wybierz symbol wielokropka (...) obok jednego z wydań i wybierz pozycję **Otwórz**.  
Dostępnych jest kilka menu, z którymi możesz się zapoznać, na przykład podsumowanie wydań, skojarzone elementy robocze i testy.

1. Wybierz pozycję **Zatwierdzenia**. Ten widok przedstawia zatwierdzenia kodu skojarzone z konkretnym wdrożeniem. 

1. Wybierz pozycję **Dzienniki**. Dzienniki zawierają przydatne informacje na temat procesu wdrażania. Mogą być wyświetlane zarówno podczas wdrożeń, jak i po nich.


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy usługa Azure App Service i inne utworzone powiązane zasoby nie będą już potrzebne, możesz je usunąć. Użyj funkcji **Usuń** na pulpicie nawigacyjnym usługi DevOps Projects.

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o zadaniach usługi Azure IoT Edge na DevOps platformy Azure w [ciągłą integrację i ciągłe wdrażanie w usłudze Azure IoT Edge](how-to-ci-cd.md)
* Omówienie wdrożenia usługi IoT Edge w [wdrożeń zrozumieć usługi IoT Edge dla urządzeń z jednej lub w odpowiedniej skali](module-deployment-monitoring.md)
* Przewodnik po krokach do utworzenia, aktualizacji lub usunięcia wdrożenia w [wdrażanie i monitorowanie moduły usługi IoT Edge na dużą skalę](how-to-deploy-monitor.md).

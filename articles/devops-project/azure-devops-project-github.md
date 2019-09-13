---
title: 'Samouczek: Utwórz potok ciągłej integracji/ciągłego wdrażania dla istniejącego kodu przy użyciu Azure DevOps Projects'
description: Usługa Azure DevOps Projects ułatwia rozpoczęcie pracy na platformie Azure. Pomaga korzystać z własnego kodu i repozytorium GitHub w celu uruchomienia aplikacji w wybranej przez użytkownika usłudze platformy Azure w kilku prostych krokach.
services: vsts
documentationcenter: vs-devops-build
ms.author: mlearned
ms.manager: gwallace
editor: ''
ms.assetid: ''
ms.workload: web
ms.prod: devops
ms.technology: devops-cicd
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: 2abe24ad65e1e8997b48a28b35ec0e65162022f2
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70898007"
---
# <a name="tutorial-create-a-cicd-pipeline-for-your-existing-code-by-using-azure-devops-projects"></a>Samouczek: Utwórz potok ciągłej integracji/ciągłego wdrażania dla istniejącego kodu przy użyciu Azure DevOps Projects

Usługa Azure DevOps Projects stanowi uproszczone środowisko, do którego możesz przenieść istniejący kod i repozytorium Git lub wybrać aplikację przykładową, aby utworzyć potok ciągłej integracji i ciągłego dostarczania na platformie Azure.

Wykonasz następujące zadania:

> [!div class="checklist"]
> * Tworzenie potoku ciągłej integracji/ciągłego wdrażania za pomocą usługi DevOps Projects
> * Konfigurowanie dostępu do repozytorium GitHub i wybieranie platformy
> * Konfigurowanie usługi Azure DevOps i subskrypcji platformy Azure 
> * Zatwierdzanie zmian w usłudze GitHub i automatyczne wdrażanie ich na platformie Azure
> * Badanie potoku ciągłej integracji/ciągłego wdrażania usługi Azure Pipelines
> * Konfigurowanie monitorowania usługi Azure Application Insights
> * Oczyszczanie zasobów

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Możesz uzyskać ją bezpłatnie za pośrednictwem programu [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).
* Uzyskaj dostęp do usługi GitHub lub zewnętrznego repozytorium Git, które zawiera kod .NET, Java, PHP, Node, Python lub statyczny kod stron internetowych.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Usługa Azure DevOps Projects tworzy potok ciągłej integracji/ciągłego wdrażania w usłudze Azure Pipelines. Możesz utworzyć nową organizację usługi Azure DevOps lub użyć istniejącej organizacji. Usługa Azure DevOps Projects tworzy również zasoby platformy Azure w wybranej przez użytkownika subskrypcji platformy Azure.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. W lewym okienku wybierz pozycję **Nowy**.

1. W polu wyszukiwania wpisz ciąg **DevOps Projects**, a następnie wybierz pozycję **Utwórz**.

    ![Pulpit nawigacyjny usługi DevOps Projects](_img/azure-devops-project-github/fullbrowser.png)

1. Wybierz pozycję **Dodaj własny kod**, a następnie wybierz pozycję **Dalej**.

## <a name="configure-access-to-your-github-repo-and-choose-a-framework"></a>Konfigurowanie dostępu do repozytorium GitHub i wybieranie platformy

1. Wybierz pozycję **GitHub** lub zewnętrzne repozytorium Git, a następnie wybierz repozytorium i gałąź, która zawiera aplikację.

1. Wybierz platformę internetową, a następnie wybierz pozycję **Dalej**.

    ![Struktura .NET](_img/azure-devops-project-github/webframework.png)

    Wybrana poprzednio struktura aplikacji decyduje o dostępnym w tym miejscu typie celu wdrożenia usługi platformy Azure. 
    
1. Wybierz usługę docelową, a następnie wybierz pozycję **Dalej**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurowanie usługi Azure DevOps i subskrypcji platformy Azure 

1. Utwórz nową organizację usługi Azure DevOps lub wybierz istniejącą organizację.

    a. Wprowadź nazwę projektu w usłudze Azure DevOps. 
    
    b. Wybierz swoją subskrypcję platformy Azure i lokalizację, wprowadź nazwę aplikacji, a następnie wybierz pozycję **Gotowe**.

    Po kilku minutach w witrynie Azure Portal zostanie wyświetlony pulpit nawigacyjny usługi DevOps Projects. Aplikacja przykładowa zostanie skonfigurowana w repozytorium w organizacji usługi Azure DevOps, skompilowana i wdrożona na platformie Azure. Ten pulpit nawigacyjny zapewnia wgląd do repozytorium kodu GitHub, potoku ciągłej integracji/ciągłego wdrażania i aplikacji na platformie Azure. 
    
1. Wybierz pozycję **Przeglądaj**, aby wyświetlić uruchomioną aplikację.

    ![Widok pulpitu nawigacyjnego usługi DevOps Projects](_img/azure-devops-project-github/dashboardnopreview.png) 
    
Usługa Azure DevOps Projects automatycznie konfiguruje wyzwalacz kompilacji i wydania ciągłej integracji. Twój kod pozostaje w repozytorium GitHub lub innym zewnętrznym repozytorium. 

## <a name="commit-changes-to-github-and-automatically-deploy-them-to-azure"></a>Zatwierdzanie zmian w usłudze GitHub i automatyczne wdrażanie ich na platformie Azure 

Teraz możesz rozpocząć współpracę z zespołem nad aplikacją w języku Python w ramach procesu ciągłej integracji/ciągłego wdrażania, który automatycznie wdraża najnowszy kod w witrynie internetowej. Każda zmiana w repozytorium GitHub rozpoczyna kompilację w usłudze Azure DevOps, a potok ciągłego wdrażania wykonuje wdrażanie na platformie Azure.

1. Wprowadź zmiany w aplikacji, a następnie zatwierdź zmiany w repozytorium GitHub.  
    Po kilku chwilach rozpocznie się kompilacja w usłudze Azure Pipelines. Stan kompilacji możesz monitorować na pulpicie nawigacyjnym usługi DevOps Projects lub w przeglądarce przy użyciu organizacji usługi Azure DevOps.

1. Po zakończeniu kompilacji odśwież aplikację, aby zweryfikować zmiany.

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>Badanie potoku ciągłej integracji/ciągłego wdrażania usługi Azure Pipelines

Usługa Azure DevOps Projects automatycznie konfiguruje potok ciągłej integracji/ciągłego wdrażania w usłudze Azure Pipelines. Możesz przeglądać i dostosowywać potok według potrzeb. Aby zapoznać się z potokami kompilacji i wydania, wykonaj następujące czynności:

1. U góry pulpitu nawigacyjnego usługi DevOps Projects wybierz pozycję **Potoki kompilacji**.  
    Zostanie wyświetlona karta przeglądarki z potokiem kompilacji dla nowego projektu.

1. Wskaż pole **Stan** i wybierz symbol wielokropka (...).  
    Zostanie wyświetlone menu z kilkoma opcjami, takimi jak dodanie nowej kompilacji do kolejki, wstrzymanie kompilacji i edytowanie potoku kompilacji.

1. Wybierz pozycję **Edit** (Edytuj).

1. W tym okienku możesz zapoznać się z różnymi zadaniami w potoku kompilacji.  
    W ramach kompilacji są wykonywane różne zadania, takie jak pobieranie kodu źródłowego z repozytorium Git, przywracanie zależności i publikowanie danych wyjściowych używanych na potrzeby wdrażania.

1. W górnej części potoku kompilacji wybierz jego nazwę.

1. Zmień nazwę potoku kompilacji na bardziej opisową, wybierz pozycję **Zapisz i dodaj do kolejki**, a następnie wybierz pozycję **Zapisz**.

1. W obszarze nazwy potoku kompilacji wybierz pozycję **Historia**.  
    Zostanie wyświetlony dziennik inspekcji ostatnio wprowadzonych zmian w kompilacji. Usługa Azure DevOps śledzi wszelkie zmiany wprowadzone do potoku kompilacji i pozwala na porównanie wersji.

1. Wybierz pozycję **Wyzwalacze**.  
    Usługa DevOps Projects automatycznie tworzy wyzwalacz ciągłej integracji — każde zatwierdzenie w repozytorium uruchamia nową kompilację. Opcjonalnie możesz zdecydować się dołączyć gałęzie do procesu ciągłej integracji lub wykluczyć je z niego.

1. Wybierz pozycję **Przechowywanie**.  
        W zależności od scenariusza możesz określić zasady przechowywania lub usuwania pewnej liczby kompilacji.

1. Wybierz pozycję **Kompilacja i wydanie**, a następnie wybierz pozycję **Wydania**.  
    Usługa Azure DevOps Projects tworzy potok wydania w celu zarządzania wdrożeniami na platformie Azure.

1. Wybierz symbol wielokropka (...) obok potoku wydania, a następnie wybierz pozycję **Edytuj**.  
    Potok wydania zawiera *potok*, który definiuje proces tworzenia wydania. 
    
1. W obszarze **Artefakty** wybierz polecenie **Porzuć**.  
    Potok kompilacji przedstawiony w poprzednich krokach generuje dane wyjściowe używane na potrzeby artefaktu. 

1. Obok ikony **Porzuć** wybierz pozycję **Wyzwalacz ciągłego wdrażania**.  
    Ten potok wydania ma włączony wyzwalacz ciągłego wdrażania, który przeprowadza wdrożenie za każdym razem, gdy dostępny jest nowy artefakt kompilacji. Opcjonalnie możesz wyłączyć wyzwalacz. Wtedy wdrożenia będą wymagać ręcznego wykonania. 

1. Po lewej stronie wybierz pozycję **Zadania**.  
    Zadania to czynności wykonywane w procesie wdrażania. W tym przykładzie zostało utworzone zadanie w celu wdrożenia w usłudze Azure App Service.

1. Po prawej stronie wybierz pozycję **Wyświetl wydania**, aby wyświetlić historię wydań.

1. Wybierz symbol wielokropka (...) obok wydania, a następnie wybierz pozycję **Otwórz**.  
    Dostępnych jest kilka menu, z którymi możesz się zapoznać, na przykład podsumowanie wydań, skojarzone elementy robocze i testy.

1. Wybierz pozycję **Zatwierdzenia**.  
    Ten widok przedstawia zatwierdzenia kodu skojarzone z danym wdrożeniem. 

1. Wybierz pozycję **Dzienniki**.  
    Dzienniki zawierają przydatne informacje na temat procesu wdrażania. Możesz je wyświetlać zarówno podczas wdrażania, jak i po jego zakończeniu.

## <a name="configure-azure-application-insights-monitoring"></a>Konfigurowanie monitorowania usługi Azure Application Insights

Usługa Azure Application Insights umożliwia łatwe monitorowanie wydajności i użycia aplikacji. Usługa Azure DevOps Projects automatycznie skonfigurował zasób usługi Application Insights dla aplikacji. Możesz dodatkowo skonfigurować różne alerty i możliwości monitorowania stosownie do potrzeb.

1. W witrynie Azure Portal przejdź do pulpitu nawigacyjnego usługi DevOps Projects. 

1. W prawym dolnym rogu wybierz link **Application Insights** dla Twojej aplikacji.  
    Zostanie otwarte okienko **Application Insights**. Ten widok zawiera informacje o monitorowaniu użycia, wydajności i dostępności aplikacji.

    ![Okienko Application Insights](_img/azure-devops-project-github/appinsights.png) 

1. Wybierz pozycję **Zakres czasu**, a następnie wybierz wartość **Ostatnia godzina**. Aby filtrować wyniki, wybierz pozycję **Aktualizuj**.  
    Teraz możesz przejrzeć wszystkie działania z ostatnich 60 minut. Aby zamknąć zakres czasu, wybierz pozycję **x**

1. Wybierz pozycję **Alerty**, a następnie wybierz pozycję **Dodaj alert metryki**. 

1. Wprowadź nazwę alertu.

1. Z listy rozwijanej **Zmiana obiektu źródłowego** wybierz **zasób usługi App Service**. <!-- Please confirm whether this should be "Source Alter on" or "Source Alert on" -->

1. Na liście rozwijanej **Metryka** zapoznaj się z różnymi metrykami alertów.  
    Domyślny alert dotyczy **czasu odpowiedzi serwera dłuższego niż 1 sekunda**. Możesz łatwo skonfigurować różne alerty, aby zwiększyć możliwości monitorowania aplikacji.

1. Zaznacz pole wyboru **Powiadom pocztą e-mail właścicieli, współautorów i czytelników**.  
    Opcjonalnie możesz wykonać dodatkowe akcje po wyświetleniu alertu, wykonując aplikację logiki platformy Azure.

1. Wybierz pozycję **OK**, aby utworzyć alert.  
    Po chwili alert będzie widoczny jako aktywny na pulpicie nawigacyjnym.
    
1. Zamknij obszar **Alerty** i przejdź z powrotem do okienka **Application Insights**.

1. Wybierz pozycję **Dostępność**, a następnie wybierz pozycję **Dodaj test**. 

1. Wprowadź nazwę testu, a następnie wybierz pozycję **Utwórz**.  
    Zostanie utworzony prosty test ping w celu sprawdzania dostępności aplikacji. Po kilku minutach wyniki testów będą dostępne, a na pulpicie nawigacyjnym usługi Application Insights zostanie wyświetlony stan dostępności.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy usługa Azure App Service i powiązane zasoby utworzone w tym samouczku nie będą już potrzebne, możesz je usunąć. W tym celu użyj funkcji **Usuń** na pulpicie nawigacyjnym usługi DevOps Projects.

## <a name="next-steps"></a>Następne kroki

Podczas pracy z tym samouczkiem skonfigurowano proces ciągłej integracji/ciągłego wdrażania oraz utworzono automatycznie potok kompilacji i wydania w usłudze Azure DevOps Projects. Możesz zmodyfikować potoki kompilacji i wydania, aby dopasować je do potrzeb swojego zespołu. W tym samouczku omówiono:

> [!div class="checklist"]
> * Tworzenie potoku ciągłej integracji/ciągłego wdrażania za pomocą usługi DevOps Projects
> * Konfigurowanie dostępu do repozytorium GitHub i wybieranie platformy
> * Konfigurowanie usługi Azure DevOps i subskrypcji platformy Azure 
> * Zatwierdzanie zmian w usłudze GitHub i automatyczne wdrażanie ich na platformie Azure
> * Badanie potoku ciągłej integracji/ciągłego wdrażania usługi Azure Pipelines
> * Konfigurowanie monitorowania usługi Azure Application Insights
> * Oczyszczanie zasobów

Aby dowiedzieć się więcej na temat potoku ciągłej integracji/ciągłego wdrażania, zobacz:

> [!div class="nextstepaction"]
> [Definiowanie wieloetapowego potoku ciągłego wdrażania](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

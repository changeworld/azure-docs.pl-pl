---
title: Tworzenie potoku ciągłej integracji/ciągłego wdrażania dla istniejącego kodu za pomocą projektu usługi Azure DevOps | Samouczek usług Azure DevOps Services
description: Projekt DevOps ułatwia rozpoczęcie pracy na platformie Azure. Pomaga wykorzystać własny kod i repozytorium GitHub w celu uruchomienia aplikacji w wybranej przez użytkownika usłudze platformy Azure w kilku prostych krokach.
services: vsts
documentationcenter: vs-devops-build
ms.author: mlearned
ms.manager: douge
editor: ''
ms.assetid: ''
ms.workload: web
ms.prod: devops
ms.technology: devops-cicd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: 02b6823a46c94edb0ba28c7a2a8b9ae0efc44ae8
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/18/2018
ms.locfileid: "49406096"
---
# <a name="tutorial--create-a-cicd-pipeline-for-your-existing-code-with-the-azure-devops-project"></a>Samouczek: tworzenie potoku ciągłej integracji/ciągłego wdrażania dla istniejącego kodu za pomocą projektu DevOps platformy Azure

Projekt DevOps platformy Azure stanowi uproszczone środowisko, do którego możesz przenieść istniejący kod i repozytorium Git lub dokonać wyboru jednej z przykładowych aplikacji, aby utworzyć potok ciągłej integracji i ciągłego dostarczania na platformie Azure.

Wykonasz następujące zadania:

> [!div class="checklist"]
> * Tworzenie projektu usługi Azure DevOps
> * Konfigurowanie dostępu do repozytorium GitHub i wybieranie platformy
> * Konfigurowanie usługi Azure DevOps Services i subskrypcji platformy Azure 
> * Zatwierdzanie zmian w usłudze GitHub i automatyczne wdrażanie na platformie Azure
> * Sprawdzanie potoku ciągłej integracji/ciągłego wdrażania usługi Azure DevOps Services
> * Konfigurowanie monitorowania usługi Azure Application Insights

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Możesz uzyskać ją bezpłatnie za pośrednictwem programu [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).
* Uzyskaj dostęp do usługi GitHub lub zewnętrznego repozytorium Git, które zawiera kod .NET, Java, PHP, Node, Python lub statyczny kod stron internetowych.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Projekt usługi Azure DevOps tworzy potok ciągłej integracji/ciągłego wdrażania w usłudze Azure DevOps Services.  Możesz utworzyć **nową organizację usługi Azure DevOps Services** lub użyć **istniejącej organizacji**.  Projekt DevOps platformy Azure tworzy również **zasoby platformy Azure** w wybranej przez użytkownika **subskrypcji platformy Azure**.

1. Zaloguj się do witryny [Microsoft Azure Portal](https://portal.azure.com).

1. Wybierz ikonę **+ Nowy** na pasku nawigacyjnym po lewej stronie, a następnie wyszukaj pozycję **Projekt DevOps**.  Wybierz pozycję **Utwórz**.

    ![Rozpoczynanie ciągłego dostarczania](_img/azure-devops-project-github/fullbrowser.png)

1. Wybierz pozycję **Model dostarczania własnego kodu**.  Gdy wszystko będzie gotowe, wybierz pozycję **Dalej**.

## <a name="configure-access-to-your-github-repository-and-choose-a-framework"></a>Konfigurowanie dostępu do repozytorium GitHub i wybieranie platformy

1. Wybierz pozycję **GitHub**.  Opcjonalnie możesz wybrać **zewnętrzne repozytorium Git**.  Wybierz **repozytorium** i **gałąź** zawierającą aplikację.

1. Wybierz swoją **platformę internetową**.  Gdy wszystko będzie gotowe, wybierz pozycję **Dalej**.

    ![Struktura .NET](_img/azure-devops-project-github/webframework.png)

1. Struktura aplikacji wybrana w poprzednich krokach decyduje o dostępnym w tym miejscu typie celu wdrożenia usługi platformy Azure.  Wybierz **usługę docelową**.  Gdy wszystko będzie gotowe, wybierz pozycję **Dalej**.

## <a name="configure-azure-devops-services-and-an-azure-subscription"></a>Konfigurowanie usługi Azure DevOps Services i subskrypcji platformy Azure 

1. Utwórz **nową** organizację usługi Azure DevOps Services lub wybierz **istniejącą** organizację.  Wybierz **nazwę** swojego projektu usługi Azure DevOps.  Wybierz swoją **subskrypcję platformy Azure**, **lokalizację** i **nazwę** aplikacji.  Gdy wszystko będzie gotowe, wybierz pozycję **Gotowe**.

1. W ciągu kilku minut w witrynie Azure Portal zostanie załadowany **pulpit nawigacyjny projektu usługi Azure DevOps**.  Aplikacja przykładowa zostanie skonfigurowana w repozytorium w organizacji usługi Azure DevOps Services, skompilowana i wdrożona na platformie Azure.  Ten pulpit nawigacyjny zapewnia wgląd do **repozytorium kodu** GitHub, **potoku ciągłej integracji/ciągłego wdrażania usług Azure DevOps Services** i **aplikacji na platformie Azure**.  Po prawej stronie pulpitu nawigacyjnego wybierz pozycję **Przeglądaj**, aby wyświetlić uruchomioną aplikację.

    ![Widok pulpitu nawigacyjnego](_img/azure-devops-project-github/dashboardnopreview.png) 
    
Projekt usługi Azure DevOps automatycznie konfiguruje wyzwalacz kompilacji i wydania ciągłej integracji.  Twój kod pozostaje w usłudze GitHub lub innym zewnętrznym repozytorium.  

## <a name="commit-changes-to-github-and-automatically-deploy-to-azure"></a>Zatwierdzanie zmian w usłudze GitHub i automatyczne wdrażanie na platformie Azure 

Możesz teraz rozpocząć pracę w zespole nad aplikacją w ramach procesu ciągłej integracji/ciągłego wdrażania, który umożliwia automatyczne wdrażanie najnowszej wersji w witrynie internetowej.  Każda zmiana w repozytorium GitHub rozpoczyna kompilację w usłudze Azure DevOps, a potok ciągłego wdrażania usługi Azure DevOps wykonuje wdrażanie na platformie Azure.

1.  Wprowadź zmiany w aplikacji, a następnie **zatwierdź** zmiany w repozytorium GitHub.
2.  W ciągu kilku chwil rozpocznie się kompilacja w usłudze Azure DevOps Services.  Możesz monitorować stan kompilacji na pulpicie nawigacyjnym projektu usługi Azure DevOps lub w przeglądarce przy użyciu Twojej organizacji usługi Azure DevOps Services.
3.  Po zakończeniu kompilacji **odśwież aplikację** w przeglądarce, aby sprawdzić, czy zmiany są wyświetlane.

## <a name="examine-the-azure-devops-services-cicd-pipeline"></a>Sprawdzanie potoku ciągłej integracji/ciągłego wdrażania usługi Azure DevOps Services

Projekt usługi Azure DevOps automatycznie skonfigurował potok ciągłej integracji/ciągłego wdrażania usługi Azure DevOps Services w Twojej organizacji usługi Azure DevOps Services.  Możesz przeglądać i dostosowywać potok według potrzeb.  Wykonaj poniższe kroki, aby zapoznać się z kompilacją usługi Azure DevOps Services i potokami wersji.

1. Wybierz pozycję **Potoki kompilacji** w **górnej części** pulpitu nawigacyjnego projektu usługi Azure DevOps.  Ten link otwiera kartę przeglądarki i otwiera potok kompilacji usługi Azure DevOps Services dla Twojego nowego projektu.

1. Przesuń wskaźnik myszy na prawo od potoku kompilacji, obok pola **Stan**. Wybierz symbol **wielokropka**, który się pojawi.  Ta czynność spowoduje otwarcie menu, w którym możesz uruchomić kilka działań, takich jak dodawanie nowych kompilacji do kolejki, wstrzymywanie kompilacji i edytowanie potoku kompilacji.

1. Wybierz pozycję **Edit** (Edytuj).

1. Z poziomu tego widoku **zapoznaj się z różnymi zadaniami** w potoku kompilacji.  W ramach kompilacji są wykonywane różne zadania, takie jak pobieranie źródeł z repozytorium Git, przywracanie zależności i publikowanie danych wyjściowych używanych do wdrożenia.

1. W górnej części potoku kompilacji wybierz **nazwę potoku kompilacji**.

1. Zmień **nazwę** potoku kompilacji na bardziej opisową.  Wybierz pozycję **Zapisz i dodaj do kolejki**, a następnie wybierz pozycję **Zapisz**.

1. W obszarze nazwy potoku kompilacji wybierz pozycję **Historia**.  Zostanie wyświetlony dziennik inspekcji ostatnio wprowadzonych zmian w kompilacji.  Usługa Azure DevOps Services śledzi wszelkie zmiany wprowadzone do potoku kompilacji i pozwala na porównanie wersji.

1. Wybierz pozycję **Wyzwalacze**.  Projekt usługi Azure DevOps automatycznie utworzył wyzwalacz ciągłej integracji, a każde zatwierdzenie w repozytorium uruchamia nową kompilację.  Opcjonalnie możesz zdecydować się dołączyć gałęzie do procesu ciągłej integracji lub je wykluczyć.

1. Wybierz pozycję **Przechowywanie**.  W zależności od scenariusza możesz określić zasady przechowywania lub usuwania pewnej liczby kompilacji.

1. Wybierz pozycję **Kompilacja i wydanie**, a następnie wybierz pozycję **Wydania**.  Projekt usługi Azure DevOps utworzył potok wersji usługi Azure DevOps Services w celu zarządzania wdrożeniami na platformie Azure.

1. Po lewej stronie okna przeglądarki wybierz symbol **wielokropka** obok potoku wydania, a następnie wybierz pozycję **Edytuj**.

1. Potok wydania zawiera **potok**, który definiuje proces tworzenia wydania.  W obszarze **Artefakty** wybierz polecenie **Porzuć**.  Potok kompilacji przedstawiony w poprzednich krokach generuje dane wyjściowe używane na potrzeby artefaktu. 

1. Z prawej strony ikony **Porzuć** wybierz pozycję **Wyzwalacz ciągłego wdrażania**.  Ten potok wydania ma włączony wyzwalacz ciągłego wdrażania, który przeprowadza wdrożenie za każdym razem, gdy dostępny jest nowy artefakt kompilacji.  Opcjonalnie możesz wyłączyć wyzwalacz. Wtedy wdrożenia będą wymagać ręcznego wykonania. 

1. Po lewej stronie okna przeglądarki wybierz pozycję **Zadania**.  Zadania to czynności wykonywane w procesie wdrażania.  W tym przykładzie zostało utworzone zadanie w celu wdrożenia w usłudze **Azure App Service**.

1. Po prawej stronie okna przeglądarki wybierz pozycję **Wyświetl wersje**.  Ten widok przedstawia historię wersji.

1. Wybierz symbol **wielokropka** obok jednej z wersji i wybierz polecenie **Otwórz**.  W tym widoku jest kilka menu, z którymi możesz się zapoznać, na przykład podsumowanie wersji, skojarzone elementy robocze i testy.

1. Wybierz pozycję **Zatwierdzenia**.  Ten widok przedstawia zatwierdzenia kodu skojarzone z konkretnym wdrożeniem. 

1. Wybierz pozycję **Dzienniki**.  Dzienniki zawierają przydatne informacje na temat procesu wdrażania.  Mogą być wyświetlane zarówno podczas wdrożeń, jak i po nich.

## <a name="configure-azure-application-insights-monitoring"></a>Konfigurowanie monitorowania usługi Azure Application Insights

Usługa Azure Application Insights umożliwia łatwe monitorowanie wydajności i użycia aplikacji.  Projekt usługi Azure DevOps automatycznie skonfigurował zasób usługi Application Insights dla aplikacji.  Możesz dodatkowo skonfigurować różne alerty i możliwości monitorowania stosownie do potrzeb.

1. Przejdź do pulpitu nawigacyjnego **projektu DevOps platformy Azure** w witrynie Azure Portal.  W prawym dolnym rogu pulpitu nawigacyjnego wybierz link **Application Insights** dla aplikacji.

1. Zostanie otwarty blok usługi **Application Insights** w witrynie Azure Portal.  Ten widok zawiera informacje o monitorowaniu użycia, wydajności i dostępności aplikacji.

    ![Application Insights](_img/azure-devops-project-github/appinsights.png) 

1. Wybierz pozycję **Zakres czasu**, a następnie wybierz wartość **Ostatnia godzina**.  Wybierz pozycję **Aktualizuj**, aby filtrować wyniki.  Zobaczysz teraz wszystkie działania z ostatnich 60 minut.  Wybierz **x** aby zakończyć działanie zakresu czasu.

1. Wybierz pozycję **Alerty**, a następnie wybierz pozycję **+ Dodaj alert dotyczący metryki**.  

1. Wprowadź **nazwę** alertu.

1. Wybierz listę rozwijaną dla **zmiany obiektu źródłowego**.  Wybierz **zasób usługi App Service.**
<!-- Could you please confirm if this should be "Source Alter on" instead of "Source Alert on"? -->
1. Domyślny alert dotyczy **czasu odpowiedzi serwera dłuższego niż 1 sekunda**.  Wybierz listę rozwijaną **Metryka**, aby zbadać różne metryki alertu.  Możesz łatwo skonfigurować różne alerty, aby zwiększyć możliwości monitorowania aplikacji.

1. Zaznacz pole wyboru w celu **powiadamiania za pośrednictwem poczty e-mail właścicieli, współautorów i czytelników**.  Opcjonalnie możesz wykonać dodatkowe akcje po wyzwoleniu alertu, wykonując aplikację logiki platformy Azure.

1. Wybierz pozycję **OK**, aby utworzyć alert.  Za chwilę alert pojawi się jako aktywny na pulpicie nawigacyjnym.  **Zakończ** działanie obszaru Alerty i przejdź z powrotem do **bloku usługi Application Insights**.

1. Wybierz pozycję **Dostępność**, a następnie wybierz pozycję **+ Dodaj test**. 

1. Wprowadź **nazwę testu**, następnie wybierz pozycję **Utwórz**.  Zostanie utworzony prosty test ping w celu sprawdzania dostępności aplikacji.  Po kilku minutach wyniki testów będą dostępne, a na pulpicie nawigacyjnym usługi Application Insights zostanie wyświetlony stan dostępności.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli usługa Azure App Service nie jest już potrzebna, możesz ją usunąć wraz z powiązanymi zasobami utworzonymi w tym przewodniku Szybki start, korzystając z funkcji **Usuń** na pulpicie nawigacyjnym projektu DevOps platformy Azure.

## <a name="next-steps"></a>Następne kroki

Podczas pracy z tym samouczkiem skonfigurowano proces ciągłej integracji/ciągłego wdrażania oraz utworzono automatycznie potok kompilacji i wydania w projekcie usługi Azure DevOps. Możesz zmodyfikować potoki kompilacji i wydania, aby dopasować je do potrzeb swojego zespołu. W tym samouczku omówiono:

> [!div class="checklist"]
> * Tworzenie projektu usługi Azure DevOps
> * Konfigurowanie dostępu do repozytorium GitHub i wybieranie platformy
> * Konfigurowanie usługi Azure DevOps Services i subskrypcji platformy Azure 
> * Zatwierdzanie zmian w usłudze GitHub i automatyczne wdrażanie na platformie Azure
> * Sprawdzanie potoku ciągłej integracji/ciągłego wdrażania usługi Azure DevOps Services
> * Konfigurowanie monitorowania usługi Azure Application Insights

Aby dowiedzieć się więcej o potoku ciągłej integracji/ciągłego wdrażania usługi Azure DevOps Services, zobacz ten samouczek:

> [!div class="nextstepaction"]
> [Dostosowywanie procesu ciągłego wdrażania](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

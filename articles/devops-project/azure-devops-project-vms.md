---
title: Wdrażanie aplikacji ASP.NET na maszynach wirtualnych platformy Azure za pomocą projektu usługi Azure DevOps | Samouczek usługi Azure DevOps Services
description: Projekt DevOps ułatwia rozpoczęcie pracy na platformie Azure. Projekt usługi Azure DevOps umożliwia łatwe wdrożenie aplikacji ASP.NET na maszynach wirtualnych platformy Azure w kilku prostych krokach.
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: b05e2c2c46aa9bfa8c92d3d3c5c83d018c547b9f
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2018
ms.locfileid: "44299138"
---
# <a name="tutorial--deploy-your-aspnet-app-to-azure-virtual-machines-with-the-azure-devops-project"></a>Samouczek: Wdrażanie aplikacji ASP.NET na maszynach wirtualnych platformy Azure za pomocą projektu DevOps platformy Azure

Projekt DevOps platformy Azure stanowi uproszczone środowisko, do którego możesz przenieść istniejący kod i repozytorium Git lub dokonać wyboru jednej z przykładowych aplikacji, aby utworzyć potok ciągłej integracji i ciągłego dostarczania na platformie Azure.  Projekt DevOps automatycznie tworzy zasoby platformy Azure, takie jak nowa maszyna wirtualna platformy Azure, tworzy i konfiguruje potok wydania w usłudze Azure DevOps zawierający potok kompilacji dla ciągłej integracji, konfiguruje potok wydania dla ciągłego wdrażania, a następnie tworzy zasób usługi Azure Application Insights na potrzeby monitorowania.

Wykonasz następujące zadania:

> [!div class="checklist"]
> * Tworzenie projektu usługi Azure DevOps dla aplikacji platformy ASP.NET
> * Konfigurowanie usługi Azure DevOps Services i subskrypcji platformy Azure 
> * Badanie potoku ciągłej integracji usługi Azure DevOps Services
> * Badanie potoku ciągłego wdrażania usługi Azure DevOps Services
> * Zatwierdzanie zmian w usłudze Azure DevOps Services i automatyczne wdrażanie na platformie Azure
> * Konfigurowanie monitorowania usługi Azure Application Insights
> * Oczyszczanie zasobów

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Możesz uzyskać ją bezpłatnie za pośrednictwem programu [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="create-an-azure-devops-project-for-an-aspnet-app"></a>Tworzenie projektu usługi Azure DevOps dla aplikacji platformy ASP.NET

Projekt usługi Azure DevOps tworzy potok ciągłej integracji/ciągłego wdrażania na platformie Azure.  Możesz utworzyć **nową organizację usługi Azure DevOps Services** lub użyć **istniejącej organizacji**.  Projekt DevOps platformy Azure tworzy również **zasoby platformy Azure**, na przykład maszyny wirtualne, w wybranej przez użytkownika **subskrypcji platformy Azure**.

1. Zaloguj się do witryny [Microsoft Azure Portal](https://portal.azure.com).

1. Wybierz ikonę **+ Nowy** na pasku nawigacyjnym po lewej stronie, a następnie wyszukaj pozycję **Projekt DevOps**.  Wybierz pozycję **Utwórz**.

    ![Rozpoczynanie ciągłego dostarczania](_img/azure-devops-project-github/fullbrowser.png)

1. Wybierz pozycję **.NET**, a następnie wybierz pozycję **Dalej**.

1. Na stronie **Wybierz strukturę aplikacji** wybierz opcję **ASP.NET**, a następnie wybierz pozycję **Dalej**. 

1. Struktura aplikacji wybrana w poprzednich krokach decyduje o dostępnym w tym miejscu typie celu wdrożenia usługi platformy Azure.  Wybierz **maszynę wirtualną**, a następnie wybierz pozycję **Dalej**.

## <a name="configure-azure-devops-services-and-an-azure-subscription"></a>Konfigurowanie usługi Azure DevOps Services i subskrypcji platformy Azure

1. Utwórz **nową** organizację usługi Azure DevOps Services lub wybierz **istniejącą** organizację.  Wybierz **nazwę** projektu usługi Azure DevOps.  

1. Wybierz **usługi subskrypcji platformy Azure**.  Opcjonalnie możesz wybrać link **Zmień**, a następnie wprowadzić więcej szczegółów konfiguracji, takich jak zmiana lokalizacji zasobów platformy Azure.
 
1. Wprowadź **nazwę maszyny wirtualnej**, **nazwę użytkownika** i **hasło** dla nowego zasobu maszyny wirtualnej platformy Azure, a następnie wybierz pozycję **Gotowe**.

1. Potrwa kilka minut, zanim maszyna wirtualna platformy Azure będzie gotowa.  Przykładowa aplikacja ASP.NET zostanie skonfigurowana w repozytorium w organizacji usługi Azure DevOps Services, zostaną wykonane kompilacja i wydanie, a aplikacja zostanie wdrożona na nowo utworzonej maszynie wirtualnej platformy Azure.  

    Po zakończeniu **pulpit nawigacyjny projektu** DevOps platformy Azure załaduje się w witrynie Azure Portal.  Możesz również przejść do **pulpitu nawigacyjnego projektu DevOps platformy Azure** bezpośrednio z obszaru **Wszystkie zasoby** w witrynie **Azure Portal**.  

    Ten pulpit nawigacyjny zapewnia wgląd do **repozytorium kodu** usługi Azure DevOps, **potoku ciągłej integracji/ciągłego wdrażania na platformie Azure** i uruchomionej **aplikacji na platformie Azure**.    

    ![Widok pulpitu nawigacyjnego](_img/azure-devops-project-vms/dashboardnopreview.png)

1. Projekt usługi Azure DevOps automatycznie konfiguruje wyzwalacz kompilacji i wydania ciągłej integracji, który automatycznie wdraża wszelkie zmiany kodu w repozytorium.  Dalsze dodatkowe opcje można skonfigurować w usłudze Azure DevOps.  Po prawej stronie pulpitu nawigacyjnego wybierz pozycję **Przeglądaj**, aby wyświetlić uruchomioną aplikację.
    
## <a name="examine-the-azure-devops-services-ci-pipeline"></a>Badanie potoku ciągłej integracji usługi Azure DevOps Services
 
Projekt usługi Azure DevOps automatycznie skonfigurował pełny potok ciągłej integracji/ciągłego wdrażania na platformie Azure w organizacji usługi Azure DevOps Services.  Możesz przeglądać i dostosowywać potok.  Wykonaj poniższe kroki, aby zapoznać się z potokiem kompilacji usługi Azure DevOps Services.

1. Wybierz pozycję **Potoki kompilacji** w **górnej części** **pulpitu nawigacyjnego projektu usługi Azure DevOps**.  Ten link powoduje otwarcie karty przeglądarki oraz otwarcie potoku kompilacji usługi Azure DevOps Services dla nowego projektu.

1. Przesuń wskaźnik myszy na prawo od potoku kompilacji, obok pola **Stan**. Wybierz symbol **wielokropka**, który się pojawi.  Ta akcja spowoduje otwarcie menu, w którym możesz wykonać kilka działań, takich jak **dodawanie nowej kompilacji do kolejki**, **wstrzymywanie kompilacji** i **edytowanie potoku kompilacji**.

1. Wybierz pozycję **Edit** (Edytuj).

1. Z poziomu tego widoku **zapoznaj się z różnymi zadaniami** w potoku kompilacji.  W ramach kompilacji są wykonywane różne zadania, takie jak pobieranie źródeł z repozytorium Git usługi Azure DevOps Services, przywracanie zależności i publikowanie danych wyjściowych używanych na potrzeby wdrożeń.

1. W górnej części potoku kompilacji wybierz **nazwę potoku kompilacji**.

1. Zmień **nazwę** potoku kompilacji na bardziej opisową.  Wybierz pozycję **Zapisz i dodaj do kolejki**, a następnie wybierz pozycję **Zapisz**.

1. W obszarze nazwy potoku kompilacji wybierz pozycję **Historia**.  Zostanie wyświetlony dziennik inspekcji ostatnio wprowadzonych zmian w kompilacji.  Usługa Azure DevOps Services śledzi wszelkie zmiany wprowadzone w potoku kompilacji i pozwala na porównywanie wersji.

1. Wybierz pozycję **Wyzwalacze**.  Projekt usługi Azure DevOps automatycznie utworzył wyzwalacz ciągłej integracji. Każde zatwierdzenie w repozytorium uruchamia nową kompilację.  Opcjonalnie możesz zdecydować się dołączyć gałęzie do procesu ciągłej integracji lub je wykluczyć.

1. Wybierz pozycję **Przechowywanie**.  W zależności od scenariusza możesz określić zasady przechowywania lub usuwania pewnej liczby kompilacji.

## <a name="examine-the-azure-devops-services-cd-pipeline"></a>Badanie potoku ciągłego wdrażania usługi Azure DevOps Services

Projekt usługi Azure DevOps automatycznie tworzy i konfiguruje wymagane kroki wdrożenia z organizacji usługi Azure DevOps Services do subskrypcji platformy Azure.  Te kroki obejmują konfigurowanie połączenia usługi platformy Azure w celu uwierzytelniania usługi Azure DevOps Services w subskrypcji platformy Azure.  Automatyzacja tworzy również potok wydania usługi Azure DevOps Services, co zapewnia ciągłe wdrażanie na maszynie wirtualnej platformy Azure.  Wykonaj poniższe kroki, aby dowiedzieć się więcej na temat potoku ciągłego wdrażania usługi Azure DevOps Services.

1. Wybierz pozycję **Kompilacja i wydanie**, a następnie wybierz pozycję **Wydania**.  Projekt Azure DevOps utworzył potok wydania usługi Azure DevOps Services w celu zarządzania wdrożeniami na platformie Azure.

1. Po lewej stronie okna przeglądarki wybierz symbol **wielokropka** obok potoku wydania, a następnie wybierz pozycję **Edytuj**.

1. Potok wydania zawiera **potok**, który definiuje proces tworzenia wydania.  W obszarze **Artefakty** wybierz polecenie **Porzuć**.  Potok kompilacji przedstawiony w poprzednich krokach generuje dane wyjściowe używane na potrzeby artefaktu. 

1. Z prawej strony ikony **Porzuć** wybierz **ikonę** **wyzwalacza ciągłego wdrażania** w kształcie błyskawicy.  Ten potok wydania ma włączony wyzwalacz ciągłego wdrażania.  Wyzwalacz przeprowadza wdrożenie za każdym razem, gdy dostępny jest nowy artefakt kompilacji.  Opcjonalnie możesz wyłączyć wyzwalacz. Wtedy wdrożenia będą wymagać ręcznego wykonania. 

1. Po lewej stronie okna przeglądarki wybierz pozycję **Zadania**, a następnie wybierz **środowisko**.  

1. Zadania to czynności wykonywane w procesie wdrażania, pogrupowane w **Fazy**.  Dla tego potoku wydania istnieją dwie **fazy**.  Pierwsza faza zawiera zadanie **Wdrożenie grupy zasobów platformy Azure**, które konfiguruje maszynę wirtualną na potrzeby wdrażania i dodaje nową maszynę wirtualną do **grupy wdrożenia usługi Azure DevOps**.  Grupa wdrożenia maszyn wirtualnych w usłudze Azure DevOps zarządza grupami logicznymi maszyn **celu wdrożenia**.

1. W drugiej fazie zostało utworzone zadanie **Zarządzanie aplikacjami internetowymi usług IIS** w celu utworzenia witryny internetowej usług IIS na maszynie wirtualnej.  Drugie zadanie **Wdrażanie aplikacji internetowych usług IIS** zostało utworzone w celu wdrożenia witryny.

1. Po prawej stronie okna przeglądarki wybierz pozycję **Wyświetl wersje**.  Ten widok przedstawia historię wersji.

1. Wybierz symbol **wielokropka** obok jednej z wersji i wybierz polecenie **Otwórz**.  W tym widoku jest kilka menu, z którymi możesz się zapoznać, na przykład **podsumowanie wersji**, **skojarzone elementy robocze** i **Testy**.

1. Wybierz pozycję **Zatwierdzenia**.  Ten widok przedstawia zatwierdzenia kodu skojarzone z konkretnym wdrożeniem. Porównaj wydania, aby wyświetlić różnice w zatwierdzeniach pomiędzy wdrożeniami.

1. Wybierz pozycję **Dzienniki**.  Dzienniki zawierają przydatne informacje na temat procesu wdrażania.  Mogą być wyświetlane zarówno podczas wdrożeń, jak i po nich.

## <a name="commit-changes-to-azure-devops-services-and-automatically-deploy-to-azure"></a>Zatwierdzanie zmian w usłudze Azure DevOps Services i automatyczne wdrażanie na platformie Azure 

Możesz teraz rozpocząć pracę w zespole nad aplikacją w ramach procesu ciągłej integracji/ciągłego wdrażania, który umożliwia automatyczne wdrażanie najnowszej wersji w witrynie internetowej.  Każda zmiana w repozytorium Git usługi Azure DevOps Services rozpoczyna kompilację w usłudze Azure DevOps Services, a potok ciągłego wdrażania usługi Azure DevOps Services wykonuje wdrażanie na maszynie wirtualnej platformy Azure.  Wykonaj poniższe kroki lub użyj innej techniki, aby zatwierdzić zmiany w repozytorium.  Zmiany kodu inicjują proces ciągłej integracji/ciągłego wdrażania i automatyczne wdrożenie nowych zmian w witrynie internetowej usług IIS na maszynie wirtualnej platformy Azure.

1. Wybierz pozycję **Kod** z menu usługi Azure DevOps Services i przejdź do repozytorium.

1. Przejdź do katalogu **Views\Home**, a następnie wybierz symbol **wielokropka** obok pliku **Index.cshtml** i wybierz polecenie **Edytuj**.

1. Wprowadź zmianę w pliku, na przykład zmień tekst w jednym z **tagów div**.  W prawym górnym rogu wybierz polecenie **Zatwierdź**.  Wybierz ponownie polecenie **Zatwierdź**, aby wypchnąć zmianę. 

1. Po chwili **kompilacja zostanie uruchomiona w usłudze Azure DevOps Services**, a następnie zostanie wykonane wydanie w celu wdrożenia zmian.  Monitoruj **stan kompilacji** na pulpicie nawigacyjnym projektu usługi DevOps lub w przeglądarce przy użyciu organizacji usługi Azure DevOps Services.

1. Po zakończeniu wydania **odśwież aplikację** w przeglądarce, aby sprawdzić, czy zmiany są wyświetlane.

## <a name="configure-azure-application-insights-monitoring"></a>Konfigurowanie monitorowania usługi Azure Application Insights

Usługa Azure Application Insights umożliwia łatwe monitorowanie wydajności i użycia aplikacji.  Projekt usługi Azure DevOps automatycznie skonfigurował zasób usługi Application Insights dla aplikacji.  Możesz dodatkowo skonfigurować różne alerty i możliwości monitorowania stosownie do potrzeb.

1. Przejdź do pulpitu nawigacyjnego **projektu DevOps platformy Azure** w **witrynie Azure Portal**.  W prawym dolnym rogu pulpitu nawigacyjnego wybierz link **Application Insights** dla aplikacji.

1. Zostanie otwarty blok usługi **Application Insights** w witrynie Azure Portal.  Ten widok zawiera informacje o monitorowaniu użycia, wydajności i dostępności aplikacji.

    ![Application Insights](_img/azure-devops-project-github/appinsights.png) 

1. Wybierz pozycję **Zakres czasu**, a następnie wybierz wartość **Ostatnia godzina**.  Wybierz pozycję **Aktualizuj**, aby filtrować wyniki.  Zobaczysz teraz wszystkie działania z ostatnich 60 minut.  Wybierz **x** aby zakończyć działanie zakresu czasu.

1. **Alerty** i kilka innych przydatnych linków możesz znaleźć w górnej części pulpitu nawigacyjnego.  Wybierz pozycję **Alerty**, a następnie wybierz pozycję **+ Dodaj alert dotyczący metryki**.

1. Wprowadź **nazwę** alertu.

1. Domyślny alert dotyczy **czasu odpowiedzi serwera dłuższego niż 1 sekunda**.  Wybierz listę rozwijaną **Metryka**, aby zbadać różne metryki alertu.  Na przykład możesz skonfigurować **czas wykonania żądania ASP.NET** dla aplikacji platformy ASP.NET.  Możesz łatwo skonfigurować różne alerty, aby zwiększyć możliwości monitorowania aplikacji.

1. Zaznacz pole wyboru w celu **powiadamiania za pośrednictwem poczty e-mail właścicieli, współautorów i czytelników**.  Opcjonalnie możesz wykonać dodatkowe akcje po wyzwoleniu alertu, wykonując aplikację logiki platformy Azure.

1. Wybierz pozycję **OK**, aby utworzyć alert.  Za chwilę alert pojawi się jako aktywny na pulpicie nawigacyjnym.  **Zakończ** działanie obszaru Alerty i przejdź z powrotem do **bloku usługi Application Insights**.

1. Wybierz pozycję **Dostępność**, a następnie wybierz pozycję **+ Dodaj test**. 

1. Wprowadź **nazwę testu**, następnie wybierz pozycję **Utwórz**.  Zostanie utworzony prosty test ping w celu sprawdzania dostępności aplikacji.  Po kilku minutach wyniki testów będą dostępne, a na pulpicie nawigacyjnym usługi Application Insights zostanie wyświetlony stan dostępności.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

 > [!NOTE]
 > Wykonanie poniższych czynności spowoduje trwałe usunięcie zasobów.  Tej funkcji można używać tylko po uważnym zapoznaniu się z monitami.

Jeśli jesteś w trakcie testów, możesz oczyścić zasoby, aby uniknąć naliczania opłat.  Jeśli maszyna wirtualna platformy Azure nie jest już potrzebna, możesz ją usunąć wraz z powiązanymi zasobami utworzonymi w tym samouczku, korzystając z funkcji **Usuń** na pulpicie nawigacyjnym projektu DevOps platformy Azure.  **Należy zachować ostrożność**, ponieważ funkcja usuwania niszczy dane utworzone w projekcie usługi Azure DevOps zarówno na platformie Azure, jak i w usłudze Azure DevOps, i nie ma możliwości ich odzyskania.

1. W witrynie**Azure Portal** przejdź do **projektu DevOps platformy Azure**.
2. W **prawym górnym** rogu pulpitu nawigacyjnego wybierz opcję **Usuń**.  Po przeczytaniu monitu wybierz pozycję **Tak**, aby **trwale usunąć** zasoby.

## <a name="next-steps"></a>Następne kroki

Opcjonalnie możesz zmodyfikować potoki kompilacji i wydania, aby zaspokoić potrzeby swojego zespołu. Możesz także użyć wzorca ciągłej integracji/ciągłego wdrażania jako szablonu podczas pracy z innymi projektami.  W tym samouczku omówiono:

> [!div class="checklist"]
> * Tworzenie projektu usługi Azure DevOps dla aplikacji platformy ASP.NET
> * Konfigurowanie usługi Azure DevOps Services i subskrypcji platformy Azure 
> * Badanie potoku ciągłej integracji usługi Azure DevOps Services
> * Badanie potoku ciągłego wdrażania usługi Azure DevOps Services
> * Zatwierdzanie zmian w usłudze Azure DevOps Services i automatyczne wdrażanie na platformie Azure
> * Konfigurowanie monitorowania usługi Azure Application Insights
> * Oczyszczanie zasobów

Aby dowiedzieć się więcej na temat potoku ciągłej integracji/ciągłego wdrażania platformy Azure, zapoznaj się z tym samouczkiem:

> [!div class="nextstepaction"]
> [Dostosowywanie procesu ciągłego wdrażania](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

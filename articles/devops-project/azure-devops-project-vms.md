---
title: 'Samouczek: Wdrażanie aplikacji ASP.NET na maszynach wirtualnych platformy Azure przy użyciu usługi Azure DevOps Projects'
description: Usługa DevOps Projects ułatwia rozpoczęcie pracy na platformie Azure oraz wdrażanie aplikacji ASP.NET na maszynach wirtualnych platformy Azure w kilku prostych krokach.
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 05643f342d51d99645d3c9204d6e63adcf2a0a73
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60546476"
---
# <a name="tutorial-deploy-your-aspnet-app-to-azure-virtual-machines-by-using-azure-devops-projects"></a>Samouczek: Wdrażanie aplikacji ASP.NET na maszynach wirtualnych platformy Azure przy użyciu usługi Azure DevOps Projects

Usługa Azure DevOps Projects stanowi uproszczone środowisko, do którego możesz przenieść istniejący kod i repozytorium Git lub wybrać aplikację przykładową, aby utworzyć potok ciągłej integracji i ciągłego dostarczania na platformie Azure. 

Usługa DevOps Projects wykonuje również następujące działania:
* Automatycznie tworzy zasoby platformy Azure, takie jak nowe maszyny wirtualne platformy Azure.
* Tworzy i konfiguruje potok wydania w usłudze Azure DevOps, który obejmuje potok kompilacji na potrzeby ciągłej integracji.
* Konfiguruje potok wydania na potrzeby ciągłego wdrażania. 
* Tworzy zasób usługi Azure Application Insights na potrzeby monitorowania.

W tym samouczku zostaną wykonane następujące czynności:

> [!div class="checklist"]
> * Wdrażanie aplikacji ASP.NET za pomocą usługi DevOps Projects
> * Konfigurowanie usługi Azure DevOps i subskrypcji platformy Azure 
> * Badanie potoku ciągłej integracji
> * Badanie potoku ciągłego wdrażania
> * Zatwierdzanie zmian w usłudze Azure Repos i automatyczne wdrażanie ich na platformie Azure
> * Konfigurowanie monitorowania usługi Azure Application Insights
> * Oczyszczanie zasobów

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Możesz uzyskać ją bezpłatnie za pośrednictwem programu [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="use-devops-projects-to-deploy-your-aspnet-app"></a>Wdrażanie aplikacji ASP.NET za pomocą usługi DevOps Projects

Usługa DevOps Projects tworzy potok ciągłej integracji/ciągłego wdrażania w usłudze Azure Pipelines. Możesz utworzyć nową organizację usługi Azure DevOps lub użyć istniejącej organizacji. Usługa DevOps Projects tworzy również zasoby platformy Azure, na przykład maszyny wirtualne, w wybranej subskrypcji platformy Azure.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. W lewym okienku wybierz pozycję **Nowy**.

1. W polu wyszukiwania wpisz ciąg **DevOps Projects**, a następnie wybierz pozycję **Utwórz**.

    ![Pulpit nawigacyjny usługi DevOps Projects](_img/azure-devops-project-github/fullbrowser.png)

1. Wybierz pozycję **.NET**, a następnie wybierz pozycję **Dalej**.

1. W obszarze **Wybór struktury aplikacji** wybierz pozycję **ASP.NET**, a następnie wybierz przycisk **Dalej**.  
    Wybrana w poprzednim kroku struktura aplikacji decyduje o dostępnym w tym miejscu typie celu wdrożenia usługi platformy Azure. 

1. Wybierz maszynę wirtualną, a następnie wybierz przycisk **Dalej**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurowanie usługi Azure DevOps i subskrypcji platformy Azure

1. Utwórz nową organizację usługi Azure DevOps lub wybierz istniejącą organizację. 

1. Wprowadź nazwę projektu usługi Azure DevOps. 

1. Wybierz usługi subskrypcji platformy Azure.  
    Opcjonalnie możesz wybrać pozycję **Zmień**, a następnie wprowadzić więcej szczegółów konfiguracji, takich jak lokalizacja zasobów platformy Azure.
 
1. Wprowadź nazwę maszyny wirtualnej, nazwę użytkownika i hasło dla nowego zasobu maszyny wirtualnej platformy Azure, a następnie wybierz przycisk **Gotowe**.  
    Po kilku minutach maszyna wirtualna platformy Azure będzie gotowa. Przykładowa aplikacja ASP.NET zostanie skonfigurowana w repozytorium w organizacji usługi Azure DevOps, zostaną wykonane kompilacja i wydanie, a aplikacja zostanie wdrożona na nowo utworzonej maszynie wirtualnej platformy Azure. 

    Po zakończeniu tych operacji zostanie wyświetlony pulpit nawigacyjny usługi DevOps Projects w witrynie Azure Portal. Możesz również przejść do pulpitu nawigacyjnego bezpośrednio z obszaru **Wszystkie zasoby** w witrynie Azure Portal. 

    Pulpit nawigacyjny zapewnia wgląd w repozytorium kodu usługi Azure DevOps, potok ciągłej integracji/ciągłego wdrażania i działającą aplikację na platformie Azure.   

    ![Widok pulpitu nawigacyjnego](_img/azure-devops-project-vms/dashboardnopreview.png)

Usługa DevOps Projects automatycznie konfiguruje wyzwalacz kompilacji i wydania ciągłej integracji, który wdraża wszelkie zmiany kodu w repozytorium. Dalsze dodatkowe opcje można skonfigurować w usłudze Azure DevOps. Aby wyświetlić uruchomioną aplikację, wybierz pozycję **Przeglądaj**.
    
## <a name="examine-the-ci-pipeline"></a>Badanie potoku ciągłej integracji
 
Usługa DevOps Projects automatycznie skonfigurowała potok ciągłej integracji/ciągłego wdrażania w usłudze Azure Pipelines. Możesz przeglądać i dostosowywać potok. Aby zapoznać się z potokiem kompilacji, wykonaj następujące czynności:

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
    W tym okienku jest wyświetlany dziennik inspekcji ostatnio wprowadzonych zmian w kompilacji. Usługa Azure DevOps śledzi wszelkie zmiany wprowadzone do potoku kompilacji i pozwala na porównanie wersji.

1. Wybierz pozycję **Wyzwalacze**.  
    Usługa DevOps Projects automatycznie tworzy wyzwalacz ciągłej integracji — każde zatwierdzenie w repozytorium uruchamia nową kompilację. Opcjonalnie możesz zdecydować się dołączyć gałęzie do procesu ciągłej integracji lub wykluczyć je z niego.

1. Wybierz pozycję **Przechowywanie**.  
    W zależności od scenariusza możesz określić zasady przechowywania lub usuwania pewnej liczby kompilacji.

## <a name="examine-the-cd-pipeline"></a>Badanie potoku ciągłego wdrażania

Usługa DevOps Projects automatycznie tworzy i konfiguruje kroki wymagane do wdrożenia z organizacji usługi Azure DevOps w ramach subskrypcji platformy Azure. Te kroki obejmują konfigurowanie połączenia usługi platformy Azure w celu uwierzytelniania usługi Azure DevOps w subskrypcji platformy Azure. Automatyzacja tworzy również potok ciągłego wdrażania, a on zapewnia ciągłe wdrażanie na maszynie wirtualnej platformy Azure. Aby dowiedzieć się więcej o potoku ciągłego wdrażania usługi Azure DevOps, wykonaj następujące czynności:

1. Wybierz pozycję **Kompilacja i wydanie**, a następnie wybierz pozycję **Wydania**.  
    Usługa DevOps Projects tworzy potok wydania w celu zarządzania wdrożeniami na platformie Azure.

1. Wybierz symbol wielokropka (...) obok potoku wydania, a następnie wybierz pozycję **Edytuj**.  
    Potok wydania zawiera *potok*, który definiuje proces tworzenia wydania.

1. W obszarze **Artefakty** wybierz polecenie **Porzuć**.  
    Potok kompilacji przedstawiony w poprzednich krokach generuje dane wyjściowe używane na potrzeby artefaktu. 

1. Obok ikony **Porzuć** wybierz pozycję **Wyzwalacz ciągłego wdrażania**.  
    Ten potok wydania ma włączony wyzwalacz ciągłego wdrażania, który przeprowadza wdrożenie za każdym razem, gdy jest dostępny nowy artefakt kompilacji. Opcjonalnie możesz wyłączyć wyzwalacz. Wtedy wdrożenia będą wymagać ręcznego wykonania. 

1. Po lewej stronie wybierz pozycję **Zadania**, a następnie wybierz środowisko.  
    Zadania to działania wykonywane w procesie wdrażania. Są one pogrupowane w fazy. Ten potok wydania ma dwie fazy:
    * Pierwsza faza obejmuje zadanie wdrożenia grupy zasobów platformy Azure, które wykonuje następujące działania:
      * Konfiguruje maszynę wirtualną pod kątem wdrożenia
      * Dodaje nową maszynę wirtualną do grupy wdrożenia usługi Azure DevOps. Grupa wdrożenia maszyn wirtualnych w usłudze Azure DevOps zarządza grupami logicznymi maszyn celu wdrożenia.
    * W drugiej fazie zadanie zarządzania aplikacjami internetowymi usług IIS tworzy witrynę internetową usług IIS na maszynie wirtualnej. Drugie zadanie wdrażania aplikacji internetowych usług IIS jest tworzone w celu wdrożenia witryny.

1. Po prawej stronie wybierz pozycję **Wyświetl wydania**, aby wyświetlić historię wydań.

1. Wybierz symbol wielokropka (...) obok wydania, a następnie wybierz pozycję **Otwórz**.  
    Dostępnych jest kilka menu, z którymi możesz się zapoznać, na przykład podsumowanie wydań, skojarzone elementy robocze i testy.

1. Wybierz pozycję **Zatwierdzenia**.  
    Ten widok przedstawia zatwierdzenia kodu skojarzone z danym wdrożeniem. Porównaj wydania, aby wyświetlić różnice w zatwierdzeniach pomiędzy wdrożeniami.

1. Wybierz pozycję **Dzienniki**.  
    Dzienniki zawierają przydatne informacje na temat procesu wdrażania. Możesz je wyświetlać zarówno podczas wdrażania, jak i po jego zakończeniu.

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>Zatwierdzanie zmian w usłudze Azure Repos i automatyczne wdrażanie ich na platformie Azure 

Teraz możesz rozpocząć współpracę z zespołem nad aplikacją w języku Python w ramach procesu ciągłej integracji/ciągłego wdrażania, który automatycznie wdraża najnowszy kod w witrynie internetowej. Każda zmiana w repozytorium Git rozpoczyna kompilację w usłudze Azure DevOps, a potok ciągłego wdrażania wykonuje wdrażanie na platformie Azure. Wykonaj procedurę opisaną w tej sekcji lub użyj innej techniki, aby zatwierdzić zmiany w repozytorium. Zmiany kodu inicjują proces ciągłej integracji/ciągłego wdrażania i automatyczne wdrożenie zmian w witrynie internetowej usług IIS na maszynie wirtualnej platformy Azure.

1. W lewym okienku wybierz pozycję **Kod**, a następnie przejdź do repozytorium.

1. Przejdź do katalogu *Views\Home*, wybierz symbol wielokropka (...) obok pliku *Index.cshtml* i wybierz pozycję **Edytuj**.

1. Wprowadź zmianę w pliku, na przykład dodaj tekst w jednym z tagów div. 

1. W prawym górnym rogu wybierz pozycję **Zatwierdź**, a następnie ponownie wybierz pozycję **Zatwierdź**, aby wypchnąć zmianę.  
    Po chwili zostanie uruchomiona kompilacja w usłudze Azure DevOps, a następnie zostanie wykonane wydanie w celu wdrożenia zmian. Monitoruj stan kompilacji na pulpicie nawigacyjnym usługi DevOps Projects lub w przeglądarce przy użyciu organizacji usługi Azure DevOps.

1. Po zakończeniu tworzenia wydania odśwież aplikację, aby zweryfikować zmiany.

## <a name="configure-azure-application-insights-monitoring"></a>Konfigurowanie monitorowania usługi Azure Application Insights

Usługa Azure Application Insights umożliwia łatwe monitorowanie wydajności i użycia aplikacji. Usługa DevOps Projects automatycznie konfiguruje zasób usługi Application Insights dla aplikacji. Możesz dodatkowo skonfigurować różne alerty i możliwości monitorowania stosownie do potrzeb.

1. W witrynie Azure Portal przejdź do pulpitu nawigacyjnego usługi DevOps Projects. 

1. W prawym dolnym rogu wybierz link **Application Insights** dla Twojej aplikacji.  
    Zostanie otwarte okienko **Application Insights**. Ten widok zawiera informacje o monitorowaniu użycia, wydajności i dostępności aplikacji.

    ![Okienko Application Insights](_img/azure-devops-project-github/appinsights.png) 

1. Wybierz pozycję **Zakres czasu**, a następnie wybierz wartość **Ostatnia godzina**. Aby filtrować wyniki, wybierz pozycję **Aktualizuj**.  
    Teraz możesz przejrzeć wszystkie działania z ostatnich 60 minut. 
    
1. Aby zamknąć zakres czasu, wybierz pozycję **x**

1. Wybierz pozycję **Alerty**, a następnie wybierz pozycję **Dodaj alert metryki**. 

1. Wprowadź nazwę alertu.

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

Jeśli przeprowadzasz testowanie, możesz uniknąć naliczania opłat, oczyszczając zasoby. Gdy maszyna wirtualna platformy Azure i powiązane zasoby utworzone w tym samouczku nie będą już potrzebne, możesz je usunąć. W tym celu użyj funkcji **Usuń** na pulpicie nawigacyjnym projektu usługi DevOps. 

> [!IMPORTANT]
> Poniższa procedura powoduje trwałe usunięcie zasobów. Funkcja *Usuń* niszczy dane utworzone przez projekt w usłudze DevOps Projects zarówno na platformie Azure, jak i w usłudze Azure DevOps. Nie będzie można ich odzyskać. Użyj tej procedury dopiero po uważnym przeczytaniu monitów.

1. W witrynie Azure Portal przejdź do pulpitu nawigacyjnego usługi DevOps Projects.
1. W prawym górnym rogu wybierz pozycję **Usuń**. 
1. Po wyświetleniu monitu wybierz pozycję **Tak**, aby *trwale usunąć* zasoby.

Opcjonalnie możesz zmodyfikować potoki kompilacji i wydania, aby zaspokoić potrzeby swojego zespołu. Możesz także użyć wzorca ciągłej integracji/ciągłego wdrażania jako szablonu podczas pracy z innymi potokami. 

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wdrażanie aplikacji ASP.NET za pomocą usługi DevOps Projects
> * Konfigurowanie usługi Azure DevOps i subskrypcji platformy Azure 
> * Badanie potoku ciągłej integracji
> * Badanie potoku ciągłego wdrażania
> * Zatwierdzanie zmian w usłudze Azure Repos i automatyczne wdrażanie ich na platformie Azure
> * Konfigurowanie monitorowania usługi Azure Application Insights
> * Oczyszczanie zasobów

Aby dowiedzieć się więcej na temat potoku ciągłej integracji/ciągłego wdrażania, zobacz:

> [!div class="nextstepaction"]
> [Definiowanie wieloetapowego potoku ciągłego wdrażania](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

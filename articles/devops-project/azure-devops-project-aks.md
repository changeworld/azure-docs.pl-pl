---
title: Wdrażanie aplikacji platformy ASP.NET Core w usłudze Azure Kubernetes Service (AKS) za pomocą projektu usługi Azure DevOps | Samouczek usługi Azure DevOps
description: Projekt usługi Azure DevOps ułatwia rozpoczęcie pracy na platformie Azure. Projekt usługi Azure DevOps ułatwia wdrażanie aplikacji ASP.NET Core za pomocą usługi Azure Kubernetes Service (AKS) w kilku prostych krokach.
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 55ea101b3a03fdb7fc375c4594cab36d4cd79978
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2018
ms.locfileid: "44299121"
---
# <a name="tutorial--deploy-your-aspnet-core-app-to-azure-kubernetes-service-aks-with-the-azure-devops-project"></a>Samouczek: wdrażanie aplikacji platformy ASP.NET Core w usłudze Azure Kubernetes Service (AKS) za pomocą projektu DevOps platformy Azure

Projekt DevOps platformy Azure stanowi uproszczone środowisko, do którego możesz przenieść istniejący kod i repozytorium Git lub dokonać wyboru jednej z przykładowych aplikacji, aby utworzyć potok ciągłej integracji i ciągłego dostarczania na platformie Azure.  Projekt DevOps automatycznie tworzy zasoby platformy Azure, takie jak usługa AKS, tworzy i konfiguruje potok wydania w usłudze Azure DevOps Services zawierający potok kompilacji i wydania dla ciągłej integracji/ciągłego wdrażania, a następnie tworzy zasób usługi Azure Application Insights na potrzeby monitorowania.

Wykonasz następujące zadania:

> [!div class="checklist"]
> * Tworzenie projektu usługi Azure DevOps dla aplikacji platformy ASP.NET Core i usługi AKS
> * Konfigurowanie usługi Azure DevOps Services i subskrypcji platformy Azure 
> * Sprawdzanie klastra AKS
> * Badanie potoku ciągłej integracji usługi Azure DevOps Services
> * Badanie potoku ciągłego wdrażania usługi Azure DevOps Services
> * Zatwierdzanie zmian w usłudze Git i automatyczne wdrażanie na platformie Azure
> * Oczyszczanie zasobów

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Możesz uzyskać ją bezpłatnie za pośrednictwem programu [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="create-an-azure-devops-project-for-an-aspnet-core-app-and-aks"></a>Tworzenie projektu usługi Azure DevOps dla aplikacji platformy ASP.NET Core i usługi AKS

Projekt usługi Azure DevOps tworzy potok ciągłej integracji/ciągłego wdrażania na platformie Azure.  Możesz utworzyć **nową organizację usługi Azure DevOps Services** lub użyć **istniejącej organizacji**.  Projekt DevOps platformy Azure tworzy również **zasoby platformy Azure**, na przykład klaster AKS, w wybranej przez użytkownika **subskrypcji platformy Azure**.

1. Zaloguj się do witryny [Microsoft Azure Portal](https://portal.azure.com).

1. Wybierz ikonę **Utwórz zasób** na pasku nawigacyjnym po lewej stronie, a następnie wyszukaj pozycję **Projekt DevOps**.  Wybierz pozycję **Utwórz**.

    ![Rozpoczynanie ciągłego dostarczania](_img/azure-devops-project-aks/fullbrowser.png)

1. Wybierz pozycję **.NET**, a następnie wybierz pozycję **Dalej**.

1. Na stronie **Wybierz strukturę aplikacji** wybierz opcję **ASP.NET Core**, a następnie wybierz pozycję **Dalej**.

1. Wybierz pozycję **Kubernetes Service**, a następnie wybierz pozycję **Dalej**.  

## <a name="configure-azure-devops-services-and-an-azure-subscription"></a>Konfigurowanie usługi Azure DevOps Services i subskrypcji platformy Azure

1. Utwórz **nową** organizację usługi Azure DevOps lub wybierz **istniejącą** organizację.  Wybierz **nazwę** projektu.  

1. Wybierz swoją **subskrypcję** platformy Azure.

1. Wybierz link **Zmień**link, aby wyświetlić dodatkowe ustawienia konfiguracji platformy Azure, a następnie zidentyfikuj **liczbę węzłów** dla **klastra Kubernetes**.  W tym miejscu dostępne są różne opcje konfigurowania typu i lokalizacji usług platformy Azure.
 
1. Opuść obszar konfiguracji platformy Azure i wybierz pozycję **Gotowe**.

1. Zakończenie procesu potrwa kilka minut.  Przykładowa aplikacja ASP.NET Core zostanie skonfigurowana w repozytorium Git usługi Azure Repos w organizacji usługi Azure DevOps Services, zostanie utworzony klaster usługi AKS, a następnie zostanie wykonany potok ciągłej integracji/ciągłego wdrażania, a aplikacja zostanie wdrożona na platformie Azure.  

    Po zakończeniu **pulpit nawigacyjny projektu** usługi Azure DevOps zostanie załadowany w witrynie Azure Portal.  Możesz również przejść do **pulpitu nawigacyjnego projektu DevOps platformy Azure** bezpośrednio z obszaru **Wszystkie zasoby** w witrynie **Azure Portal**.  

    Ten pulpit nawigacyjny zapewnia wgląd do **repozytorium kodu** usługi Azure Repos, **potoku ciągłej integracji/ciągłego wdrażania usług Azure DevOps Services** i **klastra usługi AKS**.  Dalsze dodatkowe opcje ciągłej integracji/ciągłego wdrażania można skonfigurować w potoku usługi Azure DevOps Services.  Po prawej stronie pulpitu nawigacyjnego wybierz pozycję **Przeglądaj**, aby wyświetlić uruchomioną aplikację.

## <a name="examine-the-aks-cluster"></a>Sprawdzanie klastra AKS

Projekt DevOps platformy Azure automatycznie konfiguruje klaster AKS.  Możesz przeglądać i dostosowywać klaster.  Wykonaj poniższe kroki, aby zapoznać się z usługą AKS.

1. Przejdź do **pulpitu nawigacyjnego projektu DevOps platformy Azure**.

1. Po prawej stronie pulpitu nawigacyjnego projektu DevOps wybierz **usługę Kubernetes**.

1. Zostanie otwarty blok klastra AKS.  W tym widoku można wykonywać różne akcje, takie jak **monitorowanie kondycji kontenera** i **przeszukiwanie dzienników**, a także otworzyć **pulpit nawigacyjny rozwiązania Kubernetes**.

1. Po prawej stronie ekranu wybierz pozycję **Wyświetl pulpit nawigacyjny platformy Kubernetes**.  Opcjonalnie postępuj zgodnie z instrukcjami, aby otworzyć pulpit nawigacyjny platformy Kubernetes.

## <a name="examine-the-azure-devops-services-ci-pipeline"></a>Badanie potoku ciągłej integracji usługi Azure DevOps Services

Projekt usługi Azure DevOps automatycznie konfiguruje potok ciągłej integracji/ciągłego wdrażania na platformie Azure w organizacji usługi Azure DevOps.  Możesz przeglądać i dostosowywać potok.  Wykonaj poniższe kroki, aby zapoznać się z potokiem ciągłej integracji/ciągłego wdrażania usługi Azure DevOps Services.

1. Przejdź do **pulpitu nawigacyjnego projektu DevOps platformy Azure**.

1. Wybierz pozycję **Potoki kompilacji** w **górnej części** **pulpitu nawigacyjnego projektu usługi Azure DevOps**.  Ten link powoduje otwarcie karty przeglądarki oraz otwarcie potoku kompilacji usługi Azure DevOps Services dla nowego projektu.

1. Przesuń wskaźnik myszy na prawo od potoku kompilacji, obok pola **Stan**. Wybierz symbol **wielokropka**, który się pojawi.  Ta akcja spowoduje otwarcie menu, w którym możesz wykonać kilka działań, takich jak **dodawanie nowej kompilacji do kolejki**, **wstrzymywanie kompilacji** i **edytowanie potoku kompilacji**.

1. Wybierz pozycję **Edit** (Edytuj).

1. Z poziomu tego widoku **zapoznaj się z różnymi zadaniami**.  W ramach kompilacji są wykonywane różne zadania, takie jak pobieranie źródeł z repozytorium Git usługi Repos w usłudze Azure DevOps Services, przywracanie zależności i publikowanie danych wyjściowych używanych do wdrożenia.

1. W górnej części potoku kompilacji wybierz **nazwę potoku kompilacji**.

1. Zmień **nazwę** potoku kompilacji na bardziej opisową.  Wybierz pozycję **Zapisz i dodaj do kolejki**, a następnie wybierz pozycję **Zapisz**.

1. W obszarze nazwy potoku kompilacji wybierz pozycję **Historia**.  Zostanie wyświetlony dziennik inspekcji ostatnio wprowadzonych zmian w kompilacji.  Usługa Azure DevOps Services śledzi wszelkie zmiany wprowadzone w potoku kompilacji i pozwala na porównywanie wersji.

1. Wybierz pozycję **Wyzwalacze**.  Projekt usługi Azure DevOps automatycznie utworzył wyzwalacz ciągłej integracji. Każde zatwierdzenie w repozytorium uruchamia nową kompilację.  Opcjonalnie możesz zdecydować się dołączyć gałęzie do procesu ciągłej integracji lub je wykluczyć.

1. Wybierz pozycję **Przechowywanie**.  W zależności od scenariusza możesz określić zasady przechowywania lub usuwania pewnej liczby kompilacji.

## <a name="examine-the-azure-devops-services-cd-release-pipeline"></a>Badanie potoku wydania ciągłego wdrażania usługi Azure DevOps Services

Projekt usługi Azure DevOps automatycznie tworzy i konfiguruje wymagane kroki wdrożenia z organizacji usługi Azure DevOps Services do subskrypcji platformy Azure.  Te kroki obejmują konfigurowanie połączenia usługi platformy Azure w celu uwierzytelniania usługi Azure DevOps Services w subskrypcji platformy Azure.  Automatyzacja tworzy również potok wydania usługi Azure DevOps Services, a ten potok wydania zapewnia ciągłe wdrażanie na platformie Azure.  Wykonaj poniższe kroki, aby dowiedzieć się więcej na temat potoku wydania usługi Azure DevOps Services.

1. Wybierz pozycję **Kompilacja i wydanie**, a następnie wybierz pozycję **Wydania**.  Projekt Azure DevOps utworzył potok wydania usługi Azure DevOps Services w celu zarządzania wdrożeniami na platformie Azure.

1. Po lewej stronie okna przeglądarki wybierz symbol **wielokropka** obok potoku wydania, a następnie wybierz pozycję **Edytuj**.

1. Potok wydania zawiera **potok**, który definiuje proces tworzenia wydania.  W obszarze **Artefakty** wybierz polecenie **Porzuć**.  Potok kompilacji przedstawiony w poprzednich krokach generuje dane wyjściowe używane na potrzeby artefaktu. 

1. Z prawej strony ikony **Porzuć** wybierz **ikonę** **wyzwalacza ciągłego wdrażania** w kształcie błyskawicy.  Ten potok wydania ma włączony wyzwalacz ciągłego wdrażania.  Wyzwalacz tworzy wdrożenie za każdym razem, gdy dostępny jest nowy artefakt kompilacji.  Opcjonalnie możesz wyłączyć wyzwalacz. Wtedy wdrożenia będą wymagać ręcznego wykonania. 

1. Po prawej stronie okna przeglądarki wybierz pozycję **Wyświetl wersje**.  Ten widok przedstawia historię wersji.

1. Wybierz symbol **wielokropka** obok jednej z wersji i wybierz polecenie **Otwórz**.  W tym widoku jest kilka menu, z którymi możesz się zapoznać, na przykład **podsumowanie wersji**, **skojarzone elementy robocze** i **Testy**.

1. Wybierz pozycję **Zatwierdzenia**.  Ten widok przedstawia zatwierdzenia kodu skojarzone z konkretnym wdrożeniem. Możesz porównać wydania, aby wyświetlić różnice w zatwierdzeniach pomiędzy wdrożeniami.

1. Wybierz pozycję **Dzienniki**.  Dzienniki zawierają przydatne informacje na temat procesu wdrażania.  Mogą być wyświetlane zarówno podczas wdrożeń, jak i po nich.

## <a name="commit-changes-to-azure-devops-services-and-automatically-deploy-to-azure"></a>Zatwierdzanie zmian w usłudze Azure DevOps Services i automatyczne wdrażanie na platformie Azure 

 > [!NOTE]
 > W poniższych krokach potok ciągłej integracji/ciągłego wdrażania zostanie sprawdzony za pomocą prostej zmiany tekstu w aplikacji internetowej.

Możesz teraz rozpocząć pracę w zespole nad aplikacją w ramach procesu ciągłej integracji/ciągłego wdrażania, który umożliwia automatyczne wdrażanie najnowszej wersji w witrynie internetowej.  Każda zmiana w repozytorium Git usługi Azure DevOps Services rozpoczyna kompilację w usłudze Azure DevOps Services, a potok ciągłego wdrażania wdraża zmiany na platformie Azure.  Wykonaj poniższe kroki lub użyj innej techniki, aby zatwierdzić zmiany w repozytorium.  Możesz na przykład **sklonować** repozytorium Git za pomocą wybranego narzędzia lub zintegrowanego środowiska projektowego, a następnie wypchnąć zmiany do tego repozytorium.

1. Wybierz pozycję **Kod**, a następnie pozycję **Pliki** z menu usługi Azure DevOps Services, i przejdź do repozytorium.
1. Przejdź do katalogu **Views\Home**, a następnie wybierz symbol **wielokropka** obok pliku **Index.cshtml** i wybierz polecenie **Edytuj**.

1. Wprowadź zmianę w pliku, na przykład zmień tekst w jednym z **tagów div**.  W prawym górnym rogu wybierz polecenie **Zatwierdź**.  Wybierz ponownie polecenie **Zatwierdź**, aby wypchnąć zmianę. 

1. Po chwili **kompilacja zostanie uruchomiona w usłudze Azure DevOps Services**, a następnie zostanie wykonane wydanie w celu wdrożenia zmian.  **Stan kompilacji** możesz monitorować na pulpicie nawigacyjnym projektu usługi DevOps lub w przeglądarce przy użyciu organizacji usługi Azure DevOps Services.

1. Po zakończeniu wydania **odśwież aplikację** w przeglądarce, aby sprawdzić, czy zmiany są wyświetlane.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

 > [!NOTE]
 > Wykonanie poniższych czynności spowoduje trwałe usunięcie zasobów.  Tej funkcji można używać tylko po uważnym zapoznaniu się z monitami.

Jeśli jesteś w trakcie testów, możesz oczyścić zasoby, aby uniknąć naliczania opłat.  Jeśli klaster Azure Kubernetes nie jest już potrzebny, możesz go usunąć wraz z powiązanymi zasobami utworzonymi w tym samouczku, korzystając z funkcji **Usuń** na pulpicie nawigacyjnym projektu DevOps platformy Azure.  **Należy zachować ostrożność**, ponieważ funkcja usuwania niszczy dane utworzone w projekcie usługi Azure DevOps zarówno na platformie Azure, jak i w usłudze Azure DevOps Services, i nie ma możliwości ich odzyskania.

1. W witrynie**Azure Portal** przejdź do **projektu DevOps platformy Azure**.
2. W **prawym górnym** rogu pulpitu nawigacyjnego wybierz opcję **Usuń**.  Po przeczytaniu monitu wybierz pozycję **Tak**, aby **trwale usunąć** zasoby.

## <a name="next-steps"></a>Następne kroki

Opcjonalnie możesz zmodyfikować potoki kompilacji i wydania, aby zaspokoić potrzeby swojego zespołu. Możesz także użyć wzorca ciągłej integracji/ciągłego wdrażania jako szablonu podczas pracy z innymi potokami.  W tym samouczku omówiono:

> [!div class="checklist"]
> * Tworzenie projektu usługi Azure DevOps dla aplikacji platformy ASP.NET Core i usługi AKS
> * Konfigurowanie usługi Azure DevOps Services i subskrypcji platformy Azure 
> * Sprawdzanie klastra AKS
> * Badanie potoku ciągłej integracji usługi Azure DevOps Services
> * Badanie potoku ciągłego wdrażania usługi Azure DevOps Services
> * Zatwierdzanie zmian w usłudze Git i automatyczne wdrażanie na platformie Azure
> * Oczyszczanie zasobów

Aby dowiedzieć się więcej na temat korzystania z pulpitu nawigacyjnego rozwiązania Kubernetes, zobacz materiały poniżej:

> [!div class="nextstepaction"]
> [Korzystanie z pulpitu nawigacyjnego rozwiązania Kubernetes](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

---
title: Wdrażanie aplikacji ASP.NET Core w usłudze Azure Service Fabric za pomocą projektu DevOps platformy Azure | Samouczek usługi VSTS
description: Projekt DevOps ułatwia rozpoczęcie pracy na platformie Azure. Projekt DevOps platformy Azure umożliwia łatwe wdrożenie aplikacji ASP.NET Core oraz usługi Azure Service Fabric w kilku szybkich krokach.
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 2fd1fc968eb6b61d7378dbc0efa48f2f7eb2aa54
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2018
ms.locfileid: "37967361"
---
# <a name="tutorial--deploy-your-aspnet-core-app-to-azure-service-fabric-with-the-azure-devops-project"></a>Samouczek: wdrażanie aplikacji ASP.NET Core w usłudze Azure Service Fabric za pomocą projektu DevOps platformy Azure

Projekt DevOps platformy Azure stanowi uproszczone środowisko, do którego możesz przenieść istniejący kod i repozytorium Git lub dokonać wyboru jednej z przykładowych aplikacji, aby utworzyć potok ciągłej integracji i ciągłego dostarczania na platformie Azure.  Projekt DevOps automatycznie tworzy zasoby platformy Azure, takie jak usługa Azure Service Fabric, tworzy i konfiguruje potok wydania w usłudze VSTS zawierający definicję kompilacji dla ciągłej integracji, konfiguruje definicję wydania dla ciągłego wdrażania, a następnie tworzy zasób usługi Azure Application Insights na potrzeby monitorowania.

Wykonasz następujące zadania:

> [!div class="checklist"]
> * Tworzenie projektu DevOps platformy Azure dla aplikacji ASP.NET Core i usługi Azure Service Fabric
> * Konfigurowanie usługi VSTS i subskrypcji platformy Azure 
> * Sprawdzanie definicji kompilacji ciągłej integracji w usłudze VSTS
> * Sprawdzanie definicji zarządzania wydaniem ciągłego wdrażania usługi VSTS
> * Zatwierdzanie zmian w usłudze VSTS i automatyczne wdrażanie na platformie Azure
> * Oczyszczanie zasobów

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Możesz uzyskać ją bezpłatnie za pośrednictwem programu [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="create-an-azure-devops-project-for-an-aspnet-core-app-and-service-fabric"></a>Tworzenie projektu DevOps platformy Azure dla aplikacji ASP.NET Core i usługi Azure Service Fabric

Projekt DevOps platformy Azure tworzy potok ciągłej integracji/ciągłego wdrażania w usłudze VSTS.  Możesz utworzyć **nowe konto usługi VSTS** lub użyć **istniejącego konta**.  Projekt DevOps platformy Azure tworzy również **zasoby platformy Azure**, na przykład klaster usługi Azure Service Fabric, w wybranej przez użytkownika **subskrypcji platformy Azure**.

1. Zaloguj się do witryny [Microsoft Azure Portal](https://portal.azure.com).

1. Wybierz ikonę **Utwórz zasób** na pasku nawigacyjnym po lewej stronie, a następnie wyszukaj hasło **Projekt DevOps**.  Wybierz pozycję **Utwórz**.

    ![Rozpoczynanie ciągłego dostarczania](_img/azure-devops-project-service-fabric/fullbrowser.png)

1. Wybierz pozycję **.NET**, a następnie wybierz pozycję **Dalej**.

1. Na stronie **Wybierz strukturę aplikacji** wybierz opcję **ASP.NET Core**, a następnie wybierz pozycję **Dalej**.

1. Wybierz pozycję **Klaster usługi Service Fabric**, a następnie wybierz pozycję **Dalej**.  

## <a name="configure-vsts-and-an-azure-subscription"></a>Konfigurowanie usługi VSTS i subskrypcji platformy Azure

1. Utwórz **nowe** konto usługi VSTS lub wybierz **istniejące** konto.  Wybierz **nazwę** projektu usługi VSTS.  

1. Wybierz swoją **subskrypcję** platformy Azure.

1. Wybierz link **Zmień**, aby wyświetlić dodatkowe ustawienia konfiguracji platformy Azure, a następnie określić **rozmiar maszyny wirtualnej węzła** i **system operacyjny** dla **klastra usługi Service Fabric**.  W tym miejscu dostępne są różne opcje konfigurowania typu i lokalizacji usług platformy Azure.
 
1. Opuść obszar konfiguracji platformy Azure i wybierz pozycję **Gotowe**.

1. Zakończenie procesu potrwa kilka minut.  Przykładowa aplikacja ASP.NET zostanie skonfigurowana w repozytorium na koncie usługi VSTS, zostanie utworzony klaster usługi Service Fabric, a następnie zostaną wykonane kompilacja i wydanie, a aplikacja zostanie wdrożona na platformie Azure.  

    Po zakończeniu **pulpit nawigacyjny projektu** DevOps platformy Azure załaduje się w witrynie Azure Portal.  Możesz również przejść do **pulpitu nawigacyjnego projektu DevOps platformy Azure** bezpośrednio z obszaru **Wszystkie zasoby** w witrynie **Azure Portal**.  

    Ten pulpit nawigacyjny zapewnia wgląd do **repozytorium kodu** usługi VSTS, **potoku ciągłej integracji/ciągłego wdrażania usługi VSTS** i **klastra usługi Service Fabric**.  Możesz skonfigurować więcej dodatkowych opcji w usłudze VSTS.  Po prawej stronie pulpitu nawigacyjnego wybierz pozycję **Przeglądaj**, aby wyświetlić uruchomioną aplikację.

## <a name="examine-the-vsts-ci-build-definition"></a>Sprawdzanie definicji kompilacji ciągłej integracji w usłudze VSTS

Projekt DevOps platformy Azure automatycznie konfiguruje pełny potok ciągłej integracji/ciągłego wdrażania usługi VSTS na koncie usługi VSTS.  Możesz przeglądać i dostosowywać potok.  Wykonaj poniższe czynności, aby zapoznać się z definicją kompilacji w usłudze VSTS.

1. Przejdź do **pulpitu nawigacyjnego projektu DevOps platformy Azure**.

1. Wybierz pozycję **Potoki kompilacji** w **górnej części** **pulpitu nawigacyjnego projektu DevOps platformy Azure**.  Ten link otwiera kartę przeglądarki i definicję kompilacji usługi VSTS dla nowego projektu.

1. Przesuń wskaźnik myszy na prawo od definicji kompilacji, obok pola **Stan**. Wybierz symbol **wielokropka**, który się pojawi.  Ta czynność spowoduje otwarcie menu, w którym możesz uruchomić kilka działań, takich jak **dodawanie nowych kompilacji do kolejki**, **wstrzymywanie kompilacji** i **edytowanie definicji kompilacji**.

1. Wybierz pozycję **Edit** (Edytuj).

1. Z poziomu tego widoku **zapoznaj się z różnymi zadaniami** w definicji kompilacji.  W ramach kompilacji są wykonywane różne zadania, takie jak pobieranie źródeł z repozytorium Git usługi VSTS, przywracanie zależności i publikowanie danych wyjściowych używanych dla wdrożeń.

1. W górnej części definicji kompilacji wybierz **nazwę definicji kompilacji**.

1. Zmień **nazwę** definicji kompilacji na bardziej opisową.  Wybierz pozycję **Zapisz i dodaj do kolejki**, a następnie wybierz pozycję **Zapisz**.

1. W obszarze nazwy definicji kompilacji wybierz pozycję **Historia**.  Zostanie wyświetlony dziennik inspekcji ostatnio wprowadzonych zmian w kompilacji.  Usługa VSTS śledzi wszystkie zmiany wprowadzone w definicji kompilacji i pozwala na porównanie wersji.

1. Wybierz pozycję **Wyzwalacze**.  Projekt DevOps platformy Azure automatycznie utworzył wyzwalacz ciągłej integracji, a każde zatwierdzenie w repozytorium uruchamia nową kompilację.  Opcjonalnie możesz zdecydować się dołączyć gałęzie do procesu ciągłej integracji lub je wykluczyć.

1. Wybierz pozycję **Przechowywanie**.  W zależności od scenariusza możesz określić zasady przechowywania lub usuwania pewnej liczby kompilacji.

## <a name="examine-the-vsts-cd-release-management-definition"></a>Sprawdzanie definicji zarządzania wydaniem ciągłego wdrażania usługi VSTS

Projekt DevOps platformy Azure automatycznie tworzy i konfiguruje wymagane kroki wdrożenia z konta usługi VSTS na konto subskrypcji platformy Azure.  Te kroki obejmują konfigurowanie połączenia usługi platformy Azure w celu uwierzytelniania usługi VSTS w subskrypcji platformy Azure.  Automatyzacja tworzy również definicję wydania usługi VSTS, a wydanie zapewnia ciągłe wdrażanie na platformie Azure.  Wykonaj poniższe kroki, aby dowiedzieć się więcej na temat definicji wydania usługi VSTS.

1. Wybierz pozycję **Kompilacja i wydanie**, a następnie wybierz pozycję **Wydania**.  Projekt DevOps platformy Azure utworzył definicję wydania usługi VSTS w celu zarządzania wdrożeniami na platformie Azure.

1. Po lewej stronie okna przeglądarki wybierz symbol **wielokropka** obok definicji wydania, następnie wybierz pozycję **Edytuj**.

1. Definicja wydania zawiera **potok**, który definiuje proces tworzenia wydania.  W obszarze **Artefakty** wybierz polecenie **Porzuć**.  Definicja kompilacji przedstawiona w poprzednich krokach generuje dane wyjściowe używane na potrzeby artefaktu. 

1. Z prawej strony ikony **Porzuć** wybierz **ikonę** **wyzwalacza ciągłego wdrażania** w kształcie błyskawicy.  Ta definicja wydania ma włączony wyzwalacz ciągłego wdrażania.  Wyzwalacz przeprowadza wdrożenie za każdym razem, gdy dostępna jest nowa kompilacja artefaktu.  Opcjonalnie możesz wyłączyć wyzwalacz. Wtedy wdrożenia będą wymagać ręcznego wykonania. 

1. Po prawej stronie okna przeglądarki wybierz pozycję **Wyświetl wersje**.  Ten widok przedstawia historię wersji.

1. Wybierz symbol **wielokropka** obok jednej z wersji i wybierz polecenie **Otwórz**.  W tym widoku jest kilka menu, z którymi możesz się zapoznać, na przykład **podsumowanie wersji**, **skojarzone elementy robocze** i **Testy**.

1. Wybierz pozycję **Zatwierdzenia**.  Ten widok przedstawia zatwierdzenia kodu skojarzone z konkretnym wdrożeniem. Możesz porównać wydania, aby wyświetlić różnice w zatwierdzeniach pomiędzy wdrożeniami.

1. Wybierz pozycję **Dzienniki**.  Dzienniki zawierają przydatne informacje na temat procesu wdrażania.  Mogą być wyświetlane zarówno podczas wdrożeń, jak i po nich.

## <a name="commit-changes-to-vsts-and-automatically-deploy-to-azure"></a>Zatwierdzanie zmian w usłudze VSTS i automatyczne wdrażanie na platformie Azure 

 > [!NOTE]
 > W poniższych krokach potok ciągłej integracji/ciągłego wdrażania zostanie sprawdzony za pomocą prostej zmiany tekstu w aplikacji internetowej.

Możesz teraz rozpocząć pracę w zespole nad aplikacją w ramach procesu ciągłej integracji/ciągłego wdrażania, który umożliwia automatyczne wdrażanie najnowszej wersji w witrynie internetowej.  Każda zmiana w repozytorium Git usługi VSTS rozpoczyna tworzenie kompilacji w usłudze VSTS, a definicja zarządzania wydaniem usługi VSTS wdraża zmiany na platformie Azure.  Wykonaj poniższe kroki lub użyj innej techniki, aby zatwierdzić zmiany w repozytorium.  Możesz na przykład **sklonować** repozytorium Git za pomocą wybranego narzędzia lub zintegrowanego środowiska projektowego, a następnie wypchnąć zmiany do tego repozytorium.

1. Wybierz pozycję **Kod**, a następnie **Pliki** z menu usługi VSTS, a następnie przejdź do repozytorium.
1. Przejdź do katalogu **Views\Home**, a następnie wybierz symbol **wielokropka** obok pliku **Index.cshtml** i wybierz polecenie **Edytuj**.

1. Wprowadź zmianę w pliku, na przykład zmień tekst w jednym z **tagów div**.  W prawym górnym rogu wybierz polecenie **Zatwierdź**.  Wybierz ponownie polecenie **Zatwierdź**, aby wypchnąć zmianę. 

1. Po chwili **kompilacja zostanie uruchomiona w usłudze VSTS**, a następnie zostanie wykonane wydanie w celu wdrożenia zmian.  Możesz monitorować **stan kompilacji** na pulpicie nawigacyjnym projektu DevOps lub w przeglądarce przy użyciu konta usługi VSTS.

1. Po zakończeniu wydania **odśwież aplikację** w przeglądarce, aby sprawdzić, czy zmiany są wyświetlane.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

 > [!NOTE]
 > Wykonanie poniższych czynności spowoduje trwałe usunięcie zasobów.  Tej funkcji można używać tylko po uważnym zapoznaniu się z monitami.

Jeśli jesteś w trakcie testów, możesz oczyścić zasoby, aby uniknąć naliczania opłat.  Jeśli klaster usługi Azure Service Fabric nie jest już potrzebny, możesz go usunąć wraz z powiązanymi zasobami utworzonymi w tym samouczku, korzystając z funkcji **Usuń** na pulpicie nawigacyjnym projektu DevOps platformy Azure.  **Należy zachować ostrożność**, ponieważ funkcja usuwania niszczy dane utworzone w projekcie DevOps zarówno na platformie Azure, jak i w usłudze VSTS, i nie ma możliwości ich odzyskania.

1. W witrynie**Azure Portal** przejdź do **projektu DevOps platformy Azure**.
2. W **prawym górnym** rogu pulpitu nawigacyjnego wybierz opcję **Usuń**.  Po przeczytaniu monitu wybierz pozycję **Tak**, aby **trwale usunąć** zasoby.

## <a name="next-steps"></a>Następne kroki

Opcjonalnie możesz zmodyfikować definicje kompilacji i wydania, aby dopasować je do potrzeb swojego zespołu. Możesz także użyć wzorca ciągłej integracji/ciągłego wdrażania jako szablonu podczas pracy z innymi projektami.  W tym samouczku omówiono:

> [!div class="checklist"]
> * Tworzenie projektu DevOps platformy Azure dla aplikacji ASP.NET Core i usługi Azure Service Fabric
> * Konfigurowanie usługi VSTS i subskrypcji platformy Azure 
> * Sprawdzanie definicji kompilacji ciągłej integracji w usłudze VSTS
> * Sprawdzanie definicji zarządzania wydaniem ciągłego wdrażania usługi VSTS
> * Zatwierdzanie zmian w usłudze VSTS i automatyczne wdrażanie na platformie Azure
> * Oczyszczanie zasobów

Aby dowiedzieć się więcej na temat usługi Service Fabric i mikrousług, zobacz poniższy artykuł:

> [!div class="nextstepaction"]
> [Use a microservices approach for building applications (Korzystanie z mikrousług podczas tworzenia aplikacji)](https://docs.microsoft.com/vsts/pipelines/release/define-multistage-release-process?view=vsts)

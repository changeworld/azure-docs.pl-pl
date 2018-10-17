---
title: Wdrażanie aplikacji ASP.NET i bazy danych Azure SQL Database za pomocą projektu usługi Azure DevOps | Samouczek usługi Azure DevOps Services
description: Projekt DevOps ułatwia rozpoczęcie pracy na platformie Azure. Projekt usługi Azure DevOps umożliwia łatwe wdrożenie aplikacji ASP.NET i bazy danych Azure SQL Database w kilku szybkich krokach.
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: d9c7c94e344daee5af87ce40ddf4dcb686696ded
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2018
ms.locfileid: "44297337"
---
# <a name="tutorial--deploy-your-aspnet-app-and-azure-sql-database-with-the-azure-devops-project"></a>Samouczek: wdrażanie aplikacji ASP.NET i bazy danych Azure SQL Database za pomocą projektu DevOps platformy Azure

Projekt DevOps platformy Azure stanowi uproszczone środowisko, do którego możesz przenieść istniejący kod i repozytorium Git lub dokonać wyboru jednej z przykładowych aplikacji, aby utworzyć potok ciągłej integracji i ciągłego dostarczania na platformie Azure.  Projekt DevOps automatycznie tworzy zasoby platformy Azure, takie jak baza danych Azure SQL Database, tworzy i konfiguruje potok wydania w usłudze Azure DevOps zawierający potok kompilacji dla ciągłej integracji, konfiguruje potok wydania dla ciągłego wdrażania, a następnie tworzy zasób usługi Azure Application Insights na potrzeby monitorowania.

Wykonasz następujące zadania:

> [!div class="checklist"]
> * Tworzenie projektu usługi Azure DevOps dla aplikacji ASP.NET i bazy danych Azure SQL Database
> * Konfigurowanie usługi Azure DevOps Services i subskrypcji platformy Azure 
> * Badanie potoku ciągłej integracji usługi Azure DevOps Services
> * Badanie potoku ciągłego wdrażania usługi Azure DevOps Services
> * Zatwierdzanie zmian w usłudze Azure DevOps Services i automatyczne wdrażanie na platformie Azure
> * Łączenie z usługą Azure SQL Server Database 
> * Oczyszczanie zasobów

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Możesz uzyskać ją bezpłatnie za pośrednictwem programu [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="create-an-azure-devops-project-for-an-aspnet-app-and-azure-sql-database"></a>Tworzenie projektu usługi Azure DevOps dla aplikacji ASP.NET i bazy danych Azure SQL Database

Projekt usługi Azure DevOps tworzy potok ciągłej integracji/ciągłego wdrażania na platformie Azure.  Możesz utworzyć **nową organizację usługi Azure DevOps Services** lub użyć **istniejącej organizacji**.  Projekt DevOps platformy Azure tworzy również **zasoby platformy Azure**, na przykład bazę danych Azure SQL Database, w wybranej przez użytkownika **subskrypcji platformy Azure**.

1. Zaloguj się do witryny [Microsoft Azure Portal](https://portal.azure.com).

1. Wybierz ikonę **Utwórz zasób** na pasku nawigacyjnym po lewej stronie, a następnie wyszukaj pozycję **Projekt DevOps**.  Wybierz pozycję **Utwórz**.

    ![Rozpoczynanie ciągłego dostarczania](_img/azure-devops-project-github/fullbrowser.png)

1. Wybierz pozycję **.NET**, a następnie wybierz pozycję **Dalej**.

1. Na stronie **Wybierz strukturę aplikacji** wybierz opcję **ASP.NET**.

1. Wybierz polecenie **Dodaj bazę danych**, a następnie wybierz pozycję **Dalej**.  

1. Struktura aplikacji wybrana w poprzednich krokach decyduje o dostępnym w tym miejscu typie celu wdrożenia usługi platformy Azure.  Wybierz opcję **Dalej**.

## <a name="configure-azure-devops-services-and-an-azure-subscription"></a>Konfigurowanie usługi Azure DevOps Services i subskrypcji platformy Azure

1. Utwórz **nową** organizację usługi Azure DevOps Services lub wybierz **istniejącą** organizację.  Wybierz **nazwę** projektu usługi Azure DevOps.  

1. Wybierz swoją **subskrypcję** platformy Azure.

1. Możesz również wybrać link **Zmień**, aby wyświetlić dodatkowe ustawienia konfiguracji platformy Azure, a następnie określić **nazwę użytkownika** w sekcji **Szczegóły logowania na serwerze bazy danych**.  **Zachowaj** **nazwę użytkownika**, aby użyć jej w dalszych krokach tego samouczka.
 
1. Jeśli wykonano powyższe czynności, opuść obszar konfiguracji platformy Azure i wybierz pozycję **Gotowe**.  W przeciwnym razie po prostu wybierz pozycję **Gotowe**.

1. Zakończenie procesu potrwa kilka minut.  Po zakończeniu **pulpit nawigacyjny projektu** usługi Azure DevOps zostanie załadowany w witrynie Azure Portal.  Możesz również przejść do **pulpitu nawigacyjnego projektu DevOps platformy Azure** bezpośrednio z obszaru **Wszystkie zasoby** w witrynie **Azure Portal**.  Po prawej stronie pulpitu nawigacyjnego wybierz pozycję **Przeglądaj**, aby wyświetlić uruchomioną aplikację.
    
## <a name="examine-the-azure-devops-services-ci-pipeline"></a>Badanie potoku ciągłej integracji usługi Azure DevOps Services

Projekt usługi Azure DevOps automatycznie skonfigurował pełny potok ciągłej integracji/ciągłego wdrażania na platformie Azure w organizacji usługi Azure DevOps Services.  Możesz przeglądać i dostosowywać potok.  Wykonaj poniższe kroki, aby zapoznać się z potokiem kompilacji usługi Azure DevOps Services.

1. Przejdź do **pulpitu nawigacyjnego projektu DevOps platformy Azure**.

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

Projekt usługi Azure DevOps automatycznie tworzy i konfiguruje wymagane kroki wdrożenia z organizacji usługi Azure DevOps Services do subskrypcji platformy Azure.  Te kroki obejmują konfigurowanie połączenia usługi platformy Azure w celu uwierzytelniania usługi Azure DevOps Services w subskrypcji platformy Azure.  Automatyzacja tworzy również definicję wydania usługi Azure DevOps Services, a wydanie zapewnia ciągłe wdrażanie na platformie Azure.  Wykonaj poniższe kroki, aby dowiedzieć się więcej o definicji potoku wydania usługi Azure DevOps Services.

1. Wybierz pozycję **Kompilacja i wydanie**, a następnie wybierz pozycję **Wydania**.  Projekt Azure DevOps utworzył potok wydania usługi Azure DevOps Services w celu zarządzania wdrożeniami na platformie Azure.

1. Po lewej stronie okna przeglądarki wybierz symbol **wielokropka** obok potoku wydania, a następnie wybierz pozycję **Edytuj**.

1. Potok wydania zawiera **potok**, który definiuje proces tworzenia wydania.  W obszarze **Artefakty** wybierz polecenie **Porzuć**.  Potok kompilacji przedstawiony w poprzednich krokach generuje dane wyjściowe używane na potrzeby artefaktu. 

1. Z prawej strony ikony **Porzuć** wybierz **ikonę** **wyzwalacza ciągłego wdrażania** w kształcie błyskawicy.  Ten potok wydania ma włączony wyzwalacz ciągłego wdrażania.  Wyzwalacz przeprowadza wdrożenie za każdym razem, gdy dostępny jest nowy artefakt kompilacji.  Opcjonalnie możesz wyłączyć wyzwalacz. Wtedy wdrożenia będą wymagać ręcznego wykonania. 

1. Projekt usługi Azure DevOps konfiguruje losowe hasło SQL i używa go w potoku wydania.  Po lewej stronie okna przeglądarki wybierz pozycję **Zmienne**. 

1. **Ten krok należy wykonać tylko, jeśli hasło programu SQL Server zostało zmienione.**  Istnieje jedna zmienna **Hasło**.  Po prawej stronie pola tekstowego **Wartość** wybierz ikonę **kłódki**.  **Wprowadź** nowe hasło, a następnie wybierz polecenie **Zapisz**.

1. Po lewej stronie okna przeglądarki wybierz pozycję **Zadania**, a następnie wybierz **środowisko**.  

1. Zadania to czynności wykonywane w procesie wdrażania, pogrupowane w **Fazy**.  W tym potoku wydania istnieje jedna faza.  Ta faza zawiera zadania **wdrożenia usługi Azure App Service** i **wdrożenia usługi Azure SQL Database**.

1. Wybierz zadanie **wykonania usługi Azure SQL** i sprawdź różne właściwości użyte podczas wdrożenia bazy danych SQL.  W obszarze **Pakiet wdrożeniowy** zwróć uwagę, czy w zadaniu używany jest **plik SQL DACPAC**.

1. Po prawej stronie okna przeglądarki wybierz pozycję **Wyświetl wersje**.  Ten widok przedstawia historię wersji.

1. Wybierz symbol **wielokropka** obok jednej z wersji i wybierz polecenie **Otwórz**.  W tym widoku jest kilka menu, z którymi możesz się zapoznać, na przykład **podsumowanie wersji**, **skojarzone elementy robocze** i **Testy**.

1. Wybierz pozycję **Zatwierdzenia**.  Ten widok przedstawia zatwierdzenia kodu skojarzone z konkretnym wdrożeniem. Możesz porównać wydania, aby wyświetlić różnice w zatwierdzeniach pomiędzy wdrożeniami.

1. Wybierz pozycję **Dzienniki**.  Dzienniki zawierają przydatne informacje na temat procesu wdrażania.  Mogą być wyświetlane zarówno podczas wdrożeń, jak i po nich.

## <a name="commit-changes-to-azure-devops-services-and-automatically-deploy-to-azure"></a>Zatwierdzanie zmian w usłudze Azure DevOps Services i automatyczne wdrażanie na platformie Azure 

 > [!NOTE]
 > W poniższych krokach potok ciągłej integracji/ciągłego wdrażania zostanie sprawdzony za pomocą prostej zmiany tekstu.  Możesz również wprowadzić zmianę w schemacie programu SQL Server w tabeli, aby sprawdzić proces wdrażania usługi SQL.

Możesz teraz rozpocząć pracę w zespole nad aplikacją w ramach procesu ciągłej integracji/ciągłego wdrażania, który umożliwia automatyczne wdrażanie najnowszej wersji w witrynie internetowej.  Każda zmiana w repozytorium Git usługi Azure DevOps Services rozpoczyna kompilację w usłudze Azure DevOps Services, a potok ciągłego wdrażania usługi Azure DevOps Services wykonuje wdrażanie na platformie Azure.  Wykonaj poniższe kroki lub użyj innej techniki, aby zatwierdzić zmiany w repozytorium.  Zmiany kodu wywołują rozpoczęcie procesu ciągłej integracji/ciągłego wdrażania i automatyczne wdrożenie nowych zmian na platformie Azure.

1. Wybierz pozycję **Kod** z menu usługi Azure DevOps Services i przejdź do repozytorium.

1. Przejdź do katalogu **SampleWebApplication\Views\Home**, a następnie wybierz symbol **wielokropka** obok pliku **Index.cshtml** i wybierz polecenie **Edytuj**.

1. Wprowadź zmianę w pliku, na przykład zmień tekst w jednym z **tagów div**.  W prawym górnym rogu wybierz polecenie **Zatwierdź**.  Wybierz ponownie polecenie **Zatwierdź**, aby wypchnąć zmianę. 

1. Po chwili **kompilacja zostanie uruchomiona w usłudze Azure DevOps Services**, a następnie zostanie wykonane wydanie w celu wdrożenia zmian.  **Stan kompilacji** możesz monitorować na pulpicie nawigacyjnym projektu usługi DevOps lub w przeglądarce przy użyciu organizacji usługi Azure DevOps Services.

1. Po zakończeniu wydania **odśwież aplikację** w przeglądarce, aby sprawdzić, czy zmiany są wyświetlane.

## <a name="connect-to-the-azure-sql-server-database"></a>Łączenie z usługą Azure SQL Server Database

Aby połączyć się z usługą Azure SQL Database potrzebne są odpowiednie uprawnienia.

1. Na pulpicie nawigacyjnym projektu DevOps platformy Azure wybierz pozycję **Baza danych SQL**, aby przejść do strony zarządzania bazą danych SQL.
   
1. Wybierz polecenie **Ustaw zaporę serwera**, a następnie wybierz pozycję **+ Dodaj adres IP klienta**.  

1. Wybierz pozycję **Zapisz**.  Adres IP klienta ma teraz dostęp do **zasobów usługi Azure SQL Server**.

1. Wróć do bloku **Baza danych SQL**. 

1. Po prawej stronie wybierz pozycję **Nazwa serwera**, aby przejść do strony konfiguracji usługi **SQL Server**.

1. Wybierz polecenie **Resetuj hasło**, wprowadź hasło **logowania administratora usługi SQL Server**, a następnie wybierz pozycję **Zapisz**.  **Zachowaj** to hasło, aby użyć go w dalszych krokach tego samouczka.

1. Możesz teraz opcjonalnie użyć narzędzi klienta, takich jak **SQL Server Management Studio** lub **Visual Studio**, aby połączyć się z usługami Azure SQL Server i Database.  Użyj właściwości **Nazwa serwera**, aby nawiązać połączenie.

   Jeśli nie zmieniono nazwy użytkownika bazy danych podczas początkowego konfigurowania projektu DevOps, nazwa użytkownika to lokalna część adresu e-mail.  Na przykład jeśli Twój adres e-mail to johndoe@microsoft.com, nazwa użytkownika to johndoe.

 > [!NOTE]
 > Jeśli zmienisz hasło logowania usługi SQL, musisz zmienić hasło w zmiennej potoku wydania usługi Azure DevOps Services, zgodnie z instrukcjami w sekcji **Badanie potoku ciągłego wdrażania usługi Azure DevOps Services**

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

 > [!NOTE]
 > Wykonanie poniższych czynności spowoduje trwałe usunięcie zasobów.  Tej funkcji można używać tylko po uważnym zapoznaniu się z monitami.

Jeśli jesteś w trakcie testów, możesz oczyścić zasoby, aby uniknąć naliczania opłat.  Jeśli baza danych Azure SQL Database nie jest już potrzebna, możesz ją usunąć wraz z powiązanymi zasobami utworzonymi w tym samouczku, korzystając z funkcji **Usuń** na pulpicie nawigacyjnym projektu DevOps platformy Azure.  **Należy zachować ostrożność**, ponieważ funkcja usuwania niszczy dane utworzone w projekcie usługi Azure DevOps zarówno na platformie Azure, jak i w usłudze Azure DevOps Services, i nie ma możliwości ich odzyskania.

1. W witrynie**Azure Portal** przejdź do **projektu DevOps platformy Azure**.
2. W **prawym górnym** rogu pulpitu nawigacyjnego wybierz opcję **Usuń**.  Po przeczytaniu monitu wybierz pozycję **Tak**, aby **trwale usunąć** zasoby.

## <a name="next-steps"></a>Następne kroki

Opcjonalnie możesz zmodyfikować potoki kompilacji i wydania, aby zaspokoić potrzeby swojego zespołu. Możesz także użyć wzorca ciągłej integracji/ciągłego wdrażania jako szablonu podczas pracy z innymi projektami.  W tym samouczku omówiono:

> [!div class="checklist"]
> * Tworzenie projektu usługi Azure DevOps dla aplikacji ASP.NET i bazy danych Azure SQL Database
> * Konfigurowanie usługi Azure DevOps Services i subskrypcji platformy Azure 
> * Badanie potoku ciągłej integracji usługi Azure DevOps Services
> * Badanie potoku ciągłego wdrażania usługi Azure DevOps Services
> * Zatwierdzanie zmian w usłudze Azure DevOps Services i automatyczne wdrażanie na platformie Azure
> * Łączenie z usługą Azure SQL Server Database 
> * Oczyszczanie zasobów

Aby dowiedzieć się więcej na temat potoku platformy Azure, zapoznaj się z tym samouczkiem:

> [!div class="nextstepaction"]
> [Dostosowywanie procesu ciągłego wdrażania](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

## <a name="videos"></a>Filmy wideo

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3308/player]
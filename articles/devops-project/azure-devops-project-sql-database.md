---
title: 'Samouczek: wdrażanie aplikacji ASP.NET i kodu bazy danych Azure SQL Database za pomocą usługi Azure DevOps Projects'
description: Usługa DevOps Projects ułatwia rozpoczęcie pracy na platformie Azure. Usługa DevOps Projects pozwala wdrożyć aplikację platformy ASP.NET i kod bazy danych Azure SQL Database w kilku prostych krokach.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 560b16a40d73d07df6c9f446c7196cd930e2274e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "71971500"
---
# <a name="tutorial-deploy-your-aspnet-app-and-azure-sql-database-code-by-using-azure-devops-projects"></a>Samouczek: wdrażanie aplikacji ASP.NET i kodu bazy danych Azure SQL Database za pomocą usługi Azure DevOps Projects

Usługa Azure DevOps Projects stanowi uproszczone środowisko, do którego możesz przenieść istniejący kod i repozytorium Git lub wybrać aplikację przykładową, aby utworzyć potok ciągłej integracji i ciągłego dostarczania na platformie Azure. 

Usługa DevOps Projects wykonuje również następujące działania:
* Automatycznie tworzy zasoby platformy Azure, takie jak baza danych Azure SQL Database.
* Tworzy i konfiguruje potok wydania w usłudze Azure Pipelines, który obejmuje potok kompilacji na potrzeby ciągłej integracji.
* Konfiguruje potok wydania na potrzeby ciągłego wdrażania. 
* Tworzy zasób usługi Azure Application Insights na potrzeby monitorowania.

W tym samouczku zostaną wykonane następujące czynności:

> [!div class="checklist"]
> * Samouczek: wdrażanie aplikacji ASP.NET i kodu bazy danych Azure SQL Database za pomocą usługi Azure DevOps Projects
> * Konfigurowanie usługi Azure DevOps i subskrypcji platformy Azure 
> * Badanie potoku ciągłej integracji
> * Badanie potoku ciągłego wdrażania
> * Zatwierdzanie zmian w usłudze Azure Repos i automatyczne wdrażanie ich na platformie Azure
> * Łączenie z bazą danych Azure SQL Database 
> * Oczyszczanie zasobów

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Możesz uzyskać ją bezpłatnie za pośrednictwem programu [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="create-a-project-in-devops-projects-for-an-aspnet-app-and-an-azure-sql-database"></a>Tworzenie projektu w usłudze Azure DevOps Projects dla aplikacji ASP.NET i bazy danych Azure SQL Database

Usługa DevOps Projects tworzy potok ciągłej integracji/ciągłego wdrażania w usłudze Azure Pipelines. Możesz utworzyć nową organizację usługi Azure DevOps lub użyć istniejącej organizacji. Usługa Azure DevOps Projects tworzy również zasoby platformy Azure, na przykład bazę danych Azure SQL Database, w wybranej przez użytkownika subskrypcji platformy Azure.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).

1. W okienku po lewej stronie wybierz pozycję **Utwórz zasób**.

1. W polu wyszukiwania wpisz ciąg **DevOps Projects**, a następnie wybierz pozycję **Utwórz**.

    ![Pulpit nawigacyjny usługi DevOps Projects](_img/azure-devops-project-github/fullbrowser.png)

1. Wybierz pozycję **.NET**, a następnie wybierz pozycję **Dalej**.

1. Na stronie **Wybierz strukturę aplikacji** wybierz pozycję **ASP.NET**.

1. Wybierz pozycję **Dodaj bazę danych**, a następnie wybierz pozycję **Dalej**.  
    Wybrana w poprzednim kroku struktura aplikacji decyduje o dostępnym w tym miejscu typie celu wdrożenia usługi platformy Azure. 
    
1. Wybierz **pozycję Dalej**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurowanie usługi Azure DevOps i subskrypcji platformy Azure

1. Utwórz nową organizację usługi Azure DevOps lub wybierz istniejącą organizację. 

1. Wprowadź nazwę projektu usługi Azure DevOps. 

1. Wybierz usługi subskrypcji platformy Azure.  
    Możesz również wybrać link **Zmień**, aby wyświetlić dodatkowe ustawienia konfiguracji platformy Azure, a następnie określić nazwę użytkownika w sekcji **Szczegóły logowania na serwerze bazy danych**. Zachowaj nazwę użytkownika, aby użyć jej w dalszych krokach tego samouczka. Jeśli możesz wykonać ten krok opcjonalny, zamknij obszar konfiguracji platformy Azure przed wybraniem pozycji **Gotowe**.
 
1. Wybierz pozycję **Done** (Gotowe).  
    Po kilku minutach proces się zakończy i zostanie otwarty pulpit nawigacyjny usługi DevOps Projects w witrynie Azure Portal. Możesz również przejść do pulpitu nawigacyjnego bezpośrednio z obszaru **Wszystkie zasoby** w witrynie Azure Portal. Po prawej stronie wybierz pozycję **Przeglądaj**, aby wyświetlić uruchomioną aplikację.
    
## <a name="examine-the-ci-pipeline"></a>Badanie potoku ciągłej integracji

Usługa DevOps Projects automatycznie konfiguruje pełny potok ciągłej integracji/ciągłego wdrażania w usłudze Azure Repos. Możesz przeglądać i dostosowywać potok. Aby zapoznać się z potokami kompilacji usługi Azure DevOps, wykonaj następujące czynności:

1. U góry pulpitu nawigacyjnego DevOps Projects wybierz pozycję **Buduj potoki**.  
    Zostanie wyświetlona karta przeglądarki z potokiem kompilacji dla nowego projektu.

1. Wskaż pole **Stan** i wybierz symbol wielokropka (...).  
    Zostanie wyświetlone menu z kilkoma opcjami, takimi jak dodanie nowej kompilacji do kolejki, wstrzymanie kompilacji i edytowanie potoku kompilacji.

1. Wybierz pozycję **Edit** (Edytuj).

1. W tym okienku możesz zapoznać się z różnymi zadaniami w potoku kompilacji.  
    W ramach kompilacji są wykonywane różne zadania, takie jak pobieranie źródeł z repozytorium Git, przywracanie zależności i publikowanie danych wyjściowych używanych do wdrożenia.

1. W górnej części potoku kompilacji wybierz jego nazwę.

1. Zmień nazwę potoku kompilacji na bardziej opisową, wybierz pozycję **Zapisz i dodaj do kolejki**, a następnie wybierz pozycję **Zapisz**.

1. W obszarze nazwy potoku kompilacji wybierz pozycję **Historia**.  
    W tym okienku jest wyświetlany dziennik inspekcji ostatnio wprowadzonych zmian w kompilacji. Usługa Azure Pipelines śledzi wszelkie zmiany wprowadzone do potoku kompilacji i pozwala na porównanie wersji.

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

1. Z prawej strony ikony **Porzuć** wybierz pozycję **Wyzwalacz ciągłego wdrażania**.  
    Ten potok wydania ma włączony wyzwalacz ciągłego wdrażania, który przeprowadza wdrożenie za każdym razem, gdy jest dostępny nowy artefakt kompilacji. Opcjonalnie możesz wyłączyć wyzwalacz. Wtedy wdrożenia będą wymagać ręcznego wykonania. 

    Usługa DevOps Projects konfiguruje losowe hasło SQL i używa go w potoku wydania.
    
1. Po lewej stronie wybierz pozycję **Zmienne**. 

   > [!NOTE]
   > Ten krok należy wykonać tylko, jeśli hasło programu SQL Server zostało zmienione. Istnieje jedna zmienna hasła.
  
1. Obok pola **Wartość** wybierz ikonę kłódki, wprowadź nowe hasło, a następnie wybierz pozycję **Zapisz**.

1. Po lewej stronie wybierz pozycję **Zadania**, a następnie wybierz środowisko.  
    Zadania to czynności wykonywane w procesie wdrażania, pogrupowane w fazy. Ten potok wydania ma jedną fazę, która zawiera zadania *wdrożenia usługi Azure App Service* i *wdrożenia usługi Azure SQL Database*.

1. Wybierz zadanie *wykonania usługi Azure SQL* i sprawdź różne właściwości użyte podczas wdrożenia bazy danych SQL.  
    W obszarze **Pakiet wdrożeniowy** w zadaniu używany jest plik *SQL DACPAC*.

1. Po prawej stronie wybierz pozycję **Wyświetl wydania**, aby wyświetlić historię wydań.

1. Wybierz symbol wielokropka (...) obok wydania, a następnie wybierz pozycję **Otwórz**.  
     Dostępnych jest kilka menu, z którymi możesz się zapoznać, na przykład podsumowanie wydań, skojarzone elementy robocze i testy.

1. Wybierz pozycję **Zatwierdzenia**.  
     Ten widok przedstawia zatwierdzenia kodu skojarzone z danym wdrożeniem. Porównaj wydania, aby wyświetlić różnice w zatwierdzeniach pomiędzy wdrożeniami.

1. Wybierz **dzienniki**.  
     Dzienniki zawierają przydatne informacje na temat procesu wdrażania. Możesz je wyświetlać zarówno podczas wdrażania, jak i po jego zakończeniu.

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>Zatwierdzanie zmian w usłudze Azure Repos i automatyczne wdrażanie ich na platformie Azure 

 > [!NOTE]
 > Poniższa procedura testuje potok ciągłej integracji/ciągłego wdrażania, wprowadzając prostą zmianę tekstu. Aby sprawdzić proces wdrażania usługi SQL, możesz również wprowadzić zmianę w schemacie programu SQL Server w tabeli.

Teraz możesz rozpocząć współpracę z zespołem nad aplikacją w języku Python w ramach procesu ciągłej integracji/ciągłego wdrażania, który automatycznie wdraża najnowszy kod w witrynie internetowej. Każda zmiana w repozytorium Git rozpoczyna kompilację w usłudze Azure DevOps, a potok ciągłego wdrażania wykonuje wdrażanie na platformie Azure. Wykonaj procedurę opisaną w tej sekcji lub użyj innej techniki, aby zatwierdzić zmiany w repozytorium. Zmiany kodu wywołują rozpoczęcie procesu ciągłej integracji/ciągłego wdrażania i automatyczne wdrożenie zmian na platformie Azure.

1. W lewym okienku wybierz pozycję **Kod**, a następnie przejdź do repozytorium.

1. Przejdź do katalogu *SampleWebApplication\Views\Home*, a następnie wybierz symbol wielokropka (...) obok pliku *Index.cshtml* i wybierz polecenie **Edytuj**. 

1. Wprowadź zmianę w pliku, na przykład dodaj tekst w jednym z tagów div. 

1. W prawym górnym rogu wybierz pozycję **Zatwierdź**, a następnie ponownie wybierz pozycję **Zatwierdź**, aby wypchnąć zmianę.  
    Po chwili zostanie uruchomiona kompilacja w usłudze Azure DevOps, a następnie zostanie wykonane wydanie w celu wdrożenia zmian. Monitoruj stan kompilacji na pulpicie nawigacyjnym usługi DevOps Projects lub w przeglądarce przy użyciu organizacji usługi Azure DevOps.

1. Po zakończeniu tworzenia wydania odśwież aplikację, aby zweryfikować zmiany.

## <a name="connect-to-the-azure-sql-database"></a>Łączenie z bazą danych Azure SQL Database

Aby połączyć się z bazą danych Azure SQL Database potrzebne są odpowiednie uprawnienia.

1. Na pulpicie nawigacyjnym usługi DevOps Projects wybierz pozycję **Baza danych SQL**, aby przejść do strony zarządzania bazą danych SQL.
   
1. Wybierz polecenie **Ustaw zaporę serwera**, a następnie wybierz pozycję **Dodaj adres IP klienta**. 

1. Wybierz **pozycję Zapisz**.  
    Adres IP klienta ma teraz dostęp do zasobów usługi Azure SQL Server.

1. Wróć do okienka **Baza danych SQL**. 

1. Po prawej stronie wybierz nazwę serwera, aby przejść do strony konfiguracji usługi **SQL Server**.

1. Wybierz **pozycję Resetuj hasło**, wprowadź hasło do logowania administratora programu SQL Server, a następnie wybierz pozycję **Zapisz**.  
    Zachowaj to hasło do użycia w dalszej części tego samouczka.

    Możesz teraz opcjonalnie użyć narzędzi klienta, takich jak SQL Server Management Studio lub Visual Studio, aby połączyć się z usługą SQL Server i bazą danych Azure SQL Database. Użyj właściwości **Nazwa serwera**, aby nawiązać połączenie.

    Jeśli nie zmieniono nazwy użytkownika bazy danych podczas początkowego konfigurowania projektu w usłudze DevOps Projects, nazwa użytkownika to lokalna część adresu e-mail. Na przykład, jeśli twój adres e-mail jest *johndoe\@microsoft.com*, twoja nazwa użytkownika jest *johndoe*.

   > [!NOTE]
   > Jeśli zmienisz hasło logowania usługi SQL, musisz zmienić hasło w zmiennej potoku wydania, zgodnie z instrukcjami w sekcji „Badanie potoku ciągłego wdrażania”.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli przeprowadzasz testowanie, możesz uniknąć naliczania opłat, oczyszczając zasoby. Gdy baza danych Azure SQL Database i powiązane zasoby utworzone w tym samouczku nie będą już potrzebne, możesz je usunąć. W tym celu użyj funkcji **Usuń** na pulpicie nawigacyjnym usługi DevOps Projects.

> [!IMPORTANT]
> Poniższa procedura powoduje trwałe usunięcie zasobów. Funkcja *Usuń* niszczy dane utworzone przez projekt w usłudze DevOps Projects zarówno na platformie Azure, jak i w usłudze Azure DevOps. Nie będzie można ich odzyskać. Użyj tej procedury dopiero po uważnym przeczytaniu monitów.

1. W witrynie Azure Portal przejdź do pulpitu nawigacyjnego usługi DevOps Projects.
2. W prawym górnym rogu wybierz pozycję **Usuń**. 
3. Po wyświetleniu monitu wybierz pozycję **Tak**, aby *trwale usunąć* zasoby.

## <a name="next-steps"></a>Następne kroki

Opcjonalnie możesz zmodyfikować potoki kompilacji i wydania, aby zaspokoić potrzeby swojego zespołu. Możesz także użyć wzorca ciągłej integracji/ciągłego wdrażania jako szablonu podczas pracy z innymi potokami. W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Samouczek: wdrażanie aplikacji ASP.NET i kodu bazy danych Azure SQL Database za pomocą usługi Azure DevOps Projects
> * Konfigurowanie usługi Azure DevOps i subskrypcji platformy Azure 
> * Badanie potoku ciągłej integracji
> * Badanie potoku ciągłego wdrażania
> * Zatwierdzanie zmian w usłudze Azure Repos i automatyczne wdrażanie ich na platformie Azure
> * Łączenie z bazą danych Azure SQL Database 
> * Oczyszczanie zasobów

Aby dowiedzieć się więcej na temat potoku ciągłej integracji/ciągłego wdrażania, zobacz:

> [!div class="nextstepaction"]
> [Definiowanie wieloetapowego potoku ciągłego wdrażania](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

## <a name="videos"></a>Filmy wideo

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3308/player]

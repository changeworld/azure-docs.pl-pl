---
title: 'Samouczek: Wdrażanie aplikacji ASP.NET do usługi Azure Functions przy użyciu usługi Azure DevOps Projects'
description: Usługa Azure DevOps Projects ułatwia rozpoczęcie pracy na platformie Azure. Przy użyciu DevOps Projects można wdrożyć aplikację ASP.NET do usługi Azure Functions w kilku prostych krokach.
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 06/20/2019
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 6894f9bc6c803e2692afb54d7459adf6b0e6dbd6
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67828092"
---
# <a name="continuously-deploy-to-azure-functions-with-devops-projects"></a>Ciągłe wdrażanie w usłudze Azure Functions przy użyciu DevOps Projects

Usługa Azure DevOps Projects stanowi uproszczone środowisko, do którego możesz przenieść istniejący kod i repozytorium Git lub wybrać aplikację przykładową, aby utworzyć potok ciągłej integracji i ciągłego dostarczania na platformie Azure.

Usługa DevOps Projects wykonuje również następujące działania:

* Automatycznie tworzy zasoby platformy Azure, takich jak Azure Functions

* Tworzy i konfiguruje potok tworzenia wersji w DevOps platformy Azure do ciągłej integracji/ciągłego wdrażania

W tym samouczku wykonasz następujące czynności:

> [!div class="checklist"]
>* Usługa DevOps Projects umożliwia wdrażanie aplikacji platformy ASP.NET funkcji platformy Azure
>* Konfigurowanie usługi Azure DevOps i subskrypcji platformy Azure
>* Sprawdź funkcji platformy Azure
>* Badanie potoku ciągłej integracji
>* Badanie potoku ciągłego wdrażania
>* Zatwierdzanie zmian w usłudze Git i automatyczne wdrażanie ich na platformie Azure
>* Oczyszczanie zasobów

Obecnie są obsługiwane środowiska uruchomieniowe funkcji **.NET** i **Node.js**. Używamy. NET środowiska uruchomieniowego na potrzeby tego samouczka wdrożyć usługi Azure Functions. 

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Możesz uzyskać jeden oferowana bezpłatnie do [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/)

## <a name="use-devops-projects-to-deploy-an-aspnet-app-to-azure-functions"></a>Usługa DevOps Projects umożliwia wdrażanie aplikacji ASP.NET w usłudze Azure Functions

Usługa DevOps Projects tworzy potok ciągłej integracji/ciągłego wdrażania w usłudze Azure Pipelines. Możesz utworzyć nową organizację usługi Azure DevOps lub użyć istniejącej organizacji. Projekty DevOps tworzy również zasoby platformy Azure, takich jak IoTHub w subskrypcji platformy Azure wybranym.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).

1. W okienku po lewej stronie wybierz pozycję **Utwórz zasób**.

1. W polu wyszukiwania wpisz **DevOps Projects**, a następnie kliknij przycisk **Dodaj**.

   ![DevOps Projects](_img/azure-devops-project-functions/devops-project.png)

1. Wybierz pozycję **.NET**, a następnie wybierz pozycję **Dalej**. W obszarze **Wybierz strukturę aplikacji**, wybierz opcję **ASP.NET** i kliknij przycisk **dalej**.

1. Wybierz **aplikacji funkcji** , a następnie wybierz **dalej**.

## <a name="configure-azure-devops-and-azure-subscription"></a>Konfigurowanie DevOps platformy Azure i subskrypcję platformy Azure

1. Wprowadź nazwę projektu usługi Azure DevOps.

1. Utwórz nową organizację usługi Azure DevOps lub wybierz istniejącą organizację.

1. Wybierz swoją subskrypcję platformy Azure.

1. Aby wyświetlić ustawienia dodatkowej konfiguracji platformy Azure i do identyfikowania warstwy cenowej i lokalizację, kliknij przycisk Określ dodatkowe ustawienia. W tym okienku wyświetla różne opcje dotyczące konfigurowania warstwy cenowej i lokalizacji usług platformy Azure.

1. Zamknij obszar konfiguracji platformy Azure, a następnie wybierz pozycję gotowe.

1. Proces zakończy się po kilku minutach. Przykładowa aplikacja platformy ASP.NET jest skonfigurowana w repozytorium Git w Twojej organizacji DevOps platformy Azure z aplikacją funkcji usługi Application Insights jest tworzony, potok ciągłej integracji/ciągłego wdrażania jest wykonywany i Twoja aplikacja jest wdrożona na platformie Azure.

   Po zakończeniu wszystkich tych operacji zostanie wyświetlony pulpit nawigacyjny projektu usługi Azure DevOps w witrynie Azure Portal. Do pulpitu nawigacyjnego usługi DevOps Projects możesz również przejść bezpośrednio z obszaru **Wszystkie zasoby** w witrynie Azure Portal.

   Ten pulpit nawigacyjny zapewnia wgląd w repozytorium kodu DevOps platformy Azure, potok ciągłej integracji/ciągłego Dostarczania i funkcji platformy Azure. Dodatkowe opcje ciągłej integracji/ciągłego wdrażania możesz skonfigurować w potoku usługi Azure DevOps. Po prawej stronie, wybierz **aplikacji funkcji** do wyświetlenia.

## <a name="examine-the-function-app"></a>Sprawdź aplikację funkcji

Usługa DevOps Projects automatycznie konfiguruje aplikacji funkcji, dzięki której możesz eksplorować i dostosować. Aby uzyskać dostęp do aplikacji funkcji wiedzieć, wykonaj następujące czynności:

1. Przejdź do pulpitu nawigacyjnego usługi DevOps Projects.

    ![Pulpit nawigacyjny projekty DevOps](_img/azure-devops-project-functions/devops-projects-dashboard.png)

1. Po prawej stronie wybierz aplikację funkcji. Zostanie otwarte okienko dla aplikacji funkcji. W tym widoku można wykonywać różne akcje, takie jak operacje monitorowania, przeszukiwania dzienników.

    ![Aplikacja usługi Functions](_img/azure-devops-project-functions/function-app.png)

## <a name="examine-the-ci-pipeline"></a>Badanie potoku ciągłej integracji

Usługa DevOps Projects automatycznie konfiguruje potok ciągłej integracji/ciągłego wdrażania w organizacji usługi Azure DevOps. Możesz przeglądać i dostosowywać potok. Aby zapoznać się z nim, wykonaj następujące czynności:

1. Przejdź do pulpitu nawigacyjnego usługi DevOps Projects.

1. Kliknij hiperlink w obszarze **kompilacji**. Zostanie wyświetlona karta przeglądarki z potokiem kompilacji dla nowego projektu.

    ![Kompilacja](_img/azure-devops-project-functions/build.png)

1. Wybierz pozycję **Edit** (Edytuj). W tym okienku możesz zapoznać się z różnymi zadaniami w potoku kompilacji. Kompilacja wykonuje różne zadania, takie jak pobieranie kodu źródłowego z repozytorium Git, tworzenie aplikacji i Uruchamianie testów jednostkowych i publikowanie danych wyjściowych, które są używane w przypadku wdrożeń.

1. Wybierz pozycję **Wyzwalacze**. Usługa DevOps Projects automatycznie tworzy wyzwalacz ciągłej integracji — każde zatwierdzenie w repozytorium uruchamia nową kompilację. Opcjonalnie możesz zdecydować się dołączyć gałęzie do procesu ciągłej integracji lub wykluczyć je z niego.

1. Wybierz pozycję **Przechowywanie**. W zależności od scenariusza możesz określić zasady przechowywania lub usuwania pewnej liczby kompilacji.

1. W górnej części potoku kompilacji wybierz jego nazwę.

1. Zmień nazwę potoku kompilacji na bardziej opisową, a następnie wybierz **Zapisz** z **Zapisz k & olejką** listy rozwijanej.

1. W obszarze nazwy potoku kompilacji wybierz pozycję **Historia**. W tym okienku jest wyświetlany dziennik inspekcji ostatnio wprowadzonych zmian w kompilacji. Wszelkie zmiany wprowadzone do potoku kompilacji zachować informacje o DevOps platformy Azure i pozwala na porównanie wersji.

## <a name="examine-the-cd-release-pipeline"></a>Badanie potoku wydania ciągłego wdrażania

Usługa DevOps Projects automatycznie tworzy i konfiguruje kroki wymagane do wdrożenia z organizacji usługi Azure DevOps w ramach subskrypcji platformy Azure. Te kroki obejmują konfigurowanie połączenia usługi platformy Azure w celu uwierzytelniania usługi Azure DevOps w subskrypcji platformy Azure. W ramach automatyzacji jest też tworzony potok wydania, który zapewnia ciągłe wdrażanie na platformie Azure. Aby dowiedzieć się więcej o potoku wydania, wykonaj następujące czynności:

1. Przejdź do **potoki | Wersje**.

1. Kliknij pozycję **Edytuj**.

1. W obszarze **Artefakty** wybierz polecenie **Porzuć**. Potok kompilacji przedstawiony w poprzednich krokach generuje dane wyjściowe używane na potrzeby artefaktu.

1. Z prawej strony ikony **Porzuć** wybierz pozycję **Wyzwalacz ciągłego wdrażania**. Ten potok wydania ma włączony wyzwalacz ciągłego wdrażania, który przeprowadza wdrożenie za każdym razem, gdy jest dostępny nowy artefakt kompilacji. Opcjonalnie możesz wyłączyć wyzwalacz. Wtedy wdrożenia będą wymagać ręcznego wykonania.

1. Po prawej stronie wybierz pozycję **Wyświetl wydania**, aby wyświetlić historię wydań.

1. Polecenie wersji, co spowoduje wyświetlenie potoku. Kliknij w dowolnym środowisku, aby sprawdzić wersji **podsumowanie, zatwierdzeń**skojarzonej **elementów roboczych**.

1. Wybierz pozycję **Zatwierdzenia**. Ten widok przedstawia zatwierdzenia kodu skojarzone z danym wdrożeniem. Porównaj wydania, aby wyświetlić różnice w zatwierdzeniach pomiędzy wdrożeniami.

1. Wybierz **wyświetlanie dzienników**. Dzienniki zawierają przydatne informacje na temat procesu wdrażania. Możesz je wyświetlać zarówno podczas wdrażania, jak i po jego zakończeniu.

## <a name="commit-code-changes-and-execute-cicd"></a>Zatwierdzanie zmian kodu i wykonywanie ciągłej integracji/ciągłego wdrażania

> [!NOTE]
> Poniższa procedura testuje potok ciągłej integracji/ciągłego wdrażania, wprowadzając prostą zmianę tekstu.

Teraz możesz przystąpić do współpracy z zespołem w aplikacji przy użyciu procesu ciągłej integracji/ciągłego wdrażania, który automatycznie wdraża pracy najnowszych funkcji platformy Azure. Każda zmiana w repozytorium Git rozpoczyna kompilację w usłudze Azure DevOps, a potok ciągłego wdrażania wykonuje wdrażanie na platformie Azure. Wykonaj procedurę opisaną w tej sekcji lub użyj innej techniki, aby zatwierdzić zmiany w repozytorium. Możesz na przykład sklonować repozytorium Git za pomocą ulubionego narzędzia lub środowiska IDE, a następnie wypchnąć zmiany do tego repozytorium.

1. W menu DevOps platformy Azure wybierz **repozytoriów | Pliki**, a następnie przejdź do repozytorium.

1. Repozytorium zawiera już kodu o nazwie **SampleFunctionApp** opierając się na wybranej w ramach procesu tworzenia aplikacji. Otwórz **Application/SampleFunctionApp/Function1.cs** pliku.

1. Wybierz **Edytuj**, a następnie wprowadzić zmiany do **31 numer wiersza** . Na przykład, aby zaktualizować **hej tam! Usługi Azure Functions za pomocą projektów DevOps — Zapraszamy!**

1. W prawym górnym rogu wybierz pozycję **Zatwierdź**, a następnie ponownie wybierz pozycję **Zatwierdź**, aby wypchnąć zmianę.

1. Otwórz **Application/SampleFunctionApp.Test/Function1TestRunner.cs** pliku. 

1. Wybierz **Edytuj**, a następnie wprowadzić zmiany do **21 numer wiersza**. Na przykład, aby zaktualizować **hej tam! Witamy w usłudze Azure Functions przy użyciu usługi Azure DevOps Projects**.

     Po chwili zostanie uruchomiona kompilacja w usłudze Azure DevOps, a następnie zostanie wykonane wydanie w celu wdrożenia zmian. Monitoruj stan kompilacji na pulpicie nawigacyjnym usługi DevOps Projects lub w przeglądarce przy użyciu organizacji usługi Azure DevOps.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Możesz usunąć powiązane zasoby, które zostały utworzone podczas możesz nie są już potrzebne. Użyj funkcji **Usuń** na pulpicie nawigacyjnym usługi DevOps Projects.

## <a name="next-steps"></a>Kolejne kroki

Opcjonalnie możesz zmodyfikować potoki kompilacji i wydania, aby zaspokoić potrzeby swojego zespołu. Możesz także użyć wzorca ciągłej integracji/ciągłego wdrażania jako szablonu podczas pracy z innymi potokami. W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Usługa DevOps Projects umożliwia wdrażanie aplikacji ASP.NET Core funkcji platformy Azure
> * Konfigurowanie usługi Azure DevOps i subskrypcji platformy Azure 
> * Sprawdź funkcji platformy Azure
> * Badanie potoku ciągłej integracji
> * Badanie potoku ciągłego wdrażania
> * Zatwierdzanie zmian w usłudze Git i automatyczne wdrażanie ich na platformie Azure
> * Oczyszczanie zasobów


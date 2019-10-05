---
title: 'Samouczek: wdrażanie aplikacji ASP.NET w Azure Functions z Azure DevOps Projects'
description: Usługa Azure DevOps Projects ułatwia rozpoczęcie pracy na platformie Azure. Za pomocą DevOps Projects można wdrożyć aplikację ASP.NET do Azure Functions w kilku prostych krokach.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 06/20/2019
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 4e7e9428af86f131632650f18d45e7dd48f4b5cb
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2019
ms.locfileid: "71971573"
---
# <a name="continuously-deploy-to-azure-functions-with-devops-projects"></a>Ciągłe wdrażanie do Azure Functions z DevOps Projects

Usługa Azure DevOps Projects stanowi uproszczone środowisko, do którego możesz przenieść istniejący kod i repozytorium Git lub wybrać aplikację przykładową, aby utworzyć potok ciągłej integracji i ciągłego dostarczania na platformie Azure.

Usługa DevOps Projects wykonuje również następujące działania:

* Automatycznie tworzy zasoby platformy Azure, takie jak Azure Functions

* Tworzy i konfiguruje potok wydania na platformie Azure DevOps dla ciągłej integracji/ciągłego wdrażania

W tym samouczku zostaną wykonane następujące czynności:

> [!div class="checklist"]
>* Używanie DevOps Projects do wdrażania aplikacji ASP.NET w usłudze Azure Function
>* Konfigurowanie usługi Azure DevOps i subskrypcji platformy Azure
>* Sprawdzanie funkcji platformy Azure
>* Badanie potoku ciągłej integracji
>* Badanie potoku ciągłego wdrażania
>* Zatwierdzanie zmian w usłudze Git i automatyczne wdrażanie ich na platformie Azure
>* Oczyszczanie zasobów

Obecnie obsługiwane środowiska uruchomieniowe funkcji to **.NET** i **Node. js**. Używamy. Środowisko uruchomieniowe sieci dla tego samouczka do wdrożenia w Azure Functions. 

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Możesz skorzystać z jednej bezpłatnej [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/)

## <a name="use-devops-projects-to-deploy-an-aspnet-app-to-azure-functions"></a>Użyj DevOps Projects, aby wdrożyć aplikację ASP.NET do Azure Functions

Usługa DevOps Projects tworzy potok ciągłej integracji/ciągłego wdrażania w usłudze Azure Pipelines. Możesz utworzyć nową organizację usługi Azure DevOps lub użyć istniejącej organizacji. DevOps Projects również tworzy zasoby platformy Azure, takie jak IoTHub, w wybranej subskrypcji platformy Azure.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).

1. W okienku po lewej stronie wybierz pozycję **Utwórz zasób**.

1. W polu wyszukiwania wpisz **DevOps projects**, a następnie kliknij przycisk **Dodaj**.

   ![DevOps Projects](_img/azure-devops-project-functions/devops-project.png)

1. Wybierz pozycję **.NET**, a następnie wybierz pozycję **Dalej**. W obszarze **Wybierz strukturę aplikacji**wybierz pozycję **ASP.NET** , a następnie kliknij przycisk **dalej**.

1. Wybierz **aplikacja funkcji** a następnie wybierz przycisk **dalej**.

## <a name="configure-azure-devops-and-azure-subscription"></a>Konfigurowanie usługi Azure DevOps i subskrypcji platformy Azure

1. Wprowadź nazwę projektu usługi Azure DevOps.

1. Utwórz nową organizację usługi Azure DevOps lub wybierz istniejącą organizację.

1. Wybierz swoją subskrypcję platformy Azure.

1. Aby wyświetlić dodatkowe ustawienia konfiguracji platformy Azure i określić warstwę cenową i lokalizację, kliknij pozycję Ustawienia dodatkowe. W tym okienku są wyświetlane różne opcje konfigurowania warstwy cenowej i lokalizacji usług platformy Azure.

1. Zamknij obszar konfiguracja platformy Azure, a następnie wybierz pozycję Gotowe.

1. Proces zakończy się po kilku minutach. Przykładowa aplikacja ASP.NET jest skonfigurowana w repozytorium Git w organizacji usługi Azure DevOps, aplikacja funkcji i Application Insights zostanie utworzona potok ciągłej integracji/ciągłego wdrażania, a aplikacja zostanie wdrożona na platformie Azure.

   Po zakończeniu wszystkich tych operacji zostanie wyświetlony pulpit nawigacyjny projektu usługi Azure DevOps w witrynie Azure Portal. Do pulpitu nawigacyjnego usługi DevOps Projects możesz również przejść bezpośrednio z obszaru **Wszystkie zasoby** w witrynie Azure Portal.

   Ten pulpit nawigacyjny zapewnia wgląd w repozytorium kodu usługi Azure DevOps, potok CI/CD i funkcję platformy Azure. Dodatkowe opcje ciągłej integracji/ciągłego wdrażania możesz skonfigurować w potoku usługi Azure DevOps. Po prawej stronie wybierz **aplikacja funkcji** do wyświetlenia.

## <a name="examine-the-function-app"></a>Sprawdzanie aplikacja funkcji

DevOps Projects automatycznie konfiguruje aplikację funkcji, którą można eksplorować i dostosowywać. Aby poznać aplikację funkcji, wykonaj następujące czynności:

1. Przejdź do pulpitu nawigacyjnego usługi DevOps Projects.

    ![Pulpit nawigacyjny DevOps Projects](_img/azure-devops-project-functions/devops-projects-dashboard.png)

1. Po prawej stronie wybierz aplikację funkcji. Zostanie otwarte okienko dla aplikacji funkcji. Z tego widoku można wykonywać różne czynności, takie jak monitorowanie operacji, wyszukiwanie dzienników.

    ![Aplikacja usługi Functions](_img/azure-devops-project-functions/function-app.png)

## <a name="examine-the-ci-pipeline"></a>Badanie potoku ciągłej integracji

Usługa DevOps Projects automatycznie konfiguruje potok ciągłej integracji/ciągłego wdrażania w organizacji usługi Azure DevOps. Możesz przeglądać i dostosowywać potok. Aby zapoznać się z nim, wykonaj następujące czynności:

1. Przejdź do pulpitu nawigacyjnego usługi DevOps Projects.

1. Kliknij hiperlink w obszarze **kompilacja**. Zostanie wyświetlona karta przeglądarki z potokiem kompilacji dla nowego projektu.

    ![Kompilacja](_img/azure-devops-project-functions/build.png)

1. Wybierz pozycję **Edit** (Edytuj). W tym okienku możesz zapoznać się z różnymi zadaniami w potoku kompilacji. Kompilacja wykonuje różne zadania, takie jak pobieranie kodu źródłowego z repozytorium git, tworzenie aplikacji, uruchamianie testów jednostkowych i publikowanie danych wyjściowych, które są używane do wdrożeń.

1. Wybierz pozycję **Wyzwalacze**. Usługa DevOps Projects automatycznie tworzy wyzwalacz ciągłej integracji — każde zatwierdzenie w repozytorium uruchamia nową kompilację. Opcjonalnie możesz zdecydować się dołączyć gałęzie do procesu ciągłej integracji lub wykluczyć je z niego.

1. Wybierz pozycję **Przechowywanie**. W zależności od scenariusza możesz określić zasady przechowywania lub usuwania pewnej liczby kompilacji.

1. W górnej części potoku kompilacji wybierz jego nazwę.

1. Zmień nazwę potoku kompilacji na coś bardziej opisowego, a następnie wybierz pozycję **Zapisz** na liście rozwijanej **Zapisz & kolejki** .

1. W obszarze nazwy potoku kompilacji wybierz pozycję **Historia**. W tym okienku jest wyświetlany dziennik inspekcji ostatnio wprowadzonych zmian w kompilacji. Usługa Azure DevOps śledzi wszelkie zmiany wprowadzone w potoku kompilacji i umożliwia porównanie wersji.

## <a name="examine-the-cd-release-pipeline"></a>Badanie potoku wydania ciągłego wdrażania

Usługa DevOps Projects automatycznie tworzy i konfiguruje kroki wymagane do wdrożenia z organizacji usługi Azure DevOps w ramach subskrypcji platformy Azure. Te kroki obejmują konfigurowanie połączenia usługi platformy Azure w celu uwierzytelniania usługi Azure DevOps w subskrypcji platformy Azure. W ramach automatyzacji jest też tworzony potok wydania, który zapewnia ciągłe wdrażanie na platformie Azure. Aby dowiedzieć się więcej o potoku wydania, wykonaj następujące czynności:

1. Przejdź do **potoków | Wersje**.

1. Kliknij pozycję **Edytuj**.

1. W obszarze **Artefakty** wybierz polecenie **Porzuć**. Potok kompilacji przedstawiony w poprzednich krokach generuje dane wyjściowe używane na potrzeby artefaktu.

1. Z prawej strony ikony **Porzuć** wybierz pozycję **Wyzwalacz ciągłego wdrażania**. Ten potok wydania ma włączony wyzwalacz ciągłego wdrażania, który przeprowadza wdrożenie za każdym razem, gdy jest dostępny nowy artefakt kompilacji. Opcjonalnie możesz wyłączyć wyzwalacz. Wtedy wdrożenia będą wymagać ręcznego wykonania.

1. Po prawej stronie wybierz pozycję **Wyświetl wydania**, aby wyświetlić historię wydań.

1. Kliknij wydanie, które spowoduje wyświetlenie potoku. Kliknij dowolne środowisko, aby sprawdzić podsumowanie wydania **, zatwierdzenia**, skojarzone **elementy robocze**.

1. Wybierz pozycję **Zatwierdzenia**. Ten widok przedstawia zatwierdzenia kodu skojarzone z danym wdrożeniem. Porównaj wydania, aby wyświetlić różnice w zatwierdzeniach pomiędzy wdrożeniami.

1. Wybierz pozycję **Wyświetl dzienniki**. Dzienniki zawierają przydatne informacje na temat procesu wdrażania. Możesz je wyświetlać zarówno podczas wdrażania, jak i po jego zakończeniu.

## <a name="commit-code-changes-and-execute-cicd"></a>Zatwierdzanie zmian kodu i wykonywanie ciągłej integracji/ciągłego wdrażania

> [!NOTE]
> Poniższa procedura testuje potok ciągłej integracji/ciągłego wdrażania, wprowadzając prostą zmianę tekstu.

Teraz możesz przystąpić do współpracy z zespołem w aplikacji przy użyciu procesu ciągłej integracji/ciągłego wdrażania, który automatycznie wdraża najnowszą pracę w funkcji platformy Azure. Każda zmiana w repozytorium Git rozpoczyna kompilację w usłudze Azure DevOps, a potok ciągłego wdrażania wykonuje wdrażanie na platformie Azure. Wykonaj procedurę opisaną w tej sekcji lub użyj innej techniki, aby zatwierdzić zmiany w repozytorium. Możesz na przykład sklonować repozytorium Git za pomocą ulubionego narzędzia lub środowiska IDE, a następnie wypchnąć zmiany do tego repozytorium.

1. W menu Azure DevOps wybierz kolejno pozycje **repozytoria | Pliki**, a następnie przejdź do repozytorium.

1. Repozytorium zawiera już kod o nazwie **SampleFunctionApp** na podstawie języka aplikacji wybranego w procesie tworzenia. Otwórz plik **Application/SampleFunctionApp/Function1. cs** .

1. Wybierz pozycję **Edytuj**, a następnie wprowadź zmiany w **wierszu numer 31** . Na przykład możesz ją zaktualizować, aby **powitać! Azure Functions przy użyciu DevOps Projects**

1. W prawym górnym rogu wybierz pozycję **Zatwierdź**, a następnie ponownie wybierz pozycję **Zatwierdź**, aby wypchnąć zmianę.

1. Otwórz plik **Application/SampleFunctionApp. test/Function1TestRunner. cs** . 

1. Wybierz pozycję **Edytuj**, a następnie wprowadź zmiany w **wierszu numer 21**. Na przykład możesz ją zaktualizować, aby **powitać! Azure Functions przy użyciu Azure DevOps Projects**.

     Po chwili zostanie uruchomiona kompilacja w usłudze Azure DevOps, a następnie zostanie wykonane wydanie w celu wdrożenia zmian. Monitoruj stan kompilacji na pulpicie nawigacyjnym usługi DevOps Projects lub w przeglądarce przy użyciu organizacji usługi Azure DevOps.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Możesz usunąć powiązane zasoby, które zostały utworzone, gdy nie są już potrzebne. Użyj funkcji **Usuń** na pulpicie nawigacyjnym usługi DevOps Projects.

## <a name="next-steps"></a>Następne kroki

Opcjonalnie możesz zmodyfikować potoki kompilacji i wydania, aby zaspokoić potrzeby swojego zespołu. Możesz także użyć wzorca ciągłej integracji/ciągłego wdrażania jako szablonu podczas pracy z innymi potokami. W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Użyj DevOps Projects, aby wdrożyć aplikację ASP.NET Core w usłudze Azure Function
> * Konfigurowanie usługi Azure DevOps i subskrypcji platformy Azure 
> * Sprawdzanie funkcji platformy Azure
> * Badanie potoku ciągłej integracji
> * Badanie potoku ciągłego wdrażania
> * Zatwierdzanie zmian w usłudze Git i automatyczne wdrażanie ich na platformie Azure
> * Oczyszczanie zasobów


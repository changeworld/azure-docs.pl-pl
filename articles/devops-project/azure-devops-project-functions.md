---
title: 'Samouczek: Wdrażanie ASP.NET aplikacji w usłudze Azure Functions za pomocą projektów devops platformy Azure'
description: Usługa Azure DevOps Projects ułatwia rozpoczęcie pracy na platformie Azure. Dzięki projektom DevOps możesz wdrożyć aplikację ASP.NET w usłudze Azure Functions w kilku szybkich krokach.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 06/20/2019
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 4e7e9428af86f131632650f18d45e7dd48f4b5cb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "71971573"
---
# <a name="continuously-deploy-to-azure-functions-with-devops-projects"></a>Ciągłe wdrażanie w usłudze Azure Functions za pomocą projektów DevOps

Usługa Azure DevOps Projects stanowi uproszczone środowisko, do którego możesz przenieść istniejący kod i repozytorium Git lub wybrać aplikację przykładową, aby utworzyć potok ciągłej integracji i ciągłego dostarczania na platformie Azure.

Usługa DevOps Projects wykonuje również następujące działania:

* Automatyczne tworzone zasoby platformy Azure, takie jak usługi Azure Functions

* Tworzy i konfiguruje potok wersji w usłudze Azure DevOps dla ciągłej integracji/ciągłego wdrażania

W tym samouczku zostaną wykonane następujące czynności:

> [!div class="checklist"]
>* Wdrażanie aplikacji ASP.NET w usłudze Azure Function za pomocą projektów DevOps
>* Konfigurowanie usługi Azure DevOps i subskrypcji platformy Azure
>* Sprawdzanie funkcji platformy Azure
>* Badanie potoku ciągłej integracji
>* Badanie potoku ciągłego wdrażania
>* Zatwierdzanie zmian w usłudze Git i automatyczne wdrażanie ich na platformie Azure
>* Oczyszczanie zasobów

Obecnie obsługiwane środowiska wykonawcze dla funkcji to **.NET** i **Node.js**. Używamy. Środowisko wykonawcze NET dla tego samouczka do wdrożenia w usłudze Azure Functions. 

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Możesz dostać jeden za darmo za pośrednictwem [Programu Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/)

## <a name="use-devops-projects-to-deploy-an-aspnet-app-to-azure-functions"></a>Wdrażanie aplikacji ASP.NET w usłudze Azure Functions za pomocą projektów DevOps

Usługa DevOps Projects tworzy potok ciągłej integracji/ciągłego wdrażania w usłudze Azure Pipelines. Możesz utworzyć nową organizację usługi Azure DevOps lub użyć istniejącej organizacji. Projekty DevOps tworzy również zasoby platformy Azure, takie jak IoTHub, w wybranej subskrypcji platformy Azure.

1. Logowanie się do [witryny Azure portal](https://portal.azure.com)

1. W okienku po lewej stronie wybierz pozycję **Utwórz zasób**.

1. W polu wyszukiwania wpisz polecenie **DevOps Projects**, a następnie kliknij przycisk **Dodaj**.

   ![DevOps Projects](_img/azure-devops-project-functions/devops-project.png)

1. Wybierz pozycję **.NET**, a następnie wybierz pozycję **Dalej**. W obszarze **Wybierz strukturę aplikacji**wybierz **ASP.NET** i kliknij przycisk **Dalej**.

1. Wybierz **pozycję Aplikacja funkcji,** a następnie wybierz pozycję **Dalej**.

## <a name="configure-azure-devops-and-azure-subscription"></a>Konfigurowanie usługi Azure DevOps i subskrypcji platformy Azure

1. Wprowadź nazwę projektu usługi Azure DevOps.

1. Utwórz nową organizację usługi Azure DevOps lub wybierz istniejącą organizację.

1. Wybierz swoją subskrypcję platformy Azure.

1. Aby wyświetlić dodatkowe ustawienia konfiguracji platformy Azure i zidentyfikować warstwę cenową i lokalizację, kliknij dodatkowe ustawienia. W tym okienku są wyświetlane różne opcje konfigurowania warstwy cenowej i lokalizacji usług platformy Azure.

1. Opuść obszar konfiguracji platformy Azure i wybierz pozycję Gotowe.

1. Proces zakończy się po kilku minutach. Przykładowa aplikacja ASP.NET jest skonfigurowana w repozytorium Git w organizacji Azure DevOps, tworzona jest aplikacja funkcji i usługa Application Insights, jest wykonywany potok ciągłej integracji/ciągłego wdrażania, a aplikacja jest wdrażana na platformie Azure.

   Po zakończeniu wszystkich tych operacji zostanie wyświetlony pulpit nawigacyjny projektu usługi Azure DevOps w witrynie Azure Portal. Do pulpitu nawigacyjnego usługi DevOps Projects możesz również przejść bezpośrednio z obszaru **Wszystkie zasoby** w witrynie Azure Portal.

   Ten pulpit nawigacyjny zapewnia wgląd w repozytorium kodu usługi Azure DevOps, potoku ciągłej integracji/ciągłego wdrażania i funkcji platformy Azure. Dodatkowe opcje ciągłej integracji/ciągłego wdrażania możesz skonfigurować w potoku usługi Azure DevOps. Po prawej stronie wybierz **aplikację funkcji,** aby wyświetlić.

## <a name="examine-the-function-app"></a>Sprawdź aplikację funkcji

DevOps Projects automatycznie konfiguruje aplikację funkcji, którą można eksplorować i dostosowywać. Aby poznać aplikację funkcji, wykonaj następujące czynności:

1. Przejdź do pulpitu nawigacyjnego usługi DevOps Projects.

    ![Pulpit nawigacyjny projektów DevOps](_img/azure-devops-project-functions/devops-projects-dashboard.png)

1. Po prawej stronie wybierz aplikację funkcyjną. Zostanie otwarte okienko dla aplikacji funkcyjnej. Z tego widoku można wykonywać różne akcje, takie jak monitorowanie operacji, wyszukiwanie dzienników.

    ![Aplikacja usługi Functions](_img/azure-devops-project-functions/function-app.png)

## <a name="examine-the-ci-pipeline"></a>Badanie potoku ciągłej integracji

Usługa DevOps Projects automatycznie konfiguruje potok ciągłej integracji/ciągłego wdrażania w organizacji usługi Azure DevOps. Możesz przeglądać i dostosowywać potok. Aby zapoznać się z nim, wykonaj następujące czynności:

1. Przejdź do pulpitu nawigacyjnego usługi DevOps Projects.

1. Kliknij hiperłącze w obszarze **Kompilacja**. Zostanie wyświetlona karta przeglądarki z potokiem kompilacji dla nowego projektu.

    ![Kompilacja](_img/azure-devops-project-functions/build.png)

1. Wybierz pozycję **Edit** (Edytuj). W tym okienku możesz zapoznać się z różnymi zadaniami w potoku kompilacji. Kompilacja wykonuje różne zadania, takie jak pobieranie kodu źródłowego z repozytorium Git, tworzenie aplikacji, uruchamianie testów jednostkowych i publikowanie danych wyjściowych, które są używane do wdrożeń.

1. Wybierz pozycję **Wyzwalacze**. Usługa DevOps Projects automatycznie tworzy wyzwalacz ciągłej integracji — każde zatwierdzenie w repozytorium uruchamia nową kompilację. Opcjonalnie możesz zdecydować się dołączyć gałęzie do procesu ciągłej integracji lub wykluczyć je z niego.

1. Wybierz pozycję **Przechowywanie**. W zależności od scenariusza możesz określić zasady przechowywania lub usuwania pewnej liczby kompilacji.

1. W górnej części potoku kompilacji wybierz jego nazwę.

1. Zmień nazwę potoku kompilacji na coś bardziej opisowego, a następnie wybierz pozycję **Zapisz** z listy rozwijanej **Zapisz & kolejki.**

1. W obszarze nazwy potoku kompilacji wybierz pozycję **Historia**. W tym okienku jest wyświetlany dziennik inspekcji ostatnio wprowadzonych zmian w kompilacji. Usługa Azure DevOps śledzić wszelkie zmiany wprowadzone do potoku kompilacji i umożliwia porównanie wersji.

## <a name="examine-the-cd-release-pipeline"></a>Badanie potoku wydania ciągłego wdrażania

Usługa DevOps Projects automatycznie tworzy i konfiguruje kroki wymagane do wdrożenia z organizacji usługi Azure DevOps w ramach subskrypcji platformy Azure. Te kroki obejmują konfigurowanie połączenia usługi platformy Azure w celu uwierzytelniania usługi Azure DevOps w subskrypcji platformy Azure. W ramach automatyzacji jest też tworzony potok wydania, który zapewnia ciągłe wdrażanie na platformie Azure. Aby dowiedzieć się więcej o potoku wydania, wykonaj następujące czynności:

1. Przejdź do **potoków | Wydania**.

1. Kliknij pozycję **Edytuj**.

1. W obszarze **Artefakty** wybierz polecenie **Porzuć**. Potok kompilacji przedstawiony w poprzednich krokach generuje dane wyjściowe używane na potrzeby artefaktu.

1. Z prawej strony ikony **Porzuć** wybierz pozycję **Wyzwalacz ciągłego wdrażania**. Ten potok wydania ma włączony wyzwalacz ciągłego wdrażania, który przeprowadza wdrożenie za każdym razem, gdy jest dostępny nowy artefakt kompilacji. Opcjonalnie możesz wyłączyć wyzwalacz. Wtedy wdrożenia będą wymagać ręcznego wykonania.

1. Po prawej stronie wybierz pozycję **Wyświetl wydania**, aby wyświetlić historię wydań.

1. Kliknij na zwolnienie, które wyświetli potoku. Kliknij dowolne środowisko, aby sprawdzić **podsumowanie wydania, zatwierdzenia, skojarzone** **elementy robocze**.

1. Wybierz pozycję **Zatwierdzenia**. Ten widok przedstawia zatwierdzenia kodu skojarzone z danym wdrożeniem. Porównaj wydania, aby wyświetlić różnice w zatwierdzeniach pomiędzy wdrożeniami.

1. Wybierz **pozycję Zobacz dzienniki**. Dzienniki zawierają przydatne informacje na temat procesu wdrażania. Możesz je wyświetlać zarówno podczas wdrażania, jak i po jego zakończeniu.

## <a name="commit-code-changes-and-execute-cicd"></a>Zatwierdzanie zmian kodu i wykonywanie ciągłej integracji/ciągłego wdrażania

> [!NOTE]
> Poniższa procedura testuje potok ciągłej integracji/ciągłego wdrażania, wprowadzając prostą zmianę tekstu.

Teraz możesz przystąpić do współpracy z zespołem w aplikacji przy użyciu procesu ciągłej integracji/ciągłego wdrażania, który automatycznie wdraża najnowszą pracę w usłudze Azure Function. Każda zmiana w repozytorium Git rozpoczyna kompilację w usłudze Azure DevOps, a potok ciągłego wdrażania wykonuje wdrażanie na platformie Azure. Wykonaj procedurę opisaną w tej sekcji lub użyj innej techniki, aby zatwierdzić zmiany w repozytorium. Możesz na przykład sklonować repozytorium Git za pomocą ulubionego narzędzia lub środowiska IDE, a następnie wypchnąć zmiany do tego repozytorium.

1. W menu Azure DevOps wybierz pozycję **Repos | pliki**, a następnie przejdź do repozytorium.

1. Repozytorium zawiera już kod o nazwie **SampleFunctionApp** na podstawie języka aplikacji wybranego w procesie tworzenia. Otwórz plik **Application/SampleFunctionApp/Function1.cs.**

1. Wybierz **pozycję Edytuj**, a następnie zmień numer **wiersza 31** . Na przykład, można zaktualizować go do **Hello tam! Usługa Azure Functions za pomocą projektów DevOps**

1. W prawym górnym rogu wybierz pozycję **Zatwierdź**, a następnie ponownie wybierz pozycję **Zatwierdź**, aby wypchnąć zmianę.

1. Otwórz plik **Application/SampleFunctionApp.Test/Function1TestRunner.cs.** 

1. Wybierz **pozycję Edytuj**, a następnie zmień numer **wiersza 21**. Na przykład, można zaktualizować go do **Hello tam! Witamy w usłudze Azure Functions przy użyciu projektów devops platformy Azure.**

     Po chwili zostanie uruchomiona kompilacja w usłudze Azure DevOps, a następnie zostanie wykonane wydanie w celu wdrożenia zmian. Monitoruj stan kompilacji na pulpicie nawigacyjnym usługi DevOps Projects lub w przeglądarce przy użyciu organizacji usługi Azure DevOps.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Powiązane zasoby można usunąć, gdy nie są już potrzebne. Użyj funkcji **Usuń** na pulpicie nawigacyjnym usługi DevOps Projects.

## <a name="next-steps"></a>Następne kroki

Opcjonalnie możesz zmodyfikować potoki kompilacji i wydania, aby zaspokoić potrzeby swojego zespołu. Możesz także użyć wzorca ciągłej integracji/ciągłego wdrażania jako szablonu podczas pracy z innymi potokami. W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wdrażanie aplikacji ASP.NET Core w usłudze Azure Function za pomocą projektów DevOps
> * Konfigurowanie usługi Azure DevOps i subskrypcji platformy Azure 
> * Sprawdzanie funkcji platformy Azure
> * Badanie potoku ciągłej integracji
> * Badanie potoku ciągłego wdrażania
> * Zatwierdzanie zmian w usłudze Git i automatyczne wdrażanie ich na platformie Azure
> * Oczyszczanie zasobów


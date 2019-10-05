---
title: 'Samouczek: wdrażanie aplikacji node. js opartych na Azure Cosmos DB z Azure DevOps Projects'
description: Usługa Azure DevOps Projects ułatwia rozpoczęcie pracy na platformie Azure. Za pomocą DevOps Projects można wdrożyć aplikację Node. js, która jest oparta na Azure Cosmos DB do aplikacji sieci Web systemu Windows w kilku prostych krokach.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/11/2019
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 1c16368990148406fbacdde2981c10fab0b2d405
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2019
ms.locfileid: "71969663"
---
# <a name="deploy-nodejs-apps-powered-by-azure-cosmos-db-with-devops-projects"></a>Wdróż aplikacje Node. js obsługiwane przez Azure Cosmos DB z DevOps Projects

Azure DevOps Projects oferuje usprawnione środowisko, w którym można utworzyć potok ciągłej integracji i ciągłego wdrażania (CD) na platformie Azure. Można to zrobić przy użyciu istniejącego kodu i repozytorium git (repozytorium) lub wybierając przykładową aplikację.

Usługa DevOps Projects wykonuje również następujące działania:

* Automatycznie tworzy zasoby platformy Azure, takie jak Azure Cosmos DB, Application Insights platformy Azure, Azure App Service i plany App Service

* Tworzy i konfiguruje potok zwolnienia ciągłej integracji/ciągłego wdrażania w usłudze Azure DevOps

W tym samouczku zostaną wykonane następujące czynności:

> [!div class="checklist"]
> * Użyj DevOps Projects do wdrożenia aplikacji node. js obsługiwanej przez Azure Cosmos DB
> * Konfigurowanie usługi Azure DevOps i subskrypcji platformy Azure
> * Sprawdzanie Azure Cosmos DB
> * Badanie potoku ciągłej integracji
> * Badanie potoku ciągłego wdrażania
> * Zatwierdź zmiany w usłudze git i automatycznie wdróż je na platformie Azure
> * Czyszczenie zasobów

## <a name="prerequisites"></a>Wymagania wstępne

Potrzebna jest subskrypcja platformy Azure, za pomocą której możesz bezpłatnie uzyskać dostęp [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) .

## <a name="use-devops-projects-to-deploy-nodejs-app"></a>Wdrażanie aplikacji node. js za pomocą DevOps Projects

Usługa DevOps Projects tworzy potok ciągłej integracji/ciągłego wdrażania w usłudze Azure Pipelines. Możesz utworzyć nową organizację usługi Azure DevOps lub użyć istniejącej organizacji. DevOps Projects tworzy również zasoby platformy Azure, takie jak Azure Cosmos DB, Application Insights, App Service i App Service, w wybranej subskrypcji platformy Azure.

1. Zaloguj się do [portalu Azure](https://portal.azure.com).

1. W okienku po lewej stronie wybierz pozycję **Utwórz zasób**.

1. W polu wyszukiwania wprowadź **DevOps projects**, a następnie wybierz pozycję **Dodaj**.

   ![Okienko DevOps Projects](_img/azure-devops-project-cosmos-db/devops-project.png)

1. Wybierz pozycję **Node. js** jako środowisko uruchomieniowe, a następnie wybierz przycisk **dalej**. W obszarze **Wybierz platformę aplikacji**wybierz pozycję **Express. js**.

1. Włącz sekcję **Dodawanie bazy danych** dla **Cosmos DB**, a następnie wybierz pozycję **dalej**.

    ![Dodawanie bazy danych](_img/azure-devops-project-cosmos-db/add-database.png)

    Azure DevOps Projects obsługuje różne struktury aplikacji, takie jak **Express. js**, **Przykładowa aplikacja Node. js**i program **. js**. W tym samouczku użyjemy języka **Express. js**.

1. Wybierz usługę platformy Azure, aby wdrożyć aplikację, a następnie wybierz pozycję **dalej**. Dostępne opcje to: Web App, Azure Kubernetes Service i Azure Web App for Containers. W tym samouczku używamy **aplikacji sieci Web systemu Windows**.

## <a name="configure-azure-devops-and-azure-subscription"></a>Konfigurowanie usługi Azure DevOps i subskrypcji platformy Azure

1. Wprowadź nazwę projektu usługi Azure DevOps.

1. Utwórz nową organizację usługi Azure DevOps lub wybierz istniejącą organizację.

1. Wybierz swoją subskrypcję platformy Azure.

1. Aby wyświetlić dodatkowe ustawienia konfiguracji platformy Azure lub określić warstwę cenową i lokalizację, wybierz pozycję **dodatkowe ustawienia**. W tym okienku przedstawiono różne opcje konfigurowania warstwy cenowej i lokalizacji usług platformy Azure.

1. Opuść obszar konfiguracji platformy Azure i wybierz pozycję **Gotowe**.

1. Proces kończy się po kilku minutach. Przykładowa aplikacja Node. js jest skonfigurowana w repozytorium Git w organizacji usługi Azure DevOps. Następnie tworzone są Azure Cosmos DB, App Service, plan App Service i zasoby Application Insights, a także potok ciągłej integracji/ciągłego wdrażania. Aplikacja zostanie następnie wdrożona na platformie Azure.

   Po zakończeniu wszystkich tych procesów pulpit nawigacyjny projektu usługi Azure DevOps zostanie wyświetlony w Azure Portal. Do pulpitu nawigacyjnego usługi DevOps Projects możesz również przejść bezpośrednio z obszaru **Wszystkie zasoby** w witrynie Azure Portal.

   Ten pulpit nawigacyjny zapewnia wgląd w repozytorium kodu usługi Azure DevOps, potok CI/CD i bazę danych Azure Cosmos DB. Dodatkowe opcje ciągłej integracji/ciągłego wdrażania możesz skonfigurować w potoku usługi Azure DevOps. Po prawej stronie pulpitu nawigacyjnego wybierz pozycję **Azure Cosmos DB** , aby wyświetlić te opcje.

## <a name="examine-azure-cosmos-db"></a>Sprawdzanie Azure Cosmos DB

DevOps Projects automatycznie konfiguruje Azure Cosmos DB, które można eksplorować i dostosowywać. Aby zaznajomić się z Azure Cosmos DB, wykonaj następujące czynności:

1. Przejdź do pulpitu nawigacyjnego usługi DevOps Projects.

    ![Pulpit nawigacyjny DevOps Projects](_img/azure-devops-project-cosmos-db/devops-project-dashboard.png)

1. Po prawej stronie wybierz pozycję Azure Cosmos DB. Zostanie otwarte okienko dla Azure Cosmos DB. Z tego widoku można wykonywać różne akcje, takie jak operacje monitorowania i wyszukiwanie dzienników.

    ![Okienko Azure Cosmos DB](_img/azure-devops-project-cosmos-db/cosmos-db.png)

## <a name="examine-the-ci-pipeline"></a>Badanie potoku ciągłej integracji

Usługa DevOps Projects automatycznie konfiguruje potok ciągłej integracji/ciągłego wdrażania w organizacji usługi Azure DevOps. Możesz przeglądać i dostosowywać potok. Aby zapoznać się z nim, wykonaj następujące czynności:

1. Przejdź do pulpitu nawigacyjnego usługi DevOps Projects.

1. Wybierz hiperlink w obszarze **kompilacja**. Zostanie wyświetlona karta przeglądarki z potokiem kompilacji dla nowego projektu.

    ![Okienko kompilacji](_img/azure-devops-project-cosmos-db/build.png)

1. Wybierz pozycję **Edit** (Edytuj). W tym okienku możesz zapoznać się z różnymi zadaniami w potoku kompilacji. Kompilacja wykonuje różne zadania, takie jak pobieranie kodu źródłowego z repozytorium git, tworzenie aplikacji, uruchamianie testów jednostkowych i publikowanie danych wyjściowych, które są używane do wdrożeń.

1. Wybierz pozycję **Wyzwalacze**. Usługa DevOps Projects automatycznie tworzy wyzwalacz ciągłej integracji — każde zatwierdzenie w repozytorium uruchamia nową kompilację. Możesz dołączać lub wykluczać gałęzie z procesu CI elementu konfiguracji.

1. Wybierz pozycję **Przechowywanie**. W zależności od scenariusza możesz określić zasady przechowywania lub usuwania pewnej liczby kompilacji.

1. W górnej części potoku kompilacji wybierz jego nazwę.

1. Zmień nazwę potoku kompilacji na coś bardziej opisowego, a następnie wybierz pozycję **Zapisz** na liście rozwijanej **Zapisz & kolejki** .

1. W obszarze nazwy potoku kompilacji wybierz pozycję **Historia**. W tym okienku jest wyświetlany dziennik inspekcji ostatnio wprowadzonych zmian w kompilacji. Usługa Azure DevOps śledzi wszelkie zmiany wprowadzone do potoku kompilacji i pozwala na porównanie wersji.

## <a name="examine-the-cd-release-pipeline"></a>Badanie potoku wydania ciągłego wdrażania

Usługa DevOps Projects automatycznie tworzy i konfiguruje kroki wymagane do wdrożenia z organizacji usługi Azure DevOps w ramach subskrypcji platformy Azure. Te kroki obejmują konfigurowanie połączenia usługi platformy Azure w celu uwierzytelniania usługi Azure DevOps w subskrypcji platformy Azure. W ramach automatyzacji jest też tworzony potok wydania, który zapewnia ciągłe wdrażanie na platformie Azure. Aby dowiedzieć się więcej o potoku wydania, wykonaj następujące czynności:

1. Przejdź do pozycji **potoki** i wybierz pozycję **wersje**.

1. Wybierz pozycję **Edit** (Edytuj).

1. W obszarze **Artefakty** wybierz polecenie **Porzuć**. Potok kompilacji przedstawiony w poprzednich krokach generuje dane wyjściowe używane na potrzeby artefaktu.

1. Z prawej strony ikony **rozwijanej** wybierz pozycję **wyzwalacz ciągłego wdrażania**. Ten potok wersji włączył wyzwalacz ciągłego wdrażania, który wykonuje wdrożenie za każdym razem, gdy jest dostępny nowy artefakt kompilacji. Można wyłączyć wyzwalacz, aby wdrożenia były wykonywane ręcznie.

1. Po prawej stronie wybierz pozycję **Wyświetl wersje** , aby wyświetlić historię wersji.

1. Wybierz wersję, która spowoduje wyświetlenie potoku. Wybierz dowolne środowisko, aby sprawdzić podsumowanie wydania, zatwierdzenia lub skojarzone elementy robocze.

1. Wybierz pozycję **Zatwierdzenia**. Ten widok przedstawia zatwierdzenia kodu skojarzone z danym wdrożeniem. Porównaj wydania, aby wyświetlić różnice w zatwierdzeniach pomiędzy wdrożeniami.

1. Wybierz pozycję **Wyświetl dzienniki**. Dzienniki zawierają przydatne informacje na temat procesu wdrażania. Możesz je wyświetlać zarówno podczas wdrażania, jak i po jego zakończeniu.

## <a name="commit-code-changes-and-execute-the-cicd-pipeline"></a>Zatwierdzanie zmian kodu i wykonywanie potoku ciągłej integracji/ciągłego wdrażania

> [!NOTE]
> Poniższa procedura testuje potok ciągłej integracji/ciągłego wdrażania, wprowadzając prostą zmianę tekstu.

Teraz możesz przystąpić do współpracy z zespołem w aplikacji przy użyciu procesu ciągłej integracji/ciągłego wdrażania, który wdraża najnowszą pracę na App Service. Każda zmiana w repozytorium Git rozpoczyna kompilację w usłudze Azure DevOps, a potok ciągłego wdrażania wykonuje wdrażanie na platformie Azure. Wykonaj procedurę opisaną w tej sekcji lub użyj innej techniki, aby zatwierdzić zmiany w repozytorium. Możesz na przykład sklonować repozytorium Git za pomocą ulubionego narzędzia lub środowiska IDE, a następnie wypchnąć zmiany do tego repozytorium.

1. W menu Azure DevOps wybierz kolejno pozycje **repozytoria** i **pliki**. Następnie przejdź do repozytorium.

1. Repozytorium zawiera już kod oparty na języku aplikacji wybranym w procesie tworzenia. Otwórz plik **Application/views/index. Pug** .

1. Wybierz pozycję **Edytuj**, a następnie wprowadź zmiany w **wierszu numer 15**. Można na przykład zmienić ją na "pierwsze wdrożenie, Azure App Service obsługiwane przez Azure Cosmos DB".

1. W prawym górnym rogu wybierz pozycję **Zatwierdź**, a następnie ponownie wybierz pozycję **Zatwierdź** , aby wypchnąć zmianę.

     Po kilku sekundach kompilacja rozpocznie się w usłudze Azure DevOps i zostanie wykonana wersja w celu wdrożenia zmian. Monitoruj stan kompilacji na pulpicie nawigacyjnym usługi DevOps Projects lub w przeglądarce przy użyciu organizacji usługi Azure DevOps.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Usuń powiązane zasoby, które zostały utworzone, gdy nie są już potrzebne. Użyj funkcji **Usuń** na pulpicie nawigacyjnym usługi DevOps Projects.

## <a name="next-steps"></a>Następne kroki

Możesz zmodyfikować potoki kompilacji i wydania, aby dopasować je do potrzeb swojego zespołu. Możesz także użyć wzorca ciągłej integracji/ciągłego wdrażania jako szablonu podczas pracy z innymi potokami. W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Użyj DevOps Projects do wdrożenia aplikacji node. js obsługiwanej przez Azure Cosmos DB
> * Konfigurowanie usługi Azure DevOps i subskrypcji platformy Azure 
> * Sprawdzanie Azure Cosmos DB
> * Badanie potoku ciągłej integracji
> * Badanie potoku ciągłego wdrażania
> * Zatwierdzanie zmian w usłudze Git i automatyczne wdrażanie ich na platformie Azure
> * Oczyszczanie zasobów

Aby uzyskać więcej informacji, zobacz sekcję [Definiowanie potoku ciągłego wdrażania wieloetapowego (CD)](https://docs.microsoft.com/en-us/azure/devops/pipelines/release/define-multistage-release-process?view=azure-devops&viewFallbackFrom=vsts) i następnych kroków.



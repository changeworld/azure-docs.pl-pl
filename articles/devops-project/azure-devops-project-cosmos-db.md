---
title: 'Samouczek: Wdrażanie aplikacji Node.js zasilanych przez usługę Azure Cosmos DB za pomocą projektów devops platformy Azure'
description: Usługa Azure DevOps Projects ułatwia rozpoczęcie pracy na platformie Azure. Dzięki projektom DevOps możesz wdrożyć aplikację Node.js, która jest obsługiwana przez usługę Azure Cosmos DB w aplikacji Windows Web App w kilku szybkich krokach.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/11/2019
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 229b4b9f53ea3866dce1169645f6d6da20827271
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "73888904"
---
# <a name="deploy-nodejs-apps-powered-by-azure-cosmos-db-with-devops-projects"></a>Wdrażanie aplikacji Node.js zasilanych przez usługę Azure Cosmos DB za pomocą projektów DevOps

Usługi Azure DevOps Projects oferuje usprawnione środowisko, w którym można utworzyć potok ciągłej integracji (CI) i ciągłego wdrażania (CD) na platformie Azure. Można to zrobić przy użyciu istniejącego kodu i repozytorium Git (repozytorium) lub wybierając przykładową aplikację.

Usługa DevOps Projects wykonuje również następujące działania:

* Automatyczne tworzenie zasobów platformy Azure, takich jak usługa Azure Cosmos DB, usługa Azure Application Insights, usługa Azure App Service i plany usługi app service

* Tworzy i konfiguruje potok wydania ciągłej integracji/ciągłego wdrażania w usłudze Azure DevOps

W tym samouczku zostaną wykonane następujące czynności:

> [!div class="checklist"]
> * Wdrażanie aplikacji Node.js obsługiwanej przez usługę Azure Cosmos DB za pomocą projektów DevOps
> * Konfigurowanie usługi Azure DevOps i subskrypcji platformy Azure
> * Sprawdź usługę Azure Cosmos DB
> * Badanie potoku ciągłej integracji
> * Badanie potoku ciągłego wdrażania
> * Zatwierdzanie zmian w gicie i automatyczne wdrażanie ich na platformie Azure
> * Oczyszczanie zasobów

## <a name="prerequisites"></a>Wymagania wstępne

Potrzebujesz subskrypcji platformy Azure, którą możesz bezpłatnie uzyskać za pośrednictwem [programu Visual Studio Dev Essentials.](https://visualstudio.microsoft.com/dev-essentials/)

## <a name="use-devops-projects-to-deploy-nodejs-app"></a>Wdrażanie aplikacji Node.js za pomocą projektów DevOps

Usługa DevOps Projects tworzy potok ciągłej integracji/ciągłego wdrażania w usłudze Azure Pipelines. Możesz utworzyć nową organizację usługi Azure DevOps lub użyć istniejącej organizacji. Projekty DevOps tworzy również zasoby platformy Azure, takie jak usługi Azure Cosmos DB, usługa Application Insights, app service i plany usługi app service, w wybranej subskrypcji platformy Azure.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).

1. W okienku po lewej stronie wybierz pozycję **Utwórz zasób**.

1. W polu wyszukiwania wpisz **devops projects**, a następnie wybierz pozycję **Dodaj**.

   ![Okienko Projekty DevOps](_img/azure-devops-project-cosmos-db/devops-project.png)

1. Wybierz **node.js** jako środowisko wykonawcze, a następnie wybierz **przycisk Dalej**. W obszarze **Wybierz strukturę aplikacji**wybierz **express.js**.

1. Włącz **sekcję Dodaj bazę danych** dla **usługi Cosmos DB,** a następnie wybierz pozycję **Dalej**.

    ![Dodawanie bazy danych](_img/azure-devops-project-cosmos-db/add-database.png)

    Projekty programu Azure DevOps obsługują różne struktury aplikacji, takie jak **Express.js**, **przykładowa aplikacja Node.js**i **Sail.js**. W tym samouczku używamy **Express.js**.

1. Wybierz usługę platformy Azure, aby wdrożyć aplikację, a następnie wybierz pozycję **Dalej**. Dostępne opcje obejmują aplikację Windows Web App, usługę Azure Kubernetes i aplikację Azure Web App for Containers. W tym samouczku używamy **aplikacji Windows Web App**.

## <a name="configure-azure-devops-and-azure-subscription"></a>Konfigurowanie usługi Azure DevOps i subskrypcji platformy Azure

1. Wprowadź nazwę projektu usługi Azure DevOps.

1. Utwórz nową organizację usługi Azure DevOps lub wybierz istniejącą organizację.

1. Wybierz swoją subskrypcję platformy Azure.

1. Aby wyświetlić dodatkowe ustawienia konfiguracji platformy Azure lub zidentyfikować warstwę cenową i lokalizację, wybierz opcję **Dodatkowe ustawienia**. W tym okienku są wyświetlane różne opcje konfigurowania warstwy cenowej i lokalizacji usług platformy Azure.

1. Opuść obszar konfiguracji platformy Azure i wybierz pozycję **Gotowe**.

1. Proces kończy się po kilku minutach. Przykładowa aplikacja Node.js jest skonfigurowana w repozytorium Git w organizacji Azure DevOps. Następnie tworzone są zasoby usługi Azure Cosmos DB, App Service, App Service i Application Insights, a także potok ciągłej integracji/ciągłego wdrażania. Aplikacja jest następnie wdrażana na platformie Azure.

   Po zakończeniu wszystkich tych procesów pulpit nawigacyjny programu Azure DevOps Project jest wyświetlany w witrynie Azure portal. Do pulpitu nawigacyjnego usługi DevOps Projects możesz również przejść bezpośrednio z obszaru **Wszystkie zasoby** w witrynie Azure Portal.

   Ten pulpit nawigacyjny zapewnia wgląd w repozytorium kodu usługi Azure DevOps, potoku ciągłej integracji/ciągłego wdrażania oraz bazy danych usługi Azure Cosmos DB. Dodatkowe opcje ciągłej integracji/ciągłego wdrażania możesz skonfigurować w potoku usługi Azure DevOps. Po prawej stronie pulpitu nawigacyjnego wybierz **usługę Azure Cosmos DB,** aby wyświetlić te opcje.

## <a name="examine-azure-cosmos-db"></a>Sprawdź usługę Azure Cosmos DB

DevOps Projects automatycznie konfiguruje usługę Azure Cosmos DB, którą można eksplorować i dostosowywać. Aby zapoznać się z usługą Azure Cosmos DB, wykonaj następujące czynności:

1. Przejdź do pulpitu nawigacyjnego usługi DevOps Projects.

    ![Pulpit nawigacyjny projektów DevOps](_img/azure-devops-project-cosmos-db/devops-project-dashboard.png)

1. Po prawej stronie wybierz pozycję Azure Cosmos DB. Zostanie otwarte okienko dla usługi Azure Cosmos DB. Z tego widoku można wykonywać różne akcje, takie jak monitorowanie operacji i przeszukiwania dzienników.

    ![Okienko usługi Azure Cosmos DB](_img/azure-devops-project-cosmos-db/cosmos-db.png)

## <a name="examine-the-ci-pipeline"></a>Badanie potoku ciągłej integracji

Usługa DevOps Projects automatycznie konfiguruje potok ciągłej integracji/ciągłego wdrażania w organizacji usługi Azure DevOps. Możesz przeglądać i dostosowywać potok. Aby zapoznać się z nim, wykonaj następujące czynności:

1. Przejdź do pulpitu nawigacyjnego usługi DevOps Projects.

1. Wybierz hiperłącze w obszarze **Kompilacja**. Zostanie wyświetlona karta przeglądarki z potokiem kompilacji dla nowego projektu.

    ![Okienko kompilacji](_img/azure-devops-project-cosmos-db/build.png)

1. Wybierz pozycję **Edit** (Edytuj). W tym okienku możesz zapoznać się z różnymi zadaniami w potoku kompilacji. Kompilacja wykonuje różne zadania, takie jak pobieranie kodu źródłowego z repozytorium Git, tworzenie aplikacji, uruchamianie testów jednostkowych i publikowanie danych wyjściowych, które są używane do wdrożeń.

1. Wybierz pozycję **Wyzwalacze**. Usługa DevOps Projects automatycznie tworzy wyzwalacz ciągłej integracji — każde zatwierdzenie w repozytorium uruchamia nową kompilację. Można uwzględnić lub wykluczyć gałęzie z procesu ciągłej integracji.

1. Wybierz pozycję **Przechowywanie**. W zależności od scenariusza możesz określić zasady przechowywania lub usuwania pewnej liczby kompilacji.

1. W górnej części potoku kompilacji wybierz jego nazwę.

1. Zmień nazwę potoku kompilacji na coś bardziej opisowego, a następnie wybierz pozycję **Zapisz** z listy rozwijanej **Zapisz & kolejki.**

1. W obszarze nazwy potoku kompilacji wybierz pozycję **Historia**. W tym okienku jest wyświetlany dziennik inspekcji ostatnio wprowadzonych zmian w kompilacji. Usługa Azure DevOps śledzi wszelkie zmiany wprowadzone do potoku kompilacji i pozwala na porównanie wersji.

## <a name="examine-the-cd-release-pipeline"></a>Badanie potoku wydania ciągłego wdrażania

Usługa DevOps Projects automatycznie tworzy i konfiguruje kroki wymagane do wdrożenia z organizacji usługi Azure DevOps w ramach subskrypcji platformy Azure. Te kroki obejmują konfigurowanie połączenia usługi platformy Azure w celu uwierzytelniania usługi Azure DevOps w subskrypcji platformy Azure. W ramach automatyzacji jest też tworzony potok wydania, który zapewnia ciągłe wdrażanie na platformie Azure. Aby dowiedzieć się więcej o potoku wydania, wykonaj następujące czynności:

1. Przejdź do **opcji Potoki** i wybierz pozycję **Wydania**.

1. Wybierz pozycję **Edit** (Edytuj).

1. W obszarze **Artefakty** wybierz polecenie **Porzuć**. Potok kompilacji przedstawiony w poprzednich krokach generuje dane wyjściowe używane na potrzeby artefaktu.

1. Po prawej stronie ikony **Upuszczanie** wybierz pozycję **Wyzwalacz ciągłego wdrażania**. Ten potok wydania włączył wyzwalacz ciągłego wdrażania, który wykonuje wdrożenie za każdym razem, gdy dostępny jest nowy artefakt kompilacji. Wyzwalacz można wyłączyć, tak aby wdrożenia były wykonywane ręcznie.

1. Po prawej stronie wybierz sekcję **Wyświetl wydania,** aby wyświetlić historię wydań.

1. Wybierz wydanie, w którym będzie wyświetlany potok. Wybierz dowolne środowisko, aby sprawdzić podsumowanie wydania, zatwierdzenia lub skojarzone elementy robocze.

1. Wybierz pozycję **Zatwierdzenia**. Ten widok przedstawia zatwierdzenia kodu skojarzone z danym wdrożeniem. Porównaj wydania, aby wyświetlić różnice w zatwierdzeniach pomiędzy wdrożeniami.

1. Wybierz **pozycję Zobacz dzienniki**. Dzienniki zawierają przydatne informacje na temat procesu wdrażania. Możesz je wyświetlać zarówno podczas wdrażania, jak i po jego zakończeniu.

## <a name="commit-code-changes-and-execute-the-cicd-pipeline"></a>Zatwierdzanie zmian kodu i wykonywanie potoku ciągłej integracji/ciągłego wdrażania

> [!NOTE]
> Poniższa procedura testuje potok ciągłej integracji/ciągłego wdrażania, wprowadzając prostą zmianę tekstu.

Teraz możesz przystąpić do współpracy z zespołem w aplikacji przy użyciu procesu ciągłej integracji/ciągłego wdrażania, który wdraża najnowszą pracę w usłudze App Service. Każda zmiana w repozytorium Git rozpoczyna kompilację w usłudze Azure DevOps, a potok ciągłego wdrażania wykonuje wdrażanie na platformie Azure. Wykonaj procedurę opisaną w tej sekcji lub użyj innej techniki, aby zatwierdzić zmiany w repozytorium. Możesz na przykład sklonować repozytorium Git za pomocą ulubionego narzędzia lub środowiska IDE, a następnie wypchnąć zmiany do tego repozytorium.

1. W menu Azure DevOps wybierz pozycję **Repos,** a następnie **pozycję Pliki**. Następnie przejdź do repozytorium.

1. Repozytorium zawiera już kod na podstawie języka aplikacji wybranego w procesie tworzenia. Otwórz plik **Application/views/index.pug.**

1. Wybierz **pozycję Edytuj**, a następnie zmień numer **wiersza 15**. Na przykład można go zmienić na "Moje pierwsze wdrożenie usługi Azure App Service obsługiwane przez usługę Azure Cosmos DB".

1. W prawym górnym rogu wybierz pozycję **Zatwierdź**, a następnie wybierz pozycję **Zatwierdź** ponownie, aby wcisnąć zmianę.

     Po kilku sekundach rozpoczyna się kompilacja w usłudze Azure DevOps i wykonuje wydanie w celu wdrożenia zmian. Monitoruj stan kompilacji na pulpicie nawigacyjnym usługi DevOps Projects lub w przeglądarce przy użyciu organizacji usługi Azure DevOps.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Usuń powiązane zasoby utworzone, gdy nie są już potrzebne. Użyj funkcji **Usuń** na pulpicie nawigacyjnym usługi DevOps Projects.

## <a name="next-steps"></a>Następne kroki

Możesz zmodyfikować potoki kompilacji i wydania, aby dopasować je do potrzeb swojego zespołu. Możesz także użyć wzorca ciągłej integracji/ciągłego wdrażania jako szablonu podczas pracy z innymi potokami. W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wdrażanie aplikacji Node.js obsługiwanej przez usługę Azure Cosmos DB za pomocą projektów DevOps
> * Konfigurowanie usługi Azure DevOps i subskrypcji platformy Azure 
> * Sprawdź usługę Azure Cosmos DB
> * Badanie potoku ciągłej integracji
> * Badanie potoku ciągłego wdrażania
> * Zatwierdzanie zmian w usłudze Git i automatyczne wdrażanie ich na platformie Azure
> * Oczyszczanie zasobów

Aby uzyskać więcej informacji, zobacz [Definiowanie wieloetapowego potoku ciągłego wdrażania (CD)](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=azure-devops&viewFallbackFrom=vsts) i następnych kroków.



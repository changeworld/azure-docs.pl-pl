---
title: 'Samouczek: Wdrażanie aplikacji Node.js obsługiwane przez usługę Azure Cosmos DB przy użyciu usługi Azure DevOps Projects'
description: Usługa Azure DevOps Projects ułatwia rozpoczęcie pracy na platformie Azure. Usługa DevOps Projects umożliwia wdrażanie aplikacji Node.js obsługiwane przez usługę Azure Cosmos DB do aplikacji sieci Web Windows w kilku prostych krokach.
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/11/2019
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 4310807423600b96078ee48a04a5ad6dab68cd7e
ms.sourcegitcommit: af31deded9b5836057e29b688b994b6c2890aa79
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67813066"
---
# <a name="deploy-nodejs-apps-powered-by-azure-cosmos-db-with-devops-projects"></a>Wdrażanie aplikacji Node.js obsługiwane przez usługę Azure Cosmos DB przy użyciu DevOps Projects

Projekty DevOps platformy Azure oferuje usprawnione środowisko, w którym można przenieść istniejący kod i repozytorium Git lub wybierz przykładową aplikację do tworzenia ciągłej integracji (CI) i ciągłe dostarczanie (CD) potoku do platformy Azure.

Usługa DevOps Projects wykonuje również następujące działania:

* Tworzenie zasobów platformy Azure automatycznie, takich jak Azure Cosmos DB w usłudze Application Insights, App Service i usługi App Service.

* Tworzy i konfiguruje potok tworzenia wersji w DevOps platformy Azure do ciągłej integracji/ciągłego wdrażania

W tym samouczku wykonasz następujące czynności:

> [!div class="checklist"]
> * Usługa DevOps Projects umożliwia wdrażanie aplikacji Node.js, obsługiwane przez usługę Azure Cosmos DB
> * Konfigurowanie usługi Azure DevOps i subskrypcji platformy Azure
> * Sprawdź usługi Azure Cosmos DB
> * Badanie potoku ciągłej integracji
> * Badanie potoku ciągłego wdrażania
> * Zatwierdź zmiany w usłudze Git i automatycznie wdrażać je na platformie Azure
> * Oczyszczanie zasobów

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Możesz uzyskać jeden oferowana bezpłatnie do [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/)

## <a name="use-devops-projects-to-deploy-nodejs-app"></a>Usługa DevOps Projects umożliwia wdrażanie aplikacji Node.js

Usługa DevOps Projects tworzy potok ciągłej integracji/ciągłego wdrażania w usłudze Azure Pipelines. Możesz utworzyć nową organizację usługi Azure DevOps lub użyć istniejącej organizacji. Projekty DevOps tworzy również zasoby platformy Azure, takich jak usługi Azure Cosmos DB, Application Insights, usługa App Service i plan usługi App Service w subskrypcji platformy Azure wybranym.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).

1. W okienku po lewej stronie wybierz właściwą sekcję **Utwórz zasób**.

1. W polu wyszukiwania wpisz **DevOps Projects**, a następnie kliknij przycisk **Dodaj**.

   ![DevOps Projects](_img/azure-devops-project-cosmos-db/devops-project.png)

1. Wybierz **Node.js** jako środowiska uruchomieniowego, a następnie wybierz **dalej**. W obszarze **Wybierz strukturę aplikacji**, wybierz opcję **Express.js**.

1. Włącz sekcji **Dodawanie bazy danych** dla **Cosmos DB** i kliknij pozycję **dalej**.

    ![Dodawanie bazy danych](_img/azure-devops-project-cosmos-db/add-database.png)

    Usługa cosmos DB obsługuje różnych struktur aplikacji, takich jak **Express.js**, **aplikacji Node.js przykładowe**, i **Sail.js**. W tym samouczku umożliwia należy wziąć pod uwagę **Express.js**.

1. Wybierz docelową usługę Azure, aby wdrożyć aplikację. Masz różnych usług, takich jak Windows, aplikacji sieci Web, usługi Kubernetes i aplikację internetową dla kontenerów. W tym samouczku użyjemy **aplikacji sieci Web Windows**. Kliknij pozycję **dalej**.

## <a name="configure-azure-devops-and-azure-subscription"></a>Konfigurowanie DevOps platformy Azure i subskrypcję platformy Azure

1. Wprowadź nazwę projektu usługi Azure DevOps.

1. Utwórz nową organizację usługi Azure DevOps lub wybierz istniejącą organizację.

1. Wybierz swoją subskrypcję platformy Azure.

1. Aby wyświetlić ustawienia dodatkowej konfiguracji platformy Azure i do identyfikowania warstwy cenowej i lokalizację, kliknij przycisk Określ dodatkowe ustawienia. W tym okienku wyświetla różne opcje dotyczące konfigurowania warstwy cenowej i lokalizacji usług platformy Azure.

1. Opuść obszar konfiguracji platformy Azure i wybierz pozycję **Gotowe**.

1. Proces zakończy się po kilku minutach. Przykładową aplikację platformy Node.js jest skonfigurowana w repozytorium Git w Twojej organizacji DevOps platformy Azure, Azure Cosmos DB, usługi App Service, plan usługi App Service i usługi Application Insights są tworzone, potok ciągłej integracji/ciągłego wdrażania jest wykonywany i aplikacja jest wdrażana na platformie Azure.

   Po zakończeniu wszystkich tych operacji zostanie wyświetlony pulpit nawigacyjny projektu usługi Azure DevOps w witrynie Azure Portal. Do pulpitu nawigacyjnego usługi DevOps Projects możesz również przejść bezpośrednio z obszaru **Wszystkie zasoby** w witrynie Azure Portal.

   Ten pulpit nawigacyjny zapewnia wgląd w repozytorium kodu DevOps platformy Azure, potok ciągłej integracji/ciągłego wdrażania i w usłudze Azure Cosmos DB. Dodatkowe opcje ciągłej integracji/ciągłego wdrażania możesz skonfigurować w potoku usługi Azure DevOps. Po prawej stronie, wybierz **usługi Azure Cosmos DB** do wyświetlenia.

## <a name="examine-the-azure-cosmos-db"></a>Sprawdź usługi Azure Cosmos DB

Usługa DevOps Projects automatycznie konfiguruje Cosmos DB, w którym można eksplorować i dostosować. Aby zapoznać się z usługą Cosmos DB, wykonaj następujące czynności:

1. Przejdź do pulpitu nawigacyjnego usługi DevOps Projects.

    ![Pulpit nawigacyjny projekty DevOps](_img/azure-devops-project-cosmos-db/devops-project-dashboard.png)

1. Po prawej stronie wybierz usługę Cosmos DB. Zostanie otwarte okienko usługi Cosmos DB. W tym widoku można wykonywać różne akcje, takie jak operacje monitorowania i przeszukiwania dzienników.

    ![Aplikacja usługi Functions](_img/azure-devops-project-cosmos-db/cosmos-db.png)

## <a name="examine-the-ci-pipeline"></a>Badanie potoku ciągłej integracji

Usługa DevOps Projects automatycznie konfiguruje potok ciągłej integracji/ciągłego wdrażania w organizacji usługi Azure DevOps. Możesz przeglądać i dostosowywać potok. Aby zapoznać się z nim, wykonaj następujące czynności:

1. Przejdź do pulpitu nawigacyjnego usługi DevOps Projects.

1. Kliknij hiperlink w obszarze **kompilacji**. Zostanie wyświetlona karta przeglądarki z potokiem kompilacji dla nowego projektu.

    ![Kompilacja](_img/azure-devops-project-cosmos-db/build.png)

1. Wybierz pozycję **Edit** (Edytuj). W tym okienku możesz zapoznać się z różnymi zadaniami w potoku kompilacji. Kompilacja wykonuje różne zadania, takie jak pobieranie kodu źródłowego z repozytorium Git, tworzenie aplikacji i Uruchamianie testów jednostkowych i publikowanie danych wyjściowych, które są używane w przypadku wdrożeń.

1. Wybierz pozycję **Wyzwalacze**. Usługa DevOps Projects automatycznie tworzy wyzwalacz ciągłej integracji — każde zatwierdzenie w repozytorium uruchamia nową kompilację. Opcjonalnie możesz zdecydować się dołączyć gałęzie do procesu ciągłej integracji lub wykluczyć je z niego.

1. Wybierz pozycję **Przechowywanie**. W zależności od scenariusza możesz określić zasady przechowywania lub usuwania pewnej liczby kompilacji.

1. W górnej części potoku kompilacji wybierz jego nazwę.

1. Zmień nazwę potoku kompilacji na bardziej opisową, a następnie wybierz **Zapisz** z **Zapisz k & olejką** listy rozwijanej.

1. W obszarze nazwy potoku kompilacji wybierz pozycję **Historia**. W tym okienku jest wyświetlany dziennik inspekcji ostatnio wprowadzonych zmian w kompilacji. Usługa Azure DevOps śledzi wszelkie zmiany wprowadzone do potoku kompilacji i pozwala na porównanie wersji.

## <a name="examine-the-cd-release-pipeline"></a>Badanie potoku wydania ciągłego wdrażania

Usługa DevOps Projects automatycznie tworzy i konfiguruje kroki wymagane do wdrożenia z organizacji usługi Azure DevOps w ramach subskrypcji platformy Azure. Te kroki obejmują konfigurowanie połączenia usługi platformy Azure w celu uwierzytelniania usługi Azure DevOps w subskrypcji platformy Azure. W ramach automatyzacji jest też tworzony potok wydania, który zapewnia ciągłe wdrażanie na platformie Azure. Aby dowiedzieć się więcej o potoku wydania, wykonaj następujące czynności:

1. Przejdź do **potoki | Wersje**.

1. Kliknij pozycję **Edytuj**.

1. W obszarze **Artefakty** wybierz polecenie **Porzuć**. Potok kompilacji przedstawiony w poprzednich krokach generuje dane wyjściowe używane na potrzeby artefaktu.

1. Z prawej strony ikony **Porzuć** wybierz pozycję **Wyzwalacz ciągłego wdrażania**. Ten potok wersji ma włączony wyzwalacz ciągłego wdrażania, które wykonuje wdrożenia za każdym razem, gdy nowy artefakt kompilacji jest dostępny. Opcjonalnie możesz wyłączyć wyzwalacz. Wtedy wdrożenia będą wymagać ręcznego wykonania.

1. Po prawej stronie, wybierz właściwą sekcję **Wyświetl wersje** Aby wyświetlić historię wersji.

1. Polecenie wersji, co spowoduje wyświetlenie potoku. Kliknij w dowolnym środowisku, aby sprawdzić wersji **podsumowanie, zatwierdzeń**skojarzonej **elementów roboczych**.

1. Wybierz pozycję **Zatwierdzenia**. Ten widok przedstawia zatwierdzenia kodu skojarzone z danym wdrożeniem. Porównaj wydania, aby wyświetlić różnice w zatwierdzeniach pomiędzy wdrożeniami.

1. Wybierz **wyświetlanie dzienników**. Dzienniki zawierają przydatne informacje na temat procesu wdrażania. Możesz je wyświetlać zarówno podczas wdrażania, jak i po jego zakończeniu.

## <a name="commit-code-changes-and-execute-cicd"></a>Zatwierdzanie zmian kodu i wykonywanie ciągłej integracji/ciągłego wdrażania

> [!NOTE]
> Poniższa procedura testuje potok ciągłej integracji/ciągłego wdrażania, wprowadzając prostą zmianę tekstu.

Teraz możesz przystąpić do współpracy z zespołem w aplikacji przy użyciu procesu ciągłej integracji/ciągłego wdrażania, który automatycznie wdraża najnowsze pracy do usługi Azure App Service. Każda zmiana w repozytorium Git rozpoczyna kompilację w usłudze Azure DevOps, a potok ciągłego wdrażania wykonuje wdrażanie na platformie Azure. Wykonaj procedurę opisaną w tej sekcji lub użyj innej techniki, aby zatwierdzić zmiany w repozytorium. Możesz na przykład sklonować repozytorium Git za pomocą ulubionego narzędzia lub środowiska IDE, a następnie wypchnąć zmiany do tego repozytorium.

1. W menu DevOps platformy Azure wybierz **repozytoriów | Pliki**, a następnie przejdź do repozytorium.

1. Repozytorium zawiera już oparte na języku aplikacji, które wybrano w procesie tworzenia kodu. Otwórz **Application/views/index.pug** pliku.

1. Wybierz **Edytuj**, a następnie wprowadzić zmiany do **15 numer wiersza** . Na przykład, aby zaktualizować **Moje pierwszym wdrożeniu usługi Azure App Service, obsługiwane przez usługę Azure Cosmos DB**

1. W prawym górnym rogu wybierz pozycję **Zatwierdź**, a następnie ponownie wybierz pozycję **Zatwierdź**, aby wypchnąć zmianę.

     Po chwili zostanie uruchomiona kompilacja w usłudze Azure DevOps, a następnie zostanie wykonane wydanie w celu wdrożenia zmian. Monitoruj stan kompilacji na pulpicie nawigacyjnym usługi DevOps Projects lub w przeglądarce przy użyciu organizacji usługi Azure DevOps.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Możesz usunąć powiązane zasoby, które zostały utworzone podczas możesz nie są już potrzebne. Użyj funkcji **Usuń** na pulpicie nawigacyjnym usługi DevOps Projects.

## <a name="next-steps"></a>Następne kroki

Opcjonalnie możesz zmodyfikować potoki kompilacji i wydania, aby zaspokoić potrzeby swojego zespołu. Możesz także użyć wzorca ciągłej integracji/ciągłego wdrażania jako szablonu podczas pracy z innymi potokami. W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Usługa DevOps Projects umożliwia wdrażanie aplikacji Node.js, obsługiwane przez usługę Azure Cosmos DB
> * Konfigurowanie usługi Azure DevOps i subskrypcji platformy Azure 
> * Sprawdź usługi Azure Cosmos DB
> * Badanie potoku ciągłej integracji
> * Badanie potoku ciągłego wdrażania
> * Zatwierdzanie zmian w usłudze Git i automatyczne wdrażanie ich na platformie Azure
> * Oczyszczanie zasobów

---
title: 'Samouczek: tworzenie potoku ciągłej integracji/ciągłego wdrażania dla istniejącego kodu za pomocą usługi Azure DevOps Projects'
description: Usługa Azure DevOps Projects ułatwia rozpoczęcie pracy na platformie Azure. W kilku prostych krokach DevOps Projects pomóc Ci w użyciu własnego kodu i repozytorium GitHub do uruchamiania aplikacji w usłudze platformy Azure.
services: vsts
documentationcenter: vs-devops-build
ms.author: mlearned
ms.manager: gwallace
editor: ''
ms.assetid: ''
ms.workload: web
ms.prod: devops
ms.technology: devops-cicd
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 06/27/2019
author: mlearned
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: a0496999a9a5dc3e9bfd57df0ec035e6db77d620
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615123"
---
# <a name="tutorial-create-a-cicd-pipeline-for-your-existing-code-by-using-azure-devops-projects"></a>Samouczek: tworzenie potoku ciągłej integracji/ciągłego wdrażania dla istniejącego kodu za pomocą usługi Azure DevOps Projects

Azure DevOps Projects przedstawia uproszczony proces tworzenia potoku ciągłej integracji i ciągłego dostarczania na platformę Azure. Możesz przenieść istniejący kod i repozytorium Git lub wybrać przykładową aplikację.

Wykonasz następujące zadania:

> [!div class="checklist"]
> * Tworzenie potoku ciągłej integracji/ciągłego wdrażania za pomocą usługi DevOps Projects
> * Konfigurowanie dostępu do repozytorium GitHub i wybieranie platformy
> * Konfigurowanie usługi Azure DevOps i subskrypcji platformy Azure 
> * Zatwierdzanie zmian w usłudze GitHub i automatyczne wdrażanie ich na platformie Azure
> * Badanie potoku ciągłej integracji/ciągłego wdrażania usługi Azure Pipelines
> * Oczyszczanie zasobów

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Możesz uzyskać ją bezpłatnie za pośrednictwem programu [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).
* Dostęp do usługi GitHub lub zewnętrznego repozytorium git, które zawiera .NET, Java, PHP, Node. js, Python lub statyczny kod internetowy.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Usługa Azure DevOps Projects tworzy potok ciągłej integracji/ciągłego wdrażania w usłudze Azure Pipelines. Możesz utworzyć nową organizację usługi Azure DevOps lub użyć istniejącej organizacji. Usługa Azure DevOps Projects tworzy również zasoby platformy Azure w wybranej przez użytkownika subskrypcji platformy Azure.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Z menu Azure Portal wybierz pozycję **Utwórz zasób**.

   ![Menu Azure Portal — tworzenie zasobu](_img/azure-devops-project-github/createaresource.png)

3. Wybierz pozycję **DevOps** > **DevOps Project**.

   ![Pulpit nawigacyjny usługi DevOps Projects](_img/azure-devops-project-github/azuredashboard.png)

1. Wybierz pozycję **Dodaj własny kod**, a następnie wybierz pozycję **Dalej**.

## <a name="configure-access-to-your-github-repo-and-select-a-framework"></a>Konfigurowanie dostępu do repozytorium GitHub i wybieranie struktury

1. Wybierz pozycję **GitHub** lub zewnętrzne repozytorium kodu **git** . Na potrzeby tego samouczka wybierz pozycję **GitHub**. Może być wymagane uwierzytelnienie za pomocą usługi GitHub po raz pierwszy, aby umożliwić systemowi Azure dostęp do repozytorium GitHub.

1. Wybierz **repozytorium** i **gałąź**, a następnie wybierz przycisk **dalej**.

1. Jeśli używasz kontenerów platformy Docker, Zmień **wartość na Dockerized aplikacji** na **tak**. Na potrzeby tego samouczka **nie** zaznaczaj żadnych opcji, a następnie wybierz przycisk **dalej**. Aby uzyskać więcej informacji o używaniu kontenerów platformy Docker, umieść kursor nad ikoną **i** .

   ![Wybór struktury aplikacji w menu rozwijanym](_img/azure-devops-project-github/appframework.png)

1. Z menu rozwijanego wybierz **środowisko uruchomieniowe aplikacji** i **strukturę aplikacji**, a następnie wybierz przycisk **dalej**. Platforma aplikacji określa typ dostępnego miejsca docelowego wdrożenia usługi platformy Azure.

1. Wybierz **usługę platformy Azure** , aby wdrożyć aplikację, a następnie wybierz pozycję **dalej**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurowanie usługi Azure DevOps i subskrypcji platformy Azure

1. Wprowadź nazwę dla **nazwy projektu**.

1. Utwórz nową bezpłatną organizację w **usłudze Azure DevOps Organization** lub wybierz istniejącą organizację z menu rozwijanego.

1. Wybierz swoją subskrypcję w **subskrypcji platformy Azure**, a następnie wprowadź nazwę w **aplikacji sieci Web** lub użyj wartości domyślnej. Wybierz **lokalizację**, a następnie wybierz pozycję **gotowe**. Po kilku minutach przegląd wdrożenia DevOps Projects zostanie wyświetlony w Azure Portal.

1. Wybierz pozycję **Przejdź do zasobu** , aby wyświetlić pulpit nawigacyjny DevOps Projects. W prawym górnym rogu Przypnij **projekt** do pulpitu nawigacyjnego, aby uzyskać szybki dostęp. Usługa Azure DevOps Projects automatycznie konfiguruje wyzwalacz kompilacji i wydania ciągłej integracji. Twój kod pozostaje w repozytorium GitHub lub innym repozytorium zewnętrznym, a Przykładowa aplikacja jest skonfigurowana w repozytorium w **organizacji usługi Azure DevOps**. Azure DevOps Projects uruchamia kompilację i wdraża aplikację na platformie Azure.

   ![Widok pulpitu nawigacyjnego Azure DevOps Projects](_img/azure-devops-project-github/projectsdashboard.png)

1. Na pulpicie nawigacyjnym zostanie wyświetlone repozytorium kodu, potok ciągłej integracji/ciągłego wdrażania oraz Twoja aplikacja na platformie Azure. Po prawej stronie w obszarze zasoby platformy Azure wybierz pozycję **Przeglądaj** , aby wyświetlić uruchomioną aplikację.

## <a name="commit-changes-to-github-and-automatically-deploy-them-to-azure"></a>Zatwierdzanie zmian w usłudze GitHub i automatyczne wdrażanie ich na platformie Azure

Teraz możesz przystąpić do współpracy nad swoją aplikacją z zespołem. Proces ciągłej integracji/ciągłego wdrażania automatycznie wdraża najnowszą swoją służbę w witrynie sieci Web. Każda zmiana repozytorium GitHub uruchamia kompilację w usłudze Azure DevOps, a potok CD uruchamia wdrożenie na platformie Azure.

1. Na pulpicie nawigacyjnym DevOps Projects wybierz pozycję **repozytoria**. Repozytorium GitHub zostanie otwarte na nowej karcie przeglądarki. Wprowadź zmiany w aplikacji, a następnie wybierz pozycję **Zatwierdź zmiany**.

1. Po kilku chwilach rozpocznie się kompilacja w usłudze Azure Pipelines. Stan kompilacji można monitorować na pulpicie nawigacyjnym DevOps Projects. Możesz również monitorować je w organizacji usługi Azure DevOps, wybierając kartę **kompilacja potoków** na pulpicie nawigacyjnym DevOps Projects.

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>Badanie potoku ciągłej integracji/ciągłego wdrażania usługi Azure Pipelines

Usługa Azure DevOps Projects automatycznie konfiguruje potok ciągłej integracji/ciągłego wdrażania w usłudze Azure Pipelines. Możesz przeglądać i dostosowywać potok według potrzeb. Aby zapoznać się z potokami kompilacji i wydania, wykonaj następujące czynności:

1. Na pulpicie nawigacyjnym DevOps Projects wybierz pozycję **potoki kompilacji**.

1. Po otwarciu strony **Azure Pipelines** zobaczysz historię najnowszych kompilacji i stan dla każdej kompilacji.

   ![Strona kompilacje Azure Pipelines](_img/azure-devops-project-github/pipelinesbuildpage.png)

1. W prawym górnym rogu strony **kompilacje** możesz wybrać pozycję **Edytuj** , aby zmienić bieżącą kompilację, **kolejkę** , dodać nową kompilację lub przycisk pionowy wielokropek ( **&#8942;** ), aby otworzyć menu z więcej opcji. Wybierz pozycję **Edit** (Edytuj).

1. Kompilacja wykonuje różne zadania, takie jak pobieranie źródeł z repozytorium, przywracanie zależności i publikowanie danych wyjściowych dla wdrożeń. Po prawej stronie w polu **Nazwa**Zmień nazwę potoku kompilacji na coś bardziej opisowego. Wybierz pozycję **zapisz & kolejkę**, a następnie wybierz pozycję **Zapisz**. Wprowadź komentarz, a następnie wybierz pozycję **Zapisz** ponownie.

   ![Strona kompilacji usługi Azure DevOps](_img/azure-devops-project-github/buildpage.png)

1. Aby wyświetlić dziennik inspekcji ostatnich zmian dla kompilacji, wybierz kartę **historia** .  Usługa Azure DevOps śledzi wszelkie zmiany wprowadzone w potoku kompilacji i umożliwia porównanie wersji.

1. Wybierz kartę **wyzwalacze** . Azure DevOps projects automatycznie tworzy wyzwalacz elementu konfiguracji z niektórymi ustawieniami domyślnymi. Można ustawić wyzwalacze, takie jak **włączenie ciągłej integracji** , aby uruchamiać kompilację za każdym razem, gdy zatwierdzisz zmianę kodu. Możesz również ustawić wyzwalacze, aby zaplanować uruchamianie kompilacji o określonych godzinach.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy Azure App Service i powiązane zasoby, które zostały utworzone w tym samouczku, nie są już potrzebne, można je usunąć. Użyj funkcji **Usuń** na pulpicie nawigacyjnym usługi DevOps Projects.

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu procesu ciągłej integracji/ciągłego wdrażania w ramach tego samouczka automatycznie utworzysz potok kompilacja i wydanie w Azure DevOps Projects. Możesz zmodyfikować potoki kompilacji i wydania, aby dopasować je do potrzeb swojego zespołu.

Aby dowiedzieć się więcej na temat potoku ciągłej integracji/ciągłego wdrażania, zobacz:

> [!div class="nextstepaction"]
> [Definiowanie wieloetapowego potoku ciągłego wdrażania](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

Aby dowiedzieć się więcej o monitorowaniu aplikacji, zobacz:
  
 > [!div class="nextstepaction"]
 > [Co to jest usługa Azure monitor?](https://docs.microsoft.com/azure/azure-monitor/overview)

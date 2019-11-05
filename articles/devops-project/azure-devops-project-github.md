---
title: 'Samouczek: tworzenie potoku ciągłej integracji/ciągłego wdrażania dla istniejącego kodu za pomocą usługi Azure DevOps Projects'
description: Usługa Azure DevOps Projects ułatwia rozpoczęcie pracy na platformie Azure. Pomaga korzystać z własnego kodu i repozytorium GitHub w celu uruchomienia aplikacji w wybranej przez użytkownika usłudze platformy Azure w kilku prostych krokach.
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
ms.openlocfilehash: 15ac201a078864717d8e0079801507cf5fc0fe3b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73481094"
---
# <a name="tutorial-create-a-cicd-pipeline-for-your-existing-code-by-using-azure-devops-projects"></a>Samouczek: tworzenie potoku ciągłej integracji/ciągłego wdrażania dla istniejącego kodu za pomocą usługi Azure DevOps Projects

Usługa Azure DevOps Projects stanowi uproszczone środowisko, do którego możesz przenieść istniejący kod i repozytorium Git lub wybrać aplikację przykładową, aby utworzyć potok ciągłej integracji i ciągłego dostarczania na platformie Azure.

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
* Uzyskaj dostęp do usługi GitHub lub zewnętrznego repozytorium Git, które zawiera kod .NET, Java, PHP, Node, Python lub statyczny kod stron internetowych.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Usługa Azure DevOps Projects tworzy potok ciągłej integracji/ciągłego wdrażania w usłudze Azure Pipelines. Możesz utworzyć nową organizację usługi Azure DevOps lub użyć istniejącej organizacji. Usługa Azure DevOps Projects tworzy również zasoby platformy Azure w wybranej przez użytkownika subskrypcji platformy Azure.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Z menu Azure Portal wybierz pozycję **Utwórz zasób**.

   ![Menu Azure Portal — tworzenie zasobu](_img/azure-devops-project-github/createaresource.png)

3. Wybierz pozycję **DevOps > DevOps Project**.

   ![Pulpit nawigacyjny usługi DevOps Projects](_img/azure-devops-project-github/azuredashboard.png)

3. Wybierz pozycję **Przenieś własny kod**, a następnie wybierz przycisk **dalej**.

## <a name="configure-access-to-your-github-repo-and-choose-a-framework"></a>Konfigurowanie dostępu do repozytorium GitHub i wybieranie platformy

1. Wybierz pozycję **GitHub** lub zewnętrzne repozytorium kodu **git** . Na potrzeby tego samouczka wybierz pozycję **GitHub**. Może być wymagane uwierzytelnienie za pomocą usługi GitHub po raz pierwszy, aby umożliwić systemowi Azure dostęp do repozytorium GitHub.

2. Zakończ, wybierając **repozytorium** i **gałąź**, a następnie wybierz pozycję **dalej**.

3. Jeśli używasz usługi Docker Containers Change **to App Dockerized** to **Yes**, w przypadku tego samouczka **nie** należy wybierać **pozycji dalej**. Aby uzyskać więcej informacji na temat używania kontenerów platformy Docker na ikonie **i** .

   ![Struktura .NET](_img/azure-devops-project-github/appframework.png)

4. Z listy rozwijanej wybierz **środowisko uruchomieniowe** i **strukturę**aplikacji, a następnie wybierz przycisk **dalej**. Wybrana struktura aplikacji określa typ dostępnego miejsca docelowego wdrożenia usługi platformy Azure.

5. Wybierz **usługę platformy Azure** , aby wdrożyć aplikację, a następnie wybierz pozycję **dalej**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurowanie usługi Azure DevOps i subskrypcji platformy Azure

1. Wprowadź **nazwę projektu**.

2. Utwórz nową organizację bezpłatnej **usługi Azure DevOps** lub wybierz istniejącą organizację z listy rozwijanej.

3. Wybierz swoją **subskrypcję platformy Azure**, wprowadź nazwę **aplikacji sieci Web** lub zapoznaj się z wartością domyślną. Wybierz **lokalizację**, a następnie wybierz pozycję **gotowe**. Po kilku minutach przegląd wdrożenia projektu DevOps zostanie wyświetlony w Azure Portal.

4. Wybierz pozycję **Przejdź do zasobu** , aby wyświetlić pulpit nawigacyjny projektu DevOps. W prawym górnym rogu Przypnij **projekt** do pulpitu nawigacyjnego, aby uzyskać szybki dostęp. Usługa Azure DevOps Projects automatycznie konfiguruje wyzwalacz kompilacji i wydania ciągłej integracji. Twój kod pozostaje w repozytorium GitHub lub innym zewnętrznym repozytorium. Przykładowa aplikacja jest skonfigurowana w repozytorium w **organizacji usługi Azure DevOps**. Kompilacja jest wykonywana, a Twoja aplikacja jest wdrażana na platformie Azure.

   ![Widok pulpitu nawigacyjnego usługi DevOps Projects](_img/azure-devops-project-github/projectsdashboard.png)

5. Pulpit nawigacyjny zapewnia wgląd do repozytorium kodu, potoku ciągłej integracji/ciągłego wdrażania i aplikacji na platformie Azure. W obszarze zasoby platformy Azure wybierz pozycję **Przeglądaj** , aby wyświetlić uruchomioną aplikację.

## <a name="commit-changes-to-github-and-automatically-deploy-them-to-azure"></a>Zatwierdzanie zmian w usłudze GitHub i automatyczne wdrażanie ich na platformie Azure

Teraz możesz rozpocząć współpracę z zespołem nad aplikacją w języku Python w ramach procesu ciągłej integracji/ciągłego wdrażania, który automatycznie wdraża najnowszy kod w witrynie internetowej. Każda zmiana w repozytorium GitHub rozpoczyna kompilację w usłudze Azure DevOps, a potok ciągłego wdrażania wykonuje wdrażanie na platformie Azure.

1. Na pulpicie nawigacyjnym projektu DevOps wybierz pozycję **repozytoria**. Repozytorium GitHub zostanie otwarte na nowej karcie przeglądarki. Wprowadź zmiany w aplikacji, a następnie kliknij przycisk **Zatwierdź zmiany**.

2. Po kilku chwilach rozpocznie się kompilacja w usłudze Azure Pipelines. Możesz monitorować stan kompilacji na pulpicie nawigacyjnym DevOps Projects lub monitorować go w organizacji usługi Azure DevOps, wybierając kartę **kompilacja potoków** na pulpicie nawigacyjnym projektu.

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>Badanie potoku ciągłej integracji/ciągłego wdrażania usługi Azure Pipelines

Usługa Azure DevOps Projects automatycznie konfiguruje potok ciągłej integracji/ciągłego wdrażania w usłudze Azure Pipelines. Możesz przeglądać i dostosowywać potok według potrzeb. Aby zapoznać się z potokami kompilacji i wydania, wykonaj następujące czynności:

1. Na pulpicie nawigacyjnym DevOps Projects wybierz pozycję **potoki kompilacji**.

2. Gdy zostanie otwarta strona **Azure Pipelines** , zobaczysz historię najnowszych kompilacji i stan dla każdej kompilacji.

   ![Kompilacje potokowe usługi Azure DevOps](_img/azure-devops-project-github/pipelinesbuildpage.png)

3. W prawym górnym rogu strony **kompilacje** zobaczysz opcje **edycji** bieżącej kompilacji, **kolejki** , aby umieścić nową kompilację w kolejce, a wielokropek ( **&#8942;** ), aby otworzyć menu z więcej opcji, wybierz pozycję **Edytuj**.

4. Kompilacja wykonuje różne zadania, takie jak pobieranie źródeł z repozytorium, przywracanie zależności i publikowanie danych wyjściowych dla wdrożeń. W polu **Nazwa**, Zmień nazwę potoku kompilacji na bardziej opisową. Wybierz pozycję **zapisz & kolejkę**, a następnie pozycję **Zapisz**, pozostaw komentarz, a następnie wybierz pozycję **Zapisz** ponownie.

   ![Strona kompilacji usługi Azure DevOps](_img/azure-devops-project-github/buildpage.png)

5. Aby wyświetlić dziennik inspekcji ostatnich zmian dla kompilacji, wybierz kartę **historia** . usługa Azure DevOps śledzi wszelkie zmiany wprowadzone w potoku kompilacji i umożliwia porównanie wersji.

6. Wybierz kartę **wyzwalacze** . projekt usługi Azure DevOps automatycznie tworzy wyzwalacz elementu konfiguracji z niektórymi ustawieniami domyślnymi. Wyzwalacze, takie jak **Włącz integrację ciągłą** , można ustawić tak, aby wykonywały kompilację za każdym razem, gdy zmiana kodu została zatwierdzona, lub planowanie kompilacji do uruchomienia o określonych godzinach.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy usługa Azure App Service i powiązane zasoby utworzone w tym samouczku nie będą już potrzebne, możesz je usunąć. W tym celu użyj funkcji **Usuń** na pulpicie nawigacyjnym usługi DevOps Projects.

## <a name="next-steps"></a>Następne kroki

Podczas pracy z tym samouczkiem skonfigurowano proces ciągłej integracji/ciągłego wdrażania oraz utworzono automatycznie potok kompilacji i wydania w usłudze Azure DevOps Projects. Możesz zmodyfikować potoki kompilacji i wydania, aby dopasować je do potrzeb swojego zespołu. W tym samouczku omówiono:

> [!div class="checklist"]
>  * Tworzenie potoku ciągłej integracji/ciągłego wdrażania za pomocą usługi DevOps Projects
> * Konfigurowanie dostępu do repozytorium GitHub i wybieranie platformy
> * Konfigurowanie usługi Azure DevOps i subskrypcji platformy Azure
> * Zatwierdzanie zmian w usłudze GitHub i automatyczne wdrażanie ich na platformie Azure
> * Badanie potoku ciągłej integracji/ciągłego wdrażania usługi Azure Pipelines
> * Oczyszczanie zasobów

Aby dowiedzieć się więcej na temat potoku ciągłej integracji/ciągłego wdrażania, zobacz:

> [!div class="nextstepaction"]
> [Definiowanie wieloetapowego potoku ciągłego wdrażania](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

Aby dowiedzieć się więcej o monitorowaniu aplikacji, zobacz:
  
 > [!div class="nextstepaction"]
 > [Co to jest usługa Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview)

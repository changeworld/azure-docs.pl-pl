---
title: 'Samouczek: tworzenie potoku ciągłej integracji/ciągłego wdrażania dla istniejącego kodu za pomocą usługi Azure DevOps Projects'
description: Usługa Azure DevOps Projects ułatwia rozpoczęcie pracy na platformie Azure. W kilku szybkich krokach devops projects pomaga używać własnego kodu i repozytorium GitHub do uruchamiania aplikacji w usłudze Platformy Azure.
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "73615123"
---
# <a name="tutorial-create-a-cicd-pipeline-for-your-existing-code-by-using-azure-devops-projects"></a>Samouczek: tworzenie potoku ciągłej integracji/ciągłego wdrażania dla istniejącego kodu za pomocą usługi Azure DevOps Projects

Projekty usługi Azure DevOps przedstawia uproszczony proces tworzenia potoku ciągłej integracji (CI) i ciągłego dostarczania (CD) na platformie Azure. Możesz przynieść istniejący kod i repozytorium Git lub wybrać przykładową aplikację.

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
* Dostęp do gitHub lub zewnętrznego repozytorium Git, które zawiera .NET, Java, PHP, Node.js, Python lub statyczny kod sieci web.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Usługa Azure DevOps Projects tworzy potok ciągłej integracji/ciągłego wdrażania w usłudze Azure Pipelines. Możesz utworzyć nową organizację usługi Azure DevOps lub użyć istniejącej organizacji. Usługa Azure DevOps Projects tworzy również zasoby platformy Azure w wybranej przez użytkownika subskrypcji platformy Azure.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).

2. Z menu Portalu platformy Azure wybierz polecenie **Utwórz zasób**.

   ![Menu portalu platformy Azure — tworzenie zasobu](_img/azure-devops-project-github/createaresource.png)

3. Wybierz **devops** > **devops projektu**.

   ![Pulpit nawigacyjny usługi DevOps Projects](_img/azure-devops-project-github/azuredashboard.png)

1. Wybierz pozycję **Dodaj własny kod**, a następnie wybierz pozycję **Dalej**.

## <a name="configure-access-to-your-github-repo-and-select-a-framework"></a>Konfigurowanie dostępu do repozytorium GitHub i wybieranie struktury

1. Wybierz **gitHub** lub zewnętrzne repozytorium kodu **Git.** W tym samouczku wybierz **gitHub**. Może być wymagane uwierzytelnienie za pomocą usługi GitHub po raz pierwszy, aby umożliwić platformie Azure dostęp do repozytorium Usługi GitHub.

1. Wybierz **repozytorium** i **gałąź**, a następnie wybierz pozycję **Dalej**.

1. Jeśli używasz kontenerów platformy Docker, zmień **czy aplikacja Jest dockerized** na **TAK**. W tym samouczku pozostaw **opcję NIE** zaznaczona, a następnie wybierz pozycję **Dalej**. Aby uzyskać więcej informacji na temat korzystania z kontenerów platformy Docker, umieść wskaźnik myszy na ikonie **i.**

   ![Wybór struktury aplikacji w menu rozwijanym](_img/azure-devops-project-github/appframework.png)

1. Z menu rozwijanych wybierz **środowisko wykonawcze aplikacji** i **strukturę aplikacji,** a następnie wybierz pozycję **Dalej**. Struktura aplikacji określa typ docelowego wdrożenia usługi platformy Azure, który jest dostępny.

1. Wybierz **usługę platformy Azure,** aby wdrożyć aplikację, a następnie wybierz pozycję **Dalej**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurowanie usługi Azure DevOps i subskrypcji platformy Azure

1. Wprowadź nazwę **nazwy programu Project**.

1. Utwórz nową bezpłatną organizację w **usłudze Azure DevOps Organization** lub wybierz istniejącą organizację z menu rozwijanego.

1. Wybierz subskrypcję w **usłudze Subskrypcja platformy Azure**i wprowadź nazwę w **aplikacji sieci Web** lub użyj wartości domyślnej. Wybierz **pozycję Lokalizacja**, a następnie wybierz pozycję **Gotowe**. Po kilku minutach omówienie wdrażania projektów DevOps jest wyświetlane w witrynie Azure portal.

1. Wybierz **pozycję Przejdź do zasobu,** aby wyświetlić pulpit nawigacyjny Projektów DevOps. W prawym górnym rogu przypnij **projekt** do pulpitu nawigacyjnego, aby uzyskać szybki dostęp. Usługa Azure DevOps Projects automatycznie konfiguruje wyzwalacz kompilacji i wydania ciągłej integracji. Kod pozostaje w repozytorium GitHub lub innym zewnętrznym repozytorium, a przykładowa aplikacja jest skonfigurowana w repozytorium w **organizacji Azure DevOps.** Projekty DevOps platformy Azure uruchamia kompilację i wdraża aplikację na platformie Azure.

   ![Widok pulpitu nawigacyjnego projektów usługi Azure DevOps](_img/azure-devops-project-github/projectsdashboard.png)

1. Pulpit nawigacyjny pokazuje repozytorium kodu, potok ciągłej integracji/ciągłego wdrażania i aplikacji na platformie Azure. Po prawej stronie w obszarze Zasoby platformy Azure wybierz pozycję **Przeglądaj,** aby wyświetlić uruchomiającą aplikację.

## <a name="commit-changes-to-github-and-automatically-deploy-them-to-azure"></a>Zatwierdzanie zmian w usłudze GitHub i automatyczne wdrażanie ich na platformie Azure

Teraz możesz współpracować nad aplikacją z zespołem. Proces ciągłej integracji/ciągłego wdrażania automatycznie wdraża najnowszą pracę w witrynie sieci Web. Każda zmiana repozytorium GitHub uruchamia kompilację w usłudze Azure DevOps, a potok dysku CD uruchamia wdrożenie na platformie Azure.

1. Na pulpicie nawigacyjnym projektów DevOps wybierz pozycję **Repozytoria**. Repozytorium GitHub zostanie otwarte na nowej karcie przeglądarki. **Commit changes**

1. Po kilku chwilach rozpocznie się kompilacja w usłudze Azure Pipelines. Stan kompilacji można monitorować na pulpicie nawigacyjnym Projektów DevOps. Można również monitorować go w organizacji Azure DevOps, wybierając **kompilacji potoków** kartę z pulpitu nawigacyjnego DevOps Projects.

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>Badanie potoku ciągłej integracji/ciągłego wdrażania usługi Azure Pipelines

Usługa Azure DevOps Projects automatycznie konfiguruje potok ciągłej integracji/ciągłego wdrażania w usłudze Azure Pipelines. Możesz przeglądać i dostosowywać potok według potrzeb. Aby zapoznać się z potokami kompilacji i wydania, wykonaj następujące czynności:

1. Na pulpicie nawigacyjnym Projektów DevOps wybierz pozycję **Buduj potoki**.

1. Po otwarciu strony **usługi Azure Potoki** zobaczysz historię najnowszych kompilacji i stan dla każdej kompilacji.

   ![Strona kompilacji potoków platformy Azure](_img/azure-devops-project-github/pipelinesbuildpage.png)

1. W prawym górnym rogu strony **Kompilacje** można wybrać opcję **Edytuj,** aby zmienić bieżącą kompilację, **Kolejka,** aby dodać nową kompilację, lub pionowy przycisk wielokropka** (&#8942;**), aby otworzyć menu z większą liczesz opcjami. Wybierz pozycję **Edit** (Edytuj).

1. Kompilacja wykonuje różne zadania, takie jak pobieranie źródeł z repozytorium, przywracanie zależności i publikowanie danych wyjściowych dla wdrożeń. Po prawej stronie w obszarze **Nazwa**zmień nazwę potoku kompilacji na coś bardziej opisowego. Wybierz **pozycję Zapisz & kolejki,** a następnie wybierz pozycję **Zapisz**. Wprowadź komentarz, a następnie wybierz pozycję **Zapisz** ponownie.

   ![Strona kompilacji deweloperów platformy Azure](_img/azure-devops-project-github/buildpage.png)

1. Aby wyświetlić ścieżkę inspekcji ostatnich zmian dla kompilacji, wybierz kartę **Historia.**  Usługa Azure DevOps śledzi wszelkie zmiany wprowadzone w potoku kompilacji i umożliwia porównywanie wersji.

1. Wybierz **wyzwalacze** kartę. Projekty devops platformy Azure automatycznie tworzy wyzwalacz ciągłej integracji z niektórymi ustawieniami domyślnymi. Można ustawić wyzwalacze, takie jak **Włącz ciągłą integrację,** aby uruchamiać kompilację za każdym razem, gdy zatwierdzasz zmianę kodu. Można również ustawić wyzwalacze, aby zaplanować kompilacje do uruchomienia w określonych godzinach.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli usługa Azure App Service nie jest już potrzebna i powiązane zasoby utworzone w tym samouczku, można je usunąć. Użyj funkcji **Usuń** na pulpicie nawigacyjnym usługi DevOps Projects.

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu procesu ciągłej integracji/ciągłego wdrażania w tym samouczku automatycznie utworzono potok kompilacji i wydania w projektach programu Azure DevOps. Możesz zmodyfikować potoki kompilacji i wydania, aby dopasować je do potrzeb swojego zespołu.

Aby dowiedzieć się więcej na temat potoku ciągłej integracji/ciągłego wdrażania, zobacz:

> [!div class="nextstepaction"]
> [Definiowanie wieloetapowego potoku ciągłego wdrażania](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

Aby dowiedzieć się więcej na temat monitorowania aplikacji, zobacz:
  
 > [!div class="nextstepaction"]
 > [Co to jest monitor platformy Azure?](https://docs.microsoft.com/azure/azure-monitor/overview)

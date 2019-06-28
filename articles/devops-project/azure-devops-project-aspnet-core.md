---
title: 'Szybki start: Tworzenie potoku ciągłej integracji/ciągłego wdrażania dla platformy .NET za pomocą usługi DevOps Projects'
description: Usługa Azure DevOps Projects ułatwia rozpoczęcie pracy na platformie Azure. Umożliwia uruchomienie aplikacji .NET w wybranej usłudze platformy Azure w kilku prostych krokach.
ms.prod: devops
ms.technology: devops-cicd
services: azure-devops-project
documentationcenter: vs-devops-build
author: mlearned
manager: douge
editor: ''
ms.assetid: ''
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/09/2018
ms.author: mlearned
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: 387801f2ecb2f5fa1639005726218efb54d75dc8
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2019
ms.locfileid: "67331417"
---
# <a name="create-a-cicd-pipeline-for-net-with-azure-devops-projects"></a>Tworzenie potoku ciągłej integracji/ciągłego wdrażania dla platformy .NET za pomocą usługi DevOps Projects

Skonfiguruj proces ciągłej integracji i ciągłego dostarczania dla aplikacji platformy .NET Core lub ASP.NET w projekcie usługi Azure DevOps. Usługa DevOps Projects ułatwia początkową konfigurację potoku kompilacji i wydania w usłudze Azure Pipelines.

Jeśli nie masz subskrypcji platformy Azure, możesz uzyskać ją bezpłatnie za pośrednictwem programu [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Usługa DevOps Projects tworzy potok ciągłej integracji/ciągłego wdrażania w usłudze Azure DevOps. Możesz utworzyć nową organizację usługi Azure DevOps lub użyć istniejącej organizacji. Usługa DevOps Projects tworzy również zasoby platformy Azure w wybranej subskrypcji platformy Azure.

1. Zaloguj się do [Portalu Microsoft Azure](https://portal.azure.com).

1. W lewym okienku wybierz ikonę **Utwórz zasób** na pasku nawigacyjnym po lewej stronie, a następnie wyszukaj hasło **DevOps Projects**.  

3.  Wybierz pozycję **Utwórz**.

    ![Rozpoczynanie ciągłego dostarczania](_img/azure-devops-project-aspnet-core/fullbrowser.png)

## <a name="select-a-sample-application-and-azure-service"></a>Wybieranie przykładowej aplikacji i usługi platformy Azure

1. Wybierz przykładową aplikację **.NET**. Przykłady platformy .NET umożliwiają wybór struktury ASP.NET typu open source lub międzyplatformowej struktury .NET Core.

   ![Struktura .NET](_img/azure-devops-project-aspnet-core/chooselanguagedotnet.png)

2. Ten przykład to aplikacja ASP.NET Core MVC. Wybierz **platformy .NET Core** strukturę aplikacji, następnie wybierz pozycję **dalej**.    
    
3. Wybierz **aplikacji sieci Web Windows** jako cel wdrożenia, następnie wybierz pozycję **dalej**. Opcjonalnie można wybrać innych usług platformy Azure dla danego wdrożenia. Strukturę aplikacji, która została wybrana opcja wcześniej, decyduje o rodzaju cel wdrożenia usługi platformy Azure dostępne w tym miejscu.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurowanie usługi Azure DevOps i subskrypcji platformy Azure 

1. Wprowadź **Nazwa projektu**.

2. Utwórz bezpłatnie nową **Azure DevOps organizacji** lub wybierz istniejącą organizację z listy rozwijanej.

3. Wybierz swoje **subskrypcji platformy Azure**, wprowadź nazwę swojej **aplikacji sieci Web** lub wykonać domyślne, a następnie wybierz **gotowe**. Po kilku minutach Przegląd wdrażania projektów DevOps jest wyświetlany w witrynie Azure portal. 

4. Wybierz **przejdź do zasobu** do wyświetlania pulpitu nawigacyjnego projekt DevOps. W prawym górnym rogu przypiąć **projektu** do pulpitu nawigacyjnego, aby uzyskać szybki dostęp. Przykładowa aplikacja jest skonfigurowana w repozytorium w swojej **Azure DevOps organizacji**. Kompilacja jest wykonywana, a aplikacja jest wdrażana na platformie Azure.

5. Pulpit nawigacyjny zapewnia wgląd do repozytorium kodu, potoku ciągłej integracji/ciągłego wdrażania i aplikacji na platformie Azure. Po prawej stronie w obszarze zasobów platformy Azure wybierz **Przeglądaj** do wyświetlenia uruchomionej aplikacji.

   ![Widok pulpitu nawigacyjnego](_img/azure-devops-project-aspnet-core/dashboardnopreview.png) 

## <a name="commit-code-changes-and-execute-cicd"></a>Zatwierdzanie zmian kodu i wykonywanie ciągłej integracji/ciągłego wdrażania

Usługa DevOps Projects utworzyła repozytorium Git w usłudze Azure Repos lub GitHub. Aby wyświetlić repozytorium i wprowadzić zmiany w kodzie aplikacji, wykonaj poniższe kroki:

1. Z lewej strony pulpitu nawigacyjnego usługi DevOps Projects wybierz link dla gałęzi **master**. Ten link otwiera widok nowo utworzonego repozytorium Git.

2. W następnych kilku krokach można użyć przeglądarki sieci web w celu podejmowania i zatwierdź zmiany w kodzie bezpośrednio **wzorca** gałęzi. Można również sklonować repozytorium Git w Twoim ulubionym środowisku IDE, wybierając **klonowania** od góry prawej części strony repozytorium. 

3. Po lewej stronie, przejść struktury plików aplikacji do **Application/aspnet-core-dotnet-core/Pages/Index.cshtml**.

4. Wybierz pozycję **Edytuj** i wprowadź zmianę w nagłówku h2. Wpisz na przykład hasło **Zaczynamy pracę z usługą Azure DevOps Projects** lub wprowadź inną zmianę.

      ![Edytowanie kodu](_img/azure-devops-project-aspnet-core/codechange.png)

5. Wybierz **zatwierdzić**, pozostaw komentarz i wybierz **zatwierdzić** ponownie.

6. W przeglądarce przejdź do pulpitu nawigacyjnego projektu usługi Azure DevOps.  W tym momencie powinna być widoczna trwająca kompilacja. Wprowadzone zmiany są automatycznie kompilowane i wdrażane za pośrednictwem potoku ciągłej integracji/ciągłego wdrażania.

## <a name="examine-the-cicd-pipeline"></a>Badanie potoku ciągłej integracji/ciągłego wdrażania

W poprzednim kroku usługa Azure DevOps Projects automatycznie skonfigurowała pełny potok ciągłej integracji/ciągłego wdrażania. Możesz przeglądać i dostosowywać potok według potrzeb. Wykonaj poniższe kroki, aby zapoznać się z potokami kompilacji i wydania usługi Azure DevOps.

1. U góry pulpitu nawigacyjnego usługi DevOps Projects wybierz pozycję **Potoki kompilacji**.  
Ten link otwiera kartę przeglądarki i potok kompilacji usługi Azure DevOps dla nowego projektu.

1. Wybierz symbol wielokropka (...).  Ta akcja powoduje otwarcie menu, w którym możesz uruchomić kilka działań, takich jak dodawanie nowej kompilacji do kolejki, wstrzymywanie kompilacji i edytowanie potoku kompilacji.

1. Wybierz pozycję **Edit** (Edytuj).

    ![Potok kompilacji](_img/azure-devops-project-aspnet-core/builddef.png)

1. W tym okienku możesz zapoznać się z różnymi zadaniami w potoku kompilacji.  
 W ramach kompilacji są wykonywane różne zadania, takie jak pobieranie źródeł z repozytorium Git, przywracanie zależności i publikowanie danych wyjściowych używanych do wdrożenia.

1. W górnej części potoku kompilacji wybierz jego nazwę.

1. Zmień nazwę potoku kompilacji na bardziej opisową, wybierz pozycję **Zapisz i dodaj do kolejki**, a następnie wybierz pozycję **Zapisz**.

1. W obszarze nazwy potoku kompilacji wybierz pozycję **Historia**.   
W okienku **Historia** zostanie wyświetlony dziennik inspekcji zmian ostatnio wprowadzonych w kompilacji.  Usługa Azure Pipelines śledzi wszelkie zmiany wprowadzone do potoku kompilacji i pozwala na porównanie wersji.

1. Wybierz pozycję **Wyzwalacze**.  
Usługa DevOps Projects automatycznie utworzyła wyzwalacz ciągłej integracji — każde zatwierdzenie w repozytorium uruchamia nową kompilację.  Opcjonalnie możesz zdecydować się dołączyć gałęzie do procesu ciągłej integracji lub je wykluczyć.

1. Wybierz pozycję **Przechowywanie**.  
W zależności od scenariusza możesz określić zasady przechowywania lub usuwania pewnej liczby kompilacji.

1. Wybierz pozycję **Kompilacja i wydanie**, a następnie wybierz pozycję **Wydania**.  
Usługa DevOps Projects tworzy potok wydania w celu zarządzania wdrożeniami na platformie Azure.

1.  Po lewej stronie wybierz symbol wielokropka (...) obok potoku wydania, a następnie wybierz pozycję **Edytuj**.  
Potok wydania zawiera potok, który definiuje proces tworzenia wydania.  

1. W obszarze **Artefakty** wybierz polecenie **Porzuć**.  Potok kompilacji przedstawiony w poprzednich krokach generuje dane wyjściowe używane na potrzeby artefaktu. 

1. Obok ikony **Porzuć** wybierz pozycję **Wyzwalacz ciągłego wdrażania**.  
Ten potok wydania ma włączony wyzwalacz ciągłego wdrażania, który przeprowadza wdrożenie za każdym razem, gdy jest dostępny nowy artefakt kompilacji. Opcjonalnie możesz wyłączyć wyzwalacz. Wtedy wdrożenia będą wymagać ręcznego wykonania.  

1. Po lewej stronie wybierz pozycję **Zadania**.   
Zadania to działania wykonywane w procesie wdrażania. W tym przykładzie zostało utworzone zadanie w celu wdrożenia w usłudze Azure App Service.

1. Po prawej stronie wybierz pozycję **Wyświetl wydania**. Ten widok przedstawia historię wersji.

1. Wybierz symbol wielokropka (...) obok jednego z wydań i wybierz pozycję **Otwórz**.  
Dostępnych jest kilka menu, z którymi możesz się zapoznać, na przykład podsumowanie wydań, skojarzone elementy robocze i testy.


1. Wybierz pozycję **Zatwierdzenia**.   
Ten widok przedstawia zatwierdzenia kodu skojarzone z konkretnym wdrożeniem. 

1. Wybierz pozycję **Dzienniki**.  
Dzienniki zawierają przydatne informacje na temat procesu wdrażania. Mogą być wyświetlane zarówno podczas wdrożeń, jak i po nich.


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy usługa Azure App Service i inne utworzone powiązane zasoby nie będą już potrzebne, możesz je usunąć. Użyj funkcji **Usuń** na pulpicie nawigacyjnym usługi DevOps Projects.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat modyfikowania potoków kompilacji i wydania w celu dopasowania ich do potrzeb Twojego zespołu, zapoznaj się z samouczkiem:

> [!div class="nextstepaction"]
> [Dostosowywanie procesu ciągłego wdrażania](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

## <a name="videos"></a>Filmy wideo

> [!VIDEO https://www.youtube.com/embed/itwqMf9aR0w]

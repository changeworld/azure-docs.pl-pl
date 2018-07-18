---
title: Tworzenie potoku ciągłej integracji/ciągłego wdrażania dla platformy .NET w projekcie DevOps platformy Azure | Szybki start
description: Projekt DevOps ułatwia rozpoczęcie pracy na platformie Azure. Umożliwia uruchomienie aplikacji .NET w wybranej usłudze platformy Azure w kilku prostych krokach.
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
ms.openlocfilehash: c858288768e9a3d93015881bf8ebbb4e9d4dc008
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2018
ms.locfileid: "37967099"
---
# <a name="create-a-cicd-pipeline-for-net-with-the-azure-devops-project"></a>Tworzenie potoku ciągłej integracji/ciągłego wdrażania dla platformy .NET w projekcie DevOps platformy Azure

Skonfiguruj proces ciągłej integracji i ciągłego dostarczania dla aplikacji ASP.NET Core w **projekcie DevOps na platformie Azure**.  Projekt DevOps platformy Azure ułatwia początkową konfigurację potoku kompilacji i wydania w usłudze VSTS.

Jeśli nie masz subskrypcji platformy Azure, możesz uzyskać ją bezpłatnie za pośrednictwem programu [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Projekt DevOps platformy Azure tworzy potok ciągłej integracji/ciągłego wdrażania w usłudze VSTS.  Możesz utworzyć **nowe konto usługi VSTS** lub użyć **istniejącego konta**.  Projekt DevOps platformy Azure tworzy również **zasoby platformy Azure** w wybranej przez użytkownika **subskrypcji platformy Azure**.

1. Zaloguj się do witryny [Microsoft Azure Portal](https://portal.azure.com).

1. Wybierz ikonę **Utwórz zasób** na pasku nawigacyjnym po lewej stronie, a następnie wyszukaj hasło **Projekt DevOps**.  Wybierz pozycję **Utwórz**.

    ![Rozpoczynanie ciągłego dostarczania](_img/azure-devops-project-aspnet-core/fullbrowser.png)

## <a name="select-a-sample-application-and-azure-service"></a>Wybieranie przykładowej aplikacji i usługi platformy Azure

1. Wybierz przykładową aplikację **.NET**.  Przykłady platformy .NET umożliwiają wybór struktury ASP.NET typu open source lub międzyplatformowej struktury .NET Core.

    ![Struktura .NET](_img/azure-devops-project-aspnet-core/chooselanguagedotnet.png)

1. Wybierz strukturę aplikacji **.NET Core**.  Ten przykład to aplikacja ASP.NET Core MVC. Gdy wszystko będzie gotowe, wybierz pozycję **Dalej**.

1. Domyślnym celem wdrożenia jest funkcja **Web App on Windows**.  Opcjonalnie możesz wybrać opcję Web App on Linux lub Web App for Containers.  Struktura aplikacji wybrana w poprzednich krokach decyduje o dostępnym w tym miejscu typie celu wdrożenia usługi platformy Azure.  Pozostaw domyślną usługę, a następnie wybierz pozycję **Dalej**.

## <a name="configure-vsts-and-an-azure-subscription"></a>Konfigurowanie usługi VSTS i subskrypcji platformy Azure 

1. Utwórz **nowe** bezpłatne konto usługi VSTS lub wybierz **istniejące** konto.  Wybierz **nazwę** projektu usługi VSTS.  Wybierz swoją **subskrypcję platformy Azure**, **lokalizację** i **nazwę** aplikacji.  Gdy wszystko będzie gotowe, wybierz pozycję **Gotowe**.

    ![Wprowadzanie informacji o usłudze VSTS](_img/azure-devops-project-aspnet-core/vstsazureinfo.png)

1. W ciągu kilku minut w witrynie Azure Portal zostanie załadowany **pulpit nawigacyjny projektu**.  Aplikacja przykładowa zostanie skonfigurowana w repozytorium na koncie usługi VSTS, skompilowana i wdrożona na platformie Azure.  Ten pulpit nawigacyjny zapewnia wgląd do **repozytorium kodu**, **potoku ciągłej integracji/ciągłego wdrażania usługi VSTS** i **aplikacji na platformie Azure**.  Po prawej stronie pulpitu nawigacyjnego wybierz pozycję **Przeglądaj**, aby wyświetlić uruchomioną aplikację.

    ![Widok pulpitu nawigacyjnego](_img/azure-devops-project-aspnet-core/dashboardnopreview.png) 

## <a name="commit-code-changes-and-execute-cicd"></a>Zatwierdzanie zmian kodu i wykonywanie ciągłej integracji/ciągłego wdrażania

Projekt DevOps platformy Azure utworzył repozytorium Git na Twoim koncie usługi VSTS lub GitHub.  Wykonaj poniższe kroki, aby wyświetlić to repozytorium i wprowadzić zmiany w kodzie aplikacji.

1. Z lewej strony pulpitu nawigacyjnego projektu DevOps wybierz link dla gałęzi **master**.  Ten link otwiera widok nowo utworzonego repozytorium Git.

1. Aby wyświetlić adres URL klonowania repozytorium, wybierz pozycję **Klonuj** w prawym górnym rogu przeglądarki. Możesz sklonować repozytorium Git w wybranym środowisku IDE.  W kolejnych kilku krokach użyjesz przeglądarki internetowej, aby dokonać zmian w kodzie i zatwierdzić je bezpośrednio w gałęzi master.

1. Przejdź do pliku **Views/Home/index.cshtml** z lewej strony okna przeglądarki.

1. Wybierz pozycję **Edytuj** i wprowadź zmianę w nagłówku h2.  Wpisz na przykład hasło **Zaczynamy pracę z projektem DevOps platformy Azure** lub wprowadź inną zmianę.

    ![Edytowanie kodu](_img/azure-devops-project-aspnet-core/codechange.png)

1. Wybierz polecenie **Zatwierdź**, a następnie zapisz zmiany.

1. W przeglądarce przejdź do **pulpitu nawigacyjnego projektu DevOps platformy Azure**.  W tym momencie powinna być widoczna trwająca kompilacja.  Wprowadzone zmiany są automatycznie kompilowane i wdrażane za pośrednictwem potoku ciągłej integracji/ciągłego wdrażania usługi VSTS.

## <a name="examine-the-vsts-cicd-pipeline"></a>Przeglądanie potoku ciągłej integracji/ciągłego wdrażania w usłudze VSTS

Projekt DevOps platformy Azure automatycznie konfiguruje pełny potok ciągłej integracji/ciągłego wdrażania usługi VSTS na koncie usługi VSTS.  Możesz przeglądać i dostosowywać potok według potrzeb.  Wykonaj poniższe kroki, aby zapoznać się z definicjami kompilacji i wydania w usłudze VSTS.

1. Wybierz pozycję **Potoki kompilacji** w **górnej części** pulpitu nawigacyjnego projektu DevOps platformy Azure.  Ten link otwiera kartę przeglądarki i definicję kompilacji usługi VSTS dla nowego projektu.

1. Wybierz symbol **wielokropka**.  Ta czynność spowoduje otwarcie menu, w którym możesz uruchomić kilka działań, takich jak dodawanie nowych kompilacji do kolejki, wstrzymywanie kompilacji i edytowanie definicji kompilacji.

1. Wybierz pozycję **Edit** (Edytuj).

    ![Definicja kompilacji](_img/azure-devops-project-aspnet-core/builddef.png)

1. Z poziomu tego widoku **zapoznaj się z różnymi zadaniami** w definicji kompilacji.  W ramach kompilacji są wykonywane różne zadania, takie jak pobieranie źródeł z repozytorium Git, przywracanie zależności i publikowanie danych wyjściowych używanych do wdrożenia.

1. W górnej części definicji kompilacji wybierz **nazwę definicji kompilacji**.

1. Zmień **nazwę** definicji kompilacji na bardziej opisową.  Wybierz pozycję **Zapisz i dodaj do kolejki**, a następnie wybierz pozycję **Zapisz**.

1. W obszarze nazwy definicji kompilacji wybierz pozycję **Historia**.  Zostanie wyświetlony dziennik inspekcji ostatnio wprowadzonych zmian w kompilacji.  Usługa VSTS śledzi wszystkie zmiany wprowadzone w definicji kompilacji i pozwala na porównanie wersji.

1. Wybierz pozycję **Wyzwalacze**.  Projekt DevOps platformy Azure automatycznie utworzył wyzwalacz ciągłej integracji, a każde zatwierdzenie w repozytorium tworzy nową kompilację.  Opcjonalnie możesz zdecydować się dołączyć gałęzie do procesu ciągłej integracji lub je wykluczyć.

1. Wybierz pozycję **Przechowywanie**.  W zależności od scenariusza możesz określić zasady przechowywania lub usuwania pewnej liczby kompilacji.

1. Wybierz pozycję **Kompilacja i wydanie**, a następnie wybierz pozycję **Wydania**.  Projekt DevOps platformy Azure utworzył definicję wydania usługi VSTS w celu zarządzania wdrożeniami na platformie Azure.

1. Po lewej stronie okna przeglądarki wybierz symbol **wielokropka** obok definicji wydania, następnie wybierz pozycję **Edytuj**.

1. Definicja wydania zawiera **potok**, który definiuje proces tworzenia wydania.  W obszarze **Artefakty** wybierz polecenie **Porzuć**.  Definicja kompilacji przedstawiona w poprzednich krokach generuje dane wyjściowe używane na potrzeby artefaktu. 

1. Z prawej strony ikony **Porzuć** wybierz pozycję **Wyzwalacz ciągłego wdrażania**.  Ta definicja wydania ma włączony wyzwalacz ciągłego wdrażania, który przeprowadza wdrożenie za każdym razem, gdy dostępny jest nowy artefakt kompilacji.  Opcjonalnie możesz wyłączyć wyzwalacz. Wtedy wdrożenia będą wymagać ręcznego wykonania. 

1. Po lewej stronie okna przeglądarki wybierz pozycję **Zadania**.  Zadania to czynności wykonywane w procesie wdrażania.  W tym przykładzie zostało utworzone zadanie w celu wdrożenia w usłudze **Azure App Service**.

1. Po prawej stronie okna przeglądarki wybierz pozycję **Wyświetl wersje**.  Ten widok przedstawia historię wersji.

1. Wybierz symbol **wielokropka** obok jednej z wersji i wybierz polecenie **Otwórz**.  W tym widoku jest kilka menu, z którymi możesz się zapoznać, na przykład podsumowanie wersji, skojarzone elementy robocze i testy.

1. Wybierz pozycję **Zatwierdzenia**.  Ten widok przedstawia zatwierdzenia kodu skojarzone z konkretnym wdrożeniem. 

1. Wybierz pozycję **Dzienniki**.  Dzienniki zawierają przydatne informacje na temat procesu wdrażania.  Mogą być wyświetlane zarówno podczas wdrożeń, jak i po nich.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli usługa Azure App Service nie jest już potrzebna, możesz ją usunąć wraz z powiązanymi zasobami utworzonymi w tym przewodniku Szybki start, korzystając z funkcji **Usuń** na pulpicie nawigacyjnym projektu DevOps platformy Azure.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat modyfikowania definicji kompilacji i wydania w celu dopasowania ich do potrzeb Twojego zespołu, zapoznaj się z samouczkiem:

> [!div class="nextstepaction"]
> [Dostosowywanie procesu ciągłego wdrażania](https://docs.microsoft.com/vsts/pipelines/release/define-multistage-release-process?view=vsts)

## <a name="videos"></a>Filmy wideo

> [!VIDEO https://www.youtube.com/embed/itwqMf9aR0w]

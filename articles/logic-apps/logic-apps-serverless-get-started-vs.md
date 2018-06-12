---
title: Tworzenie aplikacji bez serwera w programie Visual Studio | Dokumentacja firmy Microsoft
description: Wprowadzenie do pierwszej aplikacji niekorzystającą z tego przewodnika na tworzenie, wdrażanie i zarządzanie aplikacjami w programie Visual Studio.
keywords: ''
services: logic-apps
author: jeffhollan
manager: jeconnoc
editor: ''
documentationcenter: ''
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: 75f2be44aa8cc239257d6d2a7dad448e9dbab4b4
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35300460"
---
# <a name="build-a-serverless-app-in-visual-studio-with-logic-apps-and-functions"></a>Tworzenie aplikacji bez serwera w programie Visual Studio z Logic Apps i funkcje

Pliki narzędzia i funkcje na platformie Azure umożliwia szybkie opracowywanie i wdrażanie aplikacji w chmurze.  Jak rozpocząć pracę w programie Visual Studio, aplikację niekorzystającą tworzenia przewodnim tego dokumentu.  Omówienie niekorzystającą na platformie Azure [znajduje się w tym artykule](logic-apps-serverless-overview.md).

## <a name="getting-everything-ready"></a>Przygotowanie wszystko

Poniżej przedstawiono wymagania wstępne niezbędne do utworzenia aplikację niekorzystającą z programu Visual Studio:

* [Visual Studio 2017](https://www.visualstudio.com/vs/) lub Visual Studio 2015 — Community, Professional lub Enterprise
* [Logic Apps Tools for Visual Studio](https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio-18551)
* [Najnowszy zestaw Azure SDK](https://azure.microsoft.com/downloads/) (2.9.1 lub nowszej)
* [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)
* [Azure funkcje podstawowe narzędzia](https://www.npmjs.com/package/azure-functions-core-tools) do debugowania działa lokalnie
* Dostęp do sieci web, korzystając z projektanta aplikacji logiki osadzonych

## <a name="getting-started-with-a-deployment-template"></a>Wprowadzenie do szablonu wdrożenia

Zarządzanie zasobami na platformie Azure są wykonywane w ramach grupy zasobów.  Grupa zasobów jest logiczną grupą zasobów.  Grupy zasobów umożliwiają wdrażanie i zarządzanie kolekcją zasobów.  Pliki aplikacji na platformie Azure naszej grupy zasobów zawiera aplikacje logiki platformy Azure i usługi Azure Functions.  Za pomocą projektu grupy zasobów w programie Visual Studio, możemy opracowywanie, zarządzanie i wdrażanie całej aplikacji jako pojedynczy zasób.

### <a name="create-a-resource-group-project-in-visual-studio"></a>Tworzenie projektu grupy zasobów w programie Visual Studio

1. W programie Visual Studio, kliknij, aby dodać **nowy projekt**
1. W **chmury** kategorii, wybierz, aby utworzyć Azure **grupy zasobów** projektu  
 * Jeśli nie ma kategorii lub na liście projektu, upewnij się, że masz zestawu Azure SDK zainstalowany dla programu Visual Studio
1. Nadaj projektu, nazwy i lokalizacji, a następnie wybierz **Ok** do utworzenia programu Visual Studio monituje o wybranie szablonu.  Możesz określić, aby uruchomić z pusta, zaczynać aplikacji logiki lub innego zasobu.  Jednak w takim przypadku używamy szablonie Szybki Start Azure można pobrać nam wprowadzenie do aplikacji bez serwera.
1. Wybierz, aby wyświetlić szablony z **Szybki Start Azure** ![szablony wybranie Szybki Start Azure][1]
1. Wybierz szablon niekorzystającą Szybki Start: **101-logic-app-and-function-app** i kliknij przycisk **Ok**

Szablon szybkiego startu tworzy szablon wdrożenia w projekcie grupy zasobów.  Szablon zawiera prostej aplikacji logiki, który wywołuje usługi Azure Functions i zwraca wynik.  Po otwarciu `azuredeploy.json` plików w Eksploratorze rozwiązań widać zasobów bez serwera aplikacji.

## <a name="deploying-the-serverless-application"></a>Wdrażanie aplikacji bez serwera

Zanim będzie można otworzyć projektanta aplikacji logiki w programie Visual Studio, musi istnieć wstępnie wdrożonej grupy zasobów platformy Azure.  Dzięki temu designer do tworzenia i używania połączenia z zasobami i usługami w aplikacji logiki.  Aby rozpocząć, po prostu należy wdrożyć rozwiązanie utworzony.

1. Kliknij prawym przyciskiem myszy projekt w programie Visual Studio, wybierz **Wdróż**i Utwórz **nowy** wdrożenia ![wybranie nowego zasobu wdrożenia][2]
1. Wybierz ważnej subskrypcji platformy Azure i grupy zasobów
1. Zaznacz, aby **Wdróż** rozwiązania
1. Wprowadź nazwę dla aplikacji funkcji platformy Azure i aplikacji logiki.  Nazwa funkcji platformy Azure musi być globalnie unikatowe

Pliki rozwiązania wdraża do określonej grupy zasobów.  Jeśli przyjrzymy się **dane wyjściowe** w programie Visual Studio można wyświetlić stan wdrożenia.

## <a name="editing-the-logic-app-in-visual-studio"></a>Edytowanie aplikacji logiki w programie Visual Studio

Gdy rozwiązanie zostało wdrożone w dowolnej grupie zasobów, Projektant wizualny można Edycja i wprowadzenie zmian do aplikacji logiki.

1. Kliknij prawym przyciskiem myszy `azuredeploy.json` plików w Eksploratorze rozwiązań i wybierz **otwarty z logiki aplikacji Projektant**
1. Wybierz **grupy zasobów** i **lokalizacji** rozwiązanie zostało wdrożone do i wybierz **OK**

Projektant wizualny aplikacji logiki teraz powinny być widoczne z programem Visual Studio.  Możesz dodać kroki, zmodyfikuj przepływu pracy i zapisać zmiany.  Można również utworzyć aplikacje logiki z programu Visual Studio.  Kliknięcie prawym przyciskiem myszy **zasobów** w Nawigatorze szablonu, możesz dodać **aplikacji logiki** do projektu.  Aplikacje logiki pusty ładowania projektanta wizualnego bez wykonaj wstępne wdrożenie w grupie zasobów.

### <a name="managing-and-viewing-run-history-for-a-deployed-logic-app"></a>Zarządzanie i historię uruchomień na potrzeby aplikacji logiki wdrożonej przeglądanie

Można również zarządzać i Wyświetl historię wykonywania aplikacji logiki wdrożonych na platformie Azure.  Po otwarciu **Eksplorator chmury** narzędzia w programie Visual Studio, kliknij prawym przyciskiem myszy dowolną aplikację logiki i edytować, wyłączyć, Wyświetl właściwości lub Wyświetl historię uruchamiania.  Kliknięcie przycisku Edytuj umożliwia również pobrać aplikacji logiki opublikowane w projekcie grupy zasobów w usłudze Visual Studio.  Oznacza to, że nawet jeśli Rozpoczęto tworzenie aplikacji logiki w portalu Azure, można nadal zaimportuj go i nim zarządzać za pomocą programu Visual Studio.

## <a name="developing-an-azure-function-in-visual-studio"></a>Tworzenie funkcji platformy Azure w programie Visual Studio

Szablon wdrożenia wdraża wszystkie funkcje platformy Azure, znajdujących się w rozwiązaniu dla repozytorium git, określona w `azuredeploy.json` zmiennych.  Jeśli tworzysz projekt funkcji w ramach rozwiązania sprawdź go do kontroli źródła (GitHub, Visual Studio Team Services itp.) i aktualizacja `repo` zmiennej, szablon wdroży funkcji platformy Azure.

### <a name="creating-an-azure-function-project"></a>Tworzenie projektu funkcji platformy Azure

Jeśli przy użyciu języka JavaScript, Python, F #, Bash, partii lub programu PowerShell, wykonaj [czynnościach w ramach funkcji interfejsu wiersza polecenia](../azure-functions/functions-run-local.md) do tworzenia projektu.  Tworzenie funkcji w języku C#, można użyć [biblioteki klas C#](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/16/publishing-a-net-class-library-as-a-function-app/) w bieżącym rozwiązaniu dla funkcji platformy Azure.

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się, jak niekorzystającą społecznościowych pulpit nawigacyjny kompilacji](logic-apps-scenario-social-serverless.md)
* [Zarządzanie aplikacji logiki z programu Visual Studio Cloud Explorer](manage-logic-apps-with-visual-studio.md)
* [Język definicji przepływu pracy aplikacji logiki](logic-apps-workflow-definition-language.md)

<!-- Image references -->
[1]: ./media/logic-apps-serverless-get-started-vs/select-template.png
[2]: ./media/logic-apps-serverless-get-started-vs/deploy.png

---
title: Tworzenie i wdrażanie zadań Webjob za pomocą programu Visual Studio – Azure
description: Dowiedz się, jak tworzenie i wdrażanie usługi Azure WebJobs w usłudze Azure App Service przy użyciu programu Visual Studio.
services: app-service
documentationcenter: ''
author: ggailey777
manager: erikre
editor: jimbe
ms.assetid: a3a9d320-1201-4ac8-9398-b4c9535ba755
ms.service: app-service
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: vs-azure
ms.workload: azure-vs
ms.date: 09/12/2017
ms.author: glenga;david.ebbo;suwatch;pbatum;naren.soni
ms.openlocfilehash: d67a564f4f3e0af3cf02b280036374d892f830e5
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2018
ms.locfileid: "53731271"
---
# <a name="develop-and-deploy-webjobs-using-visual-studio---azure-app-service"></a>Tworzenie i wdrażanie zadań Webjob za pomocą programu Visual Studio — usłudze Azure App Service

## <a name="overview"></a>Przegląd

W tym temacie wyjaśniono, jak używać programu Visual Studio, aby wdrożyć projekt aplikacji konsoli w aplikacji sieci web w [usługi App Service](overview.md) jako [zadanie Azure WebJob](https://go.microsoft.com/fwlink/?LinkId=390226). Aby uzyskać informacje o tym, jak wdrażać zadania Webjob przy użyciu [witryny Azure portal](https://portal.azure.com), zobacz [zadania w tle uruchom za pomocą zadań Webjob](webjobs-create.md).

Gdy program Visual Studio wdroży projekt aplikacji konsoli z możliwością zadań Webjob, wykonuje dwie czynności:

* Kopiuje pliki środowiska uruchomieniowego do odpowiedniego folderu w aplikacji sieci web (*App_Data/zadania/continuous* dla ciągłych zadań Webjob, *App_Data/zadania/triggered* dla zaplanowanych, jak i na żądanie usługi WebJobs).
* Konfiguruje [usługi Azure Scheduler](https://docs.microsoft.com/azure/scheduler/) zadania dla zadań Webjob, które są planowane do uruchomienia w określonym czasie. (To nie jest potrzebna do ciągłych zadań Webjob.)

Włączone usługi WebJobs projekt ma następujące elementy, które dodano do niego:

* [Microsoft.Web.WebJobs.Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) pakietu NuGet.
* A [zadania webjob — publikowanie settings.json](#publishsettings) pliku zawierającego ustawienia wdrożenia i harmonogramu. 

![Diagram przedstawiający, co to jest dodawany do aplikacji konsoli, aby umożliwić wdrożenie jako zadanie WebJob](./media/websites-dotnet-deploy-webjobs/convert.png)

Można dodać te elementy do istniejącego projektu aplikacji konsoli lub Utwórz nowy projekt aplikacji konsoli z możliwością zadań Webjob przy użyciu szablonu. 

Możesz wdrożyć projekt jako zadanie WebJob przez siebie lub link do projektu sieci web tak, aby automatycznie wdraża zawsze wtedy, gdy wdrażanie projektu sieci web. Aby połączyć projektów, program Visual Studio zawiera nazwę projektu zadań Webjob, włączone w [webjobs list.json](#webjobslist) pliku w projekcie sieci web.

![Diagram przedstawiający projektu zadania WebJob łączenie do projektu sieci web](./media/websites-dotnet-deploy-webjobs/link.png)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli używasz programu Visual Studio 2015, zainstaluj [zestawu Azure SDK dla platformy .NET (Visual Studio 2015)](https://azure.microsoft.com/downloads/).

Jeśli używasz programu Visual Studio 2017, zainstaluj [obciążenie programistyczne platformy Azure](https://docs.microsoft.com/visualstudio/install/install-visual-studio#step-4---select-workloads).

## <a id="convert"></a> Włącz wdrożenie zadania Webjob do istniejącego projektu aplikacji konsoli

Dostępne są dwie opcje:

* [Włącz automatyczne wdrażanie projektu sieci web](#convertlink).

  Skonfiguruj istniejący projekt aplikacji konsoli, aby go automatycznie wdraża jako zadanie WebJob podczas wdrażania projektu sieci web. Użyj tej opcji, gdy użytkownik chce uruchomić zadania WebJob w tej samej aplikacji sieci web, w którym są uruchomione aplikacji sieci web powiązanych.

* [Włącz wdrożenie bez projektu sieci web](#convertnolink).

  Skonfiguruj istniejący projekt aplikacji konsoli do wdrażania jako zadanie WebJob przez siebie, za pomocą żadnego linku do projektu sieci web. Użyj tej opcji, jeśli chcesz uruchamiać zadanie WebJob w aplikacji sieci web przez siebie, z żadnej aplikacji sieci web, uruchamiany w aplikacji sieci web. Można to zrobić, aby można było skalować swoje zasoby zadań WebJob, niezależnie od zasobów aplikacji sieci web.

### <a id="convertlink"></a> Włącz automatyczne wdrażanie zadań Webjob za pomocą projektu sieci web

1. Kliknij prawym przyciskiem myszy projekt sieci web w **Eksploratora rozwiązań**, a następnie kliknij przycisk **Dodaj** > **istniejący projekt jako zadanie Azure WebJob**.
   
    ![Istniejący projekt jako zadanie WebJob platformy Azure](./media/websites-dotnet-deploy-webjobs/eawj.png)
   
    [Dodaj zadanie WebJob systemu Azure](#configure) pojawi się okno dialogowe.
2. W **Nazwa projektu** listy rozwijanej, wybierz projekt aplikacji konsoli, można dodać jako zadanie WebJob.
   
    ![Wybieranie projektu w oknie dialogowym Dodaj zadanie WebJob systemu Azure](./media/websites-dotnet-deploy-webjobs/aaw1.png)
3. Wykonaj [Dodaj zadanie WebJob systemu Azure](#configure) okna dialogowego, a następnie kliknij **OK**. 

### <a id="convertnolink"></a> Włącz wdrożenie usługi WebJobs bez projektu sieci web
1. Kliknij prawym przyciskiem myszy projekt aplikacji konsoli w **Eksploratora rozwiązań**, a następnie kliknij przycisk **Publikuj jako zadanie Azure WebJob...** . 
   
    ![Publikuj jako zadanie WebJob platformy Azure](./media/websites-dotnet-deploy-webjobs/paw.png)
   
    [Dodaj zadanie WebJob systemu Azure](#configure) pojawi się okno dialogowe z projektu wybranego w **Nazwa projektu** pole.
2. Wykonaj [Dodaj zadanie WebJob systemu Azure](#configure) okno dialogowe, a następnie kliknij przycisk **OK**.
   
   **Publikowanie w sieci Web** pojawi się Kreator.  Jeśli nie chcesz opublikować natychmiast, zamknąć kreatora. Ustawienia, które zostały wprowadzone są zapisywane dla, gdy użytkownik chce [wdrażanie projektu](#deploy).

## <a id="create"></a>Utwórz nowy projekt z obsługą usługi WebJobs
Aby utworzyć nowy projekt z obsługą usługi WebJobs, można użyć szablonu projektu aplikacji konsoli i włączyć wdrażanie zadań Webjob, jak wyjaśniono w [poprzedniej sekcji](#convert). Alternatywnie można użyć szablonu nowy projekt zadania Webjob:

* [Użyj szablonu nowego projektu zadań Webjob dla niezależnych zadań WebJob](#createnolink)
  
    Tworzenie projektu i skonfigurowania go do wdrożenia przez siebie jako zadanie WebJob przy użyciu żadnego linku do projektu sieci web. Użyj tej opcji, jeśli chcesz uruchamiać zadanie WebJob w aplikacji sieci web przez siebie, z żadnej aplikacji sieci web, uruchamiany w aplikacji sieci web. Można to zrobić, aby można było skalować swoje zasoby zadań WebJob, niezależnie od zasobów aplikacji sieci web.
* [Użyj szablonu nowego projektu zadań Webjob dla zadania WebJob połączonej do projektu sieci web](#createlink)
  
    Utwórz projekt, który jest skonfigurowany do wdrożenia automatycznie jako zadanie WebJob podczas wdrażania projektu sieci web w taki sposób, w tym samym rozwiązaniu. Użyj tej opcji, gdy użytkownik chce uruchomić zadania WebJob w tej samej aplikacji sieci web, w którym są uruchomione aplikacji sieci web powiązanych.

> [!NOTE]
> Szablon nowego projektu zadania Webjob automatycznie instaluje pakiety NuGet i zawiera kod w *Program.cs* dla [zestawu SDK usługi WebJobs](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs). Jeśli nie chcesz używać zestawu SDK usługi WebJobs, usuń lub zmień `host.RunAndBlock` instrukcji w *Program.cs*.
> 
> 

### <a id="createnolink"></a> Użyj szablonu nowego projektu zadań Webjob dla niezależnych zadań WebJob
1. Kliknij przycisk **pliku** > **nowy projekt**, a następnie w polu **nowy projekt** kliknij okno dialogowe **chmury**  >   **Zadanie WebJob platformy Azure (.NET Framework)**.
   
    ![Okno dialogowe nowego projektu przedstawiający szablon zadania WebJob](./media/websites-dotnet-deploy-webjobs/np.png)
2. Wykonaj instrukcje przedstawione wcześniej do [aplikację konsoli projektu niezależnie od projektu zadania Webjob](#convertnolink).

### <a id="createlink"></a> Użyj szablonu nowego projektu zadań Webjob dla zadania WebJob połączonej do projektu sieci web
1. Kliknij prawym przyciskiem myszy projekt sieci web w **Eksploratora rozwiązań**, a następnie kliknij przycisk **Dodaj** > **nowy projekt zadania WebJob Azure**.
   
    ![Nowy wpis menu projektu zadania WebJob platformy Azure](./media/websites-dotnet-deploy-webjobs/nawj.png)
   
    [Dodaj zadanie WebJob systemu Azure](#configure) pojawi się okno dialogowe.
2. Wykonaj [Dodaj zadanie WebJob systemu Azure](#configure) okno dialogowe, a następnie kliknij przycisk **OK**.

## <a id="configure"></a>Okno dialogowe Dodaj zadanie WebJob systemu Azure
**Dodaj zadanie WebJob systemu Azure** okno dialogowe pozwala wprowadzić nazwę zadania WebJob, i uruchom tryb zadania WebJob. 

![Dodaj okno dialogowe zadań WebJob platformy Azure](./media/websites-dotnet-deploy-webjobs/aaw2.png)

Pola w tym oknie dialogowym odpowiadają polom na **Dodaj zadanie WebJob** okna dialogowego w witrynie Azure Portal. Aby uzyskać więcej informacji, zobacz [zadania w tle uruchom za pomocą zadań Webjob](webjobs-create.md).

> [!NOTE]
> * Aby uzyskać informacji o wdrażaniu wiersza polecenia, zobacz [włączenie wiersza polecenia lub ciągłej dostawy Azure WebJobs](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/).
> * Jeśli wdrażać zadania WebJob, a następnie zdecydować, aby zmienić typ zadania WebJob i ponownie wdróż, musisz usunąć *webjobs — publikowanie settings.json* pliku. To spowoduje, że program Visual Studio pokazuj opcje publikowania, dzięki czemu można zmienić typ zadania WebJob.
> * Jeśli wdrażać zadania WebJob i późniejszej zmiany w trybie uruchamiania z ciągłego — ciągły lub odwrotnie, Visual Studio tworzy nowe zadanie WebJob na platformie Azure, podczas ponownego wdrażania. Jeśli zmienisz inne ustawienia harmonogramu, ale pozostaw tryb uruchamiania, taka sama lub przełączać się między zaplanowane i na żądanie, Visual Studio aktualizuje istniejące zadanie zamiast Utwórz nową.
> 
> 

## <a id="publishsettings"></a>webjob-publish-settings.json
Po skonfigurowaniu aplikacji konsoli dla zadań Webjob, wdrażania, program Visual Studio instaluje [Microsoft.Web.WebJobs.Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet pakietu i planowania informacje zawarte w magazynach *zadania webjob — publikowanie settings.json*  pliku w projekcie *właściwości* folderu projektu zadania Webjob. Oto przykład tego pliku:

        {
          "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
          "webJobName": "WebJob1",
          "startTime": "null",
          "endTime": "null",
          "jobRecurrenceFrequency": "null",
          "interval": null,
          "runMode": "Continuous"
        }

Ten plik można edytować bezpośrednio, a program Visual Studio oferuje funkcję IntelliSense. Schemat pliku jest przechowywany w [ https://schemastore.org ](https://schemastore.org/schemas/json/webjob-publish-settings.json) i można je tam.  

## <a id="webjobslist"></a>webjobs-list.json
Gdy łączysz projektu zadań Webjob, włączone do projektu sieci web programu Visual Studio zapisuje Nazwa projektu zadania Webjob w *webjobs list.json* pliku w projekcie sieci web *właściwości* folderu. Lista może zawierać wiele projektów zadań Webjob, jak pokazano w poniższym przykładzie:

        {
          "$schema": "http://schemastore.org/schemas/json/webjobs-list.json",
          "WebJobs": [
            {
              "filePath": "../ConsoleApplication1/ConsoleApplication1.csproj"
            },
            {
              "filePath": "../WebJob1/WebJob1.csproj"
            }
          ]
        }

Ten plik można edytować bezpośrednio, a program Visual Studio oferuje funkcję IntelliSense. Schemat pliku jest przechowywany w [ https://schemastore.org ](https://schemastore.org/schemas/json/webjobs-list.json) i można je tam.

## <a id="deploy"></a>Wdrażanie projektu zadania Webjob
Projektu zadania Webjob, powiązane z projektem sieci web automatycznie wdraża się z projektem sieci web. Uzyskać informacje dotyczące wdrażania projektu sieci web, zobacz **prowadzi instrukcje** > **wdrażanie aplikacji** w nawigacji po lewej stronie.

Aby wdrożyć projekt zadania Webjob przez siebie, kliknij prawym przyciskiem myszy projekt w **Eksploratora rozwiązań** i kliknij przycisk **Publikuj jako zadanie Azure WebJob...** . 

![Publikuj jako zadanie WebJob platformy Azure](./media/websites-dotnet-deploy-webjobs/paw.png)

Dla niezależnych zadań WebJob, taka sama **publikowanie w sieci Web** kreatora, który jest używany dla projektów sieci web jest wyświetlana, ale mniej ustawień można zmienić.

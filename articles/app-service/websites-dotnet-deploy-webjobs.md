---
title: Tworzenie i wdrażanie przy użyciu programu Visual Studio - Azure Webjob
description: Informacje o sposobie tworzenia i wdrażania zadań Webjob Azure w usłudze Azure App Service przy użyciu programu Visual Studio.
services: app-service
documentationcenter: ''
author: tdykstra
manager: erikre
editor: jimbe
ms.assetid: a3a9d320-1201-4ac8-9398-b4c9535ba755
ms.service: app-service
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: glenga;david.ebbo;suwatch;pbatum;naren.soni
ms.openlocfilehash: babe190c0865f5be4aeecb40ca48b52673c6920e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
ms.locfileid: "30161527"
---
# <a name="develop-and-deploy-webjobs-using-visual-studio---azure-app-service"></a>Tworzenie i wdrażanie zadań Webjob przy użyciu programu Visual Studio — usługa aplikacji Azure

## <a name="overview"></a>Omówienie

W tym temacie wyjaśniono, jak używać programu Visual Studio, aby wdrożyć projekt aplikacji konsoli w aplikacji sieci web w [usługi aplikacji](app-service-web-overview.md) jako [zadań WebJob Azure](http://go.microsoft.com/fwlink/?LinkId=390226). Informacje o sposobie wdrażania przy użyciu zadań Webjob [portalu Azure](https://portal.azure.com), zobacz [zadania w tle uruchom z zadań Webjob](web-sites-create-web-jobs.md).

Gdy program Visual Studio wdroży projekt aplikacji konsoli włączone zadań Webjob, wykonuje dwie czynności:

* Kopiuje pliki środowiska uruchomieniowego do odpowiedniego folderu w aplikacji sieci web (*App_Data/zadania/ciągłego* dla ciągłe zadania Webjob, *App_Data/zadania/wyzwalane* dla zadań Webjob zaplanowanych, jak i na żądanie).
* Konfiguruje [Harmonogram systemu Azure](https://docs.microsoft.com/azure/scheduler/) zadania dla zadań Webjob, które są zaplanowane do uruchomienia w określonym czasie. (To nie potrzebne do ciągłe zadania Webjob.)

Projekt z obsługą zadań Webjob ma następujące elementy dodane do niej:

* [Microsoft.Web.WebJobs.Publish](http://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) pakietu NuGet.
* A [zadania webjob publikowania settings.json](#publishsettings) plik zawierający ustawienia wdrażania i harmonogramu. 

![Diagram przedstawiający, co jest dodawany do aplikacji konsoli, aby umożliwić wdrożenie jako zadanie WebJob](./media/websites-dotnet-deploy-webjobs/convert.png)

Można dodać te elementy do istniejącego projektu aplikacji konsoli lub użyj szablonu, aby utworzyć nowy projekt aplikacji konsoli włączone zadań Webjob. 

Wdróż projekt jako zadanie WebJob samodzielnie lub połączyć ją z projektu sieci web tak, aby automatycznie wdraża zawsze, gdy wdrażanie projektu sieci web. Aby powiązać projekty, Visual Studio zawiera nazwę projektu włączone zadań Webjob w [list.json webjob](#webjobslist) plik w projekcie sieci web.

![Diagram przedstawiający projektu zadania WebJob łączenie do projektu sieci web](./media/websites-dotnet-deploy-webjobs/link.png)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli używasz programu Visual Studio 2015, zainstaluj [zestawu Azure SDK dla platformy .NET (Visual Studio 2015)](https://azure.microsoft.com/downloads/).

Jeśli używasz programu Visual Studio 2017, zainstaluj [obciążenia Azure programowanie](https://docs.microsoft.com/visualstudio/install/install-visual-studio#step-4---select-workloads).

## <a id="convert"></a> Włącz wdrożenie zadań Webjob dla istniejący projekt aplikacji konsoli

Dostępne są dwie opcje:

* [Włącz automatyczne wdrażanie projektu sieci web](#convertlink).

  Skonfiguruj istniejący projekt aplikacji konsoli, aby go automatycznie wdraża jako zadanie WebJob podczas wdrażania projektu sieci web. Użyj tej opcji, jeśli chcesz uruchomić WebJob w tej samej aplikacji sieci web, w którym można uruchomić aplikacji sieci web pokrewne.

* [Włącz wdrożenie bez projektu sieci web](#convertnolink).

  Skonfiguruj istniejący projekt aplikacji konsoli, aby wdrożyć jako zadanie WebJob przez siebie, nie łącz do projektu sieci web. Użyj tej opcji, jeśli chcesz uruchomić zadanie WebJob w aplikacji sieci web, z żadnej aplikacji sieci web w aplikacji sieci web. Można to zrobić, aby można było skalowanie zasobami zadania WebJob, niezależnie od zasobów aplikacji sieci web.

### <a id="convertlink"></a> Włącz automatyczne wdrażanie zadań Webjob z projektu sieci web

1. Kliknij prawym przyciskiem myszy projekt sieci web w **Eksploratora rozwiązań**, a następnie kliknij przycisk **Dodaj** > **istniejący projekt jako zadanie WebJob Azure**.
   
    ![Istniejący projekt jako zadanie WebJob platformy Azure](./media/websites-dotnet-deploy-webjobs/eawj.png)
   
    [Dodać zadania WebJob Azure](#configure) zostanie wyświetlone okno dialogowe.
2. W **Nazwa projektu** listy rozwijanej wybierz pozycję projekt aplikacji konsoli do dodania jako zadanie WebJob.
   
    ![Wybieranie projektu w oknie dialogowym Dodawanie zadania WebJob platformy Azure](./media/websites-dotnet-deploy-webjobs/aaw1.png)
3. Zakończenie [dodać zadania WebJob Azure](#configure) okna dialogowego, a następnie kliknij przycisk **OK**. 

### <a id="convertnolink"></a> Włącz wdrożenie zadań Webjob bez projektu sieci web
1. Kliknij prawym przyciskiem myszy projekt aplikacji konsoli w **Eksploratora rozwiązań**, a następnie kliknij przycisk **Publikuj jako zadanie WebJob platformy Azure...** . 
   
    ![Publikuj jako zadanie WebJob platformy Azure](./media/websites-dotnet-deploy-webjobs/paw.png)
   
    [Dodać zadania WebJob Azure](#configure) zostanie wyświetlone okno dialogowe z projekt wybrany w **Nazwa projektu** pole.
2. Zakończenie [dodać zadania WebJob Azure](#configure) , a następnie kliknij przycisk **OK**.
   
   **Publikowanie w sieci Web** zostanie wyświetlony Kreator.  Jeśli nie chcesz opublikować natychmiast, zamknąć kreatora. Ustawienia, które zostały wprowadzone są zapisywane dla, jeśli chcesz [wdrażanie projektu](#deploy).

## <a id="create"></a>Utwórz nowy projekt z obsługą zadań Webjob
Aby utworzyć nowy projekt z obsługą zadań Webjob, można użyć szablonu projektu aplikacji Konsolowej i włączyć wdrożenie zadań Webjob, zgodnie z objaśnieniem w [poprzedniej sekcji](#convert). Alternatywnie można użyć szablonu nowy projekt zadania Webjob:

* [Szablon dla niezależnych zadania WebJob nowy projekt zadania Webjob](#createnolink)
  
    Tworzenie projektu i skonfigurować go do wdrożenia przez samego siebie jako zadanie WebJob z żadnego linku do projektu sieci web. Użyj tej opcji, jeśli chcesz uruchomić zadanie WebJob w aplikacji sieci web, z żadnej aplikacji sieci web w aplikacji sieci web. Można to zrobić, aby można było skalowanie zasobami zadania WebJob, niezależnie od zasobów aplikacji sieci web.
* [Użyj szablonu nowy projekt zadania Webjob dla zadania WebJob powiązany projekt sieci web](#createlink)
  
    Utwórz projekt, który jest skonfigurowany do wdrożenia automatycznie jako zadanie WebJob podczas wdrażania projektu sieci web, w tym samym rozwiązaniu. Użyj tej opcji, jeśli chcesz uruchomić WebJob w tej samej aplikacji sieci web, w którym można uruchomić aplikacji sieci web pokrewne.

> [!NOTE]
> Szablon nowego projektu zadania Webjob automatycznie instaluje pakiety NuGet i zawiera kod w *Program.cs* dla [zestaw SDK zadań Webjob](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs). Jeśli nie chcesz korzystać z zestawu SDK zadań Webjob, usunąć ani zmienić `host.RunAndBlock` instrukcji w *Program.cs*.
> 
> 

### <a id="createnolink"></a> Szablon dla niezależnych zadania WebJob nowy projekt zadania Webjob
1. Kliknij przycisk **pliku** > **nowy projekt**, a następnie w **nowy projekt** kliknij okno dialogowe **chmury** > **zadań WebJob Azure (.NET Framework)**.
   
    ![Okno dialogowe nowego projektu przedstawiający szablonu zadania WebJob](./media/websites-dotnet-deploy-webjobs/np.png)
2. Postępuj zgodnie z instrukcjami pokazano wcześniej do [Aplikacja konsoli projektu niezależne projektu zadania Webjob](#convertnolink).

### <a id="createlink"></a> Użyj szablonu nowy projekt zadania Webjob dla zadania WebJob powiązany projekt sieci web
1. Kliknij prawym przyciskiem myszy projekt sieci web w **Eksploratora rozwiązań**, a następnie kliknij przycisk **Dodaj** > **nowy projekt zadania WebJob Azure**.
   
    ![Nowy wpis menu projektu zadania WebJob platformy Azure](./media/websites-dotnet-deploy-webjobs/nawj.png)
   
    [Dodać zadania WebJob Azure](#configure) zostanie wyświetlone okno dialogowe.
2. Zakończenie [dodać zadania WebJob Azure](#configure) , a następnie kliknij przycisk **OK**.

## <a id="configure"></a>Okno dialogowe Dodawanie zadania WebJob platformy Azure
**Dodać zadania WebJob Azure** oknie dialogowym można wprowadzić nazwę zadania WebJob i uruchom tryb WebJob. 

![Dodawanie zadania WebJob Azure w oknie dialogowym](./media/websites-dotnet-deploy-webjobs/aaw2.png)

Pola w tym oknie dialogowym odpowiadają polom na **dodać zadania WebJob** okna dialogowego z portalu Azure. Aby uzyskać więcej informacji, zobacz [zadania w tle uruchom z zadań Webjob](web-sites-create-web-jobs.md).

> [!NOTE]
> * Informacji o wdrażaniu wiersza polecenia, zobacz [Włączanie wiersza polecenia lub ciągłego dostarczania Azure WebJobs](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/).
> * Jeśli zdecydujesz, aby zmienić typ zadania WebJob i utwórz je ponownie po wdrożeniu zadanie WebJob, musisz usunąć *zadań webjob publikowania settings.json* pliku. Spowoduje to Visual Studio pokazuj opcje publikowania, dzięki czemu można zmieniać typ zadania WebJob.
> * Jeśli wdrożyć zadanie WebJob i późniejszej zmiany w trybie uruchamiania z ciągłego nieciągły lub odwrotnie, Visual Studio tworzy nowe zadanie WebJob na platformie Azure, podczas ponownego wdrażania. Jeśli zmienisz inne ustawienia harmonogramu, ale pozostaw tryb uruchamiania takie same lub przełączać się między zaplanowane i na żądanie, Visual Studio aktualizuje istniejące zadanie zamiast Utwórz nową.
> 
> 

## <a id="publishsettings"></a>webjob-publish-settings.json
Po skonfigurowaniu aplikacji konsoli dla zadań Webjob wdrożenia programu Visual Studio instaluje [Microsoft.Web.WebJobs.Publish](http://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet pakietu i magazyny planowania informacji w *zadania webjob publikowania settings.json* pliku w projekcie *właściwości* folderu projektu zadania Webjob. Oto przykład tego pliku:

        {
          "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
          "webJobName": "WebJob1",
          "startTime": "null",
          "endTime": "null",
          "jobRecurrenceFrequency": "null",
          "interval": null,
          "runMode": "Continuous"
        }

Ten plik można edytować bezpośrednio, a program Visual Studio udostępnia IntelliSense. Schemat pliku są przechowywane w [ http://schemastore.org ](http://schemastore.org/schemas/json/webjob-publish-settings.json) i można wyświetlić.  

## <a id="webjobslist"></a>webjobs-list.json
Po połączeniu włączone zadań Webjob projektu do projektu sieci web programu Visual Studio przechowuje Nazwa projektu zadania Webjob w *list.json webjob* plik w projekcie sieci web *właściwości* folderu. Lista może zawierać wiele projektów zadań Webjob, jak pokazano w poniższym przykładzie:

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

Ten plik można edytować bezpośrednio, a program Visual Studio udostępnia IntelliSense. Schemat pliku są przechowywane w [ http://schemastore.org ](http://schemastore.org/schemas/json/webjobs-list.json) i można wyświetlić.

## <a id="deploy"></a>Wdrażanie projektu zadania Webjob
Powiązane do projektu sieci web projektu zadania Webjob automatycznie wdraża z projektu sieci web. Aby uzyskać informacje dotyczące wdrażania projektu sieci web, zobacz **prowadzi instrukcje** > **wdrażanie aplikacji** na lewym pasku nawigacyjnym.

Aby wdrożyć projekt zadania Webjob samodzielnie, kliknij prawym przyciskiem myszy projekt w **Eksploratora rozwiązań** i kliknij przycisk **Publikuj jako zadanie WebJob platformy Azure...** . 

![Publikuj jako zadanie WebJob platformy Azure](./media/websites-dotnet-deploy-webjobs/paw.png)

Dla niezależnych zadania WebJob, taki sam **publikowanie w sieci Web** kreatora, który jest używany dla projektów sieci web jest wyświetlana, ale mniej dostępne zmienić ustawienia.

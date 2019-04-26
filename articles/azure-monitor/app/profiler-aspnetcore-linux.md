---
title: Profil aplikacji sieci web platformy ASP.NET Core platformy Azure w systemie Linux za pomocą Application Insights Profiler | Dokumentacja firmy Microsoft
description: Omówienie pojęć i samouczek krok po kroku dotyczące sposobu używania Application Insights Profiler.
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.reviewer: mbullwin
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: cweining
ms.openlocfilehash: 35789cc1e516fb24d5e985e12b44fe3cd01b795d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60306536"
---
# <a name="profile-aspnet-core-azure-linux-web-apps-with-application-insights-profiler"></a>Profil aplikacji sieci web platformy ASP.NET Core platformy Azure w systemie Linux za pomocą Application Insights Profiler

Ta funkcja jest obecnie dostępna w wersji zapoznawczej.

Dowiedz się, jaka jest zużywany czas w każdej metodzie działającej aplikacji internetowej przy użyciu [usługi Application Insights](../../azure-monitor/app/app-insights-overview.md). Application Insights Profiler jest teraz dostępny do aplikacji sieci web platformy ASP.NET Core, które są hostowane w systemie Linux w usłudze Azure App Service. Ten przewodnik zawiera instrukcje krok po kroku w sposób można zbierać ślady Profiler dla aplikacji sieci web platformy ASP.NET Core w systemie Linux.

Po ukończeniu tego przewodnika aplikacji może zbierać ślady Profiler, takich jak śladów, które są wyświetlane na obrazie. W tym przykładzie śledzenia Profiler wskazuje, że żądanie namierzenie internetowego jest powolne, ze względu na czas oczekiwania. *Ścieżka aktywna* w kodzie, który spowalnia aplikacji jest oznaczony ikoną gaśniczego. **o** method in Class metoda **HomeController** sekcji spowalnia aplikacji sieci web, ponieważ wywołuje metodę **Thread.Sleep** funkcji.

![Ślady narzędzia Profiler](./media/profiler-aspnetcore-linux/profiler-traces.png)

## <a name="prerequisites"></a>Wymagania wstępne
Poniższe instrukcje mają zastosowanie do wszystkich środowisk programowania Windows, Linux i Mac:

* Zainstaluj [zestawu .NET Core SDK 2.1.2 lub nowszej](https://dotnet.microsoft.com/download/archives).
* Zainstaluj oprogramowanie Git, postępując zgodnie z instrukcjami w artykule [wprowadzenie — Instalowanie usługi Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).

## <a name="set-up-the-project-locally"></a>Konfigurowanie projektu lokalnie

1. Otwórz okno wiersza polecenia na komputerze. Zgodnie z poniższymi instrukcjami działać dla wszystkich środowisk programowania Windows, Linux i Mac.

1. Tworzenie aplikacji sieci web platformy ASP.NET Core MVC:

    ```
    dotnet new mvc -n LinuxProfilerTest
    ```

1. Zmień katalog roboczy do folderu głównego projektu.

1. Dodawanie pakietu NuGet, aby zbierać ślady Profiler:

    ```shell
    dotnet add package Microsoft.ApplicationInsights.Profiler.AspNetCore
    ```

1. Włącz usługę Application Insights w pliku Program.cs:

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseApplicationInsights() // Add this line of code to Enable Application Insights
            .UseStartup<Startup>();
    ```
    
1. Włącz Profiler w pliku Startup.cs:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddServiceProfiler(); // Add this line of code to Enable Profiler
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    }
    ```

1. Dodaj wiersz kodu w **HomeController.cs** sekcji, aby losowo opóźnienie kilka sekund:

    ```csharp
        using System.Threading;
        ...

        public IActionResult About()
            {
                Random r = new Random();
                int delay = r.Next(5000, 10000);
                Thread.Sleep(delay);
                return View();
            }
    ```

1. Zapisz i zatwierdź zmiany w repozytorium lokalnym:

    ```
        git init
        git add .
        git commit -m "first commit"
    ```

## <a name="create-the-linux-web-app-to-host-your-project"></a>Utwórz aplikację internetową systemu Linux w celu hostowania projektu

1. Utwórz środowiska aplikacji sieci web przy użyciu usługi App Service w systemie Linux:

    ![Utwórz aplikację internetową systemu Linux](./media/profiler-aspnetcore-linux/create-linux-appservice.png)

2. Utwórz poświadczenia wdrożenia:

    > [!NOTE]
    > Zapisz hasło do użycia w przyszłości podczas wdrażania aplikacji sieci web.

    ![Utwórz poświadczenia wdrożenia](./media/profiler-aspnetcore-linux/create-deployment-credentials.png)

3. Wybierz opcje wdrażania. Konfigurowanie lokalnego repozytorium Git w aplikacji sieci web, postępując zgodnie z instrukcjami w witrynie Azure portal. Repozytorium Git jest tworzona automatycznie.

    ![Konfigurowanie repozytorium Git](./media/profiler-aspnetcore-linux/setup-git-repo.png)

Aby uzyskać więcej opcji wdrażania, zobacz [w tym artykule](https://docs.microsoft.com/azure/app-service/containers/choose-deployment-type).

## <a name="deploy-your-project"></a>Wdrażanie projektu

1. W oknie wiersza polecenia przejdź do folderu głównego projektu. Dodaj zdalne repozytorium Git, aby wskazywał repozytorium w usłudze App Service:

    ```
    git remote add azure https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
    ```

    * Użyj **username** umożliwia Utwórz poświadczenia wdrożenia.
    * Użyj **nazwy aplikacji** umożliwia tworzenie aplikacji sieci web przy użyciu usługi App Service w systemie Linux.

2. Wdrażanie projektu, wypychając zmiany do platformy Azure:

    ```
    git push azure master
    ```

Powinny zostać wyświetlone dane wyjściowe podobne do poniższego przykładu:

    ```
    Counting objects: 9, done.
    Delta compression using up to 8 threads.
    Compressing objects: 100% (8/8), done.
    Writing objects: 100% (9/9), 1.78 KiB | 911.00 KiB/s, done.
    Total 9 (delta 3), reused 0 (delta 0)
    remote: Updating branch 'master'.
    remote: Updating submodules.
    remote: Preparing deployment for commit id 'd7369a99d7'.
    remote: Generating deployment script.
    remote: Running deployment command...
    remote: Handling ASP.NET Core Web Application deployment.
    remote: ......
    remote:   Restoring packages for /home/site/repository/EventPipeExampleLinux.csproj...
    remote: .
    remote:   Installing Newtonsoft.Json 10.0.3.
    remote:   Installing Microsoft.ApplicationInsights.Profiler.Core 1.1.0-LKG
    …

    ```

## <a name="add-application-insights-to-monitor-your-web-apps"></a>Dodaj usługę Application Insights do monitorowania aplikacji sieci web

1. [Utwórz zasób usługi Application Insights](./../../azure-monitor/app/create-new-resource.md ).

2. Kopiuj **iKey** wartości zasobu usługi Application Insights i skonfigurować następujące ustawienia w aplikacjach sieci web:

    ```
    APPINSIGHTS_INSTRUMENTATIONKEY: [YOUR_APPINSIGHTS_KEY]
    ```

    Po zmianie ustawień aplikacji, witryna zostanie automatycznie uruchomiony ponownie. Po zastosowaniu nowych ustawień, Profiler jest uruchamiany natychmiast na dwie minuty. Profiler następnie działa na dwie minuty, co godzinę.

3. Generowanie część ruchu do swojej witryny sieci Web. Możesz wygenerować ruchu, odświeżając lokacji **o** strony kilka razy.

4. Poczekaj 2 do 5 minut, zanim zdarzenia do agregacji do usługi Application Insights.

5. Przejdź do usługi Application Insights **wydajności** okienko w witrynie Azure portal. Można wyświetlić ślady Profiler w prawej dolnej części okienka.

    ![Wyświetlanie śladów Profiler](./media/profiler-aspnetcore-linux/view-traces.png)

## <a name="known-issues"></a>Znane problemy

### <a name="profile-now-button-doesnt-work-for-linux-profiler"></a>Przycisk teraz profil nie działa dla systemu Linux Profiler
Wersji systemu Linux profiler usługi App Insights nie obsługuje jeszcze na żądanie profilowania przy użyciu profilu teraz przycisk.


## <a name="next-steps"></a>Kolejne kroki
Jeśli używasz kontenerów niestandardowych, które są hostowane przez usługę Azure App Service, postępuj zgodnie z instrukcjami [ Włącz Profiler usługi dla konteneryzowanych aplikacji platformy ASP.NET Core](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/tree/master/examples/EnableServiceProfilerForContainerApp) umożliwiające Application Insights Profiler.

Zgłoś wszelkie problemy lub sugestie do repozytorium usługi Application Insights w witrynie GitHub: [ApplicationInsights-Profiler-AspNetCore: Problemy z](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/issues).

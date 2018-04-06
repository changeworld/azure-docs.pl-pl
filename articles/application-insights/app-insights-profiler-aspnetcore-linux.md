---
title: Profil aplikacji sieci web platformy ASP.NET Core Azure Linux z Application Insights profilera | Dokumentacja firmy Microsoft
description: Omówienie pojęć i samouczek krok po kroku dotyczące sposobu używania aplikacji Insights profilera.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/23/2018
ms.author: mbullwin
ms.openlocfilehash: 5596c4efeba14e9d2bfdadd7ce92bb6b2c9fcbf0
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="profile-aspnet-core-azure-linux-web-apps-with-application-insights-profiler"></a>Aplikacje sieci web platformy ASP.NET Core Azure Linux profil z Application Insights profilera

Ta funkcja jest obecnie dostępna w wersji zapoznawczej.

Sprawdzić, ile jest czas w każdej metodzie aplikacji sieci web na żywo przy użyciu [usługi Application Insights](app-insights-overview.md). Application Insights Profiler jest teraz dostępna dla aplikacji sieci web platformy ASP.NET Core, które są obsługiwane w systemie Linux w usłudze Azure App Service. Ten przewodnik zawiera instrukcje krok po kroku w sposób można zbierać ślady profilera dla aplikacji sieci web platformy ASP.NET Core w systemie Linux.

Po ukończeniu tego przewodnika aplikacji może zbierać ślady profilera, takich jak dane śledzenia, które przedstawiono w obrazie. W tym przykładzie śledzenia profilera wskazuje, że żądania sieci web określonego jest powolne, ze względu na czas oczekiwania. *Aktywnej ścieżki* w kodzie spowalnia aplikacji jest oznaczony ikoną zabezpieczenia. **o** metody w **HomeController** sekcji spowalnia aplikacji sieci web, ponieważ wywołuje metodę **Thread.Sleep** funkcji.

![Ślady profilera](./media/app-insights-profiler-aspnetcore-linux/profiler-traces.png)

## <a name="prerequisites"></a>Wymagania wstępne
Poniższe instrukcje dotyczą wszystkich środowisk deweloperskich systemu Windows, Linux i komputerów Mac:

* Zainstaluj [.NET Core SDK 2.1.2 lub nowszym](https://www.microsoft.com/net/download/windows/build).
* Zainstaluj system Git, postępując zgodnie z instrukcjami w [wprowadzenie — instalowanie Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).

## <a name="set-up-the-project-locally"></a>Konfigurowanie projektu lokalnie

1. Otwórz okno wiersza polecenia na tym komputerze. Poniższe instrukcje działają dla wszystkich środowisk deweloperskich systemu Windows, Linux i komputerów Mac.

2. Tworzenie aplikacji sieci web platformy ASP.NET MVC rdzeni:

    ```
    dotnet new mvc -n LinuxProfilerTest
    ```

3. Zmień katalog roboczy do folderu głównego dla projektu.

4. Dodaj pakiet NuGet, aby zbierać ślady profilera:

    ```
    dotnet add package Microsoft.ApplicationInsights.Profiler.AspNetCore
    ```

5. Dodaj wiersz kodu w **HomeController.cs** sekcji losowo opóźnienia kilka sekund:

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

6. Zapisz i zatwierdź zmiany w lokalnym repozytorium:

    ```
        git init
        git add .
        git commit -m "first commit"
    ```

## <a name="create-the-linux-web-app-to-host-your-project"></a>Tworzenie aplikacji sieci web systemu Linux w celu hostowania projektu

1. Utwórz środowisko aplikacji sieci web za pomocą usługi aplikacji w systemie Linux:

    ![Tworzenie aplikacji sieci web systemu Linux](./media/app-insights-profiler-aspnetcore-linux/create-linux-appservice.png)

2. Utwórz poświadczenia wdrażania:

    > [!NOTE]
    > Zapisz hasło do użycia w przyszłości podczas wdrażania aplikacji sieci web.

    ![Utwórz poświadczenia wdrażania](./media/app-insights-profiler-aspnetcore-linux/create-deployment-credentials.png)

3. Wybierz opcje wdrażania. Konfigurowanie lokalnego repozytorium Git w aplikacji sieci web, zgodnie z instrukcjami w portalu Azure. Repozytorium Git jest tworzony automatycznie.

    ![Konfigurowanie repozytorium Git](./media/app-insights-profiler-aspnetcore-linux/setup-git-repo.png)

Aby uzyskać więcej opcji wdrażania, zobacz [w tym artykule](https://docs.microsoft.com/azure/app-service/containers/choose-deployment-type).

## <a name="deploy-your-project"></a>Wdrażanie projektu

1. W oknie wiersza polecenia przejdź do folderu głównego dla projektu. Dodaj zdalnego repozytorium Git, aby wskazywał repozytorium w usłudze aplikacji:

    ```
    git remote add azure https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
    ```

    * Użyj **username** umożliwia Utwórz poświadczenia wdrażania.
    * Użyj **Nazwa aplikacji** umożliwia tworzenie aplikacji sieci web za pomocą usługi aplikacji w systemie Linux.

2. Wdrażanie projektu przez naciśnięcie zmiany na platformie Azure:

    ```
    git push azure master
    ```

Powinny pojawić się dane wyjściowe podobne do poniższego przykładu:

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

## <a name="add-application-insights-to-monitor-your-web-apps"></a>Dodawanie usługi Application Insights do monitorowania aplikacji sieci web

1. [Tworzenie zasobu usługi Application Insights](./app-insights-create-new-resource.md).

2. Kopiuj **iKey** wartości zasobu usługi Application Insights i skonfigurować następujące ustawienia w aplikacjach sieci web:

    ```
    APPINSIGHTS_INSTRUMENTATIONKEY: [YOUR_APPINSIGHTS_KEY]
    ASPNETCORE_HOSTINGSTARTUPASSEMBLIES: Microsoft.ApplicationInsights.Profiler.AspNetCore
    ```

    ![Konfiguruj ustawienia aplikacji](./media/app-insights-profiler-aspnetcore-linux/set-appsettings.png)

    Po zmianie ustawień aplikacji, witryna zostanie automatycznie uruchomiony ponownie. Po zastosowaniu nowych ustawień, profilera natychmiast uruchamia dwie minuty. Profiler następnie uruchamia przez dwie minuty co godzinę.

3. Generowanie niektórych ruch do witryny sieci Web. Możesz wygenerować ruchu przez odświeżenie witryny **o** strony kilka razy.

4. Poczekaj 2 do 5 minut na zdarzenia do agregacji do usługi Application Insights.

5. Przejdź do usługi Application Insights **wydajności** okienku w portalu Azure. Można wyświetlić ślady profilera w prawym dolnym rogu okienka.

    ![Wyświetl ślady profilera](./media/app-insights-profiler-aspnetcore-linux/view-traces.png)

## <a name="known-issues"></a>Znane problemy

### <a name="the-enable-action-in-the-profiler-configuration-pane-doesnt-work"></a>Włącz akcji w okienku Konfiguracja profilera nie działa

> [!NOTE]
> Jeśli na serwerze aplikacji za pomocą usługi aplikacji w systemie Linux, nie trzeba ponownie włączyć profilera w **wydajności** okienku w portalu usługi Application Insights. Możesz dołączyć pakiet NuGet do projektu i ustawić usługi Application Insights **iKey** wartość ustawienia aplikacji sieci web, aby umożliwić profilera.

Po wykonaniu aktywacji przepływu pracy do [Application Insights profilera dla systemu Windows](./app-insights-profiler.md) i wybierz **włączyć** w **skonfiguruj profilera** okienku komunikat o błędzie. Akcji włączania próbuje zainstalować agenta profilera wersji systemu Windows w środowisku systemu Linux.

Pracujemy nad rozwiązanie tego problemu.

![Nie należy próbować ponownie włączyć w okienku wydajności profilera](./media/app-insights-profiler-aspnetcore-linux/issue-enable-profiler.png)


## <a name="next-steps"></a>Kolejne kroki
Jeśli używasz niestandardowego kontenerów, które są obsługiwane przez usługę Azure App Service, postępuj zgodnie z instrukcjami [ włączyć profilera usługi dla aplikacji platformy ASP.NET Core konteneryzowanych](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/tree/master/examples/EnableServiceProfilerForContainerApp) umożliwiające Application Insights profilera.

Zgłoś wszelkie problemy lub sugestie do repozytorium GitHub Insights aplikacji: [ApplicationInsights-profilera-AspNetCore: problemów](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/issues).

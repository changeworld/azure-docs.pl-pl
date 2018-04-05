---
title: Azure Application Insights dla platformy ASP.NET Core | Dokumentacja firmy Microsoft
description: Monitorowanie aplikacji sieci web dla dostępności, wydajności i użycia.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: mbullwin
ms.openlocfilehash: 3f57eb3ede3b57e28c13e053b86ab022fdd21b7e
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="application-insights-for-aspnet-core"></a>Usługa Application Insights dla aplikacji ASP.NET Core

Azure Application Insights udostępnia szczegółowe monitorowanie aplikacji sieci web do poziomu kodu. Ułatwia monitorowanie aplikacji sieci web, dostępności, wydajności i użycia. Pozwala też szybko identyfikować i diagnozować błędy w aplikacji bez oczekiwania na zgłoszenie ich przez użytkownika.

W tym artykule przedstawiono tworzenie przykładowej platformy ASP.NET Core [stron Razor](https://docs.microsoft.com/aspnet/core/mvc/razor-pages/?tabs=visual-studio) aplikacji w Visual Studio i jak rozpocząć monitorowanie za pomocą usługi Azure Application Insights.

## <a name="prerequisites"></a>Wymagania wstępne

- NET Core 2.0.0 SDK lub nowszym.
- [Visual Studio 2017](https://www.visualstudio.com/downloads/) wersji 15.3 lub nowszej z obciążeniem programowanie ASP.NET i sieć web.

## <a name="create-an-aspnet-core-project-in-visual-studio"></a>Tworzenie projektu platformy ASP.NET Core w programie Visual Studio

1. Kliknij prawym przyciskiem myszy, a następnie uruchom **programu Visual Studio 2017** jako administrator.
2. Wybierz **pliku** > **nowe** > **projektu** (Ctrl-Shift-N).

   ![Zrzut ekranu przedstawiający Menu programu Visual Studio pliku nowego projektu](./media/app-insights-asp-net-core/0001-file-new-project.png)

3. Rozwiń węzeł **Visual C#** > Wybierz **.NET Core** > **aplikacji sieci Web platformy ASP.NET Core**. Wprowadź **nazwa** > **Nazwa rozwiązania** > Sprawdź **Tworzenie nowego repozytorium Git**.

   ![Zrzut ekranu przedstawiający Kreator nowego projektu plików programu Visual Studio](./media/app-insights-asp-net-core/0002-new-project-web-application.png)

4. Wybierz **.Net Core** > **platformy ASP.NET Core 2.0** **aplikacji sieci Web** > **OK**.

    ![Zrzut ekranu przedstawiający Menu wyboru projektu nowy plik programu Visual Studio](./media/app-insights-asp-net-core/0003-dot-net-core.png)

## <a name="add-application-insights-telemetry"></a>Dodawanie telemetrii usługi Application Insights

1. Wybierz **projektu** > **Dodaj Telemetrię usługi Application Insights...** (Alternatywnie możesz kliknąć prawym przyciskiem myszy **usług połączonych** > Dodaj połączonych usług.)

    ![Zrzut ekranu przedstawiający Menu wyboru projektu nowy plik programu Visual Studio](./media/app-insights-asp-net-core/0004-add-application-insights-telemetry.png)

2. Wybierz pozycję **Rozpocznij bezpłatnie**.

    ![Zrzut ekranu przedstawiający Menu wyboru projektu nowy plik programu Visual Studio](./media/app-insights-asp-net-core/0005-start-free.png)

3. Wybierz odpowiednie **subskrypcji** > **zasobów** > i umożliwia określenie, czy zezwolić na zbieranie ponad 1 GB w miesiącu > **zarejestrować**.

    ![Zrzut ekranu przedstawiający Menu wyboru projektu nowy plik programu Visual Studio](./media/app-insights-asp-net-core/0006-register.png)

## <a name="changes-made-to-your-project"></a>Zmienia Made do projektu

Usługa Application Insights jest bardzo niskie obciążenie. Aby przejrzeć zmiany do projektu, dodając telemetrii usługi Application Insights:

Wybierz **widoku** > **Team Explorer** (Ctrl +\, Ctrl + M) > **projektu** > **zmiany**

- Całkowita liczba czterech zmiany:

  ![Zrzut ekranu przedstawiający pliki zmodyfikowane przez dodanie usługi Application Insights](./media/app-insights-asp-net-core/0007-changes.png)

- Tworzony jest jeden nowy plik:

   **ConnectedService.json**

  ![Zrzut ekranu przedstawiający pliki zmodyfikowane przez dodanie usługi Application Insights](./media/app-insights-asp-net-core/0008-connectedservice-json.png)

- Trzy pliki zostały zmodyfikowane:

  **appsettings.json**

   ![Zrzut ekranu przedstawiający pliki zmodyfikowane przez dodanie usługi Application Insights](./media/app-insights-asp-net-core/0009-appsettings-json.png)

  **ContosoDotNetCore.csproj**

   ![Zrzut ekranu przedstawiający pliki zmodyfikowane przez dodanie usługi Application Insights](./media/app-insights-asp-net-core/0010-contoso-netcore-csproj.png)

   **Program.cs**

   ![Zrzut ekranu przedstawiający pliki zmodyfikowane przez dodanie usługi Application Insights](./media/app-insights-asp-net-core/0011-program-cs.png)

## <a name="synthetic-transactions-with-powershell"></a>Transakcje syntetyczne przy użyciu programu PowerShell

Uruchamianie aplikacji, a następnie klikając wokół linki ręcznie może służyć do generowania testu ruchu. Jednak często jest przydatne do tworzenia prostych transakcji syntetycznych w programie PowerShell.

1. Uruchom aplikację, klikając pozycję IIS Express ![Ikona zrzut ekranu programu Visual Studio usług IIS Express](./media/app-insights-asp-net-core/0012-iis-express.png)

2. Skopiuj adres url z pola adresu przeglądarki. Jest w formacie http://localhost:{random numer portu}

   ![Zrzut ekranu przedstawiający adres url na pasku adresu przeglądarki](./media/app-insights-asp-net-core/0013-copy-url.png)

3. Uruchom następującą pętlę programu PowerShell do utworzenia 100 transakcji syntetycznych względem aplikacji testu. Zmodyfikuj numer portu po **localhost:** odpowiadające adresu url skopiowanego w poprzednim kroku.

   ```PS
   for ($i = 0 ; $i -lt 100; $i++)
   {
    Invoke-WebRequest -uri http://localhost:50984/
   }
   ```

## <a name="open-application-insights-portal"></a>Portal usługi Otwórz Application Insights

Po uruchomieniu programu PowerShell z poprzedniej sekcji, uruchom program Application Insights, aby wyświetlić transakcje i upewnij się, że dane są zbierane. 

Wybierz z menu programu Visual Studio **projektu** > **usługi Application Insights** > **Otwórz Portal usługi Application Insights**

   ![Zrzut ekranu przedstawiający aplikacji Insights — omówienie](./media/app-insights-asp-net-core/0014-portal-01.png)

> [!NOTE]
> Na przykład zrzucie ekranu powyżej **strumień na żywo**, **czas ładowania strony widoku**, i **nieudanych żądań** nie są obecnie zbierane. Następna sekcja przeprowadzi każdego dodawania. Jeśli są zbierane **strumień na żywo**, i **czas ładowania strony widoku**, tylko wykonaj kroki **nieudanych żądań**.

## <a name="collect-failed-requests-live-stream--page-view-load-time"></a>Zbieranie żądań zakończonych niepowodzeniem, strumień na żywo i czas ładowania strony widoku

### <a name="failed-requests"></a>Żądania zakończone niepowodzeniem

Technicznie **nieudanych żądań** są zbierane, ale brak jeszcze wystąpił. Aby przyspieszyć proces wzdłuż wyjątek niestandardowych można dodać do istniejącego projektu, aby symulować wyjątek rzeczywistych. Jeśli aplikacja nadal działa w programie Visual Studio przed kontynuowaniem **Zatrzymaj debugowanie** (Shift + F5)

1. W **Eksploratora rozwiązań** > Rozwiń **stron** > **About.cshtml** > Otwórz **About.cshtml.cs**.

   ![Zrzut ekranu przedstawiający Eksploratora rozwiązań programu Visual Studio](./media/app-insights-asp-net-core/0015-solution-explorer-about.png)

2. Dodaj wyjątek w obszarze ``Message=`` > Zapisz zmiany w pliku.

   ```C#
   throw new Exception("Test Exception");
   ```

   ![Zrzut ekranu przedstawiający kod wyjątku:](./media/app-insights-asp-net-core/000016-exception.png)

### <a name="live-stream"></a>Transmisja strumieniowa na żywo

Dostęp do funkcji strumień na żywo z usługi Application Insights o aktualizacji platformy ASP.NET Core **Microsoft.ApplicationInsights.AspNetCore 2.2.0** pakietów NuGet.

W programie Visual Studio, wybierz **projektu** > **Zarządzaj pakietami NuGet** > **Microsoft.ApplicationInsights.AspNetCore** > wersji **2.2.0** > **aktualizacji**.

  ![Zrzut ekranu Menedżera pakietów NuGet](./media/app-insights-asp-net-core/0017-update-nuget.png)

Wiele o potwierdzenie będą występować przeczytaj i zaakceptuj Jeśli zgadzasz się z zmiany.

### <a name="page-view-load-time"></a>Czas ładowania strony widoku

1. W programie Visual Studio przejdź do **Eksploratora rozwiązań** > **stron** > będzie konieczna modyfikacja dwa pliki: **_Layout.cshtml**, i **_ ViewImports.cshtml**

2. W **_ViewImports.cshtml**, dodać:

   ```C#
   @using Microsoft.ApplicationInsights.AspNetCore
   @inject JavaScriptSnippet snippet
   ```
     ![Zrzut ekranu przedstawiający _ViewImports.cshtml zmiany kodu](./media/app-insights-asp-net-core/00018-view-imports.png)

3. W **_Layout.cshtml** Dodaj wiersz poniżej przed ``</head>`` tag, ale przed innymi skryptami.

    ```C#
    @Html.Raw(snippet.FullScript)
    ```
    ![Zrzut ekranu przedstawiający zmiany kodu layout.cshtml](./media/app-insights-asp-net-core/0018-layout-cshtml.png)

### <a name="test-failed-requests-page-view-load-time-live-stream"></a>Test nie powiódł się żądania, czas ładowania strony widoku, strumień na żywo

Teraz, że zostały wykonane kroki z poprzedniej można przetestować i upewnij się, że wszystko działa.

1. Uruchom aplikację, klikając pozycję IIS Express ![Ikona zrzut ekranu programu Visual Studio usług IIS Express](./media/app-insights-asp-net-core/0012-iis-express.png)

2. Przejdź do **o** stronę, aby wyzwolić wyjątek testu. (Jeśli system jest uruchomiony w trybie debugowania, należy kliknąć **Kontynuuj** w programie Visual Studio przed wyjątek będą pobierane przez usługę Application Insights.)

3. Uruchom ponownie symulowane skryptu transakcji programu PowerShell z wcześniej (może być konieczne dostosowanie numer portu w skrypcie).

4. Jeśli Przegląd Insights aplikacji nie jest nadal otwarty, z menu programu Visual Studio, wybierz **projektu** > **usługi Application Insights** > **Otwórz aplikacji Insights Portal**. 

   > [!TIP]
   > Jeśli nie widzisz jeszcze nowego ruchu, sprawdź **zakres czasu** i kliknij przycisk **Odśwież**.

   ![Okno zrzut ekranu — omówienie](./media/app-insights-asp-net-core/0019-overview-updated.png)

5. Wybierz strumień na żywo

   ![Zrzut ekranu przedstawiający strumień na żywo metryk](./media/app-insights-asp-net-core/0020-live-metrics-stream.png)

   (Jeśli skrypt programu PowerShell jest nadal uruchomiona, powinny pojawić się na żywo metryk, jeśli została zatrzymana, uruchom skrypt ponownie z strumień na żywo Otwórz.)

## <a name="app-insights-sdk-comparison"></a>Porównanie SDK Insights aplikacji

Twarde Praca grupy produktów usługi Application Insights do osiągnięcia maksymalnie zbliżony parzystość funkcji możliwie między [pełnego zestawu .NET Framework SDK](https://github.com/Microsoft/ApplicationInsights-dotnet) i .net Core SDK. 2.2.0 wydanie [platformy ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore) dla usługi Application Insights przede wszystkim zamknął luki funkcji.

Aby dowiedzieć się więcej na temat różnic oraz wady i zalety między [.NET i .NET Core](https://docs.microsoft.com/en-us/dotnet/standard/choosing-core-framework-server).

   | Porównanie zestawu SDK | ASP.NET        | ASP.NET Core 2.1.0    | ASP.NET Core 2.2.0 |
  |:-- | :-------------: |:------------------------:|:----------------------:|
   | **Metryki na żywo**      | **+** |**-** | **+** |
   | **Kanał danych Telemetrycznych serwera** | **+** |**-** | **+**|
   |**Adaptacyjną pobierania próbek**| **+** | **-** | **+**|
   | **Wywołania zależności SQL**     | **+** |**-** | **+**|
   | **Liczniki wydajności*** | **+** | **-**| **-**|

_Liczniki wydajności_ w tym kontekście odnosi się do [liczniki wydajności po stronie serwera](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-performance-counters) , takich jak procesor, pamięć i użycie dysku.

## <a name="open-source-sdk"></a>Zestaw SDK open source
[Przeczytaj i brać udział w kodzie](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates)

## <a name="video"></a>Połączenia wideo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player] 

## <a name="next-steps"></a>Kolejne kroki
* [Eksploruj przepływa użytkowników](app-insights-usage-flows.md) zrozumieć, jak użytkownicy nawigują między aplikacji.
* [Konfigurowanie zbierania migawek](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger#configure-snapshot-collection-for-aspnet-core-20-applications) aby zobaczyć stan kodu źródłowego i zmiennych w tej chwili jest zgłaszany wyjątek.
* [Za pomocą interfejsu API](app-insights-api-custom-events-metrics.md) wysyłanie własnych zdarzenia i metryki dla bardziej szczegółowy widok wydajności i użycia aplikacji.
* [Badania dostępności](app-insights-monitor-web-app-availability.md) Sprawdź aplikację stale z całego świata.

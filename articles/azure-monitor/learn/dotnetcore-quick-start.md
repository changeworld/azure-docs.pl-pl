---
title: Szybki start — Azure Application Insights | Microsoft Docs
description: Przedstawiono instrukcje pozwalające szybko skonfigurować aplikację internetową ASP.NET Core, do monitorowania za pomocą usługi Application Insights
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 03/29/2019
ms.service: application-insights
ms.custom: mvc
ms.topic: quickstart
manager: carmonm
ms.openlocfilehash: 7700efb7bfdb60c8f8f82b442cf01aae691f2fc3
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2019
ms.locfileid: "65595583"
---
# <a name="start-monitoring-your-aspnet-core-web-application"></a>Rozpoczynanie monitorowania aplikacji internetowej ASP.NET Core

Usługa Azure Application Insights umożliwia łatwe monitorowanie dostępności, wydajności i użycia aplikacji internetowej. Pozwala też szybko identyfikować i diagnozować błędy w aplikacji bez oczekiwania na zgłoszenie ich przez użytkownika. 

Ten przewodnik Szybki Start przeprowadzi Cię przez proces dodawania zestawu SDK usługi Application Insights do istniejącej aplikacji sieci web platformy ASP.NET Core. Aby dowiedzieć się więcej o konfigurowaniu usługi Application Insights bez wyewidencjonowania programu Visual Studio, to [artykułu](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core-no-visualstudio).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start:

- [Instalowanie programu Visual Studio 2019](https://www.visualstudio.com/downloads/) z następującymi pakietami roboczymi:
  - ASP.NET i tworzenie aplikacji internetowych
  - Programowanie na platformie Azure
- [Instalowanie zestawu SDK programu .NET Core 2.0](https://www.microsoft.com/net/core)
- Wymagana jest subskrypcja platformy Azure i istniejąca aplikacja internetowa .NET Core.

Jeśli masz aplikację sieci web platformy ASP.NET Core, można użyć naszego przewodnik krok po kroku [tworzenie aplikacji ASP.NET Core i Dodaj usługę Application Insights.](../../azure-monitor/app/asp-net-core.md)

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="enable-application-insights"></a>Włącz usługę Application Insights

Usługa Application Insights umożliwia zbieranie danych telemetrycznych z dowolnej aplikacji połączonej z Internetem, niezależnie od tego, czy jest to aplikacja lokalna czy aplikacja w chmurze. Aby rozpocząć wyświetlanie tych danych, wykonaj poniższe czynności.

1. Wybierz pozycję **Utwórz zasób** > **Narzędzia deweloperskie** > **Application Insights**.

   ![Dodawanie zasobu usługi Application Insights](./media/dotnetcore-quick-start/1createresourceappinsight.png)

    Zostanie wyświetlone okno konfiguracji. Wypełnij pola wejściowe, używając poniższej tabeli.

    | Ustawienia        |  Wartość           | Opis  |
   | ------------- |:-------------|:-----|
   | **Nazwa**      | Wartość unikatowa w skali globalnej | Nazwa identyfikująca monitorowaną aplikację |
   | **Typ aplikacji** | Aplikacja internetowa platformy ASP.NET | Typ monitorowanej aplikacji |
   | **Grupa zasobów**     | myResourceGroup      | Nazwa nowej grupy zasobów hostującej dane usługi App Insights |
   | **Lokalizacja** | Wschodnie stany USA | Wybierz lokalizację w pobliżu Ciebie lub w pobliżu miejsca hostowania aplikacji |

2. Kliknij pozycję **Utwórz**.

## <a name="configure-app-insights-sdk"></a>Konfigurowanie zestawu SDK usługi App Insights

1. Otwórz **projekt** aplikacji internetowej ASP.NET Core w programie Visual Studio, kliknij prawym przyciskiem myszy nazwę aplikacji w **Eksploratorze rozwiązań**, a następnie wybierz pozycję **Dodaj**  >  **Telemetria usługi Application Insights**.

    ![Dodawanie telemetrii usługi Application Insights](./media/dotnetcore-quick-start/2vsaddappinsights.png)

2. Kliknij przycisk **wprowadzenie** przycisku

3. Wybierz swoje konto i Subskrypcja > Wybierz **istniejący zasób** utworzone w witrynie Azure portal > kliknij **zarejestrować**.

4. Wybierz pozycję **Debuguj**  >  **Uruchom bez debugowania** (Ctrl+F5), aby uruchomić aplikację

    ![Menu Przegląd usługi Application Insights](./media/dotnetcore-quick-start/3debug.png)

> [!NOTE]
> Dane zaczną pojawiać się w portalu po 3–5 minutach. W przypadku aplikacji testowej o małym natężeniu ruchu należy pamiętać, że większość metryk jest przechwytywana tylko wtedy, gdy istnieją aktywne żądania lub operacje.

## <a name="start-monitoring-in-the-azure-portal"></a>Rozpoczynanie monitorowania w witrynie Azure Portal

1. Otwórz usługę Application Insights **Przegląd** strony w witrynie Azure portal, wybierając **Home** i w obszarze najnowszych zasobów wybierz zasób został utworzony wcześniej, aby wyświetlić szczegółowe informacje o aktualnie uruchomionych aplikacja.

   ![Menu Przegląd usługi Application Insights](./media/dotnetcore-quick-start/4overview.png)

2. Kliknij pozycję **Mapa aplikacji**, aby uzyskać wizualny układ relacji zależności między składnikami aplikacji. Każdy składnik przedstawia kluczowe wskaźniki wydajności, takie jak obciążenie, wydajność, błędy i alerty.

   ![Mapa aplikacji](./media/dotnetcore-quick-start/5appmap.png)

3. Kliknij pozycję **analizy aplikacji** ikonę ![ikona mapy aplikacji](./media/dotnetcore-quick-start/006.png) **Wyświetl w obszarze analiza**. Spowoduje to otwarcie strony **Analiza usługi Application Insights**, która udostępnia zaawansowany język zapytań na potrzeby analizy wszystkich danych zbieranych przez usługę Application Insights. W tym przypadku jest generowane zapytanie, które renderuje liczbę żądań w formie wykresu. Możesz pisać własne zapytania do analizy innych danych.

   ![Wykres analizy żądań użytkowników w danym okresie](./media/dotnetcore-quick-start/6analytics.png)

4. Wróć do strony **Przegląd** i sprawdź pulpity nawigacyjne kluczowych wskaźników wydajności.  Ten pulpit nawigacyjny przedstawia dane statystyczne dotyczące kondycji aplikacji, w tym liczbę żądań przychodzących, czas trwania tych żądań i błędy. 

   ![Wykresy osi czasu przeglądu kondycji](./media/dotnetcore-quick-start/7kpidashboards.png)

5. Po kliknięciu lewym na **metryki**. Skorzystaj z Eksploratora metryk do badania kondycji i wykorzystania zasobu. Możesz kliknąć pozycję **Dodaj nowy wykres**, aby utworzyć dodatkowe widoki niestandardowe, lub wybrać pozycję **Edytuj**, aby zmodyfikować istniejące typy wykresów, wysokość, paletę kolorów, grupowanie i metryki. Na przykład można utworzyć wykres, wyświetlającą czas ładowania strony przeglądarki średnia, wprost wybierając "Czas ładowania strony przeglądarki" z rozwijanego metryki i "Średnia" z agregacji. Aby dowiedzieć się więcej o Eksploratorze metryk platformy Azure, odwiedź stronę [wprowadzenie do Eksploratora metryk usługi Azure](../../azure-monitor/platform/metrics-getting-started.md).

     ![Karta metryki: Wykres czasu ładowania strony przeglądarki średni](./media/dotnetcore-quick-start/8metrics.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Po zakończeniu testowania, możesz usunąć grupę zasobów i wszystkie pokrewne zasoby. Można więc wykonaj poniższe kroki.

1. W menu znajdującym się po lewej stronie w witrynie Azure Portal kliknij pozycję **Grupy zasobów**, a następnie kliknij pozycję **myResourceGroup**.
2. Na stronie grupy zasobów kliknij pozycję **Usuń**, wpisz w polu tekstowym nazwę **myResourceGroup**, a następnie kliknij pozycję **Usuń**.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wyszukiwanie i diagnozowanie wyjątków w czasie wykonywania](https://docs.microsoft.com/azure/application-insights/app-insights-tutorial-runtime-exceptions)

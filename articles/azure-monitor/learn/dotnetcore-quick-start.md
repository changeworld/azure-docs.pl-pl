---
title: Szybki start ASP.NET Core — usługa Azure Monitor Application Insights
description: Zawiera instrukcje szybkiego konfigurowania ASP.NET core web app do monitorowania za pomocą usługi Azure Monitor Application Insights
ms.subservice: application-insights
ms.topic: quickstart
author: mrbullwinkle
ms.author: mbullwin
ms.date: 06/26/2019
ms.custom: mvc
ms.openlocfilehash: ef46b86186d1f5e26360de891b3a090ab0ece66b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78894813"
---
# <a name="start-monitoring-your-aspnet-core-web-application"></a>Rozpoczynanie monitorowania aplikacji internetowej ASP.NET Core

Usługa Azure Application Insights umożliwia łatwe monitorowanie dostępności, wydajności i użycia aplikacji internetowej. Pozwala też szybko identyfikować i diagnozować błędy w aplikacji bez oczekiwania na zgłoszenie ich przez użytkownika. 

Ten przewodnik szybki start prowadzi użytkownika przez dodawanie SDK usługi Application Insights do istniejącej aplikacji sieci web ASP.NET Core. Aby dowiedzieć się więcej o konfigurowaniu usługi Application Insights bez wyewidencjonowania programu Visual Studio w tym [artykule](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core).

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start:

- [Zainstaluj program Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) z następującymi obciążeniami:
  - Tworzenie aplikacji na platformie ASP.NET i aplikacji internetowych
  - Tworzenie aplikacji na platformie Azure
- [Instalowanie zestawu SDK programu .NET Core 2.0](https://dotnet.microsoft.com/download)
- Wymagana jest subskrypcja platformy Azure i istniejąca aplikacja internetowa .NET Core.

Jeśli nie masz aplikacji sieci web ASP.NET Core, możesz użyć naszego przewodnika krok po kroku, aby [utworzyć aplikację ASP.NET Core i dodać usługa Application Insights.](../../azure-monitor/app/asp-net-core.md)

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne](https://azure.microsoft.com/free/) konto przed rozpoczęciem.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do [Portalu Azure](https://portal.azure.com/).

## <a name="enable-application-insights"></a>Włączanie usługi Application Insights

Usługa Application Insights umożliwia zbieranie danych telemetrycznych z dowolnej aplikacji połączonej z Internetem, niezależnie od tego, czy jest to aplikacja lokalna czy aplikacja w chmurze. Aby rozpocząć wyświetlanie tych danych, wykonaj poniższe czynności.

1. Wybierz pozycję **Utwórz narzędzie** > **Developer tools** > deweloperskie aplikacji **.**

   > [!NOTE]
   >Jeśli jest to pierwszy raz tworzenia zasobu usługi Application Insights można dowiedzieć się więcej, odwiedzając [utwórz plik doc zasobów usługi Application Insights.](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)

    Zostanie wyświetlone okno konfiguracji. Wypełnij pola wejściowe, używając poniższej tabeli.

   | Ustawienia        |  Wartość           | Opis  |
   | ------------- |:-------------|:-----|
   | **Nazwa**      | Wartość unikatowa w skali globalnej | Nazwa identyfikująca monitorowaną aplikację |
   | **Grupa zasobów**     | myResourceGroup      | Nazwa nowej grupy zasobów do hosta danych usługi App Insights. istnieje możliwość utworzenia nowej lub użycia istniejącej grupy zasobów. |
   | **Lokalizacja** | Wschodnie stany USA | Wybierz lokalizację w pobliżu Ciebie lub w pobliżu miejsca hostowania aplikacji |

2. Kliknij przycisk **Utwórz**.



## <a name="configure-app-insights-sdk"></a>Konfigurowanie zestawu SDK usługi App Insights

1. Otwórz **projekt** aplikacji internetowej ASP.NET Core w programie Visual Studio, kliknij prawym przyciskiem myszy nazwę aplikacji w **Eksploratorze rozwiązań**, a następnie wybierz pozycję **Dodaj** > **Telemetria usługi Application Insights**.

    ![Dodawanie telemetrii usługi Application Insights](./media/dotnetcore-quick-start/2vsaddappinsights.png)

2. Kliknij przycisk **Wprowadzenie**

3. Wybierz swoje konto i subskrypcję > Wybierz **istniejący zasób** utworzony w witrynie Azure portal > **kliknij przycisk Zarejestruj**się .

4. Wybierz**źródło pakietu** > **Pakietów** > Pakietu Programu **Project** > Manage: nuget.org**zaktualizowanie** pakietów zestawu SDK usługi Application Insights do najnowszej stabilnej wersji.

5. Wybierz **przycisk Start debugowania** > **bez debugowania** (Ctrl+F5), aby uruchomić aplikację

    ![Menu Przegląd usługi Application Insights](./media/dotnetcore-quick-start/3debug.png)

> [!NOTE]
> Dane zaczną pojawiać się w portalu po 3–5 minutach. W przypadku aplikacji testowej o małym natężeniu ruchu należy pamiętać, że większość metryk jest przechwytywana tylko wtedy, gdy istnieją aktywne żądania lub operacje.

## <a name="start-monitoring-in-the-azure-portal"></a>Rozpoczynanie monitorowania w witrynie Azure Portal

1. Otwórz ponownie stronę **Przegląd** usługi Application Insights w witrynie Azure portal, wybierając **pozycję Narzędzia domowe** i w obszarze ostatnie zasoby, wybierz zasób utworzony wcześniej, aby wyświetlić szczegółowe informacje o aktualnie uruchomionej aplikacji.

   ![Menu Przegląd usługi Application Insights](./media/dotnetcore-quick-start/4overview.png)

2. Kliknij pozycję **Mapa aplikacji**, aby uzyskać wizualny układ relacji zależności między składnikami aplikacji. Każdy składnik przedstawia kluczowe wskaźniki wydajności, takie jak obciążenie, wydajność, błędy i alerty.

   ![Mapa aplikacji](./media/dotnetcore-quick-start/5appmap.png)

3. Kliknij ikonę ![](./media/dotnetcore-quick-start/006.png) Mapy aplikacji Analizy **aplikacji** **Wyświetl ikonę w Analytics**. Spowoduje to otwarcie strony **Analiza usługi Application Insights**, która udostępnia zaawansowany język zapytań na potrzeby analizy wszystkich danych zbieranych przez usługę Application Insights. W tym przypadku jest generowane zapytanie, które renderuje liczbę żądań w formie wykresu. Możesz pisać własne zapytania do analizy innych danych.

   ![Wykres analizy żądań użytkowników w danym okresie](./media/dotnetcore-quick-start/6analytics.png)

4. Wróć do strony **Przegląd** i sprawdź pulpity nawigacyjne kluczowych wskaźników wydajności.  Ten pulpit nawigacyjny przedstawia dane statystyczne dotyczące kondycji aplikacji, w tym liczbę żądań przychodzących, czas trwania tych żądań i błędy. 

   ![Wykresy osi czasu przeglądu kondycji](./media/dotnetcore-quick-start/7kpidashboards.png)

5. Po lewej stronie kliknij **metryki**. Użyj Eksploratora metryk, aby zbadać kondycję i wykorzystanie zasobu. Możesz kliknąć pozycję **Dodaj nowy wykres**, aby utworzyć dodatkowe widoki niestandardowe, lub wybrać pozycję **Edytuj**, aby zmodyfikować istniejące typy wykresów, wysokość, paletę kolorów, grupowanie i metryki. Na przykład można zrobić wykres, który wyświetla średni czas ładowania strony przeglądarki, wybierając "Czas ładowania strony przeglądarki" z listy rozwijanej metryki i "Avg" z agregacji. Aby dowiedzieć się więcej o Eksploratorze metryk platformy Azure odwiedź wprowadzenie [do Eksploratora metryk platformy Azure.](../../azure-monitor/platform/metrics-getting-started.md)

     ![Karta Metryki: Wykres średniego czasu ładowania strony przeglądarki](./media/dotnetcore-quick-start/8metrics.png)

## <a name="video"></a>Film wideo

- Zewnętrzny film krok po kroku dotyczący [konfigurowania usługi Application Insights za pomocą programu .NET Core i programu Visual Studio](https://www.youtube.com/watch?v=NoS9UhcR4gA&t) od podstaw.
- Zewnętrzny film krok po kroku dotyczący [konfigurowania usługi Application Insights za pomocą programu .NET Core i visual studio code](https://youtu.be/ygGt84GDync) od podstaw.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Po zakończeniu testowania można usunąć grupę zasobów i wszystkie powiązane zasoby. Aby to zrobić, wykonaj poniższe czynności.

> [!NOTE]
> Jeśli użyto istniejącej grupy zasobów, poniższe instrukcje nie będą działać i trzeba będzie po prostu usunąć pojedynczy zasób usługi Application Insights. Pamiętaj, że w każdej chwili usuniesz grupę zasobów, wszystkie zasoby, które są członkami tej grupy, zostaną usunięte.

1. W menu znajdującym się po lewej stronie w witrynie Azure Portal kliknij pozycję **Grupy zasobów**, a następnie kliknij pozycję **myResourceGroup**.
2. Na stronie grupy zasobów kliknij pozycję **Usuń**, wpisz **myResourceGroup** w polu tekstowym, a następnie kliknij pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wyszukiwanie i diagnozowanie wyjątków w czasie wykonywania](https://docs.microsoft.com/azure/application-insights/app-insights-tutorial-runtime-exceptions)

---
title: Debugowanie aplikacji przy użyciu usługi Azure Application Insights w programie Visual Studio | Dokumentacja firmy Microsoft
description: Analiza wydajności i diagnostyka aplikacji internetowej podczas debugowania oraz w środowisku produkcyjnym.
services: application-insights
documentationcenter: .net
author: NumberByColors
manager: carmonm
ms.assetid: 2059802b-1131-477e-a7b4-5f70fb53f974
ms.service: application-insights
ms.custom: vs-azure
ms.workload: azure-vs
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/07/2017
ms.pm_owner: daviste;NumberByColors
ms.reviewer: mbullwin
ms.author: daviste
ms.openlocfilehash: 1b2f429129c0bb9098f4f5029cb07ce06bc5db13
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66255131"
---
# <a name="debug-your-applications-with-azure-application-insights-in-visual-studio"></a>Debugowanie aplikacji przy użyciu usługi Azure Application Insights w programie Visual Studio
W programie Visual Studio (w wersji 2015 i nowszych) można analizować wydajność i diagnozować problemy w aplikacji internetowej platformy ASP.NET zarówno podczas debugowania, jak i w środowisku produkcyjnym, przy użyciu telemetrii z usługi [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md).

Jeśli aplikację internetową platformy ASP.NET utworzono przy użyciu programu Visual Studio 2017 lub nowszego, zawiera już ona zestaw SDK usługi Application Insights. W przeciwnym razie, jeśli jeszcze tego nie zrobiono, należy [dodać usługę Application Insights do aplikacji](../../azure-monitor/app/asp-net.md).

Monitorowanie aplikacji w środowisku produkcyjnym zwykle polega na przeglądaniu danych telemetrycznych z usługi Application Insights w witrynie [Azure Portal](https://portal.azure.com), w której można ustawiać alerty i stosować zaawansowane narzędzia do monitorowania. Jednak na potrzeby debugowania można również wyszukiwać i analizować dane telemetryczne w programie Visual Studio. Aby analizować dane telemetryczne, zarówno z witryny produkcyjnej i debugowanie uruchomienia na komputerze deweloperskim, można użyć programu Visual Studio. W tym ostatnim przypadku można analizować przebiegi debugowania, nawet jeśli nie skonfigurowano jeszcze zestawu SDK na potrzeby wysyłania danych telemetrycznych do witryny Azure Portal. 

## <a name="run"></a> Debugowanie projektu
Uruchom aplikację internetową w trybie debugowania lokalnego, naciskając klawisz F5. Otwórz różne strony w celu wygenerowania telemetrii.

W programie Visual Studio zobaczysz liczbę zdarzeń, które zostały zarejestrowane przez moduł Application Insights w projekcie.

![Przycisk usługi Application Insights jest widoczny w programie Visual Studio podczas debugowania.](./media/visual-studio/appinsights-09eventcount.png)

Kliknij ten przycisk, aby wyszukać dane telemetryczne. 

## <a name="application-insights-search"></a>Wyszukiwanie usługi Application Insights
Okno wyszukiwania usługi Application Insights pokazuje zarejestrowane zdarzenia. (Jeśli zalogowano do platformy Azure podczas konfigurowania usługi Application Insights, można wyszukiwać te same zdarzenia w witrynie Azure portal.)

![Kliknij prawym przyciskiem myszy projekt i wybierz kolejno opcje Application Insights, Wyszukiwanie](./media/visual-studio/34.png)

> [!NOTE] 
> Po wybraniu lub anulowaniu wyboru filtrów kliknij przycisk wyszukiwania na końcu tekstowego pola wyszukiwania.
>

Wyszukiwanie dowolnego tekstu działa we wszystkich polach zdarzeń. Można wyszukać na przykład część adresu URL strony, wartość właściwości (taką jak miasto klienta) lub określone słowa w dzienniku śledzenia.

Kliknij dowolne zdarzenie, aby wyświetlić jego szczegółowe właściwości.

W celu wyświetlenia żądań wysyłanych do aplikacji internetowej można kliknąć w obrębie kodu.

![W obszarze Szczegóły żądania kliknij w obrębie kodu](./media/visual-studio/31.png)

Można również otworzyć elementy pokrewne, aby łatwiej diagnozować niepowodzenia żądań lub wyjątki.

![W obszarze Szczegóły żądania przewiń w dół do elementów pokrewnych](./media/visual-studio/41.png)

## <a name="view-exceptions-and-failed-requests"></a>Wyświetl wyjątki i żądania zakończone niepowodzeniem
Raporty dotyczące wyjątków są wyświetlane w oknie wyszukiwania. W niektórych starszych typach aplikacji sieci Web platformy ASP.NET trzeba [skonfigurować monitorowanie wyjątków](../../azure-monitor/app/asp-net-exceptions.md), aby zobaczyć wyjątki, które są obsługiwane przez architekturę.

Kliknij wyjątek, aby uzyskać ślad stosu. Jeśli kod aplikacji jest otwarty w programie Visual Studio, można przejść przez ślad stosu do odpowiedniego wiersza kodu.

![Ślad stosu wyjątków](./media/visual-studio/17.png)

## <a name="view-request-and-exception-summaries-in-the-code"></a>Wyświetlanie podsumowania żądań i wyjątków w kodzie
W wierszu Code Lens powyżej każdej metody procedury obsługi można zobaczyć liczbę żądań i wyjątków zarejestrowanych przez usługę Application Insights w ciągu ostatnich 24 godzin.

![Ślad stosu wyjątków](./media/visual-studio/21.png)

> [!NOTE] 
> Dane usługi Application Insights są wyświetlane w wierszach Code Lens tylko wtedy, gdy [skonfigurowano aplikację na potrzeby wysyłania danych telemetrycznych do portalu usługi Application Insights](../../azure-monitor/app/asp-net.md).
>

[Więcej informacji o usłudze Application Insights w wierszach Code Lens](../../azure-monitor/app/visual-studio-codelens.md)

## <a name="trends"></a>Trends
Trends to narzędzie do wizualizacji zachowania aplikacji wraz z upływem czasu. 

Na przycisku paska narzędzi Application Insights lub w oknie Application Insights Search wybierz pozycję **Eksploruj trendy telemetryczne**. Wybierz jedno z pięciu typowych zapytań, aby rozpocząć. W zależności od typów telemetrii, zakresów czasu i innych właściwości możesz analizować różne zestawy danych . 

Aby znaleźć anomalie w danych, wybierz jedną z opcji anomalii w menu rozwijanym „Typ widoku”. Opcje filtrowania u dołu okna ułatwiają sprecyzowanie konkretnych podzestawów w telemetrii.

![Trends](./media/visual-studio/51.png)

[Więcej informacji na temat narzędzia Trends](../../azure-monitor/app/visual-studio-trends.md).

## <a name="local-monitoring"></a>Monitorowanie lokalne
(Z programu Visual Studio 2015 Update 2) Jeśli nie skonfigurowano zestawu SDK do wysyłania telemetrii do portalu usługi Application Insights (tak, aby w pliku ApplicationInsights.config, istnieje nie klucza Instrumentacji) w oknie diagnostyki przedstawia dane telemetryczne z ostatniej sesji debugowania. 

Jest to pożądane, jeśli poprzednia wersji aplikacji została już opublikowana. Lepiej, aby telemetria z sesji debugowania nie była mieszana z telemetrią z opublikowanej aplikacji w portalu Application Insights.

Jest to również przydatne, jeśli masz trochę [niestandardowej telemetrii](../../azure-monitor/app/api-custom-events-metrics.md), którą chcesz debugować przed wysłaniem telemetrii do portalu.

* *Na początku usługa Application Insights została skonfigurowana do wysyłania telemetrii do portalu, ale teraz chcę widzieć telemetrię tylko w programie Visual Studio.*
  
  * W ustawieniach okna wyszukiwania istnieje możliwość wyszukiwania lokalnych danych diagnostycznych, nawet jeśli aplikacja wysyła telemetrię do portalu.
  * Aby zatrzymać przesyłanie telemetrii do portalu, zakomentuj wiersz `<instrumentationkey>...` w pliku ApplicationInsights.config. Gdy wszystko będzie gotowe, odkomentuj wiersz, aby wznowić wysyłanie telemetrii do portalu.


## <a name="next-steps"></a>Kolejne kroki
|  |  |
| --- | --- |
| **[Dodawanie większej ilości danych](../../azure-monitor/app/asp-net-more.md)**<br/>Monitorowanie użycia, dostępności, zależności i wyjątków. Integrowanie śladów ze struktur rejestrowania. Zapisywanie niestandardowych danych telemetrycznych. |![Visual Studio](./media/visual-studio/64.png) |
| **[Praca z portalem usługi Application Insights](../../azure-monitor/app/overview-dashboard.md)**<br/>Wyświetlać pulpity nawigacyjne, zaawansowane narzędzia diagnostyczne i analityczne, alerty, mapa zależności na żywo, aplikacji i danych telemetrycznych wyeksportowany. |![Visual Studio](./media/visual-studio/62.png) |


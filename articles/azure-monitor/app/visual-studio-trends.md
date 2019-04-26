---
title: Analizowanie trendów w programie Visual Studio | Microsoft Docs
description: Analizuj, wizualizuj i eksploruj trendy w telemetrii usługi Application Insights w programie Visual Studio.
services: application-insights
documentationcenter: .net
author: NumberByColors
manager: carmonm
ms.assetid: 3150c6fc-2691-44f6-a290-fc5cd68e692a
ms.service: application-insights
ms.custom: vs-azure
ms.workload: azure-vs
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/17/2017
ms.reviewer: mbullwin
ms.pm_owner: daviste;NumberByColors
ms.author: daviste
ms.openlocfilehash: 898f0974a6a29abde5c84d611adc8d50c3873141
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60372790"
---
# <a name="analyzing-trends-in-visual-studio"></a>Analizowanie trendów w programie Visual Studio
Narzędzie Trendy usługi Application Insights wizualizuje sposób, w jaki ważne zdarzenia telemetrii aplikacji internetowej zmieniają się z upływem czasu, co ułatwia szybkie identyfikowanie problemów i anomalii. Łącząc Cię z bardziej szczegółowymi informacjami diagnostycznymi, narzędzie Trendy może ułatwić zwiększenie wydajności aplikacji, śledzenie przyczyn wyjątków oraz odkrywanie szczegółowych danych w zdarzeniach niestandardowych.

![Przykładowe okno narzędzia Trendy](./media/visual-studio-trends/app-insights-trends-hero-750.png)

## <a name="configure-your-web-app-for-application-insights"></a>Konfigurowanie aplikacji internetowej na potrzeby usługi Application Insights

Jeśli nie zostało to jeszcze zrobione, [skonfiguruj swoją aplikację internetową na potrzeby usługi Application Insights](../../azure-monitor/app/app-insights-overview.md). Dzięki temu będzie można wysyłać dane telemetryczne do portalu usługi Application Insights. Narzędzie Trendy odczytuje dane telemetryczne z tego miejsca.

Narzędzie Trendy usługi Application Insights jest dostępne w programie Visual Studio 2015 Update 3 i nowszych wersjach.

## <a name="open-application-insights-trends"></a>Otwieranie narzędzia Trendy usługi Application Insights
Aby otworzyć okno narzędzia Trendy usługi Application Insights:

* Na przycisku paska narzędzi Application Insights wybierz pozycję **Eksploruj trendy telemetrii** lub
* Z menu kontekstowego projektu wybierz pozycję **Application Insights > Eksploruj trendy telemetrii** lub
* Na pasku menu programu Visual Studio wybierz pozycję **Widok > Inne okna > Trendy usługi Application Insights**.

Może zostać wyświetlony monit o wybranie zasobu. Kliknij pozycję **Wybierz zasób**, zaloguj się przy użyciu subskrypcji platformy Azure, a następnie wybierz z listy zasób usługi Application Insights, dla którego chcesz analizować trendy telemetryczne.

## <a name="choose-a-trend-analysis"></a>Wybieranie analizy trendów
![Menu popularnych typów analizy trendów](./media/visual-studio-trends/app-insights-trends-1-750.png)

Zacznij od wybrania jednej z pięciu typowych analiz trendów, które analizują dane z ostatnich 24 godzin:

* **Zbadaj problemy z wydajnością w żądaniach serwera** — żądania wysłane do usługi pogrupowane według czasu odpowiedzi
* **Przeanalizuj błędy żądań serwera** — żądania wysłane do usługi pogrupowane według kodu odpowiedzi HTTP
* **Przeanalizuj wyjątki aplikacji** — wyjątki od usługi pogrupowane według typu wyjątku
* **Sprawdzanie wydajności zależności aplikacji** — usługi wywołane przez Twoją usługę pogrupowane według czasu odpowiedzi
* **Zbadaj zdarzenia niestandardowe** — zdarzenia niestandardowe skonfigurowane dla Twojej usługi, pogrupowane według typu zdarzenia.

Dostęp do tych wstępnie skompilowanych analiz można uzyskać później, naciskając przycisk **Wyświetl popularne typy analizy telemetrii** w lewym górnym rogu okna narzędzia Trendy.

## <a name="visualize-trends-in-your-application"></a>Wizualizowanie trendów w aplikacji
Narzędzie Trendy usługi Application Insights tworzy wizualizację szeregu czasowego w oparciu o telemetrię aplikacji. W każdej wizualizacji szeregu czasowego jest wyświetlany jeden typ telemetrii, pogrupowany według jednej właściwości tej telemetrii, w określonym zakresie czasu. Na przykład możesz wyświetlić żądania serwera z ostatnich 24 godzin pogrupowane według kraju, z którego pochodzą. W tym przykładzie każdy bąbelek na wizualizacji reprezentuje liczbę żądań serwera dla określonego kraju/regionu w ciągu jednej godziny.

Aby dostosować wyświetlane typy telemetrii, użyj kontrolek w górnej części okna. Najpierw wybierz interesujące Cię typy telemetrii:

* **Typ telemetrii** — żądania serwera, wyjątki, zależności lub zdarzenia niestandardowe
* **Zakres czasu** — dowolny zakres od ostatnich 30 minut do ostatnich 3 dni
* **Grupuj według** — typ wyjątku, identyfikator problemu, kraj/region itd.

Następnie kliknij pozycję **Analizuj telemetrię**, aby uruchomić zapytanie.

Aby poruszać się między bąbelkami w wizualizacji:

* Kliknij, aby wybrać bąbelek, który aktualizuje filtry w dolnej części okna, podsumowując tylko te zdarzenia, które wystąpiły w określonym czasie
* Kliknij dwukrotnie bąbelek, aby przejść do narzędzia Search i wyświetlić wszystkie zdarzenia telemetrii, które wystąpiły w tym okresie
* Naciśnij klawisz Ctrl i kliknij bąbelek, aby usunąć jego zaznaczenie w wizualizacji.

> [!TIP]
> Narzędzia Trendy i Wyszukiwanie współpracują ze sobą, aby ułatwić wykrycie przyczyn problemów z usługą wśród tysięcy zdarzeń telemetrii. Jeśli na przykład pewnego popołudnia klient zauważy, że aplikacja reaguje wolniej niż do tej pory, najpierw użyj narzędzia Trendy. Przeanalizuj żądania wysłane do usługi w ciągu ostatnich kilku godzin, pogrupowane według czasu odpowiedzi. Sprawdź, czy nie ma nietypowo dużej liczby powolnych żądań. Następnie kliknij dwukrotnie ten bąbelek, aby przejść do narzędzia Wyszukiwanie z włączonym filtrem tych zdarzeń żądania. W narzędziu Wyszukiwanie możesz zbadać zawartość tych żądań i przejść do odpowiedniego kodu, aby rozwiązać problem.
> 
> 

## <a name="filter"></a>Filtr
Odkryj bardziej szczegółowe trendy, używając kontrolek filtrów w dolnej części okna. Aby zastosować filtr, kliknij jego nazwę. Możliwość szybkiego przełączania się między różnymi filtrami ułatwia odkrywanie trendów, które mogą ukrywać się w konkretnym wymiarze telemetrii. Jeśli zastosujesz filtr w jednym wymiarze, na przykład Typ wyjątku, nadal możesz klikać filtry w innych wymiarach, nawet pomimo tego, że są one wyszarzone. Aby anulować zastosowanie filtru, kliknij go ponownie. Naciśnij klawisz Ctrl i kliknij, aby wybrać wiele filtrów w tym samym wymiarze.

![Filtry trendów](./media/visual-studio-trends/TrendsFiltering-750.png)

Co zrobić, jeśli chcesz zastosować wiele filtrów? 

1. Zastosuj pierwszy filtr. 
2. Kliknij przycisk **Zastosuj wybrane filtry i ponownie prześlij zapytanie** obok nazwy wymiaru pierwszego filtru. Spowoduje to ponowne odpytanie telemetrii pod kątem zdarzeń pasujących do pierwszego filtru. 
3. Zastosuj drugi filtr. 
4. Powtarzaj ten proces, aby znaleźć trendy w określonych podzestawach telemetrii. Na przykład żądania serwera nazwane „GET Home/Index” *oraz* te, które nadeszły z Niemiec *oraz* te, które otrzymały kod odpowiedzi 500. 

Aby anulować zastosowanie jednego z tych filtrów, kliknij przycisk **Usuń wybrane filtry i ponownie prześlij zapytanie** dla danego wymiaru.

![Wiele filtrów](./media/visual-studio-trends/TrendsFiltering2-750.png)

## <a name="find-anomalies"></a>Znajdowanie anomalii
Narzędzie Trendy umożliwia wyróżnianie bąbelków zdarzeń, które są nieprawidłowe w porównaniu do innych bąbelków w tym samym szeregu czasowym. Z menu rozwijanego Typ widoku wybierz pozycję **Liczby w przedziale czasu (wyróżnij anomalie)** lub **Wartości procentowe w przedziale czasu (wyróżnij anomalie)**. Czerwone bąbelki są nieprawidłowe. Anomalie są zdefiniowane jako bąbelki zawierające liczby/wartości procentowe przekraczające 2,1 raza standardowe odchylenie od liczb/wartości procentowych, które wystąpiły w ostatnich dwóch okresach (48 godzin w przypadku przeglądania ostatnich 24 godzin, itp.).

![Kolorowe punkty oznaczają anomalie](./media/visual-studio-trends/TrendsAnomalies-750.png)

> [!TIP]
> Proces wyróżniania anomalii może być szczególnie przydatny do znajdowania wartości odstających w szeregu czasowym małych bąbelków, które w przeciwnym razie mogłyby mieć podobny rozmiar.  
> 
> 

## <a name="next"></a>Następne kroki
|  |  |
| --- | --- |
| **[Praca z usługą Application Insights w programie Visual Studio](../../azure-monitor/app/visual-studio.md)**<br/>Wyszukiwanie danych telemetrycznych, wyświetlanie danych CodeLens i konfigurowanie usługi Application Insights. Wszystko to w programie Visual Studio. |![Kliknij prawym przyciskiem myszy projekt i wybierz kolejno opcje Application Insights, Wyszukiwanie](./media/visual-studio-trends/34.png) |
| **[Dodawanie większej ilości danych](../../azure-monitor/app/asp-net-more.md)**<br/>Monitorowanie użycia, dostępności, zależności i wyjątków. Integrowanie śladów ze struktur rejestrowania. Zapisywanie niestandardowych danych telemetrycznych. |![Visual Studio](./media/visual-studio-trends/64.png) |
| **[Praca z portalem usługi Application Insights](../../azure-monitor/app/app-insights-dashboards.md)**<br/>Pulpity nawigacyjne, zaawansowane narzędzia diagnostyczne i analityczne, alerty, mapa zależności aplikacji na żywo oraz eksportowanie telemetrii. |![Visual Studio](./media/visual-studio-trends/62.png) |


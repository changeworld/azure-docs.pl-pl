---
title: Analiza przechowywania użytkowników dla aplikacji sieci Web za pomocą usługi Azure Application Insights | Dokumenty firmy Microsoft
description: Ilu użytkowników powraca do aplikacji?
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 05/03/2017
ms.reviewer: mbullwin
ms.openlocfilehash: 37ae97d5fbc62e507f726c452999a7f6e7c989c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670971"
---
# <a name="user-retention-analysis-for-web-applications-with-application-insights"></a>Analiza przechowywania użytkowników dla aplikacji sieci web za pomocą usługi Application Insights

Funkcja przechowywania w [usłudze Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) ułatwia analizowanie liczby użytkowników powracających do aplikacji i jak często wykonują określone zadania lub osiągają cele. Na przykład, jeśli uruchomisz witrynę gry, możesz porównać liczbę użytkowników, którzy wracają do witryny po przegranej grze z numerem, który powraca po wygranej. Ta wiedza może pomóc poprawić zarówno doświadczenie użytkownika, jak i strategię biznesową.

## <a name="get-started"></a>Wprowadzenie

Jeśli nie widzisz jeszcze danych w narzędziu przechowywania w portalu usługi Application Insights, [dowiedz się, jak rozpocząć pracę z narzędziami użycia.](usage-overview.md)

## <a name="the-retention-tool"></a>Narzędzie Retencja

![Narzędzie utrzymywania](./media/usage-retention/retention.png)

1. Pasek narzędzi umożliwia użytkownikom tworzenie nowych raportów dotyczących przechowywania, otwieranie istniejących raportów dotyczących przechowywania, zapisywanie bieżącego raportu przechowywania lub zapisywanie jako, ponowne wprowadzanie zmian zapisanych raportów, odświeżanie danych w raporcie, udostępnianie raportu za pośrednictwem poczty e-mail lub bezpośredniego łącza oraz dostęp do dokumentacji Strona. 
2. Domyślnie retencja pokazuje wszystkich użytkowników, którzy cokolwiek zrobili, a następnie wrócili i zrobili cokolwiek innego w ciągu pewien okres. Można wybrać inną kombinację zdarzeń, aby zawęzić fokus na określonych działaniach użytkownika.
3. Dodaj jeden lub więcej filtrów właściwości. Na przykład można skupić się na użytkownikach w danym kraju lub regionie. Po ustawieniu filtrów kliknij **pozycję Aktualizuj.** 
4. Ogólny wykres przechowywania zawiera podsumowanie retencji użytkowników w wybranym okresie. 
5. Siatka pokazuje liczbę użytkowników zatrzymanych zgodnie z konstruktorem zapytań w #2. Każdy wiersz reprezentuje kohortę użytkowników, którzy wykonali dowolne zdarzenie w wyświetlonym okresie. Każda komórka w wierszu pokazuje, ile z tej kohorty zwróciło co najmniej raz w późniejszym okresie. Niektórzy użytkownicy mogą powrócić w ciągu więcej niż jednego okresu. 
6. Karty szczegółowych informacji pokazują pięć najważniejszych zdarzeń inicjujących i pięć najważniejszych zwróconych zdarzeń, aby zapewnić użytkownikom lepsze zrozumienie raportu przechowywania. 

![Wskaźnik myszy retencji](./media/usage-retention/hover.png)

Użytkownicy mogą najechać kursorem na komórki w narzędziu przechowywania, aby uzyskać dostęp do przycisku analizy i wskazówek dotyczących narzędzi wyjaśniających, co oznacza komórka. Przycisk Analytics przenosi użytkowników do narzędzia Analytics z wstępnie wypełnioną kwerendą w celu wygenerowania użytkowników z komórki. 

## <a name="use-business-events-to-track-retention"></a>Śledzenie retencji za pomocą zdarzeń biznesowych

Aby uzyskać najbardziej użyteczną analizę przechowywania, należy zmierzyć zdarzenia, które reprezentują znaczące działania biznesowe. 

Na przykład wielu użytkowników może otworzyć stronę w aplikacji bez grania w wyświetlaną przez nią grę. Śledzenie tylko odsłon strony zapewni zatem niedokładne oszacowanie, ile osób powraca do gry po wcześniejszym cieszeniu się nią. Aby uzyskać jasny obraz powracających graczy, aplikacja powinna wysłać zdarzenie niestandardowe, gdy użytkownik faktycznie gra.  

Dobrą praktyką jest kodowania zdarzeń niestandardowych, które reprezentują kluczowe akcje biznesowe i używać ich do analizy przechowywania. Aby przechwycić wynik gry, należy napisać wiersz kodu, aby wysłać zdarzenie niestandardowe do usługi Application Insights. Jeśli piszesz go w kodzie strony internetowej lub w Node.JS, wygląda to tak:

```JavaScript
    appinsights.trackEvent("won game");
```

Lub w ASP.NET kod serwera:

```csharp
   telemetry.TrackEvent("won game");
```

[Dowiedz się więcej o pisaniu zdarzeń niestandardowych](../../azure-monitor/app/api-custom-events-metrics.md#trackevent).


## <a name="next-steps"></a>Następne kroki
- Aby włączyć środowisko użycia, zacznij wysyłać [niestandardowe zdarzenia](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) lub [widoki strony](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Jeśli wysyłasz już niestandardowe zdarzenia lub widoki stron, zapoznaj się z narzędziami Użycia, aby dowiedzieć się, jak użytkownicy korzystają z usługi.
    - [Użytkownicy, sesje, zdarzenia](usage-segmentation.md)
    - [Lejki](usage-funnels.md)
    - [User Flows (Przepływy użytkowników)](usage-flows.md)
    - [Skoroszyty](../../azure-monitor/app/usage-workbooks.md)
    - [Dodawanie kontekstu użytkownika](usage-send-user-context.md)



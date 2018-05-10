---
title: Analiza przechowywania użytkownika dla aplikacji sieci web za pomocą usługi Azure Application Insights | Dokumentacja firmy Microsoft
description: Zwraca liczbę użytkowników do aplikacji?
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 05/03/2017
ms.author: mbullwin
ms.openlocfilehash: bafe9579cc783dbc5c851e626e3b9bea0c559520
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="user-retention-analysis-for-web-applications-with-application-insights"></a>Analiza przechowywania użytkownika dla aplikacji sieci web za pomocą usługi Application Insights

Funkcja przechowywania w [Azure Application Insights](app-insights-overview.md) pomaga analizować ilu użytkowników powrót do aplikacji i jak często wykonywać określone zadania lub osiągnięcia celów. Na przykład po uruchomieniu gier lokacji można porównywać liczby użytkowników, którzy wróć do witryny po utracie gry o numerze którzy powrócić po nadanie pierwszeństwa. Ta wiedza może pomóc w usprawnieniu zarówno użytkowników, jak i jej strategią biznesową.

## <a name="get-started"></a>Rozpoczęcie pracy

Jeśli nie widzisz jeszcze danych w narzędziu przechowywania w portalu usługi Application Insights [Dowiedz się, jak rozpocząć pracę z narzędziami użycia](app-insights-usage-overview.md).

## <a name="the-retention-tool"></a>Narzędzie przechowywania

![Narzędzie utrzymywania](./media/app-insights-usage-retention/retention.png)

1. Pasek narzędzi umożliwia tworzenia nowych raportów przechowywania, otwórz istniejących raportów przechowywania, Zapisz bieżący raport przechowywania lub Zapisz jako cofnięcie zmian wprowadzonych do zapisanych raportów, odświeżania danych w raporcie udostępnianie raportów za pośrednictwem poczty e-mail lub bezpośredniego łącza i uzyskać dostęp do strony dokumentacji. 
2. Domyślnie przechowywania zawiera wszystkich użytkowników, którzy niczego została następnie wrócił i czy czymkolwiek innym przez okres. Możesz wybrać inną kombinację zdarzeń, aby zawęzić na działania użytkownika.
3. Dodaj co najmniej jeden filtr właściwości. Na przykład można skoncentrować się na użytkowników w danym kraju lub regionu. Kliknij przycisk **aktualizacji** kliknij ikonę znacznika wyboru. 
4. Ogólny wykresu przechowywania przedstawia podsumowanie przechowywania użytkownika przez wybrany okres czasu. 
5. Siatka pokazuje liczbę użytkowników zachowane zgodnie z konstruktora zapytań w #2. Każdy wiersz reprezentuje kohorty użytkowników, który wykonał wszystkie zdarzenia w przedstawionym okresie. Każdej komórki w wierszu pokazuje, ile tego kohorty zwróciła co najmniej raz w późniejszym terminie. W przypadku niektórych użytkowników może zwrócić więcej niż jednym okresie. 
6. Karty insights Pokaż górny pięć inicjujący zdarzenia i górny pięć zwrócony umożliwić użytkownikom lepsze zrozumienie ich przechowywania raportu. 

![Przesuwania myszy przechowywania](./media/app-insights-usage-retention/hover.png)

Użytkownicy mogą umieść kursor nad komórek narzędzie przechowywania dostępu etykietki narzędzia i przycisk analytics wyjaśniający, co oznacza komórki do. Przycisk Analytics powoduje przejście do narzędzia do analizy za pomocą wstępnie wypełnionych kwerendy do generowania użytkowników z komórki. 

## <a name="use-business-events-to-track-retention"></a>Umożliwia śledzenie przechowywania zdarzeń biznesowych

Aby uzyskać najbardziej przydatne analizy przechowywania, pomiarów zdarzenia, które reprezentują znaczących działalności. 

Na przykład wielu użytkowników może zostać otwarta strona w aplikacji bez gry, który jest wyświetlany. Śledzenia po prostu wyświetleń strony w związku z tym Podaj oszacowanie niedokładne ile osób powrócić do gry po korzystających z wcześniej. Aby uzyskać jasny obraz przekazujących odtwarzaczy, aplikacji należy wysłać zdarzenie niestandardowe, gdy użytkownik faktycznie odgrywa.  

Jest dobrą praktyką jest kodem niestandardowych zdarzeń, które reprezentują akcji klucza biznesowych i ich używać do przechowywania analizy. Aby przechwycić wyniku gier, należy napisać wiersza kodu w celu wysyłania niestandardowych zdarzeń do usługi Application Insights. Podczas pisania kodu strony sieci web lub w środowisku Node.JS, wygląda następująco:

```JavaScript
    appinsights.trackEvent("won game");
```

Lub w kodzie serwera ASP.NET:

```csharp
   telemetry.TrackEvent("won game");
```

[Dowiedz się więcej na temat zapisywania zdarzeń niestandardowych](app-insights-api-custom-events-metrics.md#trackevent).


## <a name="next-steps"></a>Kolejne kroki
- Aby umożliwić korzystanie z użycia, Rozpocznij wysyłanie [zdarzeń niestandardowych](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) lub [wyświetlenia strony](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Jeżeli już zdarzeń niestandardowych lub wyświetleń strony, Poznaj narzędzia użycia, aby dowiedzieć się, jak używać usługi przez użytkowników.
    - [Użytkownicy, sesje, zdarzenia](app-insights-usage-segmentation.md)
    - [Lejki](usage-funnels.md)
    - [User Flows (Przepływy użytkowników)](app-insights-usage-flows.md)
    - [Skoroszyty](app-insights-usage-workbooks.md)
    - [Dodaj kontekstu użytkownika](app-insights-usage-send-user-context.md)



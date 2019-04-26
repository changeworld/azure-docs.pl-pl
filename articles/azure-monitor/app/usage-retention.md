---
title: Analiza zachowania użytkowników dla aplikacji sieci web za pomocą usługi Azure Application Insights | Dokumentacja firmy Microsoft
description: Ilu użytkowników wraca do swojej aplikacji?
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/03/2017
ms.pm_owner: daviste;NumberByColors
ms.reviewer: mbullwin
ms.author: daviste
ms.openlocfilehash: bda79520dd86cc14161f6f22cd24feb2e35849ab
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60372638"
---
# <a name="user-retention-analysis-for-web-applications-with-application-insights"></a>Analiza zachowania użytkownika dla aplikacji sieci web za pomocą usługi Application Insights

Funkcja przechowywania w [usługi Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) pomaga analizować, ilu użytkowników wraca do swojej aplikacji i jak często wykonywać określone zadania lub osiąganiu celów. Na przykład po uruchomieniu witryny gier, możesz porównać liczby użytkowników, którzy wróć do witryny po utracie grę z liczbą którzy powrotu po wygraną. Ta wiedza może pomóc ulepszyć środowisko użytkownika i jej strategią biznesową.

## <a name="get-started"></a>Rozpoczęcie pracy

Jeśli jeszcze nie widzisz danych w portalu usługi Application Insights za pomocą narzędzia przechowywanie [Dowiedz się, jak rozpocząć pracę z narzędziami użycia](usage-overview.md).

## <a name="the-retention-tool"></a>Narzędzie utrzymywania

![Narzędzie utrzymywania](./media/usage-retention/retention.png)

1. Pasek narzędzi umożliwia użytkownikom tworzenie nowych raportów przechowywania, Otwórz istniejące raporty przechowywania, Zapisz bieżący raport do przechowywania lub Zapisz jako, Cofnij zmiany wprowadzone do zapisanymi raportami, odświeżanie danych w raporcie, udostępnianie raportu pocztą e-mail lub linku bezpośredniego i dostęp do dokumentacji Strona. 
2. Domyślnie przechowywania zawiera wszystkich użytkowników, którzy spowodowali niczego następnie wrócił i czy jakichkolwiek innych czynności w okresie. Możesz wybrać inną kombinację zdarzeń, aby zawęzić zakres działań określonego użytkownika.
3. Dodaj co najmniej jeden filtr właściwości. Na przykład można skoncentrować się na użytkowników w danym kraju lub regionie. Kliknij przycisk **aktualizacji** znacznika wyboru. 
4. Wykres całkowitego przechowywania przedstawia podsumowanie utrzymania użytkowników w wybranym okresie. 
5. Siatka zawiera liczbę użytkowników przechowywane zgodnie z konstruktora zapytań w #2. Każdy wiersz reprezentuje kohorta użytkowników, który wykonał dowolnego zdarzenia w przedstawionym okresie. Każda komórka w wierszu pokazuje, ile tego kohorty zwracany przynajmniej raz w późniejszym terminie. Niektórzy użytkownicy mogą zwracać w więcej niż jeden okres. 
6. Kart ze szczegółowymi Pokaż najlepsze wydarzenia inicjujący pięć i najważniejsze pięć zdarzenia zwróconego umożliwić użytkownikom lepsze zrozumienie ich przechowywania raportów. 

![Umieść kursor myszy przechowywania](./media/usage-retention/hover.png)

Użytkownicy mogą umieść kursor nad komórek w narzędziu przechowywania dostępu wyjaśniający, co oznacza komórki porady przycisk i narzędzia analizy do. Przycisk Analiza przejście do narzędzia analizy za pomocą wstępnie wypełnionych zapytania do generowania użytkowników z komórki. 

## <a name="use-business-events-to-track-retention"></a>Umożliwia śledzenie przechowywania zdarzeń biznesowych

Aby uzyskać najbardziej przydatne analizy przechowywania, mierzenie zdarzenia, które reprezentują działalności znaczące. 

Na przykład wielu użytkowników może otworzyć stronę w aplikacji bez gry, który jest wyświetlany. Śledzenia po prostu wyświetleń stron w związku z tym zapewniają oszacowanie niedokładne ile osób powrócić do Zagraj w grę po korzystających z wcześniej. Aby uzyskać jasny obraz przekazujących dane graczy, aplikacja powinna Wyślij zdarzenie niestandardowe po użytkownik faktycznie odgrywa.  

Jest dobrą praktyką jest Zakoduj zdarzenia niestandardowe, które reprezentują akcji klucza biznesowych i ich używać do przechowywania analizy. Aby przechwycić wynik gier, należy napisać linię kodu w celu wysyłania zdarzeń niestandardowych do usługi Application Insights. Jeśli można go zapisać w kodzie strony sieci web lub w środowisku Node.JS, wygląda następująco:

```JavaScript
    appinsights.trackEvent("won game");
```

Lub w kodzie serwera ASP.NET:

```csharp
   telemetry.TrackEvent("won game");
```

[Dowiedz się więcej na temat pisania niestandardowych zdarzeń](../../azure-monitor/app/api-custom-events-metrics.md#trackevent).


## <a name="next-steps"></a>Kolejne kroki
- Aby umożliwić użycie środowiska, Rozpocznij wysyłanie [zdarzenia niestandardowe](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) lub [wyświetlenia strony](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Jeśli już wysyłać niestandardowe zdarzenia lub wyświetlenia stron, zapoznaj się z narzędzia obciążenia, aby dowiedzieć się, jak używać usługi przez użytkowników.
    - [Użytkownicy, sesje, zdarzenia](usage-segmentation.md)
    - [Lejki](usage-funnels.md)
    - [User Flows (Przepływy użytkowników)](usage-flows.md)
    - [Skoroszyty](../../azure-monitor/app/usage-workbooks.md)
    - [Dodawanie kontekstu użytkownika](usage-send-user-context.md)



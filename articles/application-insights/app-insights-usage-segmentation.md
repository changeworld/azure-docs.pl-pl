---
title: Analiza użytkowników, sesji i zdarzeń w usłudze Azure Application Insights | Dokumentacja firmy Microsoft
description: Analiza demograficznych użytkowników aplikacji sieci web.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 01/24/2018
ms.author: mbullwin; daviste
ms.openlocfilehash: 3ac738d6b3c0f1f3579a9b644a03a01f1509173a
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="users-sessions-and-events-analysis-in-application-insights"></a>Analiza użytkownikami, sesjami i zdarzeń w usłudze Application Insights

Dowiedz się, wykorzystywane przez użytkowników aplikacji sieci web, ich jest najbardziej zainteresowani, gdzie znajdują się użytkownicy i jakie przeglądarek i systemów operacyjnych używają strony. Analizowanie telemetrii działalności biznesowej i użycia za pomocą [Azure Application Insights](app-insights-overview.md).

![Zrzut ekranu przedstawiający Application Insights użytkowników](./media/app-insights-usage-segmentation/0001-users.png)

## <a name="get-started"></a>Rozpoczęcie pracy

Jeśli nie widzisz jeszcze dane użytkowników, sesji lub bloków zdarzenia w portalu usługi Application Insights [Dowiedz się, jak rozpocząć pracę z narzędziami użycia](app-insights-usage-overview.md).

## <a name="the-users-sessions-and-events-segmentation-tool"></a>Narzędzie segmentacji użytkowników, sesji i zdarzenia

Trzy bloków użycia umożliwia tego samego narzędzia selekcji i dane telemetryczne z aplikacji sieci web z trzema perspektyw. Filtrowanie i dzielenia danych, można ujawnić informacjami na temat względną użycie różnych stron i funkcji.

* **Narzędzie Użytkownicy**: używane wiele aplikacji i ich funkcje.  Użytkownicy są liczone przy użyciu anonimowego identyfikatorów przechowywane w plikach cookie przeglądarki. Jedna osoba, za pomocą różnych przeglądarkach lub maszyny będą liczone jako więcej niż jednego użytkownika.
* **Narzędzie sesje**: ile sesji aktywności użytkowników, zostały uwzględnione w pewnych stron i funkcji aplikacji. Sesja jest traktowane po pół godziny czas braku aktywności użytkownika lub po 24 godzinach pracy.
* **Narzędzie zdarzenia**: jak często używane pewnych stron i funkcji aplikacji. Widok strony jest liczony przeglądarką załadowanie strony z aplikacji, pod warunkiem, że [zinstrumentowane on](app-insights-javascript.md). 

    Zdarzenie niestandardowe reprezentuje jedno wystąpienie określonego zdarzenia w aplikacji, często interakcji użytkownika, np. Kliknij przycisk lub zakończenie niektórych zadań. Wstaw kod aplikacji w celu [generowanie zdarzeń niestandardowych](app-insights-api-custom-events-metrics.md#trackevent).

## <a name="querying-for-certain-users"></a>Wykonywanie zapytania dla niektórych użytkowników

Zapoznaj się z różnych grup użytkowników, zmieniając opcje zapytania w górnej części narzędzia użytkowników:

* Pokaż: Wybierz kohorty użytkowników do analizy.
* Kto używane: Wybierz niestandardowych zdarzeń i wyświetlenia strony.
* W trakcie: Wybierz zakres czasu.
* Przez: Wybierz sposób pojemnik danych przez pewien czas lub innej właściwości, takie jak przeglądarki lub Miasto.
* Dzielenie przez: Wybierz właściwość za pomocą którego podziału lub segmentu danych. 
* Dodaj filtry: Ograniczyć zapytanie do niektórych użytkowników, sesji lub zdarzenia na podstawie ich właściwości, takie jak przeglądarki lub Miasto. 
 
## <a name="saving-and-sharing-reports"></a>Zapisywanie i udostępnianie raportów 
Użytkownicy raportów, prywatne tylko dla Ciebie w sekcji Moje raporty lub udostępnionego można zapisać z inne osoby z dostępem do tego zasobu usługi Application Insights w sekcji udostępnionych raportów.

Aby udostępnić łącze do raportu użytkowników, sesji lub zdarzeń; Kliknij przycisk **udziału** na pasku narzędzi, a następnie skopiuj link.

Aby udostępnić kopii danych w raporcie użytkowników, sesji lub zdarzeń; Kliknij przycisk **udziału** na pasku narzędzi, następnie kliknij przycisk **ikona programu Word** można utworzyć dokumentu programu Word z danymi. Lub kliknij przycisk **ikona programu Word** powyżej wykresu głównego.

## <a name="meet-your-users"></a>Użytkownika spełnia

**Użytkownika spełnia** sekcja zawiera informacje o około pięciu użytkowników próbki są dopasowane wg bieżącego zapytania. Biorąc pod uwagę i eksploracji zachowania jednostek, oprócz agregacje, zapewniają wgląd w sposób osoby faktycznie używają Twojej aplikacji.

## <a name="next-steps"></a>Kolejne kroki

- Aby umożliwić korzystanie z użycia, Rozpocznij wysyłanie [zdarzeń niestandardowych](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) lub [wyświetlenia strony](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Jeżeli już zdarzeń niestandardowych lub wyświetleń strony, Poznaj narzędzia użycia, aby dowiedzieć się, jak używać usługi przez użytkowników.
    - [Lejki](usage-funnels.md)
    - [Przechowywanie](app-insights-usage-retention.md)
    - [User Flows (Przepływy użytkowników)](app-insights-usage-flows.md)
    - [Skoroszyty](app-insights-usage-workbooks.md)
    - [Dodaj kontekstu użytkownika](app-insights-usage-send-user-context.md)
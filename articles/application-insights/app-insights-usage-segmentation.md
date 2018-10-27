---
title: Analiza użytkowników, sesji i zdarzeń w usłudze Azure Application Insights | Dokumentacja firmy Microsoft
description: Analiza demograficznych użytkowników aplikacji sieci web.
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: conceptual
ms.date: 01/24/2018
ms.reviewer: mbullwin
ms.pm_owner: daviste;NumberByColors
ms.author: daviste
ms.openlocfilehash: 6fd8adab93f5741afe6d3eab0c50ca50a327fbff
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2018
ms.locfileid: "50140336"
---
# <a name="users-sessions-and-events-analysis-in-application-insights"></a>Użytkownicy, sesje i zdarzenia analizy w usłudze Application Insights

Dowiedz się, ułatwiają użytkownikom korzystanie z aplikacji sieci web, które strony są najbardziej interesują, gdzie znajdują się użytkownicy, a jakie przeglądarek i systemów operacyjnych używają. Analizy biznesowe i dane telemetrii użycia za pomocą [usługi Azure Application Insights](app-insights-overview.md).

![Zrzut ekranu przedstawiający Application Insights użytkowników](./media/app-insights-usage-segmentation/0001-users.png)

## <a name="get-started"></a>Rozpoczęcie pracy

Jeśli jeszcze nie widać danych użytkownicy, sesje lub zdarzenia bloków w portalu usługi Application Insights [Dowiedz się, jak rozpocząć pracę z narzędziami użycia](app-insights-usage-overview.md).

## <a name="the-users-sessions-and-events-segmentation-tool"></a>Narzędzie segmentacji użytkowników, sesje i zdarzenia

Trzy bloków użycia umożliwia tego samego narzędzia kątami danych telemetrycznych z aplikacji sieci web z trzema perspektyw. Filtrowania i dzielenia danych, można uzyskiwanie szczegółowych informacji dotyczących użycia względne różnych stron i funkcji.

* **Narzędzie Użytkownicy**: ile osób używana aplikacja i jej funkcji.  Użytkownicy są zliczane przy użyciu identyfikatorów anonimowe, przechowywane w plikach cookie przeglądarki. Jedna osoba, za pomocą różnych przeglądarek lub maszyny, będzie traktowane jako więcej niż jednego użytkownika.
* **Narzędzia sesje**: ile sesji aktywności użytkownika, mają ograniczenia niektórych stron i funkcji aplikacji. Sesja jest liczony po pół godziny braku aktywności użytkownika lub po 24 godziny ciągłego użycia.
* **Narzędzie zdarzeń**: jak często są używane niektórych stron i funkcji aplikacji. Widok strony jest liczona przeglądarki załadowanie strony z aplikacji, pod warunkiem, że [instrumentowany jego](app-insights-javascript.md). 

    Zdarzenie niestandardowe reprezentuje jedno wystąpienie określonego zdarzenia w swojej aplikacji, a często interakcji użytkownika, takie jak kliknięcie przycisku lub zakończenie niektóre zadania. Wstaw kod w aplikacji [generowanie zdarzeń niestandardowych](app-insights-api-custom-events-metrics.md#trackevent).

## <a name="querying-for-certain-users"></a>Wykonywanie zapytań w przypadku niektórych użytkowników

Zapoznaj się z różnych grup użytkowników przez dostosowanie opcji zapytania w górnej części narzędzie Użytkownicy:

* Pokaż: Wybierz kohorta użytkowników w celu analizowania.
* Kto korzystał: Wybierz zdarzenia niestandardowe i wyświetlenia stron.
* W trakcie: Wybierz zakres czasu.
* Według: Wybierz sposób zasobnika danych przez pewien czas lub innej właściwości, takie jak przeglądarki lub Miasto.
* Podziel według: Wybierz właściwości, za pomocą którego do podziału lub segmentu danych. 
* Dodaj filtry: Ogranicz zapytanie, aby niektórych użytkowników, sesje lub zdarzenia na podstawie ich właściwości, takie jak przeglądarki lub Miasto. 
 
## <a name="saving-and-sharing-reports"></a>Zapisywanie i udostępnianie raportów 
Możesz zapisać użytkowników raportów, prywatne tylko w sekcji Moje raporty lub udostępnione z inne osoby z uprawnieniami do tego zasobu usługi Application Insights, w sekcji raportów udostępnionych.

Aby udostępnić link do raportu użytkownicy, sesje lub zdarzenia; Kliknij przycisk **udziału** na pasku narzędzi, a następnie skopiuj link.

Udostępnianie kopii danych w raporcie użytkownicy, sesje lub zdarzenia; Kliknij przycisk **udziału** na pasku narzędzi, następnie kliknij przycisk **ikona programu Word** utworzyć dokument programu Word z danymi. Ewentualnie kliknij przycisk **ikona programu Word** powyżej wykresu głównego.

## <a name="meet-your-users"></a>Poznaj swoich użytkowników

**Spełniać użytkownicy** sekcja zawiera informacje o około pięciu przykładowych użytkowników dopasowane przez bieżącego zapytania. Biorąc pod uwagę i eksplorować zachowania użytkowników indywidualnych, oprócz wartości zagregowane, może zapewnić wgląd w sposób osoby faktycznie używają Twojej aplikacji.

## <a name="next-steps"></a>Kolejne kroki

- Aby umożliwić użycie środowiska, Rozpocznij wysyłanie [zdarzenia niestandardowe](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) lub [wyświetlenia strony](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Jeśli już wysyłać niestandardowe zdarzenia lub wyświetlenia stron, zapoznaj się z narzędzia obciążenia, aby dowiedzieć się, jak używać usługi przez użytkowników.
    - [Lejki](usage-funnels.md)
    - [Przechowywanie](app-insights-usage-retention.md)
    - [User Flows (Przepływy użytkowników)](app-insights-usage-flows.md)
    - [Skoroszyty](app-insights-usage-workbooks.md)
    - [Dodawanie kontekstu użytkownika](app-insights-usage-send-user-context.md)
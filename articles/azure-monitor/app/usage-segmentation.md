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
ms.topic: conceptual
ms.date: 01/24/2018
ms.reviewer: mbullwin
ms.pm_owner: daviste;NumberByColors
ms.author: daviste
ms.openlocfilehash: 7d378c2f72035c3584e1f5cd3c1f0fb9a5d5c2ed
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60372303"
---
# <a name="users-sessions-and-events-analysis-in-application-insights"></a>Użytkownicy, sesje i zdarzenia analizy w usłudze Application Insights

Dowiedz się, ułatwiają użytkownikom korzystanie z aplikacji sieci web, które strony są najbardziej interesują, gdzie znajdują się użytkownicy, a jakie przeglądarek i systemów operacyjnych używają. Analizy biznesowe i dane telemetrii użycia za pomocą [usługi Azure Application Insights](../../azure-monitor/app/app-insights-overview.md).

![Zrzut ekranu przedstawiający Application Insights użytkowników](./media/usage-segmentation/0001-users.png)

## <a name="get-started"></a>Rozpoczęcie pracy

Jeśli jeszcze nie widać danych użytkownicy, sesje lub zdarzenia bloków w portalu usługi Application Insights [Dowiedz się, jak rozpocząć pracę z narzędziami użycia](usage-overview.md).

## <a name="the-users-sessions-and-events-segmentation-tool"></a>Narzędzie segmentacji użytkowników, sesje i zdarzenia

Trzy bloków użycia umożliwia tego samego narzędzia kątami danych telemetrycznych z aplikacji sieci web z trzema perspektyw. Filtrowania i dzielenia danych, można uzyskiwanie szczegółowych informacji dotyczących użycia względne różnych stron i funkcji.

* **Narzędzie Użytkownicy**: Jak wiele osób używana aplikacja i jej funkcji.  Użytkownicy są zliczane przy użyciu identyfikatorów anonimowe, przechowywane w plikach cookie przeglądarki. Jedna osoba, za pomocą różnych przeglądarek lub maszyny, będzie traktowane jako więcej niż jednego użytkownika.
* **Narzędzia sesje**: Ile sesji aktywności użytkownika wprowadzono niektórych stron i funkcji aplikacji. Sesja jest liczony po pół godziny braku aktywności użytkownika lub po 24 godziny ciągłego użycia.
* **Narzędzie zdarzeń**: Jak często są używane niektórych stron i funkcji aplikacji. Widok strony jest liczona przeglądarki załadowanie strony z aplikacji, pod warunkiem, że [instrumentowany jego](../../azure-monitor/app/javascript.md). 

    Zdarzenie niestandardowe reprezentuje jedno wystąpienie określonego zdarzenia w swojej aplikacji, a często interakcji użytkownika, takie jak kliknięcie przycisku lub zakończenie niektóre zadania. Wstaw kod w aplikacji [generowanie zdarzeń niestandardowych](../../azure-monitor/app/api-custom-events-metrics.md#trackevent).

## <a name="querying-for-certain-users"></a>Wykonywanie zapytań w przypadku niektórych użytkowników

Zapoznaj się z różnych grup użytkowników przez dostosowanie opcji zapytania w górnej części narzędzie Użytkownicy:

* Pokaż: Wybierz kohorta użytkowników w celu analizowania.
* Kto korzystał: Wybierz niestandardowe zdarzenia i wyświetlenia stron.
* During: Wybierz zakres czasu.
* Według: Wybierz sposób zasobnika danych przez pewien czas lub innej właściwości, takie jak przeglądarki lub Miasto.
* Podziel według: Wybierz właściwości, za pomocą którego do podziału lub segmentu danych. 
* Dodaj filtry: Ogranicz zapytanie, aby niektórych użytkowników, sesje lub zdarzenia na podstawie ich właściwości, takie jak przeglądarki lub Miasto. 
 
## <a name="saving-and-sharing-reports"></a>Zapisywanie i udostępnianie raportów 
Możesz zapisać użytkowników raportów, prywatne tylko w sekcji Moje raporty lub udostępnione z inne osoby z uprawnieniami do tego zasobu usługi Application Insights, w sekcji raportów udostępnionych.

Aby udostępnić link do raportu użytkownicy, sesje lub zdarzenia; Kliknij przycisk **udziału** na pasku narzędzi, a następnie skopiuj link.

Udostępnianie kopii danych w raporcie użytkownicy, sesje lub zdarzenia; Kliknij przycisk **udziału** na pasku narzędzi, następnie kliknij przycisk **ikona programu Word** utworzyć dokument programu Word z danymi. Ewentualnie kliknij przycisk **ikona programu Word** powyżej wykresu głównego.

## <a name="meet-your-users"></a>Spełnia użytkowników

**Spełniać użytkownicy** sekcja zawiera informacje o około pięciu przykładowych użytkowników dopasowane przez bieżącego zapytania. Biorąc pod uwagę i eksplorować zachowania użytkowników indywidualnych, oprócz wartości zagregowane, może zapewnić wgląd w sposób osoby faktycznie używają Twojej aplikacji.

## <a name="next-steps"></a>Kolejne kroki

- Aby umożliwić użycie środowiska, Rozpocznij wysyłanie [zdarzenia niestandardowe](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) lub [wyświetlenia strony](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Jeśli już wysyłać niestandardowe zdarzenia lub wyświetlenia stron, zapoznaj się z narzędzia obciążenia, aby dowiedzieć się, jak używać usługi przez użytkowników.
    - [Lejki](usage-funnels.md)
    - [Przechowywanie](usage-retention.md)
    - [User Flows (Przepływy użytkowników)](usage-flows.md)
    - [Skoroszyty](../../azure-monitor/app/usage-workbooks.md)
    - [Dodawanie kontekstu użytkownika](usage-send-user-context.md)
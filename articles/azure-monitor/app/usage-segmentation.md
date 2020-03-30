---
title: Analiza użytkowników, sesji i zdarzeń w usłudze Azure Application Insights
description: Analiza demograficzna użytkowników aplikacji internetowej.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 01/24/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 56059304026b060e2215ce73e0e94e3200573a14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670988"
---
# <a name="users-sessions-and-events-analysis-in-application-insights"></a>Analiza użytkowników, sesji i zdarzeń w usłudze Application Insights

Dowiedz się, kiedy użytkownicy korzystają z Twojej aplikacji internetowej, jakie strony są najbardziej zainteresowani, gdzie znajdują się Twoi użytkownicy oraz z jakich przeglądarek i systemów operacyjnych korzystają. Analizowanie danych telemetrycznych biznesowych i użycia przy użyciu [usługi Azure Application Insights](../../azure-monitor/app/app-insights-overview.md).

![Zrzut ekranu przedstawiający użytkowników usługi Application Insights](./media/usage-segmentation/0001-users.png)

## <a name="get-started"></a>Wprowadzenie

Jeśli nie widzisz jeszcze danych w blokach użytkowników, sesji lub zdarzeń w portalu usługi Application Insights, [dowiedz się, jak rozpocząć pracę z narzędziami użycia.](usage-overview.md)

## <a name="the-users-sessions-and-events-segmentation-tool"></a>Narzędzie Segmentacja Użytkownicy, Sesje i Zdarzenia

Trzy ostrza użycia używają tego samego narzędzia do wycinania i krojenia danych telemetrycznych z aplikacji sieci web z trzech perspektyw. Filtrując i dzieląc dane, można odkryć szczegółowe informacje na temat względnego użycia różnych stron i funkcji.

* **Narzędzie Użytkownicy**: Ile osób korzystało z twojej aplikacji i jej funkcji.  Użytkownicy są liczeni przy użyciu anonimowych identyfikatorów przechowywanych w plikach cookie przeglądarki. Jedna osoba korzystająca z różnych przeglądarek lub maszyn będzie liczona jako więcej niż jeden użytkownik.
* **Narzędzie Sesje:** ile sesji aktywności użytkownika zawierało określone strony i funkcje aplikacji. Sesja jest liczona po pół godzinie braku aktywności użytkownika lub po 24 godzinach ciągłego użytkowania.
* **Narzędzie Zdarzenia:** jak często używane są określone strony i funkcje aplikacji. Widok strony jest liczony, gdy przeglądarka ładuje stronę z aplikacji, pod warunkiem, [że została obrządkowana.](../../azure-monitor/app/javascript.md) 

    Zdarzenie niestandardowe reprezentuje jedno wystąpienie czegoś, co dzieje się w aplikacji, często interakcji użytkownika, takich jak kliknięcie przycisku lub ukończenie niektórych zadań. Wstawianie kodu w aplikacji do [generowania zdarzeń niestandardowych](../../azure-monitor/app/api-custom-events-metrics.md#trackevent).

## <a name="querying-for-certain-users"></a>Wykonywanie zapytań dla niektórych użytkowników

Eksploruj różne grupy użytkowników, dostosowując opcje kwerendy w górnej części narzędzia Użytkownicy:

* Pokaż: wybierz kohortę użytkowników do przeanalizowania.
* Kto użył: wybierz niestandardowe zdarzenia i widoki strony.
* Podczas: Wybierz zakres czasu.
* Przez: Wybierz sposób zasobnika danych, albo przez okres czasu lub przez inną właściwość, takich jak przeglądarka lub miasto.
* Podziel według: Wybierz właściwość, według której mają być dzielone lub segmenty danych. 
* Dodaj filtry: ogranicz kwerendę do niektórych użytkowników, sesji lub zdarzeń na podstawie ich właściwości, takich jak przeglądarka lub miasto. 
 
## <a name="saving-and-sharing-reports"></a>Zapisywanie i udostępnianie raportów 
Raporty użytkowników można zapisywać w trybie prywatnym tylko w sekcji Moje raporty lub udostępniane wszystkim innym osobom, które mają dostęp do tego zasobu usługi Application Insights w sekcji Raporty udostępnione.

Aby udostępnić łącze do raportu Użytkownicy, Sesje lub Zdarzenia; Kliknij pozycję **Udostępnij** na pasku narzędzi, a następnie skopiuj łącze.

Aby udostępnić kopię danych w raporcie Użytkownicy, Sesje lub Zdarzenia; Kliknij **pozycję Udostępnij** na pasku narzędzi, a następnie kliknij **ikonę programu Word,** aby utworzyć dokument programu Word z danymi. Możesz też kliknąć **ikonę programu Word** nad wykresem głównym.

## <a name="meet-your-users"></a>Poznaj swoich użytkowników

Sekcja **Poznaj użytkowników zawiera** informacje o pięciu przykładowych użytkownikach dopasowanych do bieżącej kwerendy. Biorąc pod uwagę i eksplorując zachowania osób, oprócz agregacji, może dostarczyć szczegółowych informacji o tym, jak użytkownicy faktycznie korzystają z aplikacji.

## <a name="next-steps"></a>Następne kroki

- Aby włączyć środowisko użycia, zacznij wysyłać [niestandardowe zdarzenia](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) lub [widoki strony](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Jeśli wysyłasz już niestandardowe zdarzenia lub widoki stron, zapoznaj się z narzędziami Użycia, aby dowiedzieć się, jak użytkownicy korzystają z usługi.
    - [Lejki](usage-funnels.md)
    - [Przechowywanie](usage-retention.md)
    - [User Flows (Przepływy użytkowników)](usage-flows.md)
    - [Skoroszyty](../../azure-monitor/app/usage-workbooks.md)
    - [Dodawanie kontekstu użytkownika](usage-send-user-context.md)

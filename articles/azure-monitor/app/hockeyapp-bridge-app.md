---
title: Eksplorowanie danych platformy HockeyApp w usłudze Azure Application Insights | Dokumentacja firmy Microsoft
description: Analizowanie użycia i wydajności aplikacji platformy Azure z usługą Application Insights.
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: 97783cc6-67d6-465f-9926-cb9821f4176e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/30/2017
ms.author: mbullwin
ms.openlocfilehash: 79adfbfde25903bfe92c94507071c9d0fe303ef1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60898756"
---
# <a name="exploring-hockeyapp-data-in-application-insights"></a>Eksplorowanie danych platformy HockeyApp w usłudze Application Insights

> [!NOTE]
> Platforma HockeyApp nie jest już dostępna dla nowych aplikacji. Istniejące wdrożenia usługi HockeyApp będą nadal działać. Visual Studio App Center jest teraz usługę zalecane przez firmę Microsoft do monitorowania nowych aplikacji mobilnych. [Dowiedz się, jak skonfigurować swoje aplikacje za pomocą platformy App Center i Application Insights](../../azure-monitor/learn/mobile-center-quickstart.md).

[Platforma HockeyApp](https://azure.microsoft.com/services/hockeyapp/) to usługa umożliwiająca monitorowanie na żywo aplikacje komputerowe i mobilne. Z usługi HockeyApp można wysyłać niestandardowe i śledzenie danych telemetrycznych do monitorowania użycia i pomagają w procesie diagnozowania (oprócz pobierania danych o awariach). Ten strumień danych telemetrycznych można wykonywać zapytania za pomocą zaawansowanego [Analytics](../../azure-monitor/app/analytics.md) funkcji [usługi Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). Ponadto mogą [Eksportowanie niestandardowych i śledzić dane telemetryczne](export-telemetry.md). Aby włączyć te funkcje, należy skonfigurować most, który przekazuje HockeyApp niestandardowe dane do usługi Application Insights.

## <a name="the-hockeyapp-bridge-app"></a>Aplikacji HockeyApp Bridge
Z aplikacji HockeyApp Bridge to funkcja core, która umożliwia dostęp do platformy HockeyApp niestandardowych i dane telemetryczne śledzenia w usłudze Application Insights za pomocą funkcji analizy i eksportu ciągłego. Zdarzenia niestandardowe i śledzenia zebrane przez usługę HockeyApp po utworzeniu aplikacji HockeyApp Bridge będzie dostępny z tych funkcji. Zobaczmy, jak skonfigurować jedną z tych aplikacji mostka.

W usłudze HockeyApp, Otwórz okno Ustawienia konta, [tokeny interfejsu API](https://rink.hockeyapp.net/manage/auth_tokens). Utwórz nowy token lub ponownego użycia istniejącej. Wymagane minimalne uprawnienia, "tylko do odczytu". Wykonaj kopię interfejs API tokenu.

![Pobierz token z interfejsu API platformy HockeyApp](./media/hockeyapp-bridge-app/01.png)

Otwórz portal Microsoft Azure i [Utwórz zasób usługi Application Insights](../../azure-monitor/app/create-new-resource.md ). Ustaw typ aplikacji "Aplikacja HockeyApp bridge":

![Nowy zasób usługi Application Insights](./media/hockeyapp-bridge-app/02.png)

Nie musisz ustawić nazwę — spowoduje to ustawienie automatycznie na podstawie nazwy platformy HockeyApp.

Pola Mostek platformy HockeyApp są wyświetlane. 

![Wprowadź pola mostka](./media/hockeyapp-bridge-app/03.png)

Wprowadź token platformy HockeyApp, zanotowaną wcześniej. Ta akcja powoduje wypełnienie menu rozwijanym "Aplikacja usługi HockeyApp" ze wszystkimi aplikacjami platformy HockeyApp. Wybierz ten, którego chcesz użyć, a następnie ukończ pozostałe pola. 

Otwórz nowy zasób. 

Należy pamiętać, że danych trwa to trochę czasu do uruchomienia przepływu.

![Oczekiwanie na dane zasób usługi Application Insights](./media/hockeyapp-bridge-app/04.png)

Gotowe. Niestandardowe i śledzenia zbieranych danych w aplikacji Instrumentacji HockeyApp od tego momentu również jest teraz dostępny w funkcje analizy i eksportu ciągłego usługi Application Insights.

Krótko Omówmy teraz każde z tych funkcji, które są teraz dostępne dla użytkownika.

## <a name="analytics"></a>Analiza
Analytics to zaawansowane narzędzie do zapytań ad hoc dane, dzięki czemu możesz diagnozować i analizować dane telemetryczne, szybko odkryjesz głównych przyczyn i wzorce.

![Analiza](./media/hockeyapp-bridge-app/05.png)

* [Dowiedz się więcej na temat analizy](../../azure-monitor/log-query/get-started-portal.md)

## <a name="continuous-export"></a>Eksport ciągły
Eksport ciągły umożliwia eksportowanie danych do kontenera usługi Azure Blob Storage. Jest to bardzo przydatne, jeśli potrzebujesz do przechowywania danych przez czas dłuższy niż okres przechowywania obecnie oferowane przez usługę Application Insights. Możesz przechowywać dane w magazynie obiektów blob, Przetwarzaj je do bazy danych SQL lub preferowany rozwiązań magazynowania danych.

[Dowiedz się więcej o eksportu ciągłego](export-telemetry.md)

## <a name="next-steps"></a>Kolejne kroki
* [Zastosowanie analizy danych](../../azure-monitor/log-query/get-started-portal.md)


---
title: Eksplorowanie danych HockeyApp na platformie Azure Application Insights | Microsoft Docs
description: Analizuj użycie i wydajność aplikacji platformy Azure za pomocą Application Insights.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 03/30/2017
ms.openlocfilehash: b14cd38a1db6804a00883ded0b38511fa46c3a52
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819586"
---
# <a name="exploring-hockeyapp-data-in-application-insights"></a>Eksplorowanie danych HockeyApp w Application Insights

> [!NOTE]
> HockeyApp nie jest już dostępna dla nowych aplikacji. Istniejące wdrożenia HockeyApp nadal będą działały. Visual Studio App Center jest teraz zalecaną usługą firmy Microsoft do monitorowania nowych aplikacji mobilnych. [Dowiedz się, jak skonfigurować aplikacje za pomocą App Center i Application Insights](../../azure-monitor/learn/mobile-center-quickstart.md).

[HockeyApp](https://azure.microsoft.com/services/hockeyapp/) to usługa służąca do monitorowania klasycznego pulpitu i aplikacji mobilnych. Od HockeyApp można wysyłać niestandardowe i śledzenie telemetrii do monitorowania użycia i pomocy w diagnostyce (oprócz uzyskiwania danych awarii). W tym strumieniu danych telemetrycznych można wykonywać zapytania przy użyciu zaawansowanych funkcji [analizy](../../azure-monitor/app/analytics.md) [Application Insights platformy Azure](../../azure-monitor/app/app-insights-overview.md). Ponadto można [eksportować dane telemetryczne niestandardowe i śledzenia](export-telemetry.md). Aby włączyć te funkcje, należy skonfigurować mostek, który przekazuje dane niestandardowe HockeyApp do Application Insights.

## <a name="the-hockeyapp-bridge-app"></a>Aplikacja HockeyApp Bridge
Aplikacja HockeyApp Bridge to podstawowa funkcja, która umożliwia dostęp do danych telemetrycznych niestandardowych i śledzenia HockeyApp w Application Insights za pomocą funkcji analizy i eksportu ciągłego. Zdarzenia niestandardowe i śledzenia zebrane przez HockeyApp po utworzeniu aplikacji HockeyApp Bridge będą dostępne z tych funkcji. Zobaczmy, jak skonfigurować jedną z tych aplikacji mostka.

W HockeyApp, Otwórz ustawienia konta, [tokeny interfejsu API](https://rink.hockeyapp.net/manage/auth_tokens). Utwórz nowy token lub ponownie Użyj istniejącego. Wymagane są uprawnienia minimalne "tylko do odczytu". Zrób kopię tokenu interfejsu API.

![Pobierz token interfejsu API HockeyApp](./media/hockeyapp-bridge-app/01.png)

Otwórz Microsoft Azure Portal i [Utwórz zasób Application Insights](../../azure-monitor/app/create-new-resource.md ). Ustaw typ aplikacji na "aplikacja mostka HockeyApp":

![Nowy zasób Application Insights](./media/hockeyapp-bridge-app/02.png)

Nie musisz ustawiać nazwy — zostanie ona automatycznie ustawiona na podstawie nazwy HockeyApp.

Pojawią się pola mostka HockeyApp. 

![Wprowadzanie pól mostku](./media/hockeyapp-bridge-app/03.png)

Wprowadź zanotowany wcześniej token HockeyApp. Ta akcja powoduje wypełnienie menu rozwijanego "aplikacja HockeyApp" wszystkimi aplikacjami HockeyApp. Wybierz ten, którego chcesz użyć, i wypełnij pozostałe pola. 

Otwórz nowy zasób. 

Należy pamiętać, że uruchomienie przepływu danych trwa dłużej.

![Application Insights zasób oczekuje na dane](./media/hockeyapp-bridge-app/04.png)

Gotowe. Dane niestandardowe i śledzenia zebrane w aplikacji HockeyApp z tego punktu są teraz również dostępne w funkcji analizy i eksportu ciągłego Application Insights.

Zapoznaj się z każdą z tych funkcji, które są teraz dostępne dla Ciebie.

## <a name="analytics"></a>Analiza
Analiza to zaawansowane narzędzie do tworzenia zapytań dotyczących danych w trybie ad hoc, co pozwala diagnozować i analizować dane telemetryczne oraz szybko wykrywać główne przyczyny i wzorce.

![Analiza](./media/hockeyapp-bridge-app/05.png)

* [Dowiedz się więcej o analizie](../../azure-monitor/log-query/get-started-portal.md)

## <a name="continuous-export"></a>Ciągły eksport
Eksport ciągły umożliwia eksportowanie danych do kontenera Blob Storage platformy Azure. Jest to bardzo przydatne, jeśli chcesz przechowywać dane dłużej niż okres przechowywania aktualnie oferowany przez Application Insights. Dane można przechowywać w magazynie obiektów blob, przetwarzać je do SQL Database lub własnego rozwiązania do magazynowania danych.

[Dowiedz się więcej na temat eksportu ciągłego](export-telemetry.md)

## <a name="next-steps"></a>Następne kroki
* [Stosowanie analizy do danych](../../azure-monitor/log-query/get-started-portal.md)


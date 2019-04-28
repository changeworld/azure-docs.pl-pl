---
title: Integracja programu SCOM z usługą Application Insights | Dokumentacja firmy Microsoft
description: Jeśli jesteś użytkownikiem programu SCOM, monitorować wydajność i diagnozować problemy z usługą Application Insights. Wszechstronne pulpity nawigacyjne, inteligentne alertów, zaawansowane narzędzia diagnostyczne i zapytań analiz.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 606e9d03-c0e6-4a77-80e8-61b75efacde0
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: mbullwin
ms.openlocfilehash: a08a67a78362325e29b1002b44f390a94e78888a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60784881"
---
# <a name="application-performance-monitoring-using-application-insights-for-scom"></a>Monitorowanie wydajności aplikacji za pomocą usługi Application Insights dla oprogramowania SCOM
Jeśli używasz programu System Center Operations Manager (SCOM) do zarządzania serwerami z można monitorować wydajność i diagnozować problemy z wydajnością za pomocą [usługi Azure Application Insights](../../azure-monitor/app/asp-net.md). Application Insights monitoruje aplikację sieci web przychodzące żądania wychodzące, REST i wywołania SQL, wyjątki i ślady dzienników. Usługa udostępnia pulpity nawigacyjne wykresy metryk i inteligentne alerty, a także zaawansowane wyszukiwanie diagnostyczne i zapytań analitycznych za pośrednictwem tych danych telemetrycznych. 

Możesz włączyć monitorowanie usługi Application Insights przy użyciu pakietu administracyjnego programu SCOM.

> [!IMPORTANT]
> Ten pakiet administracyjny System Center Operations Manager jest teraz **przestarzałe**. Go nie obsługuje najnowsze zestawów SDK Application Insights i nie jest już zalecany.

## <a name="before-you-start"></a>Przed rozpoczęciem
Przyjęto założenie, że:

* Użytkownicy zaznajomieni z programu SCOM i użyć programu SCOM 2012 R2 lub 2016 do zarządzania usługi IIS serwery sieci web.
* Masz już zainstalowany na serwerach aplikacji sieci web, którą chcesz monitorować za pomocą usługi Application Insights.
* Wersja platformy aplikacji jest .NET 4.5 lub nowszej.
* Masz dostęp do subskrypcji w [Microsoft Azure](https://azure.com) i zalogować się do [witryny Azure portal](https://portal.azure.com). Twoja organizacja może mieć subskrypcji i do niego dodać konta Microsoft.

(Zespół deweloperów może zbudować [zestawu SDK usługi Application Insights](../../azure-monitor/app/asp-net.md) do aplikacji sieci web. Ten czas kompilacji Instrumentacji daje im większą elastyczność w zapisywanie niestandardowych danych telemetrycznych. Jednak nie ma znaczenia: możesz wykonać kroki opisane w tym miejscu, bez wbudowany zestaw SDK.)

## <a name="one-time-install-application-insights-management-pack"></a>(Jeden raz) Zainstaluj pakiet administracyjny usługi Application Insights
Na komputerze, na którym uruchamiasz programu Operations Manager:

1. Odinstaluj wszystkie starą wersję pakietu administracyjnego:
   1. W programie Operations Manager Otwórz administrację, pakietów administracyjnych. 
   2. Usuń starą wersję.
2. Pobierz i zainstaluj pakiet administracyjny z katalogu.
3. Ponowne uruchomienie programu Operations Manager.

## <a name="create-a-management-pack"></a>Tworzenie pakietu administracyjnego
1. W programie Operations Manager Otwórz **tworzenie**, **.NET... with Application Insights**, **Kreator dodawania monitorowania**i ponownie wybierz **.NET... z aplikacją Szczegółowe informacje**.
   
    ![](./media/scom/020.png)
2. Nazwa konfiguracji po swojej aplikacji. (Masz do instrumentacji aplikacji w czasie).
   
    ![](./media/scom/030.png)
3. Na tej samej stronie Kreatora tworzenia nowego pakietu administracyjnego lub wybierz pakiet, który został utworzony dla usługi Application Insights wcześniej.
   
     (W usłudze Application Insights [pakiet administracyjny](https://technet.microsoft.com/library/cc974491.aspx) jest szablonem, z którego można utworzyć wystąpienie. Można ponownie użyć tego samego wystąpienia później.)

    ![Na karcie Ogólne właściwości wpisz nazwę aplikacji. Kliknij pozycję Nowy, a następnie wpisz nazwę dla pakietu administracyjnego. Kliknij przycisk OK, a następnie kliknij przycisk Dalej.](./media/scom/040.png)

1. Wybierz jedną aplikację, którą chcesz monitorować. Funkcja wyszukiwania wyszukuje między aplikacje zainstalowane na Twoich serwerach.
   
    ![W celu karcie monitorowanie kliknij przycisk Dodaj, wpisz część nazwy aplikacji, kliknij przycisk Wyszukaj, wybierz aplikację, a następnie dodaj OK.](./media/scom/050.png)
   
    Pole opcjonalne zakres monitorowania może służyć do określenia podzestawu serwerów, jeśli nie chcesz monitorować aplikację na wszystkich serwerach.
2. Na następnej stronie kreatora najpierw należy podać swoje poświadczenia, aby zarejestrować się w usłudze Microsoft Azure.
   
    Na tej stronie możesz wybrać zasobu usługi Application Insights, w którym mają być analizowane i wyświetlane dane telemetryczne. 
   
   * Jeśli aplikacja została skonfigurowana dla usługi Application Insights podczas programowania, wybierz jego istniejącego zasobu.
   * W przeciwnym razie utwórz nowy zasób o nazwie dla aplikacji. Jeśli istnieją inne aplikacje, które są składnikami tego samego systemu, umieść je w tej samej grupie zasobów, aby ułatwić dostęp do danych telemetrycznych na łatwiejsze w zarządzaniu.
     
     Te ustawienia można zmienić później.
     
     ![Na karcie Ustawienia usługi Application Insights kliknij przycisk "Zaloguj", a następnie podaj swoje poświadczenia konta Microsoft na platformie Azure. Następnie wybierz subskrypcję, grupy zasobów i zasobów.](./media/scom/060.png)
3. Wykonaj kroki kreatora.
   
    ![Kliknięcie pozycji Utwórz](./media/scom/070.png)

Powtórz tę procedurę dla każdej aplikacji, którą chcesz monitorować.

Jeśli musisz zmienić ustawienia później, ponownie otwórz właściwości monitora z okna Tworzenie.

![W tworzenie, wybierz opcję monitorowanie wydajności aplikacji .NET za pomocą usługi Application Insights Wybierz monitor i kliknij polecenie Właściwości.](./media/scom/080.png)

## <a name="verify-monitoring"></a>Sprawdź, monitorowanie
Monitor, na każdym serwerze zainstalowano wyszukiwania dla aplikacji. Gdy znajdzie aplikacji, ponieważ konfiguruje na Monitor stanu usługi Application Insights do monitorowania aplikacji. Jeśli to konieczne, najpierw instaluje Monitora stanu na serwerze.

Aby sprawdzić, które wystąpienia aplikacji ma znaleźć:

![W obszarze monitorowanie, otwórz usługę Application Insights](./media/scom/100.png)

## <a name="view-telemetry-in-application-insights"></a>Wyświetlanie telemetrii w usłudze Application Insights
W [witryny Azure portal](https://portal.azure.com), przejdź do zasobu dla aplikacji. Możesz [zobaczyć wykresy pokazujące dane telemetryczne](../../azure-monitor/app/app-insights-dashboards.md) ze swojej aplikacji. (Jeśli go nie nie pojawiają na stronie głównej jeszcze, kliknij pozycję Live Stream metryk).

## <a name="next-steps"></a>Kolejne kroki
* [Ustawianie pulpitu nawigacyjnego](../../azure-monitor/app/app-insights-dashboards.md) ze sobą najważniejszych wykresów monitorowania to i inne aplikacje.
* [Dowiedz się więcej o metrykach](../../azure-monitor/app/metrics-explorer.md)
* [Konfigurowanie alertów](../../azure-monitor/app/alerts.md)
* [Diagnozowanie problemów z wydajnością](../../azure-monitor/app/detect-triage-diagnose.md)
* [Zaawansowane zapytania usługi Analytics](../../azure-monitor/app/analytics.md)
* [Testy sieci web dostępności](../../azure-monitor/app/monitor-web-app-availability.md)


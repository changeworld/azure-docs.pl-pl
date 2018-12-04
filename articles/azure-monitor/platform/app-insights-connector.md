---
title: Wyświetlanie danych aplikacji usługi Azure Application Insights | Dokumentacja firmy Microsoft
description: Za pomocą rozwiązania łącznik usługi Application Insights do diagnozowania problemów z wydajnością i zrozumieć, jak użytkownicy korzystają z aplikacją, gdy są monitorowane przy użyciu usługi Application Insights.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 49280cad-3526-43e1-a365-c6a3bf66db52
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/29/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: c704639ce696672a87485a36d43a282d04a47a7e
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52845180"
---
# <a name="application-insights-connector-management-solution-preview"></a>Rozwiązanie do zarządzania Insights Connector aplikacji (wersja zapoznawcza)

![Application Insights symboli](./media/app-insights-connector/app-insights-connector-symbol.png)

>[!NOTE]
> Dzięki obsłudze [zapytania obejmujące wiele zasobów](../../log-analytics/log-analytics-cross-workspace-search.md), rozwiązanie do zarządzania łącznika usługi Application Insights nie jest już wymagany i staną się przestarzałe. Począwszy od lipca, nie można połączyć nowe zasoby usługi Application Insights do obszarów roboczych usługi Log Analytics. Istniejące linki i pulpity nawigacyjne będą nadal działać do listopada 2018 r. Aby uzyskać więcej informacji, zobacz [portalu pakietu OMS na platformę Azure](../../log-analytics/log-analytics-oms-portal-transition.md).

Rozwiązanie łącznik aplikacji usługi Insights pomaga diagnozować problemy z wydajnością i zrozumieć do czego służą użytkowników z aplikacją, gdy są monitorowane [usługi Application Insights](../../application-insights/app-insights-overview.md). Widoki te same dane telemetryczne aplikacji, który zobaczą deweloperzy w usłudze Application Insights są dostępne w usłudze Log Analytics. Jednak gdy możesz zintegrować swoje aplikacje usługi Application Insights z usługą Log Analytics, widoczność aplikacji zwiększa się przez umieszczenie danych i danych aplikacji w jednym miejscu. O tej samej widoków ułatwia współpracę z deweloperów aplikacji. Wspólne widoki mogą pomóc w skróceniu czasu wykrywanie i rozwiązywanie zarówno aplikacji, jak i problemy dotyczące platformy.

Korzystając z rozwiązania, możesz wykonywać następujące czynności:

- Wyświetl wszystkie aplikacje usługi Application Insights w jednym miejscu, nawet wtedy, gdy są one w różnych subskrypcjach platformy Azure
- Korelowanie danych infrastruktury za pomocą danych aplikacji
- Wizualizacja danych aplikacji przy użyciu perspektywy podczas wyszukiwania dziennika
- Przestawianie z danymi usługi Log Analytics do swojej aplikacji usługę Application Insights w witrynie Azure portal

## <a name="connected-sources"></a>Połączone źródła

W przeciwieństwie do większości innych rozwiązań usługi Log Analytics dane nie są zbierane dla łącznik usługi Application Insights przez agentów. Wszystkie dane używane przez rozwiązania pochodzi bezpośrednio na platformie Azure.

| Połączone źródło | Obsługiwane | Opis |
| --- | --- | --- |
| [Agenci dla systemu Windows](../../azure-monitor/platform/agent-windows.md) | Nie | Rozwiązanie nie zbiera informacji od agentów Windows. |
| [Agenci dla systemu Linux](../../log-analytics/log-analytics-quick-collect-linux-computer.md) | Nie | Rozwiązanie zbiera informacje z agentów dla systemu Linux. |
| [Grupy zarządzania SCOM](../../log-analytics/log-analytics-om-agents.md) | Nie | Rozwiązanie nie zbiera informacji od agentów w połączonej grupie zarządzania programu SCOM. |
| [Konto usługi Azure Storage](collect-azure-metrics-logs.md) | Nie | Działa to rozwiązanie nie zbierania informacji z usługi Azure storage. |

## <a name="prerequisites"></a>Wymagania wstępne

- Aby uzyskać dostęp do informacji łącznik usługi Application Insights, musi mieć subskrypcję platformy Azure
- Musi mieć co najmniej jeden skonfigurowany zasób usługi Application Insights.
- Musi być właścicielem lub współautorem zasób usługi Application Insights.

## <a name="configuration"></a>Konfigurowanie

1. Włącz rozwiązanie Azure Web Apps Analytics z [portalu Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ApplicationInsights?tab=Overview) lub przy użyciu procesu opisanego w [rozwiązań Dodaj usługi Log Analytics z galerii rozwiązań](../../azure-monitor/insights/solutions.md).
2. Przejdź do witryny [Azure Portal](https://portal.azure.com). Wybierz **wszystkich usług** , aby otworzyć usługę Application Insights. Następnie wyszukaj usługę Application Insights. 
3. W obszarze **subskrypcje**, wybierz subskrypcję, która zawiera zasoby usługi Application Insights i w obszarze **nazwa**, wybierz co najmniej jednej aplikacji.
4. Kliknij pozycję **Zapisz**.

W ciągu 30 minut dane będą dostępne, a Kafelek Application Insights jest aktualizowane przy użyciu danych, takich jak na poniższej ilustracji:

![Kafelek Application Insights](./media/app-insights-connector/app-insights-tile.png)

Inne punkty, których należy pamiętać:

- Aplikacje usługi Application Insights można połączyć tylko z jednym obszarem roboczym usługi Log Analytics.
- Możesz dodać tylko łącze [Basic lub Enterprise Application Insights zasobów](https://azure.microsoft.com/pricing/details/application-insights) do usługi Log Analytics. Jednak można użyć usługi Log Analytics w warstwie bezpłatna.

## <a name="management-packs"></a>Pakiety administracyjne

To rozwiązanie nie można zainstalować wszystkie pakiety administracyjne w podłączone grupy zarządzania.

## <a name="use-the-solution"></a>Korzystanie z odpowiedniego rozwiązania

W poniższych sekcjach opisano, jak można użyć bloków widoczne w pulpicie nawigacyjnym usługi Application Insights można wyświetlać i korzystać z danych pochodzących z tworzonych aplikacji.

### <a name="view-application-insights-connector-information"></a>Wyświetl informacje na łącznik usługi Application Insights

Kliknij przycisk **usługi Application Insights** Kafelek, aby otworzyć **usługi Application Insights** pulpitu nawigacyjnego, aby wyświetlić poniższe bloki.

![Pulpit nawigacyjny szczegółowych informacji w aplikacji](./media/app-insights-connector/app-insights-dash01.png)

![Pulpit nawigacyjny szczegółowych informacji w aplikacji](./media/app-insights-connector/app-insights-dash02.png)

Pulpit nawigacyjny zawiera bloki pokazano w tabeli. Każdy blok zawiera do 10 elementów spełniających kryteria tego bloku dla określonego zakresu i czasu. Można uruchomić wyszukiwanie w dzienniku, które zwróci wszystkie rekordy, po kliknięciu **holograficznych** w dolnej części bloku lub kliknięcie nagłówka bloku.


| **Kolumny** | **Opis** |
| --- | --- |
| Aplikacje — liczba aplikacji | Przedstawia liczbę aplikacji w ramach aplikacji zasobów. Zawiera także listę nazw aplikacji i dla każdej liczby rekordów w aplikacji. Kliknij liczbę, aby uruchomić wyszukiwanie w Dzienniku <code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName</code> <br><br>  Kliknij nazwę aplikacji, aby uruchomić wyszukiwanie w dzienniku dla aplikacji, która zawiera rekordy aplikacji na hoście, rekordy według typu telemetrii i wszystkie dane według typu (oparte na ostatni dzień). |
| Ilość danych — hosty wysyłające dane | Pokazuje liczbę hostów komputerów, które wysyłają dane. Zawiera również listę komputerów hostów i liczba rekordów dla każdego hosta. Kliknij liczbę, aby uruchomić wyszukiwanie w Dzienniku <code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by Host</code> <br><br> Kliknij nazwę komputera, aby uruchomić wyszukiwanie w dzienniku dla hosta, który pokazuje rekordy aplikacji na hoście, rekordy według typu telemetrii i wszystkie dane według typu (oparte na ostatni dzień). |
| Dostępność — wyniki testu internetowego | Przedstawia wykres pierścieniowy na potrzeby wyników testu sieci web, wskazując zakończone powodzeniem lub niepowodzeniem. Kliknij wykres, aby uruchomić wyszukiwanie w Dzienniku <code>ApplicationInsights &#124; where TelemetryType == "Availability" &#124; summarize AggregatedValue = sum(SampledCount) by AvailabilityResult</code> <br><br> Liczba przebiegów i niepowodzeń dla wszystkich testów w wynikach. Raport uwzględnia wszystkie aplikacje sieci Web z ruchem w ciągu ostatniej minuty. Kliknij nazwę aplikacji w taki sposób, aby wyświetlić przeszukiwania dzienników, przedstawiający szczegółowe informacje o testach sieci web nie powiodło się. |
| Żądania serwera — żądania na godzinę | Przedstawia wykres liniowy serwera na żądania na godzinę dla różnych aplikacji. Umieść kursor nad linii na wykresie, aby zobaczyć 3 najczęściej używane aplikacje odbierania żądań do punktu w czasie. Zawiera również listę aplikacji odbierać żądania i liczba żądań dla wybranego okresu. <br><br>Kliknij wykres, aby uruchomić wyszukiwanie w dzienniku dla <code>ApplicationInsights &#124; where TelemetryType == "Request" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> pokazujący wykres liniowy bardziej szczegółowe dla żądań serwera, na godzinę dla różnych aplikacji. <br><br> Kliknij aplikację na liście, aby uruchomić wyszukiwanie w dzienniku <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code> , pokazuje listę żądań, wykresy dla żądań w ciągu godziny i żądanie czasu trwania i listę żądań kodów odpowiedzi.   |
| Błędy — nieudane żądania na godzinę | Przedstawia wykres liniowy w aplikacji nieudane żądania na godzinę. Umieść kursor nad wykresie, aby zobaczyć 3 najczęściej używane aplikacje za pomocą żądań zakończonych niepowodzeniem dla punktu w czasie. Zawiera również listę aplikacji z liczbą żądań zakończonych niepowodzeniem dla każdego. Kliknij wykres, aby uruchomić wyszukiwanie w dzienniku dla <code>ApplicationInsights &#124; where TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> pokazujący wykres liniowy bardziej szczegółowe żądań aplikacji nie powiodło się. <br><br>Kliknij element na liście, aby uruchomić wyszukiwanie w dzienniku dla <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code> pokazuje żądania, wykresy zakończone niepowodzeniem żądania zakończone niepowodzeniem przez okres czasu i żądania i listę kodów odpowiedzi żądań zakończonych niepowodzeniem. |
| Wyjątki — liczba wyjątków na godzinę | Przedstawia wykres liniowy wyjątków na godzinę. Umieść kursor nad wykresie, aby zobaczyć 3 najczęściej używane aplikacje wyjątków dla punktu w czasie. Zawiera również listę aplikacji z liczbą wyjątków dla każdego. Kliknij wykres, aby uruchomić wyszukiwanie w dzienniku dla <code>ApplicationInsights &#124; where TelemetryType == "Exception" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> pokazujący wykres łącze bardziej szczegółowe wyjątki. <br><br>Kliknij element na liście, aby uruchomić wyszukiwanie w dzienniku dla <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Exception"</code> którym wyświetlana jest lista wyjątków, wykresy wyjątki względem czasu i nieudane żądania oraz listę typów wyjątków.  |

### <a name="view-the-application-insights-perspective-with-log-search"></a>Wyświetl perspektywy usługi Application Insights, wyszukiwanie w dziennikach

Po kliknięciu dowolnego elementu w pulpicie nawigacyjnym, zobaczysz perspektywy usługi Application Insights, wyświetlana w polu wyszukiwania. Perspektywa zapewnia rozszerzoną wizualizacji, na podstawie wybranego typu danych telemetrycznych. Tak, wizualizacja zmiany zawartości dla typów różnych danych telemetrycznych.

Po kliknięciu w bloku aplikacje w dowolnym miejscu zostanie wyświetlona domyślna **aplikacje** perspektywy.

![Perspektywa aplikacji szczegółowych informacji w aplikacji](./media/app-insights-connector/applications-blade-drill-search.png)

Perspektywa zawiera omówienie aplikacji, które wybrano.

**Dostępności** blok zawiera widoku różne perspektywy, w którym można zobaczyć wyniki testu sieci web i powiązanych żądań zakończonych niepowodzeniem.

![Perspektywa Insights dostępności aplikacji](./media/app-insights-connector/availability-blade-drill-search.png)

Po kliknięciu dowolnego miejsca w **żądań serwera** lub **błędy** bloków, zmiany składników perspektyw umożliwiają wizualizację, która dotyczy żądań.

![Application Insights błędy bloku](./media/app-insights-connector/server-requests-failures-drill-search.png)

Po kliknięciu dowolnego miejsca w **wyjątki** wyświetlony blok wizualizację, która została dopasowana pod kątem wyjątków.

![Application Insights wyjątki bloku](./media/app-insights-connector/exceptions-blade-drill-search.png)

Niezależnie od tego, czy wybrano opcję wystąpił jeden **łącznik usługi Application Insights** pulpitu nawigacyjnego, w ramach **wyszukiwania** strony, każde zapytanie zwraca dane usługi Application Insights zawiera aplikację Perspektywa szczegółowych informacji. Na przykład, jeśli przeglądasz dane usługi Application Insights **&#42;** zapytanie wyświetla również na karcie perspektywy, takich jak na poniższej ilustracji:

![Application Insights ](./media/app-insights-connector/app-insights-search.png)

Składniki perspektywy są aktualizowane w zależności od zapytania wyszukiwania. Oznacza to, czy można filtrować wyniki za pomocą dowolnego pola wyszukiwania, która daje możliwość wyświetlenia danych z:

- Wszystkie swoje aplikacje
- Pojedynczy wybranej aplikacji
- Grupy aplikacji

### <a name="pivot-to-an-app-in-the-azure-portal"></a>Przełącz się do aplikacji w witrynie Azure portal

Application Insights Connector bloki są przeznaczone do umożliwiają Przełącz się do wybranej aplikacji w usłudze Application Insights *kiedy używać witryny Azure portal*. To rozwiązanie służy jako platforma monitorowania wysokiego poziomu, która ułatwia rozwiązywanie problemów z aplikacją. Po wyświetleniu potencjalny problem w dowolnym z połączonych aplikacji można albo testowania odzyskiwania po awarii do niego w polu wyszukiwania usługi Log Analytics lub można przestawiać bezpośrednio do aplikacji usługi Application Insights.

Aby przestawić, kliknij przycisk z wielokropkiem (**...** ) pojawia się na końcu każdego wiersza i wybierz **Otwórz w usłudze Application Insights**.

>[!NOTE]
>**Otwórz w usłudze Application Insights** nie jest dostępna w witrynie Azure portal.

![Otwórz w usłudze Application Insights](./media/app-insights-connector/open-in-app-insights.png)

### <a name="sample-corrected-data"></a>Poprawione próbki danych

Usługa Application Insights zapewnia *[próbkowanie korekcji](../../application-insights/app-insights-sampling.md)* do zmniejszenia ruchu telemetrycznego. Po włączeniu pobierania próbek na aplikacji usługi Application Insights uzyskasz zmniejszenie liczby wpisów przechowywane zarówno w usłudze Application Insights, jak i w usłudze Log Analytics. Gdy wyjaśnienie pojęcia spójności danych jest zachowywana w **łącznik usługi Application Insights** strony i perspektyw, należy ręcznie rozwiązać próbki danych niestandardowych zapytań.

Oto przykład korekty próbkowania w zapytanie wyszukiwania w dzienniku:

```
ApplicationInsights | summarize AggregatedValue = sum(SampledCount) by TelemetryType
```

**Licznik próbkowania** pole znajduje się w wszystkie wpisy i pokazuje liczbę punktów danych, które reprezentuje wpis. W przypadku równoczesnego włączenia próbkowania dla aplikacji usługi Application Insights, **licznik próbkowania** jest większa niż 1. Aby zliczyć rzeczywista liczba wpisów, które generuje aplikacji, należy zsumować **licznik próbkowania** pola.

Próbkowania ma wpływ tylko łączna liczba wpisów, generowane przez aplikację. Nie trzeba poprawić próbkowania dla pól metryk, takich jak **RequestDuration** lub **AvailabilityDuration** ponieważ tych polach wskaźnika myszy wyświetlane średnia dla reprezentowana wpisów.

## <a name="input-data"></a>Dane wejściowe

Rozwiązanie odbiera dane telemetryczne następujących rodzajów danych z połączonych aplikacji usługi Application Insights:

- Dostępność
- Wyjątki
- Żądania
- Wyświetlenia stron — dla obszaru roboczego w celu odbierania wyświetleń stron, należy skonfigurować aplikacje do zbierania tych informacji. Aby uzyskać więcej informacji, zobacz [PageViews](../../application-insights/app-insights-api-custom-events-metrics.md#page-views).
- Zdarzenia niestandardowe — dla obszaru roboczego w celu odbierania zdarzeń niestandardowych, należy skonfigurować aplikacje do zbierania tych informacji. Aby uzyskać więcej informacji, zobacz [TrackEvent](../../application-insights/app-insights-api-custom-events-metrics.md#trackevent).

Dane są odebrane przez usługę Log Analytics z usługi Application Insights po jej udostępnieniu.

## <a name="output-data"></a>Dane wyjściowe

Rekord z *typu* z *ApplicationInsights* jest tworzony dla każdego typu danych wejściowych. ApplicationInsights rekordy mają właściwości wyświetlane w następujących sekcjach:

### <a name="generic-fields"></a>Ogólny pola

| Właściwość | Opis |
| --- | --- |
| Typ | ApplicationInsights |
| ClientIP |   |
| TimeGenerated | Czas rekordu |
| ApplicationId | Klucz instrumentacji aplikacji usługi Application Insights |
| ApplicationName | Nazwa usługi Application Insights aplikacji |
| RoleInstance | Identyfikator serwera hosta |
| Typ urządzenia | Urządzenie klienckie |
| ScreenResolution |   |
| Kontynent | Kontynent, skąd pochodzi żądanie |
| Kraj | Kraj, skąd pochodzi żądanie |
| Województwo | Prowincja, stanu lub ustawień regionalnych, skąd pochodzi żądanie |
| Miasto | Miasto lub miejscowość, skąd pochodzi żądanie |
| isSynthetic | Wskazuje, czy żądanie zostało utworzone przez użytkownika lub zautomatyzowanej metody. TRUE = wygenerowane przez użytkowników, lub FAŁSZ = zautomatyzowanej metody |
| SamplingRate | Procent telemetrii wygenerowanej przez zestaw SDK, które są wysyłane do portalu. Należeć do zakresu od 0,0 100,0. |
| SampledCount | 100/(SamplingRate). Na przykład, 4 =&gt; 25% |
| Właściwości | Prawda lub fałsz |
| OperationID | Elementy, które mają tę samą operację identyfikator są wyświetlane jako elementy powiązane w portalu. Zazwyczaj identyfikator żądania |
| ParentOperationID | Identyfikator operacji nadrzędnej |
| OperationName |   |
| Identyfikator sesji | Identyfikator GUID służący do unikatowej identyfikacji sesji, w którym utworzono żądanie |
| SourceSystem | ApplicationInsights |

### <a name="availability-specific-fields"></a>Pola charakterystyczne dla dostępności

| Właściwość | Opis |
| --- | --- |
| TelemetryType | Dostępność |
| AvailabilityTestName | Nazwa testu sieci web |
| AvailabilityRunLocation | Geograficzne źródła żądania http |
| AvailabilityResult | Wskazuje wynik wskazujący Powodzenie testu sieci web |
| AvailabilityMessage | Wiadomości dołączone do testu sieci web |
| AvailabilityCount | 100 /(Sampling Rate). Na przykład, 4 =&gt; 25% |
| DataSizeMetricValue | w wersji 1.0 lub 0.0 |
| DataSizeMetricCount | 100 /(Sampling Rate). Na przykład, 4 =&gt; 25% |
| AvailabilityDuration | Czas w milisekundach czas trwania testu sieci web |
| AvailabilityDurationCount | 100 /(Sampling Rate). Na przykład, 4 =&gt; 25% |
| AvailabilityValue |   |
| AvailabilityMetricCount |   |
| AvailabilityTestId | Unikatowy identyfikator GUID dla testu sieci web |
| AvailabilityTimestamp | Dokładny znacznik czasu testu dostępności |
| AvailabilityDurationMin | Próbkowane rekordów to pole zawiera czas trwania testu minimalna sieci web (w milisekundach) dla punktów danych reprezentowanego |
| AvailabilityDurationMax | Próbkowane rekordów to pole zawiera czas trwania testu sieci web maksymalna (w milisekundach) dla punktów danych reprezentowanego |
| AvailabilityDurationStdDev | Próbkowane rekordów to pole zawiera standardowe odchylenie od wszystkich sieci web czasów trwania testów (w milisekundach) dla punktów danych reprezentowanego |
| AvailabilityMin |   |
| AvailabilityMax |   |
| AvailabilityStdDev | &nbsp;  |

### <a name="exception-specific-fields"></a>Pola charakterystyczne dla wyjątków

| Typ | ApplicationInsights |
| --- | --- |
| TelemetryType | Wyjątek |
| Typ | Typ wyjątku |
| ExceptionMethod | Metoda, która tworzy wyjątek |
| ExceptionAssembly | Zestaw zawiera platformę i wersji, a także token klucza publicznego |
| ExceptionGroup | Typ wyjątku |
| ExceptionHandledAt | Wskazuje poziom obsługi wyjątku |
| ExceptionCount | 100 /(Sampling Rate). Na przykład, 4 =&gt; 25% |
| ExceptionMessage | Komunikat o wyjątku |
| ExceptionStack | Pełny stos wyjątku |
| ExceptionHasStack | Wartość true, jeśli wyjątek ma stosu |



### <a name="request-specific-fields"></a>Pola charakterystyczne dla żądania

| Właściwość | Opis |
| --- | --- |
| Typ | ApplicationInsights |
| TelemetryType | Żądanie |
| ResponseCode | Odpowiedzi HTTP wysłanej do klienta |
| RequestSuccess | Wskazuje powodzenie lub niepowodzenie. Wartość PRAWDA lub FAŁSZ. |
| Identyfikator żądania | Identyfikator, aby jednoznacznie zidentyfikować żądania |
| RequestName | GET/POST i podstawowy adres URL |
| RequestDuration | Czas w sekundach czas trwania żądania |
| Adres URL | Adres URL żądania, nie wliczając hosta |
| Host | Hosta serwera sieci Web |
| URLBase | Pełny adres URL żądania |
| ApplicationProtocol | Typ protokołu używanego przez aplikację |
| RequestCount | 100 /(Sampling Rate). Na przykład, 4 =&gt; 25% |
| RequestDurationCount | 100 /(Sampling Rate). Na przykład, 4 =&gt; 25% |
| RequestDurationMin | Próbkowane rekordów to pole zawiera minimalne żądania czas trwania (w milisekundach) dla punktów danych reprezentowanego. |
| RequestDurationMax | Próbkowane rekordów to pole zawiera maksymalny czas trwania żądania (w milisekundach) dla punktów danych reprezentowanego |
| RequestDurationStdDev | Próbkowane rekordów to pole zawiera standardowe odchylenie między wszystkie żądania czasów trwania (w milisekundach) dla punktów danych reprezentowanego |

## <a name="sample-log-searches"></a>Przykładowe wyszukiwania dzienników

To rozwiązanie nie ma zbiór przykładowe wyszukiwania dzienników wyświetlane na pulpicie nawigacyjnym. Jednak przykładowych zapytań funkcji przeszukiwania dzienników przy użyciu opisy są wyświetlane w [łącznik usługi Application Insights Wyświetl informacje](#view-application-insights-connector-information) sekcji.

## <a name="next-steps"></a>Kolejne kroki

- Użyj [wyszukiwanie w dzienniku](../../azure-monitor/log-query/log-query-overview.md) Aby wyświetlić szczegółowe informacje o aplikacjach Application Insights.

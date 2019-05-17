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
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: magoedte
ms.openlocfilehash: c7c0d2e3fb818f74a65502674188c523d23729e8
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2019
ms.locfileid: "65606747"
---
# <a name="application-insights-connector-management-solution-deprecated"></a>Rozwiązanie do zarządzania Insights Connector aplikacji (przestarzałe)

![Application Insights symboli](./media/app-insights-connector/app-insights-connector-symbol.png)

>[!NOTE]
> Dzięki obsłudze [zapytania obejmujące wiele zasobów](../../azure-monitor/log-query/cross-workspace-query.md), rozwiązanie do zarządzania łącznik usługi Application Insights nie jest już wymagane. Został przestarzałe i usunięte z portalu Azure Marketplace, wraz z portalu pakietu OMS, która została oficjalnie uznane za przestarzałe w 15 stycznia 2019 w komercyjnej chmury Azure. Zostanie on wycofany w dniu 30 marca 2019 r w chmurze dla administracji USA.
>
>Istniejące połączenia będą w dalszym ciągu działać aż do 30 czerwca 2019 r.  Za pomocą obsługi portalu pakietu OMS nie ma możliwości do konfigurowania i usunąć istniejące połączenia z portalu. Zobacz [usunięcie łącznika przy użyciu programu PowerShell](#removing-the-connector-with-powershell) poniżej dla skryptu na przy użyciu programu PowerShell, aby usunąć istniejące połączenia.
>
>Aby uzyskać wskazówki dotyczące zapytania usługi Application Insights możesz rejestrować dane dla wielu aplikacji, zobacz [ujednolicenie wiele zasobów usługi Azure Monitor Application Insights](../log-query/unify-app-resource-data.md). Aby uzyskać więcej informacji dotyczących obsługi portalu pakietu OMS, zobacz [portalu pakietu OMS na platformę Azure](../../azure-monitor/platform/oms-portal-transition.md).
>
> 

Rozwiązanie łącznik aplikacji usługi Insights pomaga diagnozować problemy z wydajnością i zrozumieć do czego służą użytkowników z aplikacją, gdy są monitorowane [usługi Application Insights](../../azure-monitor/app/app-insights-overview.md). Widoki te same dane telemetryczne aplikacji, który zobaczą deweloperzy w usłudze Application Insights są dostępne w usłudze Log Analytics. Jednak gdy możesz zintegrować swoje aplikacje usługi Application Insights z usługą Log Analytics, widoczność aplikacji zwiększa się przez umieszczenie danych i danych aplikacji w jednym miejscu. O tej samej widoków ułatwia współpracę z deweloperów aplikacji. Wspólne widoki mogą pomóc w skróceniu czasu wykrywanie i rozwiązywanie zarówno aplikacji, jak i problemy dotyczące platformy.

Korzystając z rozwiązania, możesz wykonywać następujące czynności:

- Wyświetl wszystkie aplikacje usługi Application Insights w jednym miejscu, nawet wtedy, gdy są one w różnych subskrypcjach platformy Azure
- Korelowanie danych infrastruktury za pomocą danych aplikacji
- Wizualizacja danych aplikacji przy użyciu perspektywy podczas wyszukiwania dziennika
- Przestawianie z danymi usługi Log Analytics do swojej aplikacji usługę Application Insights w witrynie Azure portal


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="connected-sources"></a>Połączone źródła

W przeciwieństwie do większości innych rozwiązań usługi Log Analytics dane nie są zbierane dla łącznik usługi Application Insights przez agentów. Wszystkie dane używane przez rozwiązania pochodzi bezpośrednio na platformie Azure.

| Połączone źródło | Obsługiwane | Opis |
| --- | --- | --- |
| [Agenci dla systemu Windows](../../azure-monitor/platform/agent-windows.md) | Nie | Rozwiązanie nie zbiera informacji od agentów Windows. |
| [Agenci dla systemu Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) | Nie | Rozwiązanie zbiera informacje z agentów dla systemu Linux. |
| [Grupy zarządzania SCOM](../../azure-monitor/platform/om-agents.md) | Nie | Rozwiązanie nie zbiera informacji od agentów w połączonej grupie zarządzania programu SCOM. |
| [Konto usługi Azure Storage](collect-azure-metrics-logs.md) | Nie | Działa to rozwiązanie nie zbierania informacji z usługi Azure storage. |

## <a name="prerequisites"></a>Wymagania wstępne

- Aby uzyskać dostęp do informacji łącznik usługi Application Insights, musi mieć subskrypcję platformy Azure
- Musi mieć co najmniej jeden skonfigurowany zasób usługi Application Insights.
- Musi być właścicielem lub współautorem zasób usługi Application Insights.

## <a name="configuration"></a>Konfigurowanie

1. Włącz rozwiązanie Azure Web Apps Analytics z [portalu Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AppInsights?tab=Overview) lub przy użyciu procesu opisanego w [rozwiązań Dodaj usługi Log Analytics z galerii rozwiązań](../../azure-monitor/insights/solutions.md).
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

![Application Insights](./media/app-insights-connector/app-insights-search.png)

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

Usługa Application Insights zapewnia *[próbkowanie korekcji](../../azure-monitor/app/sampling.md)* do zmniejszenia ruchu telemetrycznego. Po włączeniu pobierania próbek na aplikacji usługi Application Insights uzyskasz zmniejszenie liczby wpisów przechowywane zarówno w usłudze Application Insights, jak i w usłudze Log Analytics. Gdy wyjaśnienie pojęcia spójności danych jest zachowywana w **łącznik usługi Application Insights** strony i perspektyw, należy ręcznie rozwiązać próbki danych niestandardowych zapytań.

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
- Wyświetlenia stron — dla obszaru roboczego w celu odbierania wyświetleń stron, należy skonfigurować aplikacje do zbierania tych informacji. Aby uzyskać więcej informacji, zobacz [PageViews](../../azure-monitor/app/api-custom-events-metrics.md#page-views).
- Zdarzenia niestandardowe — dla obszaru roboczego w celu odbierania zdarzeń niestandardowych, należy skonfigurować aplikacje do zbierania tych informacji. Aby uzyskać więcej informacji, zobacz [TrackEvent](../../azure-monitor/app/api-custom-events-metrics.md#trackevent).

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
| Kraj | Kraj/region, skąd pochodzi żądanie |
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

## <a name="removing-the-connector-with-powershell"></a>Usunięcie łącznika przy użyciu programu PowerShell
Za pomocą obsługi portalu pakietu OMS nie ma możliwości do konfigurowania i usunąć istniejące połączenia z portalu. Możesz usunąć istniejące połączenia za pomocą następującego skryptu programu PowerShell. Musi być właściciela lub współautora obszaru roboczego i czytnika zasób usługi Application Insights, aby wykonać tę operację.

```powershell
$Subscription_app = "App Subscription Name"
$ResourceGroup_app = "App ResourceGroup"
$Application = "Application Name"
$Subscription_workspace = "Workspace Subscription Name"
$ResourceGroup_workspace = "Workspace ResourceGroup"
$Workspace = "Workspace Name"

Connect-AzAccount
Set-AzContext -SubscriptionId $Subscription_app
$AIApp = Get-AzApplicationInsights -ResourceGroupName $ResourceGroup_app -Name $Application 
Set-AzContext -SubscriptionId $Subscription_workspace
Remove-AzOperationalInsightsDataSource -WorkspaceName $Workspace -ResourceGroupName $ResourceGroup_workspace -Name $AIApp.Id
```

Możesz pobrać listę aplikacji za pomocą poniższy skrypt programu PowerShell, który wywołuje wywołania interfejsu API REST. 

```powershell
Connect-AzAccount
$Tenant = "TenantId"
$Subscription_workspace = "Workspace Subscription Name"
$ResourceGroup_workspace = "Workspace ResourceGroup"
$Workspace = "Workspace Name"
$AccessToken = "AAD Authentication Token" 

Set-AzContext -SubscriptionId $Subscription_workspace
$LAWorkspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName $ResourceGroup_workspace -Name $Workspace

$Headers = @{
    "Authorization" = "Bearer $($AccessToken)"
    "x-ms-client-tenant-id" = $Tenant
}

$Connections = Invoke-RestMethod -Method "GET" -Uri "https://management.azure.com$($LAWorkspace.ResourceId)/dataSources/?%24filter=kind%20eq%20'ApplicationInsights'&api-version=2015-11-01-preview" -Headers $Headers
$ConnectionsJson = $Connections | ConvertTo-Json
```
Ten skrypt wymaga tokenu uwierzytelniania elementu nośnego do uwierzytelniania względem usługi Azure Active Directory. Jednym ze sposobów, aby pobrać ten token jest przy użyciu artykułu w [witrynie dokumentacji interfejsu API REST](https://docs.microsoft.com/rest/api/loganalytics/datasources/createorupdate). Kliknij przycisk **wypróbuj** i zaloguj się do subskrypcji platformy Azure. Możesz skopiować token elementu nośnego z **żądania (wersja zapoznawcza)** jak pokazano na poniższej ilustracji.


![Token elementu nośnego](media/app-insights-connector/bearer-token.png)


Możesz również pobrać listę aplikacji, użyj zapytanie dziennika:

```Kusto
ApplicationInsights | summarize by ApplicationName
```

## <a name="next-steps"></a>Kolejne kroki

- Użyj [wyszukiwanie w dzienniku](../../azure-monitor/log-query/log-query-overview.md) Aby wyświetlić szczegółowe informacje o aplikacjach Application Insights.

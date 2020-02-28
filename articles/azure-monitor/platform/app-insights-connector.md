---
title: Wyświetlanie danych aplikacji usługi Azure Application Insights | Dokumentacja firmy Microsoft
description: Za pomocą rozwiązania łącznik usługi Application Insights do diagnozowania problemów z wydajnością i zrozumieć, jak użytkownicy korzystają z aplikacją, gdy są monitorowane przy użyciu usługi Application Insights.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/13/2019
ms.openlocfilehash: c143d8aa24d3479f4619ea2c220d4a0c593f9cb1
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77665158"
---
# <a name="application-insights-connector-management-solution-deprecated"></a>Rozwiązanie do zarządzania Application Insights Connector (przestarzałe)

![Application Insights symboli](./media/app-insights-connector/app-insights-connector-symbol.png)

>[!NOTE]
> W przypadku obsługi [zapytań między zasobami](../../azure-monitor/log-query/cross-workspace-query.md), rozwiązanie do zarządzania Application Insights Connector nie jest już wymagane. Jest on przestarzały i usuwany z portalu Azure Marketplace wraz z portalem pakietu OMS, który został oficjalnie uznany za przestarzały 15 stycznia 2019 dla chmury komercyjnej platformy Azure. Zostanie ona wycofana 30 marca 2019 dla chmury rządowej USA platformy Azure.
>
>Istniejące połączenia będą nadal działały do 30 czerwca 2019.  W przypadku wycofania portalu pakietu OMS nie istnieje sposób konfigurowania i usuwania istniejących połączeń z portalu. Zobacz [usuwanie łącznika za pomocą programu PowerShell](#removing-the-connector-with-powershell) poniżej, aby uzyskać skrypt dotyczący usuwania istniejących połączeń przy użyciu programu PowerShell.
>
>Aby uzyskać wskazówki dotyczące wykonywania zapytań dotyczących danych dziennika Application Insights dla wielu aplikacji, zobacz [ujednolicaniaing multiple Azure Monitor Application Insights Resources](../log-query/unify-app-resource-data.md). Aby uzyskać więcej informacji na temat zaniechania portalu pakietu OMS, zobacz Portal pakietu OMS [przeniesiony na platformę Azure](../../azure-monitor/platform/oms-portal-transition.md).
>
> 

Rozwiązanie Application Insights Connector pomaga zdiagnozować problemy z wydajnością i zrozumieć, co użytkownicy robią z aplikacją podczas jej monitorowania za pomocą [Application Insights](../../azure-monitor/app/app-insights-overview.md). Widoki te same dane telemetryczne aplikacji, który zobaczą deweloperzy w usłudze Application Insights są dostępne w usłudze Log Analytics. Jednak gdy możesz zintegrować swoje aplikacje usługi Application Insights z usługą Log Analytics, widoczność aplikacji zwiększa się przez umieszczenie danych i danych aplikacji w jednym miejscu. O tej samej widoków ułatwia współpracę z deweloperów aplikacji. Wspólne widoki mogą pomóc w skróceniu czasu wykrywanie i rozwiązywanie zarówno aplikacji, jak i problemy dotyczące platformy.

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
| [Grupa zarządzania programu SCOM](../../azure-monitor/platform/om-agents.md) | Nie | Rozwiązanie nie zbiera informacji od agentów w połączonej grupie zarządzania programu SCOM. |
| [Konto usługi Azure Storage](collect-azure-metrics-logs.md) | Nie | Działa to rozwiązanie nie zbierania informacji z usługi Azure storage. |

## <a name="prerequisites"></a>Wymagania wstępne

- Aby uzyskać dostęp do informacji łącznik usługi Application Insights, musi mieć subskrypcję platformy Azure
- Musi mieć co najmniej jeden skonfigurowany zasób usługi Application Insights.
- Musi być właścicielem lub współautorem zasób usługi Application Insights.

## <a name="configuration"></a>Konfiguracja

1. Włącz rozwiązanie Azure Web Apps Analytics z [witryny Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AppInsights?tab=Overview) lub przy użyciu procesu opisanego w temacie [Dodawanie rozwiązań log Analytics z Galeria rozwiązań](../../azure-monitor/insights/solutions.md).
2. Przejdź do witryny [Azure Portal](https://portal.azure.com). Wybierz pozycję **wszystkie usługi** , aby otworzyć Application Insights. Następnie wyszukaj usługę Application Insights. 
3. W obszarze **subskrypcje**wybierz subskrypcję zawierającą Application Insights zasoby, a następnie w obszarze **Nazwa**wybierz co najmniej jedną aplikację.
4. Kliknij przycisk **Save** (Zapisz).

W ciągu 30 minut dane będą dostępne, a Kafelek Application Insights jest aktualizowane przy użyciu danych, takich jak na poniższej ilustracji:

![Kafelek Application Insights](./media/app-insights-connector/app-insights-tile.png)

Inne punkty, których należy pamiętać:

- Aplikacje usługi Application Insights można połączyć tylko z jednym obszarem roboczym usługi Log Analytics.
- Do Log Analytics można połączyć tylko [zasoby podstawowe lub Application Insights przedsiębiorstwa](https://azure.microsoft.com/pricing/details/application-insights) . Jednak można użyć usługi Log Analytics w warstwie bezpłatna.

## <a name="management-packs"></a>Pakiety administracyjne

To rozwiązanie nie można zainstalować wszystkie pakiety administracyjne w podłączone grupy zarządzania.

## <a name="use-the-solution"></a>Korzystanie z odpowiedniego rozwiązania

W poniższych sekcjach opisano, jak można użyć bloków widoczne w pulpicie nawigacyjnym usługi Application Insights można wyświetlać i korzystać z danych pochodzących z tworzonych aplikacji.

### <a name="view-application-insights-connector-information"></a>Wyświetl informacje na łącznik usługi Application Insights

Kliknij kafelek **Application Insights** , aby otworzyć pulpit nawigacyjny **Application Insights** , aby zobaczyć następujące bloki.

![Pulpit nawigacyjny szczegółowych informacji w aplikacji](./media/app-insights-connector/app-insights-dash01.png)

![Pulpit nawigacyjny szczegółowych informacji w aplikacji](./media/app-insights-connector/app-insights-dash02.png)

Pulpit nawigacyjny zawiera bloki pokazano w tabeli. Każdy blok zawiera do 10 elementów spełniających kryteria tego bloku dla określonego zakresu i czasu. Można uruchomić wyszukiwanie w dzienniku, które zwraca wszystkie rekordy po kliknięciu przycisku **Pokaż wszystkie** w dolnej części bloku lub po kliknięciu nagłówka bloku.


| **Kolumna** | **Opis** |
| --- | --- |
| Aplikacje — liczba aplikacji | Przedstawia liczbę aplikacji w ramach aplikacji zasobów. Zawiera także listę nazw aplikacji i dla każdej liczby rekordów w aplikacji. Kliknij liczbę, aby uruchomić wyszukiwanie w dzienniku dla <code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName</code> <br><br>  Kliknij nazwę aplikacji, aby uruchomić wyszukiwanie w dzienniku dla aplikacji, która zawiera rekordy aplikacji na hoście, rekordy według typu telemetrii i wszystkie dane według typu (oparte na ostatni dzień). |
| Ilość danych — hosty wysyłające dane | Pokazuje liczbę hostów komputerów, które wysyłają dane. Zawiera również listę komputerów hostów i liczba rekordów dla każdego hosta. Kliknij liczbę, aby uruchomić wyszukiwanie w dzienniku dla <code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by Host</code> <br><br> Kliknij nazwę komputera, aby uruchomić wyszukiwanie w dzienniku dla hosta, który pokazuje rekordy aplikacji na hoście, rekordy według typu telemetrii i wszystkie dane według typu (oparte na ostatni dzień). |
| Dostępność — wyniki testu internetowego | Przedstawia wykres pierścieniowy na potrzeby wyników testu sieci web, wskazując zakończone powodzeniem lub niepowodzeniem. Kliknij wykres, aby uruchomić wyszukiwanie w dzienniku dla <code>ApplicationInsights &#124; where TelemetryType == "Availability" &#124; summarize AggregatedValue = sum(SampledCount) by AvailabilityResult</code> <br><br> Liczba przebiegów i niepowodzeń dla wszystkich testów w wynikach. Raport uwzględnia wszystkie aplikacje sieci Web z ruchem w ciągu ostatniej minuty. Kliknij nazwę aplikacji w taki sposób, aby wyświetlić przeszukiwania dzienników, przedstawiający szczegółowe informacje o testach sieci web nie powiodło się. |
| Żądania serwera — żądania na godzinę | Przedstawia wykres liniowy serwera na żądania na godzinę dla różnych aplikacji. Umieść kursor nad linii na wykresie, aby zobaczyć 3 najczęściej używane aplikacje odbierania żądań do punktu w czasie. Zawiera również listę aplikacji odbierać żądania i liczba żądań dla wybranego okresu. <br><br>Kliknij wykres, aby uruchomić wyszukiwanie w dzienniku dla <code>ApplicationInsights &#124; where TelemetryType == "Request" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code>, który pokazuje bardziej szczegółowy wykres liniowy żądań serwera na godzinę dla różnych aplikacji. <br><br> Kliknij aplikację na liście, aby uruchomić wyszukiwanie w dzienniku dla <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code>, które wyświetla listę żądań, wykresy żądań w czasie i czas trwania żądania oraz listę kodów odpowiedzi na żądania.   |
| Błędy — nieudane żądania na godzinę | Przedstawia wykres liniowy w aplikacji nieudane żądania na godzinę. Umieść kursor nad wykresie, aby zobaczyć 3 najczęściej używane aplikacje za pomocą żądań zakończonych niepowodzeniem dla punktu w czasie. Zawiera również listę aplikacji z liczbą żądań zakończonych niepowodzeniem dla każdego. Kliknij wykres, aby uruchomić wyszukiwanie w dzienniku dla <code>ApplicationInsights &#124; where TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code>, który pokazuje bardziej szczegółowy wykres liniowy zakończonych niepowodzeniem żądań aplikacji. <br><br>Kliknij element na liście, aby uruchomić wyszukiwanie w dzienniku dla <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code>, w którym są wyświetlane Nieudane żądania, wykresy dla żądań zakończonych niepowodzeniem w okresie i czasie trwania żądania oraz listę kodów odpowiedzi na żądanie zakończone niepowodzeniem. |
| Wyjątki — liczba wyjątków na godzinę | Przedstawia wykres liniowy wyjątków na godzinę. Umieść kursor nad wykresie, aby zobaczyć 3 najczęściej używane aplikacje wyjątków dla punktu w czasie. Zawiera również listę aplikacji z liczbą wyjątków dla każdego. Kliknij wykres, aby uruchomić wyszukiwanie w dzienniku dla <code>ApplicationInsights &#124; where TelemetryType == "Exception" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code>, w którym znajduje się bardziej szczegółowy wykres linków wyjątków. <br><br>Kliknij element na liście, aby uruchomić wyszukiwanie w dzienniku dla <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Exception"</code>, które wyświetla listę wyjątków, wykresy dla wyjątków w czasie i żądania zakończone niepowodzeniem oraz listę typów wyjątków.  |

### <a name="view-the-application-insights-perspective-with-log-search"></a>Wyświetl perspektywy usługi Application Insights, wyszukiwanie w dziennikach

Po kliknięciu dowolnego elementu w pulpicie nawigacyjnym, zobaczysz perspektywy usługi Application Insights, wyświetlana w polu wyszukiwania. Perspektywa zapewnia rozszerzoną wizualizacji, na podstawie wybranego typu danych telemetrycznych. Tak, wizualizacja zmiany zawartości dla typów różnych danych telemetrycznych.

Po kliknięciu dowolnego miejsca w bloku aplikacje zostanie wyświetlony domyślny perspektywa **aplikacji** .

![Perspektywa aplikacji szczegółowych informacji w aplikacji](./media/app-insights-connector/applications-blade-drill-search.png)

Perspektywa zawiera omówienie aplikacji, które wybrano.

Blok **dostępności** pokazuje inny widok perspektywy, w którym można zobaczyć wyniki testu sieci Web i powiązane żądania zakończone niepowodzeniem.

![Perspektywa Insights dostępności aplikacji](./media/app-insights-connector/availability-blade-drill-search.png)

Po kliknięciu dowolnego miejsca w blokach żądania lub **Błędy** **serwera** składniki perspektywy zmieniają się w celu udostępnienia wizualizacji powiązanej z żądaniami.

![Application Insights błędy bloku](./media/app-insights-connector/server-requests-failures-drill-search.png)

Po kliknięciu dowolnego miejsca w bloku **wyjątki** zostanie wyświetlona Wizualizacja dostosowana do wyjątków.

![Application Insights wyjątki bloku](./media/app-insights-connector/exceptions-blade-drill-search.png)

Bez względu na to, czy klikniesz coś jednego z pulpitów nawigacyjnych **Application Insights Connector** na stronie **wyszukiwania** , wszystkie zapytania zwracające Application Insights dane pokazują Application Insights perspektywę. Na przykład, Jeśli przeglądasz dane Application Insights, **&#42;** zapytanie pokazuje również kartę perspektywy, jak na poniższej ilustracji:

![Application Insights](./media/app-insights-connector/app-insights-search.png)

Składniki perspektywy są aktualizowane w zależności od zapytania wyszukiwania. Oznacza to, czy można filtrować wyniki za pomocą dowolnego pola wyszukiwania, która daje możliwość wyświetlenia danych z:

- Wszystkie swoje aplikacje
- Pojedynczy wybranej aplikacji
- Grupy aplikacji

### <a name="pivot-to-an-app-in-the-azure-portal"></a>Przełącz się do aplikacji w witrynie Azure portal

Bloki Application Insights Connector zaprojektowano w celu umożliwienia przestawiania na wybraną aplikację Application Insights *podczas korzystania z Azure Portal*. To rozwiązanie służy jako platforma monitorowania wysokiego poziomu, która ułatwia rozwiązywanie problemów z aplikacją. Po wyświetleniu potencjalny problem w dowolnym z połączonych aplikacji można albo testowania odzyskiwania po awarii do niego w polu wyszukiwania usługi Log Analytics lub można przestawiać bezpośrednio do aplikacji usługi Application Insights.

Aby przestawić, kliknij wielokropek ( **...** ), który pojawia się na końcu każdego wiersza, a następnie wybierz pozycję **Otwórz w Application Insights**.

>[!NOTE]
>**Aplet Otwórz w Application Insights** nie jest dostępny w Azure Portal.

![Otwórz w usłudze Application Insights](./media/app-insights-connector/open-in-app-insights.png)

### <a name="sample-corrected-data"></a>Poprawione próbki danych

Application Insights zapewnia *[korekcję próbkowania](../../azure-monitor/app/sampling.md)* , aby pomóc w zmniejszeniu ruchu telemetrii. Po włączeniu pobierania próbek na aplikacji usługi Application Insights uzyskasz zmniejszenie liczby wpisów przechowywane zarówno w usłudze Application Insights, jak i w usłudze Log Analytics. Chociaż spójność danych jest zachowywana na stronie **Application Insights Connector** i perspektywach, należy ręcznie skorygować dane próbkowania dla zapytań niestandardowych.

Oto przykład korekty próbkowania w zapytanie wyszukiwania w dzienniku:

```
ApplicationInsights | summarize AggregatedValue = sum(SampledCount) by TelemetryType
```

Pole **Liczba próbek** jest obecna we wszystkich wpisach i pokazuje liczbę punktów danych, które reprezentuje wpis. Jeśli włączysz próbkowanie dla aplikacji Application Insights, **Liczba próbek** jest większa niż 1. Aby policzyć rzeczywistą liczbę wpisów generowanych przez aplikację, należy zsumować pola **liczby próbkowanej** .

Próbkowania ma wpływ tylko łączna liczba wpisów, generowane przez aplikację. Nie ma potrzeby poprawiania próbkowania dla pól metryk, takich jak **RequestDuration** lub **AvailabilityDuration** , ponieważ te pola pokazują średnią dla wpisów reprezentowanych.

## <a name="input-data"></a>Dane wejściowe

Rozwiązanie odbiera dane telemetryczne następujących rodzajów danych z połączonych aplikacji usługi Application Insights:

- Dostępność
- Wyjątki
- Żądania
- Wyświetlenia stron — dla obszaru roboczego w celu odbierania wyświetleń stron, należy skonfigurować aplikacje do zbierania tych informacji. Aby uzyskać więcej informacji, zobacz [PageViews](../../azure-monitor/app/api-custom-events-metrics.md#page-views).
- Zdarzenia niestandardowe — dla obszaru roboczego w celu odbierania zdarzeń niestandardowych, należy skonfigurować aplikacje do zbierania tych informacji. Aby uzyskać więcej informacji, zobacz [poleceń trackEvent](../../azure-monitor/app/api-custom-events-metrics.md#trackevent).

Dane są odebrane przez usługę Log Analytics z usługi Application Insights po jej udostępnieniu.

## <a name="output-data"></a>Dane wyjściowe

Rekord z *typem* *ApplicationInsights* jest tworzony dla każdego typu danych wejściowych. ApplicationInsights rekordy mają właściwości wyświetlane w następujących sekcjach:

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
| Kraj | Kraj/region, w którym pochodziło żądanie |
| Województwo | Prowincja, stanu lub ustawień regionalnych, skąd pochodzi żądanie |
| Miasto | Miasto lub miejscowość, skąd pochodzi żądanie |
| isSynthetic | Wskazuje, czy żądanie zostało utworzone przez użytkownika lub zautomatyzowanej metody. True = Metoda zautomatyzowana lub FAŁSZ = wygenerowane przez użytkownika |
| SamplingRate | Procent telemetrii wygenerowanej przez zestaw SDK, które są wysyłane do portalu. Należeć do zakresu od 0,0 100,0. |
| SampledCount | 100/(SamplingRate). Na przykład 4 =&gt; 25% |
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
| AvailabilityCount | 100 /(Sampling Rate). Na przykład 4 =&gt; 25% |
| DataSizeMetricValue | w wersji 1.0 lub 0.0 |
| DataSizeMetricCount | 100 /(Sampling Rate). Na przykład 4 =&gt; 25% |
| AvailabilityDuration | Czas w milisekundach czas trwania testu sieci web |
| AvailabilityDurationCount | 100 /(Sampling Rate). Na przykład 4 =&gt; 25% |
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
| ExceptionCount | 100 /(Sampling Rate). Na przykład 4 =&gt; 25% |
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
| RequestID | Identyfikator, aby jednoznacznie zidentyfikować żądania |
| RequestName | GET/POST i podstawowy adres URL |
| RequestDuration | Czas w sekundach czas trwania żądania |
| Adres URL | Adres URL żądania, nie wliczając hosta |
| Host | Hosta serwera sieci Web |
| URLBase | Pełny adres URL żądania |
| ApplicationProtocol | Typ protokołu używanego przez aplikację |
| RequestCount | 100 /(Sampling Rate). Na przykład 4 =&gt; 25% |
| RequestDurationCount | 100 /(Sampling Rate). Na przykład 4 =&gt; 25% |
| RequestDurationMin | Próbkowane rekordów to pole zawiera minimalne żądania czas trwania (w milisekundach) dla punktów danych reprezentowanego. |
| RequestDurationMax | Próbkowane rekordów to pole zawiera maksymalny czas trwania żądania (w milisekundach) dla punktów danych reprezentowanego |
| RequestDurationStdDev | Próbkowane rekordów to pole zawiera standardowe odchylenie między wszystkie żądania czasów trwania (w milisekundach) dla punktów danych reprezentowanego |

## <a name="sample-log-searches"></a>Przykładowe wyszukiwania dzienników

To rozwiązanie nie ma zbiór przykładowe wyszukiwania dzienników wyświetlane na pulpicie nawigacyjnym. Jednak przykładowe zapytania wyszukiwania w dzienniku zawierające opisy są wyświetlane w sekcji [Wyświetl informacje Application Insights Connector](#view-application-insights-connector-information) .

## <a name="removing-the-connector-with-powershell"></a>Usuwanie łącznika przy użyciu programu PowerShell
W przypadku wycofania portalu pakietu OMS nie istnieje sposób konfigurowania i usuwania istniejących połączeń z portalu. Istniejące połączenia można usunąć za pomocą poniższego skryptu programu PowerShell. Aby wykonać tę operację, musisz być właścicielem lub współautorem obszaru roboczego i czytnika zasobów Application Insights.

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

Listę aplikacji można pobrać przy użyciu następującego skryptu programu PowerShell, który wywołuje wywołanie interfejsu API REST. 

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
Ten skrypt wymaga tokenu uwierzytelniania okaziciela do uwierzytelniania w odniesieniu do Azure Active Directory. Jednym ze sposobów pobrania tego tokenu jest użycie artykułu w [witrynie dokumentacji interfejsu API REST](https://docs.microsoft.com/rest/api/loganalytics/datasources/createorupdate). Kliknij przycisk Wypróbuj i zaloguj **się** do subskrypcji platformy Azure. Token okaziciela można skopiować z **wersji zapoznawczej żądania** , jak pokazano na poniższej ilustracji.


![Token okaziciela](media/app-insights-connector/bearer-token.png)


Możesz również pobrać listę aplikacji, korzystając z kwerendy dziennika:

```Kusto
ApplicationInsights | summarize by ApplicationName
```

## <a name="next-steps"></a>Następne kroki

- Użyj [wyszukiwania w dzienniku](../../azure-monitor/log-query/log-query-overview.md) , aby wyświetlić szczegółowe informacje dotyczące Application Insights aplikacji.

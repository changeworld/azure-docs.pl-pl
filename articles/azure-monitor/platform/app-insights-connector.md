---
title: Wyświetlanie danych aplikacji usługi Azure Application Insights | Dokumenty firmy Microsoft
description: Za pomocą rozwiązania łącznika usługi Application Insights można zdiagnozować problemy z wydajnością i zrozumieć, co użytkownicy robią z aplikacją, gdy są monitorowane za pomocą usługi Application Insights.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/13/2019
ms.openlocfilehash: c143d8aa24d3479f4619ea2c220d4a0c593f9cb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665158"
---
# <a name="application-insights-connector-management-solution-deprecated"></a>Rozwiązanie do zarządzania łącznikami usługi Application Insights (przestarzałe)

![Symbol usługi Application Insights](./media/app-insights-connector/app-insights-connector-symbol.png)

>[!NOTE]
> Dzięki obsłudze [zapytań między zasobami](../../azure-monitor/log-query/cross-workspace-query.md)rozwiązanie do zarządzania łącznikiem usługi Application Insights nie jest już wymagane. Został przestarzały i usunięty z portalu Azure Marketplace wraz z portalem OMS, który został oficjalnie przestarzały 15 stycznia 2019 r. dla chmury komercyjnej platformy Azure. Zostanie wycofany 30 marca 2019 r. dla chmury platformy Azure dla instytucji rządowych w Stanach Zjednoczonych.
>
>Istniejące połączenia będą działać do 30 czerwca 2019 r.  Po usunięciu portalu usługi OMS nie można skonfigurować i usunąć istniejących połączeń z portalu. Zobacz [Usuwanie łącznika z programem PowerShell](#removing-the-connector-with-powershell) poniżej skryptu na temat używania programu PowerShell do usuwania istniejących połączeń.
>
>Aby uzyskać wskazówki dotyczące wykonywania zapytań o dane dziennika usługi Application Insights dla wielu aplikacji, zobacz [Ujednolicenie wielu zasobów usługi Azure Monitor Application Insights.](../log-query/unify-app-resource-data.md) Aby uzyskać więcej informacji na temat uszczypania portalu OMS, zobacz [Portal OMS przenoszący się na platformę Azure](../../azure-monitor/platform/oms-portal-transition.md).
>
> 

Rozwiązanie łącznika usługi Applications Insights ułatwia diagnozowanie problemów z wydajnością i zrozumienie, co użytkownicy robią z aplikacją, gdy jest monitorowana za pomocą [usługi Application Insights.](../../azure-monitor/app/app-insights-overview.md) Widoki tej samej telemetrii aplikacji, które deweloperzy widzą w usłudze Application Insights, są dostępne w usłudze Log Analytics. Jednak po zintegrowaniu aplikacji usługi Application Insights z usługą Log Analytics widoczność aplikacji zwiększa się dzięki operacji i danych aplikacji w jednym miejscu. Posiadanie tych samych widoków ułatwia współpracę z deweloperami aplikacji. Typowe widoki mogą pomóc skrócić czas wykrywania i rozwiązywania problemów z aplikacją i platformą.

Korzystając z rozwiązania, można:

- Wyświetlanie wszystkich aplikacji usługi Application Insights w jednym miejscu, nawet jeśli są one w różnych subskrypcjach platformy Azure
- Skorelowanie danych infrastruktury z danymi aplikacji
- Wizualizuj dane aplikacji z perspektywami wyszukiwania w dzienniku
- Przestawiaj dane usługi Log Analytics do aplikacji usługi Application Insights w witrynie Azure portal


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="connected-sources"></a>Połączone źródła

W przeciwieństwie do większości innych rozwiązań usługi Log Analytics dane nie są zbierane dla łącznika usługi Application Insights przez agentów. Wszystkie dane używane przez rozwiązanie pochodzą bezpośrednio z platformy Azure.

| Połączone źródło | Obsługiwane | Opis |
| --- | --- | --- |
| [Agenci dla systemu Windows](../../azure-monitor/platform/agent-windows.md) | Nie | Rozwiązanie nie zbiera informacji od agentów systemu Windows. |
| [Agenci dla systemu Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) | Nie | Rozwiązanie nie zbiera informacji od agentów Systemu Linux. |
| [Grupa zarządzająca SCOM](../../azure-monitor/platform/om-agents.md) | Nie | Rozwiązanie nie zbiera informacji od agentów w połączonej grupie zarządzania SCOM. |
| [Konto magazynu platformy Azure](collect-azure-metrics-logs.md) | Nie | Rozwiązanie nie zbiera informacji z usługi Azure storage. |

## <a name="prerequisites"></a>Wymagania wstępne

- Aby uzyskać dostęp do informacji łącznika usługi Application Insights, musisz mieć subskrypcję platformy Azure
- Musi istnieć co najmniej jeden skonfigurowany zasób usługi Application Insights.
- Użytkownik musi być właścicielem lub współautorem zasobu usługi Application Insights.

## <a name="configuration"></a>Konfigurowanie

1. Włącz rozwiązanie usługi Azure Web Apps Analytics z [portalu Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AppInsights?tab=Overview) lub przy użyciu procesu opisanego w [dodatku rozwiązań analizy dzienników z Galerii rozwiązań.](../../azure-monitor/insights/solutions.md)
2. Przejdź do [witryny Azure portal](https://portal.azure.com). Wybierz **wszystkie usługi,** aby otworzyć aplikację Usługi Application Insights. Następnie wyszukaj aplikację Usługi Application Insights. 
3. W obszarze **Subskrypcje**wybierz subskrypcję zawierającą zasoby usługi Application Insights, a następnie w obszarze **Nazwa**wybierz jedną lub więcej aplikacji.
4. Kliknij przycisk **Zapisz**.

W ciągu około 30 minut dane staną się dostępne, a kafelek usługi Application Insights zostanie zaktualizowany o dane, takie jak poniższy obraz:

![Kafelek usługi Application Insights](./media/app-insights-connector/app-insights-tile.png)

Inne kwestie, o które należy pamiętać:

- Aplikacje usługi Application Insights można łączyć tylko z jednym obszarem roboczym usługi Log Analytics.
- Zasoby usługi [Basic lub Enterprise Application Insights](https://azure.microsoft.com/pricing/details/application-insights) można łączyć tylko z usługą Log Analytics. Można jednak użyć bezpłatnej warstwy usługi Log Analytics.

## <a name="management-packs"></a>Pakiety administracyjne

To rozwiązanie nie instaluje żadnych pakietów administracyjnych w połączonych grupach zarządzania.

## <a name="use-the-solution"></a>Użyj rozwiązania

W poniższych sekcjach opisano, jak można używać bloków wyświetlanych na pulpicie nawigacyjnym usługi Application Insights do wyświetlania danych z aplikacji i interakcji z nimi.

### <a name="view-application-insights-connector-information"></a>Wyświetlanie informacji o łączniku usługi Application Insights

Kliknij kafelek **usługi Application Insights,** aby otworzyć pulpit nawigacyjny **usługi Application Insights,** aby wyświetlić następujące ostrza.

![Pulpit nawigacyjny usługi Application Insights](./media/app-insights-connector/app-insights-dash01.png)

![Pulpit nawigacyjny usługi Application Insights](./media/app-insights-connector/app-insights-dash02.png)

Pulpit nawigacyjny zawiera ostrza pokazane w tabeli. Każdy blok zawiera do 10 elementów spełniających kryteria tego bloku dla określonego zakresu i czasu. Można uruchomić wyszukiwanie dziennika, który zwraca wszystkie rekordy po kliknięciu **przycisku Zobacz wszystko** w dolnej części bloku lub po kliknięciu nagłówka bloku.


| **Kolumna** | **Opis** |
| --- | --- |
| Wnioski - Liczba wniosków | Pokazuje liczbę aplikacji w zasobach aplikacji. Wyświetla również nazwy aplikacji i dla każdego liczby rekordów aplikacji. Kliknij numer, aby uruchomić wyszukiwanie w dzienniku<code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName</code> <br><br>  Kliknij nazwę aplikacji, aby uruchomić wyszukiwanie dziennika aplikacji, która pokazuje rekordy aplikacji na hoście, rekordy według typu telemetrii i wszystkie dane według typu (na podstawie ostatniego dnia). |
| Ilość danych — hosty wysyłające dane | Pokazuje liczbę hostów komputerów, które wysyłają dane. Wyświetla również listę hostów komputera i liczbę rekordów dla każdego hosta. Kliknij numer, aby uruchomić wyszukiwanie w dzienniku<code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by Host</code> <br><br> Kliknij nazwę komputera, aby uruchomić wyszukiwanie dziennika dla hosta, który pokazuje rekordy aplikacji na hoście, rekordy według typu telemetrii i wszystkie dane według typu (na podstawie ostatniego dnia). |
| Dostępność – wyniki testów internetowych | Pokazuje wykres pierścieniowy dla wyników testów internetowych, wskazujący przebieg lub niepowodzenie. Kliknij wykres, aby uruchomić wyszukiwanie w dzienniku<code>ApplicationInsights &#124; where TelemetryType == "Availability" &#124; summarize AggregatedValue = sum(SampledCount) by AvailabilityResult</code> <br><br> Wyniki pokazują liczbę przebiegów i błędów dla wszystkich testów. Pokazuje wszystkie aplikacje sieci Web z ruchem w ostatniej chwili. Kliknij nazwę aplikacji, aby wyświetlić wyszukiwanie w dzienniku z informacjami o nieudanych testach sieci Web. |
| Żądania serwera — żądania na godzinę | Pokazuje wykres liniowy żądań serwera na godzinę dla różnych aplikacji. Umieść wskaźnik myszy na wierszu na wykresie, aby wyświetlić 3 najlepsze aplikacje odbierające żądania o punkt w czasie. Pokazuje również listę aplikacji odbierających żądania oraz liczbę żądań dla wybranego okresu. <br><br>Kliknij wykres, aby uruchomić wyszukiwanie <code>ApplicationInsights &#124; where TelemetryType == "Request" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> dziennika, które pokazuje bardziej szczegółowy wykres liniowy żądań serwera na godzinę dla różnych aplikacji. <br><br> Kliknij aplikację na <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code> liście, aby uruchomić wyszukiwanie dziennika, które pokazuje listę żądań, wykresy dla żądań w czasie i czas trwania żądania oraz listę kodów odpowiedzi żądania.   |
| Błędy — żądania nie powiodły się na godzinę | Pokazuje wykres liniowy żądań aplikacji, które nie powiodły się na godzinę. Umieść wskaźnik myszy na wykresie, aby wyświetlić 3 najlepsze aplikacje z nieudanymi żądaniami punktu w czasie. Pokazuje również listę aplikacji z liczbą żądań nie powiodło się dla każdego. Kliknij wykres, aby uruchomić <code>ApplicationInsights &#124; where TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> wyszukiwanie dziennika, które pokazuje bardziej szczegółowy wykres liniowy nieudanych żądań aplikacji. <br><br>Kliknij element na liście, aby uruchomić <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code> wyszukiwanie dziennika, które pokazuje nieudane żądania, wykresy dla żądań nie powiodło się w czasie i czas trwania żądania oraz listę kodów odpowiedzi żądania nie powiodło się. |
| Wyjątki – wyjątki na godzinę | Pokazuje wykres liniowy wyjątków na godzinę. Umieść wskaźnik myszy na wykresie, aby wyświetlić 3 najlepsze aplikacje z wyjątkami dla punktu w czasie. Pokazuje również listę aplikacji z liczbą wyjątków dla każdego. Kliknij wykres, aby uruchomić <code>ApplicationInsights &#124; where TelemetryType == "Exception" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> wyszukiwanie dziennika, które zawiera bardziej szczegółowy wykres łączy wyjątków. <br><br>Kliknij element na liście, aby uruchomić <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Exception"</code> wyszukiwanie dziennika, który pokazuje listę wyjątków, wykresy wyjątków w czasie i żądań nie powiodło się i listę typów wyjątków.  |

### <a name="view-the-application-insights-perspective-with-log-search"></a>Wyświetlanie perspektywy usługi Application Insights za pomocą wyszukiwania dzienników

Po kliknięciu dowolnego elementu na pulpicie nawigacyjnym zostanie wyświetlona perspektywa usługi Application Insights wyświetlana w wyszukiwaniu. Perspektywa zapewnia rozszerzoną wizualizację na podstawie wybranego typu telemetrii. Tak więc zmiany zawartości wizualizacji dla różnych typów telemetrii.

Po kliknięciu dowolnego miejsca w aplikacji bloku, zobaczysz domyślną **perspektywę aplikacji.**

![Perspektywa aplikacji usługi Application Insights](./media/app-insights-connector/applications-blade-drill-search.png)

Perspektywa pokazuje przegląd wybranej aplikacji.

Blok **Dostępność** pokazuje inny widok perspektywy, w którym można zobaczyć wyniki testów sieci web i powiązane żądania nie powiodło się.

![Perspektywa dostępności usługi Application Insights](./media/app-insights-connector/availability-blade-drill-search.png)

Po kliknięciu dowolnego miejsca w **serwerze żądania** lub **błędy** bloków, składniki perspektywy zmienić, aby zapewnić wizualizację, która odnosi się do żądań.

![Blok Błędów usługi Application Insights](./media/app-insights-connector/server-requests-failures-drill-search.png)

Po kliknięciu dowolnego miejsca w **wyjątki** bloku, zostanie wyświetlena wizualizacja, która jest dostosowana do wyjątków.

![Blok Wyjątki usługi Application Insights](./media/app-insights-connector/exceptions-blade-drill-search.png)

Niezależnie od tego, czy klikniesz jeden pulpit nawigacyjny **łącznika usługi Application Insights,** na samej stronie **Wyszukiwanie** każda kwerenda zwracająca dane usługi Application Insights zawiera perspektywę usługi Application Insights. Na przykład w przypadku wyświetlania danych usługi Application Insights kwerenda **&#42;** pokazuje również kartę perspektywy, taką jak poniższa ilustracja:

![Application Insights](./media/app-insights-connector/app-insights-search.png)

Składniki perspektywy są aktualizowane w zależności od zapytania wyszukiwania. Oznacza to, że można filtrować wyniki przy użyciu dowolnego pola wyszukiwania, które daje możliwość, aby zobaczyć dane z:

- Wszystkie twoje aplikacje
- Pojedyncza wybrana aplikacja
- Grupa aplikacji

### <a name="pivot-to-an-app-in-the-azure-portal"></a>Przestawianie do aplikacji w witrynie Azure Portal

Bloki łącznika usługi Application Insights są zaprojektowane w taki sposób, aby umożliwić przesuń do wybranej aplikacji Usługi Application Insights *podczas korzystania z witryny Azure portal*. Rozwiązanie można używać jako platformy monitorowania wysokiego poziomu, która pomaga rozwiązywać problemy z aplikacją. Gdy pojawi się potencjalny problem w dowolnej z połączonych aplikacji, można przejść do szczegółów w wyszukiwarce usługi Log Analytics lub można przejść bezpośrednio do aplikacji usługi Application Insights.

Aby przestawiać, kliknij wielokropek (**...**), który pojawia się na końcu każdego wiersza, a następnie wybierz pozycję **Otwórz w aplikacji Insights**.

>[!NOTE]
>**Otwórz w usłudze Application Insights** nie jest dostępna w witrynie Azure portal.

![Otwórz w usłudze Application Insights](./media/app-insights-connector/open-in-app-insights.png)

### <a name="sample-corrected-data"></a>Dane poprawione próbką

Usługa Application Insights zapewnia *[korekcję próbkowania](../../azure-monitor/app/sampling.md)* w celu zmniejszenia ruchu telemetryczne. Po włączeniu próbkowania w aplikacji usługi Application Insights, można uzyskać zmniejszoną liczbę wpisów przechowywanych zarówno w usłudze Application Insights i w usłudze Log Analytics. Spójność danych jest zachowywana na stronie i perspektywach **łącznika usługi Application Insights,** należy ręcznie poprawić próbkowane dane dla zapytań niestandardowych.

Oto przykład korekty próbkowania w kwerendzie wyszukiwania dziennika:

```
ApplicationInsights | summarize AggregatedValue = sum(SampledCount) by TelemetryType
```

Pole **Liczba próbkowania** jest obecne we wszystkich zapisach i zawiera liczbę punktów danych, które reprezentuje zapis. Jeśli włączysz próbkowanie aplikacji Usługi Application Insights, **liczba próbkowania** jest większa niż 1. Aby policzyć rzeczywistą liczbę wpisów generowanych przez aplikację, zsumuj pola **Liczba próbkowania.**

Próbkowanie ma wpływ tylko na całkowitą liczbę wpisów, które generuje aplikacja. Nie trzeba korygować próbkowania dla pól metryk, takich jak **RequestDuration** lub **AvailabilityDuration,** ponieważ te pola pokazują średnią dla reprezentowanych wpisów.

## <a name="input-data"></a>Dane wejściowe

Rozwiązanie odbiera następujące typy danych telemetrycznych z połączonych aplikacji usługi Application Insights:

- Dostępność
- Wyjątki
- Żądania
- Widoki strony — aby obszar roboczy odbierał widoki stron, należy skonfigurować aplikacje do zbierania tych informacji. Aby uzyskać więcej informacji, zobacz [PageViews](../../azure-monitor/app/api-custom-events-metrics.md#page-views).
- Zdarzenia niestandardowe — aby obszar roboczy odbierał zdarzenia niestandardowe, należy skonfigurować aplikacje do zbierania tych informacji. Więcej informacji można znaleźć na [pomylić TrackEvent](../../azure-monitor/app/api-custom-events-metrics.md#trackevent).

Dane są odbierane przez usługi Log Analytics z usługi Application Insights, gdy staną się dostępne.

## <a name="output-data"></a>Dane wyjściowe

Rekord z *typem* *ApplicationInsights* jest tworzony dla każdego typu danych wejściowych. Rekordy ApplicationInsights mają właściwości pokazane w następujących sekcjach:

### <a name="generic-fields"></a>Pola ogólne

| Właściwość | Opis |
| --- | --- |
| Typ | ApplicationInsights |
| KlientIP |   |
| TimeGenerated | Czas rekordu |
| ApplicationId | Klucz instrumentacji aplikacji Usługi Insights |
| ApplicationName | Nazwa aplikacji Usługi Insights aplikacji |
| RoleInstance (Nieumieja roli) | Identyfikator hosta serwera |
| DeviceType | Urządzenie klienckie |
| Rozwiązanie ekranu |   |
| Kontynent | Kontynent, na którym wniosek pochodzi |
| Kraj | Kraj/region, w którym pochodzi żądanie |
| Prowincji | Prowincja, stan lub ustawienia regionalne, z których pochodzi żądanie |
| Miasto | Miasto lub miasto, z którego pochodzi wniosek |
| isSyntetyczne | Wskazuje, czy żądanie zostało utworzone przez użytkownika, czy za pomocą metody zautomatyzowanej. Prawda = metoda automatyczna lub fałsz = wygenerowane przez użytkownika |
| SamplingRate (Szybkość próbkowania) | Procent danych telemetrycznych generowanych przez sdk, który jest wysyłany do portalu. Zakres 0,0-100,0. |
| Liczba próbkowania | 100/(SamplingRate). Na przykład 4&gt; = 25% |
| Isauthenticated | Prawda lub fałsz |
| OperationID (OperationID) | Elementy, które mają ten sam identyfikator operacji są wyświetlane jako elementy pokrewne w portalu. Zazwyczaj identyfikator żądania |
| Element nadrzędny | Identyfikator operacji nadrzędnej |
| OperationName |   |
| SessionId | Identyfikator GUID umożliwiający jednoznaczną identyfikację sesji, w której utworzono żądanie |
| SourceSystem | ApplicationInsights |

### <a name="availability-specific-fields"></a>Pola specyficzne dla dostępności

| Właściwość | Opis |
| --- | --- |
| Typ telemetrii | Dostępność |
| Nazwa testu dostępności | Nazwa testu sieci webowego |
| DostępnośćRunLocation | Źródło geograficzne żądania http |
| DostępnośćWysokie | Wskazuje wynik sukcesu testu sieci web |
| DostępnośćMessage | Wiadomość dołączona do testu sieci web |
| DostępnośćCount | 100/(Częstotliwość próbkowania). Na przykład 4&gt; = 25% |
| DataSizeMetricValue | 1,0 lub 0,0 |
| DataSizeMetricCount | 100/(Częstotliwość próbkowania). Na przykład 4&gt; = 25% |
| DostępnośćDuration | Czas trwania testu sieci Web w milisekundach |
| DostępnośćLiczna liczba | 100/(Częstotliwość próbkowania). Na przykład 4&gt; = 25% |
| Wartość dostępności |   |
| DostępnośćLiczna |   |
| DostępnośćTestIdId | Unikatowy identyfikator GUID do testu sieci Web |
| DostępnośćStyg | Precyzyjny sygnatura czasowa testu dostępności |
| DostępnośćDurationMin | W przypadku rekordów próbkowanych to pole zawiera minimalny czas trwania testu sieci Web (milisekundy) dla reprezentowanych punktów danych |
| DostępnośćDurationMax | W przypadku rekordów próbkowanych to pole zawiera maksymalny czas trwania testu sieci Web (milisekundy) dla reprezentowanych punktów danych |
| DostępnośćDurationStdDev | W przypadku rekordów próbkowanych to pole zawiera odchylenie standardowe między wszystkimi czasami trwania testu sieci Web (milisekundy) dla reprezentowanych punktów danych |
| AvailabilityMin |   |
| DostępnośćMax |   |
| DostępnośćStdDev | &nbsp;  |

### <a name="exception-specific-fields"></a>Pola specyficzne dla wyjątku

| Typ | ApplicationInsights |
| --- | --- |
| Typ telemetrii | Wyjątek |
| Typ wyjątku | Typ wyjątku |
| ExceptionMethod | Metoda, która tworzy wyjątek |
| WyjątekAsześcij | Zestaw zawiera ramy i wersję, a także token klucza publicznego |
| Grupa wyjątków | Typ wyjątku |
| ExceptionHandledAt | Wskazuje poziom, który obsługiwał wyjątek |
| Liczba wyjątków | 100/(Częstotliwość próbkowania). Na przykład 4&gt; = 25% |
| WyjątekMessage | Komunikat o wyjątku |
| WyjątekStack | Pełny stos wyjątku |
| ExceptionHasStack | Prawda, jeśli wyjątek ma stos |



### <a name="request-specific-fields"></a>Pola specyficzne dla żądania

| Właściwość | Opis |
| --- | --- |
| Typ | ApplicationInsights |
| Typ telemetrii | Żądanie |
| Kod odpowiedzi | Odpowiedź HTTP wysłana do klienta |
| PoprośSukces | Wskazuje sukces lub porażkę. Prawda lub fałsz. |
| RequestID | Identyfikator umożliwiający jednoznaczną identyfikację żądania |
| Nazwa żądania | GET/POST + baza adresów URL |
| RequestDuration (Wniosekduration) | Czas, w sekundach, czasu trwania żądania |
| Adres URL | Adres URL żądania bez hosta |
| Host | Host serwera sieci Web |
| Baza adresów URL | Pełny adres URL żądania |
| ApplicationProtocol | Typ protokołu używanego przez aplikację |
| Liczba żądań | 100/(Częstotliwość próbkowania). Na przykład 4&gt; = 25% |
| RequestDurationCount (Liczba żądańDurationCount) | 100/(Częstotliwość próbkowania). Na przykład 4&gt; = 25% |
| RequestDurationMin | W przypadku rekordów próbkowanych to pole zawiera minimalny czas trwania żądania (milisekund) dla reprezentowanych punktów danych. |
| RequestDurationMax | W przypadku rekordów próbkowanych to pole zawiera maksymalny czas trwania żądania (milisekund) dla reprezentowanych punktów danych |
| WniosekDurationStdDev | W przypadku rekordów próbkowanych to pole zawiera odchylenie standardowe między wszystkimi okresami trwania żądania (milisekundy) dla reprezentowanych punktów danych |

## <a name="sample-log-searches"></a>Przykładowe wyszukiwania dzienników

To rozwiązanie nie ma zestawu przykładowych wyszukiwań dziennika wyświetlanych na pulpicie nawigacyjnym. Jednak przykładowe zapytania wyszukiwania dziennika z opisami są wyświetlane w sekcji [Informacje o łączniku programu View Application Insights.](#view-application-insights-connector-information)

## <a name="removing-the-connector-with-powershell"></a>Usuwanie łącznika za pomocą programu PowerShell
Po usunięciu portalu usługi OMS nie można skonfigurować i usunąć istniejących połączeń z portalu. Istniejące połączenia można usunąć za pomocą następującego skryptu programu PowerShell. Aby wykonać tę operację, użytkownik musi być właścicielem lub współautorem obszaru roboczego i czytnika zasobów usługi Application Insights.

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

Można pobrać listę aplikacji przy użyciu następującego skryptu programu PowerShell, który wywołuje wywołanie interfejsu API REST. 

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
Ten skrypt wymaga tokenu uwierzytelniania na okaziciela do uwierzytelniania w usłudze Azure Active Directory. Jednym ze sposobów pobrania tego tokenu jest użycie artykułu w [witrynie dokumentacji interfejsu API REST](https://docs.microsoft.com/rest/api/loganalytics/datasources/createorupdate). Kliknij przycisk **Wypróbuj** i zaloguj się do subskrypcji platformy Azure. Token nośnika można skopiować z **podglądu żądania,** jak pokazano na poniższej ilustracji.


![Token na okaziciela](media/app-insights-connector/bearer-token.png)


Można również pobrać listę aplikacji użyć kwerendy dziennika:

```Kusto
ApplicationInsights | summarize by ApplicationName
```

## <a name="next-steps"></a>Następne kroki

- Użyj [wyszukiwania dziennika,](../../azure-monitor/log-query/log-query-overview.md) aby wyświetlić szczegółowe informacje dotyczące aplikacji usługi Application Insights.

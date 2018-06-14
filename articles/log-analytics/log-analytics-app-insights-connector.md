---
title: Wyświetl dane aplikacji Azure Application Insights | Dokumentacja firmy Microsoft
description: Rozwiązanie Application Insights łącznika można użyć do diagnozowania problemów z wydajnością i zrozumieć, co zrobić użytkownicy, z aplikacji podczas monitorowania z usługą Application Insights.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: 49280cad-3526-43e1-a365-c6a3bf66db52
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: magoedte
ms.openlocfilehash: 854ec70c897b6a561fdec056228f82ccec3ae16c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
ms.locfileid: "30186237"
---
# <a name="application-insights-connector-management-solution-preview"></a>Rozwiązania do zarządzania łącznika Insights aplikacji (wersja zapoznawcza)

![Application Insights symbol](./media/log-analytics-app-insights-connector/app-insights-connector-symbol.png)

Łącznik usługi Insights aplikacji rozwiązanie pomaga diagnozować problemy z wydajnością i zrozumieć, co zrobić użytkownicy z aplikacją, gdy jest monitorowany z [usługi Application Insights](../application-insights/app-insights-overview.md). Widoki danych telemetrycznych tej samej aplikacji, deweloperzy widoczne w usłudze Application Insights są dostępne w analizy dzienników. Jednak po zintegrowaniu aplikacji usługi Application Insights z analizy dzienników widoczność aplikacji jest zwiększana o zebranie danych operacji i aplikacji w jednym miejscu. O tym samym widoków ułatwia współpracę z deweloperów w Twojej aplikacji. Widok typowych może pomóc zmniejszyć czas do wykrywania i rozwiązywania zarówno aplikacji, jak i problemów platformy.

Użycie rozwiązania, można:

- Wyświetl wszystkie aplikacje usługi Application Insights w jednym miejscu, nawet wtedy, gdy znajdują się w różnych subskrypcji platformy Azure
- Skorelować danych infrastruktury z danych aplikacji
- Wizualizuj dane aplikacji z perspektywy wyszukiwania dziennika
- Przestawianie z analizy dzienników danych do aplikacji usługi Application Insights w portalu Azure

## <a name="connected-sources"></a>Połączone źródła

W przeciwieństwie do większości innych rozwiązań analizy dzienników nie jest zebranym dla łącznika usługi Insights aplikacji przez agentów. Wszystkie dane używane przez to rozwiązanie pochodzi bezpośrednio z platformy Azure.

| Połączone źródło | Obsługiwane | Opis |
| --- | --- | --- |
| [Agenci dla systemu Windows](log-analytics-windows-agent.md) | Nie | Rozwiązanie nie zbiera informacje z agentów systemu Windows. |
| [Agenci dla systemu Linux](log-analytics-linux-agents.md) | Nie | Rozwiązanie nie zbiera informacje z agentów systemu Linux. |
| [Grupa zarządzania programu SCOM](log-analytics-om-agents.md) | Nie | Rozwiązanie nie zbiera informacje z agentów w podłączonej grupy zarządzania SCOM. |
| [Konto usługi Azure Storage](log-analytics-azure-storage.md) | Nie | Rozwiązanie nie nie zbierają informacje z usługi Azure storage. |

## <a name="prerequisites"></a>Wymagania wstępne

- Aby uzyskać dostęp do informacji o aplikacji Insights łącznika, musi mieć subskrypcję platformy Azure
- Musi mieć co najmniej jeden zasób usługi Application Insights skonfigurowany.
- Musisz być właścicielem lub współautorem zasobu usługi Application Insights.

## <a name="configuration"></a>Konfigurowanie

1. Włącz rozwiązania analizy aplikacji sieci Web platformy Azure z [witrynę Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ApplicationInsights?tab=Overview) lub przy użyciu procesu opisanego w [rozwiązań dodać analizy dzienników z galerii rozwiązań](log-analytics-add-solutions.md).
2. W portalu OMS kliknij **ustawienia** &gt; **danych** &gt; **usługi Application Insights**.
3. W obszarze **Wybierz subskrypcję**, wybierz subskrypcję, która ma zasobów usługi Application Insights, a następnie w obszarze **Nazwa aplikacji**, wybierz co najmniej jednej aplikacji.
4. Kliknij pozycję **Zapisz**.

W ciągu 30 minut dane będą dostępne, i na kafelku usługi Application Insights został zaktualizowany o dane, takie jak na poniższej ilustracji:

![Kafelek usługi Application Insights](./media/log-analytics-app-insights-connector/app-insights-tile.png)

Inne punkty, które należy wziąć pod uwagę:

- Aplikacje usługi Application Insights można połączyć tylko jednego obszaru roboczego analizy dzienników.
- Można połączyć tylko [Basic lub Enterprise Application Insights zasobów](https://azure.microsoft.com/pricing/details/application-insights) do analizy dzienników. Można jednak użyć warstwę bezpłatna analizy dziennika.

## <a name="management-packs"></a>Pakiety administracyjne

To rozwiązanie nie instalować żadnych pakietów administracyjnych w podłączonych grup zarządzania.

## <a name="use-the-solution"></a>Użycie rozwiązania

W poniższych sekcjach opisano, jak można użyć bloków pokazywane na pulpicie nawigacyjnym usługi Application Insights do wyświetlania i interakcji z danymi z aplikacji.

### <a name="view-application-insights-connector-information"></a>Wyświetlanie informacji o aplikacji Insights łącznika

Kliknij przycisk **usługi Application Insights** Kafelek, aby otworzyć **usługi Application Insights** pulpitu nawigacyjnego, aby wyświetlić poniższe bloki.

![Pulpit nawigacyjny szczegółowych informacji w aplikacji](./media/log-analytics-app-insights-connector/app-insights-dash01.png)

![Pulpit nawigacyjny szczegółowych informacji w aplikacji](./media/log-analytics-app-insights-connector/app-insights-dash02.png)

Pulpit nawigacyjny zawiera bloki z tabelą. Każdy blok zawiera maksymalnie 10 elementów spełniających kryteria tego bloku dla określonego zakresu i zakres czasu. Można uruchomić wyszukiwania dziennika, który zwraca wszystkie rekordy po kliknięciu **zobaczyć wszystkie** w dolnej części bloku lub po kliknięciu nagłówka bloku.

[!INCLUDE [log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

| **Kolumny** | **Opis** |
| --- | --- |
| Aplikacje — liczba aplikacji | Przedstawia liczbę aplikacji w zasobów aplikacji. Zawiera także listę nazw aplikacji i dla każdej liczby rekordów w aplikacji. Kliknij liczbę do uruchamiania wyszukiwania dziennika <code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName</code> <br><br>  Kliknij nazwę aplikacji, aby uruchomić wyszukiwanie dziennika dla aplikacji, która zawiera rekordy aplikacji według hosta, rekordów typu telemetrii i wszystkie dane według typu (na podstawie ostatniego dnia). |
| Ilość danych — obsługuje wysyłanie danych | Pokazuje liczbę komputerów hostów, które wysyłają dane. Zawiera także listę hostów komputera i liczba rekordów dla każdego hosta. Kliknij liczbę do uruchamiania wyszukiwania dziennika <code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by Host</code> <br><br> Kliknij nazwę komputera, aby uruchomić wyszukiwanie dziennika dla hosta, który zawiera rekordy aplikacji według hosta, rekordów typu telemetrii i wszystkie dane według typu (na podstawie ostatniego dnia). |
| Dostępność — wyniki w teście sieci Web | Przedstawia wykres pierścieniowy dla wyników testu sieci web, wskazując pomyślnie lub niepowodzeniem. Kliknij na wykresie, aby uruchomić wyszukiwanie dziennika <code>ApplicationInsights &#124; where TelemetryType == "Availability" &#124; summarize AggregatedValue = sum(SampledCount) by AvailabilityResult</code> <br><br> Liczba błędy wszystkich testów i przebiegów w wynikach. Wszystkie aplikacje sieci Web z ruchem będzie wyświetlana w ciągu ostatniej minuty. Kliknij nazwę aplikacji, aby wyświetlić dziennik wyszukiwania zawierającego szczegóły testów sieci web nie powiodło się. |
| Żądań serwera — żądań na godzinę | Przedstawia wykres liniowy żądań serwera na godzinę dla różnych aplikacji. Umieść kursor nad wiersza na wykresie, aby wyświetlić aplikacje 3 pierwszych odbierania żądań do punktu w czasie. Zawiera także listę aplikacji odbieranie żądań i liczba żądań dla wybranego okresu. <br><br>Kliknij wykres tak, aby uruchomić wyszukiwanie dziennika <code>ApplicationInsights &#124; where TelemetryType == "Request" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> który przedstawiono bardziej szczegółowe wykres liniowy żądań serwera na godzinę dla różnych aplikacji. <br><br> Kliknij na liście uruchamiania aplikacji wyszukiwania dziennika <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code> który pokazuje listę żądań, wykresy żądań w czasie trwania czasu i żądania i listę żądania kody odpowiedzi.   |
| Błędy — nieudane żądania na godzinę | Przedstawia wykres liniowy żądań aplikacji nie powiodło się na godzinę. Umieść wskaźnik myszy na wykresie, aby wyświetlać pierwsze 3 aplikacji z żądań zakończonych niepowodzeniem dla punktu w czasie. Zawiera także listę aplikacji o liczbie nieudanych żądań dla każdego. Kliknij na wykresie, aby uruchomić wyszukiwanie dziennika <code>ApplicationInsights &#124; where TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> który przedstawiono bardziej szczegółowe wykres liniowy żądań aplikacji nie powiodło się. <br><br>Kliknij element na liście, aby uruchomić wyszukiwanie dziennika <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code> czy pokazuje nieudanych żądań, wykresy dla niepomyślnych żądań przez okres czasu i żądania i listę kodów odpowiedzi nieudanych żądań. |
| Wyjątki — wyjątków na godzinę | Przedstawia wykres liniowy wyjątków na godzinę. Umieść wskaźnik myszy na wykresie, aby wyświetlać pierwsze 3 aplikacji wyjątków dla punktu w czasie. Zawiera także listę aplikacji za pomocą liczby wyjątków dla każdego. Kliknij na wykresie, aby uruchomić wyszukiwanie dziennika <code>ApplicationInsights &#124; where TelemetryType == "Exception" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> który przedstawiono bardziej szczegółowe wykres łącze wyjątków. <br><br>Kliknij element na liście, aby uruchomić wyszukiwanie dziennika <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Exception"</code> którym wyświetlana jest lista wyjątków, wykresy dotyczące wyjątków w czasie, jak i nieudane żądania i listę typów wyjątków.  |

### <a name="view-the-application-insights-perspective-with-log-search"></a>Wyświetlanie z wyszukiwania dziennika perspektywy usługi Application Insights

Po kliknięciu dowolny element w pulpicie nawigacyjnym zostanie wyświetlony perspektywy usługi Application Insights wyświetlany w obszarze wyszukiwania. Perspektywy udostępnia rozszerzone wizualizacji na podstawie wybranego typu telemetrii. Tak, wizualizacji zmiany zawartości telemetrii różnych typów.

Gdy użytkownik kliknij w dowolnym miejscu w bloku aplikacji, zobacz domyślne **aplikacji** perspektywy.

![Perspektywa aplikacji Insights aplikacji](./media/log-analytics-app-insights-connector/applications-blade-drill-search.png)

Perspektywy przedstawiono przegląd wybranej aplikacji.

**Dostępności** bloku pokazuje widoku perspektywy różnych umożliwia wyświetlenie wyników testu sieci web i powiązane żądań zakończonych niepowodzeniem.

![Perspektywa Insights dostępność aplikacji](./media/log-analytics-app-insights-connector/availability-blade-drill-search.png)

Po kliknięciu dowolnej lokalizacji w **żądań serwera** lub **błędów** zmiany składników perspektywy bloków, aby zapewnić możliwość wizualizacji, które dotyczą żądań.

![Application Insights błędów bloku](./media/log-analytics-app-insights-connector/server-requests-failures-drill-search.png)

Po kliknięciu dowolnej lokalizacji w **wyjątki** bloku, zobacz wizualizacji dostosowane do wyjątków.

![Application Insights wyjątki bloku](./media/log-analytics-app-insights-connector/exceptions-blade-drill-search.png)

Niezależnie od tego, czy można kliknąć element jedną **Application Insights łącznik** pulpitu nawigacyjnego, poziomu **wyszukiwania** strony, każde zapytanie zwraca dane usługi Application Insights zawiera perspektywę usługi Application Insights. Na przykład, jeśli można przeglądać dane usługi Application Insights **&#42;** zapytanie wyświetla również karcie perspektywy, podobnie jak na poniższej ilustracji:

![Application Insights ](./media/log-analytics-app-insights-connector/app-insights-search.png)

Składniki perspektywy są aktualizowane w zależności od zapytania wyszukiwania. Oznacza to, że wyniki można filtrować przy użyciu każde pole wyszukiwania, które pozwala przeglądać dane z:

- Wszystkie aplikacje
- Pojedynczy wybranej aplikacji
- Grupy aplikacji

### <a name="pivot-to-an-app-in-the-azure-portal"></a>Przestawianie do aplikacji w portalu Azure

Application Insights łącznika bloki zostały zaprojektowane do tabeli przestawnej — do wybranej aplikacji usługi Application Insights *Jeśli używasz portalu OMS*. Rozwiązanie służy jako platforma wysokiego poziomu monitorowania, ułatwiające rozwiązywanie problemów z aplikacją. Po wyświetleniu potencjalny problem w żadnym z połączonych aplikacji, można albo przejście do niego w wyszukiwaniu analizy dzienników lub można przestawiać bezpośrednio do aplikacji usługi Application Insights.

Aby przestawić, kliknij przycisk wielokropka (**...** ) pojawi się na końcu każdej linii i wybierz **Otwórz w usłudze Application Insights**.

>[!NOTE]
>**Otwórz w usłudze Application Insights** nie jest dostępna w portalu Azure.

![Otwórz w usłudze Application Insights](./media/log-analytics-app-insights-connector/open-in-app-insights.png)

### <a name="sample-corrected-data"></a>Poprawione próbki danych

Usługa Application Insights udostępnia *[próbkowania korekty](../application-insights/app-insights-sampling.md)* Aby zmniejszyć ruch danych telemetrycznych. Po włączeniu próbkowania w aplikacji usługi Application Insights, możesz uzyskać zmniejszenie liczby wpisów przechowywanych zarówno w usłudze Application Insights i analizy dzienników. Gdy spójność danych jest zachowywana w **Application Insights łącznik** strony i perspektywy, należy ręcznie rozwiązać próbki danych dla zapytań niestandardowych.

Oto przykład korekty próbkowania w kwerendzie wyszukiwania dziennika:

```
ApplicationInsights | summarize AggregatedValue = sum(SampledCount) by TelemetryType
```

**Próbkowany liczba** pole znajduje się w wszystkie wpisy i pokazuje liczbę punktów danych, które reprezentuje wpis. Jeśli włączysz próbkowania dla aplikacji usługi Application Insights, **próbkowany liczba** jest większa niż 1. Suma, aby liczba rzeczywista liczba wpisów, które generuje aplikacji, **próbkowany liczba** pola.

Próbkowanie dotyczy tylko całkowita liczba wpisów, które generuje aplikacji. Nie trzeba poprawić próbkowania dla pola metryk, takich jak **RequestDuration** lub **AvailabilityDuration** ponieważ te zawierają średnią reprezentowanego wpisy.

## <a name="input-data"></a>Dane wejściowe

Rozwiązanie otrzymuje następujące typy telemetrii danych z połączonych aplikacji usługi Application Insights:

- Dostępność
- Wyjątki
- Żądania
- Wyświetleń strony — obszaru roboczego do odbierania wyświetleń strony, należy skonfigurować aplikacje do zbierania tych informacji. Aby uzyskać więcej informacji, zobacz [PageViews](../application-insights/app-insights-api-custom-events-metrics.md#page-views).
- Niestandardowe zdarzenia — obszaru roboczego do odbierania zdarzeń niestandardowych, należy skonfigurować aplikacje do zbierania tych informacji. Aby uzyskać więcej informacji, zobacz [TrackEvent](../application-insights/app-insights-api-custom-events-metrics.md#trackevent).

Danych jest odbierany przez analizy dzienników z usługi Application Insights po jej udostępnieniu.

## <a name="output-data"></a>Dane wyjściowe

Rekord z *typu* z *ApplicationInsights* jest tworzony dla każdego typu danych wejściowych. Rejestruje ApplicationInsights mają właściwości opisane w następujących sekcjach:

### <a name="generic-fields"></a>Ogólny pól

| Właściwość | Opis |
| --- | --- |
| Typ | ApplicationInsights |
| ClientIP |   |
| TimeGenerated | Czas rekordu |
| ApplicationId | Klucz instrumentacji aplikacji usługi Application Insights |
| ApplicationName | Nazwa usługi Application Insights aplikacji |
| RoleInstance | Identyfikator serwera hosta |
| DeviceType | Urządzenia klienckiego |
| ScreenResolution |   |
| Kontynent | Kontynencie, skąd pochodzi żądanie |
| Kraj | Kraju, w której pochodzi żądanie |
| Województwo | Województwo, stanu lub ustawień regionalnych, skąd pochodzi żądanie |
| Miasto | Miasta lub miejscowości, w której pochodzi żądanie |
| isSynthetic | Wskazuje, czy żądanie zostało utworzone przez użytkownika lub przez metodę automatycznego. = TRUE użytkownika wygenerowany lub = false zautomatyzowanych — metoda |
| SamplingRate | Wartość procentowa telemetrii generowane przez zestaw SDK, który jest wysyłany do portalu. Zakres niż 100,0 0,0. |
| SampledCount | 100/(SamplingRate). Na przykład, 4 =&gt; 25% |
| IsAuthenticated | Prawda lub fałsz |
| OperationID | Elementy, które mają tę samą operację identyfikator są wyświetlane jako elementy powiązane w portalu. Zazwyczaj identyfikator żądania |
| ParentOperationID | Identyfikator działania nadrzędnego |
| OperationName |   |
| Identyfikator sesji | Identyfikator GUID do jednoznacznej identyfikacji sesji, w którym utworzono żądanie |
| SourceSystem | ApplicationInsights |

### <a name="availability-specific-fields"></a>Dostępność określonych pól

| Właściwość | Opis |
| --- | --- |
| TelemetryType | Dostępność |
| AvailabilityTestName | Nazwa testu sieci web |
| AvailabilityRunLocation | Geograficzne źródłowego żądania http |
| AvailabilityResult | Wskazuje wynik wskazujący Powodzenie testu sieci web |
| AvailabilityMessage | Komunikat dołączony do testu sieci web |
| AvailabilityCount | 100 /(Sampling Rate). Na przykład, 4 =&gt; 25% |
| DataSizeMetricValue | w wersji 1.0 lub 0,0 |
| DataSizeMetricCount | 100 /(Sampling Rate). Na przykład, 4 =&gt; 25% |
| AvailabilityDuration | Czas w milisekundach czas trwania testu sieci web |
| AvailabilityDurationCount | 100 /(Sampling Rate). Na przykład, 4 =&gt; 25% |
| AvailabilityValue |   |
| AvailabilityMetricCount |   |
| AvailabilityTestId | Unikatowy identyfikator GUID dla testu sieci web |
| AvailabilityTimestamp | Dokładne sygnatura czasowa test dostępności |
| AvailabilityDurationMin | Próbki rekordów to pole zawiera czas trwania testu sieci web minimalną (w milisekundach) dla punktów danych reprezentowanego |
| AvailabilityDurationMax | Próbki rekordów to pole zawiera czas trwania testu sieci web maksymalna (w milisekundach) dla punktów danych reprezentowanego |
| AvailabilityDurationStdDev | Próbki rekordów to pole zawiera odchylenie standardowe między wszystkich sieci web testu czasów trwania (w milisekundach) dla punktów danych reprezentowanego |
| AvailabilityMin |   |
| AvailabilityMax |   |
| AvailabilityStdDev | &nbsp;  |

### <a name="exception-specific-fields"></a>Wyjątek określonych pól

| Typ | ApplicationInsights |
| --- | --- |
| TelemetryType | Wyjątek |
| ExceptionType | Typ wyjątku |
| ExceptionMethod | Metoda, która tworzy wyjątek |
| ExceptionAssembly | Obejmuje zestaw platformę i wersji, jak i token klucza publicznego |
| ExceptionGroup | Typ wyjątku |
| ExceptionHandledAt | Wskazuje poziom obsługi wyjątku |
| ExceptionCount | 100 /(Sampling Rate). Na przykład, 4 =&gt; 25% |
| ExceptionMessage | Komunikat wyjątku |
| ExceptionStack | Pełna stosu wyjątku |
| ExceptionHasStack | Wartość true, jeśli wyjątek ma stosu |



### <a name="request-specific-fields"></a>Pola specyficzne dla żądania

| Właściwość | Opis |
| --- | --- |
| Typ | ApplicationInsights |
| TelemetryType | Żądanie |
| ResponseCode | Odpowiedź HTTP wysyłane do klienta |
| RequestSuccess | Wskazuje powodzenie lub niepowodzenie. Wartość PRAWDA lub FAŁSZ. |
| Identyfikator żądania | Identyfikator do unikatowego identyfikowania żądania |
| RequestName | GET/POST + baza adresów URL |
| RequestDuration | Czas w sekundach czas trwania żądania |
| Adres URL | Adres URL żądania, z wyłączeniem hosta |
| Host | Hosta serwera sieci Web |
| URLBase | Pełny adres URL żądania |
| ApplicationProtocol | Typ protokołu używanego przez aplikację |
| RequestCount | 100 /(Sampling Rate). Na przykład, 4 =&gt; 25% |
| RequestDurationCount | 100 /(Sampling Rate). Na przykład, 4 =&gt; 25% |
| RequestDurationMin | Próbki rekordów to pole zawiera żądanie minimalny czas trwania (w milisekundach) dla punktów danych reprezentowanego. |
| RequestDurationMax | Próbki rekordów to pole zawiera maksymalny czas trwania żądania (w milisekundach) dla punktów danych reprezentowanego |
| RequestDurationStdDev | Próbki rekordów to pole zawiera odchylenie standardowe między wszystkie żądania wartości czasu trwania (w milisekundach) dla punktów danych reprezentowanego |

## <a name="sample-log-searches"></a>Przykładowe wyszukiwania dzienników

To rozwiązanie nie ma zestaw przykładowy dziennik wyszukiwania wyświetlane na pulpicie nawigacyjnym. Jednak w przedstawiono przykładowy dziennik wyszukiwania zapytania z opisami [informacji o widoku Application Insights łącznika](#view-application-insights-connector-information) sekcji.

## <a name="next-steps"></a>Kolejne kroki

- Użyj [wyszukiwania dziennika](log-analytics-log-searches.md) Aby wyświetlić szczegółowe informacje o aplikacjach Application Insights.

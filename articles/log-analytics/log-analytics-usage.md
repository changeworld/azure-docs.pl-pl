---
title: Analizowanie użycia danych w usłudze Log Analytics | Microsoft Docs
description: Pulpit nawigacyjny użycia i szacowanych kosztów w usłudze Log Analytics umożliwia szacowanie ilości danych wysyłanych do usługi Log Analytics oraz identyfikowanie potencjalnych przyczyn nieprzewidzianego wzrostu tej ilości.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 74d0adcb-4dc2-425e-8b62-c65537cef270
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/11/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: 843271901b8d58c2c5a6c4cf495997498b8278b6
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52848854"
---
# <a name="analyze-data-usage-in-log-analytics"></a>Analizowanie użycia danych w usłudze Log Analytics

> [!NOTE]
> W tym artykule opisano sposób analizowania użycia danych w usłudze Log Analytics.  Zapoznaj się z następującymi artykułami, aby uzyskać powiązane informacje.
> - [Zarządzanie kosztami przez kontrolowanie ilości danych i przechowywania w usłudze Log Analytics](log-analytics-manage-cost-storage.md) opisano, jak kontrolować ponoszone koszty, zmieniając okresu przechowywania danych.
> - [Monitorowanie użycia i szacowanych kosztów](../monitoring-and-diagnostics/monitoring-usage-and-estimated-costs.md) zawiera opis sposobu wyświetlania użycie i szacowane koszty w wielu monitorowania funkcji różne modele cen platformy Azure. Opisuje ona również, jak można zmienić modelu cen.

## <a name="understand-usage"></a>Analizy użycia

Użyj **użycie usługi Log Analytics i szacowane koszty** do przeglądania i analizowania użycia danych. Pokazuje, jak dużo danych zbieranych przez każde rozwiązanie, jak dużo danych jest zachowywane i oszacowanie kosztów na podstawie ilości danych wprowadzanych i wszelkie dodatkowe przechowywanie danych ponad uwzględnioną kwotę.

![Użycie i szacunkowe koszty](media/log-analytics-usage/usage-estimated-cost-dashboard-01.png)<br>

Eksplorowanie danych bardziej szczegółowo, kliknij ikonę w prawym górnym rogu albo wykresów na **użycie i szacowane koszty** strony. Teraz możesz pracować z tym zapytaniem, aby poznać więcej szczegółów dotyczących użycia.  

![Wyświetl dzienniki](media/log-analytics-usage/logs.png)<br>

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>Rozwiązywanie problemów związanych z użyciem przekraczającym oczekiwania
Większe użycie jest spowodowane przez jedną lub obie z następujących przyczyn:
- Do usługi Log Analytics jest wysyłana większa ilość danych niż oczekiwano
- Więcej węzłów niż oczekiwano wysyłania danych do usługi Log Analytics lub pewne węzły przesyłają więcej danych niż zwykle

Przyjrzyjmy, jak firma Microsoft może Dowiedz się więcej o obu tych przyczyn. 

> [!NOTE]
> Niektóre pola typu danych użycia, chociaż nadal w schemacie, są przestarzałe i ich wartości są już wypełnione. Są to **komputera** oraz pola powiązane z pozyskiwania (**TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**,  **BatchesCapped** i **AverageProcessingTimeMs**.

### <a name="data-volume"></a>Ilość danych 
Na **użycie i szacowane koszty** stronie *pozyskiwanie danych na rozwiązanie* wykres przedstawia łączny wolumin danych wysyłanych i ile wysyłanych przez każde rozwiązanie. Dzięki temu można określić trendy, takie jak czy rośnie całkowite użycie danych (lub użycie przez konkretnego rozwiązania), pozostały stały, czy też maleje. Zapytanie używane do generowania, to jest

`Usage| where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| summarize TotalVolumeGB = sum(Quantity) / 1024 by bin(TimeGenerated, 1d), Solution| render barchart`

Należy pamiętać, że klauzuli "gdzie IsBillable = true" odfiltrowuje typy danych, z niektórych rozwiązań, dla których nie są pobierane opłaty pozyskiwania. 

Możesz przejść dostosowaną Zobacz dane trendów dla konkretnych typów danych, na przykład jeśli chcesz badanie danych z powodu dzienniki usług IIS:

`Usage| where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| where DataType == "W3CIISLog"
| summarize TotalVolumeGB = sum(Quantity) / 1024 by bin(TimeGenerated, 1d), Solution| render barchart`

### <a name="nodes-sending-data"></a>Węzły wysyłające dane

Aby dowiedzieć się, liczba węzłów danych raportowania w ciągu ostatniego miesiąca, użyj

`Heartbeat | where TimeGenerated > startofday(ago(31d))
| summarize dcount(ComputerIP) by bin(TimeGenerated, 1d)    
| render timechart`

Aby wyświetlić liczbę zdarzeń przetwarzanych na komputerze, należy użyć

`union withsource = tt *
| summarize count() by Computer | sort by count_ nulls last`

Skorzystaj z tej kwerendy rzadko się kosztowne do wykonania. Aby wyświetlić liczbę płatnych zdarzeń wprowadzanych na komputerze, należy użyć 

`union withsource = tt * 
| where _IsBillable == true 
| summarize count() by Computer  | sort by count_ nulls last`

Jeśli chcesz zobaczyć, jakie typy danych płatnych wysyłania danych do konkretnego komputera, należy użyć:

`union withsource = tt *
| where Computer == "*computer name*"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last `

Aby wyświetlić elementy podrzędne źródło danych dla określonego typu danych, poniżej przedstawiono pewne przydatne przykładowe zapytania:

+ Rozwiązanie **zabezpieczające**
  - `SecurityEvent | summarize AggregatedValue = count() by EventID`
+ Rozwiązanie do **zarządzania dziennikami**
  - `Usage | where Solution == "LogManagement" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | summarize AggregatedValue = count() by DataType`
+ Typ danych **Perf**
  - `Perf | summarize AggregatedValue = count() by CounterPath`
  - `Perf | summarize AggregatedValue = count() by CounterName`
+ Typ danych **Event**
  - `Event | summarize AggregatedValue = count() by EventID`
  - `Event | summarize AggregatedValue = count() by EventLog, EventLevelName`
+ Typ danych **Syslog**
  - `Syslog | summarize AggregatedValue = count() by Facility, SeverityLevel`
  - `Syslog | summarize AggregatedValue = count() by ProcessName`
+ Typ danych **AzureDiagnostics**
  - `AzureDiagnostics | summarize AggregatedValue = count() by ResourceProvider, ResourceId`

### <a name="tips-for-reducing-data-volume"></a>Jak zmniejszyć wolumin danych

Sugestie dotyczące zmniejszyć wolumin zebranych danych dzienników obejmują:

| Źródło dużego woluminu danych | Jak zmniejszyć wolumin danych |
| -------------------------- | ------------------------- |
| Zdarzenia zabezpieczeń            | Wybierz [pospolite lub minimalne zdarzenia zabezpieczeń](https://blogs.technet.microsoft.com/msoms/2016/11/08/filter-the-security-events-the-oms-security-collects/). <br> Zmień zasady inspekcji zabezpieczeń w celu zbierania tylko potrzebnych zdarzeń. W szczególności zastanów się nad koniecznością zbierania następujących zdarzeń: <br> - [inspekcja platformy filtrowania](https://technet.microsoft.com/library/dd772749(WS.10).aspx) <br> - [inspekcja rejestru](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10))<br> - [inspekcja systemu plików](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10))<br> - [inspekcja obiektu jądra](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10))<br> - [inspekcja manipulowania dojściem](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10))<br> — Inspekcja magazynu wymiennego |
| Liczniki wydajności       | Zmień [konfigurację licznika wydajności](log-analytics-data-sources-performance-counters.md) w następujący sposób: <br> — Zmniejsz częstotliwość gromadzenia <br> — Zmniejsz liczbę liczników wydajności |
| Dzienniki zdarzeń                 | Zmień [konfigurację dziennika zdarzeń](log-analytics-data-sources-windows-events.md) w następujący sposób: <br> — Zmniejsz liczbę gromadzonych danych dzienników zdarzeń <br> — Zbieraj wyłącznie zdarzenia o wymaganym poziomie. Na przykład nie zbieraj zdarzeń na poziomie *Informacje*. |
| Dziennik systemu                     | Zmień [konfigurację dziennika systemu](log-analytics-data-sources-syslog.md) w następujący sposób: <br> — Zmniejsz liczbę urządzeń, z których zbierane są dane <br> — Zbieraj wyłącznie zdarzenia o wymaganym poziomie. Na przykład nie zbieraj zdarzeń na poziomie *Informacje* i *Debugowanie*. |
| AzureDiagnostics           | Zmień kolekcję dziennika zasobów, aby: <br> — zmniejszyć liczbę dzienników zasobów wysyłanych do usługi Log Analytics, <br> — zbierać tylko wymagane dzienniki. |
| Dane rozwiązań z komputerów, które nie wymagają rozwiązania | Użyj funkcji [określania celu rozwiązania](../azure-monitor/insights/solution-targeting.md), aby zbierać dane tylko z wymaganych grup komputerów. |

### <a name="getting-node-counts"></a>Pobieranie liczby węzłów 

Jeśli użytkownik pracuje na "Na węzeł (OMS)" warstwy cenowej, a następnie opłaty są naliczane na podstawie liczby węzłów i rozwiązań można używać, numer wglądu w szczegółowe dane oraz węzły Analytics, dla których są naliczane będą wyświetlane w tabeli **użycie i szacowane koszty**strony.  

Aby wyświetlić liczbę liczymy węzły zabezpieczeń, można użyć zapytania:

`union
(
    Heartbeat
    | where (Solutions has 'security' or Solutions has 'antimalware' or Solutions has 'securitycenter')
    | project Computer
),
(
    ProtectionStatus
    | where Computer !in~
    (
        (
            Heartbeat
            | project Computer
        )
    )
    | project Computer
)
| distinct Computer
| project lowComputer = tolower(Computer)
| distinct lowComputer
| count`

Aby wyświetlić liczbę liczymy węzły usługi Automation, użyj zapytania:

` ConfigurationData 
 | where (ConfigDataType == "WindowsServices" or ConfigDataType == "Software" or ConfigDataType =="Daemons") 
 | extend lowComputer = tolower(Computer) | summarize by lowComputer 
 | join (
     Heartbeat 
       | where SCAgentChannel == "Direct"
       | extend lowComputer = tolower(Computer) | summarize by lowComputer, ComputerEnvironment
 ) on lowComputer
 | summarize count() by ComputerEnvironment | sort by ComputerEnvironment asc`

## <a name="create-an-alert-when-data-collection-is-higher-than-expected"></a>Tworzenie alertu, gdy ilość zebranych danych jest większa od oczekiwanej
W tej sekcji opisano sposób tworzenia alertu w sytuacji, gdy:
- Ilość danych przekracza określoną wartość.
- Przewiduje się, że ilość danych przekroczy określoną wartość.

Alerty platformy Azure obsługują [alerty dziennika](../monitoring-and-diagnostics/monitor-alerts-unified-log.md), które korzystają z zapytań wyszukiwania. 

Poniższe zapytanie daje rezultat, jeśli w ciągu ostatnich 24 godzin zebrano więcej niż 100 GB danych:

`union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type | where DataGB > 100`

Następujące zapytanie używa prostej formuły umożliwiającej przewidywanie, kiedy w ciągu jednego dnia zostanie wysłanych więcej niż 100 GB danych: 

`union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type | where EstimatedGB > 100`

Aby utworzyć alerty dotyczące innego woluminu danych, zmień w zapytaniach wartość 100 na liczbę gigabajtów, po przekroczeniu której ma zostać wyświetlony alert.

Wykonaj kroki opisane w sekcji dotyczącej [tworzenia nowego alertu dziennika](../monitoring-and-diagnostics/alert-metric.md), aby otrzymywać powiadomienia w sytuacji, gdy ilość zebranych danych jest większa niż oczekiwano.

Podczas tworzenia alertu dla pierwszego zapytania odnoszącego się do przypadku, gdy w ciągu 24 godzin występuje więcej niż 100 GB danych, ustaw wartości elementów:  

- **Zdefiniuj warunek alertu** — określ obszar roboczy usługi Log Analytics jako element docelowy zasobu.
- **Kryteria alertu** — określ następujące informacje:
   - **Nazwa sygnału** — wybierz pozycję **Przeszukiwanie dzienników niestandardowych**
   - **Zapytanie wyszukiwania** na `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type | where DataGB > 100`
   - **Alert logiki** **opiera się na** *liczbie wyników*, a **warunek** jest *większy niż*  **próg**  wynoszący *0*
   - **Okres** o długości *1440* minut i **częstotliwość alertów** o wartości *60* minut, ponieważ dane użycia są aktualizowane tylko raz na godzinę.
- **Zdefiniuj szczegóły alertu** — określ następujące informacje:
   - **Nazwa** na *Data volume greater than 100 GB in 24 hours* (Wolumin danych większy niż 100 GB w ciągu 24 godzin)
   - **Ważność** na *Ostrzeżenie*

Określ istniejącą [grupę akcji](../monitoring-and-diagnostics/monitoring-action-groups.md) lub utwórz nową, tak aby otrzymywać powiadomienie, gdy alert dziennika spełni kryteria.

Podczas tworzenia alertu dla drugiego zapytania dotyczącego przypadku, w którym przewiduje się, że w ciągu 24 godzin wystąpi więcej niż 100 GB danych, ustaw wartości elementów:

- **Zdefiniuj warunek alertu** — określ obszar roboczy usługi Log Analytics jako element docelowy zasobu.
- **Kryteria alertu** — określ następujące informacje:
   - **Nazwa sygnału** — wybierz pozycję **Przeszukiwanie dzienników niestandardowych**
   - **Zapytanie wyszukiwania** na `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type | where EstimatedGB > 100`
   - **Alert logiki** **opiera się na** *liczbie wyników*, a **warunek** jest *większy niż*  **próg**  wynoszący *0*
   - **Okres** o długości *180* minut i **częstotliwość alertów** o wartości *60* minut, ponieważ dane użycia są aktualizowane tylko raz na godzinę.
- **Zdefiniuj szczegóły alertu** — określ następujące informacje:
   - **Nazwa** na *Data volume expected to greater than 100 GB in 24 hours* (Oczekiwany wolumin danych większy niż 100 GB w ciągu 24 godzin)
   - **Ważność** na *Ostrzeżenie*

Określ istniejącą [grupę akcji](../monitoring-and-diagnostics/monitoring-action-groups.md) lub utwórz nową, tak aby otrzymywać powiadomienie, gdy alert dziennika spełni kryteria.

Po otrzymaniu alertu wykonaj kroki przedstawione w poniższej sekcji, aby rozwiązać problemy związane z większym niż oczekiwano użyciem.

## <a name="next-steps"></a>Kolejne kroki
* Zobacz temat [Wyszukiwanie w dziennikach w usłudze Log Analytics](../azure-monitor/log-query/log-query-overview.md), aby dowiedzieć się, jak korzystać z języka wyszukiwania. Możesz użyć zapytań wyszukiwania w celu przeprowadzenia dodatkowej analizy danych użycia.
* Wykonaj kroki opisane w sekcji dotyczącej [tworzenia nowego alertu dziennika](../monitoring-and-diagnostics/alert-metric.md), aby otrzymywać powiadomienie, gdy kryteria wyszukiwania zostaną spełnione.
* Użyj funkcji [określania celu rozwiązania](../azure-monitor/insights/solution-targeting.md), aby zbierać dane tylko z wymaganych grup komputerów.
* Aby skonfigurować efektywne zasady zbierania zdarzeń zabezpieczeń, przejrzyj [zasady filtrowania usługi Azure Security Center](../security-center/security-center-enable-data-collection.md).
* Zmień [konfigurację licznika wydajności](log-analytics-data-sources-performance-counters.md).
* Aby zmodyfikować ustawienia zbierania zdarzeń, przejrzyj [konfigurację dziennika zdarzeń](log-analytics-data-sources-windows-events.md).
* Aby zmodyfikować ustawienia zbierania dla dziennika systemowego, przejrzyj [konfigurację dziennika systemowego](log-analytics-data-sources-syslog.md).

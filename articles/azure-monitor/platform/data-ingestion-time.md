---
title: Zaloguj się czas wprowadzania danych w usłudze Azure Monitor | Dokumentacja firmy Microsoft
description: W tym artykule wyjaśniono różne czynniki, które mają wpływ na opóźnienie w zbieraniu danych dziennika w usłudze Azure Monitor.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: log-analytics
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/24/2019
ms.author: bwren
ms.openlocfilehash: ba9a0ab775e062f21a058b537e289fe3ea2b40bb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61093968"
---
# <a name="log-data-ingestion-time-in-azure-monitor"></a>Czas pozyskiwanie danych dziennika w usłudze Azure Monitor
Usługa Azure Monitor to usługa danych dużej skali, która obsługuje tysiące klientów wysyłania terabajtów danych każdego miesiąca w tempie rosnącą. Są często zadawane pytania dotyczące czasu, jaki zajmuje za dane dziennika staną się dostępne po ich zebraniu. W tym artykule opisano różne czynniki, które wpływają na ten czas oczekiwania.

## <a name="typical-latency"></a>Typowy czas oczekiwania
Opóźnienie odnosi się do danych jest tworzona w monitorowanym systemie czas i czas, który jest dostarczany poddać analizie w usłudze Azure Monitor. Typowe opóźnienia w celu pozyskiwania danych dziennika jest od 2 do 5 minut. Określone opóźnienie dla dowolnych danych określonego różnią się w zależności od różnych czynników, które zostało opisane poniżej.


## <a name="factors-affecting-latency"></a>Czynniki wpływające na opóźnienie
Czas całkowity pozyskiwania dla określonego zestawu danych można podzielić na następujących kategoriach wysokiego poziomu. 

- Czas agenta — czas odnajdywania zdarzenie, zbieranie go i wysłać go do usługi Azure Monitor punktem pozyskiwania jako rekord dziennika. W większości przypadków proces ten jest obsługiwany przez agenta.
- Czas potoku — czas przetwarzania rekordu dziennika przez potok pozyskiwania. W tym analizy właściwości zdarzenia i potencjalnie dodać informacje obliczeniowych.
- Czas indeksowania — czas do pozyskiwania rekordu dziennika w magazynie danych big Data w usłudze Azure Monitor.

Szczegółowe informacje na temat różnych opóźnienia w ramach tego procesu są opisane poniżej.

### <a name="agent-collection-latency"></a>Czas oczekiwania na agenta kolekcji
Rozwiązań do zarządzania i agentów należy użyć różne strategie zbierania danych z maszyny wirtualnej, która może wpływać na czas oczekiwania. Oto kilka przykładów:

- Windows zdarzeń, zdarzenia dziennika systemowego i metryki wydajności są zbierane od razu. Liczniki wydajności systemu Linux są wysyłane w odstępach 30 sekund.
- Dzienniki usług IIS i niestandardowe dzienniki są zbierane, gdy zmieni się ich sygnatury czasowej. W przypadku dzienników usług IIS to ma wpływ [skonfigurowany na serwerze IIS Harmonogram przerzucania](data-sources-iis-logs.md). 
- Rozwiązania z usługą Active Directory Replication wykonuje oceny co pięć dni, podczas gdy rozwiązania oceny usługi Active Directory wykonuje co tydzień oceny infrastruktury usługi Active Directory. Agent będzie zbierać dzienniki, tylko wtedy, gdy oceny zostało zakończone.

### <a name="agent-upload-frequency"></a>Agent przekazywania częstotliwości
Aby upewnić się, że agent usługi Log Analytics jest uproszczone, agent buforuje dzienniki i okresowo przesyła je do usługi Azure Monitor. Przekaż częstotliwość waha się między 30 sekund i 2 minut w zależności od typu danych. Większość danych jest przekazywany w obszarze 1 minutę. Warunki sieciowe może negatywnie wpłynąć na czas oczekiwania na tych danych do usługi Azure Monitor punktem pozyskiwania osiągną.

### <a name="azure-activity-logs-diagnostic-logs-and-metrics"></a>Dzienniki aktywności platformy Azure, dzienniki diagnostyczne i metryki
Usługa Azure data dodaje dodatkowy czas staną się dostępne w momencie pozyskiwania usługi Log Analytics do przetworzenia:

- Dane z dzienników diagnostycznych potrwać 2 – 15 minut, w zależności od usługi platformy Azure. Zobacz [poniższe zapytanie](#checking-ingestion-time) do sprawdzenia tego opóźnienia w danym środowisku
- Metryki platformy Azure zająć więcej niż trzy minuty do wysłania do usługi Log Analytics punktem pozyskiwania.
- Dane dzienników aktywności potrwa około 10 – 15 minut do wysłania do usługi Log Analytics punktem pozyskiwania.

Po jego udostępnieniu w momencie pozyskiwania danych ma dodatkowe 2 do 5 minut, które będą dostępne dla zapytań.

### <a name="management-solutions-collection"></a>Zarządzanie rozwiązaniami kolekcji
Niektóre rozwiązania nie zbierają dane z agenta i może używać metody kolekcji, które wprowadza dodatkowe opóźnienie. Niektóre rozwiązania zbierania danych w regularnych odstępach czasu, bez próby kolekcji niemal w czasie rzeczywistym. Konkretne przykłady są następujące:

- Rozwiązania dla pakietu Office 365 sonduje dzienników aktywności, za pomocą pakietu Office 365 działania interfejsu API zarządzania, które aktualnie nie zapewniają gwarancje wszelkie opóźnienia niemal w czasie rzeczywistym.
- Windows Analytics solutions (zgodność aktualizacji na przykład) dane są zbierane przez rozwiązania z częstotliwością dziennych.

Zapoznaj się z dokumentacją dla każdego rozwiązania jej częstotliwość zbierania.

### <a name="pipeline-process-time"></a>Czas przetwarzania potoku
Po rekordy dziennika są pozyskiwane do potoku usługi Azure Monitor, są one zapisywane do magazynu tymczasowego w celu zapewnienia izolacji dzierżawy i upewnij się, że dane nie są tracone. Ten proces zwykle dodaje 5 – 15 sekund. Niektóre rozwiązania do zarządzania wdrożenie większych algorytmów do agregacji danych i uzyskiwania szczegółowych informacji, zgodnie z przesyłania strumieniowego danych w. Na przykład monitorowanie wydajności sieci agreguje dane przychodzące w odstępach 3-minutowy skutecznie Dodawanie 3-minutowy czas oczekiwania. Inny proces, który dodaje opóźnienie to proces, który obsługuje niestandardowe dzienniki. W niektórych przypadkach ten proces może dodać kilka minut opóźnienia do dzienników, które są zbierane z plików przez agenta.

### <a name="new-custom-data-types-provisioning"></a>Nowe typy danych niestandardowych, inicjowanie obsługi administracyjnej
Po utworzeniu nowego typu danych niestandardowych z [dziennik niestandardowy](data-sources-custom-logs.md) lub [interfejsu API modułu zbierającego dane](data-collector-api.md), system tworzy kontener dedykowanych dla magazynu. Jest to jednorazowa obciążenie, które występuje tylko na pierwsze wystąpienie tego typu danych.

### <a name="surge-protection"></a>Ochrona skoków
O najwyższym priorytecie usługi Azure Monitor jest upewnij się, że żadne dane klienta zostały utracone, więc system ma wbudowaną ochronę gwałtownych danych. Dotyczy to również buforów, aby upewnić się, czy nawet obciążeniem ogromną, system będzie nadal działa. W warunkach normalnego obciążenia tych kontrolek dodać mniej niż minutę, ale w ekstremalnych warunków i błędów, może ona dodać znaczną ilość czasu, przy jednoczesnym zapewnieniu danych jest bezpieczne.

### <a name="indexing-time"></a>Godziny indeksowania
Ma wbudowane równoważenie dla każdej platformy danych big data, od analizy i możliwościom wyszukiwania zaawansowanego w przeciwieństwie zapewniając natychmiastowy dostęp do danych. Usługa Azure Monitor pozwala na uruchamianie zaawansowanych zapytań dotyczących miliardów rekordów i uzyskuj wyników w ciągu kilku sekund. Jest to możliwe ponieważ infrastruktury znacznie przekształca dane podczas ich pozyskiwania i zapisuje go w strukturach compact unikatowy. System buforuje dane, dopóki nie jest wystarczająca ilość dostępnych do tworzenia tych struktur. Należy to wykonać, zanim rekord dziennika jest wyświetlana w wynikach wyszukiwania.

Ten proces trwa obecnie około 5 minut po niskiej ilości danych, ale mniej czasu wyższych stawek za dane. Ta wydaje się nielogiczna, ale ten proces umożliwia optymalizacji czas oczekiwania dla obciążeń produkcyjnych dużej liczby.



## <a name="checking-ingestion-time"></a>Sprawdzanie czasu wprowadzania
Czas wprowadzania mogą być różne dla różnych zasobów w różnych okolicznościach. Dziennik zapytań służy do identyfikowania określone zachowanie środowiska.

### <a name="ingestion-latency-delays"></a>Pozyskiwanie opóźnienie opóźnienia
Opóźnienie określonego rekordu można zmierzyć, porównując wynik [ingestion_time()](/azure/kusto/query/ingestiontimefunction) funkcja _TimeGenerated_ pola. Tych danych może służyć za pomocą różnych agregacji, aby dowiedzieć się, jak zachowuje się opóźnienia w pozyskiwaniu danych. Sprawdź niektórych. percentyl czasu pozyskiwania, aby uzyskać szczegółowe informacje z dużych ilości danych. 

Na przykład następujące zapytanie będzie pokazują, komputery, które miały najwyższą czas wprowadzania za pośrednictwem bieżącego dnia: 

``` Kusto
Heartbeat
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95) by Computer 
| top 20 by percentile_E2EIngestionLatency_95 desc  
```
 
Jeśli chcesz przejść do szczegółów czasu wprowadzania dla określonego komputera w określonym czasie, użyj następującego zapytania, która również wizualizuje dane w postaci wykresu: 

``` Kusto
Heartbeat 
| where TimeGenerated > ago(24h) and Computer == "ContosoWeb2-Linux"  
| extend E2EIngestionLatencyMin = todouble(datetime_diff("Second",ingestion_time(),TimeGenerated))/60 
| summarize percentiles(E2EIngestionLatencyMin,50,95) by bin(TimeGenerated,30m) 
| render timechart  
```
 
Użyj następującego zapytania, aby wyświetlić czas wprowadzania na komputerze według kraju, że znajdują się one w opartym na adresy IP: 

``` Kusto
Heartbeat 
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95) by RemoteIPCountry 
```
 
Różne typy danych pochodzące od agenta może być czas opóźnienia różnych pozyskiwania, więc poprzednich zapytań, można użyć z innymi typami. Użyj następującego zapytania, aby sprawdzić czas wprowadzania różne usługi platformy Azure: 

``` Kusto
AzureDiagnostics 
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95) by ResourceProvider
```

### <a name="resources-that-stop-responding"></a>Zasoby, które przestanie odpowiadać 
W niektórych przypadkach zasobu można zatrzymać wysyłanie danych. Aby dowiedzieć się, jeśli zasób jest wysyłanie danych, czy nie, Przyjrzyj się jej najbardziej aktualną rekordu, który może być zidentyfikowany przez standard _TimeGenerated_ pola.  

Użyj _pulsu_ tabeli, aby sprawdzić dostępność maszyny Wirtualnej, ponieważ pulsu są wysyłane raz na minutę przez agenta. Użyj następującego zapytania, aby wyświetlić listę aktywnych komputerów, które nie zostały zgłoszone ostatnio pulsu: 

``` Kusto
Heartbeat  
| where TimeGenerated > ago(1d) //show only VMs that were active in the last day 
| summarize NoHeartbeatPeriod = now() - max(TimeGenerated) by Computer  
| top 20 by NoHeartbeatPeriod desc 
```

## <a name="next-steps"></a>Kolejne kroki
* Odczyt [poziomu umowy dotyczącej usług (SLA)](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_1/) dla usługi Azure Monitor.


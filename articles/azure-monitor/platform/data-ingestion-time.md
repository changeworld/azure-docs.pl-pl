---
title: Rejestrowanie czasu pozyskiwania danych w usłudze Azure Monitor | Dokumenty firmy Microsoft
description: Wyjaśniono różne czynniki, które wpływają na opóźnienie w zbieraniu danych dziennika w usłudze Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/18/2019
ms.openlocfilehash: 99d5594dd3ebe3750cb0a09ea803065e2aeb5ba2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77666641"
---
# <a name="log-data-ingestion-time-in-azure-monitor"></a>Czas pozyskiwania danych dziennika w usłudze Azure Monitor
Usługa Azure Monitor to usługa danych na dużą skalę, która obsługuje tysiące klientów wysyłających terabajty danych każdego miesiąca w rosnącym tempie. Często pojawiają się pytania dotyczące czasu, jaki zajmuje udostępnienie danych dziennika po ich zebraniu. W tym artykule wyjaśniono różne czynniki, które wpływają na to opóźnienie.

## <a name="typical-latency"></a>Typowe opóźnienie
Opóźnienie odnosi się do czasu, przez który dane są tworzone w monitorowanym systemie i czasu, który jest dostępny do analizy w usłudze Azure Monitor. Typowe opóźnienie do pozyskiwania danych dziennika wynosi od 2 do 5 minut. Szczególne opóźnienie dla poszczególnych danych będzie się różnić w zależności od różnych czynników wyjaśnionych poniżej.


## <a name="factors-affecting-latency"></a>Czynniki wpływające na opóźnienie
Całkowity czas pozyskiwania dla określonego zestawu danych można podzielić na następujące obszary wysokiego poziomu. 

- Czas agenta — czas, aby odnajdywać zdarzenie, zbierać go, a następnie wysłać do punktu pozyskiwania usługi Azure Monitor jako rekord dziennika. W większości przypadków ten proces jest obsługiwany przez agenta.
- Czas potoku — czas przetwarzania rekordu dziennika przez potok pozyskiwania. Obejmuje to analizowanie właściwości zdarzenia i potencjalnie dodawanie informacji obliczeniowych.
- Czas indeksowania — czas poświęcony na spożycie rekordu dziennika do magazynu dużych zbiorów danych usługi Azure Monitor.

Szczegóły dotyczące różnych opóźnień wprowadzonych w tym procesie są opisane poniżej.

### <a name="agent-collection-latency"></a>Opóźnienie zbierania agenta
Agenci i rozwiązania do zarządzania używają różnych strategii do zbierania danych z maszyny wirtualnej, co może mieć wpływ na opóźnienie. Oto kilka konkretnych przykładów:

- Zdarzenia systemu Windows, zdarzenia syslog i metryki wydajności są zbierane natychmiast. Liczniki wydajności systemu Linux są sondowane w odstępach 30-sekundowych.
- Dzienniki i dzienniki niestandardowe usługi IIS są zbierane po zmianie sygnatury czasowych. W przypadku dzienników iis zależy od [harmonogramu najazdów skonfigurowanych w serwisach IIS](data-sources-iis-logs.md). 
- Rozwiązanie Replikacja usługi Active Directory wykonuje swoją ocenę co pięć dni, podczas gdy rozwiązanie oceny usługi Active Directory przeprowadza cotygodniową ocenę infrastruktury usługi Active Directory. Agent będzie zbierać te dzienniki tylko po zakończeniu oceny.

### <a name="agent-upload-frequency"></a>Częstotliwość przekazywania agenta
Aby upewnić się, że agent usługi Log Analytics jest lekki, agent buforuje dzienniki i okresowo przekazuje je do usługi Azure Monitor. Częstotliwość przesyłania waha się od 30 sekund do 2 minut w zależności od typu danych. Większość danych jest przesyłana w mniej niż 1 minutę. Warunki sieciowe mogą negatywnie wpłynąć na opóźnienie tych danych, aby osiągnąć punkt pozyskiwania usługi Azure Monitor.

### <a name="azure-activity-logs-resource-logs-and-metrics"></a>Dzienniki aktywności platformy Azure, dzienniki zasobów i metryki
Dane platformy Azure dodaje dodatkowy czas, aby stać się dostępne w log analytics punkt pozyskiwania do przetwarzania:

- Dane z dzienników zasobów trwać 2-15 minut, w zależności od usługi platformy Azure. Zobacz [poniższą kwerendę,](#checking-ingestion-time) aby sprawdzić to opóźnienie w twoim środowisku
- Metryki platformy Azure trwać 3 minuty, aby wysłać do punktu pozyskiwania usługi Log Analytics.
- Dane dziennika aktywności zajmie około 10-15 minut, które mają być wysyłane do punktu pozyskiwania usługi Log Analytics.

Po udostępnieniu w punkcie pozyskiwania dane trwa dodatkowe 2-5 minut, aby być dostępne dla zapytań.

### <a name="management-solutions-collection"></a>Zbieranie rozwiązań do zarządzania
Niektóre rozwiązania nie zbierają swoich danych od agenta i mogą używać metody zbierania, która wprowadza dodatkowe opóźnienia. Niektóre rozwiązania zbierają dane w regularnych odstępach czasu bez podejmowania prób zbierania w czasie zbliżonym do rzeczywistego. Konkretne przykłady są następujące:

- Rozwiązanie usługi Office 365 sonduje dzienniki aktywności przy użyciu interfejsu API działania zarządzania usługą Office 365, który obecnie nie zapewnia żadnych gwarancji opóźnienia w czasie zbliżonym do rzeczywistego.
- Rozwiązania Windows Analytics (na przykład update compliance) dane są zbierane przez rozwiązanie z dzienną częstotliwością.

Zapoznaj się z dokumentacją dla każdego rozwiązania, aby określić jego częstotliwość zbierania.

### <a name="pipeline-process-time"></a>Czas procesu potoku
Po wprowadzeniu rekordów dziennika do potoku usługi Azure Monitor (zgodnie z zidentyfikowaniem we właściwości [_TimeReceived),](log-standard-properties.md#_timereceived) są one zapisywane do magazynu tymczasowego, aby zapewnić izolację dzierżawy i upewnić się, że dane nie zostaną utracone. Ten proces zazwyczaj dodaje 5-15 sekund. Niektóre rozwiązania do zarządzania implementują cięższe algorytmy do agregowania danych i uzyskiwania szczegółowych informacji podczas przesyłania strumieniowego danych. Na przykład monitorowanie wydajności sieci agreguje przychodzące dane w odstępach 3-minutowych, skutecznie dodając 3-minutowe opóźnienie. Innym procesem, który dodaje opóźnienie jest proces, który obsługuje dzienniki niestandardowe. W niektórych przypadkach ten proces może dodać kilka minut opóźnienia do dzienników, które są zbierane z plików przez agenta.

### <a name="new-custom-data-types-provisioning"></a>Nowe niestandardowe typy danych inicjowania obsługi administracyjnej
Po utworzeniu nowego typu danych niestandardowych z [dziennika niestandardowego](data-sources-custom-logs.md) lub [interfejsu API modułu zbierającego dane](data-collector-api.md)system tworzy dedykowany kontener magazynu. Jest to jednorazowe obciążenie, które występuje tylko przy pierwszym pojawieniu się tego typu danych.

### <a name="surge-protection"></a>Ochrona przeciwprzepięciowa
Priorytetem usługi Azure Monitor jest zapewnienie, że żadne dane klienta nie są tracone, więc system ma wbudowaną ochronę przed przepięciami danych. Obejmuje to bufory, aby zapewnić, że nawet przy ogromnym obciążeniu system będzie nadal funkcjonował. Przy normalnym obciążeniu te formanty dodać mniej niż minutę, ale w ekstremalnych warunkach i awarii mogą dodać znaczny czas przy jednoczesnym zapewnieniu bezpieczeństwa danych.

### <a name="indexing-time"></a>Czas indeksowania
Istnieje wbudowana równowaga dla każdej platformy dużych zbiorów danych między dostarczaniem analiz i zaawansowanych funkcji wyszukiwania, a nie zapewnia natychmiastowy dostęp do danych. Usługa Azure Monitor umożliwia uruchamianie zaawansowanych zapytań na miliardach rekordów i uzyskanie wyników w ciągu kilku sekund. Jest to możliwe, ponieważ infrastruktura przekształca dane dramatycznie podczas ich pozyskiwania i przechowuje je w unikalnych strukturach kompaktowych. System buforuje dane, dopóki nie będzie ich wystarczającej ilości do utworzenia tych struktur. Należy to zakończyć, zanim rekord dziennika pojawi się w wynikach wyszukiwania.

Ten proces trwa obecnie około 5 minut, gdy jest mała ilość danych, ale mniej czasu przy wyższych szybkościach transmisji danych. Wydaje się to sprzeczne z intuicją, ale ten proces umożliwia optymalizację opóźnień dla obciążeń produkcyjnych o dużej objętości.



## <a name="checking-ingestion-time"></a>Sprawdzanie czasu położenia
Czas spożycia może się różnić w zależności od zasobów w różnych okolicznościach. Kwerendy dziennika można użyć do identyfikowania określonego zachowania środowiska. W poniższej tabeli określono, jak można określić różne godziny dla rekordu, ponieważ jest on tworzony i wysyłany do usługi Azure Monitor.

| Krok | Właściwość lub funkcja | Komentarze |
|:---|:---|:---|
| Rekord utworzony w źródle danych | [TimeGenerated](log-standard-properties.md#timegenerated-and-timestamp) <br>Jeśli źródło danych nie ustawi tej wartości, zostanie ona ustawiona na ten sam czas, co _TimeReceived. |
| Rekord odebrany przez punkt końcowy pozyskiwania usługi Azure Monitor | [_TimeReceived](log-standard-properties.md#_timereceived) | |
| Nagrywanie przechowywane w obszarze roboczym i dostępne dla kwerend | [ingestion_time()](/azure/kusto/query/ingestiontimefunction) | |

### <a name="ingestion-latency-delays"></a>Opóźnienia opóźnienia połknięcia
Opóźnienie określonego rekordu można zmierzyć, porównując wynik funkcji [ingestion_time()](/azure/kusto/query/ingestiontimefunction) z właściwością _TimeGenerated._ Te dane mogą być używane z różnych agregacji, aby znaleźć, jak zachowuje się opóźnienie pozyskiwania. Sprawdź niektóre percentyl czasu pozyskiwania, aby uzyskać szczegółowe informacje na dużą ilość danych. 

Na przykład następująca kwerenda pokaże, które komputery miały najwyższy czas pozyskiwania w ciągu poprzednich 8 godzin: 

``` Kusto
Heartbeat
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| extend AgentLatency = _TimeReceived - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95), percentiles(AgentLatency,50,95) by Computer 
| top 20 by percentile_E2EIngestionLatency_95 desc
```

Poprzednie kontrole percentyla są dobre do znajdowania ogólnych trendów w opóźnieniu. Aby zidentyfikować krótkoterminowe skok opóźnienia, przy użyciu`max()`maksymalnego ( ) może być bardziej skuteczne.

Jeśli chcesz przejść do szczegółów czasu pozyskiwania dla określonego komputera w danym okresie czasu, użyj następującej kwerendy, która również wizualizuje dane z ostatniego dnia na wykresie: 


``` Kusto
Heartbeat 
| where TimeGenerated > ago(24h) //and Computer == "ContosoWeb2-Linux"  
| extend E2EIngestionLatencyMin = todouble(datetime_diff("Second",ingestion_time(),TimeGenerated))/60 
| extend AgentLatencyMin = todouble(datetime_diff("Second",_TimeReceived,TimeGenerated))/60 
| summarize percentiles(E2EIngestionLatencyMin,50,95), percentiles(AgentLatencyMin,50,95) by bin(TimeGenerated,30m) 
| render timechart
```
 
Użyj następującej kwerendy, aby pokazać czas pozyskiwania komputera przez kraj/region, w którym się znajdują, w którym jest oparty na ich adresIE IP: 

``` Kusto
Heartbeat 
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| extend AgentLatency = _TimeReceived - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95),percentiles(AgentLatency,50,95) by RemoteIPCountry 
```
 
Różne typy danych pochodzących z agenta może mieć inny czas opóźnienia pozyskiwania, więc poprzednie kwerendy mogą być używane z innymi typami. Użyj następującej kwerendy, aby sprawdzić czas pozyskiwania różnych usług platformy Azure: 

``` Kusto
AzureDiagnostics 
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| extend AgentLatency = _TimeReceived - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95), percentiles(AgentLatency,50,95) by ResourceProvider
```

### <a name="resources-that-stop-responding"></a>Zasoby, które przestają odpowiadać 
W niektórych przypadkach zasób może zatrzymać wysyłanie danych. Aby zrozumieć, czy zasób wysyła dane, czy nie, spójrz na jego najnowszy rekord, który może być zidentyfikowany przez standardowe pole _TimeGenerated._  

Użyj _Pulsu_ tabeli, aby sprawdzić dostępność maszyny Wirtualnej, ponieważ puls jest wysyłany raz na minutę przez agenta. Użyj następującej kwerendy, aby wyświetlić listę aktywnych komputerów, które nie zgłosiły ostatnio pulsu: 

``` Kusto
Heartbeat  
| where TimeGenerated > ago(1d) //show only VMs that were active in the last day 
| summarize NoHeartbeatPeriod = now() - max(TimeGenerated) by Computer  
| top 20 by NoHeartbeatPeriod desc 
```

## <a name="next-steps"></a>Następne kroki
* Przeczytaj [umowę dotyczącą poziomu usług (SLA)](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_1/) dla usługi Azure Monitor.


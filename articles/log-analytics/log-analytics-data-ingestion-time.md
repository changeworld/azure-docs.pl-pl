---
title: Czas wprowadzania danych w usłudze Azure Log Analytics | Dokumentacja firmy Microsoft
description: W tym artykule wyjaśniono różne czynniki, które mają wpływ na opóźnienie w zbieraniu danych w usłudze Azure Log Analytics.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: log-analytics
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2018
ms.author: bwren
ms.openlocfilehash: 7a7ba9f7a990a563a64a255ed6468c3b8e2534d5
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2018
ms.locfileid: "52724336"
---
# <a name="data-ingestion-time-in-log-analytics"></a>Czas wprowadzania danych w usłudze Log Analytics
Usługa Azure Log Analytics to usługa danych dużej skali w usłudze Azure Monitor, pełniącą tysiące klientów wysyłania terabajtów danych każdego miesiąca w tempie rosnącą. Są często zadawane pytania dotyczące czasu, jaki zajmuje dane staną się dostępne w usłudze Log Analytics po ich zebraniu. W tym artykule opisano różne czynniki, które wpływają na ten czas oczekiwania.

## <a name="typical-latency"></a>Typowy czas oczekiwania
Opóźnienie odnosi się do danych jest tworzona w monitorowanym systemie czas i czas, który jest dostarczany poddać analizie w usłudze Log Analytics. Typowe opóźnienia w celu pozyskiwania danych w usłudze Log Analytics to od 2 do 5 minut. Określone opóźnienie dla dowolnych danych określonego różnią się w zależności od różnych czynników, które zostało opisane poniżej.


## <a name="factors-affecting-latency"></a>Czynniki wpływające na opóźnienie
Czas całkowity pozyskiwania dla określonego zestawu danych można podzielić na następujących kategoriach wysokiego poziomu. 

- Czas agenta — czas odnajdywania zdarzenie, zbieranie go i wysłać go do usługi Log Analytics punktem pozyskiwania jako rekord dziennika. W większości przypadków proces ten jest obsługiwany przez agenta.
- Czas potoku — czas potok pozyskiwania do przetwarzania rekordu dziennika. W tym analizy właściwości zdarzenia i potencjalnie dodać informacje obliczeniowych.
- Czas indeksowania — czas do pozyskiwania rekordu dziennika w magazynie danych big Data Analytics dziennika.

Szczegółowe informacje na temat różnych opóźnienia w ramach tego procesu są opisane poniżej.

### <a name="agent-collection-latency"></a>Czas oczekiwania na agenta kolekcji
Rozwiązań do zarządzania i agentów należy użyć różne strategie zbierania danych z maszyny wirtualnej, która może wpływać na czas oczekiwania. Oto kilka przykładów:

- Windows zdarzeń, zdarzenia dziennika systemowego i metryki wydajności są zbierane od razu. Liczniki wydajności systemu Linux są wysyłane w odstępach 30 sekund.
- Dzienniki usług IIS i niestandardowe dzienniki są zbierane, gdy zmieni się ich sygnatury czasowej. W przypadku dzienników usług IIS to ma wpływ [skonfigurowany na serwerze IIS Harmonogram przerzucania](../azure-monitor/platform/data-sources-iis-logs.md). 
- Rozwiązania z usługą Active Directory Replication wykonuje oceny co pięć dni, podczas gdy rozwiązania oceny usługi Active Directory wykonuje co tydzień oceny infrastruktury usługi Active Directory. Agent będzie zbierać dzienniki, tylko wtedy, gdy oceny zostało zakończone.

### <a name="agent-upload-frequency"></a>Agent przekazywania częstotliwości
Aby upewnić się, że agent usługi Log Analytics jest uproszczone, agent buforuje dzienniki i okresowo przesyła je do usługi Log Analytics. Przekaż częstotliwość waha się między 30 sekund i 2 minut w zależności od typu danych. Większość danych jest przekazywany w obszarze 1 minutę. Warunki sieciowe może negatywnie wpłynąć na czas oczekiwania na tych danych do usługi Log Analytics punktem pozyskiwania osiągną.

### <a name="azure-logs-and-metrics"></a>Dzienniki platformy Azure i metryk 
Dane dzienników aktywności może potrwać około 5 minut na udostępnienie w usłudze Log Analytics. Dane z dzienniki diagnostyczne i metryki może potrwać od 1 do 5 minut stają się dostępne, w zależności od usługi platformy Azure. Następnie przyjmują dodatkowe 30 – 60 sekund dla dzienników i więcej niż trzy minuty dla metryk dla danych do wysłania do usługi Log Analytics punktem pozyskiwania.

### <a name="management-solutions-collection"></a>Zarządzanie rozwiązaniami kolekcji
Niektóre rozwiązania nie zbierają dane z agenta i może używać metody kolekcji, które wprowadza dodatkowe opóźnienie. Niektóre rozwiązania zbierania danych w regularnych odstępach czasu, bez próby kolekcji niemal w czasie rzeczywistym. Konkretne przykłady są następujące:

- Rozwiązania dla pakietu Office 365 sonduje dzienników aktywności, za pomocą pakietu Office 365 działania interfejsu API zarządzania, które aktualnie nie zapewniają gwarancje wszelkie opóźnienia niemal w czasie rzeczywistym.
- Windows Analytics solutions (zgodność aktualizacji na przykład) dane są zbierane przez rozwiązania z częstotliwością dziennych.

Zapoznaj się z dokumentacją dla każdego rozwiązania jej częstotliwość zbierania.

### <a name="pipeline-process-time"></a>Czas przetwarzania potoku
Po rekordy dziennika są pozyskiwane potokiem usługi Log Analytics, są one zapisywane do magazynu tymczasowego w celu zapewnienia izolacji dzierżawy i upewnij się, że dane nie są tracone. Ten proces zwykle dodaje 5 – 15 sekund. Niektóre rozwiązania do zarządzania wdrożenie większych algorytmów do agregacji danych i uzyskiwania szczegółowych informacji, zgodnie z przesyłania strumieniowego danych w. Na przykład monitorowanie wydajności sieci agreguje dane przychodzące w odstępach 3-minutowy skutecznie Dodawanie 3-minutowy czas oczekiwania. Inny proces, który dodaje opóźnienie to proces, który obsługuje niestandardowe dzienniki. W niektórych przypadkach ten proces może dodać kilka minut opóźnienia do dzienników, które są zbierane z plików przez agenta.

### <a name="new-custom-data-types-provisioning"></a>Nowe typy danych niestandardowych, inicjowanie obsługi administracyjnej
Po utworzeniu nowego typu danych niestandardowych z [dziennik niestandardowy](../log-analytics/../azure-monitor/platform/data-sources-custom-logs.md) lub [interfejsu API modułu zbierającego dane](../log-analytics/log-analytics-data-collector-api.md), system tworzy kontener dedykowanych dla magazynu. Jest to jednorazowa obciążenie, które występuje tylko na pierwsze wystąpienie tego typu danych.

### <a name="surge-protection"></a>Ochrona skoków
Najwyższy priorytet, usługi Log Analytics jest upewnij się, że żadne dane klienta zostały utracone, więc system ma wbudowaną ochronę gwałtownych danych. Dotyczy to również buforów, aby upewnić się, czy nawet obciążeniem ogromną, system będzie nadal działa. W warunkach normalnego obciążenia tych kontrolek dodać mniej niż minutę, ale w ekstremalnych warunków i błędów, może ona dodać znaczną ilość czasu, przy jednoczesnym zapewnieniu danych jest bezpieczne.

### <a name="indexing-time"></a>Godziny indeksowania
Ma wbudowane równoważenie dla każdej platformy danych Big Data, od analizy i możliwościom wyszukiwania zaawansowanego w przeciwieństwie zapewniając natychmiastowy dostęp do danych. Usługa Azure Log Analytics pozwala na uruchamianie zaawansowanych zapytań dotyczących miliardów rekordów i uzyskuj wyników w ciągu kilku sekund. Jest to możliwe ponieważ infrastruktury znacznie przekształca dane podczas ich pozyskiwania i zapisuje go w strukturach compact unikatowy. System buforuje dane, dopóki nie jest wystarczająca ilość dostępnych do tworzenia tych struktur. Należy to wykonać, zanim rekord dziennika jest wyświetlana w wynikach wyszukiwania.

Ten proces trwa obecnie około 5 minut po niskiej ilości danych, ale mniej czasu wyższych stawek za dane. Ta wydaje się nielogiczna, ale ten proces umożliwia optymalizacji czas oczekiwania dla obciążeń produkcyjnych dużej liczby.



## <a name="checking-ingestion-time"></a>Sprawdzanie czasu wprowadzania
Możesz użyć **pulsu** tabeli, aby poznać szacunkową liczbę opóźnieniami dla danych z agentów. Ponieważ po wysłaniu pulsu minutę, różnicy między bieżącym czasem i ostatni rekord pulsu najlepiej będzie zbliżony minuta, jak to możliwe.

Użyj następującego zapytania, aby wyświetlić listę komputerów z opóźnieniem najwyższy.

    Heartbeat 
    | summarize IngestionTime = now() - max(TimeGenerated) by Computer 
    | top 50 by IngestionTime asc

 
Użyj następującej kwerendy w dużych środowiskach podsumowanie opóźnienie dla różnych wartości procentowych łączna liczba komputerów.

    Heartbeat 
    | summarize IngestionTime = now() - max(TimeGenerated) by Computer 
    | summarize percentiles(IngestionTime, 50,95,99)



## <a name="next-steps"></a>Kolejne kroki
* Odczyt [poziomu umowy dotyczącej usług (SLA)](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_1/) usługi Log Analytics.


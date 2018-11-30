---
title: Metryki usługi Azure Relay w usłudze Azure Monitor (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Korzystać z programu Azure Monitoring do monitorowania usługi Azure Relay
services: service-bus-relay
documentationcenter: .NET
author: spelluru
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2018
ms.author: spelluru
ms.openlocfilehash: 5ca4535afb44238380cb6db55953072598130fae
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/29/2018
ms.locfileid: "52582364"
---
# <a name="azure-relay-metrics-in-azure-monitor-preview"></a>Metryki usługi Azure Relay w usłudze Azure Monitor (wersja zapoznawcza)
Metryki usługi Azure Relay zapewniają stan zasobów w ramach subskrypcji platformy Azure. Bogaty zestaw danych metryk możesz ocenić ogólną kondycję zasobów usługi Relay, nie tylko na poziomie przestrzeni nazw, ale na poziomie jednostki. Te statystyki może być ważne, ponieważ one pomóc Ci do monitorowania stanu usługi Azure Relay. Metryki może również pomóc główną przyczynę problemów bez konieczności skontaktuj się z działem pomocy technicznej platformy Azure.

Usługa Azure Monitor zapewnia interfejsy użytkownika ujednolicone monitorowanie z przekraczaniem różne usługi platformy Azure. Aby uzyskać więcej informacji, zobacz [monitorowania na platformie Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) i [metryki pobierania usługi Azure Monitor przy użyciu platformy .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) próbki w witrynie GitHub.

> [!IMPORTANT]
> Ten artykuł dotyczy tylko funkcji połączeń hybrydowych usługi Azure Relay, a nie do przekaźnika WCF. 

## <a name="access-metrics"></a>Dostęp do metryk

Usługa Azure Monitor zapewnia wiele sposobów, aby dostęp do metryk. Możesz albo metryk dostęp za pośrednictwem [witryny Azure portal](https://portal.azure.com), lub użyj rozwiązań analitycznych, takich jak pakietu Operation Management Suite i usługi Event Hubs i interfejsów API usługi Azure Monitor (REST i .NET). Aby uzyskać więcej informacji, zobacz [dane monitorowania zbierane przez usługi Azure Monitor](../azure-monitor/platform/data-collection.md).

Metryki są domyślnie włączone i możesz uzyskać dostęp z ostatnich 30 dni danych. Jeśli zachodzi potrzeba Zachowaj dane przez dłuższy okres czasu, można archiwizować dane metryk do konta usługi Azure Storage. To ustawienie jest konfigurowane w [ustawień diagnostycznych](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#diagnostic-settings) w usłudze Azure Monitor.

## <a name="access-metrics-in-the-portal"></a>Dostęp do metryk w portalu

Możesz monitorować metryki, wraz z upływem czasu w [witryny Azure portal](https://portal.azure.com). Jak wyświetlić żądania zakończone powodzeniem i żądania przychodzące na poziomie konta można znaleźć w poniższym przykładzie:

![][1]

Można również uzyskać dostęp do metryk bezpośrednio za pośrednictwem przestrzeni nazw. Aby to zrobić, wybierz przestrzeń nazw, a następnie kliknij przycisk **metryki (wersja zapoznawcza)**. 

Dla metryk, obsługa wymiarów możesz odfiltrować z żądaną wartością.

## <a name="billing"></a>Rozliczenia

Przy użyciu metryk w usłudze Azure Monitor jest aktualnie opłat w wersji zapoznawczej. Jednak jeśli używasz dodatkowych rozwiązań, które pozyskiwania danych metryk, może zostać naliczona według tych rozwiązań. Na przykład opłaty są naliczane przez usługę Azure Storage archiwizowania danych metryk z kontem usługi Azure Storage. Również są opłaty naliczane przez usługę Log Analytics, jeżeli strumień danych metryk do usługi Log Analytics w celu zaawansowanej analizy.

Następujące metryki umożliwiają przegląd kondycji usługi. 

> [!NOTE]
> Firma Microsoft jest wycofano kilka metryk, ponieważ są one przenoszone pod inną nazwą. Może to wymagać aktualizacji odwołaniami. Oznaczona za pomocą słowa kluczowego "przestarzałe" metryk nie będą obsługiwane przyszłości.

Wszystkie wartości metryk są wysyłane do usługi Azure Monitor na minutę. Stopień szczegółowości czasu określa interwał czasu, dla której są prezentowane wartości metryk. Interwał czasu obsługiwane wszystkie metryki usługi Azure Relay to 1 minuta.

## <a name="connection-metrics"></a>Metryki połączeń

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
| Połączeniach ListenerConnections — Powodzenie (wersja zapoznawcza) | Liczba połączeń odbiornika pomyślne wprowadzone do usługi Azure Relay w określonym czasie. <br/><br/> Jednostka: liczba <br/> Typ agregacji: łączna liczba <br/> Dimension: EntityName|
|Błąd ClientError na połączeniach ListenerConnections (wersja zapoznawcza)|Liczba błędów klienta dla połączeń odbiornika w określonym czasie.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łączna liczba <br/> Dimension: EntityName|
|Błąd ServerError na połączeniach ListenerConnections (wersja zapoznawcza)|Liczba błędów serwera dla połączeń odbiornika w określonym czasie.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łączna liczba <br/> Dimension: EntityName|
|SenderConnections — Powodzenie (wersja zapoznawcza)|Liczba połączeń pomyślne nadawcy w określonym czasie.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łączna liczba <br/> Dimension: EntityName|
|Błąd ClientError na SenderConnections (wersja zapoznawcza)|Liczba błędów klienta dla połączeń nadawcy w określonym czasie.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łączna liczba <br/> Dimension: EntityName|
|Błąd ServerError na SenderConnections (wersja zapoznawcza)|Liczba błędów serwera dla połączeń nadawcy w określonym czasie.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łączna liczba <br/> Dimension: EntityName|
|Połączeniach ListenerConnections-TotalRequests (wersja zapoznawcza)|Całkowita liczba połączeń odbiornika w określonym czasie.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łączna liczba <br/> Dimension: EntityName|
|SenderConnections-TotalRequests (wersja zapoznawcza)|Żądania połączenia wykonywane przez nadawców w określonym czasie.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łączna liczba <br/> Dimension: EntityName|
|Połączeń ActiveConnections (wersja zapoznawcza)|Liczba aktywnych połączeń w określonym czasie.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łączna liczba <br/> Dimension: EntityName|
|ActiveListeners (wersja zapoznawcza)|Liczba aktywne odbiorniki w określonym czasie.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łączna liczba <br/> Dimension: EntityName|
|Rozłączeń ListenerDisconnects (wersja zapoznawcza)|Liczba odłączonego odbiorników w określonym czasie.<br/><br/> Jednostka: bajty <br/> Typ agregacji: łączna liczba <br/> Dimension: EntityName|
|Rozłączeń SenderDisconnects (wersja zapoznawcza)|Liczba odłączonego nadawców w określonym czasie.<br/><br/> Jednostka: bajty <br/> Typ agregacji: łączna liczba <br/> Dimension: EntityName|

## <a name="memory-usage-metrics"></a>Metryki użycia pamięci

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
|Bajtów BytesTransferred (wersja zapoznawcza)|Liczba bajtów przesłanych w określonym czasie.<br/><br/> Jednostka: bajty <br/> Typ agregacji: łączna liczba <br/> Dimension: EntityName|

## <a name="metrics-dimensions"></a>Wymiary metryki

Usługa Azure Relay obsługuje następujące wymiary metryk w usłudze Azure Monitor. Dodawanie wymiarów do metryk jest opcjonalne. Jeśli nie dodasz wymiarów, metryki są określone na poziomie przestrzeni nazw. 

|Nazwa wymiaru|Opis|
| ------------------- | ----------------- |
|EntityName| Usługa Azure Relay obsługuje jednostek obsługi komunikatów w przestrzeni nazw.|

## <a name="next-steps"></a>Kolejne kroki

Zobacz [monitorowania platformy Azure — omówienie](../monitoring-and-diagnostics/monitoring-overview.md).

[1]: ./media/relay-metrics-azure-monitor/relay-monitor1.png





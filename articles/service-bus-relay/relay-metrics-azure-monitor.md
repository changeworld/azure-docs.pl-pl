---
title: Azure metryki przekazywania w monitorze Azure (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Użyj monitorowania Azure, aby monitorować przekaźnika usługi Azure
services: service-bus-relay
documentationcenter: .NET
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: sethm
ms.openlocfilehash: b919f1fb0799ec497742115b2611f34872af2027
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="azure-relay-metrics-in-azure-monitor-preview"></a>Azure metryki przekazywania w monitorze Azure (wersja zapoznawcza)

Azure metryki przekazywania zapewnia stanu zasobów w Twojej subskrypcji platformy Azure. Ze bogatym zestawem danych metryki można ocenić ogólną kondycję zasobów przekazywania, nie tylko na poziomie przestrzeni nazw, ale na poziomie jednostki. Te statystyki może być istotne, ponieważ ich ułatwiają monitorowanie stanu przekaźnika usługi Azure. Metryki może również pomóc rozwiązać głównej przyczyny problemów bez konieczności skontaktuj się z pomocą techniczną platformy Azure.

Azure Monitor udostępnia interfejsy użytkownika ujednoliconego do monitorowania na różne usługi platformy Azure. Aby uzyskać więcej informacji, zobacz [monitorowania na platformie Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) i [metryki pobrać Monitor Azure z platformą .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) w witrynie GitHub.

## <a name="access-metrics"></a>Metryki dostępu

Azure Monitor oferuje wiele sposobów metryki dostępu. Można albo metryki dostęp za pośrednictwem [portalu Azure](https://portal.azure.com), lub użyj interfejsów API usługi Azure monitora (REST i .NET) i rozwiązań analitycznych, takie jak operacji Management Suite i usługi Event Hubs. Aby uzyskać więcej informacji, zobacz [metryki Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-metrics.md#access-metrics-via-the-rest-api).

Metryki są domyślnie włączone i są dostępne najnowsze w ciągu 30 dni od danych. Jeśli chcesz zachować dane przez dłuższy okres czasu, można archiwizować dane metryk do konta usługi Azure Storage. Te ustawienia zostaną skonfigurowane w [ustawień diagnostycznych](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings) w monitorze Azure.

## <a name="access-metrics-in-the-portal"></a>Metryki dostępu w portalu

Można monitorować metryk w czasie, [portalu Azure](https://portal.azure.com). Poniższy przykład przedstawia sposób wyświetlania pomyślnych żądań i żądań przychodzących na poziomie konta:

![][1]

Dostępne metryki bezpośrednio za pomocą przestrzeni nazw. Aby to zrobić, wybierz obszar nazw, a następnie kliknij przycisk **metryki (Peview)**. 

Dla metryki pomocnicze, wymiary możesz filtrować z żądaną wartością.

## <a name="billing"></a>Rozliczenia

Przy użyciu metryk w monitorze Azure jest obecnie bezpłatna podczas w wersji zapoznawczej. Jednak użycie dodatkowe rozwiązania, które pozyskiwania danych metryki, użytkownik może zostać użyta przez te rozwiązania. Na przykład możesz są rozliczane przez usługi Azure Storage archiwizacji danych metryk do konta usługi Azure Storage. Można również są rozliczane według analizy dzienników Jeśli strumienia danych metryk do analizy dzienników dla zaawansowanej analizy.

Następujące metryki zawiera przegląd kondycji usługi. 

> [!NOTE]
> Firma Microsoft są wycofano kilka miar, ponieważ są one przenoszone pod inną nazwą. To może wymagać aktualizacji z odwołania. Metryki oznaczonej jako "przestarzałe" — słowo kluczowe nie będą obsługiwane idąc dalej.

Wszystkie wartości metryki są wysyłane do monitora Azure co minutę. Odstęp czasu określa przedział czasu, dla której przedstawiono wartości metryk. Obsługiwany interwał wszystkie metryki przekazywania Azure to 1 minuta.

## <a name="connection-metrics"></a>Metryki połączenia

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
| ListenerConnections — Powodzenie (wersja zapoznawcza) | Liczba pomyślnie odbiornika połączeń przekazywania Azure w określonym czasie. <br/><br/> Jednostka: liczba <br/> Typ agregacji: Całkowita liczba <br/> Dimension: EntityName|
|ListenerConnections-ClientError (wersja zapoznawcza)|Liczba klientów z błędami połączeń odbiornika w określonym czasie.<br/><br/> Jednostka: liczba <br/> Typ agregacji: Całkowita liczba <br/> Dimension: EntityName|
|ListenerConnections-ServerError (wersja zapoznawcza)|Liczba błędów serwera dla połączeń odbiornika w określonym czasie.<br/><br/> Jednostka: liczba <br/> Typ agregacji: Całkowita liczba <br/> Dimension: EntityName|
|SenderConnections — Powodzenie (wersja zapoznawcza)|Liczba połączeń pomyślne nadawcy w określonym czasie.<br/><br/> Jednostka: liczba <br/> Typ agregacji: Całkowita liczba <br/> Dimension: EntityName|
|SenderConnections-ClientError (wersja zapoznawcza)|Liczba klientów z błędami połączeń nadawcy w określonym czasie.<br/><br/> Jednostka: liczba <br/> Typ agregacji: Całkowita liczba <br/> Dimension: EntityName|
|SenderConnections-ServerError (wersja zapoznawcza)|Liczba błędów serwera w przypadku połączeń nadawcy w określonym czasie.<br/><br/> Jednostka: liczba <br/> Typ agregacji: Całkowita liczba <br/> Dimension: EntityName|
|ListenerConnections-TotalRequests (wersja zapoznawcza)|Całkowita liczba połączeń odbiornika w określonym czasie.<br/><br/> Jednostka: liczba <br/> Typ agregacji: Całkowita liczba <br/> Dimension: EntityName|
|SenderConnections-TotalRequests (wersja zapoznawcza)|Żądania połączeń wykonywanych przez nadawców w w określonym przedziale czasu.<br/><br/> Jednostka: liczba <br/> Typ agregacji: Całkowita liczba <br/> Dimension: EntityName|
|ActiveConnections (wersja zapoznawcza)|Liczba aktywnych połączeń w określonym czasie.<br/><br/> Jednostka: liczba <br/> Typ agregacji: Całkowita liczba <br/> Dimension: EntityName|
|ActiveListeners (wersja zapoznawcza)|Liczba active odbiorników w określonym czasie.<br/><br/> Jednostka: liczba <br/> Typ agregacji: Całkowita liczba <br/> Dimension: EntityName|
|ListenerDisconnects (wersja zapoznawcza)|Liczba odłączonego odbiorników w określonym czasie.<br/><br/> Jednostka: bajtów <br/> Typ agregacji: Całkowita liczba <br/> Dimension: EntityName|
|SenderDisconnects (wersja zapoznawcza)|Liczba odłączonego nadawców w określonym czasie.<br/><br/> Jednostka: bajtów <br/> Typ agregacji: Całkowita liczba <br/> Dimension: EntityName|

## <a name="memory-usage-metrics"></a>Metryki użycia pamięci

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
|BytesTransferred (wersja zapoznawcza)|Liczba bajtów przesłanych w określonym czasie.<br/><br/> Jednostka: bajtów <br/> Typ agregacji: Całkowita liczba <br/> Dimension: EntityName|

## <a name="metrics-dimensions"></a>Wymiary metryk

Przekaźnik Azure obsługuje następujące wymiary metryki w monitorze Azure. Dodawanie wymiarów do Twojej metryki jest opcjonalna. Jeśli nie zostaną dodane wymiarów, metryki są określone na poziomie przestrzeni nazw. 

|Nazwa wymiaru|Opis|
| ------------------- | ----------------- |
|EntityName| Azure przekaźnika obsługuje jednostek obsługi komunikatów w przestrzeni nazw.|

## <a name="next-steps"></a>Kolejne kroki

Zobacz [Azure monitorowanie — Przegląd](../monitoring-and-diagnostics/monitoring-overview.md).

[1]: ./media/relay-metrics-azure-monitor/relay-monitor1.png





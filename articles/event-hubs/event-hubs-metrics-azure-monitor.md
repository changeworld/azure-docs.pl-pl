---
title: Metryki w Azure Monitor — Event Hubs platformy Azure | Microsoft Docs
description: Ten artykuł zawiera informacje dotyczące sposobu używania funkcji monitorowania platformy Azure do monitorowania usługi Azure Event Hubs
services: event-hubs
documentationcenter: .NET
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 99b3b4b8d48ff04fc2ced686c01b2d4de12c6555
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2019
ms.locfileid: "68742131"
---
# <a name="azure-event-hubs-metrics-in-azure-monitor"></a>Metryki usługi Azure Event Hubs w Azure Monitor

Event Hubs metryki zapewnia stan zasoby usługi Event Hubs w ramach subskrypcji platformy Azure. Bogaty zestaw danych metryk możesz ocenić ogólną kondycję usługi event hubs, nie tylko na poziomie przestrzeni nazw, ale na poziomie jednostki. Te statystyki może być ważne, ponieważ one pomóc Ci do monitorowania stanu usługi event hubs. Metryki może również pomóc główną przyczynę problemów bez konieczności skontaktuj się z działem pomocy technicznej platformy Azure.

Usługa Azure Monitor zapewnia interfejsy użytkownika ujednolicone monitorowanie z przekraczaniem różne usługi platformy Azure. Aby uzyskać więcej informacji, zobacz [monitorowania na platformie Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) i [metryki pobierania usługi Azure Monitor przy użyciu platformy .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) próbki w witrynie GitHub.

## <a name="access-metrics"></a>Dostęp do metryk

Usługa Azure Monitor zapewnia wiele sposobów, aby dostęp do metryk. Możesz uzyskać dostęp do metryk przy użyciu [Azure Portal](https://portal.azure.com)lub użyć interfejsów API Azure monitor (REST i .NET) oraz rozwiązań do analizy, takich jak Log Analytics i Event Hubs. Aby uzyskać więcej informacji, zobacz [dane monitorowania zbierane przez usługi Azure Monitor](../azure-monitor/platform/data-platform.md).

Metryki są domyślnie włączone i możesz uzyskać dostęp z ostatnich 30 dni danych. Jeśli zachodzi potrzeba Zachowaj dane przez dłuższy okres czasu, można archiwizować dane metryk do konta usługi Azure Storage. To ustawienie jest konfigurowane w [ustawień diagnostycznych](../azure-monitor/platform/diagnostic-logs-overview.md#diagnostic-settings) w usłudze Azure Monitor.

## <a name="access-metrics-in-the-portal"></a>Dostęp do metryk w portalu

Możesz monitorować metryki, wraz z upływem czasu w [witryny Azure portal](https://portal.azure.com). Jak wyświetlić żądania zakończone powodzeniem i żądania przychodzące na poziomie konta można znaleźć w poniższym przykładzie:

![Wyświetl metryki pomyślne][1]

Można również uzyskać dostęp do metryk bezpośrednio za pośrednictwem przestrzeni nazw. Aby to zrobić, wybierz przestrzeń nazw, a następniekliknij pozycję metryki. Aby wyświetlić metryki odfiltrowane do zakresu centrum zdarzeń, wybierz centrum zdarzeń, a następnie kliknij pozycję **metryki**.

Dla metryk, obsługa wymiarów możesz filtrować z żądaną wartością jak pokazano w poniższym przykładzie:

![Filtrowanie za pomocą wartości wymiaru][2]

## <a name="billing"></a>Rozliczenia

Korzystanie z metryk w Azure Monitor jest obecnie bezpłatne. Jednak jeśli używasz dodatkowych rozwiązań, które pozyskiwania danych metryk, może zostać naliczona według tych rozwiązań. Na przykład opłaty są naliczane przez usługę Azure Storage archiwizowania danych metryk z kontem usługi Azure Storage. Opłaty są naliczane także przez platformę Azure, jeśli dane metryk są przesyłane strumieniowo do dzienników Azure Monitor w celu przeprowadzenia zaawansowanej analizy.

Następujące metryki umożliwiają przegląd kondycji usługi. 

> [!NOTE]
> Firma Microsoft jest wycofano kilka metryk, ponieważ są one przenoszone pod inną nazwą. Może to wymagać aktualizacji odwołaniami. Oznaczona za pomocą słowa kluczowego "przestarzałe" metryk nie będą obsługiwane przyszłości.

Wszystkie wartości metryk są wysyłane do usługi Azure Monitor na minutę. Stopień szczegółowości czasu określa interwał czasu, dla której są prezentowane wartości metryk. Interwał czasu obsługiwane wszystkie metryki usługi Event Hubs to 1 minuta.

## <a name="request-metrics"></a>Metryki żądania

Zlicza żądania operacji danych i zarządzania.

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
| Żądania przychodzące  | Liczba żądań kierowanych do usługi Azure Event Hubs w określonym czasie. <br/><br/> Jednostka Count <br/> Typ agregacji: Łącznie <br/> Elementów EntityName |
| Żądania zakończone powodzeniem    | Liczba żądań zakończonych powodzeniem kierowanych do usługi Azure Event Hubs w określonym czasie. <br/><br/> Jednostka Count <br/> Typ agregacji: Łącznie <br/> Elementów EntityName |
| Błędy serwera  | Liczba żądań, które nie zostały przetworzone z powodu błędu w usłudze Azure Event Hubs w określonym czasie. <br/><br/>Jednostka Count <br/> Typ agregacji: Łącznie <br/> Elementów EntityName |
|Błędy użytkownika |Liczba żądań, które nie zostały przetworzone z powodu błędów użytkowników w określonym czasie.<br/><br/> Jednostka Count <br/> Typ agregacji: Łącznie <br/> Elementów EntityName|
|Błędy przekroczenia limitu przydziału |Liczba żądań przekroczyła dostępny limit przydziału. Zobacz [w tym artykule](event-hubs-quotas.md) Aby uzyskać więcej informacji na temat przydziałów usługi Event Hubs.<br/><br/> Jednostka Count <br/> Typ agregacji: Łącznie <br/> Elementów EntityName|

## <a name="throughput-metrics"></a>Dane pomiarowe przepływności

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
|Ograniczone żądania |Liczba żądań, które zostały ograniczone, ponieważ przekroczono użycie jednostek przepływności.<br/><br/> Jednostka Count <br/> Typ agregacji: Łącznie <br/> Elementów EntityName|

## <a name="message-metrics"></a>Metryki wiadomości

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
|Wiadomości przychodzące |Liczba zdarzeń lub komunikaty wysyłane do usługi Event Hubs w określonym czasie.<br/><br/> Jednostka Count <br/> Typ agregacji: Łącznie <br/> Elementów EntityName|
|Wiadomości wychodzące |Liczba zdarzeń lub komunikatów są pobierane z usługi Event Hubs w określonym czasie.<br/><br/> Jednostka Count <br/> Typ agregacji: Łącznie <br/> Elementów EntityName|
|Przychodzące bajty |Liczba bajtów wysłanych do usługi Azure Event Hubs w określonym czasie.<br/><br/> Jednostka Bajty <br/> Typ agregacji: Łącznie <br/> Elementów EntityName|
|Bajty wychodzące |Liczba bajtów jest pobierane z usługi Azure Event Hubs w określonym czasie.<br/><br/> Jednostka Bajty <br/> Typ agregacji: Łącznie <br/> Elementów EntityName|

## <a name="connection-metrics"></a>Metryki połączeń

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
|ActiveConnections |Liczba aktywnych połączeń w przestrzeni nazw, a także w jednostce.<br/><br/> Jednostka Count <br/> Typ agregacji: Łącznie <br/> Elementów EntityName|
|Otwarte połączenia |Liczba otwartych połączeń.<br/><br/> Jednostka Count <br/> Typ agregacji: Łącznie <br/> Elementów EntityName|
|Połączenia zamknięte |Liczba zamkniętych połączeń.<br/><br/> Jednostka Count <br/> Typ agregacji: Łącznie <br/> Elementów EntityName|

## <a name="event-hubs-capture-metrics"></a>Event Hubs Capture metryki

Możesz monitorować metryki usługi Event Hubs Capture, gdy można włączyć funkcję przechwytywania usługi event hubs. Następujące metryki opisują, jakie można monitorować za pomocą włączono funkcję przechwytywania.

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
|Zaległości przechwytywania |Liczba bajtów, które mają dopiero zostać przechwycone do wybranego miejsca docelowego.<br/><br/> Jednostka Bajty <br/> Typ agregacji: Łącznie <br/> Elementów EntityName|
|Przechwycone komunikaty |Liczba komunikatów lub zdarzeń, które są przechwytywane do wybranego miejsca docelowego w określonym czasie.<br/><br/> Jednostka Count <br/> Typ agregacji: Łącznie <br/> Elementów EntityName|
|Przechwycone bajty |Liczba bajtów, które są przechwytywane do wybranego miejsca docelowego w określonym czasie.<br/><br/> Jednostka Bajty <br/> Typ agregacji: Łącznie <br/> Elementów EntityName|

## <a name="metrics-dimensions"></a>Wymiary metryki

Usługa Azure Event Hubs obsługuje następujące wymiary metryk w usłudze Azure Monitor. Dodawanie wymiarów do metryk jest opcjonalne. Jeśli nie dodasz wymiarów, metryki są określone na poziomie przestrzeni nazw. 

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
|EntityName| Usługa Event Hubs obsługuje jednostek Centrum zdarzeń w przestrzeni nazw.|

## <a name="next-steps"></a>Kolejne kroki

* Zobacz [monitorowania platformy Azure — omówienie](../monitoring-and-diagnostics/monitoring-overview.md).
* [Pobieranie metryk usługi Azure Monitor przy użyciu platformy .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) próbki w witrynie GitHub. 

Aby uzyskać więcej informacji na temat usługi Event Hubs, skorzystaj z następujących linków:

* Rozpocznij pracę od [samouczka dotyczącego usługi Event Hubs](event-hubs-dotnet-standard-getstarted-send.md)
* [Event Hubs — często zadawane pytania](event-hubs-faq.md)
* [Przykładowe aplikacje korzystające z usługi Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor1.png
[2]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor2.png




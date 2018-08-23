---
title: Metryki usługi Azure Event Hubs w usłudze Azure Monitor (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Korzystać z programu Azure Monitoring do monitorowania usługi Event Hubs
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
ms.date: 08/16/2018
ms.author: shvija
ms.openlocfilehash: e65fc329af0af585caf20c26bd2ef79a1f39d501
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/17/2018
ms.locfileid: "42054811"
---
# <a name="azure-event-hubs-metrics-in-azure-monitor-preview"></a>Metryki usługi Azure Event Hubs w usłudze Azure Monitor (wersja zapoznawcza)

Event Hubs metryki zapewnia stan zasoby usługi Event Hubs w ramach subskrypcji platformy Azure. Bogaty zestaw danych metryk możesz ocenić ogólną kondycję usługi event hubs, nie tylko na poziomie przestrzeni nazw, ale na poziomie jednostki. Te statystyki może być ważne, ponieważ one pomóc Ci do monitorowania stanu usługi event hubs. Metryki może również pomóc główną przyczynę problemów bez konieczności skontaktuj się z działem pomocy technicznej platformy Azure.

Usługa Azure Monitor zapewnia interfejsy użytkownika ujednolicone monitorowanie z przekraczaniem różne usługi platformy Azure. Aby uzyskać więcej informacji, zobacz [monitorowania na platformie Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) i [metryki pobierania usługi Azure Monitor przy użyciu platformy .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) próbki w witrynie GitHub.

## <a name="access-metrics"></a>Dostęp do metryk

Usługa Azure Monitor zapewnia wiele sposobów, aby dostęp do metryk. Możesz albo metryk dostęp za pośrednictwem [witryny Azure portal](https://portal.azure.com), lub użyj rozwiązań analitycznych, takich jak pakietu Operation Management Suite i usługi Event Hubs i interfejsów API usługi Azure Monitor (REST i .NET). Aby uzyskać więcej informacji, zobacz [metryk usługi Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-metrics.md#access-metrics-via-the-rest-api).

Metryki są domyślnie włączone i możesz uzyskać dostęp z ostatnich 30 dni danych. Jeśli zachodzi potrzeba Zachowaj dane przez dłuższy okres czasu, można archiwizować dane metryk do konta usługi Azure Storage. To ustawienie jest konfigurowane w [ustawień diagnostycznych](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#diagnostic-settings) w usłudze Azure Monitor.

## <a name="access-metrics-in-the-portal"></a>Dostęp do metryk w portalu

Możesz monitorować metryki, wraz z upływem czasu w [witryny Azure portal](https://portal.azure.com). Jak wyświetlić żądania zakończone powodzeniem i żądania przychodzące na poziomie konta można znaleźć w poniższym przykładzie:

![][1]

Można również uzyskać dostęp do metryk bezpośrednio za pośrednictwem przestrzeni nazw. Aby to zrobić, wybierz przestrzeń nazw, a następnie kliknij przycisk **metryki (Peview)**. Aby wyświetlić metryki filtrowane do zakresu Centrum zdarzeń, wybierz Centrum zdarzeń, a następnie kliknij przycisk **metryki (wersja zapoznawcza)**.

Dla metryk, obsługa wymiarów możesz filtrować z żądaną wartością jak pokazano w poniższym przykładzie:

![][2]

## <a name="billing"></a>Rozliczenia

Przy użyciu metryk w usłudze Azure Monitor jest aktualnie opłat w wersji zapoznawczej. Jednak jeśli używasz dodatkowych rozwiązań, które pozyskiwania danych metryk, może zostać naliczona według tych rozwiązań. Na przykład opłaty są naliczane przez usługę Azure Storage archiwizowania danych metryk z kontem usługi Azure Storage. Są również rozliczane przez platformę Azure w przypadku strumienia danych metryk do usługi Log Analytics w celu zaawansowanej analizy.

Następujące metryki umożliwiają przegląd kondycji usługi. 

> [!NOTE]
> Firma Microsoft jest wycofano kilka metryk, ponieważ są one przenoszone pod inną nazwą. Może to wymagać aktualizacji odwołaniami. Oznaczona za pomocą słowa kluczowego "przestarzałe" metryk nie będą obsługiwane przyszłości.

Wszystkie wartości metryk są wysyłane do usługi Azure Monitor na minutę. Stopień szczegółowości czasu określa interwał czasu, dla której są prezentowane wartości metryk. Interwał czasu obsługiwane wszystkie metryki usługi Event Hubs to 1 minuta.

## <a name="request-metrics"></a>Metryki żądania

Zlicza żądania operacji danych i zarządzania.

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
| Przychodzące żądania (wersja zapoznawcza) | Liczba żądań kierowanych do usługi Azure Event Hubs w określonym czasie. <br/><br/> Jednostka: liczba <br/> Typ agregacji: łączna liczba <br/> Dimension: EntityName |
| Liczba pomyślnych żądań (wersja zapoznawcza)   | Liczba żądań zakończonych powodzeniem kierowanych do usługi Azure Event Hubs w określonym czasie. <br/><br/> Jednostka: liczba <br/> Typ agregacji: łączna liczba <br/> Dimension: EntityName |
| Błędy serwera (wersja zapoznawcza) | Liczba żądań, które nie zostały przetworzone z powodu błędu w usłudze Azure Event Hubs w określonym czasie. <br/><br/>Jednostka: liczba <br/> Typ agregacji: łączna liczba <br/> Dimension: EntityName |
|Błędy użytkowników (wersja zapoznawcza)|Liczba żądań, które nie zostały przetworzone z powodu błędów użytkowników w określonym czasie.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łączna liczba <br/> Dimension: EntityName|
|Żądania ograniczone (wersja zapoznawcza)|Liczba żądań, które zostały ograniczone, ponieważ przekroczono użycie jednostek przepływności.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łączna liczba <br/> Dimension: EntityName|
|Błędy przekroczenia limitu przydziału (wersja zapoznawcza)|Liczba żądań przekroczyła dostępny limit przydziału. Zobacz [w tym artykule](event-hubs-quotas.md) Aby uzyskać więcej informacji na temat przydziałów usługi Event Hubs.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łączna liczba <br/> Dimension: EntityName|

## <a name="throughput-metrics"></a>Dane pomiarowe przepływności

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
|Żądania ograniczone (wersja zapoznawcza)|Liczba żądań, które zostały ograniczone, ponieważ przekroczono użycie jednostek przepływności.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łączna liczba <br/> Dimension: EntityName|

## <a name="message-metrics"></a>Metryki wiadomości

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
|Komunikaty przychodzące (wersja zapoznawcza)|Liczba zdarzeń lub komunikaty wysyłane do usługi Event Hubs w określonym czasie.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łączna liczba <br/> Dimension: EntityName|
|Komunikaty wychodzące (wersja zapoznawcza)|Liczba zdarzeń lub komunikatów są pobierane z usługi Event Hubs w określonym czasie.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łączna liczba <br/> Dimension: EntityName|
|Przychodzące bajty (wersja zapoznawcza)|Liczba bajtów wysłanych do usługi Azure Event Hubs w określonym czasie.<br/><br/> Jednostka: bajty <br/> Typ agregacji: łączna liczba <br/> Dimension: EntityName|
|Bajty wychodzące (wersja zapoznawcza)|Liczba bajtów jest pobierane z usługi Azure Event Hubs w określonym czasie.<br/><br/> Jednostka: bajty <br/> Typ agregacji: łączna liczba <br/> Dimension: EntityName|

## <a name="connection-metrics"></a>Metryki połączeń

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
|Połączeń ActiveConnections (wersja zapoznawcza)|Liczba aktywnych połączeń w przestrzeni nazw, a także w jednostce.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łączna liczba <br/> Dimension: EntityName|
|Otwartego połączenia (wersja zapoznawcza)|Liczba otwartych połączeń.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łączna liczba <br/> Dimension: EntityName|
|Połączenia zamknięte (wersja zapoznawcza)|Liczba zamkniętych połączeń.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łączna liczba <br/> Dimension: EntityName|

## <a name="event-hubs-capture-metrics"></a>Event Hubs Capture metryki

Możesz monitorować metryki usługi Event Hubs Capture, gdy można włączyć funkcję przechwytywania usługi event hubs. Następujące metryki opisują, jakie można monitorować za pomocą włączono funkcję przechwytywania.

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
|(Wersja zapoznawcza) lista prac funkcji Capture|Liczba bajtów, które mają dopiero zostać przechwycone do wybranego miejsca docelowego.<br/><br/> Jednostka: bajty <br/> Typ agregacji: łączna liczba <br/> Dimension: EntityName|
|Przechwycone komunikaty (wersja zapoznawcza)|Liczba komunikatów lub zdarzeń, które są przechwytywane do wybranego miejsca docelowego w określonym czasie.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łączna liczba <br/> Dimension: EntityName|
|Przechwycone bajty (wersja zapoznawcza)|Liczba bajtów, które są przechwytywane do wybranego miejsca docelowego w określonym czasie.<br/><br/> Jednostka: bajty <br/> Typ agregacji: łączna liczba <br/> Dimension: EntityName|

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




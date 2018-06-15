---
title: Azure Event Hubs metryki w monitorze Azure (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Użyj monitorowania Azure do monitorowania usługi Event Hubs
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
ms.date: 04/12/2018
ms.author: sethm
ms.openlocfilehash: 445b439ff77a88a4b7783427f5fab0e40a485542
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31405144"
---
# <a name="azure-event-hubs-metrics-in-azure-monitor-preview"></a>Azure Event Hubs metryki w monitorze Azure (wersja zapoznawcza)

Metryki centra zdarzeń umożliwia stanu zasobów usługi Event Hubs w Twojej subskrypcji platformy Azure. Ze bogatym zestawem danych metryki można ocenić ogólną kondycję event hubs nie tylko na poziomie przestrzeni nazw, ale na poziomie jednostki. Te statystyki może być istotne, ponieważ ich ułatwiają monitorowanie stanu centrów zdarzeń. Metryki może również pomóc rozwiązać głównej przyczyny problemów bez konieczności skontaktuj się z pomocą techniczną platformy Azure.

Azure Monitor udostępnia interfejsy użytkownika ujednoliconego do monitorowania na różne usługi platformy Azure. Aby uzyskać więcej informacji, zobacz [monitorowania na platformie Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) i [metryki pobrać Monitor Azure z platformą .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) w witrynie GitHub.

## <a name="access-metrics"></a>Metryki dostępu

Azure Monitor oferuje wiele sposobów metryki dostępu. Można albo metryki dostęp za pośrednictwem [portalu Azure](https://portal.azure.com), lub użyj interfejsów API usługi Azure monitora (REST i .NET) i rozwiązań analitycznych, takie jak operacji Management Suite i usługi Event Hubs. Aby uzyskać więcej informacji, zobacz [metryki Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-metrics.md#access-metrics-via-the-rest-api).

Metryki są domyślnie włączone i są dostępne najnowsze w ciągu 30 dni od danych. Jeśli chcesz zachować dane przez dłuższy okres czasu, można archiwizować dane metryk do konta usługi Azure Storage. Te ustawienia zostaną skonfigurowane w [ustawień diagnostycznych](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings) w monitorze Azure.

## <a name="access-metrics-in-the-portal"></a>Metryki dostępu w portalu

Można monitorować metryk w czasie, [portalu Azure](https://portal.azure.com). Poniższy przykład przedstawia sposób wyświetlania pomyślnych żądań i żądań przychodzących na poziomie konta:

![][1]

Dostępne metryki bezpośrednio za pomocą przestrzeni nazw. Aby to zrobić, wybierz obszar nazw, a następnie kliknij przycisk **metryki (Peview)**. Aby wyświetlić metryki filtrowane do zakresu Centrum zdarzeń, wybierz Centrum zdarzeń, a następnie kliknij przycisk **metryki (wersja zapoznawcza)**.

Dla metryki pomocnicze, wymiary musi filtru o wartości wymiaru, jak pokazano w poniższym przykładzie:

![][2]

## <a name="billing"></a>Rozliczenia

Przy użyciu metryk w monitorze Azure jest obecnie bezpłatna podczas w wersji zapoznawczej. Jednak użycie dodatkowe rozwiązania, które pozyskiwania danych metryki, użytkownik może zostać użyta przez te rozwiązania. Na przykład możesz są rozliczane przez usługi Azure Storage archiwizacji danych metryk do konta usługi Azure Storage. Możesz również są rozliczane przez platformę Azure w przypadku strumienia danych metryk do analizy dzienników dla zaawansowanej analizy.

Następujące metryki zawiera przegląd kondycji usługi. 

> [!NOTE]
> Firma Microsoft są wycofano kilka miar, ponieważ są one przenoszone pod inną nazwą. To może wymagać aktualizacji z odwołania. Metryki oznaczonej jako "przestarzałe" — słowo kluczowe nie będą obsługiwane idąc dalej.

Wszystkie wartości metryki są wysyłane do monitora Azure co minutę. Odstęp czasu określa przedział czasu, dla której przedstawiono wartości metryk. Obsługiwany interwał wszystkie metryki usługi Event Hubs to 1 minuta.

## <a name="request-metrics"></a>Metryki żądania

Zlicza żądania operacji danych i zarządzania.

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
| Przychodzące żądania (wersja zapoznawcza) | Liczba żądań wysyłanych do usługi Azure Event Hubs w określonym czasie. <br/><br/> Jednostka: liczba <br/> Typ agregacji: Całkowita liczba <br/> Dimension: EntityName |
| Liczba pomyślnych żądań (wersja zapoznawcza)   | Liczba pomyślnych żądań wprowadzone w usłudze Azure Event Hubs w określonym czasie. <br/><br/> Jednostka: liczba <br/> Typ agregacji: Całkowita liczba <br/> Dimension: EntityName |
| Błędy serwera (wersja zapoznawcza) | Liczba żądań przetworzonych nie ze względu na błąd w usłudze Azure Event Hubs w określonym czasie. <br/><br/>Jednostka: liczba <br/> Typ agregacji: Całkowita liczba <br/> Dimension: EntityName |
|Błędy użytkownika (wersja zapoznawcza)|Liczba żądań przetworzonych nie ze względu na błędy użytkowników w określonym czasie.<br/><br/> Jednostka: liczba <br/> Typ agregacji: Całkowita liczba <br/> Dimension: EntityName|
|Ograniczeniem przepustowości żądań (wersja zapoznawcza)|Liczba żądań, które zostały ograniczenie, ponieważ przekroczono użycia jednostek przepływności.<br/><br/> Jednostka: liczba <br/> Typ agregacji: Całkowita liczba <br/> Dimension: EntityName|
|Błędy przekroczyła przydział (wersja zapoznawcza)|Liczba żądań przekracza dostępny przydział. Zobacz [w tym artykule](event-hubs-quotas.md) uzyskać więcej informacji na temat usługi Event Hubs przydziałów.<br/><br/> Jednostka: liczba <br/> Typ agregacji: Całkowita liczba <br/> Dimension: EntityName|

## <a name="throughput-metrics"></a>Dane pomiarowe przepływności

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
|Ograniczeniem przepustowości żądań (wersja zapoznawcza)|Liczba żądań, które zostały ograniczenie, ponieważ przekroczono użycia jednostek przepływności.<br/><br/> Jednostka: liczba <br/> Typ agregacji: Całkowita liczba <br/> Dimension: EntityName|

## <a name="message-metrics"></a>Metryki wiadomości

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
|Komunikaty przychodzące (wersja zapoznawcza)|Liczba zdarzeń lub komunikaty wysyłane do usługi Event Hubs w określonym czasie.<br/><br/> Jednostka: liczba <br/> Typ agregacji: Całkowita liczba <br/> Dimension: EntityName|
|Wysyła komunikaty wychodzące (wersja zapoznawcza)|Liczba zdarzeń lub wiadomości pobierane z usługi Event Hubs w określonym czasie.<br/><br/> Jednostka: liczba <br/> Typ agregacji: Całkowita liczba <br/> Dimension: EntityName|
|Przychodzące bajty (wersja zapoznawcza)|Liczba bajtów wysłanych z usługą Azure Event Hubs w określonym czasie.<br/><br/> Jednostka: bajtów <br/> Typ agregacji: Całkowita liczba <br/> Dimension: EntityName|
|Wychodzące bajty (wersja zapoznawcza)|Liczba bajtów pobrany z usługi Azure Event Hubs w określonym czasie.<br/><br/> Jednostka: bajtów <br/> Typ agregacji: Całkowita liczba <br/> Dimension: EntityName|

## <a name="connection-metrics"></a>Metryki połączenia

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
|ActiveConnections (wersja zapoznawcza)|Liczba aktywnych połączeń przestrzeni nazw, a także jednostki.<br/><br/> Jednostka: liczba <br/> Typ agregacji: Całkowita liczba <br/> Dimension: EntityName|
|Otwartego połączenia (wersja zapoznawcza)|Liczba otwartych połączeń.<br/><br/> Jednostka: liczba <br/> Typ agregacji: Całkowita liczba <br/> Dimension: EntityName|
|Zamknięte połączenia (wersja zapoznawcza)|Liczba połączeń zamknięte.<br/><br/> Jednostka: liczba <br/> Typ agregacji: Całkowita liczba <br/> Dimension: EntityName|

## <a name="event-hubs-capture-metrics"></a>Metryki przechwytywania centra zdarzeń

Można monitorować metryki przechwytywania centra zdarzeń, po włączeniu funkcji przechwytywania dla centrów zdarzeń. Następujące metryki opisują, jakie można monitorować z włączonym.

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
|Przechwyć zaległości (wersja zapoznawcza)|Liczba bajtów, które jeszcze mają być przechwytywane do wybranej lokalizacji docelowej.<br/><br/> Jednostka: bajtów <br/> Typ agregacji: Całkowita liczba <br/> Dimension: EntityName|
|Przechwycony wiadomości (wersja zapoznawcza)|Liczba komunikatów lub zdarzeń, które są przechwytywane do wybranej lokalizacji docelowej w określonym czasie.<br/><br/> Jednostka: liczba <br/> Typ agregacji: Całkowita liczba <br/> Dimension: EntityName|
|Przechwycony bajtów (wersja zapoznawcza)|Liczba bajtów, które są przechwytywane do wybranej lokalizacji docelowej w określonym czasie.<br/><br/> Jednostka: bajtów <br/> Typ agregacji: Całkowita liczba <br/> Dimension: EntityName|

## <a name="metrics-dimensions"></a>Wymiary metryk

Usługa Azure Event Hubs obsługuje następujące wymiary metryki w monitorze Azure. Dodawanie wymiarów do Twojej metryki jest opcjonalna. Jeśli nie zostaną dodane wymiarów, metryki są określone na poziomie przestrzeni nazw. 

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
|EntityName| Usługa Event Hubs obsługuje jednostek Centrum zdarzeń w przestrzeni nazw.|

## <a name="next-steps"></a>Kolejne kroki

* Zobacz [Azure monitorowanie — Przegląd](../monitoring-and-diagnostics/monitoring-overview.md).
* [Pobrać metryki Azure Monitor z platformą .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) w witrynie GitHub. 

Aby uzyskać więcej informacji na temat usługi Event Hubs, skorzystaj z następujących linków:

* Rozpocznij pracę od [samouczka dotyczącego usługi Event Hubs](event-hubs-dotnet-standard-getstarted-send.md)
* [Event Hubs — często zadawane pytania](event-hubs-faq.md)
* [Przykładowe aplikacje korzystające z usługi Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor1.png
[2]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor2.png




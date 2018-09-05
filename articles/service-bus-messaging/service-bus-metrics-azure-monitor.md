---
title: Metryki usługi Azure Service Bus w usłudze Azure Monitor (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Użyj monitorowania platformy Azure do monitorowania jednostek usługi Service Bus
services: service-bus-messaging
documentationcenter: .NET
author: spelluru
manager: timlt
ms.service: service-bus-messaging
ms.topic: article
ms.date: 05/31/2018
ms.author: spelluru
ms.openlocfilehash: b4865c1ba7532910ef0b4a41a5a19d2880e37d6e
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/05/2018
ms.locfileid: "43698965"
---
# <a name="azure-service-bus-metrics-in-azure-monitor-preview"></a>Metryki usługi Azure Service Bus w usłudze Azure Monitor (wersja zapoznawcza)

Metryki usługi Service Bus zapewnia stan zasobów w ramach subskrypcji platformy Azure. Bogaty zestaw danych metryk możesz ocenić ogólną kondycję zasobów usługi Service Bus, nie tylko na poziomie przestrzeni nazw, ale na poziomie jednostki. Te statystyki może być ważne, ponieważ one pomóc Ci do monitorowania stanu usługi Service Bus. Metryki może również pomóc główną przyczynę problemów bez konieczności skontaktuj się z działem pomocy technicznej platformy Azure.

Usługa Azure Monitor zapewnia interfejsy użytkownika ujednolicone monitorowanie z przekraczaniem różne usługi platformy Azure. Aby uzyskać więcej informacji, zobacz [monitorowania na platformie Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) i [metryki pobierania usługi Azure Monitor przy użyciu platformy .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) próbki w witrynie GitHub.

> [!IMPORTANT]
> Jeśli nie było żadnych interakcji z jednostką przez 2 godziny, metryki zostanie uruchomiony, przedstawiający jako wartość "0" do momentu jednostka jest już w stanie bezczynności.

## <a name="access-metrics"></a>Dostęp do metryk

Usługa Azure Monitor zapewnia wiele sposobów, aby dostęp do metryk. Możesz albo metryk dostęp za pośrednictwem [witryny Azure portal](https://portal.azure.com), lub użyj interfejsów API usługi Azure Monitor (REST i .NET) i rozwiązań analitycznych, takich jak usługi Log Analytics i centrów zdarzeń. Aby uzyskać więcej informacji, zobacz [metryk usługi Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-metrics.md#access-metrics-via-the-rest-api).

Metryki są domyślnie włączone i możesz uzyskać dostęp z ostatnich 30 dni danych. Jeśli zachodzi potrzeba Zachowaj dane przez dłuższy okres czasu, można archiwizować dane metryk do konta usługi Azure Storage. To ustawienie jest konfigurowane w [ustawień diagnostycznych](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#diagnostic-settings) w usłudze Azure Monitor.

## <a name="access-metrics-in-the-portal"></a>Dostęp do metryk w portalu

Możesz monitorować metryki, wraz z upływem czasu w [witryny Azure portal](https://portal.azure.com). Jak wyświetlić żądania zakończone powodzeniem i żądania przychodzące na poziomie konta można znaleźć w poniższym przykładzie:

![][1]

Można również uzyskać dostęp do metryk bezpośrednio za pośrednictwem przestrzeni nazw. Aby to zrobić, wybierz przestrzeń nazw, a następnie kliknij przycisk **metryki (Peview)**. Aby wyświetlić metryki filtrowany, aby zakres jednostki, wybierz jednostkę, a następnie kliknij przycisk **metryki (wersja zapoznawcza)**.

![][2]

Dla metryk, obsługa wymiarów możesz odfiltrować z żądaną wartością.

## <a name="billing"></a>Rozliczenia

Przy użyciu metryk w usłudze Azure Monitor jest w wersji zapoznawczej. Jednak jeśli używasz dodatkowych rozwiązań, które pozyskiwania danych metryk, może zostać naliczona według tych rozwiązań. Na przykład opłaty są naliczane przez usługę Azure Storage archiwizowania danych metryk z kontem usługi Azure Storage. Również są opłaty naliczane przez usługę Log Analytics, jeżeli strumień danych metryk do usługi Log Analytics w celu zaawansowanej analizy.

Następujące metryki umożliwiają przegląd kondycji usługi. 

> [!NOTE]
> Firma Microsoft jest wycofano kilka metryk, ponieważ są one przenoszone pod inną nazwą. Może to wymagać aktualizacji odwołaniami. Oznaczona za pomocą słowa kluczowego "przestarzałe" metryk nie będą obsługiwane przyszłości.

Wszystkie wartości metryk są wysyłane do usługi Azure Monitor na minutę. Stopień szczegółowości czasu określa interwał czasu, dla której są prezentowane wartości metryk. Interwał czasu obsługiwane wszystkie metryki usługi Service Bus to 1 minuta.

## <a name="request-metrics"></a>Metryki żądania

Zlicza żądania operacji danych i zarządzania.

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
| Przychodzące żądania (wersja zapoznawcza) | Liczba żądań kierowanych do usługi Service Bus w określonym czasie. <br/><br/> Jednostka: liczba <br/> Typ agregacji: łączna liczba <br/> Dimension: EntityName|
|Liczba pomyślnych żądań (wersja zapoznawcza)|Liczba żądań zakończonych powodzeniem kierowanych do usługi Service Bus w określonym czasie.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łączna liczba <br/> Dimension: EntityName|
|Błędy serwera (wersja zapoznawcza)|Liczba żądań, które nie zostały przetworzone z powodu błędu w usłudze Service Bus w określonym czasie.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łączna liczba <br/> Dimension: EntityName|
|Błędy użytkowników (wersja zapoznawcza — można znaleźć w poniższej podsekcji)|Liczba żądań, które nie zostały przetworzone z powodu błędów użytkowników w określonym czasie.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łączna liczba <br/> Dimension: EntityName|
|Żądania ograniczone (wersja zapoznawcza)|Liczba żądań, które zostały ograniczone z powodu przekroczenia użycia.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łączna liczba <br/> Dimension: EntityName|

### <a name="user-errors"></a>Błędy użytkowników

Dwa rodzaje błędów sklasyfikowanych jako błędy użytkownika:

1. Błędy po stronie klienta (w protokołu HTTP, który będzie 400 błędów).
2. Błędów występujących podczas przetwarzania komunikatów, takie jak [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception).


## <a name="message-metrics"></a>Metryki wiadomości

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
|Komunikaty przychodzące (wersja zapoznawcza)|Liczba zdarzeń lub komunikaty wysyłane do usługi Service Bus w określonym czasie.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łączna liczba <br/> Dimension: EntityName|
|Komunikaty wychodzące (wersja zapoznawcza)|Liczba zdarzeń lub komunikatów odebranych z usługi Service Bus w określonym czasie.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łączna liczba <br/> Dimension: EntityName|

## <a name="connection-metrics"></a>Metryki połączeń

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
|Połączeń ActiveConnections (wersja zapoznawcza)|Liczba aktywnych połączeń w przestrzeni nazw, a także w jednostce.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łączna liczba <br/> Dimension: EntityName|
|Otwartego połączenia (wersja zapoznawcza)|Liczba otwartych połączeń.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łączna liczba <br/> Dimension: EntityName|
|Połączenia zamknięte (wersja zapoznawcza)|Liczba zamkniętych połączeń.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łączna liczba <br/> Dimension: EntityName |

## <a name="resource-usage-metrics"></a>Metryki użycia zasobów

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
|Użycie Procesora na przestrzeń nazw (wersja zapoznawcza)|Procent użycia Procesora przestrzeni nazw.<br/><br/> Jednostka: procent <br/> Typ agregacji: maksymalna <br/> Dimension: EntityName|
|Użycie rozmiaru pamięci na przestrzeń nazw (wersja zapoznawcza)|Wartość procentowa użycia pamięci przestrzeni nazw.<br/><br/> Jednostka: procent <br/> Typ agregacji: maksymalna <br/> Dimension: EntityName|

## <a name="metrics-dimensions"></a>Wymiary metryki

Usługa Azure Service Bus obsługuje następujące wymiary metryk w usłudze Azure Monitor. Dodawanie wymiarów do metryk jest opcjonalne. Jeśli nie dodasz wymiarów, metryki są określone na poziomie przestrzeni nazw. 

|Nazwa wymiaru|Opis|
| ------------------- | ----------------- |
|EntityName| Usługa Service Bus obsługuje jednostek obsługi komunikatów w przestrzeni nazw.|

## <a name="next-steps"></a>Kolejne kroki

Zobacz [monitorowania platformy Azure — omówienie](../monitoring-and-diagnostics/monitoring-overview.md).

[1]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor1.png
[2]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor2.png



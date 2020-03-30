---
title: Metryki w usłudze Azure Monitor — usługi Azure Event Hubs | Dokumenty firmy Microsoft
description: Ten artykuł zawiera informacje dotyczące używania usługi Azure Monitoring do monitorowania usługi Azure Event Hubs
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
ms.date: 09/18/2019
ms.author: shvija
ms.openlocfilehash: 96c346f4359740fda5638dfdbe5735c5bdfce8c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162654"
---
# <a name="azure-event-hubs-metrics-in-azure-monitor"></a>Metryki usługi Azure Event Hubs na platformie Azure Monitor

Metryki usługi Event Hubs zapewniają stan zasobów usługi Event Hubs w ramach subskrypcji platformy Azure. Dzięki bogatemu zestawowi danych metryk można ocenić ogólną kondycję centrów zdarzeń nie tylko na poziomie obszaru nazw, ale także na poziomie jednostki. Te statystyki mogą być ważne, ponieważ pomagają one monitorować stan centrów zdarzeń. Metryki mogą również pomóc w rozwiązywaniu problemów z przyczynami źródłowymi bez konieczności kontaktowania się z pomocą techniczną platformy Azure.

Usługa Azure Monitor udostępnia ujednolicone interfejsy użytkownika do monitorowania w różnych usługach platformy Azure. Aby uzyskać więcej informacji, zobacz [monitorowanie na platformie Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) i pobieranie [metryki usługi Azure Monitor z .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) próbki w usłudze GitHub.

## <a name="access-metrics"></a>Dostęp do danych

Usługa Azure Monitor udostępnia wiele sposobów dostępu do metryk. Można uzyskać dostęp do metryk za pośrednictwem [witryny Azure portal](https://portal.azure.com)lub użyć interfejsów API usługi Azure Monitor (REST i .NET) i rozwiązań analitycznych, takich jak usługa Log Analytics i usługi Event Hubs. Aby uzyskać więcej informacji, zobacz [Monitorowanie danych zebranych przez usługę Azure Monitor](../azure-monitor/platform/data-platform.md).

Metryki są domyślnie włączone i można uzyskać dostęp do ostatnich 30 dni danych. Jeśli chcesz zachować dane przez dłuższy okres czasu, możesz archiwizować dane metryk na koncie usługi Azure Storage. Jest to skonfigurowane w [ustawieniach diagnostycznych](../azure-monitor/platform/diagnostic-settings.md) w usłudze Azure Monitor.


## <a name="access-metrics-in-the-portal"></a>Dostęp do danych w portalu

Metryki można monitorować w czasie w [witrynie Azure portal](https://portal.azure.com). W poniższym przykładzie pokazano, jak wyświetlić pomyślne żądania i przychodzące żądania na poziomie konta:

![Wyświetlanie pomyślnych danych][1]

Można również uzyskać dostęp do metryk bezpośrednio za pośrednictwem obszaru nazw. Aby to zrobić, wybierz obszar nazw, a następnie kliknij **pozycję Metryki**. Aby wyświetlić metryki filtrowane do zakresu centrum zdarzeń, wybierz centrum zdarzeń, a następnie kliknij pozycję **Metryki**.

W przypadku metryk pomocniczych wymiarów należy filtrować z żądaną wartością wymiaru, jak pokazano w poniższym przykładzie:

![Filtr z wartością wymiaru][2]

## <a name="billing"></a>Rozliczenia

Korzystanie z metryk w usłudze Azure Monitor jest obecnie bezpłatne. Jeśli jednak używasz dodatkowych rozwiązań, które pochłonie dane metryki, mogą być rozliczane przez te rozwiązania. Na przykład są rozliczane przez usługę Azure Storage, jeśli archiwum danych metryk do konta usługi Azure Storage. Są również rozliczane przez platformę Azure, jeśli przesyłasz strumieniowo dane metryk do dzienników usługi Azure Monitor w celu zaawansowanej analizy.

Poniższe metryki zapewniają przegląd kondycji usługi. 

> [!NOTE]
> Jesteśmy przestarzałe kilka metryk, ponieważ są one przenoszone pod inną nazwą. Może to wymagać aktualizacji odwołań. Dane oznaczone słowem kluczowym "przestarzałe" nie będą obsługiwane w przyszłości.

Wszystkie wartości metryk są wysyłane do usługi Azure Monitor co minutę. Ziarnistość czasu definiuje przedział czasu, dla którego są prezentowane wartości metryk. Obsługiwany interwał czasu dla wszystkich metryk usługi Event Hubs wynosi 1 minutę.

## <a name="request-metrics"></a>Żądania metryki

Zlicza liczbę żądań operacji danych i zarządzania.

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
| Żądania przychodzące  | Liczba żądań do usługi Azure Event Hubs w określonym okresie. <br/><br/> Jednostka: Liczba <br/> Typ agregacji: Suma <br/> Wymiar: Nazwa elementu |
| Pomyślne żądania    | Liczba pomyślnych żądań złożonych do usługi Usługi Azure Event Hubs w określonym okresie. <br/><br/> Jednostka: Liczba <br/> Typ agregacji: Suma <br/> Wymiar: Nazwa elementu |
| Błędy serwera  | Liczba żądań nie są przetwarzane z powodu błędu w usłudze Usługi Azure Event Hubs w określonym okresie. <br/><br/>Jednostka: Liczba <br/> Typ agregacji: Suma <br/> Wymiar: Nazwa elementu |
|Błędy użytkownika |Liczba żądań nie przetworzonych z powodu błędów użytkownika w określonym czasie.<br/><br/> Jednostka: Liczba <br/> Typ agregacji: Suma <br/> Wymiar: Nazwa elementu|
|Przekroczone błędy przydziału |Liczba żądań przekroczyła dostępny przydział. Zobacz [ten artykuł,](event-hubs-quotas.md) aby uzyskać więcej informacji na temat przydziałów usługi Event Hubs.<br/><br/> Jednostka: Liczba <br/> Typ agregacji: Suma <br/> Wymiar: Nazwa elementu|

## <a name="throughput-metrics"></a>Metryki przepływności

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
|Ograniczone żądania |liczba żądań, które zostały ograniczone, ponieważ przekroczono użycie jednostek przepływności.<br/><br/> Jednostka: Liczba <br/> Typ agregacji: Suma <br/> Wymiar: Nazwa elementu|

## <a name="message-metrics"></a>Metryki wiadomości

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
|Wiadomości przychodzące |Liczba zdarzeń lub wiadomości wysłanych do centrów zdarzeń w określonym okresie.<br/><br/> Jednostka: Liczba <br/> Typ agregacji: Suma <br/> Wymiar: Nazwa elementu|
|Wiadomości wychodzące |Liczba zdarzeń lub wiadomości pobranych z centrów zdarzeń w określonym okresie.<br/><br/> Jednostka: Liczba <br/> Typ agregacji: Suma <br/> Wymiar: Nazwa elementu|
|Bajty przychodzące |Liczba bajtów wysłanych do usługi Usługi Azure Event Hubs w określonym okresie.<br/><br/> Jednostka: Bajty <br/> Typ agregacji: Suma <br/> Wymiar: Nazwa elementu|
|Bajty wychodzące |Liczba bajtów pobranych z usługi Azure Event Hubs w określonym okresie.<br/><br/> Jednostka: Bajty <br/> Typ agregacji: Suma <br/> Wymiar: Nazwa elementu|

## <a name="connection-metrics"></a>Metryki połączeń

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
|ActiveConnections (Aktywne połączenia) |Liczba aktywnych połączeń w obszarze nazw, a także w jednostce.<br/><br/> Jednostka: Liczba <br/> Typ agregacji: Suma <br/> Wymiar: Nazwa elementu|
|Połączenia otwarte |Liczba otwartych połączeń.<br/><br/> Jednostka: Liczba <br/> Typ agregacji: Suma <br/> Wymiar: Nazwa elementu|
|Połączenia zamknięte |Liczba zamkniętych połączeń.<br/><br/> Jednostka: Liczba <br/> Typ agregacji: Suma <br/> Wymiar: Nazwa elementu|

## <a name="event-hubs-capture-metrics"></a>Metryki przechwytywania centrów zdarzeń

Metryki przechwytywania centrów zdarzeń można monitorować po włączeniu funkcji Przechwytywanie dla centrów zdarzeń. Poniżej opisano, co można monitorować z włączoną funkcją przechwytywania.

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
|Przechwytywanie zaległości |Liczba bajtów, które nie zostały jeszcze przechwycone do wybranego miejsca docelowego.<br/><br/> Jednostka: Bajty <br/> Typ agregacji: Suma <br/> Wymiar: Nazwa elementu|
|Przechwycone wiadomości |Liczba wiadomości lub zdarzeń, które są przechwytywane do wybranego miejsca docelowego w określonym czasie.<br/><br/> Jednostka: Liczba <br/> Typ agregacji: Suma <br/> Wymiar: Nazwa elementu|
|Przechwycone bajty |Liczba bajtów przechwyconych do wybranego miejsca docelowego w określonym czasie.<br/><br/> Jednostka: Bajty <br/> Typ agregacji: Suma <br/> Wymiar: Nazwa elementu|

## <a name="metrics-dimensions"></a>Wymiary metryki

Usługa Azure Event Hubs obsługuje następujące wymiary metryk w usłudze Azure Monitor. Dodawanie wymiarów do danych jest opcjonalne. Jeśli wymiary nie zostaną dodane, metryki są określane na poziomie obszaru nazw. 

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
|Nazwa elementu| Usługa Event Hubs obsługuje encje centrum zdarzeń w obszarze nazw.|

## <a name="azure-monitor-integration-with-siem-tools"></a>Integracja usługi Azure Monitor z narzędziami SIEM
Routing danych monitorowania (dzienniki aktywności, dzienniki diagnostyki itp.) do centrum zdarzeń za pomocą usługi Azure Monitor umożliwia łatwą integrację z narzędziami SIEM (Security Information and Event Management). Aby uzyskać więcej informacji, zobacz następujące artykuły/wpisy w blogu:

- [Przesyłanie strumieniowe danych monitorowania platformy Azure do centrum zdarzeń w celu użycia za pomocą narzędzia zewnętrznego](../azure-monitor/platform/stream-monitoring-data-event-hubs.md)
- [Wprowadzenie do integracji dzienników platformy Azure](../security/fundamentals/azure-log-integration-overview.md)
- [Integrowanie platformy Azure Monitor z narzędziami SIEM](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

W scenariuszu, w którym narzędzie SIEM zużywa dane dziennika z centrum zdarzeń, jeśli nie widzisz żadnych wiadomości przychodzących lub widzisz wiadomości przychodzące, ale nie ma wiadomości wychodzących na wykresie metryk, wykonaj następujące kroki:

- Jeśli **nie ma żadnych wiadomości przychodzących,** oznacza to, że usługa Azure Monitor nie przenosi dzienników inspekcji/diagnostyki do centrum zdarzeń. Otwórz bilet pomocy technicznej z zespołem usługi Azure Monitor w tym scenariuszu. 
- jeśli są przychodzące wiadomości, ale **nie wychodzących wiadomości,** oznacza to, że aplikacja SIEM nie odczytuje wiadomości. Skontaktuj się z dostawcą SIEM, aby ustalić, czy konfiguracja centrum zdarzeń tych aplikacji jest poprawna.


## <a name="next-steps"></a>Następne kroki

* Zobacz [omówienie monitorowania platformy Azure](../monitoring-and-diagnostics/monitoring-overview.md).
* [Pobieranie metryk usługi Azure Monitor z](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) próbki .NET w usłudze GitHub. 

Aby uzyskać więcej informacji na temat usługi Event Hubs, skorzystaj z następujących linków:

- Wprowadzenie do samouczka centrum zdarzeń
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [Javascript](get-started-java-send-v2.md)
* [Event Hubs — często zadawane pytania](event-hubs-faq.md)
* [Przykładowe aplikacje korzystające z usługi Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor1.png
[2]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor2.png




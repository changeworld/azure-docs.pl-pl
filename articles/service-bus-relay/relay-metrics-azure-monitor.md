---
title: Metryki usługi Azure Relay w usłudze Azure Monitor | Dokumenty firmy Microsoft
description: Ten artykuł zawiera informacje na temat sposobu korzystania z usługi Azure Monitor do monitorowania stanu usługi Azure Relay.
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
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 159249e2c997e4c414127992b08a83b488281e46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273118"
---
# <a name="azure-relay-metrics-in-azure-monitor"></a>Metryki usługi Azure Relay w usłudze Azure Monitor 
Metryki usługi Azure Relay zapewniają stan zasobów w ramach subskrypcji platformy Azure. Za pomocą bogatego zestawu danych metryk można ocenić ogólną kondycję zasobów przekazu, nie tylko na poziomie obszaru nazw, ale także na poziomie jednostki. Te statystyki mogą być ważne, ponieważ pomagają one monitorować stan usługi Azure Relay. Metryki mogą również pomóc w rozwiązywaniu problemów z przyczynami źródłowymi bez konieczności kontaktowania się z pomocą techniczną platformy Azure.

Usługa Azure Monitor udostępnia ujednolicone interfejsy użytkownika do monitorowania w różnych usługach platformy Azure. Aby uzyskać więcej informacji, zobacz [monitorowanie na platformie Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) i pobieranie [metryki usługi Azure Monitor z .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) próbki w usłudze GitHub.

> [!IMPORTANT]
> Ten artykuł dotyczy tylko funkcji połączenia hybrydowe usługi Azure Relay, a nie do WCF Relay. 

## <a name="access-metrics"></a>Dostęp do danych

Usługa Azure Monitor udostępnia wiele sposobów dostępu do metryk. Można uzyskać dostęp do metryk za pośrednictwem [witryny Azure portal](https://portal.azure.com)lub użyć interfejsów API Usługi Azure Monitor (REST i .NET) i rozwiązań analitycznych, takich jak Pakiet zarządzania operacjami i centra zdarzeń. Aby uzyskać więcej informacji, zobacz [Monitorowanie danych zebranych przez usługę Azure Monitor](../azure-monitor/platform/data-platform.md).

Metryki są domyślnie włączone i można uzyskać dostęp do ostatnich 30 dni danych. Jeśli chcesz zachować dane przez dłuższy okres czasu, możesz archiwizować dane metryk na koncie usługi Azure Storage. Jest to skonfigurowane w [ustawieniach diagnostycznych](../azure-monitor/platform/diagnostic-settings.md) w usłudze Azure Monitor.

## <a name="access-metrics-in-the-portal"></a>Dostęp do danych w portalu

Metryki można monitorować w czasie w [witrynie Azure portal](https://portal.azure.com). W poniższym przykładzie pokazano, jak wyświetlić pomyślne żądania i przychodzące żądania na poziomie konta:

![][1]

Można również uzyskać dostęp do metryk bezpośrednio za pośrednictwem obszaru nazw. Aby to zrobić, wybierz obszar nazw, a następnie kliknij **Metryki **. 

W przypadku metryk pomocniczych wymiarów należy filtrować z żądaną wartością wymiaru.

## <a name="billing"></a>Rozliczenia

Korzystanie z metryk w usłudze Azure Monitor jest obecnie bezpłatne w wersji zapoznawczej. Jeśli jednak używasz dodatkowych rozwiązań, które pochłonie dane metryki, mogą być rozliczane przez te rozwiązania. Na przykład są rozliczane przez usługę Azure Storage, jeśli archiwum danych metryk do konta usługi Azure Storage. Są również rozliczane przez dzienniki usługi Azure Monitor, jeśli przesyłasz strumieniowo dane metryk do dzienników usługi Azure Monitor w celu zaawansowanej analizy.

Poniższe metryki zapewniają przegląd kondycji usługi. 

> [!NOTE]
> Jesteśmy przestarzałe kilka metryk, ponieważ są one przenoszone pod inną nazwą. Może to wymagać aktualizacji odwołań. Dane oznaczone słowem kluczowym "przestarzałe" nie będą obsługiwane w przyszłości.

Wszystkie wartości metryk są wysyłane do usługi Azure Monitor co minutę. Ziarnistość czasu definiuje przedział czasu, dla którego są prezentowane wartości metryk. Obsługiwany przedział czasu dla wszystkich metryk usługi Azure Relay wynosi 1 minutę.

## <a name="connection-metrics"></a>Metryki połączeń

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
| ListenerConnections-Sukces  | Liczba udanych połączeń odbiornika wykonane z usługi Azure Relay w określonym okresie. <br/><br/> Jednostka: Liczba <br/> Typ agregacji: Suma <br/> Wymiar: Nazwa elementu|
|OdbiornikConnections-ClientError |Liczba błędów klienta w połączeniach odbiornika w określonym okresie.<br/><br/> Jednostka: Liczba <br/> Typ agregacji: Suma <br/> Wymiar: Nazwa elementu|
|OdbiornikConnections-ServerError |Liczba błędów serwera w połączeniach odbiornika w określonym czasie.<br/><br/> Jednostka: Liczba <br/> Typ agregacji: Suma <br/> Wymiar: Nazwa elementu|
|SenderConnections-Sukces |Liczba udanych połączeń nadawców nawiązanych w określonym okresie.<br/><br/> Jednostka: Liczba <br/> Typ agregacji: Suma <br/> Wymiar: Nazwa elementu|
|SenderConnections-ClientError |Liczba błędów klienta w połączeniach nadawców w określonym okresie.<br/><br/> Jednostka: Liczba <br/> Typ agregacji: Suma <br/> Wymiar: Nazwa elementu|
|SenderConnections-ServerError |Liczba błędów serwera w połączeniach nadawców w określonym czasie.<br/><br/> Jednostka: Liczba <br/> Typ agregacji: Suma <br/> Wymiar: Nazwa elementu|
|Połączenia słuchaczy-TotalRequests |Całkowita liczba połączeń odbiornika w określonym okresie.<br/><br/> Jednostka: Liczba <br/> Typ agregacji: Suma <br/> Wymiar: Nazwa elementu|
|Połączenia nadawców-TotalRequests |Żądania połączenia wysyłane przez nadawców w określonym czasie.<br/><br/> Jednostka: Liczba <br/> Typ agregacji: Suma <br/> Wymiar: Nazwa elementu|
|ActiveConnections (Aktywne połączenia) |Liczba aktywnych połączeń. Ta wartość jest wartością punktu w czasie.<br/><br/> Jednostka: Liczba <br/> Typ agregacji: Suma <br/> Wymiar: Nazwa elementu|
|AktywneListenery |Liczba aktywnych słuchaczy. Ta wartość jest wartością punktu w czasie.<br/><br/> Jednostka: Liczba <br/> Typ agregacji: Suma <br/> Wymiar: Nazwa elementu|
|OdbiornikRozłącza |Liczba rozłączonych odbiorników w określonym czasie.<br/><br/> Jednostka: Bajty <br/> Typ agregacji: Suma <br/> Wymiar: Nazwa elementu|
|SenderDisconnects |Liczba rozłączonych nadawców w określonym czasie.<br/><br/> Jednostka: Bajty <br/> Typ agregacji: Suma <br/> Wymiar: Nazwa elementu|

## <a name="memory-usage-metrics"></a>Metryki użycia pamięci

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
|BajtyPrzejstrzej |Liczba bajtów przeniesionych w określonym czasie.<br/><br/> Jednostka: Bajty <br/> Typ agregacji: Suma <br/> Wymiar: Nazwa elementu|

## <a name="metrics-dimensions"></a>Wymiary metryki

Usługa Azure Relay obsługuje następujące wymiary metryk w usłudze Azure Monitor. Dodawanie wymiarów do danych jest opcjonalne. Jeśli wymiary nie zostaną dodane, metryki są określane na poziomie obszaru nazw. 

|Nazwa wymiaru|Opis|
| ------------------- | ----------------- |
|Nazwa elementu| Usługa Azure Relay obsługuje jednostki obsługi wiadomości w obszarze nazw.|

## <a name="next-steps"></a>Następne kroki

Zobacz [omówienie monitorowania platformy Azure](../monitoring-and-diagnostics/monitoring-overview.md).

[1]: ./media/relay-metrics-azure-monitor/relay-monitor1.png





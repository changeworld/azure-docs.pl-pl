---
title: Wydajność i skalę w funkcjach trwałe - Azure
description: Wprowadzenie do funkcji trwałe rozszerzenie dla usługi Azure Functions.
services: functions
author: cgillum
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: 110f393e723c7e784a4bd7e79559dd9d55147140
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34599436"
---
# <a name="performance-and-scale-in-durable-functions-azure-functions"></a>Wydajność i skalę w funkcjach trwałe (usługi Azure Functions)

Aby zoptymalizować wydajność i skalowalność, ważne jest, aby zrozumieć skalowania unikatowość tej [trwałe funkcji](durable-functions-overview.md).

Aby zrozumieć zachowania skali, należy zapoznać się szczegóły dla źródłowego dostawcy usługi Azure Storage.

## <a name="history-table"></a>Tabela historii

**Historii** jest tabela usługi Azure Storage, zawierający zdarzenia historii dla wszystkich wystąpień aranżacji w Centrum zadań. Nazwa tabeli jest w formie *TaskHubName*historii. Jak uruchomić wystąpień, nowe wiersze zostaną dodane do tej tabeli. Klucz partycji tabeli pochodzi z Identyfikatora wystąpienia orchestration. Identyfikator wystąpienia jest losowe w większości przypadków, co zapewnia optymalne dystrybucji wewnętrzny partycji w usłudze Azure Storage.

Jeśli wystąpienie aranżacji musi uruchomić, odpowiednie wiersze w tabeli historii są ładowane do pamięci. Te *historii zdarzeń* następnie są odtwarzane do kodu funkcji programu orchestrator, aby przywrócić go do stanu w użyciu. Korzystanie z historii wykonywania odbudować stanu w ten sposób wpływają [źródłem zdarzeń wzorzec](https://docs.microsoft.com/en-us/azure/architecture/patterns/event-sourcing).

## <a name="instances-table"></a>Tabela wystąpień

**Wystąpień** tabela jest innej tabeli magazynu Azure, która zawiera stanami wszystkich wystąpień aranżacji w Centrum zadań. Podczas tworzenia wystąpienia, a nowe wiersze zostaną dodane do tej tabeli. Klucz partycji tej tabeli jest identyfikator wystąpienia aranżacji i klucz wiersza jest stałą o stałej. Istnieje jeden wiersz dla każdego wystąpienia aranżacji.

Ta tabela jest używana do spełnienia żądań zapytań wystąpienia z [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_System_String_) interfejsu API, jak również [kwerendy stanu HTTP API](https://docs.microsoft.com/en-us/azure/azure-functions/durable-functions-http-api#get-instance-status). Jest przechowywana ostatecznie zgodne z zawartością **historii** tabeli wymienione wcześniej. Stosowanie osobnej tabeli magazynu Azure do zaspokojenia wydajnie wystąpienia operacje kwerend w ten sposób wpływają [wzorzec poleceń i zapytań podział odpowiedzialności (CQRS)](https://docs.microsoft.com/en-us/azure/architecture/patterns/cqrs).

## <a name="internal-queue-triggers"></a>Wyzwalacze kolejki wewnętrznej

Funkcje programu orchestrator i funkcje działania są oba wyzwalane przez wewnętrzne kolejki w Centrum zadań aplikacji funkcji. Korzystanie z kolejek w ten sposób zapewnia gwarancje dostarczenia niezawodny komunikatu "na najmniej jednokrotne". Istnieją dwa typy kolejek w funkcjach trwałe: **kolejki kontroli** i **kolejki elementu roboczego**.

### <a name="the-work-item-queue"></a>Kolejki elementu roboczego

Brak jednego elementu roboczego kolejki na Centrum zadania w funkcje trwałe. Jest kolejką podstawowe i działa podobnie jak każdy inny `queueTrigger` kolejki w funkcji platformy Azure. Kolejka ta służy do wyzwalania bezstanowych *działania funkcji* przez dequeueing pojedynczej wiadomości w czasie. Każdy z tych wiadomości zawiera danych wejściowych działania funkcji i dodatkowe metadane, takie jak funkcji, które można wykonać. Gdy aplikacji funkcji trwałe skaluje się do wielu maszyn wirtualnych, wszystkie te maszyny wirtualne konkurować uzyskanie pracy z kolejki elementu roboczego.

### <a name="control-queues"></a>Formant kolejek

Dostępnych jest wiele *kontrolować kolejek* na Centrum zadania w funkcje trwałe. A *kolejki kontroli* jest bardziej złożone niż prostsze kolejki elementu roboczego. Kolejki sterowania są używane do wyzwolenia funkcje stanowe orchestrator. Ponieważ wystąpień funkcji programu orchestrator są stanowe pojedynczych, nie jest możliwość użycia modelu konkurujących konsumentów rozłożenie obciążenia na maszynach wirtualnych. Zamiast tego wiadomości programu orchestrator są równoważeniem obciążenia kolejek formantu. Więcej informacji na temat tego zachowania znajduje się w kolejnych sekcjach.

Formant kolejki zawierają różne typy komunikatów cyklu życia aranżacji. Przykłady obejmują [komunikatów formantu orchestrator](durable-functions-instance-management.md), działania funkcji *odpowiedzi* komunikatów i komunikatów czasomierza. Komunikaty do 32 będzie usuniętej z kolejki kontroli w jednym sondowania. Te komunikaty zawierają ładunek danych, a także w tym wystąpieniu aranżacji, z którym jest on przeznaczony do metadanych. Jeśli wiele komunikatów dequeued są przeznaczone do tego samego wystąpienia aranżacji, będą przetwarzane jako zadanie wsadowe.

## <a name="storage-account-selection"></a>Wybór konta magazynu

Kolejek, tabel i obiektów blob używanych przez funkcje trwałe są tworzone przez skonfigurowanym koncie magazynu Azure. Można określić konto, które będzie używane przy użyciu `durableTask/azureStorageConnectionStringName` w **host.json** pliku.

```json
{
  "durableTask": {
    "azureStorageConnectionStringName": "MyStorageAccountAppSetting"
  }
}
```

Jeśli nie zostanie określony, domyślnie `AzureWebJobsStorage` konta magazynu jest używany. W przypadku obciążeń wrażliwych wydajności konfigurowania konta magazynu z systemem innym niż domyślny jest jednak zalecane. Funkcje trwałe silnie używa usługi Azure Storage, a przy użyciu konta dedykowanych dla magazynu izoluje użycia magazynu trwałego funkcji z wewnętrznego użycia przez hosta usługi Azure Functions.

## <a name="orchestrator-scale-out"></a>Orchestrator skalowalnego w poziomie

Działanie funkcji są bezstanowe i skalowanej automatycznie przez dodanie maszyn wirtualnych. Funkcje programu orchestrator z drugiej strony są *partycjonowanej* przez jedną lub więcej kolejek formantu. Liczba kolejek kontroli jest zdefiniowany w **host.json** pliku. Następujące przykładowe zestawy fragment host.json `durableTask/partitionCount` właściwości `3`.

```json
{
  "durableTask": {
    "partitionCount": 3
  }
}
```
Koncentrator zadań można skonfigurować z zakresu od 1 do 16 partycji. Jeśli nie określono domyślnej liczby partycji jest **4**.

W trakcie skalowania do wielu wystąpień hosta — funkcja (zwykle na różnych maszyn wirtualnych), każde wystąpienie uzyskuje blokady na jednym z kolejek formantu. Te blokad wewnętrznie są zaimplementowane jako obiektu blob magazynu dzierżawy i upewnij się, że wystąpienie aranżacji działa tylko w wystąpienia jednego hosta w czasie. Jeśli koncentrator zadań jest skonfigurowany z trzech kolejek kontroli, wystąpień orchestration można równoważeniem obciążenia na maszynach wirtualnych trzech. Aby zwiększyć pojemność dla wykonania działania funkcji można dodać dodatkowe maszyny wirtualne.

Na poniższym diagramie przedstawiono, jak hosta usługi Azure Functions współdziała z jednostek magazynu w środowisku skalowanej.

![Diagram skali](media/durable-functions-perf-and-scale/scale-diagram.png)

Jak pokazano na poprzedni diagram, wszystkich maszyn wirtualnych konkurować wiadomości w kolejce elementu roboczego. Jednak tylko trzech maszyn wirtualnych można uzyskać komunikaty z kolejek kontroli i każdej maszyny Wirtualnej blokuje kolejki jeden formant.

Wystąpienia aranżacji są dystrybuowane we wszystkich wystąpieniach kolejki formantu. Dystrybucji odbywa się przez tworzenie skrótów, identyfikator wystąpienia orchestration. Identyfikatory wystąpienia domyślnie są losowe identyfikatory GUID, zapewnienie, że wystąpienia są równomiernie we wszystkich kolejkach formantu.

Ogólnie rzecz biorąc funkcje programu orchestrator powinny być lekkie i nie powinna wymagać dużych ilości mocy obliczeniowej. W związku z tym nie jest konieczne tworzenie dużą liczbę kontroli kolejki partycji można uzyskać doskonałe przepływności. Większość pracy duże ma się odbywać w funkcji bezstanowych działania, które może być skalowana w poziomie nieograniczonej.

## <a name="auto-scale"></a>Automatyczne skalowanie

Wszystkie funkcje Azure działa w planie zużycie trwałe funkcje obsługuje automatycznego skalowania za pomocą [kontrolera skali usługi Azure Functions](functions-scale.md#runtime-scaling). Kontroler skali monitoruje opóźnienia wszystkich kolejek okresowo wysyłając _peek_ poleceń. W oparciu o opóźnienia peeked wiadomości, kontrolera skali podejmie decyzję, czy można dodać lub usunąć maszyny wirtualnej.

Kontrolera skali Określa, czy formant kolejki wiadomości opóźnienia są zbyt duże, spowoduje dodanie wystąpień maszyn wirtualnych aż do zmniejsza opóźnienie komunikat do akceptowalnego poziomu lub osiągnie liczba partycji kolejki formantu. Podobnie kontrolera skali zostanie bezustannie dodają wystąpień maszyn wirtualnych, jeśli czasy oczekiwania w kolejce elementu roboczego jest wysoka, niezależnie od liczby partycji.

## <a name="thread-usage"></a>Użycie wątku

Funkcje programu orchestrator są wykonywane w jednym wątku, aby upewnić się, że wykonanie może być deterministyczna między wiele odtworzenie. Ze względu na to jednowątkowego wykonywania ważne jest, czy wątków funkcji programu orchestrator nie wykonywać zadania obciążające Procesor, czy we/wy lub blokowanie różnych przyczyn. Pracę, które mogą wymagać we/wy, blokowanie, lub wiele wątków powinna zostać przeniesiona do działania funkcji.

Działanie funkcji ma te same zachowania jako regularne funkcji wyzwalanych przez kolejkę. One bezpiecznie wykonać operacji We/Wy, wykonaj operacje o znacznym wykorzystaniu procesora CPU i użyj wielu wątków. Ponieważ działania wyzwalaczy bezstanowych, ich za darmo skalować w poziomie do niepowiązanego liczbę maszyn wirtualnych.

## <a name="concurrency-throttles"></a>Ogranicza współbieżności

Środowisko Azure Functions obsługuje wykonywanie wielu funkcji jednocześnie w ramach wystąpienia jednej aplikacji. Wykonanie tego równoczesnych pomaga zwiększyć równoległości i minimalizuje liczbę "zimnych startów", które wystąpią typowych aplikacji wraz z upływem czasu. Jednak wysokiej współbieżności może spowodować wirtualna wysokiego użycia pamięci. W zależności od potrzeb aplikacji funkcji może być konieczne ograniczenie przepustowości współbieżności na wystąpienie, aby uniknąć możliwości brakiem pamięci w sytuacjach duże obciążenie.

Oba działania funkcji i orchestrator funkcja współbieżności limity można skonfigurować w **host.json** pliku. Istotne ustawienia są `durableTask/maxConcurrentActivityFunctions` i `durableTask/maxConcurrentOrchestratorFunctions` odpowiednio.

```json
{
  "durableTask": {
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10,
  }
}
```

W poprzednim przykładzie maksymalnie 10 funkcji programu orchestrator i 10 działania funkcji jednocześnie mogą działać na jednej maszynie Wirtualnej. Jeśli nie zostanie określony, liczba równoczesnych wykonań funkcja działania i orchestrator jest ograniczona do 10 X liczba rdzeni na maszynie Wirtualnej.

> [!NOTE]
> Ustawienia te są przydatne w celu ułatwienia zarządzania pamięci i Procesora CPU na jednej maszynie Wirtualnej. Jednak skalowana w poziomie między wieloma maszynami wirtualnymi, każda maszyna wirtualna ma swój własny zestaw Ustaw limity. Te ustawienia nie może służyć do sterowania współbieżnością na poziomie globalnym.

## <a name="orchestrator-function-replay"></a>Powtarzania funkcji programu orchestrator
Jak wspomniano wcześniej, funkcje programu orchestrator są odtwarzane przy użyciu zawartości **historii** tabeli. Domyślnie program orchestrator kod funkcji jest odtwarzany za każdym razem, gdy partia komunikatów są usuniętej z kolejki kontroli.

Przez włączenie można wyłączyć to zachowanie agresywne powtarzania **rozszerzony sesji**. Po włączeniu rozszerzonej sesji wystąpieniami funkcji programu orchestrator są przechowywane w pamięci, który dłużej i nowych wiadomości mogą być przetwarzane bez pełnego powtarzania. Rozszerzone sesje są włączone przez ustawienie `durableTask/extendedSessionsEnabled` do `true` w **host.json** pliku. `durableTask/extendedSessionIdleTimeoutInSeconds` Ustawienie jest używane do sterowania czas bezczynności sesji odbędzie się w pamięci:

```json
{
  "durableTask": {
    "extendedSessionsEnabled": true,
    "extendedSessionIdleTimeoutInSeconds": 30
  }
}
```

Typowe skutki włączenie sesji rozszerzonej, zostanie zmniejszona we/wy względem konta magazynu Azure i ogólną lepszą przepustowość.

Jednak jeden potencjalnych wadą interfejsu tej funkcji jest tej funkcji bezczynności orchestrator, który pozostanie wystąpień w pamięci dłużej. Istnieją dwa efekty pod uwagę:

1. Ogólny wzrost zużycia pamięci funkcji aplikacji.
2. Jeśli istnieje wiele równoczesnych, krótkim okresie orchestrator funkcja wykonaniami ogólne zmniejszyć przepustowość.

Na przykład jeśli `durableTask/extendedSessionIdleTimeoutInSeconds` ustawiono 30 sekund, a następnie epizodu funkcji programu orchestrator w krótkim okresie, wykonujący w mniej niż 1 sekunda nadal będą używać pamięci przez 30 sekund. Będzie on również odliczona `durableTask/maxConcurrentOrchestratorFunctions` przydziału wspomniano, potencjalnie uniemożliwia uruchomiony inne funkcje programu orchestrator.

> [!NOTE]
> Te ustawienia należy używać tylko po funkcji programu orchestrator została pełni opracowany i przetestowany. Domyślne zachowanie agresywne powtórzeń jest przydatna do wykrywania błędów idempotency w funkcje programu orchestrator w czasie opracowywania.

## <a name="performance-targets"></a>Cele wydajności

Podczas planowania należy używać funkcji niezawodna w przypadku aplikacji produkcyjnej, należy wziąć pod uwagę wymagania dotyczące wydajności wcześnie w procesie planowania. W tej sekcji omówiono niektóre scenariusze użycia podstawowe i numery oczekiwanego maksymalną przepustowość.

* **Działanie sekwencyjne wykonanie**: w tym scenariuszu opisano funkcję programu orchestrator, która uruchamia serię działania funkcji jeden po drugim. Najbardziej podobny [łańcucha funkcji](durable-functions-sequence.md) próbki.
* **Równoległe wykonywanie działania**: w tym scenariuszu opisano funkcję programu orchestrator, która wykonuje wiele funkcji działania równolegle [rozdysponowywania, Fan-in](durable-functions-cloud-backup.md) wzorca.
* **Równoległe przetwarzanie odpowiedzi**: Ten scenariusz jest drugiej połowie [rozdysponowywania, Fan-in](durable-functions-cloud-backup.md) wzorca. Koncentruje się on na wydajność fan-in. Należy zauważyć, że w przeciwieństwie do rozdysponowywania, fan-in odbywa się przez wystąpienie funkcji pojedynczego orchestrator i w związku z tym można uruchomić tylko na jednej maszynie Wirtualnej.
* **Przetwarzanie zewnętrznych zdarzeń**: w tym scenariuszu reprezentuje wystąpienie funkcji pojedynczego orchestrator oczekiwania na [zdarzenia zewnętrzne](durable-functions-external-events.md), pojedynczo.

> [!TIP]
> W odróżnieniu od rozdysponowywania operacje fan-in są ograniczone do jednej maszyny Wirtualnej. Jeśli aplikacja używa rozdysponowywania, wzorzec fan-in i dane dotyczące wydajności fan-in, należy wziąć pod uwagę podrzędna dzielenia rozdysponowywania funkcja działania w wielu [orchestrations podrzędne](durable-functions-sub-orchestrations.md).

W poniższej tabeli przedstawiono oczekiwanej *maksymalna* numery przepływności w scenariuszach opisany wcześniej. "Instance" odwołuje się do pojedynczego wystąpienia uruchomione na jednym małych funkcji programu orchestrator ([A1](../virtual-machines/windows/sizes-previous-gen.md#a-series)) maszyny Wirtualnej w usłudze Azure App Service. We wszystkich przypadkach, zakłada się, że [rozszerzony sesji](#orchestrator-function-replay) są włączone. Rzeczywiste wyniki mogą się różnić w zależności od pracy procesora CPU lub we/wy, wykonywane przez kod funkcji.

| Scenariusz | Maksymalna przepustowość |
|-|-|
| Wykonanie działania sekwencyjnego | 5 działań na sekundę dla każdego wystąpienia |
| Działanie równoległe wykonywanie (rozdysponowywania) | 100 działań na sekundę dla każdego wystąpienia |
| Równoległe przetwarzanie odpowiedzi (fan-in) | 150 odpowiedzi na sekundę dla każdego wystąpienia |
| Przetwarzanie zewnętrznych zdarzeń | 50 zdarzeń na sekundę dla każdego wystąpienia |

> [!NOTE]
> Numery te są aktualne w wersji v1.4.0 (GA) rozszerzenia funkcji trwałe. Te liczby mogą ulec zmianie wraz z upływem czasu w miarę rozwoju funkcję i optymalizacje zostały wprowadzone.

Jeśli nie widzisz numery przepływności spodziewasz się i Procesora i użycie pamięci jest wyświetlany w dobrej kondycji, sprawdź, czy przyczyną jest powiązany z [kondycji konta magazynu](../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance). Trwałe funkcje, których rozszerzenia można umieścić znaczne obciążenie na konto magazynu Azure i obciążeń wystarczająco wysoka, co może spowodować ograniczanie konta magazynu.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Zainstaluj rozszerzenie funkcji trwałe i przykłady](durable-functions-install.md)

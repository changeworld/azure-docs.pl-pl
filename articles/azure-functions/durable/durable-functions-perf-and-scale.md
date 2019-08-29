---
title: Wydajność i skalowanie w Durable Functions na platformie Azure
description: Wprowadzenie do rozszerzenia Durable Functions dla usługi Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: azfuncdf
ms.openlocfilehash: ed0fe22903412d4164fb3a85dbd9afafdc7023e6
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098000"
---
# <a name="performance-and-scale-in-durable-functions-azure-functions"></a>Wydajność i skalowanie w Durable Functions (Azure Functions)

Aby zoptymalizować wydajność i skalowalność, ważne jest zrozumienie unikatowych cech skalowania [Durable Functions](durable-functions-overview.md).

Aby zrozumieć zachowanie skalowania, należy poznać niektóre szczegóły podstawowego dostawcy usługi Azure Storage.

## <a name="history-table"></a>Tabela historii

Tabela **historia** jest tabelą usługi Azure Storage, która zawiera zdarzenia historii dla wszystkich wystąpień aranżacji w centrum zadań. Nazwa tej tabeli ma postać historia *TaskHubName*. W miarę uruchamiania wystąpień nowe wiersze są dodawane do tej tabeli. Klucz partycji tej tabeli pochodzi od identyfikatora wystąpienia aranżacji. Identyfikator wystąpienia jest w większości przypadków losowych, co zapewnia optymalną dystrybucję partycji wewnętrznych w usłudze Azure Storage.

Gdy wystąpienie aranżacji musi działać, odpowiednie wiersze tabeli historii są ładowane do pamięci. Te *zdarzenia historii* są następnie odtwarzane w kodzie funkcji programu Orchestrator w celu przywrócenia ich do stanu poprzedniego punktu kontrolnego. Użycie historii wykonywania w celu odbudowania stanu w ten sposób wpływa na wzorzec określania [źródła zdarzeń](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing).

## <a name="instances-table"></a>Tabela wystąpień

Tabela **wystąpień** jest inną tabelą usługi Azure Storage, która zawiera Stany wszystkich wystąpień aranżacji w centrum zadań. Po utworzeniu wystąpienia nowe wiersze są dodawane do tej tabeli. Kluczem partycji tej tabeli jest identyfikator wystąpienia aranżacji, a klucz wiersza to stała stała. Istnieje jeden wiersz na wystąpienie aranżacji.

Ta tabela służy do zaspokojenia żądań zapytań wystąpienia z interfejsów API [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_System_String_) (.NET `getStatus` ) i (JavaScript), a także do [zapytania o stan Query API](durable-functions-http-api.md#get-instance-status). Jest on stale spójny z zawartością tabeli **historii** wymienionej wcześniej. Użycie oddzielnej tabeli usługi Azure Storage w celu wydajnej realizacji operacji zapytania o wystąpienie w ten sposób wpływa na [wzorzec Command and Query Responsibility Segregation (CQRS)](https://docs.microsoft.com/azure/architecture/patterns/cqrs).

## <a name="internal-queue-triggers"></a>Wyzwalacze wewnętrznej kolejki

Funkcje i funkcje programu Orchestrator są wyzwalane przez kolejki wewnętrzne w centrum zadań aplikacji funkcji. Użycie kolejek w ten sposób zapewnia niezawodne gwarancje dostarczania komunikatów "co najmniej raz". W Durable Functions istnieją dwa typy kolejek: kolejka **sterowania** i kolejka **elementów roboczych**.

### <a name="the-work-item-queue"></a>Kolejka elementów roboczych

W Durable Functions istnieje jedna kolejka elementów pracy dla każdego centrum zadań. Jest to podstawowa Kolejka i zachowuje się podobnie do każdej innej `queueTrigger` kolejki w Azure Functions. Ta Kolejka służy do wyzwalania bezstanowych *funkcji działania* przez odtworzenie pojedynczej wiadomości w danym momencie. Każdy z tych komunikatów zawiera dane wejściowe funkcji działania i dodatkowe metadane, takie jak funkcja, która ma zostać wykonana. Gdy aplikacja Durable Functions skaluje się do wielu maszyn wirtualnych, te maszyny wirtualne współpracują w celu uzyskania pracy z kolejki elementów roboczych.

### <a name="control-queues"></a>Kolejki sterowania

Istnieje wiele *kolejek sterowania* na centrum zadań w Durable Functions. *Kolejka sterująca* jest bardziej zaawansowana niż w przypadku prostszej kolejki elementów roboczych. Kolejki sterujące są używane do wyzwalania funkcji programu Orchestrator. Ponieważ wystąpienia funkcji programu Orchestrator są stanowymi pojedynczymi, nie jest możliwe używanie konkurującego modelu odbiorców do dystrybucji obciążenia między maszynami wirtualnymi. Zamiast tego komunikaty programu Orchestrator są zrównoważone obciążenie w kolejkach sterowania. Więcej szczegółów dotyczących tego zachowania można znaleźć w kolejnych sekcjach.

Kolejki sterujące zawierają różne typy komunikatów cyklu życia aranżacji. Przykładami mogą być [komunikaty sterujące programu Orchestrator](durable-functions-instance-management.md), komunikaty *odpowiedzi* funkcji działania i komunikaty czasomierza. Tak wiele komunikatów, jak wiadomości 32 zostaną rozkolejkowane z kolejki kontroli w ramach jednej ankiety. Te komunikaty zawierają dane ładunku, a także metadane, w tym to wystąpienie aranżacji, dla którego jest przeznaczone. Jeśli wiele komunikatów z kolejki jest przeznaczonych dla tego samego wystąpienia aranżacji, zostaną one przetworzone jako Partia zadań.

### <a name="queue-polling"></a>Sondowanie kolejki

Rozszerzenie zadania trwałego implementuje losowy wykładniczy algorytm wycofywania, aby zmniejszyć wpływ sondowania w kolejce na koszty transakcji magazynu. Po znalezieniu komunikatu środowisko uruchomieniowe natychmiast sprawdza inny komunikat; gdy nie zostanie znaleziony żaden komunikat, czeka przez pewien czas przed ponowieniem próby. Po kolejnych nieudanych próbach uzyskania komunikatu w kolejce czas oczekiwania będzie się zwiększać do momentu osiągnięcia maksymalnego czasu oczekiwania, którego wartość domyślna to 30 sekund.

Maksymalne opóźnienie sondowania można skonfigurować za pomocą `maxQueuePollingInterval` właściwości w [pliku host. JSON](../functions-host-json.md#durabletask). Ustawienie tej wartości na wyższą może skutkować większymi opóźnieniami przetwarzania komunikatów. Wyższe opóźnienia byłyby oczekiwane tylko po okresach braku aktywności. Ustawienie tej wartości na niższą może skutkować wyższym kosztem magazynu spowodowanym zwiększonymi transakcjami magazynu.

> [!NOTE]
> Po uruchomieniu w planach użycia Azure Functions i Premium [kontroler Azure Functions skalowania](../functions-scale.md#how-the-consumption-and-premium-plans-work) będzie sondował każdą kolejkę formantów i elementów roboczych co 10 sekund. Ta dodatkowa sonda jest konieczna do określenia, kiedy należy aktywować wystąpienia aplikacji funkcji i podejmować decyzje dotyczące skalowania. W czasie pisania ten 10-sekundowy interwał jest stały i nie można go skonfigurować.

## <a name="storage-account-selection"></a>Wybór konta magazynu

Kolejki, tabele i obiekty blob używane przez Durable Functions są tworzone w skonfigurowanym koncie usługi Azure Storage. Konto, które ma być używane, można określić `durableTask/azureStorageConnectionStringName` przy użyciu ustawienia w pliku **host. JSON** .

### <a name="functions-1x"></a>Functions w wersji 1.x

```json
{
  "durableTask": {
    "azureStorageConnectionStringName": "MyStorageAccountAppSetting"
  }
}
```

### <a name="functions-2x"></a>Functions w wersji 2.x

```json
{
  "extensions": {
    "durableTask": {
      "azureStorageConnectionStringName": "MyStorageAccountAppSetting"
    }
  }
}
```

Jeśli nie zostanie określony, zostanie `AzureWebJobsStorage` użyte domyślne konto magazynu. W przypadku obciążeń z uwzględnieniem wydajności zaleca się jednak skonfigurowanie konta magazynu innego niż domyślne. Durable Functions korzysta z usługi Azure Storage silniej i użycie dedykowanego konta magazynu izoluje Durable Functions użycie magazynu z użytku wewnętrznego przez hosta Azure Functions.

## <a name="orchestrator-scale-out"></a>Skalowanie w poziomie programu Orchestrator

Funkcje działania są bezstanowe i skalowane automatycznie przez dodanie maszyn wirtualnych. Z drugiej strony funkcje programu Orchestrator są podzielone na *partycje* w jednej lub kilku kolejkach sterowania. Liczba kolejek sterujących jest zdefiniowana w pliku **host. JSON** . Poniższy przykład fragmentu pliku host. JSON ustawia `durableTask/partitionCount` właściwość na `3`.

### <a name="functions-1x"></a>Functions w wersji 1.x

```json
{
  "durableTask": {
    "partitionCount": 3
  }
}
```

### <a name="functions-2x"></a>Functions w wersji 2.x

```json
{
  "extensions": {
    "durableTask": {
      "partitionCount": 3
    }
  }
}
```

Centrum zadań można skonfigurować z zakresu od 1 do 16 partycji. Jeśli nie zostanie określony, domyślna liczba partycji to **4**.

Podczas skalowania do wielu wystąpień hosta funkcji (zwykle na różnych maszynach wirtualnych) każde wystąpienie uzyskuje blokadę dla jednej z kolejek kontrolek. Te blokady są wewnętrznie implementowane jako dzierżawy magazynu obiektów blob i zapewniają, że wystąpienie aranżacji działa tylko na jednym wystąpieniu hosta naraz. Jeśli centrum zadań jest skonfigurowane z trzema kolejkami sterowania, wystąpienia aranżacji mogą być zrównoważone obciążenie dla maksymalnie trzech maszyn wirtualnych. Dodatkowe maszyny wirtualne można dodać, aby zwiększyć pojemność wykonywania funkcji działania.

Na poniższym diagramie przedstawiono sposób interakcji hosta Azure Functions z jednostkami magazynu w środowisku skalowania w poziomie.

![Diagram skalowania](./media/durable-functions-perf-and-scale/scale-diagram.png)

Jak pokazano na poprzednim diagramie, wszystkie maszyny wirtualne konkurują o komunikaty w kolejce elementów roboczych. Jednak tylko trzy maszyny wirtualne mogą pobierać komunikaty z kolejek kontroli, a każda maszyna wirtualna blokuje jedną kolejkę kontroli.

Wystąpienia aranżacji są dystrybuowane we wszystkich wystąpieniach kolejek kontroli. Dystrybucja odbywa się przez mieszanie identyfikatora wystąpienia aranżacji. Identyfikatory wystąpień domyślnie są losowymi identyfikatorami GUID, dzięki czemu wystąpienia są równomiernie dystrybuowane we wszystkich kolejkach sterowania.

Ogólnie mówiąc, funkcje programu Orchestrator mają być lekkie i nie powinny wymagać dużej ilości mocy obliczeniowej. W związku z tym nie jest konieczne tworzenie dużej liczby partycji kolejki kontroli w celu uzyskania dużej przepływności. Większość dużej ilości pracy należy wykonać w funkcjach bezstanowych, które można skalować w nieskończoność.

## <a name="auto-scale"></a>Automatyczne skalowanie

Podobnie jak w przypadku wszystkich Azure Functions uruchomionych w ramach planu zużycia, Durable Functions obsługuje skalowanie automatyczne za pośrednictwem [kontrolera Azure Functions Scale](../functions-scale.md#runtime-scaling). Kontroler skalowania monitoruje opóźnienie wszystkich kolejek przez okresowe wydawanie poleceń wglądu. Na podstawie opóźnień wiadomości z wglądem, kontroler skalowania zdecyduje się, czy dodać lub usunąć maszyny wirtualne.

Jeśli kontroler skalowania ustali, że opóźnienia komunikatów w kolejce sterującej są zbyt wysokie, to spowoduje dodanie wystąpień maszyn wirtualnych do momentu zmniejszenia opóźnienia komunikatów do akceptowalnego poziomu lub osiągnie liczbę partycji kolejki kontroli. Podobnie kontroler skalowania ciągle dodaje wystąpienia maszyn wirtualnych, jeśli opóźnienia kolejki elementu pracy są wysokie, niezależnie od liczby partycji.

## <a name="thread-usage"></a>Użycie wątku

Funkcje programu Orchestrator są wykonywane w pojedynczym wątku, aby zapewnić, że wykonywanie może być deterministyczne dla wielu odtworzeń. Ze względu na to wykonywanie jednowątkowe należy pamiętać, że wątki funkcji programu Orchestrator nie wykonują zadań intensywnie korzystających z procesora CPU, operacji we/wy lub bloku z dowolnego powodu. Każda z zadań, które mogą wymagać wejścia/wyjścia, blokowania lub wielu wątków, powinna zostać przeniesiona do funkcji działania.

Funkcje działania mają wszystkie te same zachowania co regularne funkcje wyzwalane przez kolejki. Mogą bezpiecznie wykonywać operacje we/wy, wykonywać intensywność procesora CPU i korzystać z wielu wątków. Ponieważ wyzwalacze aktywności są bezstanowe, mogą swobodnie skalować w poziomie do nieograniczonej liczby maszyn wirtualnych.

## <a name="concurrency-throttles"></a>Ograniczenia współbieżności

Azure Functions obsługuje wykonywanie wielu funkcji jednocześnie w ramach jednego wystąpienia aplikacji. Takie współbieżne wykonywanie pozwala zwiększyć równoległość i zminimalizować liczbę "zimnego startu", które będą miały w czasie typową aplikację. Jednak wysoka współbieżność może spowodować duże użycie pamięci na maszynę wirtualną. W zależności od potrzeb aplikacji funkcji może być konieczne ograniczenie współbieżności poszczególnych wystąpień, aby uniknąć możliwości uruchamiania pamięci w sytuacjach wymagających dużego obciążenia.

W pliku **host. JSON** można skonfigurować zarówno funkcję działania, jak i limity współbieżności funkcji programu Orchestrator. Odpowiednie ustawienia są `durableTask/maxConcurrentActivityFunctions` i `durableTask/maxConcurrentOrchestratorFunctions` odpowiednio.

### <a name="functions-1x"></a>Functions w wersji 1.x

```json
{
  "durableTask": {
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10
  }
}
```

### <a name="functions-2x"></a>Functions w wersji 2.x

```json
{
  "extensions": {
    "durableTask": {
      "maxConcurrentActivityFunctions": 10,
      "maxConcurrentOrchestratorFunctions": 10
    }
  }
}
```

W poprzednim przykładzie maksymalnie 10 funkcji programu Orchestrator i 10 funkcji działania można uruchomić na jednej maszynie wirtualnej współbieżnie. Jeśli nie zostanie określony, liczba operacji współbieżnych i wykonywanych przez funkcję programu Orchestrator jest ograniczona w 10X liczba rdzeni na maszynie wirtualnej.

> [!NOTE]
> Te ustawienia ułatwiają zarządzanie użyciem pamięci i procesora na jednej maszynie wirtualnej. Jednak w przypadku skalowania w poziomie wielu maszyn wirtualnych każda maszyna wirtualna będzie miała swój własny zestaw limitów. Tych ustawień nie można używać do kontrolowania współbieżności na poziomie globalnym.

## <a name="orchestrator-function-replay"></a>Powtarzanie funkcji programu Orchestrator

Jak wspomniano wcześniej, funkcje programu Orchestrator są odtwarzane przy użyciu zawartości tabeli **historii** . Domyślnie kod funkcji programu Orchestrator jest odtwarzany za każdym razem, gdy partia komunikatów jest usuwana z kolejki sterującej.

To agresywne zachowanie powtarzania można wyłączyć przez włączenie **sesji rozszerzonych**. Po włączeniu rozszerzonych sesji wystąpienia funkcji programu Orchestrator są przechowywane w pamięci, a nowe komunikaty mogą być przetwarzane bez konieczności pełnego ponownego rozegrania. Sesje rozszerzone są włączane przez `durableTask/extendedSessionsEnabled` ustawienie `true` w pliku **host. JSON** . To `durableTask/extendedSessionIdleTimeoutInSeconds` ustawienie służy do kontrolowania, jak długo bezczynna sesja będzie utrzymywana w pamięci:

### <a name="functions-1x"></a>Functions w wersji 1.x

```json
{
  "durableTask": {
    "extendedSessionsEnabled": true,
    "extendedSessionIdleTimeoutInSeconds": 30
  }
}
```

### <a name="functions-2x"></a>Functions w wersji 2.x

```json
{
  "extensions": {
    "durableTask": {
      "extendedSessionsEnabled": true,
      "extendedSessionIdleTimeoutInSeconds": 30
    }
  }
}
```

Typowym efektem włączenia sesji rozszerzonych jest zredukowanie operacji we/wy na konto usługi Azure Storage i ogólna lepsza przepływność.

Jednak jedną z potencjalnych Minusem tej funkcji jest to, że bezczynne wystąpienia funkcji programu Orchestrator będą już działać w pamięci. Istnieją dwa efekty, dla których należy pamiętać:

1. Ogólny wzrost użycia pamięci aplikacji funkcji.
2. Ogólny spadek przepływności w przypadku wielu współbieżnych, krótkoterminowych wykonań funkcji programu Orchestrator.

Przykładowo, jeśli `durableTask/extendedSessionIdleTimeoutInSeconds` jest ustawiona na 30 sekund, wówczas czas działania programu Orchestrator, który jest wykonywany w mniej niż 1 sekund, będzie nadal zajmował pamięć przez 30 sekund. Zostanie również `durableTask/maxConcurrentOrchestratorFunctions` osiągnięty limit przydziału wymieniony wcześniej, co potencjalnie uniemożliwia uruchomienie innych funkcji programu Orchestrator.

> [!NOTE]
> Tych ustawień należy używać tylko po pełnym opracowaniu i przetestowaniu funkcji programu Orchestrator. Domyślne zachowanie funkcji agresywnego powtarzania jest przydatne do wykrywania błędów idempotentności w funkcjach programu Orchestrator w czasie projektowania.

## <a name="performance-targets"></a>Cele wydajności

Planując użycie Durable Functions dla aplikacji produkcyjnej, ważne jest, aby uwzględnić wymagania dotyczące wydajności wczesne w procesie planowania. W tej sekcji omówiono niektóre podstawowe scenariusze użycia i oczekiwane maksymalne numery przepływności.

* **Wykonanie działania sekwencyjnego**: W tym scenariuszu opisano funkcję programu Orchestrator, która uruchamia serię funkcji działania jeden po drugim. Jest to najbardziej zbliżone do przykładu [łańcucha funkcji](durable-functions-sequence.md) .
* **Równoległe wykonywanie działań**: W tym scenariuszu opisano funkcję programu Orchestrator, która wykonuje równolegle wiele funkcji działania [](durable-functions-cloud-backup.md) przy użyciu wzorca wentylatorów.
* **Równoległe przetwarzanie odpowiedzi**: Ten scenariusz to druga połowa [wentylatoru i](durable-functions-cloud-backup.md) wzorca wentylatorów. Koncentruje się na wydajności wentylatorów. Należy pamiętać, że w przeciwieństwie do wentylatorów, wentylator jest wykonywane przez pojedyncze wystąpienie funkcji programu Orchestrator i w związku z tym może działać tylko na jednej maszynie wirtualnej.
* **Przetwarzanie zdarzeń zewnętrznych**: Ten scenariusz reprezentuje pojedyncze wystąpienie funkcji programu Orchestrator, które czeka na [zdarzenia zewnętrzne](durable-functions-external-events.md), po jednym naraz.

> [!TIP]
> W przeciwieństwie do wentylatorów, operacje wentylatorów są ograniczone do pojedynczej maszyny wirtualnej. Jeśli aplikacja korzysta z wentylatorów, wentylator-in i ma na celu wydajność wentylatorów, rozważ poddzielenie wentylatorów funkcji działania między wiele [podkategorii](durable-functions-sub-orchestrations.md).

W poniższej tabeli przedstawiono oczekiwane *maksymalne* numery przepływności dla poprzednio opisanych scenariuszy. "Wystąpienie" odnosi się do pojedynczego wystąpienia funkcji programu Orchestrator działającej na pojedynczej niewielkiej maszynie wirtualnej ([a1](../../virtual-machines/windows/sizes-previous-gen.md#a-series)) w Azure App Service. We wszystkich przypadkach zakłada się, że [rozszerzone sesje](#orchestrator-function-replay) są włączone. Rzeczywiste wyniki mogą się różnić w zależności od procesora lub pracy we/wy wykonanej przez kod funkcji.

| Scenariusz | Maksymalna przepływność |
|-|-|
| Wykonywanie działania sekwencyjnego | 5 działań na sekundę, na wystąpienie |
| Równoległe wykonywanie działań (wentylator-out) | 100 działań na sekundę, na wystąpienie |
| Przetwarzanie reakcji równoległej (wentylator) | 150 odpowiedzi na sekundę, na wystąpienie |
| Przetwarzanie zdarzeń zewnętrznych | 50 zdarzeń na sekundę, na wystąpienie |

> [!NOTE]
> Te numery są aktualne w Durable Functions wersji 1.4.0 rozszerzenia (GA). Liczby te mogą ulec zmianie w miarę upływu czasu, gdy funkcja zostanie zakończyła się, a w miarę dokonywania optymalizacji.

Jeśli nie widzisz oczekiwanych numerów przepływności, a użycie procesora i pamięci jest w dobrej kondycji, sprawdź, czy przyczyna jest związana z [kondycją konta magazynu](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance). Rozszerzenie Durable Functions może znacznie obciążać konto usługi Azure Storage, a wystarczające duże obciążenia mogą spowodować ograniczenie konta magazynu.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie pierwszej funkcji trwałej w języku C#](durable-functions-create-first-csharp.md)

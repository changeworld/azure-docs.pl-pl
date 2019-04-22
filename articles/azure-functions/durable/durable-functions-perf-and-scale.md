---
title: Wydajność i skalowanie do wewnątrz funkcje trwałe - Azure
description: Wprowadzenie do rozszerzenia Durable Functions dla usługi Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: azfuncdf
ms.openlocfilehash: e6ae4cc527ae0828f530ab7f3904d2b3c64c910b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58895753"
---
# <a name="performance-and-scale-in-durable-functions-azure-functions"></a>Wydajność i skalowanie do wewnątrz funkcje trwałe (usługi Azure Functions)

W celu zoptymalizowania wydajności i skalowalności, jest ważne, aby zrozumieć charakterystyki unikatowy skalowania [funkcje trwałe](durable-functions-overview.md).

Zrozumienie zachowania skalowania, musisz zrozumieć niektóre szczegóły źródłowy dostawca usługi Azure Storage.

## <a name="history-table"></a>Tabela historii

**Historii** tabela jest tabeli usługi Azure Storage, która zawiera historię zdarzeń dla wszystkich wystąpień aranżacji w ramach Centrum zadania. Nazwa ta tabela ma postać *TaskHubName*historii. Podczas uruchamiania wystąpienia nowe wiersze są dodawane do tej tabeli. Klucz partycji w tej tabeli jest tworzony na podstawie Identyfikatora wystąpienia aranżacji. Identyfikator wystąpienia jest losowa w większości przypadków, co zapewnia optymalne dystrybucji wewnętrznej partycji w usłudze Azure Storage.

Gdy wystąpienie aranżacji musi zostać uruchomiony, odpowiednie wiersze w tabeli historii są ładowane do pamięci. Te *zdarzenia historii* następnie są odtwarzane w kodzie funkcji programu orchestrator uzyskanie dostępu do stanu wcześniej utworzono punkt kontrolny. Korzystanie z historii wykonywania, aby odbudować stanu w ten sposób mają wpływ [wzorzec określania źródła zdarzeń](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing).

## <a name="instances-table"></a>Tabela wystąpień

**Wystąpień** tabela jest innej tabeli usługi Azure Storage, która zawiera stany wszystkich wystąpień aranżacji w ramach Centrum zadania. Podczas tworzenia wystąpienia nowe wiersze są dodawane do tej tabeli. Klucz partycji w tej tabeli jest identyfikator wystąpienia aranżacji i klucz wiersza to stała stały. Istnieje jeden wiersz dla każdego wystąpienia aranżacji.

Ta tabela jest używana do spełnienia żądania zapytania wystąpienia z [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_System_String_) (.NET) i `getStatus` (JavaScript) interfejsów API, jak również [zapytania stanie interfejsu API protokołu HTTP](durable-functions-http-api.md#get-instance-status). Skoroszyt jest przechowywany ostatecznie spójny z zawartości **historii** tabeli wymienionych wcześniej. Użycie osobnej tabeli usługi Azure Storage skutecznie spełniać operacje zapytań wystąpienia w ten sposób mają wpływ [wzorzec polecenia i podział odpowiedzialności zapytania (CQRS)](https://docs.microsoft.com/azure/architecture/patterns/cqrs).

## <a name="internal-queue-triggers"></a>Wyzwalacze kolejki wewnętrznej

Działanie i funkcje programu orchestrator są zarówno wyzwalane przez wewnętrzne kolejki w Centrum zadań aplikacji funkcji. Korzystanie z kolejek w ten sposób zapewnia gwarantuje dostarczanie niezawodnych komunikat "co najmniej jednokrotne". Istnieją dwa typy kolejek w funkcje trwałe: **kolejki kontroli** i **kolejki elementu roboczego**.

### <a name="the-work-item-queue"></a>Kolejki elementu roboczego

Istnieje jedna kolejka elementu pracy dla Centrum zadania w funkcje trwałe. Jest kolejką podstawowe i działa podobnie jak każdy inny `queueTrigger` kolejki w usłudze Azure Functions. Kolejka ta służy do wyzwalania bezstanowe *działania funkcji* przez dequeueing pojedynczej wiadomości w czasie. Każda z tych komunikatów zawiera działania funkcji w danych wejściowych i dodatkowe metadane, takie jak funkcji do wykonania. Gdy aplikacja trwałe funkcje skalowania na wielu maszynach wirtualnych, te maszyny wirtualne, wszystkie rywalizować uzyskania pracę z kolejki elementu roboczego.

### <a name="control-queues"></a>Kontrolka kolejki

Dostępnych jest wiele *kontrolować kolejek* na Centrum zadania w funkcje trwałe. A *kolejki kontroli* jest bardziej zaawansowane niż prostsze kolejki elementu roboczego. Formant kolejki są używane do wyzwolenia funkcji stanowych programu orchestrator. Ponieważ wystąpienia funkcji programu orchestrator są stanowe pojedynczych elementów, nie jest możliwe użycie modelu konkurujących konsumentów rozłożenie obciążenia między maszynami wirtualnymi. Zamiast tego programu orchestrator są komunikaty o ze zrównoważonym obciążeniem kolejek kontroli. Szczegółowe informacje na temat tego zachowania można znaleźć w kolejnych sekcjach.

Kolejki kontroli zawierają różne typy komunikatów cyklu życia aranżacji. Przykłady obejmują [komunikatów sterujących orchestrator](durable-functions-instance-management.md), działanie funkcji *odpowiedzi* komunikatów i komunikatów czasomierza. Maksymalnie 32 komunikaty będą usuniętej z kolejki kontrolki w pojedynczej sondowania. Te komunikaty zawierają ładunek danych, jak i metadane, takie jak wystąpienia aranżacji, które jest przeznaczony dla. Jeśli wiele wiadomości dequeued są przeznaczone do tego samego wystąpienia aranżacji, zostaną one przetworzone jako zadania wsadowego.

### <a name="queue-polling"></a>Sondowanie kolejki

Rozszerzenie zadań trwałe implementuje losowe wykładniczego wycofywania algorytmu zmniejszają efekt bezczynności kolejki sondowania kosztów transakcji magazynu. Gdy komunikat zostanie znaleziony, środowisko uruchomieniowe sprawdza natychmiast dla innego message; Jeśli żaden komunikat nie zostanie znaleziony, czeka czasie przed podjęciem ponownej próby. Po kolejnych nieudanych prób można pobrać komunikatu w kolejce czas oczekiwania stale rośnie, dopóki nie osiągnie maksymalny czas oczekiwania, którego wartość domyślna to 30 sekund.

Opóźnienie maksymalna sondowania jest można konfigurować za pomocą `maxQueuePollingInterval` właściwość [pliku host.json](../functions-host-json.md#durabletask). Ustawienie tej opcji na wartość większą może doprowadzić do wyższych przetwarzania opóźnienia komunikatów. Większych opóźnień oczekuje się dopiero po okresy braku aktywności. Ustawienie tej opcji na niższą wartość może spowodować wyższe koszty magazynowania, ze względu na transakcje magazynu zwiększone.

> [!NOTE]
> Podczas uruchamiania w planach użycia funkcji platformy Azure i Premium, [Azure funkcji skalowania kontrolera](../functions-scale.md#how-the-consumption-and-premium-plans-work) sondowania każdej kontrolki i element roboczy kolejki co 10 sekund. To dodatkowe sondowania jest niezbędne do określenia, kiedy można aktywować funkcji wystąpień aplikacji oraz do podejmowania decyzji dotyczących skalowania. W momencie pisania tego 10 Drugi interwał jest stała i nie można skonfigurować.

## <a name="storage-account-selection"></a>Wybór konta magazynu

Kolejek, tabel i obiektów blob używanych przez funkcje trwałe są tworzone w ramach skonfigurowanego konta magazynu platformy Azure. Można określić konto do użycia przy użyciu `durableTask/azureStorageConnectionStringName` w **host.json** pliku.

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

Jeśli nie zostanie określony, domyślnie `AzureWebJobsStorage` używane jest konto magazynu. W przypadku wrażliwego na wydajność obciążeń konfigurowania konta magazynu innego niż domyślny jest jednak zalecane. Trwałe funkcje intensywnie używa usługi Azure Storage, a przy użyciu konta dedykowanych dla magazynu izoluje funkcje trwałe użycie magazynu z wewnętrznego użycia przez hosta usługi Azure Functions.

## <a name="orchestrator-scale-out"></a>Program orchestrator skalowalnego w poziomie

Działanie funkcji są bezstanowe i skalowanych w poziomie automatycznie, dodając maszyny wirtualne. Funkcje programu orchestrator, z drugiej strony są *partycjonowane* przez jedną lub więcej kolejek kontroli. Liczba kolejek kontroli jest zdefiniowany w **host.json** pliku. Poniższy przykład ustawia fragment host.json `durableTask/partitionCount` właściwość `3`.

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

Koncentrator zadania można skonfigurować za pomocą od 1 do 16 partycji. Jeśli nie zostanie określony, domyślna liczba partycji jest **4**.

Skalowania w poziomie do wielu wystąpień hosta — funkcja (zwykle na różnych maszynach wirtualnych), każde wystąpienie uzyskuje blokadę na jednym z kolejek kontroli. Blokadami tymi są implementowane wewnętrznie, zgodnie z usługi blob storage dzierżawy i upewnij się, że wystąpienie aranżacji działa tylko w wystąpienie jednego hosta w danym momencie. Jeśli koncentrator zadania jest skonfigurowany za pomocą trzech kolejek kontroli, aranżacji wystąpienia może być ze zrównoważonym obciążeniem dowolną liczbę trzech maszyn wirtualnych. Aby zwiększyć pojemność dla działań wykonywania funkcji można dodać dodatkowe maszyny wirtualne.

Na poniższym diagramie przedstawiono, jak hosta usługi Azure Functions współdziałają z jednostkami magazynu w środowiskach skalowanych w poziomie.

![Diagram skalowania](./media/durable-functions-perf-and-scale/scale-diagram.png)

Jak pokazano na poprzednim rysunku, wszystkie maszyny wirtualne konkurować o komunikaty w kolejce elementu roboczego. Jednak tylko dla trzech maszyn wirtualnych można pobrać komunikaty z kolejek o kontroli i każda maszyna wirtualna blokuje kolejki jeden formant.

Wystąpienia aranżacji są rozproszone we wszystkich wystąpieniach kolejki kontroli. Dystrybucji odbywa się przez utworzenie wartości skrótu Identyfikatora wystąpienia aranżacji. Identyfikatory wystąpień domyślnie są losowe identyfikatory GUID zapewnienie wystąpienia są równomiernie rozłożone we wszystkich kolejkach kontroli.

Ogólnie rzecz biorąc funkcje programu orchestrator umożliwiają uproszczone i nie powinna wymagać dużych ilości mocy obliczeniowej. W związku z tym nie jest niezbędne do utworzenia partycji kolejki w celu uzyskiwania informacji o przepływności doskonałe dużej liczby kontroli. Większość zadań samochodów powinna być podejmowana w bezstanowe działanie funkcji, które można skalować w poziomie nieskończoność.

## <a name="auto-scale"></a>Automatyczne skalowanie

Ponieważ wszystkie usługi Azure Functions działającej w planie zużycie, funkcje trwałe obsługuje Skalowanie automatyczne za pomocą [kontrolera skalowania usługi Azure Functions](../functions-scale.md#runtime-scaling). Kontroler skalowania monitoruje opóźnienia wszystkich kolejek okresowo wydając _podglądu_ poleceń. Oparte na opóźnienia peeked komunikatów, kontroler skalowania podejmie decyzję, czy dodawanie i usuwanie maszyn wirtualnych.

Kontroler skali Określa, czy kontroli kolejki komunikatów opóźnienia są zbyt duże, spowoduje dodanie wystąpień maszyn wirtualnych aż do zmniejsza opóźnienie wiadomości, do akceptowalnego poziomu lub napotkania liczba partycji kolejki kontroli. Podobnie kontroler skalowania stale spowoduje dodanie wystąpień maszyn wirtualnych, gdy opóźnienia elementu roboczego kolejki są wysokie, niezależnie od liczby partycji.

## <a name="thread-usage"></a>Użycie wątku

Funkcje programu orchestrator są wykonywane w jednym wątku, aby upewnić się, że wykonanie może być deterministyczna między wiele odtworzenie. Ze względu na to wykonanie jednowątkowe ważne jest, że wątków funkcji programu orchestrator nie wykonywać zadania intensywnie obciążające procesor CPU, czy operacje We/Wy lub blokowanie jakiegokolwiek powodu. Wszelkie prace może to wymagać operacji We/Wy, blokowanie, lub wiele wątków zostanie przeniesiona do działania funkcji.

Działanie funkcji ma ten sam zachowania jako regularne funkcji wyzwalanej przez kolejkę. One bezpiecznie wykonać operacji We/Wy, wykonać operacje o znacznym wykorzystaniu procesora CPU i użyciu wielu wątków. Ponieważ działania wyzwalacze są bezstanowe, ich dowolnie skalować w poziomie do nieograniczoną liczbę maszyn wirtualnych.

## <a name="concurrency-throttles"></a>Ograniczenia współbieżności

Usługa Azure Functions obsługuje wykonywanie wielu funkcji jednocześnie w ramach wystąpienie pojedynczej aplikacji. To wykonywanie równoczesne pomaga zwiększyć równoległość i minimalizuje liczbę "zimnych startów", które doświadczy typowych aplikacji wraz z upływem czasu. Jednak współbieżności Wysoki może powodować VM wysokiego użycia pamięci. W zależności od potrzeb aplikacji funkcji może być konieczne ograniczenie współbieżności na wystąpienie, aby uniknąć możliwości ilość pamięci w sytuacji wysokiego obciążenia.

Można skonfigurować zarówno działania funkcji i orchestrator funkcja limitów współbieżności w **host.json** pliku. Odpowiednie ustawienia są `durableTask/maxConcurrentActivityFunctions` i `durableTask/maxConcurrentOrchestratorFunctions` odpowiednio.

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

W poprzednim przykładzie maksymalnie 10 funkcje aktywności i 10 funkcje programu orchestrator można uruchamiać na jednej maszynie Wirtualnej jednocześnie. Jeśli nie zostanie określony, liczba współbieżnych wykonań funkcji działanie i orchestrator jest ograniczone do 10 X liczba rdzeni na maszynie Wirtualnej.

> [!NOTE]
> Te ustawienia są przydatne w celu ułatwienia zarządzania pamięci i Procesora CPU na jednej maszynie Wirtualnej. Jednak skalowane do wewnątrz na wielu maszynach wirtualnych, każda maszyna wirtualna ma swój własny zestaw ograniczeń. Te ustawienia nie może służyć do sterowania współbieżnością na poziomie globalnym.

## <a name="orchestrator-function-replay"></a>Powtarzanie funkcji programu orchestrator

Jak wspomniano wcześniej, funkcje programu orchestrator są odtwarzane przy użyciu zawartości **historii** tabeli. Domyślnie kod funkcji programu orchestrator jest odtwarzany za każdym razem, gdy partię komunikatów są usuwane z kolejki z kolejki kontroli.

Po włączeniu można wyłączyć to zachowanie agresywne powtarzania **rozszerzony sesji**. Po włączeniu sesji rozszerzonej wystąpieniami funkcji programu orchestrator są przechowywane w pamięci, którą dłużej i nowe komunikaty mogą być przetwarzane bez pełnej powtarzania. Rozszerzone sesje są włączone, ustawiając `durableTask/extendedSessionsEnabled` do `true` w **host.json** pliku. `durableTask/extendedSessionIdleTimeoutInSeconds` Ustawienie jest używane do kontroli, ile czasu bezczynności sesji odbędzie się w pamięci:

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

Zmniejszono typowe efekt Włączanie sesji rozszerzonej operacji We/Wy względem konta usługi Azure Storage i ogólnego ulepszoną przepływność.

Jednak jeden potencjalną wadą stosowania tej funkcji jest tej funkcji bezczynności programu orchestrator, której wystąpienia będzie pozostawać w pamięci dłużej. Istnieją dwa skutki wiedzieć:

1. Ogólny wzrost użycia pamięci aplikacji funkcji.
2. Ogólne zmniejszenie przepływności, jeśli istnieje wiele wykonań funkcji współbieżnych, krótkotrwałych programu orchestrator.

Na przykład jeśli `durableTask/extendedSessionIdleTimeoutInSeconds` jest ustawiona na 30 sekund, odcinek funkcja krótkotrwałe programu orchestrator, który jest wykonywany w mniej niż 1 sekundę nadal zajmie pamięci przez 30 sekund. Również będą uwzględniane w limicie `durableTask/maxConcurrentOrchestratorFunctions` przydziału wspomniano wcześniej, potencjalnie uniemożliwia innych funkcji programu orchestrator uruchomiona.

> [!NOTE]
> Te ustawienia należy używać tylko w przypadku, po funkcję orkiestratora został w pełni opracowany i przetestowany. Domyślne zachowanie agresywne powtarzania przydaje się do wykrywania błędów idempotentności w funkcjach programu orchestrator w czasie projektowania.

## <a name="performance-targets"></a>Elementy docelowe wydajności

Podczas planowania użycia funkcje trwałe dla aplikacji produkcyjnych, należy wziąć pod uwagę wymagania dotyczące wydajności wcześnie w procesie planowania. Tej sekcji opisano niektóre scenariusze użycia podstawowego i numery oczekiwanego maksymalną przepustowość.

* **Działanie sekwencyjne wykonywania**: W tym scenariuszu opisano funkcja orkiestratora, która wykonuje serię działania funkcji jeden po drugim. Najbardziej przypomina [łańcucha funkcja](durable-functions-sequence.md) próbki.
* **Równoległe wykonywanie działania**: W tym scenariuszu opisano funkcja orkiestratora, która wykonuje wiele działań funkcji równolegle przy użyciu [wielokierunkowe, Fan-in](durable-functions-cloud-backup.md) wzorca.
* **Równoległe przetwarzanie odpowiedzi**: Ten scenariusz jest w drugiej połowie [wielokierunkowe, Fan-in](durable-functions-cloud-backup.md) wzorca. Koncentruje się ona na wydajność fan-in. Należy pamiętać, że w przeciwieństwie do wielokierunkowe, fan-in odbywa się przez wystąpienie funkcji pojedynczego programu orchestrator i w związku z tym można uruchomić tylko na jednej maszynie Wirtualnej.
* **Przetwarzanie zdarzeń zewnętrznych**: W tym scenariuszu reprezentuje wystąpienie funkcji pojedynczego programu orchestrator, która czeka na [zdarzenia zewnętrzne](durable-functions-external-events.md), pojedynczo.

> [!TIP]
> W odróżnieniu od wielokierunkowe operacje fan-in są ograniczone do pojedynczej maszyny Wirtualnej. Jeśli aplikacja używa wielokierunkowe, wzorzec fan-in, a dane dotyczące wydajności fan-in, należy wziąć pod uwagę podrzędnych dzielenia wielokierunkowego funkcja działania w wielu [podrzędnych aranżacji](durable-functions-sub-orchestrations.md).

W poniższej tabeli przedstawiono oczekiwanej *maksymalna* numery przepływności dla scenariuszy opisany wcześniej. "Wystąpienie" odnosi się do pojedynczego wystąpienia uruchomione na jednym małych funkcji programu orchestrator ([A1](../../virtual-machines/windows/sizes-previous-gen.md#a-series)) maszyny Wirtualnej w usłudze Azure App Service. We wszystkich przypadkach, zakłada się, że [rozszerzony sesji](#orchestrator-function-replay) są włączone. Rzeczywiste wyniki mogą się różnić w zależności od użycia Procesora lub we/wy wykonywanych przez kod funkcji.

| Scenariusz | Maksymalna przepływność |
|-|-|
| Działanie sekwencyjne wykonywania | 5 działań na sekundę dla każdego wystąpienia |
| Działanie równoległe wykonywanie (wielokierunkowego) | 100 działań na sekundę dla każdego wystąpienia |
| Równoległe przetwarzanie odpowiedzi (fan-in) | 150 odpowiedzi na sekundę dla każdego wystąpienia |
| Przetwarzanie zewnętrznego zdarzenia | 50 zdarzeń na sekundę dla każdego wystąpienia |

> [!NOTE]
> Te liczby są aktualne v1.4.0 (GA) wersję rozszerzenia funkcji trwałych. Te numery mogą ulec zmianie wraz z upływem czasu, jak dojrzewa tę funkcję i optymalizacje zostały wprowadzone.

Jeśli nie widzisz numery przepływności można oczekiwać i your CPU i użycie pamięci jest wyświetlana w dobrej kondycji, sprawdź, czy przyczyną jest powiązany z [kondycję swojego konta magazynu](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance). Trwałe funkcje, których rozszerzenie można umieszczać znaczne obciążenie na konto usługi Azure Storage i wystarczająco wysokie obciążenie może spowodować ograniczenie konta magazynu.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Tworzenie pierwszej funkcji trwałej w języku C#](durable-functions-create-first-csharp.md)

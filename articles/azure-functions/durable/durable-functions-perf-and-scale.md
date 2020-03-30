---
title: Wydajność i skalowanie w funkcjach trwałych — azure
description: Wprowadzenie do rozszerzenia Durable Functions dla usługi Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 260811c4ae15b45de6f7bc1b22e3ed6dcea44259
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277910"
---
# <a name="performance-and-scale-in-durable-functions-azure-functions"></a>Wydajność i skalowanie w usłudze Durable Functions (Azure Functions)

Aby zoptymalizować wydajność i skalowalność, ważne jest, aby zrozumieć unikalne cechy skalowania [funkcji trwałych.](durable-functions-overview.md)

Aby zrozumieć zachowanie skalowania, musisz zrozumieć niektóre szczegóły dostawcy usługi Azure Storage.

## <a name="history-table"></a>Tabela historii

Historia **History** tabela jest tabela usługi Azure Storage, która zawiera zdarzenia historii dla wszystkich wystąpień aranżacji w centrum zadań. Nazwa tej tabeli znajduje się w formularzu *TaskHubName*History. W miarę uruchamiania wystąpień do tej tabeli dodawane są nowe wiersze. Klucz partycji tej tabeli pochodzi od identyfikatora wystąpienia aranżacji. Identyfikator wystąpienia jest losowy w większości przypadków, co zapewnia optymalną dystrybucję partycji wewnętrznych w usłudze Azure Storage.

Gdy wystąpienie aranżacji musi być uruchomione, odpowiednie wiersze tabeli Historia są ładowane do pamięci. Te *zdarzenia historii* są następnie odtwarzane do kodu funkcji koordynatora, aby przywrócić go do stanu wcześniej punkt kontrolny. Na użycie historii wykonania do odbudowania stanu w ten sposób ma wpływ [wzorzec pozyskiwania zdarzeń.](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing)

## <a name="instances-table"></a>Tabela wystąpień

Tabela **Wystąpienia** to kolejna tabela usługi Azure Storage zawierająca stany wszystkich wystąpień aranżacji i encji w centrum zadań. Podczas tworzenia wystąpień do tej tabeli dodawane są nowe wiersze. Klucz partycji tej tabeli jest identyfikator wystąpienia aranżacji lub klucz jednostki i klucz wiersza jest stała stała. Istnieje jeden wiersz na aranżacji lub wystąpienia jednostki.

Ta tabela służy do zaspokajania `GetStatusAsync` żądań zapytań wystąpień z interfejsów API (.NET) i `getStatus` (JavaScript), a także interfejsu API http [kwerendy o stanie.](durable-functions-http-api.md#get-instance-status) Jest on utrzymywany w końcu zgodne z zawartością **tabeli historia** wymienione wcześniej. Na użycie oddzielnej tabeli usługi Azure Storage w celu efektywnego zaspokojenia operacji kwerend wystąpienia w ten sposób ma wpływ [wzorzec segregacji odpowiedzialność za polecenia i kwerendy (CQRS).](https://docs.microsoft.com/azure/architecture/patterns/cqrs)

## <a name="internal-queue-triggers"></a>Wyzwalacze kolejki wewnętrznej

Funkcje i funkcje działania orchestrator są wyzwalane przez kolejki wewnętrzne w centrum zadań aplikacji funkcji. Korzystanie z kolejek w ten sposób zapewnia niezawodne gwarancje dostarczania komunikatów "co najmniej raz". Istnieją dwa typy kolejek w funkcji trwałych: **kolejka kontrolna** i **kolejka elementu roboczego**.

### <a name="the-work-item-queue"></a>Kolejka elementu roboczego

Istnieje jedna kolejka elementu pracy na centrum zadań w funkcji trwałych. Jest to kolejka podstawowa i zachowuje `queueTrigger` się podobnie do każdej innej kolejki w usłudze Azure Functions. Ta kolejka jest używana do wyzwalania *funkcji aktywności* bezstanowej przez usunięcie jednej wiadomości w kolejce naraz. Każdy z tych komunikatów zawiera dane wejściowe funkcji działania i dodatkowe metadane, takie jak funkcja do wykonania. Gdy aplikacja funkcje trwałe skaluje się do wielu maszyn wirtualnych, wszystkie te maszyny wirtualne konkurują o uzyskanie pracy z kolejki elementu pracy.

### <a name="control-queues"></a>Kolejki kontrolne

Istnieje wiele *kolejek sterowania* na centrum zadań w funkcji trwałych. *Kolejka kontrolna* jest bardziej wyrafinowana niż prostsza kolejka elementów roboczych. Kolejki kontroli są używane do wyzwalania funkcji koordynatora i jednostki stanowej. Ponieważ wystąpienia funkcji koordynatora i jednostki są stanowe singletony, nie jest możliwe użycie konkurencyjnego modelu konsumenta do dystrybucji obciążenia między maszynami wirtualnymi. Zamiast tego orchestrator i jednostki komunikaty są równoważenia obciążenia w kolejkach kontroli. Więcej szczegółów na temat tego zachowania można znaleźć w kolejnych sekcjach.

Kolejki kontroli zawierają różne typy komunikatów cyklu życia aranżacji. Przykłady obejmują [komunikaty sterowania aranżatora,](durable-functions-instance-management.md) *komunikaty odpowiedzi funkcji* działania i komunikaty czasomierza. Aż 32 wiadomości zostaną odsuniętye z kolejki kontrolnej w jednej ankiecie. Te komunikaty zawierają dane ładunku, a także metadane, w tym, dla którego wystąpienia aranżacji jest przeznaczona. Jeśli wiele komunikatów de-sejka są przeznaczone dla tego samego wystąpienia aranżacji, będą one przetwarzane jako partii.

### <a name="queue-polling"></a>Sondowanie kolejek

Rozszerzenie zadania trwałe implementuje losowe wykładnicze algorytm wycofywania, aby zmniejszyć wpływ sondowania kolejki bezczynności na koszty transakcji magazynu. Po znalezieniu komunikatu środowisko wykonawcze natychmiast sprawdza, czy jest inny komunikat; gdy nie zostanie znaleziony żaden komunikat, czeka na pewien czas przed ponowną próbą. Po kolejnych nieudanych próbach uzyskania wiadomości kolejki czas oczekiwania nadal wzrasta, aż osiągnie maksymalny czas oczekiwania, który jest domyślnie 30 sekund.

Maksymalne opóźnienie sondowania można `maxQueuePollingInterval` skonfigurować za pomocą właściwości w [pliku host.json](../functions-host-json.md#durabletask). Ustawienie tej właściwości na wyższą wartość może spowodować wyższe opóźnienia przetwarzania wiadomości. Większe opóźnienia można się spodziewać dopiero po okresach braku aktywności. Ustawienie tej właściwości na niższą wartość może spowodować wyższe koszty magazynowania ze względu na zwiększone transakcje magazynu.

> [!NOTE]
> Podczas uruchamiania w planach zużycie i premia usług Azure Functions [kontroler skalowania funkcji](../functions-scale.md#how-the-consumption-and-premium-plans-work) sonduje każdą kontrolę i kolejkę elementu roboczego raz na 10 sekund. To dodatkowe sondowanie jest konieczne, aby określić, kiedy aktywować wystąpienia aplikacji funkcji i podejmować decyzje skali. W momencie pisania ten interwał 10 sekund jest stały i nie można go skonfigurować.

## <a name="storage-account-selection"></a>Wybór konta magazynu

Kolejki, tabele i obiekty blob używane przez funkcje trwałe są tworzone na skonfigurowanym koncie usługi Azure Storage. Konto do użycia można określić za pomocą `durableTask/storageProvider/connectionStringName` ustawienia (lub `durableTask/azureStorageConnectionStringName` ustawienia w funkcji trwałych 1.x) w pliku **host.json.**

### <a name="durable-functions-2x"></a>Funkcje trwałe 2.x

```json
{
  "extensions": {
    "durableTask": {
      "storageProvider": {
        "connectionStringName": "MyStorageAccountAppSetting"
      }
    }
  }
}
```

### <a name="durable-functions-1x"></a>Funkcje trwałe 1.x

```json
{
  "extensions": {
    "durableTask": {
      "azureStorageConnectionStringName": "MyStorageAccountAppSetting"
    }
  }
}
```

Jeśli nie zostanie `AzureWebJobsStorage` określony, używane jest domyślne konto magazynu. Jednak w przypadku obciążeń zależnych od wydajności zaleca się skonfigurowanie konta magazynu nieobe domyślnego. Funkcje trwałe intensywnie używa usługi Azure Storage, a przy użyciu dedykowanego konta magazynu izoluje użycia magazynu funkcji trwałych od użycia wewnętrznego przez hosta usługi Azure Functions.

## <a name="orchestrator-scale-out"></a>Skalowanie w poziomie koordynatora

Funkcje działania są bezstanowe i skalowane w poziomie automatycznie przez dodanie maszyn wirtualnych. Funkcje i jednostki orchestrator, z drugiej strony, są podzielone na *partycje* między jedną lub więcej kolejek sterowania. Liczba kolejek kontrolnych jest zdefiniowana w pliku **host.json.** Poniższy przykładowy fragment kodu host.json `durableTask/storageProvider/partitionCount` ustawia `durableTask/partitionCount` właściwość (lub w `3`funkcji trwałych 1.x) na .

### <a name="durable-functions-2x"></a>Funkcje trwałe 2.x

```json
{
  "extensions": {
    "durableTask": {
      "storageProvider": {
          "partitionCount": 3
      }
    }
  }
}
```

### <a name="durable-functions-1x"></a>Funkcje trwałe 1.x

```json
{
  "extensions": {
    "durableTask": {
      "partitionCount": 3
    }
  }
}
```

Centrum zadań można skonfigurować z partycjami od 1 do 16. Jeśli nie zostanie określona, domyślna liczba partycji wynosi **4**.

Podczas skalowania w poziomie do wielu wystąpień hosta funkcji (zazwyczaj na różnych maszynach wirtualnych), każde wystąpienie uzyskuje blokadę na jednej z kolejek formantu. Blokady te są wewnętrznie implementowane jako dzierżawy magazynu obiektów blob i upewnij się, że wystąpienie aranżacji lub jednostka działa tylko w jednym wystąpieniu hosta w czasie. Jeśli centrum zadań jest skonfigurowany z trzema kolejkami kontroli, wystąpienia aranżacji i jednostek mogą być równoważenia obciążenia na aż trzech maszynach wirtualnych. Dodatkowe maszyny wirtualne można dodać, aby zwiększyć pojemność dla wykonywania funkcji działania.

Na poniższym diagramie przedstawiono, jak host usług Azure Functions współdziała z jednostkami magazynu w środowisku skalowany w poziomie.

![Diagram skalowania](./media/durable-functions-perf-and-scale/scale-diagram.png)

Jak pokazano na poprzednim diagramie, wszystkie maszyny wirtualne konkurują o wiadomości w kolejce elementu roboczego. Jednak tylko trzy maszyny wirtualne mogą uzyskić wiadomości z kolejek kontroli, a każda maszyna wirtualna blokuje jedną kolejkę formantu.

Wystąpienia i jednostki aranżacji są dystrybuowane we wszystkich wystąpieniach kolejki formantu. Dystrybucja odbywa się przez mieszanie identyfikator wystąpienia aranżacji lub nazwę jednostki i pary kluczy. Identyfikatory wystąpień aranżacji domyślnie są losowymi identyfikatorami GUID, dzięki czemu wystąpienia są jednakowo rozłożone na wszystkie kolejki formantu.

Ogólnie rzecz biorąc, funkcje orkiestratora są przeznaczone do lekkich i nie powinny wymagać dużych ilości mocy obliczeniowej. W związku z tym nie jest konieczne, aby utworzyć dużą liczbę partycji kolejki kontroli, aby uzyskać dużą przepływność dla aranżacji. Większość ciężkiej pracy należy wykonać w bezstanowych funkcji działania, które mogą być skalowane w nieskończoność.

## <a name="auto-scale"></a>Automatyczne skalowanie

Podobnie jak w przypadku wszystkich funkcji platformy Azure uruchomionych w planach Zużycie i Elastyczna premia, funkcje trwałe obsługują automatyczne skalowanie za pośrednictwem [kontrolera skalowania usługi Azure Functions.](../functions-scale.md#runtime-scaling) Kontroler skalowania monitoruje opóźnienie wszystkich kolejek, okresowo wydając polecenia _wglądu._ Na podstawie opóźnień zaglądających wiadomości kontroler skalowania zdecyduje, czy dodać lub usunąć maszyny wirtualne.

Jeśli kontroler skalowania określa, że opóźnienia komunikatów kolejki sterującej są zbyt wysokie, doda wystąpienia maszyn wirtualnych, dopóki opóźnienie komunikatu nie zmniejszy się do dopuszczalnego poziomu lub osiągnie liczbę partycji kolejki sterującej. Podobnie kontroler skalowania będzie stale dodawać wystąpienia maszyn wirtualnych, jeśli opóźnienia kolejki elementu roboczego są wysokie, niezależnie od liczby partycji.

> [!NOTE]
> Począwszy od funkcji trwałe 2.0, aplikacje funkcji można skonfigurować do uruchamiania w punktach końcowych usługi chronionej przez sieci wirtualne w planie Elastic Premium. W tej konfiguracji funkcje trwałe wyzwala zainicjować żądania skali zamiast kontrolera skalowania.

## <a name="thread-usage"></a>Użycie wątku

Funkcje orchestrator są wykonywane w jednym wątku, aby upewnić się, że wykonanie może być deterministyczne w wielu powtórkach. Z powodu tego wykonywania jednowątkowego ważne jest, aby wątki funkcji programu orchestrator nie wykonywały zadań intensywnie korzystających z procesora CPU, ani nie blokował ani nie blokują z jakiegokolwiek powodu. Wszelkie prace, które mogą wymagać we/wy, blokowanie lub wiele wątków powinny być przenoszone do funkcji działania.

Funkcje działania mają wszystkie takie same zachowania jak zwykłe funkcje wyzwalane kolejką. Mogą bezpiecznie wykonywać operacje we/wy, wykonywać operacje intensywnie korzystające z procesora CPU i używać wielu wątków. Ponieważ wyzwalacze aktywności są bezstanowe, mogą swobodnie skalować w poziomie do nieograniczonej liczby maszyn wirtualnych.

Funkcje jednostki są również wykonywane w jednym wątku, a operacje są przetwarzane pojedynczo. Jednak funkcje jednostki nie mają żadnych ograniczeń dotyczących typu kodu, który może być wykonywany.

## <a name="concurrency-throttles"></a>Przepustnice współbieżności

Usługa Azure Functions obsługuje wykonywanie wielu funkcji jednocześnie w ramach jednego wystąpienia aplikacji. To równoczesne wykonanie pomaga zwiększyć równoległość i minimalizuje liczbę "zimnych uruchomień", które z czasem będzie świadczyć typowa aplikacja. Jednak wysoka współbieżność może wyczerpyć zasoby systemowe dla maszyn wirtualnych, takie jak połączenia sieciowe lub dostępna pamięć. W zależności od potrzeb aplikacji funkcji może być konieczne przepustnicy współbieżności na wystąpienie, aby uniknąć możliwości wyczerpania pamięci w sytuacjach wysokiego obciążenia.

Limity współbieżności funkcji działania, koordynatora i jednostki można skonfigurować w pliku **host.json.** Odpowiednie ustawienia `durableTask/maxConcurrentActivityFunctions` dotyczą funkcji `durableTask/maxConcurrentOrchestratorFunctions` działania oraz funkcji koordynatora i encji.

### <a name="functions-20"></a>Funkcje 2.0

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

### <a name="functions-1x"></a>Functions w wersji 1.x

```json
{
  "durableTask": {
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10
  }
}
```

W poprzednim przykładzie maksymalnie 10 funkcji koordynatora lub jednostki i 10 funkcji działania można uruchomić na jednej maszynie wirtualnej jednocześnie. Jeśli nie zostanie określony, liczba równoczesnych działań i wykonania funkcji koordynatora lub jednostki jest ograniczona do 10X liczba rdzeni na maszynie Wirtualnej.

> [!NOTE]
> Te ustawienia są przydatne do zarządzania użyciem pamięci i procesora CPU na jednej maszynie wirtualnej. Jednak po skalowaniu w poziomie między wieloma maszynami wirtualnymi każda maszyna wirtualna ma swój własny zestaw limitów. Tych ustawień nie można używać do kontrolowania współbieżności na poziomie globalnym.

## <a name="extended-sessions"></a>Sesje rozszerzone

Sesje rozszerzone to ustawienie, które przechowuje aranżacji i jednostek w pamięci, nawet po zakończeniu przetwarzania wiadomości. Typowy efekt włączania rozszerzonych sesji zmniejsza liczbę we/wy w stosunku do konta usługi Azure Storage i ogólnie zwiększa przepływność.

Sesje rozszerzone można włączyć, ustawiając `durableTask/extendedSessionsEnabled` je `true` w pliku **host.json.** Ustawienie `durableTask/extendedSessionIdleTimeoutInSeconds` może służyć do kontrolowania, jak długo bezczynna sesja będzie utrzymywana w pamięci:

**Funkcje 2.0**
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

**Funkcje 1.0**
```json
{
  "durableTask": {
    "extendedSessionsEnabled": true,
    "extendedSessionIdleTimeoutInSeconds": 30
  }
}
```

Istnieją dwa potencjalne wady tego ustawienia, aby być świadomym:

1. Istnieje ogólny wzrost użycia pamięci aplikacji funkcji.
2. Może to być ogólny spadek przepływności, jeśli istnieje wiele równoczesnych, krótkotrwałe wykonania funkcji orkiestra lub jednostki.

Na przykład, `durableTask/extendedSessionIdleTimeoutInSeconds` jeśli jest ustawiona na 30 sekund, a następnie krótkotrwałe orchestrator lub epizod funkcji jednostki, który wykonuje w mniej niż 1 sekundę nadal zajmuje pamięć przez 30 sekund. Jest również wliczany do przydziału, `durableTask/maxConcurrentOrchestratorFunctions` o którym mowa wcześniej, potencjalnie uniemożliwiając uruchamianie innych funkcji koordynatora lub jednostki.

Konkretne skutki rozszerzonych sesji na funkcje orkiestratora i jednostki są opisane w następnych sekcjach.

### <a name="orchestrator-function-replay"></a>Powtórka funkcji orkiestry

Jak wspomniano wcześniej, funkcje orkiestratora są odtwarzane przy użyciu zawartości **historii** tabeli. Domyślnie kod funkcji koordynatora jest odtwarzany za każdym razem, gdy partia wiadomości są usuwane z kolejki formantu. Nawet jeśli używasz fan-out, fan-in wzorzec i oczekuje na wszystkie `Task.WhenAll` zadania do `context.df.Task.all` wykonania (na przykład przy użyciu w .NET lub JavaScript), nie będzie powtórki, które występują jako partie odpowiedzi zadań są przetwarzane w czasie. Gdy sesje rozszerzone są włączone, wystąpienia funkcji koordynatora są przechowywane w pamięci dłużej i nowe wiadomości mogą być przetwarzane bez pełnej powtórki historii.

Poprawa wydajności rozszerzonych sesji jest najczęściej obserwowana w następujących sytuacjach:

* Gdy istnieje ograniczona liczba wystąpień aranżacji uruchomionych jednocześnie.
* Gdy aranżacji mają dużą liczbę akcji sekwencyjnych (np. setki wywołań funkcji działania), które kończą się szybko.
* Gdy aranżacja fan-out i fan-in dużą liczbę działań, które kończą się w tym samym czasie.
* Gdy funkcje aranżatora trzeba przetwarzać duże komunikaty lub wykonać wszelkie przetwarzania danych intensywnie korzystających z procesora CPU.

We wszystkich innych sytuacjach zazwyczaj nie jest zauważalna poprawa wydajności dla funkcji koordynatora.

> [!NOTE]
> Te ustawienia powinny być używane tylko po w pełni opracowany i przetestowany funkcję koordynatora. Domyślne zachowanie agresywnej powtórki może być przydatne do wykrywania [ograniczeń kodu funkcji programu orchestrator](durable-functions-code-constraints.md) w czasie opracowywania i dlatego jest domyślnie wyłączona.

### <a name="entity-function-unloading"></a>Rozładunek funkcji jednostki

Funkcje jednostki przetwarzają do 20 operacji w jednej partii. Jak tylko jednostka kończy przetwarzanie partii operacji, utrzymuje się jego stan i zwalnia z pamięci. Można opóźnić zwolnienie jednostek z pamięci przy użyciu ustawienia sesji rozszerzonych. Jednostki nadal utrwalić ich zmiany stanu jak poprzednio, ale pozostają w pamięci przez skonfigurowany okres czasu, aby zmniejszyć liczbę obciążeń z usługi Azure Storage. Ta redukcja obciążeń z usługi Azure Storage może poprawić ogólną przepływność często używanych jednostek.

## <a name="performance-targets"></a>Cele dotyczące wydajności

Planując użycie funkcji trwałych dla aplikacji produkcyjnej, należy wziąć pod uwagę wymagania dotyczące wydajności na wczesnym etapie procesu planowania. W tej sekcji opisano niektóre podstawowe scenariusze użycia i oczekiwane maksymalne liczby przepływności.

* **Wykonanie działania sekwencyjnego:** W tym scenariuszu opisano funkcję koordynatora, która uruchamia serię funkcji działania jeden po drugim. Najbardziej przypomina [próbkę łańcucha funkcji.](durable-functions-sequence.md)
* **Wykonanie działania równoległego:** W tym scenariuszu opisano funkcję koordynatora, która wykonuje wiele funkcji działania równolegle przy użyciu [fan-out, fan-in](durable-functions-cloud-backup.md) wzorzec.
* **Przetwarzanie odpowiedzi równoległej:** Ten scenariusz jest drugą połową [wzorca fan-out, fan-in.](durable-functions-cloud-backup.md) Koncentruje się na wydajności fan-in. Należy pamiętać, że w przeciwieństwie do fan-out, fan-in odbywa się przez wystąpienie funkcji pojedynczego koordynatora i dlatego można uruchomić tylko na jednej maszynie Wirtualnej.
* **Przetwarzanie zdarzeń zewnętrznych:** Ten scenariusz reprezentuje wystąpienie funkcji pojedynczego koordynatora, które czeka na [zdarzenia zewnętrzne](durable-functions-external-events.md), po jednym na raz.
* **Przetwarzanie operacji jednostki:** Ten scenariusz sprawdza, jak szybko _pojedyncza_ [jednostka Counter](durable-functions-entities.md) może przetwarzać stały strumień operacji.

> [!TIP]
> W przeciwieństwie do wyjścia z wentylatora, operacje wchodzania są ograniczone do jednej maszyny Wirtualnej. Jeśli aplikacja używa fan-out, fan-in wzór i jesteś zaniepokojony wydajności wentylatora, należy rozważyć podziału funkcji działania fan-out na wiele [pod-aranżacji](durable-functions-sub-orchestrations.md).

W poniższej tabeli przedstawiono oczekiwane *maksymalne* liczby przepływności dla wcześniej opisanych scenariuszy. "Wystąpienie" odnosi się do pojedynczego wystąpienia funkcji koordynatora uruchomionego na pojedynczej małej maszynie wirtualnej[(A1)](../../virtual-machines/sizes-previous-gen.md)w usłudze Azure App Service. We wszystkich przypadkach zakłada się, że [sesje rozszerzone](#orchestrator-function-replay) są włączone. Rzeczywiste wyniki mogą się różnić w zależności od pracy procesora CPU lub we/wy wykonywanej przez kod funkcji.

| Scenariusz | Maksymalna przepływność |
|-|-|
| Wykonywanie działań sekwencyjnych | 5 działań na sekundę, na wystąpienie |
| Równoległe wykonywanie aktywności (wyjście wentylatora) | 100 działań na sekundę, na wystąpienie |
| Przetwarzanie odpowiedzi równoległej (wlaczynie) | 150 odpowiedzi na sekundę, na wystąpienie |
| Przetwarzanie zdarzeń zewnętrznych | 50 zdarzeń na sekundę na wystąpienie |
| Przetwarzanie operacji jednostki | 64 operacje na sekundę |

> [!NOTE]
> Liczby te są aktualne od wersji 1.4.0 (GA) rozszerzenia funkcje trwałe. Liczby te mogą się zmieniać w miarę dojrzewania funkcji i optymalizacji.

Jeśli nie widzisz oczekiwanych numerów przepływności, a użycie procesora CPU i pamięci wydaje się zdrowe, sprawdź, czy przyczyna jest związana [ze zdrowiem konta magazynu](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance). Rozszerzenie funkcje trwałe można umieścić znaczne obciążenie na koncie usługi Azure Storage i wystarczająco wysokie obciążenia może spowodować ograniczanie konta magazynu.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o odzyskiwaniu po awarii i dystrybucji geograficznej](durable-functions-disaster-recovery-geo-distribution.md)

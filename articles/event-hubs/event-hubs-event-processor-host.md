---
title: Odbieranie zdarzeń za pomocą hosta procesora zdarzeń — usługa Azure Event Hubs | Dokumentacja firmy Microsoft
description: W tym artykule opisano hosta procesora zdarzeń w usługi Azure Event Hubs, co upraszcza zarządzanie tworzenie punktów kontrolnych, dzierżawienie i odczytywania zdarzeń jon równoległych.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 01/10/2020
ms.author: shvija
ms.openlocfilehash: 414179d62970315a7575be0411bf1cb152349fdc
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162297"
---
# <a name="event-processor-host"></a>Host procesora zdarzeń
> [!NOTE]
> Ten artykuł ma zastosowanie do starej wersji usługi Azure Event Hubs SDK. Aby dowiedzieć się, jak przeprowadzić migrację kodu do nowszej wersji zestawu SDK, zobacz te przewodniki dotyczące migracji. 
> - [.NET](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MIGRATIONGUIDE.md)
> - [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/migration-guide.md)
> - [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md)
> - [Skrypt Java](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/migrationguide.md)
>
> Ponadto zobacz [równoważenia obciążenia partycji w wielu wystąpieniach aplikacji](event-processor-balance-partition-load.md).

Azure Event Hubs to usługa do pozyskiwania zaawansowanych danych telemetrycznych, która umożliwia strumieniowe przesyłanie milionów zdarzeń przy niskich kosztach. W tym artykule opisano sposób korzystania z zdarzeń pozyskiwanych za pomocą *hosta procesora zdarzeń* (EPH); inteligentny Agent konsumencki, który upraszcza zarządzanie czytnikami punktów kontrolnych, dzierżaw i zdarzeń równoległych.  

Klucz skalowanie dla usługi Event Hubs jest podzielone na partycje konsumentów. W przeciwieństwie do wzorca [konkurujących odbiorców](https://msdn.microsoft.com/library/dn568101.aspx) , partycjonowany wzorzec klienta umożliwia wysoką skalowalność, usuwając wąskie gardła rywalizacji i ułatwiając zakończenie równoległości.

## <a name="home-security-scenario"></a>Scenariusz domu zabezpieczeń

Jako przykładowy scenariusz należy wziąć pod uwagę firmy macierzystego zabezpieczeń, która monitoruje domów 100 000. Co minutę, otrzymuje dane od różnych czujników, takich jak wykrywanie ruchu, czujnik otwórz drzwi lub nowym oknie, wykrywanie break szkła, itp., które są zainstalowane w każdym głównej. Firma udostępnia witryny sieci web dla mieszkańców monitorować aktywność ich DOM w czasie zbliżonym do rzeczywistego.

Każdy czujnik wypycha dane do Centrum zdarzeń. Centrum zdarzeń jest skonfigurowany z 16 partycji. Na koniec konsumencki należy mechanizm, który może odczytywać te zdarzenia, skonsolidować je (filtrować, agregować itp.) oraz zrzutu agregacji do magazynu obiektów blob, który jest następnie przekazywany do strony sieci web przyjazna dla użytkownika.

## <a name="write-the-consumer-application"></a>Napisanie aplikacji klienta

Podczas projektowania klienta w środowisku rozproszonym, scenariusz musi obsługiwać następujące wymagania:

1. **Skala:** Utwórz wielu odbiorców, z których każdy konsument przejmuje prawo do odczytu z kilku Event Hubs partycji.
2. **Równoważenie obciążenia:** Dynamiczne zwiększanie lub zmniejszanie liczby klientów. Na przykład po dodaniu nowego typu czujnika (na przykład wykrywanie tlenku węgla) do każdej strony głównej zwiększa liczbę zdarzeń. W takiej sytuacji — operator (człowieka) powoduje zwiększenie liczby wystąpień odbiorców. Następnie pulę odbiorców można ponownie zrównoważyć liczby partycji są właścicielami, aby udostępnić obciążenia nowo dodanych użytkowników.
3. **Bezproblemowe wznowienie w przypadku awarii:** Jeśli odbiorca (**konsument a**) zakończy się niepowodzeniem (na przykład w maszynie wirtualnej, na której znajdują się nieoczekiwane awarie klienta), inni klienci muszą mieć możliwość pobrania partycji należących do **konsumenta a** i kontynuować. Ponadto punkt kontynuacji, nazywany punktem *kontrolnym* lub *przesunięciem*, powinien znajdować się w dokładnie określonym punkcie, w którym **odbiorca** nie zakończył się niepowodzeniem lub nieco przed nim.
4. **Korzystaj z zdarzeń:** Mimo że poprzednie trzy punkty zajmują się zarządzaniem konsumentem, musi istnieć kod, który umożliwia korzystanie z tych zdarzeń i jest bardziej użyteczny dla niego. na przykład Agreguj go i Przekaż do magazynu obiektów BLOB.

Zamiast tworzyć własne rozwiązanie, Event Hubs udostępnia tę funkcję za pomocą interfejsu [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) oraz klasy [klasy eventprocessorhost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) .

## <a name="ieventprocessor-interface"></a>Interfejsu IEventProcessor

Pierwsze, zużywające aplikacje implementują interfejs [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) , który ma cztery metody: [openAsync, CloseAsync, ProcessErrorAsync i ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor?view=azure-dotnet#methods). Ten interfejs zawiera rzeczywisty kod na korzystanie ze zdarzeń, które wysyła usługi Event Hubs. Poniższy kod pokazuje implementację proste:

```csharp
public class SimpleEventProcessor : IEventProcessor
{
    public Task CloseAsync(PartitionContext context, CloseReason reason)
    {
       Console.WriteLine($"Processor Shutting Down. Partition '{context.PartitionId}', Reason: '{reason}'.");
       return Task.CompletedTask;
    }

    public Task OpenAsync(PartitionContext context)
    {
       Console.WriteLine($"SimpleEventProcessor initialized. Partition: '{context.PartitionId}'");
       return Task.CompletedTask;
     }

    public Task ProcessErrorAsync(PartitionContext context, Exception error)
    {
       Console.WriteLine($"Error on Partition: {context.PartitionId}, Error: {error.Message}");
       return Task.CompletedTask;
    }

    public Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
    {
       foreach (var eventData in messages)
       {
          var data = Encoding.UTF8.GetString(eventData.Body.Array, eventData.Body.Offset, eventData.Body.Count);
             Console.WriteLine($"Message received. Partition: '{context.PartitionId}', Data: '{data}'");
       }
       return context.CheckpointAsync();
    }
}
```

Następnie Utwórz wystąpienie wystąpienia [klasy eventprocessorhost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) . W zależności od przeciążenia podczas tworzenia wystąpienia [klasy eventprocessorhost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) w Konstruktorze są używane następujące parametry:

- **hostname:** Nazwa każdego wystąpienia odbiorcy. Każde wystąpienie elementu **klasy eventprocessorhost** musi mieć unikatową wartość tej zmiennej w grupie odbiorców, dlatego nie należy nakodować tej wartości.
- **eventHubPath:** Nazwa centrum zdarzeń.
- **consumerGroupName:** Event Hubs używa **$default** jako nazwy domyślnej grupy odbiorców, ale dobrym sposobem jest utworzenie grupy odbiorców dla określonego aspektu przetwarzania.
- **eventHubConnectionString:** Parametry połączenia z centrum zdarzeń, które można pobrać z Azure Portal. Te parametry połączenia powinny mieć uprawnienia do **nasłuchiwania** w centrum zdarzeń.
- **storageConnectionString:** Konto magazynu używane na potrzeby wewnętrznego zarządzania zasobami.

Na koniec użytkownicy rejestrują wystąpienie [klasy eventprocessorhost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) za pomocą usługi Event Hubs. Rejestrowanie klasy procesora zdarzeń z wystąpieniem klasy EventProcessorHost rozpoczyna przetwarzanie zdarzeń. Rejestracja instruuje usługę Event Hubs, aby spodziewać się, że aplikacja konsumencka korzysta z zdarzeń z niektórych partycji i wywołuje kod implementacji [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) za każdym razem, gdy wypycha zdarzenia do użycia. 


### <a name="example"></a>Przykład

Na przykład Wyobraźmy sobie, że istnieją 5 maszyn wirtualnych (VM) w wersji dedykowanej do używania zdarzenia i prostą aplikację konsolową w każdej maszynie Wirtualnej, która rzeczywiste zużycie działa. Każda aplikacja konsolowa tworzy jedno wystąpienie [klasy eventprocessorhost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) i rejestruje je w usłudze Event Hubs.

W tym przykładowym scenariuszu Załóżmy, że 16 partycji są przydzielono do 5 wystąpień **klasy eventprocessorhost** . Niektóre wystąpienia **klasy eventprocessorhost** mogą być właścicielami kilku innych partycji niż inne. Dla każdej partycji, do której należy wystąpienie **klasy eventprocessorhost** , tworzy wystąpienie klasy `SimpleEventProcessor`. W związku z tym istnieje 16 wystąpień `SimpleEventProcessor` ogólnych, z jednym przypisanym do każdej partycji.

Na poniższej liście podsumowano w tym przykładzie:

- 16 partycje usługi event Hubs.
- 5 maszyn wirtualnych, aplikacji konsumentów 1 (na przykład Consumer.exe) w każdej maszynie Wirtualnej.
- Zarejestrowane 5 wystąpień EPH 1 w każdej maszynie Wirtualnej przez Consumer.exe.
- 16 `SimpleEventProcessor` obiektów utworzonych przez wystąpienia 5 EPH.
- 1 aplikacja Consumer. exe może zawierać 4 `SimpleEventProcessor` obiektów, ponieważ wystąpienie 1 EPH może mieć 4 partycje.

## <a name="partition-ownership-tracking"></a>Własność partycji śledzenia

Własność partycji do wystąpienia EPH (lub konsumenta) są śledzone za pomocą konta usługi Azure Storage, który jest udostępniany dla śledzenia. Można wizualizować śledzenia jako prostej tabeli, w następujący sposób. Możesz zobaczyć rzeczywiste wdrożenie, sprawdzając obiektów blob w ramach konta magazynu podanego:

| **Nazwa grupy konsumentów** | **Identyfikator partycji** | **Nazwa hosta (właściciel)** | **Czas trwania dzierżawy (lub własności)** | **Przesunięcie na partycję (punkt kontrolny)** |
| --- | --- | --- | --- | --- |
| $Default | 0 | VM3\_konsumenta | 2018-04-15T01:23:45 | 156 |
| $Default | 1 | VM4\_konsumenta | 2018-04-15T01:22:13 | 734 |
| $Default | 2 | VM0\_konsumenta | 2018-04-15T01:22:56 | 122 |
| : |   |   |   |   |
| : |   |   |   |   |
| $Default | 15 | VM3\_konsumenta | 2018-04-15T01:22:56 | 976 |

W tym miejscu każdy host uzyskuje własność partycji przez określony czas (czas trwania dzierżawy). Jeśli host kończy się niepowodzeniem (maszyna wirtualna zostanie zamknięte), dzierżawa wygasa. Inne hosty spróbować uzyskać prawo własności partycji i jednym z hostów zakończy się pomyślnie. Ten proces przywraca dzierżawy na partycji przy użyciu nowego właściciela. W ten sposób w danym momencie tylko jeden czytnik może odczytywać dane z dowolnego danej partycji w obrębie grupy odbiorców.

## <a name="receive-messages"></a>Odbieranie komunikatów

Każde wywołanie [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) dostarcza kolekcję zdarzeń. Jest odpowiedzialny za obsługę następujących zdarzeń. Aby upewnić się, że host procesora przetwarza każdy komunikat co najmniej raz, należy napisać swój własny kod Kontynuuj ponawianie próby. Należy zachować ostrożność w przypadku skażonych komunikatów.

Zalecane jest wykonanie czynności relatywnie szybko; oznacza to czy jako nieco przetwarzania, jak to możliwe. Zamiast tego należy użyć grup odbiorców. Jeśli zachodzi potrzeba zapisu do magazynu i przeprowadzenia pewnych routingu, lepiej jest używać dwóch grup odbiorców i mieć dwie implementacje [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) , które są uruchamiane oddzielnie.

W pewnym momencie podczas przetwarzania Twojego zechcieć do śledzenia co użytkownik przeczytał i zakończone. Śledzenia ma krytyczne znaczenie, jeśli należy ponownie uruchomić odczytu, więc nie można wrócić do początku strumienia. [Klasy eventprocessorhost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) upraszcza to śledzenie przy użyciu *punktów kontrolnych*. Punkt kontrolny to lokalizacji lub przesunięcie w danej partycji w ramach danej grupy odbiorców, w tym momencie możesz ustali, że zostały przetworzone komunikaty. Oznaczanie punktu kontrolnego w **klasy eventprocessorhost** jest realizowane przez wywołanie metody [CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) w obiekcie [PartitionContext](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext) . Ta operacja jest wykonywana w metodzie [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) , ale można ją również wykonać w [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync).

## <a name="checkpointing"></a>Tworzenie punktów kontrolnych

Metoda [CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) ma dwa przeciążenia: pierwszy, bez parametrów, punkty kontrolne do najwyższego przesunięcia zdarzenia w kolekcji zwróconej przez [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync). To przesunięcie jest znakiem "górny"; założono, że zostały przetworzone wszystkie ostatnie zdarzenia podczas jego wywoływania. W ten sposób możesz użyć tej metody, należy pamiętać, może wywołać go po kodzie przetwarzanie zdarzeń zostały zwrócone. Drugie Przeciążenie pozwala określić wystąpienie [EVENTDATA](/dotnet/api/microsoft.azure.eventhubs.eventdata) dla punktu kontrolnego. Ta metoda umożliwia użycie innego rodzaju znak wodny do punktu kontrolnego. Przy użyciu znaku wodnego, można zaimplementować znacznik "niska water": przetworzeniu najniższą zdarzenia Sekwencyjna wiesz na pewno. To przeciążenie zapewnia elastyczność zarządzania przesunięcia.

Gdy punkt kontrolny jest wykonywany, plik JSON z informacjami specyficznymi dla partycji (w konkretnym przesunięciu) jest zapisywana na koncie magazynu dostarczonym w konstruktorze do [klasy eventprocessorhost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Plik ten jest stale aktualizowany. Warto wziąć pod uwagę punkty kontrolne w kontekście — będzie zalecane przerywającej połączenie programem punktu kontrolnego każdej wiadomości. Konto magazynu używane do tworzenia punktów kontrolnych, prawdopodobnie nie będzie obsługiwać tego obciążenia, ale co ważniejsze tworzenie punktów kontrolnych każdego zdarzenia jest wskaźnikiem umieszczonych w kolejce wzorzec przesyłania komunikatów dotyczących, dla którego kolejki usługi Service Bus może być lepszym rozwiązaniem niż Centrum zdarzeń. Ideą usługi Event Hubs jest, skorzystaj z "co najmniej raz" dostarczania na dużą skalę. Wprowadzając swoje systemy klienckie idempotentne, można łatwo odzyskać sprawność po awarii lub ponowne uruchomienie wynik w te same zdarzenia, które są odbierane wiele razy.

## <a name="thread-safety-and-processor-instances"></a>Wątek wystąpień bezpieczeństwa i procesora

Domyślnie [klasy eventprocessorhost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) jest bezpieczny wątkowo i zachowuje się synchronicznie w odniesieniu do wystąpienia elementu [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor). Po nadejściu zdarzeń dla partycji [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) jest wywoływana w wystąpieniu **IEventProcessor** dla tej partycji i będzie blokować dalsze wywołania **ProcessEventsAsync** dla partycji. Kolejne komunikaty i wywołania w kolejce **ProcessEventsAsync** w tle, gdy pompa komunikatów będzie działać w tle w innych wątkach. Bezpieczeństwo wątków, to eliminuje potrzebę kolekcje obsługujące wielowątkowość i znacznie zwiększa wydajność.

## <a name="shut-down-gracefully"></a>Zamknięcie

Na koniec [klasy eventprocessorhost. UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) włącza czyste zamknięcie wszystkich czytników partycji i zawsze powinna być wywoływana podczas zamykania wystąpienia [klasy eventprocessorhost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Niewykonanie tej operacji może spowodować opóźnienia podczas uruchamiania innych wystąpień **klasy eventprocessorhost** ze względu na wygaśnięcie dzierżawy i konflikty epoki. Zarządzanie epoką zostało szczegółowo omówione w sekcji [epoki](#epoch) artykułu. 

## <a name="lease-management"></a>Zarządzanie dzierżawą
Rejestrowanie klasy procesora zdarzeń z wystąpieniem klasy EventProcessorHost rozpoczyna przetwarzanie zdarzeń. Wystąpienie hosta uzyskuje dzierżawy na partycji Centrum zdarzeń, prawdopodobnie Przechwytywanie niektóre z innych wystąpień hosta w taki sposób, że na partycji równomiernego rozłożenia zbieżna dla wartości we wszystkich wystąpieniach hosta. Dla każdej dzierżawy partycji wystąpienie hosta tworzy wystąpienie klasy procesora zdarzeń podana, a następnie odbiera zdarzenia z tej partycji i przekazuje je do wystąpienia procesora zdarzeń. Poproś o dodanie większej liczby wystąpień oraz liczby możliwych dzierżaw jest pobierany, EventProcessorHost ostatecznie równoważy obciążenia na wszystkich użytkowników.

Jak wyjaśniono wcześniej, tabela śledzenia znacznie upraszcza charakter automatycznego skalowania [klasy eventprocessorhost. UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync). Wystąpienie **klasy eventprocessorhost** rozpoczyna się od wielu dzierżaw, a następnie rozpoczyna odczytywanie zdarzeń. Gdy dzierżawy zbliżają się do wygaśnięcia, **klasy eventprocessorhost** próbuje odnowić je, umieszczając rezerwację. Jeśli dzierżawa jest dostępna do odnowienia, procesor kontynuuje odczyt, ale jeśli nie jest, czytnik jest zamknięty i [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync) jest wywoływana. **CloseAsync** to dobry moment, aby przeprowadzić końcowe czyszczenie tej partycji.

**Klasy eventprocessorhost** zawiera właściwość [PartitionManagerOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.partitionmanageroptions) . Ta właściwość umożliwia kontrolę nad zarządzaniem dzierżawy. Ustaw te opcje przed zarejestrowaniem implementacji [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) .

## <a name="control-event-processor-host-options"></a>Sterowanie opcjami hosta procesora zdarzeń

Ponadto jedno Przeciążenie [RegisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync?view=azure-dotnet#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) przyjmuje obiekt [EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync?view=azure-dotnet#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) jako parametr. Użyj tego parametru, aby kontrolować zachowanie [klasy eventprocessorhost. UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) . [EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions) definiuje cztery właściwości i jedno zdarzenie:

- [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize): maksymalny rozmiar kolekcji, która ma zostać odebrana w wywołaniu [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync). Ten rozmiar nie jest minimalny, maksymalny rozmiar. Jeśli jest mniej komunikatów do odebrania, **ProcessEventsAsync** wykonuje się za pomocą tak dużo, jak były dostępne.
- [PrefetchCount](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.prefetchcount): wartość używana przez podstawowy kanał AMQP do określenia górnego limitu liczby komunikatów, które powinien odebrać klient. Ta wartość powinna być większa lub równa [maxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize).
- [InvokeProcessorAfterReceiveTimeout](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.invokeprocessorafterreceivetimeout): Jeśli ten parametr ma **wartość true**, [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) jest wywoływana, gdy bazowe wywołanie do odbierania zdarzeń na partycji przekracza limit czasu. Ta metoda jest przydatna do podejmowania akcji opartych na czasie w okresach braku aktywności na partycji.
- [InitialOffsetProvider](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.initialoffsetprovider): umożliwia określenie wskaźnika funkcji lub wyrażenia lambda, które jest wywoływane, aby zapewnić początkowe przesunięcie, gdy czytnik rozpocznie Odczytywanie partycji. Bez określenia tego przesunięcia czytnik zaczyna się od najstarszego zdarzenia, chyba że plik JSON z przesunięciami został już zapisany na koncie magazynu dostarczonym do konstruktora [klasy eventprocessorhost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) . Ta metoda jest przydatna, jeśli chcesz zmienić zachowanie uruchamiania czytnika. Po wywołaniu tej metody parametr obiektu zawiera identyfikator partycji, dla którego jest uruchamiany czytelnika.
- [ExceptionReceivedEventArgs](/dotnet/api/microsoft.azure.eventhubs.processor.exceptionreceivedeventargs): umożliwia otrzymywanie powiadomień o wszelkich podstawowych wyjątkach występujących w [klasy eventprocessorhost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Jeśli elementy nie działają zgodnie z oczekiwaniami, to zdarzenie jest dobrym miejscem do rozpoczęcia wyszukiwania.

## <a name="epoch"></a>EPOCH

Oto jak działa Epoka odbioru:

### <a name="with-epoch"></a>Z epoką
Epoka jest unikatowym identyfikatorem (wartość epoki) używaną przez usługę, aby wymusić własność partycji/dzierżawy. Można utworzyć odbiornik oparty na epoki przy użyciu metody [CreateEpochReceiver](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createepochreceiver?view=azure-dotnet) . Ta metoda tworzy odbiornik oparty na epoki. Odbiorca jest tworzony dla określonej partycji centrum zdarzeń z określonej grupy odbiorców.

Funkcja Epoka zapewnia użytkownikom możliwość zapewnienia, że w dowolnym momencie w grupie odbiorców istnieje tylko jeden odbiornik z następującymi regułami:

- Jeśli nie ma żadnych istniejących odbiorników w grupie odbiorców, użytkownik może utworzyć odbiorcę z dowolną wartością epoki.
- Jeśli istnieje odbiornik z wartością epoki E1 i zostanie utworzony nowy odbiornik z wartością epoki E2, gdzie E1 < = E2, odbiorca z E1 zostanie odłączony automatycznie, odbiornik z E2 zostanie utworzony pomyślnie.
- Jeśli istnieje odbiornik z wartością epoki E1 i nowy odbiorca jest tworzony z wartością epoki E2, gdzie E1 > E2, a następnie utworzenie E2 z zakończeniem się niepowodzeniem z powodu błędu: odbiornik z epoką.

### <a name="no-epoch"></a>Brak epoki
Tworzysz odbiornik oparty na protokole epoki przy użyciu metody [OnReceive](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createreceiver?view=azure-dotnet) . 

W przypadku przetwarzania strumieniowego istnieją pewne scenariusze, w których użytkownicy mogą utworzyć wielu odbiorników w pojedynczej grupie odbiorców. Aby wspierać takie scenariusze, firma Microsoft może utworzyć odbiorcę bez epoki. w takim przypadku zezwolimy na maksymalnie 5 współbieżnych odbiorców w grupie konsumentów.

### <a name="mixed-mode"></a>Tryb mieszany
Nie zalecamy użycia aplikacji, gdy tworzysz odbiornik z epoką, a następnie przełączasz się do opcji No-Epoka lub vice versa w tej samej grupie odbiorców. Jednak w przypadku wystąpienia tego zachowania usługa obsługuje je przy użyciu następujących reguł:

- Jeśli istnieje już odbiornik z epoką E1 i aktywnie odbieramy zdarzenia i zostanie utworzony nowy odbiorca bez epoki, tworzenie nowego odbiornika zakończy się niepowodzeniem. Odbiorniki epok zawsze mają pierwszeństwo w systemie.
- Jeśli został już utworzony odbiornik z epoką E1 i nastąpiło odłączenie, a nowy odbiorca jest tworzony bez epoki na nowym MessagingFactory, tworzenie nowego odbiorcy powiedzie się. W tym miejscu występuje zastrzeżenia, które system wykrywa "odłączenie odbiorcy" po ~ 10 minut.
- Jeśli istnieje co najmniej jeden odbiornik, który został utworzony bez epoki, a nowy odbiorca jest tworzony z epoką E1, wszystkie stare odbiorniki są rozłączone.


> [!NOTE]
> Zalecamy używanie różnych grup odbiorców dla aplikacji korzystających z epoki i dla tych, które nie używają epoki, aby uniknąć błędów. 


## <a name="next-steps"></a>Następne kroki

Teraz, kiedy znasz już hosta procesora zdarzeń, zobacz następujące artykuły, aby dowiedzieć się więcej na temat usługi Event Hubs:

- Rozpoczynanie pracy z usługą Event Hubs
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [JavaScript](get-started-java-send-v2.md)
* [Przewodnik programowania w usłudze Event Hubs](event-hubs-programming-guide.md)
* [Availability and consistency in Event Hubs](event-hubs-availability-and-consistency.md) (Dostępność i spójność w usłudze Event Hubs)
* [Event Hubs — często zadawane pytania](event-hubs-faq.md)
* [Przykłady Event Hubs w witrynie GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples)

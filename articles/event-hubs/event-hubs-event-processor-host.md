---
title: Co to jest hosta procesora zdarzeń Azure Event Hubs i dlaczego warto korzystać z jego | Dokumentacja firmy Microsoft
description: Omówienie i wprowadzenie do hosta procesora zdarzeń Azure Event Hubs
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
ms.date: 06/26/2018
ms.author: shvija
ms.openlocfilehash: 4907004f4bb88cf0fe9fb799cab236ebf98bba7a
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37133016"
---
# <a name="azure-event-hubs-event-processor-host-overview"></a>Omówienie usługi Azure hosta procesora zdarzeń centra zdarzeń

Usługa Azure Event Hubs to usługa wprowadzanie zaawansowanych telemetrii, która może służyć do strumienia miliony zdarzeń przy niskich kosztach. W tym artykule opisano sposób wykorzystywania pozyskiwane zdarzenia przy użyciu *hosta procesora zdarzeń* (EPH); inteligentny agent odbiorcy upraszczającym Zarządzanie punkty kontrolne, dzierżawę i czytników zdarzeń równoległych.  

Klucz do skalowania usługi Event hubs jest partycjonowany konsumentów. Contrast do [konkurujących konsumentów](http://msdn.microsoft.com/en-us/library/dn568101.aspx) wzorcu partycjonowanego wzorca odbiorców umożliwia dużą skalę przez usunięcie wąskich gardeł rywalizacji i ułatwienia równoległości pełnego.

## <a name="home-security-scenario"></a>Scenariusz domu zabezpieczeń

Jako przykładowy scenariusz należy wziąć pod uwagę firmy macierzystego zabezpieczeń, który monitoruje domach 100 000. Co minutę, pobiera dane z różnych czujników, takich jak czujnik ruchu, czujnik otwórz drzwi/okna, detektora podziału awaryjne, itp., które są zainstalowane w każdym głównej. Firma zapewnia witryny sieci web mieszkańców monitorować aktywność ich głównej w najbliższym czasie rzeczywistym.

Każdy czujnik wypycha dane do Centrum zdarzeń. Centrum zdarzeń jest skonfigurowany z 16 partycji. Na końcu odbierającą należy mechanizm, który może odczytywać te zdarzenia, konsolidować je (filtru, agregacji, itp.) i zrzutu agregacji do obiektu blob magazynu, który następnie jest zaprojektowana do strony sieci web przyjaznych dla użytkownika.

## <a name="write-the-consumer-application"></a>Pisanie aplikacji klienta

Podczas projektowania odbiorców w środowisku rozproszonym, scenariusz musi obsługiwać następujące wymagania:

1. **Skala:** tworzenie wielu klientów z każdego konsumenta przejmowanie na własność odczytu z kilku partycji usługi Event Hubs.
2. **Równoważenie obciążenia:** zwiększyć lub zmniejszyć konsumentów dynamicznie. Na przykład po dodaniu nowego typu czujnika (na przykład detektora tlenku węgla) do każdej strony głównej, powoduje zwiększenie liczby zdarzeń. W takim przypadku operatora (człowieka) zwiększa liczbę wystąpień użytkownika. Następnie puli konsumentów można przeprowadzić ponowne równoważenie liczby partycji są właścicielami, aby udostępnić obciążenia nowo dodanych użytkowników.
3. **Bezproblemowe wznowienia na awarie:** Jeśli konsumenta (**konsumenta A**) kończy się niepowodzeniem (na przykład hostingu maszyn wirtualnych konsumenta nieoczekiwanie ulegnie awarii), innym klientom musi być może podnieść partycje własnością **konsumenta A** i kontynuować. Ponadto kontynuacji punktu, nazywany *punktu kontrolnego* lub *przesunięcie*, powinien być dokładnie punktu, w którym **konsumenta A** nie powiodło się, lub przed nim nieco.
4. **Korzystanie ze zdarzeń:** podczas poprzedniego trzy punkty dotyczą zarządzania konsumenta, musi być kodem korzystanie ze zdarzeń czymś przydatne z nim; na przykład agregacji go i przekaż go do magazynu obiektów blob.

Zamiast tworzenia własnych rozwiązań dla tego, usługa Event Hubs zapewnia tę funkcję za pomocą [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) interfejsu i [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) klasy.

## <a name="ieventprocessor-interface"></a>Interfejs IEventProcessor

Po pierwsze, wykorzystywanie wdrożenie aplikacji [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) interfejs, który ma cztery metody: [OpenAsync, CloseAsync ProcessErrorAsync i ProcessEventsAsnyc](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor?view=azure-dotnet#methods). Ten interfejs zawiera kod na korzystanie ze zdarzeń, które wysyła centrów zdarzeń. Poniższy kod przedstawia implementację prostego:

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

Następnie należy utworzyć wystąpienia [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) wystąpienia. W zależności od przeciążenia, tworząc [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) wystąpienia w konstruktorze, są używane następujące parametry:

- **Nazwa hosta:** nazwę każdego wystąpienia użytkownika. Każde wystąpienie **EventProcessorHost** musi mieć unikatową wartość tej zmiennej w ramach grupy odbiorców, dlatego najlepiej nie twardego code tej wartości.
- **eventHubPath:** nazwę Centrum zdarzeń.
- **consumerGroupName:** usługa Event Hubs używa **$Default** jako nazwa domyślna grupa odbiorców, ale jest dobrym rozwiązaniem jest utworzenie grupy konsumentów dla Twojego określonej proporcji przetwarzania.
- **eventHubConnectionString:** parametry połączenia do Centrum zdarzeń, który można pobrać z portalu Azure. Ten ciąg połączenia powinny mieć **nasłuchiwania** uprawnienia do Centrum zdarzeń.
- **storageConnectionString:** konto magazynu używane do zarządzania zasobów wewnętrznych.

Ponadto klienci rejestrują [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) wystąpienie z usługi Event Hubs. Rejestrowanie powoduje, że usługi Event Hubs można oczekiwać, że aplikacja klienta wykorzystuje zdarzenia z niektórych z jej partycji i wywoływanie [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) implementacji kod zawsze, gdy jej wypychanie zdarzeń, aby korzystać z.

### <a name="example"></a>Przykład

Na przykład załóżmy, że istnieją 5 maszynach wirtualnych (VM) przeznaczona do używania zdarzeń i prostej aplikacji konsolowej w każdej maszyny Wirtualnej, która rzeczywiste zużycie działa. Każda aplikacja konsoli, następnie tworzy jeden [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) wystąpienia i rejestruje go z usługą Event Hubs.

W tym przykładowym scenariuszu Załóżmy, że 16 partycji są przydzielone do 5 **EventProcessorHost** wystąpień. Niektóre **EventProcessorHost** wystąpień może być właścicielem kilka partycje więcej niż inne. Dla każdej partycji **EventProcessorHost** właścicielem wystąpienia, tworzy wystąpienie `SimpleEventProcessor` klasy. W związku z tym istnieją 16 wystąpienia `SimpleEventProcessor` ogólny, z jedną przypisaną do każdej partycji.

Poniższa lista zawiera podsumowanie w tym przykładzie:

- 16 partycji usługi event Hubs.
- 5 maszyn wirtualnych, konsumenta 1 aplikacja (na przykład Consumer.exe) na każdej maszynie Wirtualnej.
- Zarejestrowane 5 wystąpień EPH 1 w każdej maszyny Wirtualnej przez Consumer.exe.
- 16 `SimpleEventProcessor` obiekty utworzone przez 5 wystąpień EPH.
- 1 aplikacja Consumer.exe może zawierać 4 `SimpleEventProcessor` obiektów, ponieważ 1 wystąpienie EPH może posiadać 4 partycji.

## <a name="partition-ownership-tracking"></a>Własność partycji śledzenia

Własność partycji do wystąpienia EPH (lub klientów) są śledzone za pomocą konta usługi Azure Storage, które jest dostępne w celu śledzenia. Można zwizualizować śledzenia jako prostą tabelę, w następujący sposób. Rzeczywista implementacja można wyświetlić, sprawdzając obiekty BLOB na koncie magazynu podana:

| **Nazwa grupy konsumentów** | **Identyfikator partycji:** | **Nazwa hosta (właściciela)** | **Czas dzierżawy (lub własność) nabyte** | **Przesunięcie w partycji (punkt kontrolny)** |
| --- | --- | --- | --- | --- |
| $Default | 0 | Konsument\_VM3 | 2018-04-15T01:23:45 | 156 |
| $Default | 1 | Konsument\_VM4 | 2018-04-15T01:22:13 | 734 |
| $Default | 2 | Konsument\_VM0 | 2018-04-15T01:22:56 | 122 |
| : |   |   |   |   |
| : |   |   |   |   |
| $Default | 15 | Konsument\_VM3 | 2018-04-15T01:22:56 | 976 |

W tym miejscu każdy host uzyskuje prawo własności do partycji przez określony czas (czas trwania dzierżawy). Jeśli host nie powiedzie się (maszyna wirtualna zostanie zamknięte), dzierżawa wygaśnie. Spróbuj pobrać własność partycji innych hostów i jednego z hostów zakończy się pomyślnie. Ten proces powoduje zresetowanie dzierżawy na partycji z nowego właściciela. Dzięki temu tylko jeden czytnik naraz może odczytywać wszystkie danej partycji w obrębie grupy odbiorców.

## <a name="receive-messages"></a>Odbieranie komunikatów

Każde wywołanie [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) zapewnia zbiór zdarzenia. Jest Twoje odpowiedzialność za obsługę tych zdarzeń. Zalecane jest wykonanie czynności stosunkowo szybkie; oznacza to czy jako małego przetwarzania, jak to możliwe. Zamiast tego należy użyć grup odbiorców. Jeśli musisz zapisać do magazynu i czy niektóre routingu, jest zazwyczaj lepiej dwie grupy konsumentów i mieć dwa [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) implementacji, które są uruchamiane oddzielnie.

W pewnym momencie podczas przetwarzania możesz do śledzenia co użytkownik przeczytał i zakończone. Śledzenia jest szczególnie ważne, jeśli należy ponownie uruchomić odczytu, więc nie powrotu do strumienia. [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) ułatwia to śledzenie z użyciem *punktów kontrolnych*. Punkt kontrolny jest lokalizacji lub przesunięcie w danej partycji w obrębie grupy odbiorców danego, na punktu, który jest spełniony, które zostały przetworzone komunikaty. Oznaczenie punktu kontrolnego w **EventProcessorHost** odbywa się przez wywołanie metody [CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) metoda [PartitionContext](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext) obiektu. Ta operacja odbywa się zazwyczaj w [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) metody, ale można również wykonać [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync).

## <a name="checkpointing"></a>Tworzenie punktów kontrolnych

[CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) metoda ma dwa przeciążenia: pierwszy, bez parametrów, punkty kontrolne do najwyższej przesunięcie zdarzeń w kolekcji zwróconej przez [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync). To przesunięcie jest znakiem "wodnym wysokiego poziomu"; zakłada się, że zostały przetworzone wszystkie ostatnie zdarzenia po jej wywołaniu. W ten sposób można użyć tej metody, należy pamiętać, mogą wywołać ją po zwrócił kodzie przetwarzania zdarzeń. Drugi przeciążenie umożliwia określenie [EventData](/dotnet/api/microsoft.azure.eventhubs.eventdata) wystąpienie do punktu kontrolnego. Ta metoda pozwala na użycie innego typu znaku wodnego do punktu kontrolnego. Z tego znaku wodnego, można zaimplementować znacznik "wodnym niskiego poziomu": przetworzeniu najniższa zdarzenia Sekwencyjna masz pewność. To przeciążenie zapewnia elastyczność w zarządzaniu przesunięcia.

Gdy punkt kontrolny jest wykonywane, to plik JSON o informacje specyficzne dla partycji (w szczególności przesunięcie), zostanie zapisany dostarczony w Konstruktorze do konta magazynu [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Plik ten jest stale aktualizowany. Należy wziąć pod uwagę tworzenie punktów kontrolnych w kontekście — byłoby zalecane przerywającej połączenie programem punktu kontrolnego każdej wiadomości. Konto magazynu używane przez punkty kontrolne prawdopodobnie nie będzie obsługiwać obciążenie, ale co ważniejsze tworzenie punktów kontrolnych każdego zdarzenia jest świadczy o kolejce wzorzec przesyłania komunikatów, dla którego kolejki usługi Service Bus może być lepszym rozwiązaniem niż Centrum zdarzeń. Ideą usługi Event Hubs jest pobrać "co najmniej raz" dostarczania na dużą skalę. Dokonując Twojej idempotentności systemy klienckie ułatwia odzyskiwanie po błędach lub ponowne uruchomienie tego wyniku w tej samej zdarzenia odbierane wiele razy.

## <a name="thread-safety-and-processor-instances"></a>Wątek wystąpień bezpieczeństwa i procesora

Domyślnie [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) jest bezpieczne wątku i działa w sposób synchroniczne względem wystąpienie [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor). W przypadku odebrania zdarzeń dla partycji, [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) jest wywoływana na **IEventProcessor** wystąpienia, dla którego partycji i blokuje kolejne wywołania do **ProcessEventsAsync**dla partycji. Kolejnych komunikatów i wywołania **ProcessEventsAsync** kolejki w tle jako przekazywanie komunikatów jest nadal uruchomione w tle na inne wątki. To bezpieczeństwo wątków eliminuje to potrzebę kolekcje obsługujące wielowątkowość i znacznie zwiększa wydajność.

## <a name="shut-down-gracefully"></a>Prawidłowe zamknięcie

Na koniec [EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) umożliwia zamknięcie czyste wszystkich partycji czytelników i powinna być zawsze wywoływana podczas zamykania wystąpienia [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Niepowodzenie w tym celu mogą powodować opóźnienia podczas uruchamiania innych wystąpień **EventProcessorHost** z powodu wygaśnięcie dzierżawy i epoki konfliktów. Zarządzanie epoki zostały szczegółowo opisane w tym [wpis w blogu](https://blogs.msdn.microsoft.com/gyan/2014/09/02/event-hubs-receiver-epoch/)

## <a name="lease-management"></a>Zarządzanie dzierżawy

Jak opisano wcześniej, tabela śledzenia jest znacznie ułatwione automatycznego skalowania rodzaj [EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync). Jako wystąpienie **EventProcessorHost** uruchomieniu uzyskuje tyle dzierżawy, jak to możliwe i rozpoczyna odczyt zdarzeń. Jako dzierżawy wkrótce wygasną **EventProcessorHost** próbuje odnowić ich przez umieszczenie rezerwacji. Jeśli dzierżawa jest dostępna dla odnowienia, procesor nadal odczytu, ale jeśli nie, proces czytający jest zamykany i [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync) jest wywoływana. **CloseAsync** jest odpowiedni moment, aby wykonać wszelkie Oczyszczanie końcowego dla tej partycji.

**EventProcessorHost** obejmuje [PartitionManagerOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.partitionmanageroptions) właściwości. Ta właściwość zapewnia kontrolę nad zarządzaniem dzierżawy. Ustaw dla tych opcji przed zarejestrowaniem Twojej [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) implementacji.

## <a name="control-event-processor-host-options"></a>Opcje kontroli hosta procesora zdarzeń

Ponadto jednego przeciążenia [RegisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync?view=azure-dotnet#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) przyjmuje [EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync?view=azure-dotnet#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) obiektu jako parametr. Ten parametr służy do sterowania zachowaniem [EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) samej siebie. [EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions) definiuje czterech właściwości i jedno zdarzenie:

- [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize): maksymalny rozmiar kolekcji, które chcesz otrzymywać w wywołaniu [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync). Ten rozmiar nie jest minimalnym maksymalny rozmiar. Jeśli ma mniejszą liczbę komunikatów do odebrania, **ProcessEventsAsync** wykonuje z tyle, ile były dostępne.
- [PrefetchCount](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.prefetchcount): wartość używana przez podstawowy kanał AMQP do określenia górny limit liczby komunikatów klient powinien zostać wyświetlony. Ta wartość powinna być większa lub równa [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize).
- [InvokeProcessorAfterReceiveTimeout](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.invokeprocessorafterreceivetimeout): Jeśli ten parametr ma **true**, [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) jest wywoływane, gdy upłynie limit czasu wywołania podstawowej, aby odbierać zdarzenia na partycji. Ta metoda jest przydatna do wykonywania działań na podstawie czasu podczas nieaktywności na partycji.
- [InitialOffsetProvider](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.initialoffsetprovider): włącza funkcji wskaźnika lambda wyrażenia lub należy ustawić, która jest wywoływana w celu zapewnienia początkowe przesunięcie, gdy czytnik rozpoczyna odczyt partycji. Bez określania to przesunięcie, czytnik rozpoczyna się od najstarszych zdarzeń, chyba, że plik JSON z przesunięciem został już zapisany na koncie magazynu do [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) konstruktora. Ta metoda jest przydatna, jeśli chcesz zmienić zachowanie uruchamiania czytnika. Po wywołaniu tej metody z parametrem obiektu zawiera identyfikator partycji, dla którego jest uruchamiany czytnika.
- [ExceptionReceivedEventArgs](/dotnet/api/microsoft.azure.eventhubs.processor.exceptionreceivedeventargs): pozwala na powiadomienie wyjątki podstawowej, które występują w [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Jeśli elementy nie działają zgodnie z oczekiwaniami, to zdarzenie jest dobrym miejscem, aby rozpocząć wyszukiwanie.

## <a name="next-steps"></a>Kolejne kroki

Teraz, kiedy znasz hosta procesora zdarzeń, zobacz następujące artykuły, aby dowiedzieć się więcej na temat usługi Event Hubs:

* Rozpocznij pracę od [samouczka dotyczącego usługi Event Hubs](event-hubs-dotnet-standard-getstarted-send.md)
* [Przewodnik programowania w usłudze Event Hubs](event-hubs-programming-guide.md)
* [Availability and consistency in Event Hubs](event-hubs-availability-and-consistency.md) (Dostępność i spójność w usłudze Event Hubs)
* [Event Hubs — często zadawane pytania](event-hubs-faq.md)
* [Przykłady centra zdarzeń w witrynie GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples)
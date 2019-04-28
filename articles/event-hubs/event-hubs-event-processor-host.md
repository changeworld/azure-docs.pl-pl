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
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 26f0abb48ba268f79167ed5d00e4f96d8b5e5998
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60821874"
---
# <a name="receive-events-from-azure-event-hubs-using-event-processor-host"></a>Odbieranie zdarzeń z usługi Azure Event Hubs za pomocą hosta procesora zdarzeń

Azure Event Hubs to usługa do pozyskiwania zaawansowanych danych telemetrycznych, która umożliwia strumieniowe przesyłanie milionów zdarzeń przy niskich kosztach. W tym artykule opisano sposób wykorzystywania odebrane zdarzenia przy użyciu *hosta procesora zdarzeń* (EPH); inteligentny agent odbiorcy, który ułatwia zarządzanie tworzenie punktów kontrolnych, dzierżawienie i czytników zdarzeń równoległych.  

Klucz skalowanie dla usługi Event Hubs jest podzielone na partycje konsumentów. W przeciwieństwie do [konkurujący odbiorcy](https://msdn.microsoft.com/library/dn568101.aspx) wzorzec, partycjonowanego wzorca odbiorców umożliwia dużej skali przez usunięcie wąskich gardeł rywalizacji o zasoby i ułatwienia równoległości typu end to end.

## <a name="home-security-scenario"></a>Scenariusz domu zabezpieczeń

Jako przykładowy scenariusz należy wziąć pod uwagę firmy macierzystego zabezpieczeń, która monitoruje domów 100 000. Co minutę, otrzymuje dane od różnych czujników, takich jak wykrywanie ruchu, czujnik otwórz drzwi lub nowym oknie, wykrywanie break szkła, itp., które są zainstalowane w każdym głównej. Firma udostępnia witryny sieci web dla mieszkańców monitorować aktywność ich DOM w czasie zbliżonym do rzeczywistego.

Każdy czujnik wypycha dane do Centrum zdarzeń. Centrum zdarzeń jest skonfigurowany z 16 partycji. Na koniec konsumencki należy mechanizm, który może odczytywać te zdarzenia, skonsolidować je (filtrować, agregować itp.) oraz zrzutu agregacji do magazynu obiektów blob, który jest następnie przekazywany do strony sieci web przyjazna dla użytkownika.

## <a name="write-the-consumer-application"></a>Napisanie aplikacji klienta

Podczas projektowania klienta w środowisku rozproszonym, scenariusz musi obsługiwać następujące wymagania:

1. **Skala:** Tworzenie wielu odbiorców z poszczególnym przejmowania własności odczytywanie ich z kilku partycje usługi Event Hubs.
2. **Równoważenie obciążenia:** Zwiększ lub Zmniejsz konsumentów dynamicznie. Na przykład po dodaniu nowego typu czujnika (na przykład wykrywanie tlenku węgla) do każdej strony głównej zwiększa liczbę zdarzeń. W takiej sytuacji — operator (człowieka) powoduje zwiększenie liczby wystąpień odbiorców. Następnie pulę odbiorców można ponownie zrównoważyć liczby partycji są właścicielami, aby udostępnić obciążenia nowo dodanych użytkowników.
3. **Bezproblemowe wznowienia — błędy:** Jeśli odbiorcy (**konsumenta A**) kończy się niepowodzeniem (na przykład maszyna wirtualna hostingu nagle ulega awarii konsumenta), innych klientów musi być może podnieść partycje należące do **konsumenta A** i kontynuować. Również utrzymania punktu, o nazwie *punktu kontrolnego* lub *przesunięcie*, powinien być wyświetlany dokładny moment, w którym **konsumenta A** nie powiodło się, lub nieco wcześniej.
4. **Korzystanie ze zdarzeń:** Gdy poprzednie trzy punkty dotyczą zarządzania konsumenta, musi być umożliwia korzystanie ze zdarzeń oraz zrobić coś, co jest przydatne w przypadku; kodu na przykład można agregować je i przekaż go do magazynu obiektów blob.

Zamiast tego, tworząc własne rozwiązanie, usługa Event Hubs zapewnia tę funkcję za pośrednictwem [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) interfejsu i [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) klasy.

## <a name="ieventprocessor-interface"></a>Interfejsu IEventProcessor

Po pierwsze, korzystanie z implementacji aplikacji [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) interfejs, który ma cztery metody: [OpenAsync, CloseAsync, ProcessErrorAsync i ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor?view=azure-dotnet#methods). Ten interfejs zawiera rzeczywisty kod na korzystanie ze zdarzeń, które wysyła usługi Event Hubs. Poniższy kod pokazuje implementację proste:

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

Następnie utwórz wystąpienie [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) wystąpienia. W zależności od przeciążenia, tworząc [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) wystąpienia w konstruktorze, są używane następujące parametry:

- **Nazwa hosta:** nazwę każdego wystąpienia konsumenta. Każde wystąpienie **EventProcessorHost** musi mieć unikatową wartość dla tej zmiennej w ramach grupy odbiorców, więc nie ciężko kodu tę wartość.
- **eventHubPath:** Nazwa Centrum zdarzeń.
- **consumerGroupName:** Usługa Event Hubs używa **$Default** jako nazwa domyślna grupa odbiorców, ale jest dobrym rozwiązaniem, Utwórz grupę odbiorców z określonej proporcji przetwarzania.
- **eventHubConnectionString:** Parametry połączenia do Centrum zdarzeń, który można pobrać z witryny Azure portal. Te parametry połączenia powinny mieć **nasłuchiwania** uprawnienia w Centrum zdarzeń.
- **storageConnectionString:** Konto magazynu używane na potrzeby zarządzania wewnętrznych zasobów.

Na koniec Zarejestruj konsumentów [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) wystąpienie z usługi Event Hubs. Rejestrowanie klasy procesora zdarzeń z wystąpieniem klasy EventProcessorHost rozpoczyna przetwarzanie zdarzeń. Rejestrowanie powoduje, że usługi Event Hubs można oczekiwać, że aplikacja konsumenta zużywa zdarzenia z niektórych z jej partycji, a także do wywołania [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) kod implementacji zawsze wtedy, gdy umieszcza ono zdarzenia z. 


### <a name="example"></a>Przykład

Na przykład Wyobraźmy sobie, że istnieją 5 maszyn wirtualnych (VM) w wersji dedykowanej do używania zdarzenia i prostą aplikację konsolową w każdej maszynie Wirtualnej, która rzeczywiste zużycie działa. Każda aplikacja konsoli, następnie tworzony jest jeden [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) wystąpienia i rejestruje je za pomocą usługi Event Hubs.

W tym przykładowym scenariuszu Załóżmy, że 16 partycji są przydzielane do 5 **EventProcessorHost** wystąpień. Niektóre **EventProcessorHost** wystąpienia może być właścicielem kilka więcej partycji niż inne. Dla każdej partycji **EventProcessorHost** jest właścicielem wystąpienia, tworzy wystąpienie `SimpleEventProcessor` klasy. Dlatego istnieją 16 wystąpienia `SimpleEventProcessor` ogólny, z jedną przypisaną do każdej partycji.

Na poniższej liście podsumowano w tym przykładzie:

- 16 partycje usługi event Hubs.
- 5 maszyn wirtualnych, aplikacji konsumentów 1 (na przykład Consumer.exe) w każdej maszynie Wirtualnej.
- Zarejestrowane 5 wystąpień EPH 1 w każdej maszynie Wirtualnej przez Consumer.exe.
- 16 `SimpleEventProcessor` obiekty utworzone przez 5 wystąpień EPH.
- 1 aplikacja Consumer.exe może zawierać 4 `SimpleEventProcessor` obiektów, ponieważ z 1 wystąpieniem EPH może posiadać 4 partycjami.

## <a name="partition-ownership-tracking"></a>Własność partycji śledzenia

Własność partycji do wystąpienia EPH (lub konsumenta) są śledzone za pomocą konta usługi Azure Storage, który jest udostępniany dla śledzenia. Można wizualizować śledzenia jako prostej tabeli, w następujący sposób. Możesz zobaczyć rzeczywiste wdrożenie, sprawdzając obiektów blob w ramach konta magazynu podanego:

| **Nazwa grupy konsumentów** | **Identyfikator partycji:** | **Nazwa hosta (owner)** | **Czas dzierżawy (lub własności) nabyte** | **Przesunięcie w partycji (punkt kontrolny)** |
| --- | --- | --- | --- | --- |
| $Default | 0 | Konsument\_VM3 | 2018-04-15T01:23:45 | 156 |
| $Default | 1 | Konsument\_VM4 | 2018-04-15T01:22:13 | 734 |
| $Default | 2 | Konsument\_VM0 | 2018-04-15T01:22:56 | 122 |
| : |   |   |   |   |
| : |   |   |   |   |
| $Default | 15 | Konsument\_VM3 | 2018-04-15T01:22:56 | 976 |

W tym miejscu każdy host uzyskuje własność partycji przez określony czas (czas trwania dzierżawy). Jeśli host kończy się niepowodzeniem (maszyna wirtualna zostanie zamknięte), dzierżawa wygasa. Inne hosty spróbować uzyskać prawo własności partycji i jednym z hostów zakończy się pomyślnie. Ten proces przywraca dzierżawy na partycji przy użyciu nowego właściciela. W ten sposób w danym momencie tylko jeden czytnik może odczytywać dane z dowolnego danej partycji w obrębie grupy odbiorców.

## <a name="receive-messages"></a>Odbieranie komunikatów

Każde wywołanie [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) zapewnia zbieranie zdarzeń. Jest odpowiedzialny za obsługę następujących zdarzeń. Jeśli chcesz upewnić się, że co najmniej raz hosta procesora przetwarza każdy komunikat, należy napisać własne Zachowaj ponowieniem próby wykonania kodu. Jednak należy zachować ostrożność uszkodzone kolejką wiadomości.

Zalecane jest wykonanie czynności relatywnie szybko; oznacza to czy jako nieco przetwarzania, jak to możliwe. Zamiast tego należy użyć grup odbiorców. Jeśli potrzebujesz do zapisywania do magazynu i czy niektóre routingu, zaleca się używać dwóch grup odbiorców i mieć dwie [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) implementacji, które są uruchamiane niezależnie.

W pewnym momencie podczas przetwarzania Twojego zechcieć do śledzenia co użytkownik przeczytał i zakończone. Śledzenia ma krytyczne znaczenie, jeśli należy ponownie uruchomić odczytu, więc nie można wrócić do początku strumienia. [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) upraszcza to śledzenie za pomocą *punktów kontrolnych*. Punkt kontrolny to lokalizacji lub przesunięcie w danej partycji w ramach danej grupy odbiorców, w tym momencie możesz ustali, że zostały przetworzone komunikaty. Oznaczanie punktu kontrolnego w **EventProcessorHost** odbywa się przez wywołanie metody [CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) metody [PartitionContext](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext) obiektu. Ta operacja odbywa się w obrębie [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) metody, ale można również wykonać [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync).

## <a name="checkpointing"></a>Tworzenie punktów kontrolnych

[CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) metoda ma dwa przeciążenia: po pierwsze, bez parametrów, punktów kontrolnych do najwyższej Przesunięcie zdarzenia w kolekcji zwróconej przez [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync). To przesunięcie jest znakiem "górny"; założono, że zostały przetworzone wszystkie ostatnie zdarzenia podczas jego wywoływania. W ten sposób możesz użyć tej metody, należy pamiętać, może wywołać go po kodzie przetwarzanie zdarzeń zostały zwrócone. Drugie przeciążenie umożliwia określenie [EventData](/dotnet/api/microsoft.azure.eventhubs.eventdata) wystąpienia punktu kontrolnego. Ta metoda umożliwia użycie innego rodzaju znak wodny do punktu kontrolnego. Przy użyciu znaku wodnego, można zaimplementować znacznik "niska water": przetworzeniu najniższą zdarzenia Sekwencyjna wiesz na pewno. To przeciążenie zapewnia elastyczność zarządzania przesunięcia.

Gdy przeprowadzane jest punkt kontrolny, plik JSON o informacje specyficzne dla partycji (w szczególności przesunięcie), są zapisywane do konta magazynu podana w Konstruktorze w [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Plik ten jest stale aktualizowany. Warto wziąć pod uwagę punkty kontrolne w kontekście — będzie zalecane przerywającej połączenie programem punktu kontrolnego każdej wiadomości. Konto magazynu używane do tworzenia punktów kontrolnych, prawdopodobnie nie będzie obsługiwać tego obciążenia, ale co ważniejsze tworzenie punktów kontrolnych każdego zdarzenia jest wskaźnikiem umieszczonych w kolejce wzorzec przesyłania komunikatów dotyczących, dla którego kolejki usługi Service Bus może być lepszym rozwiązaniem niż Centrum zdarzeń. Ideą usługi Event Hubs jest, skorzystaj z "co najmniej raz" dostarczania na dużą skalę. Wprowadzając swoje systemy klienckie idempotentne, można łatwo odzyskać sprawność po awarii lub ponowne uruchomienie wynik w te same zdarzenia, które są odbierane wiele razy.

## <a name="thread-safety-and-processor-instances"></a>Wątek wystąpień bezpieczeństwa i procesora

Domyślnie [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) jest bezpieczne dla wątków i zachowuje się w sposób synchroniczne w odniesieniu do wystąpienia programu [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor). Po odebraniu zdarzenia w partycji, [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) jest wywoływana w **IEventProcessor** wystąpienia, partycji i spowoduje zablokowanie dalszego wywołania **ProcessEventsAsync**dla partycji. Kolejnych komunikatów i wywołania **ProcessEventsAsync** w kolejce w tle jako "pompy komunikatów" w dalszym ciągu działać w tle na inny wątek. Bezpieczeństwo wątków, to eliminuje potrzebę kolekcje obsługujące wielowątkowość i znacznie zwiększa wydajność.

## <a name="shut-down-gracefully"></a>Zamknięcie

Na koniec [EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) umożliwia zamknięcie czyste wszystkich partycji czytników i powinno być zawsze wywoływane podczas zamykania wystąpienie [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Niewykonanie tej czynności może powodować opóźnienia podczas uruchamiania innych wystąpień **EventProcessorHost** z powodu wygaśnięcia dzierżawy i epoki konfliktów. Zarządzanie epoki jest omówiona szczegółowo w [epoki](#epoch) części tego artykułu. 

## <a name="lease-management"></a>Zarządzanie dzierżawą
Rejestrowanie klasy procesora zdarzeń z wystąpieniem klasy EventProcessorHost rozpoczyna przetwarzanie zdarzeń. Wystąpienie hosta uzyskuje dzierżawy na partycji Centrum zdarzeń, prawdopodobnie Przechwytywanie niektóre z innych wystąpień hosta w taki sposób, że na partycji równomiernego rozłożenia zbieżna dla wartości we wszystkich wystąpieniach hosta. Dla każdej dzierżawy partycji wystąpienie hosta tworzy wystąpienie klasy procesora zdarzeń podana, a następnie odbiera zdarzenia z tej partycji i przekazuje je do wystąpienia procesora zdarzeń. Poproś o dodanie większej liczby wystąpień oraz liczby możliwych dzierżaw jest pobierany, EventProcessorHost ostatecznie równoważy obciążenia na wszystkich użytkowników.

Jak wyjaśniono wcześniej, tabela śledzenia znacząco upraszcza charakter skalowania automatycznego [EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync). Jako wystąpienie **EventProcessorHost** zostanie uruchomiony, uzyskuje tak wielu dzierżaw, jak to możliwe i rozpoczyna się odczytywanie zdarzeń. Jako dzierżawy wygasną **EventProcessorHost** próbuje odnowić je, umieszczając rezerwacji. Jeśli dzierżawa jest dostępny do odnowienia, procesor będzie kontynuować odczytywanie, ale nie Jeśli czytnik jest zamykany i [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync) jest wywoływana. **CloseAsync** jest dobry moment na wykonanie ostatecznego czyszczenia dla tej partycji.

**EventProcessorHost** obejmuje [PartitionManagerOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.partitionmanageroptions) właściwości. Ta właściwość umożliwia kontrolę nad zarządzaniem dzierżawy. Ustawić te opcje przed zarejestrowaniem swoje [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) implementacji.

## <a name="control-event-processor-host-options"></a>Sterowanie opcjami hosta procesora zdarzeń

Ponadto jednego przeciążenia [RegisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync?view=azure-dotnet#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) przyjmuje [EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync?view=azure-dotnet#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) obiektu jako parametr. Ten parametr służy do sterowania zachowaniem [EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) sam. [EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions) definiuje cztery właściwości i jedno zdarzenie:

- [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize): Maksymalny rozmiar kolekcji ma być wyświetlany w wywołanie [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync). Ten rozmiar nie jest minimalny, maksymalny rozmiar. Jeśli ma mniejszą liczbę komunikatów do odbioru, **ProcessEventsAsync** wykonuje się przy użyciu tyle, ile były dostępne.
- [PrefetchCount](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.prefetchcount): Wartość używana przez kanału źródłowego protokołu AMQP do określenie górnego limitu liczby wiadomości powinien otrzymać klient. Ta wartość powinna być większa lub równa [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize).
- [InvokeProcessorAfterReceiveTimeout](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.invokeprocessorafterreceivetimeout): Jeśli ten parametr jest **true**, [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) jest wywoływana, gdy upłynie limit czasu podstawowym wywołaniu, aby odbierać zdarzenia w partycji. Ta metoda jest przydatna do wykonywania akcji na podstawie czasu podczas nieaktywności na partycji.
- [InitialOffsetProvider](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.initialoffsetprovider): Umożliwia funkcji wskaźnika lub wyrażenia lambda można ustawić, która jest wywoływana w celu zapewnienia początkowe przesunięcie, gdy czytnik rozpoczyna się odczytywanie partycji. Bez określania to przesunięcie, czytnik rozpoczyna się od najstarszych zdarzeń, o ile nie został wcześniej zapisany plik JSON z przesunięciem na koncie magazynu przekazana do [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) konstruktora. Ta metoda jest przydatna, jeśli chcesz zmienić zachowanie uruchamiania czytnika. Po wywołaniu tej metody parametr obiektu zawiera identyfikator partycji, dla którego jest uruchamiany czytelnika.
- [ExceptionReceivedEventArgs](/dotnet/api/microsoft.azure.eventhubs.processor.exceptionreceivedeventargs): Umożliwia otrzymywanie powiadomień o podstawowej wyjątków, które występują w [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Jeśli elementy nie działają zgodnie z oczekiwaniami, to zdarzenie jest dobrym miejscem do rozpoczęcia wyszukiwania.

## <a name="epoch"></a>Epoka.

Poniżej przedstawiono, jak działa epoki receive:

### <a name="with-epoch"></a>Za pomocą epoka.
Uniksowym to unikatowy identyfikator (wartość epoki) używany przez usługę, aby wymusić własność partycji/dzierżawy. Tworzenie na podstawie epoki odbiornika zdarzenia za pomocą [CreateEpochReceiver](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createepochreceiver?view=azure-dotnet) metody. Ta metoda tworzy na podstawie epoki odbiorcy. Odbiorca jest tworzony dla partycji Centrum określonego zdarzenia z grupy konsumentów określony.

Funkcja epoki daje użytkownikom możliwość upewnij się, że istnieje tylko jeden odbiornik grupy konsumentów w dowolnym momencie w czasie, za pomocą następujących reguł:

- Jeśli grupa odbiorców jest nie istniejący odbiornik, użytkownik może utworzyć odbiornik z dowolną wartością epoki.
- Jeśli istnieje odbiorcy z epoki wartość e1, a nowy odbiornik jest tworzona przy użyciu e2 wartość epoki gdzie e1 < = e2, odbiornika z e1 zostanie automatycznie rozłączone, odbiorcy z e2 został utworzony pomyślnie.
- Jeśli istnieje odbiorcy z epoki wartość e1, a nowy odbiornik jest tworzona przy użyciu e2 wartość epoki gdzie e1 > e2, a następnie utworzenie e2 z zakończyć się niepowodzeniem z powodu błędu: Odbiornik o e1 epoki już istnieje.

### <a name="no-epoch"></a>Nie epoki
Utwórz odbiornik oparty epoki przy użyciu [CreateReceiver](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createreceiver?view=azure-dotnet) metody. 

Istnieją pewne scenariusze, w usłudze stream, przetwarzanie, których użytkowników chcesz utworzyć wiele odbiorników w grupie jednego konsumenta. Do obsługi takich scenariuszy, mamy możliwość utworzenia odbiornika bez epoki, a w tym przypadku zezwalamy na maksymalnie 5 współbieżnych odbiorcy dla grupy odbiorców.

### <a name="mixed-mode"></a>W trybie mieszanym
Nie zalecamy użycia aplikacji, w którym Utwórz odbiornik o epoki, a następnie przejdź do epoki nie lub odwrotnie w tej samej grupie odbiorców. Jednak w takiej sytuacji usługa obsługuje go za pomocą następujących reguł:

- Występuje odbiornik już utworzonych za pomocą epoki e1 i będzie aktywnie odbierać zdarzenia i nowy odbiornik jest tworzona przy użyciu nie epoki, tworzenie nowych odbiorcy zakończy się niepowodzeniem. Odbiorniki epoki zawsze mają pierwszeństwo w systemie.
- Jeśli było odbiornika już utworzonych za pomocą epoki e1 i stało się odłączony, a nowy odbiornik jest tworzona przy użyciu nie epoki na nowe MessagingFactory, tworzenie nowych odbiorcy zakończy się pomyślnie. Ma tutaj ostrzeżenie, że nasz system wykryje "rozłączania odbiornika" po ~ 10 minut.
- Jeśli ma jednego lub więcej odbiorników utworzonych za pomocą nie epoki, a nowy odbiornik jest tworzona przy użyciu epoki e1, rozłączone stare odbiorców.


## <a name="next-steps"></a>Kolejne kroki

Teraz, kiedy znasz już hosta procesora zdarzeń, zobacz następujące artykuły, aby dowiedzieć się więcej na temat usługi Event Hubs:

* Rozpocznij pracę od [samouczka dotyczącego usługi Event Hubs](event-hubs-dotnet-standard-getstarted-send.md)
* [Przewodnik programowania w usłudze Event Hubs](event-hubs-programming-guide.md)
* [Availability and consistency in Event Hubs](event-hubs-availability-and-consistency.md) (Dostępność i spójność w usłudze Event Hubs)
* [Event Hubs — często zadawane pytania](event-hubs-faq.md)
* [Przykłady usługi Event Hubs w usłudze GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples)

---
title: Odbieranie zdarzeń przy użyciu hosta procesora zdarzeń — usługi Azure Event Hubs | Dokumenty firmy Microsoft
description: W tym artykule opisano hosta procesora zdarzeń w usłudze Azure Event Hubs, co upraszcza zarządzanie punktami kontrolnymi, leasingiem i odczytywaniem zdarzeń równoleżniejących.
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
ms.openlocfilehash: 485f51e45e342ca28d54d609fd975bef5b204f7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372225"
---
# <a name="event-processor-host"></a>Host procesora zdarzeń
> [!NOTE]
> Ten artykuł dotyczy starej wersji zestawu SDK usługi Azure Event Hubs. Aby dowiedzieć się, jak przeprowadzić migrację kodu do nowszej wersji sdk, zobacz te przewodniki migracji. 
> - [.NET](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md)
> - [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/migration-guide.md)
> - [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md)
> - [Skrypt Java](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/event-hubs/migrationguide.md)
>
> Zobacz [też: Równoważenie obciążenia partycji w wielu wystąpieniach aplikacji](event-processor-balance-partition-load.md).

Usługa Azure Event Hubs to zaawansowana usługa pozyskiwania danych telemetrycznych, która może służyć do przesyłania strumieniowego milionów zdarzeń przy niskich kosztach. W tym artykule opisano sposób używania pozyskiwania zdarzeń przy użyciu *hosta procesora zdarzeń* (EPH); inteligentny agent konsumencki, który upraszcza zarządzanie punktami kontrolnymi, leasingiem i równoległymi czytnikami zdarzeń.  

Kluczem do skalowania dla centrów zdarzeń jest idea podzielonych na partycje konsumentów. W przeciwieństwie do [wzorca konkurujących konsumentów](https://msdn.microsoft.com/library/dn568101.aspx) wzorzec konsumenta, wzorzec konsumenta podzielony na partycje umożliwia dużą skalę, usuwając wąskie gardło rywalizacji i ułatwiając równoległość końca do końca.

## <a name="home-security-scenario"></a>Scenariusz bezpieczeństwa w domu

W przykładowym scenariuszu rozważmy firmę zajmującą się bezpieczeństwem w domu, która monitoruje 100 000 domów. Co minutę pobiera dane z różnych czujników, takich jak czujnik ruchu, czujnik otwierania drzwi / okien, detektor pęknięć szkła itp., zainstalowany w każdym domu. Firma udostępnia stronę internetową dla mieszkańców do monitorowania działalności ich domu w czasie zbliżonym do rzeczywistego.

Każdy czujnik wypycha dane do centrum zdarzeń. Centrum zdarzeń jest skonfigurowane z 16 partycjami. Na końcu zużywa, potrzebujesz mechanizmu, który może odczytywać te zdarzenia, konsolidować je (filtrować, agregować itp.) i zrzucać agregację do obiektu blob magazynu, który jest następnie rzutowany na przyjazną dla użytkownika stronę sieci web.

## <a name="write-the-consumer-application"></a>Napisz aplikację konsumencką

Podczas projektowania konsumenta w środowisku rozproszonym scenariusz musi obsługiwać następujące wymagania:

1. **Skala:** Tworzenie wielu konsumentów, z każdego konsumenta przejęcia na własność odczytu z kilku partycji Centrum zdarzeń.
2. **Bilans obciążenia:** Dynamicznie zwiększaj lub zmniejszaj konsumentów. Na przykład, gdy do każdego domu dodawany jest nowy typ czujnika (na przykład detektor tlenku węgla), liczba zdarzeń wzrasta. W takim przypadku operator (człowiek) zwiększa liczbę wystąpień konsumentów. Następnie pula konsumentów można zrównoważyć liczbę partycji, które posiadają, aby udostępnić obciążenie nowo dodanych konsumentów.
3. **Bezproblemowe wznowienie w przypadku awarii:** Jeśli konsument **(konsument A**) ulegnie awarii (na przykład maszyna wirtualna obsługująca konsumenta nagle ulega awarii), inni konsumenci muszą mieć możliwość pobrania partycji należących do **konsumenta A** i kontynuowania. Ponadto punkt kontynuacji, zwany *punktem kontrolnym* lub *przesunięciem,* powinien znajdować się dokładnie w punkcie, w którym **konsument A** nie powiódł się lub nieco wcześniej.
4. **Zużywają zdarzenia:** Podczas gdy poprzednie trzy punkty dotyczą zarządzania konsumentem, musi istnieć kod, aby konsumować zdarzenia i zrobić coś pożytecznego z nim; na przykład zagregować go i przekazać go do magazynu obiektów blob.

Zamiast tworzenia własnego rozwiązania dla tego, Event Hubs zapewnia tę funkcję za pośrednictwem interfejsu [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) i [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) klasy.

## <a name="ieventprocessor-interface"></a>Interfejs IEventProcessor

Po pierwsze, korzystanie z aplikacji implementuje interfejs [IEventProcessor,](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) który ma cztery metody: [OpenAsync, CloseAsync, ProcessErrorAsync i ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor?view=azure-dotnet#methods). Ten interfejs zawiera rzeczywisty kod do korzystania ze zdarzeń wysyłanych przez centra zdarzeń. Poniższy kod przedstawia prostą implementację:

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

Next, instantiate an [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) instance. W zależności od przeciążenia podczas tworzenia [EventProcessorHost wystąpienie](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) w konstruktorze, następujące parametry są używane:

- **hostName:** nazwa każdego wystąpienia konsumenta. Każde wystąpienie **EventProcessorHost** musi mieć unikatową wartość dla tej zmiennej w grupie odbiorców, więc nie kod twardy tej wartości.
- **eventHubPath:** Nazwa centrum zdarzeń.
- **consumerGroupName:** Usługa Event Hubs używa **$Default** jako nazwy domyślnej grupy odbiorców, ale jest dobrą praktyką, aby utworzyć grupę odbiorców dla określonego aspektu przetwarzania.
- **eventHubConnectionString:** Parametry połączenia z centrum zdarzeń, które można pobrać z witryny Azure portal. Ten ciąg połączenia powinien mieć uprawnienia **nasłuchiwki** w centrum zdarzeń.
- **storageConnectionString:** Konto magazynu używane do zarządzania zasobami wewnętrznymi.

Na koniec konsumenci rejestrują [EventProcessorHost wystąpienie](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) z usługi Centrum zdarzeń. Rejestrowanie klasy procesora zdarzeń z wystąpieniem EventProcessorHost rozpoczyna przetwarzanie zdarzeń. Rejestrowanie nakazuje usługi Event Hubs oczekiwać, że aplikacja konsumenta zużywa zdarzenia z niektórych partycji i wywołać [iEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) kod implementacji, gdy wypycha zdarzenia do użycia. 


### <a name="example"></a>Przykład

Na przykład załóżmy, że istnieje 5 maszyn wirtualnych (VM) przeznaczonych do korzystania ze zdarzeń i prostej aplikacji konsoli w każdej maszynie wirtualnej, która wykonuje pracę zużycia rzeczywistego. Każda aplikacja konsoli następnie tworzy jedno [wystąpienie EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) i rejestruje go w usłudze Centrum zdarzeń.

W tym przykładzie załóżmy, że 16 partycji są przydzielane do 5 **EventProcessorHost** wystąpień. Niektóre **wystąpienia EventProcessorHost** może posiadać kilka partycji więcej niż inne. Dla każdej partycji, która **eventProcessorHost wystąpienie** `SimpleEventProcessor` jest właścicielem, tworzy wystąpienie klasy. W związku z tym istnieje 16 wystąpień `SimpleEventProcessor` ogólnej, z jednym przypisany do każdej partycji.

Poniższa lista podsumowuje ten przykład:

- 16 partycji Centrum zdarzeń.
- 5 maszyn wirtualnych, 1 aplikacja dla konsumentów (na przykład Consumer.exe) w każdej maszynie wirtualnej.
- 5 Zarejestrowanych wystąpień EPH, 1 na każdej maszynie wirtualnej przez Consumer.exe.
- 16 `SimpleEventProcessor` obiektów utworzonych przez 5 wystąpień EPH.
- 1 Aplikacja Consumer.exe może `SimpleEventProcessor` zawierać 4 obiekty, ponieważ wystąpienie 1 EPH może być właścicielem 4 partycji.

## <a name="partition-ownership-tracking"></a>Śledzenie własności partycji

Własność partycji do wystąpienia EPH (lub konsumenta) jest śledzona za pośrednictwem konta usługi Azure Storage, które jest przewidziane do śledzenia. Śledzenie można wizualizować w prosty sposób w następujący sposób. Rzeczywistą implementację można wyświetlić, badając obiekty blob w obszarze podane konto magazynu:

| **Nazwa grupy konsumentów** | **Identyfikator partycji** | **Nazwa hosta (właściciel)** | **Czas dzierżawy (lub własności)** | **Przesunięcie w partycji (punkt kontrolny)** |
| --- | --- | --- | --- | --- |
| $Default | 0 | Maszyna\_wirtualna dla konsumentów3 | 2018-04-15T01:23:45 | 156 |
| $Default | 1 | KonsumenckiE\_VM4 | 2018-04-15T01:22:13 | 734 |
| $Default | 2 | Maszyna\_wirtualna dla konsumentów0 | 2018-04-15T01:22:56 | 122 |
| : |   |   |   |   |
| : |   |   |   |   |
| $Default | 15 | Maszyna\_wirtualna dla konsumentów3 | 2018-04-15T01:22:56 | 976 |

W tym miejscu każdy host przejmuje własność partycji przez pewien czas trwania (czas trwania dzierżawy). Jeśli host ulegnie awarii (maszyna wirtualna zostanie zamknięta), dzierżawa wygaśnie. Inne hosty próbują uzyskać własność partycji, a jeden z hostów powiedzie się. Ten proces resetuje dzierżawę na partycji z nowym właścicielem. W ten sposób tylko jeden czytnik w danym momencie można odczytać z dowolnej partycji w grupie konsumentów.

## <a name="receive-messages"></a>Odbieranie komunikatów

Każde [wywołanie ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) dostarcza kolekcję zdarzeń. Twoim obowiązkiem jest obsługa tych zdarzeń. Jeśli chcesz upewnić się, że host procesora przetwarza każdą wiadomość co najmniej raz, musisz napisać własny zachować ponawianie kodu. Ale bądź ostrożny w sprawie zatrutych wiadomości.

Zaleca się, aby robić rzeczy stosunkowo szybko; oznacza to, że jak najmniej przetwarzania. Zamiast tego należy użyć grup odbiorców. Jeśli chcesz zapisać do magazynu i zrobić kilka routingu, lepiej jest użyć dwóch grup konsumentów i mieć dwie implementacje [IEventProcessor,](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) które są uruchamiane oddzielnie.

W pewnym momencie podczas przetwarzania możesz chcieć śledzić to, co przeczytałeś i ukończono. Śledzenie jest krytyczne, jeśli trzeba ponownie uruchomić odczyt, więc nie powrócisz do początku strumienia. [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) upraszcza to śledzenie za pomocą *punktów kontrolnych*. Punkt kontrolny jest lokalizacją lub przesunięciem dla danej partycji w obrębie danej grupy odbiorców, w którym to momencie użytkownik jest zadowolony z przetworzenia wiadomości. Oznaczanie punktu kontrolnego w **EventProcessorHost** odbywa się przez wywołanie [CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) metody na [PartitionContext](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext) obiektu. Ta operacja jest wykonywana w ramach [metody ProcessEventsAsync,](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) ale można ją również wykonać w [programie CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync).

## <a name="checkpointing"></a>Tworzenie punktów kontrolnych

[CheckpointAsync](/dotnet/api/microsoft.azure.eventhubs.processor.partitioncontext.checkpointasync) Metoda ma dwa przeciążenia: pierwszy, bez parametrów, punkty kontrolne do najwyższego przesunięcia zdarzeń w kolekcji zwrócone przez [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync). To przesunięcie jest znakiem "wysokiej wody"; zakłada, że zostały przetworzone wszystkie ostatnie zdarzenia, gdy go wywołasz. Jeśli używasz tej metody w ten sposób, należy pamiętać, że oczekuje się wywołać go po innym kod przetwarzania zdarzenia został zwrócony. Drugie przeciążenie umożliwia określenie wystąpienia [EventData](/dotnet/api/microsoft.azure.eventhubs.eventdata) do punktu kontrolnego. Ta metoda umożliwia użycie innego typu znaku wodnego do punktu kontrolnego. Za pomocą tego znaku wodnego można zaimplementować znak "niska woda": najniższe zdarzenie sekwencjonowane, które masz pewność, że zostało przetworzone. To przeciążenie jest przewidziane w celu umożliwienia elastyczności w zarządzaniu offsetem.

Po wykonaniu punktu kontrolnego plik JSON z informacjami specyficznymi dla partycji (w szczególności przesunięcie) jest zapisywany na koncie magazynu dostarczonym w konstruktorze do [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Ten plik jest stale aktualizowany. Ważne jest, aby wziąć pod uwagę punkty kontrolne w kontekście - byłoby nierozsądne do punktu kontrolnego każdej wiadomości. Konto magazynu używane do obsługi punktów kontrolnych prawdopodobnie nie obsłuży tego obciążenia, ale co ważniejsze, punkt kontrolny każdego pojedynczego zdarzenia wskazuje na wzorzec obsługi w kolejce, dla którego kolejka usługi Service Bus może być lepszym rozwiązaniem niż centrum zdarzeń. Ideą Centrum zdarzeń jest to, że otrzymujesz "co najmniej raz" dostawę na dużą skalę. Dzięki temu, że systemy podrzędne są idempotentne, można łatwo odzyskać po awarii lub ponownym uruchomieniu, które powodują, że te same zdarzenia są odbierane wiele razy.

## <a name="thread-safety-and-processor-instances"></a>Bezpieczeństwo wątków i wystąpienia procesora

Domyślnie [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) jest bezpieczny dla wątków i zachowuje się w sposób synchroniczne w odniesieniu do wystąpienia [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor). Gdy zdarzenia przybywają dla partycji [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) jest wywoływana w wystąpieniu **IEventProcessor** dla tej partycji i zablokuje dalsze wywołania **ProcessEventsAsync** dla partycji. Kolejne komunikaty i wywołania **ProcessEventsAsync** kolejki w górę w tle, jak pompa wiadomości nadal działać w tle w innych wątków. To bezpieczeństwo gwintów eliminuje potrzebę kolekcji bezpiecznych dla wątków i znacznie zwiększa wydajność.

## <a name="shut-down-gracefully"></a>Zamknij się z gracją

Na koniec [EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) umożliwia czyste zamknięcie wszystkich czytników partycji i zawsze powinno być wywoływane podczas zamykania wystąpienia [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Nie wykaże się to może spowodować opóźnienia podczas uruchamiania innych wystąpień **EventProcessorHost** z powodu wygaśnięcia dzierżawy i konfliktów Epoch. Zarządzanie epoką jest szczegółowo opisane w sekcji [Epoch](#epoch) artykułu. 

## <a name="lease-management"></a>Zarządzanie leasingiem
Rejestrowanie klasy procesora zdarzeń z wystąpieniem EventProcessorHost rozpoczyna przetwarzanie zdarzeń. Wystąpienie hosta uzyskuje dzierżawy na niektórych partycjach Centrum zdarzeń, prawdopodobnie chwytając niektóre z innych wystąpień hosta, w sposób, który zbiega się w równej dystrybucji partycji we wszystkich wystąpieniach hosta. Dla każdej dzierżawionej partycji wystąpienie hosta tworzy wystąpienie podanej klasy procesora zdarzeń, a następnie odbiera zdarzenia z tej partycji i przekazuje je do wystąpienia procesora zdarzeń. Jak więcej wystąpień dodać i więcej dzierżaw są chwycił, EventProcessorHost ostatecznie równoważy obciążenie wśród wszystkich konsumentów.

Jak wyjaśniono wcześniej, tabela śledzenia znacznie upraszcza charakter skalowania automatycznego [EventProcessorHost.UnregisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync). Jako wystąpienie **EventProcessorHost** rozpoczyna, nabywa jak najwięcej dzierżaw, jak to możliwe i rozpoczyna czytanie zdarzeń. Jako dzierżawy w pobliżu wygaśnięcia **EventProcessorHost** próbuje odnowić je, umieszczając rezerwację. Jeśli dzierżawa jest dostępna do odnowienia, procesor kontynuuje odczyt, ale jeśli tak nie jest, czytnik jest zamknięty i [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.closeasync) jest wywoływana. **CloseAsync** to dobry moment, aby wykonać wszelkie końcowe oczyszczanie dla tej partycji.

**EventProcessorHost** zawiera właściwość [PartitionManagerOptions.](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.partitionmanageroptions) Ta właściwość umożliwia kontrolę nad zarządzaniem dzierżawą. Ustaw te opcje przed zarejestrowaniem implementacji [IEventProcessor.](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor)

## <a name="control-event-processor-host-options"></a>Opcje hosta procesora zdarzeń sterowania

Ponadto jedno przeciążenie [RegisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync?view=azure-dotnet#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) przyjmuje [EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync?view=azure-dotnet#Microsoft_Azure_EventHubs_Processor_EventProcessorHost_RegisterEventProcessorAsync__1_Microsoft_Azure_EventHubs_Processor_EventProcessorOptions_) obiektu jako parametr. Ten parametr służy do kontrolowania zachowania [eventprocessorHost.UnregisterEventProcessorAsync.](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.unregistereventprocessorasync) [EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions) definiuje cztery właściwości i jedno zdarzenie:

- [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize): Maksymalny rozmiar kolekcji, którą chcesz otrzymać w wywołaniu [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync). Ten rozmiar nie jest minimalny, tylko maksymalny rozmiar. Jeśli istnieje mniej wiadomości do odebraniu, **ProcessEventsAsync** wykonuje z tyle, ile były dostępne.
- [PrefetchCount](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.prefetchcount): Wartość używana przez podstawowy kanał AMQP do określenia górnego limitu liczby wiadomości, które klient powinien odbierać. Ta wartość powinna być większa lub równa [MaxBatchSize](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.maxbatchsize).
- [InvokeProcessorAfterReceiveTimeout:](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.invokeprocessorafterreceivetimeout)Jeśli ten parametr jest **true**, [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync) jest wywoływana, gdy podstawowe wywołanie do odbierania zdarzeń na partycji limit czasu. Ta metoda jest przydatna do podejmowania akcji opartych na czasie w okresach braku aktywności na partycji.
- [InitialOffsetProvider:](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.initialoffsetprovider)Umożliwia ustawienie wskaźnika funkcji lub wyrażenia lambda, które jest wywoływane w celu zapewnienia początkowego przesunięcia, gdy czytelnik rozpoczyna czytanie partycji. Bez określania tego przesunięcia czytnik rozpoczyna się od najstarszego zdarzenia, chyba że plik JSON z przesunięciem został już zapisany na koncie magazynu dostarczonym do konstruktora [EventProcessorHost.](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost) Ta metoda jest przydatna, gdy chcesz zmienić zachowanie uruchamiania czytnika. Gdy ta metoda jest wywoływana, parametr obiektu zawiera identyfikator partycji, dla którego czytnik jest uruchamiany.
- [ExceptionReceivedEventArgs](/dotnet/api/microsoft.azure.eventhubs.processor.exceptionreceivedeventargs): Umożliwia otrzymywanie powiadomień o wszelkich podstawowych wyjątkach występujących w [EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost). Jeśli rzeczy nie działają zgodnie z oczekiwaniami, to wydarzenie jest dobrym miejscem, aby zacząć szukać.

## <a name="epoch"></a>Epoch

Oto jak działa epoka otrzymywania:

### <a name="with-epoch"></a>Z Epoką
Epoka jest unikatowy identyfikator (wartość epoki), który używa usługi, aby wymusić własność partycji/dzierżawy. Tworzenie odbiornika opartego na epoce przy użyciu [CreateEpochReceiver](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createepochreceiver?view=azure-dotnet) metody. Ta metoda tworzy odbiornik oparty na epoce. Odbiornik jest tworzony dla określonej partycji centrum zdarzeń z określonej grupy odbiorców.

Funkcja epoki zapewnia użytkownikom możliwość zapewnienia, że w grupie odbiorców w dowolnym momencie znajduje się tylko jeden odbiornik, z następującymi regułami:

- Jeśli w grupie odbiorców nie ma istniejącego odbiornika, użytkownik może utworzyć odbiornik o dowolnej wartości epoki.
- Jeśli istnieje odbiornik o wartości epoki e1 i tworzony jest nowy odbiornik o wartości epoki e2, gdzie e1 <= e2, odbiornik z e1 zostanie automatycznie odłączony, odbiornik z e2 zostanie pomyślnie utworzony.
- Jeśli istnieje odbiornik o wartości epoki e1 i tworzony jest nowy odbiornik o wartości epoki e2, gdzie e1 > e2, to tworzenie e2 z błędem z błędem: Odbiornik z epoką e1 już istnieje.

### <a name="no-epoch"></a>Nie epoka
Tworzenie odbiornika nienaparte epoki przy użyciu [CreateReceiver](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createreceiver?view=azure-dotnet) metody. 

Istnieje kilka scenariuszy przetwarzania strumienia, w którym użytkownicy chcieliby utworzyć wiele odbiorników w jednej grupie odbiorców. Aby wspierać takie scenariusze, mamy możliwość tworzenia odbiornika bez epoki i w tym przypadku pozwalamy na maksymalnie 5 równoczesnych odbiorników w grupie konsumentów.

### <a name="mixed-mode"></a>Tryb mieszany
Nie zaleca się użycia aplikacji, w którym tworzysz odbiornik z epoką, a następnie przełączasz się na no-epoch lub odwrotnie w tej samej grupie odbiorców. Jednak gdy to zachowanie występuje, usługa obsługuje go przy użyciu następujących reguł:

- Jeśli istnieje odbiornik już utworzony z epoki e1 i aktywnie odbiera zdarzenia i nowy odbiornik jest tworzony bez epoki, tworzenie nowego odbiornika zakończy się niepowodzeniem. Odbiorniki epokowe zawsze mają pierwszeństwo w systemie.
- Jeśli nie było odbiornika już utworzone z epoki e1 i został odłączony, a nowy odbiornik jest tworzony bez epoki na nowy MessagingFactory, utworzenie nowego odbiornika zakończy się sukcesem. Istnieje zastrzeżenie, że nasz system wykryje "odłączenie odbiornika" po ~ 10 minutach.
- Jeśli istnieje jeden lub więcej odbiorników utworzonych bez epoki, a nowy odbiornik jest tworzony z epoki e1, wszystkie stare odbiorniki rozłączają się.


> [!NOTE]
> Zaleca się używanie różnych grup konsumentów dla aplikacji, które używają epok i dla tych, które nie używają epok, aby uniknąć błędów. 


## <a name="next-steps"></a>Następne kroki

Teraz, gdy znasz hosta procesora zdarzeń, zobacz następujące artykuły, aby dowiedzieć się więcej o Centrach zdarzeń:

- Rozpoczynanie pracy z usługą Event Hubs
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [Javascript](get-started-java-send-v2.md)
* [Przewodnik po programowaniu centrów zdarzeń](event-hubs-programming-guide.md)
* [Availability and consistency in Event Hubs](event-hubs-availability-and-consistency.md) (Dostępność i spójność w usłudze Event Hubs)
* [Event Hubs — często zadawane pytania](event-hubs-faq.md)
* [Przykłady centrów zdarzeń w usłudze GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples)

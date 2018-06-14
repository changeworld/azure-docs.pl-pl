---
title: Azure transferów komunikatów usługi Service Bus, blokad i rozliczenia | Dokumentacja firmy Microsoft
description: Omówienie transferu komunikatów usługi Service Bus i rozliczenia operacji
services: service-bus-messaging
documentationcenter: ''
author: clemensv
manager: timlt
editor: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2018
ms.author: sethm
ms.openlocfilehash: 4789da3c84d52b2615bf4250a36093a74154e1d4
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2018
ms.locfileid: "28199036"
---
# <a name="message-transfers-locks-and-settlement"></a>Przesyłania wiadomości, blokad i rozliczenia

Możliwości centralnego brokera komunikatów, takie jak usługi Service Bus jest akceptować komunikaty w kolejce lub tematu i je przechowywać dostępne do pobrania nowsze. *Wyślij* termin, który jest powszechnie używany do przekazywania wiadomości w brokera komunikatów. *Odbieranie* termin powszechnie używany do przekazywania wiadomości do pobierania klienta.

Gdy klient wysyła komunikat, zwykle chce wiedzieć, czy wiadomość została prawidłowo przeniesione do i zaakceptowane przez brokera lub czy wystąpiły jakieś błędu. To potwierdzenie dodatnie lub ujemne reguluje klienta i broker opis o stanie transferu wiadomości i w związku z tym jest określany jako *rozliczenia*.

Podobnie, gdy brokera przenosi wiadomość do klienta, broker i klienta chcesz ustanowić zrozumienia Określa, czy wiadomość została przetworzona pomyślnie i mogą zostać usunięte lub czy dostarczanie komunikatów lub przetwarzania nie, a tym samym komunikat może ma być dostarczony ponownie.

## <a name="settling-send-operations"></a>Operacje wysyłania rozliczania

Przy użyciu dowolnej z obsługiwanych klientów interfejsu API usługi Service Bus, Wyślij operacji do usługi Service Bus zawsze jawnie są rozliczane, co oznacza, że operacji interfejsu API czeka na wynik akceptacji z usługi Service Bus zostanie dostarczona, a następnie kończy operację wysyłania.

Jeśli komunikat zostanie odrzucony przez usługi Service Bus, odrzucenia zawiera błąd wskaźnik i tekst z "-identyfikator śledzenia" wewnątrz niej. Odrzucenia zawiera także informacje dotyczące tego, czy można ponowić próbę wykonania operacji z dowolnego oczekiwania Powodzenie. W kliencie te informacje są przekształcane w wyjątek i zgłaszane do obiektu wywołującego operację wysyłania. Jeśli wiadomość zostanie zaakceptowana, dyskretnie zakończeniu operacji.

Podczas korzystania z protokołu AMQP, który jest protokołem wyłącznego oraz klienta .NET Standard języka Java i [który jest dostępny dla klienta programu .NET Framework](service-bus-amqp-dotnet.md), są potokowe transferu wiadomości i rozliczeń i całkowicie asynchroniczne, i zaleca się, że używasz wariantów asynchronicznego interfejsu API modelu programowania.

Nadawca można umieścić komunikaty przesyłania w krótkim odstępie czasu bez potrzeby czekania na każdy komunikat do potwierdzenia, ponieważ byłby w przypadku z protokołem SBMP lub z protokołu HTTP 1.1. Te operacje asynchronicznego wysyłania zakończyła się zgodnie z odpowiednich komunikaty są akceptowane i przechowywane na partycjonowane jednostki lub operacji podczas wysyłania do różnych obiektów nakładają się na siebie. Zakończeń może również wystąpić poza kolejności wysłania.

Strategia obsługi wyniku operacji wysyłania mogą mieć wpływ natychmiastowego i znacznie wydajności aplikacji. Przykłady w tej sekcji są napisane w języku C# i ekwiwalentnie poprosić prognoz Java.

Jeśli aplikacja generuje Seria wiadomości, w tym miejscu przedstawiane za pomocą zwykłego pętli i się poczekać na zakończenie każdej operacji przed wysłaniem następny komunikat synchroniczne wysyłania lub asynchroniczne kształtów interfejsu API podobne, tylko wysyłanie wiadomości 10 zakończeniu po 10 kolejne pełne rund do rozliczenia.

Zajmuje z zakładanego 70 milisekund TCP komunikacji dwukierunkowej opóźnienia odległości od lokacji lokalnej usługi Service Bus i podając tylko 10 ms dla usługi Service Bus, aby zaakceptować i przechowywanie każdej wiadomości, co najmniej 8 sekund, nie licząc czas transferu ładunku lub potencjalne następującą pętlę efekty przeciążenia trasy:

```csharp
for (int i = 0; i < 100; i++)
{
  // creating the message omitted for brevity
  await client.SendAsync(…);
}
```

Jeśli aplikacja rozpoczyna się 10 operacje asynchroniczne wysyłania kolejno natychmiastowego i oczekujące na ich odpowiednich zakończenia oddzielnie, nakłada się na czas obiegu dla tych 10 operacji wysyłania. 10 wiadomości są przesyłane kolejno natychmiastowego potencjalnie nawet udostępnianie ramki TCP i całkowity czas trwania transfer zależy przede wszystkim związanych z siecią czas potrzebny na uzyskanie dostępu do brokera komunikatów przesyłanych.

Założenie sam jak w przypadku wcześniejszego pętli, łączny czas wykonywania nakładających się następujące pętli for mogą pozostać również w ciągu sekundy:

```csharp
var tasks = new List<Task>();
for (int i = 0; i < 100; i++)
{
  tasks.Add(client.SendAsync(…));
}
await Task.WhenAll(tasks.ToArray());
```

Należy koniecznie należy pamiętać, że wszystkie modele programowania asynchronicznego jakiegoś kolejki pracy pamięci, ukryta, która przechowuje oczekujących operacji. Gdy [SendAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.sendasync#Microsoft_Azure_ServiceBus_QueueClient_SendAsync_Microsoft_Azure_ServiceBus_Message_) (C#) lub **wysyłania** return (Java), wysyłanie zadań jest umieszczone w kolejce w tej kolejce pracy, ale gestu protokołu rozpocznie się tylko, gdy jest włączony zadania do uruchomienia. Kod, który zwykle push Seria wiadomości i niezawodność w przypadku wątpliwości dotyczących należy zwrócić uwagę, że nie jest zbyt wiele komunikatów są umieszczane "w locie", ponieważ wszystkich wiadomości wysłanych dopiero pamięci zostały faktycznie wprowadzone serializacji.

Semaforów, jak pokazano w poniższy fragment kodu w języku C# są obiektami synchronizacji, które włączyć takie ograniczania poziomie aplikacji w razie potrzeby. Ta semafora umożliwiają maksymalnie 10 komunikatów być transmitowane jednocześnie. Jeden z 10 blokad semafora dostępne są podejmowane przed wysłaniem i po uwolnieniu jako zakończeniu wysyłania. 11 przekazywania czeka Pętla do momentu wysłania przez co najmniej jedną z wcześniej została zakończona, a następnie udostępnia jego blokady:

```csharp
var semaphore = new SemaphoreSlim(10);

var tasks = new List<Task>();
for (int i = 0; i < 100; i++)
{
  await semaphore.WaitAsync();

  tasks.Add(client.SendAsync(…).ContinueWith((t)=>semaphore.Release()));
}
await Task.WhenAll(tasks.ToArray());
```

Aplikacje powinny **nigdy nie** zainicjować operację asynchronicznego wysyłania w sposób "wyzwalać i zapomnij" bez wyników operacji pobierania. W ten sposób można załadować kolejki zadań wewnętrznych, jak i niewidoczne do wyczerpanie pamięci i zapobiega wykrywanie błędów wysyłania aplikacji:

```csharp
for (int i = 0; i < 100; i++)
{

  client.SendAsync(message); // DON’T DO THIS
}
```

Za pomocą niskiego poziomu klienta protokołu AMQP usługi Service Bus akceptuje transferów "wstępnie rozliczony". Wstępnie rozliczony transfer jest fire i zapomnij operacji, dla którego wynik, w obu przypadkach jest nie zgłaszana z powrotem do klienta i wiadomość jest uznawany za rozliczane przy wysyłaniu. Brak informacji pozwalających na kliencie oznacza także, że jest nie można wykonać danych diagnostyki, co oznacza, że w tym trybie nie kwalifikuje się o pomoc za pomocą techniczną platformy Azure.

## <a name="settling-receive-operations"></a>Operacje rozliczania odbioru

Dla operacji odbioru, klienci interfejsu API usługi Service Bus włączyć dwa różne tryby explicit: *Receive i usuwania* i *Peek blokady*.

[Receive i usuwania](/dotnet/api/microsoft.servicebus.messaging.receivemode) tryb informuje brokera wziąć pod uwagę wszystkie komunikaty wysyła do klienta odbierania jako rozliczony podczas obliczania wysyłane. Oznacza to, że wiadomość jest uznawany za używane jak brokera umieścił go serializacji. Przesłania nie powiedzie się, komunikat zostanie utracone.

Odwróć tego trybu jest odbiornik nie trzeba wykonać dalsze czynności w komunikacie oraz jest również nie spowolnienie oczekiwanie na wyniki rozliczeń. Jeśli dane znajdujące się w poszczególnych wiadomości ma niskiej wartości i/lub są tylko istotne bardzo krótki czas, ten tryb jest uzasadnione wybór.

[Blokady Peek](/dotnet/api/microsoft.servicebus.messaging.receivemode) tryb informuje brokera odbierania klienta chce jawnie uregulowanie odebranej wiadomości. Komunikat jest udostępniana do odbiornika do przetwarzania, dopóki utrzymywana w trybie wyłączności w usłudze, dzięki czemu inne, konkurencyjnych odbiorców niewidoczny. Czas trwania blokady jest wstępnie zdefiniowana na poziomie kolejki lub subskrypcji i można rozszerzyć przez klienta za pośrednictwem będący właścicielem blokady, [RenewLock](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_RenewLockAsync_System_String_) operacji.

Gdy komunikat jest zablokowana, innym klientom odbieranie z tej samej kolejki lub subskrypcji może przybrać blokad i pobieranie dalej wiadomości dostępne pod active blokady nie. Po zwolnieniu jawnie blokady na wiadomości lub po wygaśnięciu blokady komunikat POP kopii zapasowych lub prawie na początku pobierania aby ponowne dostarczenie.

Jeśli komunikat wielokrotnie został wydany przez odbiorców lub umożliwiają one blokady upłynąć dla zdefiniowanej liczby razy ([maxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount#Microsoft_ServiceBus_Messaging_QueueDescription_MaxDeliveryCount)), wiadomość jest automatycznie usuwane z kolejki lub subskrypcji i umieszczane w skojarzonych Kolejka utraconych wiadomości.

Odbieranie klienta inicjuje rozliczenia odebranego komunikatu o potwierdzenie pozytywne, gdy wywołuje [Complete](/dotnet/api/microsoft.servicebus.messaging.queueclient.complete#Microsoft_ServiceBus_Messaging_QueueClient_Complete_System_Guid_) na poziomie interfejsu API. Wskazuje brokera komunikat został przetworzony, a wiadomość zostanie usunięta z kolejki lub subskrypcji. Odpowiedzi brokera zamiar rozliczenia odbiorcy odpowiedzi, wskazującą, czy rozliczenia może być wykonana.

Podczas odbierania klient nie może przetworzyć komunikatu, ale chce komunikat, który ma zostać dostarczony, jawnie można zadawać wiadomości są zwalniane i natychmiast odblokowany przez wywołanie metody [Abandon](/dotnet/api/microsoft.servicebus.messaging.queueclient.abandon) lub go nie rób nic i pozwól upłynąć blokady.

Odbieranie klienta nie może przetworzyć komunikatu i zna redelivering wiadomości, operacji pomoże nie można odrzucić komunikat, który przenosi je do kolejki utraconych wiadomości, wywołując [utraconych wiadomości](/dotnet/api/microsoft.servicebus.messaging.queueclient.deadletter), który również Umożliwia ustawienie właściwości niestandardowej, w tym kod przyczyny, który można pobrać z komunikatu z kolejki utraconych wiadomości.

Specjalny przypadek rozliczenia jest opóźnienia, co zostało omówione w oddzielnym artykule.

**Complete** lub **utraconych wiadomości** operacje, jak również **RenewLock** może spowodować niepowodzenie operacji spowodowane problemami z siecią, jeśli przechowywanych blokady wygasł lub ma innych warunki po stronie serwera, które uniemożliwiają rozliczenia. W jednym z ostatnich przypadkach usługa wysyła potwierdzenie negatywne tego powierzchni jako wyjątek w klientach interfejsu API. Jeśli przyczyną jest połączenie sieciowe przerwane, blokady jest pomijane, ponieważ usługi Service Bus nie obsługuje odzyskiwania istniejące linki AMQP w ramach innego połączenia.

Jeśli **Complete** kończy się niepowodzeniem, w których występuje zwykle na końcu obsługi wiadomości, a w niektórych przypadkach po minut pracy przetwarzania aplikacja odbierająca może zdecydować, czy zachowuje stan pracy i ignoruje takie same komunikat po dostarczeniu po raz drugi lub czy tosses limit wyników pracy i ponawia próbę co komunikat jest przed przeniesieniem.

Typowe mechanizmu identyfikowania dostaw zduplikowanych komunikatów jest sprawdzanie komunikatu — identyfikator, który można i powinien być ustawiony przez nadawcę do unikatową wartość, prawdopodobnie są wyrównane z identyfikatorem w procesie źródłowego. Harmonogram zadań prawdopodobnie ustawiał identyfikator komunikatu z identyfikatorem zadania, które podejmuje próbę przypisania do pracownika z danego pracownika i proces roboczy czy Ignoruj drugie wystąpienie przypisania zadania, jeśli to zadanie zostało już przeprowadzone.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej o komunikatów usługi Service Bus, zobacz następujące tematy:

* [Podstawy usługi Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

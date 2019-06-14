---
title: Usługa Azure transferu wiadomości usługi Service Bus, blokady i uzgadnianie | Dokumentacja firmy Microsoft
description: Omówienie operacji rozliczenia i transfery komunikatów usługi Service Bus
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: aschhab
ms.openlocfilehash: a78409a15acb4e60fc4200778d0f33b3fb566e85
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60403945"
---
# <a name="message-transfers-locks-and-settlement"></a>Transferowanie, blokady i uzgadnianie komunikatów

Możliwości centralnego brokera komunikatów, takie jak Service Bus jest akceptować komunikaty do kolejki lub tematu i udostępniane do pobrania nowsze. *Wyślij* jest terminem, który jest powszechnie używany do transferu wiadomości do brokera komunikatów. *Odbieranie* termin często używane do przekazywania wiadomości do klienta podczas pobierania.

Gdy klient wysyła komunikat, zazwyczaj chce wiedzieć, czy wiadomość została prawidłowo przeniesione do i zaakceptowane przez brokera lub tego, czy Wystąpił jakiś błąd. Po potwierdzeniu tej pozytywnych lub negatywnych rozlicza klienta i broker opis o stanie transferu wiadomości i jest zatem określany jako *rozliczenia*.

Podobnie, gdy brokera przenosi wiadomość do klienta, broker i klienta ma zostać zrozumienia tego, czy wiadomość została pomyślnie przetworzona i może zostać usunięty lub czy dostarczaniem wiadomości lub przetwarzania nie, a tym samym komunikat ma może być dostarczony ponownie.

## <a name="settling-send-operations"></a>Operacje wysyłania rozliczania

Przy użyciu dowolnej z obsługiwanych klientów API usługi Service Bus, Wyślij operacji w usłudze Service Bus należy zawsze jawnie są rozliczane, co oznacza, że operacji interfejsu API czeka na wynik akceptacji z usługi Service Bus zostanie dostarczona, a następnie kończy operację wysyłania.

Jeśli komunikat zostanie odrzucony przez usługę Service Bus, odrzucenia zawiera wskaźnik błędów i tekstu za pomocą "-identyfikator śledzenia" wewnątrz niej. Odrzucenia zawiera także informacje dotyczące tego, czy operacja mogą być ponawiane przy użyciu dowolnej oczekiwania na powodzenie. W kliencie te informacje są przekształcane w wyjątek i wywoływane obiektowi wywołującemu operację wysyłania. Jeśli komunikat zostanie zaakceptowana, dyskretnie zakończeniu operacji.

Podczas korzystania z protokołu AMQP, który jest protokołem wyłączny dla klienta programu .NET Standard i klienta Java i [czyli opcję klienta .NET Framework](service-bus-amqp-dotnet.md), są potokowe transferu wiadomości i rozliczenia i w całości asynchroniczny, i zaleca się, że używasz wariantów asynchronicznego interfejsu API modelu programowania.

Nadawca można umieścić kilka komunikatów na potrzeby przesyłu w krótkim odstępie czasu bez konieczności oczekiwania dla każdego komunikatu do potwierdzenia, ponieważ w przeciwnym razie byłoby w przypadku protokołu SBMP lub za pomocą protokołu HTTP 1.1. Te operacje asynchronicznego wysyłania wykonaj kroki zgodnie z odpowiednich komunikaty zostaną zaakceptowane i przechowywane na partycjonowane jednostki lub podczas wysyłania operacji do nakładania się różnymi jednostkami. Uzupełnianie może również wystąpić poza pierwotną kolejność wysyłania.

Strategię obsługi wynik operacji wysyłania może mieć wpływ na wydajność natychmiastowe i istotne dla twojej aplikacji. Przykłady w tej sekcji są napisane w języku C# i zgłoś się ekwiwalentnie prognoz języka Java.

Jeśli aplikacja generuje wzrosty wiadomości, przedstawione w tym miejscu przy użyciu zwykłego pętli i zostałby oczekiwać na zakończenie każdego Wyślij operację przed wysłaniem nowego komunikatu synchronicznego lub asynchronicznego interfejsu API wzornika podobne tylko wysyłanie komunikatów 10 kończy się po 10 kolejne pełne rund do rozliczenia.

Zajmuje z zakładanego 70 milisekund TCP w obie strony opóźnienie odległość między lokacją lokalną usługi Service Bus i zapewniając tylko 10 ms dla usługi Service Bus, aby zaakceptować i przechowywanie każdej wiadomości, co najmniej 8 sekund, nie licząc zamykającego czas transferu ładunku lub potencjalne następującą pętlę efekty przeciążenia trasy:

```csharp
for (int i = 0; i < 100; i++)
{
  // creating the message omitted for brevity
  await client.SendAsync(…);
}
```

Jeśli aplikacja rozpoczyna się 10 operacji asynchronicznego wysyłania kolejno natychmiastowego i czeka na ich zakończenie odpowiednich oddzielnie, nakłada czas obiegu tych 10 operacji wysyłania. 10 wiadomości są przesyłane w bezpośrednim odstępie czasu, potencjalnie nawet udostępnianie ramki protokołu TCP, a całkowity czas trwania transferu dużej mierze zależy związanych z siecią czas potrzebny do pobrania komunikaty przekazywane do brokera.

Wprowadzania tych samych założeń, jak w przypadku wcześniejszego pętli, łączny czas wykonywania nachodzące na następującą pętlę mogą pozostać również w obszarze jednej sekundy:

```csharp
var tasks = new List<Task>();
for (int i = 0; i < 100; i++)
{
  tasks.Add(client.SendAsync(…));
}
await Task.WhenAll(tasks);
```

Jest to należy pamiętać, że wszystkie modele programowania asynchronicznego formularz niektóre kolejki pracy opartego na pamięci, ukryte, który posiada oczekujące operacje. Gdy [SendAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.sendasync#Microsoft_Azure_ServiceBus_QueueClient_SendAsync_Microsoft_Azure_ServiceBus_Message_) (C#) lub **wysyłania** return (Java), zadanie wysyłania jest umieszczone w kolejce w tej kolejce pracy, ale gestu protokołu tylko rozpoczyna się po kolej zadania do uruchomienia. Dla kodu, który ma tendencję do wypychania wzrosty komunikatów i gdzie niezawodności jest istotna należy z rozwagą, nie jest zbyt wiele komunikatów są umieszczane "w locie", ponieważ wszystkie komunikaty wysłane zajmują pamięć do momentu ich rzeczywiście zostały wprowadzone do sieci.

Semaforów, jak pokazano w poniższym fragmencie kodu w języku C# są obiekty synchronizacji, umożliwiające takich aplikacji na poziomie ograniczania, gdy potrzebne. Umożliwia to wykorzystania semafor co najwyżej 10 wiadomości za w locie na raz. Jedną z 10 blokad semafora dostępne jest pobierana przed wysłaniem i jest zwalniany jako zakończeniu wysyłania. 11 przekazywania czeka pętli, dopóki nie wysyła co najmniej jedną z wcześniej została ukończona, a następnie udostępnia blokady:

```csharp
var semaphore = new SemaphoreSlim(10);

var tasks = new List<Task>();
for (int i = 0; i < 100; i++)
{
  await semaphore.WaitAsync();

  tasks.Add(client.SendAsync(…).ContinueWith((t)=>semaphore.Release()));
}
await Task.WhenAll(tasks);
```

Aplikacje powinny **nigdy nie** zainicjować operację asynchronicznego wysyłania w sposób "fire and forget" bez pobierania wyniki operacji. Ten sposób można załadować kolejki zadań wewnętrznych i niewidoczności, aż do wyczerpania pamięci i uniemożliwiają aplikacji wykrywanie błędów wysyłania:

```csharp
for (int i = 0; i < 100; i++)
{

  client.SendAsync(message); // DON’T DO THIS
}
```

Za pomocą niskiego poziomu klienta protokołu AMQP usługi Service Bus akceptuje transfery "wstępnie rozliczone". Wstępnie rozliczona transferu jest pożarowego i zapominać operacji, dla której wynik w obu przypadkach nie jest zgłaszany ponownie do klienta i komunikat jest uznawany za rozliczane po wysłaniu. Brak opinii do klienta oznacza również, że jest nie wiarygodne dane wymagane diagnostyki, co oznacza, że w tym trybie nie kwalifikuje się o pomoc za pośrednictwem pomocy technicznej platformy Azure.

## <a name="settling-receive-operations"></a>Operacje rozliczania odbioru

Dla operacji odbioru, klienci interfejsu API usługi Service Bus włączyć dwa różne tryby jawne: *Odbieranie i usuwanie* i *Odbierz*.

[Odbieranie i usuwanie](/dotnet/api/microsoft.servicebus.messaging.receivemode) tryb informuje brokera wziąć pod uwagę wszystkie komunikaty, które wysyła do klienta odbieranie jako rozliczona kiedy wysyłane. Oznacza to, że wiadomość jest uważana za używane tak szybko, jak broker ma umieścić go na podczas transmisji. Jeśli transfer komunikatów nie powiedzie się, komunikat zostanie utracony.

Odwróć tego trybu jest, nie trzeba wykonać dalsze czynności w komunikacie odbiornik i jest również nie spowolnienie czekasz na wyniki rozliczenia. Jeśli dane znajdujące się w poszczególnych wiadomości ma niską wartość i/lub tylko mają znaczenie dla bardzo krótkim czasie, ten tryb jest uzasadnione wybór.

[Odbierz](/dotnet/api/microsoft.servicebus.messaging.receivemode) tryb informuje brokera odbieranie klient chce jawnie rozliczenia Odebrane komunikaty. Komunikat jest udostępniany dla odbiornika do przetwarzania, podczas gdy przechowywanych w ramach blokady na wyłączność w usłudze, aby innych, konkurencyjnych odbiorców nie jest widoczna. Czas trwania blokady jest wstępnie zdefiniowana na poziomie kolejki lub subskrypcji i można rozszerzyć przez klienta, będąca właścicielem blokady, za pośrednictwem [RenewLock](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_RenewLockAsync_System_String_) operacji.

Gdy komunikat jest zablokowany, innym klientom odbieranie z tej samej kolejki lub subskrypcji można przyjmą blokad i pobrania dalej komunikatów dostępne nieobjętego active blokady. Zwolnionych blokadę komunikatu jest jawnie lub po wygaśnięciu blokady komunikat POP kopii zapasowej na lub w pobliżu początku pobierania aby ponowne dostarczenie.

Gdy komunikat jest wielokrotnie wydane przez odbiorniki lub umożliwiają one blokady upłynąć dla zdefiniowanej liczby godzin ([maxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount#Microsoft_ServiceBus_Messaging_QueueDescription_MaxDeliveryCount)), wiadomość jest automatycznie usuwane z kolejki lub subskrypcji i umieszczane w skojarzonej Kolejka utraconych wiadomości.

Odbieranie klienta inicjuje rozliczenia odebranego komunikatu o potwierdzenie pozytywne, gdy wywołuje [Complete](/dotnet/api/microsoft.servicebus.messaging.queueclient.complete#Microsoft_ServiceBus_Messaging_QueueClient_Complete_System_Guid_) na poziomie interfejsu API. Oznacza to, aby broker czy wiadomość została pomyślnie przetworzona i komunikat zostanie usunięty z kolejki lub subskrypcji. Odpowiedzi broker na intencje rozliczenia odbiorcy odpowiedzi, która wskazuje, czy może być wykonana, rozliczenia.

Podczas odbierania klient nie może przetworzyć komunikatu, ale chce komunikat, który ma być dostarczony ponownie, jawnie można zadawać na komunikat, który ma być zwolnione i odblokować natychmiast, wywołując [Abandon](/dotnet/api/microsoft.servicebus.messaging.queueclient.abandon) lub go nie rób nic i pozwól blokady upłynąć.

Jeśli odbieranie klient nie może przetworzyć komunikatu i wie, że redelivering komunikat i ponownym wykonaniem operacji nie pomoże, można go odrzucić komunikatu, która przenosi je do kolejki utraconych wiadomości, wywołując [utraconych](/dotnet/api/microsoft.servicebus.messaging.queueclient.deadletter), który również Umożliwia ustawienie właściwości niestandardowej, w tym kod przyczyny, które można pobrać za pomocą komunikatu z kolejki utraconych wiadomości.

Przypadek specjalny rozliczenia jest opóźnienia, która została omówiona w oddzielnym artykule.

**Complete** lub **utraconych** operacje, jak również **RenewLock** operacje mogą kończyć się niepowodzeniem z powodu problemów z siecią wygasł gruncie blokadę, czy istnieją inne warunki po stronie usługi, które uniemożliwiają rozliczenia. W jednym z ostatnich przypadkach usługa wysyła negatywnego potwierdzenia który uwypukli najistotniejsze jako wyjątek w klientach interfejsu API. Jeśli przyczyną jest przerwane połączenie, blokada zostało porzucone, ponieważ usługa Service Bus nie obsługuje odzyskiwania istniejących powiązań protokołu AMQP na inne połączenie.

Jeśli **Complete** kończy się niepowodzeniem, w których występuje zwykle na samym końcu obsługi wiadomości, a w niektórych przypadkach po minut pracy przetwarzania aplikacja odbierająca może zdecydować, czy zachowuje stan pracy i ignoruje takie same komunikat po dostarczeniu po raz drugi lub czy tosses limit wyników pracy i ponawia próbę co komunikat jest dostarczony ponownie.

Typowe mechanizm do identyfikowania dostaw zduplikowany komunikat jest, sprawdzając identyfikator wiadomości, co może i powinna być ustawiona przez nadawcę do unikatowej wartości prawdopodobnie są wyrównane z identyfikatorem procesu źródłowy. Harmonogram zadań będzie prawdopodobnie ustawienie message ID. Identyfikator zadania, które podejmuje próbę przypisania do procesu roboczego z danego procesu roboczego i proces roboczy będzie Ignoruj drugie wystąpienie przypisania zadania, jeśli zadania zostało już przeprowadzone.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat obsługi komunikatów usługi Service Bus, zobacz następujące tematy:

* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

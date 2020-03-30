---
title: Transfery, blokady i rozliczenia komunikatów usługi Azure Service Bus
description: Ten artykuł zawiera omówienie transferu komunikatów usługi Azure Service Bus, blokad i operacji rozliczenia.
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
ms.date: 01/24/2019
ms.author: aschhab
ms.openlocfilehash: a2c353d612280981a83b32463d34efdc70878495
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260997"
---
# <a name="message-transfers-locks-and-settlement"></a>Transferowanie, blokady i uzgadnianie komunikatów

Centralną możliwością brokera komunikatów, takich jak usługa Service Bus, jest akceptowanie wiadomości w kolejce lub temacie i utrzymywanie ich dostępnych do późniejszego pobierania. *Wyślij* to termin, który jest powszechnie używany do przesyłania wiadomości do brokera wiadomości. *Receive* to termin powszechnie używany do przenoszenia wiadomości do klienta pobierającego.

Gdy klient wysyła wiadomość, zwykle chce wiedzieć, czy wiadomość została prawidłowo przeniesiona do i zaakceptowana przez brokera lub czy wystąpił jakiś błąd. To pozytywne lub negatywne potwierdzenie rozlicza klienta i brokera zrozumienia stanu transferu wiadomości i w ten sposób jest określany jako *rozliczenie*.

Podobnie, gdy broker przekazuje wiadomość do klienta, broker i klient chcą ustalić, czy wiadomość została pomyślnie przetworzona i w związku z tym może zostać usunięta, czy też dostarczenie lub przetworzenie wiadomości nie powiodło się, a tym samym może być jeszcze trzeba ponownie dostarczyć wiadomość.

## <a name="settling-send-operations"></a>Rozliczanie operacji wysyłania

Przy użyciu dowolnego z obsługiwanych klientów interfejsu API usługi Service Bus, wysyłanie operacji do usługi Service Bus są zawsze jawnie rozliczane, co oznacza, że operacja interfejsu API czeka na wynik akceptacji z usługi Service Bus, aby dojść do przybycia, a następnie kończy operację wysyłania.

Jeśli wiadomość zostanie odrzucona przez usługę Service Bus, odrzucenie zawiera wskaźnik błędu i tekst z "identyfikatorem śledzenia" wewnątrz niego. Odrzucenie zawiera również informacje o tym, czy operacja może zostać ponowiona z wszelkimi oczekiwaniami powodzenia. W kliencie te informacje są przekształcane w wyjątek i wywoływane do wywołującego operacji wysyłania. Jeśli wiadomość została zaakceptowana, operacja w trybie cichym zostanie zakończona.

Podczas korzystania z protokołu AMQP, który jest wyłącznym protokołem dla klienta .NET Standard i klienta Java i [który jest opcją dla klienta .NET Framework,](service-bus-amqp-dotnet.md)transfery i rozliczenia wiadomości są potokowane i całkowicie asynchroniczne i zaleca się użycie wariantów asynchronicznie modelu programowania INTERFEJSU API.

Nadawca może umieścić kilka wiadomości w sieci w krótkim odstępie czasu bez konieczności oczekiwania na każdą wiadomość, która ma zostać potwierdzona, jak w przypadku protokołu SBMP lub HTTP 1.1. Te operacje asynchroniczne wysyłania zakończyć, jak odpowiednie komunikaty są akceptowane i przechowywane, na podzielonych jednostek lub podczas wysyłania operacji do różnych jednostek nakładają się. Uzupełnienia mogą również wystąpić z oryginalnego zamówienia wysyłania.

Strategia obsługi wyników operacji wysyłania może mieć natychmiastowy i znaczący wpływ na wydajność aplikacji. Przykłady w tej sekcji są zapisywane w języku C# i stosuje się równoważne dla java futures.

Jeśli aplikacja generuje wybuchy wiadomości, zilustrowane tutaj z zwykłą pętlą i miały czekać na zakończenie każdej operacji wysyłania przed wysłaniem następnej wiadomości, synchroniczne lub asynchroniczne kształty interfejsu API, wysyłanie 10 wiadomości kończy się dopiero po 10 sekwencyjne pełne przeloty w obie strony do rozliczenia.

Przy zakładanej odległości opóźnienia tcp 70 milisekundy z lokacji lokalnej do usługi Service Bus i dając tylko 10 ms dla usługi Service Bus do zaakceptowania i przechowywania każdej wiadomości, następująca pętla trwa co najmniej 8 sekund, nie licząc czasu transferu ładunku lub potencjału skutki zatorów na trasie:

```csharp
for (int i = 0; i < 100; i++)
{
  // creating the message omitted for brevity
  await client.SendAsync(…);
}
```

Jeśli aplikacja uruchamia 10 asynchronicznych operacji wysyłania w bezpośredniej kolejności i oczekuje na ich odpowiednie zakończenie oddzielnie, czas podróży w obie strony dla tych 10 operacji wysyłania nakłada się. 10 wiadomości są przesyłane w bezpośredniej kolejności, potencjalnie nawet udostępnianie ramek TCP, a ogólny czas trwania transferu w dużej mierze zależy od czasu związanego z siecią, aby uzyskać wiadomości przesłane do brokera.

Przywdziewając te same założenia, co w poprzedniej pętli, całkowity nakładający się czas wykonywania dla następującej pętli może pozostać znacznie poniżej jednej sekundy:

```csharp
var tasks = new List<Task>();
for (int i = 0; i < 100; i++)
{
  tasks.Add(client.SendAsync(…));
}
await Task.WhenAll(tasks);
```

Należy pamiętać, że wszystkie modele programowania asynchroniiowego używają jakiejś formy opartej na pamięci, ukrytej kolejki pracy, która przechowuje oczekujące operacje. Po powrocie [SendAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.sendasync#Microsoft_Azure_ServiceBus_QueueClient_SendAsync_Microsoft_Azure_ServiceBus_Message_) (C#) lub **Send** (Java) zadanie wysyłania jest umieszczane w kolejce w kolejce do pracy, ale gest protokołu rozpoczyna się dopiero wtedy, gdy zostanie uruchomione zadanie. W przypadku kodu, który ma tendencję do wypychania serii wiadomości i gdzie niezawodność jest problemem, należy uważać, aby nie zbyt wiele wiadomości zostało umieszczonych "w locie" naraz, ponieważ wszystkie wysłane wiadomości zajmują pamięć, dopóki nie zostaną faktycznie umieszczone na przewodzie.

Semafory, jak pokazano w poniższym fragmentie kodu w języku C#, są obiekty synchronizacji, które umożliwiają takie ograniczanie na poziomie aplikacji w razie potrzeby. To użycie semafora pozwala na co najwyżej 10 wiadomości, aby być w locie na raz. Jeden z 10 dostępnych blokad semafora jest pobierana przed wysłaniem i jest zwalniany po zakończeniu wysyłania. Jedenaste przejście przez pętlę czeka, aż co najmniej jeden z wcześniejszych wysyła zakończy się, a następnie udostępnia blokadę:

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

Aplikacje **nigdy nie** powinny inicjować asynchroniczny wysłać operacji w sposób "ognia i zapomnij" bez pobierania wyników operacji. W ten sposób można załadować wewnętrzną i niewidoczną kolejkę zadań do wyczerpania pamięci i uniemożliwić aplikacji wykrywanie błędów wysyłania:

```csharp
for (int i = 0; i < 100; i++)
{

  client.SendAsync(message); // DON’T DO THIS
}
```

W kliencie AMQP niskiego poziomu usługa Service Bus akceptuje również "wstępnie rozliczone" transfery. Wstępnie rozliczone transfer jest fire-and-forget operacji, dla których wynik, tak czy inaczej, nie jest zgłaszane z powrotem do klienta i wiadomość jest uważany za rozliczony po wysłaniu. Brak opinii do klienta oznacza również, że nie ma żadnych dostępnych danych zasłyniętych do diagnostyki, co oznacza, że ten tryb nie kwalifikuje się do pomocy za pośrednictwem pomocy technicznej platformy Azure.

## <a name="settling-receive-operations"></a>Rozliczanie operacji odbioru

W przypadku operacji odbierania klienci interfejsu API usługi Service Bus włączą dwa różne tryby jawne: *Odbieranie i usuwanie* oraz *Zaglądaj do bloku*.

### <a name="receiveanddelete"></a>ReceiveAndDelete (ReceiveAndDelete)

Tryb [Receive-and-Delete](/dotnet/api/microsoft.servicebus.messaging.receivemode) informuje brokera, aby rozważył wszystkie wiadomości wysyłane do klienta odbierającego jako rozliczone po wysłaniu. Oznacza to, że wiadomość jest uważana za zużyte, jak tylko broker umieścił go na przewodach. Jeśli transfer wiadomości nie powiedzie się, wiadomość zostanie utracona.

Plusem tego trybu jest to, że odbiorca nie musi podejmować dalszych działań w sprawie wiadomości, a także nie jest spowolniony, czekając na wynik ugody. Jeśli dane zawarte w poszczególnych wiadomościach mają niską wartość i/lub mają znaczenie tylko przez bardzo krótki czas, ten tryb jest rozsądnym wyborem.

### <a name="peeklock"></a>PeekLock (Śluza zagląda

Tryb [Peek-Lock](/dotnet/api/microsoft.servicebus.messaging.receivemode) informuje brokera, że klient odbierający chce jawnie rozliczyć odebrane wiadomości. Komunikat jest udostępniany dla odbiornika do przetwarzania, podczas gdy przechowywane pod wyłączną blokadę w usłudze tak, że inne, konkurencyjne odbiorniki nie widzą go. Czas trwania blokady jest początkowo zdefiniowany na poziomie kolejki lub subskrypcji i może zostać przedłużony przez klienta posiadającego blokadę za pośrednictwem operacji [RenewLock.](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_RenewLockAsync_System_String_)

Gdy wiadomość jest zablokowana, inni klienci odbierający z tej samej kolejki lub subskrypcji mogą przyjmować blokady i pobierać następne dostępne komunikaty, które nie są w aktywnej blokadzie. Gdy blokada wiadomości jest jawnie zwolniony lub po wygaśnięciu blokady, komunikat pojawia się z powrotem na lub w pobliżu przodu kolejności pobierania dla ponownego dostarczenia.

Gdy wiadomość jest wielokrotnie zwalniana przez odbiorniki lub pozwalają one zablokować upłynąć dla określonej liczby razy ([maxDeliveryCount),](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount#Microsoft_ServiceBus_Messaging_QueueDescription_MaxDeliveryCount)wiadomość jest automatycznie usuwana z kolejki lub subskrypcji i umieszczana w skojarzonej kolejce utraconych wiadomości.

Klient odbierający inicjuje rozliczanie odebranej wiadomości z pozytywnym potwierdzeniem, gdy wywołuje [Complete](/dotnet/api/microsoft.servicebus.messaging.queueclient.complete#Microsoft_ServiceBus_Messaging_QueueClient_Complete_System_Guid_) na poziomie interfejsu API. Oznacza to brokerowi, że wiadomość została pomyślnie przetworzona, a wiadomość zostanie usunięta z kolejki lub subskrypcji. Broker odpowiada na zamiar rozliczenia odbiorcy z odpowiedzią, która wskazuje, czy rozliczenie może być wykonane.

Gdy klient odbierający nie może przetworzyć wiadomości, ale chce, aby wiadomość została ponownie dostarczona, może jawnie poprosić o natychmiastowe zwolnienie i odblokowanie wiadomości przez [wywołanie Abandon](/dotnet/api/microsoft.servicebus.messaging.queueclient.abandon) lub nie może nic zrobić i pozwolić na upłynie blokady.

Jeśli klient odbierający nie przetwarza wiadomości i wie, że ponowne dostarczenie wiadomości i ponowienie próby wykonania operacji nie pomoże, może odrzucić wiadomość, która przenosi ją do kolejki utraconych wiadomości, wywołując [deadletter](/dotnet/api/microsoft.servicebus.messaging.queueclient.deadletter), który umożliwia również ustawienie właściwości niestandardowej, w tym kodu przyczyny, który można pobrać z wiadomością z kolejki utraconych wiadomości.

Szczególnym przypadkiem rozliczenia jest odroczenie, które zostało omówione w osobnym artykule.

Operacje **Complete** lub **Deadletter,** a także operacje **RenewLock** mogą zakończyć się niepowodzeniem z powodu problemów z siecią, jeśli blokada wstrzymana wygasła lub istnieją inne warunki po stronie usługi, które uniemożliwiają rozliczenie. W jednym z tych ostatnich przypadków usługa wysyła negatywne potwierdzenie, że powierzchnie jako wyjątek w klientach interfejsu API. Jeśli przyczyną jest przerwane połączenie sieciowe, blokada jest odrzucana, ponieważ usługa Service Bus nie obsługuje odzyskiwania istniejących łączy AMQP w innym połączeniu.

Jeśli **Complete** nie powiedzie się, co występuje zazwyczaj na samym końcu obsługi wiadomości i w niektórych przypadkach po minutach pracy przetwarzania, odbierająca aplikacja może zdecydować, czy zachowuje stan pracy i ignoruje ten sam komunikat, gdy jest dostarczany po raz drugi lub czy rzuca się wynik pracy i ponawia próby, gdy wiadomość jest ponownie dostarczana.

Typowy mechanizm identyfikacji zduplikowanych dostaw wiadomości polega na sprawdzeniu identyfikatora wiadomości, który może i powinien być ustawiony przez nadawcę na unikatową wartość, ewentualnie wyrównaną z identyfikatorem z procesu źródłowego. Harmonogram zadań prawdopodobnie ustawi identyfikator wiadomości na identyfikator zadania, które próbuje przypisać do pracownika z danym pracownikiem, a pracownik zignoruje drugie wystąpienie zadania, jeśli to zadanie zostało już wykonane.

> [!IMPORTANT]
> Ważne jest, aby pamiętać, że blokada, którą PeekLock nabywa w wiadomości, jest niestabilna i może zostać utracona w następujących warunkach
>   * Aktualizacja usługi
>   * Aktualizacja systemu operacyjnego
>   * Zmiana właściwości w jednostce (Kolejka, Temat, Subskrypcja) przy jednoczesnymtrzymując blokadę.
>
> Gdy blokada zostanie utracona, usługa Azure Service Bus wygeneruje LockLostException, który zostanie upobieżeniowy w kodzie aplikacji klienckiej. W takim przypadku domyślna logika ponawiania próby klienta powinna automatycznie rozpocząć i ponowić próbę wykonania operacji.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o wiadomościach usługi Service Bus, zobacz następujące tematy:

* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

---
title: Najlepsze rozwiązania dotyczące zwiększania wydajności przy użyciu usługi Azure Service Bus | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak używać usługi Service Bus do optymalizacji wydajności podczas wymiany komunikatów obsługiwanych przez brokera.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 09/14/2018
ms.author: aschhab
ms.openlocfilehash: edd7a397598bcb5941f3ac1b29d385d6eac40f8d
ms.sourcegitcommit: 41015688dc94593fd9662a7f0ba0e72f044915d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/11/2019
ms.locfileid: "59501641"
---
# <a name="best-practices-for-performance-improvements-using-service-bus-messaging"></a>Najlepsze rozwiązania zwiększające wydajność przy użyciu komunikatów usługi Service Bus

W tym artykule opisano sposób użycia usługi Azure Service Bus do optymalizacji wydajności podczas wymiany komunikatów obsługiwanych przez brokera. Pierwsza część w tym artykule opisano różne mechanizmy, które są oferowane w celu ułatwienia zwiększenia wydajności. Druga sekcja zawiera wskazówki dotyczące sposobu używania usługi Service Bus w sposób zapewniający najlepszą wydajność w danym scenariuszu.

W tym artykule termin "klient" oznacza każda jednostka, która uzyskuje dostęp do usługi Service Bus. Klient może potrwać roli nadawcy i adresata. Termin "sender" jest używana dla usługi Service Bus kolejki lub tematu klienta, który wysyła wiadomości do kolejki lub tematu subskrypcji usługi Service Bus. Termin "odbiornik" odnosi się do usługi Service Bus kolejki lub subskrypcji klienta, który odbiera komunikaty z kolejki usługi Service Bus lub subskrypcji.

Poniższe sekcje wprowadzić kilka koncepcji, które korzysta z usługi Service Bus ułatwia zwiększanie wydajności.

## <a name="protocols"></a>Protokoły

Usługa Service Bus umożliwia klientom wysyłanie i odbieranie wiadomości przy użyciu jednej z trzech protokołów:

1. Advanced Message Queuing Protocol (AMQP)
2. Protokół (SBMP) komunikatów usługi Service Bus
3. HTTP

Protokół AMQP i SBMP są bardziej wydajne, ponieważ obsługa połączenia usługi Service Bus, tak długo, jak istnieje fabryki obsługi komunikatów. Implementuje również przetwarzanie wsadowe i pobieranie z wyprzedzeniem. Chyba że jawnie wymienione całej zawartości w tym artykule założono użycie połączeń AMQP lub SBMP.

## <a name="reusing-factories-and-clients"></a>Ponowne użycie fabryk i klientów

Klient usługi Service Bus obiekty, takie jak [QueueClient] [ QueueClient] lub [MessageSender][MessageSender], są tworzone za pomocą [ MessagingFactory] [ MessagingFactory] obiektu, który udostępnia również zarządzania wewnętrznych połączeń. Zaleca się, że nie zamkniesz fabryki obsługi komunikatów lub kolejki, tematu i subskrypcji klientów po wysłać wiadomość, a następnie ponownie utwórz je, podczas przesyłania komunikatu dalej. Zamykanie fabryki obsługi komunikatów usuwa połączenia z usługą Service Bus, a nowe połączenie zostanie nawiązane, podczas ponownego tworzenia fabryki. Nawiązując połączenie jest kosztowną operacją, który można uniknąć przez ponowne użycie tego samego fabryki i obiekty klienta dla wielu operacji. Bezpiecznie służy tych obiektów klienta współbieżnych operacji asynchronicznych i z wielu wątków. 

## <a name="concurrent-operations"></a>Równoczesne wykonywanie operacji

Wykonanie operacji (wysyłanie, odbieranie, usunąć itp.) zajmuje trochę czasu. Teraz obejmuje przetwarzanie operacji w usłudze Service Bus, poza tym zwiększa opóźnienia żądania i odpowiedzi. Aby zwiększyć liczbę operacji na czas, jednocześnie muszą wykonać operacje. 

Klient planuje jednoczesnych operacji za wykonywanie operacji asynchronicznych. Kolejne żądanie jest uruchamiane przed zakończeniem poprzedniego żądania. Poniższy fragment kodu jest przykładem operacja asynchronicznego wysyłania:
  
 ```csharp
  Message m1 = new BrokeredMessage(body);
  Message m2 = new BrokeredMessage(body);
  
  Task send1 = queueClient.SendAsync(m1).ContinueWith((t) => 
    {
      Console.WriteLine("Sent message #1");
    });
  Task send2 = queueClient.SendAsync(m2).ContinueWith((t) => 
    {
      Console.WriteLine("Sent message #2");
    });
  Task.WaitAll(send1, send2);
  Console.WriteLine("All messages sent");
  ```
  
  Poniższy kod jest przykładem asynchronicznej operacji odbioru. Zobacz pełny program [tutaj](https://github.com/Azure/azure-service-bus/blob/master/samples/DotNet/Microsoft.Azure.ServiceBus/SendersReceiversWithQueues):
  
  ```csharp
  var receiver = new MessageReceiver(connectionString, queueName, ReceiveMode.PeekLock);
  var doneReceiving = new TaskCompletionSource<bool>();

  receiver.RegisterMessageHandler(...);
  ```

## <a name="receive-mode"></a>Tryb odbierania

Podczas tworzenia kolejki lub subskrypcji klienta, należy określić tryb odbierania: *Odbierz* lub *odbieranie i usuwanie*. Domyślny tryb odbierania jest [PeekLock][PeekLock]. Podczas pracy w tym trybie, klient wysyła żądanie, aby komunikat o błędzie z usługi Service Bus. Po otrzymaniu komunikatu klient wysyła żądanie można zakończyć wiadomości.

Po ustawieniu trybu odbioru na [ReceiveAndDelete][ReceiveAndDelete], oba kroki są połączone w jedno żądanie. Te kroki zmniejszyć ogólną liczbę operacji i może zwiększyć ogólną przepływność komunikatów. Jest to bardziej wydajne ryzyko utraty wiadomości.

Usługa Service Bus nie obsługuje transakcji dla operacji odbierania i usuwania. Ponadto semantyki Odbierz są wymagane przez wszystkie scenariusze, w których klient chce mają być odroczone lub [utraconych](service-bus-dead-letter-queues.md) wiadomość.

## <a name="client-side-batching"></a>Przetwarzanie wsadowe po stronie klienta

Przetwarzanie wsadowe po stronie klienta umożliwia klientowi kolejki lub tematu opóźnić wysyłanie wiadomości przez pewien czas. Jeśli klient wysyła dodatkowe komunikaty w tym okresie, przesyła komunikaty w jednej partii. Przetwarzanie wsadowe po stronie klienta również powoduje, że kolejki lub subskrypcji klienta partii wielu **Complete** żądań w pojedynczym żądaniu. Przetwarzanie wsadowe jest dostępna tylko dla asynchronicznego **wysyłania** i **Complete** operacji. Synchroniczne operacje są natychmiast wysyłane do usługi Service Bus. Przetwarzanie wsadowe nie wystąpi dla podglądu ani operacji odbioru nie jest przetwarzanie wsadowe wykonywane na klientach.

Domyślnie klient używa usługi batch interwał 20 ms. Można zmienić interwał przetwarzania wsadowego, ustawiając [BatchFlushInterval] [ BatchFlushInterval] właściwości przed utworzeniem fabryki obsługi komunikatów. To ustawienie dotyczy wszystkich klientów, które są tworzone przez tę fabrykę.

Aby wyłączyć, przetwarzanie wsadowe, ustaw [BatchFlushInterval] [ BatchFlushInterval] właściwości **TimeSpan.Zero**. Na przykład:

```csharp
MessagingFactorySettings mfs = new MessagingFactorySettings();
mfs.TokenProvider = tokenProvider;
mfs.NetMessagingTransportSettings.BatchFlushInterval = TimeSpan.FromSeconds(0.05);
MessagingFactory messagingFactory = MessagingFactory.Create(namespaceUri, mfs);
```

Przetwarzanie wsadowe nie wpływa na liczbę płatnych operacji obsługi komunikatów i jest dostępna tylko dla usługi Service Bus klienta protokołu za pomocą [Microsoft.ServiceBus.Messaging](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) biblioteki. Protokół HTTP nie obsługuje przetwarzanie wsadowe.

## <a name="batching-store-access"></a>Przetwarzanie wsadowe dostęp do sklepu

Aby zwiększyć przepływność kolejki, tematu lub subskrypcji, usługi Service Bus partii wiele wiadomości, czy zapisuje jego wewnętrznego magazynu. Jeśli włączona dla kolejki lub tematu, będzie wsadowej zapisywania komunikatów w magazynie. Jeśli włączone, w obrębie kolejki lub subskrypcji, usuwanie wiadomości ze sklepu będzie partii. Jeśli dostęp do sklepu wsadowej jest włączona dla jednostki, usługi Service Bus opóźnia dotyczące tej jednostki operacji zapisu magazynu przez maksymalnie 20 ms. 

> [!NOTE]
> Istnieje ryzyko utraty komunikatów za pomocą adapterów przetwarzania wsadowego, nawet jeśli wystąpi awaria usługi Service Bus na końcu interwału do przetwarzania wsadowego 20 MS. 

Operacje magazynu dodatkowego, występujące w danym przedziale czasu są dodawane do usługi batch. Wsadowe wpływa tylko na dostęp do magazynu **wysyłania** i **Complete** operacji odbierania nie wpływają na operacje. Dostęp do sklepu wsadowej jest właściwością w jednostce. Przetwarzanie wsadowe odbywa się we wszystkich jednostek, które umożliwiają dostęp do sklepu wsadowej.

Podczas tworzenia nowej kolejki, tematu lub subskrypcji, dostęp do sklepu wsadowej jest domyślnie włączona. Aby wyłączyć dostęp do sklepu wsadowych, należy ustawić [EnableBatchedOperations] [ EnableBatchedOperations] właściwości **false** przed utworzeniem jednostki. Na przykład:

```csharp
QueueDescription qd = new QueueDescription();
qd.EnableBatchedOperations = false;
Queue q = namespaceManager.CreateQueue(qd);
```

Dostęp do sklepu wsadowej nie wpływa na liczbę płatnych operacji obsługi komunikatów i jest właściwością kolejki, tematu lub subskrypcji. Jest ono niezależne od trybu odbioru i protokół, który jest używany między klientem a usługą Service Bus.

## <a name="prefetching"></a>Trwa pobieranie z wyprzedzeniem

[Trwa pobieranie z wyprzedzeniem](service-bus-prefetch.md) umożliwia klientowi kolejki lub subskrypcji, można załadować dodatkowych komunikatów z usługi podczas wykonywania operacji odbierania. Klient przechowuje te komunikaty w lokalnej pamięci podręcznej. Rozmiar pamięci podręcznej jest określany przez [QueueClient.PrefetchCount] [ QueueClient.PrefetchCount] lub [SubscriptionClient.PrefetchCount] [ SubscriptionClient.PrefetchCount] właściwości. Każdy klient, który umożliwia pobieranie z wyprzedzeniem zachowuje swój własny pamięci podręcznej. Pamięć podręczna nie są współdzielone przez klientów. Jeśli operacja odbioru inicjowania przez klienta i jego pamięci podręcznej jest pusta, usługa przesyła partię komunikatów. Rozmiar partii jest równe wielkości pamięci podręcznej lub 256 KB, w zależności jest mniejsze. Jeśli operacja odbioru inicjowania przez klienta i pamięć podręczna zawiera komunikat, komunikat jest pobierana z pamięci podręcznej.

Gdy komunikat jest pobieranych z wyprzedzeniem, usługa blokuje komunikat pobieranych z wyprzedzeniem. Z blokadą pobieranych z wyprzedzeniem komunikat nie może otrzymać inny odbiornik. Jeśli odbiornik nie może ukończyć komunikatu przed wygaśnięciem blokady, wiadomość zostaje udostępniony innym odbiornikom. Pobieranych z wyprzedzeniem kopię wiadomości pozostaje w pamięci podręcznej. Odbiornik zużywa wygasłe buforowana kopia otrzymasz wyjątek podczas próby wykonania tego komunikatu. Domyślnie wygaśnięciu blokady komunikat po 60 sekundach. Tę wartość można rozszerzyć na 5 minut. Aby uniknąć użycia wygasłe wiadomości, rozmiar pamięci podręcznej powinny zawsze być mniejszy niż liczba wiadomości, które mogą być wykorzystane przez klienta w ramach interwał limitu czasu blokady.

Korzystając z domyślnego czasu wygaśnięcia blokady 60 sekund, wartość jest dobrą [PrefetchCount] [ SubscriptionClient.PrefetchCount] maksymalnie 20 razy przetwarza stawki wszystkich odbiorców fabryki. Na przykład fabrykę tworzy trzy odbiorców, a każdy odbiorca może przetwarzać do 10 komunikatów na sekundę. Liczba pobierania z wyprzedzeniem nie powinna przekraczać 20 X 3 X 10 = 600. Domyślnie [PrefetchCount] [ QueueClient.PrefetchCount] jest ustawiona na 0, co oznacza, że nie dodatkowe komunikaty pobrane z usługi.

Pobieranie z wyprzedzeniem wiadomości zwiększa ogólną przepływność kolejki lub subskrypcji, ponieważ zmniejsza ogólną liczbę operacji wiadomości lub wystąpień komunikacji dwustronnej. Pobieranie pierwszy komunikat, jednak będzie trwać dłużej (ze względu na rozmiar zwiększone komunikat). Odbieranie komunikatów pobieranych z wyprzedzeniem będzie przebiegać szybciej, ponieważ te komunikaty zostały już pobrane przez klienta.

Time to live (TTL) właściwość wiadomości jest sprawdzana przez serwer w momencie serwer wysyła wiadomość do klienta. Klient nie sprawdza właściwości TTL wiadomości po odebraniu wiadomości. Zamiast tego może zostać odebrany komunikat, nawet, jeśli czas wygaśnięcia komunikatu został przekazany, gdy komunikat był buforowany przez klienta.

Trwa pobieranie z wyprzedzeniem nie wpływa na liczbę płatnych operacji obsługi komunikatów i jest dostępna tylko dla protokołu klienta usługi Service Bus. Protokół HTTP nie obsługuje pobieranie z wyprzedzeniem. Trwa pobieranie z wyprzedzeniem jest dostępna dla operacji odbioru synchroniczne i asynchroniczne.

## <a name="prefetching-and-receivebatch"></a>Trwa pobieranie z wyprzedzeniem i ReceiveBatch

Podczas gdy koncepcji pobieranie z wyprzedzeniem wielu komunikatów jednocześnie mają podobną semantyką na przetwarzanie komunikatów w partii (ReceiveBatch), istnieją pewne niewielkie różnice, które muszą być przechowywane na uwadze, podczas korzystania z je razem.

Pobieranie z wyprzedzeniem to konfiguracji (lub tryb) na komputerze klienckim (QueueClient i SubscriptionClient) i ReceiveBatch jest operacją (który ma semantykę odpowiedź na żądanie).

Podczas korzystania z tych razem, należy wziąć pod uwagę następujące przypadki-

* Pobieranie z wyprzedzeniem powinna być większa lub równa liczbie wiadomości, które są oczekiwane, odbierać z ReceiveBatch.
* Pobieranie z wyprzedzeniem może zawierać maksymalnie n/3 razy liczba komunikatów przetwarzanych na sekundę, gdzie n to domyślny czas trwania blokady.

Istnieją niektóre wyzwania, wymaga zachłanne podejście (czyli przechowywanie dużej liczby pobieranie z wyprzedzeniem), ponieważ zakłada się, że wiadomość jest ograniczona do określonego odbiornika. Zalecane jest, aby spróbować się pobrana z wyprzedzeniem wartości między wartościami progowymi wymienionych powyżej i empirically zidentyfikować, co odpowiada wymaganiom.

## <a name="multiple-queues"></a>Wielu kolejek

Jeśli oczekiwane obciążenia nie mogą być obsługiwane przez pojedynczy podzieleniu kolejki lub tematu, należy użyć wielu jednostek obsługi komunikatów. Korzystając z wielu jednostek, Utwórz dedykowane klienta dla każdej jednostki, zamiast korzystać z tego samego klienta dla wszystkich jednostek.

## <a name="development-and-testing-features"></a>Programowanie i testowanie funkcji

Usługa Service Bus ma jedną funkcję, używana specjalnie dla rozwoju, który **nie mogą być używane w konfiguracjach produkcji**: [TopicDescription.EnableFilteringMessagesBeforePublishing][].

Gdy nowe reguły lub filtry są dodawane do tematu, można użyć [TopicDescription.EnableFilteringMessagesBeforePublishing][] Aby sprawdzić, czy nowe wyrażenie filtru działa zgodnie z oczekiwaniami.

## <a name="scenarios"></a>Scenariusze

W poniższych sekcjach opisano typowe scenariusze dotyczące przesyłania komunikatów i opisują preferowanych ustawień usługi Service Bus. Przepustowość są klasyfikowane jako małe (mniej niż 1 komunikatów na sekundę), średnie (1 komunikatów na sekundę lub większą, ale mniej niż 100 komunikatów na sekundę) i Wysoka (100 wiadomości/drugi lub nowszej). Liczba klientów są klasyfikowane jako mała liczba godzin (5 lub mniej), średni (więcej niż 5, ale mniej niż 20) i duże (więcej niż 20).

### <a name="high-throughput-queue"></a>Kolejki o wysokiej przepływności

Cel: Maksymalizuj przepływności pojedynczej kolejki. Liczba nadawców i odbiorców jest mały.

* Aby zwiększyć ogólną szybkość wysyłania do kolejki, umożliwia tworzenie nadawców wiele fabryk komunikatów. Dla każdego nadawcy użyj operacji asynchronicznych lub wielu wątków.
* Aby zwiększyć ogólną szybkość odbierania z kolejki, umożliwia tworzenie odbiorników wiele fabryk komunikatów.
* Operacje asynchroniczne umożliwiają korzystanie z zalet przetwarzanie wsadowe po stronie klienta.
* Ustaw interwał przetwarzania wsadowego do 50 ms, aby zmniejszyć liczbę transmisji protokołu klienta usługi Service Bus. Jeśli wiele nadawcy są używane, należy zwiększyć interwał przetwarzania wsadowego do 100 ms.
* Pozostawienie włączonego dostępu do magazynu wsadowej. Ten dostęp, zwiększa ogólną szybkość jaką komunikaty mogą być zapisywane w kolejce.
* Ustaw liczbę pobieranie z wyprzedzeniem do 20 razy maksymalnej szybkości przetwarzania wszystkich odbiorników fabryki. Ten licznik zmniejsza liczbę transmisji protokołu klienta usługi Service Bus.
* Na użytek podzieleniu kolejki lepszą wydajność i dostępność.

### <a name="multiple-high-throughput-queues"></a>Wielu kolejek o wysokiej przepływności

Cel: Maksymalizuj ogólną przepustowość wielu kolejek. Przepływności pojedynczej kolejki wynoszącej, średni lub wysoki.

Aby uzyskać maksymalną przepływność wiele kolejek, należy użyć ustawienia opisane w celu zmaksymalizowania wydajności z pojedynczą kolejką. Ponadto Użyj różnych fabryk, aby utworzyć klientów, którzy wysłać lub odebrać z różnych kolejek.

### <a name="low-latency-queue"></a>Małe opóźnienia kolejki

Cel: Zminimalizować opóźnienie end-to-end kolejki lub tematu. Liczba nadawców i odbiorców jest mały. Przepływność kolejki jest mały lub Średni.

* Wyłącz przetwarzanie wsadowe po stronie klienta. Klient natychmiast wysyła wiadomość.
* Wyłącz dostęp do sklepu wsadowej. Usługa od razu zapisuje komunikat w magazynie.
* Jeśli używasz jednego klienta, ustaw 20 razy szybkości przetwarzania odbiornika liczba pobierania z wyprzedzeniem. Jeśli wiele wiadomości do kolejki w tym samym czasie, protokół klienta usługi Service Bus przesyła je wszystkie w tym samym czasie. Gdy klient odbierze następnej wiadomości, ten komunikat jest już w lokalnej pamięci podręcznej. Pamięć podręczna powinna być niewielka.
* Jeśli używasz wielu klientów, należy ustawić na 0 liczba pobierania z wyprzedzeniem. Ustawiając liczbę, drugi klient może odbierać drugi komunikat, podczas gdy pierwszy klient jest nadal przetwarza pierwszego komunikatu.
* Na użytek podzieleniu kolejki lepszą wydajność i dostępność.

### <a name="queue-with-a-large-number-of-senders"></a>Kolejki z dużą liczbą nadawców

Cel: Maksymalizuj przepływność kolejki lub tematu z dużą liczbą nadawcy. Każdy Nadawca wysyła komunikaty z średni współczynnik. Numer odbiorcy jest mały.

Service Bus umożliwia maksymalnie 1000 równoczesnych połączeń jednostki obsługi komunikatów (lub 5000 za pomocą protokołu AMQP). Ten limit jest wymuszane na poziomie przestrzeni nazw i kolejek/tematów/subskrypcji są ograniczone przez limit jednoczesnych połączeń dla jednej przestrzeni nazw. W przypadku kolejek liczba ta jest udostępniana między nadawcami a odbiornikami. Jeśli wszystkie połączenia 1000 są wymagane do nadawców, Zamień kolejki tematu i jednej subskrypcji. Temat akceptuje do 1000 równoczesnych połączeń od nadawcy, podczas gdy subskrypcja akceptuje dodatkowe 1000 równoczesnych połączeń od odbiorcy. Jeśli więcej niż 1000 równoczesnych nadawcy są wymagane, nadawców powinien wysyłać wiadomości protokołu usługi Service Bus za pośrednictwem protokołu HTTP.

Aby zmaksymalizować przepływność, wykonaj następujące czynności:

* Jeśli każdego nadawcy znajduje się w ramach innego procesu, należy użyć pojedynczej fabryki jednego procesu.
* Operacje asynchroniczne umożliwiają korzystanie z zalet przetwarzanie wsadowe po stronie klienta.
* Użyj domyślnego interwału wynoszącego 20 ms dzielenia na partie, aby zmniejszyć liczbę transmisji protokołu klienta usługi Service Bus.
* Pozostawienie włączonego dostępu do magazynu wsadowej. Ten dostęp, zwiększa ogólną szybkość jaką komunikaty mogą być zapisywane do kolejki lub tematu.
* Ustaw liczbę pobieranie z wyprzedzeniem do 20 razy maksymalnej szybkości przetwarzania wszystkich odbiorników fabryki. Ten licznik zmniejsza liczbę transmisji protokołu klienta usługi Service Bus.
* Na użytek podzieleniu kolejki lepszą wydajność i dostępność.

### <a name="queue-with-a-large-number-of-receivers"></a>Kolejki z wieloma odbiornikami

Cel: Maksymalizuj szybkość odbierania kolejki lub subskrypcji z wieloma odbiornikami. Każdy odbiorca odbiera komunikaty z szybkością umiarkowane. Numer nadawcy jest mały.

Usługa Service Bus umożliwia maksymalnie 1000 równoczesnych połączeń do jednostki. Jeśli kolejka wymaga więcej niż 1000 odbiorników, Zamień kolejki tematu i wielu subskrypcji. Każda subskrypcja może obsługiwać maksymalnie 1000 równoczesnych połączeń. Alternatywnie odbiorniki mają dostęp do kolejki za pośrednictwem protokołu HTTP.

Aby zmaksymalizować przepływność, wykonaj następujące czynności:

* Jeśli każdy odbiorca znajduje się w ramach innego procesu, należy użyć pojedynczej fabryki jednego procesu.
* Odbiorniki można użyć operacji synchroniczna lub asynchroniczna. Biorąc pod uwagę szybkość odbierania umiarkowane odbiornika poszczególnych, przetwarzanie wsadowe kompletne żądanie po stronie klienta nie ma wpływu na przepływność odbiorcy.
* Pozostawienie włączonego dostępu do magazynu wsadowej. Ten dostęp zmniejsza całkowite obciążenie jednostki. Zmniejsza to także ogólną szybkość jaką komunikaty mogą być zapisywane do kolejki lub tematu.
* Ustaw liczbę pobierania z wyprzedzeniem małej wartości (na przykład PrefetchCount = 10). Ten licznik zapobiega odbiorniki ze stanu bezczynności, a innym odbiornikom dużej liczby komunikatów, pamięci podręcznej.
* Na użytek podzieleniu kolejki lepszą wydajność i dostępność.

### <a name="topic-with-a-small-number-of-subscriptions"></a>Temat z małą liczbą subskrypcji

Cel: Maksymalizuj przepływność tematu z niewielką liczbę subskrypcji. Wiadomość zostaje odebrana się przez wiele subskrypcji, co oznacza, że szybkość odbierania połączone za pośrednictwem wszystkich subskrypcji jest większa niż szybkość wysyłania. Numer nadawcy jest mały. Numer odbiorcy na subskrypcję jest mały.

Aby zmaksymalizować przepływność, wykonaj następujące czynności:

* Aby zwiększyć ogólną szybkość wysyłania w temacie, umożliwia tworzenie nadawców wiele fabryk komunikatów. Dla każdego nadawcy użyj operacji asynchronicznych lub wielu wątków.
* Aby zwiększyć ogólną szybkość odbierania z subskrypcji, użyj wiele fabryk komunikatów, aby utworzyć odbiorników. Dla każdego adresata użyj operacji asynchronicznych lub wielu wątków.
* Operacje asynchroniczne umożliwiają korzystanie z zalet przetwarzanie wsadowe po stronie klienta.
* Użyj domyślnego interwału wynoszącego 20 ms dzielenia na partie, aby zmniejszyć liczbę transmisji protokołu klienta usługi Service Bus.
* Pozostawienie włączonego dostępu do magazynu wsadowej. Ten dostęp, zwiększa ogólną szybkość jaką komunikaty mogą być zapisywane w temacie.
* Ustaw liczbę pobieranie z wyprzedzeniem do 20 razy maksymalnej szybkości przetwarzania wszystkich odbiorników fabryki. Ten licznik zmniejsza liczbę transmisji protokołu klienta usługi Service Bus.
* Aby uzyskać lepszą wydajność i dostępność kolejki użyć tematu podzielonym na partycje.

### <a name="topic-with-a-large-number-of-subscriptions"></a>Temat z dużej liczby subskrypcji

Cel: Maksymalizuj przepływność tematu z dużą liczbę subskrypcji. Komunikat jest odbierany przez wiele subskrypcji, co oznacza, że szybkość odbierania połączone za pośrednictwem wszystkich subskrypcji jest znacznie większa niż szybkość wysyłania. Numer nadawcy jest mały. Numer odbiorcy na subskrypcję jest mały.

Tematy z dużą liczbą subskrypcje zazwyczaj ujawnić niski ogólną przepływność, ile wszystkie komunikaty są kierowane do wszystkich subskrypcji. Przyczyną jest fakt, że każdy komunikat jest odbierany wiele razy, a wszystkie komunikaty, które są zawarte w temacie i wszystkie jego subskrypcje są przechowywane w tym samym magazynie to niska przepływność. Zakłada się, czy liczba nadawców i liczbę odbiorców na subskrypcję jest mały. Usługa Service Bus obsługuje maksymalnie 2000 subskrypcji w temacie.

Aby zmaksymalizować przepływność, spróbuj wykonać następujące kroki:

* Operacje asynchroniczne umożliwiają korzystanie z zalet przetwarzanie wsadowe po stronie klienta.
* Użyj domyślnego interwału wynoszącego 20 ms dzielenia na partie, aby zmniejszyć liczbę transmisji protokołu klienta usługi Service Bus.
* Pozostawienie włączonego dostępu do magazynu wsadowej. Ten dostęp, zwiększa ogólną szybkość jaką komunikaty mogą być zapisywane w temacie.
* Ustaw liczbę pobieranie z wyprzedzeniem do 20 razy szybkości odbioru oczekiwane w ciągu kilku sekund. Ten licznik zmniejsza liczbę transmisji protokołu klienta usługi Service Bus.
* Aby uzyskać lepszą wydajność i dostępność kolejki użyć tematu podzielonym na partycje.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat optymalizowania wydajności usługi Service Bus, zobacz [partycjonowane jednostki do obsługi komunikatów][Partitioned messaging entities].

[QueueClient]: /dotnet/api/microsoft.azure.servicebus.queueclient
[MessageSender]: /dotnet/api/microsoft.azure.servicebus.core.messagesender
[MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[PeekLock]: /dotnet/api/microsoft.azure.servicebus.receivemode
[ReceiveAndDelete]: /dotnet/api/microsoft.azure.servicebus.receivemode
[BatchFlushInterval]: /dotnet/api/microsoft.servicebus.messaging.messagesender.batchflushinterval
[EnableBatchedOperations]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablebatchedoperations
[QueueClient.PrefetchCount]: /dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount
[SubscriptionClient.PrefetchCount]: /dotnet/api/microsoft.azure.servicebus.subscriptionclient.prefetchcount
[ForcePersistence]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage.forcepersistence
[EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning
[Partitioned messaging entities]: service-bus-partitioning.md
[TopicDescription.EnableFilteringMessagesBeforePublishing]: /dotnet/api/microsoft.servicebus.messaging.topicdescription.enablefilteringmessagesbeforepublishing

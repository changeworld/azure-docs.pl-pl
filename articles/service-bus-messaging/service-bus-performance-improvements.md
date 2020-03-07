---
title: Najlepsze rozwiązania dotyczące poprawy wydajności przy użyciu Azure Service Bus
description: Opisuje, w jaki sposób używać Service Bus do optymalizowania wydajności podczas wymiany komunikatów obsługiwanych przez brokera.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 01/16/2020
ms.author: aschhab
ms.openlocfilehash: 683a28ca3cdabd5a7ffbf6e9ffdc3ed0c58d3247
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78370483"
---
# <a name="best-practices-for-performance-improvements-using-service-bus-messaging"></a>Najlepsze rozwiązania dotyczące ulepszeń wydajności przy użyciu Service Bus obsługi komunikatów

W tym artykule opisano, jak za pomocą Azure Service Bus zoptymalizować wydajność podczas wymiany komunikatów obsługiwanych przez brokera. W pierwszej części tego artykułu opisano różne mechanizmy, które są oferowane w celu zwiększenia wydajności. Druga część zawiera wskazówki dotyczące sposobu używania Service Bus w sposób, który może zaoferować najlepszą wydajność w danym scenariuszu.

W tym artykule termin "klient" odnosi się do każdej jednostki, która uzyskuje dostęp do Service Bus. Klient może przejąć rolę nadawcy lub odbiornika. Termin "Sender" jest używany dla klienta kolejki Service Bus lub tematu, który wysyła komunikaty do kolejki Service Bus lub subskrypcji tematu. Termin "odbiornik" odnosi się do Service Bus kolejki lub klienta subskrypcji, który odbiera wiadomości z kolejki Service Bus lub subskrypcji.

W tych sekcjach wprowadzono kilka koncepcji, których Service Bus używa w celu zwiększenia wydajności.

## <a name="protocols"></a>Protokoły

Service Bus umożliwia klientom wysyłanie i odbieranie komunikatów za pośrednictwem jednego z trzech protokołów:

1. Advanced Message Queuing Protocol (AMQP)
2. Service Bus Messaging Protocol (SBMP)
3. HTTP

AMQP i SBMP są bardziej wydajne, ponieważ utrzymują połączenie z Service Bus, dopóki nie istnieje fabryka komunikatów. Implementuje również przetwarzanie wsadowe i pobieranie z wyprzedzeniem. O ile nie zostało to wyraźnie określone, cała zawartość tego artykułu zakłada użycie AMQP lub SBMP.

## <a name="reusing-factories-and-clients"></a>Używanie fabryk i klientów

Service Bus obiektów klienta, takich jak [QueueClient][QueueClient] lub [MessageSender][MessageSender], są tworzone za pomocą obiektu [MessagingFactory][MessagingFactory] , co zapewnia także wewnętrzne zarządzanie połączeniami. Zaleca się, aby nie zamykać fabryk komunikatów lub kolejek, tematów i subskrypcji klienta po wysłaniu wiadomości, a następnie tworzyć je ponownie po wysłaniu kolejnej wiadomości. Zamknięcie fabryki komunikatów powoduje usunięcie połączenia z usługą Service Bus i nawiązanie nowego połączenia podczas odtwarzania fabryki. Nawiązywanie połączenia jest kosztowną operacją, którą można uniknąć przez ponowne użycie tych samych obiektów Factory i Client dla wielu operacji. Można bezpiecznie używać tych obiektów klienta do równoczesnych operacji asynchronicznych i z wielu wątków. 

## <a name="concurrent-operations"></a>Operacje współbieżne

Wykonywanie operacji (wysyłanie, odbieranie, usuwanie itp.) zajmuje trochę czasu. Teraz obejmuje przetwarzanie operacji w usłudze Service Bus, poza tym zwiększa opóźnienia żądania i odpowiedzi. Aby zwiększyć liczbę operacji na czas, jednocześnie muszą wykonać operacje. 

Klient planuje współbieżne operacje przez wykonywanie operacji asynchronicznych. Następne żądanie zostało uruchomione przed ukończeniem poprzedniego żądania. Poniższy fragment kodu jest przykładem asynchronicznej operacji wysyłania:
  
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
  
  Poniższy kod jest przykładem asynchronicznej operacji odbierania. Zobacz pełny program [tutaj](https://github.com/Azure/azure-service-bus/blob/master/samples/DotNet/Microsoft.Azure.ServiceBus/SendersReceiversWithQueues):
  
  ```csharp
  var receiver = new MessageReceiver(connectionString, queueName, ReceiveMode.PeekLock);
  var doneReceiving = new TaskCompletionSource<bool>();

  receiver.RegisterMessageHandler(...);
  ```

## <a name="receive-mode"></a>Tryb odbioru

Podczas tworzenia kolejki lub klienta subskrypcji można określić tryb odbioru: *Zablokuj* lub *Odbierz i Usuń*. Domyślny tryb odbioru to [PeekLock][PeekLock]. Gdy działa w tym trybie, klient wysyła żądanie odebrania komunikatu z Service Bus. Po odebraniu wiadomości klient wysyła żądanie, aby zakończyć ten komunikat.

Podczas ustawiania trybu odbioru na [ReceiveAndDelete][ReceiveAndDelete], oba kroki są łączone w pojedynczym żądaniu. Te kroki umożliwiają zmniejszenie ogólnej liczby operacji i zwiększenie ogólnej przepływności komunikatów. Ten zysk wydajności jest narażony na ryzyko utraty komunikatów.

Service Bus nie obsługuje transakcji dla operacji odbierania i usuwania. Ponadto semantyka blokady wglądu jest wymagana dla wszystkich scenariuszy, w których klient chce odroczyć lub [martwić](service-bus-dead-letter-queues.md) wiadomość.

## <a name="client-side-batching"></a>Przetwarzanie wsadowe po stronie klienta

Tworzenie wsadowe po stronie klienta umożliwia klientowi kolejki lub tematu opóźnienie wysyłania komunikatu przez określony czas. Jeśli klient wysyła dodatkowe komunikaty w tym okresie, przesyła komunikaty w jednej partii. Przetwarzanie wsadowe po stronie klienta powoduje także, że klient kolejki lub subskrypcji **wykonuje** wsadowe wiele żądań w ramach pojedynczego żądania. Przetwarzanie wsadowe jest dostępne tylko w przypadku asynchronicznych operacji **wysyłania** i **kończenia** . Operacje synchroniczne są natychmiast wysyłane do usługi Service Bus. Przetwarzanie wsadowe nie odbywa się w przypadku operacji wglądu lub odbierania, ani nie występuje przetwarzanie wsadowe na wielu klientach.

Domyślnie klient używa interwału partii 20 ms. Możesz zmienić interwał partii, ustawiając właściwość [BatchFlushInterval][BatchFlushInterval] przed utworzeniem fabryki komunikatów. To ustawienie ma wpływ na wszystkich klientów utworzonych przez tę fabrykę.

Aby wyłączyć tworzenie partii, ustaw właściwość [BatchFlushInterval][BatchFlushInterval] na **TimeSpan. zero**. Na przykład:

```csharp
MessagingFactorySettings mfs = new MessagingFactorySettings();
mfs.TokenProvider = tokenProvider;
mfs.NetMessagingTransportSettings.BatchFlushInterval = TimeSpan.FromSeconds(0.05);
MessagingFactory messagingFactory = MessagingFactory.Create(namespaceUri, mfs);
```

Przetwarzanie wsadowe nie ma wpływu na liczbę operacji do rozliczania komunikatów i jest dostępne tylko dla protokołu klienta Service Bus przy użyciu biblioteki [Microsoft. ServiceBus. Messaging](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) . Protokół HTTP nie obsługuje przetwarzania wsadowego.

> [!NOTE]
> Ustawienie BatchFlushInterval gwarantuje, że przetwarzanie wsadowe jest niejawne w perspektywie aplikacji. oznacza to, że aplikacja wykonuje wywołania SendAsync () i CompleteAsync () i nie tworzy określonych wywołań wsadowych.
>
> Jawne wykonywanie wsadowe po stronie klienta można zaimplementować przy użyciu poniższego wywołania metody- 
> ```csharp
> Task SendBatchAsync (IEnumerable<BrokeredMessage> messages);
> ```
> Łączny rozmiar komunikatów musi być mniejszy niż maksymalny rozmiar obsługiwany przez warstwę cenową.

## <a name="batching-store-access"></a>Dostęp do magazynu wsadowego

Aby zwiększyć przepływność kolejki, tematu lub subskrypcji, Service Bus przetworzyć wiele komunikatów podczas zapisywania ich w magazynie wewnętrznym. Jeśli ta funkcja jest włączona dla kolejki lub tematu, zapisywanie wiadomości w sklepie zostanie przeparte. Jeśli ta funkcja jest włączona dla kolejki lub subskrypcji, usunięcie komunikatów ze sklepu spowoduje przetworzenie ich wsadowych. Jeśli dla jednostki jest włączony dostęp do magazynu w trybie wsadowym, Service Bus opóźni operacji zapisu w sklepie dotyczącej tej jednostki o maksymalnie 20 ms. 

> [!NOTE]
> Nie ma ryzyka utraty komunikatów z przetwarzaniem wsadowym, nawet jeśli wystąpi błąd Service Bus na końcu interwału wsadowego 20ms. 

Dodatkowe operacje magazynu występujące w tym interwale są dodawane do zadania wsadowego. Dostęp do magazynu wsadowego ma wpływ tylko na operacje **wysyłania** i **kończenia** . nie ma to żadnego oddziaływania na operacje odbierania. Dostęp do magazynu wsadowego jest właściwością obiektu. Przetwarzanie wsadowe odbywa się we wszystkich jednostkach, które umożliwiają dostęp do magazynu wsadowego.

Podczas tworzenia nowej kolejki, tematu lub subskrypcji dostęp do magazynu wsadowego jest domyślnie włączony. Aby wyłączyć dostęp do magazynu wsadowego, przed utworzeniem jednostki ustaw właściwość [EnableBatchedOperations][EnableBatchedOperations] na **wartość false** . Na przykład:

```csharp
QueueDescription qd = new QueueDescription();
qd.EnableBatchedOperations = false;
Queue q = namespaceManager.CreateQueue(qd);
```

Dostęp do magazynu wsadowego nie ma wpływu na liczbę operacji związanych z obsługą komunikatów i jest właściwością kolejki, tematu lub subskrypcji. Jest on niezależny od trybu odbioru i protokołu, który jest używany między klientem a usługą Service Bus.

## <a name="prefetching"></a>Wyprzedzeniem

Wstępne [pobieranie](service-bus-prefetch.md) umożliwia klientowi kolejki lub subskrypcji ładowanie dodatkowych komunikatów z usługi podczas wykonywania operacji odbierania. Klient przechowuje te komunikaty w lokalnej pamięci podręcznej. Rozmiar pamięci podręcznej jest określany przez właściwości [QueueClient. PrefetchCount][QueueClient.PrefetchCount] lub [SubscriptionClient. PrefetchCount][SubscriptionClient.PrefetchCount] . Każdy klient, który umożliwia prepobieranie, utrzymuje własną pamięć podręczną. Pamięć podręczna nie jest udostępniana przez klientów. Jeśli klient inicjuje operację odbierania, a jej pamięć podręczna jest pusta, usługa przesyła partię komunikatów. Rozmiar wsadu jest równy rozmiarowi pamięci podręcznej lub 256 KB, w zależności od tego, który jest mniejszy. Jeśli klient inicjuje operację odbierania, a pamięć podręczna zawiera komunikat, komunikat jest pobierany z pamięci podręcznej.

Gdy wiadomość jest pobierana, usługa blokuje pobierany komunikat. Po zablokowaniu nie można odebrać pobranego komunikatu przez innego odbiorcę. Jeśli odbiorca nie może zakończyć komunikatu przed wygaśnięciem blokady, komunikat zostanie udostępniony innym odbiornikom. Prebrana kopia komunikatu pozostaje w pamięci podręcznej. Odbiorca zużywający wygasłą kopię w pamięci podręcznej otrzyma wyjątek podczas próby wykonania tego komunikatu. Domyślnie Blokada wiadomości wygasa po 60 sekundach. Ta wartość może zostać przedłużona do 5 minut. Aby zapobiec zużyciu wygasłych komunikatów, rozmiar pamięci podręcznej powinien zawsze być mniejszy niż liczba komunikatów, które mogą być używane przez klienta w ramach interwału czasu blokady.

W przypadku korzystania z domyślnego czasu blokady 60 sekund, dobrą wartością dla [PrefetchCount][SubscriptionClient.PrefetchCount] jest 20 razy większa szybkość przetwarzania wszystkich odbiorników fabryki. Na przykład fabryka tworzy trzy odbiorniki, a każdy odbiornik może przetwarzać maksymalnie 10 komunikatów na sekundę. Liczba pobieranych odczytów nie powinna przekraczać 20 X 3 X 10 = 600. Domyślnie [PrefetchCount][QueueClient.PrefetchCount] jest ustawiona na 0, co oznacza, że żadne dodatkowe komunikaty nie są pobierane z usługi.

Pobieranie z wyprzedzeniem komunikatów zwiększa ogólną przepływność kolejki lub subskrypcji, ponieważ zmniejsza ogólną liczbę operacji komunikatów lub rundy. Pobieranie pierwszego komunikatu może jednak trwać dłużej (ze względu na zwiększony rozmiar wiadomości). Otrzymywanie niepobranych komunikatów będzie szybsze, ponieważ te komunikaty zostały już pobrane przez klienta.

Właściwość Time-to-Live (TTL) komunikatu jest sprawdzana przez serwer w momencie, gdy serwer wysyła komunikat do klienta programu. Po odebraniu komunikatu klient nie sprawdza właściwości TTL komunikatu. Zamiast tego komunikat można odbierać nawet wtedy, gdy czas wygaśnięcia komunikatu został przekazany, gdy komunikat został zbuforowany przez klienta.

Pobieranie z wyprzedzeniem nie ma wpływu na liczbę operacji wysyłania komunikatów i jest dostępne tylko dla protokołu klienta Service Bus. Protokół HTTP nie obsługuje pobierania z wyprzedzeniem. Pobieranie z wyprzedzeniem jest dostępne zarówno dla operacji synchronicznych, jak i asynchronicznych.

## <a name="prefetching-and-receivebatch"></a>Pobieranie i ReceiveBatch

Chociaż koncepcje pobierania wielu wiadomości razem mają podobną semantykę do przetwarzania komunikatów w partii (ReceiveBatch), istnieją pewne drobne różnice, które należy wziąć pod uwagę, gdy są one używane razem.

Pobieranie z wyprzedzeniem jest konfiguracją (lub trybem) na kliencie (QueueClient i SubscriptionClient), a ReceiveBatch to operacja (z semantyką odpowiedzi na żądanie).

Korzystając z tych funkcji, należy wziąć pod uwagę następujące przypadki:

* Pobieranie z wyprzedzeniem powinno być większe lub równe liczbie komunikatów, które mają być odbierane z ReceiveBatch.
* Pobieranie z wyprzedzeniem może trwać do n/3 razy więcej komunikatów przetworzonych na sekundę, gdzie n to domyślny czas blokady.

Istnieją pewne wyzwania z podejściem zachłanne (tj. utrzymywanie liczby pobierania z wyprzedzeniem), ponieważ oznacza to, że komunikat jest zablokowany dla określonego odbiorcy. Zaleca się wypróbowanie wartości pobieranych z wyprzedzeniem między progami wymienionymi powyżej i empirycznie zidentyfikować, co pasuje.

## <a name="multiple-queues"></a>Wiele kolejek

Jeśli oczekiwane obciążenie nie może być obsługiwane przez pojedynczą kolejkę lub jeden temat, należy użyć wielu jednostek obsługi komunikatów. W przypadku korzystania z wielu jednostek należy utworzyć dedykowanego klienta dla każdej jednostki, zamiast korzystać z tego samego klienta dla wszystkich jednostek.

## <a name="development-and-testing-features"></a>Funkcje deweloperskie i testowe

Service Bus ma jedną funkcję, która została użyta do celów deweloperskich, która **nigdy nie powinna być używana w konfiguracjach produkcyjnych**: [TopicDescription.EnableFilteringMessagesBeforePublishing][].

Po dodaniu nowych reguł lub filtrów do tematu można użyć [TopicDescription.EnableFilteringMessagesBeforePublishing][] , aby sprawdzić, czy nowe wyrażenie filtru działa zgodnie z oczekiwaniami.

## <a name="scenarios"></a>Scenariusze

W poniższych sekcjach opisano typowe scenariusze obsługi komunikatów oraz zawarto szczegółowe informacje o preferowanych ustawieniach Service Bus. Stawki za przepływność są klasyfikowane jako małe (mniej niż 1 komunikat/sekundę), umiarkowane (1 komunikat/sekundę lub większe, ale mniej niż 100 komunikatów/sekundę) i wysoka (100 komunikaty/s lub nowsze). Liczba klientów została sklasyfikowana jako mała (5 lub mniej), umiarkowana (więcej niż 5, ale mniejsza lub równa 20) i duża (więcej niż 20).

### <a name="high-throughput-queue"></a>Kolejka o wysokiej przepływności

Cel: Maksymalizuj przepływność pojedynczej kolejki. Liczba nadawców i odbiorników jest mała.

* Aby zwiększyć ogólną szybkość przesyłania do kolejki, należy użyć wielu fabryk komunikatów do tworzenia nadawców. Dla każdego nadawcy Użyj operacji asynchronicznych lub wielu wątków.
* Aby zwiększyć ogólną szybkość odbierania z kolejki, należy użyć wielu fabryk komunikatów do tworzenia odbiorników.
* Użyj operacji asynchronicznych, aby skorzystać z tworzenia wsadowego po stronie klienta.
* Ustaw interwał przetwarzania wsadowego na 50 ms, aby zmniejszyć liczbę operacji przesyłania Service Bus protokołu klienta. Jeśli używanych jest wiele nadawców, zwiększ interwał przetwarzania wsadowego do 100 ms.
* Pozostaw włączony dostęp do magazynu wsadowego. Ten dostęp zwiększa ogólną szybkość, z jaką komunikaty mogą być zapisywane do kolejki.
* Ustaw liczbę pobrania z wyprzedzeniem na 20 razy większą szybkość przetwarzania wszystkich odbiorników fabryki. Ta liczba zmniejsza liczbę Service Bus transmisjach protokołu klienta.

### <a name="multiple-high-throughput-queues"></a>Wiele kolejek o dużej przepływności

Cel: maksymalizowanie ogólnej przepływności wielu kolejek. Przepływność pojedynczej kolejki ma wartość umiarkowana lub wysoka.

Aby uzyskać maksymalną przepływność między wieloma kolejkami, Użyj ustawień opisanych w celu zmaksymalizowania przepływności pojedynczej kolejki. Ponadto należy użyć różnych fabryk do tworzenia klientów wysyłających lub odbierających z różnych kolejek.

### <a name="low-latency-queue"></a>Kolejka małych opóźnień

Cel: Minimalizacja opóźnienia końca kolejki lub tematu. Liczba nadawców i odbiorników jest mała. Przepływność kolejki jest mała lub średnia.

* Wyłącz przetwarzanie wsadowe po stronie klienta. Klient natychmiast wysyła komunikat.
* Wyłącz dostęp do magazynu wsadowego. Usługa natychmiast zapisuje komunikat w sklepie.
* W przypadku korzystania z jednego klienta należy ustawić liczbę pobieranych z wyprzedzeniem na 20 razy szybkość przetwarzania odbiornika. Jeśli wiele komunikatów dociera do kolejki w tym samym czasie, protokół klienta Service Bus przesyła je wszystkie w tym samym czasie. Gdy klient odbierze następny komunikat, ten komunikat jest już w lokalnej pamięci podręcznej. Pamięć podręczna powinna być mała.
* W przypadku korzystania z wielu klientów należy ustawić liczbę pobrania z wyprzedzeniem na 0. Ustawiając licznik, drugi klient może odebrać drugi komunikat, podczas gdy pierwszy klient nadal przetwarza pierwszy komunikat.

### <a name="queue-with-a-large-number-of-senders"></a>Kolejka z dużą liczbą nadawców

Cel: maksymalizowanie przepływności kolejki lub tematu z dużą liczbą nadawców. Każdy nadawca wysyła komunikaty o umiarkowanej stawce. Liczba odbiorników jest mała.

Service Bus włącza do 1000 współbieżnych połączeń z jednostką obsługi komunikatów (lub 5000 przy użyciu AMQP). Ten limit jest wymuszany na poziomie przestrzeni nazw, a kolejki/tematy/subskrypcje są ograniczone przez limit równoczesnych połączeń na przestrzeń nazw. W przypadku kolejek ta liczba jest udostępniana między nadawcami i odbiornikami. Jeśli dla nadawców są wymagane wszystkie połączenia 1000, Zastąp kolejkę z tematem i jedną subskrypcją. Temat akceptuje do 1000 połączeń współbieżnych od nadawców, podczas gdy subskrypcja akceptuje dodatkowe, współbieżne połączenia (1000) od odbiorników. Jeśli wymagane są ponad 1000 współbieżnych nadawców, nadawcy powinni wysyłać komunikaty do protokołu Service Bus za pośrednictwem protokołu HTTP.

Aby zmaksymalizować przepływność, wykonaj następujące czynności:

* Jeśli każdy nadawca znajduje się w innym procesie, użyj tylko jednej fabryki na proces.
* Użyj operacji asynchronicznych, aby skorzystać z tworzenia wsadowego po stronie klienta.
* Użyj domyślnego interwału wsadowego o wartości 20 ms, aby zmniejszyć liczbę Service Bus transmisjach protokołu klienta.
* Pozostaw włączony dostęp do magazynu wsadowego. Ten dostęp zwiększa ogólną szybkość, z jaką komunikaty mogą być zapisywane do kolejki lub tematu.
* Ustaw liczbę pobrania z wyprzedzeniem na 20 razy większą szybkość przetwarzania wszystkich odbiorników fabryki. Ta liczba zmniejsza liczbę Service Bus transmisjach protokołu klienta.

### <a name="queue-with-a-large-number-of-receivers"></a>Kolejka z dużą liczbą odbiorników

Cel: maksymalizuje szybkość odbierania kolejki lub subskrypcji o dużej liczbie odbiorników. Każdy odbiorca otrzymuje komunikaty o umiarkowanej stawce. Liczba nadawców jest niewielka.

Service Bus włącza do 1000 współbieżnych połączeń z jednostką. Jeśli kolejka wymaga więcej niż 1000 odbiorników, Zastąp kolejkę temat i wieloma subskrypcjami. Każda subskrypcja może obsługiwać maksymalnie 1000 połączeń współbieżnych. Alternatywnie odbiorcy mogą uzyskać dostęp do kolejki za pośrednictwem protokołu HTTP.

Aby zmaksymalizować przepływność, wykonaj następujące czynności:

* Jeśli każdy odbiornik znajduje się w innym procesie, użyj tylko jednej fabryki na proces.
* Odbiorcy mogą korzystać z operacji synchronicznych lub asynchronicznych. Mając na względnie umiarkowany wskaźnik odbioru poszczególnych odbiorników, przetwarzanie wsadowe po stronie klienta dla całego żądania nie wpływa na przepływność odbiorcy.
* Pozostaw włączony dostęp do magazynu wsadowego. Ten dostęp zmniejsza całkowite obciążenie jednostki. Zmniejsza również ogólną szybkość, z jaką komunikaty mogą być zapisywane do kolejki lub tematu.
* Ustaw liczbę pobierania z wyprzedzeniem na niewielką wartość (na przykład PrefetchCount = 10). Ta liczba uniemożliwia bezczynne odbiorców, a inne odbiorniki mają w pamięci podręcznej dużą liczbę komunikatów.

### <a name="topic-with-a-small-number-of-subscriptions"></a>Temat z małą liczbą subskrypcji

Cel: Maksymalizuj przepływność tematu o niewielkiej liczbie subskrypcji. Wiadomość jest odbierana przez wiele subskrypcji, co oznacza, że łączna szybkość odbierania dla wszystkich subskrypcji jest większa niż szybkość wysyłania. Liczba nadawców jest niewielka. Liczba odbiorników na subskrypcję jest mała.

Aby zmaksymalizować przepływność, wykonaj następujące czynności:

* Aby zwiększyć ogólną szybkość przesyłania do tematu, należy użyć wielu fabryk komunikatów do tworzenia nadawców. Dla każdego nadawcy Użyj operacji asynchronicznych lub wielu wątków.
* Aby zwiększyć ogólną szybkość odbierania z subskrypcji, Użyj wielu fabryk komunikatów do tworzenia odbiorników. Dla każdego odbiornika Użyj operacji asynchronicznych lub wielu wątków.
* Użyj operacji asynchronicznych, aby skorzystać z tworzenia wsadowego po stronie klienta.
* Użyj domyślnego interwału wsadowego o wartości 20 ms, aby zmniejszyć liczbę Service Bus transmisjach protokołu klienta.
* Pozostaw włączony dostęp do magazynu wsadowego. Ten dostęp zwiększa ogólną szybkość, z jaką komunikaty mogą być zapisywane w temacie.
* Ustaw liczbę pobrania z wyprzedzeniem na 20 razy większą szybkość przetwarzania wszystkich odbiorników fabryki. Ta liczba zmniejsza liczbę Service Bus transmisjach protokołu klienta.

### <a name="topic-with-a-large-number-of-subscriptions"></a>Temat z dużą liczbą subskrypcji

Cel: Maksymalizuj przepływność tematu o dużej liczbie subskrypcji. Wiadomość jest odbierana przez wiele subskrypcji, co oznacza, że łączna szybkość odbierania dla wszystkich subskrypcji jest znacznie większa niż szybkość wysyłania. Liczba nadawców jest niewielka. Liczba odbiorników na subskrypcję jest mała.

Tematy zawierające dużą liczbę subskrypcji zazwyczaj uwidaczniają niską ogólną przepływność, jeśli wszystkie komunikaty są kierowane do wszystkich subskrypcji. Ta niska przepływność jest spowodowana przez fakt, że każdy komunikat jest odbierany wiele razy, a wszystkie komunikaty zawarte w temacie i wszystkie jego subskrypcje są przechowywane w tym samym magazynie. Przyjęto założenie, że liczba nadawców i liczba odbiorników na subskrypcję jest mała. Service Bus obsługuje do 2 000 subskrypcji na temat.

Aby zmaksymalizować przepływność, spróbuj wykonać następujące czynności:

* Użyj operacji asynchronicznych, aby skorzystać z tworzenia wsadowego po stronie klienta.
* Użyj domyślnego interwału wsadowego o wartości 20 ms, aby zmniejszyć liczbę Service Bus transmisjach protokołu klienta.
* Pozostaw włączony dostęp do magazynu wsadowego. Ten dostęp zwiększa ogólną szybkość, z jaką komunikaty mogą być zapisywane w temacie.
* Ustaw liczbę pobierania z wyprzedzeniem na 20 razy określoną częstotliwość odbierania w sekundach. Ta liczba zmniejsza liczbę Service Bus transmisjach protokołu klienta.

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

---
title: Najlepsze rozwiązania dotyczące poprawy wydajności przy użyciu usługi Azure Service Bus | Dokumentacja firmy Microsoft
description: Informacje dotyczące używania usługi Service Bus w celu zoptymalizowania wydajności podczas wymiany komunikatów obsługiwanych przez brokera.
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: e756c15d-31fc-45c0-8df4-0bca0da10bb2
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/05/2018
ms.author: sethm
ms.openlocfilehash: e6762d988da7d34893852505d8ce0fd30622eaaf
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34802548"
---
# <a name="best-practices-for-performance-improvements-using-service-bus-messaging"></a>Najlepsze rozwiązania dotyczące poprawy wydajności przy użyciu usługi magistrali komunikatów

W tym artykule opisano sposób użycia usługi Azure Service Bus w celu zoptymalizowania wydajności podczas wymiany komunikatów obsługiwanych przez brokera. Pierwsza część w tym artykule opisano różne mechanizmy, które są oferowane w celu zwiększenia wydajności. Druga sekcja zawiera wskazówki dotyczące sposobu używania usługi Service Bus w sposób, który można zaoferować najlepszą wydajność w danym scenariuszu.

W tym temacie termin "klient" oznacza każda jednostka, która uzyskuje dostęp do usługi Service Bus. Klient może zająć roli nadawcy i adresata. Termin "sender" jest używana dla usługi Service Bus kolejka lub temat klienta, który wysyła wiadomości do kolejki lub temat subskrypcji usługi Service Bus. Termin "odbiorcy" odnosi się do usługi Service Bus kolejki lub subskrypcji klienta, który odbiera komunikaty z kolejki usługi Service Bus lub subskrypcji.

Tych sekcjach przedstawiono kilka pojęcia, które używa usługi Service Bus, aby ułatwić wydajnością.

## <a name="protocols"></a>Protokoły

Usługa Service Bus umożliwia klientom wysyłanie i odbieranie wiadomości za pomocą jednego z trzech protokołów:

1. Zaawansowane kolejkowania wiadomości protokołu (protokół AMQP)
2. Protokół (SBMP) do obsługi komunikatów usługi Service Bus
3. HTTP

Protokół AMQP i SBMP są bardziej wydajne, ponieważ utrzymują one połączenia usługi Service Bus, tak długo, jak istnieje fabryki obsługi komunikatów. Implementuje również przetwarzanie wsadowe i Odczyt z wyprzedzeniem. Chyba że jawnie wspomniano, całej zawartości w tym temacie założono użycie protokołu AMQP lub SBMP.

## <a name="reusing-factories-and-clients"></a>Ponowne wykorzystywanie fabryk i klientów

Klient usługi Service Bus obiekty, takie jak [QueueClient] [ QueueClient] lub [MessageSender][MessageSender], są tworzone za pomocą [ MessagingFactory] [ MessagingFactory] obiektu, który udostępnia również wewnętrznego zarządzania połączenia. Zaleca się, że nie został zamknięty fabryki obsługi wiadomości i kolejki, tematu i subskrypcji klientów po wysłać wiadomość, a następnie utwórz je po wysłaniu następny komunikat. Zamykanie fabryki obsługi komunikatów Usuwa połączenie z usługą Service Bus i nawiązaniem nowego połączenia podczas odtwarzania fabryka. Ustanowienie połączenia jest kosztowna operacja, która można uniknąć przez ponowne użycie tej samej fabryki i obiektów klientów w wielu operacjach. Można bezpiecznie [QueueClient] [ QueueClient] obiekt do wysyłania wiadomości z równoczesnych operacji asynchronicznych i wiele wątków. 

## <a name="concurrent-operations"></a>Równoczesne wykonywanie operacji

Wykonywanie operacji (wysyłanie, odbierania, usuwanie, itp.) dopiero po pewnym czasie. Teraz obejmuje przetwarzanie operacji przez usługi Service Bus, oprócz opóźnienia żądania i odpowiedzi. Aby zwiększyć liczbę operacji na czas, operacji musi być wykonywany współbieżnie. Ta zgodzie można uzyskać na kilka różnych sposobów:

* **Operacje asynchroniczne**: klient planuje operacji przez wykonanie operacji asynchronicznych. Przy następnym żądaniu została uruchomiona przed zakończeniem poprzedniego żądania. Poniższy fragment kodu jest przykładem operacja asynchronicznego wysyłania:
  
 ```csharp
  BrokeredMessage m1 = new BrokeredMessage(body);
  BrokeredMessage m2 = new BrokeredMessage(body);
  
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
  
  Następujący kod jest przykładem asynchronicznej operacji odbierania:
  
  ```csharp
  Task receive1 = queueClient.ReceiveAsync().ContinueWith(ProcessReceivedMessage);
  Task receive2 = queueClient.ReceiveAsync().ContinueWith(ProcessReceivedMessage);
  
  Task.WaitAll(receive1, receive2);
  Console.WriteLine("All messages received");
  
  async void ProcessReceivedMessage(Task<BrokeredMessage> t)
  {
    BrokeredMessage m = t.Result;
    Console.WriteLine("{0} received", m.Label);
    await m.CompleteAsync();
    Console.WriteLine("{0} complete", m.Label);
  }
  ```

* **Wiele fabryk**: wszystkich klientów (nadawców oprócz odbiorniki), które zostały utworzone przy użyciu tej samej fabryki udostępniania jednego połączenia TCP. Przepustowość maksymalna wiadomości jest ograniczona liczba operacji, które może przejść przez to połączenie TCP. Przepływność, którą można uzyskać za pomocą pojedynczego fabryki różny czasami opóźnienia TCP i rozmiar wiadomości. Aby uzyskać wyższy przepływności, użyj wielu fabryki obsługi komunikatów.

## <a name="receive-mode"></a>Tryb odbierania

Podczas tworzenia kolejki lub subskrypcji klienta, można określić tryb odbierania: *blokady Peek* lub *odbieranie i usuwanie*. Domyślny tryb odbierania jest [PeekLock][PeekLock]. Podczas działania w tym trybie, klient wysyła żądanie komunikat o błędzie z usługi Service Bus. Po odebraniu komunikatu klient wysyła żądanie do ukończenia wiadomości.

Po ustawieniu trybu receive na [ReceiveAndDelete][ReceiveAndDelete], oba kroki są łączone w ramach pojedynczego żądania. Czynności te zmniejszyć ogólną liczbę operacji i może zwiększyć ogólną wydajność obsługi wiadomości. Jest to bardziej wydajne ryzyko utraty wiadomości.

Usługa Service Bus nie obsługuje transakcji dla operacji odbierania i usuwania. Ponadto semantyki peek blokady są wymagane dla wszelkie scenariusze, w których klient będzie chciał odroczenie lub [utraconych](service-bus-dead-letter-queues.md) wiadomości.

## <a name="client-side-batching"></a>Przetwarzanie wsadowe po stronie klienta

Przetwarzanie wsadowe po stronie klienta umożliwia kolejka lub temat klientowi Opóźnij wysyłanie wiadomości w danym okresie czasu. Jeśli klient wysyła dodatkowe komunikaty w trakcie tego okresu, przesyłanie komunikatów w jednym zadaniu wsadowym. Przetwarzanie wsadowe po stronie klienta powoduje także, że kolejka lub subskrypcji klientowi partii wielu **Complete** żądań do pojedynczego żądania. Tworzenie plików wsadowych jest dostępna tylko dla asynchronicznego **wysyłania** i **Complete** operacji. Operacje synchroniczne są natychmiast wysyłane do usługi Service Bus. Przetwarzanie wsadowe nie nastąpić peek ani operacji pobrania nie jest przetwarzanie wsadowe wykonywane przez klientów.

Domyślnie klient używa przedział wsadowego w 20 ms. Można zmienić interwał partii ustawiając [BatchFlushInterval] [ BatchFlushInterval] właściwości przed utworzeniem fabryki obsługi komunikatów. To ustawienie dotyczy wszystkich klientów, które zostały utworzone przy użyciu tej fabryki.

Aby wyłączyć tworzenie plików wsadowych, ustaw [BatchFlushInterval] [ BatchFlushInterval] właściwości **TimeSpan.Zero**. Na przykład:

```csharp
MessagingFactorySettings mfs = new MessagingFactorySettings();
mfs.TokenProvider = tokenProvider;
mfs.NetMessagingTransportSettings.BatchFlushInterval = TimeSpan.FromSeconds(0.05);
MessagingFactory messagingFactory = MessagingFactory.Create(namespaceUri, mfs);
```

Przetwarzanie wsadowe nie wpływa na liczbę rozliczeniowy operacji obsługi wiadomości i jest dostępna tylko dla protokołu klienta usługi Service Bus. Protokół HTTP nie obsługuje przetwarzanie wsadowe.

## <a name="batching-store-access"></a>Przetwarzanie wsadowe dostęp do sklepu

W celu zwiększenia przepływności kolejki, tematu lub subskrypcji, usługi Service Bus partii wiele komunikatów, czy jest zapisywany do magazynu wewnętrznej. Jeśli jest włączona kolejka lub temat, zapisywania komunikatów w magazynie będzie można umieścić w partii. Jeśli włączona dla kolejki lub subskrypcji, usuwania komunikatów w sklepie będzie można umieścić w partii. Po włączeniu dostępu do sklepu wsadów dla jednostki usługi Service Bus opóźnienia dotyczące tej jednostki operacji zapisu magazynu przez maksymalnie 20 ms. 

> [!NOTE]
> Istnieje ryzyko utraty wiadomości z partii, nawet jeśli dostępny jest na końcu zakresu przetwarzanie wsadowe 20ms awarii usługi Service Bus. 

Operacji dodatkowego magazynu, które występują w danym przedziale czasu są dodawane do wykonywania zadania wsadowego. Umieścić w zadaniu wsadowym wpływa jedynie na dostęp do magazynu **wysyłania** i **Complete** operacji; odbierania nie wpływ na operacje. Dostęp do sklepu wsadów jest właściwością w jednostce. Przetwarzanie wsadowe występuje we wszystkich jednostek, które umożliwiają dostęp do sklepu wsadowej.

Podczas tworzenia nowej kolejki, tematu lub subskrypcji, dostęp do sklepu wsadów jest domyślnie włączona. Aby wyłączyć dostęp do sklepu wsadów, ustaw [EnableBatchedOperations] [ EnableBatchedOperations] właściwości **false** przed utworzeniem jednostki. Na przykład:

```csharp
QueueDescription qd = new QueueDescription();
qd.EnableBatchedOperations = false;
Queue q = namespaceManager.CreateQueue(qd);
```

Dostęp do sklepu wsadów nie wpływa na liczbę rozliczeniowy operacji obsługi wiadomości i jest właściwością kolejki, tematu lub subskrypcji. Jest niezależna od trybu receive i protokół, który jest używany między klientem a usługi Service Bus.

## <a name="prefetching"></a>Odczyt z wyprzedzeniem

[Odczyt z wyprzedzeniem](service-bus-prefetch.md) umożliwia klienta kolejki lub subskrypcji, aby załadować dodatkowych komunikatów z usługi, podczas wykonywania operacji odbierania. Klient przechowuje te komunikaty w lokalnej pamięci podręcznej. Rozmiar pamięci podręcznej jest określany przez [QueueClient.PrefetchCount] [ QueueClient.PrefetchCount] lub [SubscriptionClient.PrefetchCount] [ SubscriptionClient.PrefetchCount] właściwości. Każdy klient, który umożliwia odczyt z wyprzedzeniem zachowuje własną pamięci podręcznej. Pamięć podręczna nie jest współużytkowana przez klientów. Jeśli klient inicjuje operacji odbierania i pamięci podręcznej jest pusta, usługa przesyła partia komunikatów. Rozmiar partii jest równe rozmiar pamięci podręcznej lub 256 KB, w zależności od jest mniejsza. Jeśli klient inicjuje operacji odbierania i pamięci podręcznej zawiera komunikat, wiadomość jest pobierana z pamięci podręcznej.

Gdy komunikat jest prefetched, usługa blokuje prefetched wiadomości. Z blokadą prefetched komunikat nie może zostać odebrany przez inny odbiornik. Jeśli odbiornik nie może ukończyć komunikatu przed upływem blokady, komunikat staną się dostępne dla innych odbiorców. Prefetched kopia wiadomości pozostaje w pamięci podręcznej. Odbiornik, który wykorzystuje wygasłe buforowanej kopii otrzyma Wystąpił wyjątek podczas próby wykonania tej wiadomości. Domyślnie blokady komunikat wygasa po 60 sekund. Ta wartość może zostać rozszerzony do 5 minut. Aby zapobiec wykorzystywaniu komunikaty wygasłe, rozmiar pamięci podręcznej zawsze powinna być krótsza niż liczba wiadomości, które mogą być używane przez klienta w ramach interwał limitu czasu blokady.

Korzystając z okresu ważności blokady domyślne 60 sekund, wartość jest dobrą [SubscriptionClient.PrefetchCount] [ SubscriptionClient.PrefetchCount] 20 razy maksymalną przetwarza stawki wszystkich odbiorników fabryki. Na przykład fabrykę tworzy odbiorniki trzy, a każdy odbiorca może przetwarzać maksymalnie 10 komunikatów na sekundę. Liczba pobierania z wyprzedzeniem nie może przekraczać 20 X 3 X 10 = 600. Domyślnie [QueueClient.PrefetchCount] [ QueueClient.PrefetchCount] jest ustawiona na 0, co oznacza, że pobrane żadnych dodatkowych komunikatów z usługi.

Odczyt z wyprzedzeniem wiadomości zwiększa ogólną przepustowość kolejki lub subskrypcji, ponieważ zmniejsza ogólną liczbę operacje dotyczące komunikatów lub rund. Pobieranie pierwszej wiadomości, jednak będzie trwało dłużej (ze względu na rozmiar komunikatu zwiększona). Odbieranie komunikatów prefetched będzie przebiegać szybciej, ponieważ komunikaty te zostały już pobrane przez klienta.

Czas wygaśnięcia (TTL) właściwość wiadomości jest sprawdzana przez serwer w momencie serwer wysyła wiadomość do klienta. Klient nie sprawdza właściwości czas wygaśnięcia wiadomości podczas odbioru wiadomości. Zamiast tego można można odebrać wiadomości, nawet jeśli czas wygaśnięcia wiadomości minęła, gdy komunikat był buforowany przez klienta.

Odczyt z wyprzedzeniem nie wpływa na liczbę rozliczeniowy operacji obsługi wiadomości i jest dostępna tylko dla protokołu klienta usługi Service Bus. Protokół HTTP nie obsługuje odczyt z wyprzedzeniem. Odczyt z wyprzedzeniem jest dostępna dla operacji odbioru synchroniczne i asynchroniczne.

## <a name="express-queues-and-topics"></a>Express kolejek i tematów

Jednostki ekspresowe włączyć uzyskać wysoką przepustowość i mniejsze opóźnienia scenariuszy i są obsługiwane tylko w warstwie standardowej obsługi wiadomości. Jednostki utworzone w [przestrzeniach nazw warstwy Premium](service-bus-premium-messaging.md) nie obsługują opcji express. Z jednostki ekspresowe Jeśli jest wysyłany komunikat do kolejki lub tematu, wiadomości nie są natychmiast przechowywane w magazynie obsługi komunikatów. Zamiast tego są buforowane w pamięci. Jeśli wiadomość pozostaje w kolejce przez więcej niż kilka sekund, są automatycznie zapisywane do stabilnego magazynu, w związku z tym ochrony przed utratą z powodu awarii. Zapisywanie wiadomości w pamięci podręcznej zwiększa przepustowość i zmniejsza opóźnienia, ponieważ nie ma dostępu do magazynu stanu stabilnego w czasie, który jest wysyłany komunikat. Komunikaty, które są używane w ciągu kilku sekund nie są zapisywane w magazynie obsługi komunikatów. Poniższy przykład tworzy express tematu.

```csharp
TopicDescription td = new TopicDescription(TopicName);
td.EnableExpress = true;
namespaceManager.CreateTopic(td);
```

Jeśli wiadomość zawierającą krytyczne informacje, które nie mogą być utracone są wysyłane do jednostki ekspresowe, nadawca wymusić usługi Service Bus, aby natychmiast utrwalić komunikat do stabilnego magazynu przez ustawienie [ForcePersistence] [ ForcePersistence] właściwości **true**.

> [!NOTE]
> Jednostki ekspresowe nie obsługuje transakcji.

## <a name="use-of-partitioned-queues-or-topics"></a>Korzystanie z podzielonym na partycje kolejki i tematy

Wewnętrznie Usługa Service Bus używa tego samego węzła i magazynu do obsługi komunikatów do przetwarzania i przechowywania wszystkie komunikaty dla jednostki obsługi komunikatów (kolejki lub tematu). A [partycjonowanej kolejka lub temat](service-bus-partitioning.md), z drugiej strony, będzie rozmieszczona na wielu węzłach i wiadomości magazynów. Partycjonowane kolejek i tematów nie tylko uzyskanie wyższej przepustowości niż regularne kolejek i tematów, charakteryzują wysokiej dostępności. Aby utworzyć partycjonowane jednostki, ustaw [parametr EnablePartitioning] [ EnablePartitioning] właściwości **true**, jak pokazano w poniższym przykładzie. Aby uzyskać więcej informacji na temat partycjonowane jednostki, zobacz [partycjonowane jednostki do obsługi komunikatów][Partitioned messaging entities].

> [!NOTE]
> Partycjonowane jednostki są już obsługiwane w [warstwy Premium](service-bus-premium-messaging.md). 

```csharp
// Create partitioned queue.
QueueDescription qd = new QueueDescription(QueueName);
qd.EnablePartitioning = true;
namespaceManager.CreateQueue(qd);
```

## <a name="use-of-multiple-queues"></a>Korzystanie z wielu kolejek

Jeśli nie jest możliwe za pomocą partycjonowanego kolejki lub tematu lub spodziewane obciążenie nie mogą być obsługiwane przez partycjonowanej kolejka lub temat, należy użyć wiele jednostek obsługi komunikatów. Korzystając z wielu jednostek, Utwórz dedykowane klienta dla każdej jednostki, zamiast używać tego samego klienta dla wszystkich obiektów.

## <a name="development-and-testing-features"></a>Programowanie i testowanie funkcji

Usługa Service Bus ma jedną funkcję używany specjalnie z myślą o programowanie, które **nie mogą być używane w konfiguracjach produkcji**: [TopicDescription.EnableFilteringMessagesBeforePublishing][].

Jeśli nowe reguły lub filtry są dodawane do tematu, możesz użyć [TopicDescription.EnableFilteringMessagesBeforePublishing][] Aby sprawdzić, czy nowe wyrażenie filtru działa zgodnie z oczekiwaniami.

## <a name="scenarios"></a>Scenariusze

W poniższych sekcjach opisano typowe scenariusze obsługi wiadomości i przedstawiają preferowanych ustawień usługi Service Bus. Przepływności są sklasyfikowane jako mały (mniej niż 1 komunikatów na sekundę), średni (1 komunikatów na sekundę lub większą, ale mniej niż 100 komunikatów na sekundę) i wysoki (100 wiadomości/sekundę lub większą). Liczba klientów są sklasyfikowane jako mała liczba godzin (5 lub mniej), umiarkowany (więcej niż 5, ale mniej niż 20) i duże (więcej niż 20).

### <a name="high-throughput-queue"></a>Kolejki wysokiej przepustowości

Cel: Zmaksymalizować przepustowość pojedynczej kolejki. Liczba nadawcami a odbiornikami jest mała.

* Aby zwiększyć ogólną szybkość wysyłania w kolejce, umożliwiają utworzenie nadawców wiele fabryk komunikatów. Dla każdego nadawcy użyj operacji asynchronicznych lub wiele wątków.
* Aby zwiększyć ogólną szybkość odbierania z kolejki, umożliwiają utworzenie odbiorców wiele fabryk komunikatów.
* Aby skorzystać z przetwarzanie wsadowe po stronie klienta, należy użyć operacji asynchronicznych.
* Ustaw interwał przetwarzanie wsadowe 50 MS, aby zmniejszyć liczbę transmisji protokołu klienta usługi Service Bus. Jeśli wielu nadawców są używane, należy zwiększyć interwał przetwarzanie wsadowe 100 MS.
* Pozostaw włączony dostęp do magazynu wsadowej. Dostęp zwiększa ogólną szybkość, w którym można pisać wiadomości do kolejki.
* Ustaw liczbę pobierania z wyprzedzeniem do 20 razy maksymalnej szybkości przetwarzania wszystkich odbiorników fabryki. Ten licznik zmniejsza liczbę transmisji protokołu klienta usługi Service Bus.
* Użyj kolejki partycjonowanej większą wydajność i dostępność.

### <a name="multiple-high-throughput-queues"></a>Wielu kolejek wysokiej przepustowości

Cel: Zmaksymalizować ogólną przepustowość wielu kolejek. Przepływność pojedynczej kolejki jest średni czy wysoki.

Aby uzyskać maksymalną przepływność wiele kolejek, użyj ustawienia opisane w celu zwiększenia przepływności pojedynczej kolejki. Ponadto użyj innego fabryki, aby utworzyć klientów, którzy wysłać lub odebrać z różnych kolejek.

### <a name="low-latency-queue"></a>Małe opóźnienia kolejki

Cel: Zminimalizować czas oczekiwania na trasie kolejka lub temat. Liczba nadawcami a odbiornikami jest mała. Przepływność kolejki jest mała lub średniego.

* Wyłącz przetwarzanie wsadowe po stronie klienta. Natychmiast wysyła wiadomość.
* Wyłącz dostęp do sklepu wsadowej. Usługa natychmiast zapisuje komunikat w magazynie.
* Jeśli przy użyciu jednego klienta, ustaw 20 razy szybkości przetwarzania odbiornika liczba pobierania z wyprzedzeniem. Jeśli wiele wiadomości do kolejki w tym samym czasie, protokół klienta usługi Service Bus umożliwiają przesyłanie wszystko na tym samym czasie. Gdy klient odbierze następny komunikat, ten komunikat jest już w lokalnej pamięci podręcznej. Pamięć podręczna powinna być niewielka.
* Jeśli używasz wielu klientów, liczba pobierania z wyprzedzeniem wartości 0. Ustawiając wartość licznika, drugi klient może odbierać to drugi komunikat, gdy pierwszy klient nadal przetwarza pierwszego komunikatu.
* Użyj kolejki partycjonowanej większą wydajność i dostępność.

### <a name="queue-with-a-large-number-of-senders"></a>Kolejka z dużą liczbą nadawców

Cel: Zmaksymalizować przepustowość kolejka lub temat z dużą liczbą nadawcy. Każdy Nadawca wysyła komunikaty z wartością średnią. Liczba odbiorcy jest mała.

Usługa Service Bus umożliwia do 1000 równoczesnych połączeń jednostki obsługi komunikatów (lub 5000 za pomocą protokołu AMQP). Ten limit jest wymuszane na poziomie przestrzeni nazw i kolejek/tematy/subskrypcje są ograniczone przez limit równoczesnych połączeń na przestrzeni nazw. W przypadku kolejek ta liczba jest udostępniana między nadawcami a odbiornikami. Jeśli wszystkie połączenia 1000 są wymagane dla nadawców, Zamień kolejki tematu i jedną subskrypcją. Temat akceptuje do 1000 równoczesnych połączeń od nadawcy, natomiast subskrypcji akceptuje dodatkowych 1000 równoczesnych połączeń od odbiorcy. Jeśli wymaganych jest więcej niż 1000 równoczesnych nadawców nadawców powinien wysyłać wiadomości protokołu usługi Service Bus za pośrednictwem protokołu HTTP.

Aby zmaksymalizować przepustowość, wykonaj następujące czynności:

* Jeśli każdego nadawcy znajduje się w ramach innego procesu, należy użyć tylko jednej fabryki na proces.
* Aby skorzystać z przetwarzanie wsadowe po stronie klienta, należy użyć operacji asynchronicznych.
* Użyj domyślnej przetwarzanie wsadowe interwał 20 ms, aby zmniejszyć liczbę transmisji protokołu klienta usługi Service Bus.
* Pozostaw włączony dostęp do magazynu wsadowej. Dostęp zwiększa ogólną szybkość, jaką można pisać wiadomości do kolejki lub temat.
* Ustaw liczbę pobierania z wyprzedzeniem do 20 razy maksymalnej szybkości przetwarzania wszystkich odbiorników fabryki. Ten licznik zmniejsza liczbę transmisji protokołu klienta usługi Service Bus.
* Użyj kolejki partycjonowanej większą wydajność i dostępność.

### <a name="queue-with-a-large-number-of-receivers"></a>Kolejka z wieloma odbiornikami

Cel: Maksymalizuj szybkość odbierania kolejki lub subskrypcji z dużą liczbą odbiorników. Każdy odbiorca odbiera komunikaty z umiarkowane szybkością. Liczba nadawców jest mała.

Usługa Service Bus umożliwia do 1000 równoczesnych połączeń z jednostką. Jeśli kolejka wymaga więcej niż 1000 odbiornikami, Zamień kolejki tematu i subskrypcji. Każda subskrypcja może obsługiwać maksymalnie 1000 równoczesnych połączeń. Alternatywnie odbiorcy mają dostęp do kolejki przy użyciu protokołu HTTP.

Aby zmaksymalizować przepustowość, wykonaj następujące czynności:

* Każdy odbiorca znajduje się w ramach innego procesu, należy użyć tylko jednej fabryki na proces.
* Odbiorniki można używać operacji synchroniczna lub asynchroniczna. Biorąc pod uwagę szybkość odbierania umiarkowane poszczególnych odbiornika, wykonać żądanie przetwarzanie wsadowe po stronie klienta nie wpływa na przepływność odbiorcy.
* Pozostaw włączony dostęp do magazynu wsadowej. Dostęp zmniejsza całkowite obciążenie jednostki. Zmniejsza ogólną szybkość, jaką można pisać wiadomości do kolejki lub temat.
* Ustaw wyprzedzeniem liczbę małej wartości (na przykład PrefetchCount = 10). Ten licznik zapobiega odbiorniki bezczynności podczas innym odbiornikom ma dużą liczbę komunikatów w pamięci podręcznej.
* Użyj kolejki partycjonowanej większą wydajność i dostępność.

### <a name="topic-with-a-small-number-of-subscriptions"></a>Temat z małą liczbą subskrypcji

Cel: Zmaksymalizować przepustowość tematu z mniejszą liczbą subskrypcji. Odebraniu komunikatu przez wiele subskrypcji, co oznacza, że szybkość odbierania połączonych za pośrednictwem wszystkie subskrypcje jest większa niż częstotliwość wysyłania. Liczba nadawców jest mała. Liczba odbiorcy dla subskrypcji jest mała.

Aby zmaksymalizować przepustowość, wykonaj następujące czynności:

* Aby zwiększyć ogólną szybkość wysyłania do tematu, umożliwiają utworzenie nadawców wiele fabryk komunikatów. Dla każdego nadawcy użyj operacji asynchronicznych lub wiele wątków.
* Aby zwiększyć ogólną szybkość odbierania z subskrypcji, umożliwiają utworzenie odbiorców wiele fabryk komunikatów. Dla każdego adresata użyj operacji asynchronicznych lub wiele wątków.
* Aby skorzystać z przetwarzanie wsadowe po stronie klienta, należy użyć operacji asynchronicznych.
* Użyj domyślnej przetwarzanie wsadowe interwał 20 ms, aby zmniejszyć liczbę transmisji protokołu klienta usługi Service Bus.
* Pozostaw włączony dostęp do magazynu wsadowej. Dostęp zwiększa ogólną szybkość jaką komunikaty mogą być zapisywane w temacie.
* Ustaw liczbę pobierania z wyprzedzeniem do 20 razy maksymalnej szybkości przetwarzania wszystkich odbiorników fabryki. Ten licznik zmniejsza liczbę transmisji protokołu klienta usługi Service Bus.
* Skorzystaj z podzielonym na partycje tematu, aby uzyskać lepszą wydajność i dostępność.

### <a name="topic-with-a-large-number-of-subscriptions"></a>Temat z dużą liczbą subskrypcji

Cel: Zmaksymalizować przepustowość tematu z dużą liczbą subskrypcji. Komunikat jest odbierany przez wiele subskrypcji, co oznacza, że szybkość odbierania połączonych za pośrednictwem wszystkie subskrypcje jest znacznie większa niż częstotliwość wysyłania. Liczba nadawców jest mała. Liczba odbiorcy dla subskrypcji jest mała.

Tematy z dużą liczbą subskrypcji zwykle ujawnia niski ogólną przepustowość, jeśli wszystkie komunikaty są kierowane do wszystkich subskrypcji. Przyczyną jest fakt, że każdy komunikat jest odbierany tyle razy, a wszystkie komunikaty, które znajdują się w temacie i wszystkie jego subskrypcje są przechowywane w tym samym magazynie to niskiej przepustowości. Zakłada się, liczba nadawców i liczbę odbiorników dla subskrypcji jest mała. Usługa Service Bus obsługuje maksymalnie 2000 subskrypcji na temat.

Aby zmaksymalizować przepustowość, spróbuj wykonać następujące kroki:

* Aby skorzystać z przetwarzanie wsadowe po stronie klienta, należy użyć operacji asynchronicznych.
* Użyj domyślnej przetwarzanie wsadowe interwał 20 ms, aby zmniejszyć liczbę transmisji protokołu klienta usługi Service Bus.
* Pozostaw włączony dostęp do magazynu wsadowej. Dostęp zwiększa ogólną szybkość jaką komunikaty mogą być zapisywane w temacie.
* Ustaw liczbę pobierania z wyprzedzeniem 20 razy szybkość odbierania oczekiwanego, w sekundach. Ten licznik zmniejsza liczbę transmisji protokołu klienta usługi Service Bus.
* Skorzystaj z podzielonym na partycje tematu, aby uzyskać lepszą wydajność i dostępność.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat optymalizacji wydajności usługi Service Bus, zobacz [partycjonowane jednostki do obsługi komunikatów][Partitioned messaging entities].

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

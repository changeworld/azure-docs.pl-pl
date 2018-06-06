---
title: Kolejki magazynu Azure i kolejek usługi Service Bus porównywane i odróżniające | Dokumentacja firmy Microsoft
description: Analizuje różnice i podobieństwa między dwoma typami kolejek oferowanych na platformie Azure.
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: f07301dc-ca9b-465c-bd5b-a0f99bab606b
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/05/2018
ms.author: sethm
ms.openlocfilehash: 0b9a79919a63056bbc17e44ef0da3697001d227f
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34802360"
---
# <a name="storage-queues-and-service-bus-queues---compared-and-contrasted"></a>Magazyn kolejek i kolejek usługi Service Bus - porównywane i odróżniające
W tym artykule analizuje różnice i podobieństwa między tymi dwoma typami kolejek oferowanych przez system Microsoft Azure obecnie: magazyn kolejek i kolejek usługi Service Bus. Dzięki tym informacjom można porównać odpowiednie technologie i świadomie wybrać rozwiązanie, które najlepiej odpowiada danym potrzebom.

## <a name="introduction"></a>Wprowadzenie
Azure obsługuje dwa typy mechanizmów kolejki: **magazynu kolejek** i **kolejek usługi Service Bus**.

**Magazyn kolejek**, które są częścią [magazynu Azure](https://azure.microsoft.com/services/storage/) infrastruktury, funkcja prostego interfejsu opartego na interfejsie REST GET/PUT/PEEK, zapewniając niezawodne, trwałe wiadomości w obrębie i między usługami.

**Kolejki usługi Service Bus** są częścią szerszego [Azure messaging](https://azure.microsoft.com/services/service-bus/) infrastruktury obsługuje kolejkowania wiadomości oraz publikowania/subskrypcji i bardziej zaawansowane wzorce integracji. Aby uzyskać więcej informacji na temat usługi Service Bus kolejek/tematy/subskrypcji, zobacz [Omówienie usługi Service Bus](service-bus-messaging-overview.md).

Istnieją obie Kolejkowanie technologie współbieżnie, kolejek magazynu zostały wprowadzone najpierw mechanizmu magazynowania dedykowanych kolejki, oparty na usługach Azure Storage. Kolejek usługi Service Bus jest oparty na szersze zaprojektowany do integrowania aplikacji lub składników aplikacji, które może obejmować wiele protokołów komunikacyjnych, kontraktów danych, zaufania domen lub w środowiskach sieci infrastruktury obsługi wiadomości.

## <a name="technology-selection-considerations"></a>Zagadnienia dotyczące wyboru technologii
Kolejki magazynu i kolejek usługi Service Bus są implementacje obecnie oferowanych przez system Microsoft Azure usługi kolejkowania wiadomości. Każdy ma zestaw nieco innych funkcji, który oznacza, że można wybrać jednego lub drugiego lub korzystać z obu w zależności od potrzeb danego rozwiązania lub są rozwiązywania problemu firm/techniczne.

Podczas określania Kolejkowanie technologie pasuje do zastosowania dla danego rozwiązania, rozwiązanie architektów i deweloperów należy rozważyć te zalecenia. Aby uzyskać więcej informacji zobacz następną sekcję.

Jako rozwiązanie architektów/deweloperów **należy rozważyć użycie magazynu kolejek** po:

* Aplikacja musi przechowywać ponad 80 GB wiadomości w kolejce.
* Aplikacja chce, aby śledzić postęp dla przetwarzania komunikatu w kolejce. Jest to przydatne w przypadku awarii procesu roboczego, przetwarzanie wiadomości. Kolejne procesu roboczego następnie można użyć tych informacji aby kontynuować, z którym przerwał poprzedniego procesu roboczego.
* Wymagane jest dzienników po stronie serwera wszystkich transakcji wykonywane z kolejki.

Jako rozwiązanie architektów/deweloperów **należy rozważyć użycie kolejek usługi Service Bus** po:

* Rozwiązanie musi mieć możliwość odbierania wiadomości bez konieczności sondowania kolejki. Z usługą Service Bus, można to osiągnąć przy użyciu sondowania long przy użyciu protokołów opartych na protokole TCP, które obsługuje usługi Service Bus operacji odbioru.
* Rozwiązanie wymaga kolejki zapewnienie gwarantowane pierwszy — w pierwszej — ruch wychodzący dostarczania uporządkowanego (FIFO).
* Rozwiązanie musi mieć możliwość obsługi automatycznego wykrywania duplikatów.
* Ma aplikacji do przetwarzania komunikatów jako równoległych strumieni długotrwałe (komunikaty są skojarzone z stream przy użyciu [SessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid) właściwości komunikatu). W tym modelu każdy węzeł w aplikacja odbierająca komunikaty konkuruje dla strumieni, w przeciwieństwie do komunikatów. Gdy strumień jest odbierającą węzła, węzeł można sprawdzić stan stan strumienia aplikacji przy użyciu transakcji.
* Rozwiązanie wymaga zachowanie transakcyjnego i niepodzielność przy wysyłaniu lub odbieraniu wiele komunikatów z kolejki.
* Aplikacji obsługi wiadomości, które może być dłuższa niż 64 KB, ale ograniczy prawdopodobnie nie podejście 256 KB.
* Należy uwzględniać konieczność zapewnienia modelu opartej na rolach dostępu do kolejek i inne prawa/uprawnienia nadawcami a odbiornikami.
* Rozmiar kolejki nie będzie rosnąć większa niż 80 GB.
* Chcesz korzystać z protokołu AMQP 1.0 opartych na standardach obsługi wiadomości protokołu. Aby uzyskać więcej informacji na temat protokołu AMQP, zobacz [Omówienie protokołu AMQP magistrali usługi](service-bus-amqp-overview.md).
* Mogą traktować ostatecznego migracji z systemem kolejki bezpośredniej komunikacji do wymiany komunikatów, który umożliwia bezproblemową integrację odbiorcy dodatkowego (subskrybentów), z których każdy otrzymuje niezależne kopie albo niektórych lub wszystkich wiadomości wysłanych do kolejki. Drugie odnosi się do możliwości publikowania/subskrypcji, natywnie udostępniane przez usługi Service Bus.
* Rozwiązanie do obsługi komunikatów musi mieć możliwość obsługi gwarancji dostarczenia "Większość-jednocześnie" bez konieczności do tworzenia składników dodatkowej infrastruktury.
* Chcesz mieć możliwość publikowania i używania partie wiadomości.

## <a name="comparing-storage-queues-and-service-bus-queues"></a>Porównanie magazynu kolejek i kolejek usługi Service Bus
Tabele w poniższych sekcjach umożliwiają logiczne grupowanie funkcji kolejki i pozwalają porównać jeden rzut oka, możliwości, które są dostępne zarówno w przypadku kolejek usługi Azure Storage, jak i kolejek usługi Service Bus.

## <a name="foundational-capabilities"></a>Podstawowych możliwości
Ta sekcja porównuje niektórych podstawowych kolejkowania możliwości oferowane przez wersję magazynu kolejek i kolejek usługi Service Bus.

| Kryteria porównania | Kolejki magazynu | Kolejki usługi Service Bus |
| --- | --- | --- |
| Porządkowanie gwarancji |**Nie** <br/><br>Aby uzyskać więcej informacji zobacz uwagę pierwszy w części "Informacje dodatkowe".</br> |**Tak — pierwszy w pierwszym Out (FIFO)**<br/><br>(przy użyciu sesji do obsługi komunikatów) |
| Gwarancji dostarczenia |**W najmniej jednokrotne** |**W najmniej jednokrotne**<br/><br/>**Większość jednocześnie** |
| Obsługa niepodzielną operację |**Nie** |**Tak**<br/><br/> |
| Działanie odbierania |**Blokowanie inne niż**<br/><br/>(zakończeniu natychmiast przypadku nieznalezienia nie nowej wiadomości) |**Blokowanie z lub bez limitu czasu**<br/><br/>(oferuje długo sondowania, lub ["Kometa techniki"](http://go.microsoft.com/fwlink/?LinkId=613759))<br/><br/>**Blokowanie inne niż**<br/><br/>(przy użyciu platformy .NET zarządzanego interfejsu API tylko) |
| Styl wypychania interfejsu API |**Nie** |**Tak**<br/><br/>[OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage#Microsoft_ServiceBus_Messaging_QueueClient_OnMessage_System_Action_Microsoft_ServiceBus_Messaging_BrokeredMessage__) i **OnMessage** sesji interfejsu API platformy .NET. |
| Tryb odbierania |**Peek & dzierżawy** |**Peek & blokady**<br/><br/>**Usuń & Odbierz** |
| Tryb wyłączności |**Na podstawie dzierżawy** |**Na podstawie blokady** |
| Czas trwania dzierżawy/blokady |**30 sekund (ustawienie domyślne)**<br/><br/>**7 dni (maksymalnie)** (możesz odnowić lub zwolnienia dzierżawy komunikat przy użyciu [UpdateMessage](/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue.updatemessage) interfejsu API.) |**60 sekund (ustawienie domyślne)**<br/><br/>Możesz odnowić blokady komunikat przy użyciu [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) interfejsu API. |
| / Blokady dokładności |**Poziom komunikatu**<br/><br/>(każdy komunikat może mieć wartości różnych limitu czasu, który można zaktualizować zgodnie z potrzebami podczas przetwarzania komunikatu, za pomocą [UpdateMessage](/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue.updatemessage) interfejsu API) |**Poziom kolejki**<br/><br/>(Każda kolejka ma dokładności blokady stosowane do wszystkich jego wiadomości, ale mogą odnowić przy użyciu blokady [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) interfejsu API.) |
| Umieścić w zadaniu wsadowym odbierania |**Tak**<br/><br/>(jawne określenie liczby komunikatów podczas pobierania wiadomości, maksymalnie 32 wiadomości) |**Tak**<br/><br/>(niejawnie włączając właściwość pobierania wstępnego lub jawnie za pomocą transakcji) |
| Wyślij wsadów |**Nie** |**Tak**<br/><br/>(przy użyciu transakcji lub przetwarzanie wsadowe po stronie klienta) |

### <a name="additional-information"></a>Dodatkowe informacje
* Komunikaty w kolejkach magazynu są zwykle pierwszy — w pierwszej — ruch wychodzący, ale czasami może być uszkodzony; na przykład komunikat widoczność limitu czasu wygaśnięcia (na przykład w wyniku awarii podczas przetwarzania aplikacji klienckiej). Po upływie limitu czasu widoczności o komunikacie staje się widoczna ponownie dla kolejki innego procesu roboczego usunąć go z kolejki. W tym momencie nowo widoczny komunikat może umieścić w kolejce (usuniętej ponownie) po komunikat, który został pierwotnie umieszczonych w kolejce po nim.
* Gwarantowane wzorzec FIFO w kolejek usługi Service Bus wymaga użycia sesji komunikacji. W przypadku, gdy aplikacja przestaje działać podczas przetwarzania komunikatów otrzymanych w **wgląd & Zablokuj** tryb, przy następnym odbiornika kolejka akceptuje sesji programu obsługi wiadomości, zostanie ona rozpoczęta wiadomości nie powiodło się po wygaśnięciu okresu czasu wygaśnięcia (TTL).
* Magazyn kolejek zostały zaprojektowane do obsługi kolejkowania scenariusze standardowe, takie jak odsprzęgania składniki aplikacji w celu zwiększenia skalowalności i tolerancję błędów, obciążenia wyrównywanie i przepływy pracy procesu kompilacji.
* Obsługa kolejek usługi Service Bus *na najmniej jednokrotne* gwarancji dostarczenia. Ponadto *większość-jednocześnie* semantycznego mogą być obsługiwane za pomocą stanu sesji na przechowywanie stanu aplikacji i za pomocą transakcji atomowo odbierania komunikatów i aktualizowania stanu sesji.
* Magazyn kolejek udostępnianie uniform i spójny model programowania w kolejek, tabel i obiektów blob — zarówno dla deweloperów i zespołów operacyjnych.
* Kolejki usługi Service Bus zapewniają obsługę transakcji lokalnych w ramach pojedynczej kolejki.
* **Odbieranie i usuwanie** tryb obsługiwany przez usługi Service Bus pozwala, aby zmniejszyć liczbę operacji obsługi komunikatów (i skojarzone koszt) zamian gwarancji dostarczenia obniżony.
* Magazyn kolejek zapewnia dzierżawy możliwość rozszerzania dzierżawy dla wiadomości. Dzięki temu pracownikom Obsługa krótkich dzierżawy na komunikaty. W związku z tym jeśli pracownik ulegnie awarii, wiadomości mogą być szybko przetwarzane ponownie przez inny proces roboczy. Ponadto pracownik może przedłużyć dzierżawę wiadomość wymaga go przetworzyć dłużej niż bieżący czas dzierżawy.
* Magazyn kolejek oferują widoczność przekroczenie limitu czasu można ustawić na enqueuing lub usuwania komunikatów. Ponadto należy zaktualizować komunikat o wartości innej dzierżawy w czasie wykonywania i zaktualizować różne wartości w wiadomości w tej samej kolejki. Limity czasu blokady usługi Service Bus są zdefiniowane w metadanych kolejki; Jednak możesz odnowić blokady przez wywołanie metody [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) metody.
* Maksymalny limit czasu operacji blokowania odbioru w kolejek usługi Service Bus jest 24 dni. Jednak opartego na interfejsie REST limitów czasu mieć maksymalną wartość 55 sekund.
* Po stronie klienta przetwarzanie wsadowe udostępniane przez usługi Service Bus umożliwia klientowi kolejki partii wiele komunikatów do operacji wysyłania pojedynczego. Tworzenie plików wsadowych jest dostępna tylko dla operacji asynchronicznego wysyłania.
* Funkcje, takie jak limitu 200 TB magazynu kolejek (więcej podczas wirtualizacji kont) i nieograniczoną liczbę kolejek był idealną platformą dla dostawców w modelu SaaS.
* Magazyn kolejek zapewniają elastyczne i wydajności delegowane mechanizmu kontroli dostępu.

## <a name="advanced-capabilities"></a>Funkcje zaawansowane
Ta sekcja porównuje zaawansowane możliwości oferowane przez wersję magazynu kolejek i kolejek usługi Service Bus.

| Kryteria porównania | Kolejki magazynu | Kolejki usługi Service Bus |
| --- | --- | --- |
| Dostarczania zaplanowanych |**Tak** |**Tak** |
| Automatyczne martwy litery |**Nie** |**Tak** |
| Zwiększenie wartości time-to-live kolejki |**Tak**<br/><br/>(za pomocą aktualizacji w miejscu limitu czasu widoczności) |**Tak**<br/><br/>(udostępnione za pośrednictwem dedykowanej funkcja interfejsu API) |
| Obsługa komunikatów zanieczyszczonych |**Tak** |**Tak** |
| Aktualizacja w miejscu |**Tak** |**Tak** |
| Dziennik transakcji po stronie serwera |**Tak** |**Nie** |
| Metryki magazynu |**Tak**<br/><br/>**Minuty metryki**: zapewnia metryk w czasie rzeczywistym dla interfejsu API dostępności, TPS, wywołaj liczby, liczby błędów i więcej, w czasie rzeczywistym (zagregowane na minutę i zgłaszanych w ciągu kilku minut od naturę tylko w środowisku produkcyjnym. Aby uzyskać więcej informacji, zobacz [o metryki analityka magazynu](/rest/api/storageservices/fileservices/About-Storage-Analytics-Metrics). |**Tak**<br/><br/>(zbiorcze zapytań przez wywołanie metody [GetQueues](/dotnet/api/microsoft.servicebus.namespacemanager.getqueues#Microsoft_ServiceBus_NamespaceManager_GetQueues)) |
| Zarządzanie stanem |**Nie** |**Tak**<br/><br/>[Microsoft.ServiceBus.Messaging.EntityStatus.Active](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.ServiceBus.Messaging.EntityStatus.Disabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.ServiceBus.Messaging.EntityStatus.SendDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.ServiceBus.Messaging.EntityStatus.ReceiveDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus) |
| Automatyczne przekazywanie komunikatów |**Nie** |**Tak** |
| Przeczyścić kolejki — funkcja |**Tak** |**Nie** |
| Komunikat grup |**Nie** |**Tak**<br/><br/>(przy użyciu sesji do obsługi komunikatów) |
| Stan aplikacji dla każdej grupy wiadomości |**Nie** |**Tak** |
| Wykrywanie duplikatów |**Nie** |**Tak**<br/><br/>(można konfigurować na stronie nadawcy) |
| Przeglądanie grup wiadomości |**Nie** |**Tak** |
| Pobieranie wiadomości sesje według Identyfikatora |**Nie** |**Tak** |

### <a name="additional-information"></a>Dodatkowe informacje
* Obie Kolejkowanie technologie Włącz wiadomości na planowanie dostarczania w późniejszym czasie.
* Automatyczne przekazywanie kolejki umożliwia tysiące kolejki, aby automatycznie do przodu ich wiadomości do pojedynczej kolejki, w którym aplikacja zużywa wiadomości. Mechanizm ten umożliwia osiągnięcia bezpieczeństwa, sterowania przepływem i izolowanie magazynu między każdy wydawca wiadomości.
* Magazyn kolejek zapewnia obsługę aktualizowanie zawartości wiadomości. Tej funkcji można używać dla trwałych informacje o stanie i aktualizacje przyrostowe postępu do wiadomości, aby mogą być przetwarzane od ostatniego znanego punktu kontrolnego, zamiast od podstaw. W przypadku kolejek usługi Service Bus można włączyć sytuacji przy użyciu sesji wiadomości. Sesje pozwalają na zapisywanie i pobrać stan przetwarzania aplikacji (przy użyciu [metoda SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) i [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState)).
* [Martwych drukiem](service-bus-dead-letter-queues.md), który jest obsługiwany tylko przez kolejek usługi Service Bus, może być przydatna do izolowania wiadomości, który nie może pomyślnie przetworzone przez aplikację odbierającą lub gdy komunikaty można nawiązać połączenia z ich przeznaczenia z powodu wygasły (time-to-live Właściwość TTL). Wartość TTL Określa, jak długo pozostaje wiadomości w kolejce. Z usługą Service Bus wiadomość zostanie przeniesiona do kolejki specjalne o nazwie $DeadLetterQueue, po wygaśnięciu okresu czas wygaśnięcia.
* Można znaleźć "" wiadomości w kolejkach magazynu podczas usuwania komunikat aplikacji sprawdza [DequeueCount](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueuemessage.dequeuecount.aspx) właściwości komunikatu. Jeśli **DequeueCount** jest większy niż podany próg aplikacji przenosi wiadomości zdefiniowane przez aplikację "kolejki utraconych wiadomości".
* Magazyn kolejek umożliwiają uzyskanie szczegółowy dziennik wszystkich transakcji wykonywane kolejki, jak również jako zagregowane metryki. Obie te opcje są przydatne w przypadku debugowania i zrozumienie, jak aplikacja korzysta z magazynu kolejek. Są one również przydatne w przypadku aplikacji dostrajanie wydajności i zmniejszenie kosztów korzystanie z kolejek.
* Pojęcie "sesje komunikat" obsługiwane przez usługi Service Bus umożliwia wiadomości, które należą do określonej grupy logicznej ma zostać skojarzony z danym odbiornik, który z kolei tworzy koligację przypominającej sesji, między wiadomości i ich odpowiednich odbiorców. Możesz je włączyć, zaawansowanych funkcji w usłudze Service Bus, ustawiając [SessionID](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid#Microsoft_ServiceBus_Messaging_BrokeredMessage_SessionId) właściwości wiadomości. Odbiorniki można następnie nasłuchiwanie na identyfikatorze określonej sesji i komunikaty, które mają identyfikator sesji.
* Funkcji wykrywania duplikatów, obsługiwane przez kolejek usługi Service Bus automatycznie usuwa zduplikowane wiadomości wysłanych do kolejki lub tematu, na podstawie wartości z [MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId) właściwości.

## <a name="capacity-and-quotas"></a>Pojemności i przydziałów
Ta sekcja porównuje magazynu kolejek i kolejek usługi Service Bus z punktu widzenia [pojemności i przydziałów](service-bus-quotas.md) , mogą być stosowane.

| Kryteria porównania | Kolejki magazynu | Kolejki usługi Service Bus |
| --- | --- | --- |
| Maksymalny rozmiar kolejki |**500 TB.**<br/><br/>(maksymalnie [pojemności konta magazynu z jednym](../storage/common/storage-introduction.md#queue-storage)) |**1 GB do 80 GB**<br/><br/>(zdefiniowane podczas tworzenia kolejki i [włączenie partycjonowania](service-bus-partitioning.md) — zobacz sekcję "Informacje dodatkowe") |
| Maksymalny rozmiar wiadomości |**64 KB**<br/><br/>(48 KB, korzystając z **Base64** kodowanie)<br/><br/>Azure obsługuje dużych wiadomości, łącząc kolejek i obiektów blob — w takim przypadku można umieścić w kolejce do 200 GB dla pojedynczego elementu. |**256 KB** lub **1 MB**<br/><br/>(w tym zarówno nagłówek i treść, rozmiar maksymalny nagłówka: 64 KB).<br/><br/>Zależy od [warstwy usług](service-bus-premium-messaging.md). |
| Maksymalny czas wygaśnięcia wiadomości. |**Nieskończone** (począwszy od wersji interfejsu api 2017-07-27) |**TimeSpan.Max** |
| Maksymalna liczba kolejek |**Unlimited (nieograniczony)** |**10,000**<br/><br/>(na przestrzeni nazw usługi) |
| Maksymalna liczba jednoczesnych klientów |**Unlimited (nieograniczony)** |**Unlimited (nieograniczony)**<br/><br/>(100 limit równoczesnych połączeń dotyczy tylko komunikacja oparta na protokole TCP) |

### <a name="additional-information"></a>Dodatkowe informacje
* Magistrala usług wymusza limity rozmiaru kolejki. Maksymalny rozmiar kolejki jest określany podczas tworzenia kolejki i może mieć wartość z zakresu od 1 do 80 GB. Po osiągnięciu wartości rozmiar kolejki ustawionej tworzenia kolejki dodatkowych komunikatów przychodzących zostanie odrzucone i wyjątek zostanie odebrana przez kod wywołujący. Aby uzyskać więcej informacji na temat przydziały w usłudze Service Bus, zobacz [przydziały magistrali usługi](service-bus-quotas.md).
* Partycjonowanie nie jest obsługiwany w [warstwy Premium](service-bus-premium-messaging.md). W warstwie standardowa można utworzyć kolejki usługi Service Bus w rozmiarze 1, 2, 3, 4 lub 5 GB (wartość domyślna to 1 GB). W standardzie warstwy z partycjonowania włączone (jest to wartość domyślna), usługi Service Bus tworzy 16 partycji dla każdego Gigabajta określisz. Tak, można utworzyć kolejkę o rozmiarze 5 GB, z 16 partycji maksymalny rozmiar kolejki staje się (5 * 16) = 80 GB. Maksymalny rozmiar kolejki podzielonym na partycje lub temat widzą analizując jego wpis [portalu Azure][Azure portal].
* W przypadku magazynu kolejek, jeśli treść wiadomości nie jest bezpieczną XML, następnie musi być **Base64** zakodowany. Jeśli użytkownik **Base64**-kodowania wiadomości, ładunku użytkownika może być maksymalnie 48 KB, zamiast 64 KB.
* W przypadku kolejek usługi Service Bus, każdy komunikat przechowywane w kolejce składa się z dwóch części: Nagłówek i treść. Całkowity rozmiar wiadomości nie może przekraczać maksymalny rozmiar wiadomości obsługiwane przez warstwę usług.
* Gdy klienci komunikują się z kolejek usługi Service Bus za pośrednictwem protokołu TCP, maksymalną liczbę równoczesnych połączeń do pojedynczej kolejki usługi Service Bus jest ograniczona do 100. Ta liczba jest udostępniana między nadawcami a odbiornikami. Po osiągnięciu tego limitu przydziału, kolejne żądania dotyczące dodatkowych połączeń zostaną odrzucone i wyjątek zostanie odebrana przez kod wywołujący. Ten limit jest nie nakłada na klientów łączących się z kolejek przy użyciu opartego na interfejsie REST API.
* Jeśli potrzebujesz więcej niż 10000 kolejek w jednej przestrzeni nazw usługi Service Bus, można skontaktuj się z zespołem pomocy technicznej platformy Azure i zwiększenia. Aby skalować ponad 10 000 kolejki z usługą Service Bus, można również utworzyć dodatkowe przestrzenie nazw przy użyciu [portalu Azure][Azure portal].

## <a name="management-and-operations"></a>Zarządzanie i operacje
Ta sekcja porównuje funkcji zarządzania dostarczanych przez magazyn kolejek i kolejek usługi Service Bus.

| Kryteria porównania | Kolejki magazynu | Kolejki usługi Service Bus |
| --- | --- | --- |
| Protokół zarządzania |**REST za pośrednictwem protokołu HTTP/HTTPS** |**Zatrzymaj przy użyciu protokołu HTTPS** |
| Protokół środowiska wykonawczego |**REST za pośrednictwem protokołu HTTP/HTTPS** |**Zatrzymaj przy użyciu protokołu HTTPS**<br/><br/>**Protokół AMQP 1.0 Standard (TCP z protokołem TLS)** |
| Interfejs API .NET |**Tak**<br/><br/>(.NET magazynu klienta interfejsu API) |**Tak**<br/><br/>(.NET usługi Service Bus interfejsu API) |
| Natywnych języka C++ |**Tak** |**Tak** |
| Interfejs API języka Java |**Tak** |**Tak** |
| INTERFEJS API PHP |**Tak** |**Tak** |
| Interfejsu API środowiska node.js |**Tak** |**Tak** |
| Obsługa dowolnego metadanych |**Tak** |**Nie** |
| Reguły nazewnictwa kolejki |**Do 63 znaków.**<br/><br/>(Litery w nazwie kolejki muszą być małe litery). |**Maksymalnie 260 znaków.**<br/><br/>(Nazwy i ścieżki kolejki jest rozróżniana wielkość liter.) |
| Get — funkcja długość kolejki |**Tak**<br/><br/>(Przybliżony wartość, jeśli wiadomości wygasają poza czasem wygaśnięcia bez usuwany.) |**Tak**<br/><br/>(Wartość dokładne, w momencie.) |
| Peek — funkcja |**Tak** |**Tak** |

### <a name="additional-information"></a>Dodatkowe informacje
* Magazyn kolejek zapewnia obsługę dowolnego atrybuty, które można zastosować w opisie kolejki w postaci pary nazwa/wartość.
* Obu tych technologii kolejki oferują możliwość wglądu bez konieczności zablokowanie, które mogą być przydatne podczas wdrażania narzędzia przeglądarki/explorer kolejki wiadomości.
* .NET magistrali usług obsługiwanych przez brokera obsługi komunikatów interfejsów API wykorzystać pełnego dupleksu połączeń TCP dla lepszą wydajność w porównaniu z REST za pośrednictwem protokołu HTTP i obsługują standardowego protokołu AMQP 1.0.
* Nazwy kolejki magazynu może mieć 3 do 63 znaków długości, mogą zawierać małe litery, cyfry i łączniki. Aby uzyskać więcej informacji, zobacz [nazewnictwo kolejek i metadanych](/rest/api/storageservices/fileservices/Naming-Queues-and-Metadata).
* Nazwy kolejki usługi Service Bus można maksymalnie 260 znaków i mieć mniej restrykcyjnych reguł nazewnictwa. Nazwy kolejki usługi Service Bus może zawierać litery, cyfry, kropki, łączniki i podkreślenia.

## <a name="authentication-and-authorization"></a>Uwierzytelnianie i autoryzacja
W tej sekcji omówiono funkcji uwierzytelniania i autoryzacji obsługiwanych przez magazyn kolejek i kolejek usługi Service Bus.

| Kryteria porównania | Kolejki magazynu | Kolejki usługi Service Bus |
| --- | --- | --- |
| Authentication |**Klucz symetryczny** |**Klucz symetryczny** |
| Model zabezpieczeń |Delegowane dostęp za pośrednictwem tokeny sygnatury dostępu Współdzielonego. |SAS |
| Federacyjnych dostawca tożsamości |**Nie** |**Tak** |

### <a name="additional-information"></a>Dodatkowe informacje
* Każde żądanie jednej Kolejkowanie technologie musi zostać uwierzytelniony. Kolejek publicznych z dostęp anonimowy nie są obsługiwane. Przy użyciu [SAS](service-bus-sas.md), w tym scenariuszu można rozwiązać przez publikowanie tylko do zapisu dysków SAS, SAS tylko do odczytu lub nawet SAS pełnego dostępu.
* Schemat uwierzytelniania podany przez magazyn kolejek obejmuje użycie klucza symetrycznego, które jest oparte na wyznaczania wartości skrótu wiadomości kodu uwierzytelniania (HMAC), obliczana przy użyciu algorytmu SHA-256 i zakodowane jako **Base64** ciągu. Aby uzyskać więcej informacji na temat protokołu odpowiednich zobacz [uwierzytelniania dla usług magazynu Azure](/rest/api/storageservices/fileservices/Authentication-for-the-Azure-Storage-Services). Kolejek usługi Service Bus obsługują model podobne przy użyciu kluczy symetrycznych. Aby uzyskać więcej informacji, zobacz [uwierzytelniania podpisu dostępu udostępnionych za pomocą usługi Service Bus](service-bus-sas.md).

## <a name="conclusion"></a>Podsumowanie
Przejmując lepiej zrozumieć te dwie technologie, będzie mogła wprowadzać bardziej świadomych decyzji o technologii kolejki oraz kiedy. Decyzja o tym, kiedy należy używać magazynu kolejek lub kolejek usługi Service Bus wyraźnie zależy od wielu czynników. Te czynniki mogą stopniu zależne od indywidualnych potrzeb aplikacji i jej architektury. Jeśli aplikacja już używa podstawowych możliwości platformy Microsoft Azure, można wybrać magazynu kolejek, zwłaszcza jeśli wymagają komunikacja podstawowa i wysyłanie komunikatów między usług lub kolejek na potrzeby, które może być większy niż rozmiar 80 GB.

Ponieważ kolejek usługi Service Bus zapewniają zaawansowane funkcje, takie jak sesje, transakcje, zduplikowany wykrywania automatycznego Obsługa utraconych komunikatów i trwałe możliwości publikowania/subskrybowania, mogą one być preferowanym rozwiązaniem budowania aplikacji hybrydowych, lub jeśli w przeciwnym razie aplikacja wymaga tych funkcji.

## <a name="next-steps"></a>Kolejne kroki
Poniższe artykuły zawierają więcej wskazówki i informacje o używaniu magazynu kolejki lub kolejek usługi Service Bus.

* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać kolejek usługi magazynu](../storage/queues/storage-dotnet-how-to-use-queues.md)
* [Najlepsze rozwiązania dotyczące poprawy wydajności przy użyciu usługi Service Bus obsługiwanych przez brokera obsługi komunikatów](service-bus-performance-improvements.md)
* [Wprowadzenie do kolejek i tematów w magistrali usługi Azure (wpis w blogu)](http://www.code-magazine.com/article.aspx?quickid=1112041)
* [Przewodnik dewelopera usługi Service Bus](http://www.cloudcasts.net/devguide/Default.aspx?id=11030)
* [Korzystanie z usługi kolejkowania wiadomości na platformie Azure](http://www.developerfusion.com/article/120197/using-the-queuing-service-in-windows-azure/)

[Azure portal]: https://portal.azure.com


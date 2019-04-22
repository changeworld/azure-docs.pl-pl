---
title: Kolejki magazynu platformy Azure i kolejek usługi Service Bus porównanie i różnice | Dokumentacja firmy Microsoft
description: Analizuje różnice i podobieństwa dwóch typów kolejek oferowanych na platformie Azure.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: f07301dc-ca9b-465c-bd5b-a0f99bab606b
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 2086813b01de6cd06f3714477e56864b36196382
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2019
ms.locfileid: "59699051"
---
# <a name="storage-queues-and-service-bus-queues---compared-and-contrasted"></a>Kolejki magazynu i kolejek usługi Service Bus — porównanie
W tym artykule przeanalizowano różnice i podobieństwa dwóch typów kolejek oferowanych przez Microsoft Azure już dzisiaj: Kolejki magazynu i kolejek usługi Service Bus. Dzięki tym informacjom można porównać odpowiednie technologie i świadomie wybrać rozwiązanie, które najlepiej odpowiada danym potrzebom.

## <a name="introduction"></a>Wprowadzenie
Platforma Azure obsługuje dwa rodzaje mechanizmów kolejki: **Kolejki magazynu** i **kolejek usługi Service Bus**.

**Kolejki magazynu**, które są częścią [usługi Azure storage](https://azure.microsoft.com/services/storage/) infrastruktury, funkcja prosty interfejs oparty na protokole REST GET/PUT/PODEJRZENIE, zapewniając niezawodnej, stałej obsługi komunikatów w ramach i między usługami.

**Kolejki usługi Service Bus** należą do szerszego [Azure messaging](https://azure.microsoft.com/services/service-bus/) infrastrukturę, która obsługuje usługi kolejkowania wiadomości, a także publikowania/subskrybowania oraz bardziej zaawansowane wzorce integracji. Aby uzyskać więcej informacji na temat usługi Service Bus kolejek/tematów/subskrypcji, zobacz [Omówienie usługi Service Bus](service-bus-messaging-overview.md).

Gdy obie technologie kolejkowania równolegle kolejek magazynu zostały wprowadzone po pierwsze, stanowiąc dedykowaną kolejkę mechanizm magazynu, korzystających z usługi Azure Storage. Kolejki usługi Service Bus są tworzone na podstawie szersze infrastruktura obsługi komunikatów, zaprojektowany pod kątem integracji z aplikacjami lub składnikami aplikacji, które może obejmować wiele protokołów komunikacyjnych, kontraktów danych, domenami zaufania lub środowiskach sieciowych.

## <a name="technology-selection-considerations"></a>Zagadnienia dotyczące wyboru technologii
Kolejki magazynu i kolejek usługi Service Bus stanowią implementacje kolejkowania usługa, obecnie oferowane przez Microsoft Azure. Zestaw funkcji nieco inny, co oznacza, że można wybrać jednego lub drugiego, lub używać ich obu w zależności od potrzeb biznesowych/technical problem, który rozwiązywanych lub konkretnego rozwiązania, na których każda ma.

Podczas określania, która technologia usługi kolejkowania pasuje do zastosowania dla danego rozwiązania, rozwiązanie dla architektów i deweloperów należy wziąć pod uwagę te zalecenia. Aby uzyskać więcej informacji zobacz następną sekcję.

Architekt rozwiązań/programista **należy rozważyć użycie kolejek magazynu** po:

* Aplikacja musi przechowywać ponad 80 GB wiadomości w kolejce.
* Aplikacja chce, aby śledzić postęp przetwarzania komunikatu w kolejce. Jest to przydatne, jeśli wystąpiła awaria procesu roboczego przetwarza komunikat. Kolejne procesu roboczego następnie można użyć tych informacji do kontynuowania pracy tam, gdzie przerwaliśmy poprzedniego procesu roboczego.
* Wymagane dzienniki po stronie serwera dla wszystkich transakcji wykonywane względem Twojej kolejki.

Architekt rozwiązań/programista **należy rozważyć użycie kolejek usługi Service Bus** po:

* Rozwiązanie musi być może odbierać wiadomości bez konieczności sondowania kolejki. Usługa Service Bus można to osiągnąć przy użyciu długiego sondowania przy użyciu protokołów oparte na protokole TCP, które Usługa Service Bus obsługuje operacji odbioru.
* Rozwiązanie wymaga kolejki do zapewnienia gwarantowanej pierwszy wejściu — pierwszy na wyjściu (FIFO) uporządkowane dostarczania.
* Rozwiązanie musi mieć możliwość obsługi automatycznego wykrywania duplikatów.
* Chcesz Twojej aplikacji do przetwarzania komunikatów w postaci równoległej strumieni długotrwałych (komunikaty są skojarzone z strumienia z wykorzystaniem [SessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid) właściwości wiadomości). W tym modelu każdy węzeł w aplikacja odbierająca komunikaty konkuruje dla strumieni, w przeciwieństwie do komunikatów. Gdy strumień jest dużo węzła, węzeł można sprawdzić stan stan strumienia aplikacji za pomocą transakcji.
* Rozwiązanie wymaga transakcyjnych zachowanie i niepodzielność podczas wysyłania lub odbierania wielu komunikatów z kolejki.
* Aplikacji obsługi wiadomości, które może być dłuższa niż 64 KB, ale ograniczy prawdopodobnie nie podejście 256 KB.
* Praca z wymaganiem, aby zapewnić model opartej na rolach dostęp do kolejek i inne prawa/uprawnienia dla nadawców i odbiorców.
* Rozmiar kolejki rośnie nie większy niż 80 GB.
* Chcesz używać protokołu obsługi komunikatów oparte na standardach protokołu AMQP 1.0. Aby uzyskać więcej informacji na temat protokołu AMQP, zobacz [Omówienie protokołu AMQP usługi Service Bus](service-bus-amqp-overview.md).
* Mogą traktować ostatecznej migracja z kolejki bezpośredniej komunikacji do wymiany komunikatów, który umożliwia bezproblemową integrację odbiorcy dodatkowego (subskrybentów), z których każdy odbiera niezależnych, pobranych kopii niektórych lub wszystkich Komunikaty wysyłane do kolejki. Te ostatnie odnosi się do możliwości publikowania/subskrybowania, natywnie dostarczane przez usługę Service Bus.
* Rozwiązanie do obsługi komunikatów musi mieć możliwość obsługi gwarancją dostarczania "Co większość jednokrotne" nie jest potrzebne do tworzenia składników dodatkowej infrastruktury.
* Czy chcesz mieć możliwość publikowania i używania partii komunikatów.

## <a name="comparing-storage-queues-and-service-bus-queues"></a>Porównanie kolejek magazynu i kolejek usługi Service Bus
Tabele w poniższych sekcjach umożliwiają logiczne grupowanie funkcji kolejki i pozwalają porównać, na pierwszy rzut oka możliwości, które są dostępne zarówno w kolejkach usługi Azure Storage i kolejek usługi Service Bus.

## <a name="foundational-capabilities"></a>Podstawowe możliwości
W tej sekcji przedstawiono porównanie, niektóre z podstawowych funkcji usługi kolejkowania, dostarczone przez kolejki magazynu i kolejek usługi Service Bus.

| Kryteria porównania | Kolejki magazynu | Kolejki usługi Service Bus |
| --- | --- | --- |
| Kolejność gwarancji |**Nie** <br/><br>Aby uzyskać więcej informacji zobacz pierwszą uwagi w części "Informacje dodatkowe".</br> |**Tak - pierwszej wejściu — pierwszy na wyjściu (FIFO)**<br/><br>(przy użyciu komunikatów sesji) |
| Gwarancja dostarczenia |**At-Least-Once** |**At-Least-Once**<br/><br/>**Większość jednocześnie** |
| Niepodzielna operacja pomocy technicznej |**Nie** |**Tak**<br/><br/> |
| Odbieranie zachowanie |**Nieblokujące**<br/><br/>(kończy natychmiast, jeśli zostanie znaleziony żaden nowy komunikat) |**Blokowanie z lub bez limitu czasu**<br/><br/>(oferuje długim sondowaniem lub ["Comet techniki"](https://go.microsoft.com/fwlink/?LinkId=613759))<br/><br/>**Nieblokujące**<br/><br/>(przy użyciu platformy .NET API tylko zarządzany) |
| Interfejsu API wypychania stylu |**Nie** |**Tak**<br/><br/>[OnMessage](/dotnet/api/microsoft.servicebus.messaging.queueclient.onmessage#Microsoft_ServiceBus_Messaging_QueueClient_OnMessage_System_Action_Microsoft_ServiceBus_Messaging_BrokeredMessage__) i **OnMessage** sesje interfejsu API platformy .NET. |
| Tryb odbierania |**Odbierz & dzierżawy** |**Odbierz & blokady**<br/><br/>**Odbieranie i Usuń** |
| Tryb wyłączności |**Na podstawie dzierżawy** |**Na podstawie blokady** |
| Czas trwania blokady/na dzierżawę |**30 sekund (ustawienie domyślne)**<br/><br/>**7 dni (maksymalnie)** (można odnowienia lub wydania dzierżawy komunikat przy użyciu [UpdateMessage](/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue.updatemessage) interfejsu API.) |**60 sekund (ustawienie domyślne)**<br/><br/>Można odnowić blokady komunikat przy użyciu [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) interfejsu API. |
| Dzierżawa/Lock dokładności |**Poziom komunikatu**<br/><br/>(każdy komunikat może mieć wartość inny limit można zaktualizować zgodnie z potrzebami podczas przetwarzania komunikatu, za pomocą [UpdateMessage](/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue.updatemessage) interfejsu API) |**Poziom kolejki**<br/><br/>(Każda kolejka ma dokładności blokady stosowane do wszystkich jego wiadomości, ale można odnowić blokady, za pomocą [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) interfejsu API.) |
| Wsadowe odbierania |**Tak**<br/><br/>(jawne określenie liczby komunikatów podczas pobierania wiadomości, maksymalnie 32 komunikaty) |**Tak**<br/><br/>(niejawnie włączenie właściwości pobierania wstępnego lub jawnie przy użyciu transakcji) |
| Wyślij wsadów |**Nie** |**Tak**<br/><br/>(przy użyciu transakcji lub przetwarzanie wsadowe po stronie klienta) |

### <a name="additional-information"></a>Dodatkowe informacje
* Komunikaty w kolejkach usługi Storage są zazwyczaj pierwsze wejściu — pierwszy na wyjściu, ale czasami może być uszkodzony; na przykład komunikat o widoczności limitu czasu wygaśnięcia (na przykład w wyniku awarii podczas przetwarzania aplikacji klienckiej). Po upływie tego czasu widoczności, komunikat staje się widoczna ponownie w kolejce dla innego procesu roboczego do usuwania go. W tym momencie nowo widoczny komunikat może być umieszczone w kolejce (usuniętej ponownie), po komunikat, który pierwotnie został umieszczony w kolejce po nim.
* Gwarantowana wzorzec FIFO w kolejek usługi Service Bus wymaga użycia sesji komunikacji. W przypadku, gdy wystąpiła awaria podczas przetwarzania wiadomość zostaje odebrana w aplikacji **wgląd i blokowanie** trybu przy następnym odbiorcy kolejka akceptuje sesji obsługi komunikatów, rozpocznie się komunikatem nie powiodło się po jego czas wygaśnięcia (TTL) okres wygaśnięcia.
* Kolejki magazynu są przeznaczone do wsparcia kolejkowania scenariusze standardowe, takie jak odsprzęgania składniki aplikacji w celu zwiększenia skalowalności i tolerancji w przypadku błędów ładowania wyrównywanie i przepływy pracy procesu kompilacji.
* Obsługa kolejki usługi Service Bus *co najmniej jednokrotne* gwarancja dostarczenia. 
* Można uniknąć niespójności w odniesieniu do obsługi komunikatów w ramach sesji usługi Service Bus przy użyciu stanu sesji na przechowywanie stanu aplikacji względem postęp obsługi sekwencji komunikatów na sesji i za pomocą transakcji wokół Rozliczanie odebrane wiadomości i aktualizowaniu stanu sesji. Czasami nazywa ten rodzaj funkcji spójności *dokładnie — po zakończeniu przetwarzania* w innych dostawców produktów, ale transakcji błędy oczywiście spowoduje, że komunikaty, aby być dostarczony ponownie i dlatego termin dokładnie odpowiednie.
* Kolejki magazynu zapewnienia jednolitego i spójny model programowania kolejek, tabel i obiektów blob — dla deweloperów i zespołów operacyjnych.
* Kolejki usługi Service Bus zapewnia obsługę lokalnej transakcji w ramach pojedynczej kolejki.
* **Odbieranie i usuwanie** trybem obsługiwanym przez usługi Service Bus zapewnia możliwość zmniejszyć liczbę operacji obsługi komunikatów (i skojarzone z nim koszty) w zamian za zapewnienie skróconego dostarczania.
* Kolejki magazynu umożliwiają dzierżawy z możliwością rozszerzenia dzierżawy dla wiadomości. Dzięki temu pracownikom zachować krótki dzierżaw dla wiadomości. W związku z tym jeśli proces roboczy ulegnie awarii, wiadomości mogą być szybko przetwarzane ponownie przez innego procesu roboczego. Ponadto proces roboczy mogą rozszerzać dzierżawy w komunikacie wymaga przetworzenia jej dłużej niż bieżący czas dzierżawy.
* Kolejki usługi Storage oferuje widoczność limit czasu można ustawić na enqueuing lub usuwania z kolejki komunikatów. Ponadto można zaktualizować wiadomości przy użyciu wartości z innej dzierżawy w czasie wykonywania i zaktualizować różne wartości w wiadomości w tej samej kolejki. Limity czasu blokowania usługi Service Bus są zdefiniowane w metadanych kolejki; jednak można odnowić blokady przez wywołanie metody [RenewLock](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.renewlock#Microsoft_ServiceBus_Messaging_BrokeredMessage_RenewLock) metody.
* Maksymalny limit czasu operacji blokowania odbioru w kolejek usługi Service Bus to 24 dni. Jednak oparte na protokole REST przekroczeń limitu czasu ma maksymalną wartość sekund 55.
* Po stronie klienta przetwarzanie wsadowe, dostarczone przez usługę Service Bus umożliwia klientowi kolejki operacji wysyłania pojedynczej usługi batch wiele wiadomości. Przetwarzanie wsadowe jest dostępna tylko dla operacji Wyślij asynchroniczne.
* Funkcje, takie jak limit 200 TB kolejki magazynu (więcej informacji podczas wirtualizacji konta) i nieograniczona liczba kolejek był jest idealną platformą dla dostawców SaaS.
* Kolejki magazynu umożliwiają elastyczne i wydajne delegowane mechanizmu kontroli dostępu.

## <a name="advanced-capabilities"></a>Zaawansowane funkcje
Ta sekcja zawiera porównanie zaawansowane możliwości zapewniane przez kolejki magazynu i kolejek usługi Service Bus.

| Kryteria porównania | Kolejki magazynu | Kolejki usługi Service Bus |
| --- | --- | --- |
| Zaplanowane dostarczanie |**Tak** |**Tak** |
| Automatyczne utraconych |**Nie** |**Tak** |
| Zwiększenie wartości czasu wygaśnięcia kolejki |**Tak**<br/><br/>(za pośrednictwem aktualizacji w miejscu limitu czasu widoczności) |**Tak**<br/><br/>(udostępnione za pośrednictwem dedykowanego interfejsu API function) |
| Obsługa komunikatów zanieczyszczonych |**Tak** |**Tak** |
| Aktualizacja "w miejscu" |**Tak** |**Tak** |
| Dziennik transakcji po stronie serwera |**Tak** |**Nie** |
| Metryki magazynu |**Tak**<br/><br/>**Minuta metryki**: udostępnia metryki w czasie rzeczywistym dla interfejsu API dostępność, TPS, wywołaj liczników, liczniki błąd i nie tylko w czasie rzeczywistym (zagregowane na minutę i zgłaszanych w ciągu kilku minut od co właśnie wydarzyło się w środowisku produkcyjnym. Aby uzyskać więcej informacji, zobacz [o Storage Analytics Metrics](/rest/api/storageservices/fileservices/About-Storage-Analytics-Metrics). |**Tak**<br/><br/>(zbiorczo zapytania, wywołując [GetQueues](/dotnet/api/microsoft.servicebus.namespacemanager.getqueues#Microsoft_ServiceBus_NamespaceManager_GetQueues)) |
| Zarządzanie stanem |**Nie** |**Tak**<br/><br/>[Microsoft.ServiceBus.Messaging.EntityStatus.Active](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.ServiceBus.Messaging.EntityStatus.Disabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.ServiceBus.Messaging.EntityStatus.SendDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus), [Microsoft.ServiceBus.Messaging.EntityStatus.ReceiveDisabled](/dotnet/api/microsoft.servicebus.messaging.entitystatus) |
| Komunikat o automatycznym przekazywaniem |**Nie** |**Tak** |
| Wyczyść kolejkę — funkcja |**Tak** |**Nie** |
| Grupy komunikatów |**Nie** |**Tak**<br/><br/>(przy użyciu komunikatów sesji) |
| Stan aplikacji dla każdej grupy komunikatów |**Nie** |**Tak** |
| Wykrywanie duplikatów |**Nie** |**Tak**<br/><br/>(można je konfigurować na stronie nadawcy) |
| Przeglądanie grupy komunikatów |**Nie** |**Tak** |
| Pobieranie sesji komunikatów według Identyfikatora |**Nie** |**Tak** |

### <a name="additional-information"></a>Dodatkowe informacje
* Obie technologie kolejkowania Włącz wiadomości do zaplanowania dostarczania w późniejszym czasie.
* Automatyczne przekazywanie kolejki umożliwia tysiące kolejek do automatycznego przekazywania dalej wiadomości do pojedynczej kolejki, z którego aplikacja zużywa wiadomości. Można użyć ten mechanizm do osiągnięcia zabezpieczenia, przepływ sterowania i izolowania magazynu między każdy wydawca wiadomości.
* Kolejki magazynu umożliwiają aktualizowanie zawartości wiadomości. Można użyć tej funkcji utrwalanie informacji o stanie i przyrostowy postęp aktualizacji do wiadomości, dzięki czemu mogą być przetwarzane od ostatniego znanego punktu kontrolnego, zamiast od zera. Za pomocą kolejek usługi Service Bus można włączyć ten sam scenariusz przy użyciu sesji komunikatów. Sesje umożliwiają zapisywanie i pobieranie stanu przetwarzania aplikacji (przy użyciu [SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) i [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState)).
* [Dead drukiem](service-bus-dead-letter-queues.md), który jest tylko do obsługiwanych przez usługi Service Bus, może być przydatne do izolowania wiadomości, który nie może pomyślnie przetworzone przez aplikację odbierającą lub gdy komunikatów nie może połączyć się ich przeznaczenia z powodu wygasły (time-to-live Właściwość TTL). Wartość TTL Określa, jak długo pozostaje wiadomość w kolejce. Usługa Service Bus wiadomość zostanie przeniesiona do kolejki specjalne o nazwie $DeadLetterQueue po wygaśnięciu danego okresu czasu wygaśnięcia.
* Znajdowanie "" wiadomości w kolejkach magazynu podczas usuwania z kolejki, komunikat aplikacji sprawdza [DequeueCount](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueuemessage.dequeuecount.aspx) właściwość komunikatu. Jeśli **DequeueCount** przekracza wartość progową, aplikacja przenosi wiadomość do zdefiniowanych przez aplikację "kolejki utraconych wiadomości".
* Kolejki magazynu umożliwiają uzyskanie szczegółowego dziennika dla wszystkich transakcji wykonywane w odniesieniu do kolejki, jak również jak zagregowanych metryk. Obie te opcje są przydatne do debugowania i zrozumienie, jak aplikacja używa kolejek magazynu. Są one również przydatne w przypadku aplikacji dostrajanie wydajności i obniżenia kosztów przy użyciu funkcji kolejek.
* Pojęcie "sesji komunikatów" obsługiwane przez usługę Service Bus umożliwia wiadomości, które należą do określonej grupy logicznej ma zostać skojarzony z danym odbiornik, który z kolei tworzy koligację sesji przypominającej między wiadomości i ich odpowiednich odbiorców. Możesz je włączyć zaawansowanych funkcji w usłudze Service Bus, ustawiając [SessionID](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid#Microsoft_ServiceBus_Messaging_BrokeredMessage_SessionId) właściwości w komunikacie. Odbiorniki można nasłuchiwać Identyfikatora określonej sesji i odbierać komunikaty, które mają identyfikator określonej sesji.
* Funkcji wykrywania duplikatów, które są obsługiwane przez usługi Service Bus automatycznie usuwa zduplikowane komunikaty wysłane do kolejki lub tematu, w oparciu o wartość [MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId) właściwości.

## <a name="capacity-and-quotas"></a>Pojemności i przydziałów
W tej sekcji porównuje kolejki magazynu i kolejek usługi Service Bus z punktu widzenia [pojemności i przydziałów](service-bus-quotas.md) , może być stosowane.

| Kryteria porównania | Kolejki magazynu | Kolejki usługi Service Bus |
| --- | --- | --- |
| Maksymalny rozmiar kolejki |**500 TB**<br/><br/>(maksymalnie [pojedynczy pojemności konta magazynu](../storage/common/storage-introduction.md#queue-storage)) |**1 GB do 80 GB**<br/><br/>(zdefiniowane podczas tworzenia kolejki i [włączenie partycjonowania](service-bus-partitioning.md) – zobacz sekcję "Informacje dodatkowe") |
| Maksymalny rozmiar wiadomości |**64 KB**<br/><br/>(48 KB w przypadku korzystania z **Base64** kodowania)<br/><br/>Platforma Azure obsługuje duże komunikaty, łącząc kolejki i obiekty BLOB — w tym momencie można umieścić w kolejce do 200 GB dla pojedynczego elementu. |**256 KB** lub **1 MB**<br/><br/>(w tym nagłówka i treści, rozmiar maksymalny nagłówka: 64 KB).<br/><br/>Zależy od [warstwy usług](service-bus-premium-messaging.md). |
| Maksymalny czas wygaśnięcia wiadomości. |**Nieskończona** (począwszy od wersji interfejsu api 2017-07-27) |**TimeSpan.Max** |
| Maksymalna liczba kolejek |**Unlimited (nieograniczony)** |**10,000**<br/><br/>(na przestrzeń nazw usług) |
| Maksymalna liczba współbieżnych klientów |**Unlimited (nieograniczony)** |**Unlimited (nieograniczony)**<br/><br/>(100 limit współbieżnych połączeń dotyczą tylko komunikacji oparte na protokole TCP) |

### <a name="additional-information"></a>Dodatkowe informacje
* Usługa Service Bus wymusza limitach rozmiaru kolejek. Maksymalny rozmiar kolejki jest określana podczas tworzenia kolejki i może mieć wartość z zakresu od 1 do 80 GB. W przypadku osiągnięcia wartości rozmiar kolejki po utworzeniu kolejki dodatkowe komunikaty przychodzące są odrzucane, a wyjątek zostanie odebrana przez kod wywołujący. Aby uzyskać więcej informacji na temat limitów przydziału w usłudze Service Bus, zobacz [przydziały magistrali usługi](service-bus-quotas.md).
* Partycjonowanie nie jest obsługiwana w [w warstwie Premium](service-bus-premium-messaging.md). W warstwie standardowa można utworzyć kolejki usługi Service Bus w rozmiarach 1, 2, 3, 4 lub 5 GB (wartość domyślna to 1 GB). W standardowej warstwie partycjonowania włączone (co jest ustawieniem domyślnym), usługi Service Bus tworzy partycje 16 za każdy GB, można określić. Jako takie, tworząc kolejki, która ma rozmiar 5 GB, z 16 partycji maksymalny rozmiar kolejki staje się (5 * 16) = 80 GB. Maksymalny rozmiar podzieleniu kolejki lub tematu widoczne, analizując jego wpis [witryny Azure portal][Azure portal].
* W przypadku kolejek magazynu, jeśli zawartość komunikatu nie jest bezpieczne XML, następnie musi być **Base64** zakodowany. Jeśli użytkownik **Base64**— Kodowanie komunikatu, ładunek użytkownika mogą mieć maksymalnie 48 KB, zamiast 64 KB.
* Za pomocą kolejek usługi Service Bus, każdy komunikat przechowywane w kolejce składa się z dwóch części: Nagłówek i treść. Łączny rozmiar komunikatu nie może przekraczać maksymalny rozmiar wiadomości obsługiwane przez warstwę usług.
* Gdy klienci komunikują się za pomocą kolejek usługi Service Bus za pośrednictwem protokołu TCP, maksymalną liczbę jednoczesnych połączeń z pojedynczą kolejką usługi Service Bus jest ograniczona do 100. Liczba ta jest udostępniana między nadawcami a odbiornikami. Po osiągnięciu tego limitu przydziału, kolejne żądania dla dodatkowych połączeń zostanie odrzucony, a wyjątek zostanie odebrana przez kod wywołujący. Ten limit nie obsługują przypadające na klientów łączących się z kolejki za pomocą interfejsu opartego na protokole REST API.
* Jeśli potrzebujesz więcej niż 10 000 kolejek w jednej przestrzeni nazw usługi Service Bus, możesz skontaktować się z zespołem pomocy technicznej platformy Azure i poprosić o zwiększenie. Będą skalowane ponad 10 000 kolejek usługi Service Bus, możesz również utworzyć dodatkowe przestrzenie nazw, używając [witryny Azure portal][Azure portal].

## <a name="management-and-operations"></a>Zarządzanie i operacje
Ta sekcja zawiera porównanie funkcji zarządzania oferowanych przez kolejki magazynu i kolejek usługi Service Bus.

| Kryteria porównania | Kolejki magazynu | Kolejki usługi Service Bus |
| --- | --- | --- |
| Protokół zarządzania |**REST za pośrednictwem protokołu HTTP/HTTPS** |**REST przy użyciu protokołu HTTPS** |
| Protokół czasu wykonywania |**REST za pośrednictwem protokołu HTTP/HTTPS** |**REST przy użyciu protokołu HTTPS**<br/><br/>**Protokół AMQP 1.0 Standard (TCP z protokołem TLS)** |
| Interfejs API .NET |**Tak**<br/><br/>(.NET interfejsu API klienta magazynu) |**Tak**<br/><br/>(.NET usługi Service Bus interfejsu API) |
| Natywnych języka C++ |**Tak** |**Tak** |
| Interfejs API języka Java |**Tak** |**Tak** |
| INTERFEJS API JĘZYKA PHP |**Tak** |**Tak** |
| Interfejsu API środowiska node.js |**Tak** |**Tak** |
| Obsługa dowolnego metadanych |**Tak** |**Nie** |
| Reguły nazewnictwa kolejki |**Do 63 znaków.**<br/><br/>(Litery w nazwie kolejki musi być mała). |**Maksymalnie 260 znaków.**<br/><br/>(Nazwy i ścieżki kolejki są bez uwzględniania wielkości liter). |
| Get — funkcja długość kolejki |**Tak**<br/><br/>(Stanowiących wartość, jeśli komunikaty wygasają poza czasem wygaśnięcia bez usuwany.) |**Tak**<br/><br/>(Dokładnie, w momencie wartości.) |
| Peek — funkcja |**Tak** |**Tak** |

### <a name="additional-information"></a>Dodatkowe informacje
* Kolejki usługi Storage zapewnia obsługę dowolnymi atrybutami, które mogą być stosowane w opisie kolejki w postaci par nazwa/wartość.
* Obie technologie kolejki oferują możliwość wgląd do wiadomości bez blokady, które mogą być przydatne podczas implementowania narzędzia Eksploratora/przeglądarki kolejki.
* .NET usługi Service Bus obsługiwanych przez brokera obsługi komunikatów interfejsów API Wykorzystaj pełny dupleks połączeń TCP w celu zwiększenia wydajności w porównaniu do REST za pośrednictwem protokołu HTTP i obsługują ze standardowego protokołu AMQP 1.0.
* Nazwy kolejek magazynu może zawierać znaków 3 – 63 long, mogą zawierać małe litery, cyfry i łączniki. Aby uzyskać więcej informacji, zobacz [nazewnictwo kolejek i metadanych](/rest/api/storageservices/fileservices/Naming-Queues-and-Metadata).
* Nazwy kolejek usługi Service Bus może zawierać maksymalnie 260 znaków i ma mniej restrykcyjnych reguł nazewnictwa. Nazwy kolejek usługi Service Bus może zawierać litery, cyfry, kropki, łączniki i podkreślenia.

## <a name="authentication-and-authorization"></a>Uwierzytelnianie i autoryzacja
W tej sekcji omówiono funkcje uwierzytelniania i autoryzacji, obsługiwane przez kolejki magazynu i kolejek usługi Service Bus.

| Kryteria porównania | Kolejki magazynu | Kolejki usługi Service Bus |
| --- | --- | --- |
| Authentication |**Klucz symetryczny** |**Klucz symetryczny** |
| Model zabezpieczeń |Delegowany dostęp za pomocą tokenów sygnatur dostępu Współdzielonego. |SAS |
| Federacji dostawcy tożsamości |**Nie** |**Tak** |

### <a name="additional-information"></a>Dodatkowe informacje
* Każde żądanie do jednej z technologii kolejkowania musi zostać uwierzytelnione. Kolejek publicznych za pomocą dostępu anonimowego nie są obsługiwane. Za pomocą [sygnatury dostępu Współdzielonego](service-bus-sas.md), w tym scenariuszu można rozwiązać, publikując sygnatury dostępu Współdzielonego tylko do zapisu, sygnatury dostępu Współdzielonego tylko do odczytu lub nawet pełnego dostępu sygnatury dostępu Współdzielonego.
* Schemat uwierzytelniania podane przez magazyn kolejek polega na użyciu tego klucza symetrycznego, czyli opartej na skrótach komunikat o kod uwierzytelniania (HMAC), obliczana przy użyciu algorytmu SHA-256 i zakodowanymi w formacie **Base64** ciągu. Aby uzyskać więcej informacji na temat protokołu odpowiednich zobacz [uwierzytelniania dla usług Azure Storage](/rest/api/storageservices/fileservices/Authentication-for-the-Azure-Storage-Services). Kolejki usługi Service Bus obsługuje model podobne przy użyciu kluczy symetrycznych. Aby uzyskać więcej informacji, zobacz [udostępnione uwierzytelniania sygnatury dostępu za pomocą usługi Service Bus](service-bus-sas.md).

## <a name="conclusion"></a>Podsumowanie
Przejmując lepiej zrozumieć te dwie technologie, będzie możliwe zapewnienie bardziej świadomie podjąć decyzję na technologii kolejki i kiedy. Decyzja o tym, kiedy należy używać magazynu kolejek lub kolejek usługi Service Bus wyraźnie zależy od wielu czynników. Te czynniki mogą dużym stopniu zależą od indywidualnych potrzeb aplikacji oraz jej architektury. Jeśli aplikacja już używa podstawowych możliwości systemu Microsoft Azure, warto wybierz kolejek magazynu, zwłaszcza, jeśli potrzebujesz podstawowe komunikacji i obsługi komunikatów między usługami lub kolejki potrzebę, które może być większy niż 80 GB, rozmiar.

Ponieważ kolejek usługi Service Bus zapewniają zaawansowane funkcje, takie jak sesje, transakcje, zduplikowane wykrywania automatycznego Obsługa utraconych komunikatów i niezawodne możliwości publikowania/subskrybowania, mogą być preferowanych przez budowania hybrydowych Aplikacja lub jeśli w przeciwnym razie aplikacja wymaga tych funkcji.

## <a name="next-steps"></a>Kolejne kroki
Poniższe artykuły zawierają więcej wskazówki i informacje o używaniu magazynu kolejki lub kolejek usługi Service Bus.

* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać usługi Queue Storage](../storage/queues/storage-dotnet-how-to-use-queues.md)
* [Najlepsze rozwiązania zwiększające wydajność przy użyciu usługi Service Bus obsługiwanych przez brokera obsługi komunikatów](service-bus-performance-improvements.md)
* [Wprowadzenie do kolejek i tematów w usłudze Azure Service Bus (wpis na blogu)](https://www.code-magazine.com/article.aspx?quickid=1112041)
* [Przewodnik dewelopera usługi Service Bus](http://www.cloudcasts.net/devguide/Default.aspx?id=11030)
* [Usługa kolejkowania na platformie Azure](https://www.developerfusion.com/article/120197/using-the-queuing-service-in-windows-azure/)

[Azure portal]: https://portal.azure.com


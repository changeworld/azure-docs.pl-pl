---
title: Asynchroniczna Obsługa komunikatów usługi Service Bus | Dokumentacja firmy Microsoft
description: Opis asynchronicznej obsługi komunikatów usługi Azure Service Bus.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: f1435549-e1f2-40cb-a280-64ea07b39fc7
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 0ff2fbf8ddfdd191c72cfdb36a9462076f8dec5b
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2019
ms.locfileid: "55657301"
---
# <a name="asynchronous-messaging-patterns-and-high-availability"></a>Wzorce asynchronicznej obsługi komunikatów i wysoka dostępność

Asynchroniczna Obsługa komunikatów może być implementowany w wiele różnych sposobów. Kolejki, tematy i subskrypcje usługi Azure Service Bus obsługuje asynchronism za pośrednictwem sklepu i mechanizm do przodu. W normalnych operacji (synchroniczne) wysyłanie komunikatów do kolejki i tematy i odbieranie komunikatów z subskrypcji i kolejek. Aplikacje, które piszesz zależą od tych jednostek, które zawsze są dostępne. Kondycja jednostki zmieni się, ze względu na różnych okolicznościach, konieczne jest sposób zapewnienia jednostki ograniczoną możliwość, która może spełnić zastosowaniach.

Aplikacje zazwyczaj umożliwiają wzorce asynchronicznej obsługi komunikatów wiele scenariuszy komunikacji. Możesz tworzyć aplikacje, w których klienci mogą wysyłać komunikaty do usług, nawet wtedy, gdy usługa nie jest uruchomiona. Dla aplikacji tego procesu, które mogą pomóc wzrosty komunikacji za pośrednictwem kolejki wyrównania obciążenia, zapewniając miejsce do buforu łączności. Na koniec możesz uzyskać modułu równoważenia obciążenia prosty, lecz skuteczne rozłożenie wiadomości na wielu komputerach.

W celu zapewnienia dostępności dowolnej z tych jednostek, należy wziąć pod uwagę wiele różnych sposobów, w którym te jednostki może znajdować się niedostępna w przypadku trwałych system obsługi komunikatów. Ogólnie rzecz biorąc widzimy jednostki stają się niedostępne dla aplikacji, które napiszemy na następujące sposoby:

* Nie można wysłać wiadomości.
* Nie można odbierać komunikaty.
* Nie można zarządzać jednostki (Tworzenie, pobieranie, aktualizowanie lub usuwanie jednostek).
* Nie można skontaktować się z usługą.

Dla każdego z tych błędów awarii w różnych trybach istnieje, które umożliwiają aplikacji, aby kontynuować do wykonywania pracy na pewien stopień ograniczoną możliwość. Na przykład system, który może wysyłać wiadomości, ale nie ich odbierania, może nadal otrzymywać zamówienia od klientów, ale nie będzie mógł przetworzyć tych zamówień. W tym temacie omówiono potencjalnych problemów, które mogą wystąpić, i jak te problemy zostały skorygowane. Usługa Service Bus ma wprowadzono szereg środki zaradcze, które użytkownik musi wyrazić zgodę, a w tym temacie omówiono również zasad regulujących korzystanie z tych opcjonalnych środki zaradcze.

## <a name="reliability-in-service-bus"></a>Niezawodność w usłudze Service Bus
Istnieje kilka sposobów obsługi problemów komunikat i jednostki, a istnieją wytyczne dotyczące właściwego użycia tych środków zaradczych. Zrozumienie wytycznych, musisz najpierw zrozumieć, co może nie działać w usłudze Service Bus. Ze względu na projekt systemy usługi Azure wszystkie te problemy zwykle krótkotrwałe. Na wysokim poziomie inne przyczyny niedostępności wyglądać następująco:

* Ograniczanie przepustowości z systemu zewnętrznego, od których zależy usługa Service Bus. Ograniczanie występuje z interakcji z zasobami magazynu i mocy obliczeniowej.
* Problem w przypadku systemu, od których zależy usługa Service Bus. Na przykład określona część magazynu mogą wystąpić problemy.
* Błąd usługi Service Bus w jednym podsystemie. W takiej sytuacji węzła obliczeniowego można uzyskać w niespójnym stanie i należy ponownie uruchomić, powodując wszystkich jednostek, który można załadować saldo do innych węzłów. Z kolei może to spowodować krótkim przetwarzania komunikatów powolne.
* Błąd usługi Service Bus w obrębie centrum danych platformy Azure. To jest "poważnej awarii" w którym system jest nieosiągalna przez wiele minut lub kilka godzin.

> [!NOTE]
> Termin **magazynu** może oznaczać zarówno usługi Azure Storage i SQL Azure.
> 
> 

Usługa Service Bus zawiera szereg środki zaradcze w przypadku tych problemów. W poniższych sekcjach omówiono poszczególne problemy i ich odpowiednich środków zaradczych.

### <a name="throttling"></a>Ograniczanie przepływności
Usługa Service Bus ograniczania Azure umożliwia zarządzanie współczynnik współpracy wiadomości. Poszczególnych węzłów usługi Service Bus przechowuje wiele jednostek. Każda z tych jednostek temu zapotrzebowanie na system pod kątem procesora CPU, pamięci, magazynu i innymi aspektami. Po wykryciu dowolnego z tych zestawów reguł użycia przekraczający zdefiniowanych progów, Service Bus można odmówić danego żądania. Obiekt wywołujący uzyskuje [ServerBusyException] [ ServerBusyException] i ponownych prób po 10 sekundach.

Jako ograniczenie kod musi odczytać błędu i zatrzymanie dowolnego powtórzeń komunikatu przez co najmniej 10 sekund. Ponieważ ten błąd może się zdarzyć w różnych rodzajów aplikacji klienta, oczekuje się, że każdy element niezależnie wykonuje Logika ponawiania. Kod może zmniejszyć prawdopodobieństwo, że jest ograniczane przez włączenie partycjonowania na kolejki lub tematu.

### <a name="issue-for-an-azure-dependency"></a>Problem w przypadku zależności platformy Azure
Inne składniki w ramach platformy Azure od czasu do czasu może mieć problemy z usługą. Na przykład po uaktualnieniu systemu, który używa usługi Service Bus tego systemu mogą tymczasowo występować zmniejszenie możliwości. Aby uniknąć tego rodzaju problemy, Service Bus regularnie bada i implementuje środki zaradcze. Efekty uboczne te środki zaradcze są wyświetlane. Na przykład aby obsłużyć przejściowe problemy z usługą storage, Usługa Service Bus implementuje system, który umożliwia wykonywanie operacji wysyłania komunikatu do działać spójnie. Ze względu na charakter środki zaradcze wysyłany komunikat może potrwać do 15 minut, są wyświetlane w usterce kolejki lub subskrypcji i gotowa do operacji odbierania. Ogólnie rzecz biorąc, większość jednostek będzie ten problem nie występuje. Jednak w usłudze Service Bus na platformie Azure, biorąc pod uwagę liczbę jednostek, takie rozwiązanie jest czasami wymagany przez mały podzbiór klientów usługi Service Bus.

### <a name="service-bus-failure-on-a-single-subsystem"></a>Błąd usługi Service Bus w jednym podsystemie
Z każdą aplikacją okolicznościach może powodować wewnętrznego składnikiem usługi Service Bus, aby stać się niespójna. Gdy Usługa Service Bus wykryje to, zbiera dane z aplikacji, aby pomóc w zdiagnozowaniu, co się stało. Po zebraniu danych ponownym uruchomieniu aplikacji w celu podjęcia próby przywrócić go do stanu spójności. Ten proces odbywa się dość szybko, a wyniki w jednostce, które pojawiają się jako niedostępny dla maksymalnie kilka minut, chociaż jest to typowy szczegółów czasu są znacznie krótszy.

W takich przypadkach aplikacja kliencka generuje [System.TimeoutException] [ System.TimeoutException] lub [MessagingException] [ MessagingException] wyjątku. Usługa Service Bus zawiera ograniczenia tego problemu w formie klienta automatycznego Logika ponawiania. Po wyczerpaniu okres ponowień i komunikat nie zostanie dostarczony, można eksplorować przy użyciu innych funkcji, takich jak [sparowane przestrzenie nazw][paired namespaces]. Sparowane przestrzenie nazw mają inne ostrzeżenia, które zostały omówione w tym artykule.

### <a name="failure-of-service-bus-within-an-azure-datacenter"></a>Błąd usługi Service Bus w obrębie centrum danych platformy Azure
Najbardziej prawdopodobna przyczyna niepowodzenia w centrum danych platformy Azure to wdrożenie nie powiodło się uaktualnienie usługi Service Bus lub systemy zależne. Jak dojrzewania platformy, prawdopodobieństwo tego typu Błąd został zmniejszony. Awaria centrum danych może też być powodów, które obejmują następujące czynności:

* Awaria elektrycznych (źródło zasilania i generowanie power zniknąć).
* Łączność (internet podziału między Twoimi klientami a Azure).

W obu przypadkach awarii fizycznych lub włókna przyczyny problemu. Aby obejść ten problem i upewnij się, że nadal może wysyłać komunikaty, możesz użyć [sparowane przestrzenie nazw] [ paired namespaces] komunikatów do wysłania w innej lokalizacji, podczas gdy lokalizacji głównej jest dobra ponownie włączyć. Aby uzyskać więcej informacji, zobacz [najlepsze rozwiązania dotyczące izolacji aplikacji w ramach usługi Service Bus wyłączeń i awarii][Best practices for insulating applications against Service Bus outages and disasters].

## <a name="paired-namespaces"></a>Sparowane przestrzenie nazw
[Sparowane przestrzenie nazw] [ paired namespaces] funkcja obsługuje scenariusze, w którym jednostki usługi Service Bus lub wdrażanie w ramach centrum danych staną się niedostępne. Gdy to zdarzenie występuje rzadko, systemami rozproszonymi nadal musi być przygotowana do obsługi najgorszego przypadku. Zwykle to zdarzenie występuje, ponieważ pewien element, od których zależy usługa Service Bus występuje problem z krótkoterminowej. W celu zapewnienia dostępności aplikacji podczas przestoju, użytkownicy usługi Service Bus umożliwia dwa oddzielne przestrzenie nazw, najlepiej w odrębnych danych centra, hostowanie ich jednostek obsługi komunikatów. Dalszej części tej sekcji korzysta z następującą terminologią:

* Podstawowa przestrzeń nazw: Przestrzeń nazw za pomocą którego aplikacja wchodzi w interakcję do wysyłania i operacji odbioru.
* Pomocnicza przestrzeń nazw: Przestrzeń nazw, który działa jako kopii zapasowej podstawowej przestrzeni nazw. Aplikacja logiki nie wchodzi w interakcję z tą przestrzenią nazw.
* Interwał trybu failover: Ilość czasu, aby zaakceptować normalne błędy, zanim aplikacja zostanie przełączona z podstawowej przestrzeni nazw do pomocniczej przestrzeni nazw.

Sparowane przestrzenie nazw obsługują *Wyślij dostępności*. Wyślij dostępności pozwala zachować możliwość wysyłania wiadomości. Aby użyć dostępności wysyłania, aplikacji musi spełniać następujące wymagania:

1. Komunikaty tylko są odbierane z podstawowej przestrzeni nazw.
2. Komunikaty wysyłane do danej kolejki lub tematu, może pojawić się poza kolejnością.
3. Komunikaty w ramach sesji może pojawić się poza kolejnością. Jest to podziału z normalnej funkcjonalności sesji. Oznacza to, że aplikacja używa sesji logiczne grupowanie wiadomości.
4. Stan sesji jest utrzymywany tylko względem podstawowej przestrzeni nazw.
5. Kolejki głównej można przejść w tryb online i zacznij akceptować komunikaty zanim kolejki dodatkowej dostarcza komunikaty do kolejki głównej.

W poniższych sekcjach omówiono interfejsów API, jak zostały zaimplementowane interfejsy API i przykładowy kod pokazuje, że używa funkcji. Należy zauważyć, że ma skutki rozliczeń skojarzone z tą funkcją.

### <a name="the-messagingfactorypairnamespaceasync-api"></a>MessagingFactory.PairNamespaceAsync interfejsu API
Funkcja sparowane przestrzenie nazw obejmuje [PairNamespaceAsync] [ PairNamespaceAsync] metody [Microsoft.ServiceBus.Messaging.MessagingFactory] [ Microsoft.ServiceBus.Messaging.MessagingFactory] klasy:

```csharp
public Task PairNamespaceAsync(PairedNamespaceOptions options);
```

Po zakończeniu zadania, parowania przestrzeni nazw jest również gotowy do wykonania działania w przypadku dowolnego [MessageReceiver][MessageReceiver], [QueueClient] [ QueueClient] , lub [TopicClient] [ TopicClient] utworzone za pomocą [MessagingFactory] [ MessagingFactory] wystąpienia. [Microsoft.ServiceBus.Messaging.PairedNamespaceOptions] [ Microsoft.ServiceBus.Messaging.PairedNamespaceOptions] jest klasą bazową dla różnych rodzajów parowania, które są dostępne za pomocą [MessagingFactory] [ MessagingFactory] obiektu. Obecnie tylko klasy pochodnej jest jeden o nazwie [SendAvailabilityPairedNamespaceOptions][SendAvailabilityPairedNamespaceOptions], który implementuje wymaganiami w zakresie dostępności wysyłania. [SendAvailabilityPairedNamespaceOptions] [ SendAvailabilityPairedNamespaceOptions] zawiera zbiór konstruktorów, które są kompilowane na siebie nawzajem. Patrząc konstruktora z parametrami większość, należy zrozumieć zachowanie innych konstruktorów.

```csharp
public SendAvailabilityPairedNamespaceOptions(
    NamespaceManager secondaryNamespaceManager,
    MessagingFactory messagingFactory,
    int backlogQueueCount,
    TimeSpan failoverInterval,
    bool enableSyphon)
```

Parametry te mają następujące znaczenie:

* *secondaryNamespaceManager*: Zainicjowana klasa [NamespaceManager] [ NamespaceManager] wystąpienia dla pomocniczej przestrzeni nazw, [PairNamespaceAsync] [ PairNamespaceAsync] metody można ustawić za pomocą się pomocniczej przestrzeni nazw. Menedżer przestrzeni nazw jest używana, aby uzyskać listę kolejek w przestrzeni nazw i upewnij się, czy istnieją wymagane zaległości kolejki. Jeśli te kolejki nie istnieją, są one tworzone. [NamespaceManager] [ NamespaceManager] wymaga możliwości Utwórz token za pomocą **Zarządzaj** oświadczenia.
* *messagingFactory*: [MessagingFactory] [ MessagingFactory] wystąpienia dla pomocniczej przestrzeni nazw. [MessagingFactory] [ MessagingFactory] obiekt jest używany do wysyłania i, jeśli [EnableSyphon] [ EnableSyphon] właściwość jest ustawiona na **true**, odbiera komunikaty z kolejki zaległości.
* *backlogQueueCount*: Liczba kolejek zaległości do utworzenia. Ta wartość musi wynosić co najmniej 1. Podczas wysyłania komunikatów do listy prac, jeden z tych kolejkach jest wybierany losowo. Jeśli wartość jest ustawiona na 1, następnie tylko jedna kolejka może być nigdy nie użył. Gdy tak się stanie, a kolejki jednej zaległości generuje błędy, klient nie jest w stanie wypróbować inną zaległości kolejki i może zakończyć się niepowodzeniem do wysłania wiadomości. Firma Microsoft zaleca ustawienie tej wartości na niektórych większych wartości i domyślne wartości do 10. Można to zmienić, aby wyższą lub niższą wartością w zależności od ilości danych, Twoja aplikacja przesyła dziennie. Każdej zaległości kolejki może zawierać maksymalnie 5 GB wiadomości.
* *failoverInterval*: Ilość czasu, w którym będzie akceptować błędów w podstawowej przestrzeni nazw przed przełączeniem wszelkie pojedynczej jednostki za pośrednictwem do pomocniczej przestrzeni nazw. Przejścia w tryb failover odbywa się na zasadzie jednostki przez jednostki. Jednostki w jednej przestrzeni nazw często znajdują się w różnych węzłach w ramach usługi Service Bus. Błąd w jednej jednostce nie oznacza niepowodzenie w innym. Ta wartość zostanie ustawiona na [System.TimeSpan.Zero] [ System.TimeSpan.Zero] do trybu failover do regionu pomocniczego, natychmiast po usługi po pierwsze, nieprzejściowy błąd. Błędy wyzwalające czasomierza trybu failover są [MessagingException] [ MessagingException] w którym [IsTransient] [ IsTransient] właściwość ma wartość false lub [ System.TimeoutException][System.TimeoutException]. Innych wyjątków takich jak [unauthorizedaccessexception —] [ UnauthorizedAccessException] nie powodują przejścia w tryb failover, ponieważ wskazują, że klient jest nieprawidłowo skonfigurowany. A [ServerBusyException] [ ServerBusyException] jest nie Przyczyna trybu failover ponieważ prawidłowy wzorzec polega na poczekaj 10 sekund, Wyślij wiadomość ponownie.
* *enableSyphon*: Wskazuje, że określonego parowanie powinien również syphon wiadomości z pomocniczą przestrzeń nazw do podstawowej przestrzeni nazw. Ogólnie rzecz biorąc, ustaw tę wartość aplikacji, które wysyłają komunikaty **false**; aplikacje, które odbierają komunikaty należy ustawić tę wartość na **true**. Przyczyna to zakłada, że często mniejszej liczby odbiorców wiadomości od nadawcy wiadomości. W zależności od liczby odbiorców można obsługiwać obowiązków syphon wystąpienie pojedynczej aplikacji. Za pomocą wielu odbiorców ma wpływ rozliczeń dla każdej kolejki zaległości.

Aby użyć kodu, należy utworzyć podstawowy [MessagingFactory] [ MessagingFactory] wystąpienia pomocniczy [MessagingFactory] [ MessagingFactory] wystąpienia pomocniczy [ NamespaceManager] [ NamespaceManager] wystąpienia, a [SendAvailabilityPairedNamespaceOptions] [ SendAvailabilityPairedNamespaceOptions] wystąpienia. Wywołanie może być tak proste, jak poniżej:

```csharp
SendAvailabilityPairedNamespaceOptions sendAvailabilityOptions = new SendAvailabilityPairedNamespaceOptions(secondaryNamespaceManager, secondary);
primary.PairNamespaceAsync(sendAvailabilityOptions).Wait();
```

Gdy zadanie zwracane przez [PairNamespaceAsync] [ PairNamespaceAsync] zakończeniu działania metody wszystko zostało skonfigurowane i gotowe do użycia. Przed zwróceniem zadania może nie zostały wykonane wszystkie niezbędne do parowanie działają prawidłowo pracy tła. W rezultacie nie należy zacząć wysyłania wiadomości, dopóki zadanie nie zwróci. W przypadku wystąpienia błędów, takich jak nieprawidłowych poświadczeń lub nie można utworzyć kolejki zaległości, tych wyjątków zostanie zgłoszony, po zakończeniu zadania. Po powrocie z zadania Sprawdź, czy kolejki zostały znalezione lub utworzone przez badanie [BacklogQueueCount] [ BacklogQueueCount] właściwość swoje [SendAvailabilityPairedNamespaceOptions] [ SendAvailabilityPairedNamespaceOptions] wystąpienia. Poprzedzający kod tej operacji wygląda następująco:

```csharp
if (sendAvailabilityOptions.BacklogQueueCount < 1)
{
    // Handle case where no queues were created.
}
```

## <a name="next-steps"></a>Kolejne kroki
Teraz, kiedy znasz już podstawy asynchronicznej obsługi komunikatów w usłudze Service Bus, przeczytaj więcej szczegółowych informacji na temat [sparowane przestrzenie nazw][paired namespaces].

[ServerBusyException]: /dotnet/api/microsoft.servicebus.messaging.serverbusyexception
[System.TimeoutException]: https://msdn.microsoft.com/library/system.timeoutexception.aspx
[MessagingException]: /dotnet/api/microsoft.servicebus.messaging.messagingexception
[Best practices for insulating applications against Service Bus outages and disasters]: service-bus-outages-disasters.md
[Microsoft.ServiceBus.Messaging.MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[MessageReceiver]: /dotnet/api/microsoft.servicebus.messaging.messagereceiver
[QueueClient]: /dotnet/api/microsoft.servicebus.messaging.queueclient
[TopicClient]: /dotnet/api/microsoft.servicebus.messaging.topicclient
[Microsoft.ServiceBus.Messaging.PairedNamespaceOptions]: /dotnet/api/microsoft.servicebus.messaging.pairednamespaceoptions
[MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[SendAvailabilityPairedNamespaceOptions]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions
[NamespaceManager]: /dotnet/api/microsoft.servicebus.namespacemanager
[PairNamespaceAsync]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[EnableSyphon]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions
[System.TimeSpan.Zero]: https://msdn.microsoft.com/library/system.timespan.zero.aspx
[IsTransient]: /dotnet/api/microsoft.servicebus.messaging.messagingexception
[UnauthorizedAccessException]: https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx
[BacklogQueueCount]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions?redirectedfrom=MSDN
[paired namespaces]: service-bus-paired-namespaces.md

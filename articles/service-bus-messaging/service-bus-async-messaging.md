---
title: Asynchroniczne komunikatów usługi Service Bus | Dokumentacja firmy Microsoft
description: Opis usługi Azure Service Bus asynchroniczną obsługę wiadomości.
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: f1435549-e1f2-40cb-a280-64ea07b39fc7
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2018
ms.author: sethm
ms.openlocfilehash: e48e95d99847e68bdb218b341ad2fbcd44eb31f4
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2018
ms.locfileid: "28929990"
---
# <a name="asynchronous-messaging-patterns-and-high-availability"></a>Wzorce asynchronicznej obsługi komunikatów i wysoka dostępność

Asynchroniczne wiadomości może być wdrożonych w różnych sposobów. Z kolejki, tematy i subskrypcje usługi Azure Service Bus obsługuje asynchronism za pośrednictwem sklepu i mechanizmu przekazywania. W normalnych warunkach (synchroniczne) wysyłanie komunikatów do kolejki i tematy i odbieranie komunikatów z subskrypcji i kolejek. Aplikacje, które należy zapisać zależą od tych jednostek zawsze będzie dostępna. Po zmianie kondycji jednostki z powodu różnych okolicznościach, należy sposobem zapewnienia jednostki ograniczoną możliwość, która spełnia wymagania większości.

Aplikacje zazwyczaj umożliwiają asynchroniczne wzorce obsługi komunikatów kilka scenariuszy komunikacji. Można tworzyć aplikacje, w których klienci mogą wysyłać wiadomości do usług, nawet wtedy, gdy usługa nie jest uruchomiona. Dla aplikacji środowiska seria komunikacji za pośrednictwem kolejki może pomóc poziomu obciążenia przez udostępniania miejsca do obsługi komunikacji buforu. Ponadto można uzyskać modułu równoważenia obciążenia prostą, ale efektywną dystrybucję wiadomości między wieloma maszynami.

Aby zapewnić dostępność dowolnej z tych obiektów, należy wziąć pod uwagę kilka różnych sposobów tych jednostek może pojawiania się niedostępne dla trwałego systemu obsługi wiadomości. Ogólnie rzecz biorąc widzimy jednostki stają się niedostępne dla aplikacji, które firma Microsoft zapisu na następujące sposoby:

* Nie można wysłać wiadomości.
* Nie można odebrać wiadomości.
* Nie można zarządzać jednostek (Tworzenie, pobieranie, aktualizować lub usuwać jednostek).
* Nie można skontaktować się z usługą.

Dla każdego z tych błędów niepowodzenie różne tryby istnieje umożliwiające aplikacji do wykonywania pracy na określonym poziomie zmniejszenie możliwości w dalszym ciągu. Na przykład system, który może wysyłać wiadomości, ale nie otrzymały ich może nadal otrzymywać zamówień klientów, ale nie można przetworzyć zamówień. W tym temacie omówiono potencjalne problemy, które mogą wystąpić, oraz jak te problemy zostaną skorygowane. Usługa Service Bus ma wprowadzono szereg środki zaradcze, które należy wybrać w, i w tym temacie omówiono również zasady stosowania tych opcjonalnych środki zaradcze.

## <a name="reliability-in-service-bus"></a>Niezawodność w usłudze Service Bus
Istnieje kilka sposobów do obsługi wiadomości i jednostek problemy, a istnieją wskazówki dotyczące odpowiedniego stosowania tych środki zaradcze. Aby zapoznać się z wytycznymi, należy najpierw zrozumieć, co może zakończyć się niepowodzeniem w magistrali usługi. Z powodu projektu systemu Azure wszystkie te problemy zwykle być krótkotrwały. Na wysokim poziomie różne przyczyny niedostępności wyglądają następująco:

* Ograniczanie z systemu zewnętrznego, od którego zależy usługi Service Bus. Ograniczanie występuje z interakcji z zasobami magazynu i zasobów obliczeniowych.
* Problem dotyczący systemu, od którego zależy usługi Service Bus. Na przykład określona część magazynu mogą wystąpić problemy.
* Błąd usługi Service Bus w podsystemie pojedynczego. W takiej sytuacji węźle obliczeń można uzyskać w stanie niespójnym i ponownie, powodując wszystkich jednostek, który służy do zrównoważeniu do innych węzłów. To z kolei może spowodować krótkim przetwarzania komunikatów powolne.
* Błąd usługi Service Bus w obrębie centrum danych Azure. Jest to "poważnej awarii" w którym system jest nieosiągalny dla wielu minut lub kilka godzin.

> [!NOTE]
> Termin **magazynu** może oznaczać magazynu Azure i SQL Azure.
> 
> 

Usługa Service Bus zawiera szereg metody ich rozwiązywania tych problemów. W poniższych sekcjach omówiono poszczególnych problemów i ich odpowiednie środki zaradcze.

### <a name="throttling"></a>Ograniczanie przepływności
Z usługą Service Bus ograniczania umożliwia zarządzania szybkość współpracy wiadomości. Każdy węzeł poszczególne usługi Service Bus przechowuje wiele jednostek. Każdy z tych jednostek nawiązuje wymagań systemu pod względem Procesora, pamięci, magazynu i inne aspekty. Po wykryciu żadnego z tych aspektów użycia przekraczający zdefiniowanych progów, usługi Service Bus można odmówić danego żądania. Obiekt wywołujący uzyskuje [ServerBusyException] [ ServerBusyException] i ponownych prób po 10 sekundach.

Zmniejszanie kod musi błąd odczytu i zatrzymanie wszelkich ponownych prób wiadomości dla co najmniej 10 sekund. Ponieważ ten błąd może się zdarzyć w części aplikacji klienta oczekuje się, że każda niezależnie wykonuje logiki ponawiania próby. Kod może zmniejszyć prawdopodobieństwo ograniczane przez włączenie partycjonowania na kolejka lub temat.

### <a name="issue-for-an-azure-dependency"></a>Problem dotyczący usługi Azure zależności
Inne składniki w obrębie platformy Azure od czasu do czasu może mieć problemy z usługi. Na przykład po uaktualnieniu systemu, w którym usługa Service Bus używa tego systemu tymczasowo może wystąpić zmniejszenie możliwości. W celu obejścia tego rodzaju problemy, usługi Service Bus regularnie sprawdza i implementuje środki zaradcze. Efekty uboczne te czynniki są wyświetlane. Na przykład do obsługi przejściowych problemów z magazynem, usługi Service Bus implementuje systemu, który umożliwia działać spójnie operacji wysyłania wiadomości. Ze względu na specyfikę środki zaradcze wiadomość może potrwać do 15 minut do znajdują się w odpowiednich kolejki lub subskrypcji i gotowość operacji odbioru. Ogólnie rzecz biorąc większość jednostek nie wystąpi ten problem. Jednak podana liczba jednostek w usłudze Service Bus na platformie Azure, to ograniczenie jest czasami potrzebne mały podzbiór klientów usługi Service Bus.

### <a name="service-bus-failure-on-a-single-subsystem"></a>Błąd usługi Service Bus w podsystemie pojedynczego
Z dowolnej aplikacji okolicznościach może spowodować wewnętrznych składników usługi Service Bus niespójność. Gdy Usługa Service Bus wykrywa, zbiera dane z aplikacji, aby pomóc w zdiagnozowaniu, co się stało. Gdy dane są zbierane, aplikacji jest uruchamiany ponownie, aby przywrócić go do stanu spójności. Ten proces odbywa się stosunkowo szybko i wyniki w jednostce pojawiające się jako niedostępny dla maksymalnie za kilka minut, chociaż typowe dół czasy są znacznie krótszy.

W takich przypadkach generuje aplikacja kliencka [System.TimeoutException] [ System.TimeoutException] lub [MessagingException] [ MessagingException] wyjątku. Usługa Service Bus zawiera ograniczenia tego problemu w formularzu logiki ponawiania próby automatycznego klienta. Po wyczerpaniu okres ponawiania i wiadomość nie zostanie dostarczona, można sprawdzić przy użyciu innych funkcji, takich jak [łączyć przestrzenie nazw][paired namespaces]. Przestrzenie nazw sparowanego mają inne uwagi, które zostały omówione w tym artykule.

### <a name="failure-of-service-bus-within-an-azure-datacenter"></a>Błąd usługi Service Bus w obrębie centrum danych Azure
Najbardziej prawdopodobne przyczyny awarii w centrum danych Azure jest niepowodzenia uaktualniania wdrożenia usługi Service Bus lub zależnego systemu. Miarę dojrzewania platformy, zmniejszył prawdopodobieństwo wystąpienia tego typu błędu. Błąd centrum danych również może się zdarzyć przyczyn, które obejmują następujące czynności:

* Awaria elektrycznego (zasilacz i generowania zasilania znikają).
* Łączność (internet podziału między klientami a Azure).

W obu przypadkach awarii fizycznej lub włókna spowodował problem. Aby obejść ten problem i upewnij się, że nadal może wysyłać wiadomości, można użyć [łączyć przestrzenie nazw] [ paired namespaces] umożliwiające komunikatów do wysłania w innej lokalizacji, podczas gdy lokalizacji głównej jest dobra ponownie. Aby uzyskać więcej informacji, zobacz [najlepszych rozwiązań dotyczących izolacji aplikacji przed awariami usługi Service Bus i awarii][Best practices for insulating applications against Service Bus outages and disasters].

## <a name="paired-namespaces"></a>Sparowane przestrzenie nazw
[Łączyć przestrzenie nazw] [ paired namespaces] funkcji obsługuje scenariusze, w którym jednostki magistrali usług lub wdrożenia w obrębie centrum danych staną się niedostępne. Gdy to zdarzenie występuje rzadko, systemów rozproszonych nadal muszą być przygotowane do obsługi najgorszy scenariuszy przypadków. Zazwyczaj zdarzenie niektórych elementu, od którego zależy usługi Service Bus występują krótkoterminowej problem. Aby zachować dostępność aplikacji podczas wystąpienia awarii, usługi Service Bus umożliwia dwóch osobnych obszarów nazw, najlepiej w centrach danych, do obsługi ich jednostek obsługi komunikatów. W pozostałej części tej sekcji korzysta z następującą terminologią:

* Głównej przestrzeni nazw: przestrzeń nazw, z którym aplikacji współdziała do wysyłania i odbierania operacji.
* Dodatkowej przestrzeni nazw: przestrzeni nazw, która pełni rolę kopii zapasowej do głównej przestrzeni nazw. Logiki aplikacji nie wchodzi w interakcję z tą przestrzenią nazw.
* Interwał trybu failover: ilość czasu, aby zaakceptować normalne błędów aplikacji zmienia się z podstawowej przestrzeni nazw do dodatkowej przestrzeni nazw.

Łączyć przestrzenie nazw obsługują *wysyłania dostępności*. Wysłanie dostępności zachowuje możliwość wysyłania wiadomości. Aby użyć dostępności wysyłania, aplikacja musi spełniać następujące wymagania:

1. Komunikaty są odbierane tylko z głównej przestrzeni nazw.
2. Komunikaty wysyłane do danej kolejki lub tematu może odbierane poza kolejnością.
3. W ramach sesji może być momencie nadejścia nowych wiadomości poza kolejnością. To jest podział z normalną funkcjonalność sesji. Oznacza to, że aplikacja korzysta z sesji do logicznego grupowania wiadomości.
4. Stan sesji jest utrzymywany tylko w głównej przestrzeni nazw.
5. Kolejki głównej można przejdzie w tryb online i Rozpocznij akceptowanie komunikatów, zanim kolejki dodatkowej dostarcza wszystkich wiadomości do kolejki głównej.

W poniższych sekcjach omówiono interfejsy API, jak są implementowane za pośrednictwem interfejsów API i zawiera przykładowy kod, który używa funkcji. Należy pamiętać, że rozliczeń wpływ na skojarzone z tą funkcją.

### <a name="the-messagingfactorypairnamespaceasync-api"></a>MessagingFactory.PairNamespaceAsync interfejsu API
Funkcja par nazw obejmuje [PairNamespaceAsync] [ PairNamespaceAsync] metoda [Microsoft.ServiceBus.Messaging.MessagingFactory] [ Microsoft.ServiceBus.Messaging.MessagingFactory] klasy:

```csharp
public Task PairNamespaceAsync(PairedNamespaceOptions options);
```

Po ukończeniu zadania parowanie przestrzeni nazw jest również gotowy działanie dla każdego [MessageReceiver][MessageReceiver], [QueueClient] [ QueueClient] , lub [TopicClient] [ TopicClient] utworzone za pomocą [MessagingFactory] [ MessagingFactory] wystąpienia. [Microsoft.ServiceBus.Messaging.PairedNamespaceOptions] [ Microsoft.ServiceBus.Messaging.PairedNamespaceOptions] jest klasą bazową dla różnych typów parowania, które są dostępne z [MessagingFactory] [ MessagingFactory] obiektu. Tylko klasy pochodnej jest obecnie jedną o nazwie [SendAvailabilityPairedNamespaceOptions][SendAvailabilityPairedNamespaceOptions], który implementuje wymagania dostępności wysyłania. [SendAvailabilityPairedNamespaceOptions] [ SendAvailabilityPairedNamespaceOptions] ma zestaw konstruktorów kompilacji na siebie. Patrzeć konstruktora z parametrami większości, można zrozumieć działanie innych konstruktorów.

```csharp
public SendAvailabilityPairedNamespaceOptions(
    NamespaceManager secondaryNamespaceManager,
    MessagingFactory messagingFactory,
    int backlogQueueCount,
    TimeSpan failoverInterval,
    bool enableSyphon)
```

Parametry te mają następujące znaczenie:

* *secondaryNamespaceManager*: zainicjowana [NamespaceManager] [ NamespaceManager] wystąpienia dla dodatkowej przestrzeni nazw, która [PairNamespaceAsync] [ PairNamespaceAsync] metody umożliwiają konfigurowanie dodatkowej przestrzeni nazw. Menedżer przestrzeni nazw jest używana do uzyskania listę kolejek w przestrzeni nazw i upewnij się, że istnieje wymagana zaległości kolejki. Jeśli te kolejki nie istnieją, są tworzone. [NamespaceManager] [ NamespaceManager] wymaga utworzenia tokenu z możliwości **Zarządzaj** oświadczeń.
* *messagingFactory*: [MessagingFactory] [ MessagingFactory] wystąpienia dodatkowej przestrzeni nazw. [MessagingFactory] [ MessagingFactory] obiekt jest używany do wysyłania i, jeżeli [EnableSyphon] [ EnableSyphon] właściwość jest ustawiona na **true**, odbierania wiadomości z kolejki zaległości.
* *backlogQueueCount*: liczba zaległości kolejki, aby utworzyć. Ta wartość musi wynosić co najmniej 1. Podczas wysyłania komunikatów do zaległości, jeden z tych kolejek jest wybierany losowo. Jeśli wartość jest ustawiona na 1, następnie tylko jedna kolejka kiedykolwiek można. Jeśli kolejki zaległości co generuje błędy, klient nie jest w stanie wypróbować inną zaległości kolejki i może zakończyć się niepowodzeniem do wysłania wiadomości. Zalecamy ustawienie tej wartości na niektórych większą wartość i domyślne wartości do 10. Można to zmienić wyższe lub niższe wartości w zależności od ilości danych aplikacji wysyła na dzień. Każdej kolejki zaległości może przechowywać maksymalnie 5 GB wiadomości.
* *failoverInterval*: ilość czasu, w którym będzie akceptować błędów w głównej przestrzeni nazw przed przełączeniem żadnych pojedynczej jednostki za pośrednictwem do dodatkowej przestrzeni nazw. Tryb failover odbywa się na zasadzie jednostki przez jednostki. Jednostki w jednej przestrzeni nazw, często na żywo w różnych węzłach w ramach usługi Service Bus. Błąd w jednej jednostce nie oznacza awarii w innym. Tę wartość można ustawić, [System.TimeSpan.Zero] [ System.TimeSpan.Zero] do trybu failover na serwerze pomocniczym, natychmiast po awarii z pierwszą, nieprzejściowego. Błędy, które wyzwalają czasomierza trybu failover są [MessagingException] [ MessagingException] w którym [IsTransient] [ IsTransient] właściwość ma wartość false lub [ System.TimeoutException][System.TimeoutException]. Pozostałe wyjątki, takich jak [unauthorizedaccessexception —] [ UnauthorizedAccessException] nie powodują trybu failover, ponieważ wskazują, że klient jest niepoprawnie skonfigurowany. A [ServerBusyException] [ ServerBusyException] jest nie trybu failover Przyczyna ponieważ poprawne wzorzec to Zaczekaj 10 sekund, następnie ponownie wysłać wiadomość.
* *enableSyphon*: wskazuje, że tego konkretnego parowanie powinny również syphon wiadomości od dodatkowej przestrzeni nazw do głównej przestrzeni nazw. Ogólnie rzecz biorąc, ustaw tę wartość na aplikacji, które wysyłają komunikaty **false**; aplikacji, który odbiera komunikaty należy ustawić wartość **true**. Przyczyną to często, Brak mniej odbiorcy wiadomości od nadawcy wiadomości. W zależności od liczby odbiorników można obsłużyć obowiązków syphon wystąpienie pojedynczej aplikacji. Przy użyciu wiele odbiorników implikacje rozliczeń dla każdej kolejki zaległości.

Aby użyć kodu, należy utworzyć podstawowy [MessagingFactory] [ MessagingFactory] wystąpienie dodatkowej [MessagingFactory] [ MessagingFactory] wystąpienie dodatkowej [ NamespaceManager] [ NamespaceManager] wystąpienia, a [SendAvailabilityPairedNamespaceOptions] [ SendAvailabilityPairedNamespaceOptions] wystąpienia. Wywołanie może być prosty jak następujące:

```csharp
SendAvailabilityPairedNamespaceOptions sendAvailabilityOptions = new SendAvailabilityPairedNamespaceOptions(secondaryNamespaceManager, secondary);
primary.PairNamespaceAsync(sendAvailabilityOptions).Wait();
```

Gdy zadanie zwracane przez [PairNamespaceAsync] [ PairNamespaceAsync] ukończeniu metody, wszystko zostało skonfigurowane i gotowe do użycia. Przed zwróceniem zadanie może nie została ukończona wszystkie niezbędne do parowanie działają prawidłowo Praca w tle. W związku z tym nie należy uruchamiać wysyłania wiadomości, dopóki nie zwraca zadania. W przypadku wystąpienia błędów, takich jak nieprawidłowych poświadczeń lub nie można utworzyć kolejki zaległości te wyjątki będą zgłaszane po zakończeniu zadania. Po zwraca zadanie, sprawdź, czy kolejki zostały znalezione lub utworzone przez sprawdzenie [BacklogQueueCount] [ BacklogQueueCount] właściwości na Twojej [SendAvailabilityPairedNamespaceOptions] [ SendAvailabilityPairedNamespaceOptions] wystąpienia. Poprzedni kod tej operacji wygląda następująco:

```csharp
if (sendAvailabilityOptions.BacklogQueueCount < 1)
{
    // Handle case where no queues were created.
}
```

## <a name="next-steps"></a>Kolejne kroki
Teraz, kiedy znasz już podstawy asynchroniczną obsługę wiadomości w usłudze Service Bus, przeczytaj więcej szczegółów na temat [łączyć przestrzenie nazw][paired namespaces].

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
[PairNamespaceAsync]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_PairNamespaceAsync_Microsoft_ServiceBus_Messaging_PairedNamespaceOptions_
[EnableSyphon]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions#Microsoft_ServiceBus_Messaging_SendAvailabilityPairedNamespaceOptions_EnableSyphon
[System.TimeSpan.Zero]: https://msdn.microsoft.com/library/system.timespan.zero.aspx
[IsTransient]: /dotnet/api/microsoft.servicebus.messaging.messagingexception#Microsoft_ServiceBus_Messaging_MessagingException_IsTransient
[UnauthorizedAccessException]: https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx
[BacklogQueueCount]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions?redirectedfrom=MSDN#Microsoft_ServiceBus_Messaging_SendAvailabilityPairedNamespaceOptions_BacklogQueueCount
[paired namespaces]: service-bus-paired-namespaces.md

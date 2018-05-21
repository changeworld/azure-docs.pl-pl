---
title: Omówienie Reliable Actors sieci szkieletowej usług | Dokumentacja firmy Microsoft
description: Wprowadzenie do modelu programowania Reliable Actors sieci szkieletowej usług.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: 7fdad07f-f2d6-4c74-804d-e0d56131f060
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/01/2017
ms.author: vturecek
ms.openlocfilehash: d0de7e7596fb7fabbc4061709816111a7a4ad827
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="introduction-to-service-fabric-reliable-actors"></a>Wprowadzenie do elementów Reliable Actors usługi Service Fabric
Reliable Actors to platforma aplikacji sieci szkieletowej usług na podstawie [aktora wirtualnego](http://research.microsoft.com/en-us/projects/orleans/) wzorca. Niezawodnej interfejs API złośliwych użytkowników zapewnia model programowania jednowątkowe oparty na skalowalność i niezawodność gwarancje udostępniane przez usługi sieć szkieletowa usług.

## <a name="what-are-actors"></a>Co to są złośliwych użytkowników?
Aktor jest jednostką izolowanym, niezależnie od zasobów obliczeniowych i stanu z jednowątkowego wykonywania. [Wzorca aktora](https://en.wikipedia.org/wiki/Actor_model) to model obliczeniową dla systemów równoczesnych lub rozproszone, w którym dużej liczby uczestników te mogą być wykonywane jednocześnie i niezależnie od siebie nawzajem. Złośliwych użytkowników może komunikować się ze sobą i mogą utworzyć więcej złośliwych użytkowników.

### <a name="when-to-use-reliable-actors"></a>Kiedy należy używać Reliable Actors
Usługa sieci szkieletowej Reliable Actors jest implementacją wzorca projektowego aktora. Podobnie jak w przypadku dowolnego wzorcu projektowym oprogramowania decyzja, czy ma być używany z określonym wzorcem jest podjęcie opartej na czy oprogramowania projektowania problem pasuje do wzorca.

Mimo że wzorzec projektowania aktora można bardzo zmieści się do liczby systemów rozproszonych problemy i scenariusze zachować ostrożność, że należy uwzględnić ograniczenia wzorca i framework implementacji. Stanowią ogólne wskazówki należy wziąć pod uwagę wzorca aktora do modelowania scenariuszu lub problem, jeśli:

* Obszar problem obejmuje dużą liczbą (tysięcy lub więcej) jednostek mały, niezależne i izolowane stanu i logiki.
* Chcesz korzystać z jednowątkowego obiektów, które nie wymagają znaczących interakcji z zewnętrznego składników, w tym badania stanu zestawu podmiotów.
* Swoich wystąpień aktora nie zablokuje wywołań nieprzewidywalne opóźnień wysyłając operacji We/Wy.

## <a name="actors-in-service-fabric"></a>Złośliwych użytkowników w sieci szkieletowej usług
W sieci szkieletowej usług, podmioty są realizowane w ramach Reliable Actors: struktura aplikacji na podstawie wzorca aktora wbudowane nad [niezawodne usługi sieć szkieletowa usług](service-fabric-reliable-services-introduction.md). Każdej usługi niezawodnego aktora, jaki jest rzeczywiście podzielonym na partycje, stanowe niezawodnej usługi.

Każdy aktora jest zdefiniowany jako wystąpienie typu aktora identyczny sposób obiektu .NET. wystąpienia typu .NET. Na przykład może to być typ aktora, która implementuje funkcje Kalkulatora i może istnieć wiele podmiotów tego typu rozproszonych w różnych węzłach w klastrze. Każdy taki aktora jest unikatowo identyfikowana przez identyfikator aktora.

## <a name="actor-lifetime"></a>Okres istnienia aktora
Sieć szkieletowa usług podmiotów są wirtualny, co oznacza, że ich istnienia nie jest związana z ich reprezentacji w pamięci. W związku z tym nie muszą jawnie utworzone lub zniszczenia. Środowisko uruchomieniowe Reliable Actors automatycznie aktywuje czas aktora pierwszy odbierze żądanie dla danego identyfikatora aktora. Jeśli aktora nie jest używany w danym okresie czasu, środowisko uruchomieniowe Reliable Actors pamięci zbiera obiektu w pamięci. Również zachowa wiedzy istnienia aktora powinien trzeba będzie ponownie aktywować później. Aby uzyskać więcej informacji, zobacz [aktora cykl życia i odzyskiwanie pamięci](service-fabric-reliable-actors-lifecycle.md).

Ta warstwa abstrakcji okres istnienia aktora wirtualnego zawiera niektóre zastrzeżenia wyniku modelu aktora wirtualnego, a w rzeczywistości implementacji Reliable Actors w czasie odbiega od tego modelu.

* Aktor jest uaktywniany automatycznie (co obiekt aktora można skonstruować) po raz pierwszy jest wysyłany komunikat, jego identyfikator aktora. Po pewnym opóźnieniem od czasu obiekt aktora jest bezużytecznych. W przyszłości za pomocą Identyfikatora aktora ponownie, powoduje, że można utworzyć nowy obiekt aktora. Okres istnienia obiektu outlives stanu aktora, gdy przechowywana w Menedżer stanów.
* Wywołaniem jakiejkolwiek jego metody aktora o identyfikatorze aktora aktywuje tego aktora. Z tego powodu aktora typy mają ich Konstruktor wywoływany niejawnie przez środowisko uruchomieniowe. W związku z tym kod klienta nie można przekazywać parametrów do konstruktora typu aktora, mimo że parametry mogą być przekazany do konstruktora aktora przez samą usługę. Wynik jest, że złośliwych użytkowników, mogą być zbudowane w stanie częściowo zainicjowany w czasie, inne metody są wywoływać w nim, jeśli aktora wymaga parametrów inicjowania od klienta. Brak punktu wejścia jednym aktywacji aktora z klienta.
* Mimo że Reliable Actors niejawnie Tworzenie obiektów aktora; masz możliwość usunięcia jawnie aktora i jego stan.

## <a name="distribution-and-failover"></a>Dystrybucji i trybu failover
Zapewnienie skalowalność i niezawodność sieci szkieletowej usług dystrybuuje podmioty w klastrze i automatycznie migruje je z węzłów do dobrej kondycji a w razie potrzeby. Jest to Abstrakcja za pośrednictwem [podzielonym na partycje, stanowe niezawodnej usługi](service-fabric-concepts-partitioning.md). Dystrybucji, skalowalności, niezawodności i automatycznej pracy awaryjnej wszystkie są dostarczane ze względu na fakt, że podmiotów działają wewnątrz stanowe niezawodnej usługi o nazwie *usługi aktora*.

Podmioty są rozproszone na partycje usługi aktora, a te partycje są dystrybuowane między węzłami w klastrze usługi sieć szkieletowa usług. Każda partycja usługi zawiera zestaw złośliwych użytkowników. Usługa Service Fabric zarządza dystrybucji i pracy awaryjnej partycji usługi.

Na przykład usługi aktora z partycjami dziewięć wdrożone do trzech węzłów przy użyciu domyślnego położenia partycji aktora będą dystrybuowane thusly:

![Niezawodne dystrybucji złośliwych użytkowników][2]

Framework aktora zarządza ustawieniami partycji schemat i klawisz zakresu dla Ciebie. Upraszcza niektóre opcje, ale również zajmuje kilka brany pod uwagę:

* Niezawodne usługi umożliwia wybierz schemat partycjonowania, klucza zakresu (w przypadku używania zakres schemat partycjonowania), a liczba partycji. Reliable Actors jest ograniczony do zakresu (uniform schematu Int64) schemat partycjonowania i wymaga, aby użyć pełnego zakresu klucza Int64.
* Domyślnie podmiotów losowo są umieszczane w partycji co uniform dystrybucji.
* Ponieważ podmiotów losowo znajdują się, powinien oczekiwano, że operacje aktora zawsze wymagają komunikacji w sieci, w tym serializacji i deserializacji obiektu danych wywołania metody, ponoszenia opóźnienia i koszty.
* W zaawansowanych scenariuszach istnieje możliwość sterowania aktora partycji umieszczania przy użyciu aktora Int64 identyfikatorów, które mapują na określonej partycji. Jednak to tak może spowodować niezrównoważonej dystrybucji podmiotów na partycje.

Więcej informacji dotyczących sposobu usługi aktora są podzielone na partycje, można znaleźć w [partycjonowania pojęcia osób](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors).

## <a name="actor-communication"></a>Komunikacja aktora
Interakcje aktora są definiowane w interfejs, który jest współużytkowany przez aktora, która implementuje interfejs i klienta, który pobiera serwer proxy, aby aktora za pomocą tego samego interfejsu. Ponieważ ten interfejs jest używane do asynchronicznego wywołania metody aktora, co metoda w interfejsie musi być zwracanie zadań.

Wywołań metod i ich odpowiedzi ostatecznie spowodować żądania sieci w klastrze, więc argumenty i typy wyników zadań, które zwracają muszą podlegać serializacji przez platformę. W szczególności muszą być [serializacji kontraktu danych](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

### <a name="the-actor-proxy"></a>Serwer proxy aktora
Interfejs API klienta Reliable Actors zapewnia komunikację między wystąpieniem aktora i klienta aktora. Do komunikacji z aktorem, klient tworzy aktora obiekt serwera proxy, który implementuje interfejs aktora. Klient wchodzi w interakcję z aktorem przez wywołanie metody dla obiekt serwera proxy. Serwer proxy aktora może służyć do komunikacji klient aktora i aktora aktora.

```csharp
// Create a randomly distributed actor ID
ActorId actorId = ActorId.CreateRandom();

// This only creates a proxy object, it does not activate an actor or invoke any methods yet.
IMyActor myActor = ActorProxy.Create<IMyActor>(actorId, new Uri("fabric:/MyApp/MyActorService"));

// This will invoke a method on the actor. If an actor with the given ID does not exist, it will be activated by this method call.
await myActor.DoWorkAsync();
```

```java
// Create actor ID with some name
ActorId actorId = new ActorId("Actor1");

// This only creates a proxy object, it does not activate an actor or invoke any methods yet.
MyActor myActor = ActorProxyBase.create(actorId, new URI("fabric:/MyApp/MyActorService"), MyActor.class);

// This will invoke a method on the actor. If an actor with the given ID does not exist, it will be activated by this method call.
myActor.DoWorkAsync().get();
```


Należy zauważyć, że dwa rodzaje informacji używanych do tworzenia obiektu proxy aktora identyfikator aktora i nazwę aplikacji. Identyfikator aktora unikatowo identyfikuje aktora, podczas gdy nazwa aplikacji identyfikuje [aplikacji usługi Service Fabric](service-fabric-reliable-actors-platform.md#application-model) wdrożonym aktora.

`ActorProxy`(C#) / `ActorProxyBase`klasy (Java) po stronie klienta przeprowadza rozpoznawanie niezbędne do lokalizowania aktora według Identyfikatora i otwórz kanał komunikacji z nim. Również ponowną do zlokalizowania aktora w przypadku niepowodzenia komunikacji i tryb failover. W związku z tym dostarczanie komunikatów o następujących cechach:

* Dostarczanie komunikatów to optymalne rozwiązanie.
* Złośliwych użytkowników może pojawić się zduplikowane wiadomości z tego samego klienta.

## <a name="concurrency"></a>Współbieżność
Środowisko uruchomieniowe Reliable Actors udostępnia model na podstawie Włączanie dostępu za pomocą prostego do uzyskiwania dostępu do metody aktora. Oznacza to, że nie więcej niż jeden wątek może być aktywny wewnątrz kodu obiektu aktora w dowolnym momencie. Włącz dostęp przez Internet jest znacznie ułatwione równoczesnych systemów jako nie istnieje potrzeba mechanizmów synchronizacji dla dostępu do danych. Ponadto oznacza to, że systemy musi zostać zaprojektowana z uwagi dotyczące charakteru jednowątkowe dostęp do każdego wystąpienia aktora.

* Aktora pojedynczego wystąpienia nie może przetworzyć więcej niż jedno żądanie naraz. Wystąpienie aktora może spowodować wąskie gardło przepływności, jeżeli oczekuje się do obsługi żądań.
* Złośliwych użytkowników można zakleszczenie od siebie wzajemnie, jeśli żądanie cykliczne między dwoma uczestnikami podczas zewnętrznych żądań na jeden z uczestników jednocześnie. Środowisko uruchomieniowe aktora automatycznie limitu czasu dla wywołań aktora i Zgłoś wyjątek, aby obiekt wywołujący, aby przerwać sytuacji możliwe zakleszczenie.

![Niezawodna komunikacja złośliwych użytkowników][3]

### <a name="turn-based-access"></a>Włącz dostęp przez Internet
Włącz składa się z pełną wykonanie metody aktora w odpowiedzi na żądanie z innych uczestników lub klientów lub zakończenie wykonywania [czasomierza/monitu](service-fabric-reliable-actors-timers-reminders.md) wywołania zwrotnego. Mimo że te metody i wywołania zwrotne są asynchroniczne, środowisko uruchomieniowe złośliwych użytkowników nie interleave je. Włącz muszą być całkowicie przed Włącz nowe jest dozwolone. Innymi słowy aktora metody lub czasomierza/monitu wywołania zwrotnego, które jest aktualnie wykonywany musi być w pełni ukończone przed nowe wywołanie do metody lub wywołania zwrotnego jest dozwolone. Metoda lub wywołania zwrotnego jest uważana za zostało ukończone, jeśli wykonanie zwrócił z metody lub wywołania zwrotnego i Zadanie zwrócone przez metodę lub wywołanie zwrotne zostało zakończone. Warto akcentowania się, że ten współbieżności opartej na włączanie jest przestrzegana nawet między różnymi metodami, czasomierze i wywołania zwrotne.

Środowisko uruchomieniowe złośliwych użytkowników wymusza współbieżności opartej na ruch przez uzyskiwanie blokady dla aktora na początku Włącz i zwolnienie blokady na końcu Włącz. W związku z tym współbieżności opartej na włączanie są wymuszane na podstawie na aktora, a nie przez złośliwych użytkowników. Metody aktora i wywołania zwrotne czasomierza/monitu mogą być wykonywane jednocześnie imieniu uczestników.

Poniższy przykład przedstawia pojęcia powyżej. Należy wziąć pod uwagę aktora typu, który implementuje dwóch metod asynchronicznych (powiedzieć *metoda1* i *metoda2*), a czasomierza oraz monitu. Na poniższym diagramie przedstawiono oś czasu wykonywania tych metod i wywołania zwrotne w imieniu dwóch uczestników (*ActorId1* i *ActorId2*) należących do tego typu aktora.

![Niezawodne podmiotów środowiska uruchomieniowego Włącz współbieżności opartej i dostępem][1]

Ten diagram jest zgodna z konwencjami te:

* Każdy pionowym wierszem przedstawia przepływ logiczny wykonania metody lub wywołania zwrotnego w imieniu aktora określonego.
* Występowania zdarzeń oznaczone w każdym wierszu pionowy w kolejności chronologicznej z nowszych zdarzeń występujących poniżej starsze.
* Różne kolory dla osi czasu odpowiadający uczestników.
* Wyróżnianie służy do wskazywania czas trwania, dla którego blokady dla aktora jest przechowywany w imieniu metody lub wywołania zwrotnego.

Niektóre ważne kwestie do rozważenia:

* Gdy *metoda1* wykonuje zastępczy *ActorId2* w odpowiedzi na żądanie klienta *xyz789*, inne żądanie klienta (*abc123*) dociera, który wymaga także *metoda1* ma być wykonane przez *ActorId2*. Jednak wykonanie drugiej *metoda1* nie zaczyna się przed zakończeniem wykonywania wcześniejszych. Podobnie, przypomnienia zarejestrowany przez *ActorId2* uruchamiany podczas *metoda1* jest wykonywana w odpowiedzi na żądanie klienta *xyz789*. Wywołanie zwrotne monitu jest wykonywany tylko po obu wykonań *metoda1* są spełnione. Wszystko to jest ze względu na podstawie Włącz współbieżności wymuszany dla *ActorId2*.
* Podobnie, współbieżności opartej na ruch jest również wymuszane dla *ActorId1*, jak to pokazano przez wykonanie *metoda1*, *metoda2*i wywołanie zwrotne czasomierza imieniu  *ActorId1* wykonywane w sposób szeregowego.
* Wykonywanie *metoda1* zastępczy *ActorId1* pokrywa się z jej wykonanie w imieniu *ActorId2*. Jest to spowodowane współbieżności opartej na włączanie są wymuszane tylko w obrębie aktora, a nie przez złośliwych użytkowników.
* W niektórych wykonania metody/wywołania zwrotnego `Task`(C#) / `CompletableFuture`(Java) zwracane przez zakończenie metody/wywołania zwrotnego po metoda zwraca. W niektórych innych operacji asynchronicznej zostało już zakończone w czasie, zwraca wartość metody/wywołania zwrotnego. W obu przypadkach blokady dla aktora jest zwalniany dopiero po zarówno zwraca metodę/wywołania zwrotnego i zakończy operację asynchroniczną.

### <a name="reentrancy"></a>Ponowne wejścia
Środowisko uruchomieniowe złośliwych użytkowników umożliwia ponowne wejście domyślnie. Oznacza to, że jeśli metoda aktora *aktora A* wywołuje metodę dla *B aktora*, który z kolei wywołuje innej metody w *aktora A*, że metoda może działać. Jest tak, ponieważ jest częścią tego samego kontekstu logicznej łańcuch wywołań. Wszystkie wywołania zegara i monitu rozpoczynać nowy kontekst wywołania logiczne. Zobacz [wielobieżność Reliable Actors](service-fabric-reliable-actors-reentrancy.md) więcej szczegółów.

### <a name="scope-of-concurrency-guarantees"></a>Zakres gwarancji współbieżności
Środowisko uruchomieniowe złośliwych użytkowników zapewnia te gwarancje współbieżność w sytuacjach, w którym kontroluje wywołania tych metod. Na przykład zapewnia te gwarancje dla wywołań metod, które są wykonywane w odpowiedzi na żądanie klienta, a także wywołania zwrotne czasomierza i przypomnienia. Jeśli jednak kodu aktora bezpośrednio wywołuje tych metod poza mechanizmów dostarczonym w czasie wykonywania uczestników, następnie środowiska uruchomieniowego nie zawiera żadnej gwarancji współbieżności. Na przykład jeśli metoda jest wywoływana w kontekście niektórych zadań, która nie jest skojarzona z zadaniem zwracane przez metody aktora, następnie środowisko uruchomieniowe nie może zagwarantować współbieżności. Jeśli metoda jest wywoływana z wątku, który tworzy aktora samodzielnie, następnie środowiska uruchomieniowego także nie może zagwarantować współbieżności. W związku z tym, aby wykonać operacje w tle, złośliwych użytkowników należy używać [aktora czasomierze i przypomnieniami aktora](service-fabric-reliable-actors-timers-reminders.md) który przestrzegać współbieżności opartej na ruch.

## <a name="next-steps"></a>Kolejne kroki
Rozpoczynanie pracy przez utworzenie pierwszej usługi Reliable Actors:
   * [Wprowadzenie do korzystania z Reliable Actors na platformie .NET](service-fabric-reliable-actors-get-started.md)
   * [Wprowadzenie do korzystania z Reliable Actors na języku Java](service-fabric-reliable-actors-get-started-java.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-introduction/concurrency.png
[2]: ./media/service-fabric-reliable-actors-introduction/distribution.png
[3]: ./media/service-fabric-reliable-actors-introduction/actor-communication.png

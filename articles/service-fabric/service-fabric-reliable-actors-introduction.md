---
title: Omówienie elementów Reliable Actors w sieci szkieletowej usług | Dokumentacja firmy Microsoft
description: Wprowadzenie do modelu programowania w elementach Reliable Actors usługi Service Fabric.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 7fdad07f-f2d6-4c74-804d-e0d56131f060
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/01/2017
ms.author: vturecek
ms.openlocfilehash: 5a237e23dffed76e6122e17b59c85d20ca7e1baf
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58668674"
---
# <a name="introduction-to-service-fabric-reliable-actors"></a>Wprowadzenie do elementów Reliable Actors usługi Service Fabric
Elementy Reliable Actors to struktura aplikacji usługi Service Fabric na podstawie [wirtualnego aktora](https://research.microsoft.com/en-us/projects/orleans/) wzorca. Interfejsu API programu Reliable Actors oferuje jednowątkowe model programowania oparty na skalowalność i niezawodność gwarancje udostępniane przez usługę Service Fabric.

## <a name="what-are-actors"></a>Co to są aktorami?
Aktor jest izolowanym, niezależnie od jednostki obliczeniowe i stan z wykonywaniem jednowątkowe. [Wzorcem aktora](https://en.wikipedia.org/wiki/Actor_model) jest modelem obliczeniową systemów współbieżnych lub rozproszonej, w którym dużej liczby Aktorzy mogą być wykonywane jednocześnie, a niezależnie od siebie. Aktorzy może komunikować się ze sobą i tworzą więcej podmiotów.

### <a name="when-to-use-reliable-actors"></a>Kiedy używać elementów Reliable Actors
Elementy Reliable Actors usługi Service Fabric to implementacja wzorca projektowego aktora. Podobnie jak w przypadku wszelkie wzorzec projektowania oprogramowania decyzji, czy ma być używane z określonym wzorcem składa się zależnie od tego, czy oprogramowanie Projektuj problem pasuje do wzorca.

Mimo że wzorca projektowego aktora można jest dobrą dopasować liczby systemów rozproszonych problemy i scenariusze, zachować ostrożność, że muszą być wykonane uwagę ograniczenia wzorzec i framework jej implementacji. Jako ogólne wskazówki należy wziąć pod uwagę wzorcem aktora do modelowania problemów lub scenariusz, jeśli:

* Obszar problemu obejmuje dużą liczbą (tysięcy lub więcej) małe, niezależne i izolowanych jednostek, stanu i logiki.
* Chcesz pracować z jednowątkowe obiekty, które nie wymagają znaczących interakcji ze składników zewnętrznych, w tym, wykonywanie zapytań stanu w zestaw podmiotów.
* Wystąpienia aktora nie będzie blokować obiektów wywołujących nieprzewidywalne opóźnień, wysyłając operacji We/Wy.

## <a name="actors-in-service-fabric"></a>Aktorzy usługi Service Fabric
W usłudze Service Fabric aktorów są realizowane w ramach elementów Reliable Actors: Struktura aplikacji na podstawie wzorca aktora skompilowane na [usług Reliable Services usługi Service Fabric](service-fabric-reliable-services-introduction.md). Każda usługa Reliable Actors, którą piszesz jest faktycznie podzielonym na partycje i stanowej usługi Reliable Service.

Każdego aktora jest zdefiniowany jako wystąpienie typu aktora taka sama jak sposób, w jaki obiekt .NET jest wystąpieniem typu .NET. Na przykład może to być typ aktora, który implementuje funkcje Kalkulator i może istnieć wiele podmiotów tego typu, rozproszonych w różnych węzłach w klastrze. Każdy taki aktora jest unikatowo identyfikowana przez identyfikator aktora.

## <a name="actor-lifetime"></a>Okres istnienia aktora
Aktorów usługi Service Fabric są wirtualne, co oznacza, że ich okres istnienia nie jest związany z ich reprezentacji w pamięci. W rezultacie nie muszą jawnie utworzone lub zniszczone. Środowisko uruchomieniowe elementów Reliable Actors automatycznie aktywuje czas aktora pierwszy odbierze żądanie dla tego identyfikatora aktora. Jeśli aktora nie jest używany przez czas, w czasie wykonywania w elementach Reliable Actors wyrzucania elementów zbiera obiektu w pamięci. Również zachowa wiedzy o istnieniu aktora należy należy później ponownie aktywować. Aby uzyskać więcej informacji, zobacz [aktora cykl życia i odzyskiwanie kolekcji](service-fabric-reliable-actors-lifecycle.md).

Tę warstwę abstrakcji okres istnienia aktora wirtualnej niesie ze sobą pewne zastrzeżenia wyniku model aktorów wirtualnego, a w rzeczywistości implementacji interfejsu Reliable Actors w czasie odbiega od tego modelu.

* Aktor jest aktywowana automatycznie (co powoduje aktora obiekt, do którego można skonstruować) po raz pierwszy komunikat jest wysyłany do jego identyfikatora aktora. Po pewnym opóźnieniem od czasu obiekt aktora jest bezużyteczne. W przyszłości ponownie przy użyciu Identyfikatora aktora, powoduje, że można utworzyć nowy obiekt aktora. Stanu aktora outlives okresu istnienia obiektu podczas przechowywane w Menedżer stanów.
* Wywołaniem jakiejkolwiek jego metody aktora o identyfikatorze aktora aktywuje tego aktora. Z tego powodu aktora typy mają swoje Konstruktor wywołuje niejawnie przez środowisko uruchomieniowe. W związku z tym kod klienta nie parametry są przekazywane do konstruktora typ aktora, mimo że parametry może być przekazywane do konstruktora aktora za samą usługę. Powoduje to, że aktorów, musi być zbudowany w stanie częściowo zainicjowanych przez razem, gdy inne metody są wywoływane, jeśli aktora wymaga parametrów inicjowania od klienta. Nie ma żadnych pojedynczego punktu wejścia dla aktywacji aktora od klienta.
* Mimo że elementów Reliable Actors niejawnie tworzenia obiektów aktora; masz możliwość jawnie usunąć aktora i jego stan.

## <a name="distribution-and-failover"></a>Rozpowszechnianie i trybu failover
Aby zapewnić skalowalność i niezawodność, usługi Service Fabric dystrybuuje aktorów w całym klastrze i automatycznie migruje je z węzłów nie powiodło się tymi dobrej kondycji, zgodnie z potrzebami. Jest klasą abstrakcyjną za pośrednictwem [podzielonym na partycje i stanowej usługi Reliable Service](service-fabric-concepts-partitioning.md). Dystrybucja, skalowalności, niezawodności i automatycznej pracy awaryjnej znajdują się wszystkie ze względu na fakt, że Aktorzy działają wewnątrz stanowej usługi Reliable Service o nazwie *usługa aktora*.

Aktorzy są dystrybuowane na partycje usługi aktora, a te partycje są dystrybuowane między węzłami w klastrze usługi Service Fabric. Każda partycja usługi zawiera zestaw podmiotów. Usługa Service Fabric zarządza dystrybucji i pracy w trybie failover partycji usługi.

Na przykład usługi aktora z dziewięciu partycjami wdrożyć trzy węzły za pomocą domyślna lokalizacja partycji aktora będą rozproszone thusly:

![Niezawodny dystrybucyjnej aktorów][2]

Struktura aktora zarządza ustawieniami partycji schematu i klucza zakresu dla Ciebie. Upraszcza niektórych opcji, ale także niesie ze sobą pewne zagadnienia:

* Usług Reliable Services umożliwia wybierz schemat partycjonowania, zakres kluczy (w przypadku używania zakres schemat partycji), a liczba partycji. Elementy Reliable Actors jest ograniczona do zakresu (uniform schematu Int64) schemat partycjonowania i wymaga, aby korzystać z pełnego zakresu kluczy Int64.
* Domyślnie aktorów losowo są umieszczane na partycje skutkuje jednolity ROZKŁAD.
* Ponieważ losowo obowiązują aktorów, oczekiwane zachowane, że operacje aktora zawsze wymagają komunikacji sieciowej, w tym serializacji i deserializacji obiektu danych wywołania metody, opóźnień oraz obciążenie.
* W zaawansowanych scenariuszach istnieje możliwość umieszczenia partycji aktora kontroli przy użyciu aktora Int64 identyfikatorów, które mapują z określonymi partycjami. Jednak wykonanie tej tak może spowodować niezrównoważone dystrybucji aktorów partycjach.

Aby uzyskać więcej informacji o jak usług aktora są podzielone na partycje, zobacz [partycjonowania pojęcia dla aktorów](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors).

## <a name="actor-communication"></a>Komunikacja aktora
Interakcje aktora są definiowane w interfejs, który jest współużytkowany przez aktora, który implementuje interfejs i klienta, który pobiera serwer proxy przy użyciu tego samego interfejsu aktora. Ponieważ ten interfejs jest wykorzystywany do wywołania metody aktora asynchronicznie, każdej metody w interfejsie musi być zwracającą zadanie.

Wywołania metod i ich odpowiedzi na ostatecznie powoduje, że w żądaniach sieci w klastrze, więc argumentów i typy wyników zadania, które zwracają musi być możliwy do serializacji przez platformę. W szczególności, musi być [kontraktu danych serializacji](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

### <a name="the-actor-proxy"></a>Serwer proxy aktora
Interfejs API klienta elementów Reliable Actors zapewnia komunikację między wystąpienia aktora i klienta usługi aktora. Nawiązać połączenia z usługą aktora, klient tworzy obiekt serwera proxy aktora, który implementuje interfejs aktora. Klient korzysta z aktora przez wywołanie metody na obiekt serwera proxy. Serwer proxy aktora może służyć do komunikacji klienta do aktora i aktora do aktora.

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


Należy zauważyć, że dwóch rodzajów informacji używanych do tworzenia obiektu serwera proxy aktora ID aktora i nazwy aplikacji. Aktor o identyfikatorze unikatowo identyfikuje aktora, podczas gdy nazwa aplikacji identyfikuje [aplikacji usługi Service Fabric](service-fabric-reliable-actors-platform.md#application-model) wdrożonym aktora.

`ActorProxy`(C#) / `ActorProxyBase`Klasy (Java) po stronie klienta wykonuje niezbędne rozpoznawania, aby zlokalizować aktora według Identyfikatora i otwórz kanał komunikacyjny z nim. Również ponowi zlokalizować aktora w przypadku niepowodzenia komunikacji oraz przejścia w tryb failover. W rezultacie dostarczanie komunikatów ma następującą charakterystykę:

* Dostarczanie komunikatów jest najlepszy nakład pracy.
* Aktorzy może zostać wyświetlony zduplikowane komunikaty z tego samego klienta.

## <a name="concurrency"></a>Współbieżność
Środowisko uruchomieniowe elementów Reliable Actors udostępnia model prosty dostęp oparty na protokole Włącz do uzyskiwania dostępu do metody aktora. Oznacza to, że nie więcej niż jeden wątek może być aktywne wewnątrz kodu obiektowego aktora w dowolnym momencie. Dostępu na podstawie Włącz znacznie upraszcza systemów jednoczesnych, ponieważ nie ma potrzeby mechanizmów synchronizacji, aby uzyskać dostęp do danych. Oznacza to również, że systemy muszą być zaprojektowane tak, za pomocą specjalne uwagi dotyczące charakteru jednowątkowe dostęp do każdego wystąpienia aktora.

* Wystąpienia aktora pojedynczej nie może przetworzyć więcej niż jedno żądanie w danym momencie. Wystąpienia aktora może spowodować wąskie gardło przepływności, jeśli oczekuje się do obsługi żądań równoczesnych.
* Aktorów można zakleszczenie od siebie, jeśli istnieje cykliczne żądanie między dwoma uczestnikami podczas zewnętrznych wysłaniu żądania do jednego z uczestnikami jednocześnie. W środowisku uruchomieniowym aktora będą automatycznie przekroczyło limit czasu dla wywołań aktora i zgłosić wyjątek do obiektu wywołującego, aby przerwać możliwe zakleszczenie sytuacjach.

![Reliable Actors komunikacji][3]

### <a name="turn-based-access"></a>Włącz dostęp
Włącz składa się z pełną wykonanie metody aktora w odpowiedzi na żądanie z innych aktorów lub klientów lub zakończenie wykonywania [czasomierza/przypomnienie](service-fabric-reliable-actors-timers-reminders.md) wywołania zwrotnego. Mimo że te metody i wywołania zwrotne są asynchroniczne, środowisko uruchomieniowe aktorów nie ich przeplotu. Włącz musi być całkowicie zakończona, zanim nowy ruch jest dozwolony. Innymi słowy aktora metody lub czasomierza/przypomnienie wywołanie zwrotne, które jest w trakcie wykonywania muszą być w pełni przed nowe wywołania do metody lub wywołania zwrotnego jest dozwolone. Metody lub wywołania zwrotnego jest uważany za ukończony, czy wykonywania została zwrócona przez metodę wywołania zwrotnego i zadanie zwracane przez metodę lub wywołanie zwrotne zostało zakończone. Jest to warte podkreślając tego współbieżności tury jest zachowana nawet w różnych metod, czasomierze i wywołania zwrotne.

Środowisko wykonawcze aktorów wymusza współbieżności opartej na ruch przez uzyskanie blokady dla aktora, na początku Włącz i zwolnienie blokady na koniec Włącz. W związku z tym współbieżności opartej na Włącz są wymuszane na podstawie poszczególnych aktora i nie tylko w aktorów. Metody aktora i wywołania zwrotne czasomierza/przypomnienie mogą być wykonywane jednocześnie w imieniu innego aktorów.

Poniższy przykład ilustruje pojęcia powyżej. Należy wziąć pod uwagę typ aktora, który implementuje dwóch metod asynchronicznych (powiedzieć, *metoda1* i *Method2*), a timer oraz monit. Na poniższym diagramie przedstawiono oś czasu wykonywania te metody i wywołania zwrotne w imieniu dwóch uczestników (*ActorId1* i *ActorId2*) należących do tego typu aktora.

![Reliable Actors środowiska uruchomieniowego Włącz współbieżności opartej i dostęp][1]

Ten diagram jest zgodna z konwencjami te:

* Każdego wiersza w pionie przedstawia przepływ logiczny wykonywania metody lub wywołania zwrotnego w imieniu określonego aktora.
* Zdarzenia oznaczony w każdym wierszu pionowy występują w kolejności chronologicznej z nowszych zdarzeń mających miejsce poniżej tych starszych.
* Różne kolory są używane do osi czasu, odpowiadający uczestników.
* Wyróżnianie jest używany do wskazania czas trwania, dla którego utrzymania blokady dla aktora w imieniu metody lub wywołania zwrotnego.

Niektóre ważne punkty, które należy wziąć pod uwagę:

* Gdy *metoda1* jest wykonywany w imieniu *ActorId2* w odpowiedzi na żądania klienta *xyz789*, kolejne żądanie klienta (*abc123*) nadejściu, która wymaga również *metoda1* ma być wykonane przez *ActorId2*. Jednak wykonanie drugiej *metoda1* nie rozpoczyna się przed zakończeniem poprzedniego wykonania. Podobnie, przypomnienia zarejestrowane przez *ActorId2* generowane podczas *metoda1* jest wykonywana w odpowiedzi na żądania klienta *xyz789*. Przypomnienie wywołanie zwrotne jest wykonywane tylko po obu wykonaniami *metoda1* są kompletne. Wszystko to jest związany ze współbieżnością tury, są wymuszane w *ActorId2*.
* Podobnie, współbieżności opartej na Włącz również jest wymuszane dla *ActorId1*, jak pokazano na wykonanie *metoda1*, *Method2*i wywołanie zwrotne czasomierza imieniu  *ActorId1* wykonywane w sposób szeregowy.
* Wykonywanie *metoda1* imieniu *ActorId1* nakłada się na jej wykonanie w imieniu *ActorId2*. Jest to spowodowane współbieżności opartej na Włącz są wymuszane tylko w obrębie aktora i nie tylko w aktorów.
* W niektórych wykonań Metoda/wywołania zwrotnego `Task`(C#) / `CompletableFuture`(Java) zwróciło zakończeniu metody/wywołania zwrotnego po powrocie z metody. W niektórych innych asynchronicznej operacji zostało już zakończone przez razem, gdy metoda/wywołania zwrotnego zwraca. W obu przypadkach blokady dla aktora jest zwalniany tylko wtedy, gdy metoda/wywołania zwrotnego zwraca i zakończeniu operacji asynchronicznej.

### <a name="reentrancy"></a>Ponowne wejścia
Środowisko uruchomieniowe aktorów umożliwia współużytkowania wątkowości domyślnie. Oznacza to, że jeśli metoda aktora *aktora A* wywołuje metodę dla *B aktora*, który z kolei wywołuje inną metodę na *aktora A*, że metoda może być uruchomiona. Jest tak, ponieważ jest on częścią ten sam kontekst łańcuch wywołań logicznych. Wszystkie wywołania czasomierza i przypomnienie rozpoczynać nowe logicznym kontekście wywołań. Zobacz [współużytkowania wątkowości w elementach Reliable Actors](service-fabric-reliable-actors-reentrancy.md) Aby uzyskać więcej informacji.

### <a name="scope-of-concurrency-guarantees"></a>Zakres gwarancje współbieżności
Aktorzy środowisko wykonawcze zapewnia następujące gwarancje współbieżność w sytuacjach, w którym kontroluje wywołania tych metod. Na przykład zapewnia następujące gwarancje dla wywołań metod, które są wykonywane w odpowiedzi na żądania klienta, a także wywołania zwrotne czasomierza i przypomnienia. Jeśli jednak kod aktora bezpośrednio wywołuje te metody poza mechanizmach zapewnianych przez środowisko uruchomieniowe aktorów, następnie środowiska uruchomieniowego nie zawiera żadnej gwarancji współbieżności. Na przykład jeśli metoda jest wywoływana w kontekście niektórych zadań, która nie jest skojarzona z zadaniem zwracane przez metody aktora, środowisko uruchomieniowe nie podajemy gwarancje współbieżności. Jeśli metoda jest wywoływana z wątku, który aktora tworzy na jego własnej, następnie środowiska uruchomieniowego nie udostępniają gwarancje współbieżności. W związku z tym, aby wykonać operacje w tle, aktorów należy używać [aktora czasomierze i przypomnienia aktora](service-fabric-reliable-actors-timers-reminders.md) które przestrzegają tury współbieżności.

## <a name="next-steps"></a>Kolejne kroki
Rozpocznij pracę od utworzenia pierwszej usługi Reliable Actors:
   * [Wprowadzenie do elementów Reliable Actors na platformie .NET](service-fabric-reliable-actors-get-started.md)
   * [Wprowadzenie do elementów Reliable Actors w języku Java](service-fabric-reliable-actors-get-started-java.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-introduction/concurrency.png
[2]: ./media/service-fabric-reliable-actors-introduction/distribution.png
[3]: ./media/service-fabric-reliable-actors-introduction/actor-communication.png

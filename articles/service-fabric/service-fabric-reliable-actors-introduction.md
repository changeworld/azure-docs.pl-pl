---
title: Omówienie wiarygodnych aktorów sieci szkieletowej usług
description: Wprowadzenie do modelu programowania niezawodnych aktorów sieci szkieletowej usług, na podstawie wzorca aktora wirtualnego.
author: vturecek
ms.topic: conceptual
ms.date: 11/01/2017
ms.author: vturecek
ms.openlocfilehash: 6aafa2a3372c431f8afa7fad41051c26c3fe5fcd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645569"
---
# <a name="introduction-to-service-fabric-reliable-actors"></a>Wprowadzenie do sieci usługowych Wiarygodnych aktorów
Reliable Actors to struktura aplikacji sieci szkieletowej usług na podstawie wzorca [aktora wirtualnego.](https://research.microsoft.com/en-us/projects/orleans/) Interfejs API reliable actors zapewnia jednowątkowy model programowania oparty na gwarancjach skalowalności i niezawodności zapewnianych przez usługę Service Fabric.

## <a name="what-are-actors"></a>Co to są aktorzy?
Aktor jest izolowane, niezależne jednostki obliczeń i stanu z jednowątkowej wykonanie. [Wzorzec aktora](https://en.wikipedia.org/wiki/Actor_model) jest modelem obliczeniowym dla systemów równoczesnych lub rozproszonych, w których duża liczba tych podmiotów może wykonywać jednocześnie i niezależnie od siebie. Aktorzy mogą komunikować się ze sobą i mogą tworzyć więcej aktorów.

### <a name="when-to-use-reliable-actors"></a>Kiedy używać wiarygodnych aktorów
Usługa Fabric Reliable Actors jest implementacją wzorca projektowania aktora. Podobnie jak w przypadku każdego wzorca projektowania oprogramowania, decyzja o użyciu określonego wzorca jest podejmowana w oparciu o to, czy problem z projektowaniem oprogramowania pasuje do wzorca.

Chociaż wzorzec projektu aktora może być dobrze dopasowany do wielu problemów i scenariuszy systemów rozproszonych, należy dokładnie rozważyć ograniczenia wzorca i struktury implementującej go. Jako ogólne wskazówki należy wziąć pod uwagę wzorzec aktora do modelowania problemu lub scenariusza, jeśli:

* Przestrzeń problemowa obejmuje dużą liczbę (tysiące lub więcej) małych, niezależnych i odizolowanych jednostek stanu i logiki.
* Chcesz pracować z obiektami jednowątkowymi, które nie wymagają znaczącej interakcji ze składnikami zewnętrznymi, w tym zekonserwazowaniem stanu w zestawie aktorów.
* Wystąpienia aktora nie będą blokować wywołujących z nieprzewidywalnymi opóźnieniami przez wystawianie operacji we/wy.

## <a name="actors-in-service-fabric"></a>Aktorzy w sieci szkieletowej usług
W sieci szkieletowej usług aktorzy są implementowane w ramach Reliable Actors: Struktura aplikacji oparta na wzorcu aktora, zbudowana na podstawie [niezawodnych usług sieci szkieletowej usług.](service-fabric-reliable-services-introduction.md) Każda usługa reliable actor, którą piszesz, jest w rzeczywistości partycjonowanym, stanowym niezawodną usługą.

Każdy aktor jest zdefiniowany jako wystąpienie typu aktora, identyczne ze sposobem obiektu .NET jest wystąpieniem typu .NET. Na przykład może istnieć typ aktora, który implementuje funkcjonalność kalkulatora i może istnieć wiele podmiotów tego typu, które są dystrybuowane w różnych węzłach w klastrze. Każdy taki aktor jest jednoznacznie identyfikowany przez identyfikator aktora.

## <a name="actor-lifetime"></a>Dożywotnia żywotność aktora
Aktorzy sieci szkieletowej usług są wirtualne, co oznacza, że ich okres istnienia nie jest związany z ich reprezentacji w pamięci. W rezultacie nie muszą być jawnie tworzone lub niszczone. Środowisko wykonawcze Reliable Actors automatycznie aktywuje aktora po raz pierwszy odbiera żądanie dla tego identyfikatora aktora. Jeśli aktor nie jest używany przez pewien okres czasu, reliable actors środowiska wykonawczego garbage-zbiera obiekt w pamięci. Będzie również utrzymywać wiedzę o istnieniu aktora, jeśli będzie musiał zostać później reaktywowany. Aby uzyskać więcej informacji, zobacz [Cykl życia aktora i wyrzucanie elementów bezużytecznych](service-fabric-reliable-actors-lifecycle.md).

Ta abstrakcja życia wirtualnego aktora niesie ze sobą pewne zastrzeżenia w wyniku modelu aktora wirtualnego, a w rzeczywistości implementacja Reliable Actors odbiega czasami od tego modelu.

* Aktor jest automatycznie aktywowany (powodując obiekt aktora do skonstruowania) po raz pierwszy wiadomość jest wysyłana do jego identyfikator aktora. Po pewnym czasie obiekt aktora jest zbierane śmieci. W przyszłości przy użyciu identyfikatora aktora ponownie powoduje, że nowy obiekt aktora do skonstruowania. Stan aktora przeżywa okres istnienia obiektu, gdy jest przechowywany w menedżerze stanu.
* Wywołanie dowolnej metody aktora dla identyfikatora aktora aktywuje tego aktora. Z tego powodu typy aktorów mają ich konstruktora wywoływane niejawnie przez środowisko wykonawcze. W związku z tym kod klienta nie może przekazać parametry do konstruktora typu aktora, chociaż parametry mogą być przekazywane do konstruktora aktora przez samą usługę. W rezultacie aktorzy mogą być konstruowane w stanie częściowo zainicjowane przez czas innych metod są wywoływane na nim, jeśli aktor wymaga parametrów inicjowania z klienta. Nie ma pojedynczego punktu wejścia dla aktywacji aktora z klienta.
* Chociaż reliable actors niejawnie utworzyć obiekty aktora; masz możliwość jawnego usunięcia aktora i jego stanu.

## <a name="distribution-and-failover"></a>Dystrybucja i przewijamy w pracy awaryjnej
Aby zapewnić skalowalność i niezawodność, usługa Service Fabric dystrybuuje podmioty w całym klastrze i automatycznie migruje je z węzłów, które uległy awarii do węzłów w dobrej kondycji zgodnie z wymaganiami. Jest to abstrakcja przez [partycjonowane, stanowe niezawodnej usługi](service-fabric-concepts-partitioning.md). Dystrybucja, skalowalność, niezawodność i automatyczna usługa awaryjna są dostarczane ze względu na fakt, że aktorzy działają wewnątrz stanowej niezawodnej usługi o nazwie *Actor Service*.

Aktorzy są dystrybuowane między partycjami usługi aktora i te partycje są rozproszone między węzłami w klastrze sieci szkieletowej usług. Każda partycja usługi zawiera zestaw aktorów. Sieć szkieletowa usług zarządza dystrybucją i obsługą awaryjną partycji usługi.

Na przykład usługa aktora z dziewięcioma partycjami wdrożonymi w trzech węzłach przy użyciu domyślnego rozmieszczenia partycji aktora będzie dystrybuowana w ten sposób:

![Dystrybucja wiarygodnych aktorów][2]

Struktura aktora zarządza schemat partycji i ustawienia zakresu kluczy dla Ciebie. Upraszcza to niektóre wybory, ale również niesie ze sobą pewne rozważania:

* Niezawodne usługi pozwala wybrać schemat partycjonowania, zakres kluczy (przy użyciu schematu partycjonowania zakresu) i liczbę partycji. Reliable Actors jest ograniczona do schematu partycjonowania zakresu (jednolity schemat Int64) i wymaga użycia pełnego zakresu klawiszy Int64.
* Domyślnie aktorzy są losowo umieszczane w partycjach w wyniku jednolitego rozkładu.
* Ponieważ podmioty są losowo umieszczane, należy oczekiwać, że operacje aktora zawsze będzie wymagać komunikacji sieciowej, w tym serializacji i deserializacji danych wywołania metody, ponoszenia opóźnienia i obciążenie.
* W zaawansowanych scenariuszach można kontrolować rozmieszczenie partycji aktora przy użyciu identyfikatorów aktora Int64, które mapują na określone partycje. Jednak w ten sposób może spowodować niezrównoważonej dystrybucji podmiotów między partycjami.

Aby uzyskać więcej informacji na temat sposobu podziału usług aktora, zapoznaj się z [pojęciami partycjonowania dla aktorów](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors).

## <a name="actor-communication"></a>Komunikacja z aktorem
Interakcje aktora są definiowane w interfejsie, który jest współużytkowany przez aktora, który implementuje interfejs i klienta, który pobiera serwer proxy do aktora za pośrednictwem tego samego interfejsu. Ponieważ ten interfejs jest używany do wywoływania metod aktora asynchronicznie, każda metoda w interfejsie musi być zwracany zadaniem.

Wywołania metody i ich odpowiedzi ostatecznie spowodować żądania sieciowe w klastrze, więc argumenty i typy wyników zadań, które zwracają muszą być serializowalne przez platformę. W szczególności muszą być [serializable umowy danych](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

### <a name="the-actor-proxy"></a>Pełnomocnik aktora
Interfejs API klienta reliable actors zapewnia komunikację między wystąpieniem aktora a klientem aktora. Aby komunikować się z aktorem, klient tworzy obiekt proxy aktora, który implementuje interfejs aktora. Klient współdziała z aktorem, wywołując metody na obiekcie proxy. Serwer proxy aktora może służyć do komunikacji między klientem a aktorem i aktora do aktora.

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


Należy zauważyć, że dwie informacje używane do tworzenia obiektu proxy aktora są identyfikator aktora i nazwę aplikacji. Identyfikator aktora jednoznacznie identyfikuje aktora, podczas gdy nazwa aplikacji identyfikuje [aplikację sieci szkieletowej usług,](service-fabric-reliable-actors-platform.md#application-model) w której jest wdrażany aktor.

Klasa `ActorProxy`(C#) `ActorProxyBase`/ (Java) po stronie klienta wykonuje rozdzielczość niezbędną do zlokalizowania aktora według identyfikatora i otwarcia kanału komunikacji z nim. Również ponawia próby zlokalizowania aktora w przypadku awarii komunikacji i pracy awaryjnej. W rezultacie dostarczanie wiadomości ma następujące cechy:

* Dostarczanie wiadomości jest najlepszym wysiłkiem.
* Aktorzy mogą odbierać zduplikowane wiadomości od tego samego klienta.

## <a name="concurrency"></a>Współbieżność
Środowisko wykonawcze Reliable Actors zapewnia prosty turowy model dostępu do uzyskiwania dostępu do metod aktora. Oznacza to, że nie więcej niż jeden wątek może być aktywny wewnątrz kodu obiektu aktora w dowolnym momencie. Dostęp turowy znacznie upraszcza równoczesnych systemów, ponieważ nie ma potrzeby mechanizmów synchronizacji dostępu do danych. Oznacza to również, że systemy muszą być zaprojektowane ze szczególnym uwzględnieniem jednowątkowego charakteru dostępu każdego wystąpienia aktora.

* Wystąpienie pojedynczego aktora nie może przetworzyć więcej niż jednego żądania naraz. Wystąpienie aktora może spowodować wąskie gardło przepływności, jeśli oczekuje się obsługi równoczesnych żądań.
* Aktorzy mogą zakleszczenie na siebie, jeśli istnieje okrągłe żądanie między dwoma podmiotami, podczas gdy żądanie zewnętrzne jest składane do jednego z aktorów jednocześnie. Środowisko wykonawcze aktora automatycznie limit czasu na wywołania aktora i zgłosić wyjątek do wywołującego, aby przerwać możliwe sytuacje zakleszczenia.

![Niezawodna komunikacja z aktorami][3]

### <a name="turn-based-access"></a>Dostęp turowy
Kolej składa się z pełnego wykonania metody aktora w odpowiedzi na żądanie od innych aktorów lub klientów lub pełne wykonanie wywołania zwrotnego [czasomierz/przypomnienie.](service-fabric-reliable-actors-timers-reminders.md) Mimo że te metody i wywołania zwrotne są asynchroniczne, środowisko wykonawcze Actors nie przeplata ich. Obrót musi być w pełni gotowy, zanim nowy obrót jest dozwolony. Innymi słowy metody aktora lub czasomierz/przypomnienie wywołania zwrotnego, który jest obecnie wykonywany musi być w pełni zakończona przed nowe wywołanie metody lub wywołania zwrotnego jest dozwolone. Metoda lub wywołanie zwrotne jest uważane za zakończone, jeśli wykonanie zostało zwrócone z metody lub wywołania zwrotnego i zadanie zwrócone przez metodę lub wywołanie zwrotne zostało zakończone. Warto podkreślić, że współbieżność turowa jest przestrzegana nawet w różnych metodach, czasomierzach i wywołaniach zwrotnych.

Środowisko wykonawcze Actors wymusza współbieżność turową, uzyskując blokadę na aktora na początku tury i zwalniając blokadę na końcu tury. W związku z tym współbieżność turowa jest wymuszana na podstawie na aktora, a nie między aktorami. Metody aktora i wywołania zwrotne czasomierz/przypomnienie można wykonać jednocześnie w imieniu różnych aktorów.

Poniższy przykład ilustruje powyższe pojęcia. Należy wziąć pod uwagę typ aktora, który implementuje dwie metody asynchroniczne (powiedzmy, *Method1* i *Method2*), czasomierz i przypomnienie. Poniższy diagram przedstawia przykład osi czasu dla wykonywania tych metod i wywołań zwrotnych w imieniu dwóch aktorów (*ActorId1* i *ActorId2*), które należą do tego typu aktora.

![Współbieżność turowa i dostęp do środowiska wykonawczego Reliable Actors][1]

Ten diagram jest zgodny z następującymi konwencjami:

* Każda linia pionowa pokazuje logiczny przepływ wykonywania metody lub wywołania zwrotnego w imieniu określonego aktora.
* Zdarzenia oznaczone na każdej linii pionowej występują w porządku chronologicznym, a nowsze zdarzenia występują poniżej starszych.
* Różne kolory są używane dla osi czasu odpowiadających różnym podmiotom.
* Wyróżnianie służy do wskazania czasu trwania blokady na aktora jest utrzymywana w imieniu metody lub wywołania zwrotnego.

Kilka ważnych kwestii do rozważenia:

* Podczas *gdy Metoda1* jest wykonywana w imieniu *ActorId2* w odpowiedzi na żądanie klienta *xyz789,* pojawia się inne żądanie klienta (*abc123*), które wymaga również *metody1* do wykonania przez *ActorId2*. Jednak drugie wykonanie *Metody1* nie rozpoczyna się, dopóki nie zakończy się poprzednie wykonanie. Podobnie przypomnienie zarejestrowane przez *ActorId2* uruchamia się, gdy *Method1* jest wykonywany w odpowiedzi na żądanie klienta *xyz789*. Wywołanie zwrotne przypomnienia jest wykonywane tylko po zakończeniu wykonywania *Metody1.* Wszystko to jest spowodowane koncelisją jest wymuszane dla *ActorId2*.
* Podobnie współbieżność turowa jest również wymuszana dla *ActorId1*, jak wykazano w wykonaniu *Metody1*, *Metody2*i wywołania zwrotnego czasomierza w imieniu *ActorId1* dzieje się w sposób szeregowy.
* Wykonanie *metody1* w imieniu *ActorId1* nakłada się na jego wykonanie w imieniu *ActorId2*. Dzieje się tak, ponieważ współbieżność turowa jest wymuszana tylko w obrębie aktora, a nie między aktorami.
* W niektórych wykonaniach metody/wywołania `Task`zwrotnego (C#) / `CompletableFuture`(Java) zwracany przez metodę/wywołanie zwrotne kończy się po powrocie metody. W niektórych innych operacja asynchroniczne została już zakończona do czasu, gdy metoda/wywołanie zwrotne zwraca. W obu przypadkach blokada na aktora jest zwalniana tylko po zakończeniu zarówno metody/wywołania zwrotnego, jak i zakończenia operacji asynchronicznej.

### <a name="reentrancy"></a>Ponowne wejścia
Środowisko wykonawcze Actors domyślnie umożliwia ponowne wniesienie. Oznacza to, że jeśli metoda aktora *aktora A* wywołuje metodę na *aktora B*, który z kolei wywołuje inną metodę na *aktora A,* ta metoda jest dozwolona do uruchomienia. Dzieje się tak, ponieważ jest częścią tego samego kontekstu logicznego łańcucha wywołań. Wszystkie wywołania czasomierza i przypomnienia zaczynają się od nowego kontekstu wywołania logicznego. Zobacz [Reliable Actors reentrancy](service-fabric-reliable-actors-reentrancy.md) więcej szczegółów.

### <a name="scope-of-concurrency-guarantees"></a>Zakres gwarancji współbieżności
Środowisko wykonawcze Actors zapewnia te gwarancje współbieżności w sytuacjach, w których kontroluje wywołanie tych metod. Na przykład zapewnia te gwarancje dla wywołań metody, które są wykonywane w odpowiedzi na żądanie klienta, a także dla czasomierza i wywołania zwrotnego przypomnienia. Jednak jeśli kod aktora bezpośrednio wywołuje te metody poza mechanizmami dostarczonymi przez środowisko uruchomieniowe Actors, środowisko wykonawcze nie może zapewnić żadnych gwarancji współbieżności. Na przykład jeśli metoda jest wywoływana w kontekście niektórych zadań, które nie jest skojarzone z zadaniem zwróconym przez metody aktora, środowisko wykonawcze nie może zapewnić gwarancji współbieżności. Jeśli metoda jest wywoływana z wątku, który aktor tworzy na własną rękę, a następnie środowisko uruchomieniowe również nie może zapewnić gwarancje współbieżności. W związku z tym do wykonywania operacji w tle, aktorzy należy użyć [czasomierze aktora i przypomnienia aktora,](service-fabric-reliable-actors-timers-reminders.md) które szanują współbieżność turowej.

## <a name="next-steps"></a>Następne kroki
Rozpocznij pracę, tworząc pierwszą usługę Reliable Actors:
   * [Wprowadzenie do reliable actors w programie .NET](service-fabric-reliable-actors-get-started.md)
   * [Pierwsze kroki z reliable actors na Javie](service-fabric-reliable-actors-get-started-java.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-introduction/concurrency.png
[2]: ./media/service-fabric-reliable-actors-introduction/distribution.png
[3]: ./media/service-fabric-reliable-actors-introduction/actor-communication.png

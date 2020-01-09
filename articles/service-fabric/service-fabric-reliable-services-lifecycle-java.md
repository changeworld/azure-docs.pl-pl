---
title: Cykl życia usługi Azure Service Fabric Reliable Services
description: Dowiedz się więcej o zdarzeniach cyklu życia w aplikacji Reliable Services Service Fabric platformy Azure przy użyciu języka Java dla usług stanowych i bezstanowych.
author: PavanKunapareddyMSFT
ms.topic: conceptual
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: 1d3be958a0649ed3e80df2d63adbdf0b91831dbd
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75639602"
---
# <a name="reliable-services-lifecycle"></a>Cykl życia usług Reliable Services
> [!div class="op_single_selector"]
> * [C# w systemie Windows](service-fabric-reliable-services-lifecycle.md)
> * [Java w systemie Linux](service-fabric-reliable-services-lifecycle-java.md)
>
>

Reliable Services to jeden z modeli programowania dostępnych w usłudze Azure Service Fabric. Podczas nauki dotyczącej cyklu życia Reliable Services najważniejsze jest zrozumienie podstawowych zdarzeń cyklu życia. Dokładne porządkowanie zdarzeń zależy od szczegółów konfiguracji. 

Ogólnie rzecz biorąc Reliable Services cykl życia obejmuje następujące zdarzenia:

* Podczas uruchamiania:
  * Usługi są zbudowane.
  * Usługi mogą tworzyć i zwracać zero lub więcej odbiorników.
  * Wszystkie zwrócone detektory są otwierane w celu komunikacji z usługą.
  * Metoda `runAsync` usługi jest wywoływana, więc usługa może działać długotrwałe lub w tle.
* Podczas zamykania:
  * Token anulowania, który został przekazano do `runAsync`, został anulowany, a detektory są zamknięte.
  * Sam obiekt usługi jest destruktorem.

Kolejność zdarzeń w Reliable Services może ulec zmianie nieco w zależności od tego, czy niezawodna usługa jest bezstanowa, czy stanowa. 

Ponadto w przypadku usług stanowych należy rozwiązać podstawowy scenariusz wymiany. Podczas tej sekwencji rola podstawowa jest przekazywana do innej repliki (lub wraca) bez zamykania usługi. 

Na koniec należy wziąć pod uwagę warunki błędu lub błędu.

## <a name="stateless-service-startup"></a>Uruchamianie usługi bezstanowej
Cykl życia usługi bezstanowej jest dość skomplikowany. Oto kolejność zdarzeń:

1. Usługa jest zbudowana.
2. Te zdarzenia są wykonywane równolegle:
    - `StatelessService.createServiceInstanceListeners()` jest wywoływana i wszystkie zwrócone detektory są otwarte. `CommunicationListener.openAsync()` jest wywoływana dla każdego odbiornika.
    - Metoda `runAsync` usługi (`StatelessService.runAsync()`) jest wywoływana.
3. Jeśli jest obecny, Metoda `onOpenAsync`a usługi jest wywoływana. W odróżnieniu od tego `StatelessService.onOpenAsync()` jest wywoływana. Jest to nietypowe przesłonięcie, ale jest dostępne.

Należy zauważyć, że nie istnieje porządkowanie między wywołaniem do tworzenia i otwierania odbiorników oraz wywołaniem `runAsync`. Detektory mogą zostać otwarte przed uruchomieniem `runAsync`. Podobnie `runAsync` mogą być wywoływane przed otwarciem odbiorników komunikacji lub przed ich skonstruowaniem. Jeśli wymagana jest jakakolwiek synchronizacja, musi ona zostać wykonana przez realizatora. Oto kilka typowych rozwiązań:

* Czasami odbiorniki nie mogą działać, dopóki nie zostaną utworzone inne informacje lub nie będą wykonywane inne prace. W przypadku usług bezstanowych zwykle można wykonać te czynności w konstruktorze usługi. Można to zrobić w trakcie wywołania `createServiceInstanceListeners()` lub jako część konstrukcji odbiornika.
* Czasami kod w `runAsync` nie zostanie uruchomiony, dopóki odbiorniki nie zostaną otwarte. W takim przypadku konieczne jest dodatkowe koordynowanie. Typowym rozwiązaniem jest dodanie flagi do odbiorników. Flaga wskazuje, kiedy detektory zostały zakończone. Metoda `runAsync` sprawdza to przed kontynuowaniem rzeczywistej pracy.

## <a name="stateless-service-shutdown"></a>Zamykanie bezstanowej usługi
Podczas zamykania usługi bezstanowej następuje ten sam wzorzec, ale w odwrocie:

1. Te zdarzenia są wykonywane równolegle:
    - Wszystkie otwarte odbiorniki są zamknięte. `CommunicationListener.closeAsync()` jest wywoływana dla każdego odbiornika.
    - Token anulowania, który został przekazano do `runAsync()`, został anulowany. Sprawdzanie, czy właściwość `isCancelled` tokenu anulowania zwraca `true`, i jeśli zostanie wywołana, Metoda `throwIfCancellationRequested` tokenu zgłosi `CancellationException`.
2. Gdy `closeAsync()` kończy się na każdym odbiorniku i `runAsync()` kończy również, Metoda `StatelessService.onCloseAsync()` usługi jest wywoływana, jeśli jest obecna. Nie jest to typowy przesłonięcie, ale może służyć do bezpiecznego zamykania zasobów, zatrzymania przetwarzania w tle, kończenia zapisywania zewnętrznego stanu lub zamykania istniejących połączeń.
3. Po zakończeniu `StatelessService.onCloseAsync()` obiekt usługi jest destruktorem.

## <a name="stateful-service-startup"></a>Uruchamianie usługi stanowej
Usługi stanowe mają wzorzec podobny do usług bezstanowych, przy czym wprowadzono kilka zmian.  Oto kolejność zdarzeń związanych z uruchamianiem usługi stanowej:

1. Usługa jest zbudowana.
2. `StatefulServiceBase.onOpenAsync()` jest wywoływana. To wywołanie nie jest zwykle zastępowane w usłudze.
3. Te zdarzenia są wykonywane równolegle:
    - `StatefulServiceBase.createServiceReplicaListeners()` jest wywoływana. 
      - Jeśli usługa jest usługą podstawową, wszystkie zwrócone detektory są otwarte. `CommunicationListener.openAsync()` jest wywoływana dla każdego odbiornika.
      - Jeśli usługa jest usługą dodatkową, otwierane są tylko odbiorniki oznaczone jako `listenOnSecondary = true`. Posiadanie odbiorników, które są otwarte na serwerze pomocniczym, jest rzadziej.
    - Jeśli usługa jest obecnie podstawową, wywoływana jest metoda `StatefulServiceBase.runAsync()` usługi.
4. Po zakończeniu wszystkich wywołań `openAsync()` dla odbiornika repliki i `runAsync()` jest wywoływana, `StatefulServiceBase.onChangeRoleAsync()` jest wywoływana. To wywołanie nie jest zwykle zastępowane w usłudze.

Podobnie jak w przypadku usług bezstanowych, w usłudze stanowej nie istnieje koordynacja między kolejnością tworzenia i otwierania odbiorników oraz podczas wywoływania `runAsync`. W przypadku konieczności koordynacji rozwiązania są znacznie takie same. Istnieje jednak jeden dodatkowy przypadek dla usługi stanowej. Załóżmy, że wywołania, które docierają do odbiorników komunikacji, wymagają informacji przechowywanych wewnątrz niektórych [niezawodnych kolekcji](service-fabric-reliable-services-reliable-collections.md). Ponieważ odbiorniki komunikacji mogą zostać otwarte przed możliwym do odczytu i zapisywalnymi kolekcjami, a przed rozpoczęciem `runAsync` należy koniecznie wykonać pewne dodatkowe koordynacji. Najprostszym i najbardziej typowym rozwiązaniem jest, aby odbiorniki komunikacji zwracały kod błędu. Klient używa kodu błędu, aby wiedzieć, jak ponowić żądanie.

## <a name="stateful-service-shutdown"></a>Wyłączenie usługi stanowej
Podobnie jak w przypadku usług bezstanowych, zdarzenia cyklu życia podczas zamykania są takie same jak podczas uruchamiania, ale odwrócone. Gdy usługa stanowa jest zamykana, wystąpią następujące zdarzenia:

1. Te zdarzenia są wykonywane równolegle:
    - Wszystkie otwarte odbiorniki są zamknięte. `CommunicationListener.closeAsync()` jest wywoływana dla każdego odbiornika.
    - Token anulowania, który został przekazano do `runAsync()`, został anulowany. Wywołanie metody `isCancelled()` tokenu anulowania zwraca `true`, a jeśli zostanie wywołana, Metoda `throwIfCancellationRequested()` tokenu zgłasza `OperationCanceledException`.
2. Po zakończeniu `closeAsync()` na każdym odbiorniku, gdy zostanie również `runAsync()`, zostanie wywołana `StatefulServiceBase.onChangeRoleAsync()` usługi. To wywołanie nie jest zwykle zastępowane w usłudze.

   > [!NOTE]  
   > Oczekiwanie na zakończenie `runAsync` jest konieczne tylko wtedy, gdy replika jest repliką podstawową.

3. Po zakończeniu `StatefulServiceBase.onChangeRoleAsync()` metody Metoda `StatefulServiceBase.onCloseAsync()` zostanie wywołana. To wywołanie jest nietypowym przesłonięciem, ale jest dostępne.
3. Po zakończeniu `StatefulServiceBase.onCloseAsync()` obiekt usługi jest destruktorem.

## <a name="stateful-service-primary-swaps"></a>Podstawowe swapy usługi stanowej
Podczas gdy usługa stanowa jest uruchomiona, detektory komunikacji są otwierane i Metoda `runAsync` jest wywoływana tylko dla replik podstawowych tych usług stanowych. Repliki pomocnicze są konstruowane, ale nie są wyświetlane żadne dalsze wywołania. Gdy usługa stanowa jest uruchomiona, replika, która jest obecnie podstawowa, może ulec zmianie. Zdarzenia cyklu życia, które może zobaczyć replika stanowa, są zależne od tego, czy replika jest obniżana czy podwyższana podczas wymiany.

### <a name="for-the-demoted-primary"></a>Dla obniżonego poziomu podstawowego
Service Fabric wymaga repliki podstawowej, która została obniżona, aby zatrzymać przetwarzanie komunikatów i zatrzymać wszystkie prace w tle. Ten krok jest podobny do momentu wyłączenia usługi. Różnica polega na tym, że usługa nie jest zainstalowana lub ZAMKNIĘTA, ponieważ pozostanie jako pomocnicza. Wystąpią następujące zdarzenia:

1. Te zdarzenia są wykonywane równolegle:
    - Wszystkie otwarte odbiorniki są zamknięte. `CommunicationListener.closeAsync()` jest wywoływana dla każdego odbiornika.
    - Token anulowania, który został przekazano do `runAsync()`, został anulowany. Sprawdzenie metody `isCancelled()` tokenu anulowania zwraca `true`. W przypadku wywołania metoda `throwIfCancellationRequested()` tokenu zgłasza `OperationCanceledException`.
2. Po zakończeniu `closeAsync()` na każdym odbiorniku, gdy zostanie również `runAsync()`, zostanie wywołana `StatefulServiceBase.onChangeRoleAsync()` usługi. To wywołanie nie jest zwykle zastępowane w usłudze.

### <a name="for-the-promoted-secondary"></a>Dla awansowanej pomocniczej
Podobnie, Service Fabric wymaga replice pomocniczej, która została podwyższona, aby rozpocząć nasłuchiwanie komunikatów w sieci oraz uruchamianie wszelkich zadań w tle, które muszą zostać ukończone. Ten proces jest podobny do podczas tworzenia usługi. Różnica polega na tym, że replika już istnieje. Wystąpią następujące zdarzenia:

1. Te zdarzenia są wykonywane równolegle:
    - `StatefulServiceBase.createServiceReplicaListeners()` jest wywoływana i wszystkie zwrócone detektory są otwarte. `CommunicationListener.openAsync()` jest wywoływana dla każdego odbiornika.
    - Metoda `StatefulServiceBase.runAsync()` usługi jest wywoływana.
2. Po zakończeniu wszystkich wywołań `openAsync()` dla odbiornika repliki i `runAsync()` jest wywoływana, `StatefulServiceBase.onChangeRoleAsync()` jest wywoływana. To wywołanie nie jest zwykle zastępowane w usłudze.

### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>Typowe problemy występujące podczas zamykania usługi stanowej i podstawowego obniżania poziomu
Service Fabric zmienia podstawową usługę stanową z wielu powodów. Najczęstszymi przyczynami jest ponowne [zrównoważenie klastra](service-fabric-cluster-resource-manager-balancing.md) i [uaktualnienie aplikacji](service-fabric-application-upgrade.md). Podczas tych operacji ważne jest, aby usługa respektować `cancellationToken`. Ma to zastosowanie również podczas normalnego zamykania usługi, na przykład jeśli usługa została usunięta.

Usługi, które nie obsługują czyszczenia anulowania mogą napotkać kilka problemów. Te operacje są wolne, ponieważ Service Fabric czeka, aż usługi przestaną być bezpieczne. Może to ostatecznie prowadzić do nieudanych uaktualnień, które przekroczą limit czasu i Wycofaj. Niepowodzenie honorowania tokenu anulowania może również spowodować niezrównoważone klastry. Klastry stają się niezrównoważone, ponieważ węzły uzyskują gorącą. Nie można jednak ponownie zrównoważyć usług, ponieważ ich przenoszenie w innym miejscu trwa zbyt długo. 

Ponieważ usługi są stanowe, istnieje również możliwość, że usługi używają [niezawodnych kolekcji](service-fabric-reliable-services-reliable-collections.md). W Service Fabric, gdy podstawowa jest obniżana, jeden z pierwszych rzeczy jest taki, że dostęp do zapisu do podstawowego stanu jest odwołany. Prowadzi to do drugiego zestawu problemów, które mogą mieć wpływ na cykl usługi. Kolekcje zwracają wyjątki na podstawie chronometrażu i tego, czy replika jest przenoszona, czy wyłączona. Ważne jest, aby prawidłowo obsługiwać te wyjątki. 

Wyjątki zgłoszone przez Service Fabric są trwałe [(`FabricException`)](https://docs.microsoft.com/java/api/system.fabric.exception) lub przejściowe [(`FabricTransientException`)](https://docs.microsoft.com/java/api/system.fabric.exception.fabrictransientexception). Stałe wyjątki powinny być rejestrowane i zgłaszane. Wyjątki przejściowe mogą być ponawiane na podstawie logiki ponawiania.

Ważnym elementem testowania i sprawdzania poprawności Reliable Services jest obsługa wyjątków, które pochodzą z używania `ReliableCollections` w połączeniu z zdarzeniami cyklu życia usługi. Zalecamy, aby zawsze uruchamiać usługę pod obciążeniem. Należy również wykonać uaktualnienia i [testowanie chaos](service-fabric-controlled-chaos.md) przed wdrożeniem w środowisku produkcyjnym. Te podstawowe kroki pomagają upewnić się, że usługa jest zaimplementowana prawidłowo i że prawidłowo obsługuje zdarzenia dotyczące cyklu życia.

## <a name="notes-on-service-lifecycle"></a>Uwagi dotyczące cyklu życia usługi
* Zarówno Metoda `runAsync()`, jak i wywołania `createServiceInstanceListeners/createServiceReplicaListeners` są opcjonalne. Usługa może mieć jeden, oba lub żadne z nich. Na przykład jeśli usługa wykonuje całą swoją działania w odpowiedzi na wywołania użytkowników, nie ma potrzeby implementowania `runAsync()`. Wymagane są tylko odbiorniki komunikacji i ich skojarzony kod.  Podobnie tworzenie i zwracanie odbiorników komunikacji jest opcjonalne. Usługa może korzystać tylko z pracy w tle, więc musi tylko zaimplementować `runAsync()`.
* Wykonanie usługi powiodło się, aby pomyślnie `runAsync()` i zwrócić ją z niej. Ta wartość nie jest traktowana jako warunek błędu. Reprezentuje prace w tle dla zakończenia usługi. W przypadku Reliable Services stanowych `runAsync()` zostanie ponownie wywołana, jeśli usługa zostanie obniżona z poziomu podstawowego, a następnie podwyższona do poziomu podstawowego.
* Jeśli usługa kończy działanie z `runAsync()` przez wygenerowanie nieoczekiwanego wyjątku, jest to błąd. Obiekt usługi jest wyłączony i raportowany jest błąd kondycji.
* Chociaż nie ma limitu czasu na zwracaniu z tych metod, natychmiast utracisz możliwość zapisu. W związku z tym nie można wykonać żadnej rzeczywistej pracy. Firma Microsoft zaleca, aby w miarę otrzymywania żądania anulowania otrzymać jak najszybciej, jak to możliwe. Jeśli usługa nie odpowiada na te wywołania interfejsu API w rozsądnym czasie, Service Fabric może wymusić zakończenie działania usługi. Zazwyczaj dzieje się to tylko podczas uaktualniania aplikacji lub podczas usuwania usługi. Ten limit czasu wynosi domyślnie 15 minut.
* Błędy w ścieżce `onCloseAsync()` powodują wywoływanie `onAbort()`. To wywołanie to Ostatnia szansa i Najlepsza okazja do wyczyszczenia i zwolnienia wszelkich zażądanych zasobów. Jest to zwykle wywoływane w przypadku wykrycia trwałego błędu w węźle lub gdy Service Fabric nie może niezawodnie zarządzać cyklem życia wystąpienia usługi ze względu na błędy wewnętrzne.
* `OnChangeRoleAsync()` jest wywoływana, gdy replika usługi stanowej zmienia rolę, na przykład na podstawową lub pomocniczą. Repliki podstawowe mają stan zapisu (można tworzyć i zapisywać w niezawodnych kolekcjach). Repliki pomocnicze uzyskują stan odczytu (mogą odczytywać tylko z istniejących niezawodnych kolekcji). Większość pracy w usłudze stanowej jest wykonywana w replice podstawowej. Repliki pomocnicze mogą wykonywać walidację, generowanie raportów, wyszukiwanie danych lub inne zadania tylko do odczytu.

## <a name="next-steps"></a>Następne kroki
* [Wprowadzenie do Reliable Services](service-fabric-reliable-services-introduction.md)
* [Reliable Services — Szybki Start](service-fabric-reliable-services-quick-start-java.md)


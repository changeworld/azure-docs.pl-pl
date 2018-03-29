---
title: Cykl życia niezawodne usługi sieć szkieletowa usług Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat zdarzenia cyklu życia w usługach niezawodnej usługi sieci szkieletowej.
services: service-fabric
documentationcenter: java
author: PavanKunapareddyMSFT
manager: timlt
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/30/2017
ms.author: pakunapa;
ms.openlocfilehash: 4270bf0b8002b5328241c6d31f399511fc38274e
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="reliable-services-lifecycle"></a>Cykl życia usług Reliable Services
> [!div class="op_single_selector"]
> * [C# w systemie Windows](service-fabric-reliable-services-lifecycle.md)
> * [Java w systemie Linux](service-fabric-reliable-services-lifecycle-java.md)
>
>

Niezawodne usługi jest jednym z modele programowania dostępne w sieci szkieletowej usług Azure. Poznawania cyklem życia niezawodne usługi, jest najważniejsza, aby zrozumieć zdarzenia podstawowe cyklu życia. Dokładne uporządkowania zdarzeń zależy od szczegółów konfiguracji. 

Ogólnie rzecz biorąc życia niezawodne usługi obejmuje następujące zdarzenia:

* Podczas uruchamiania:
  * Usługi są wykonane.
  * Usługi mają możliwość utworzenia i zwracać odbiorników zero lub więcej.
  * Wszystkie zwracane obiekty nasłuchujące są otwarte do komunikacji z usługą.
  * Usługi `runAsync` metoda jest wywoływana, dlatego usługi można wykonać długotrwałe lub pracy w tle.
* Podczas zamykania:
  * Token anulowania, który został przekazany do `runAsync` została anulowana, a odbiorniki są zamknięte.
  * Sam obiekt usługi jest niszczone.

Kolejność zdarzeń w niezawodnej usługi mogą się nieznacznie zmieniać w zależności od tego, czy usługa niezawodnej jest bezstanowych i stanowych. 

Ponadto dla stanowych usług muszą spełnić scenariusz podstawowy wymiany. Podczas tej sekwencji rolę podstawową jest przenoszona do innej repliki (lub wróci) bez zamknięcie usługi. 

Na koniec trzeba myśleć o warunkach błędu lub niepowodzenie.

## <a name="stateless-service-startup"></a>Uruchamianie usługi bezstanowej
Cykl życia usługi bezstanowej jest bardzo prosta. Poniżej przedstawiono kolejność zdarzeń:

1. Usługa jest tworzony.
2. Zdarzenia te występują równolegle:
    - `StatelessService.createServiceInstanceListeners()` zostanie wywołany, i dowolne zwrócił odbiorników są otwarte. `CommunicationListener.openAsync()` jest wywoływana na każdym odbiornika.
    - Usługi `runAsync` — metoda (`StatelessService.runAsync()`) jest wywoływana.
3. Jeśli jest obecny, własne usługi `onOpenAsync` metoda jest wywoływana. W szczególności `StatelessService.onOpenAsync()` jest wywoływana. Jest to rzadko zastąpienia, ale jest ona dostępna.

Ważne jest, aby należy pamiętać, że nie kolejność nie między wywołania do tworzenia i otwierania odbiorniki i wywołania w celu `runAsync`. Odbiorniki może zostać otwarta przed `runAsync` została uruchomiona. Podobnie `runAsync` może być wywołana przed odbiorników komunikacji są otwarte, czy przed nawet zostały wykonane. Jeśli wymagana jest każdej synchronizacji, musi zostać wykonana przez implementujący. Poniżej przedstawiono typowe rozwiązania:

* Czasami odbiorników nie może działać, dopóki innych informacji o utworzeniu lub innych praca jest wykonywana. Dla usług bezstanowych, które pracy zwykle może odbywać się w Konstruktorze tej usługi. Mogą to robić podczas `createServiceInstanceListeners()` wywołać, lub jako część konstrukcji samego odbiornika.
* Czasami kod w `runAsync` nie można uruchomić do momentu odbiorniki są otwarte. W takim przypadku niezbędne jest dodatkowe koordynacji. Dodaj flagę w odbiorniki jest typowe rozwiązania. Flaga wskazuje, kiedy odbiorniki zostało ukończone. `runAsync` Metoda sprawdza to przed kontynuowaniem pracy rzeczywistej.

## <a name="stateless-service-shutdown"></a>Zamykanie usługi bezstanowej
Podczas zamykania usługi bezstanowej, tego samego wzorca jest po, ale odwrotnie:

1. Zdarzenia te występują równolegle:
    - Są zamykane wszystkie otwarte odbiorników. `CommunicationListener.closeAsync()` jest wywoływana na każdym odbiornika.
    - Token anulowania, który został przekazany do `runAsync()` zostało anulowane. Sprawdzanie token anulowania `isCancelled` zwraca `true`i wywołuje tokenu `throwIfCancellationRequested` metoda zgłasza `CancellationException`.
2. Gdy `closeAsync()` zakończeniu na każdym odbiornika i `runAsync()` również zakończeniu usługi `StatelessService.onCloseAsync()` metoda jest wywoływana, jeśli jest obecny. Ponownie nie jest to typowe zastąpienie, ale może służyć do bezpiecznie zamknąć zasobów, Zatrzymaj przetwarzanie w tle, Zakończ zapisywanie stanu zewnętrznych lub zamknięcia w istniejących połączeń.
3. Po `StatelessService.onCloseAsync()` zakończeniu obiekt usługi jest niszczone.

## <a name="stateful-service-startup"></a>Uruchamianie usługi stanowej
Usługi stanowej mają wzorzec, który jest podobny do usług bezstanowych, kilka zmian.  Poniżej przedstawiono kolejność zdarzeń do uruchamiania usługi stanowej:

1. Usługa jest tworzony.
2. `StatefulServiceBase.onOpenAsync()` jest wywoływana. To wywołanie nie jest zazwyczaj zastępowany przez usługę.
3. Zdarzenia te występują równolegle:
    - `StatefulServiceBase.createServiceReplicaListeners()` jest wywoływane. 
      - Jeśli usługa jest usługą podstawowy, wszystkie zwracane obiekty nasłuchujące są otwarte. `CommunicationListener.openAsync()` jest wywoływana na każdym odbiornika.
      - Jeśli usługa jest usługą dodatkowej, tylko odbiorników oznaczona jako `listenOnSecondary = true` są otwarte. Odbiorniki otwarte na pomocnicze bazy danych jest mniej typowych.
    - Jeśli usługa jest obecnie podstawowym, usługa firmy `StatefulServiceBase.runAsync()` metoda jest wywoływana.
4. Po wszystkich replik odbiornika dla `openAsync()` wywołuje Zakończ i `runAsync()` jest nazywany `StatefulServiceBase.onChangeRoleAsync()` jest wywoływana. To wywołanie nie jest zazwyczaj zastępowany przez usługę.

Podobnie jak do usług bezstanowych, w usługi stanowej, nie ma żadnych koordynację między tymi kolejność, w którym są tworzone i otworzyć odbiorniki i kiedy `runAsync` jest wywoływana. Jeśli potrzebujesz koordynacji rozwiązania są znacznie takie same. Występuje jeden przypadek dodatkowe usługi stanowej. Powiedzieć, że wywołania, które przyjeździe odbiorników komunikacji wymagają informacje przechowywane w niektórych [niezawodnej kolekcje](service-fabric-reliable-services-reliable-collections.md). Ponieważ odbiorników komunikacji może zostać otwarta przed niezawodnej kolekcje są do odczytu lub zapisu, a przed `runAsync` rozpoczyna się, że niektóre dodatkowe koordynacji jest konieczne. Najprostsza i najczęściej używane rozwiązanie jest w przypadku odbiorników komunikacji zwrócić kod błędu. Klient korzysta z kodu błędu wiedzieć, aby ponowić żądanie.

## <a name="stateful-service-shutdown"></a>Zamknięcie usługi stanowej
Podobnie jak usługi bezstanowej zdarzenia cyklu życia podczas zamykania są takie same jak podczas uruchamiania, ale wycofane. Gdy trwa zamykanie usługi stanowej, wykonywane są następujące zdarzenia:

1. Zdarzenia te występują równolegle:
    - Są zamykane wszystkie otwarte odbiorników. `CommunicationListener.closeAsync()` jest wywoływana na każdym odbiornika.
    - Token anulowania, który został przekazany do `runAsync()` zostało anulowane. Wywołanie token anulowania `isCancelled()` metoda zwraca `true`i wywołuje tokenu `throwIfCancellationRequested()` metoda zgłasza `OperationCanceledException`.
2. Po `closeAsync()` zakończeniu na każdym odbiornika i `runAsync()` również zakończeniu usługi `StatefulServiceBase.onChangeRoleAsync()` jest wywoływana. To wywołanie nie jest zazwyczaj zastępowany przez usługę.

   > [!NOTE]  
   > Oczekiwanie na `runAsync` do zakończenia jest konieczne tylko wtedy, gdy ta replika jest repliki podstawowej.

3. Po `StatefulServiceBase.onChangeRoleAsync()` zakończeniu metody `StatefulServiceBase.onCloseAsync()` metoda jest wywoływana. To wywołanie jest rzadko zastąpienia, ale jest ona dostępna.
3. Po `StatefulServiceBase.onCloseAsync()` zakończeniu obiekt usługi jest niszczone.

## <a name="stateful-service-primary-swaps"></a>Zamienia głównej usługi stanowej
Po uruchomieniu usługi stanowej odbiorników komunikacji są otwarte i `runAsync` metoda jest wywoływana tylko dla podstawowego replik tej usługi stanowej. Replik pomocniczych są wykonane, ale nie kolejne wywołania w temacie. Po uruchomieniu usługi stanowej repliki, który jest obecnie podstawowej, można zmienić. Zdarzenia cyklu życia, które można zobaczyć stanowe repliki zależy od tego, czy jest repliki trwa obniżyć poziom jest podwyższany podczas wymiany.

### <a name="for-the-demoted-primary"></a>Dla którego poziom został obniżony podstawowego
Sieć szkieletowa usług musi repliką podstawową, która jest obniżenie poziomu, aby zatrzymać przetwarzanie komunikatów i zatrzymaj wszelkie Praca w tle. Ten krok jest podobny do podczas zamykania usługi. Jedną różnicą jest czy usługi nie jest niszczone lub zamknięty, ponieważ pozostaje pomocniczego. Wystąpią następujące zdarzenia:

1. Zdarzenia te występują równolegle:
    - Są zamykane wszystkie otwarte odbiorników. `CommunicationListener.closeAsync()` jest wywoływana na każdym odbiornika.
    - Token anulowania, który został przekazany do `runAsync()` zostało anulowane. Sprawdź token anulowania `isCancelled()` metoda zwraca `true`. Jeśli wywołuje tokenu `throwIfCancellationRequested()` metoda zgłasza `OperationCanceledException`.
2. Po `closeAsync()` zakończeniu na każdym odbiornika i `runAsync()` również zakończeniu usługi `StatefulServiceBase.onChangeRoleAsync()` jest wywoływana. To wywołanie nie jest zazwyczaj zastępowany przez usługę.

### <a name="for-the-promoted-secondary"></a>Dla awansowana pomocniczego
Podobnie sieci szkieletowej usług wymaga repliki pomocniczej, która zostanie podniesiona rozpocząć nasłuchiwania dla wiadomości w sieci i uruchomić wszystkie zadania w tle, które należy wykonać. Ten proces jest podobny do utworzenia usługi. Różnica polega na tym samego repliki już istnieje. Wystąpią następujące zdarzenia:

1. Zdarzenia te występują równolegle:
    - `StatefulServiceBase.createServiceReplicaListeners()` jest wywoływany i wszelkie zwrócił odbiorników są otwarte. `CommunicationListener.openAsync()` jest wywoływana na każdym odbiornika.
    - Usługi `StatefulServiceBase.runAsync()` metoda jest wywoływana.
2. Po wszystkich replik odbiornika dla `openAsync()` wywołuje Zakończ i `runAsync()` jest nazywany `StatefulServiceBase.onChangeRoleAsync()` jest wywoływana. To wywołanie nie jest zazwyczaj zastępowany przez usługę.

### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>Typowe problemy podczas zamykania usługi stanowej i obniżania poziomu podstawowego
Sieć szkieletowa usług zmienia podstawowej usługi stanowej kilka przyczyn. Najbardziej typowe przyczyny to [klastra, ponowne równoważenie](service-fabric-cluster-resource-manager-balancing.md) i [uaktualniania aplikacji](service-fabric-application-upgrade.md). Podczas tych czynności, jest ważne, czy usługa szanuje `cancellationToken`. Dotyczy to również podczas zamykania normalnego użytkowania, np. Jeśli usługa została usunięta.

Usługi, które nie obsługują prawidłowo anulowania mogą występować problemy z kilku. Te operacje są wolne, ponieważ usługi sieć szkieletowa czeka bezpiecznie zatrzymania usługi. Ostatecznie może to prowadzić do uaktualnienia nie powiodło się, które przekraczają limit czasu i wycofywania. Nie można uwzględnić token anulowania mogą dodatkowo powodować imbalanced klastrów. Klastry stają się równowagi ponieważ gorących węzłów. Jednak usługi nie rebalanced, ponieważ trwa zbyt długo, aby przenieść je w innym miejscu. 

Ponieważ usługi są stanowe, również jest prawdopodobne, że usługi używają [niezawodnej kolekcje](service-fabric-reliable-services-reliable-collections.md). W sieci szkieletowej usług podczas obniżania poziomu podstawowego jedną pierwszy co się stanie, jest odebraniu podstawowy stan do zapisu. Prowadzi to do drugiego zestawu problemy, które mogą mieć wpływ na cyklem życia usługi. Kolekcje zwracany wyjątki oparte na czas i czy jest przenoszony repliki lub zamykania. Należy poprawnie obsługę tych wyjątków. 

Wyjątki generowane przez sieć szkieletowa usług są trwałe [(`FabricException`)](https://docs.microsoft.com/en-us/java/api/system.fabric.exception) lub przejściowego [(`FabricTransientException`)](https://docs.microsoft.com/en-us/java/api/system.fabric.exception._fabric_transient_exception). Stałe wyjątki powinny być rejestrowane i zgłoszony. Wyjątki przejściowego można ponowić opartych na logice ponów próbę.

Ważnym elementem przetestowaniu i zweryfikowaniu niezawodne usługi jest obsługi wyjątków, które pochodzą od przy użyciu `ReliableCollections` w połączeniu z zdarzenia cyklu życia usługi. Firma Microsoft zaleca zawsze uruchamiaj usługi pod obciążeniem. Należy również wykonać uaktualnienia i [testowania chaos](service-fabric-controlled-chaos.md) przed wdrożeniem w środowisku produkcyjnym. Te podstawowe kroki pomocy, upewnij się, że usługa jest zaimplementowana poprawnie i poprawnie obsługi zdarzenia cyklu życia.

## <a name="notes-on-service-lifecycle"></a>Uwagi dotyczące cyklu życia usług
* Zarówno `runAsync()` — metoda i `createServiceInstanceListeners/createServiceReplicaListeners` wywołania są opcjonalne. Usługa może mieć jedną, obie lub nie. Na przykład, jeśli usługa jest jego pracy w odpowiedź na wywołania użytkownika, jest niepotrzebna go do zaimplementowania `runAsync()`. Wymagane są tylko odbiorników komunikacji i ich skojarzonego kodu.  Podobnie tworzenie i zwracanie odbiorników komunikacji jest opcjonalne. Usługa może mieć tylko praca w tle w celu, dlatego należy zaimplementować `runAsync()`.
* Jest on prawidłowy dla usługi, aby ukończyć `runAsync()` pomyślnie i z powrotem od niego. To nie jest traktowane jako warunek błędu. Reprezentuje Praca w tle zakończenia usługi. Dla stanowych usług niezawodne `runAsync()` będzie wywołana, jeśli usługa jest obniżenie poziomu z podstawowego, a następnie podwyższony do podstawowego.
* Jeśli zamknie usługę `runAsync()` przez zgłaszanie nieoczekiwany wyjątek, to jest błąd. Obiekt usługi jest zamknięta, a jest zgłaszany błąd kondycji.
* Mimo że nie ma żadnego limitu czasu na zwracanie z tych metod, natychmiast utracisz możliwość zapisu. W związku z tym nie można ukończyć rzeczywistą pracę. Firma Microsoft zaleca powrocie możliwie jak najszybciej po otrzymaniu żądania anulowania. Jeśli usługi nie odpowiadają na te wywołania interfejsu API w rozsądnym czasie, usługi sieć szkieletowa może wymusić przerwanie usługi. Zazwyczaj dzieje się tak tylko podczas uaktualniania aplikacji lub gdy usługa jest usuwana. Tego limitu czasu wynosi 15 minut domyślnie.
* Błędy w `onCloseAsync()` doprowadzi do ścieżki `onAbort()` wywoływane. To wywołanie jest ostatniej szansy, optymalnych możliwości na wyczyszczenie i zwolnić wszystkie zasoby, które mają one żądane usługi. Zazwyczaj jest to nazywane po wykryciu trwałego błędu w węźle lub sieci szkieletowej usług nie można niezawodnie zarządzania cyklem życia wystąpienia usługi z powodu błędów wewnętrznych.
* `OnChangeRoleAsync()` jest wywoływane, gdy replika usługi stanowej jest zmiana roli, na przykład na podstawowy lub pomocniczy. Replik podstawowych podano stanu zapisu (dozwolone są do tworzenia i zapisywania niezawodnej kolekcji). Replik pomocniczych podano stanu (tylko można odczytać z istniejących zbiorach niezawodnej). Większość pracy w usługi stanowej odbywa się repliką podstawową. Replik pomocniczych można wykonać walidacji tylko do odczytu, generowanie raportów, wyszukiwania danych lub innych zadań tylko do odczytu.

## <a name="next-steps"></a>Kolejne kroki
* [Wprowadzenie do niezawodne usługi](service-fabric-reliable-services-introduction.md)
* [Niezawodne usługi Szybki Start](service-fabric-reliable-services-quick-start-java.md)


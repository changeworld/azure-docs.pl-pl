---
title: Azure cyklu życia usług Reliable Services usługi Service Fabric | Dokumentacja firmy Microsoft
description: Więcej informacji na temat zdarzenia cyklu życia usług Reliable Services usługi Service Fabric.
services: service-fabric
documentationcenter: java
author: PavanKunapareddyMSFT
manager: chackdan
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: 36c1ff2ace944d84120bf456060c7504170a814c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60772945"
---
# <a name="reliable-services-lifecycle"></a>Cykl życia usług Reliable Services
> [!div class="op_single_selector"]
> * [C# w systemie Windows](service-fabric-reliable-services-lifecycle.md)
> * [Java w systemie Linux](service-fabric-reliable-services-lifecycle-java.md)
>
>

Usług Reliable Services jest jednym z modeli programowania, która jest dostępna w usłudze Azure Service Fabric. Wiedzę na temat cyklu życia usług Reliable Services, jest najbardziej należy zrozumieć zdarzenia podstawowy cykl życia. Dokładne uporządkowania zdarzeń zależy od szczegółów konfiguracji. 

Ogólnie rzecz biorąc cykl życia usług Reliable Services obejmuje następujące zdarzenia:

* Podczas uruchamiania:
  * Usługi są konstruowane.
  * Usługi mają możliwość utworzenia i zwracają zero lub więcej odbiorników.
  * Żadnych odbiorników zwracane są otwarte dla komunikacji z usługą.
  * Usługa `runAsync` metoda jest wywoływana, dzięki czemu usługa można wykonać długotrwałe lub pracy w tle.
* Podczas zamykania:
  * Token anulowania, który został przekazany do `runAsync` zostanie anulowane, a odbiorniki są zamknięte.
  * Sam obiekt usługi jest usuwane.

Kolejność zdarzeń w usług Reliable Services mogą ulec zmianie nieco w zależności od tego, czy usługi reliable service bezstanowe lub stanowe. 

Ponadto dla usług stanowych muszą spełnić scenariusz podstawowego wymiany. Podczas tej sekwencji jest przekazywana do innej repliki rolę podstawowego (lub wróci) bez zamknięcie usługi. 

Na koniec musisz myśleć o warunkach błędów lub niepowodzenie.

## <a name="stateless-service-startup"></a>Uruchamianie usługi bezstanowej
Cykl życia usługi bezstanowej jest dość prosta. Poniżej przedstawiono kolejność zdarzeń:

1. Usługa jest konstruowany.
2. Te zdarzenia występują równolegle:
    - `StatelessService.createServiceInstanceListeners()` zostanie wywołana, i dowolnego zwrócony odbiorniki są otwarte. `CommunicationListener.openAsync()` jest wywoływana w każdej odbiornika.
    - Usługa `runAsync` — metoda (`StatelessService.runAsync()`) jest wywoływana.
3. Jeśli jest obecny, własne usługi `onOpenAsync` metoda jest wywoływana. W szczególności `StatelessService.onOpenAsync()` jest wywoływana. To zastąpienie niczym niezwykłym, ale jest on dostępny.

Ważne jest, aby należy pamiętać, że nie kolejności wywołań, aby utworzyć i otworzyć odbiorniki i wywołania `runAsync`. Odbiorniki może zostać otwarta przed `runAsync` została uruchomiona. Podobnie `runAsync` może być wywołana przed odbiorników komunikacji są otwarte lub przed nawet został skonstruowany. Wszelkie synchronizacja jest wymagana, musi odbywać przez implementujący. Poniżej przedstawiono niektóre typowe rozwiązania:

* Czasami odbiorniki nie będzie działać do czasu utworzeniu innych informacji lub inne zadania są wykonywane. Przypadku usług bezstanowych, które pracy zwykle może odbywać się w Konstruktorze tej usługi. Można to zrobić w trakcie `createServiceInstanceListeners()` wywołać, lub w ramach konstrukcji samego odbiornika.
* Czasami kodu w `runAsync` nie będą naliczane, dopóki odbiorniki są otwarte. W tym przypadku dodatkowe koordynacja jest to konieczne. Typowym rozwiązaniem jest dodanie flagi w odbiorników. Flaga wskazuje, kiedy odbiorniki została zakończona. `runAsync` Metoda sprawdza, czy to przed kontynuowaniem faktyczną pracę.

## <a name="stateless-service-shutdown"></a>Zamykanie usługi bezstanowej
Podczas zamykania usługę bezstanową, tego samego wzorca jest obserwowane, ale w odwrotnej kolejności:

1. Te zdarzenia występują równolegle:
    - Są zamykane wszystkie otwarte odbiorników. `CommunicationListener.closeAsync()` jest wywoływana w każdej odbiornika.
    - Token anulowania, który został przekazany do `runAsync()` zostało anulowane. Sprawdzanie token anulowania `isCancelled` właściwość zwraca `true`i jeśli wywołano tokenu `throwIfCancellationRequested` metoda zgłasza wyjątek `CancellationException`.
2. Gdy `closeAsync()` kończy się w dniu każdego odbiornika i `runAsync()` również zakończeniu usługi `StatelessService.onCloseAsync()` metoda jest wywoływana, jeśli jest obecny. Ponownie to nie jest przesłonięciem wspólnego, ale może służyć do bezpiecznie zamknąć zasoby, zatrzymanie przetwarzania w tle, Zakończ zapisywanie stanu zewnętrznego lub zamknięcia w istniejących połączeń.
3. Po `StatelessService.onCloseAsync()` zostanie zakończone, obiekt usługi jest usuwane.

## <a name="stateful-service-startup"></a>Uruchamianie usługi stanowej
Usługi stanowe mają wzorzec, który jest podobny do bezstanowych usług, za pomocą kilku zmian.  Poniżej przedstawiono kolejność zdarzeń do uruchamiania usługi stanowej:

1. Usługa jest konstruowany.
2. `StatefulServiceBase.onOpenAsync()` jest wywoływana. To wywołanie nie jest powszechnie zastępowane w usłudze.
3. Te zdarzenia występują równolegle:
    - `StatefulServiceBase.createServiceReplicaListeners()` jest wywoływane. 
      - Jeśli usługa jest podstawowej usługi, wszystkie detektory zwracane są otwarte. `CommunicationListener.openAsync()` jest wywoływana w każdej odbiornika.
      - Jeśli usługa jest inna usługa, tylko odbiorników oznaczone jako `listenOnSecondary = true` są otwarte. Mniej typowe jest posiadanie odbiorników, które są otwarte na pomocnicze bazy danych.
    - Jeśli usługa jest obecnie podstawowym, usługa firmy `StatefulServiceBase.runAsync()` metoda jest wywoływana.
4. Po wszystkich replik odbiornika `openAsync()` wywołuje Zakończ i `runAsync()` jest wywoływana, `StatefulServiceBase.onChangeRoleAsync()` jest wywoływana. To wywołanie nie jest powszechnie zastępowane w usłudze.

Podobnie jak usług bezstanowych, usługi stanowe, jest nie koordynacji między kolejność, w którym są tworzone i otwierane odbiorniki i kiedy `runAsync` jest wywoływana. Jeśli potrzebujesz koordynacji, te rozwiązania są bardzo podobne. Występuje jeden przypadek dodatkowe usługi stanowej. Załóżmy, że wywołania, które docierają odbiorników komunikacji wymagają informacje przechowywane wewnątrz niektórych [elementów Reliable Collections](service-fabric-reliable-services-reliable-collections.md). Ponieważ przed elementami Reliable Collections są do odczytu lub zapisywalnego może otworzyć odbiorników komunikacji i przed `runAsync` zostanie uruchomiony, niektóre dodatkowe koordynacja jest konieczna. To najprostszy i najbardziej typowe rozwiązanie dla odbiorników komunikacji zwrócić kod błędu. Klient korzysta z kodu błędu, aby ponowić próbę żądania.

## <a name="stateful-service-shutdown"></a>Zamykanie usługi stanowej
Podobnie jak w przypadku usług bezstanowych zdarzenia cyklu życia podczas zamykania są takie same jak podczas uruchamiania, ale odwrócony. Gdy trwa zamykanie usługi stanowej, zachodzą następujące zdarzenia:

1. Te zdarzenia występują równolegle:
    - Są zamykane wszystkie otwarte odbiorników. `CommunicationListener.closeAsync()` jest wywoływana w każdej odbiornika.
    - Token anulowania, który został przekazany do `runAsync()` zostało anulowane. Wywołania z tokenem anulowania `isCancelled()` metoda zwraca `true`i jeśli wywołano tokenu `throwIfCancellationRequested()` metoda zgłasza wyjątek `OperationCanceledException`.
2. Po `closeAsync()` kończy się w dniu każdego odbiornika i `runAsync()` również zakończeniu usługi `StatefulServiceBase.onChangeRoleAsync()` jest wywoływana. To wywołanie nie jest powszechnie zastępowane w usłudze.

   > [!NOTE]  
   > Oczekiwanie na `runAsync` zakończenie jest konieczne tylko wtedy, gdy ta replika jest repliką podstawową.

3. Po `StatefulServiceBase.onChangeRoleAsync()` metoda zostanie zakończone, `StatefulServiceBase.onCloseAsync()` metoda jest wywoływana. To wywołanie jest przesłonięciem niczym niezwykłym, ale jest on dostępny.
3. Po `StatefulServiceBase.onCloseAsync()` zostanie zakończone, obiekt usługi jest usuwane.

## <a name="stateful-service-primary-swaps"></a>Zamienia podstawowej usługi stanowej
Po uruchomieniu usługi stanowej odbiorników komunikacji są otwarte i `runAsync` metoda jest wywoływana tylko w przypadku repliki podstawowej tego usług stanowych. Repliki pomocnicze są tworzone, ale wyświetlenie nie dalsze wezwania. Po uruchomieniu usługi stanowej repliki, który jest aktualnie podstawowego można zmienić. Zdarzenia cyklu życia, widocznych dla stanowych repliki zależy od tego, czy jest replika jest obniżony podnoszony podczas wymiany.

### <a name="for-the-demoted-primary"></a>Dla podstawowego o obniżonym poziomie
Usługa Service Fabric wymaga repliką podstawową, która jest obniżenie poziomu, aby zatrzymać przetwarzanie komunikatów i zatrzymaj wszelkie prace w tle. Ten krok jest podobny do po zamknięciu usługi. Jedną różnicą jest czy usługi nie są niszczone lub closed, ponieważ pozostaje pomocniczego. Wystąpią następujące zdarzenia:

1. Te zdarzenia występują równolegle:
    - Są zamykane wszystkie otwarte odbiorników. `CommunicationListener.closeAsync()` jest wywoływana w każdej odbiornika.
    - Token anulowania, który został przekazany do `runAsync()` zostało anulowane. Sprawdź token anulowania `isCancelled()` metoda zwraca `true`. Jeśli wywołano tokenu `throwIfCancellationRequested()` metoda zgłasza wyjątek `OperationCanceledException`.
2. Po `closeAsync()` kończy się w dniu każdego odbiornika i `runAsync()` również zakończeniu usługi `StatefulServiceBase.onChangeRoleAsync()` jest wywoływana. To wywołanie nie jest powszechnie zastępowane w usłudze.

### <a name="for-the-promoted-secondary"></a>Dla pomocniczego o podwyższonym poziomie
Podobnie Usługa Service Fabric wymaga repliki pomocniczej, która zostanie podniesiona rozpocząć nasłuchiwania wiadomości na łączu i uruchomić wszystkie zadania w tle, które należy wykonać. Ten proces jest podobny do utworzenia usługi. Różnica polega na tym samego repliki już istnieje. Wystąpią następujące zdarzenia:

1. Te zdarzenia występują równolegle:
    - `StatefulServiceBase.createServiceReplicaListeners()` jest wywołany i zwrócony dowolnej, odbiorniki są otwarte. `CommunicationListener.openAsync()` jest wywoływana w każdej odbiornika.
    - Usługa `StatefulServiceBase.runAsync()` metoda jest wywoływana.
2. Po wszystkich replik odbiornika `openAsync()` wywołuje Zakończ i `runAsync()` jest wywoływana, `StatefulServiceBase.onChangeRoleAsync()` jest wywoływana. To wywołanie nie jest powszechnie zastępowane w usłudze.

### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>Typowe problemy występujące podczas zamykania usługi stanowej i obniżanie poziomu podstawowego
Usługa Service Fabric zmienia podstawowej usługi stanowej kilka przyczyn. Najbardziej typowe przyczyny to [klastra, ponowne równoważenie](service-fabric-cluster-resource-manager-balancing.md) i [uaktualnienia aplikacji](service-fabric-application-upgrade.md). Podczas tych czynności jest ważne, że usługa szanuje `cancellationToken`. Dotyczy to również podczas zamykania normalnego użytkowania, np. Jeśli usługa została usunięta.

Kilka problemów możesz skorzystać z usług, które nie obsługują anulowania nie pozostawia żadnych śladów. Te operacje są powolne, ponieważ usługa Service Fabric czeka dla usług, które można bezpiecznie zatrzymać. Ostatecznie może to prowadzić do uaktualnienia nie powiodło się, że przekroczenie limitu czasu i wycofywania. Nie można honorować token anulowania mogą dodatkowo powodować imbalanced klastrów. Klastry stać się niezrównoważone, ponieważ węzły Pobierz gorąca. Jednak usługi nie można ponownie zbilansowana, ponieważ trwa zbyt długo, aby przenieść je w innym miejscu. 

Ponieważ usługi są stanowe, również jest prawdopodobne, że usługi używają [elementów Reliable Collections](service-fabric-reliable-services-reliable-collections.md). W usłudze Service Fabric gdy obniżenie poziomu podstawowego jedną z pierwszych czynności, które się dzieje jest odebraniu dostęp do zapisu do podstawowej stanu. Prowadzi to do drugiego zestawu problemy, które mogłyby wpłynąć na cyklu życia usług. Kolekcje, wyjątki zwracane na podstawie czas i czy jest przenoszony repliki lub zamykania. Jest to ważne, aby prawidłowo obsługiwać te wyjątki. 

Wyjątki generowane przez usługę Service Fabric są trwałe [(`FabricException`)](https://docs.microsoft.com/java/api/system.fabric.exception) lub przejściowy [(`FabricTransientException`)](https://docs.microsoft.com/java/api/system.fabric.exception.fabrictransientexception). Stałe wyjątki powinien być rejestrowane i zgłaszany. Przejściowych wyjątków mogą być ponawiane w oparciu o logikę ponawiania próby.

Ważnym elementem testowania i sprawdzania poprawności usług Reliable Services jest obsługi wyjątków, które pochodzą z przy użyciu `ReliableCollections` w połączeniu z zdarzeń cyklu życia usługi. Firma Microsoft zaleca, zawsze uruchamiaj usługi pod obciążeniem. Należy również wykonywać uaktualnienia i [testowania chaos](service-fabric-controlled-chaos.md) przed wdrożeniem w środowisku produkcyjnym. Te proste kroki, pomoc, upewnij się, że usługa jest implementowana prawidłowo i poprawnie obsługi zdarzeń cyklu życia.

## <a name="notes-on-service-lifecycle"></a>Uwagi dotyczące cyklu życia usług
* Zarówno `runAsync()` metody i `createServiceInstanceListeners/createServiceReplicaListeners` wywołania są opcjonalne. Usługa może mieć jedną, obie lub nie. Na przykład, jeśli usługa ma swoją pracę w odpowiedzi na zaproszenie użytkownika, nie ma potrzeby jego implementacji `runAsync()`. Tylko odbiorników komunikacji i ich skojarzonego kodu są niezbędne.  Podobnie tworzenie i zwracanie odbiorników komunikacji jest opcjonalne. Usługa może być tylko tła zadania do wykonania, więc tylko musi implementować `runAsync()`.
* Jest on prawidłowy dla usługi w celu ukończenia `runAsync()` pomyślnie i z powrotem z niego. To nie jest uznawana za warunek błędu. Reprezentuje pracę w tle zakończenia usługi. Dla stanowych usług Reliable Services `runAsync()` będzie wywołana, jeśli usługa jest obniżony z podstawowego, a następnie podwyższony do podstawowego.
* Jeśli Usługa zamyka `runAsync()` zgłaszając nieoczekiwany wyjątek, to jest błąd. Obiekt usługi zostanie zamknięta i jest zgłaszany błąd kondycji.
* Mimo że nie ma żadnego limitu czasu, po powrocie z tych metod, natychmiast utracą możliwość pisania. W związku z tym nie można ukończyć wszelkie faktyczną pracę. Firma Microsoft zaleca powrocie możliwie jak najszybciej po otrzymaniu żądania anulowania. Jeśli usługa nie odpowie na te wywołania interfejsu API w rozsądnym czasie, Usługa Service Fabric może wymusić zakończyć korzystanie z usługi. Zwykle dzieje się tak tylko podczas uaktualnień aplikacji lub jeśli usługa jest usuwana. Limit czasu wynosi 15 minut, domyślnie.
* Błędy w `onCloseAsync()` doprowadzić do ścieżki `onAbort()` wywoływana. To wywołanie jest ostatniej szansy, optymalny szansy sprzedaży usługi oczyszczenia i zwolnić wszystkie zasoby, które mają one zgłoszone. Zazwyczaj jest to nazywane po wykryciu trwałych błędów w węźle lub w przypadku, gdy Usługa Service Fabric niezawodnie nie może zarządzać życia wystąpienia usługi z powodu błędów wewnętrznych.
* `OnChangeRoleAsync()` jest wywoływana, gdy replika usługi stanowej jest zmiana roli, na przykład podstawowy lub pomocniczy. Replikami podstawowymi otrzymują stanu zapisu (są dozwolone do tworzenia i zapisu do elementów Reliable Collections). Repliki pomocnicze są podane Stan odczytu (tylko może czytać z istniejących elementów Reliable Collections). Większość pracy w stanowej usłudze odbywa się w replice podstawowej. Repliki pomocnicze można wykonywać sprawdzanie poprawności tylko do odczytu, generowania raportu, wyszukiwania danych lub innych zadań tylko do odczytu.

## <a name="next-steps"></a>Kolejne kroki
* [Wprowadzenie do usług Reliable Services](service-fabric-reliable-services-introduction.md)
* [Niezawodne usługi Szybki Start](service-fabric-reliable-services-quick-start-java.md)


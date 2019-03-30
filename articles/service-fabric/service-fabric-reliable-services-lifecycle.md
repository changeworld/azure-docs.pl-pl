---
title: Przegląd cyklu życia usług Reliable Services usługi Azure Service Fabric | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o zdarzeniach inny cykl życia usług Reliable Services usługi Service Fabric
services: Service-Fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: vturecek;
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: ebc7aec63b34630b606178aa17e2ae7fdd0fc87f
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58669612"
---
# <a name="reliable-services-lifecycle-overview"></a>Omówienie cyklu życia usług Reliable Services
> [!div class="op_single_selector"]
> * [C# w systemie Windows](service-fabric-reliable-services-lifecycle.md)
> * [Java w systemie Linux](service-fabric-reliable-services-lifecycle-java.md)
>
>

Jeśli myślisz o cykle życia usług Reliable Services usługi Azure Service Fabric, podstawowe informacje o cyklu życia są dla Ciebie najważniejsze. Ogólnie rzecz biorąc cyklu życia obejmuje następujące funkcje:

- Podczas uruchamiania:
  - Usługi są konstruowane.
  - Usługi mają możliwość utworzenia i zwracają zero lub więcej odbiorników.
  - Żadnych odbiorników zwracane są otwarte, zezwolenie na komunikację z usługą.
  - Usługa **RunAsync** metoda jest wywoływana, dzięki czemu usługa wykonywanie zadań długotrwałych lub Praca w tle.
- Podczas zamykania:
  - Token anulowania jest przekazywany do **RunAsync** zostanie anulowane, a odbiorniki są zamknięte.
  - Po odbiorniki zamknąć, sam obiekt usługi jest usuwane.

Brak szczegółów wokół dokładnie kolejność tych zdarzeń. Kolejność zdarzeń można zmienić nieco w zależności od tego, czy usługi Reliable Service bezstanowe lub stanowe. Ponadto dla usług stanowych, firma Microsoft musi obsłużyć scenariusz podstawowego wymiany. Podczas tej sekwencji jest przekazywana do innej repliki rolę podstawowego (lub wróci) bez zamknięcie usługi. Na koniec należy naszym zdaniem o warunkach błędów lub niepowodzenie.

## <a name="stateless-service-startup"></a>Uruchamianie usługi bezstanowej
Cykl życia usługi bezstanowej jest bardzo proste. Poniżej przedstawiono kolejność zdarzeń:

1. Usługa jest konstruowany.
2. Następnie w sposób równoległy, dzieją się dwie rzeczy:
    - `StatelessService.CreateServiceInstanceListeners()` jest wywołany i zwrócony dowolnej, odbiorniki są otwarte. `ICommunicationListener.OpenAsync()` jest wywoływana w każdej odbiornika.
    - Usługa `StatelessService.RunAsync()` metoda jest wywoływana.
3. Jeśli jest obecny, usługi `StatelessService.OnOpenAsync()` metoda jest wywoływana. To wywołanie jest przesłonięciem niczym niezwykłym, ale jest on dostępny. W tej chwili można uruchomić zadania inicjowania usługi rozszerzonej.

Należy pamiętać, że nie ma żadnych kolejności wywołań, aby utworzyć i otworzyć odbiorniki i **RunAsync**. Odbiorniki można otworzyć przed **RunAsync** została uruchomiona. Podobnie, można wywołać **RunAsync** przed odbiorników komunikacji są otwarte lub nawet skonstruowany. Jeśli wymagana jest żadnej synchronizacji, jego pozostanie w charakterze ćwiczenia implementujący. Poniżej przedstawiono niektóre typowe rozwiązania:

  - Czasami odbiorniki nie będzie działać do czasu niektóre informacje o utworzeniu lub praca jest wykonywana. W przypadku usług bezstanowych, które zazwyczaj pracy można wykonać w innych lokalizacjach, takich jak następujące: 
    - W Konstruktorze tej usługi.
    - Podczas `CreateServiceInstanceListeners()` wywołania.
    - W ramach tworzenia odbiornika, sam.
  - Czasami kodu w **RunAsync** nie zaczyna się dopóki odbiorniki są otwarte. W tym przypadku dodatkowe koordynacja jest to konieczne. Jednym z typowych rozwiązań jest flaga w ramach odbiorniki, która wskazuje, kiedy została zakończona. Ta flaga jest sprawdzany **RunAsync** przed kontynuowaniem faktyczną pracę.

## <a name="stateless-service-shutdown"></a>Zamykanie usługi bezstanowej
Wyłączaniem usługę bezstanową, tego samego wzorca występuje tylko w odwrotnej kolejności:

1. Równoległe:
    - Są zamykane wszystkie otwarte odbiorników. `ICommunicationListener.CloseAsync()` jest wywoływana w każdej odbiornika.
    - Token anulowania jest przekazywany do `RunAsync()` zostało anulowane. Sprawdź token anulowania `IsCancellationRequested` właściwość zwraca wartość true, a o nazwie tokenu `ThrowIfCancellationRequested` metoda zgłasza wyjątek `OperationCanceledException`.
2. Po `CloseAsync()` kończy się w dniu każdego odbiornika i `RunAsync()` również zakończeniu usługi `StatelessService.OnCloseAsync()` metoda jest wywoływana, jeśli jest obecny.  OnCloseAsync jest wywoływana, gdy wystąpienie usługi bezstanowej zamierza zostanie poprawnie zamknięte. Taka sytuacja może wystąpić, gdy kodu usługi jest uaktualniany, wystąpienie usługi jest przenoszony z powodu równoważenia obciążenia lub wykrycia błędu przejściowego. Jest niczym niezwykłym, aby zastąpić `StatelessService.OnCloseAsync()`, ale można bezpiecznie zamknąć zasoby, zatrzymanie przetwarzania w tle, Zakończ zapisywanie stanu zewnętrznego lub zamknięcia w istniejących połączeń.
3. Po `StatelessService.OnCloseAsync()` zostanie zakończone, obiekt usługi jest usuwane.

## <a name="stateful-service-startup"></a>Uruchamianie usługi stanowej
Usługi stanowe mają podobny wzorzec do bezstanowych usług, za pomocą kilku zmian. Na rozpoczęcie usługi stanowej, kolejność zdarzeń jest następująca:

1. Usługa jest konstruowany.
2. `StatefulServiceBase.OnOpenAsync()` jest wywoływana. To wywołanie nie jest powszechnie zastępowane w usłudze.
3. Równolegle się zdarzyć następujących czynności:
    - `StatefulServiceBase.CreateServiceReplicaListeners()` jest wywoływane. 
      - Jeśli usługa jest podstawowej usługi, wszystkie detektory zwracane są otwarte. `ICommunicationListener.OpenAsync()` jest wywoływana w każdej odbiornika.
      - Jeśli usługa jest inna usługa, tylko te odbiorniki oznaczone jako `ListenOnSecondary = true` są otwarte. Mniej typowe jest posiadanie odbiorników, które są otwarte na pomocnicze bazy danych.
    - Jeśli usługa jest obecnie podstawowym, usługa firmy `StatefulServiceBase.RunAsync()` metoda jest wywoływana.
4. Po wszystkich replik odbiornika `OpenAsync()` wywołuje Zakończ i `RunAsync()` jest wywoływana, `StatefulServiceBase.OnChangeRoleAsync()` jest wywoływana. To wywołanie nie jest powszechnie zastępowane w usłudze.

Podobnie jak usług bezstanowych, nie zachodzi koordynacja między kolejność, w którym są tworzone i otwierane odbiorniki i kiedy **RunAsync** jest wywoływana. Jeśli potrzebujesz koordynacji, te rozwiązania są bardzo podobne. Istnieje jeden przypadek dodatkowe usługi stanowej. Załóżmy, że wywołania, które docierają odbiorników komunikacji wymagają informacje przechowywane wewnątrz niektórych [elementów Reliable Collections](service-fabric-reliable-services-reliable-collections.md).

   > [!NOTE]  
   > Ponieważ przed elementów reliable collections są do odczytu lub zapisywalnego można otworzyć odbiorników komunikacji i przed **RunAsync** można uruchomić niektórych dodatkowych koordynacja jest konieczna. To najprostszy i najbardziej typowe rozwiązanie dla odbiorników komunikacji zwrócić kod błędu, używanych przez klienta, aby ponowić próbę żądania.

## <a name="stateful-service-shutdown"></a>Zamykanie usługi stanowej
Podobnie jak w przypadku usług bezstanowych zdarzenia cyklu życia podczas zamykania są takie same jak podczas uruchamiania, ale odwrócony. Gdy trwa zamykanie usługi stanowej, zachodzą następujące zdarzenia:

1. Równoległe:
    - Są zamykane wszystkie otwarte odbiorników. `ICommunicationListener.CloseAsync()` jest wywoływana w każdej odbiornika.
    - Token anulowania jest przekazywany do `RunAsync()` zostało anulowane. Sprawdź token anulowania `IsCancellationRequested` właściwość zwraca wartość true, a o nazwie tokenu `ThrowIfCancellationRequested` metoda zgłasza wyjątek `OperationCanceledException`.
2. Po `CloseAsync()` kończy się w dniu każdego odbiornika i `RunAsync()` również zakończeniu usługi `StatefulServiceBase.OnChangeRoleAsync()` jest wywoływana. To wywołanie nie jest powszechnie zastępowane w usłudze.

   > [!NOTE]  
   > Trzeba czekać na **RunAsync** na zakończenie jest wymagane tylko, jeśli ta replika jest repliką podstawową.

3. Po `StatefulServiceBase.OnChangeRoleAsync()` metoda zostanie zakończone, `StatefulServiceBase.OnCloseAsync()` metoda jest wywoływana. To wywołanie jest przesłonięciem niczym niezwykłym, ale jest on dostępny.
3. Po `StatefulServiceBase.OnCloseAsync()` zostanie zakończone, obiekt usługi jest usuwane.

## <a name="stateful-service-primary-swaps"></a>Zamiany podstawowej usługi stanowej
Po uruchomieniu usługi stanowej replikami podstawowymi tego stanowych usług ma ich odbiorników komunikacji otwarte i ich **RunAsync** metodę o nazwie. Repliki pomocnicze są tworzone, ale wyświetlenie nie dalsze wezwania. Po uruchomieniu usługi stanowej repliki, która jest obecnie serwerem podstawowym można zmienić w wyniku błędu lub w klastrze równoważenia optymalizacji. Co to znaczy w warunkach użytkowania zdarzenia cyklu życia, widocznych dla repliki Zachowanie, które widzi stanowych repliki zależy od tego, czy jest replika jest obniżony podnoszony podczas wymiany.

### <a name="for-the-primary-thats-demoted"></a>Dla podstawowego, który jest obniżony
Dla repliki podstawowej, która jest obniżony usługi Service Fabric wymaga tej repliki, aby zatrzymać przetwarzanie komunikatów, a następnie zamknij wszelkie prace tła, których wykonywanie operacji. W wyniku tego kroku wygląda tak, jak po zamknięciu usługi. Jeden różnica polega na tym, usługa nie jest niszczone lub zamknięta, ponieważ pozostaje pomocniczego. Następujące interfejsy API są nazywane:

1. Równoległe:
    - Są zamykane wszystkie otwarte odbiorników. `ICommunicationListener.CloseAsync()` jest wywoływana w każdej odbiornika.
    - Token anulowania jest przekazywany do `RunAsync()` zostało anulowane. Sprawdź token anulowania `IsCancellationRequested` właściwość zwraca wartość true, a o nazwie tokenu `ThrowIfCancellationRequested` metoda zgłasza wyjątek `OperationCanceledException`.
2. Po `CloseAsync()` kończy się w dniu każdego odbiornika i `RunAsync()` również zakończeniu usługi `StatefulServiceBase.OnChangeRoleAsync()` jest wywoływana. To wywołanie nie jest powszechnie zastępowane w usłudze.

### <a name="for-the-secondary-thats-promoted"></a>Dla dodatkowej, która jest podwyższany
Podobnie Usługa Service Fabric wymaga repliki pomocniczej, która zostanie podniesiona do nasłuchiwania wiadomości na łączu rozpoczęcie i wszelkie potrzebne do ukończenia zadania w tle. W wyniku tego procesu wygląda tak, jak podczas tworzenia usługi z tą różnicą, że sam repliki już istnieje. Następujące interfejsy API są nazywane:

1. Równoległe:
    - `StatefulServiceBase.CreateServiceReplicaListeners()` jest wywołany i zwrócony dowolnej, odbiorniki są otwarte. `ICommunicationListener.OpenAsync()` jest wywoływana w każdej odbiornika.
    - Usługa `StatefulServiceBase.RunAsync()` metoda jest wywoływana.
2. Po wszystkich replik odbiornika `OpenAsync()` wywołuje Zakończ i `RunAsync()` jest wywoływana, `StatefulServiceBase.OnChangeRoleAsync()` jest wywoływana. To wywołanie nie jest powszechnie zastępowane w usłudze.

### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>Typowe problemy występujące podczas zamykania usługi stanowej i obniżanie poziomu podstawowego
Usługa Service Fabric zmienia podstawowego stanowej usługi z różnych powodów. Najczęściej są [klastra, ponowne równoważenie](service-fabric-cluster-resource-manager-balancing.md) i [uaktualnienia aplikacji](service-fabric-application-upgrade.md). Podczas tych czynności (a także podczas normalnej pracy zamykania systemu, takie jak zostanie wyświetlony, jeśli usługa została usunięta), jest ważne, że usługa przestrzegać `CancellationToken`. 

Usługi, które nie obsługują anulowania nie pozostawia żadnych śladów może wystąpić kilka problemów. Te operacje są powolne, ponieważ usługa Service Fabric czeka dla usług, które można bezpiecznie zatrzymać. Ostatecznie to spowodować niepowodzenie uaktualnienia tego limitu czasu i wycofać. Nie można honorować token anulowania powodują imbalanced klastrów. Klastry stać się niezrównoważone, ponieważ węzły uzyskać dostępu, ale usługi nie można ponownie zbilansowana, ponieważ trwa zbyt długo, aby przenieść je w innym miejscu. 

Ponieważ usługi są stanowe, również jest prawdopodobne, że używają [elementów Reliable Collections](service-fabric-reliable-services-reliable-collections.md). W usłudze Service Fabric gdy obniżenie poziomu podstawowego jedną z pierwszych czynności, które się dzieje jest odebraniu dostęp do zapisu do podstawowej stanu. Prowadzi to do drugiego zestawu problemy, które mogą wpłynąć na cyklu życia usług. Kolekcje, wyjątki zwracane na podstawie czas i czy jest przenoszony repliki lub zamykania. Te wyjątki powinny być traktowane jako poprawnie. Wyjątki generowane przez usługę Service Fabric można podzielić na trwałe [(`FabricException`)](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception?view=azure-dotnet) i przejściowych [(`FabricTransientException`)](https://docs.microsoft.com/dotnet/api/system.fabric.fabrictransientexception?view=azure-dotnet) kategorii. Stałe wyjątki powinien być rejestrowane i zgłaszany podczas przejściowych wyjątków mogą być ponawiane w oparciu o logikę ponawiania prób.

Obsługa wyjątków, które pochodzą z użytkowania `ReliableCollections` w połączeniu z zdarzeń cyklu życia usługi jest ważną częścią testowania i sprawdzania poprawności niezawodnej usługi. Firma Microsoft zaleca, zawsze uruchamiaj usługi w warunkach obciążenia podczas przeprowadzania uaktualnienia i [testowania chaos](service-fabric-controlled-chaos.md) przed wdrożeniem w środowisku produkcyjnym. Te proste kroki, pomoc, upewnij się, że usługa jest poprawnie zaimplementowana poprawnie obsługuje zdarzenia cyklu życia.


## <a name="notes-on-the-service-lifecycle"></a>Uwagi dotyczące cyklu życia usług
  - Zarówno `RunAsync()` metody i `CreateServiceReplicaListeners/CreateServiceInstanceListeners` wywołania są opcjonalne. Usługa może mieć jedną z ich, obie lub nie. Na przykład, jeśli usługa ma swoją pracę w odpowiedzi na zaproszenie użytkownika, nie ma potrzeby jego implementacji `RunAsync()`. Tylko odbiorników komunikacji i ich skojarzonego kodu są niezbędne. Podobnie, tworzenie i zwracanie odbiorników komunikacji jest opcjonalne, ponieważ usługa może mieć tylko robić prace, tła i dlatego tylko musi implementować `RunAsync()`.
  - Jest on prawidłowy dla usługi w celu ukończenia `RunAsync()` pomyślnie i z powrotem z niego. Kończenie pracy nie jest warunkiem błędu. Kończenie `RunAsync()` wskazuje, że praca w tle usługi została zakończona. Dla stanowych usług reliable services `RunAsync()` wywoływana jest ponownie, gdy replika jest obniżony z podstawowego do pomocniczej, a następnie podwyższony do podstawowego.
  - Jeśli Usługa zamyka `RunAsync()` zgłaszając nieoczekiwany wyjątek, powoduje błąd. Obiekt usługi zostanie zamknięta i jest zgłaszany błąd kondycji.
  - Mimo że nie ma żadnego limitu czasu, po powrocie z tych metod, natychmiast utracą możliwość zapisu do elementów Reliable Collections i dlatego nie można ukończyć wszelkie faktyczną pracę. Zalecane jest powrocie możliwie jak najszybciej po otrzymaniu żądania anulowania. Jeśli usługa nie odpowie na te wywołania interfejsu API w rozsądnym czasie, Usługa Service Fabric można wymusić zakończyć korzystanie z usługi. Zwykle dzieje się to tylko podczas uaktualniania aplikacji lub jeśli usługa jest usuwana. Limit czasu wynosi 15 minut, domyślnie.
  - Błędy w `OnCloseAsync()` doprowadzić do ścieżki `OnAbort()` o nazwie, co jest to okazja optymalnych ostatniej szansy usługi oczyszczenia i zwolnić wszystkie zasoby, które mają one zgłoszone. Zazwyczaj jest to nazywane po wykryciu trwałych błędów w węźle lub w przypadku, gdy Usługa Service Fabric niezawodnie nie może zarządzać życia wystąpienia usługi z powodu błędów wewnętrznych.
  - `OnChangeRoleAsync()` jest wywoływana, gdy replika usługi stanowej jest zmiana roli, na przykład podstawowy lub pomocniczy. Replikami podstawowymi otrzymują stanu zapisu (są dozwolone do tworzenia i zapisu do elementów Reliable Collections). Repliki pomocnicze są podane Stan odczytu (tylko może czytać z istniejących elementów Reliable Collections). Większość pracy w stanowej usłudze odbywa się w replice podstawowej. Repliki pomocnicze można wykonywać sprawdzanie poprawności tylko do odczytu, generowania raportu, wyszukiwania danych lub innych zadań tylko do odczytu.

## <a name="next-steps"></a>Kolejne kroki
- [Wprowadzenie do usług Reliable Services](service-fabric-reliable-services-introduction.md)
- [Reliable Services — szybki start](service-fabric-reliable-services-quick-start.md)
- [Repliki i wystąpienia](service-fabric-concepts-replica-lifecycle.md)

---
title: Przegląd cyklu życia Azure Usługa sieci szkieletowej niezawodnej usługi | Dokumentacja firmy Microsoft
description: Więcej informacji na temat zdarzeń cyklu życia różnych w niezawodne usługi sieci szkieletowej usług
services: Service-Fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: vturecek;
ms.assetid: ''
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 42833323cbebf25ce2ca14e6ab7ec4fa5adbfd15
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="reliable-services-lifecycle-overview"></a>Niezawodne usługi cyklu życia — omówienie
> [!div class="op_single_selector"]
> * [C# w systemie Windows](service-fabric-reliable-services-lifecycle.md)
> * [Java w systemie Linux](service-fabric-reliable-services-lifecycle-java.md)
>
>

Jeśli myślisz o cykle zaufania usługi sieci szkieletowej usług Azure, podstawowe informacje o cyklu życia są najważniejsze. Ogólnie rzecz biorąc cyklem obejmuje następujące elementy:

- Podczas uruchamiania:
  - Usługi są wykonane.
  - Usługi mają możliwość utworzenia i zwracać odbiorników zero lub więcej.
  - Wszystkie zwracane obiekty nasłuchujące są otwarte, zezwolenie na komunikację z usługą.
  - Usługi **RunAsync** metoda jest wywoływana, dzięki czemu usługa czy długotrwałych zadań lub Praca w tle.
- Podczas zamykania:
  - Token anulowania przekazany do **RunAsync** została anulowana, a odbiorniki są zamknięte.
  - Po odbiorniki zamknąć, sam obiekt usługi jest niszczone.

Brak szczegółów wokół dokładne kolejność tych zdarzeń. Kolejność zdarzeń może zmienić się nieco w zależności od tego, czy usługa niezawodnej jest bezstanowe i stanowe. Ponadto dla stanowych usług, firma Microsoft musi uwzględniać scenariusz podstawowy wymiany. Podczas tej sekwencji rolę podstawową jest przenoszona do innej repliki (lub wróci) bez zamknięcie usługi. Na koniec należy naszym zdaniem o warunkach błędu lub niepowodzenie.

## <a name="stateless-service-startup"></a>Uruchamianie usługi bezstanowej
Cykl życia usługi bezstanowej jest prosta. Poniżej przedstawiono kolejność zdarzeń:

1. Usługa jest tworzony.
2. Następnie równolegle, stanie się dwie czynności:
    - `StatelessService.CreateServiceInstanceListeners()` jest wywoływany i wszelkie zwrócił odbiorników są otwarte. `ICommunicationListener.OpenAsync()` jest wywoływana na każdym odbiornika.
    - Usługi `StatelessService.RunAsync()` metoda jest wywoływana.
3. Jeśli jest obecny, usługi `StatelessService.OnOpenAsync()` metoda jest wywoływana. To wywołanie jest rzadko zastąpienia, ale jest ona dostępna. W tym momencie można uruchomić zadania inicjowania usługi rozszerzonej.

Należy pamiętać, że nie ma żadnych kolejności między wywołaniami do tworzenia i otwierania odbiorniki i **RunAsync**. Odbiorniki można otworzyć przed **RunAsync** została uruchomiona. Podobnie można wywołać **RunAsync** przed otwierania lub nawet skonstruowane odbiorników komunikacji. Jeśli wymagana jest każdej synchronizacji, jest pozostawiany bez jako wykonywania do implementujący. Poniżej przedstawiono typowe rozwiązania:

  - Czasami odbiorników nie może działać, dopóki informacje z innych jest tworzony lub praca jest wykonywana. Dla usług bezstanowych, które pracy zwykle można zrobić w innych lokalizacjach, takich jak następujące: 
    - W Konstruktorze tej usługi.
    - Podczas `CreateServiceInstanceListeners()` wywołania.
    - Jako część konstrukcji samego odbiornika.
  - Czasami kod w **RunAsync** nie możesz uruchomić do momentu odbiorniki są otwarte. W takim przypadku niezbędne jest dodatkowe koordynacji. Jednym z typowych rozwiązań jest dostępne w odbiorniki flagę wskazującą, kiedy zostało ukończone. Ta flaga jest sprawdzany **RunAsync** przed kontynuowaniem pracy rzeczywistej.

## <a name="stateless-service-shutdown"></a>Zamykanie usługi bezstanowej
Zamykanie usługi bezstanowej, aby uzyskać tego samego wzorca występuje tylko w odwrotnej:

1. Równolegle:
    - Są zamykane wszystkie otwarte odbiorników. `ICommunicationListener.CloseAsync()` jest wywoływana na każdym odbiornika.
    - Token anulowania przekazany do `RunAsync()` zostało anulowane. Sprawdź token anulowania `IsCancellationRequested` właściwość zwraca wartość true i wywołuje tokenu `ThrowIfCancellationRequested` metoda zgłasza `OperationCanceledException`.
2. Po `CloseAsync()` zakończeniu na każdym odbiornika i `RunAsync()` również zakończeniu usługi `StatelessService.OnCloseAsync()` metoda jest wywoływana, jeśli jest obecny.  OnCloseAsync jest wywoływane, gdy wystąpienie usługi bezstanowej będzie można bezpiecznie zamknąć. Taka sytuacja może wystąpić, gdy kodu usługi jest uaktualniany, wystąpienie usługi jest przenoszony z powodu równoważenia obciążenia lub wykrycia błędu przejściowego. Jest rzadko do przesłonięcia `StatelessService.OnCloseAsync()`, ale może służyć do bezpiecznie zamknąć zasobów, Zatrzymaj przetwarzanie w tle, Zakończ zapisywanie stanu zewnętrznych lub zamknięcia w istniejących połączeń.
3. Po `StatelessService.OnCloseAsync()` zakończeniu obiekt usługi jest niszczone.

## <a name="stateful-service-startup"></a>Uruchamianie usługi stanowej
Usługi stanowej mają podobnego wzorca do usług bezstanowych, kilka zmian. Uruchamianie usługi stanowej, kolejność zdarzeń jest następująca:

1. Usługa jest tworzony.
2. `StatefulServiceBase.OnOpenAsync()` jest wywoływana. To wywołanie nie jest zazwyczaj zastępowany przez usługę.
3. Równolegle się zdarzyć następujących czynności:
    - `StatefulServiceBase.CreateServiceReplicaListeners()` jest wywoływane. 
      - Jeśli usługa jest usługą podstawowy, wszystkie zwracane obiekty nasłuchujące są otwarte. `ICommunicationListener.OpenAsync()` jest wywoływana na każdym odbiornika.
      - Jeśli usługa jest usługą dodatkowej, tylko te odbiorniki oznaczona jako `ListenOnSecondary = true` są otwarte. Odbiorniki otwarte na pomocnicze bazy danych jest mniej typowych.
    - Jeśli usługa jest obecnie podstawowym, usługa firmy `StatefulServiceBase.RunAsync()` metoda jest wywoływana.
4. Po wszystkich replik odbiornika dla `OpenAsync()` wywołuje Zakończ i `RunAsync()` jest nazywany `StatefulServiceBase.OnChangeRoleAsync()` jest wywoływana. To wywołanie nie jest zazwyczaj zastępowany przez usługę.

Podobnie jak usług bezstanowych, nie ma żadnych koordynację między tymi kolejność, w którym są tworzone i otworzyć odbiorniki i kiedy **RunAsync** jest wywoływana. Jeśli potrzebujesz koordynacji rozwiązania są znacznie takie same. Istnieje jeden przypadek dodatkowe usługi stanowej. Powiedzieć, że wywołania, które przyjeździe odbiorników komunikacji wymagają informacje przechowywane w niektórych [niezawodnej kolekcje](service-fabric-reliable-services-reliable-collections.md). Ponieważ odbiorników komunikacji może otworzyć przed niezawodnej kolekcje są do odczytu lub zapisu, a przed **RunAsync** można uruchomić niektórych dodatkowych koordynacji jest konieczne. Rozwiązanie najprostszym i najczęściej jest w przypadku odbiorników komunikacji zwrócić kod błędu, który klient używa do ponowić żądanie.

## <a name="stateful-service-shutdown"></a>Zamknięcie usługi stanowej
Podobnie jak usługi bezstanowej zdarzenia cyklu życia podczas zamykania są takie same jak podczas uruchamiania, ale wycofane. Gdy trwa zamykanie usługi stanowej, wykonywane są następujące zdarzenia:

1. Równolegle:
    - Są zamykane wszystkie otwarte odbiorników. `ICommunicationListener.CloseAsync()` jest wywoływana na każdym odbiornika.
    - Token anulowania przekazany do `RunAsync()` zostało anulowane. Sprawdź token anulowania `IsCancellationRequested` właściwość zwraca wartość true i wywołuje tokenu `ThrowIfCancellationRequested` metoda zgłasza `OperationCanceledException`.
2. Po `CloseAsync()` zakończeniu na każdym odbiornika i `RunAsync()` również zakończeniu usługi `StatefulServiceBase.OnChangeRoleAsync()` jest wywoływana. To wywołanie nie jest zazwyczaj zastępowany przez usługę.

   > [!NOTE]  
   > Poczekaj na potrzeby **RunAsync** do zakończenia jest konieczny tylko wtedy, jeśli ta replika jest repliki podstawowej.

3. Po `StatefulServiceBase.OnChangeRoleAsync()` zakończeniu metody `StatefulServiceBase.OnCloseAsync()` metoda jest wywoływana. To wywołanie jest rzadko zastąpienia, ale jest ona dostępna.
3. Po `StatefulServiceBase.OnCloseAsync()` zakończeniu obiekt usługi jest niszczone.

## <a name="stateful-service-primary-swaps"></a>Zamiany głównej usługi stanowej
Po uruchomieniu usługi stanowej replik podstawowych tej usługi stanowej ma ich odbiorników komunikacji otwarty i ich **RunAsync** wywołana metoda. Replik pomocniczych są wykonane, ale nie kolejne wywołania w temacie. Po uruchomieniu usługi stanowej repliki, który jest obecnie podstawowej, można zmienić. Co to znaczy w kategoriach zdarzenia cyklu życia, które można wyświetlić repliki Zachowanie, które widzi stanowe repliki zależy od tego, czy jest repliki trwa obniżyć poziom jest podwyższany podczas wymiany.

### <a name="for-the-primary-thats-demoted"></a>Dla serwera podstawowego, który jest obniżenie poziomu
Dla repliki podstawowej jest obniżenie poziomu sieć szkieletowa usług musi tę replikę, aby zatrzymać przetwarzanie komunikatów i zakończyć pracę tła, których wykonywanie operacji. W związku z tym w tym kroku wygląda tak, jak podczas zamykania usługi. Jeden różnica polega na czy usługi nie jest niszczone lub zamknięta, ponieważ pozostaje pomocniczego. Następujące interfejsy API są nazywane:

1. Równolegle:
    - Są zamykane wszystkie otwarte odbiorników. `ICommunicationListener.CloseAsync()` jest wywoływana na każdym odbiornika.
    - Token anulowania przekazany do `RunAsync()` zostało anulowane. Sprawdź token anulowania `IsCancellationRequested` właściwość zwraca wartość true i wywołuje tokenu `ThrowIfCancellationRequested` metoda zgłasza `OperationCanceledException`.
2. Po `CloseAsync()` zakończeniu na każdym odbiornika i `RunAsync()` również zakończeniu usługi `StatefulServiceBase.OnChangeRoleAsync()` jest wywoływana. To wywołanie nie jest zazwyczaj zastępowany przez usługę.

### <a name="for-the-secondary-thats-promoted"></a>Dla lokacji dodatkowej, która zostanie podniesiona
Podobnie sieci szkieletowej usług wymaga repliki pomocniczej, która zostanie podniesiona do nasłuchiwania komunikatów przesyłania rozpoczęcie i wszelkie niezbędne do ukończenia zadania w tle. W związku z tym proces ten wygląda tak, jak podczas tworzenia usługi z tą różnicą, że sama repliki już istnieje. Następujące interfejsy API są nazywane:

1. Równolegle:
    - `StatefulServiceBase.CreateServiceReplicaListeners()` jest wywoływany i wszelkie zwrócił odbiorników są otwarte. `ICommunicationListener.OpenAsync()` jest wywoływana na każdym odbiornika.
    - Usługi `StatefulServiceBase.RunAsync()` metoda jest wywoływana.
2. Po wszystkich replik odbiornika dla `OpenAsync()` wywołuje Zakończ i `RunAsync()` jest nazywany `StatefulServiceBase.OnChangeRoleAsync()` jest wywoływana. To wywołanie nie jest zazwyczaj zastępowany przez usługę.

### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>Typowe problemy podczas zamykania usługi stanowej i obniżania poziomu podstawowego
Sieć szkieletowa usług zmienia podstawowej usługi stanowej z różnych przyczyn. Najbardziej typowe są [klastra, ponowne równoważenie](service-fabric-cluster-resource-manager-balancing.md) i [uaktualniania aplikacji](service-fabric-application-upgrade.md). Podczas tych czynności (a także podczas zamykania normalnego użytkowania, np. zostanie wyświetlony, jeśli usługa została usunięta), należy pamiętać, że usługa przestrzegać `CancellationToken`. 

Usług, które nie obsługują anulowania prawidłowo może wystąpić pewne problemy. Te operacje są wolne, ponieważ usługi sieć szkieletowa czeka bezpiecznie zatrzymania usługi. Ostatecznie to prowadzić do uaktualnienia nie powiodło się tego limitu czasu i wycofać. Nie można uwzględnić token anulowania powodują imbalanced klastrów. Klastry stać się równowagi, ponieważ węzły uzyskać gorących, ale nie można rebalanced usługi, ponieważ trwa zbyt długo, aby przenieść je w innym miejscu. 

Ponieważ usługi są stanowe, również jest prawdopodobne, że używają one [niezawodnej kolekcje](service-fabric-reliable-services-reliable-collections.md). W sieci szkieletowej usług podczas obniżania poziomu podstawowego jedną pierwszy co się stanie, jest odebraniu podstawowy stan do zapisu. Prowadzi to do drugiego zestawu problemy, które mogą wpłynąć na cyklem życia usługi. Kolekcje zwracany wyjątki oparte na czas i czy jest przenoszony repliki lub zamykania. Te wyjątki powinny być traktowane jako poprawnie. Wyjątki generowane przez sieć szkieletowa usług można podzielić na stałe [(`FabricException`)](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception?view=azure-dotnet) i przejściowych [(`FabricTransientException`)](https://docs.microsoft.com/dotnet/api/system.fabric.fabrictransientexception?view=azure-dotnet) kategorii. Stałe wyjątki powinny być rejestrowane i zgłoszony podczas przejściowej wyjątków można ponowić opartych na logice niektórych ponów próbę.

Obsługa wyjątków, które pochodzą od stosowania `ReliableCollections` w połączeniu z zdarzenia cyklu życia usługi jest ważnym elementem przetestowaniu i zweryfikowaniu niezawodnej usługi. Zaleca się zawsze uruchamiaj usługi Obciążenie podczas wykonywania uaktualnienia i [testowania chaos](service-fabric-controlled-chaos.md) przed wdrożeniem w środowisku produkcyjnym. Te podstawowe kroki pomoc, sprawdź, czy usługa jest poprawnie zaimplementowana i poprawnie obsługuje zdarzenia cyklu życia.


## <a name="notes-on-the-service-lifecycle"></a>Uwagi dotyczące cyklu życia usług
  - Zarówno `RunAsync()` — metoda i `CreateServiceReplicaListeners/CreateServiceInstanceListeners` wywołania są opcjonalne. Usługa może mieć ich, zarówno lub nie. Na przykład, jeśli usługa jest jego pracy w odpowiedź na wywołania użytkownika, jest niepotrzebna go do zaimplementowania `RunAsync()`. Wymagane są tylko odbiorników komunikacji i ich skojarzonego kodu. Podobnie, tworzenie i zwracanie odbiorników komunikacji jest opcjonalne, ponieważ usługa może mieć tylko pracy tła i dlatego tylko musi implementować `RunAsync()`.
  - Jest on prawidłowy dla usługi, aby ukończyć `RunAsync()` pomyślnie i z powrotem od niego. Kończenie pracy nie jest warunek błędu. Kończenie pracy `RunAsync()` wskazuje, że praca w tle usługi zostało zakończone. Dla stanowych usług niezawodne `RunAsync()` zostanie ponownie wywołany, jeśli replika nie zostanie obniżony z podstawowego na pomocniczym i następnie podwyższony do podstawowego.
  - Jeśli zamknie usługę `RunAsync()` przez zgłaszanie nieoczekiwany wyjątek, powoduje błąd. Obiekt usługi jest zamknięta i jest zgłaszany błąd kondycji.
  - Mimo że nie ma żadnego limitu czasu na zwracanie z tych metod, natychmiast spowoduje utratę możliwości zapisu do kolekcji niezawodnych i w związku z tym nie można ukończyć rzeczywistą pracę. Firma Microsoft zaleca powrocie możliwie jak najszybciej po otrzymaniu żądania anulowania. Jeśli usługa nie odpowiada na te wywołania interfejsu API w rozsądnym czasie, sieci szkieletowej usług można wymusić przerwanie usługi. Zwykle to tylko odbywa się podczas uaktualniania aplikacji lub gdy usługa jest usuwana. Tego limitu czasu wynosi 15 minut domyślnie.
  - Błędy w `OnCloseAsync()` doprowadzi do ścieżki `OnAbort()` o nazwie, która jest ostatniej szansy optymalnych na wyczyszczenie i zwolnić wszystkie zasoby, które mają one żądane usługi. Zazwyczaj jest to nazywane po wykryciu trwałego błędu w węźle lub sieci szkieletowej usług nie można niezawodnie zarządzania cyklem życia wystąpienia usługi z powodu błędów wewnętrznych.
  - `OnChangeRoleAsync()` jest wywoływane, gdy replika usługi stanowej jest zmiana roli, na przykład na podstawowy lub pomocniczy. Replik podstawowych podano stanu zapisu (dozwolone są do tworzenia i zapisywania niezawodnej kolekcji). Replik pomocniczych podano stanu (tylko można odczytać z istniejących zbiorach niezawodnej). Większość pracy w usługi stanowej odbywa się repliką podstawową. Replik pomocniczych można wykonać walidacji tylko do odczytu, generowanie raportów, wyszukiwania danych lub innych zadań tylko do odczytu.

## <a name="next-steps"></a>Kolejne kroki
- [Wprowadzenie do niezawodne usługi](service-fabric-reliable-services-introduction.md)
- [Niezawodne usługi szybki start](service-fabric-reliable-services-quick-start.md)
- [Repliki i wystąpienia](service-fabric-concepts-replica-lifecycle.md)

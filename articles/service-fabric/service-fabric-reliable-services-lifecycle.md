---
title: Przegląd cyklu życia niezawodnych usług
description: Dowiedz się więcej o zdarzeniach cyklu życia w aplikacji niezawodne usługi sieci szkieletowej usług Azure dla usług stanowych i bezstanowych.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: fe338ca3f25cd606da7f95f6c9437a3cd3dc4e69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258280"
---
# <a name="reliable-services-lifecycle-overview"></a>Omówienie cyklu życia niezawodnych usług
> [!div class="op_single_selector"]
> * [C# w systemie Windows](service-fabric-reliable-services-lifecycle.md)
> * [Java w systemie Linux](service-fabric-reliable-services-lifecycle-java.md)
>
>

Gdy myślisz o cyklach życia usług niezawodnych sieci szkieletowej usług Azure, najważniejsze są podstawy cyklu życia. Ogólnie rzecz biorąc, cykl życia obejmuje następujące elementy:

- Podczas uruchamiania:
  - Usługi są konstruowane.
  - Usługi mają możliwość konstruowania i zwracania zero lub więcej odbiorników.
  - Wszystkie zwrócone odbiorniki są otwierane, umożliwiając komunikację z usługą.
  - Usługa **RunAsync** metoda jest wywoływana, dzięki czemu usługa do długotrwałych zadań lub pracy w tle.
- Podczas wyłączania:
  - Token anulowania przekazany do **RunAsync** jest anulowany, a odbiorniki są zamknięte.
  - Po zamknięciu odbiorników sam obiekt usługi jest zniszczony.

Istnieją szczegółowe informacje na temat dokładnego zamawiania tych zdarzeń. Kolejność zdarzeń może ulec nieznacznej zmianie w zależności od tego, czy usługa niezawodna jest bezstanowa czy stanowa. Ponadto w przypadku usług stanowych musimy radzić sobie ze scenariuszem wymiany podstawowej. Podczas tej sekwencji rola podstawowy jest przenoszony do innej repliki (lub wraca) bez zamykania usługi. Na koniec musimy pomyśleć o warunkach błędu lub awarii.

## <a name="stateless-service-startup"></a>Uruchamianie usługi bezstanowej
Cykl życia usługi bezstanowej jest prosta. Oto kolejność wydarzeń:

1. Usługa jest konstruowana.
2. Równolegle dzieją się dwie rzeczy:
    - `StatelessService.CreateServiceInstanceListeners()`wywoływane i wszystkie zwracane odbiorniki są otwierane. `ICommunicationListener.OpenAsync()`jest wywoływana na każdego słuchacza.
    - `StatelessService.RunAsync()` Metoda usługi jest wywoływana.
3. Jeśli jest obecny, `StatelessService.OnOpenAsync()` wywoływana jest metoda usługi. To wywołanie jest nietypowe zastąpienie, ale jest ono dostępne. W tej chwili można uruchomić zadania inicjowania usługi rozszerzonej.

Należy pamiętać, że nie ma zamawiania między wywołaniami, aby utworzyć i otworzyć odbiorników i **RunAsync**. Detektory mogą otwierać się przed uruchomieniem **RunAsync.** Podobnie można wywołać **RunAsync** przed odbiorników komunikacji są otwarte lub nawet skonstruowane. Jeśli wymagana jest synchronizacja, pozostaje jako ćwiczenie dla realizatora. Oto kilka typowych rozwiązań:

  - Czasami słuchacze nie mogą działać, dopóki nie zostanie utworzone inne informacje lub praca zostanie wykonana. W przypadku usług bezstanowych ta praca może być zwykle wykonywana w innych lokalizacjach, takich jak: 
    - W konstruktorze usługi.
    - Podczas `CreateServiceInstanceListeners()` rozmowy.
    - W ramach budowy samego słuchacza.
  - Czasami kod w **RunAsync** nie uruchamia się, dopóki odbiorniki są otwarte. W takim przypadku konieczna jest dodatkowa koordynacja. Jednym z typowych rozwiązań jest to, że istnieje flaga w detektorze, który wskazuje, kiedy zostały zakończone. Ta flaga jest następnie sprawdzana w **RunAsync** przed kontynuowaniem pracy rzeczywistej.

## <a name="stateless-service-shutdown"></a>Zamknięcie usługi bezstanowej
Do zamykania usługi bezstanowej, ten sam wzorzec jest następuje, tylko w odwrotnej kolejności:

1. Równolegle:
    - Wszystkie otwarte odbiorniki są zamknięte. `ICommunicationListener.CloseAsync()`jest wywoływana na każdego słuchacza.
    - Token anulowania `RunAsync()` przekazany do jest anulowany. Sprawdzanie `IsCancellationRequested` właściwości tokenu anulowania zwraca wartość true, a jeśli `ThrowIfCancellationRequested` jest wywoływana, metoda tokenu `OperationCanceledException`zgłasza .
2. Po `CloseAsync()` zakończeniu na każdym `RunAsync()` odbiorniku, a także `StatelessService.OnCloseAsync()` kończy, metoda usługi jest wywoływana, jeśli jest obecny.  OnCloseAsync jest wywoływana, gdy wystąpienie usługi bezstanowej będzie bezpiecznie zamknięty. Może to wystąpić, gdy kod usługi jest uaktualniany, wystąpienie usługi jest przenoszona z powodu równoważenia obciążenia lub wykryto błąd przejściowy. Jest rzadkością, aby `StatelessService.OnCloseAsync()`zastąpić , ale może służyć do bezpiecznego zamykania zasobów, zatrzymać przetwarzanie w tle, zakończyć zapisywanie stanu zewnętrznego lub zamknąć istniejące połączenia.
3. Po `StatelessService.OnCloseAsync()` zakończeniu obiekt usługi jest zniszczony.

## <a name="stateful-service-startup"></a>Uruchamianie usługi stanowej
Usługi stanowe mają podobny wzorzec do usług bezstanowych, z kilkoma zmianami. W przypadku uruchamiania usługi stanowej kolejność zdarzeń jest następująca:

1. Usługa jest konstruowana.
2. `StatefulServiceBase.OnOpenAsync()`nazywa się. To wywołanie nie jest często zastępowane w usłudze.
3. Następujące rzeczy dzieją się równolegle:
    - `StatefulServiceBase.CreateServiceReplicaListeners()`jest wywoływana. 
      - Jeśli usługa jest usługą podstawową, wszystkie zwrócone odbiorniki są otwierane. `ICommunicationListener.OpenAsync()`jest wywoływana na każdego słuchacza.
      - Jeśli usługa jest usługą dodatkową, tylko `ListenOnSecondary = true` te odbiorniki oznaczone jako są otwierane. Posiadanie słuchaczy, które są otwarte na pomocnicze jest mniej powszechne.
    - Jeśli usługa jest obecnie podstawowa, `StatefulServiceBase.RunAsync()` wywoływana jest metoda usługi.
4. Po zakończeniu wszystkich wywołań `OpenAsync()` odbiornika `RunAsync()` repliki `StatefulServiceBase.OnChangeRoleAsync()` i jest wywoływana, jest wywoływana. To wywołanie nie jest często zastępowane w usłudze.

Podobnie jak usługi bezstanowe, nie ma koordynacji między kolejnością, w której odbiorniki są tworzone i otwierane i kiedy **RunAsync** jest wywoływana. Jeśli potrzebujesz koordynacji, rozwiązania są takie same. Istnieje jeden dodatkowy przypadek dla usługi stanowej. Powiedzmy, że połączenia, które docierają do słuchaczy komunikacji wymagają informacji przechowywanych w niektórych [niezawodnych kolekcjach.](service-fabric-reliable-services-reliable-collections.md)

   > [!NOTE]  
   > Ponieważ detektory komunikacji można otworzyć przed niezawodne kolekcje są czytelne lub zapisywalne, a przed **Uruchomienie RunAsync,** niektóre dodatkowe koordynacji jest konieczne. Najprostszym i najbardziej typowym rozwiązaniem jest dla odbiorników komunikacji, aby zwrócić kod błędu, który klient używa wiedzieć, aby ponowić próbę żądania.

## <a name="stateful-service-shutdown"></a>Stanowe zamknięcie usługi
Podobnie jak usługi bezstanowe zdarzenia cyklu życia podczas zamykania są takie same jak podczas uruchamiania, ale odwrócone. Gdy usługa stanowa jest zamykana, występują następujące zdarzenia:

1. Równolegle:
    - Wszystkie otwarte odbiorniki są zamknięte. `ICommunicationListener.CloseAsync()`jest wywoływana na każdego słuchacza.
    - Token anulowania `RunAsync()` przekazany do jest anulowany. Sprawdzanie `IsCancellationRequested` właściwości tokenu anulowania zwraca wartość true, a jeśli `ThrowIfCancellationRequested` jest wywoływana, metoda tokenu `OperationCanceledException`zgłasza .
2. Po `CloseAsync()` zakończeniu na każdym `RunAsync()` odbiorniku, a także `StatefulServiceBase.OnChangeRoleAsync()` kończy, usługa jest wywoływana. To wywołanie nie jest często zastępowane w usłudze.

   > [!NOTE]  
   > Konieczność oczekiwania na zakończenie **runasync** jest konieczne tylko wtedy, gdy ta replika jest repliką podstawową.

3. Po `StatefulServiceBase.OnChangeRoleAsync()` zakończeniu metody `StatefulServiceBase.OnCloseAsync()` wywoływana jest metoda. To wywołanie jest nietypowe zastąpienie, ale jest ono dostępne.
3. Po `StatefulServiceBase.OnCloseAsync()` zakończeniu obiekt usługi jest zniszczony.

## <a name="stateful-service-primary-swaps"></a>Usługa stanowa Podstawowe swapy
Gdy usługa stanowa jest uruchomiona, tylko podstawowe repliki tego usługi stanowe mają ich odbiorników komunikacji otwarte i ich **RunAsync** metoda wywoływana. Repliki pomocnicze są konstruowane, ale nie widzą żadnych dalszych wywołań. Gdy usługa stanowa jest uruchomiona, replika, która jest obecnie podstawowa może zmienić w wyniku optymalizacji równoważenia błędów lub klastra. Co to oznacza w odniesieniu do zdarzeń cyklu życia, które replika może zobaczyć? Zachowanie repliki stanowej widzi zależy od tego, czy jest to replika jest obniżane lub promowane podczas wymiany.

### <a name="for-the-primary-thats-demoted"></a>Dla organizacji podstawowej, która jest zdegradowana
Dla repliki podstawowej, która jest obniżona, sieci szkieletowej usług potrzebuje tej repliki, aby zatrzymać przetwarzanie wiadomości i zamknąć wszelkie prace w tle, które wykonuje. W rezultacie ten krok wygląda tak, jak to miało miejsce, gdy usługa jest zamknięta. Jedną z różnic jest to, że usługa nie jest zniszczony lub zamknięty, ponieważ pozostaje jako pomocniczy. Nazywane są następujące interfejsy API:

1. Równolegle:
    - Wszystkie otwarte odbiorniki są zamknięte. `ICommunicationListener.CloseAsync()`jest wywoływana na każdego słuchacza.
    - Token anulowania `RunAsync()` przekazany do jest anulowany. Sprawdzanie `IsCancellationRequested` właściwości tokenu anulowania zwraca wartość true, a jeśli `ThrowIfCancellationRequested` jest wywoływana, metoda tokenu `OperationCanceledException`zgłasza .
2. Po `CloseAsync()` zakończeniu na każdym `RunAsync()` odbiorniku, a także `StatefulServiceBase.OnChangeRoleAsync()` kończy, usługa jest wywoływana. To wywołanie nie jest często zastępowane w usłudze.

### <a name="for-the-secondary-thats-promoted"></a>Dla drugorzędnych, które są promowane
Podobnie sieć szkieletowa usług potrzebuje repliki pomocniczej, która jest promowana, aby rozpocząć nasłuchiwanie wiadomości w sieci i rozpocząć wszystkie zadania w tle, które musi wykonać. W rezultacie ten proces wygląda tak, jak podczas tworzenia usługi, z tą różnicą, że sama replika już istnieje. Nazywane są następujące interfejsy API:

1. Równolegle:
    - `StatefulServiceBase.CreateServiceReplicaListeners()`wywoływane i wszystkie zwracane odbiorniki są otwierane. `ICommunicationListener.OpenAsync()`jest wywoływana na każdego słuchacza.
    - `StatefulServiceBase.RunAsync()` Metoda usługi jest wywoływana.
2. Po zakończeniu wszystkich wywołań `OpenAsync()` odbiornika `RunAsync()` repliki `StatefulServiceBase.OnChangeRoleAsync()` i jest wywoływana, jest wywoływana. To wywołanie nie jest często zastępowane w usłudze.

### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>Typowe problemy podczas zamykania usługi stanowej i obniżania poziomu podstawowego
Sieci szkieletowej usług zmienia podstawowy usługi stanowej z różnych powodów. Najczęstsze są [ponowne równoważenie klastra](service-fabric-cluster-resource-manager-balancing.md) i [uaktualnianie aplikacji.](service-fabric-application-upgrade.md) Podczas tych operacji (jak również podczas normalnego zamykania usługi, jak można sprawdzić, czy `CancellationToken`usługa została usunięta), ważne jest, aby usługa przestrzegać . 

Usługi, które nie obsługują anulowania czysto może wystąpić kilka problemów. Te operacje są powolne, ponieważ sieci szkieletowej usług czeka na usługi, aby zatrzymać bezpiecznie. Może to ostatecznie prowadzić do nieudanych uaktualnień, które limit czasu i wycofać. Nieprzestrzeganie tokenu anulowania może również spowodować nierównomiejne klastry. Klastry stają się niezrównoważone, ponieważ węzły się nagrzewają, ale usługi nie mogą być równoważone, ponieważ trwa zbyt długo, aby przenieść je w innym miejscu. 

Ponieważ usługi są stanowe, jest również prawdopodobne, że używają [niezawodne kolekcje](service-fabric-reliable-services-reliable-collections.md). W sieci szkieletowej usług, gdy podstawowy jest obniżona, jedną z pierwszych rzeczy, które się dzieje jest to, że dostęp do zapisu do stanu źródłowego jest odwołany. Prowadzi to do drugiego zestawu problemów, które mogą mieć wpływ na cykl życia usługi. Kolekcje zwracają wyjątki na podstawie chronometrażu i czy replika jest przenoszona lub zamykana. Wyjątki te powinny być obsługiwane poprawnie. Wyjątki generowane przez sieci szkieletowej usług należą do kategorii stałe [(`FabricException`)](https://docs.microsoft.com/dotnet/api/system.fabric.fabricexception?view=azure-dotnet) i przejściowe [(`FabricTransientException`).](https://docs.microsoft.com/dotnet/api/system.fabric.fabrictransientexception?view=azure-dotnet) Trwałe wyjątki powinny być rejestrowane i generowane, podczas gdy wyjątki przejściowe mogą być ponowione na podstawie niektórych logiki ponawiania.

Obsługa wyjątków, które pochodzą `ReliableCollections` z korzystania w połączeniu ze zdarzeniami cyklu życia usługi jest ważną częścią testowania i sprawdzania poprawności niezawodnej usługi. Zaleca się, aby zawsze uruchamiać usługę pod obciążeniem podczas wykonywania uaktualnień i [testowania chaosu](service-fabric-controlled-chaos.md) przed wdrożeniem w procesach produkcyjnych. Te podstawowe kroki pomagają upewnić się, że usługa jest poprawnie zaimplementowana i poprawnie obsługuje zdarzenia cyklu życia.


## <a name="notes-on-the-service-lifecycle"></a>Uwagi dotyczące cyklu życia serwisu
  - Zarówno `RunAsync()` metoda, `CreateServiceReplicaListeners/CreateServiceInstanceListeners` jak i wywołania są opcjonalne. Usługa może mieć jeden z nich, oba lub żaden z nich. Na przykład jeśli usługa wykonuje całą swoją pracę w odpowiedzi na wywołania `RunAsync()`użytkownika, nie ma potrzeby implementowania . Tylko odbiorniki komunikacji i ich kod skojarzony są niezbędne. Podobnie tworzenie i zwracanie odbiorników komunikacji jest opcjonalne, ponieważ usługa może mieć tylko pracę `RunAsync()`w tle do wykonania, a więc musi tylko zaimplementować .
  - Jest on ważny dla `RunAsync()` usługi, aby zakończyć pomyślnie i wrócić z niego. Ukończenie nie jest warunkiem awarii. Ukończenie `RunAsync()` wskazuje, że praca w tle usługi została zakończona. W przypadku stanowych `RunAsync()` niezawodnych usług jest wywoływana ponownie, jeśli replika zostanie obniżona z podstawowego do pomocniczego, a następnie awansowana z powrotem do podstawowej.
  - Jeśli usługa kończy `RunAsync()` działanie przez zgłaszanie niektórych nieoczekiwany wyjątek, stanowi to błąd. Obiekt usługi zostanie zamknięty i zostanie zgłoszony błąd kondycji.
  - Chociaż nie ma limitu czasu na powrót z tych metod, natychmiast tracisz możliwość pisania do reliable collections i w związku z tym nie można ukończyć żadnej rzeczywistej pracy. Zalecamy jak najszybszy zwrot po otrzymaniu prośby o anulowanie rezerwacji. Jeśli usługa nie odpowiada na te wywołania interfejsu API w rozsądnym czasie, sieci szkieletowej usług można na przymusowo zakończyć usługę. Zwykle dzieje się tak tylko podczas uaktualniania aplikacji lub gdy usługa jest usuwana. Ten limit czasu wynosi domyślnie 15 minut.
  - Błędy w `OnCloseAsync()` ścieżce `OnAbort()` powodują wywoływania, co jest ostatnią szansą na najlepszy wysiłek dla usługi, aby oczyścić i zwolnić wszystkie zasoby, które zostały zgłoszone. Jest to zwykle wywoływane, gdy w węźle zostanie wykryty błąd trwały lub gdy sieci szkieletowej usług nie można niezawodnie zarządzać cyklem życia wystąpienia usługi z powodu awarii wewnętrznych.
  - `OnChangeRoleAsync()`jest wywoływana, gdy replika usługi stanowej zmienia rolę, na przykład na podstawową lub pomocniczą. Repliki podstawowe mają stan zapisu (mogą tworzyć i zapisywać w niezawodnych kolekcjach). Repliki pomocnicze mają stan odczytu (można odczytać tylko z istniejących niezawodnych kolekcji). Większość pracy w usłudze stanowej jest wykonywana w replice podstawowej. Repliki pomocnicze mogą wykonywać sprawdzanie poprawności tylko do odczytu, generowanie raportów, wyszukiwanie danych lub inne zadania tylko do odczytu.

## <a name="next-steps"></a>Następne kroki
- [Wprowadzenie do niezawodnych usług](service-fabric-reliable-services-introduction.md)
- [Szybki start niezawodnych usług](service-fabric-reliable-services-quick-start.md)
- [Repliki i wystąpienia](service-fabric-concepts-replica-lifecycle.md)

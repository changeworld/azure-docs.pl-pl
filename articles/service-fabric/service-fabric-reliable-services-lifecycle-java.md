---
title: Cykl życia niezawodnych usług sieci szkieletowej usług Azure
description: Dowiedz się więcej o zdarzeniach cyklu życia w aplikacji niezawodnych usług sieci szkieletowej usług Azure przy użyciu języka Java dla usług stanowych i bezstanowych.
author: PavanKunapareddyMSFT
ms.topic: conceptual
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: 1d3be958a0649ed3e80df2d63adbdf0b91831dbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75639602"
---
# <a name="reliable-services-lifecycle"></a>Cykl życia usług Reliable Services
> [!div class="op_single_selector"]
> * [C# w systemie Windows](service-fabric-reliable-services-lifecycle.md)
> * [Java w systemie Linux](service-fabric-reliable-services-lifecycle-java.md)
>
>

Niezawodne usługi to jeden z modeli programowania dostępnych w sieci szkieletowej usług Azure. Podczas poznawania cyklu życia niezawodnych usług, najważniejsze jest zrozumienie podstawowych zdarzeń cyklu życia. Dokładna kolejność zdarzeń zależy od szczegółów konfiguracji. 

Ogólnie rzecz biorąc cykl życia niezawodnych usług obejmuje następujące zdarzenia:

* Podczas uruchamiania:
  * Usługi są konstruowane.
  * Usługi mają możliwość konstruowania i zwracania zero lub więcej odbiorników.
  * Wszystkie zwrócone odbiorniki są otwierane w celu komunikacji z usługą.
  * `runAsync` Metoda usługi jest wywoływana, więc usługa może wykonywać długotrwałą pracę lub w tle.
* Podczas wyłączania:
  * Token anulowania, który `runAsync` został przekazany do jest anulowany, a odbiorniki są zamknięte.
  * Sam obiekt usługi jest zniszczony.

Kolejność zdarzeń w niezawodnych usług może ulec zmianie nieznacznie w zależności od tego, czy niezawodna usługa jest bezstanowa czy stanowa. 

Ponadto w przypadku usług stanowych należy rozwiązać podstawowy scenariusz wymiany. Podczas tej sekwencji rola podstawowej jest przenoszona do innej repliki (lub wraca) bez zamykania usługi. 

Na koniec musisz pomyśleć o warunkach błędu lub awarii.

## <a name="stateless-service-startup"></a>Uruchamianie usługi bezstanowej
Cykl życia usługi bezstanowej jest dość prosty. Oto kolejność wydarzeń:

1. Usługa jest konstruowana.
2. Zdarzenia te występują równolegle:
    - `StatelessService.createServiceInstanceListeners()`wywoływane, a wszystkie zwracane odbiorniki są otwierane. `CommunicationListener.openAsync()`jest wywoływana na każdego słuchacza.
    - `runAsync` Metoda usługi (`StatelessService.runAsync()`) jest wywoływana.
3. Jeśli jest obecny, wywoływana jest metoda własna `onOpenAsync` usługi. W szczególności, `StatelessService.onOpenAsync()` nazywa. Jest to nietypowe zastąpienie, ale jest ono dostępne.

Ważne jest, aby pamiętać, że nie ma kolejności między wywołaniem, aby `runAsync`utworzyć i otworzyć słuchaczy i wywołanie . Detektory mogą otwierać się przed `runAsync` rozpoczęciem. Podobnie może `runAsync` być wywoływane przed odbiorników komunikacji są otwarte lub przed ich nawet zostały skonstruowane. Jeśli wymagana jest synchronizacja, musi być wykonana przez realizatora. Oto kilka typowych rozwiązań:

* Czasami słuchacze nie mogą funkcjonować, dopóki nie zostanie utworzona inna informacja lub nie zostanie wykonana inna praca. Dla usług bezstanowych, że praca zwykle można wykonać w konstruktorze usługi. Można to zrobić `createServiceInstanceListeners()` podczas połączenia lub w ramach budowy samego słuchacza.
* Czasami kod `runAsync` w nie rozpocznie się, dopóki odbiorniki są otwarte. W takim przypadku konieczna jest dodatkowa koordynacja. Typowym rozwiązaniem jest dodanie flagi w detektorach. Flaga wskazuje, kiedy detektory zostały zakończone. Metoda `runAsync` sprawdza to przed kontynuowaniem pracy rzeczywistej.

## <a name="stateless-service-shutdown"></a>Zamknięcie usługi bezstanowej
Podczas zamykania usługi bezstanowej, ten sam wzorzec jest następuje, ale w odwrotnej kolejności:

1. Zdarzenia te występują równolegle:
    - Wszystkie otwarte odbiorniki są zamknięte. `CommunicationListener.closeAsync()`jest wywoływana na każdego słuchacza.
    - Token anulowania, który `runAsync()` został przekazany do jest anulowany. Sprawdzanie `isCancelled` właściwości tokenu anulowania `true`zwraca , a jeśli `throwIfCancellationRequested` wywoływane, `CancellationException`token metoda zgłasza .
2. Po `closeAsync()` zakończeniu na każdym `runAsync()` odbiorniku, a także `StatelessService.onCloseAsync()` kończy, metoda usługi jest wywoływana, jeśli jest obecny. Ponownie nie jest to typowe zastąpienie, ale może służyć do bezpiecznego zamykania zasobów, zatrzymywania przetwarzania w tle, kończeń zapisywania stanu zewnętrznego lub zamykania istniejących połączeń.
3. Po `StatelessService.onCloseAsync()` zakończeniu obiekt usługi jest zniszczony.

## <a name="stateful-service-startup"></a>Uruchamianie usługi stanowej
Usługi stanowe mają wzorzec, który jest podobny do usług bezstanowych, z kilkoma zmianami.  Oto kolejność zdarzeń do uruchamiania usługi stanowej:

1. Usługa jest konstruowana.
2. `StatefulServiceBase.onOpenAsync()`nazywa się. To wywołanie nie jest często zastępowane w usłudze.
3. Zdarzenia te występują równolegle:
    - `StatefulServiceBase.createServiceReplicaListeners()`jest wywoływana. 
      - Jeśli usługa jest usługą podstawową, wszystkie zwrócone odbiorniki są otwierane. `CommunicationListener.openAsync()`jest wywoływana na każdego słuchacza.
      - Jeśli usługa jest usługą pomocniczą, `listenOnSecondary = true` tylko detektory oznaczone jako otwarte. Posiadanie słuchaczy, które są otwarte na pomocnicze jest mniej powszechne.
    - Jeśli usługa jest obecnie podstawową, `StatefulServiceBase.runAsync()` wywoływana jest metoda usługi.
4. Po zakończeniu wszystkich wywołań `openAsync()` odbiornika `runAsync()` repliki `StatefulServiceBase.onChangeRoleAsync()` i jest wywoływana, jest wywoływana. To wywołanie nie jest często zastępowane w usłudze.

Podobnie jak usługi bezstanowe, w służbie stanowej, nie ma koordynacji między kolejnością, w której odbiorniki są tworzone i otwierane i kiedy `runAsync` jest wywoływana. Jeśli potrzebujesz koordynacji, rozwiązania są takie same. Ale jest jeden dodatkowy przypadek dla służby stanowej. Powiedzmy, że połączenia, które docierają do słuchaczy komunikacji wymagają informacji przechowywanych w niektórych [niezawodnych kolekcjach.](service-fabric-reliable-services-reliable-collections.md) Ponieważ detektory komunikacji może otworzyć przed niezawodne kolekcje są `runAsync` czytelne lub zapisywalne, a przed rozpoczęciem, niektóre dodatkowe koordynacji jest konieczne. Najprostszym i najbardziej typowym rozwiązaniem jest dla słuchaczy komunikacji zwrócić kod błędu. Klient używa kodu błędu, aby wiedzieć, aby ponowić próbę żądania.

## <a name="stateful-service-shutdown"></a>Stanowe zamknięcie usługi
Podobnie jak usługi bezstanowe zdarzenia cyklu życia podczas zamykania są takie same jak podczas uruchamiania, ale odwrócone. Gdy usługa stanowa jest zamykana, występują następujące zdarzenia:

1. Zdarzenia te występują równolegle:
    - Wszystkie otwarte odbiorniki są zamknięte. `CommunicationListener.closeAsync()`jest wywoływana na każdego słuchacza.
    - Token anulowania, który `runAsync()` został przekazany do jest anulowany. Wywołanie `isCancelled()` metody tokenu anulowania `true`zwraca , a jeśli `throwIfCancellationRequested()` jest wywoływana, metoda tokenu `OperationCanceledException`zgłasza .
2. Po `closeAsync()` zakończeniu na każdym `runAsync()` odbiorniku, a także `StatefulServiceBase.onChangeRoleAsync()` kończy, usługa jest wywoływana. To wywołanie nie jest często zastępowane w usłudze.

   > [!NOTE]  
   > Oczekiwanie `runAsync` na zakończenie jest konieczne tylko wtedy, gdy ta replika jest repliką podstawową.

3. Po `StatefulServiceBase.onChangeRoleAsync()` zakończeniu metody `StatefulServiceBase.onCloseAsync()` wywoływana jest metoda. To wywołanie jest nietypowe zastąpienie, ale jest ono dostępne.
3. Po `StatefulServiceBase.onCloseAsync()` zakończeniu obiekt usługi jest zniszczony.

## <a name="stateful-service-primary-swaps"></a>Zamiany podstawowe usługi stanowej
Gdy usługa stanowa jest uruchomiona, odbiorniki `runAsync` komunikacji są otwierane i metoda jest wywoływana tylko dla replik podstawowych tej usługi stanowe. Repliki pomocnicze są konstruowane, ale nie widzą żadnych dalszych wywołań. Gdy usługa stanowa jest uruchomiona, replika, która jest obecnie podstawowa może ulec zmianie. Zdarzenia cyklu życia, które replika stanowa można zobaczyć zależy od tego, czy jest to replika jest obniżane lub promowane podczas wymiany.

### <a name="for-the-demoted-primary"></a>W przypadku zdegradowanych
Sieci szkieletowej usług potrzebuje repliki podstawowej, która jest obniżona, aby zatrzymać przetwarzanie wiadomości i zatrzymać wszelkie prace w tle. Ten krok jest podobny do gdy usługa jest zamknięta. Jedną z różnic jest to, że usługa nie jest zniszczony lub zamknięty, ponieważ pozostaje jako pomocniczy. Wystąpią następujące zdarzenia:

1. Zdarzenia te występują równolegle:
    - Wszystkie otwarte odbiorniki są zamknięte. `CommunicationListener.closeAsync()`jest wywoływana na każdego słuchacza.
    - Token anulowania, który `runAsync()` został przekazany do jest anulowany. Zwraca się czek metody `isCancelled()` tokenu `true`anulowania. Jeśli wywoływane, `throwIfCancellationRequested()` token metody zgłasza `OperationCanceledException`.
2. Po `closeAsync()` zakończeniu na każdym `runAsync()` odbiorniku, a także `StatefulServiceBase.onChangeRoleAsync()` kończy, usługa jest wywoływana. To wywołanie nie jest często zastępowane w usłudze.

### <a name="for-the-promoted-secondary"></a>W przypadku promowanych
Podobnie sieć szkieletowa usług potrzebuje repliki pomocniczej, która jest promowana, aby rozpocząć nasłuchiwanie wiadomości w sieci i uruchomić wszystkie zadania w tle, które musi wykonać. Ten proces jest podobny do podczas tworzenia usługi. Różnica polega na tym, że sama replika już istnieje. Wystąpią następujące zdarzenia:

1. Zdarzenia te występują równolegle:
    - `StatefulServiceBase.createServiceReplicaListeners()`wywoływane i wszystkie zwracane odbiorniki są otwierane. `CommunicationListener.openAsync()`jest wywoływana na każdego słuchacza.
    - `StatefulServiceBase.runAsync()` Metoda usługi jest wywoływana.
2. Po zakończeniu wszystkich wywołań `openAsync()` odbiornika `runAsync()` repliki `StatefulServiceBase.onChangeRoleAsync()` i jest wywoływana, jest wywoływana. To wywołanie nie jest często zastępowane w usłudze.

### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>Typowe problemy podczas zamykania usług stanowych i obniżania poziomu podstawowego
Sieci szkieletowej usług zmienia podstawowy usługi stanowej z wielu powodów. Najczęstszymi przyczynami są [równoważenie klastra](service-fabric-cluster-resource-manager-balancing.md) i [uaktualnianie aplikacji.](service-fabric-application-upgrade.md) Podczas tych operacji ważne jest, aby usługa `cancellationToken`przestrzegała . Dotyczy to również podczas normalnego zamykania usługi, na przykład jeśli usługa została usunięta.

Usługi, które nie obsługują anulowania czysto może wystąpić kilka problemów. Te operacje są powolne, ponieważ sieci szkieletowej usług czeka na usługi, aby zatrzymać bezpiecznie. Może to ostatecznie prowadzić do nieudanych uaktualnień, że limit czasu i wycofywania. Nieprzestrzeganie tokenu anulowania może również spowodować nierównomiejne klastry. Klastry stają się niezrównoważone, ponieważ węzły się nagrzewać. Jednak usługi nie można zrównoważyć, ponieważ trwa zbyt długo, aby przenieść je w innym miejscu. 

Ponieważ usługi są stanowe, jest również prawdopodobne, że usługi korzystają z [niezawodnych kolekcji.](service-fabric-reliable-services-reliable-collections.md) W sieci szkieletowej usług, gdy podstawowy jest obniżona, jedną z pierwszych rzeczy, które się dzieje jest to, że dostęp do zapisu do stanu źródłowego jest odwołany. Prowadzi to do drugiego zestawu problemów, które mogą mieć wpływ na cykl życia usługi. Kolekcje zwracają wyjątki na podstawie chronometrażu i czy replika jest przenoszona lub zamykana. Ważne jest, aby poprawnie obsługiwać te wyjątki. 

Wyjątki generowane przez sieci szkieletowe usług są trwałe [(`FabricException`)](https://docs.microsoft.com/java/api/system.fabric.exception) lub przejściowe [(`FabricTransientException`)](https://docs.microsoft.com/java/api/system.fabric.exception.fabrictransientexception). Trwałe wyjątki powinny być rejestrowane i generowane. Wyjątki przejściowe mogą być ponowione na podstawie logiki ponawiania.

Ważną częścią testowania i sprawdzania poprawności niezawodnych usług jest `ReliableCollections` obsługa wyjątków, które pochodzą z korzystania w połączeniu ze zdarzeniami cyklu życia usługi. Zaleca się, aby zawsze uruchamiać usługę pod obciążeniem. Należy również wykonać uaktualnienia i [testowania chaosu](service-fabric-controlled-chaos.md) przed wdrożeniem w procesach produkcyjnych. Te podstawowe kroki pomagają upewnić się, że usługa jest poprawnie zaimplementowana i że obsługuje zdarzenia cyklu życia poprawnie.

## <a name="notes-on-service-lifecycle"></a>Uwagi dotyczące cyklu życia serwisu
* Zarówno `runAsync()` metoda, `createServiceInstanceListeners/createServiceReplicaListeners` jak i wywołania są opcjonalne. Usługa może mieć jeden, oba lub żaden. Na przykład jeśli usługa wykonuje całą swoją pracę w odpowiedzi na wywołania `runAsync()`użytkownika, nie ma potrzeby implementowania . Tylko odbiorniki komunikacji i ich kod skojarzony są niezbędne.  Podobnie tworzenie i zwracanie odbiorników komunikacji jest opcjonalne. Usługa może mieć tylko pracę w tle do `runAsync()`wykonania, więc musi tylko zaimplementować .
* Jest prawidłowy dla usługi, `runAsync()` aby zakończyć pomyślnie i wrócić z niego. Nie jest to uważane za warunek awarii. Reprezentuje pracę w tle zakończenia usługi. W przypadku stanowych `runAsync()` niezawodnych usług zostanie wywołana ponownie, jeśli usługa zostanie obniżona z poziomu podstawowego, a następnie awansowana z powrotem do poziomu podstawowego.
* Jeśli usługa kończy `runAsync()` działanie przez zgłaszanie niektórych nieoczekiwany wyjątek, jest to błąd. Obiekt usługi zostanie zamknięty i zgłaszany jest błąd kondycji.
* Chociaż nie ma limitu czasu na powrót z tych metod, natychmiast tracisz zdolność do pisania. W związku z tym nie można wykonać żadnej prawdziwej pracy. Zalecamy jak najszybszy zwrot po otrzymaniu prośby o anulowanie. Jeśli usługa nie odpowiada na te wywołania interfejsu API w rozsądnym czasie, sieci szkieletowej usług może na stałe zakończyć usługę. Zwykle dzieje się tak tylko podczas uaktualniania aplikacji lub gdy usługa jest usuwana. Ten limit czasu wynosi domyślnie 15 minut.
* Błędy w `onCloseAsync()` ścieżce `onAbort()` powodują wywołanie. To wywołanie jest ostatnią szansą, najlepszym rozwiązaniem dla usługi, aby oczyścić i zwolnić wszelkie zasoby, które zostały zgłoszone. Jest to zwykle wywoływane, gdy w węźle zostanie wykryty błąd trwały lub gdy sieci szkieletowej usług nie można niezawodnie zarządzać cyklem życia wystąpienia usługi z powodu awarii wewnętrznych.
* `OnChangeRoleAsync()`jest wywoływana, gdy replika usługi stanowej zmienia rolę, na przykład na podstawową lub pomocniczą. Repliki podstawowe mają stan zapisu (mogą tworzyć i zapisywać w niezawodnych kolekcjach). Repliki pomocnicze mają stan odczytu (można odczytać tylko z istniejących niezawodnych kolekcji). Większość pracy w usłudze stanowej jest wykonywana w replice podstawowej. Repliki pomocnicze mogą wykonywać sprawdzanie poprawności tylko do odczytu, generowanie raportów, wyszukiwanie danych lub inne zadania tylko do odczytu.

## <a name="next-steps"></a>Następne kroki
* [Wprowadzenie do niezawodnych usług](service-fabric-reliable-services-introduction.md)
* [Szybki start niezawodnych usług](service-fabric-reliable-services-quick-start-java.md)


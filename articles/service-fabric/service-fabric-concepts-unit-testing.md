---
title: Testowanie jednostek usług stanowych w usłudze Azure Service Fabric | Dokumentacja firmy Microsoft
description: Więcej informacji na temat pojęć i rozwiązania w zakresie usług stanowych w usłudze Service Fabric testów jednostkowych.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/04/2018
ms.author: atsenthi
ms.openlocfilehash: ca473b9947a9b0df610a9c3dac66914b06cc9217
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58662571"
---
# <a name="unit-testing-stateful-services-in-service-fabric"></a>Testowanie jednostek usług stanowych w usłudze Service Fabric

W tym artykule opisano pojęcia i rozwiązania w zakresie usług stanowych w usłudze Service Fabric testów jednostkowych. Testowanie w ramach usługi Service Fabric jednostek zasługuje na swoje własne kryteria z faktu, że kod aplikacji aktywnie jest uruchamiany w wielu różnych kontekstach. W tym artykule opisano rozwiązań stosowanych w celu zapewnienia, że kod aplikacji jest objęta wszystkich kontekstów, które można uruchomić.

## <a name="unit-testing-and-mocking"></a>Jednostki testowania i pozorowanie
Testowanie jednostek w kontekście tego artykułu jest zautomatyzowane testy, które mogą być wykonywane w kontekście modułu uruchamiającego testy, takich jak MSTest czy NUnit. Testy jednostkowe w tym artykule nie należy wykonywać operacji względem zasobu zdalnego, takich jak bazy danych lub interfejsu API RESTFul. Te zasoby zdalne powinien być w postaci makiet. Pozorowanie w kontekście tego artykułu fałszywe rekord i kontrolować wartości zwracane dla zasobów zdalnych.

### <a name="service-fabric-considerations"></a>Zagadnienia dotyczące usługi Service Fabric
Jednostki testowania stanowej usługi Service Fabric zawiera kilka zagadnień. Po pierwsze kod usługi wykonuje w wielu węzłach, ale różnych ról. Testy jednostkowe powinny ocenić kod w każdej roli w celu osiągnięcia pełnego pokrycia. Różne role będzie podstawowy, aktywnej pomocniczej, bezczynności dodatkowej i nieznany. Brak roli nie zwykle wymaga specjalnych pokrycia jako usługi Service Fabric traktuje tę rolę się usługa void lub ma wartość null. Po drugie każdy węzeł zmieni swoją rolę w dowolnym danym momencie. Aby uzyskać pełne pokrycie, ścieżki wykonywania kodu powinien zostać przetestowany przy użyciu roli zmiany zachodzące.

## <a name="why-unit-test-stateful-services"></a>Dlaczego usług stanowych test jednostkowy? 
Testowanie usług stanowych jednostek może pomóc odkryć niektórych typowych pomyłek, które zostały wprowadzone, które będą nie musi być przechwycony przez konwencjonalne aplikacji lub testy jednostkowe specyficznego dla domeny. Na przykład jeśli usługa stanowa ma każdy stan w pamięci, tego rodzaju kontrolach może sprawdzić, czy ten stan w pamięci są utrzymywane w synchronizacji dla każdej repliki. Tego rodzaju kontrolach można również sprawdzić, czy usługa stanowa ma odpowiadać na tokenów anulowania przekazany aranżacji usługi Service Fabric odpowiednio. Po wyzwoleniu anulowania usługi powinien zatrzymanie wszystkich długo działa i/lub asynchronicznej operacji.  

## <a name="common-practices"></a>Typowe rozwiązania

Poniższa sekcja z informacją o tym w najbardziej typowe rozwiązania dotyczące usługi stanowej testów jednostkowych. Informuje również pozorowania warstwy mają do ściśle dopasować do organizowania usługi Service Fabric i zarządzania stanem. Dostępne są biblioteki pozorowania bibliotek, które udostępniają tę funkcję. [ServiceFabric.Mocks](https://www.nuget.org/packages/ServiceFabric.Mocks/) od 3.3.0 lub nowszej jest jedną bibliotekę, która udostępnia funkcje pozorowania, zalecane i korzysta z rozwiązania opisane poniżej.

### <a name="arrangement"></a>Rozmieszczenie

#### <a name="use-multiple-service-instances"></a>Korzystać z wielu wystąpień usługi
Testy jednostkowe powinny wykonać wiele wystąpień usługi stanowej. Symuluje to, co rzeczywiście się dzieje w klastrze, w którym usługi Service Fabric obsługuje wiele replik uruchamiania usługi w różnych węzłach. Każde z tych wystąpień będziesz wykonywać w innym kontekście jednak. Podczas uruchamiania testu, każde wystąpienie powinno przygotowywany przy użyciu konfiguracji roli oczekiwano w klastrze. Na przykład jeśli usługa jest powinny mieć rozmiar repliki docelowej 3, usługi Service Fabric będzie zainicjować obsługę administracyjną trzech replik w różnych węzłach. Jednym z nich jest podstawową a pozostałe dwa trwa aktywnej pomocniczej firmy.

W większości przypadków ścieżki wykonywania usługi różnią się nieznacznie dla każdego z tych ról. Na przykład jeśli usługa nie akceptuje postanowień żądań z aktywnej pomocniczej, usługa może być wyboru dla tego przypadku ponownie zgłosić, że podjęto informacyjne wyjątek, który wskazuje żądania na serwerze pomocniczym. Posiadanie wielu wystąpień umożliwi ta sytuacja ma zostać przetestowana.

Ponadto posiadanie wielu wystąpień pozwala testy, aby przełączyć role każdego z tych wystąpień, aby sprawdzić, czy odpowiedzi są spójne mimo zmiany roli.

#### <a name="mock-the-state-manager"></a>Testowanie przez menedżera stanu
Menedżer stanu należy traktowane jako zasób zdalny i w związku z tym w postaci makiet. Gdy pozorowanie przez menedżera stanu, musi istnieć niektórych podstawowych magazynu w pamięci, do śledzenia, co jest zapisywane do menedżera stanu, aby można go odczytać i zweryfikowane. Najprościej można to osiągnąć, jest tworzenie makiety wystąpień każdego z typów elementów Reliable Collections. W ramach tych mocks Użyj typu danych, pasującą ściśle zintegrowana za pomocą operacji wykonywanych względem tej kolekcji. Poniżej przedstawiono niektóre typy danych sugerowanych dla każdej kolekcji niezawodne

- IReliableDictionary<TKey, TValue> -> System.Collections.Concurrent.ConcurrentDictionary<TKey, TValue>
- IReliableQueue<T> -> System.Collections.Generic.Queue<T>
- IReliableConcurrentQueue<T> -> System.Collections.Concurrent.ConcurrentQueue<T>

#### <a name="many-state-manager-instances-single-storage"></a>Wiele wystąpień Menedżera stanu, pojedynczy magazyn
Jak wspomniano wcześniej, State Manager i elementów Reliable Collections powinny być traktowane jako zasób zdalny. Dlatego te zasoby powinny i będzie postaci makiet w ramach testów jednostkowych. Jednak podczas uruchamiania wielu wystąpień usługi stanowej będzie wezwanie do synchronizowania każdego menedżera stanu pozorowane między wystąpieniami innej usługi stanowej. Usługi stanowej uruchamianego w klastrze usługi Service Fabric zajmuje się zachowywanie menedżera stanu każdej repliki pomocniczej zgodnie z repliką podstawową. W związku z tym testy powinny działa tak samo, dzięki czemu symulują one zmiany roli.

Jest to prosty sposób, który można osiągnąć tej synchronizacji, można użyć wzorca singleton dla obiektu podstawowego, który przechowuje dane zapisywane w każdym Reliable Collection. Na przykład, jeśli korzysta z usługi stanowej `IReliableDictionary<string, string>`. Menedżer stanów makiety powinna zwrócić pozorny `IReliableDictionary<string, string>`. Ten projekt może używać `ConcurrentDictionary<string, string>` do śledzenia pary klucz/wartość, zapisane. `ConcurrentDictionary<string, string>` Powinien być pojedynczego używany przez wszystkie wystąpienia elementu menedżerów stanu przekazywany do usługi.

#### <a name="keep-track-of-cancellation-tokens"></a>Informacje o tokenów anulowania
Anulowanie tokenów są ważne jeszcze często pomijane aspektów usług stanowych. Podczas uruchamiania usługi Service Fabric replika podstawowa usługi stanowej, znajduje się token anulowania. Ten token anulowania jest przeznaczona do sygnalizowania do usługi, gdy zostanie usunięte, lub obniżenie poziomu do innej roli. Usługi stanowej należy zatrzymać wszystkie operacje długotrwałego lub asynchronicznego, tak, aby Usługa Service Fabric można wykonać przepływu pracy zmiany roli.

Podczas wykonywania testów jednostkowych, wszelkie tokenów anulowania, które są dostarczane do RunAsync, ChangeRoleAsync i OpenAsync, CloseAsync powinny odbywać się podczas wykonywania testów. Gospodarstwo na te tokeny umożliwi test, aby symulować zamknięcie usługi lub obniżania poziomu i sprawdź współpracuje usługi, odpowiednio.

#### <a name="test-end-to-end-with-mocked-remote-resources"></a>Test end-to-end z pozorowane zasoby zdalne
Testy jednostkowe powinien zostać wykonany tyle kod aplikacji, które można zmodyfikować stanu usługi stanowej, jak to możliwe. Zaleca się, że testy były bardziej end-to-end charakter. Tylko mocks, które istnieją dotyczą rejestrowania, symulowanie i/lub sprawdzić interakcje zasobu zdalnego. Dotyczy to również interakcje z State Manager i elementów Reliable Collections. Poniższy fragment kodu jest przykładem korniszon dla testu, który pokazuje end-to-end testowania:

```
    Given stateful service named "fabric:/MyApp/MyService" is created
    And a new replica is created as "Primary" with id "111"
    And a new replica is created as "IdleSecondary" with id "222"
    And a new replica is created as "IdleSecondary" with id "333"
    And all idle secondary replicas are promoted to active secondary
    When a request is made to add the an employee "John Smith"
    And the active secondary replica "222" is promoted to primary
    And a request is made to get all employees
    Then the request should should return the "John Smith" employee
```

Ten test potwierdza, że dane są przechwytywane na jednej z replik jest dostępna dla repliki pomocniczej po jego poziom jest podnoszony do podstawowej. Przy założeniu, że reliable collection jest magazyn zapasowy dane pracowników, Aa potencjalny błąd, który może zostać przechwycony z tym testem jest, jeśli kod aplikacji nie zostało wykonane `CommitAsync` transakcji można zapisać nowego pracownika. W takiej sytuacji drugie żądanie pobrania pracownicy nie będzie zwracać pracowników dodane przez pierwsze żądanie.

### <a name="acting"></a>Działając
#### <a name="mimic-service-fabric-replica-orchestration"></a>Naśladowanie aranżacji repliki usługi Service Fabric
Podczas zarządzania wiele wystąpień usługi, testy powinny inicjuje się i zatrzymywania te usługi w taki sam sposób jak aranżacji usługi Service Fabric. Na przykład po utworzeniu w nowej replice podstawowej usługi Service Fabric wywoła CreateServiceReplicaListener, OpenAsync, ChangeRoleAsync i RunAsync. Zdarzenia cyklu życia są opisane w następujących artykułach:

- [Uruchamianie usługi stanowej](service-fabric-reliable-services-lifecycle.md#stateful-service-startup)
- [Zamykanie usługi stanowej](service-fabric-reliable-services-lifecycle.md#stateful-service-shutdown)
- [Zamiany podstawowej usługi stanowej](service-fabric-reliable-services-lifecycle.md#stateful-service-primary-swaps)

#### <a name="run-replica-role-changes"></a>Uruchom zmiany roli repliki
Testy jednostkowe, należy zmienić role wystąpień usługi w taki sam sposób jak aranżacji usługi Service Fabric. Rola komputera stanu jest udokumentowany w następującym artykule:

[Stan roli repliki maszyny](service-fabric-concepts-replica-lifecycle.md#replica-role)

Zmiany roli Symulacja jest jednym z kluczowych aspektów testowania i odkrywać problemy, których stan repliki nie są zgodne ze sobą. Stan niespójną replikę może wystąpić z powodu przechowywania stanu w pamięci w statyczny lub zmienne poziomu wystąpienia klasy. Przykłady tego może być tokenów anulowania, wyliczeń i wartości obiektów konfiguracji. Pozwoli to również zagwarantować, że usługa jest uwzględnienie tokenów anulowania oferowane w trakcie RunAsync umożliwia zmianę roli. Zmiany roli Symulacja może ujawnić problemy, które mogą wystąpić, jeśli kod nie jest zapisywany umożliwia wywołanie RunAsync wiele razy.

#### <a name="cancel-cancellation-tokens"></a>Anulowanie tokenów anulowania
Powinna istnieć testów jednostkowych, gdzie token anulowania dostarczany do RunAsync zostało anulowane. Umożliwi to test, aby sprawdzić, czy usługa zostanie wyłączone poprawnie zamknięty. Podczas tego zamykania dowolne operacje długotrwałego lub asynchroniczne powinna zostać zatrzymana. Przykład długotrwałe procesu, który może znajdować się w usłudze to taki, który nasłuchuje komunikatów w kolejce niezawodne. To może istnieć bezpośrednio z poziomu metody RunAsync lub wątku w tle. Implementacja powinna zawierać logikę Kończenie operacji, jeśli ten token anulowania jest anulowane.

Jeśli używasz stanowe, które usługi pamięci podręcznej lub w pamięci stanu, który powinien istnieć tylko na podstawowym, powinny zostać usunięte w tej chwili. To, aby upewnić się, że ten stan jest zgodne, jeśli węzeł podstawowy ponownie później. Testowanie anulowania umożliwi test, aby sprawdzić, czy ten stan jest prawidłowo usunięte.

#### <a name="execute-requests-against-multiple-replicas"></a>Wykonywania żądań względem wielu replik
Potwierdź, że testy mają być wykonywane tego samego żądania i porównuje różne repliki. W połączeniu z zmiany roli, problemy spójności może być niepokryty. Przykład testu może wykonać następujące czynności:
1. Wykonywanie żądania zapisu względem bieżącego podstawowego
2. Wykonaj żądanie odczytu zwracające dane zapisane w kroku 1 względem bieżącego podstawowego
3. Wypromowanie pomocniczej do podstawowej bazy danych. Należy to również obniżyć poziom bieżącego podstawowej do dodatkowej
4. Wykonanie tego samego żądania odczytu w kroku 2 z nowym serwerem pomocniczym.

W ostatnim kroku test można assert, dane zwracane są spójne. Potencjalny problem, który to można odkryć to, że dane są zwracane przez usługę mogą być w pamięci, ale to jest poparte ostatecznie reliable collection. Te dane w pamięci nie jest zsynchronizowany prawidłowo z czym istnieje w niezawodnej kolekcji.

Dane w pamięci jest zazwyczaj używany do tworzenia indeksów pomocniczych lub agregacji danych, który istnieje w niezawodnej kolekcji.

### <a name="asserting"></a>Potwierdzanie
#### <a name="ensure-responses-match-across-replicas"></a>Upewnij się, że repliki pasować do odpowiedzi
Testy jednostkowe powinny potwierdzenia, że odpowiedzi dla danego żądania jest spójny w ramach wielu replik po ich przejście do podstawowej. To może pojawiać się potencjalnych problemów, gdzie dane dostarczone w odpowiedzi jest nie obsługiwane przez reliable collection albo przechowywane w pamięci bez mechanizm do synchronizowania danych w replikach. Pozwoli to zagwarantować, że usługa wysyła ponownie spójne odpowiedzi po usługi Service Fabric rebalances lub awaryjnie do nowej repliki podstawowej.

#### <a name="verify-service-respects-cancellation"></a>Sprawdź usługi względem anulowania
Długotrwałe lub asynchronicznych procesów, które powinno zostać zakończone, gdy token anulowania jest anulowane należy zweryfikować, że zakończony one faktycznie po anulowaniu. Pozwoli to zagwarantować, że pomimo repliki zmienia role, procesy, które nie są przeznaczone do pozostanie uruchomione po innych niż podstawowe repliki zatrzymane przed zakończeniem przejścia. To jest również ujawnić problemy, gdy taki proces blokuje żądania zmiany lub zamknięcie roli z usługi Service Fabric ukończenie.

#### <a name="verify-which-replicas-should-serve-requests"></a>Sprawdź replik, które powinny obsługiwać żądań
Testy powinny assert oczekiwane zachowanie, jeśli żądanie jest kierowane do repliki innych niż podstawowe. Usługi Service Fabric zapewnia możliwość ma obsługiwać żądania replik pomocniczych. Jednak operacje zapisu do elementów reliable collections może wystąpić tylko z repliki podstawowej. Jeśli aplikacja zamierza uzyskać tylko replikami podstawowymi obsługiwać żądań lub tylko podzbiór żądań może być obsługiwany przez pomocniczy, testy powinny assert oczekiwane zachowanie dodatnie i ujemne przypadków. Tak ujemna jest żądanie jest przekierowywane do repliki, który nie powinien obsługiwać żądania i pozytywny jest przeciwieństwem.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się, jak [usług stanowych testów jednostkowych](service-fabric-how-to-unit-test-stateful-services.md).
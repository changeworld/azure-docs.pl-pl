---
title: Testowanie usług stanowych jednostek w sieci szkieletowej usług Azure
description: Dowiedz się więcej o pojęciach i praktykach testowania jednostek usługi stanowe usługi sieci szkieletowej.
ms.topic: conceptual
ms.date: 09/04/2018
ms.openlocfilehash: 12e8a47d9685dee12594f4e2afaa848d9688d185
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75433906"
---
# <a name="unit-testing-stateful-services-in-service-fabric"></a>Jednostkowe testowanie usług stanowych w sieci szkieletowej usług

W tym artykule opisano pojęcia i praktyki jednostki testowania usługi sieci szkieletowej usługi stanowe. Testowanie jednostek w ramach sieci szkieletowej usług zasługuje na własne uwagi ze względu na fakt, że kod aplikacji aktywnie działa w wielu różnych kontekstach. W tym artykule opisano praktyki stosowane w celu zapewnienia, że kod aplikacji jest objęty każdym z różnych kontekstów, które można uruchomić.

## <a name="unit-testing-and-mocking"></a>Testowanie jednostkowe i szyderstwo
Testowanie jednostkowe w kontekście tego artykułu jest automatyczne testowanie, które mogą być wykonywane w kontekście karty wyników testów, takich jak MSTest lub NUnit. Testy jednostkowe w tym artykule nie wykonują operacji względem zasobu zdalnego, takiego jak baza danych lub interfejs API RESTFUL. Te zasoby zdalne powinny być wyśmiewane. Szydercze w kontekście tego artykułu będzie fałszywe, rekord i kontrolować wartości zwracane dla zasobów zdalnych.

### <a name="service-fabric-considerations"></a>Zagadnienia dotyczące sieci szkieletowej usług
Jednostka testowania usługi stanowej sieci szkieletowej usługi ma kilka zagadnień. Po pierwsze kod usługi jest wykonywany w wielu węzłach, ale w ramach różnych ról. Testy jednostkowe należy ocenić kod w ramach każdej roli, aby osiągnąć pełne pokrycie. Różne role będą podstawowe, aktywne pomocnicze, bezczynne pomocnicze i nieznany. Rola None zazwyczaj nie wymaga specjalnego pokrycia, ponieważ sieci szkielet usług uznaje tę rolę za nieprawidłową lub nullową usługę. Po drugie, każdy węzeł zmieni swoją rolę w danym momencie. Aby osiągnąć pełne pokrycie, ścieżka wykonywania kodu powinna być testowana z występującymi zmianami ról.

## <a name="why-unit-test-stateful-services"></a>Dlaczego jednostki test usług stanowych? 
Testowanie jednostek stanowych usług może pomóc odkryć niektóre typowe błędy, które są wykonane, które niekoniecznie zostaną przechwycone przez konwencjonalne aplikacji lub specyficzne dla domeny badania jednostki. Na przykład jeśli usługa stanowa ma dowolny stan w pamięci, ten typ testowania można sprawdzić, czy ten stan w pamięci jest synchronizowany między każdą repliką. Ten typ testowania można również sprawdzić, czy usługa stanowa odpowiada tokeny anulowania przekazywane przez aranżacji sieci szkieletowej usługi odpowiednio. Po wyzwoleniu anulowania usługa powinna zatrzymać wszelkie długotrwałe i/lub asynchroniczne operacje.  

## <a name="common-practices"></a>Wspólne praktyki

W poniższej sekcji zaleca się najbardziej typowe praktyki dotyczące testowania jednostkowego usługi stanowej. Zaleca się również, co ma być konieczności szydercze warstwy ściśle wyrównać do aranżacji sieci szkieletowej usług i zarządzania stanem. [ServiceFabric.Mocks](https://www.nuget.org/packages/ServiceFabric.Mocks/) od 3.3.0 lub nowszej jest jedną z takich bibliotek, która zapewnia funkcji szyderczy zalecane i wynika z praktyk opisanych poniżej.

### <a name="arrangement"></a>Układ

#### <a name="use-multiple-service-instances"></a>Używanie wielu wystąpień usługi
Testy jednostkowe należy wykonać wiele wystąpień usługi stanowej. Symuluje to, co faktycznie dzieje się w klastrze, w którym usługa Sieci szkieletowej zapewnia wiele replik uruchomionych usługi w różnych węzłach. Każde z tych wystąpień będzie jednak wykonywane w innym kontekście. Podczas uruchamiania testu, każde wystąpienie powinny być zagruntowane z konfiguracji roli oczekiwane w klastrze. Na przykład jeśli oczekuje się, że usługa ma docelowy rozmiar repliki 3, sieci szkieletowej usług będzie aprowizować trzy repliki w różnych węzłach. Jednym z nich jest podstawowy, a pozostałe dwa są Active Secondary's.

W większości przypadków ścieżka wykonywania usługi będzie się nieznacznie różnić dla każdej z tych ról. Na przykład jeśli usługa nie powinna akceptować żądań z Active Secondary, usługa może mieć czek dla tego przypadku, aby zgłosić wyjątek informacyjny, który wskazuje, że żądanie zostało podjęte w pomocniczej. Mając wiele wystąpień pozwoli tej sytuacji do testowania.

Ponadto o wiele wystąpień umożliwia testy, aby przełączyć role każdego z tych wystąpień, aby sprawdzić odpowiedzi są spójne pomimo zmian roli.

#### <a name="mock-the-state-manager"></a>Makieta menedżera stanu
Menedżer stanu powinny być traktowane jako zdalnego zasobu i w związku z tym wyśmiewane. Podczas szydzenia menedżera stanu, musi istnieć kilka podstawowych magazynu w pamięci do śledzenia, co jest zapisywane w menedżerze stanu, dzięki czemu można go odczytać i zweryfikować. Prosty sposób, aby to osiągnąć jest utworzenie makiety wystąpień każdego z typów niezawodnych kolekcji. W ramach tych makiet użyj typu danych, który ściśle jest zgodny z operacjami wykonywanymi względem tej kolekcji. Poniżej przedstawiono niektóre sugerowane typy danych dla każdego wiarygodnego

- IReliableDictionary<TKey, TValue> -> System.Collections.Concurrent.ConcurrentDictionary<TKey, TValue>
- IReliableQueue\<T> -> System.Collections.Generic.Queue\<T>
- IReliableConcurrentQueue\<T> -> System.Collections.Concurrent.ConcurrentQueue\<T>

#### <a name="many-state-manager-instances-single-storage"></a>Wiele wystąpień Menedżera stanu, pojedynczy magazyn
Jak wspomniano wcześniej, Menedżer stanu i niezawodne kolekcje powinny być traktowane jako zdalnego zasobu. W związku z tym te zasoby powinny i będą szydzi w ramach testów jednostkowych. Jednak podczas uruchamiania wielu wystąpień usługi stanowej będzie wyzwaniem, aby zachować każdy szyderczy menedżer stanu w synchronizacji w różnych wystąpieniach usługi stanowej. Gdy usługa stanowa jest uruchomiona w klastrze, sieci szkieletowej usług zajmuje się utrzymaniem menedżera stanu każdej repliki pomocniczej zgodne z repliki podstawowej. W związku z tym testy powinny zachowywać się tak samo, aby mogły symulować zmiany roli.

Prosty sposób tej synchronizacji można osiągnąć, jest użycie wzorca singleton dla obiektu źródłowego, który przechowuje dane zapisane w każdej niezawodnej kolekcji. Na przykład, jeśli usługa stanowa `IReliableDictionary<string, string>`używa pliku . Menedżer stanu makiety powinien `IReliableDictionary<string, string>`zwrócić makietę . Ten makieta `ConcurrentDictionary<string, string>` może używać do śledzenia par klucz/wartość napisane. Powinien `ConcurrentDictionary<string, string>` być singleton używane przez wszystkie wystąpienia menedżerów stanu przekazywane do usługi.

#### <a name="keep-track-of-cancellation-tokens"></a>Śledzenie tokenów anulowania
Tokeny anulowania są ważnym, ale często pomijanym aspektem usług stanowych. Gdy usługa Service Fabric uruchamia replikę podstawową dla usługi stanowej, udostępniany jest token anulowania. Ten token anulowania jest przeznaczony do sygnalizowania do usługi, gdy zostanie usunięty lub obniżona do innej roli. Usługa stanowa powinna zatrzymać wszelkie długotrwałe lub asynchroniczne operacje, dzięki czemu sieci szkieletowej usług można ukończyć przepływ pracy zmiany roli.

Podczas uruchamiania testów jednostkowych wszystkie tokeny anulowania, które są dostarczane do RunAsync, ChangeRoleAsync, OpenAsync i CloseAsync powinny być przechowywane podczas wykonywania testu. Przytrzymanie tych tokenów pozwoli test symulować zamknięcie lub degradacji usługi i sprawdzić, czy usługa reaguje odpowiednio.

#### <a name="test-end-to-end-with-mocked-remote-resources"></a>Testowanie end-to-end z szyderczych zasobów zdalnych
Testy jednostkowe należy wykonać jak najwięcej kodu aplikacji, które można zmodyfikować stan usługi stanowej, jak to możliwe. Zaleca się, aby testy były bardziej end-to-end w przyrodzie. Tylko kpiny, które istnieją są do rejestrowania, symulowania i/lub weryfikacji interakcji zdalnych zasobów. Obejmuje to interakcje z Menedżerem stanu i niezawodne kolekcje. Poniższy fragment kodu jest przykładem korniszonu dla testu, który pokazuje kompleksowe testowanie:

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

Ten test potwierdza, że dane przechwycone na jednej replice jest dostępna dla repliki pomocniczej, gdy jest promowany do podstawowego. Zakładając, że kolekcja niezawodne jest magazynem kopii zapasowych dla danych pracownika, Aa potencjalny błąd, który może zostać przechwycony z tego testu jest, jeśli kod aplikacji nie został wykonany `CommitAsync` w transakcji, aby zapisać nowego pracownika. W takim przypadku drugie żądanie uzyskania pracowników nie zwróci pracownika dodanego przez pierwsze żądanie.

### <a name="acting"></a>Działając
#### <a name="mimic-service-fabric-replica-orchestration"></a>Aranżacja repliki sieci szkieletowej mimic service
Podczas zarządzania wieloma wystąpieniami usługi, testy należy zainicjować i zniszczyć te usługi w taki sam sposób jak aranżacji sieci szkieletowej usług. Na przykład, gdy usługa jest tworzona na nowej repliki podstawowej, sieć szkieletowa usług wywoła CreateServiceReplicaListener, OpenAsync, ChangeRoleAsync i RunAsync. Zdarzenia cyklu życia są udokumentowane w następujących artykułach:

- [Uruchamianie usługi stanowej](service-fabric-reliable-services-lifecycle.md#stateful-service-startup)
- [Stanowe zamknięcie usługi](service-fabric-reliable-services-lifecycle.md#stateful-service-shutdown)
- [Zamiany podstawowe usługi stanowej](service-fabric-reliable-services-lifecycle.md#stateful-service-primary-swaps)

#### <a name="run-replica-role-changes"></a>Uruchamianie zmian roli repliki
Testy jednostkowe należy zmienić role wystąpień usługi w taki sam sposób jak aranżacji sieci szkieletowej usług. Maszyna stanu roli jest udokumentowana w następującym artykule:

[Maszyna stanu roli repliki](service-fabric-concepts-replica-lifecycle.md#replica-role)

Symulowanie zmian ról jest jednym z bardziej krytycznych aspektów testowania i może odkryć problemy, w których stan repliki nie są ze sobą spójne. Niespójny stan repliki może wystąpić z powodu przechowywania stanu w pamięci w zmiennych wystąpienia na poziomie statycznym lub klasy. Przykładami mogą być tokeny anulowania, wyliczenia i obiekty konfiguracji/wartości. Zapewni to również, że usługa jest respektowanie tokenów anulowania dostarczonych podczas RunAsync, aby umożliwić zmianę roli występuje. Symulowanie zmiany roli można również odkryć problemy, które mogą pojawić się, jeśli kod nie jest napisany, aby umożliwić wywołanie RunAsync wiele razy.

#### <a name="cancel-cancellation-tokens"></a>Anuluj tokeny anulowania
Powinny istnieć testy jednostkowe, w których token anulowania dostarczony do RunAsync zostanie anulowany. Umożliwi to test, aby sprawdzić, czy usługa bezpiecznie zamyka. Podczas tego zamknąć wszelkie długotrwałe lub asynchroniczne operacje powinny zostać zatrzymane. Przykład długotrwałego procesu, który może istnieć w usłudze jest taki, który nasłuchuje wiadomości w kolejce niezawodne. Może to istnieć bezpośrednio w RunAsync lub wątku w tle. Implementacja powinna zawierać logikę do zamykania operacji, jeśli ten token anulowania zostanie anulowany.

Jeśli usługi stanowe korzystają z dowolnej pamięci podręcznej lub stanu w pamięci, który powinien istnieć tylko na podstawowym, powinny być usuwane w tej chwili. Ma to na celu zapewnienie, że ten stan jest spójny, jeśli węzeł staje się podstawowym ponownie później. Testowanie anulowania pozwoli testowi sprawdzić, czy ten stan jest usuwany prawidłowo.

#### <a name="execute-requests-against-multiple-replicas"></a>Wykonywanie żądań względem wielu replik
Assert testy należy wykonać to samo żądanie dla różnych replik. Po sparowaniu ze zmianami ról można odkryć problemy ze spójnością. Przykładowy test może wykonać następujące kroki:
1. Wykonywanie żądania zapisu względem bieżącej podstawowej
2. Wykonanie żądania odczytu, które zwraca dane zapisane w kroku 1 względem bieżącego podstawowego
3. Podwyższanie poziomu pomocniczego do podstawowego. Powinno to również obniżyć obecną pierwotną do drugorzędną
4. Wykonaj to samo żądanie odczytu z kroku 2 względem nowego pomocniczego.

W ostatnim kroku test może potwierdzić, że zwrócone dane są spójne. Potencjalny problem, który może to odkryć jest, że dane zwracane przez usługę może być w pamięci, ale ostatecznie wspierane przez kolekcję niezawodne. Te dane w pamięci mogą nie być poprawnie zsynchronizowane z tym, co istnieje w niezawodnej kolekcji.

Dane w pamięci jest zwykle używany do tworzenia indeksów pomocniczych lub agregacji danych, które istnieją w niezawodnej kolekcji.

### <a name="asserting"></a>Potwierdzające
#### <a name="ensure-responses-match-across-replicas"></a>Upewnij się, że odpowiedzi są zgodne z replikami
Testy jednostkowe należy potwierdzić, że odpowiedź dla danego żądania jest spójne w wielu replikach po przejściu do podstawowego. Może to powierzchni potencjalnych problemów, gdzie dane dostarczone w odpowiedzi nie jest wspierany przez kolekcji niezawodne lub przechowywane w pamięci bez mechanizmu synchronizacji tych danych między replikami. Zapewni to, że usługa wysyła z powrotem spójne odpowiedzi po ponownej równoważenia sieci szkieletowej usług lub awaryjnie do nowej repliki podstawowej.

#### <a name="verify-service-respects-cancellation"></a>Weryfikowanie, że usługa respektuje anulowanie
Długotrwałe lub asynchroniczne procesy, które powinny zostać zakończone po anulowaniu tokenu anulowania, powinny zostać zweryfikowane, że faktycznie zostały zakończone po anulowaniu. Zapewni to, że pomimo repliki zmiany ról, procesy, które nie są przeznaczone do utrzymania uruchomiony na repliki innych niż podstawowe zatrzymać przed zakończeniem przejścia. Może to również odkryć problemy, w których taki proces blokuje ukończenie żądania zmiany roli lub zamknięcia z sieci szkieletowej usług.

#### <a name="verify-which-replicas-should-serve-requests"></a>Sprawdzanie, które repliki powinny obsługiwać żądania
Testy powinny potwierdzać oczekiwane zachowanie, jeśli żądanie jest kierowane do repliki innych niż podstawowe. Sieci szkieletowej usług zapewnia możliwość mają repliki pomocnicze obsługują żądania. Jednak zapisuje do kolekcji niezawodne mogą wystąpić tylko z repliki podstawowej. Jeśli aplikacja ma na celu tylko repliki podstawowe do obsługi żądań lub tylko podzbiór żądań mogą być obsługiwane przez pomocnicze, a następnie testy powinny potwierdzić oczekiwane zachowanie zarówno w przypadkach dodatnich i negatywnych. Przypadek ujemny jest żądanie jest kierowane do repliki, która nie powinna obsługiwać żądania i dodatni jest odwrotnie.

## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak [przetestować usługi stanowe.](service-fabric-how-to-unit-test-stateful-services.md)

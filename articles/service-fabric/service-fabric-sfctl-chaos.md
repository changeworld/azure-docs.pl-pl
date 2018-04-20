---
title: Azure Service Fabric interfejsu wiersza polecenia - sfctl choas | Dokumentacja firmy Microsoft
description: Informacje dotyczące poleceń chaos sfctl interfejsu wiersza polecenia usługi sieci szkieletowej.
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 02/22/2018
ms.author: ryanwi
ms.openlocfilehash: 34e4d47b1de509c2053996d9d1078733d7055447
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2018
---
# <a name="sfctl-chaos"></a>sfctl chaos
Uruchom, Zatrzymaj, a raport dotyczący usługi testu chaos.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
|    raport| Pobiera następny segment Chaos raportu na podstawie token kontynuacji przekazany lub przekazany w czasie zakresie.|
|    rozpoczynanie | Uruchamia Chaos w klastrze.|
|    Zatrzymaj  | Zatrzymuje Chaos w klastrze, jeśli jest już uruchomiona, w przeciwnym razie go nie działa.|


## <a name="sfctl-chaos-report"></a>sfctl chaos raportu
Pobiera następny segment Chaos raportu na podstawie token kontynuacji przekazany lub przekazany w czasie zakresie.

Można określić ContinuationToken uzyskać następny segment raportu Chaos lub można określić zakres czasu za pośrednictwem StartTimeUtc i EndTimeUtc, ale nie można określić zarówno ContinuationToken, jak i zakres czasu, w tym samym wywołaniu. W przypadku więcej niż 100 zdarzeń Chaos raportu Chaos jest zwracany w segmentów gdy segment zawiera nie więcej niż 100 Chaos zdarzeń. 

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --token kontynuacji| Parametr token kontynuacji służy do uzyskiwania następnej zestaw wyników. Token kontynuacji z wartość pusta jest uwzględniana w odpowiedzi interfejsu API wyników z systemu nie mieszczą się w jednej odpowiedzi. Jeśli ta wartość jest przekazywany do następnego wywołania interfejsu API interfejsu API zwraca następny zestaw wyników. Jeśli nie są dalsze wyniki token kontynuacji nie zawiera wartości. Wartość tego parametru nie powinny być zakodowane w adresie URL.|
| --Godzina zakończenia utc   | Czas reprezentującą godzinę zakończenia przedziału czasu, dla którego ma być generowany raport Chaos plików systemu Windows. Przejrzyj [metody DateTime.ToFileTimeUtc](https://msdn.microsoft.com/library/system.datetime.tofiletimeutc(v=vs.110).aspx) szczegółowe informacje.|
| --utc-start-time | Czas reprezentującą godzinę rozpoczęcia przedziału czasu, dla którego ma być generowany raport Chaos plików systemu Windows. Przejrzyj [metody DateTime.ToFileTimeUtc](https://msdn.microsoft.com/library/system.datetime.tofiletimeutc(v=vs.110).aspx) szczegółowe informacje.|
| limit czasu — -t     | W sekundach limit czasu serwera.  Domyślnie: 60.|

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug          | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki.|
| — Pomoc -h        | Pokaż ten komunikat pomocy i Zakończ.|
| --output -o      | Format danych wyjściowych.  Dozwolone wartości: json, jsonc, tabeli, tsv.  Domyślne: json.|
| — zapytania          | Ciąg zapytania JMESPath. Zobacz http://jmespath.org/ dodatkowe informacje i przykłady.|
| -verbose        | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna.|

## <a name="sfctl-chaos-start"></a>sfctl chaos start
Uruchamia Chaos w klastrze.

Jeśli Chaos nie jest już uruchomiona w klastrze, Chaos rozpoczyna się od przekazanych parametrów Chaos. Jeśli Chaos jest już uruchomiona, gdy jest to wywołanie, połączenie kończy się niepowodzeniem z kodem błędu FABRIC_E_CHAOS_ALREADY_RUNNING.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --aplikacji typu kondycji zasad map  | Zakodowane JSON listy o złej kondycji aplikacji maksymalny procent dla typów określonej aplikacji. Każdy wpis określa jako klucz, nazwa typu aplikacji i jako wartość całkowitą reprezentującą procent MaxPercentUnhealthyApplications używane do analizowania aplikacji typu określonej aplikacji. Definiuje mapowanie o złej kondycji aplikacji maksymalny procent dla typów określonej aplikacji. Każdy wpis określa jako klucz, nazwa typu aplikacji, a wartość całkowitą reprezentującą procent MaxPercentUnhealthyApplications używane do analizowania aplikacji typu określonej aplikacji. Mapowanie zasad kondycji typu aplikacji można podczas oceny kondycji klastra opisano typy aplikacji specjalnych. Typy aplikacji uwzględniony w mapowaniu są oceniane pod względem procent określone w planie, a nie z globalnego MaxPercentUnhealthyApplications, zdefiniowane w klastrze zasad dotyczących kondycji. Aplikacje określone w planie typy aplikacji nie są uwzględniane w globalnej puli aplikacji. Na przykład, jeśli niektóre aplikacje typu krytycznego, administrator klastra można dodać wpis do mapy dla tego typu aplikacji i przypisz jej wartość 0% (to znaczy nie tolerowanie awarii). Wszystkie inne aplikacje może przyjąć z MaxPercentUnhealthyApplications ustawioną 20% tolerować pewne awarie poza wielu wystąpień aplikacji. Mapowanie zasad kondycji typu aplikacji jest używany tylko wtedy, gdy w manifeście klastra umożliwia ocenę kondycji typu aplikacji za pomocą pozycji konfiguracji dla HealthManager/EnableApplicationTypeHealthEvaluation.|
|--chaos-target-filter         | JSON zakodowane słownika za pomocą dwóch kluczy typu ciąg. Dwa klucze są NodeTypeInclusionList i ApplicationInclusionList. Wartości dla obu tych kluczy są listy ciągów. chaos_target_filter definiuje wszystkie filtry docelowe Chaos błędów, na przykład powodujący błąd tylko niektóre typy węzłów lub powodujący błąd tylko określonych aplikacji. Jeśli chaos_target_filter nie jest używany, Chaos błędów wszystkich jednostek klastra. Użycie chaos_target_filter Chaos błędów jednostek spełniających specyfikację chaos_target_filter. NodeTypeInclusionList i ApplicationInclusionList umożliwiają tylko Unii semantyki. Nie jest możliwe określenie przecięcia NodeTypeInclusionList i ApplicationInclusionList. Na przykład nie jest możliwe określenie "fault tej aplikacji, tylko wtedy, gdy jest tego typu węzła". Gdy podmiot jest uwzględniony w NodeTypeInclusionList lub ApplicationInclusionList, tej jednostki nie można wykluczyć przy użyciu ChaosTargetFilter. Nawet wtedy, gdy nie ma applicationX ApplicationInclusionList, w niektórych iteracji Chaos applicationX może być uszkodzona ponieważ zdarza się w węźle nodeTypeY, który znajduje się w NodeTypeInclusionList. Jeśli zarówno NodeTypeInclusionList i ApplicationInclusionList są puste, zostanie zgłoszony ArgumentException. Wszystkie typy błędów (ponownie uruchomić węzeł, ponownie uruchomić pakietu kodu Usuń replikę, uruchom ponownie repliki, Przenieś podstawowego i Przenieś dodatkowej) są włączone dla węzłów typu węzła. Jeśli typ węzła (powiedzieć NodeTypeX) nie ma NodeTypeInclusionList, a następnie błędów poziomu węzła (na przykład NodeRestart) nigdy nie zostanie włączona dla węzłów NodeTypeX, ale błędów kodu pakietu i repliki nadal mogą być włączone dla NodeTypeX, jeśli aplikacja programu ApplicationInclusionList odbywa się w węźle NodeTypeX. Co najwyżej 100 nazwy typu węzła można dołączyć do tej listy, aby zwiększyć ten numer, konfiguracja MaxNumberOfNodeTypesInChaosEntityFilter wymagany jest uaktualnienie konfiguracji. Wszystkie repliki należące do usługi te aplikacje są nadającymi się do repliki usterek (repliki ponownego uruchomienia, Usuń replikę, Przenieś podstawowe i pomocnicze przenoszenia) przez Chaos. Chaos może ponownie uruchomić pakietu kodu tylko wtedy, gdy pakiet kodu hostem repliki tylko te aplikacje. Jeśli aplikacja nie ma na liście, go może nadal być umieszczone w niektórych iteracji Chaos Jeśli kończenia aplikacji w węźle typu węzła, który znajduje się w NodeTypeInclusionList. Jednak jeśli applicationX jest związany z nodeTypeY za pośrednictwem ograniczenia umieszczania i applicationX jest nieobecny z ApplicationInclusionList i nodeTypeY jest nieobecny z NodeTypeInclusionList, następnie applicationX nigdy nie wystąpi błąd. Maksymalnie 1000 nazwy aplikacji można dołączyć do tej listy, aby zwiększyć ten numer, uaktualnienia konfiguracji jest wymagany dla konfiguracji MaxNumberOfApplicationsInChaosEntityFilter.|
|— kontekst                     | Mapa kodowany w formacie JSON (ciąg) wpisz pary klucz wartość. Mapy może służyć do rejestrowania informacji o przebiegu Chaos. Nie może być więcej niż 100 takie pary, a każdy ciąg (klucz lub wartość) może mieć maksymalnie 4095 znaków. Ta mapa jest ustawiana przez starter milczenia Uruchom na przechowywanie w kontekście o określonym przebiegu.|
| --disable-move-replica-faults | Wyłącza podstawowy przenoszenia i Przenieś dodatkowej błędów.|
| -max klastra stabilizacji| Maksymalna ilość czasu oczekiwania dla wszystkich klastrów jednostki do stają się stabilne i działa prawidłowo.  Domyślnie: 60. Wykonuje chaos w iteracji i na początku każdej iteracji sprawdza poprawność kondycji jednostek klastra. Jeśli jednostka klastra nie jest stabilna i dobrej kondycji w MaxClusterStabilizationTimeoutInSeconds, podczas sprawdzania poprawności Chaos generuje zdarzenie zakończonych niepowodzeniem sprawdzania poprawności.|
| -max równoczesnych — błędy    | Maksymalna liczba jednoczesnych błędów powstaniu na iterację.           Domyślne: 1. Wykonuje chaos w iteracji i dwóch kolejnych iteracji są oddzielone w fazie sprawdzania poprawności. Im wyższa współbieżności, bardziej agresywną iniekcji błędów--wywołania bardziej złożonych serii stanów do wykrycia błędów. Zalecane jest, aby uruchomić o wartości 2 lub 3 i należy zachować ostrożność podczas przenoszenia w.|
| --max-percent-unhealthy-apps  | Podczas oceny kondycji klastra podczas Chaos, maksymalna dozwolona wartość procentowa w złej kondycji aplikacji przed zgłoszeniem błędu. Maksymalna dozwolona wartość procentowa w złej kondycji aplikacji przed zgłoszeniem błędu. Na przykład aby umożliwić 10% aplikacji jest zła, ta wartość wynosi 10. Wartość procentowa reprezentuje maksymalny procent tolerowaną aplikacje, które mogą być zła, zanim klaster zostanie uznane za błąd. Jeśli wartość procentowa jest przestrzegana, ale istnieje co najmniej jednej aplikacji w złej kondycji, kondycji jest szacowana jako ostrzeżenia. To jest obliczany przez podzielenie liczby złej kondycji aplikacji za pośrednictwem całkowita liczba wystąpień aplikacji w klastrze, z wyjątkiem aplikacji typów aplikacji, które znajdują się w ApplicationTypeHealthPolicyMap. Obliczenia Zaokrągla wartość w do tolerowanie awarii jednego w małej liczby aplikacji. Domyślnej wartości procentowej wynosi zero.|
| -max procent —-węzłów w złej kondycji | Podczas oceny kondycji klastra podczas Chaos, maksymalna dozwolona wartość procentowa węzłów w złej kondycji przed zgłoszeniem błędu. Maksymalna dozwolona wartość procentowa węzłów w złej kondycji przed zgłoszeniem błędu. Na przykład aby umożliwić 10% węzłów się zła, wartość ta wynosi 10. Wartość procentowa reprezentuje maksymalny procent tolerowaną węzły, które mogą być zła, zanim klaster zostanie uznane za błąd. Jeśli wartość procentowa jest przestrzegana, ale istnieje co najmniej jeden węzeł złej kondycji, kondycji jest szacowana jako ostrzeżenia. Wartość procentowa jest obliczana przez podzielenie liczby węzłów w złej kondycji przez całkowitą liczbę węzłów w klastrze. Obliczenia Zaokrągla wartość w do tolerowanie awarii jednego w małej liczby węzłów. Domyślnej wartości procentowej wynosi zero. W dużych klastrach niektóre węzły będą zawsze miały w dół lub wychodzących do naprawy, dzięki tej wartości procentowej, należy skonfigurować tolerować, który.|
| — czas do uruchomienia              | Łączny czas (w sekundach), dla którego Chaos zostanie uruchomione przed zatrzymaniem automatycznie. Maksymalna dozwolona wartość to 4 294 967 295 (System.UInt32.MaxValue).  Default: 4294967295.|
| limit czasu — -t               | W sekundach limit czasu serwera.  Domyślnie: 60.|
| --oczekiwania czas między błędów | Poczekaj czas (w sekundach) między kolejnych błędów w ramach pojedynczego iteracji.  Domyślne: 20. Im większa wartość, tym mniejszy nakładających się między usterek i łatwiejsze sekwencji stanu przejścia przechodzącego przez klaster. Zaleca się rozpoczynać wartość z zakresu od 1 i 5 i wykonywania ostrożność podczas przenoszenia w.|
| --oczekiwania czas między iteracji| Czas — rozdzielenie (w sekundach) między dwoma kolejnych iteracji milczenia. Im większa wartość, tym niższy ilość iniekcji błędów. Domyślne: 30.|
| --warning-as-error         | Podczas oceny kondycji klastra podczas Chaos, Traktuj ostrzeżenia o tej samej ważność jako błędy.|

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug                    | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki.|
| — Pomoc -h                  | Pokaż ten komunikat pomocy i Zakończ.|
| --output -o                | Format danych wyjściowych.  Dozwolone wartości: json, jsonc, tabeli, tsv.           Domyślne: json.|
| — zapytania                    | Ciąg zapytania JMESPath. Zobacz http://jmespath.org/ dodatkowe informacje i przykłady.|
| -verbose                  | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna.|

## <a name="sfctl-chaos-stop"></a>Zatrzymaj chaos sfctl
Zatrzymuje Chaos w klastrze, jeśli jest już uruchomiona, w przeciwnym razie go nie działa.

Zatrzymuje Chaos z planowania dalsze błędy; jednak nie dotyczy locie usterek.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| limit czasu — -t| W sekundach limit czasu serwera.  Domyślnie: 60.|

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug  | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki.|
| — Pomoc -h| Pokaż ten komunikat pomocy i Zakończ.|
| --output -o | Format danych wyjściowych.  Dozwolone wartości: json, jsonc, tabeli, tsv.  Domyślne: json.|
| — zapytania  | Ciąg zapytania JMESPath. Zobacz http://jmespath.org/ dodatkowe informacje i przykłady.|
| -verbose| Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna.|

## <a name="next-steps"></a>Kolejne kroki
- [Instalator](service-fabric-cli.md) sieci szkieletowej usług interfejsu wiersza polecenia.
- Dowiedz się, jak używać przy użyciu interfejsu wiersza polecenia usługi sieć szkieletowa [przykładowe skrypty](/azure/service-fabric/scripts/sfctl-upgrade-application).
---
title: Azure interfejsu wiersza polecenia usługi Service Fabric - sfctl chaos | Dokumentacja firmy Microsoft
description: W tym artykule opisano poleceń interfejsu wiersza polecenia usługi Service Fabric chaos sfctl.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: b584ec301f0f4841c8df8fbbafb410abf645c373
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58666753"
---
# <a name="sfctl-chaos"></a>sfctl chaos
Uruchamianie, zatrzymywanie i sporządzić raport na temat usługi testu chaos.

## <a name="subgroups"></a>Podgrupy
|Podgrupy|Opis|
| --- | --- |
| [schedule](service-fabric-sfctl-chaos-schedule.md) | Pobieranie i ustawianie harmonogramu chaos. |
## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| zdarzenia | Pobiera następny segment zdarzenia Chaos na podstawie token kontynuacji lub zakresu czasu. |
| Pobierz | Pobierz stan Chaos. |
| rozpoczynanie | Uruchamia Chaos w klastrze. |
| Zatrzymaj | Zatrzymuje chaosu, jeśli jest uruchomiony w klastrze i umieścić harmonogram Chaos w stanie zatrzymania. |

## <a name="sfctl-chaos-events"></a>Interfejs sfctl chaos zdarzenia
Pobiera następny segment zdarzenia Chaos na podstawie token kontynuacji lub zakresu czasu.

Aby pobrać następny segment zdarzenia chaosu, można określić token kontynuacji. Aby uzyskać rozpoczęcia nowego segmentu Chaos zdarzeń, można określić zakres czasu za pośrednictwem StartTimeUtc i EndTimeUtc. Nie można określić zakres czasu i token kontynuacji w tym samym wywołaniu. W przypadku więcej niż 100 zdarzeń Chaos Chaos zdarzenia są zwracane w wielu segmentów, w których segment zawiera nie więcej niż 100 zdarzeń Chaos i pobrać następny segment z wywołania do tego interfejsu API przy użyciu tokenu kontynuacji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --token kontynuacji | Parametr tokenu kontynuacji służy do uzyskania następny zestaw wyników. Token kontynuacji o wartości niepuste znajduje się w odpowiedzi interfejsu API, gdy wyniki z systemu nie mieszczą się w jednej odpowiedzi. Jeśli ta wartość jest przekazywana do następnego wywołania interfejsu API, interfejs API zwraca następny zestaw wyników. Jeśli nie istnieją żadne dalsze wyniki, token kontynuacji nie zawiera wartości. Wartość tego parametru nie powinny być zakodowane w adresie URL. |
| --end-time-utc | Windows pliku reprezentuje czas zakończenia przedziału czasu, dla którego ma być generowany raport Chaos. Zapoznaj się z [metoda DateTime.ToFileTimeUtc](https://msdn.microsoft.com/library/system.datetime.tofiletimeutc(v=vs.110).aspx) Aby uzyskać szczegółowe informacje. |
| --max-results | Maksymalna liczba wyników do zwrócenia w ramach kwerend stronicowanych. Ten parametr określa górną granicę na liczbie zwrócone wyniki. Wyniki zwracane, może być mniejsza niż określona maksymalna liczba wyników, jeśli nie mieszczą się w komunikacie zgodnie z ograniczeniami rozmiar maksymalny komunikatu zdefiniowane w konfiguracji. Jeśli ten parametr ma wartość zero lub nie określono, stronicowane zapytanie zawiera dowolną liczbę wyników jak to możliwe, który mieści się w komunikacie zwrotu. |
| --start-time-utc | Windows pliku reprezentuje czas rozpoczęcia zakresu czasu, dla którego ma być generowany raport Chaos. Zapoznaj się z [metoda DateTime.ToFileTimeUtc](https://msdn.microsoft.com/library/system.datetime.tofiletimeutc(v=vs.110).aspx) Aby uzyskać szczegółowe informacje. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-chaos-get"></a>Interfejs sfctl chaos get
Pobierz stan Chaos.

Pobierz stan Chaos wskazującą, czy Chaos jest uruchomiona, parametry Chaos używane do uruchamiania Chaotyczne i stan harmonogramu Chaos.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-chaos-start"></a>Interfejs sfctl chaos start
Uruchamia Chaos w klastrze.

Jeśli Chaos nie jest już uruchomiona w klastrze, zaczyna Chaos przekazanych w parametrach Chaos. Jeśli Chaos jest już uruchomiona po tym wywołaniu, podając mu kod błędu FABRIC_E_CHAOS_ALREADY_RUNNING nie powiedzie się wywołanie.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --— Typ kondycji — zasady — Mapa aplikacji | Lista z maksymalny procent aplikacji w złej kondycji dla aplikacji o określonych typach zakodowane JSON. Każdy wpis określa jako klucz, nazwa typu aplikacji i jako wartość typu Liczba całkowita, która reprezentuje procent MaxPercentUnhealthyApplications używane do analizowania aplikacji typu określonej aplikacji. <br><br> Definiuje mapowanie o maksymalny procent aplikacji w złej kondycji dla aplikacji o określonych typach. Każdy wpis określa jako klucz, nazwa typu aplikacji, a wartość całkowitą, która reprezentuje procent MaxPercentUnhealthyApplications używane do analizowania aplikacji typu określonej aplikacji. Mapa zasad kondycji typ aplikacji może służyć podczas oceny kondycji klastra do opisania typy specjalne aplikacji. Typy aplikacji uwzględniony w mapowaniu są sprawdzane pod kątem wartości procentowej określony na mapie, a nie z globalnego MaxPercentUnhealthyApplications, zdefiniowane w zasadach kondycji klastra. Aplikacje typy aplikacji, które określono w planie nie są przeliczane względem globalnej puli aplikacji. Na przykład, jeśli niektóre aplikacje typu mają kluczowe znaczenie, administrator klastra można dodać odpowiedni wpis mapy dla tego typu aplikacji i przypisz mu wartość 0% (oznacza to, nie ma znaczenia błędów). Wszystkie inne aplikacje, może zostać oceniony przy użyciu MaxPercentUnhealthyApplications równa 20% tolerować kilka błędów tysięcy wystąpień aplikacji. Mapa zasad kondycji typu aplikacji jest używana tylko wtedy, gdy w manifeście klastra umożliwia ocenę kondycji typu aplikacji za pomocą pozycji konfiguracji dla HealthManager/EnableApplicationTypeHealthEvaluation. |
| --chaos-target-filter | Słownik zakodowane w JSON za pomocą dwóch kluczy typu ciąg. Dwa klucze są obie opcje NodeTypeInclusionList i ApplicationInclusionList. Wartości dla obu tych kluczy są listą ciągów. chaos_target_filter definiuje wszystkie filtry docelowych Chaos usterek, na przykład powodujący błąd niektórych typów węzłów lub powodujący błąd tylko określone aplikacje. <br><br> Jeżeli nie zastosowano chaos_target_filter, Chaos błędów wszystkie jednostki klastra. Jeśli chaos_target_filter Chaos błędów jednostek, które spełniają specyfikacji chaos_target_filter. Obie opcje NodeTypeInclusionList i ApplicationInclusionList umożliwiają tylko złożenia semantyki. Nie jest możliwe określenie część wspólna obie opcje NodeTypeInclusionList i ApplicationInclusionList. Na przykład nie jest możliwe określenie "błędów tej aplikacji, tylko wtedy, gdy jej dla tego typu węzła". Gdy obie opcje NodeTypeInclusionList i ApplicationInclusionList zawiera jednostki, tej jednostki nie można wykluczyć za pomocą obiekcie ChaosTargetFilter. Nawet wtedy, gdy opcja ApplicationInclusionList nie ma applicationX, w niektórych iteracji Chaos applicationX mogą być błędne ponieważ zdarza się w węźle nodeTypeY, który znajduje się w obie opcje NodeTypeInclusionList. Jeśli obie opcje NodeTypeInclusionList i ApplicationInclusionList są puste, zostanie zgłoszony ArgumentException. Wszystkie typy błędów (ponownie uruchomić węzeł, ponowne uruchomienie pakietu kodu, usunąć replikę, uruchom ponownie repliki, przenieść podstawowy i Przenieś dodatkowej) są włączone dla węzłów z tych typów węzłów. Typ węzła (powiedzieć NodeTypeX) nie jest widoczna w obie opcje NodeTypeInclusionList, a następnie węzeł poziomu błędów (np. NodeRestart) nigdy nie zostanie włączona dla węzłów NodeTypeX, ale błędów pakietu i repliki kod nadal może zostać włączona dla NodeTypeX, jeśli aplikacja w Opcja ApplicationInclusionList stanie się znajdują się w węźle NodeTypeX. Co najwyżej 100 nazw typ węzła może obejmować na tej liście, aby zwiększyć tę liczbę, wymagane jest uaktualnienie konfiguracji dla konfiguracji MaxNumberOfNodeTypesInChaosEntityFilter. Wszystkie repliki należących do usługi te aplikacje są podatna na błędy repliki (repliki ponownego uruchomienia, Usuń replikę, Przenieś podstawowego i zapasowego przenoszenia) przez Chaos. Chaos może ponownie uruchomić pakiet kodu, tylko wtedy, gdy pakiet kodu obsługuje replik tylko do tych aplikacji. Jeśli aplikacja nie ma na tej liście, jego może nadal wystąpi błąd w niektórych iteracji Chaos Jeśli aplikacja kończy się w węźle typ węzła, który znajduje się w obie opcje NodeTypeInclusionList. Jednak jeśli applicationX jest powiązany z nodeTypeY ograniczeniami dotyczącymi umieszczania i applicationX Brak ApplicationInclusionList i nodeTypeY jest nieobecny w obie opcje NodeTypeInclusionList, następnie applicationX nigdy nie wystąpi błąd. Maksymalnie 1000 nazwy aplikacji mogły zostać uwzględnione w tej liście, aby zwiększyć tę liczbę, wymagane jest uaktualnienie konfiguracji dla konfiguracji MaxNumberOfApplicationsInChaosEntityFilter. |
| --context | Mapa kodowany w formacie JSON (string, string) wpisz pary klucz wartość. Mapy może służyć do rejestrowania informacji o przebiegu Chaos. Nie może istnieć więcej niż 100 takich pary i każdego ciągu (klucza lub wartości) mogą mieć maksymalnie 4095 znaków. Ta mapa jest ustawiany przez początkowego elementu Chaos, uruchom na przechowywanie kontekst dotyczący określonego przebiegu. |
| --disable-move-replica-faults | Wyłącza podstawowy przenoszenia i przenieść dodatkowej błędów. |
| --max-cluster-stabilization | Maksymalna ilość czasu oczekiwania dla wszystkich klastrów jednostki stają się stabilne i działa prawidłowo.  Domyślne\: 60. <br><br> Chaos wykonuje w iteracji i na początku każdej iteracji sprawdza poprawność kondycji jednostek klastra. Jeśli jednostka klastra nie jest stabilna i działa prawidłowo w ramach MaxClusterStabilizationTimeoutInSeconds, Chaos podczas sprawdzania poprawności generuje zdarzenie nie powiodło się sprawdzanie poprawności. |
| --max-concurrent-faults | Maksymalna liczba współbieżnych błędów wywołane na iterację. Chaos wykonuje iteracje i dwóch kolejnych iteracji są rozdzielone na etapie weryfikacji. Zapewnienie większej współbieżności, bardziej agresywnego iniekcji błędów — wykonuje bardziej złożonych serii stanami w celu wykrycia błędów. Zalecane jest, aby uruchomić o wartości 2 lub 3 i należy zachować ostrożność podczas przenoszenia w.  Domyślne\: 1. |
| --max-percent-unhealthy-apps | Podczas oceny kondycji klastra podczas chaosu, maksymalna dozwolona wartość procentowa aplikacje w złej kondycji przed zgłoszeniem błędu. <br><br> Maksymalna dozwolona wartość procentowa aplikacje w złej kondycji przed zgłoszeniem błędu. Na przykład aby zezwolić na 10% aplikacje będącą w złej kondycji, wartość ta wynosi 10. Wartość procentowa reprezentuje maksymalny procent tolerowana aplikacje, które mogą być złej kondycji, zanim klaster zostanie uznane za błąd. Jeśli wartość procentowa jest zachowana, ale istnieje co najmniej jednej aplikacji w złej kondycji, kondycji jest oceniane jako ostrzeżenie. To jest obliczana na podstawie dzielona całkowitej liczby wystąpień aplikacji w klastrze, z wyjątkiem aplikacji typów aplikacji, które są objęte ApplicationTypeHealthPolicyMap aplikacje w złej kondycji. Obliczenia zaokrągla w górę do tolerowanie awarii jednego w małej liczby aplikacji. Wartość procentowa domyślną jest zero. |
| --max-percent-unhealthy-nodes | Podczas oceny kondycji klastra podczas chaosu, maksymalny dozwolony procent węzłów w złej kondycji przed zgłoszeniem błędu. <br><br> Maksymalna dozwolona wartość procentowa węzłów w złej kondycji przed zgłoszeniem błędu. Na przykład aby zezwolić na 10% węzłów będącą w złej kondycji, wartość ta wynosi 10. Wartość procentowa reprezentuje tolerowana maksymalny procent węzłów, które mogą być złej kondycji, zanim klaster zostanie uznane za błąd. Jeśli wartość procentowa jest zachowana, ale istnieje co najmniej jeden węzeł w złej kondycji, kondycji jest oceniane jako ostrzeżenie. Wartość procentowa jest obliczana przez podzielenie liczby węzłów w złej kondycji przez całkowitą liczbę węzłów w klastrze. Obliczenia zaokrągla w górę do tolerowanie awarii jednego w małej liczby węzłów. Wartość procentowa domyślną jest zero. W dużych klastrach niektóre węzły zawsze będzie wciśnięty lub na zewnątrz do naprawy, dlatego ta wartość procentowa, należy skonfigurować tolerowana liczba, która. |
| — czas do uruchomienia | Łączny czas (w sekundach), dla którego Chaos zostanie uruchomione przed zatrzymaniem automatycznie. Maksymalna dozwolona wartość to 4 294 967 295 (System.UInt32.MaxValue).  Domyślne\: 4294967295. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |
| --oczekiwania czas między błędów | Poczekaj czas (w sekundach) między kolejnych błędów w ramach jednej iteracji.  Domyślne\: 20. <br><br> Im większa wartość, dolna nakładających się między błędów i prostszej sekwencji stanu przechodzi prowadzącego klastra za pośrednictwem. Zaleca się zaczynać się wartością z zakresu od 1 i 5 oraz wykonywania ostrożność podczas przenoszenia w. |
| --wait-time-between-iterations | Czas — separacji (w sekundach) między dwa kolejne iteracje elementu Chaos. Im większa wartość, tym niższy współczynnik iniekcji błędów.  Domyślne\: 30. |
| --warning-as-error | Wskazuje, czy ostrzeżenia są traktowane z tego samego ważność jako błędy. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-chaos-stop"></a>Interfejs sfctl chaos stop
Zatrzymuje chaosu, jeśli jest uruchomiony w klastrze i umieścić harmonogram Chaos w stanie zatrzymania.

Zatrzymuje Chaos wykonywanie nowych błędów. Aktywne błędy będą nadal wykonać dopiero po ich zakończeniu. Bieżący harmonogram Chaos są umieszczane w stanie zatrzymania. Po zatrzymaniu harmonogramu spowoduje to pozostanie w stanie zatrzymania i nie można wykorzystać na nowe przebiegi harmonogram Chaos elementu Chaos. Nowy harmonogram Chaos musi być ustawiona w celu wznowienia harmonogramu.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |


## <a name="next-steps"></a>Kolejne kroki
- [Instalator](service-fabric-cli.md) usługi Service Fabric CLI.
- Dowiedz się, jak używać przy użyciu interfejsu wiersza polecenia usługi Service Fabric [przykładowe skrypty](/azure/service-fabric/scripts/sfctl-upgrade-application).
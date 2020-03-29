---
title: Platforma Azure Service Fabric CLI- sfctl chaos
description: Dowiedz się więcej o sfctl, interfejsie wiersza polecenia sieci szkieletowej usługi Azure. Zawiera listę poleceń do zarządzania chaosem.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 6668446363361fbc6d24afc3d11a36a0b786667d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906169"
---
# <a name="sfctl-chaos"></a>sfctl chaos
Start, zatrzymaj i raport na temat usługi testu chaosu.

## <a name="subgroups"></a>Podgrup
|Podgrupy|Opis|
| --- | --- |
| [Harmonogram](service-fabric-sfctl-chaos-schedule.md) | Pobierz i ustaw harmonogram chaosu. |
## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| zdarzenia | Pobiera następny segment Chaos zdarzenia na podstawie tokenu kontynuacji lub zakresu czasu. |
| get | Uzyskaj status Chaosu. |
| rozpoczynanie | Uruchamia Chaos w klastrze. |
| zatrzymanie | Zatrzymuje Chaos, jeśli jest uruchomiony w klastrze i umieścić Harmonogram chaosu w stanie zatrzymania. |

## <a name="sfctl-chaos-events"></a>sfctl chaos wydarzenia
Pobiera następny segment Chaos zdarzenia na podstawie tokenu kontynuacji lub zakresu czasu.

Aby uzyskać następny segment chaosu zdarzeń, można określić ContinuationToken. Aby rozpocząć nowy segment zdarzeń Chaos, można określić zakres czasu za pośrednictwem StartTimeUtc i EndTimeUtc. Nie można określić zarówno ContinuationToken i zakres czasu w tym samym wywołaniu. Gdy istnieje więcej niż 100 zdarzenia Chaos, Chaos zdarzenia są zwracane w wielu segmentach, gdzie segment zawiera nie więcej niż 100 chaos zdarzeń i uzyskać następny segment można wywołać do tego interfejsu API z tokenem kontynuacji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --continuation-token | Parametr tokenu kontynuacji jest używany do uzyskania następnego zestawu wyników. Token kontynuacji z wartością niepustą jest uwzględniony w odpowiedzi interfejsu API, gdy wyniki z systemu nie mieszczą się w pojedynczej odpowiedzi. Gdy ta wartość jest przekazywana do następnego wywołania interfejsu API, interfejs API zwraca następny zestaw wyników. Jeśli nie ma żadnych dalszych wyników, token kontynuacji nie zawiera wartości. Wartość tego parametru nie powinna być zakodowana w adresie URL. |
| --end-time-utc | Czas pliku systemu Windows reprezentujący czas zakończenia zakresu czasu, dla którego ma zostać wygenerowany raport Chaos. Szczegółowe informacje można znaleźć w [metodzie DateTime.ToFileTimeUtc.](https\://msdn.microsoft.com/library/system.datetime.tofiletimeutc(v=vs.110).aspx) |
| --max-wyniki | Maksymalna liczba wyników do zwrotu w ramach zapytań stronicowanych. Ten parametr definiuje górną granicę na liczbę zwracanych wyników. Zwracane wyniki mogą być mniejsze niż określone maksymalne wyniki, jeśli nie mieszczą się w wiadomości zgodnie z ograniczeniami maksymalnego rozmiaru wiadomości zdefiniowanymi w konfiguracji. Jeśli ten parametr jest zerowy lub nie określony, kwerenda stronicowana zawiera jak najwięcej wyników, które pasują do wiadomości zwrotnej. |
| --start-time-utc | Czas pliku systemu Windows reprezentujący czas rozpoczęcia zakresu czasu, dla którego ma zostać wygenerowany raport Chaos. Szczegółowe informacje można znaleźć w [metodzie DateTime.ToFileTimeUtc.](https\://msdn.microsoft.com/library/system.datetime.tofiletimeutc(v=vs.110).aspx) |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-chaos-get"></a>sfctl chaos dostać
Uzyskaj status Chaosu.

Uzyskaj status Chaosu wskazujący, czy chaos jest uruchomiony, parametry Chaosu używane do uruchamiania Chaosu i stan Harmonogramu Chaosu.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-chaos-start"></a>sfctl chaos start
Uruchamia Chaos w klastrze.

Jeśli Chaos nie jest jeszcze uruchomiony w klastrze, rozpoczyna Chaos z przekazanych w Chaos parametrów. Jeśli Chaos jest już uruchomiony po wykonaniu tego wywołania, wywołanie kończy się niepowodzeniem z kodem błędu FABRIC_E_CHAOS_ALREADY_RUNNING. Więcej informacji można znaleźć w artykule [Wywołaj kontrolowany chaos w klastrach sieci szkieletowej usług.](https\://docs.microsoft.com/azure/service-fabric/service-fabric-controlled-chaos)

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --app-type-health-policy-map | Lista zakodowana w u usług JSON z maksymalną procentową złą aplikacją dla określonych typów aplikacji. Każdy wpis określa jako klucz nazwę typu aplikacji i jako wartość liczby całkowitej, która reprezentuje maxpercentUnhealthyApplications procent używany do oceny aplikacji określonego typu aplikacji. <br><br> Definiuje mapę z maksymalną procentową złą aplikacją dla określonych typów aplikacji. Każdy wpis określa jako klucz nazwę typu aplikacji i jako wartość liczbę całkowitą reprezentującą wartość procentową MaxPercentUnhealthyApplications używaną do oceny aplikacji określonego typu aplikacji. Mapa zasad kondycji typu aplikacji może służyć podczas oceny kondycji klastra do opisywania specjalnych typów aplikacji. Typy aplikacji zawarte na mapie są oceniane względem wartości procentowej określonej na mapie, a nie z globalnymi zastosowaniami MaxPercentUnhealthyApplications zdefiniowanymi w zasadach kondycji klastra. Aplikacje typów aplikacji określonych na mapie nie są wliczane do globalnej puli aplikacji. Na przykład jeśli niektóre aplikacje typu są krytyczne, administrator klastra można dodać wpis do mapy dla tego typu aplikacji i przypisać mu wartość 0% (oznacza to, że nie toleruje żadnych błędów). Wszystkie inne aplikacje mogą być oceniane za pomocą MaxPercentUnhealthyApplications ustawiona na 20%, aby tolerować niektóre błędy z tysięcy wystąpień aplikacji. Mapa zasad kondycji typu aplikacji jest używana tylko wtedy, gdy manifest klastra umożliwia ocenę kondycji typu aplikacji przy użyciu wpisu konfiguracji dla HealthManager/EnableApplicationTypeHealthEvaluation. |
| --chaos-target-filter | Słownik zakodowany json z dwoma kluczami typu ciąg. Dwa klucze są NodeTypeInclusionList i ApplicationInclusionList. Wartości dla obu tych kluczy są listy ciągów. chaos_target_filter definiuje wszystkie filtry dla ukierunkowanych błędów Chaos, na przykład, błąd tylko niektóre typy węzłów lub błędów tylko niektóre aplikacje. <br><br> Jeśli chaos_target_filter nie jest używany, Chaos błędów wszystkich jednostek klastra. Jeśli jest używany chaos_target_filter, Chaos błędów tylko jednostki, które spełniają specyfikację chaos_target_filter. NodeTypeInclusionList i ApplicationInclusionList zezwalają tylko na semantykę unii. Nie jest możliwe określenie przecięcia NodeTypeInclusionList i ApplicationInclusionList. Na przykład nie jest możliwe określenie "błąd tej aplikacji tylko wtedy, gdy jest w tym typie węzła." Gdy jednostka jest uwzględniona w NodeTypeInclusionList lub ApplicationInclusionList, tej jednostki nie można wykluczyć za pomocą ChaosTargetFilter. Nawet jeśli applicationX nie pojawia się w ApplicationInclusionList, w niektórych Chaos iteracji applicationX może być uszkodzony, ponieważ dzieje się to w węźle nodeTypeY, który znajduje się w NodeTypeInclusionList. Jeśli zarówno NodeTypeInclusionList i ApplicationInclusionList są puste, ArgumentException jest generowany. Wszystkie typy usterek (węzeł ponownego uruchomienia, uruchom ponownie pakiet kodu, usuń replikę, uruchom ponownie replikę, przenieś podstawowy i przenieś pomocniczy) są włączone dla węzłów tych typów węzłów. Jeśli typ węzła (powiedzmy NodeTypeX) nie pojawi się na liście NodeTypeInclusionList, błędy poziomu węzła (takie jak NodeRestart) nigdy nie będą włączone dla węzłów NodeTypeX, ale pakiet kodu i błędy replik mogą być nadal włączone dla NodeTypeX, jeśli aplikacja w ApplicationInclusionList znajduje się w węźle NodeTypeX. Co najwyżej 100 nazw typów węzłów mogą być zawarte na tej liście, aby zwiększyć tę liczbę, uaktualnienie konfiguracji jest wymagane dla konfiguracji MaxNumberOfNodeTypesInChaosEntityFilter. Wszystkie repliki należące do usług tych aplikacji są podatne na błędy repliki (ponowne uruchomienie repliki, usunięcie repliki, przeniesienie podstawowego i przeniesienie pomocniczego) przez chaos. Chaos może ponownie uruchomić pakiet kodu tylko wtedy, gdy pakiet kodu obsługuje repliki tylko tych aplikacji. Jeśli aplikacja nie pojawia się na tej liście, nadal może być uszkodzony w niektórych iteracji Chaos, jeśli aplikacja kończy się w węźle typu węzła, który znajduje się w NodeTypeInclusionList. Jednak jeśli applicationX jest powiązany z nodeTypeY poprzez ograniczenia umieszczania i applicationX jest nieobecny w ApplicationInclusionList i nodeTypeY jest nieobecny z NodeTypeInclusionList, a następnie applicationX nigdy nie będzie uszkodzony. Co najwyżej 1000 nazw aplikacji mogą być zawarte na tej liście, aby zwiększyć tę liczbę, uaktualnienie konfiguracji jest wymagane dla konfiguracji MaxNumberOfApplicationsInChaosEntityFilter. |
| --kontekst | Kodowana json mapa par (ciąg, ciąg), typ klucza wartości. Mapa może służyć do rejestrowania informacji o przebiegu chaosu. Nie może być więcej niż 100 takich par i każdy ciąg (klucz lub wartość) może mieć co najwyżej 4095 znaków. Ta mapa jest ustawiana przez rozrusznik chaos uruchomić opcjonalnie przechowywać kontekst o określonym przebiegu. |
| --disable-move-replica-faults --disable-move-replica-faults --disable-move-replica-faults -- | Wyłącza przeniesienie podstawowego i przenieść błędy pomocnicze. |
| --max-stabilizacja klastra | Maksymalna ilość czasu oczekiwania na wszystkie jednostki klastra stają się stabilne i w dobrej kondycji.  Wartość\: domyślna 60. <br><br> Chaos jest wykonywany w iteracjach i na początku każdej iteracji sprawdza poprawność kondycji jednostek klastra. Podczas sprawdzania poprawności, jeśli jednostka klastra nie jest stabilna i w dobrej kondycji w maxclusterstabilizationTimeoutInSeconds, Chaos generuje sprawdzanie poprawności zdarzenia nie powiodło się. |
| --max-równoczesnych błędów | Maksymalna liczba równoczesnych błędów wywołanych na iterację. Chaos wykonuje w iteracjach i dwie kolejne iteracje są oddzielone fazą sprawdzania poprawności. Im wyższa współbieżność, tym bardziej agresywne wtrysk usterek - wywoływanie bardziej złożonych serii stanów w celu wykrycia błędów. Zaleca się, aby rozpocząć od wartości 2 lub 3 i zachować ostrożność podczas ruchu w górę.  Wartość\: domyślna 1. |
| --max-percent-unhealthy-apps | Podczas oceny kondycji klastra podczas chaosu maksymalny dozwolony procent aplikacji w złej kondycji przed zgłoszeniem błędu. <br><br> Maksymalny dozwolony procent aplikacji w złej kondycji przed zgłoszeniem błędu. Na przykład, aby umożliwić 10% aplikacji w złej kondycji, ta wartość będzie 10. Wartość procentowa reprezentuje maksymalny tolerowany procent aplikacji, które mogą być w złej kondycji, zanim klaster zostanie uznany za błąd. Jeśli wartość procentowa jest przestrzegana, ale istnieje co najmniej jedna aplikacja w złej kondycji, kondycja jest oceniana jako Ostrzeżenie. Jest to obliczane przez podzielenie liczby aplikacji w złej kondycji na całkowitą liczbę wystąpień aplikacji w klastrze, z wyłączeniem aplikacji typów aplikacji, które są zawarte w ApplicationTypeHealthPolicyMap. Obliczenia zaokrągla się tolerować jeden błąd na małej liczbie aplikacji. Domyślna wartość procentowa wynosi zero. |
| --max-percent-unhealthy-nodes | Podczas oceny kondycji klastra podczas chaosu, maksymalny dozwolony procent węzłów w złej kondycji przed zgłoszeniem błędu. <br><br> Maksymalny dozwolony procent węzłów w złej kondycji przed zgłoszeniem błędu. Na przykład, aby umożliwić 10% węzłów w złej kondycji, ta wartość będzie 10. Wartość procentowa reprezentuje maksymalny tolerowany procent węzłów, które mogą być w złej kondycji, zanim klaster zostanie uznany za błąd. Jeśli wartość procentowa jest przestrzegana, ale istnieje co najmniej jeden węzeł w złej kondycji, kondycja jest oceniana jako Ostrzeżenie. Wartość procentowa jest obliczana przez podzielenie liczby węzłów w złej kondycji na całkowitą liczbę węzłów w klastrze. Obliczenia zaokrągla się tolerować jeden błąd na małej liczbie węzłów. Domyślna wartość procentowa wynosi zero. W dużych klastrach niektóre węzły będą zawsze w dół lub na zewnątrz do naprawy, więc ten procent powinien być skonfigurowany do tolerowania tego. |
| --time-to-run | Całkowity czas (w sekundach), dla którego Chaos zostanie uruchomiony przed automatycznym zatrzymaniem. Maksymalna dozwolona wartość to 4 294 967 295 (System.UInt32.MaxValue).  Wartość\: domyślna 4294967295. |
| --timeout -t | Wartość\: domyślna 60. |
| --wait-time-between-faults --wait-time-between-faults --wait-time-between-faults -- | Czas oczekiwania (w sekundach) między kolejnymi błędami w ramach jednej iteracji.  Wartość\: domyślna 20. <br><br> Im większa wartość, tym niższe nakładanie się między błędami i prostsza sekwencja przejść stanu, przez które przechodzi klaster. Zalecenie jest, aby rozpocząć od wartości między 1 i 5 i zachować ostrożność podczas ruchu w górę. |
| --wait-time-between-iterations --wait-time-between-iterations --wait-time-between-iterations -- | Separacja czasu (w sekundach) między dwiema kolejnymi iteracjami Chaosu. Im większa wartość, tym niższa szybkość wtrysku usterek.  Wartość\: domyślna 30. |
| --warning-as-error | Wskazuje, czy ostrzeżenia są traktowane z taką samą ważnością jak błędy. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-chaos-stop"></a>sfctl chaos stop
Zatrzymuje Chaos, jeśli jest uruchomiony w klastrze i umieścić Harmonogram chaosu w stanie zatrzymania.

Zatrzymuje chaos przed wykonywaniem nowych usterek. Usterki podczas lotu będą nadal wykonywane do momentu ich zakończenia. Bieżący harmonogram chaosu jest umieszczany w stanie zatrzymania. Po zatrzymaniu harmonogramu, pozostanie w stanie zatrzymania i nie będzie używany do Chaos Schedule nowe przebiegi Chaos. Aby wznowić planowanie, należy ustawić nowy harmonogram chaosu.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |


## <a name="next-steps"></a>Następne kroki
- [Konfigurowanie](service-fabric-cli.md) interfejsu wiersza polecenia sieci szkieletowej usług.
- Dowiedz się, jak używać interfejsu wiersza polecenia sieci szkieletowej usług przy użyciu [przykładowych skryptów.](/azure/service-fabric/scripts/sfctl-upgrade-application)
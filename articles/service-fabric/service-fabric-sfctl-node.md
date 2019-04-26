---
title: Węzeł platformy Azure interfejsu wiersza polecenia usługi Service Fabric - sfctl | Dokumentacja firmy Microsoft
description: Zawiera opis poleceń interfejsu wiersza polecenia usługi Service Fabric sfctl węzła.
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
ms.openlocfilehash: 08ea0081c84ea31b2b71d03679b1b527cf94c075
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60556784"
---
# <a name="sfctl-node"></a>sfctl node
Zarządzaj węzły, które tworzą klaster.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| wyłącz | Dezaktywuj z zamiarem dezaktywacji określony węzeł klastra usługi Service Fabric. |
| włącz | Aktywuj węzeł klastra usługi Service Fabric, który jest obecnie wyłączona. |
| zdrowie | Pobiera kondycji węzła usługi Service Fabric. |
| informacje | Pobiera informacje dotyczące określonego węzła w klastrze usługi Service Fabric. |
| list | Pobiera listę węzłów w klastrze usługi Service Fabric. |
| ładowanie | Pobiera informacje o ładowaniu węzła usługi Service Fabric. |
| remove-state | Powiadamia usługi Service Fabric, że stan utrwalony w węźle trwale ani nie usunięto utracone. |
| report-health | Wysyła raport o kondycji w węźle usługi Service Fabric. |
| restart | Powoduje ponowne uruchomienie węzła klastra usługi Service Fabric. |
| przejścia | Uruchamia lub zatrzymuje węzła klastra. |
| transition-status | Pobiera postęp operacji wprowadzenie przy użyciu StartNodeTransition. |

## <a name="sfctl-node-disable"></a>Wyłącz węzeł sfctl
Dezaktywuj z zamiarem dezaktywacji określony węzeł klastra usługi Service Fabric.

Dezaktywuj z zamiarem dezaktywacji określony węzeł klastra usługi Service Fabric. Po dezaktywacji jest w toku, zamiar dezaktywacji może zostać zwiększona, ale nie spadła (na przykład węzeł, który jest dezaktywowany z zamiarem Wstrzymaj można dezaktywować dalsze o ponowne uruchomienie, ale nie odwrotnie. Węzły mogą ponownie aktywować przy użyciu Aktywuj operacją węzła w dowolnej chwili po ich zostaną dezaktywowane. Dezaktywacja nie zostało zakończone, spowoduje to anulowanie dezaktywację. Węzeł ulegnie awarii, a następnie wróci do sprawności podczas dezaktywacji nadal konieczne ponowne aktywowanie przed usługi, które zostaną umieszczone w tym węźle.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — [wymagane] Nazwa węzła | Nazwa węzła. |
| --deactivation-intent | Opisuje przeznaczenie lub powód dezaktywowanie węzła. Możliwe wartości są następujące. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-node-enable"></a>Włącz węzła sfctl
Aktywuj węzeł klastra usługi Service Fabric, który jest obecnie wyłączona.

Aktywuje węzeł klastra usługi Service Fabric, który jest obecnie wyłączona. Po aktywowana, węzeł ponownie staną się możliwego do użycia docelowej do wprowadzenia do nowej repliki i zostanie ponownie uaktywnione wszystkie repliki dezaktywowane pozostały w węźle.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — [wymagane] Nazwa węzła | Nazwa węzła. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-node-health"></a>Interfejs sfctl kondycji węzła
Pobiera kondycji węzła usługi Service Fabric.

Pobiera kondycji węzła usługi Service Fabric. Użyj EventsHealthStateFilter do filtrowania kolekcji zdarzeń dotyczących kondycji zgłoszone w węźle na podstawie stanu kondycji. Jeśli węzeł, który określisz o nazwie nie istnieje w magazynie kondycji, to zwraca błąd.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — [wymagane] Nazwa węzła | Nazwa węzła. |
| --events-health-state-filter | Umożliwia filtrowanie kolekcji zwracanych obiektów HealthEvent oparte na stanie kondycji. Możliwe wartości dla tego parametru to wartość całkowitą, jednego z następujących stanów kondycji. Zwracane są tylko te zdarzenia, które są zgodne z filtrem. Wszystkie zdarzenia są używane do oceny stanu kondycji zagregowane. Jeśli nie zostanie określony, zwracane są wszystkie wpisy. Wartości stanu są wyliczanie oparte na flagi, dzięki czemu może to być kombinacją tych wartości uzyskanych przy użyciu bitowego operatora "Lub". Na przykład jeśli podana wartość jest 6 następnie wszystkie zdarzenia z wartością atrybutu HealthState OK (2) i ostrzeżenia (4) są zwracane.  <br> -Domyślnie — wartość domyślną. Pasuje do dowolnego atrybutu HealthState. Ta wartość wynosi zero.  <br> -Brak — filtr, który nie jest zgodny z dowolną wartością atrybutu HealthState. Używany, aby zwracała żadnych wyników w danej kolekcji stanów. Wartość to 1.  <br> -Ok — filtrowanie, że dopasowania danych wejściowych z wartością atrybutu HealthState Ok. Wartość jest równa 2.  <br> -Warning - filtru, że wprowadzanie dopasowania z atrybutem HealthState wartość ostrzeżenie. Wartość wynosi 4.  <br> -Błąd — filtr, który pasuje do danych wejściowych z wartością atrybutu HealthState błędu. Wartość jest 8.  <br> -Al - filtr, który pasuje do danych wejściowych z dowolną wartością atrybutu HealthState. Wartość jest 65535. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-node-info"></a>informacje o węźle sfctl
Pobiera informacje dotyczące określonego węzła w klastrze usługi Service Fabric.

Odpowiedź zawiera nazwę, stan, identyfikator, kondycji, czasu dostępności i inne szczegóły dotyczące węzła.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — [wymagane] Nazwa węzła | Nazwa węzła. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-node-list"></a>Interfejs sfctl listy węzłów
Pobiera listę węzłów w klastrze usługi Service Fabric.

Odpowiedź zawiera nazwę, stan, identyfikator, kondycji, czasu dostępności i inne szczegóły dotyczące węzłów.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --token kontynuacji | Parametr tokenu kontynuacji służy do uzyskania następny zestaw wyników. Token kontynuacji o wartości niepuste znajduje się w odpowiedzi interfejsu API, gdy wyniki z systemu nie mieszczą się w jednej odpowiedzi. Jeśli ta wartość jest przekazywana do następnego wywołania interfejsu API, interfejs API zwraca następny zestaw wyników. Jeśli nie istnieją żadne dalsze wyniki, token kontynuacji nie zawiera wartości. Wartość tego parametru nie powinny być zakodowane w adresie URL. |
| --max-results | Maksymalna liczba wyników do zwrócenia w ramach kwerend stronicowanych. Ten parametr określa górną granicę na liczbie zwrócone wyniki. Wyniki zwracane, może być mniejsza niż określona maksymalna liczba wyników, jeśli nie mieszczą się w komunikacie zgodnie z ograniczeniami rozmiar maksymalny komunikatu zdefiniowane w konfiguracji. Jeśli ten parametr ma wartość zero lub nie określono, stronicowane zapytanie zawiera dowolną liczbę wyników jak to możliwe, który mieści się w komunikacie zwrotu. |
| --node-status-filter | Umożliwia filtrowanie węzłów, w oparciu o NodeStatus. Zostaną zwrócone tylko węzły, które są zgodne z wartością określonego filtru. Wartość filtru może być jednym z następujących czynności.  Domyślne\: domyślne. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-node-load"></a>Interfejs sfctl węzła obciążenia
Pobiera informacje o ładowaniu węzła usługi Service Fabric.

Pobiera informacje o ładowaniu węzła usługi Service Fabric dla metryk, które mają obciążenia lub zdefiniowano zdolności produkcyjnych.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — [wymagane] Nazwa węzła | Nazwa węzła. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-node-remove-state"></a>Interfejs sfctl remove stan węzła
Powiadamia usługi Service Fabric, że stan utrwalony w węźle trwale ani nie usunięto utracone.

Oznacza to, że nie jest możliwe do odzyskania utrwalonego stanu tego węzła. Zwykle dzieje się tak, jeśli dysk twardy zostało wyczyszczone czyste lub jeśli wystąpiła awaria dysku twardego. Węzeł musi być wyłączone dla tej operacji zakończy się powodzeniem. Ta operacja służy do usługi Service Fabric wiedzieć, że repliki w tym węźle nie istnieją już i powinna zostać przerwana w tej usługi Service Fabric Trwa oczekiwanie na tych replik w celu przywracane. Nie należy uruchamiać tego polecenia cmdlet, jeśli stan, w tym węźle nie została usunięta, a węzeł możesz wrócić i jej stanem bez zmian.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — [wymagane] Nazwa węzła | Nazwa węzła. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-node-report-health"></a>Interfejs sfctl węzła raportów kondycji
Wysyła raport o kondycji w węźle usługi Service Fabric.

Raporty stan kondycji określonego węzła usługi Service Fabric. Raport musi zawierać informacje o źródle raport o kondycji i właściwości, na którym jest zgłaszany. Raport jest wysyłany do węzła bramy usługi Service Fabric, która przekazuje w magazynie kondycji. Raport może być akceptowane przez bramę, ale odrzucony przez magazynu kondycji po dodatkową walidację. Na przykład magazynu kondycji może odrzucić raport ze względu na nieprawidłowy parametr, takich jak numer sekwencji starych. Aby sprawdzić, czy raport został zastosowany w magazynie kondycji, sprawdź, czy raport jest wyświetlany w sekcji HealthEvents.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --kondycji — właściwość [wymagane] | Właściwość o kondycji. <br><br> Jednostka może mieć raportów o kondycji dla różnych właściwości. Właściwość jest ciągu i stałych wyliczenia aby zezwalał na elastyczność reportera do kategoryzowania warunek stanu, która powoduje uruchomienie raportu. Na przykład reportera o ID "LocalWatchdog" można monitorować stan wolnego w węźle, aby go zgłosić właściwości "AvailableDisk" w tym węźle. Ten sam reportera monitorować łączność węzeł tak go zgłosić właściwości "Łączność" w tym samym węźle. W magazynie kondycji te raporty są traktowane jako zdarzenia dotyczące kondycji oddzielnych dla określonego węzła. Wraz z SourceId właściwość jednoznacznie identyfikuje informacje o kondycji. |
| --health-state    [Required] | Możliwe wartości to\: "Nieprawidłowy", "Ok", "Ostrzeżenie", "Error", "Nieznany". |
| — [wymagane] Nazwa węzła | Nazwa węzła. |
| --source-id       [Required] | Nazwa źródła, która identyfikuje składnik klienta/strażnika/systemu, który wygenerował informacji o kondycji. |
| --description | Opis informacji o kondycji. <br><br> Reprezentuje dowolny tekst, które umożliwiają dodawanie ludzi do odczytu informacji na temat raportu. Maksymalna długość ciągu opisu wynosi 4096 znaków. Jeśli podany ciąg jest dłuższy, zostaną automatycznie obcięte. W przypadku obcięty, ostatnie znaki opis zawiera znacznik "[obcięte]", a ciąg łączny rozmiar wynosi 4096 znaków. Obecność znacznika wskazuje, aby użytkownicy tej obcięcie wystąpił. Należy pamiętać, że gdy obcięty, opis ma mniej niż 4096 znaków z oryginalnego ciągu. |
| --immediate | Flaga, która wskazuje, czy raport powinna zostać wysłana natychmiast. <br><br> Raport o kondycji są wysyłane do aplikacji, która przekazuje w magazynie kondycji bramy usługi Service Fabric. Jeśli bezpośrednie jest ustawiona na wartość true, raport jest wysyłany bezpośrednio z bramy protokołu HTTP w magazynie kondycji niezależnie od ustawień klienta sieci szkieletowej, które używa aplikacji bramy protokołu HTTP. Jest to przydatne dla krytycznych raportów, które mają być wysyłane tak szybko, jak to możliwe. W zależności od czasu i innych warunków wysłaniem raportu może nadal się nie powieść, na przykład jeśli bramy HTTP został zamknięty lub komunikat nie dociera do bramy. Jeśli bezpośrednie jest ustawiona na wartość false, raport jest wysyłana na podstawie ustawień klienta kondycji z bramy protokołu HTTP. W związku z tym będzie partii zgodnie z konfiguracją HealthReportSendInterval. Jest to zalecane ustawienie ponieważ zezwala ona na kondycji klienta do optymalizacji raportowania komunikatów w magazynie danych kondycji, a także przetwarzania raportu kondycji kondycji. Domyślnie raporty nie są wysyłane bezpośrednio. |
| --remove-when-expired | Wartość wskazująca, czy raport jest usuwany z magazynu kondycji po jego wygaśnięciu. <br><br> Jeśli ustawiono wartość true, raport zostanie usunięty z magazynu kondycji po jego wygaśnięciu. Jeśli ma wartość false, raport jest traktowana jako błąd po upływie. Wartość tej właściwości to false domyślnie. Gdy klienci okresowo raportu ustala RemoveWhenExpired false (domyślnie). W ten sposób jest zgłaszającą ma problemy (np. zakleszczenia) i nie można zgłosić jednostki jest oceniany na błąd, po wygaśnięciu raport o kondycji. Oznacza flagą jednostki jako błąd stanu kondycji. |
| --sequence-number | Numer sekwencji dla tego raportu o kondycji jako ciągu numerycznego. <br><br> Numer sekwencyjny raportu służy magazynu kondycji do wykrywania stare raportów. Jeśli nie zostanie określony, numer sekwencyjny został wygenerowany automatycznie przez klienta usługi kondycji po dodaniu raportu. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |
| --ttl | Czas trwania, dla których raport o kondycji jest nieprawidłowy. To pole używa formatu ISO8601 do określania czasu trwania. <br><br> Gdy klienci okresowo raport powinien wysyłać raporty, z częstotliwością wyższe niż czas wygaśnięcia. Jeśli klienci raportować przejścia, ich ustawić czas wygaśnięcia na nieograniczoną. Po wygaśnięciu czasu wygaśnięcia zdarzenie kondycji, który zawiera informacje o kondycji jest usunięte z magazynu kondycji, jeśli jest RemoveWhenExpired wartość true, lub oceniona błąd, jeśli RemoveWhenExpired wartość false. Jeśli nie zostanie określony, czas wygaśnięcia wartością domyślną jest wartość nieskończona. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-node-restart"></a>ponowne uruchomienie węzła sfctl
Powoduje ponowne uruchomienie węzła klastra usługi Service Fabric.

Powoduje ponowne uruchomienie węzła klastra usługi Service Fabric, który jest już uruchomiona.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — [wymagane] Nazwa węzła | Nazwa węzła. |
| --create-fabric-dump | Określ wartość True, aby utworzyć zrzutu procesu węzła sieci szkieletowej. Wielkość liter nie jest rozróżniana.  Domyślne\: wartość False. |
| --node-instance-id | Identyfikator wystąpienia węzła docelowego. Jeśli określono identyfikator wystąpienia węzeł będzie uruchamiany ponownie, tylko wtedy, gdy jest on zgodny z bieżącym wystąpieniem węzła. Wartość domyślna "0" umożliwi dopasowanie dowolnej identyfikatora wystąpienia. Identyfikator wystąpienia można uzyskać przy użyciu get węzła zapytania.  Domyślne\: 0. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-node-transition"></a>Interfejs sfctl przechodzenia węzłów
Uruchamia lub zatrzymuje węzła klastra.

Uruchamia lub zatrzymuje węzła klastra.  Węzeł klastra jest procesem nie wystąpienie systemu operacyjnego, sam.  Do uruchomienia węzła, są przekazywane w "Start" parametru NodeTransitionType. Aby zatrzymać węzła, są przekazywane w "Stop" dla parametru NodeTransitionType. Ten interfejs API rozpoczyna operację —, gdy interfejs API zwraca węzeł może nie mieć zakończone przechodzenie jeszcze. Wywołaj GetNodeTransitionProgress z tym samym identyfikatorem OperationId uzyskanie postępu operacji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| -node-— identyfikator wystąpienia [wymagane] | Identyfikator wystąpienia węzła węzeł docelowy. Można to ustalić przy użyciu interfejsu API GetNodeInfo. |
| — [wymagane] Nazwa węzła | Nazwa węzła. |
| --przejścia — typ węzła [wymagane] | Wskazuje typ przejścia do wykonania.  NodeTransitionType.Start rozpocznie zatrzymania węzła. NodeTransitionType.Stop spowoduje zatrzymanie węzła, który jest uruchomiony. |
| — Identyfikator operacji [wymagane] | Identyfikator GUID, który identyfikuje wywołanie tego interfejsu API.  Te informacje są przekazywane do odpowiedniego interfejsu API GetProgress. |
| --stop-duration-in-seconds [Required] | Czas trwania w sekundach, aby zachować węzeł zatrzymane.  Wartość minimalna wynosi 600, wartość maksymalna to 14400.  Po upływie tego czasu, węzeł zostanie automatycznie przywracane. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-node-transition-status"></a>stan przejścia sfctl węzła
Pobiera postęp operacji wprowadzenie przy użyciu StartNodeTransition.

Pobiera postęp operacji wprowadzenie StartNodeTransition przy użyciu podanego OperationId.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — [wymagane] Nazwa węzła | Nazwa węzła. |
| — Identyfikator operacji [wymagane] | Identyfikator GUID, który identyfikuje wywołanie tego interfejsu API.  Te informacje są przekazywane do odpowiedniego interfejsu API GetProgress. |
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
---
title: Azure usługi Service Fabric interfejsu wiersza polecenia sfctl repliki | Dokumentacja firmy Microsoft
description: Zawiera opis poleceń interfejsu wiersza polecenia usługi Service Fabric sfctl repliki.
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
ms.openlocfilehash: d0a7199ff0e9cb17c3fbc179a9b37a6620f521f9
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58666821"
---
# <a name="sfctl-replica"></a>sfctl replica
Zarządzaj replik, które należą do partycji usługi.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| wdrożony | Pobiera szczegóły repliki wdrożone w węźle usługi Service Fabric. |
| deployed-list | Pobiera listę replikami wdrożonymi w węźle usługi Service Fabric. |
| zdrowie | Pobiera kondycji usługi Service Fabric usługi stanowej replik lub wystąpień usługi bezstanowej. |
| informacje | Pobiera informacje o repliki partycji usługi Service Fabric. |
| list | Pobiera informacje o replikach partycji usługi Service Fabric. |
| usuń | Usuwa replikę usługi uruchomione w węźle. |
| report-health | Wysyła raport o kondycji w replice usługi Service Fabric. |
| restart | Powoduje ponowne uruchomienie repliki usług utrwalonych usługi uruchomionej w węźle. |

## <a name="sfctl-replica-deployed"></a>Interfejs sfctl repliki wdrożony
Pobiera szczegóły repliki wdrożone w węźle usługi Service Fabric.

Pobiera szczegóły repliki wdrożone w węźle usługi Service Fabric. Informacje dotyczące rodzaju usługi, nazwa usługi, bieżącą operację usługi, bieżącą operację usługi Data i godzina rozpoczęcia, identyfikator partycji:, identyfikator repliki i wystąpienia, obciążenia zgłoszone, inne informacje.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — [wymagane] Nazwa węzła | Nazwa węzła. |
| — Identyfikator partycji [wymagane] | Tożsamość partycji. |
| --replica-id   [Required] | Identyfikator repliki. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-replica-deployed-list"></a>Interfejs sfctl repliki wdrożone listy
Pobiera listę replikami wdrożonymi w węźle usługi Service Fabric.

Pobiera listę zawierającą informacje o replikami wdrożonymi w węźle usługi Service Fabric. Informacje obejmują Identyfikatora partycji, identyfikator repliki, stan repliki, nazwę usługi, nazwa typu usługi i inne informacje. Użyj identyfikatora partycji lub ServiceManifestName parametry zapytania, aby zwrócić informacje o replikach wdrożone, dopasowania określonej wartości tych parametrów.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator aplikacji [wymagane] | Tożsamość aplikacji. Zazwyczaj jest to pełna nazwa aplikacji bez "Service fabric\:" schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchiczne nazwy są oddzielane za pomocą znaku "\~" znaków. Na przykład, jeśli nazwa aplikacji jest "Service fabric\:/myapp/app1", tożsamość aplikacji będzie "myapp\~app1" w wersji 6.0 + i "myapp app1" w poprzednich wersjach. |
| — [wymagane] Nazwa węzła | Nazwa węzła. |
| --partition-id | Tożsamość partycji. |
| --service-manifest-name | Nazwa manifestu usługi zarejestrowany jako część typu aplikacji w klastrze usługi Service Fabric. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-replica-health"></a>Interfejs sfctl replica health
Pobiera kondycji usługi Service Fabric usługi stanowej replik lub wystąpień usługi bezstanowej.

Pobiera kondycję repliki usługi Service Fabric. Użyj EventsHealthStateFilter do filtrowania kolekcji zdarzeń dotyczących kondycji zgłoszone w replice oparte na stanie kondycji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator partycji [wymagane] | Tożsamość partycji. |
| --replica-id      [Required] | Identyfikator repliki. |
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

## <a name="sfctl-replica-info"></a>informacje o repliki sfctl
Pobiera informacje o repliki partycji usługi Service Fabric.

Odpowiedź zawiera identyfikator, rola, stan, kondycji, nazwa węzła, czas pracy i inne szczegóły dotyczące repliki.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator partycji [wymagane] | Tożsamość partycji. |
| --replica-id   [Required] | Identyfikator repliki. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-replica-list"></a>Interfejs sfctl repliki listy
Pobiera informacje o replikach partycji usługi Service Fabric.

Punkt końcowy GetReplicas zwraca informacje o replikach określonej partycji. Odpowiedź zawiera identyfikator, rola, stan, kondycji, nazwa węzła, czas pracy i inne szczegóły dotyczące repliki.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator partycji [wymagane] | Tożsamość partycji. |
| --token kontynuacji | Parametr tokenu kontynuacji służy do uzyskania następny zestaw wyników. Token kontynuacji o wartości niepuste znajduje się w odpowiedzi interfejsu API, gdy wyniki z systemu nie mieszczą się w jednej odpowiedzi. Jeśli ta wartość jest przekazywana do następnego wywołania interfejsu API, interfejs API zwraca następny zestaw wyników. Jeśli nie istnieją żadne dalsze wyniki, token kontynuacji nie zawiera wartości. Wartość tego parametru nie powinny być zakodowane w adresie URL. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-replica-remove"></a>Usuń replikę sfctl
Usuwa replikę usługi uruchomione w węźle.

Ten interfejs API symuluje awarię repliki usługi Service Fabric, usuwając replikę z klastrem usługi Service Fabric. Usunięcie zamyka repliki, przechodzi repliki do roli, None, a następnie usuwa wszystkie informacje stanu repliki z klastra. Ten interfejs API testów ścieżki usunięcia stanu repliki i symuluje ścieżka trwałych błędów raportu za pomocą interfejsów API klienta. Ostrzeżenie — tam są nie kontroli bezpieczeństwa, wykonywane, gdy jest używany ten interfejs API. Niepoprawne użycie tego interfejsu API może prowadzić do utraty danych dla usług stanowych. Ponadto Flaga forceRemove ma wpływ na inne replik hostowanych w tym samym procesie.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — [wymagane] Nazwa węzła | Nazwa węzła. |
| — Identyfikator partycji [wymagane] | Tożsamość partycji. |
| --replica-id   [Required] | Identyfikator repliki. |
| --force-remove | Usuwanie aplikacji usługi Service Fabric lub usługa wymuszone bez pośrednictwa sekwencji łagodne zamykanie. Ten parametr może służyć do wymuszone usunięcie aplikacji lub usługi w przypadku usuwania, które jest przekroczeniem limitu czasu z powodu problemów w kodzie usługi, który uniemożliwia łagodne zamknięcia repliki. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-replica-report-health"></a>Interfejs sfctl repliki raportów kondycji
Wysyła raport o kondycji w replice usługi Service Fabric.

Raporty stan kondycji określona replika usługi Service Fabric. Raport musi zawierać informacje o źródle raport o kondycji i właściwości, na którym jest zgłaszany. Raport jest wysyłany do bramy usługi Service Fabric repliki, która przekazuje w magazynie kondycji. Raport może być akceptowane przez bramę, ale odrzucony przez magazynu kondycji po dodatkową walidację. Na przykład magazynu kondycji może odrzucić raport ze względu na nieprawidłowy parametr, takich jak numer sekwencji starych. Aby sprawdzić, czy raport został zastosowany w magazynie kondycji, uruchom Uzyskaj kondycji repliki i upewnij się, że raport jest wyświetlany w sekcji HealthEvents.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --kondycji — właściwość [wymagane] | Właściwość o kondycji. <br><br> Jednostka może mieć raportów o kondycji dla różnych właściwości. Właściwość jest ciągu i stałych wyliczenia aby zezwalał na elastyczność reportera do kategoryzowania warunek stanu, która powoduje uruchomienie raportu. Na przykład reportera o ID "LocalWatchdog" można monitorować stan wolnego w węźle, aby go zgłosić właściwości "AvailableDisk" w tym węźle. Ten sam reportera monitorować łączność węzeł tak go zgłosić właściwości "Łączność" w tym samym węźle. W magazynie kondycji te raporty są traktowane jako zdarzenia dotyczące kondycji oddzielnych dla określonego węzła. Wraz z SourceId właściwość jednoznacznie identyfikuje informacje o kondycji. |
| --health-state    [Required] | Możliwe wartości to\: "Nieprawidłowy", "Ok", "Ostrzeżenie", "Error", "Nieznany". |
| — Identyfikator partycji [wymagane] | Tożsamość partycji. |
| --replica-id      [Required] | Tożsamość partycji. |
| --source-id       [Required] | Nazwa źródła, która identyfikuje składnik klienta/strażnika/systemu, który wygenerował informacji o kondycji. |
| --description | Opis informacji o kondycji. <br><br> Reprezentuje dowolny tekst, które umożliwiają dodawanie ludzi do odczytu informacji na temat raportu. Maksymalna długość ciągu opisu wynosi 4096 znaków. Jeśli podany ciąg jest dłuższy, zostaną automatycznie obcięte. W przypadku obcięty, ostatnie znaki opis zawiera znacznik "[obcięte]", a ciąg łączny rozmiar wynosi 4096 znaków. Obecność znacznika wskazuje, aby użytkownicy tej obcięcie wystąpił. Należy pamiętać, że gdy obcięty, opis ma mniej niż 4096 znaków z oryginalnego ciągu. |
| --immediate | Flaga, która wskazuje, czy raport powinna zostać wysłana natychmiast. <br><br> Raport o kondycji są wysyłane do aplikacji, która przekazuje w magazynie kondycji bramy usługi Service Fabric. Jeśli bezpośrednie jest ustawiona na wartość true, raport jest wysyłany bezpośrednio z bramy protokołu HTTP w magazynie kondycji niezależnie od ustawień klienta sieci szkieletowej, które używa aplikacji bramy protokołu HTTP. Jest to przydatne dla krytycznych raportów, które mają być wysyłane tak szybko, jak to możliwe. W zależności od czasu i innych warunków wysłaniem raportu może nadal się nie powieść, na przykład jeśli bramy HTTP został zamknięty lub komunikat nie dociera do bramy. Jeśli bezpośrednie jest ustawiona na wartość false, raport jest wysyłana na podstawie ustawień klienta kondycji z bramy protokołu HTTP. W związku z tym będzie partii zgodnie z konfiguracją HealthReportSendInterval. Jest to zalecane ustawienie ponieważ zezwala ona na kondycji klienta do optymalizacji raportowania komunikatów w magazynie danych kondycji, a także przetwarzania raportu kondycji kondycji. Domyślnie raporty nie są wysyłane bezpośrednio. |
| --remove-when-expired | Wartość wskazująca, czy raport jest usuwany z magazynu kondycji po jego wygaśnięciu. <br><br> Jeśli ustawiono wartość true, raport zostanie usunięty z magazynu kondycji po jego wygaśnięciu. Jeśli ma wartość false, raport jest traktowana jako błąd po upływie. Wartość tej właściwości to false domyślnie. Gdy klienci okresowo raportu ustala RemoveWhenExpired false (domyślnie). W ten sposób jest zgłaszającą ma problemy (np. zakleszczenia) i nie można zgłosić jednostki jest oceniany na błąd, po wygaśnięciu raport o kondycji. Oznacza flagą jednostki jako błąd stanu kondycji. |
| --sequence-number | Numer sekwencji dla tego raportu o kondycji jako ciągu numerycznego. <br><br> Numer sekwencyjny raportu służy magazynu kondycji do wykrywania stare raportów. Jeśli nie zostanie określony, numer sekwencyjny został wygenerowany automatycznie przez klienta usługi kondycji po dodaniu raportu. |
| --service-kind | Rodzaj usługi repliki bazy danych (bezstanowe lub stanowe), dla którego zgłaszania kondycji. Poniżej przedstawiono możliwe wartości\: "Bezstanowe", "Stanowa".  Domyślne\: stanowe. |
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

## <a name="sfctl-replica-restart"></a>ponowne uruchomienie repliki sfctl
Powoduje ponowne uruchomienie repliki usług utrwalonych usługi uruchomionej w węźle.

Powoduje ponowne uruchomienie repliki usług utrwalonych usługi uruchomionej w węźle. Ostrzeżenie — tam są nie kontroli bezpieczeństwa, wykonywane, gdy jest używany ten interfejs API. Niepoprawne użycie tego interfejsu API może prowadzić do utraty dostępności dla usług stanowych.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — [wymagane] Nazwa węzła | Nazwa węzła. |
| — Identyfikator partycji [wymagane] | Tożsamość partycji. |
| --replica-id   [Required] | Identyfikator repliki. |
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

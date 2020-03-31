---
title: Usługa interfejsu wiersza polecenia sieci szkieletowej usługi Azure
description: Dowiedz się więcej o sfctl, interfejsie wiersza polecenia sieci szkieletowej usługi Azure. Zawiera listę poleceń do zarządzania usługami, typami usług i pakietami usług.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 696de713129ca71dd7f2451501a7cc9eca0ee9b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906243"
---
# <a name="sfctl-service"></a>sfctl service
Tworzenie, usuwanie i zarządzanie usługami, typami usług i pakietami usług.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| nazwa aplikacji | Pobiera nazwę aplikacji sieci szkieletowej usług dla usługi. |
| lista pakietów kodu | Pobiera listę pakietów kodu wdrożonych w węźle sieci szkieletowej usług. |
| create | Tworzy określoną usługę sieci szkieletowej usług. |
| delete | Usuwa istniejącą usługę sieci szkieletowej usług. |
| typu wdrożonego | Pobiera informacje o określonym typie usługi aplikacji wdrożonej w węźle w klastrze sieci szkieletowej usług. |
| wdrożona lista typów | Pobiera listę zawierającą informacje o typach usług z aplikacji wdrożonych w węźle w klastrze sieci szkieletowej usług. |
| description | Pobiera opis istniejącej usługi sieci szkieletowej usług. |
| get-container-logs | Pobiera dzienniki kontenera dla kontenera wdrożony w węźle sieci szkieletowej usług. |
| Zdrowia | Pobiera kondycję określonej usługi sieci szkieletowej usług. |
| informacje o | Pobiera informacje o określonej usługi należącej do aplikacji sieci szkieletowej usług. |
| list | Pobiera informacje o wszystkich usług należących do aplikacji określonych przez identyfikator aplikacji. |
| manifest | Pobiera manifest opisujący typ usługi. |
| wdrażanie pakietów | Pobiera pakiety skojarzone z określonym manifestem usługi do pamięci podręcznej obrazu w określonym węźle. |
| pakiet-zdrowie | Pobiera informacje o kondycji pakietu usług dla określonej aplikacji wdrożonej dla węzła sieci szkieletowej usług i aplikacji. |
| pakiet-info | Pobiera listę pakietów usług wdrożonych w węźle sieci szkieletowej usług pasujących dokładnie określoną nazwę. |
| lista pakietów | Pobiera listę pakietów usług wdrożonych w węźle sieci szkieletowej usług. |
| odzyskiwanie | Wskazuje klastrowi sieci szkieletowej usług, że należy podjąć próbę odzyskania określonej usługi, która obecnie utknęła w utracie kworum. |
| raport-zdrowie | Wysyła raport kondycji usługi sieci szkieletowej usług. |
| Rozwiązać | Rozwiązywanie problemu partycji sieci szkieletowej usług. |
| lista typów | Pobiera listę zawierającą informacje o typach usług, które są obsługiwane przez aprowizowanym typem aplikacji w klastrze sieci szkieletowej usług. |
| update | Aktualizuje określoną usługę przy użyciu podanego opisu aktualizacji. |

## <a name="sfctl-service-app-name"></a>nazwa aplikacji usługi sfctl
Pobiera nazwę aplikacji sieci szkieletowej usług dla usługi.

Pobiera nazwę aplikacji dla określonej usługi. Błąd 404 FABRIC_E_SERVICE_DOES_NOT_EXIST jest zwracany, jeśli usługa o identyfikatorze usługi nie istnieje.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --service-id [Wymagane] | Tożsamość usługi. Ten identyfikator jest zazwyczaj pełną nazwą usługi bez\:schematu URI "fabric". Począwszy od wersji 6.0, nazwy hierarchiczne\~są rozdzielane znakiem " ". Na przykład jeśli nazwa usługi\:to "fabric /myapp/app1/svc1", tożsamość\~usługi\~będzie "myapp app1 svc1" w 6.0+ i "myapp/app1/svc1" w poprzednich wersjach. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-service-code-package-list"></a>sfctl service code-package-list
Pobiera listę pakietów kodu wdrożonych w węźle sieci szkieletowej usług.

Pobiera listę pakietów kodu wdrożonych w węźle sieci szkieletowej usług dla danej aplikacji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --application-id [Wymagane] | Tożsamość aplikacji. Zazwyczaj jest to pełna nazwa aplikacji bez\:schematu URI 'fabric'. Począwszy od wersji 6.0, nazwy hierarchiczne\~są rozdzielane znakiem " ". Na przykład jeśli nazwa aplikacji\:to "fabric /myapp/app1", tożsamość\~aplikacji będzie "myapp app1" w wersji 6.0+ i "myapp/app1" w poprzednich wersjach. |
| --node-name [Wymagane] | Nazwa węzła. |
| --code-package-name | Nazwa pakietu kodu określonego w manifeście usługi zarejestrowany jako część typu aplikacji w klastrze sieci szkieletowej usług. |
| --service-manifest-name | Nazwa manifestu usługi zarejestrowanego jako część typu aplikacji w klastrze sieci szkieletowej usług. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-service-create"></a>tworzenie usługi sfctl
Tworzy określoną usługę sieci szkieletowej usług.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --app-id [Wymagane] | Tożsamość aplikacji. Zazwyczaj jest to pełna nazwa aplikacji bez\:schematu URI 'fabric'. Począwszy od wersji 6.0, nazwy hierarchiczne\~są rozdzielane znakiem ' . Na przykład jeśli nazwa aplikacji\:to "fabric /myapp/app1", tożsamość\~aplikacji będzie "myapp app1" w wersji 6.0+ i "myapp/app1" w poprzednich wersjach. |
| --name [Wymagane] | Nazwa usługi. Powinien to być podrzędny identyfikator aplikacji. Jest to pełna nazwa, `fabric\:` w tym identyfikator URI. Na przykład `fabric\:/A/B` usługa jest `fabric\:/A`dziewką aplikacji . |
| --service-type [Wymagane] | Nazwa typu usługi. |
| --aktywacja-tryb | Tryb aktywacji pakietu serwisowego. |
| --ograniczenia | Ograniczenia umieszczania jako ciąg. Ograniczenia umieszczania są wyrażeniami logicznymi we właściwościach węzła i umożliwiają ograniczenie usługi do określonych węzłów na podstawie wymagań dotyczących usługi. Na przykład, aby umieścić usługę w węzłach, w\:których NodeType jest niebieski, określ następujące "NodeColor == blue". |
| --skorelowana usługa | Nazwa usługi docelowej do skorelowania z. |
| --korelacja | Skorelować usługę z istniejącą usługą przy użyciu koligacji wyrównania. |
| --dns-name | Nazwa DNS usługi, która ma zostać utworzona. Dla tego ustawienia musi być włączona usługa systemu DNS sieci szkieletowej usług. |
| --liczba wystąpień | Liczba wystąpień. Dotyczy to tylko usług bezstanowych. |
| --int-schemat | Wskazuje, że usługa powinna być jednolicie podzielona na partycje w zakresie niepodpisanych liczby całkowite. |
| --int-scheme-count | Liczba partycji wewnątrz zakresu klucza liczby całkowitej do utworzenia, jeśli używasz jednolitego schematu partycji całkowitej. |
| --int-scheme-high | Koniec zakresu liczby całkowitej klucza, jeśli używa się jednolitego schematu partycji całkowitej. |
| --int-scheme-low | Początek zakresu liczby całkowitej klucza, jeśli używa się jednolitego schematu partycji całkowitej. |
| --load-metryki | Zakodowana w UJson lista metryk używanych podczas równoważenia obciążenia usług w węzłach. |
| --min-replica-set-size | Minimalny rozmiar zestawu replik jako liczba. Dotyczy to tylko usług stanowych. |
| --move-koszt | Określa koszt przeniesienia usługi. Możliwe wartości\: to "Zero", "Niski", "Średni", "Wysoki", "Bardzo wysoki". |
| --named-schemat | Wskazuje, że usługa powinna mieć wiele partycji o nazwie. |
| --named-scheme-list | Lista nazw zakodowanych w usłudze JSON do partycjonowania usługi, jeśli używa się nazwanego schematu partycji. |
| --no-persisted-state | Jeśli true, oznacza to, że usługa nie ma trwałego stanu przechowywane na dysku lokalnym lub tylko przechowuje stan w pamięci. |
| --placement-policy-list | Zakodowana w Usłudze lista zasad umieszczania usługi i wszelkich skojarzonych nazw domen. Zasady mogą być jednym\: `NonPartiallyPlaceService` `PreferPrimaryDomain`lub `RequireDomain` `RequireDomainDistribution`kilkoma , , , . |
| --kworum-strata-czekać | Maksymalny czas trwania w sekundach, dla którego partycja może znajdować się w stanie utraty kworum. Dotyczy to tylko usług stanowych. |
| --replica-restart-wait | Czas trwania w sekundach między po upływie repliki a utworzeniem nowej repliki. Dotyczy to tylko usług stanowych. |
| --skalowanie-zasady | Lista zasad skalowania dla tej usługi zakodowana przez json. |
| --service-placement-time | Czas trwania, dla którego repliki mogą pozostać InBuild przed zgłoszeniem, że kompilacja jest zablokowany. Dotyczy to tylko usług stanowych. |
| --singleton-schemat | Wskazuje, że usługa powinna mieć jedną partycję lub być usługą nieobjętą partycją. |
| --stand-by-replica-keep | Maksymalny czas trwania w sekundach, dla którego repliki w trybie gotowości zostaną zachowane przed usunięciem. Dotyczy to tylko usług stanowych. |
| --stateful | Wskazuje, że usługa jest usługą stanową. |
| --bezstanowy | Wskazuje, że usługa jest usługą bezstanową. |
| --target-replica-set-size | Rozmiar zestawu replik docelowych jako liczba. Dotyczy to tylko usług stanowych. |
| --timeout -t | Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-service-delete"></a>Usuwanie usługi sfctl
Usuwa istniejącą usługę sieci szkieletowej usług.

Usługa musi zostać utworzona, zanim będzie można ją usunąć. Domyślnie sieci szkieletowej usług spróbuje zamknąć repliki usługi w sposób pełen wdzięku, a następnie usunąć usługę. Jednak jeśli usługa ma problemy z zamknięciem repliki bezpiecznie, operacja usuwania może zająć dużo czasu lub utknąć. Użyj opcjonalnej forceremove flagi pominąć sekwencji zamknięcia w sposób wdzięku i zdecydowanie usunąć usługę.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --service-id [Wymagane] | Tożsamość usługi. Ten identyfikator jest zazwyczaj pełną nazwą usługi bez\:schematu URI "fabric". Począwszy od wersji 6.0, nazwy hierarchiczne\~są rozdzielane znakiem " ". Na przykład jeśli nazwa usługi\:to "fabric /myapp/app1/svc1", tożsamość\~usługi\~będzie "myapp app1 svc1" w 6.0+ i "myapp/app1/svc1" w poprzednich wersjach. |
| --force-remove | Usuń aplikację sieci szkieletowej usług lub usługi na siłę bez przechodzenia przez sekwencję bezpiecznego zamykania. Ten parametr może służyć do zdecydowanego usunięcia aplikacji lub usługi, dla których delete jest limit czasu z powodu problemów w kodzie usługi, który uniemożliwia wdzięczne zamknięcie replik. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-service-deployed-type"></a>wdrożony typ usługi sfctl
Pobiera informacje o określonym typie usługi aplikacji wdrożonej w węźle w klastrze sieci szkieletowej usług.

Pobiera listę zawierającą informacje o określonym typie usługi z aplikacji wdrożonych w węźle w klastrze sieci szkieletowej usług. Odpowiedź zawiera nazwę typu usługi, jej stan rejestracji, pakiet kodu, który go zarejestrował i identyfikator aktywacji pakietu usługi. Każdy wpis reprezentuje jedną aktywację typu usługi, zróżnicowaną identyfikatorem aktywacji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --application-id [Wymagane] | Tożsamość aplikacji. Zazwyczaj jest to pełna nazwa aplikacji bez\:schematu URI 'fabric'. Począwszy od wersji 6.0, nazwy hierarchiczne\~są rozdzielane znakiem " ". Na przykład jeśli nazwa aplikacji\:to "fabric /myapp/app1", tożsamość\~aplikacji będzie "myapp app1" w wersji 6.0+ i "myapp/app1" w poprzednich wersjach. |
| --node-name [Wymagane] | Nazwa węzła. |
| --service-type-name [Wymagane] | Określa nazwę typu usługi sieci szkieletowej usług. |
| --service-manifest-name | Nazwa manifestu usługi do filtrowania listy wdrożonych informacji o typie usługi. Jeśli zostanie określony, odpowiedź będzie zawierać tylko informacje o typach usług, które są zdefiniowane w tym manifeście usługi. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-service-deployed-type-list"></a>lista typu wdrożonej usługi sfctl
Pobiera listę zawierającą informacje o typach usług z aplikacji wdrożonych w węźle w klastrze sieci szkieletowej usług.

Pobiera listę zawierającą informacje o typach usług z aplikacji wdrożonych w węźle w klastrze sieci szkieletowej usług. Odpowiedź zawiera nazwę typu usługi, jej stan rejestracji, pakiet kodu, który go zarejestrował i identyfikator aktywacji pakietu usługi.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --application-id [Wymagane] | Tożsamość aplikacji. Zazwyczaj jest to pełna nazwa aplikacji bez\:schematu URI 'fabric'. Począwszy od wersji 6.0, nazwy hierarchiczne\~są rozdzielane znakiem " ". Na przykład jeśli nazwa aplikacji\:to "fabric /myapp/app1", tożsamość\~aplikacji będzie "myapp app1" w wersji 6.0+ i "myapp/app1" w poprzednich wersjach. |
| --node-name [Wymagane] | Nazwa węzła. |
| --service-manifest-name | Nazwa manifestu usługi do filtrowania listy wdrożonych informacji o typie usługi. Jeśli zostanie określony, odpowiedź będzie zawierać tylko informacje o typach usług, które są zdefiniowane w tym manifeście usługi. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-service-description"></a>opis usługi sfctl
Pobiera opis istniejącej usługi sieci szkieletowej usług.

Pobiera opis istniejącej usługi sieci szkieletowej usług. Usługa musi zostać utworzona, zanim będzie można uzyskać jej opis.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --service-id [Wymagane] | Tożsamość usługi. Ten identyfikator jest zazwyczaj pełną nazwą usługi bez\:schematu URI "fabric". Począwszy od wersji 6.0, nazwy hierarchiczne\~są rozdzielane znakiem " ". Na przykład jeśli nazwa usługi\:to "fabric /myapp/app1/svc1", tożsamość\~usługi\~będzie "myapp app1 svc1" w 6.0+ i "myapp/app1/svc1" w poprzednich wersjach. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-service-get-container-logs"></a>sfctl usługi get-container-logs
Pobiera dzienniki kontenera dla kontenera wdrożony w węźle sieci szkieletowej usług.

Pobiera dzienniki kontenera dla kontenera wdrożony w węźle sieci szkieletowej usług dla danego pakietu kodu.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --application-id [Wymagane] | Tożsamość aplikacji. Zazwyczaj jest to pełna nazwa aplikacji bez\:schematu URI 'fabric'. Począwszy od wersji 6.0, nazwy hierarchiczne\~są rozdzielane znakiem " ". Na przykład jeśli nazwa aplikacji\:to "fabric /myapp/app1", tożsamość\~aplikacji będzie "myapp app1" w wersji 6.0+ i "myapp/app1" w poprzednich wersjach. |
| --code-package-name [Wymagane] | Nazwa pakietu kodu określonego w manifeście usługi zarejestrowany jako część typu aplikacji w klastrze sieci szkieletowej usług. |
| --node-name [Wymagane] | Nazwa węzła. |
| --service-manifest-name [Wymagane] | Nazwa manifestu usługi zarejestrowanego jako część typu aplikacji w klastrze sieci szkieletowej usług. |
| --poprzedni | Określa, czy dzienniki kontenerów mają być wyjęte z otwartych/martwych kontenerów wystąpienia pakietu kodu. |
| --ogon | Liczba wierszy do wyświetlenia od końca dzienników. Wartość domyślna to 100. "wszystko", aby wyświetlić pełne dzienniki. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-service-health"></a>sfctl kondycja usługi
Pobiera kondycję określonej usługi sieci szkieletowej usług.

Pobiera informacje o kondycji określonej usługi. Za pomocą EventsHealthStateFilter do filtrowania kolekcji zdarzeń kondycji zgłoszonych w usłudze na podstawie stanu kondycji. Użyj PartitionsHealthStateFilter do filtrowania kolekcji partycji zwróconych. Jeśli określisz usługę, która nie istnieje w magazynie kondycji, to żądanie zwraca błąd.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --service-id [Wymagane] | Tożsamość usługi. Ten identyfikator jest zazwyczaj pełną nazwą usługi bez\:schematu URI "fabric". Począwszy od wersji 6.0, nazwy hierarchiczne\~są rozdzielane znakiem " ". Na przykład jeśli nazwa usługi\:to "fabric /myapp/app1/svc1", tożsamość\~usługi\~będzie "myapp app1 svc1" w 6.0+ i "myapp/app1/svc1" w poprzednich wersjach. |
| --events-health-state-filter | Umożliwia filtrowanie kolekcji HealthEvent obiektów zwracanych na podstawie stanu kondycji. Możliwe wartości dla tego parametru obejmują wartość całkowitą jednego z następujących stanów kondycji. Zwracane są tylko zdarzenia, które pasują do filtru. Wszystkie zdarzenia są używane do oceny stanu kondycji zagregowane. Jeśli nie zostanie określony, wszystkie wpisy są zwracane. Wartości stanu są wyliczenia oparte na flagi, więc wartość może być kombinacją tych wartości, uzyskane przy użyciu bitowego "OR" operatora. Na przykład Jeśli podana wartość wynosi 6, zwracane są wszystkie zdarzenia o wartości HealthState OK (2) i Warning (4).  <br> - Domyślna — wartość domyślna. Pasuje do dowolnego HealthState. Wartość wynosi zero.  <br> - Brak — filtr, który nie pasuje do żadnej wartości HealthState. Używane w celu zwrócenia żadnych wyników w danej kolekcji stanów. Wartość wynosi 1.  <br> - Ok - Filtr, który pasuje do danych wejściowych z HealthState wartość Ok. Wartość wynosi 2.  <br> - Ostrzeżenie — filtr, który pasuje do danych wejściowych z HealthState wartość Ostrzeżenie. Wartość wynosi 4.  <br> - Błąd — filtr, który pasuje do danych wejściowych z HealthState wartość Error. Wartość wynosi 8.  <br> - Wszystkie — filtr, który pasuje do danych wejściowych z dowolnej wartości HealthState. Wartość to 65535. |
| --exclude-health-statistics | Wskazuje, czy statystyki kondycji powinny być zwracane jako część wyniku kwerendy. False domyślnie. Statystyki pokazują liczbę jednostek podrzędnych w stanie kondycji Ok, Ostrzeżenie i Błąd. |
| --partitions-health-state-filter | Umożliwia filtrowanie obiektów stanu kondycji partycji zwracanych w wyniku kwerendy kondycji usługi na podstawie ich stanu kondycji. Możliwe wartości dla tego parametru obejmują wartość całkowitą jednego z następujących stanów kondycji. Zwracane są tylko partycje, które pasują do filtru. Wszystkie partycje są używane do oceny stanu kondycji zagregowane. Jeśli nie zostanie określony, wszystkie wpisy są zwracane. Wartości stanu są wyliczenia oparte na flagi, więc wartość może być kombinacją tych wartości uzyskanych za pomocą bitowego operatora "OR". Na przykład jeśli podana wartość wynosi 6, stan kondycji partycji z HealthState wartość OK (2) i Ostrzeżenie (4) zostaną zwrócone.  <br> - Domyślna — wartość domyślna. Pasuje do dowolnego HealthState. Wartość wynosi zero.  <br> - Brak — filtr, który nie pasuje do żadnej wartości HealthState. Używane w celu zwrócenia żadnych wyników w danej kolekcji stanów. Wartość wynosi 1.  <br> - Ok - Filtr, który pasuje do danych wejściowych z HealthState wartość Ok. Wartość wynosi 2.  <br> - Ostrzeżenie — filtr, który pasuje do danych wejściowych z HealthState wartość Ostrzeżenie. Wartość wynosi 4.  <br> - Błąd — filtr, który pasuje do danych wejściowych z HealthState wartość Error. Wartość wynosi 8.  <br> - Wszystkie — filtr, który pasuje do danych wejściowych z dowolnej wartości HealthState. Wartość to 65535. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-service-info"></a>informacje o usłudze sfctl
Pobiera informacje o określonej usługi należącej do aplikacji sieci szkieletowej usług.

Zwraca informacje o określonej usłudze należącej do określonej aplikacji sieci szkieletowej usług.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --application-id [Wymagane] | Tożsamość aplikacji. Zazwyczaj jest to pełna nazwa aplikacji bez\:schematu URI 'fabric'. Począwszy od wersji 6.0, nazwy hierarchiczne\~są rozdzielane znakiem " ". Na przykład jeśli nazwa aplikacji\:to "fabric /myapp/app1", tożsamość\~aplikacji będzie "myapp app1" w wersji 6.0+ i "myapp/app1" w poprzednich wersjach. |
| --service-id [Wymagane] | Tożsamość usługi. Ten identyfikator jest zazwyczaj pełną nazwą usługi bez\:schematu URI "fabric". Począwszy od wersji 6.0, nazwy hierarchiczne\~są rozdzielane znakiem " ". Na przykład jeśli nazwa usługi\:to "fabric /myapp/app1/svc1", tożsamość\~usługi\~będzie "myapp app1 svc1" w 6.0+ i "myapp/app1/svc1" w poprzednich wersjach. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-service-list"></a>lista usług sfctl
Pobiera informacje o wszystkich usług należących do aplikacji określonych przez identyfikator aplikacji.

Zwraca informacje o wszystkich usługach należących do aplikacji określonej przez identyfikator aplikacji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --application-id [Wymagane] | Tożsamość aplikacji. Zazwyczaj jest to pełna nazwa aplikacji bez\:schematu URI 'fabric'. Począwszy od wersji 6.0, nazwy hierarchiczne\~są rozdzielane znakiem " ". Na przykład jeśli nazwa aplikacji\:to "fabric /myapp/app1", tożsamość\~aplikacji będzie "myapp app1" w wersji 6.0+ i "myapp/app1" w poprzednich wersjach. |
| --continuation-token | Parametr tokenu kontynuacji jest używany do uzyskania następnego zestawu wyników. Token kontynuacji z wartością niepustą jest uwzględniony w odpowiedzi interfejsu API, gdy wyniki z systemu nie mieszczą się w pojedynczej odpowiedzi. Gdy ta wartość jest przekazywana do następnego wywołania interfejsu API, interfejs API zwraca następny zestaw wyników. Jeśli nie ma żadnych dalszych wyników, token kontynuacji nie zawiera wartości. Wartość tego parametru nie powinna być zakodowana w adresie URL. |
| --service-type-name | Nazwa typu usługi używana do filtrowania usług do kwerendy. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-service-manifest"></a>manifest usługi sfctl
Pobiera manifest opisujący typ usługi.

Pobiera manifest opisujący typ usługi. Odpowiedź zawiera manifest usługi XML jako ciąg.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --application-type-name [Wymagane] | Nazwa typu aplikacji. |
| --application-type-version [Wymagane] | Wersja typu aplikacji. |
| --service-manifest-name [Wymagane] | Nazwa manifestu usługi zarejestrowanego jako część typu aplikacji w klastrze sieci szkieletowej usług. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-service-package-deploy"></a>wdrażanie pakietu usługi sfctl
Pobiera pakiety skojarzone z określonym manifestem usługi do pamięci podręcznej obrazu w określonym węźle.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --app-type-name [Wymagane] | Nazwa manifestu aplikacji dla odpowiedniego manifestu żądanej usługi. |
| --app-type-version [Wymagane] | Wersja manifestu aplikacji dla odpowiedniego manifestu żądanej usługi. |
| --node-name [Wymagane] | Nazwa węzła. |
| --service-manifest-name [Wymagane] | Nazwa manifestu usługi skojarzonego z pakietami, które zostaną pobrane. |
| --share-policy | Lista zasad udostępniania zakodowana przez JSON. Każdy element zasad udostępniania składa się z "name" i "scope". Nazwa odpowiada nazwie kodu, konfiguracji lub pakietu danych, który ma zostać udostępniony. Zakres może być albo "Brak", "Wszystkie", "Kod", "Config" lub "Dane". |
| --timeout -t | Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-service-package-health"></a>pakiet usługi sfctl-zdrowie
Pobiera informacje o kondycji pakietu usług dla określonej aplikacji wdrożonej dla węzła sieci szkieletowej usług i aplikacji.

Pobiera informacje o kondycji pakietu usług dla określonej aplikacji wdrożonych w węźle sieci szkieletowej usług. Użyj EventsHealthStateFilter opcjonalnie filtrować dla kolekcji HealthEvent obiektów zgłoszonych na wdrożony pakiet usługi na podstawie stanu kondycji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --application-id [Wymagane] | Tożsamość aplikacji. Zazwyczaj jest to pełna nazwa aplikacji bez\:schematu URI 'fabric'. Począwszy od wersji 6.0, nazwy hierarchiczne\~są rozdzielane znakiem " ". Na przykład jeśli nazwa aplikacji\:to "fabric /myapp/app1", tożsamość\~aplikacji będzie "myapp app1" w wersji 6.0+ i "myapp/app1" w poprzednich wersjach. |
| --node-name [Wymagane] | Nazwa węzła. |
| --service-package-name [Wymagane] | Nazwa pakietu usług. |
| --events-health-state-filter | Umożliwia filtrowanie kolekcji HealthEvent obiektów zwracanych na podstawie stanu kondycji. Możliwe wartości dla tego parametru obejmują wartość całkowitą jednego z następujących stanów kondycji. Zwracane są tylko zdarzenia, które pasują do filtru. Wszystkie zdarzenia są używane do oceny stanu kondycji zagregowane. Jeśli nie zostanie określony, wszystkie wpisy są zwracane. Wartości stanu są wyliczenia oparte na flagi, więc wartość może być kombinacją tych wartości, uzyskane przy użyciu bitowego "OR" operatora. Na przykład Jeśli podana wartość wynosi 6, zwracane są wszystkie zdarzenia o wartości HealthState OK (2) i Warning (4).  <br> - Domyślna — wartość domyślna. Pasuje do dowolnego HealthState. Wartość wynosi zero.  <br> - Brak — filtr, który nie pasuje do żadnej wartości HealthState. Używane w celu zwrócenia żadnych wyników w danej kolekcji stanów. Wartość wynosi 1.  <br> - Ok - Filtr, który pasuje do danych wejściowych z HealthState wartość Ok. Wartość wynosi 2.  <br> - Ostrzeżenie — filtr, który pasuje do danych wejściowych z HealthState wartość Ostrzeżenie. Wartość wynosi 4.  <br> - Błąd — filtr, który pasuje do danych wejściowych z HealthState wartość Error. Wartość wynosi 8.  <br> - Wszystkie — filtr, który pasuje do danych wejściowych z dowolnej wartości HealthState. Wartość to 65535. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-service-package-info"></a>sfctl usługa pakiet-info
Pobiera listę pakietów usług wdrożonych w węźle sieci szkieletowej usług pasujących dokładnie określoną nazwę.

Zwraca informacje o pakietach usług wdrożonych w węźle sieci szkieletowej usług dla danej aplikacji. Wyniki te są pakietów usług, których nazwa jest dokładnie nazwa pakietu usługi określony jako parametr.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --application-id [Wymagane] | Tożsamość aplikacji. Zazwyczaj jest to pełna nazwa aplikacji bez\:schematu URI 'fabric'. Począwszy od wersji 6.0, nazwy hierarchiczne\~są rozdzielane znakiem " ". Na przykład jeśli nazwa aplikacji\:to "fabric /myapp/app1", tożsamość\~aplikacji będzie "myapp app1" w wersji 6.0+ i "myapp/app1" w poprzednich wersjach. |
| --node-name [Wymagane] | Nazwa węzła. |
| --service-package-name [Wymagane] | Nazwa pakietu usług. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-service-package-list"></a>lista pakietów usług sfctl
Pobiera listę pakietów usług wdrożonych w węźle sieci szkieletowej usług.

Zwraca informacje o pakietach usług wdrożonych w węźle sieci szkieletowej usług dla danej aplikacji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --application-id [Wymagane] | Tożsamość aplikacji. Zazwyczaj jest to pełna nazwa aplikacji bez\:schematu URI 'fabric'. Począwszy od wersji 6.0, nazwy hierarchiczne\~są rozdzielane znakiem " ". Na przykład jeśli nazwa aplikacji\:to "fabric /myapp/app1", tożsamość\~aplikacji będzie "myapp app1" w wersji 6.0+ i "myapp/app1" w poprzednich wersjach. |
| --node-name [Wymagane] | Nazwa węzła. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-service-recover"></a>odzyskiwanie usługi sfctl
Wskazuje klastrowi sieci szkieletowej usług, że należy podjąć próbę odzyskania określonej usługi, która obecnie utknęła w utracie kworum.

Wskazuje klastrowi sieci szkieletowej usług, że należy podjąć próbę odzyskania określonej usługi, która obecnie utknęła w utracie kworum. Ta operacja powinna być wykonywana tylko wtedy, gdy wiadomo, że repliki, które są w dół nie można odzyskać. Nieprawidłowe użycie tego interfejsu API może spowodować potencjalną utratę danych.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --service-id [Wymagane] | Tożsamość usługi. Ten identyfikator jest zazwyczaj pełną nazwą usługi bez\:schematu URI "fabric". Począwszy od wersji 6.0, nazwy hierarchiczne\~są rozdzielane znakiem " ". Na przykład jeśli nazwa usługi\:to "fabric /myapp/app1/svc1", tożsamość\~usługi\~będzie "myapp app1 svc1" w 6.0+ i "myapp/app1/svc1" w poprzednich wersjach. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-service-report-health"></a>sfctl service report-health
Wysyła raport kondycji usługi sieci szkieletowej usług.

Raportuje stan kondycji określonej usługi sieci szkieletowej usług. Raport musi zawierać informacje o źródle raportu kondycji i właściwości, na których jest raport. Raport jest wysyłany do usługi bramy sieci szkieletowej usług, która jest przesyłana dalej do magazynu kondycji. Raport może zostać zaakceptowany przez bramę, ale odrzucony przez magazyn kondycji po dodatkowej weryfikacji. Na przykład magazyn kondycji może odrzucić raport z powodu nieprawidłowego parametru, takiego jak nieświeży numer sekwencyjny. Aby sprawdzić, czy raport został zastosowany w magazynie kondycji, sprawdź, czy raport pojawia się w zdarzeniach kondycji usługi.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --health-property [Wymagane] | Właściwość informacji o kondycji. <br><br> Jednostka może mieć raporty kondycji dla różnych właściwości. Właściwość jest ciągiem, a nie stałe wyliczenie, aby umożliwić elastyczność reporter kategoryzować warunek stanu, który wyzwala raport. Na przykład reporter z SourceId "LocalWatchdog" może monitorować stan dostępnego dysku w węźle, dzięki czemu może zgłosić właściwość "AvailableDisk" w tym węźle. Ten sam reporter może monitorować łączność węzła, dzięki czemu może zgłaszać właściwość "Łączność" w tym samym węźle. W magazynie kondycji te raporty są traktowane jako oddzielne zdarzenia kondycji dla określonego węzła. Wraz z SourceId właściwość jednoznacznie identyfikuje informacje o kondycji. |
| --health-state [Wymagane] | Możliwe wartości\: to "Invalid", "Ok", "Warning", "Error", "Unknown". |
| --service-id [Wymagane] | Tożsamość usługi. <br><br> Zazwyczaj jest to pełna nazwa usługi bez\:schematu URI 'fabric'. Począwszy od wersji 6.0, nazwy hierarchiczne\~są rozdzielane znakiem ' . Na przykład jeśli nazwa usługi\:to "fabric /myapp/app1/svc1", tożsamość\~usługi\~będzie "myapp app1 svc1" w 6.0+ i "myapp/app1/svc1" w poprzednich wersjach. |
| --source-id [Wymagane] | Nazwa źródła identyfikująca składnik klienta/watchdog/system, który wygenerował informacje o kondycji. |
| --opis | Opis informacji o kondycji. <br><br> Reprezentuje wolny tekst używany do dodawania informacji o raporcie czytelnych dla ludzi. Maksymalna długość ciągu opisu wynosi 4096 znaków. Jeśli podany ciąg jest dłuższy, zostanie automatycznie obcięty. Po obcięciu ostatnie znaki opisu zawierają znacznik "[Obcięty]", a całkowity rozmiar ciągu to 4096 znaków. Obecność znacznika wskazuje użytkownikom, że doszło do obcinania. Należy zauważyć, że po obcięciu opis ma mniej niż 4096 znaków z oryginalnego ciągu. |
| --natychmiastowe | Flaga wskazująca, czy raport powinien zostać wysłany natychmiast. <br><br> Raport kondycji jest wysyłany do aplikacji bramy sieci szkieletowej usług, która przekazuje do magazynu kondycji. Jeśli immediate jest ustawiona na true, raport jest wysyłany natychmiast z bramy HTTP do magazynu kondycji, niezależnie od ustawień klienta sieci szkieletowej, które używa aplikacji bramy HTTP. Jest to przydatne w przypadku raportów krytycznych, które powinny być wysyłane tak szybko, jak to możliwe. W zależności od czasu i innych warunków wysłanie raportu może zakończyć się niepowodzeniem, na przykład jeśli brama HTTP jest zamknięta lub wiadomość nie dociera do bramy. Jeśli immediate jest ustawiona na false, raport jest wysyłany na podstawie ustawień klienta kondycji z bramy HTTP. W związku z tym będzie partią zgodnie z HealthReportSendInterval konfiguracji. Jest to zalecane ustawienie, ponieważ umożliwia klientowi kondycji optymalizację komunikatów raportowania kondycji do magazynu kondycji, a także przetwarzania raportu kondycji. Domyślnie raporty nie są wysyłane natychmiast. |
| --remove-when-expired | Wartość wskazująca, czy raport jest usuwany z magazynu kondycji po jego wygaśnięciu. <br><br> Jeśli ustawiona wartość true, raport zostanie usunięty z magazynu kondycji po jego wygaśnięciu. Jeśli ustawiona na false, raport jest traktowany jako błąd po wygaśnięciu. Wartość tej właściwości jest domyślnie false. Gdy klienci raportują okresowo, należy ustawić RemoveWhenExpired false (domyślnie). W ten sposób jest reporter ma problemy (np. zakleszczenie) i nie może zgłosić, jednostka jest oceniana w przypadku błędu po wygaśnięciu raportu kondycji. Oznacza to jednostkę jako w stanie kondycji błąd. |
| --sekwencja-numer | Numer sekwencyjny dla tego raportu kondycji jako ciąg numeryczny. <br><br> Numer sekwencyjny raportu jest używany przez magazyn kondycji do wykrywania starych raportów. Jeśli nie zostanie określony, numer sekwencyjny jest generowany automatycznie przez klienta kondycji po dodaniu raportu. |
| --timeout -t | Wartość\: domyślna 60. |
| --ttl | Czas trwania, dla którego ten raport kondycji jest prawidłowy. To pole używa formatu ISO8601 do określania czasu trwania. <br><br> Gdy klienci zgłaszają się okresowo, powinni wysyłać raporty z większą częstotliwością niż czas życia. Jeśli klienci raportują o przejściu, mogą ustawić czas na nieskończoność. Po upływie czasu wygaśnięcia zdarzenia kondycji, który zawiera informacje o kondycji jest usuwany z magazynu kondycji, jeśli RemoveWhenExpired jest true, lub oceniane w przypadku błędu, jeśli RemoveWhenExpired false. Jeśli nie określono, czas na żywo domyślnie nieskończoną wartość. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-service-resolve"></a>rozwiązywanie problemów z usługą sfctl
Rozwiązywanie problemu partycji sieci szkieletowej usług.

Rozwiąż partycję usługi sieci szkieletowej, aby uzyskać punkty końcowe replik usługi.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --service-id [Wymagane] | Tożsamość usługi. Ten identyfikator jest zazwyczaj pełną nazwą usługi bez\:schematu URI "fabric". Począwszy od wersji 6.0, nazwy hierarchiczne\~są rozdzielane znakiem " ". Na przykład jeśli nazwa usługi\:to "fabric /myapp/app1/svc1", tożsamość\~usługi\~będzie "myapp app1 svc1" w 6.0+ i "myapp/app1/svc1" w poprzednich wersjach. |
| --partition-key-type | Typ klucza dla partycji. Ten parametr jest wymagany, jeśli schemat partycji dla usługi jest Int64Range lub Named. Możliwe wartości są następujące. - Brak (1) - Wskazuje, że parametr PartitionKeyValue nie jest określony. Jest to ważne dla partycji ze schematem partycjonowania jako Singleton. Jest to wartość domyślna. Wartość wynosi 1. - Int64Range (2) - Wskazuje, że parametr PartitionKeyValue jest kluczem partycji int64. Jest to ważne dla partycji ze schematem partycjonowania jako Int64Range. Wartość wynosi 2. - Nazwany (3) - Wskazuje, że Parametr PartitionKeyValue jest nazwą partycji. Jest to prawidłowe dla partycji ze schematem partycjonowania jako nazwany. Wartość wynosi 3. |
| --partition-key-value | Klucz partycji. Jest to wymagane, jeśli schemat partycji dla usługi jest Int64Range lub Named. Nie jest to identyfikator partycji, ale raczej wartość klucza całkowitej lub nazwa identyfikatora partycji. Na przykład jeśli usługa używa partycji z zakresu od 0 do 10, a następnie PartitionKeyValue będzie całkowitej liczby w tym zakresie. Opis usługi kwerendy, aby zobaczyć zakres lub nazwę. |
| --poprzednia-rsp-wersja | Wartość w polu Version odpowiedzi, która została odebrana wcześniej. Jest to wymagane, jeśli użytkownik wie, że wynik, który został zdobyty wcześniej jest przestarzały. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-service-type-list"></a>lista typów usługi sfctl
Pobiera listę zawierającą informacje o typach usług, które są obsługiwane przez aprowizowanym typem aplikacji w klastrze sieci szkieletowej usług.

Pobiera listę zawierającą informacje o typach usług, które są obsługiwane przez aprowizowanym typem aplikacji w klastrze sieci szkieletowej usług. Podany typ aplikacji musi istnieć. W przeciwnym razie zwracany jest stan 404.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --application-type-name [Wymagane] | Nazwa typu aplikacji. |
| --application-type-version [Wymagane] | Wersja typu aplikacji. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-service-update"></a>Aktualizacja usługi sfctl
Aktualizuje określoną usługę przy użyciu podanego opisu aktualizacji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --service-id [Wymagane] | Tożsamość usługi. Zazwyczaj jest to pełna nazwa usługi bez\:schematu URI 'fabric'. Począwszy od wersji 6.0, nazwy hierarchiczne\~są rozdzielane znakiem " ". Na przykład jeśli nazwa usługi\:to "fabric /myapp/app1/svc1", tożsamość\~usługi\~będzie "myapp app1 svc1" w 6.0+ i "myapp/app1/svc1" w poprzednich wersjach. |
| --ograniczenia | Ograniczenia umieszczania jako ciąg. Ograniczenia umieszczania są wyrażeniami logicznymi we właściwościach węzła i umożliwiają ograniczenie usługi do określonych węzłów na podstawie wymagań dotyczących usługi. Na przykład, aby umieścić usługę w węzłach, w\: których NodeType jest niebieski, określ następujące "NodeColor == blue". |
| --skorelowana usługa | Nazwa usługi docelowej do skorelowania z. |
| --korelacja | Skorelować usługę z istniejącą usługą przy użyciu koligacji wyrównania. |
| --liczba wystąpień | Liczba wystąpień. Dotyczy to tylko usług bezstanowych. |
| --load-metryki | Zakodowana w UJson lista metryk używanych podczas równoważenia obciążenia między węzłami. |
| --min-replica-set-size | Minimalny rozmiar zestawu replik jako liczba. Dotyczy to tylko usług stanowych. |
| --move-koszt | Określa koszt przeniesienia usługi. Możliwe wartości\: to "Zero", "Niski", "Średni", "Wysoki", "Bardzo wysoki". |
| --placement-policy-list | Zakodowana w Usłudze lista zasad umieszczania usługi i wszelkich skojarzonych nazw domen. Zasady mogą być jednym\: `NonPartiallyPlaceService` `PreferPrimaryDomain`lub `RequireDomain` `RequireDomainDistribution`kilkoma , , , . |
| --kworum-strata-czekać | Maksymalny czas trwania w sekundach, dla którego partycja może znajdować się w stanie utraty kworum. Dotyczy to tylko usług stanowych. |
| --replica-restart-wait | Czas trwania w sekundach między po upływie repliki a utworzeniem nowej repliki. Dotyczy to tylko usług stanowych. |
| --skalowanie-zasady | Lista zasad skalowania dla tej usługi zakodowana przez json. |
| --service-placement-time | Czas trwania, dla którego repliki mogą pozostać InBuild przed zgłoszeniem, że kompilacja jest zablokowany. Dotyczy to tylko usług stanowych. |
| --stand-by-replica-keep | Maksymalny czas trwania w sekundach, dla którego repliki w trybie gotowości zostaną zachowane przed usunięciem. Dotyczy to tylko usług stanowych. |
| --stateful | Wskazuje, że usługa docelowa jest usługą stanową. |
| --bezstanowy | Wskazuje, że usługa docelowa jest usługą bezstanową. |
| --target-replica-set-size | Rozmiar zestawu replik docelowych jako liczba. Dotyczy to tylko usług stanowych. |
| --timeout -t | Wartość\: domyślna 60. |

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

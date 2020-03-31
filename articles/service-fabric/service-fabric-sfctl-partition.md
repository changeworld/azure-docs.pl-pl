---
title: Partycja interfejsu WIERSZA POLECENIA sieci szkieletowej usługi Azure
description: Dowiedz się więcej o sfctl, interfejsie wiersza polecenia sieci szkieletowej usługi Azure. Zawiera listę poleceń do zarządzania partycjami dla usługi.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: c038ef3266a727bf6984a5bd88ca540a589380db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905838"
---
# <a name="sfctl-partition"></a>sfctl partition
Kwerenda i zarządzanie partycjami dla dowolnej usługi.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| utrata danych | Ten interfejs API spowoduje utratę danych dla określonej partycji. |
| status utraty danych | Pobiera postęp operacji utraty danych partycji rozpoczęte przy użyciu interfejsu API StartDataLoss. |
| Zdrowia | Pobiera kondycję określonej partycji sieci szkieletowej usług. |
| informacje o | Pobiera informacje o partycji sieci szkieletowej usług. |
| list | Pobiera listę partycji usługi sieci szkieletowej usług. |
| ładowanie | Pobiera informacje o obciążeniu określonej partycji sieci szkieletowej usług. |
| resetowanie obciążenia | Resetuje bieżące obciążenie partycji sieci szkieletowej usług. |
| strata kworum | Wywołuje utratę kworum dla danej partycji usługi stanowej. |
| stan kworum-strata | Pobiera postęp operacji utraty kworum na partycji uruchomionej przy użyciu interfejsu API StartQuorumLoss. |
| odzyskiwanie | Wskazuje klastrowi sieci szkieletowej usług, że należy podjąć próbę odzyskania określonej partycji, która jest obecnie zablokowana w utracie kworum. |
| recover-all | Wskazuje klastrowi sieci szkieletowej usług, że należy podjąć próbę odzyskania wszelkich usług (w tym usług systemowych), które obecnie utknęły w utracie kworum. |
| raport-zdrowie | Wysyła raport kondycji na partycji sieci szkieletowej usług. |
| restart | Ten interfejs API uruchomi ponownie niektóre lub wszystkie repliki lub wystąpienia określonej partycji. |
| stan ponownego uruchomienia | Pobiera postęp operacji PartitionRestart rozpoczęto przy użyciu StartPartitionRestart. |
| nazwa svc | Pobiera nazwę usługi sieci szkieletowej usług dla partycji. |

## <a name="sfctl-partition-data-loss"></a>sfctl utrata danych partycji
Ten interfejs API spowoduje utratę danych dla określonej partycji.

Spowoduje to wywołanie interfejsu API OnDataLossAsync partycji.  Ten interfejs API spowoduje utratę danych dla określonej partycji. Spowoduje to wywołanie interfejsu API OnDataLoss partycji. Rzeczywista utrata danych będzie zależeć od określonego DataLossMode.
- PartialDataLoss: Usuwane są tylko kworum replik, a ondataloss jest wyzwalany dla partycji, ale rzeczywista utrata danych zależy od obecności replikacji w locie.  
- FullDataLoss: Wszystkie repliki są usuwane, dlatego wszystkie dane są tracone i OnDataLoss jest wyzwalany. Ten interfejs API powinien być wywoływany tylko z usługą stanową jako miejsce docelowe. Wywołanie tego interfejsu API z usługą systemową jako obiekt docelowy nie jest zalecane.

> [!NOTE]   
> Po wywołaniu tego interfejsu API nie można go odwrócić. Wywołanie CancelOperation zatrzyma tylko wykonanie i oczyścić wewnętrzny stan systemu. Nie przywróci danych, jeśli polecenie postępuje wystarczająco daleko, aby spowodować utratę danych. Wywołanie GetDataLossProgress API z tym samym OperationId do zwrócenia informacji o operacji rozpoczętej z tego interfejsu API.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --data-loss-mode [Wymagane] | Ten wyliczenie jest przekazywany do interfejsu API StartDataLoss, aby wskazać, jaki typ utraty danych do wywołania. |
| --operation-id [Wymagane] | Identyfikator GUID identyfikujący wywołanie tego interfejsu API.  Jest to przekazywane do odpowiedniego interfejsu API GetProgress. |
| --partition-id [Wymagane] | Tożsamość partycji. |
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

## <a name="sfctl-partition-data-loss-status"></a>sfctl partycji utraty-status
Pobiera postęp operacji utraty danych partycji rozpoczęte przy użyciu interfejsu API StartDataLoss.

Pobiera postęp operacji utraty danych rozpoczęte z StartDataLoss, przy użyciu OperationId.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --operation-id [Wymagane] | Identyfikator GUID identyfikujący wywołanie tego interfejsu API.  Jest to przekazywane do odpowiedniego interfejsu API GetProgress. |
| --partition-id [Wymagane] | Tożsamość partycji. |
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

## <a name="sfctl-partition-health"></a>sfctl kondycja partycji
Pobiera kondycję określonej partycji sieci szkieletowej usług.

Za pomocą EventsHealthStateFilter do filtrowania kolekcji zdarzeń kondycji zgłoszonych w usłudze na podstawie stanu kondycji. Użyj ReplicasHealthStateFilter do filtrowania kolekcji ReplicaHealthState obiektów na partycji. Jeśli określisz partycję, która nie istnieje w magazynie kondycji, to żądanie zwraca błąd.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --partition-id [Wymagane] | Tożsamość partycji. |
| --events-health-state-filter | Umożliwia filtrowanie kolekcji HealthEvent obiektów zwracanych na podstawie stanu kondycji. Możliwe wartości dla tego parametru obejmują wartość całkowitą jednego z następujących stanów kondycji. Zwracane są tylko zdarzenia, które pasują do filtru. Wszystkie zdarzenia są używane do oceny stanu kondycji zagregowane. Jeśli nie zostanie określony, wszystkie wpisy są zwracane. Wartości stanu są wyliczenia oparte na flagi, więc wartość może być kombinacją tych wartości, uzyskane przy użyciu bitowego "OR" operatora. Na przykład Jeśli podana wartość wynosi 6, zwracane są wszystkie zdarzenia o wartości HealthState OK (2) i Warning (4).  <br> - Domyślna — wartość domyślna. Pasuje do dowolnego HealthState. Wartość wynosi zero.  <br> - Brak — filtr, który nie pasuje do żadnej wartości HealthState. Używane w celu zwrócenia żadnych wyników w danej kolekcji stanów. Wartość wynosi 1.  <br> - Ok - Filtr, który pasuje do danych wejściowych z HealthState wartość Ok. Wartość wynosi 2.  <br> - Ostrzeżenie — filtr, który pasuje do danych wejściowych z HealthState wartość Ostrzeżenie. Wartość wynosi 4.  <br> - Błąd — filtr, który pasuje do danych wejściowych z HealthState wartość Error. Wartość wynosi 8.  <br> - Wszystkie — filtr, który pasuje do danych wejściowych z dowolnej wartości HealthState. Wartość to 65535. |
| --exclude-health-statistics | Wskazuje, czy statystyki kondycji powinny być zwracane jako część wyniku kwerendy. False domyślnie. Statystyki pokazują liczbę jednostek podrzędnych w stanie kondycji Ok, Ostrzeżenie i Błąd. |
| --replicas-health-state-filter | Umożliwia filtrowanie kolekcji ReplicaHealthState obiektów na partycji. Wartość można uzyskać z elementów członkowskich lub operacji bitowych na członków HealthStateFilter. Zwracane będą tylko repliki, które pasują do filtru. Wszystkie repliki będą używane do oceny stanu kondycji zagregowane. Jeśli nie zostanie określony, wszystkie wpisy zostaną zwrócone. Wartości stanu są wyliczenia oparte na flagi, więc wartość może być kombinacją tych wartości uzyskanych za pomocą bitowego operatora "OR". Na przykład Jeśli podana wartość wynosi 6, zostaną zwrócone wszystkie zdarzenia o wartości HealthState OK (2) i Warning (4). Możliwe wartości dla tego parametru obejmują wartość całkowitą jednego z następujących stanów kondycji.  <br> - Domyślna — wartość domyślna. Pasuje do dowolnego HealthState. Wartość wynosi zero.  <br> - Brak — filtr, który nie pasuje do żadnej wartości HealthState. Używane w celu zwrócenia żadnych wyników w danej kolekcji stanów. Wartość wynosi 1.  <br> - Ok - Filtr, który pasuje do danych wejściowych z HealthState wartość Ok. Wartość wynosi 2.  <br> - Ostrzeżenie — filtr, który pasuje do danych wejściowych z HealthState wartość Ostrzeżenie. Wartość wynosi 4.  <br> - Błąd — filtr, który pasuje do danych wejściowych z HealthState wartość Error. Wartość wynosi 8.  <br> - Wszystkie — filtr, który pasuje do danych wejściowych z dowolnej wartości HealthState. Wartość to 65535. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-partition-info"></a>informacje o partycji sfctl
Pobiera informacje o partycji sieci szkieletowej usług.

Pobiera informacje o określonej partycji. Odpowiedź zawiera identyfikator partycji, informacje o schemacie partycjonowania, klucze obsługiwane przez partycję, stan, kondycję i inne szczegóły dotyczące partycji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --partition-id [Wymagane] | Tożsamość partycji. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-partition-list"></a>lista partycji sfctl
Pobiera listę partycji usługi sieci szkieletowej usług.

Odpowiedź zawiera identyfikator partycji, informacje o schemacie partycjonowania, klucze obsługiwane przez partycję, stan, kondycję i inne szczegóły dotyczące partycji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --service-id [Wymagane] | Tożsamość usługi. Ten identyfikator jest zazwyczaj pełną nazwą usługi bez\:schematu URI "fabric". Począwszy od wersji 6.0, nazwy hierarchiczne\~są rozdzielane znakiem " ". Na przykład jeśli nazwa usługi\:to "fabric /myapp/app1/svc1", tożsamość\~usługi\~będzie "myapp app1 svc1" w 6.0+ i "myapp/app1/svc1" w poprzednich wersjach. |
| --continuation-token | Parametr tokenu kontynuacji jest używany do uzyskania następnego zestawu wyników. Token kontynuacji z wartością niepustą jest uwzględniony w odpowiedzi interfejsu API, gdy wyniki z systemu nie mieszczą się w pojedynczej odpowiedzi. Gdy ta wartość jest przekazywana do następnego wywołania interfejsu API, interfejs API zwraca następny zestaw wyników. Jeśli nie ma żadnych dalszych wyników, token kontynuacji nie zawiera wartości. Wartość tego parametru nie powinna być zakodowana w adresie URL. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-partition-load"></a>obciążenie partycji sfctl
Pobiera informacje o obciążeniu określonej partycji sieci szkieletowej usług.

Zwraca informacje o obciążeniu określonej partycji. Odpowiedź zawiera listę raportów ładowania dla partycji sieci szkieletowej usług. Każdy raport zawiera nazwę metryki obciążenia, wartość i ostatni raportowane czas w czasie UTC.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --partition-id [Wymagane] | Tożsamość partycji. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-partition-load-reset"></a>sfctl partycji load-reset
Resetuje bieżące obciążenie partycji sieci szkieletowej usług.

Resetuje bieżące obciążenie partycji sieci szkieletowej usług do domyślnego obciążenia usługi.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --partition-id [Wymagane] | Tożsamość partycji. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-partition-quorum-loss"></a>sfctl partycji kworum-strata
Wywołuje utratę kworum dla danej partycji usługi stanowej.

Ten interfejs API jest przydatny w przypadku tymczasowej utraty kworum w usłudze. Wywołanie Interfejsu API GetQuorumLossProgress z tym samym OperationId, aby zwrócić informacje o operacji rozpoczętej z tym interfejsem API. To może być wywołane tylko na usługi utrwalone stanu (HasPersistedState==true) usługi.  Nie należy używać tego interfejsu API w usługach bezstanowych lub usług tylko w pamięci stanowej.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --operation-id [Wymagane] | Identyfikator GUID identyfikujący wywołanie tego interfejsu API.  Jest to przekazywane do odpowiedniego interfejsu API GetProgress. |
| --partition-id [Wymagane] | Tożsamość partycji. |
| --czas trwania strat kworum [Wymagane] | Czas, dla którego partycja będzie przechowywana w utracie kworum.  Należy to określić w sekundach. |
| --kworum-loss-mode [Wymagane] | Ten wyliczenie jest przekazywany do interfejsu API StartQuorumLoss, aby wskazać, jaki typ utraty kworum wywołać. |
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

## <a name="sfctl-partition-quorum-loss-status"></a>sfctl partycji kworum-strata-status
Pobiera postęp operacji utraty kworum na partycji uruchomionej przy użyciu interfejsu API StartQuorumLoss.

Pobiera postęp operacji utraty kworum rozpoczęte z StartQuorumLoss, przy użyciu pod warunkiem OperationId.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --operation-id [Wymagane] | Identyfikator GUID identyfikujący wywołanie tego interfejsu API.  Jest to przekazywane do odpowiedniego interfejsu API GetProgress. |
| --partition-id [Wymagane] | Tożsamość partycji. |
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

## <a name="sfctl-partition-recover"></a>odzyskiwanie partycji sfctl
Wskazuje klastrowi sieci szkieletowej usług, że należy podjąć próbę odzyskania określonej partycji, która jest obecnie zablokowana w utracie kworum.

Ta operacja powinna być wykonywana tylko wtedy, gdy wiadomo, że repliki, które są w dół nie można odzyskać. Nieprawidłowe użycie tego interfejsu API może spowodować potencjalną utratę danych.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --partition-id [Wymagane] | Tożsamość partycji. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-partition-recover-all"></a>sfctl partycji recover-all
Wskazuje klastrowi sieci szkieletowej usług, że należy podjąć próbę odzyskania wszelkich usług (w tym usług systemowych), które obecnie utknęły w utracie kworum.

Ta operacja powinna być wykonywana tylko wtedy, gdy wiadomo, że repliki, które są w dół nie można odzyskać. Nieprawidłowe użycie tego interfejsu API może spowodować potencjalną utratę danych.

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

## <a name="sfctl-partition-report-health"></a>sfctl partition report-health
Wysyła raport kondycji na partycji sieci szkieletowej usług.

Raportuje stan kondycji określonej partycji sieci szkieletowej usług. Raport musi zawierać informacje o źródle raportu kondycji i właściwości, na których jest raport. Raport jest wysyłany do partycji bramy sieci szkieletowej usług, która jest przesyłana dalej do magazynu kondycji. Raport może zostać zaakceptowany przez bramę, ale odrzucony przez magazyn kondycji po dodatkowej weryfikacji. Na przykład magazyn kondycji może odrzucić raport z powodu nieprawidłowego parametru, takiego jak nieświeży numer sekwencyjny. Aby sprawdzić, czy raport został zastosowany w magazynie kondycji, sprawdź, czy raport jest wyświetlany w sekcji zdarzenia.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --health-property [Wymagane] | Właściwość informacji o kondycji. <br><br> Jednostka może mieć raporty kondycji dla różnych właściwości. Właściwość jest ciągiem, a nie stałe wyliczenie, aby umożliwić elastyczność reporter kategoryzować warunek stanu, który wyzwala raport. Na przykład reporter z SourceId "LocalWatchdog" może monitorować stan dostępnego dysku w węźle, dzięki czemu może zgłosić właściwość "AvailableDisk" w tym węźle. Ten sam reporter może monitorować łączność węzła, dzięki czemu może zgłaszać właściwość "Łączność" w tym samym węźle. W magazynie kondycji te raporty są traktowane jako oddzielne zdarzenia kondycji dla określonego węzła. Wraz z SourceId właściwość jednoznacznie identyfikuje informacje o kondycji. |
| --health-state [Wymagane] | Możliwe wartości\: to "Invalid", "Ok", "Warning", "Error", "Unknown". |
| --partition-id [Wymagane] | Tożsamość partycji. |
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

## <a name="sfctl-partition-restart"></a>Ponowne uruchomienie partycji sfctl
Ten interfejs API uruchomi ponownie niektóre lub wszystkie repliki lub wystąpienia określonej partycji.

Ten interfejs API jest przydatny do testowania pracy awaryjnej. Jeśli jest używany do docelowej partycji usługi bezstanowej, RestartPartitionMode musi być AllReplicasOrInstances. Wywołanie GetPartitionRestartProgress API przy użyciu tego samego OperationId, aby uzyskać postęp.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --operation-id [Wymagane] | Identyfikator GUID identyfikujący wywołanie tego interfejsu API.  Jest to przekazywane do odpowiedniego interfejsu API GetProgress. |
| --partition-id [Wymagane] | Tożsamość partycji. |
| --restart-partition-mode [Wymagane] | Opisz partycje, które mają zostać ponownie uruchomione. |
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

## <a name="sfctl-partition-restart-status"></a>stan ponownego uruchomienia partycji sfctl
Pobiera postęp operacji PartitionRestart rozpoczęto przy użyciu StartPartitionRestart.

Pobiera postęp PartitionRestart rozpoczęty z StartPartitionRestart przy użyciu pod warunkiem OperationId.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --operation-id [Wymagane] | Identyfikator GUID identyfikujący wywołanie tego interfejsu API.  Jest to przekazywane do odpowiedniego interfejsu API GetProgress. |
| --partition-id [Wymagane] | Tożsamość partycji. |
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

## <a name="sfctl-partition-svc-name"></a>sfctl partycji svc-name
Pobiera nazwę usługi sieci szkieletowej usług dla partycji.

Pobiera nazwę usługi dla określonej partycji. Błąd 404 jest zwracany, jeśli identyfikator partycji nie istnieje w klastrze.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --partition-id [Wymagane] | Tożsamość partycji. |
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

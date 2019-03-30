---
title: Azure usługi Service Fabric interfejsu wiersza polecenia sfctl partycji | Dokumentacja firmy Microsoft
description: Zawiera opis poleceń interfejsu wiersza polecenia usługi Service Fabric sfctl partycji.
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
ms.openlocfilehash: f7c9bcc51757100cb1fc957dee12213bc8bf2eec
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58666770"
---
# <a name="sfctl-partition"></a>sfctl partition
Zapytanie partycji i zarządzania nimi dla dowolnej usługi.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| utrata danych | Ten interfejs API będzie wywoływać utrata danych dla określonej partycji. |
| data-loss-status | Pobiera postęp pracy przy użyciu interfejsu API StartDataLoss operacji utraty danych partycji. |
| zdrowie | Pobiera kondycji określonej partycji usługi Service Fabric. |
| informacje | Pobiera informacje o partycji usługi Service Fabric. |
| list | Pobiera listę partycji usługi Service Fabric. |
| ładowanie | Pobiera informacje o ładowaniu określonej partycji usługi Service Fabric. |
| load-reset | Resetuje bieżącego obciążenia partycji usługi Service Fabric. |
| quorum-loss | Wywołuje utraciła kworum dla partycji danej usługi stanowej. |
| quorum-loss-status | Pobiera postępu operacji utratę kworum na pracę, przy użyciu interfejsu API StartQuorumLoss partycji. |
| Odzyskiwanie | Wskazuje do klastra usługi Service Fabric, że ma podejmować do odzyskania z określonej partycji, który jest obecnie zablokowany utraciła kworum. |
| recover-all | Wskazuje, aby klaster usługi Service Fabric, należy spróbować odzyskać wszystkie usługi (w tym usługi systemowe), które są aktualnie zablokowane utraciła kworum. |
| report-health | Wysyła raport o kondycji na partycji usługi Service Fabric. |
| restart | Ten interfejs API zostanie uruchomiony ponownie, niektórych lub wszystkich replik lub wystąpień określonej partycji. |
| stan ponownego uruchomienia | Pobiera postępu operacji PartitionRestart wprowadzenie przy użyciu StartPartitionRestart. |
| svc-name | Pobiera nazwę usługi Service Fabric dla partycji. |

## <a name="sfctl-partition-data-loss"></a>Interfejs sfctl partycji z utratą danych
Ten interfejs API będzie wywoływać utrata danych dla określonej partycji.

Wywoła wywołanie interfejsu API OnDataLossAsync partycji.  Ten interfejs API będzie wywoływać utrata danych dla określonej partycji. Wywoła wywołanie interfejsu API OnDataLoss partycji. Utrata danych rzeczywistych będzie zależeć od określonej tryb DataLossMode.  <br> -PartialDataLoss — tylko kworum replik są usuwane i OnDataLoss zostanie wywołany dla partycji, ale utraty rzeczywistych danych zależy od obecności śledząc replikacji.  <br> -FullDataLoss — wszystkie repliki są usuwane. Dlatego wszystkie dane zostaną utracone i OnDataLoss zostanie wywołany. Ten interfejs API powinna być wywoływana z usługą stanową jako element docelowy. Wywołanie tego interfejsu API z usługą system jako element docelowy nie jest zalecane.

> [!NOTE] 
> Po wywołaniu tego interfejsu API nie można cofnąć. Wywoływanie CancelOperation tylko zatrzymać wykonywanie i wyczyścić stanu systemu wewnętrznego. Go nie spowoduje przywrócenia danych Jeśli polecenie zanotowano daleko, aby spowodować utratę danych. Wywołaj interfejs API GetDataLossProgress z tym samym identyfikatorem OperationId do zwracania informacji o nieudanej operacji pracę z tym interfejsem API.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --dane utraty trybu [wymagane] | To wyliczenie jest przekazywany do interfejsu API StartDataLoss, aby wskazać, jakiego rodzaju utraty danych, aby wywołać. |
| — Identyfikator operacji [wymagane] | Identyfikator GUID, który identyfikuje wywołanie tego interfejsu API.  Te informacje są przekazywane do odpowiedniego interfejsu API GetProgress. |
| — Identyfikator partycji [wymagane] | Tożsamość partycji. |
| --service-id     [Required] | Tożsamość usługi. Ten identyfikator jest zazwyczaj pełną nazwę usługi bez "Service fabric\:" schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchiczne nazwy są oddzielane za pomocą znaku "\~" znaków. Na przykład, jeśli nazwa usługi jest "Service fabric\:/myapp/app1/svc1", będzie tożsamości usługi "myapp\~app1\~svc1" w wersji 6.0 + i "myapp/app1/svc1" w poprzednich wersjach. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-partition-data-loss-status"></a>Interfejs sfctl partycji danych utraty status
Pobiera postęp pracy przy użyciu interfejsu API StartDataLoss operacji utraty danych partycji.

Pobiera postęp operacji utraty danych, pracy z usługą StartDataLoss, za pomocą OperationId.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator operacji [wymagane] | Identyfikator GUID, który identyfikuje wywołanie tego interfejsu API.  Te informacje są przekazywane do odpowiedniego interfejsu API GetProgress. |
| — Identyfikator partycji [wymagane] | Tożsamość partycji. |
| --service-id   [Required] | Tożsamość usługi. Ten identyfikator jest zazwyczaj pełną nazwę usługi bez "Service fabric\:" schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchiczne nazwy są oddzielane za pomocą znaku "\~" znaków. Na przykład, jeśli nazwa usługi jest "Service fabric\:/myapp/app1/svc1", będzie tożsamości usługi "myapp\~app1\~svc1" w wersji 6.0 + i "myapp/app1/svc1" w poprzednich wersjach. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-partition-health"></a>Interfejs sfctl partition health
Pobiera kondycji określonej partycji usługi Service Fabric.

Zgłoszone EventsHealthStateFilter użycia do filtrowania kolekcji zdarzeń dotyczących kondycji usługi oparte na stanie kondycji. Użyj ReplicasHealthStateFilter do filtrowania kolekcji obiektów ReplicaHealthState na partycji. Jeśli określisz partycji, który nie istnieje w magazynie kondycji, to żądanie zwraca błąd.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator partycji [wymagane] | Tożsamość partycji. |
| --events-health-state-filter | Umożliwia filtrowanie kolekcji zwracanych obiektów HealthEvent oparte na stanie kondycji. Możliwe wartości dla tego parametru to wartość całkowitą, jednego z następujących stanów kondycji. Zwracane są tylko te zdarzenia, które są zgodne z filtrem. Wszystkie zdarzenia są używane do oceny stanu kondycji zagregowane. Jeśli nie zostanie określony, zwracane są wszystkie wpisy. Wartości stanu są wyliczanie oparte na flagi, dzięki czemu może to być kombinacją tych wartości uzyskanych przy użyciu bitowego operatora "Lub". Na przykład jeśli podana wartość jest 6 następnie wszystkie zdarzenia z wartością atrybutu HealthState OK (2) i ostrzeżenia (4) są zwracane.  <br> -Domyślnie — wartość domyślną. Pasuje do dowolnego atrybutu HealthState. Ta wartość wynosi zero.  <br> -Brak — filtr, który nie jest zgodny z dowolną wartością atrybutu HealthState. Używany, aby zwracała żadnych wyników w danej kolekcji stanów. Wartość to 1.  <br> -Ok — filtrowanie, że dopasowania danych wejściowych z wartością atrybutu HealthState Ok. Wartość jest równa 2.  <br> -Warning - filtru, że wprowadzanie dopasowania z atrybutem HealthState wartość ostrzeżenie. Wartość wynosi 4.  <br> -Błąd — filtr, który pasuje do danych wejściowych z wartością atrybutu HealthState błędu. Wartość jest 8.  <br> -Al - filtr, który pasuje do danych wejściowych z dowolną wartością atrybutu HealthState. Wartość jest 65535. |
| --exclude-health-statistics | Wskazuje, czy statystyki kondycji ma zostać zwrócone w wyniku zapytania. Wartość false, domyślnie. Statystyki pokazują liczbę elementów podrzędnych jednostek w kondycja Ok, ostrzeżenia i błędu. |
| --replicas-health-state-filter | Umożliwia filtrowanie kolekcji obiektów ReplicaHealthState na partycji. Wartość można uzyskać z elementów członkowskich lub bitowego operacje na elementach członkowskich HealthStateFilter. Zostaną zwrócone tylko tych replik, które są zgodne z filtrem. Wszystkie repliki będzie służyć do oceny stanu kondycji zagregowane. Jeśli nie zostanie określony, zostaną zwrócone wszystkie wpisy. Wartości stanu są wyliczanie oparte na flagi, dzięki czemu może to być kombinacją tych wartości uzyskanych przy użyciu bitowego operatora "Lub". Na przykład jeśli podana wartość jest 6 wtedy wszystkie zdarzenia z wartością atrybutu HealthState OK (2) i ostrzeżenia (4) zostanie zwrócony. Możliwe wartości dla tego parametru to wartość całkowitą, jednego z następujących stanów kondycji.  <br> -Domyślnie — wartość domyślną. Pasuje do dowolnego atrybutu HealthState. Ta wartość wynosi zero.  <br> -Brak — filtr, który nie jest zgodny z dowolną wartością atrybutu HealthState. Używany, aby zwracała żadnych wyników w danej kolekcji stanów. Wartość to 1.  <br> -Ok — filtrowanie, że dopasowania danych wejściowych z wartością atrybutu HealthState Ok. Wartość jest równa 2.  <br> -Warning - filtru, że wprowadzanie dopasowania z atrybutem HealthState wartość ostrzeżenie. Wartość wynosi 4.  <br> -Błąd — filtr, który pasuje do danych wejściowych z wartością atrybutu HealthState błędu. Wartość jest 8.  <br> -Al - filtr, który pasuje do danych wejściowych z dowolną wartością atrybutu HealthState. Wartość jest 65535. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-partition-info"></a>informacje o partycji sfctl
Pobiera informacje o partycji usługi Service Fabric.

Pobiera informacje o określonej partycji. Odpowiedź zawiera identyfikator partycji: informacje o schemat partycjonowania, klucze obsługiwane przez partycji, stan, kondycję i inne szczegółowe informacje o partycji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator partycji [wymagane] | Tożsamość partycji. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-partition-list"></a>Lista partycji sfctl
Pobiera listę partycji usługi Service Fabric.

Odpowiedź zawiera identyfikator partycji: informacje o schemat partycjonowania, klucze obsługiwane przez partycji, stan, kondycję i inne szczegółowe informacje o partycji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator usługi [wymagane] | Tożsamość usługi. Ten identyfikator jest zazwyczaj pełną nazwę usługi bez "Service fabric\:" schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchiczne nazwy są oddzielane za pomocą znaku "\~" znaków. Na przykład, jeśli nazwa usługi jest "Service fabric\:/myapp/app1/svc1", będzie tożsamości usługi "myapp\~app1\~svc1" w wersji 6.0 + i "myapp/app1/svc1" w poprzednich wersjach. |
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

## <a name="sfctl-partition-load"></a>Interfejs sfctl obciążenia partycji
Pobiera informacje o ładowaniu określonej partycji usługi Service Fabric.

Zwraca informacje dotyczące obciążenia określonej partycji. Odpowiedź zawiera listę raportów obciążenia dla partycji usługi Service Fabric. Każdy raport zawiera nazwę metryki obciążenia, wartość i czas ostatniego zgłoszone w formacie UTC.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator partycji [wymagane] | Tożsamość partycji. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-partition-load-reset"></a>Interfejs sfctl partycji resetowania obciążenia
Resetuje bieżącego obciążenia partycji usługi Service Fabric.

Resetuje bieżącego obciążenia partycji usługi Service Fabric można załadować domyślnego dla usługi.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator partycji [wymagane] | Tożsamość partycji. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-partition-quorum-loss"></a>Interfejs sfctl partycji z utraciła kworum
Wywołuje utraciła kworum dla partycji danej usługi stanowej.

Ten interfejs API jest przydatne w sytuacji utratę kworum tymczasowe, w usłudze. Wywołaj interfejs API GetQuorumLossProgress z tym samym identyfikatorem OperationId do zwracania informacji o nieudanej operacji pracę z tym interfejsem API. To lze volat pouze na stanowe utrwalone (HasPersistedState == true) usługi.  Nie należy używać tego interfejsu API usług bezstanowych lub stanowych usługi tylko w pamięci.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator operacji [wymagane] | Identyfikator GUID, który identyfikuje wywołanie tego interfejsu API.  Te informacje są przekazywane do odpowiedniego interfejsu API GetProgress. |
| — Identyfikator partycji [wymagane] | Tożsamość partycji. |
| --kworum utraty — czas trwania [wymagane] | Ilość czasu, dla której będą przechowywane partycja utraciła kworum.  To jest wymagane w ciągu kilku sekund. |
| --kworum utraty trybu [wymagane] | To wyliczenie jest przekazywany do interfejsu API StartQuorumLoss, aby wskazać typ utraciła kworum do wywołania. |
| --service-id           [Required] | Tożsamość usługi. Ten identyfikator jest zazwyczaj pełną nazwę usługi bez "Service fabric\:" schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchiczne nazwy są oddzielane za pomocą znaku "\~" znaków. Na przykład, jeśli nazwa usługi jest "Service fabric\:/myapp/app1/svc1", będzie tożsamości usługi "myapp\~app1\~svc1" w wersji 6.0 + i "myapp/app1/svc1" w poprzednich wersjach. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-partition-quorum-loss-status"></a>Interfejs sfctl partycji kworum utraty status
Pobiera postępu operacji utratę kworum na pracę, przy użyciu interfejsu API StartQuorumLoss partycji.

Pobiera postęp operacji utratę kworum, wprowadzenie StartQuorumLoss, przy użyciu podanego OperationId.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator operacji [wymagane] | Identyfikator GUID, który identyfikuje wywołanie tego interfejsu API.  Te informacje są przekazywane do odpowiedniego interfejsu API GetProgress. |
| — Identyfikator partycji [wymagane] | Tożsamość partycji. |
| --service-id   [Required] | Tożsamość usługi. Ten identyfikator jest zazwyczaj pełną nazwę usługi bez "Service fabric\:" schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchiczne nazwy są oddzielane za pomocą znaku "\~" znaków. Na przykład, jeśli nazwa usługi jest "Service fabric\:/myapp/app1/svc1", będzie tożsamości usługi "myapp\~app1\~svc1" w wersji 6.0 + i "myapp/app1/svc1" w poprzednich wersjach. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-partition-recover"></a>Odzyskaj partycji sfctl
Wskazuje do klastra usługi Service Fabric, że ma podejmować do odzyskania z określonej partycji, który jest obecnie zablokowany utraciła kworum.

Ta operacja powinna można wykonać tylko, jeśli jest znany, nie będzie można odzyskać replik, które nie działają. Niepoprawne użycie tego interfejsu API może spowodować ryzyko utraty danych.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator partycji [wymagane] | Tożsamość partycji. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-partition-recover-all"></a>Interfejs sfctl partycji recover-all
Wskazuje, aby klaster usługi Service Fabric, należy spróbować odzyskać wszystkie usługi (w tym usługi systemowe), które są aktualnie zablokowane utraciła kworum.

Ta operacja powinna można wykonać tylko, jeśli jest znany, nie będzie można odzyskać replik, które nie działają. Niepoprawne użycie tego interfejsu API może spowodować ryzyko utraty danych.

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

## <a name="sfctl-partition-report-health"></a>Interfejs sfctl partycji raportów kondycji
Wysyła raport o kondycji na partycji usługi Service Fabric.

Raporty stan kondycji określonej partycji usługi Service Fabric. Raport musi zawierać informacje o źródle raport o kondycji i właściwości, na którym jest zgłaszany. Raport jest wysyłany do bramy usługi Service Fabric partycję, która przekazuje w magazynie kondycji. Raport może być akceptowane przez bramę, ale odrzucony przez magazynu kondycji po dodatkową walidację. Na przykład magazynu kondycji może odrzucić raport ze względu na nieprawidłowy parametr, takich jak numer sekwencji starych. Aby sprawdzić, czy raport został zastosowany w magazynie kondycji, sprawdź, czy raport jest wyświetlany w sekcji zdarzenia.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --kondycji — właściwość [wymagane] | Właściwość o kondycji. <br><br> Jednostka może mieć raportów o kondycji dla różnych właściwości. Właściwość jest ciągu i stałych wyliczenia aby zezwalał na elastyczność reportera do kategoryzowania warunek stanu, która powoduje uruchomienie raportu. Na przykład reportera o ID "LocalWatchdog" można monitorować stan wolnego w węźle, aby go zgłosić właściwości "AvailableDisk" w tym węźle. Ten sam reportera monitorować łączność węzeł tak go zgłosić właściwości "Łączność" w tym samym węźle. W magazynie kondycji te raporty są traktowane jako zdarzenia dotyczące kondycji oddzielnych dla określonego węzła. Wraz z SourceId właściwość jednoznacznie identyfikuje informacje o kondycji. |
| --health-state    [Required] | Możliwe wartości to\: "Nieprawidłowy", "Ok", "Ostrzeżenie", "Error", "Nieznany". |
| — Identyfikator partycji [wymagane] | Tożsamość partycji. |
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

## <a name="sfctl-partition-restart"></a>ponowne uruchomienie partycji sfctl
Ten interfejs API zostanie uruchomiony ponownie, niektórych lub wszystkich replik lub wystąpień określonej partycji.

Ten interfejs API jest przydatne w przypadku testowania trybu failover. Jeśli używane pod kątem partycji usługę bezstanową, tryb RestartPartitionMode musi być AllReplicasOrInstances. Wywoływanie interfejsu API GetPartitionRestartProgress postępu przy użyciu taki sam identyfikator OperationId.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator operacji [wymagane] | Identyfikator GUID, który identyfikuje wywołanie tego interfejsu API.  Te informacje są przekazywane do odpowiedniego interfejsu API GetProgress. |
| — Identyfikator partycji [wymagane] | Tożsamość partycji. |
| — ponowne uruchamianie partycji trybu [wymagane] | Opis partycji do ponownego uruchomienia. |
| --service-id             [Required] | Tożsamość usługi. Ten identyfikator jest zazwyczaj pełną nazwę usługi bez "Service fabric\:" schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchiczne nazwy są oddzielane za pomocą znaku "\~" znaków. Na przykład, jeśli nazwa usługi jest "Service fabric\:/myapp/app1/svc1", będzie tożsamości usługi "myapp\~app1\~svc1" w wersji 6.0 + i "myapp/app1/svc1" w poprzednich wersjach. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-partition-restart-status"></a>Interfejs sfctl partycji ponownego uruchomienia stanu dzierżawy
Pobiera postępu operacji PartitionRestart wprowadzenie przy użyciu StartPartitionRestart.

Pobiera postęp PartitionRestart wprowadzenie StartPartitionRestart przy użyciu podanego OperationId.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator operacji [wymagane] | Identyfikator GUID, który identyfikuje wywołanie tego interfejsu API.  Te informacje są przekazywane do odpowiedniego interfejsu API GetProgress. |
| — Identyfikator partycji [wymagane] | Tożsamość partycji. |
| --service-id   [Required] | Tożsamość usługi. Ten identyfikator jest zazwyczaj pełną nazwę usługi bez "Service fabric\:" schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchiczne nazwy są oddzielane za pomocą znaku "\~" znaków. Na przykład, jeśli nazwa usługi jest "Service fabric\:/myapp/app1/svc1", będzie tożsamości usługi "myapp\~app1\~svc1" w wersji 6.0 + i "myapp/app1/svc1" w poprzednich wersjach. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-partition-svc-name"></a>Interfejs sfctl svc nazwa partycji
Pobiera nazwę usługi Service Fabric dla partycji.

Pobiera nazwę usługi dla określonej partycji. Błąd 404 jest zwracany, jeśli identyfikator partycji: nie istnieje w klastrze.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator partycji [wymagane] | Tożsamość partycji. |
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

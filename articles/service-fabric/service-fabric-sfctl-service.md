---
title: Usługa Service Fabric interfejsu wiersza polecenia sfctl usługi platformy Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano poleceń usługi sfctl interfejsu wiersza polecenia usługi Service Fabric.
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
ms.openlocfilehash: e0454d0124efba04434884fbac9056c5e324710d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60556376"
---
# <a name="sfctl-service"></a>sfctl service
Tworzenie, usuwanie i zarządzać usługą, typy usług i pakietów usługi.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| app-name | Pobiera nazwę aplikacji usługi Service Fabric dla usługi. |
| code-package-list | Pobiera listę pakietów kod wdrożony w węźle usługi Service Fabric. |
| create | Tworzy określony usługi Service Fabric. |
| delete | Usuwa istniejącą usługę Service Fabric. |
| wdrożone typu | Pobiera informacje o określonej usługi rodzaj aplikacji wdrożonej w węźle w klastrze usługi Service Fabric. |
| wdrożony — — lista typów | Pobiera listę zawierającą informacje o typach usługi z poziomu aplikacji wdrożonych w węźle w klastrze usługi Service Fabric. |
| description | Pobiera opis istniejącej usługi Service Fabric. |
| get-container-logs | Pobiera dzienniki kontenera dla kontenera wdrażane w węźle usługi Service Fabric. |
| health | Pobiera kondycji określonej usługi Service Fabric. |
| info | Pobiera informacje o określonej usługi należące do aplikacji usługi Service Fabric. |
| list | Pobiera informacje o wszystkich usług należących do aplikacji, określonego przez identyfikator aplikacji. |
| manifest | Pobiera manifest opisujące typ usługi. |
| package-deploy | Pobiera pakiety skojarzone z określoną usługą manifestu do pamięci podręcznej obrazów w określonym węźle. |
| package-health | Pobiera informacje o kondycji usług pakietu dla określonej aplikacji wdrożone dla węzła usługi Service Fabric i aplikacji. |
| package-info | Pobiera listę pakietów usług wdrożonych w węźle usługi Service Fabric dopasowania określonej nazwy. |
| package-list | Pobiera listę pakietów usług wdrożonych w węźle usługi Service Fabric. |
| Odzyskiwanie | Klaster usługi Service Fabric wskazywać, że ma podejmować do odzyskania z określonej usługi, który jest obecnie zablokowany utraciła kworum. |
| report-health | Wysyła raport o kondycji usługi Service Fabric. |
| resolve | Rozwiąż partycji usługi Service Fabric. |
| type-list | Pobiera listę zawierającą informacje o typach usługi, które są obsługiwane przez typ aplikacji aprowizowanych w klastrze usługi Service Fabric. |
| update | Aktualizuje określonej usługi przy użyciu opisu danej aktualizacji. |

## <a name="sfctl-service-app-name"></a>Nazwa aplikacji interfejsu sfctl usługi
Pobiera nazwę aplikacji usługi Service Fabric dla usługi.

Pobiera nazwę aplikacji dla określonej usługi. Błąd 404 FABRIC_E_SERVICE_DOES_NOT_EXIST jest zwracany, jeśli usługa o identyfikatorze podanej usługi nie istnieje.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator usługi [wymagane] | Tożsamość usługi. Ten identyfikator jest zazwyczaj pełną nazwę usługi bez "Service fabric\:" schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchiczne nazwy są oddzielane za pomocą znaku "\~" znaków. Na przykład, jeśli nazwa usługi jest "Service fabric\:/myapp/app1/svc1", będzie tożsamości usługi "myapp\~app1\~svc1" w wersji 6.0 + i "myapp/app1/svc1" w poprzednich wersjach. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-service-code-package-list"></a>Interfejs sfctl usługi kod —-listy pakietów
Pobiera listę pakietów kod wdrożony w węźle usługi Service Fabric.

Pobiera listę pakietów kod wdrożony w węźle usługi Service Fabric dla danej aplikacji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator aplikacji [wymagane] | Tożsamość aplikacji. Zazwyczaj jest to pełna nazwa aplikacji bez "Service fabric\:" schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchiczne nazwy są oddzielane za pomocą znaku "\~" znaków. Na przykład, jeśli nazwa aplikacji jest "Service fabric\:/myapp/app1", tożsamość aplikacji będzie "myapp\~app1" w wersji 6.0 + i "myapp app1" w poprzednich wersjach. |
| — [wymagane] Nazwa węzła | Nazwa węzła. |
| --code-package-name | Nazwa pakietu kodu określonego w manifeście usługi zarejestrowany jako część typu aplikacji w klastrze usługi Service Fabric. |
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

## <a name="sfctl-service-create"></a>Tworzenie usługi sfctl
Tworzy określony usługi Service Fabric.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| Identyfikator aplikacji —, [wymagane] | Tożsamość aplikacji. Zazwyczaj jest to pełna nazwa aplikacji bez "Service fabric\:" schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchiczne nazwy są oddzielane za pomocą znaku "\~" znaków. Na przykład, jeśli nazwa aplikacji jest "Service fabric\:/myapp/app1", tożsamość aplikacji będzie "myapp\~app1" 6.0 + i "myapp app1" w poprzednich wersjach. |
| — Nazwa [wymagane] | Nazwa usługi. Powinna to być elementem podrzędnym identyfikator aplikacji. Jest pełną nazwę w tym `fabric\:` identyfikatora URI. Na przykład usługi `fabric\:/A/B` jest elementem podrzędnym aplikacji `fabric\:/A`. |
| — Typ usługi [wymagane] | Nazwa typu usługi. |
| --activation-mode | Tryb aktywacji pakietu usługi. |
| — ograniczenia | Ograniczenia dotyczące umieszczania jako ciąg. Ograniczeniami dotyczącymi umieszczania są wyrażeń logicznych na — właściwości węzła i umożliwiają ograniczenie usługi do określonych węzłów w oparciu o wymagania dotyczące usługi. Na przykład, aby umieścić usługi na węzłach, gdzie element NodeType to niebieski określ następujące\:"NodeColor == niebieski". |
| --correlated-service | Nazwa docelowej usługi w celu skorelowania za pomocą. |
| --correlation | Korelowanie usługi za pomocą istniejącej usługi przy użyciu koligacji wyrównania. |
| --dns-name | Nazwa DNS usługi, które ma zostać utworzony. Usługa system DNS w sieci szkieletowej usługi musi być włączona dla tego ustawienia. |
| --instance-count | Liczba wystąpień. Dotyczy to tylko w przypadku usług bezstanowych. |
| --int-scheme | Wskazuje, że usługi powinny być dzielone równomiernie różnych liczb całkowitych bez znaku. |
| --int-scheme-count | Liczba partycji wewnątrz zakres kluczy liczby całkowitej w celu utworzenia, jeśli przy użyciu schematu partycji jednolitego liczby całkowitej. |
| --int-scheme-high | Koniec zakresu kluczy liczby całkowitej, jeśli przy użyciu schematu partycji jednolitego liczby całkowitej. |
| --int-scheme-low | Początek zakresu kluczy liczby całkowitej, jeśli przy użyciu schematu partycji jednolitego liczby całkowitej. |
| --metryk obciążenia | Lista kodowany w formacie JSON metryki używany podczas równoważenia obciążenia usług między węzłami. |
| --min-replica-set-size | Minimalna repliki Ustaw rozmiar jako liczba. Dotyczy to tylko w przypadku usług stanowych. |
| --move-cost | Określa koszt przeniesienia dla usługi. Możliwe wartości to\: "Zero", "Niska", "Średnie", "Wysokiego". |
| --named-scheme | Wskazuje, że usługa powinny mieć wiele partycji o nazwie. |
| --named-scheme-list | Lista kodowany w formacie JSON nazwy partycji usługi, jeśli przy użyciu schematu partycji o nazwie. |
| --nie utrwalone — stan | W przypadku opcji true oznacza to, usługa nie ma żadnych trwały stan przechowywane na dysku lokalnym lub tylko przechowuje stan w pamięci. |
| --placement-policy-list | Zakodowane JSON listy zasady umieszczania dla usługi, wraz ze wszystkimi skojarzonymi nazw domen. Zasady mogą być co najmniej\: `NonPartiallyPlaceService`, `PreferPrimaryDomain`, `RequireDomain`, `RequireDomainDistribution`. |
| --kworum utraty wait | Maksymalny czas trwania w sekundach, dla której partycji może być w stanie utraciła kworum. Dotyczy to tylko w przypadku usług stanowych. |
| --replica-restart-wait | Czas w sekundach między Jeśli replika ulegnie awarii, a po utworzeniu nowej repliki. Dotyczy to tylko w przypadku usług stanowych. |
| --scaling-policies | JSON zakodowany listę skalowania zasad dla tej usługi. |
| --singleton-scheme | Wskazuje usługi powinien mieć jednej partycji lub być usługi niepartycjonowana. |
| --stand-by-replica-keep | Maksymalny czas trwania w sekundach, w których stan wstrzymania repliki zostanie zachowana przed usuwany. Dotyczy to tylko w przypadku usług stanowych. |
| --stateful | Wskazuje, że usługa jest usługi stanowej. |
| --stateless | Wskazuje, że usługa jest usługą bezstanowe. |
| --target-replica-set-size | Replika docelowej Ustaw rozmiar jako liczba. Dotyczy to tylko w przypadku usług stanowych. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-service-delete"></a>Usuń usługę interfejsu sfctl
Usuwa istniejącą usługę Service Fabric.

Usługa musi zostać utworzona, aby można było usunąć. Domyślnie Usługa Service Fabric podejmie próbę zamknięcie usługi replik w sposób płynnego, a następnie usuń usługę. Jednak jeśli usługa występują problemy, które bez problemu zmieniała zamknięcia repliki, operacja usuwania może zająć dużo czasu lub zatrzymywane. Opcjonalna Flaga ForceRemove umożliwia pominąć łagodne zamknięcia sekwencji i wymuszać usunąć usługę.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator usługi [wymagane] | Tożsamość usługi. Ten identyfikator jest zazwyczaj pełną nazwę usługi bez "Service fabric\:" schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchiczne nazwy są oddzielane za pomocą znaku "\~" znaków. Na przykład, jeśli nazwa usługi jest "Service fabric\:/myapp/app1/svc1", będzie tożsamości usługi "myapp\~app1\~svc1" w wersji 6.0 + i "myapp/app1/svc1" w poprzednich wersjach. |
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

## <a name="sfctl-service-deployed-type"></a>Usługa interfejs sfctl wdrożone typu
Pobiera informacje o określonej usługi rodzaj aplikacji wdrożonej w węźle w klastrze usługi Service Fabric.

Pobiera listę zawierającą informacje o typie określonej usługi, z poziomu aplikacji wdrożonych w węźle w klastrze usługi Service Fabric. Odpowiedź zawiera nazwę typu usługi, jego stan rejestracji, pakiet kodu, który zarejestrował ją i aktywacja identyfikator pakietu usługi. Każdy wpis przedstawia jeden aktywacji typu usługi zróżnicowane według identyfikatora aktywacji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator aplikacji [wymagane] | Tożsamość aplikacji. Zazwyczaj jest to pełna nazwa aplikacji bez "Service fabric\:" schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchiczne nazwy są oddzielane za pomocą znaku "\~" znaków. Na przykład, jeśli nazwa aplikacji jest "Service fabric\:/myapp/app1", tożsamość aplikacji będzie "myapp\~app1" w wersji 6.0 + i "myapp app1" w poprzednich wersjach. |
| — [wymagane] Nazwa węzła | Nazwa węzła. |
| --service-type-name [wymagane] | Określa nazwę typu usługi Service Fabric. |
| --service-manifest-name | Nazwa manifestu usługi, aby filtrować listę informacji o typie wdrożonej usługi. Jeśli zostanie określony, odpowiedź będzie zawierać tylko informacje dotyczące typów usług, które są zdefiniowane w manifeście tej usługi. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-service-deployed-type-list"></a>Interfejs sfctl usługa wdrożona typ list
Pobiera listę zawierającą informacje o typach usługi z poziomu aplikacji wdrożonych w węźle w klastrze usługi Service Fabric.

Pobiera listę zawierającą informacje o typach usługi z poziomu aplikacji wdrożonych w węźle w klastrze usługi Service Fabric. Odpowiedź zawiera nazwę typu usługi, jego stan rejestracji, pakiet kodu, który zarejestrował ją i aktywacja identyfikator pakietu usługi.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator aplikacji [wymagane] | Tożsamość aplikacji. Zazwyczaj jest to pełna nazwa aplikacji bez "Service fabric\:" schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchiczne nazwy są oddzielane za pomocą znaku "\~" znaków. Na przykład, jeśli nazwa aplikacji jest "Service fabric\:/myapp/app1", tożsamość aplikacji będzie "myapp\~app1" w wersji 6.0 + i "myapp app1" w poprzednich wersjach. |
| — [wymagane] Nazwa węzła | Nazwa węzła. |
| --service-manifest-name | Nazwa manifestu usługi, aby filtrować listę informacji o typie wdrożonej usługi. Jeśli zostanie określony, odpowiedź będzie zawierać tylko informacje dotyczące typów usług, które są zdefiniowane w manifeście tej usługi. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-service-description"></a>Opis usługi sfctl
Pobiera opis istniejącej usługi Service Fabric.

Pobiera opis istniejącej usługi Service Fabric. Usługa musi zostać utworzona przed można uzyskać jego opis.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator usługi [wymagane] | Tożsamość usługi. Ten identyfikator jest zazwyczaj pełną nazwę usługi bez "Service fabric\:" schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchiczne nazwy są oddzielane za pomocą znaku "\~" znaków. Na przykład, jeśli nazwa usługi jest "Service fabric\:/myapp/app1/svc1", będzie tożsamości usługi "myapp\~app1\~svc1" w wersji 6.0 + i "myapp/app1/svc1" w poprzednich wersjach. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-service-get-container-logs"></a>Interfejs sfctl get-container dzienniki usługi
Pobiera dzienniki kontenera dla kontenera wdrażane w węźle usługi Service Fabric.

Pobiera dzienniki kontenera dla kontenera wdrażane w węźle usługi Service Fabric dla pakietu danego kodu.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator aplikacji [wymagane] | Tożsamość aplikacji. Zazwyczaj jest to pełna nazwa aplikacji bez "Service fabric\:" schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchiczne nazwy są oddzielane za pomocą znaku "\~" znaków. Na przykład, jeśli nazwa aplikacji jest "Service fabric\:/myapp/app1", tożsamość aplikacji będzie "myapp\~app1" w wersji 6.0 + i "myapp app1" w poprzednich wersjach. |
| --Kod pakietu nazwa-[wymagane] | Nazwa pakietu kodu określonego w manifeście usługi zarejestrowany jako część typu aplikacji w klastrze usługi Service Fabric. |
| — [wymagane] Nazwa węzła | Nazwa węzła. |
| --service-manifest-name [wymagane] | Nazwa manifestu usługi zarejestrowany jako część typu aplikacji w klastrze usługi Service Fabric. |
| --poprzedniej | Określa, czy można pobrać dzienniki kontenerów z kontenerów zakończył działanie dead wystąpienia pakietu kodu. |
| --tail | Liczba wierszy do pokazania od końca dzienniki. Domyślna to 100. "wszystkie", aby wyświetlić pełne dzienniki. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-service-health"></a>Kondycja usługi sfctl
Pobiera kondycji określonej usługi Service Fabric.

Pobiera informacje o kondycji określonej usługi. Zgłoszone EventsHealthStateFilter użycia do filtrowania kolekcji zdarzeń dotyczących kondycji usługi oparte na stanie kondycji. Użyj PartitionsHealthStateFilter do filtrowania kolekcji zwrócił partycji. Jeśli określisz to usługa, która nie istnieje w magazynie kondycji, to żądanie zwraca błąd.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --service-id          [Required] | Tożsamość usługi. Ten identyfikator jest zazwyczaj pełną nazwę usługi bez "Service fabric\:" schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchiczne nazwy są oddzielane za pomocą znaku "\~" znaków. Na przykład, jeśli nazwa usługi jest "Service fabric\:/myapp/app1/svc1", będzie tożsamości usługi "myapp\~app1\~svc1" w wersji 6.0 + i "myapp/app1/svc1" w poprzednich wersjach. |
| --events-health-state-filter | Umożliwia filtrowanie kolekcji zwracanych obiektów HealthEvent oparte na stanie kondycji. Możliwe wartości dla tego parametru to wartość całkowitą, jednego z następujących stanów kondycji. Zwracane są tylko te zdarzenia, które są zgodne z filtrem. Wszystkie zdarzenia są używane do oceny stanu kondycji zagregowane. Jeśli nie zostanie określony, zwracane są wszystkie wpisy. Wartości stanu są wyliczanie oparte na flagi, dzięki czemu może to być kombinacją tych wartości uzyskanych przy użyciu bitowego operatora "Lub". Na przykład jeśli podana wartość jest 6 następnie wszystkie zdarzenia z wartością atrybutu HealthState OK (2) i ostrzeżenia (4) są zwracane.  <br> -Domyślnie — wartość domyślną. Pasuje do dowolnego atrybutu HealthState. Ta wartość wynosi zero.  <br> -Brak — filtr, który nie jest zgodny z dowolną wartością atrybutu HealthState. Używany, aby zwracała żadnych wyników w danej kolekcji stanów. Wartość to 1.  <br> -Ok — filtrowanie, że dopasowania danych wejściowych z wartością atrybutu HealthState Ok. Wartość jest równa 2.  <br> -Warning - filtru, że wprowadzanie dopasowania z atrybutem HealthState wartość ostrzeżenie. Wartość wynosi 4.  <br> -Błąd — filtr, który pasuje do danych wejściowych z wartością atrybutu HealthState błędu. Wartość jest 8.  <br> -Al - filtr, który pasuje do danych wejściowych z dowolną wartością atrybutu HealthState. Wartość jest 65535. |
| --exclude-health-statistics | Wskazuje, czy statystyki kondycji ma zostać zwrócone w wyniku zapytania. Wartość false, domyślnie. Statystyki pokazują liczbę elementów podrzędnych jednostek w kondycja Ok, ostrzeżenia i błędu. |
| --partitions-health-state-filter | Umożliwia filtrowanie obiektów stanu kondycji partycje zwrócone w wyniku zapytania usługi kondycji, na podstawie ich stanu kondycji. Możliwe wartości dla tego parametru to wartość całkowitą, jednego z następujących stanów kondycji. Zwracane są tylko te partycje, które są zgodne z filtrem. Wszystkie partycje służą do oceny stanu kondycji zagregowane. Jeśli nie zostanie określony, zwracane są wszystkie wpisy. Wartości stanu są wyliczanie oparte na flagi, dzięki czemu może to być kombinacją tych wartości uzyskanych przy użyciu bitowego operatora "Lub". Na przykład jeśli podana wartość jest 6 następnie stan kondycji partycji przy użyciu wartości atrybutu HealthState OK (2) i ostrzeżenia (4) zwracaną.  <br> -Domyślnie — wartość domyślną. Pasuje do dowolnego atrybutu HealthState. Ta wartość wynosi zero.  <br> -Brak — filtr, który nie jest zgodny z dowolną wartością atrybutu HealthState. Używany, aby zwracała żadnych wyników w danej kolekcji stanów. Wartość to 1.  <br> -Ok — filtrowanie, że dopasowania danych wejściowych z wartością atrybutu HealthState Ok. Wartość jest równa 2.  <br> -Warning - filtru, że wprowadzanie dopasowania z atrybutem HealthState wartość ostrzeżenie. Wartość wynosi 4.  <br> -Błąd — filtr, który pasuje do danych wejściowych z wartością atrybutu HealthState błędu. Wartość jest 8.  <br> -Al - filtr, który pasuje do danych wejściowych z dowolną wartością atrybutu HealthState. Wartość jest 65535. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-service-info"></a>informacje o usłudze sfctl
Pobiera informacje o określonej usługi należące do aplikacji usługi Service Fabric.

Zwraca informacje dotyczące określonej usługi należących do określonej aplikacji usługi Service Fabric.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator aplikacji [wymagane] | Tożsamość aplikacji. Zazwyczaj jest to pełna nazwa aplikacji bez "Service fabric\:" schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchiczne nazwy są oddzielane za pomocą znaku "\~" znaków. Na przykład, jeśli nazwa aplikacji jest "Service fabric\:/myapp/app1", tożsamość aplikacji będzie "myapp\~app1" w wersji 6.0 + i "myapp app1" w poprzednich wersjach. |
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

## <a name="sfctl-service-list"></a>Lista usług interfejsu sfctl
Pobiera informacje o wszystkich usług należących do aplikacji, określonego przez identyfikator aplikacji.

Zwraca informacje dotyczące wszystkich usług należących do aplikacji, określonego przez identyfikator aplikacji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator aplikacji [wymagane] | Tożsamość aplikacji. Zazwyczaj jest to pełna nazwa aplikacji bez "Service fabric\:" schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchiczne nazwy są oddzielane za pomocą znaku "\~" znaków. Na przykład, jeśli nazwa aplikacji jest "Service fabric\:/myapp/app1", tożsamość aplikacji będzie "myapp\~app1" w wersji 6.0 + i "myapp app1" w poprzednich wersjach. |
| --token kontynuacji | Parametr tokenu kontynuacji służy do uzyskania następny zestaw wyników. Token kontynuacji o wartości niepuste znajduje się w odpowiedzi interfejsu API, gdy wyniki z systemu nie mieszczą się w jednej odpowiedzi. Jeśli ta wartość jest przekazywana do następnego wywołania interfejsu API, interfejs API zwraca następny zestaw wyników. Jeśli nie istnieją żadne dalsze wyniki, token kontynuacji nie zawiera wartości. Wartość tego parametru nie powinny być zakodowane w adresie URL. |
| --service-type-name | Nazwa typu usługi używane do filtrowania usług dla kwerendy. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-service-manifest"></a>Interfejs sfctl manifestu usługi
Pobiera manifest opisujące typ usługi.

Pobiera manifest opisujące typ usługi. Odpowiedź zawiera kod XML manifestu usługi jako ciąg.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| -aplikacji type-name [wymagane] | Nazwa typu aplikacji. |
| --— wersję typu aplikacji — [wymagane] | Wersja typu aplikacji. |
| --service-manifest-name    [Required] | Nazwa manifestu usługi zarejestrowany jako część typu aplikacji w klastrze usługi Service Fabric. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-service-package-deploy"></a>Wdrażanie pakietu usługi sfctl
Pobiera pakiety skojarzone z określoną usługą manifestu do pamięci podręcznej obrazów w określonym węźle.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| -aplikacji-type-name [wymagane] | Nazwa manifestu aplikacji odpowiednie manifestu żądanej usługi. |
| --— Typ wersja aplikacji [wymagane] | Wersja manifestu aplikacji odpowiednie manifestu żądanej usługi. |
| — [wymagane] Nazwa węzła | Nazwa węzła. |
| --service-manifest-name [wymagane] | Nazwa manifestu usługi skojarzone z pakietami, które będą pobierane. |
| — zasady udziału | Lista kodowany w formacie JSON zasad udostępniania. Każdy udostępniania element zasad składa się z "name" i "scope". Nazwa odpowiada nazwa pakietu kodu, konfiguracji lub danych, który ma zostać udostępniony. Zakres nie może być wartość "None", "All", "Code", "Konfiguracja" lub "Dane". |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-service-package-health"></a>Interfejs sfctl pakietu — kondycja usługi
Pobiera informacje o kondycji usług pakietu dla określonej aplikacji wdrożone dla węzła usługi Service Fabric i aplikacji.

Pobiera informacje o kondycji usług pakietu dla określonej aplikacji wdrożonych w węźle usługi Service Fabric. Użyj EventsHealthStateFilter opcjonalnie filtrowania dla kolekcji obiektów HealthEvent zgłoszone w pakiecie wdrożonej usługi na podstawie stanu kondycji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator aplikacji [wymagane] | Tożsamość aplikacji. Zazwyczaj jest to pełna nazwa aplikacji bez "Service fabric\:" schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchiczne nazwy są oddzielane za pomocą znaku "\~" znaków. Na przykład, jeśli nazwa aplikacji jest "Service fabric\:/myapp/app1", tożsamość aplikacji będzie "myapp\~app1" w wersji 6.0 + i "myapp app1" w poprzednich wersjach. |
| — [wymagane] Nazwa węzła | Nazwa węzła. |
| --service pakietu name [wymagane] | Nazwa pakietu usługi. |
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

## <a name="sfctl-service-package-info"></a>Interfejs sfctl usługi — informacje o pakiecie
Pobiera listę pakietów usług wdrożonych w węźle usługi Service Fabric dopasowania określonej nazwy.

Zwraca informacje o pakietach usługi wdrożone w węźle usługi Service Fabric dla danej aplikacji. Pakietów usługi, którego nazwa odpowiada dokładnie nazwę pakietu usługi te wyniki są określone jako parametr.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator aplikacji [wymagane] | Tożsamość aplikacji. Zazwyczaj jest to pełna nazwa aplikacji bez "Service fabric\:" schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchiczne nazwy są oddzielane za pomocą znaku "\~" znaków. Na przykład, jeśli nazwa aplikacji jest "Service fabric\:/myapp/app1", tożsamość aplikacji będzie "myapp\~app1" w wersji 6.0 + i "myapp app1" w poprzednich wersjach. |
| — [wymagane] Nazwa węzła | Nazwa węzła. |
| --service pakietu name [wymagane] | Nazwa pakietu usługi. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-service-package-list"></a>Interfejs sfctl usługę z listy pakietów
Pobiera listę pakietów usług wdrożonych w węźle usługi Service Fabric.

Zwraca informacje o pakietach usługi wdrożone w węźle usługi Service Fabric dla danej aplikacji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator aplikacji [wymagane] | Tożsamość aplikacji. Zazwyczaj jest to pełna nazwa aplikacji bez "Service fabric\:" schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchiczne nazwy są oddzielane za pomocą znaku "\~" znaków. Na przykład, jeśli nazwa aplikacji jest "Service fabric\:/myapp/app1", tożsamość aplikacji będzie "myapp\~app1" w wersji 6.0 + i "myapp app1" w poprzednich wersjach. |
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

## <a name="sfctl-service-recover"></a>Odzyskaj usługi sfctl
Klaster usługi Service Fabric wskazywać, że ma podejmować do odzyskania z określonej usługi, który jest obecnie zablokowany utraciła kworum.

Klaster usługi Service Fabric wskazywać, że ma podejmować do odzyskania z określonej usługi, który jest obecnie zablokowany utraciła kworum. Ta operacja powinna można wykonać tylko, jeśli jest znany, nie będzie można odzyskać replik, które nie działają. Niepoprawne użycie tego interfejsu API może spowodować ryzyko utraty danych.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator usługi [wymagane] | Tożsamość usługi. Ten identyfikator jest zazwyczaj pełną nazwę usługi bez "Service fabric\:" schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchiczne nazwy są oddzielane za pomocą znaku "\~" znaków. Na przykład, jeśli nazwa usługi jest "Service fabric\:/myapp/app1/svc1", będzie tożsamości usługi "myapp\~app1\~svc1" w wersji 6.0 + i "myapp/app1/svc1" w poprzednich wersjach. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-service-report-health"></a>Interfejs sfctl usługi raportów kondycji
Wysyła raport o kondycji usługi Service Fabric.

Raporty stan kondycji określonej usługi Service Fabric. Raport musi zawierać informacje o źródle raport o kondycji i właściwości, na którym jest zgłaszany. Raport jest wysyłany do usługi, która przekazuje w magazynie kondycji bramy usługi Service Fabric. Raport może być akceptowane przez bramę, ale odrzucony przez magazynu kondycji po dodatkową walidację. Na przykład magazynu kondycji może odrzucić raport ze względu na nieprawidłowy parametr, takich jak numer sekwencji starych. Aby sprawdzić, czy raport został zastosowany w magazynie kondycji, sprawdź, czy raport jest wyświetlany w zdarzeń kondycji usługi.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --kondycji — właściwość [wymagane] | Właściwość o kondycji. <br><br> Jednostka może mieć raportów o kondycji dla różnych właściwości. Właściwość jest ciągu i stałych wyliczenia aby zezwalał na elastyczność reportera do kategoryzowania warunek stanu, która powoduje uruchomienie raportu. Na przykład reportera o ID "LocalWatchdog" można monitorować stan wolnego w węźle, aby go zgłosić właściwości "AvailableDisk" w tym węźle. Ten sam reportera monitorować łączność węzeł tak go zgłosić właściwości "Łączność" w tym samym węźle. W magazynie kondycji te raporty są traktowane jako zdarzenia dotyczące kondycji oddzielnych dla określonego węzła. Wraz z SourceId właściwość jednoznacznie identyfikuje informacje o kondycji. |
| --health-state    [Required] | Możliwe wartości to\: "Nieprawidłowy", "Ok", "Ostrzeżenie", "Error", "Nieznany". |
| --service-id      [Required] | Tożsamość usługi. <br><br> Zazwyczaj jest to pełna nazwa usługi bez "Service fabric\:" schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchiczne nazwy są oddzielane za pomocą znaku "\~" znaków. Na przykład, jeśli nazwa usługi jest "Service fabric\:/myapp/app1/svc1", będzie tożsamości usługi "myapp\~app1\~svc1" w wersji 6.0 + i "myapp/app1/svc1" w poprzednich wersjach. |
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

## <a name="sfctl-service-resolve"></a>Interfejs sfctl service resolve
Rozwiąż partycji usługi Service Fabric.

Rozwiąż partycji usługi Service Fabric można pobrać z punktami końcowymi repliki usługi.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator usługi [wymagane] | Tożsamość usługi. Ten identyfikator jest zazwyczaj pełną nazwę usługi bez "Service fabric\:" schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchiczne nazwy są oddzielane za pomocą znaku "\~" znaków. Na przykład, jeśli nazwa usługi jest "Service fabric\:/myapp/app1/svc1", będzie tożsamości usługi "myapp\~app1\~svc1" w wersji 6.0 + i "myapp/app1/svc1" w poprzednich wersjach. |
| --partition-key-type | Typ klucza partycji. Ten parametr jest wymagany, jeśli schemat partycji dla usługi jest Int64Range lub nazwane. Możliwe wartości są następujące. -Brak (1) — wskazuje, że nie określono parametru PartitionKeyValue. To jest prawidłowy dla partycji z podziałem na partycje schematu jako pojedyncza. Jest to wartość domyślna. Wartość to 1. -Int64Range (2) — wskazuje parametr PartitionKeyValue klucz partycji typu int64. To jest prawidłowy dla partycji przy użyciu schematu jako Int64Range partycjonowania. Wartość jest równa 2. -O nazwie (3) — wskazuje, że parametr PartitionKeyValue nazwę partycji. To jest prawidłowy dla partycji przy użyciu schemat jako nazwanych partycji. Wartość to 3. |
| --partition-key-value | Klucz partycji. Jest to wymagane, jeśli schemat partycji dla usługi jest Int64Range lub nazwane. To nie jest Identyfikatorem partycji, ale raczej to liczba całkowita klucza, lub nazwa identyfikatora partycji. Na przykład jeśli usługa używa ranged partycji z zakresu od 0 do 10, ich PartitionKeyValue będzie liczbą całkowitą, w tym zakresie. Wyślij zapytanie do opisu usługi, aby zobaczyć, zakresu lub nazwy. |
| --poprzedniej wersji rsp | Wartość w polu Wersja odpowiedź, która została otrzymana wcześniej. Jest to wymagane, jeśli użytkownik wie, że wynik, który był coraz wcześniej jest przestarzałe. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-service-type-list"></a>Lista typów interfejsu sfctl usługi
Pobiera listę zawierającą informacje o typach usługi, które są obsługiwane przez typ aplikacji aprowizowanych w klastrze usługi Service Fabric.

Pobiera listę zawierającą informacje o typach usługi, które są obsługiwane przez typ aplikacji aprowizowanych w klastrze usługi Service Fabric. Typ aplikacji podana, musi istnieć. W przeciwnym razie zwracany jest stan 404.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| -aplikacji type-name [wymagane] | Nazwa typu aplikacji. |
| --— wersję typu aplikacji — [wymagane] | Wersja typu aplikacji. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-service-update"></a>Aktualizacja usługi sfctl
Aktualizuje określonej usługi przy użyciu opisu danej aktualizacji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --service-id   [Required] | Tożsamość usługi. Zazwyczaj jest to pełna nazwa usługi bez "Service fabric\:" schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchiczne nazwy są oddzielane za pomocą znaku "\~" znaków. Na przykład, jeśli nazwa usługi jest "Service fabric\:/myapp/app1/svc1", będzie tożsamości usługi "myapp\~app1\~svc1" w wersji 6.0 + i "myapp/app1/svc1" w poprzednich wersjach. |
| — ograniczenia | Ograniczenia dotyczące umieszczania jako ciąg. Ograniczeniami dotyczącymi umieszczania są wyrażeń logicznych na — właściwości węzła i umożliwiają ograniczenie usługi do określonych węzłów w oparciu o wymagania dotyczące usługi. Na przykład, aby umieścić usługi na węzłach, gdzie element NodeType to niebieski określ następujące\: "NodeColor == niebieski". |
| --correlated-service | Nazwa docelowej usługi w celu skorelowania za pomocą. |
| --correlation | Korelowanie usługi za pomocą istniejącej usługi przy użyciu koligacji wyrównania. |
| --instance-count | Liczba wystąpień. Dotyczy to tylko w przypadku usług bezstanowych. |
| --metryk obciążenia | Zakodowane w formacie JSON listy metryk używany podczas ładowania równoważenia między węzłami. |
| --min-replica-set-size | Minimalna repliki Ustaw rozmiar jako liczba. Dotyczy to tylko w przypadku usług stanowych. |
| --move-cost | Określa koszt przeniesienia dla usługi. Możliwe wartości to\: "Zero", "Niska", "Średnie", "Wysokiego". |
| --placement-policy-list | Zakodowane JSON listy zasady umieszczania dla usługi, wraz ze wszystkimi skojarzonymi nazw domen. Zasady mogą być co najmniej\: `NonPartiallyPlaceService`, `PreferPrimaryDomain`, `RequireDomain`, `RequireDomainDistribution`. |
| --kworum utraty wait | Maksymalny czas trwania w sekundach, dla której partycji może być w stanie utraciła kworum. Dotyczy to tylko w przypadku usług stanowych. |
| --replica-restart-wait | Czas w sekundach między Jeśli replika ulegnie awarii, a po utworzeniu nowej repliki. Dotyczy to tylko w przypadku usług stanowych. |
| --scaling-policies | JSON zakodowany listę skalowania zasad dla tej usługi. |
| --stand-by-replica-keep | Maksymalny czas trwania w sekundach, w których stan wstrzymania repliki zostanie zachowana przed usuwany. Dotyczy to tylko w przypadku usług stanowych. |
| --stateful | Wskazuje, że Usługa docelowa jest usługi stanowej. |
| --stateless | Wskazuje, że Usługa docelowa jest bezstanowej usługi. |
| --target-replica-set-size | Replika docelowej Ustaw rozmiar jako liczba. Dotyczy to tylko w przypadku usług stanowych. |
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
- [Konfigurowanie](service-fabric-cli.md) interfejsu wiersza polecenia usługi Service Fabric.
- Dowiedz się, jak używać przy użyciu interfejsu wiersza polecenia usługi Service Fabric [przykładowe skrypty](/azure/service-fabric/scripts/sfctl-upgrade-application).

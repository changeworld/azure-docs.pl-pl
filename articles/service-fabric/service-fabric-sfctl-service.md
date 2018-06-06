---
title: Usługa sieci szkieletowej interfejsu wiersza polecenia - sfctl usługi Azure | Dokumentacja firmy Microsoft
description: Opis poleceń usługi sfctl interfejsu wiersza polecenia usługi sieci szkieletowej.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 05/23/2018
ms.author: bikang
ms.openlocfilehash: e027bb7f61d19fc274f7eddd8f28e9e6dac099ce
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763548"
---
# <a name="sfctl-service"></a>sfctl service
Tworzenie, usuwanie i zarządzanie usługi, typów usług i pakietów usług.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| Nazwa aplikacji | Pobiera nazwę aplikacji sieci szkieletowej usług dla usługi. |
| code-package-list | Pobiera listę pakietów kodu wdrożone w węźle sieci szkieletowej usług. |
| create | Tworzy określony usługi sieć szkieletowa usług. |
| usuwanie | Usuwa istniejącą usługę sieć szkieletowa usług. |
| wdrożone typu | Pobiera informacje o typie określonej usługi, aplikacji wdrożonych w węźle klastra sieci szkieletowej usług. |
| wdrożony typ — listy | Pobiera listę zawierającą informacje o typach usługi z aplikacje wdrożone w węźle klastra sieci szkieletowej usług. |
| description | Pobiera opis istniejącej usługi sieć szkieletowa usług. |
| Get kontenera — dzienniki | Pobiera kontener dzienniki dla kontenera wdrożone w węźle sieci szkieletowej usług. |
| kondycja | Pobiera kondycji określonej usługi sieć szkieletowa usług. |
| informacje | Pobiera informacje o określonej usługi należące do aplikacji sieci szkieletowej usług. |
| lista | Pobiera informacje o wszystkich usług należących do aplikacji określonej przez identyfikator aplikacji. |
| Manifest | Pobiera manifest opisujące typ usługi. |
| Wdrażanie pakietu | Pobiera pakiety skojarzone z określoną usługą manifestu do pamięci podręcznej obrazu na określony węzeł. |
| package-health | Pobiera informacje o kondycji pakiet usługi dla określonej aplikacji wdrożone dla węzła sieci szkieletowej usług i aplikacji. |
| package-info | Pobiera listę pakietów usług wdrożone w węźle sieci szkieletowej usług dopasowania określonej nazwy. |
| package-list | Pobiera listę pakietów usług wdrożone w węźle sieci szkieletowej usług. |
| Odzyskiwanie | Wskazuje, aby klaster sieci szkieletowej usług mają podejmować próbę odzyskać określonej usługi, który jest obecnie zablokowane w wyniku utraty kworum. |
| report-health | Wysyła raport o kondycji usługi sieć szkieletowa usług. |
| rozwiąż | Rozwiąż partycji usługi sieć szkieletowa usług. |
| Lista typów | Pobiera listę zawierającą informacje o typach usługi, które są obsługiwane przez typ elastycznie aplikacji w klastrze usługi sieć szkieletowa usług. |
| Aktualizacja | Aktualizuje określonej usługi przy użyciu opisu danego aktualizacji. |

## <a name="sfctl-service-app-name"></a>Nazwa aplikacji sfctl usługi
Pobiera nazwę aplikacji sieci szkieletowej usług dla usługi.

Pobiera nazwę aplikacji dla określonej usługi. Błąd 404 FABRIC_E_SERVICE_DOES_NOT_EXIST jest zwracany, jeśli usługa o identyfikatorze świadczonych usług nie istnieje.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator usługi [wymagane] | Tożsamość usługi. Zazwyczaj jest to pełna nazwa tej usługi bez ' sieci szkieletowej\:"schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchicznych nazwy są rozdzielane "\~" znaków. Na przykład, jeśli nazwa usługi jest "sieć szkieletowa\:/myapp/app1/svc1", jest tożsamość usługi "moja_aplikacja\~app1\~svc1" w wersji 6.0 + i "myapp/app1/svc1" w poprzednich wersjach. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-service-code-package-list"></a>sfctl usługi kodu pakietu — listy
Pobiera listę pakietów kodu wdrożone w węźle sieci szkieletowej usług.

Pobiera listę pakietów kodu wdrożone w węźle sieci szkieletowej usług dla danej aplikacji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator aplikacji [wymagane] | Tożsamość aplikacji. Zazwyczaj jest to pełna nazwa aplikacji bez ' sieci szkieletowej\:"schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchicznych nazwy są rozdzielane "\~" znaków. Na przykład, jeśli nazwa aplikacji jest "sieć szkieletowa\:/myapp/app1", jest tożsamość aplikacji "moja_aplikacja\~app1" w wersji 6.0 + i "myapp/app1" w poprzednich wersjach. |
| --Nazwa węzła [wymagane] | Nazwa węzła. |
| --nazwy w przypadku pakietu kodu | Nazwa pakietu kodu określone w zarejestrowany jako część typu aplikacji w klastrze usługi sieć szkieletowa manifestu usługi. |
| --nazwa_usługi manifestu | Nazwa manifestu usługi, w zarejestrowany jako część typu aplikacji w klastrze usługi sieć szkieletowa usług. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-service-create"></a>Tworzenie usługi sfctl
Tworzy określony usługi sieć szkieletowa usług.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| [wymagane] - app-id | Tożsamość aplikacji. Zazwyczaj jest to pełna nazwa aplikacji bez ' sieci szkieletowej\:"schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchicznych nazwy są rozdzielane "\~" znaków. Na przykład, jeśli nazwa aplikacji jest "sieć szkieletowa\:/myapp/app1", jest tożsamość aplikacji "moja_aplikacja\~app1" w wersji 6.0 + i "myapp/app1" w poprzednich wersjach. |
| --name [wymagane] | Nazwa usługi. Powinno to być elementem podrzędnym identyfikator aplikacji. Jest to pełny nazwy w tym `fabric\:` identyfikatora URI. Na przykład usługi `fabric\:/A/B` jest elementem podrzędnym aplikacji `fabric\:/A`. |
| — Typ usługi [wymagane] | Nazwa typu usługi. |
| --trybie aktywacji | Tryb aktywacji dla pakietu usług. |
| — ograniczenia | Ograniczenia dotyczące umieszczania jako ciąg. Ograniczenia dotyczące umieszczania są wyrażeń logicznych na właściwości węzła i umożliwiają ograniczenie usługi dla określonych węzłów na podstawie wymagań usługi. Na przykład, aby umieścić usługi na węzłach, gdzie jest niebieska NodeType określ następujące\:"NodeColor == niebieski". |
| --correlated-service | Nazwa usługi docelowej, aby mieć związek z. |
| --Korelacja | Korelowanie usługę za pomocą istniejącej usługi za pomocą koligacji wyrównania. |
| --dns-name | Nazwa DNS usługa ma zostać utworzony. To ustawienie, można włączyć usługę systemu DNS sieci szkieletowej usług. |
| — Liczba wystąpień | Liczba wystąpień. Dotyczy to tylko w przypadku usług bezstanowych. |
| --int-scheme | Wskazuje, że usługa powinna być jednolicie podzielonym na partycje w zakres liczb całkowitych bez znaku. |
| --int-scheme-count | Liczba partycji wewnątrz zakresu klucza liczby całkowitej w celu utworzenia, jeśli przy użyciu całkowitą jednolity schemat partycji. |
| --int-scheme-high | Koniec zakresu klucza liczba całkowita, jeśli przy użyciu schematu partycji uniform liczby całkowitej. |
| --int-scheme-low | Początek zakresu klucza liczba całkowita, jeśli przy użyciu schematu partycji uniform liczby całkowitej. |
| --metryki obciążenia | Lista kodowany w formacie JSON metryki używane, gdy usług równoważenia obciążenia w węzłach. |
| --min-replica-set-size | Minimalna repliki Ustaw rozmiar jako liczba. Dotyczy to tylko usług stanowych. |
| --move-cost | Określa koszt przeniesienia dla usługi. Możliwe wartości to\: "Zero", "Od", "Średni", "High". |
| --schematu o nazwie | Wskazuje, że usługa ma wiele partycji o nazwie. |
| --named-scheme-list | Kodowany w formacie JSON listę nazw do partycjonowania usługi, jeśli przy użyciu schematu partycji o nazwie. |
| --nie utrwalony — stan | Jeśli ma wartość true, wskazuje usługi nie ma trwałego stanu przechowywane na dysku lokalnym lub tylko zapisuje stan w pamięci. |
| --placement-policy-list | Listę umieszczania zasad dla usługi kodowany w formacie JSON, wraz ze wszystkimi skojarzonymi nazw domen. Zasady mogą być co najmniej jeden\: `NonPartiallyPlaceService`, `PreferPrimaryDomain`, `RequireDomain`, `RequireDomainDistribution`. |
| --kworum utraty — oczekiwania | Maksymalny czas w sekundach, dla których partycji może być w stanie utraciła kworum. Dotyczy to tylko usług stanowych. |
| --replica-restart-wait | Czas w sekundach między gdy spadnie repliki i utworzenia nowej repliki. Dotyczy to tylko usług stanowych. |
| --Skalowanie zasad | Zakodowane JSON listy skalowanie zasad dla tej usługi. |
| --singleton schematu | Wskazuje usługi powinny mieć jednej partycji lub być niepartycjonowany usługi. |
| --wstrzymania przez repliki zachować | Maksymalny czas trwania w sekundach, w których stan wstrzymania replik będzie obsługiwany przed usuwana. Dotyczy to tylko usług stanowych. |
| --stateful | Wskazuje, że usługa jest usługą stanowych. |
| --bezstanowych | Wskazuje, że usługa jest usługą bezstanowe. |
| --docelowy —-rozmiar zestawu replik | Repliki docelowej Ustaw rozmiar jako liczba. Dotyczy to tylko usług stanowych. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-service-delete"></a>Usuń usługi sfctl
Usuwa istniejącą usługę sieć szkieletowa usług.

Usługa musi zostać utworzony, aby można było usunąć. Domyślnie usługi sieć szkieletowa spróbuje zamknięcie repliki usługi w sposób bezpieczne, a następnie usuń usługę. Jednak jeśli usługa ma problemy bezpiecznie zamykania repliki, operacja usuwania może zająć dużo czasu lub zatrzymywane. Opcjonalna Flaga ForceRemove umożliwia Pomiń operację prawidłowego zamknięcia sekwencji i wymuszone Usuń usługę.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator usługi [wymagane] | Tożsamość usługi. Zazwyczaj jest to pełna nazwa tej usługi bez ' sieci szkieletowej\:"schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchicznych nazwy są rozdzielane "\~" znaków. Na przykład, jeśli nazwa usługi jest "sieć szkieletowa\:/myapp/app1/svc1", jest tożsamość usługi "moja_aplikacja\~app1\~svc1" w wersji 6.0 + i "myapp/app1/svc1" w poprzednich wersjach. |
| --force-remove | Usuń sieć szkieletowa usług aplikacji lub usługi wymuszone bez pośrednictwa bezpiecznego zamknięcia sekwencji. Ten parametr może służyć do wymuszone usunięcie aplikacji lub usługi, dla których delete jest przekroczeniem limitu czasu z powodu problemów z kodem usługi, który uniemożliwia łagodne zamykanie replik. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-service-deployed-type"></a>Usługa sfctl wdrożone typu
Pobiera informacje o typie określonej usługi, aplikacji wdrożonych w węźle klastra sieci szkieletowej usług.

Pobiera listę zawierającą informacje dotyczące określonego typu usługi z aplikacje wdrożone w węźle klastra sieci szkieletowej usług. Odpowiedź zawiera nazwę typu usługi, jego stan rejestracji, pakiet kodu, który zarejestrowany go i aktywacji Identyfikatora pakietu usług. Każdy wpis reprezentuje jeden aktywacji usługi typu zróżnicowane według identyfikatora aktywacji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator aplikacji [wymagane] | Tożsamość aplikacji. Zazwyczaj jest to pełna nazwa aplikacji bez ' sieci szkieletowej\:"schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchicznych nazwy są rozdzielane "\~" znaków. Na przykład, jeśli nazwa aplikacji jest "sieć szkieletowa\:/myapp/app1", jest tożsamość aplikacji "moja_aplikacja\~app1" w wersji 6.0 + i "myapp/app1" w poprzednich wersjach. |
| --Nazwa węzła [wymagane] | Nazwa węzła. |
| --— Nazwa typu usługi — [wymagane] | Określa nazwę typu usługi sieć szkieletowa usług. |
| --nazwa_usługi manifestu | Nazwa manifestu usługi, aby filtrować listę informacji o typie wdrożonej usługi. Jeśli zostanie określona, odpowiedź będzie zawierać tylko informacje dotyczące typów usług, które są zdefiniowane w tym manifeście usługi. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-service-deployed-type-list"></a>sfctl usługi wdrożone typ — listy
Pobiera listę zawierającą informacje o typach usługi z aplikacje wdrożone w węźle klastra sieci szkieletowej usług.

Pobiera listę zawierającą informacje o typach usługi z aplikacje wdrożone w węźle klastra sieci szkieletowej usług. Odpowiedź zawiera nazwę typu usługi, jego stan rejestracji, pakiet kodu, który zarejestrowany go i aktywacji Identyfikatora pakietu usług.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator aplikacji [wymagane] | Tożsamość aplikacji. Zazwyczaj jest to pełna nazwa aplikacji bez ' sieci szkieletowej\:"schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchicznych nazwy są rozdzielane "\~" znaków. Na przykład, jeśli nazwa aplikacji jest "sieć szkieletowa\:/myapp/app1", jest tożsamość aplikacji "moja_aplikacja\~app1" w wersji 6.0 + i "myapp/app1" w poprzednich wersjach. |
| --Nazwa węzła [wymagane] | Nazwa węzła. |
| --nazwa_usługi manifestu | Nazwa manifestu usługi, aby filtrować listę informacji o typie wdrożonej usługi. Jeśli zostanie określona, odpowiedź będzie zawierać tylko informacje dotyczące typów usług, które są zdefiniowane w tym manifeście usługi. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-service-description"></a>Opis usługi sfctl
Pobiera opis istniejącej usługi sieć szkieletowa usług.

Pobiera opis istniejącej usługi sieć szkieletowa usług. Usługi należy utworzyć przed jego opis można znaleźć.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator usługi [wymagane] | Tożsamość usługi. Zazwyczaj jest to pełna nazwa tej usługi bez ' sieci szkieletowej\:"schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchicznych nazwy są rozdzielane "\~" znaków. Na przykład, jeśli nazwa usługi jest "sieć szkieletowa\:/myapp/app1/svc1", jest tożsamość usługi "moja_aplikacja\~app1\~svc1" w wersji 6.0 + i "myapp/app1/svc1" w poprzednich wersjach. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-service-get-container-logs"></a>sfctl get kontenera — dzienniki usługi
Pobiera kontener dzienniki dla kontenera wdrożone w węźle sieci szkieletowej usług.

Pobiera kontener dzienniki dla kontenera wdrożone w węźle sieci szkieletowej usług pakietu podanego kodu.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator aplikacji [wymagane] | Tożsamość aplikacji. Zazwyczaj jest to pełna nazwa aplikacji bez ' sieci szkieletowej\:"schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchicznych nazwy są rozdzielane "\~" znaków. Na przykład, jeśli nazwa aplikacji jest "sieć szkieletowa\:/myapp/app1", jest tożsamość aplikacji "moja_aplikacja\~app1" w wersji 6.0 + i "myapp/app1" w poprzednich wersjach. |
| --kodu — pakiet — wymagana jest nazwa [] | Nazwa pakietu kodu określone w zarejestrowany jako część typu aplikacji w klastrze usługi sieć szkieletowa manifestu usługi. |
| --Nazwa węzła [wymagane] | Nazwa węzła. |
| --usługi manifest — wymagana jest nazwa [] | Nazwa manifestu usługi, w zarejestrowany jako część typu aplikacji w klastrze usługi sieć szkieletowa usług. |
| --poprzedniej | Określa, czy można pobrać dzienników kontenera z kontenerów zakończony wiadomości wystąpienia pakiet kodu. |
| --tail | Liczba wierszy do wyświetlenia na końcu dzienniki. Domyślna to 100. "wszystkie", aby wyświetlić pełną dzienników. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-service-health"></a>Kondycja usługi sfctl
Pobiera kondycji określonej usługi sieć szkieletowa usług.

Pobiera informacje o kondycji określonej usługi. Filtr EventsHealthStateFilter umożliwia filtrowanie zbierania zdarzeń kondycji zgłoszonych w usłudze oparte na stanie kondycji. Filtr PartitionsHealthStateFilter używany do filtrowania kolekcji zwrócił partycji. Jeśli określisz to usługa, która nie istnieje w magazynie kondycji to żądanie zwraca błąd.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator usługi [wymagane] | Tożsamość usługi. Zazwyczaj jest to pełna nazwa tej usługi bez ' sieci szkieletowej\:"schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchicznych nazwy są rozdzielane "\~" znaków. Na przykład, jeśli nazwa usługi jest "sieć szkieletowa\:/myapp/app1/svc1", jest tożsamość usługi "moja_aplikacja\~app1\~svc1" w wersji 6.0 + i "myapp/app1/svc1" w poprzednich wersjach. |
| --events-health-state-filter | Umożliwia filtrowanie kolekcji zwracanych obiektów HealthEvent oparte na stanie kondycji. Możliwe wartości tego parametru obejmują liczbę całkowitą jednego z następujących stanów kondycji. Zwracane są tylko zdarzenia, które są zgodne z filtrem. Wszystkie zdarzenia są używane do oceny stanu kondycji zagregowanych. Jeśli nie zostanie określona, zwracane są wszystkie wpisy. Wartości stanu są oparte na flagi wyliczenie, może to być kombinacją te wartości uzyskanych przy użyciu bitowego operatora "Lub". Na przykład jeśli podana wartość jest 6 następnie wszystkie zdarzenia o wartości atrybutu HealthState OK (2) i ostrzeżenia (4) są zwracane.  <br> -Domyślnie — wartość domyślna. Dopasowuje wszystkie właściwości HealthState. Wartość wynosi zero.  <br> -None - filtr, który nie odpowiada żadnej wartości właściwości HealthState. Używany, aby nie zwracała żadnych wyników w danej kolekcji stanów. Wartość to 1.  <br> -Ok - filtru, że dopasowań danych wejściowych o wartości atrybutu HealthState Ok. Wartość jest równa 2.  <br> — Ostrzeżenie - filtru, że dane wejściowe zgodna z atrybutem HealthState wartość ostrzeżenie. Wartość to 4.  <br> -Błąd filtru pasującego do danych wejściowych o wartości atrybutu HealthState błędu. Wartość jest 8.  <br> -All - filtru pasującego do danych wejściowych z dowolną wartością właściwości HealthState. Wartość jest 65535. |
| — Wyklucz kondycji statystyk | Wskazuje, czy statystyki kondycji powinny być zwracane w ramach wyniku zapytania. Wartość false, domyślnie. Statystyki zawierają liczbę elementów podrzędnych obiektów w kondycja Ok, ostrzeżeń i błędów. |
| --partitions-health-state-filter | Umożliwia filtrowanie obiekty stanu kondycji partycje zwrócone w wyniku zapytania kondycji usługi na podstawie ich stanu kondycji. Możliwe wartości tego parametru obejmują liczbę całkowitą jednego z następujących stanów kondycji. Zwracane są tylko partycje, które są zgodne z filtrem. Wszystkie partycje służą do oceny stanu kondycji zagregowanych. Jeśli nie zostanie określona, zwracane są wszystkie wpisy. Wartości stanu są oparte na flagi wyliczenie, może to być kombinacją te wartości uzyskanych przy użyciu bitowego operatora "Lub". Na przykład jeśli podana wartość jest 6 wtedy stan kondycji partycji o wartości atrybutu HealthState OK (2) i ostrzeżenia (4) zostanie zwrócony.  <br> -Domyślnie — wartość domyślna. Dopasowuje wszystkie właściwości HealthState. Wartość wynosi zero.  <br> -None - filtr, który nie odpowiada żadnej wartości właściwości HealthState. Używany, aby nie zwracała żadnych wyników w danej kolekcji stanów. Wartość to 1.  <br> -Ok - filtru, że dopasowań danych wejściowych o wartości atrybutu HealthState Ok. Wartość jest równa 2.  <br> — Ostrzeżenie - filtru, że dane wejściowe zgodna z atrybutem HealthState wartość ostrzeżenie. Wartość to 4.  <br> -Błąd filtru pasującego do danych wejściowych o wartości atrybutu HealthState błędu. Wartość jest 8.  <br> -All - filtru pasującego do danych wejściowych z dowolną wartością właściwości HealthState. Wartość jest 65535. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-service-info"></a>informacje o usłudze sfctl
Pobiera informacje o określonej usługi należące do aplikacji sieci szkieletowej usług.

Zwraca informacje o określonej usługi należące do określonej aplikacji sieci szkieletowej usług.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator aplikacji [wymagane] | Tożsamość aplikacji. Zazwyczaj jest to pełna nazwa aplikacji bez ' sieci szkieletowej\:"schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchicznych nazwy są rozdzielane "\~" znaków. Na przykład, jeśli nazwa aplikacji jest "sieć szkieletowa\:/myapp/app1", jest tożsamość aplikacji "moja_aplikacja\~app1" w wersji 6.0 + i "myapp/app1" w poprzednich wersjach. |
| — Identyfikator usługi [wymagane] | Tożsamość usługi. Zazwyczaj jest to pełna nazwa tej usługi bez ' sieci szkieletowej\:"schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchicznych nazwy są rozdzielane "\~" znaków. Na przykład, jeśli nazwa usługi jest "sieć szkieletowa\:/myapp/app1/svc1", jest tożsamość usługi "moja_aplikacja\~app1\~svc1" w wersji 6.0 + i "myapp/app1/svc1" w poprzednich wersjach. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-service-list"></a>Lista usług sfctl
Pobiera informacje o wszystkich usług należących do aplikacji określonej przez identyfikator aplikacji.

Zwraca informacje dotyczące wszystkich usług należących do aplikacji określonej przez identyfikator aplikacji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator aplikacji [wymagane] | Tożsamość aplikacji. Zazwyczaj jest to pełna nazwa aplikacji bez ' sieci szkieletowej\:"schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchicznych nazwy są rozdzielane "\~" znaków. Na przykład, jeśli nazwa aplikacji jest "sieć szkieletowa\:/myapp/app1", jest tożsamość aplikacji "moja_aplikacja\~app1" w wersji 6.0 + i "myapp/app1" w poprzednich wersjach. |
| --token kontynuacji | Parametr token kontynuacji służy do uzyskiwania następnej zestaw wyników. Token kontynuacji z niepustą wartość jest uwzględniana w odpowiedzi interfejsu API wyników z systemu nie mieszczą się w jednej odpowiedzi. Jeśli ta wartość jest przekazywany do następnego wywołania interfejsu API interfejsu API zwraca następny zestaw wyników. Jeśli nie są dalsze wyniki, token kontynuacji nie zawiera wartości. Wartość tego parametru nie powinny być zakodowane w adresie URL. |
| --service-type-name | Nazwa typu usługi używane do filtrowania usług na kwerendę. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-service-manifest"></a>sfctl manifestu usługi
Pobiera manifest opisujące typ usługi.

Pobiera manifest opisujące typ usługi. Odpowiedź zawiera manifest usługi XML jako ciąg.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --aplikacji typu nazwa-[wymagane] | Nazwa typu aplikacji. |
| --— Typ — wersja aplikacji [wymagane] | Wersja typu aplikacji. |
| --service-manifest-name    [Required] | Nazwa manifestu usługi, w zarejestrowany jako część typu aplikacji w klastrze usługi sieć szkieletowa usług. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-service-package-deploy"></a>Wdrażanie pakietu sfctl usługi
Pobiera pakiety skojarzone z określoną usługą manifestu do pamięci podręcznej obrazu na określony węzeł.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --aplikacji typu nazwa-[wymagane] | Nazwa manifest aplikacji dla manifest odpowiedniego żądanej usługi. |
| --— Typ — wersja aplikacji [wymagane] | Wersja aplikacji manifestu dla manifest odpowiedniego żądanej usługi. |
| --Nazwa węzła [wymagane] | Nazwa węzła. |
| --usługi manifest — wymagana jest nazwa [] | Nazwa manifestu usługi skojarzone z pakietami, które zostaną pobrane. |
| --udziału zasad | Lista zasad udostępniania zakodowane JSON. Każdy element udostępniania zasad składa się z "name" i "scope". Nazwa odpowiada nazwie pakietu kodu, konfiguracji lub danych, który ma zostać udostępniony. Zakres nie może być "None", "All", 'Kod', 'Config' lub 'Data'. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-service-package-health"></a>Kondycja pakietu sfctl usługi
Pobiera informacje o kondycji pakiet usługi dla określonej aplikacji wdrożone dla węzła sieci szkieletowej usług i aplikacji.

Pobiera informacje o kondycji usługi pakietu dla określonej aplikacji wdrożonych w węźle sieci szkieletowej usług. Filtr EventsHealthStateFilter umożliwia również filtrować dla kolekcji obiektów HealthEvent zgłoszone w pakiecie wdrożonej usługi na podstawie stanu kondycji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator aplikacji [wymagane] | Tożsamość aplikacji. Zazwyczaj jest to pełna nazwa aplikacji bez ' sieci szkieletowej\:"schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchicznych nazwy są rozdzielane "\~" znaków. Na przykład, jeśli nazwa aplikacji jest "sieć szkieletowa\:/myapp/app1", jest tożsamość aplikacji "moja_aplikacja\~app1" w wersji 6.0 + i "myapp/app1" w poprzednich wersjach. |
| --Nazwa węzła [wymagane] | Nazwa węzła. |
| — Pakiet nazw usług [wymagane] | Nazwa pakietu usługi. |
| --events-health-state-filter | Umożliwia filtrowanie kolekcji zwracanych obiektów HealthEvent oparte na stanie kondycji. Możliwe wartości tego parametru obejmują liczbę całkowitą jednego z następujących stanów kondycji. Zwracane są tylko zdarzenia, które są zgodne z filtrem. Wszystkie zdarzenia są używane do oceny stanu kondycji zagregowanych. Jeśli nie zostanie określona, zwracane są wszystkie wpisy. Wartości stanu są oparte na flagi wyliczenie, może to być kombinacją te wartości uzyskanych przy użyciu bitowego operatora "Lub". Na przykład jeśli podana wartość jest 6 następnie wszystkie zdarzenia o wartości atrybutu HealthState OK (2) i ostrzeżenia (4) są zwracane.  <br> -Domyślnie — wartość domyślna. Dopasowuje wszystkie właściwości HealthState. Wartość wynosi zero.  <br> -None - filtr, który nie odpowiada żadnej wartości właściwości HealthState. Używany, aby nie zwracała żadnych wyników w danej kolekcji stanów. Wartość to 1.  <br> -Ok - filtru, że dopasowań danych wejściowych o wartości atrybutu HealthState Ok. Wartość jest równa 2.  <br> — Ostrzeżenie - filtru, że dane wejściowe zgodna z atrybutem HealthState wartość ostrzeżenie. Wartość to 4.  <br> -Błąd filtru pasującego do danych wejściowych o wartości atrybutu HealthState błędu. Wartość jest 8.  <br> -All - filtru pasującego do danych wejściowych z dowolną wartością właściwości HealthState. Wartość jest 65535. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-service-package-info"></a>Usługa sfctl pakietu — informacje
Pobiera listę pakietów usług wdrożone w węźle sieci szkieletowej usług dopasowania określonej nazwy.

Zwraca informacje dotyczące pakietów usług wdrożone w węźle sieci szkieletowej usług dla danej aplikacji. Wyniki te pakietów usługi, którego nazwa odpowiada dokładnie nazwę pakietu usługi określono jako parametr.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator aplikacji [wymagane] | Tożsamość aplikacji. Zazwyczaj jest to pełna nazwa aplikacji bez ' sieci szkieletowej\:"schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchicznych nazwy są rozdzielane "\~" znaków. Na przykład, jeśli nazwa aplikacji jest "sieć szkieletowa\:/myapp/app1", jest tożsamość aplikacji "moja_aplikacja\~app1" w wersji 6.0 + i "myapp/app1" w poprzednich wersjach. |
| --Nazwa węzła [wymagane] | Nazwa węzła. |
| — Pakiet nazw usług [wymagane] | Nazwa pakietu usługi. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-service-package-list"></a>Lista pakietów sfctl usługi
Pobiera listę pakietów usług wdrożone w węźle sieci szkieletowej usług.

Zwraca informacje dotyczące pakietów usług wdrożone w węźle sieci szkieletowej usług dla danej aplikacji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator aplikacji [wymagane] | Tożsamość aplikacji. Zazwyczaj jest to pełna nazwa aplikacji bez ' sieci szkieletowej\:"schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchicznych nazwy są rozdzielane "\~" znaków. Na przykład, jeśli nazwa aplikacji jest "sieć szkieletowa\:/myapp/app1", jest tożsamość aplikacji "moja_aplikacja\~app1" w wersji 6.0 + i "myapp/app1" w poprzednich wersjach. |
| --Nazwa węzła [wymagane] | Nazwa węzła. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-service-recover"></a>Odzyskaj usługi sfctl
Wskazuje, aby klaster sieci szkieletowej usług mają podejmować próbę odzyskać określonej usługi, który jest obecnie zablokowane w wyniku utraty kworum.

Wskazuje, aby klaster sieci szkieletowej usług mają podejmować próbę odzyskać określonej usługi, który jest obecnie zablokowane w wyniku utraty kworum. Ta operacja powinna być wykonana tylko, jeśli wiadomo, że nie można odzyskać replik, które nie działają. Nieprawidłowe użycie tego interfejsu API może spowodować ryzyko utraty danych.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator usługi [wymagane] | Tożsamość usługi. Zazwyczaj jest to pełna nazwa tej usługi bez ' sieci szkieletowej\:"schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchicznych nazwy są rozdzielane "\~" znaków. Na przykład, jeśli nazwa usługi jest "sieć szkieletowa\:/myapp/app1/svc1", jest tożsamość usługi "moja_aplikacja\~app1\~svc1" w wersji 6.0 + i "myapp/app1/svc1" w poprzednich wersjach. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-service-report-health"></a>Kondycja raport sfctl usługi
Wysyła raport o kondycji usługi sieć szkieletowa usług.

Raporty kondycji określonej usługi sieć szkieletowa usług. Raport musi zawierać informacje o źródle raport o kondycji i właściwości, na którym będzie zgłaszane. Raport jest wysyłany do bramy usługi, która przekazuje do magazynu kondycji sieci szkieletowej usług. Raport może być akceptowane przez bramę, ale odrzucone przez magazynu kondycji po dodatkowej weryfikacji. Na przykład magazynu kondycji może odrzucić raportu z powodu nieprawidłowego parametru, takich jak numer sekwencyjny przestarzałe. Aby sprawdzić, czy raport został zastosowany w magazynie kondycji, sprawdź, czy raport jest wyświetlany w zdarzenia kondycji usługi.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --kondycji — właściwość [wymagane] | Właściwość informacje o kondycji. <br><br> Jednostka może mieć raportów o kondycji dla różnych właściwości. Właściwość jest ciągiem i nie stałej wyliczenia umożliwia elastyczność osoby zgłaszającej kategoryzację warunek stan raportu. Na przykład osoby zgłaszającej o ID "LocalWatchdog" można monitorować stan dysku w węźle, więc może raportować właściwości "AvailableDisk" w tym węźle. Tej samej osoby zgłaszającej można monitorować łączność węzła, więc może raportować właściwości "Łączności" w tym samym węźle. W magazynie kondycji te raporty są traktowane jako zdarzenia oddzielne kondycji dla określonego węzła. Wraz z SourceId właściwość unikatowo identyfikuje informacje o kondycji. |
| --Kondycja [wymagane] | Możliwe wartości\: "Nieprawidłowe", "Ok", "Ostrzeżenie", "Błąd", "Nieznany". |
| — Identyfikator usługi [wymagane] | Tożsamość usługi. <br><br> Zazwyczaj jest to pełna nazwa tej usługi bez ' sieci szkieletowej\:"schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchicznych nazwy są rozdzielane "\~" znaków. Na przykład, jeśli nazwa usługi jest ' sieci szkieletowej\:/myapp/app1/svc1 ", jest tożsamość usługi" moja_aplikacja\~app1\~svc1 "w wersji 6.0 + i" myapp/app1/svc1"w poprzednich wersjach. |
| — Identyfikator źródła [wymagane] | Nazwa źródła, która identyfikuje składnik klienta/programu alarmowego/system, który generuje informacje o kondycji. |
| — Opis | Opis informacji o kondycji. <br><br> Reprezentuje dowolny tekst służy do dodawania człowieka odczytać informacje na temat raportu. Maksymalna długość ciągu opisu jest znaków równą 4096. Jeśli podany string jest dłuższy, zostanie automatycznie obcięta. W przypadku obcięty, ostatnie znaki opisu zawiera znacznik "[Truncated]", a rozmiar całkowitą ciągu jest znaków równą 4096. Obecność znacznika wskazuje użytkownikom tego obcięcie wystąpił. Należy pamiętać, że gdy obcięty, opis ma mniej niż 4096 znaków z oryginalnego ciągu. |
| --bezpośrednim | Flaga wskazująca, czy mają być wysyłane raport natychmiast. <br><br> Raport o kondycji są wysyłane do aplikacji, która przekazuje do magazynu kondycji bramy sieci szkieletowej usług. Jeśli Immediate ma ustawioną wartość true, raport jest wysyłany bezpośrednio z bramy HTTP w magazynie kondycji, niezależnie od ustawień klienta sieci szkieletowej, których używa aplikacja bramy HTTP. Jest to przydatne w przypadku krytyczne raportów, które mają być wysyłane tak szybko, jak to możliwe. W zależności od czasu i innych warunków wysłaniem raportu może nadal się nie powieść, na przykład jeśli HTTP bramy został zamknięty lub komunikat nie nawiązać połączenia z bramą. Jeśli Immediate jest ustawiona na wartość false, raport jest wysyłany na podstawie kondycji ustawień klienta z bramy HTTP. W związku z tym go będzie można umieścić w partii zgodnie z konfiguracją HealthReportSendInterval. Jest to zalecane ustawienie ponieważ zezwala ona na kondycji klienta w celu zoptymalizowania wiadomości do magazynu kondycji, a także przetwarzania raportu kondycji raportowania kondycji. Domyślnie raporty nie są wysyłane bezpośrednio. |
| --ważność w przypadku usuwania | Wartość wskazująca, czy raport jest usuwany z magazynu kondycji po jego wygaśnięciu. <br><br> Jeśli ma wartość true, raport zostanie usunięty z magazynu kondycji po jego wygaśnięciu. Jeśli ma wartość false, raport jest traktowana jako błąd po wygaśnięciu. Wartość tej właściwości jest domyślnie false. Gdy klienci okresowo raport ustala RemoveWhenExpired FAŁSZ (ustawienie domyślne). W ten sposób jest reportera, który ma problemy (na przykład zakleszczenie) i nie można utworzyć raportu, obiekt jest oceniane w błąd, po wygaśnięciu raport o kondycji. Oznacza flagą jednostki jako błąd stanu kondycji. |
| --numer sekwencji | Numer sekwencji dla tego raportu kondycji jako ciąg numeryczny. <br><br> Numer sekwencyjny raportu jest używana przez magazynu kondycji do wykrywania starych raportów. Jeśli nie zostanie określony, numer kolejny został wygenerowany automatycznie przez klienta kondycji po dodaniu raportu. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |
| czas wygaśnięcia-- | Czas, przez jaki raport o kondycji są prawidłowe. To pole używa formatu ISO8601 służący do określania czasu trwania. <br><br> Gdy klienci okresowo raport one wysyłać raporty, z częstotliwością wyższe niż czas wygaśnięcia. Jeśli klienci raport dotyczący przejścia, ich ustawić czas wygaśnięcia do nieskończone. Po upływie czasu wygaśnięcia, zdarzenie kondycji, który zawiera informacje o kondycji jest usunięty z magazynu kondycji, jeśli RemoveWhenExpired jest true, lub na błąd, jeśli RemoveWhenExpired wartość false. Jeśli nie zostanie określony, czas wygaśnięcia wartości domyślnych do wartości nieskończonej. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-service-resolve"></a>Usuń usługi sfctl
Rozwiąż partycji usługi sieć szkieletowa usług.

Rozwiąż partycji usługi sieć szkieletowa usług można pobrać punkty końcowe repliki usługi.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator usługi [wymagane] | Tożsamość usługi. Zazwyczaj jest to pełna nazwa tej usługi bez ' sieci szkieletowej\:"schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchicznych nazwy są rozdzielane "\~" znaków. Na przykład, jeśli nazwa usługi jest "sieć szkieletowa\:/myapp/app1/svc1", jest tożsamość usługi "moja_aplikacja\~app1\~svc1" w wersji 6.0 + i "myapp/app1/svc1" w poprzednich wersjach. |
| --partycji klucza typu | Typ klucza partycji. Ten parametr jest wymagany w przypadku Int64Range lub nazwa schematu partycji dla usługi. Możliwe wartości są następujące. -Brak (1) — wskazuje, że nie określono parametru PartitionKeyValue. Jest to prawidłowa dla partycji z partycji schematu jako pojedyncza. Jest to wartość domyślna. Wartość to 1. -Int64Range (2) — wskazuje, że parametr PartitionKeyValue jest kluczem partycji int64. Jest to prawidłowa dla partycji zawierających schemat jako Int64Range partycji. Wartość jest równa 2. -O nazwie (3) — wskazuje, że parametr PartitionKeyValue jest nazwa partycji. Jest to prawidłowa dla partycji zawierających schemat jako nazwa partycji. Wartość to 3. |
| --wartości kluczy partycji | Klucz partycji. Jest to wymagane, jeśli Int64Range lub nazwa schematu partycji dla usługi. |
| --poprzedniej wersji źródło | Wartość w polu wersja odpowiedzi odebrany wcześniej. Jest to wymagane, jeśli użytkownik wie, że wynik, który został zaakceptujesz wcześniej jest przestarzała. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-service-type-list"></a>Lista typów sfctl usługi
Pobiera listę zawierającą informacje o typach usługi, które są obsługiwane przez typ elastycznie aplikacji w klastrze usługi sieć szkieletowa usług.

Pobiera listę zawierającą informacje o typach usługi, które są obsługiwane przez typ elastycznie aplikacji w klastrze usługi sieć szkieletowa usług. Typ aplikacji podane, musi istnieć. W przeciwnym razie zwracany jest stanu 404.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --aplikacji typu nazwa-[wymagane] | Nazwa typu aplikacji. |
| --— Typ — wersja aplikacji [wymagane] | Wersja typu aplikacji. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-service-update"></a>Aktualizacja usługi sfctl
Aktualizuje określonej usługi przy użyciu opisu danego aktualizacji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator usługi [wymagane] | Identyfikator usługi. Ten identyfikator jest zwykle pełną nazwę usługi bez ' sieci szkieletowej\:"schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchicznych nazwy są rozdzielane "\~" znaków. Na przykład, jeśli nazwa usługi jest ' sieci szkieletowej\:/myapp/app1/svc1 ", jest tożsamość usługi" moja_aplikacja\~app1\~svc1 "w wersji 6.0 + i" myapp/app1/svc1"w poprzednich wersjach. |
| — ograniczenia | Ograniczenia dotyczące umieszczania jako ciąg. Ograniczenia dotyczące umieszczania są wyrażeń logicznych na właściwości węzła i umożliwiają ograniczenie usługi dla określonych węzłów na podstawie wymagań usługi. Na przykład, aby umieścić usługi na węzłach, gdzie jest niebieska NodeType określ następujące\: "NodeColor == niebieski". |
| --correlated-service | Nazwa usługi docelowej, aby mieć związek z. |
| --Korelacja | Korelowanie usługę za pomocą istniejącej usługi za pomocą koligacji wyrównania. |
| — Liczba wystąpień | Liczba wystąpień. Dotyczy to tylko w przypadku usług bezstanowych. |
| --metryki obciążenia | Kodowany w formacie JSON listy metryki używane podczas ładowania równoważenia między węzłami. |
| --min-replica-set-size | Minimalna repliki Ustaw rozmiar jako liczba. Ten rozmiar zestawu dotyczą tylko usług stanowych. |
| --move-cost | Określa koszt przeniesienia dla usługi. Możliwe wartości to\: "Zero", "Od", "Średni", "High". |
| --placement-policy-list | Listę umieszczania zasad dla usługi kodowany w formacie JSON, wraz ze wszystkimi skojarzonymi nazw domen. Zasady mogą być co najmniej jeden\: `NonPartiallyPlaceService`, `PreferPrimaryDomain`, `RequireDomain`, `RequireDomainDistribution`. |
| --kworum utraty — oczekiwania | Maksymalny czas w sekundach, dla których partycji może być w stanie utraciła kworum. Ten czas trwania dotyczą tylko usług stanowych. |
| --replica-restart-wait | Czas w sekundach między gdy spadnie repliki i utworzenia nowej repliki. Ten czas trwania dotyczą tylko usług stanowych. |
| --Skalowanie zasad | Zakodowane JSON listy skalowanie zasad dla tej usługi. |
| --wstrzymania przez repliki zachować | Maksymalny czas trwania w sekundach, w których stan wstrzymania replik będzie obsługiwany przed usuwana. Ten czas trwania dotyczą tylko usług stanowych. |
| --stateful | Wskazuje, że Usługa docelowa jest usługi stanowej. |
| --bezstanowych | Wskazuje, że Usługa docelowa jest usługę bezstanową. |
| --docelowy —-rozmiar zestawu replik | Repliki docelowej Ustaw rozmiar jako liczba. Ten rozmiar zestawu dotyczą tylko usług stanowych. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="next-steps"></a>Kolejne kroki
- [Konfigurowanie](service-fabric-cli.md) interfejsu wiersza polecenia usługi sieci szkieletowej.
- Dowiedz się, jak używać przy użyciu interfejsu wiersza polecenia usługi sieć szkieletowa [przykładowe skrypty](/azure/service-fabric/scripts/sfctl-upgrade-application).

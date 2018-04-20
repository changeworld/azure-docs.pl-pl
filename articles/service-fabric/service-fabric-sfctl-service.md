---
title: Usługa sieci szkieletowej interfejsu wiersza polecenia - sfctl usługi Azure | Dokumentacja firmy Microsoft
description: Opis poleceń usługi sfctl interfejsu wiersza polecenia usługi sieci szkieletowej.
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
ms.date: 02/23/2018
ms.author: ryanwi
ms.openlocfilehash: 5b30d3732ff00e5bb79e2d58a9f0b3e5b29dedf8
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2018
---
# <a name="sfctl-service"></a>sfctl service
Tworzenie, usuwanie i zarządzanie usługi, typów usług i pakietów usług.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
|    Nazwa aplikacji       | Pobiera nazwę aplikacji sieci szkieletowej usług dla usługi.|
|    code-package-list | Pobiera listę pakietów kodu wdrożone w węźle sieci szkieletowej usług.|
|    create         | Tworzy określony usługi sieć szkieletowa usług z opisu.|
|    usuwanie         | Usuwa istniejącą usługę sieć szkieletowa usług.|
|    wdrożone typu  | Pobiera informacje o typie określonej usługi, aplikacji wdrożonych w węźle klastra sieci szkieletowej usług.|
|    wdrożony typ — listy| Pobiera listę zawierającą informacje o typach usługi z aplikacje wdrożone w węźle klastra sieci szkieletowej usług.|
|    description    | Pobiera opis istniejącej usługi sieć szkieletowa usług.|
|Get kontenera — dzienniki| Pobiera kontener dzienniki dla kontenera wdrożone w węźle sieci szkieletowej usług.|
|    kondycja         | Pobiera kondycji określonej usługi sieć szkieletowa usług.|
|    Informacje o           | Pobiera informacje o określonej usługi należące do aplikacji sieci szkieletowej usług.|
|    lista           | Pobiera informacje o wszystkich usług należących do aplikacji określonej przez identyfikator aplikacji.|
|    Manifest       | Pobiera manifest opisujące typ usługi.|
|    Wdrażanie pakietu | Pobiera pakiety skojarzone z określoną usługą manifestu do pamięci podręcznej obrazu na określony węzeł.|
|    package-health | Pobiera informacje o kondycji pakiet usługi dla określonej aplikacji wdrożone dla węzła sieci szkieletowej usług i aplikacji.|
|    package-info   | Pobiera listę pakietów usług wdrożone w węźle sieci szkieletowej usług dopasowania określonej nazwy.|
|    package-list   | Pobiera listę pakietów usług wdrożone w węźle sieci szkieletowej usług.|
|    Odzyskiwanie        | Wskazuje, aby klaster sieci szkieletowej usług mają podejmować próbę odzyskać określonej usługi, która jest obecnie zablokowane w wyniku utraty kworum.|
|    report-health  | Wysyła raport o kondycji usługi sieć szkieletowa usług.|
|    rozwiąż        | Rozwiąż partycji usługi sieć szkieletowa usług.|
|    Lista typów      | Pobiera listę zawierającą informacje o typach usługi, które są obsługiwane przez typ elastycznie aplikacji w klastrze usługi sieć szkieletowa usług.|
|    Aktualizacja         | Aktualizuje określonej usługi przy użyciu opisu danego aktualizacji.|


## <a name="sfctl-service-create"></a>Tworzenie usługi sfctl
Tworzy określony usługi sieć szkieletowa usług z opisu.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| [wymagane] - app-id| Tożsamość aplikacji nadrzędnej. Zazwyczaj jest to pełny identyfikator aplikacji bez "fabric:" schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchicznych nazwy są rozdzielane "~" znaków. Na przykład, jeśli nazwa aplikacji jest "sieci szkieletowej: / myapp/app1", jest tożsamość aplikacji "moja_aplikacja ~ app1" w wersji 6.0 + i "myapp/app1" w poprzednich wersjach.|
| --name [wymagane]| Nazwa usługi. Powinno to być elementem podrzędnym identyfikator aplikacji.           Jest to pełny nazwy w tym `fabric:` identyfikatora URI. Na przykład usługi `fabric:/A/B` jest elementem podrzędnym aplikacji `fabric:/A`.|
| — Typ usługi [wymagane]| Nazwa typu usługi.|
| --trybie aktywacji     | Tryb aktywacji dla pakietu usług.|
| — ograniczenia         | Ograniczenia dotyczące umieszczania jako ciąg. Ograniczenia dotyczące umieszczania są wyrażeń logicznych na właściwości węzła i umożliwiają ograniczenie usługi dla określonych węzłów na podstawie wymagań usługi. Na przykład, aby umieścić usługi na węzłach, gdzie jest niebieska NodeType określ następujące: "NodeColor == niebieski".|
| --correlated-service  | Nazwa usługi docelowej, aby mieć związek z.|
| --Korelacja         | Korelowanie usługę za pomocą istniejącej usługi za pomocą koligacji wyrównania.|
| --dns-name            | Nazwa DNS usługa ma zostać utworzony. To ustawienie, można włączyć usługę systemu DNS sieci szkieletowej usług.|
| — Liczba wystąpień      | Liczba wystąpień. Dotyczy to tylko w przypadku usług bezstanowych.|
| --int-scheme          | Wskazuje, że usługa powinna być jednolicie podzielonym na partycje w zakres liczb całkowitych bez znaku.|
| --int-scheme-count    | Liczba partycji wewnątrz liczb całkowitych klucza zakres (schemat partycji uniform liczba całkowita) można utworzyć.|
| --int-scheme-high     | Koniec zakresu klucza liczba całkowita, jeśli przy użyciu schematu partycji uniform liczby całkowitej.|
| --int-scheme-low      | Początek zakresu klucza liczba całkowita, jeśli przy użyciu schematu partycji uniform liczby całkowitej.|
| --metryki obciążenia        | Lista kodowany w formacie JSON metryki używane, gdy usług równoważenia obciążenia w węzłach.|
| --min-replica-set-size| Minimalna repliki Ustaw rozmiar jako liczba. Dotyczy to tylko usług stanowych.|
| --move-cost           | Określa koszt przeniesienia dla usługi. Możliwe wartości to: "0", "Od", "Średni", "High".|
| --schematu o nazwie        | Wskazuje, że usługa ma wiele partycji o nazwie.|
| --named-scheme-list   | Kodowany w formacie JSON listę nazw do partycjonowania usługi, jeśli przy użyciu schematu partycji o nazwie.|
| --nie utrwalony — stan  | Jeśli ma wartość true, wskazuje usługi nie ma trwałego stanu przechowywane na dysku lokalnym lub tylko zapisuje stan w pamięci.|
| --placement-policy-list  | Listę umieszczania zasad dla usługi kodowany w formacie JSON, wraz ze wszystkimi skojarzonymi nazw domen. Zasady mogą być co najmniej jeden z: `NonPartiallyPlaceService`, `PreferPrimaryDomain`, `RequireDomain`, `RequireDomainDistribution`.|
| --kworum utraty — oczekiwania    | Maksymalny czas w sekundach, dla których partycji może być w stanie utraciła kworum. Dotyczy to tylko usług stanowych.|
| --replica-restart-wait| Czas w sekundach między gdy spadnie repliki i utworzenia nowej repliki. Dotyczy to tylko usług stanowych.|
| --singleton schematu    | Wskazuje usługi powinny mieć jednej partycji lub być partycjonowane usługi z systemem innym niż.|
| --wstrzymania przez repliki zachować  | Maksymalny czas trwania w sekundach, w których stan wstrzymania repliki są obsługiwane przed usuwana. Dotyczy to tylko usług stanowych.|
| --stateful            | Wskazuje, że usługa jest usługą stanowych.|
| --bezstanowych           | Wskazuje, że usługa jest usługą bezstanowe.|
| --docelowy —-rozmiar zestawu replik| Repliki docelowej Ustaw rozmiar jako liczba. Dotyczy to tylko usług stanowych.|
| limit czasu — -t          | W sekundach limit czasu serwera.  Domyślnie: 60.|

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug               | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki.|
| — Pomoc -h             | Pokaż ten komunikat pomocy i Zakończ.|
| --output -o           | Format danych wyjściowych.  Dozwolone wartości: json, jsonc, tabeli, tsv.  Domyślne: json.|
| — zapytania               | Ciąg zapytania JMESPath. Aby uzyskać dodatkowe informacje i przykłady, zobacz http://jmespath.org/.|
| -verbose             | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna.|

## <a name="sfctl-service-delete"></a>Usuń usługi sfctl
Usuwa istniejącą usługę sieć szkieletowa usług.

Usuwa istniejącą usługę sieć szkieletowa usług. Usługa musi zostać utworzony, aby można było usunąć. Domyślnie usługa sieć szkieletowa próbuje zamknięcie repliki usługi w sposób bezpieczne, a następnie usuń usługę. Jeśli usługa ma problemy bezpiecznie zamykania repliki, operacja usuwania może zająć dużo czasu lub zostać zablokowane. Opcjonalna Flaga ForceRemove umożliwia Pomiń operację prawidłowego zamknięcia sekwencji i wymuszone Usuń usługę.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator usługi [wymagane]| Tożsamość usługi. Zazwyczaj jest to pełna nazwa tej usługi bez "fabric:" schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchicznych nazwy są rozdzielane "~" znaków. Na przykład, jeśli nazwa usługi jest fabric: / myapp/app1/svc1 ", będzie tożsamości usługi" moja_aplikacja ~ app1 ~ svc1 "w wersji 6.0 + i" myapp/app1/svc1"w poprzednich wersjach.|
| --force-remove      | Usuń sieć szkieletowa usług aplikacji lub usługi wymuszone bez pośrednictwa bezpiecznego zamknięcia sekwencji. Ten parametr może służyć do wymuszone usunięcie aplikacji lub usługi, dla których delete jest przekroczeniem limitu czasu z powodu problemów z kodem usługi, który uniemożliwia łagodne zamykanie replik.|
| limit czasu — -t        | W sekundach limit czasu serwera.  Domyślnie: 60.|

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug             | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki.|
| — Pomoc -h           | Pokaż ten komunikat pomocy i Zakończ.|
| --output -o         | Format danych wyjściowych.  Dozwolone wartości: json, jsonc, tabeli, tsv.  Domyślne: json.|
| — zapytania             | Ciąg zapytania JMESPath. Aby uzyskać dodatkowe informacje i przykłady, zobacz http://jmespath.org/.|
| -verbose           | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna.|

## <a name="sfctl-service-description"></a>Opis usługi sfctl
Pobiera opis istniejącej usługi sieć szkieletowa usług.

Pobiera opis istniejącej usługi sieć szkieletowa usług. Usługi należy utworzyć przed jego opis można znaleźć.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator usługi [wymagane]| Tożsamość usługi. Zazwyczaj jest to pełna nazwa tej usługi bez "fabric:" schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchicznych nazwy są rozdzielane "~" znaków. Na przykład, jeśli nazwa usługi jest "fabric: / myapp/app1/svc1", będzie tożsamość usługi "moja_aplikacja ~ app1 ~ svc1" w wersji 6.0 + i "myapp/app1/svc1" w poprzednich wersjach.|
| limit czasu — -t        | W sekundach limit czasu serwera.  Domyślnie: 60.|

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug             | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki.|
| — Pomoc -h           | Pokaż ten komunikat pomocy i Zakończ.|
| --output -o         | Format danych wyjściowych.  Dozwolone wartości: json, jsonc, tabeli, tsv.  Domyślne: json.|
| — zapytania             | Ciąg zapytania JMESPath. Aby uzyskać dodatkowe informacje i przykłady, zobacz http://jmespath.org/.|
| -verbose           | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna.|

## <a name="sfctl-service-health"></a>Kondycja usługi sfctl
Pobiera kondycji określonej usługi sieć szkieletowa usług.

Pobiera informacje o kondycji określonej usługi. Filtr EventsHealthStateFilter umożliwia filtrowanie zbierania zdarzeń kondycji zgłoszonych w usłudze oparte na stanie kondycji. Filtr PartitionsHealthStateFilter używany do filtrowania kolekcji zwrócił partycji. To polecenie cmdlet zwraca błąd, jeśli określono to usługa, która nie istnieje w magazynie kondycji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator usługi [wymagane]| Tożsamość usługi. Zazwyczaj jest to pełna nazwa tej usługi bez "fabric:" schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchicznych nazwy są rozdzielane "~" znaków. Na przykład, jeśli nazwa usługi jest "fabric: / myapp/app1/svc1", będzie tożsamość usługi "moja_aplikacja ~ app1 ~ svc1" w wersji 6.0 + i "myapp/app1/svc1" w poprzednich wersjach.|
| --events-health-state-filter | Umożliwia filtrowanie kolekcji zwracanych obiektów HealthEvent oparte na stanie kondycji. Możliwe wartości tego parametru obejmują liczbę całkowitą jednego z następujących stanów kondycji. Zwracane są tylko zdarzenia, które są zgodne z filtrem. Wszystkie zdarzenia są używane do oceny stanu kondycji zagregowanych. Jeśli nie zostanie określona, zwracane są wszystkie wpisy. Wartości stanu są oparte na flagi wyliczenie, może to być kombinacją te wartości uzyskanych przy użyciu bitowego operatora "Lub". Na przykład jeśli podana wartość jest 6 następnie wszystkie zdarzenia o wartości atrybutu HealthState OK (2) i ostrzeżenia (4) są zwracane. -Domyślnie — wartość domyślna. Dopasowuje wszystkie właściwości HealthState. Wartość wynosi zero. -None - filtr, który nie odpowiada żadnej wartości właściwości HealthState. Używany, aby nie zwracała żadnych wyników w danej kolekcji stanów. Wartość to 1. -Ok - filtru, że dopasowań danych wejściowych o wartości atrybutu HealthState Ok. Wartość jest równa 2. — Ostrzeżenie - filtru, że dane wejściowe zgodna z atrybutem HealthState wartość ostrzeżenie. Wartość to 4. -Błąd filtru pasującego do danych wejściowych o wartości atrybutu HealthState błędu. Wartość jest 8. -All - filtru pasującego do danych wejściowych z dowolną wartością właściwości HealthState. Wartość jest 65535.|
|— Wyklucz kondycji statystyk     | Wskazuje, czy statystyki kondycji powinny być zwracane w ramach wyniku zapytania. Wartość false, domyślnie. Statystyki zawierają liczbę elementów podrzędnych obiektów w kondycja Ok, ostrzeżeń i błędów.|
| --partitions-health-state-filter| Umożliwia filtrowanie obiekty stanu kondycji partycje zwrócone w wyniku zapytania kondycji usługi na podstawie ich stanu kondycji. Możliwe wartości tego parametru obejmują liczbę całkowitą jednego z następujących stanów kondycji. Zwracane są tylko partycje, które są zgodne z filtrem. Wszystkie partycje służą do oceny stanu kondycji zagregowanych. Jeśli nie zostanie określona, zwracane są wszystkie wpisy. Wartości stanu są oparte na flagi wyliczenie, może to być kombinacją te wartości uzyskanych przy użyciu bitowego operatora "Lub". Na przykład jeśli podana wartość jest "6" następnie kondycja partycji o wartości atrybutu HealthState OK (2) i ostrzeżenia (4) są zwracane. -Domyślnie — wartość domyślna. Dopasowuje wszystkie właściwości HealthState.                  Wartość wynosi zero. -None - filtr, który nie odpowiada żadnej wartości właściwości HealthState. Używany, aby nie zwracała żadnych wyników w danej kolekcji stanów. Wartość to 1. -Ok - filtru, że dopasowań danych wejściowych o wartości atrybutu HealthState Ok. Wartość jest równa 2. — Ostrzeżenie - filtru, że dane wejściowe zgodna z atrybutem HealthState wartość ostrzeżenie. Wartość to 4. -Błąd filtru pasującego do danych wejściowych o wartości atrybutu HealthState błędu. Wartość jest 8. -All - filtru pasującego do danych wejściowych z dowolną wartością właściwości HealthState. Wartość jest 65535.|
| limit czasu — -t                 | W sekundach limit czasu serwera.  Domyślnie: 60.|

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug                      | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki.|
| — Pomoc -h                    | Pokaż ten komunikat pomocy i Zakończ.|
| --output -o                  | Format danych wyjściowych.  Dozwolone wartości: json, jsonc, tabeli, tsv.                  Domyślne: json.|
| — zapytania                      | Ciąg zapytania JMESPath. Zobacz http://jmespath.org/ dodatkowe informacje i przykłady.|
| -verbose                    | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna.|

## <a name="sfctl-service-info"></a>informacje o usłudze sfctl
Pobiera informacje o określonej usługi należące do aplikacji sieci szkieletowej usług.

Zwraca informacje o określonej usługi należące do określonej aplikacji sieci szkieletowej usług.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator aplikacji [wymagane]| Tożsamość aplikacji. Zazwyczaj jest to pełna nazwa aplikacji bez "fabric:" schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchicznych nazwy są rozdzielane "~" znaków. Na przykład, jeśli nazwa aplikacji jest "fabric: / myapp/app1", jest tożsamość aplikacji "moja_aplikacja ~ app1" w wersji 6.0 + i "myapp/app1" w poprzednich wersjach.|
| — Identyfikator usługi [wymagane]| Tożsamość usługi. Zazwyczaj jest to pełna nazwa tej usługi bez "fabric:" schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchicznych nazwy są rozdzielane "~" znaków. Na przykład, jeśli nazwa usługi jest "fabric: / myapp/app1/svc1", będzie tożsamość usługi "moja_aplikacja ~ app1 ~ svc1" w wersji 6.0 + i "myapp/app1/svc1" w poprzednich wersjach.|
| limit czasu — -t            | W sekundach limit czasu serwera.  Domyślnie: 60.|

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug                 | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki.|
| — Pomoc -h               | Pokaż ten komunikat pomocy i Zakończ.|
| --output -o             | Format danych wyjściowych.  Dozwolone wartości: json, jsonc, tabeli, tsv.  Domyślne: json.|
| — zapytania                 | Ciąg zapytania JMESPath. Aby uzyskać dodatkowe informacje i przykłady, zobacz http://jmespath.org/.|
| -verbose               | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna.|

## <a name="sfctl-service-list"></a>Lista usług sfctl
Pobiera informacje o wszystkich usług należących do aplikacji określonej przez identyfikator aplikacji.

Zwraca informacje dotyczące wszystkich usług należących do aplikacji określonej przez identyfikator aplikacji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator aplikacji [wymagane]| Tożsamość aplikacji. Zazwyczaj jest to pełna nazwa aplikacji bez "fabric:" schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchicznych nazwy są rozdzielane "~" znaków. Na przykład, jeśli nazwa aplikacji jest "fabric: / myapp/app1", jest tożsamość aplikacji "moja_aplikacja ~ app1" w wersji 6.0 + i "myapp/app1" w poprzednich wersjach.|
| --token kontynuacji    | Parametr token kontynuacji służy do uzyskiwania następnej zestaw wyników. Token kontynuacji z wartość pusta jest uwzględniana w odpowiedzi interfejsu API wyników z systemu nie mieszczą się w jednej odpowiedzi. Jeśli ta wartość jest przekazywany do następnego wywołania interfejsu API interfejsu API zwraca następny zestaw wyników. Jeśli nie są dalsze wyniki, token kontynuacji nie zawiera wartości. Wartość tego parametru nie powinny być zakodowane w adresie URL.|
| --service-type-name     | Nazwa typu usługi używane do filtrowania usług na kwerendę.|
| limit czasu — -t            | W sekundach limit czasu serwera.  Domyślnie: 60.|

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug                 | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki.|
| — Pomoc -h               | Pokaż ten komunikat pomocy i Zakończ.|
| --output -o             | Format danych wyjściowych.  Dozwolone wartości: json, jsonc, tabeli, tsv.  Domyślne: json.|
| — zapytania                 | Ciąg zapytania JMESPath. Aby uzyskać dodatkowe informacje i przykłady, zobacz http://jmespath.org/.|
| -verbose               | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna.|

## <a name="sfctl-service-manifest"></a>sfctl manifestu usługi
Pobiera manifest opisujące typ usługi.

Pobiera manifest opisujące typ usługi. Odpowiedź zawiera manifest usługi XML jako ciąg.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --aplikacji typu nazwa-[wymagane]| Nazwa typu aplikacji.|
| --— Typ — wersja aplikacji [wymagane]| Wersja typu aplikacji.|
| --service-manifest-name    [Required]| Nazwa manifestu usługi, w zarejestrowany jako część typu aplikacji w klastrze usługi sieć szkieletowa usług.|
| limit czasu — -t                      | W sekundach limit czasu serwera.  Domyślnie: 60.|

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug                           | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki.|
| — Pomoc -h                         | Pokaż ten komunikat pomocy i Zakończ.|
| --output -o                       | Format danych wyjściowych.  Dozwolone wartości: json, jsonc, tabeli, tsv.                       Domyślne: json.|
| — zapytania                           | Ciąg zapytania JMESPath. Zobacz http://jmespath.org/ dodatkowe informacje i przykłady.|
| -verbose                         | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna.|

## <a name="sfctl-service-recover"></a>Odzyskaj usługi sfctl
Wskazuje, aby klaster sieci szkieletowej usług mają podejmować próbę odzyskać określonej usługi, która jest obecnie zablokowane w wyniku utraty kworum.

Wskazuje, aby klaster sieci szkieletowej usług mają podejmować próbę odzyskać określonej usługi, która jest obecnie zablokowane w wyniku utraty kworum. Tej operacji należy można wykonać tylko, jeśli nie będzie można odzyskać replik, które nie działają. Nieprawidłowe użycie tego interfejsu API może spowodować ryzyko utraty danych.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator usługi [wymagane]| Tożsamość usługi. Zazwyczaj jest to pełna nazwa tej usługi bez "fabric:" schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchicznych nazwy są rozdzielane "~" znaków. Na przykład, jeśli nazwa usługi jest fabric: / myapp/app1/svc1 ", będzie tożsamości usługi" moja_aplikacja ~ app1 ~ svc1 "w wersji 6.0 + i" myapp/app1/svc1"w poprzednich wersjach.|
| limit czasu — -t        | W sekundach limit czasu serwera.  Domyślnie: 60.|

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug             | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki.|
| — Pomoc -h           | Pokaż ten komunikat pomocy i Zakończ.|
| --output -o         | Format danych wyjściowych.  Dozwolone wartości: json, jsonc, tabeli, tsv.  Domyślne: json.|
| — zapytania             | Ciąg zapytania JMESPath. Aby uzyskać dodatkowe informacje i przykłady, zobacz http://jmespath.org/.|
| -verbose           | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna.|

## <a name="sfctl-service-resolve"></a>Usuń usługi sfctl
Rozwiąż partycji usługi sieć szkieletowa usług.

Rozwiąż partycji usługi sieć szkieletowa usług, można pobrać punkty końcowe repliki usługi.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator usługi [wymagane]| Tożsamość usługi. Zazwyczaj jest to pełna nazwa tej usługi bez "fabric:" schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchicznych nazwy są rozdzielane "~" znaków. Na przykład, jeśli nazwa usługi jest "fabric: / myapp/app1/svc1", będzie tożsamość usługi "moja_aplikacja ~ app1 ~ svc1" w wersji 6.0 + i "myapp/app1/svc1" w poprzednich wersjach.|
| --partycji klucza typu| Typ klucza partycji. Ten parametr jest wymagany w przypadku Int64Range lub nazwa schematu partycji dla usługi. Możliwe wartości są następujące. -Brak (1) — wskazuje, że nie określono parametru PartitionKeyValue. Jest to prawidłowa dla partycji z partycji schematu jako pojedyncza. Jest to wartość domyślna. Wartość to 1. -Int64Range (2) — wskazuje, że parametr PartitionKeyValue jest kluczem partycji int64. Jest to prawidłowa dla partycji zawierających schemat jako Int64Range partycji. Wartość jest równa 2. -O nazwie (3) — wskazuje, że parametr PartitionKeyValue jest nazwa partycji. Jest to prawidłowa dla partycji zawierających schemat jako nazwa partycji. Wartość to 3.|
| --wartości kluczy partycji  | Klucz partycji. Jest to wymagane, jeśli Int64Range lub nazwa schematu partycji dla usługi.|
| --poprzedniej wersji źródło | Wartość w polu wersja odpowiedzi odebrany wcześniej. Jest to wymagane, jeśli użytkownik wie, że wynik, który został uzyskano wcześniej jest przestarzała.|
| limit czasu — -t        | W sekundach limit czasu serwera.  Domyślnie: 60.|

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug             | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki.|
| — Pomoc -h           | Pokaż ten komunikat pomocy i Zakończ.|
| --output -o         | Format danych wyjściowych.  Dozwolone wartości: json, jsonc, tabeli, tsv.  Domyślne: json.|
| — zapytania             | Ciąg zapytania JMESPath. Aby uzyskać dodatkowe informacje i przykłady, zobacz http://jmespath.org/.|
| -verbose           | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna.|

## <a name="sfctl-service-update"></a>Aktualizacja usługi sfctl
Aktualizuje określonej usługi przy użyciu opisu danego aktualizacji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator usługi [wymagane]| Usługa docelowa do aktualizacji. Zazwyczaj jest to pełny identyfikator usługi bez "fabric:" schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchicznych nazwy są rozdzielane "~" znaków. Na przykład, jeśli nazwa usługi jest ' sieci szkieletowej: / myapp/app1/svc1 ", jest tożsamość usługi" moja_aplikacja ~ app1 ~ svc1 "w wersji 6.0 + i" myapp/app1/svc1"w poprzednich wersjach.|
| — ograniczenia         | Ograniczenia dotyczące umieszczania jako ciąg. Ograniczenia dotyczące umieszczania są wyrażeń logicznych na właściwości węzła i umożliwiają ograniczenie usługi dla określonych węzłów na podstawie wymagań usługi. Na przykład, aby umieścić usługi na węzłach, gdzie jest niebieska NodeType określ następujące: "NodeColor == niebieski".|
| --correlated-service  | Nazwa usługi docelowej, aby mieć związek z.|
| --Korelacja         | Korelowanie usługę za pomocą istniejącej usługi za pomocą koligacji wyrównania.|
| — Liczba wystąpień      | Liczba wystąpień. Dotyczy to tylko w przypadku usług bezstanowych.|
| --metryki obciążenia        | Kodowany w formacie JSON listy metryki używane podczas ładowania równoważenia między węzłami.|
| --min-replica-set-size| Minimalna repliki Ustaw rozmiar jako liczba. Dotyczy to tylko usług stanowych.|
| --move-cost           | Określa koszt przeniesienia dla usługi. Możliwe wartości to: "0", "Od", "Średni", "High".|
| --placement-policy-list  | Listę umieszczania zasad dla usługi kodowany w formacie JSON, wraz ze wszystkimi skojarzonymi nazw domen. Zasady mogą być co najmniej jeden z: `NonPartiallyPlaceService`, `PreferPrimaryDomain`, `RequireDomain`, `RequireDomainDistribution`.|
| --kworum utraty — oczekiwania    | Maksymalny czas w sekundach, dla których partycji może być w stanie utraciła kworum. Dotyczy to tylko usług stanowych.|
| --replica-restart-wait| Czas w sekundach między gdy spadnie repliki i utworzenia nowej repliki. Dotyczy to tylko usług stanowych.|
| --wstrzymania przez repliki zachować  | Maksymalny czas trwania w sekundach, w których stan wstrzymania repliki są obsługiwane przed usuwana. Dotyczy to tylko usług stanowych.|
| --stateful            | Wskazuje, że Usługa docelowa jest usługi stanowej.|
| --bezstanowych           | Wskazuje, że Usługa docelowa jest usługę bezstanową.|
| --docelowy —-rozmiar zestawu replik| Repliki docelowej Ustaw rozmiar jako liczba. Dotyczy to tylko usług stanowych.|
| limit czasu — -t          | W sekundach limit czasu serwera.  Domyślnie: 60.|

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug               | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki.|
| — Pomoc -h             | Pokaż ten komunikat pomocy i Zakończ.|
| --output -o           | Format danych wyjściowych.  Dozwolone wartości: json, jsonc, tabeli, tsv.  Domyślne: json.|
| — zapytania               | Ciąg zapytania JMESPath. Aby uzyskać dodatkowe informacje i przykłady, zobacz http://jmespath.org/.|
| -verbose             | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna.|

## <a name="next-steps"></a>Kolejne kroki
- [Konfigurowanie](service-fabric-cli.md) interfejsu wiersza polecenia usługi sieci szkieletowej.
- Dowiedz się, jak używać przy użyciu interfejsu wiersza polecenia usługi sieć szkieletowa [przykładowe skrypty](/azure/service-fabric/scripts/sfctl-upgrade-application).

---
title: Azure Usługa sieci szkieletowej interfejsu wiersza polecenia sfctl węzłami | Dokumentacja firmy Microsoft
description: Informacje dotyczące poleceń węzła sfctl interfejsu wiersza polecenia usługi sieci szkieletowej.
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
ms.date: 02/22/2018
ms.author: ryanwi
ms.openlocfilehash: 50c7fe38d8bf7b14adf437f85c758e465e7d231d
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2018
---
# <a name="sfctl-node"></a>sfctl node
Zarządzanie węzły, które tworzą klaster.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
|    wyłącz       | Dezaktywowanie z docelowa dezaktywacji określonego węzła klastra sieci szkieletowej usług.|
|    włącz        | Aktywuj węzłem klastra usługi sieć szkieletowa jest obecnie wyłączona.|
|    kondycja        | Pobiera stan węzła sieci szkieletowej usług.|
|    Informacje o          | Pobiera informacje dotyczące określonego węzła w klastrze usługi sieć szkieletowa usług.|
|    lista          | Pobiera listę węzłów w klastrze usługi sieć szkieletowa usług.|
|    ładowanie          | Pobiera informacje o obciążenia węzła sieci szkieletowej usług.|
|    remove-state  | Powiadamia sieci szkieletowej usług, że stan utrwalony na węźle trwale ani nie usunięto utracone.|
|    report-health | Wysyła raport o kondycji w węźle sieci szkieletowej usług.|
|    Ponowne uruchomienie       | Ponowne uruchomienie węzła klastra sieci szkieletowej usług.|
|    przejścia    | Uruchomienia lub zatrzymania węzła klastra.|
|    stan przejścia| Pobiera postęp operacji uruchomiony przy użyciu StartNodeTransition.|


## <a name="sfctl-node-disable"></a>Wyłącz węzeł sfctl
Dezaktywowanie z docelowa dezaktywacji określonego węzła klastra sieci szkieletowej usług.

Dezaktywowanie z docelowa dezaktywacji określonego węzła klastra sieci szkieletowej usług. Po dezaktywacji jest w toku, docelowa dezaktywacji mogą być zwiększona, ale nie zmniejszyć (na przykład węzła, który jest dezaktywowana z zamiarem Wstrzymaj można dezaktywować dalsze o ponowne uruchomienie, ale nie odwrotnie. Węzły mogą ponownie aktywować przy użyciu Aktywuj operacji węzła po ich zostaną wyłączone. Jeśli dezaktywację nie została ukończona to anuluje dezaktywację. Węzeł ulegnie awarii, a następnie wróci do sprawności podczas dezaktywowane nadal konieczna będzie ponownie aktywować przed usług można umieścić w tym węźle.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Nazwa węzła [wymagane]| Nazwa węzła.|
| --docelowa dezaktywacji | Opisuje zamierzone lub powód dezaktywacji węzła. |
| limit czasu — -t       | W sekundach limit czasu serwera.  Domyślnie: 60.|

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug            | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki.|
| — Pomoc -h          | Pokaż ten komunikat pomocy i Zakończ.|
| --output -o        | Format danych wyjściowych.  Dozwolone wartości: json, jsonc, tabeli, tsv.  Domyślne: json.|
| — zapytania            | Ciąg zapytania JMESPath. Aby uzyskać dodatkowe informacje i przykłady, zobacz http://jmespath.org/.|
| -verbose          | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna.|

## <a name="sfctl-node-enable"></a>Włącz węzła sfctl
Aktywuj węzła klastra sieci szkieletowej usług, który jest obecnie wyłączona.

Aktywuje węzła klastra sieci szkieletowej usług, który jest obecnie wyłączona. Po aktywowana, węzeł ponownie staje się działało celu wprowadzania nowych replik, a wszystkie repliki dezaktywowane na węźle są ponownie aktywować.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Nazwa węzła [wymagane]| Nazwa węzła.|
| limit czasu — -t       | W sekundach limit czasu serwera.  Domyślnie: 60.|

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug            | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki.|
| — Pomoc -h          | Pokaż ten komunikat pomocy i Zakończ.|
| --output -o        | Format danych wyjściowych.  Dozwolone wartości: json, jsonc, tabeli, tsv.  Domyślne: json.|
| — zapytania            | Ciąg zapytania JMESPath. Aby uzyskać dodatkowe informacje i przykłady, zobacz http://jmespath.org/.|
| -verbose          | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna.|

## <a name="sfctl-node-health"></a>sfctl węzła kondycji
Pobiera stan węzła sieci szkieletowej usług.

Pobiera stan węzła sieci szkieletowej usług. Filtr EventsHealthStateFilter umożliwia filtrowanie zbierania zdarzeń kondycji zgłoszonych w węźle na podstawie stanu kondycji. Jeśli węzeł, który jest określany na podstawie nazwy nie istnieje w magazynie kondycji, to zwróci błąd.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Nazwa węzła [wymagane]| Nazwa węzła.|
| --events-health-state-filter| Umożliwia filtrowanie kolekcji zwracanych obiektów HealthEvent oparte na stanie kondycji. Możliwe wartości tego parametru obejmują liczbę całkowitą jednego z następujących stanów kondycji. Zwracane są tylko zdarzenia, które są zgodne z filtrem. Wszystkie zdarzenia są używane do oceny stanu kondycji zagregowanych. Jeśli nie zostanie określona, zwracane są wszystkie wpisy. Wartości stanu są oparte na flagi wyliczenie, może to być kombinacją te wartości uzyskanych przy użyciu bitowego operatora "Lub". Na przykład jeśli podana wartość jest 6 następnie wszystkie zdarzenia o wartości atrybutu HealthState OK (2) i ostrzeżenia (4) są zwracane. -Domyślnie — wartość domyślna. Dopasowuje wszystkie właściwości HealthState. Wartość wynosi zero. -None - filtr, który nie odpowiada żadnej wartości właściwości HealthState. Używany, aby nie zwracała żadnych wyników w danej kolekcji stanów. Wartość to 1. -Ok - filtru, że dopasowań danych wejściowych o wartości atrybutu HealthState Ok. Wartość jest równa 2. — Ostrzeżenie - filtru, że dane wejściowe zgodna z atrybutem HealthState wartość ostrzeżenie. Wartość to 4. -Błąd filtru pasującego do danych wejściowych o wartości atrybutu HealthState błędu. Wartość jest 8. -All - filtru pasującego do danych wejściowych z dowolną wartością właściwości HealthState. Wartość jest 65535.|
| limit czasu — -t             | W sekundach limit czasu serwera.  Domyślnie: 60.|

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug                  | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki.|
| — Pomoc -h                | Pokaż ten komunikat pomocy i Zakończ.|
| --output -o              | Format danych wyjściowych.  Dozwolone wartości: json, jsonc, tabeli, tsv.  Domyślne: json.|
| — zapytania                  | Ciąg zapytania JMESPath. Zobacz http://jmespath.org/ dodatkowe informacje i przykłady.|
| -verbose                | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna.|

## <a name="sfctl-node-info"></a>informacje o węźle sfctl
Pobiera informacje dotyczące określonego węzła w klastrze usługi sieć szkieletowa usług.

Pobiera informacje o określonym węźle Cluster.The sieci szkieletowej usług odpowiedzi to nazwa, stan, identyfikator, kondycji, czas działania i inne szczegóły dotyczące węzła.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Nazwa węzła [wymagane]| Nazwa węzła.|
| limit czasu — -t       | W sekundach limit czasu serwera.  Domyślnie: 60.|

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug            | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki.|
| — Pomoc -h          | Pokaż ten komunikat pomocy i Zakończ.|
| --output -o        | Format danych wyjściowych.  Dozwolone wartości: json, jsonc, tabeli, tsv.  Domyślne: json.|
| — zapytania            | Ciąg zapytania JMESPath. Aby uzyskać dodatkowe informacje i przykłady, zobacz http://jmespath.org/.|
| -verbose          | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna.|

## <a name="sfctl-node-list"></a>sfctl węzła listy
Pobiera listę węzłów w klastrze usługi sieć szkieletowa usług.

Pobiera listę węzłów w klastrze usługi sieć szkieletowa usług. Odpowiedź zawierać nazwę, stan, identyfikator, kondycji, czas pracy i inne szczegóły dotyczące węzła.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --token kontynuacji| Parametr token kontynuacji służy do uzyskiwania następnej zestaw wyników. Token kontynuacji z wartość pusta jest uwzględniana w odpowiedzi interfejsu API wyników z systemu nie mieszczą się w jednej odpowiedzi.      Jeśli ta wartość jest przekazywany do następnego wywołania interfejsu API interfejsu API zwraca następny zestaw wyników. Jeśli nie są dalsze wyniki, token kontynuacji nie zawiera wartości. Wartość tego parametru nie powinny być zakodowane w adresie URL.|
| --node-status-filter| Umożliwia filtrowanie oparte na NodeStatus węzłów. Zwracane są tylko węzły, które są zgodne z wartością określony filtr. Wartość filtru może być jedną z następujących czynności. Domyślne: domyślny.|
| limit czasu — -t     | W sekundach limit czasu serwera.  Domyślnie: 60.|

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug          | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki.|
| — Pomoc -h        | Pokaż ten komunikat pomocy i Zakończ.|
| --output -o      | Format danych wyjściowych.  Dozwolone wartości: json, jsonc, tabeli, tsv.  Domyślne: json.|
| — zapytania          | Ciąg zapytania JMESPath. Aby uzyskać dodatkowe informacje i przykłady, zobacz http://jmespath.org/.|
| -verbose        | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna.|

## <a name="sfctl-node-load"></a>sfctl węzła obciążenia
Pobiera informacje o obciążenia węzła sieci szkieletowej usług.

Pobiera informacje o obciążenia węzła sieci szkieletowej usług dla wszystkich metryki obciążenia lub zdefiniowano zdolności produkcyjnych.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Nazwa węzła [wymagane]| Nazwa węzła.|
| limit czasu — -t       | W sekundach limit czasu serwera.  Domyślnie: 60.|

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug            | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki.|
| — Pomoc -h          | Pokaż ten komunikat pomocy i Zakończ.|
| --output -o        | Format danych wyjściowych.  Dozwolone wartości: json, jsonc, tabeli, tsv.  Domyślne: json.|
| — zapytania            | Ciąg zapytania JMESPath. Aby uzyskać dodatkowe informacje i przykłady, zobacz http://jmespath.org/.|
| -verbose          | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna.|

## <a name="sfctl-node-restart"></a>ponowne uruchomienie węzła sfctl
Ponowne uruchomienie węzła klastra sieci szkieletowej usług.

Uruchamia ponownie usługi sieć szkieletowa węzła klastra, który jest już uruchomiony.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Nazwa węzła [wymagane]| Nazwa węzła.|
| — Utwórz — sieci szkieletowej — zrzutu  | Określ wartość True, aby utworzyć zrzutu procesu węzła sieci szkieletowej. To jest rozróżniana wielkość liter.  Domyślnie: False.|
| --node-instance-id | Identyfikator wystąpienia węzła docelowego. Jeśli identyfikator wystąpienia jest określony węzeł jest uruchomiony ponownie tylko wtedy, gdy jest on zgodny z bieżącym wystąpieniem węzła. Wartość domyślna "0" spowoduje dopasowanie dowolnej identyfikatora wystąpienia. Identyfikator wystąpienia można uzyskać za pomocą get węzła zapytania.  Domyślna: 0.|
| limit czasu — -t       | W sekundach limit czasu serwera.  Domyślnie: 60.|

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug            | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki.|
| — Pomoc -h          | Pokaż ten komunikat pomocy i Zakończ.|
| --output -o        | Format danych wyjściowych.  Dozwolone wartości: json, jsonc, tabeli, tsv.  Domyślne: json.|
| — zapytania            | Ciąg zapytania JMESPath. Aby uzyskać dodatkowe informacje i przykłady, zobacz http://jmespath.org/.|
| -verbose          | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna.|

## <a name="sfctl-node-transition"></a>Przejście węzła sfctl
Uruchomienia lub zatrzymania węzła klastra.

Uruchomienia lub zatrzymania węzła klastra.  Węzeł klastra jest procesem, nie wystąpienie systemu operacyjnego, samej siebie.
Do uruchomienia węzła, podaj "Start" dla parametru NodeTransitionType. Aby zatrzymać węzła, podaj "Stop" dla parametru NodeTransitionType. Ten interfejs API rozpoczyna operację — gdy węzeł może nie mieć zakończyło się jeszcze przejście zwraca interfejsu API. Wywołanie GetNodeTransitionProgress z tej samej OperationId uzyskać postęp operacji. 

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --węzeł-— identyfikator wystąpienia [wymagane]| Identyfikator wystąpienia węzła węzeł docelowy. Można to ustalić za pośrednictwem interfejsu API GetNodeInfo.|
| --Nazwa węzła [wymagane]| Nazwa węzła.|
| --przejścia typu węzła [wymagane]| Wskazuje typ przejścia do wykonania.                       NodeTransitionType.Start rozpoczyna się węzeł zatrzymana.                       NodeTransitionType. Stop Zatrzymuje węzła, który jest włączony. |
| — Identyfikator operacji [wymagane]| Identyfikator GUID identyfikujący wywołanie tego interfejsu API.  To jest przekazywany do odpowiedniego interfejsu API GetProgress.|
| --stop-czas trwania w — sekund [wymagane]| Czas w sekundach, aby zachować węzeł zatrzymana.  Wartość minimalna wynosi 600, wartość maksymalna to 14400. Po upływie tego czasu, węzeł automatycznie wróci do sprawności.|
| limit czasu — -t                      | W sekundach limit czasu serwera.  Domyślnie: 60.|

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug                           | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki.|
| — Pomoc -h                         | Pokaż ten komunikat pomocy i Zakończ.|
| --output -o                       | Format danych wyjściowych.  Dozwolone wartości: json, jsonc, tabeli, tsv.                       Domyślne: json.|
| — zapytania                           | Ciąg zapytania JMESPath. Zobacz http://jmespath.org/ dodatkowe informacje i przykłady.|
| -verbose                         | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna.|

## <a name="next-steps"></a>Kolejne kroki
- [Instalator](service-fabric-cli.md) sieci szkieletowej usług interfejsu wiersza polecenia.
- Dowiedz się, jak używać przy użyciu interfejsu wiersza polecenia usługi sieć szkieletowa [przykładowe skrypty](/azure/service-fabric/scripts/sfctl-upgrade-application).
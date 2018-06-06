---
title: Azure Service Fabric interfejsu wiersza polecenia sfctl partycji | Dokumentacja firmy Microsoft
description: Zawiera opis poleceń interfejsu wiersza polecenia usługi sieć szkieletowa sfctl partycji.
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
ms.openlocfilehash: a9455683c5fad7fad4dda62fd967da617d8a8496
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763650"
---
# <a name="sfctl-partition"></a>sfctl partition
Zapytanie partycji i zarządzania nimi dla żadnej usługi.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| utrata danych | Ten interfejs API będzie wywoływać utrata danych dla określonej partycji. |
| data-loss-status | Pobiera postęp uruchamiania przy użyciu interfejsu API StartDataLoss operacji utraty danych partycji. |
| kondycja | Pobiera kondycji określonej partycji usługi sieć szkieletowa usług. |
| informacje | Pobiera informacje o partycji usługi sieć szkieletowa usług. |
| lista | Pobiera listę partycji usługi sieć szkieletowa usług. |
| ładowanie | Pobiera informacje o obciążenia określonej partycji usługi sieć szkieletowa usług. |
| Resetowanie obciążenia | Resetuje bieżącego obciążenia partycji usługi sieć szkieletowa usług. |
| utrata kworum | Powoduje utratę kworum dla danej usługi stanowej partycji. |
| stanu w przypadku utraty kworum | Pobiera postępu operacji utraty kworum na partycji uruchomiony przy użyciu interfejsu API StartQuorumLoss. |
| Odzyskiwanie | Wskazuje, aby klaster sieci szkieletowej usług mają podejmować próbę odzyskać określonej partycji, który jest obecnie zablokowane w wyniku utraty kworum. |
| Odzyskaj wszystkie | Do klastra usługi sieć szkieletowa wskazuje, czy powinien on podejmowana próba odzyskania żadnych usług (takich jak usługi systemu), które są aktualnie zatrzymane w wyniku utraty kworum. |
| report-health | Wysyła raport o kondycji na partycji usługi sieć szkieletowa usług. |
| Ponowne uruchomienie | Ten interfejs API zostanie uruchomiony ponownie, niektóre lub wszystkie repliki lub wystąpienia określonej partycji. |
| stan ponownego uruchomienia | Pobiera postępu operacji PartitionRestart uruchomiony przy użyciu StartPartitionRestart. |
| Nazwa SVC | Pobiera nazwę usługi Service Fabric dla partycji. |

## <a name="sfctl-partition-data-loss"></a>utrata danych sfctl partycji
Ten interfejs API będzie wywoływać utrata danych dla określonej partycji.

Wyzwoli on wywołanie interfejsu API OnDataLossAsync partycji.  Ten interfejs API będzie wywoływać utrata danych dla określonej partycji. Wyzwoli on wywołanie interfejsu API OnDataLoss partycji. Utrata danych rzeczywistych będzie zależeć od określonego elementu DataLossMode. <br> PartialDataLoss - tylko kworum replik są usuwane i OnDataLoss zostanie wywołany dla partycji, ale utraty danych rzeczywisty zależy od obecności locie replikacji. <br>FullDataLoss — wszystkie repliki są usunięte, dlatego wszystkie dane zostaną utracone i OnDataLoss zostanie wywołany. <br>Ten interfejs API należy wywoływać tylko z usługą stanową jako element docelowy. Wywołujący ten interfejs API z usługą systemową jako element docelowy nie jest zalecane. 
> [!NOTE]
> Po wywołaniu tego interfejsu API, nie można cofnąć. Wywoływanie CancelOperation tylko zatrzymuje wykonywanie i wyczyścić stanu wewnętrznego systemu. Będzie on nie przywrócić dane, jeśli polecenie zanotowano stopniu, aby spowodować utratę danych. Wywołanie interfejsu API GetDataLossProgress z tym samym OperationId do zwracania informacji o wprowadzenie tego interfejsu API operacji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --danych utraty trybu [wymagane] | To wyliczenie jest przekazywany do interfejsu API StartDataLoss z informacją o typie utraty danych do wywołania. |
| — Identyfikator operacji [wymagane] | Identyfikator GUID identyfikujący wywołanie tego interfejsu API.  To jest przekazywany do odpowiedniego interfejsu API GetProgress. |
| — Identyfikator partycji [wymagane] | Tożsamość partycji. |
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

## <a name="sfctl-partition-data-loss-status"></a>sfctl partycji danych utraty — stan
Pobiera postęp uruchamiania przy użyciu interfejsu API StartDataLoss operacji utraty danych partycji.

Pobiera postęp wprowadzenie StartDataLoss przy użyciu OperationId operacji utraty danych.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator operacji [wymagane] | Identyfikator GUID identyfikujący wywołanie tego interfejsu API.  To jest przekazywany do odpowiedniego interfejsu API GetProgress. |
| — Identyfikator partycji [wymagane] | Tożsamość partycji. |
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

## <a name="sfctl-partition-health"></a>sfctl partycji kondycji
Pobiera kondycji określonej partycji usługi sieć szkieletowa usług.

Pobiera informacje o kondycji określonej partycji. Filtr EventsHealthStateFilter umożliwia filtrowanie zbierania zdarzeń kondycji zgłoszonych w usłudze oparte na stanie kondycji. Filtr ReplicasHealthStateFilter umożliwia filtrowanie kolekcji obiektów ReplicaHealthState na partycji. To żądanie zwraca błąd, jeśli określono partycję, która nie istnieje w magazynie kondycji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator partycji [wymagane] | Tożsamość partycji. |
| --events-health-state-filter | Umożliwia filtrowanie kolekcji zwracanych obiektów HealthEvent oparte na stanie kondycji. Możliwe wartości tego parametru obejmują liczbę całkowitą jednego z następujących stanów kondycji. Zwracane są tylko zdarzenia, które są zgodne z filtrem. Wszystkie zdarzenia są używane do oceny stanu kondycji zagregowanych. Jeśli nie zostanie określona, zwracane są wszystkie wpisy. Wartości stanu są oparte na flagi wyliczenie, może to być kombinacją te wartości uzyskanych przy użyciu bitowego operatora "Lub". Na przykład jeśli podana wartość jest 6 następnie wszystkie zdarzenia o wartości atrybutu HealthState OK (2) i ostrzeżenia (4) są zwracane.  <br> -Domyślnie — wartość domyślna. Dopasowuje wszystkie właściwości HealthState. Wartość wynosi zero.  <br> -None - filtr, który nie odpowiada żadnej wartości właściwości HealthState. Używany, aby nie zwracała żadnych wyników w danej kolekcji stanów. Wartość to 1.  <br> -Ok - filtru, że dopasowań danych wejściowych o wartości atrybutu HealthState Ok. Wartość jest równa 2.  <br> — Ostrzeżenie - filtru, że dane wejściowe zgodna z atrybutem HealthState wartość ostrzeżenie. Wartość to 4.  <br> -Błąd filtru pasującego do danych wejściowych o wartości atrybutu HealthState błędu. Wartość jest 8.  <br> -All - filtru pasującego do danych wejściowych z dowolną wartością właściwości HealthState. Wartość jest 65535. |
| — Wyklucz kondycji statystyk | Wskazuje, czy statystyki kondycji powinny być zwracane w ramach wyniku zapytania. Wartość false, domyślnie. Statystyki zawierają liczbę elementów podrzędnych obiektów w kondycja Ok, ostrzeżeń i błędów. |
| --replicas-health-state-filter | Umożliwia filtrowanie kolekcji obiektów ReplicaHealthState na partycji. Wartość można uzyskać z bitowe operacje na elementach członkowskich HealthStateFilter lub elementy członkowskie. Tylko replik zgodne z filtrem zostaną zwrócone. Wszystkie repliki będą używane do oceny stanu kondycji zagregowanych. Jeśli nie zostanie określony, zostaną zwrócone wszystkie wpisy. Wartości stanu są oparte na flagi wyliczenie, może to być kombinacją te wartości uzyskanych przy użyciu bitowego operatora "Lub". Na przykład jeśli podana wartość jest 6 wtedy wszystkie zdarzenia o wartości atrybutu HealthState OK (2) i ostrzeżenia (4) zostanie zwrócony. Możliwe wartości tego parametru obejmują liczbę całkowitą jednego z następujących stanów kondycji.  <br> -Domyślnie — wartość domyślna. Dopasowuje wszystkie właściwości HealthState. Wartość wynosi zero.  <br> -None - filtr, który nie odpowiada żadnej wartości właściwości HealthState. Używany, aby nie zwracała żadnych wyników w danej kolekcji stanów. Wartość to 1.  <br> -Ok - filtru, że dopasowań danych wejściowych o wartości atrybutu HealthState Ok. Wartość jest równa 2.  <br> — Ostrzeżenie - filtru, że dane wejściowe zgodna z atrybutem HealthState wartość ostrzeżenie. Wartość to 4.  <br> -Błąd filtru pasującego do danych wejściowych o wartości atrybutu HealthState błędu. Wartość jest 8.  <br> -All - filtru pasującego do danych wejściowych z dowolną wartością właściwości HealthState. Wartość jest 65535. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-partition-info"></a>informacje o partycji sfctl
Pobiera informacje o partycji usługi sieć szkieletowa usług.

Pobiera informacje o określonej partycji. Odpowiedź zawiera identyfikator partycji: informacji schemat partycjonowania, kluczy obsługiwanych przez partycji, stan kondycji i inne szczegółowe informacje o partycji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator partycji [wymagane] | Tożsamość partycji. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-partition-list"></a>listy partycji sfctl
Pobiera listę partycji usługi sieć szkieletowa usług.

Pobiera listę partycji usługi sieć szkieletowa usług. Odpowiedź zawiera identyfikator partycji: informacji schemat partycjonowania, kluczy obsługiwanych przez partycji, stan kondycji i inne szczegółowe informacje o partycji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator usługi [wymagane] | Tożsamość usługi. Zazwyczaj jest to pełna nazwa tej usługi bez ' sieci szkieletowej\:"schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchicznych nazwy są rozdzielane "\~" znaków. Na przykład, jeśli nazwa usługi jest "sieć szkieletowa\:/myapp/app1/svc1", jest tożsamość usługi "moja_aplikacja\~app1\~svc1" w wersji 6.0 + i "myapp/app1/svc1" w poprzednich wersjach. |
| --token kontynuacji | Parametr token kontynuacji służy do uzyskiwania następnej zestaw wyników. Token kontynuacji z niepustą wartość jest uwzględniana w odpowiedzi interfejsu API wyników z systemu nie mieszczą się w jednej odpowiedzi. Jeśli ta wartość jest przekazywany do następnego wywołania interfejsu API interfejsu API zwraca następny zestaw wyników. Jeśli nie są dalsze wyniki, token kontynuacji nie zawiera wartości. Wartość tego parametru nie powinny być zakodowane w adresie URL. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-partition-load"></a>sfctl obciążenia partycji
Pobiera informacje o obciążenia określonej partycji usługi sieć szkieletowa usług.

Zwraca informacje dotyczące obciążenia określonej partycji. Odpowiedź zawiera listę raportów obciążenia partycji usługi sieć szkieletowa usług. Każdy raport zawiera Nazwa metryki ładowania, wartość oraz czas ostatniego zgłoszone w formacie UTC.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator partycji [wymagane] | Tożsamość partycji. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-partition-load-reset"></a>partycja sfctl resetowania obciążenia
Resetuje bieżącego obciążenia partycji usługi sieć szkieletowa usług.

Resetuje bieżącego obciążenia partycji usługi sieć szkieletowa obciążenia domyślny dla usługi.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator partycji [wymagane] | Tożsamość partycji. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-partition-quorum-loss"></a>utraciła kworum sfctl partycji
Powoduje utratę kworum dla danej usługi stanowej partycji.

Powoduje utratę kworum dla danej usługi stanowej partycji.  Ten interfejs API jest przydatne w przypadku utraty kworum tymczasowego sytuacji w usłudze. Wywołanie interfejsu API GetQuorumLossProgress z tym samym OperationId do zwracania informacji o wprowadzenie tego interfejsu API operacji. To może być wywołana tylko dla stateful utrwalone (HasPersistedState == true) usługi.  Nie należy używać tego interfejsu API na usług bezstanowych i stanowych usługi tylko w pamięci.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator operacji [wymagane] | Identyfikator GUID identyfikujący wywołanie tego interfejsu API.  To jest przekazywany do odpowiedniego interfejsu API GetProgress. |
| — Identyfikator partycji [wymagane] | Tożsamość partycji. |
| --kworum utraty — czas trwania [wymagane] | Ilość czasu, dla której partycja zostaną zachowane w wyniku utraty kworum.  To musi być określony w sekundach. |
| --kworum utraty trybu [wymagane] | To wyliczenie jest przekazywany do interfejsu API StartQuorumLoss z informacją o typie wyniku utraty kworum do wywołania. |
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

## <a name="sfctl-partition-quorum-loss-status"></a>partycja sfctl kworum utraty — stan
Pobiera postępu operacji utraty kworum na partycji uruchomiony przy użyciu interfejsu API StartQuorumLoss.

Pobiera postępu operacji utraty kworum wprowadzenie StartQuorumLoss przy użyciu podanego OperationId.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator operacji [wymagane] | Identyfikator GUID identyfikujący wywołanie tego interfejsu API.  To jest przekazywany do odpowiedniego interfejsu API GetProgress. |
| — Identyfikator partycji [wymagane] | Tożsamość partycji. |
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

## <a name="sfctl-partition-recover"></a>Odzyskaj sfctl partycji
Wskazuje, aby klaster sieci szkieletowej usług mają podejmować próbę odzyskać określonej partycji, który jest obecnie zablokowane w wyniku utraty kworum.

Wskazuje, aby klaster sieci szkieletowej usług mają podejmować próbę odzyskać określonej partycji, który jest obecnie zablokowane w wyniku utraty kworum. Ta operacja powinna być wykonana tylko, jeśli wiadomo, że nie można odzyskać replik, które nie działają. Nieprawidłowe użycie tego interfejsu API może spowodować ryzyko utraty danych.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator partycji [wymagane] | Tożsamość partycji. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-partition-recover-all"></a>partycja sfctl recover-all
Do klastra usługi sieć szkieletowa wskazuje, czy powinien on podejmowana próba odzyskania żadnych usług (takich jak usługi systemu), które są aktualnie zatrzymane w wyniku utraty kworum.

Do klastra usługi sieć szkieletowa wskazuje, czy powinien on podejmowana próba odzyskania żadnych usług (takich jak usługi systemu), które są aktualnie zatrzymane w wyniku utraty kworum. Ta operacja powinna być wykonana tylko, jeśli wiadomo, że nie można odzyskać replik, które nie działają. Nieprawidłowe użycie tego interfejsu API może spowodować ryzyko utraty danych.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-partition-report-health"></a>Raport partycji sfctl-kondycji
Wysyła raport o kondycji na partycji usługi sieć szkieletowa usług.

Raporty kondycji określonej partycji usługi sieć szkieletowa usług. Raport musi zawierać informacje o źródle raport o kondycji i właściwości, na którym będzie zgłaszane. Raport jest wysyłany do bramy partycję, która przekazuje do magazynu kondycji sieci szkieletowej usług. Raport może być akceptowane przez bramę, ale odrzucone przez magazynu kondycji po dodatkowej weryfikacji. Na przykład magazynu kondycji może odrzucić raportu z powodu nieprawidłowego parametru, takich jak numer sekwencyjny przestarzałe. Aby sprawdzić, czy raport został zastosowany w magazynie kondycji, sprawdź, czy raport jest wyświetlany w sekcji zdarzenia.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --kondycji — właściwość [wymagane] | Właściwość informacje o kondycji. <br><br> Jednostka może mieć raportów o kondycji dla różnych właściwości. Właściwość jest ciągiem i nie stałej wyliczenia umożliwia elastyczność osoby zgłaszającej kategoryzację warunek stan raportu. Na przykład osoby zgłaszającej o ID "LocalWatchdog" można monitorować stan dysku w węźle, więc może raportować właściwości "AvailableDisk" w tym węźle. Tej samej osoby zgłaszającej można monitorować łączność węzła, więc może raportować właściwości "Łączności" w tym samym węźle. W magazynie kondycji te raporty są traktowane jako zdarzenia oddzielne kondycji dla określonego węzła. Wraz z SourceId właściwość unikatowo identyfikuje informacje o kondycji. |
| --Kondycja [wymagane] | Możliwe wartości\: "Nieprawidłowe", "Ok", "Ostrzeżenie", "Błąd", "Nieznany". |
| — Identyfikator partycji [wymagane] | Tożsamość partycji. |
| — Identyfikator źródła [wymagane] | Nazwa źródła, która identyfikuje składnik klienta/programu alarmowego/systemu, który wygenerował informacji o kondycji. |
| — Opis | Opis informacji o kondycji. <br><br> Reprezentuje dowolny tekst służy do dodawania człowieka odczytać informacje na temat raportu. Maksymalna długość ciągu opisu jest znaków równą 4096. Jeśli podany string jest dłuższy, zostanie automatycznie obcięta. W przypadku obcięty, ostatnie znaki opisu zawiera znacznik "[Truncated]", a rozmiar całkowitą ciągu jest znaków równą 4096. Obecność znacznika wskazuje użytkownikom tego obcięcie wystąpił. Należy pamiętać, że gdy obcięty, opis ma mniej niż 4096 znaków z oryginalnego ciągu. |
| --bezpośrednim | Flaga wskazująca, czy mają być wysyłane raport natychmiast. <br><br> Raport o kondycji są wysyłane do aplikacji, która przekazuje do magazynu kondycji bramy sieci szkieletowej usług. Jeśli Immediate ma ustawioną wartość true, raport jest wysyłany bezpośrednio z bramy HTTP w magazynie kondycji, niezależnie od ustawień klienta sieci szkieletowej, których używa aplikacja bramy HTTP. Jest to przydatne w przypadku krytyczne raportów, które mają być wysyłane tak szybko, jak to możliwe. W zależności od czasu i innych warunków wysłaniem raportu może nadal się nie powieść, na przykład jeśli HTTP bramy został zamknięty lub komunikat nie nawiązać połączenia z bramą. Jeśli Immediate jest ustawiona na wartość false, raport jest wysyłany na podstawie kondycji ustawień klienta z bramy HTTP. W związku z tym go będzie można umieścić w partii zgodnie z konfiguracją HealthReportSendInterval. Jest to zalecane ustawienie ponieważ zezwala ona na kondycji klienta w celu zoptymalizowania wiadomości do magazynu kondycji, a także przetwarzania raportu kondycji raportowania kondycji. Domyślnie raporty nie są wysyłane bezpośrednio. |
| --ważność w przypadku usuwania | Wartość wskazująca, czy raport jest usuwany z magazynu kondycji po jego wygaśnięciu. <br><br> Jeśli ma wartość true, raport zostanie usunięty z magazynu kondycji po jego wygaśnięciu. Jeśli ma wartość false, raport jest traktowana jako błąd po wygaśnięciu. Wartość tej właściwości jest domyślnie false. Gdy klienci okresowo raport ustala RemoveWhenExpired FAŁSZ (ustawienie domyślne). W ten sposób jest reportera, który ma problemy (np. zakleszczenie) i nie można utworzyć raportu, obiekt jest oceniane w błąd, po wygaśnięciu raport o kondycji. Oznacza flagą jednostki jako błąd stanu kondycji. |
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

## <a name="sfctl-partition-restart"></a>ponowne uruchomienie partycji sfctl
Ten interfejs API zostanie uruchomiony ponownie, niektóre lub wszystkie repliki lub wystąpienia określonej partycji.

Ten interfejs API jest przydatna przy testowaniu trybu failover. Jeśli używane pod kątem partycji usługi bezstanowej, tryb RestartPartitionMode musi być AllReplicasOrInstances. Wywołanie interfejsu API GetPartitionRestartProgress przy użyciu tego samego OperationId uzyskać postęp.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator operacji [wymagane] | Identyfikator GUID identyfikujący wywołanie tego interfejsu API.  To jest przekazywany do odpowiedniego interfejsu API GetProgress. |
| — Identyfikator partycji [wymagane] | Tożsamość partycji. |
| — ponowne uruchomienie partycji — trybu [wymagane] | Opis partycji do ponownego uruchomienia. |
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

## <a name="sfctl-partition-restart-status"></a>ponowne uruchomienie partycji sfctl — stan
Pobiera postępu operacji PartitionRestart uruchomiony przy użyciu StartPartitionRestart.

Pobiera postęp PartitionRestart, wprowadzenie StartPartitionRestart przy użyciu podanego OperationId.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator operacji [wymagane] | Identyfikator GUID identyfikujący wywołanie tego interfejsu API.  To jest przekazywany do odpowiedniego interfejsu API GetProgress. |
| — Identyfikator partycji [wymagane] | Tożsamość partycji. |
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

## <a name="sfctl-partition-svc-name"></a>Nazwa svc sfctl partycji
Pobiera nazwę usługi Service Fabric dla partycji.

Pobiera nazwę usługi dla określonej partycji. Jeśli identyfikator partycji nie istnieje w klastrze, jest zwracany błąd 404.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator partycji [wymagane] | Tożsamość partycji. |
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
- [Instalator](service-fabric-cli.md) sieci szkieletowej usług interfejsu wiersza polecenia.
- Dowiedz się, jak używać przy użyciu interfejsu wiersza polecenia usługi sieć szkieletowa [przykładowe skrypty](/azure/service-fabric/scripts/sfctl-upgrade-application).

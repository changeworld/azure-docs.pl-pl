---
title: Azure Service Fabric interfejsu wiersza polecenia sfctl repliki | Dokumentacja firmy Microsoft
description: Zawiera opis poleceń interfejsu wiersza polecenia usługi sieć szkieletowa sfctl repliki.
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
ms.date: 12/22/2017
ms.author: ryanwi
ms.openlocfilehash: ba67a2a20d3f3e8e9fbccb2674cea500bfbde3fb
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2018
---
# <a name="sfctl-replica"></a>sfctl replica
Zarządzanie replik, które należą do partycji usługi.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
|    Wdrożony  | Pobiera szczegóły repliki wdrożone w węźle sieci szkieletowej usług.|
|    wdrożone listy| Pobiera listę replik wdrożone w węźle sieci szkieletowej usług.|
|    kondycja    | Pobiera kondycji sieci szkieletowej usług repliki usługi stanowej lub wystąpienia usługi bezstanowej.|
|    Informacje o      | Pobiera informacje o repliki partycji usługi sieć szkieletowa usług.|
|    lista      | Pobiera informacje o replik partycji usługi sieć szkieletowa usług.|
|    usuń    | Usuwa replikę usługi uruchomione w węźle.|
|    report-health| Wysyła raport o kondycji w replice sieci szkieletowej usług.|
|    Ponowne uruchomienie   | Uruchamia ponownie usługi repliki usługi utrwalonego uruchomionej w węźle.|


## <a name="sfctl-replica-deployed"></a>Replika sfctl wdrożony
Pobiera szczegóły repliki wdrożone w węźle sieci szkieletowej usług.

Pobiera szczegóły repliki wdrożone w węźle sieci szkieletowej usług. Informacje obejmują typ usługi, nazwę usługi, bieżącą operację usługi, bieżącą operację usługi Data i godzina rozpoczęcia, identyfikator partycji, identyfikator repliki i wystąpienia, zgłoszone obciążenia inne informacje.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Nazwa węzła [wymagane]| Nazwa węzła.|
| — Identyfikator partycji [wymagane]| Tożsamość partycji.|
| — identyfikator repliki [wymagane]| Identyfikator repliki.|
| limit czasu — -t          | W sekundach limit czasu serwera.  Domyślnie: 60.|

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug               | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki.|
| — Pomoc -h             | Pokaż ten komunikat pomocy i Zakończ.|
| --output -o           | Format danych wyjściowych.  Dozwolone wartości: json, jsonc, tabeli, tsv.  Domyślne: json.|
| — zapytania               | Ciąg zapytania JMESPath. Aby uzyskać dodatkowe informacje i przykłady, zobacz http://jmespath.org/.|
| -verbose             | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna.|

## <a name="sfctl-replica-health"></a>sfctl repliki kondycji
Pobiera kondycji sieci szkieletowej usług repliki usługi stanowej lub wystąpienia usługi bezstanowej.

Pobiera kondycji sieci szkieletowej usług repliki. Filtr EventsHealthStateFilter umożliwia filtrowanie zbierania zdarzeń kondycji zgłoszonych repliki na podstawie stanu kondycji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator partycji [wymagane]| Tożsamość partycji.|
| — identyfikator repliki [wymagane]| Identyfikator repliki.|
| --events-health-state-filter| Umożliwia filtrowanie kolekcji zwracanych obiektów HealthEvent oparte na stanie kondycji. Możliwe wartości tego parametru obejmują liczbę całkowitą jednego z następujących stanów kondycji. Zwracane są tylko zdarzenia, które są zgodne z filtrem. Wszystkie zdarzenia są używane do oceny stanu kondycji zagregowanych. Jeśli nie zostanie określona, zwracane są wszystkie wpisy. Wartości stanu są oparte na flagi wyliczenie, może to być kombinacją te wartości uzyskanych przy użyciu bitowego operatora "Lub". Na przykład jeśli podana wartość jest 6 następnie wszystkie zdarzenia o wartości atrybutu HealthState OK (2) i ostrzeżenia (4) są zwracane. -Domyślnie — wartość domyślna. Dopasowuje wszystkie właściwości HealthState. Wartość wynosi zero. -None - filtr, który nie odpowiada żadnej wartości właściwości HealthState. Używany, aby nie zwracała żadnych wyników w danej kolekcji stanów. Wartość to 1. -Ok - filtru, że dopasowań danych wejściowych o wartości atrybutu HealthState Ok. Wartość jest równa 2. — Ostrzeżenie - filtru, że dane wejściowe zgodna z atrybutem HealthState wartość ostrzeżenie. Wartość to 4. -Błąd filtru pasującego do danych wejściowych o wartości atrybutu HealthState błędu. Wartość jest 8. -All - filtru pasującego do danych wejściowych z dowolną wartością właściwości HealthState. Wartość jest 65535.|
| limit czasu — -t             | W sekundach limit czasu serwera.  Domyślnie: 60.|

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug                  | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki.|
| — Pomoc -h                | Pokaż ten komunikat pomocy i Zakończ.|
| --output -o              | Format danych wyjściowych.  Dozwolone wartości: json, jsonc, tabeli, tsv.  Domyślne: json.|
| — zapytania                  | Ciąg zapytania JMESPath. Aby uzyskać więcej informacji, zobacz http://jmespath.org/.|
| -verbose                | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna.|

## <a name="sfctl-replica-info"></a>informacje o repliki sfctl
Pobiera informacje o repliki partycji usługi sieć szkieletowa usług.

Odpowiedź zawiera identyfikator, roli, stanu, kondycji, nazwa węzła, czas pracy i inne szczegółowe informacje o repliki.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator partycji [wymagane]| Tożsamość partycji.|
| — identyfikator repliki [wymagane]| Identyfikator repliki.|
| --token kontynuacji  | Parametr token kontynuacji służy do uzyskiwania następnej zestaw wyników. Token kontynuacji z wartość pusta jest uwzględniana w odpowiedzi interfejsu API wyników z systemu nie mieszczą się w jednej odpowiedzi. Jeśli ta wartość jest przekazywany do następnego wywołania interfejsu API interfejsu API zwraca następny zestaw wyników. Jeśli nie są dalsze wyniki, token kontynuacji nie zawiera wartości. Wartość tego parametru nie powinny być zakodowane w adresie URL.|
| limit czasu — -t          | W sekundach limit czasu serwera.  Domyślnie: 60.|

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug               | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki.|
| — Pomoc -h             | Pokaż ten komunikat pomocy i Zakończ.|
| --output -o           | Format danych wyjściowych.  Dozwolone wartości: json, jsonc, tabeli, tsv.  Domyślne: json.|
| — zapytania               | Ciąg zapytania JMESPath. Aby uzyskać więcej informacji, zobacz http://jmespath.org/.|
| -verbose             | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna.|

## <a name="sfctl-replica-list"></a>Lista repliki sfctl
Pobiera informacje o replik partycji usługi sieć szkieletowa usług.

Punkt końcowy GetReplicas zwraca informacje na temat replik określonej partycji.
Respons zawierają identyfikator, roli, stanu, kondycji, nazwa węzła, czas pracy i inne szczegółowe informacje o repliki.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator partycji [wymagane]| Tożsamość partycji.|
| --token kontynuacji  | Parametr token kontynuacji służy do uzyskiwania następnej zestaw wyników. Token kontynuacji z wartość pusta jest uwzględniana w odpowiedzi interfejsu API wyników z systemu nie mieszczą się w jednej odpowiedzi. Jeśli ta wartość jest przekazywany do następnego wywołania interfejsu API interfejsu API zwraca następny zestaw wyników. Jeśli nie są dalsze wyniki token kontynuacji nie zawiera wartości. Wartość tego parametru nie powinny być zakodowane w adresie URL.|
| limit czasu — -t          | W sekundach limit czasu serwera.  Domyślnie: 60.|

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug               | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki.|
| — Pomoc -h             | Pokaż ten komunikat pomocy i Zakończ.|
| --output -o           | Format danych wyjściowych.  Dozwolone wartości: json, jsonc, tabeli, tsv.  Domyślne: json.|
| — zapytania               | Ciąg zapytania JMESPath. Zobacz http://jmespath.org/ dodatkowe informacje i przykłady.|
| -verbose             | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna.|

## <a name="sfctl-replica-remove"></a>Usuń replikę sfctl
Usuwa replikę usługi uruchomione w węźle.

Ten interfejs API symuluje awarii repliki usługi sieć szkieletowa poprzez usunięcie repliki z klastra sieci szkieletowej usług. Usunięcie zamyka repliki, przejścia do roli repliki None, a następnie usuwa wszystkie informacje stanu repliki z klastra. Ten interfejs API testów ścieżki usunięcie stanu repliki i symuluje ścieżka trwały błąd raportu za pośrednictwem interfejsów API klienta. Ostrzeżenie - dostępne są wykonywane, gdy jest używany ten interfejs API nie są sprawdzane bezpieczeństwa. Nieprawidłowe użycie tego interfejsu API może prowadzić do utraty danych dla usług stanowych. Ponadto flagi forceRemove ma wpływ na inne replik hostowanych w tym samym procesie.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Nazwa węzła [wymagane]| Nazwa węzła.|
| — Identyfikator partycji [wymagane]| Tożsamość partycji.|
| — identyfikator repliki [wymagane]| Identyfikator repliki.|
| --force-remove        | Usuń sieć szkieletowa usług aplikacji lub usługi wymuszone bez pośrednictwa bezpiecznego zamknięcia sekwencji. Ten parametr może służyć do wymuszone usunięcie aplikacji lub usługi, dla których delete jest przekroczeniem limitu czasu z powodu problemów z kodem usługi, który uniemożliwia łagodne zamykanie replik.|
| limit czasu — -t          | W sekundach limit czasu serwera.  Domyślnie: 60.|

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug               | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki.|
| — Pomoc -h             | Pokaż ten komunikat pomocy i Zakończ.|
| --output -o           | Format danych wyjściowych.  Dozwolone wartości: json, jsonc, tabeli, tsv.  Domyślne: json.|
| — zapytania               | Ciąg zapytania JMESPath. Zobacz http://jmespath.org/ dodatkowe informacje i przykłady.|
| -verbose             | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna.|

## <a name="sfctl-replica-restart"></a>ponowne uruchomienie repliki sfctl
Uruchamia ponownie usługi repliki usługi utrwalonego uruchomionej w węźle.

Uruchamia ponownie usługi repliki usługi utrwalonego uruchomionej w węźle. Ostrzeżenie - dostępne są wykonywane, gdy jest używany ten interfejs API nie są sprawdzane bezpieczeństwa. Nieprawidłowe użycie tego interfejsu API może prowadzić do utraty dostępności usług stanowych.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Nazwa węzła [wymagane]| Nazwa węzła.|
| — Identyfikator partycji [wymagane]| Tożsamość partycji.|
| — identyfikator repliki [wymagane]| Identyfikator repliki.|
| limit czasu — -t          | W sekundach limit czasu serwera.  Domyślnie: 60.|

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug               | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki.|
| — Pomoc -h             | Pokaż ten komunikat pomocy i Zakończ.|
| --output -o           | Format danych wyjściowych.  Dozwolone wartości: json, jsonc, tabeli, tsv.  Domyślne: json.|
| — zapytania               | Ciąg zapytania JMESPath. Zobacz http://jmespath.org/ dodatkowe informacje i przykłady.|
| -verbose             | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna.|

## <a name="next-steps"></a>Kolejne kroki
- [Instalator](service-fabric-cli.md) sieci szkieletowej usług interfejsu wiersza polecenia.
- Dowiedz się, jak używać przy użyciu interfejsu wiersza polecenia usługi sieć szkieletowa [przykładowe skrypty](/azure/service-fabric/scripts/sfctl-upgrade-application).

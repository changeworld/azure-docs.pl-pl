---
title: Redagowanie Azure Service Fabric interfejsu wiersza polecenia - sfctl | Dokumentacja firmy Microsoft
description: Zawiera opis usługi sieci szkieletowej interfejsu wiersza polecenia sfctl tworzą poleceń.
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
ms.openlocfilehash: 19afd35248cc0796eddbb50db4f38b813f5d568e
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2018
---
# <a name="sfctl-compose"></a>sfctl compose
Tworzenie, usuwanie i zarządzanie wdrożeniami rozwiązania Docker Compose.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
|    create| Wdrażanie aplikacji sieci szkieletowej usług z tworzenia pliku.|
|    lista  | Pobiera listę tworzą wdrożeń utworzonych w klastrze usługi sieć szkieletowa usług.|
|   usuń| Usuwa istniejące sieci szkieletowej usług utworzyć wdrożenia z klastra.|
|   status| Pobiera informacje o sieci szkieletowej usług utworzyć wdrożenia.|
|Uaktualnienie       | Uruchamia uaktualniania wdrożenia tworzenia klastra usługi sieć szkieletowa usług.|
|    Stan uaktualnienia| Pobiera szczegóły dla najnowszą aktualizację w tej sieci szkieletowej usług utworzyć wdrożenia.|


## <a name="sfctl-compose-create"></a>Redagowanie sfctl tworzenie
Tworzy usługi sieć szkieletowa tworzą wdrożenia.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Ścieżka pliku [wymagane]| Ścieżka do pliku rozwiązania Docker Compose docelowego.|
 |   --Nazwa wdrożenia [wymagane]| Nazwa wdrożenia.|
|    --szyfrowane przebiegu             | Zamiast monitowania o podanie hasła rejestru kontener, użyj już zaszyfrowane hasło.|
|    --ma przebiegu                   | Wyświetla monit o podanie hasła w rejestrze kontenera.|
|    limit czasu — -t                 | Limit czasu serwera w sekundach.  Domyślnie: 60.|
 |   --user                       | Nazwa użytkownika do połączenia się z rejestrem kontenera.|

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug                 | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki.|
| — Pomoc -h               | Pokaż ten komunikat pomocy i Zakończ.|
| --output -o             | Format danych wyjściowych.  Dozwolone wartości: json, jsonc, tabeli, tsv.  Domyślne: json.|
| — zapytania                 | Ciąg zapytania JMESPath. Aby uzyskać dodatkowe informacje i przykłady, zobacz http://jmespath.org/.|
| -verbose               | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna.|

## <a name="sfctl-compose-list"></a>sfctl utworzenie listy
Pobiera listę tworzą wdrożeń utworzonych w klastrze usługi sieć szkieletowa usług.

Pobiera stan o wdrożeniach Redaguj, które zostały utworzone lub właśnie trwa tworzenie klastra sieci szkieletowej usług. Odpowiedź zawiera nazwy, stanu i innych szczegółów dotyczących tworzenia wdrożenia. W przypadku wdrożeń nie mieści się na stronie, co strony wyników jest zwracana oraz token kontynuacji, którego można użyć do pobrania następnej strony.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --token kontynuacji| Parametr token kontynuacji służy do uzyskiwania następnej zestaw wyników. Token kontynuacji z wartość pusta jest uwzględniana w odpowiedzi interfejsu API wyników z systemu nie mieszczą się w jednej odpowiedzi.      Jeśli ta wartość jest przekazywany do następnego wywołania interfejsu API interfejsu API zwraca następny zestaw wyników. Jeśli nie są dalsze wyniki, token kontynuacji nie zawiera wartości. Wartość tego parametru nie powinny być zakodowane w adresie URL.|
| — Maksymalna liczba wyników    | Maksymalna liczba wyników ma zostać zwrócona w ramach kwerend stronicowanych.      Ten parametr określa górną granicę na liczbę wyników zwracanych.      Jeśli nie mieszczą się w komunikacie zgodnie z ograniczeniami rozmiar maksymalny komunikatu zdefiniowanych w konfiguracji, wyników zwróconych może być mniejsza niż określona maksymalna liczba wyników. Jeśli ten parametr jest zerowy lub nie jest określony, kwerend stronicowanych zawiera tyle wyniki, jak to możliwe, który mieści się w komunikacie zwracany.|
| limit czasu — -t     | Limit czasu serwera w sekundach.  Domyślnie: 60.|

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug          | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki.|
| — Pomoc -h        | Pokaż ten komunikat pomocy i Zakończ.|
| --output -o      | Format danych wyjściowych.  Dozwolone wartości: json, jsonc, tabeli, tsv.  Domyślne: json.|
| — zapytania          | Ciąg zapytania JMESPath. Aby uzyskać dodatkowe informacje i przykłady, zobacz http://jmespath.org/.|
| -verbose        | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna.|

## <a name="sfctl-compose-remove"></a>Usuń tworzą sfctl
Usuwa istniejące sieci szkieletowej usług utworzyć wdrożenia z klastra.

Usuwa istniejące sieci szkieletowej usług utworzyć wdrożenia. 

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Nazwa wdrożenia [wymagane]| Tożsamość rozmieszczenia. Zazwyczaj jest to pełna nazwa aplikacji bez "fabric:" schemat identyfikatora URI.|
| limit czasu — -t            | Limit czasu serwera w sekundach.  Domyślnie: 60.|

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug                 | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki.|
| — Pomoc -h               | Pokaż ten komunikat pomocy i Zakończ.|
| --output -o             | Format danych wyjściowych.  Dozwolone wartości: json, jsonc, tabeli, tsv.  Domyślne: json.|
| — zapytania                 | Ciąg zapytania JMESPath. Aby uzyskać dodatkowe informacje i przykłady, zobacz http://jmespath.org/.|
| -verbose               | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna.|

## <a name="sfctl-compose-status"></a>Stan tworzenia sfctl
Pobiera informacje o sieci szkieletowej usług utworzyć wdrożenia.

Zwraca stan tworzenia wdrożenia, który został utworzony lub właśnie tworzona w sieci szkieletowej usług klastra i której nazwa jest zgodna ze strukturą określony jako parametr. Odpowiedź zawiera nazwy, stanu i inne szczegółowe informacje o aplikacji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Nazwa wdrożenia [wymagane]| Tożsamość rozmieszczenia. |
| limit czasu — -t            | Limit czasu serwera w sekundach.  Domyślnie: 60.|

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug                 | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki.|
| — Pomoc -h               | Pokaż ten komunikat pomocy i Zakończ.|
| --output -o             | Format danych wyjściowych.  Dozwolone wartości: json, jsonc, tabeli, tsv.  Domyślne: json.|
| — zapytania                 | Ciąg zapytania JMESPath. Aby uzyskać dodatkowe informacje i przykłady, zobacz http://jmespath.org/.|
| -verbose               | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna.|

## <a name="sfctl-compose-upgrade"></a>sfctl tworzą uaktualnienia
Uruchamia uaktualniania wdrożenia tworzenia klastra usługi sieć szkieletowa usług.

Weryfikuje podane parametry uaktualniania i uruchamia uaktualniania wdrożenia.

### <a name="arguments"></a>Argumenty
|Argument|Opis|
| --- | --- |
|    — Ścieżka pliku [wymagane]| Ścieżka do pliku rozwiązania Docker Compose docelowego.|
|    --Nazwa wdrożenia [wymagane]| Nazwa wdrożenia.|
|    --Domyślna svc--kondycji mapy typu| Słownik, który opisuje zasad dotyczących kondycji używane do oceny kondycji usług zakodowane JSON.|
|    --szyfrowane przebiegu             | Zamiast monitowania o podanie hasła rejestru kontener, użyj już zaszyfrowane hasło.|
 |   --Niepowodzenie akcji             | Możliwe wartości: "Nieprawidłowe", "Wycofania", "Ręczny".|
|    --force-restart              | Wymusić ponowne uruchomienie.|
 |   --ma przebiegu                   | Wyświetla monit o podanie hasła w rejestrze kontenera.|
|    --ponownej próby sprawdzenia kondycji         | Limit ponownych prób sprawdzania kondycji mierzony w milisekundach.|
|    --health-check-stable        | Sprawdzanie kondycji stabilna czas w milisekundach.|
|    --health-check-wait          | Czas trwania oczekiwania sprawdzania kondycji mierzony w milisekundach.|
|    --repliki zestaw sprawdzania          | Sprawdzenie limitu czasu mierzony w sekundach zestawu replik uaktualnienia.|
|    -svc--kondycji mapy typu        | Lista obiektów, które opisano zasady dotyczące kondycji używane do oceny kondycji usługi różnych typów zakodowane JSON.|
|    limit czasu — -t                 | Limit czasu serwera w sekundach.  Domyślnie: 60.|
|    --unhealthy-app              | Maksymalna dozwolona wartość procentowa w złej kondycji aplikacji przed zgłoszeniem błędu.        Na przykład aby umożliwić 10% aplikacji jest zła, ta wartość wynosi 10. Wartość procentowa reprezentuje maksymalny procent tolerowaną aplikacje, które mogą być zła, zanim klaster zostanie uznane za błąd. Jeśli wartość procentowa jest przestrzegana, ale istnieje co najmniej jednej aplikacji w złej kondycji, kondycji jest szacowana jako ostrzeżenia. Ta wartość jest obliczana przez podzielenie liczby złej kondycji aplikacji za pośrednictwem całkowita liczba wystąpień aplikacji w klastrze.|
|    — limit czasu domeny uaktualnienia     | Limit czasu domeny uaktualnień mierzony w milisekundach.|
|    --rodzaj uaktualnienia               | Domyślne: wycofanie.|
|    --Tryb uaktualniania               | Możliwe wartości: "Nieprawidłowe", "UnmonitoredAuto", "UnmonitoredManual", "Monitorowanej".  Domyślne: UnmonitoredAuto.|
|    — limit czasu uaktualniania            | Uaktualnienia limitu czasu mierzony w milisekundach.|
|    --user                       | Nazwa użytkownika do połączenia się z rejestrem kontenera.|
|    --warning-as-error           | Ostrzeżenia są traktowane z tym samym ważność jako błędy.|

### <a name="global-arguments"></a>Argumenty globalne
 |Argument|Opis|
| --- | --- |
|   --debug                      | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki.|
|    — Pomoc -h                    | Pokaż ten komunikat pomocy i Zakończ.|
|   --output -o                  | Format danych wyjściowych.  Dozwolone wartości: json, jsonc, tabeli, tsv. Domyślne: json.|
|   — zapytania                      | Ciąg zapytania JMESPath. Zobacz http://jmespath.org/ dodatkowe informacje i przykłady.|
|   -verbose                    | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna.|

## <a name="next-steps"></a>Kolejne kroki
- [Konfigurowanie](service-fabric-cli.md) interfejsu wiersza polecenia usługi sieci szkieletowej.
- Dowiedz się, jak używać przy użyciu interfejsu wiersza polecenia usługi sieć szkieletowa [przykładowe skrypty](/azure/service-fabric/scripts/sfctl-upgrade-application).
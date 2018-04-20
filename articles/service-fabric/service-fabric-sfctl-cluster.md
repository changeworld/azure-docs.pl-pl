---
title: Azure Service Fabric interfejsu wiersza polecenia sfctl klastra | Dokumentacja firmy Microsoft
description: Zawiera opis poleceń interfejsu wiersza polecenia usługi sieć szkieletowa sfctl klastra.
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
ms.openlocfilehash: c83dc3eeb6ca0d66b0c70236354fd7bab80f355f
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2018
---
# <a name="sfctl-cluster"></a>sfctl cluster
Wybierz, zarządzania i obsługi klastrów sieci szkieletowej usług.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
|    wersje kodu| Pobiera listę sieci szkieletowej wersje kodu, które są udostępniane w klastrze usługi sieć szkieletowa usług.|
|    wersje konfiguracji | Pobiera listę sieci szkieletowej wersji konfiguracji, które są udostępniane w klastrze usługi sieć szkieletowa usług.|
|    kondycja       | Pobiera stan klastra usługi sieć szkieletowa usług.|
|    Manifest     | Pobierz manifest klastra sieci szkieletowej usług.|
|    Anuluj operację| Anuluje operację błędów wywołanych przez użytkownika.|
|    operationgit | Pobiera listę błędów wywołanych przez użytkownika działań filtrowane według podanej wartości wejściowej.|
|    Zainicjuj obsługę     | Udostępnianie pakietów kodu lub konfiguracji klastra sieci szkieletowej usług.|
|    odzyskiwanie systemu  | Wskazuje, aby klaster sieci szkieletowej usług mają podejmować próbę odzyskać usług systemowych, które są aktualnie zatrzymane w wyniku utraty kworum.|
|report-health   | Wysyła raport o kondycji w klastrze usługi sieć szkieletowa usług.|
|    wybierz       | Nawiązanie połączenia punktu końcowego klastra sieci szkieletowej usług.|
| Wstrzymanie obsługi administracyjnej     | Wstrzymał obsługi administracyjnej pakiety kodu lub konfiguracji klastra sieci szkieletowej usług.|
|    Uaktualnienie         | Uruchom uaktualnianie wersji kodu lub konfiguracji klastra sieci szkieletowej usług.|
|    Wznów uaktualnienia  | Należy przejść do następnej domeny uaktualnienia Uaktualnianie klastra.|
|    Wycofywanie uaktualnienia| Wycofywanie uaktualnienia klastra sieci szkieletowej usług.|
|    Stan uaktualnienia  | Pobiera postęp bieżącego uaktualnienia klastra.|
|uaktualnienie aktualizacji  | Zaktualizuj parametry uaktualniania uaktualnienia klastra sieci szkieletowej usług.|


## <a name="sfctl-cluster-health"></a>Stan klastra sfctl
Pobiera stan klastra usługi sieć szkieletowa usług.

Pobiera stan klastra usługi sieć szkieletowa usług. Filtr EventsHealthStateFilter umożliwia filtrowanie zbierania zdarzeń kondycji zgłoszonych w klastrze, na podstawie stanu kondycji. Podobnie, użyj filtr NodesHealthStateFilter i filtr ApplicationsHealthStateFilter do filtrowania kolekcji węzły i aplikacji zwrócił oparte na ich zagregowane kondycji. 

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --applications-health-state-filter| Umożliwia filtrowanie zwracanych w wyniku zapytania kondycji klastra na podstawie ich stanu kondycji obiektów stanu kondycji aplikacji. Możliwe wartości tego parametru obejmują uzyskane z elementów członkowskich lub bitowe operacje na elementach członkowskich wyliczenie HealthStateFilter liczbę całkowitą. Zwracane są tylko aplikacje, które są zgodne z filtrem.  Wszystkie aplikacje są używane do oceny stanu kondycji zagregowanych. Jeśli nie zostanie określona, zwracane są wszystkie wpisy. Wartości stanu są flagi na podstawie wyliczenia, dlatego wartość może być kombinacji tych wartości uzyskanych przy użyciu bitowego operatora "Lub". Na przykład jeśli podana wartość jest 6 następnie Kondycja aplikacji o wartości atrybutu HealthState OK (2) i ostrzeżenia (4) są zwracane. -Domyślnie — wartość domyślna. Dopasowuje wszystkie właściwości HealthState. Wartość wynosi zero. -None - filtr, który nie odpowiada żadnej wartości właściwości HealthState. Używany, aby nie zwracała żadnych wyników w danej kolekcji stanów. Wartość to 1. -Ok - filtru, że dopasowań danych wejściowych o wartości atrybutu HealthState Ok. Wartość jest równa 2. — Ostrzeżenie - filtru, że dane wejściowe zgodna z atrybutem HealthState wartość ostrzeżenie. Wartość to 4. -Błąd filtru pasującego do danych wejściowych o wartości atrybutu HealthState błędu. Wartość jest 8. -All - filtru pasującego do danych wejściowych z dowolną wartością właściwości HealthState. Wartość jest 65535.|
| --events-health-state-filter   | Umożliwia filtrowanie kolekcji zwracanych obiektów HealthEvent oparte na stanie kondycji. Możliwe wartości tego parametru obejmują liczbę całkowitą jednego z następujących stanów kondycji. Zwracane są tylko zdarzenia, które są zgodne z filtrem. Wszystkie zdarzenia są używane do oceny stanu kondycji zagregowanych. Jeśli nie zostanie określona, zwracane są wszystkie wpisy. Wartości stanu to flaga na podstawie wyliczenia, dlatego wartość może być kombinacji tych wartości uzyskanych przy użyciu bitowego operatora "Lub". Na przykład jeśli podana wartość jest 6 następnie wszystkie zdarzenia o wartości atrybutu HealthState OK (2) i ostrzeżenia (4) są zwracane. -Domyślnie — wartość domyślna. Dopasowuje wszystkie właściwości HealthState. Wartość wynosi zero. -None - filtr, który nie odpowiada żadnej wartości właściwości HealthState. Używany, aby nie zwracała żadnych wyników w danej kolekcji stanów. Wartość to 1. -Ok - filtru, że dopasowań danych wejściowych o wartości atrybutu HealthState Ok. Wartość jest równa 2. — Ostrzeżenie - filtru, że dane wejściowe zgodna z atrybutem HealthState wartość ostrzeżenie.  Wartość to 4. -Błąd filtru pasującego do danych wejściowych o wartości atrybutu HealthState błędu. Wartość jest 8. -All - filtru pasującego do danych wejściowych z dowolną wartością właściwości HealthState. Wartość jest 65535.|
|— Wyklucz kondycji statystyk                   | Wskazuje, czy statystyki kondycji powinny być zwracane w ramach wyniku zapytania. Wartość false, domyślnie. Statystyki zawierają liczbę elementów podrzędnych obiektów w kondycja Ok, ostrzeżeń i błędów.|
 |   --include-system-application-health-statistics| Określa, czy statystyki kondycji powinna zawierać sieci szkieletowej: / statystyki kondycji aplikacji systemu. Wartość false, domyślnie. Jeśli IncludeSystemApplicationHealthStatistics jest ustawiona na wartość true, kondycję statystyka zawiera jednostki, które należą do sieci szkieletowej: / aplikacji systemowej. W przeciwnym razie wynik zapytania zawiera statystyki kondycji tylko dla użytkownika aplikacji. Statystyki kondycji musi być uwzględniona w wynikach zapytania dla tego parametru, które mają być stosowane.|
| --nodes-health-state-filter    | Umożliwia filtrowanie zwracanych w wyniku zapytania kondycji klastra na podstawie ich stanu kondycji obiektów stanu kondycji węzła. Możliwe wartości tego parametru obejmują liczbę całkowitą jednego z następujących stanów kondycji. Zwracane są tylko węzły, które są zgodne z filtrem. Wszystkie węzły są używane do oceny stanu kondycji zagregowanych. Jeśli nie zostanie określona, zwracane są wszystkie wpisy. Wartości stanu są oparte na flagi wyliczenie, może to być kombinacją te wartości uzyskanych przy użyciu bitowego operatora "Lub". Na przykład jeśli podana wartość jest "6" następnie stan kondycji węzłów o wartości atrybutu HealthState OK (2) i ostrzeżenia (4) są zwracane. -Domyślnie — wartość domyślna. Dopasowuje wszystkie właściwości HealthState. Wartość wynosi zero. -None - filtr, który nie odpowiada żadnej wartości właściwości HealthState. Używany, aby nie zwracała żadnych wyników w danej kolekcji stanów. Wartość to 1. -Ok - filtru, że dopasowań danych wejściowych o wartości atrybutu HealthState Ok. Wartość jest równa 2. — Ostrzeżenie - filtru, że dane wejściowe zgodna z atrybutem HealthState wartość ostrzeżenie.  Wartość to 4. -Błąd filtru pasującego do danych wejściowych o wartości atrybutu HealthState błędu. Wartość jest 8. -All - filtru pasującego do danych wejściowych z dowolną wartością właściwości HealthState. Wartość jest 65535.|
| limit czasu — -t                   | W sekundach limit czasu serwera.  Domyślnie: 60.|

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug                        | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki.|
| — Pomoc -h                      | Pokaż ten komunikat pomocy i Zakończ.|
| --output -o                    | Format danych wyjściowych.  Dozwolone wartości: json, jsonc, tabeli, tsv.                    Domyślne: json.|
| — zapytania                        | Ciąg zapytania JMESPath. Zobacz http://jmespath.org/ dodatkowe informacje i przykłady.|
| -verbose                      | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna.|

## <a name="sfctl-cluster-manifest"></a>sfctl manifestu klastra
Pobierz manifest klastra sieci szkieletowej usług.

Pobierz manifest klastra sieci szkieletowej usług. Manifest klastra zawiera właściwości klastra, które obejmują typy węzłów w klastrze, konfiguracjach zabezpieczeń, odporności i topologii domeny uaktualnienia itp. Te właściwości są określone jako część pliku pliku ClusterConfig.JSON podczas wdrażania klastra autonomicznych. Jednak większość informacji w manifeście klastra jest wygenerowane wewnętrznie przez usługi sieć szkieletowa podczas wdrażania klastra w innych scenariuszach wdrażania (na przykład w przypadku korzystania z [portalu Azure](https://portal.azure.com)). Zawartość manifestu klastra jest tylko do celów informacyjnych i nie powinny użytkowników do podjęcia zależność format zawartość pliku lub jego interpretacji. 

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| limit czasu — -t| W sekundach limit czasu serwera.  Domyślnie: 60.|

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug  | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki.|
| — Pomoc -h| Pokaż ten komunikat pomocy i Zakończ.|
| --output -o | Format danych wyjściowych.  Dozwolone wartości: json, jsonc, tabeli, tsv.  Domyślne: json.|
| — zapytania  | Ciąg zapytania JMESPath. Zobacz http://jmespath.org/ dodatkowe informacje i przykłady.|
| -verbose| Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna.|

## <a name="sfctl-cluster-provision"></a>sfctl udostępniania klastra
Udostępnianie pakietów kodu lub konfiguracji klastra sieci szkieletowej usług.
Sprawdzanie poprawności i Inicjowanie ich obsługi pakietów kodu lub konfiguracji klastra sieci szkieletowej usług.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
|--cluster-manifest-file-path| Ścieżka do pliku manifestu klastra.|
|    --code-file-path            | Ścieżka pliku pakietu kodu klastra.|
|    limit czasu — -t                | W sekundach limit czasu serwera.  Domyślnie: 60.|

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki.|
| — Pomoc -h  | Pokaż ten komunikat pomocy i Zakończ.|
| --output -o| Format danych wyjściowych.  Dozwolone wartości: json, jsonc, tabeli, tsv.  Domyślne: json.|
| — zapytania | Ciąg zapytania JMESPath. Zobacz http://jmespath.org/ dodatkowe informacje i przykłady.|
| -verbose  | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna.|

## <a name="sfctl-cluster-select"></a>Wybierz klaster sfctl
Nawiązanie połączenia punktu końcowego klastra sieci szkieletowej usług.

Nawiązywania bezpiecznej klastra, Określ certyfikat (CRT) i klucza pliku (.key) lub pojedynczy plik z obu (PEM). Nie można określić jednocześnie. Opcjonalnie nawiązywania bezpiecznej klastra, należy także określić ścieżkę do pliku pakietu urzędu certyfikacji lub katalogu zaufane certyfikaty urzędu certyfikacji.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — punkt końcowy [wymagane]| Klastra adres URL punktu końcowego, w tym portu i prefiksu protokołu HTTP lub HTTPS.|
| --aad             | Użyj usługi Azure Active Directory do uwierzytelniania.|
| --ca              | Ścieżka do pliku pakietu urzędu certyfikacji lub katalogu certyfikaty urzędu certyfikacji można traktować jako prawidłowy.|
| --certyfikatu            | Ścieżka do pliku certyfikatu klienta.|
| --klucza             | Ścieżka do pliku klucza certyfikatu klienta.|
| — Sprawdź nie       | Wyłącz weryfikacji certyfikatów przy użyciu protokołu HTTPS, należy zwrócić uwagę: to jest opcją niebezpieczne i nie powinna być używana dla środowisk produkcyjnych.|
| --pem             | Ścieżka do certyfikat klienta jako plik PEM.|

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug           | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki.|
| — Pomoc -h         | Pokaż ten komunikat pomocy i Zakończ.|
| --output -o       | Format danych wyjściowych.  Dozwolone wartości: json, jsonc, tabeli, tsv.  Domyślne: json.|
| — zapytania           | Ciąg zapytania JMESPath. Zobacz http://jmespath.org/ dodatkowe informacje i przykłady.|
| -verbose         | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna.|

## <a name="sfctl-cluster-unprovision"></a>Wstrzymanie obsługi administracyjnej sfctl klastra
Wstrzymał obsługi administracyjnej pakiety kodu lub konfiguracji klastra sieci szkieletowej usług.

Wstrzymał obsługi administracyjnej pakiety kodu lub konfiguracji klastra sieci szkieletowej usług. Jest ona obsługiwana wstrzymał obsługi administracyjnej kodem i konfiguracją oddzielnie.

### <a name="arguments"></a>Argumenty
|Argument|Opis|
| --- | --- |
|--wersja kodu  | Wersja pakietu kodu klastra.|
|    --config-wersja| Wersja manifestu klastra.|
|    limit czasu — -t    | W sekundach limit czasu serwera.  Domyślnie: 60.|

### <a name="global-arguments"></a>Argumenty globalne
|Argument|Opis|
| --- | --- |
|--debug         | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki.|
 |   — Pomoc -h       | Pokaż ten komunikat pomocy i Zakończ.|
 |   --output -o     | Format danych wyjściowych.  Dozwolone wartości: json, jsonc, tabeli, tsv.  Domyślne: json.|
 |   — zapytania         | Ciąg zapytania JMESPath. Zobacz http://jmespath.org/ dodatkowe informacje i przykłady.|
 |   -verbose       | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna.|


## <a name="sfctl-cluster-upgrade"></a>Uaktualnianie klastra sfctl
Uruchom uaktualnianie wersji kodu lub konfiguracji klastra sieci szkieletowej usług.
Sprawdzanie poprawności podanych parametrów uaktualniania, a następnie uruchom uaktualnianie wersji kodu lub konfiguracji klastra usługi sieć szkieletowa, jeśli parametry są prawidłowe.

### <a name="arguments"></a>Argumenty
|Argument|Opis|
| --- | --- |
|    --aplikacji kondycji map                      | Słownik par nazwa aplikacji i maksymalnej wartości procentowej zła przed zgłoszeniem błędu kodowany w formacie JSON.|
 |   --aplikacji typu kondycji map                 | Słownik par nazwa typu aplikacji i maksymalnej wartości procentowej zła przed zgłoszeniem błędu kodowany w formacie JSON.|
 |   --wersja kodu                        | Wersja kodu klastra.|
 |   --config-wersja                      | Wersja konfiguracji klastra.|
 |   --delta-health-evaluation             | Umożliwia ocenę kondycji delta zamiast oceny kondycji bezwzględną po zakończeniu każdej z domen.|
 |   --delta--węzłów w złej kondycji               | Maksymalna dozwolona wartość procentowa węzłów degradacji kondycji dozwolona podczas uaktualniania klastra.  Domyślny: 10. Delta jest mierzony między stan węzłów na początku uaktualniania i stan węzłów w czasie oceny kondycji. Sprawdzanie jest wykonywane po każdej domeny uaktualnienia zakończenia uaktualnienia ma upewnij się, że stan globalny klastra jest w granicach tolerowaną.|
 |   --Niepowodzenie akcji                      | Możliwe wartości: "Nieprawidłowe", "Wycofania", "Ręczny".|
 |   --force-restart                       | Wymusić ponowne uruchomienie.|
 |   --ponownej próby sprawdzenia kondycji                  | Limit ponownych prób sprawdzania kondycji mierzony w milisekundach.|
 |   --health-check-stable                 | Sprawdzanie kondycji stabilna czas w milisekundach.|
  |  --health-check-wait                   | Czas trwania oczekiwania sprawdzania kondycji mierzony w milisekundach.|
  |  --replica-set-check-timeout           | Uaktualnienia replik ustawić wyboru limitu czasu mierzony w sekundach.|
 |   --stopniowych uaktualnienia — trybu                | Możliwe wartości: "Nieprawidłowe", "UnmonitoredAuto", "UnmonitoredManual", "Monitorowanej".  Domyślne: UnmonitoredAuto.|
  |  limit czasu — -t                          | W sekundach limit czasu serwera.  Domyślnie: 60.|
  |  --unhealthy-applications              | Maksymalna dozwolona wartość procentowa w złej kondycji aplikacji przed zgłoszeniem błędu. Na przykład aby umożliwić 10% aplikacji jest zła, ta wartość wynosi 10. Wartość procentowa reprezentuje maksymalny procent tolerowaną aplikacje, które mogą być zła, zanim klaster zostanie uznane za błąd. Jeśli wartość procentowa jest przestrzegana, ale istnieje co najmniej jednej aplikacji w złej kondycji, kondycji jest szacowana jako ostrzeżenia. To jest obliczany przez podzielenie liczby złej kondycji aplikacji za pośrednictwem całkowita liczba wystąpień aplikacji w klastrze, z wyjątkiem aplikacji typów aplikacji, które znajdują się w ApplicationTypeHealthPolicyMap. Obliczenia Zaokrągla wartość w do tolerowanie awarii jednego w małej liczby aplikacji.|
 |   --złej kondycji węzłów                     | Maksymalna dozwolona wartość procentowa węzłów w złej kondycji przed zgłoszeniem błędu. Na przykład aby umożliwić 10% węzłów się zła, wartość ta wynosi 10. Wartość procentowa reprezentuje maksymalny procent tolerowaną węzły, które mogą być zła, zanim klaster zostanie uznane za błąd. Jeśli wartość procentowa jest przestrzegana, ale istnieje co najmniej jeden węzeł złej kondycji, kondycji jest szacowana jako ostrzeżenia. Wartość procentowa jest obliczana przez podzielenie liczby węzłów w złej kondycji przez całkowitą liczbę węzłów w klastrze. Obliczenia Zaokrągla wartość w do tolerowanie awarii jednego w małej liczby węzłów. W dużych klastrach niektóre węzły będą zawsze miały w dół lub wychodzących do naprawy, dzięki tej wartości procentowej, należy skonfigurować tolerować, który.|
 |   --upgrade-domain-delta-unhealthy-nodes| Maksymalna dozwolona wartość procentowa węzły domeny uaktualnień degradacji kondycji dozwolona podczas uaktualniania klastra. Domyślne: 15. Delta jest mierzony między stan węzły domeny uaktualnień na początku uaktualniania i stan węzły domeny uaktualnień w czasie oceny kondycji. Sprawdzanie jest wykonywane po zakończeniu każdej domeny uaktualnienia ukończenia uaktualniania dla wszystkich domen uaktualnienia do upewnij się, że stan uaktualnienia domeny jest w granicach tolerowaną.|
 |   — limit czasu domeny uaktualnienia              | Limit czasu domeny uaktualnień mierzony w milisekundach.|
 |   — limit czasu uaktualniania                     | Limit czasu uaktualniania mierzony w milisekundach.|
 |   --warning-as-error                    | Ostrzeżenia są traktowane z tym samym ważność jako błędy.|

### <a name="global-arguments"></a>Argumenty globalne
|Argument|Opis|
| --- | --- |
|--debug                               | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki.|
|    — Pomoc -h                             | Pokaż ten komunikat pomocy i Zakończ.|
|    --output -o                           | Format danych wyjściowych.  Dozwolone wartości: json, jsonc, tabeli, tsv. Domyślne: json.|
|    — zapytania                               | Ciąg zapytania JMESPath. Zobacz http://jmespath.org/ dodatkowe informacje i przykłady.|
|    -verbose                             | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna.|

## <a name="next-steps"></a>Kolejne kroki
- [Instalator](service-fabric-cli.md) sieci szkieletowej usług interfejsu wiersza polecenia.
- Dowiedz się, jak używać przy użyciu interfejsu wiersza polecenia usługi sieć szkieletowa [przykładowe skrypty](/azure/service-fabric/scripts/sfctl-upgrade-application).
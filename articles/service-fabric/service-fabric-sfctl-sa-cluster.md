---
title: Sieci szkieletowej usługi Azure CLI- sfctl sa-cluster
description: Dowiedz się więcej o sfctl, interfejsie wiersza polecenia sieci szkieletowej usługi Azure. Zawiera listę poleceń do zarządzania klastrami autonomicznymi.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 233148c04fb1a9c6cf1d6c7042c12c54eebd0205
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76904922"
---
# <a name="sfctl-sa-cluster"></a>sfctl sa-cluster
Zarządzanie autonomicznymi klastrami sieci szkieletowej usług.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| config | Pobierz konfigurację autonomicznego klastra sieci szkieletowej usług. |
| config-upgrade | Rozpocznij uaktualnianie konfiguracji autonomicznego klastra sieci szkieletowej usług. |
| stan uaktualnienia | Uzyskaj stan uaktualnienia konfiguracji klastra w klastrze autonomicznym sieci szkieletowej usług. |

## <a name="sfctl-sa-cluster-config"></a>sfctl sa-cluster config
Pobierz konfigurację autonomicznego klastra sieci szkieletowej usług.

Konfiguracja klastra zawiera właściwości klastra, które obejmują różne typy węzłów w klastrze, konfiguracje zabezpieczeń, usterkę i topologie domeny uaktualnienia itp.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --configuration-api-version [Wymagane] | Wersja interfejsu API konfiguracji json klastra autonomicznego. |
| --timeout -t | Limit czasu serwera do wykonania operacji w sekundach. Ten limit czasu określa czas trwania, który klient chce czekać na żądaną operację, aby zakończyć. Wartość domyślna dla tego parametru wynosi 60 sekund.  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-sa-cluster-config-upgrade"></a>sfctl sa-cluster config-upgrade
Rozpocznij uaktualnianie konfiguracji autonomicznego klastra sieci szkieletowej usług.

Sprawdź poprawność podanych parametrów uaktualnienia konfiguracji i rozpocznij uaktualnianie konfiguracji klastra, jeśli parametry są prawidłowe.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --cluster-config [Wymagane] | Konfiguracja klastra. |
| --application-health-policies | JSON zakodowany słownik par nazwy typu aplikacji i maksymalny procent w złej kondycji przed podniesieniem błędu. |
| --delta-unhealthy-nodes | Maksymalny dozwolony procent degradacji kondycji różnicowej podczas uaktualniania. Dozwolone wartości są wartościami całkowitymi od zera do 100. |
| --health-check-retry | Czas między próbami wykonywania kontroli kondycji, jeśli aplikacja lub klaster nie jest w dobrej kondycji.  Domyślne\: PT0H0M0S. |
| --health-check-stable | Czas, przez który aplikacja lub klaster musi pozostać w dobrej kondycji, zanim uaktualnienie przejdzie do następnej domeny uaktualnienia.  Domyślne\: PT0H0M0S. <br><br> Po raz pierwszy jest interpretowany jako ciąg reprezentujący czas trwania ISO 8601. Jeśli to się nie powiedzie, to jest interpretowany jako liczba reprezentująca całkowitą liczbę milisekund. |
| --health-check-wait | Czas oczekiwania po ukończeniu domeny uaktualnienia przed rozpoczęciem procesu sprawdzania kondycji.  Domyślne\: PT0H0M0S. |
| --timeout -t | Wartość\: domyślna 60. |
| --unhealthy-aplikacje | Maksymalny dozwolony procent aplikacji w złej kondycji podczas uaktualniania. Dozwolone wartości są wartościami całkowitymi od zera do 100. |
| --niezdrowe węzły | Maksymalny dozwolony procent węzłów w złej kondycji podczas uaktualniania. Dozwolone wartości są wartościami całkowitymi od zera do 100. |
| --upgrade-domain-delta-unhealthy-nodes --upgrade-domain-delta-unhealthy-nodes --upgrade-domain-delta-unhealthy-nodes -- | Maksymalny dozwolony procent degradacji kondycji domeny różnicowej uaktualnienia podczas uaktualniania. Dozwolone wartości są wartościami całkowitymi od zera do 100. |
| --upgrade-domain-timeout | Czas, przez który każda domena uaktualnienia musi zostać ukończona przed wykonaniem FailureAction.  Domyślne\: PT0H0M0S. <br><br> Po raz pierwszy jest interpretowany jako ciąg reprezentujący czas trwania ISO 8601. Jeśli to się nie powiedzie, to jest interpretowany jako liczba reprezentująca całkowitą liczbę milisekund. |
| --limit czasu uaktualnienia | Czas ukończenia całego uaktualnienia przed wykonaniem FailureAction.  Domyślne\: PT0H0M0S. <br><br> Po raz pierwszy jest interpretowany jako ciąg reprezentujący czas trwania ISO 8601. Jeśli to się nie powiedzie, to jest interpretowany jako liczba reprezentująca całkowitą liczbę milisekund. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

### <a name="examples"></a>Przykłady

Uruchamianie aktualizacji konfiguracji klastra
```
sfctl sa-cluster config-upgrade --cluster-config <YOUR CLUSTER CONFIG> --application-health-
policies "{"fabric:/System":{"ConsiderWarningAsError":true}}"
```

## <a name="sfctl-sa-cluster-upgrade-status"></a>Sfctl sa-cluster upgrade-status sfctl sa-cluster-status sfctl sa-cluster
Uzyskaj stan uaktualnienia konfiguracji klastra w klastrze autonomicznym sieci szkieletowej usług.

Pobierz szczegóły stanu uaktualnienia konfiguracji klastra w klastrze autonomicznym sieci szkieletowej usług.

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


## <a name="next-steps"></a>Następne kroki
- [Konfigurowanie](service-fabric-cli.md) interfejsu wiersza polecenia sieci szkieletowej usług.
- Dowiedz się, jak używać interfejsu wiersza polecenia sieci szkieletowej usług przy użyciu [przykładowych skryptów.](/azure/service-fabric/scripts/sfctl-upgrade-application)
---
title: Azure klastra usługi Service Fabric — interfejs wiersza polecenia sfctl sa-| Dokumentacja firmy Microsoft
description: W tym artykule opisano polecenia interfejsu wiersza polecenia usługi Service Fabric sfctl autonomicznego klastra.
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
ms.openlocfilehash: a652439729e538b3ce2545ab3b09284e6645ce9d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60556393"
---
# <a name="sfctl-sa-cluster"></a>sfctl sa-cluster
Zarządzaj autonomicznych klastrów usługi Service Fabric.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| config | Pobieranie konfiguracji klastra autonomicznego usługi Service Fabric. |
| Uaktualnianie konfiguracji | Uruchom uaktualnianie konfiguracji klastra autonomicznego usługi Service Fabric. |
| upgrade-status | Pobierz stan uaktualnienia konfiguracji klastra autonomicznego klastra usługi Service Fabric. |

## <a name="sfctl-sa-cluster-config"></a>Konfiguracja klastra sa sfctl
Pobieranie konfiguracji klastra autonomicznego usługi Service Fabric.

Konfiguracja klastra zawiera właściwości klastra, które obejmują różnych typów węzłów klastra, konfiguracji zabezpieczeń, błędów i topologii domeny uaktualnień, itp.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Konfiguracja api-version [wymagane] | Wersja interfejsu API json konfiguracji klastra autonomicznego. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-sa-cluster-config-upgrade"></a>Interfejs sfctl sa-cluster konfiguracji uaktualnień
Uruchom uaktualnianie konfiguracji klastra autonomicznego usługi Service Fabric.

Zweryfikuj parametry uaktualniania wprowadzonej konfiguracji i rozpocząć uaktualnianie konfiguracji klastra, jeśli parametry są prawidłowe.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — klaster config [wymagane] | Konfiguracja klastra. |
| — zasady w przypadku kondycji aplikacji | Zakodowane w formacie JSON. Słownik par nazwa typu aplikacji i maksymalnej wartości procentowej złej kondycji przed zgłoszeniem błędu. |
| --delta-unhealthy-nodes | Maksymalna dozwolona wartość procentowa zmian kondycji degradacji podczas uaktualniania. Dozwolone wartości to liczby całkowite od 0 do 100. |
| --health-check-retry | Odstęp czasu między próbami sprawdzać kondycję, jeśli aplikacja lub klastra nie jest w dobrej kondycji.  Domyślne\: PT0H0M0S. |
| --health-check-stable | Ilość czasu, aplikacji lub klastra musi pozostać dobrej kondycji przed uaktualnienia przechodzi do następnej domeny uaktualnienia.  Domyślne\: PT0H0M0S. <br><br> Najpierw jest interpretowany jako ciąg reprezentujący czas trwania ISO 8601. Jeśli ono zawiedzie, następnie jest interpretowany jako liczba reprezentujący całkowitą liczbę milisekund. |
| --health-check-wait | Czas oczekiwania po wykonaniu uaktualnienia domeny przed rozpoczęciem kondycji sprawdza, czy proces.  Domyślne\: PT0H0M0S. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |
| --unhealthy-applications | Maksymalna dozwolona wartość procentowa aplikacje w złej kondycji, podczas uaktualniania. Dozwolone wartości to liczby całkowite od 0 do 100. |
| --węzłów w złej kondycji — | Maksymalna dozwolona wartość procentowa węzłów w złej kondycji, podczas uaktualniania. Dozwolone wartości to liczby całkowite od 0 do 100. |
| --upgrade-domain-delta-unhealthy-nodes | Maksymalna dozwolona wartość procentowa obniżenie wydajności kondycji różnicowych domeny uaktualnień podczas uaktualniania. Dozwolone wartości to liczby całkowite od 0 do 100. |
| --upgrade-domain-timeout | Czas każdej z domen musi wykonać, zanim zostanie wykonany FailureAction.  Domyślne\: PT0H0M0S. <br><br> Najpierw jest interpretowany jako ciąg reprezentujący czas trwania ISO 8601. Jeśli ono zawiedzie, następnie jest interpretowany jako liczba reprezentujący całkowitą liczbę milisekund. |
| — limit czasu uaktualniania | Czas ogólną uaktualnienia musi wykonać, zanim zostanie wykonany FailureAction.  Domyślne\: PT0H0M0S. <br><br> Najpierw jest interpretowany jako ciąg reprezentujący czas trwania ISO 8601. Jeśli ono zawiedzie, następnie jest interpretowany jako liczba reprezentujący całkowitą liczbę milisekund. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

### <a name="examples"></a>Przykłady

Rozpocznij aktualizację konfiguracji klastra

```
sfctl sa-cluster config-upgrade --cluster-config <YOUR CLUSTER CONFIG> --application-health-
policies "{"fabric:/System":{"ConsiderWarningAsError":true}}"
```

## <a name="sfctl-sa-cluster-upgrade-status"></a>Interfejs sfctl sa-cluster uaktualnienia status
Pobierz stan uaktualnienia konfiguracji klastra autonomicznego klastra usługi Service Fabric.

Konfiguracja klastra uaktualnienia Szczegóły pobierania stanu klastra autonomicznego usługi Service Fabric.

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


## <a name="next-steps"></a>Kolejne kroki
- [Konfigurowanie](service-fabric-cli.md) interfejsu wiersza polecenia usługi Service Fabric.
- Dowiedz się, jak używać przy użyciu interfejsu wiersza polecenia usługi Service Fabric [przykładowe skrypty](/azure/service-fabric/scripts/sfctl-upgrade-application).
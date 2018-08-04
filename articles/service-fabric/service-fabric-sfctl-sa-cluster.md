---
title: Azure klastra usługi Service Fabric — interfejs wiersza polecenia sfctl sa-| Dokumentacja firmy Microsoft
description: W tym artykule opisano polecenia interfejsu wiersza polecenia usługi Service Fabric sfctl autonomicznego klastra.
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
ms.date: 07/31/2018
ms.author: bikang
ms.openlocfilehash: d7f33bf0657ca2a6888387b7651706f9de537bb4
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2018
ms.locfileid: "39494360"
---
# <a name="sfctl-sa-cluster"></a>sfctl sa-cluster
Zarządzaj autonomicznych klastrów usługi Service Fabric.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| config | Pobieranie konfiguracji klastra autonomicznego usługi Service Fabric. |
| Uaktualnianie konfiguracji | Uruchom uaktualnianie konfiguracji klastra autonomicznego usługi Service Fabric. |
| Stan uaktualnienia | Pobierz stan uaktualnienia konfiguracji klastra autonomicznego klastra usługi Service Fabric. |

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
| --debugowania | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| — Pomoc -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-sa-cluster-config-upgrade"></a>Interfejs sfctl sa-cluster konfiguracji uaktualnień
Uruchom uaktualnianie konfiguracji klastra autonomicznego usługi Service Fabric.

Zweryfikuj parametry uaktualniania wprowadzonej konfiguracji i rozpocząć uaktualnianie konfiguracji klastra, jeśli parametry są prawidłowe.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — klaster config [wymagane] | Konfiguracja klastra, która zostanie zastosowana do klastra. |
| — zasady w przypadku kondycji aplikacji | Zakodowane w formacie JSON. Słownik par nazwa typu aplikacji i maksymalnej wartości procentowej złej kondycji przed zgłoszeniem błędu. |
| --delta--węzłów w złej kondycji | Maksymalna dozwolona wartość procentowa zmian kondycji degradacji podczas uaktualniania. Dozwolone wartości to liczby całkowite od 0 do 100. |
| --ponownej próby sprawdzenia kondycji | Czas między próbami wykonania kondycji umożliwia sprawdzenie, czy aplikacja lub klastra nie działa prawidłowo.  Domyślne\: PT0H0M0S. |
| --health-check-stable | Czas, że aplikacja lub klastra musi pozostać dobrej kondycji.  Domyślne\: PT0H0M0S. |
| --health-check-wait | Czas oczekiwania po wykonaniu uaktualnienia domeny przed rozpoczęciem kondycji sprawdza, czy proces.  Domyślne\: PT0H0M0S. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |
| --unhealthy-applications | Maksymalna dozwolona wartość procentowa aplikacje w złej kondycji, podczas uaktualniania. Dozwolone wartości to liczby całkowite od 0 do 100. |
| --węzłów w złej kondycji — | Maksymalna dozwolona wartość procentowa węzłów w złej kondycji, podczas uaktualniania. Dozwolone wartości to liczby całkowite od 0 do 100. |
| --upgrade-domain-delta-unhealthy-nodes | Maksymalna dozwolona wartość procentowa obniżenie wydajności kondycji różnicowych domeny uaktualnień podczas uaktualniania. Dozwolone wartości to liczby całkowite od 0 do 100. |
| — limit czasu domeny uaktualnienia | Limit czasu domeny uaktualnień.  Domyślne\: PT0H0M0S. |
| — limit czasu uaktualniania | Limit czasu uaktualniania.  Domyślne\: PT0H0M0S. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debugowania | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| — Pomoc -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

### <a name="examples"></a>Przykłady

Rozpocznij klastra konfiguracji aktualizacji sfctl sa-config uaktualniania klastra — konfiguracji klastra <YOUR CLUSTER CONFIG> — zasady w przypadku kondycji aplikacji "{" Service fabric: / System ": {"ConsiderWarningAsError": true}}"

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
| --debugowania | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| — Pomoc -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |


## <a name="next-steps"></a>Kolejne kroki
- [Konfigurowanie](service-fabric-cli.md) interfejsu wiersza polecenia usługi Service Fabric.
- Dowiedz się, jak używać przy użyciu interfejsu wiersza polecenia usługi Service Fabric [przykładowe skrypty](/azure/service-fabric/scripts/sfctl-upgrade-application).
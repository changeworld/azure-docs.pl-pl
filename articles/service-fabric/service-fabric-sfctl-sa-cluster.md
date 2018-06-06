---
title: Azure klastra sieci szkieletowej usług interfejsu wiersza polecenia - sfctl sa-| Dokumentacja firmy Microsoft
description: Informacje dotyczące poleceń klastra autonomiczny sfctl interfejsu wiersza polecenia usługi sieci szkieletowej.
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
ms.openlocfilehash: ffdbff7edc5af187071615c8b1e61790b3a38429
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34764020"
---
# <a name="sfctl-sa-cluster"></a>sfctl sa klaster
Zarządzanie autonomicznych klastrów sieci szkieletowej usług.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| Konfiguracji | Pobierz autonomiczny konfiguracji klastra sieci szkieletowej usług. |
| Uaktualnianie konfiguracji | Uruchom uaktualnianie konfiguracji autonomicznej klastra sieci szkieletowej usług. |
| Stan uaktualnienia | Pobierz stan uaktualnienia konfiguracji klastra autonomicznej klastra sieci szkieletowej usług. |

## <a name="sfctl-sa-cluster-config"></a>Konfiguracja klastra sa sfctl
Pobierz autonomiczny konfiguracji klastra sieci szkieletowej usług.

Pobierz autonomiczny konfiguracji klastra sieci szkieletowej usług. Konfiguracja klastra zawiera właściwości klastra, które obejmują typy węzłów klastra, konfiguracjach zabezpieczeń, usterka i topologii domeny uaktualnień, itp.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Konfiguracja api-version [wymagane] | Wersja interfejsu API w konfiguracji autonomicznej klastra json. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-sa-cluster-config-upgrade"></a>sfctl sa klastra konfiguracji uaktualnienia
Uruchom uaktualnianie konfiguracji autonomicznej klastra sieci szkieletowej usług.

Zweryfikuj parametry uaktualniania wprowadzonej konfiguracji, a następnie uruchom uaktualnianie konfiguracji klastra, jeśli parametry są prawidłowe.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| -klastra config [wymagane] | Konfiguracja klastra. |
| --delta--węzłów w złej kondycji | Maksymalna dozwolona liczba procent degradacji kondycji różnicowych podczas uaktualniania. Dozwolone wartości to wartości liczbą całkowitą od 0 do 100. |
| --ponownej próby sprawdzenia kondycji | Czas między próbami kondycji sprawdza, jeśli aplikacji lub w klastrze nie działa prawidłowo.  Domyślna\: PT0H0M0S. |
| --health-check-stable | Czas, że aplikacja lub w klastrze muszą pozostać dobrej kondycji.  Domyślna\: PT0H0M0S. |
| --health-check-wait | Czas oczekiwania po zakończeniu uaktualnienia domeny przed rozpoczęciem kondycji sprawdza procesu.  Domyślna\: PT0H0M0S. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |
| --unhealthy-applications | Maksymalna dozwolona wartość procentowa w złej kondycji aplikacji podczas uaktualniania. Dozwolone wartości to wartości liczbą całkowitą od 0 do 100. |
| --złej kondycji węzłów | Maksymalna dozwolona wartość procentowa węzłów w złej kondycji, podczas uaktualniania. Dozwolone wartości to wartości liczbą całkowitą od 0 do 100. |
| --upgrade-domain-delta-unhealthy-nodes | Maksymalna dozwolona wartość procentowa degradacji kondycji różnicowych domeny uaktualnień podczas uaktualniania. Dozwolone wartości to wartości liczbą całkowitą od 0 do 100. |
| — limit czasu domeny uaktualnienia | Limit czasu domeny uaktualnień.  Domyślna\: PT0H0M0S. |
| — limit czasu uaktualniania | Limit czasu uaktualniania.  Domyślna\: PT0H0M0S. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-sa-cluster-upgrade-status"></a>uaktualnienia klastra sa sfctl — stan
Pobierz stan uaktualnienia konfiguracji klastra autonomicznej klastra sieci szkieletowej usług.

Konfiguracja klastra uaktualnienia Szczegóły pobierania stanu autonomicznej klastra sieci szkieletowej usług.

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

## <a name="next-steps"></a>Kolejne kroki
- [Konfigurowanie](service-fabric-cli.md) interfejsu wiersza polecenia usługi sieci szkieletowej.
- Dowiedz się, jak używać przy użyciu interfejsu wiersza polecenia usługi sieć szkieletowa [przykładowe skrypty](/azure/service-fabric/scripts/sfctl-upgrade-application).
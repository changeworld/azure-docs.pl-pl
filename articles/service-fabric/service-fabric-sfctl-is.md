---
title: Azure Service Fabric interfejsu wiersza polecenia - sfctl jest | Dokumentacja firmy Microsoft
description: Zawiera opis usługi sieci szkieletowej interfejsu wiersza polecenia sfctl jest poleceń.
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
ms.openlocfilehash: aa1e4d588f367351f0bed10370c7d67d50dd9927
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763055"
---
# <a name="sfctl-is"></a>sfctl is
Zapytania i wysyłać polecenia do usługi infrastruktury.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| polecenie | Wywołuje polecenie administracyjne dla danego wystąpienia usługi infrastruktury. |
| query | Wywołuje zapytanie tylko do odczytu w wystąpieniu usługi danej infrastruktury. |

## <a name="sfctl-is-command"></a>polecenie jest sfctl
Wywołuje polecenie administracyjne dla danego wystąpienia usługi infrastruktury.

W przypadku klastrów ma co najmniej jedno wystąpienie usługi infrastruktury skonfigurowane ten interfejs API umożliwia wysyłanie poleceń specyficznych dla infrastruktury do konkretnego wystąpienia usługi infrastruktury. Dostępnych poleceń i ich odpowiednich formatów odpowiedzi zależy od infrastruktury, na którym działa klastra. Ten interfejs API obsługuje platformy Service Fabric; nie jest przeznaczona do użycia bezpośrednio w kodzie.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — polecenie [wymagane] | Tekst polecenia do wywołania. Zawartość polecenia jest specyficzne dla infrastruktury. |
| --service-id | Tożsamość usługi infrastruktury. <br><br> Jest to pełna nazwa usługi infrastruktury bez schemat identyfikatora URI "sieci szkieletowej". Ten parametr jest wymagany tylko dla klastra, który ma więcej niż jedno wystąpienie usługi infrastruktury uruchomiona. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-is-query"></a>sfctl jest zapytania
Wywołuje zapytanie tylko do odczytu w wystąpieniu usługi danej infrastruktury.

W przypadku klastrów ma co najmniej jedno wystąpienie usługi infrastruktury skonfigurowany ten interfejs API umożliwia wysyłanie zapytań specyficzne dla infrastruktury do konkretnego wystąpienia usługi infrastruktury. Dostępnych poleceń i ich odpowiednich formatów odpowiedzi zależy od infrastruktury, na którym działa klastra. Ten interfejs API obsługuje platformy Service Fabric; nie jest przeznaczona do użycia bezpośrednio w kodzie.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — polecenie [wymagane] | Tekst polecenia do wywołania. Zawartość polecenia jest specyficzne dla infrastruktury. |
| --service-id | Tożsamość usługi infrastruktury. <br><br> Jest to pełna nazwa usługi infrastruktury bez ' sieci szkieletowej\:"schemat identyfikatora URI. Ten parametr jest wymagany tylko dla klastra, który ma więcej niż jedno wystąpienie usługi infrastruktury uruchomiona. |
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
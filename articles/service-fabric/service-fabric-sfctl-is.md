---
title: Azure Service Fabric interfejsu wiersza polecenia — interfejs sfctl jest | Dokumentacja firmy Microsoft
description: W tym artykule opisano interfejs wiersza polecenia usługi Service Fabric sfctl jest poleceń.
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
ms.openlocfilehash: 2039dd9222809d2c05aaeaf01f9d38c51f3b3797
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60837325"
---
# <a name="sfctl-is"></a>sfctl is
Zapytania i wysyłanie poleceń do usługi infrastruktury.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| command | Wywołuje polecenie administracyjne dla danego wystąpienia usługi infrastruktury. |
| query | Wywołuje zapytanie tylko do odczytu w wystąpieniu usługi danej infrastruktury. |

## <a name="sfctl-is-command"></a>Interfejs sfctl to polecenie
Wywołuje polecenie administracyjne dla danego wystąpienia usługi infrastruktury.

W przypadku klastrów, które mają co najmniej jedno wystąpienie usługi infrastruktury, skonfigurować ten interfejs API umożliwia wysyłanie poleceń specyficznych dla infrastruktury do konkretnego wystąpienia usługi infrastruktury. Dostępnych poleceń i ich odpowiednich formatów odpowiedź zależy od infrastruktury, na którym jest uruchomiony klaster. Ten interfejs API obsługuje platformy usługi Service Fabric; nie jest przeznaczona do użycia bezpośrednio w kodzie.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — polecenie [wymagane] | Tekst polecenia do wywołania. Zawartość polecenia jest specyficzne dla infrastruktury. |
| --service-id | Tożsamość usługi infrastruktury. <br><br> Jest to pełna nazwa usługi infrastruktury bez "Service fabric\:" schemat identyfikatora URI. Ten parametr jest wymagane tylko dla klastra który zawiera więcej niż jedno wystąpienie infrastruktury usługa jest uruchomiona. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-is-query"></a>Interfejs sfctl jest zapytanie
Wywołuje zapytanie tylko do odczytu w wystąpieniu usługi danej infrastruktury.

W przypadku klastrów, które mają co najmniej jedno wystąpienie usługi infrastruktury, skonfigurować ten interfejs API umożliwia wysyłanie zapytań specyficznych dla infrastruktury do konkretnego wystąpienia usługi infrastruktury. Dostępnych poleceń i ich odpowiednich formatów odpowiedź zależy od infrastruktury, na którym jest uruchomiony klaster. Ten interfejs API obsługuje platformy usługi Service Fabric; nie jest przeznaczona do użycia bezpośrednio w kodzie.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — polecenie [wymagane] | Tekst polecenia do wywołania. Zawartość polecenia jest specyficzne dla infrastruktury. |
| --service-id | Tożsamość usługi infrastruktury. <br><br> Jest to pełna nazwa usługi infrastruktury bez "Service fabric\:" schemat identyfikatora URI. Ten parametr jest wymagane tylko dla klastra który zawiera więcej niż jedno wystąpienie infrastruktury usługa jest uruchomiona. |
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
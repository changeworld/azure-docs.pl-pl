---
title: Azure Service Fabric interfejsu wiersza polecenia — sfctl siatki secretvalue | Dokumentacja firmy Microsoft
description: W tym artykule opisano poleceń secretvalue siatki sfctl interfejsu wiersza polecenia usługi Service Fabric.
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
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: b8172ba50f7fec8f0aa1ba768c05d087ecabf8c8
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2018
ms.locfileid: "53285417"
---
# <a name="sfctl-mesh-secretvalue"></a>Interfejs sfctl siatki secretvalue
Pobieranie i usuwanie zasobów secretvalue siatki.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| delete | Usuwa określoną wartość nazwany zasób wpisu tajnego. |
| list | Listę nazw wszystkich wartości z określonego zasobu wpisu tajnego. |
| Pokaż | Pobierz wartość określonej wersji klucza tajnego zasobów. |

## <a name="sfctl-mesh-secretvalue-delete"></a>Interfejs sfctl siatki secretvalue delete
Usuwa określoną wartość nazwany zasób wpisu tajnego.

Usuwa zasób wartość tajna identyfikowane przez nazwę. Nazwa zasobu jest zazwyczaj wersję powiązaną z tej wartości. Usunięcie zakończy się niepowodzeniem, jeśli określona wartość jest używana.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Nazwa klucza tajnego - n [wymagane] | Nazwa wpisu tajnego zasobów. |
| --wersji - v [wymagane] | Nazwa wpisu tajnego wersji. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debugowania | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| — Pomoc -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-mesh-secretvalue-list"></a>Interfejs sfctl siatki secretvalue listy
Lista nazw dla wszystkich wartości z określonego zasobu wpisu tajnego.

Pobiera informacje o wszystkich zasobów wartość tajna określonego zasobu wpisu tajnego. Informacje obejmują nazwy zasobów wartość wpisu tajnego, ale nie rzeczywistych wartości.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Nazwa klucza tajnego - n [wymagane] | Nazwa wpisu tajnego zasobów. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debugowania | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| — Pomoc -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-mesh-secretvalue-show"></a>Interfejs sfctl siatki secretvalue show
Pobierz wartość określonej wersji klucza tajnego zasobów.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Nazwa klucza tajnego - n [wymagane] | Nazwa wpisu tajnego zasobów. |
| --wersji - v [wymagane] | Nazwa wpisu tajnego wersji. |
| — Pokaż wartość | Pokaż wartości rzeczywistej wersję klucza tajnego. |

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
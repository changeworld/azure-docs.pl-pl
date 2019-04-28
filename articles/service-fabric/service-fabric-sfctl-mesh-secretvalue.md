---
title: Azure Service Fabric interfejsu wiersza polecenia — sfctl siatki secretvalue | Dokumentacja firmy Microsoft
description: W tym artykule opisano poleceń secretvalue siatki sfctl interfejsu wiersza polecenia usługi Service Fabric.
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
ms.openlocfilehash: 3f8e46f063d3e725e2174fd907169f3e0167586a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60836953"
---
# <a name="sfctl-mesh-secretvalue"></a>sfctl mesh secretvalue
Pobieranie i usuwanie zasobów secretvalue siatki.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| delete | Usuwa określoną wartość nazwany zasób wpisu tajnego. |
| list | Lista nazw dla wszystkich wartości z określonego zasobu wpisu tajnego. |
| pokaż | Pobierz wartość określonej wersji klucza tajnego zasobów. |

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
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
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
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
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
| --show-value | Pokaż wartości rzeczywistej wersję klucza tajnego. |

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
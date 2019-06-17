---
title: Azure usługi sieci szkieletowej interfejsu wiersza polecenia sfctl siatki obciążające | Dokumentacja firmy Microsoft
description: W tym artykule opisano poleceń interfejsu wiersza polecenia usługi Service Fabric sfctl siatki woluminu.
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
ms.openlocfilehash: 740aec6ccb9b20cbcb8f55b2518c2b2539ef82ac
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60836913"
---
# <a name="sfctl-mesh-volume"></a>sfctl mesh volume
Pobieranie i usuwanie zasobów woluminu.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| delete | Usuwa zasób woluminu. |
| list | Wyświetla listę wszystkich zasobów woluminu. |
| pokaż | Pobiera zasób woluminu o podanej nazwie. |

## <a name="sfctl-mesh-volume-delete"></a>Usuń wolumin siatki interfejsu sfctl
Usuwa zasób woluminu.

Usuwa zasób woluminu identyfikowane przez nazwę.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Nazwa -n [wymagane] | Nazwa woluminu. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-mesh-volume-list"></a>listę woluminów siatki interfejsu sfctl
Wyświetla listę wszystkich zasobów woluminu.

Pobiera informacje o wszystkich zasobów woluminu w danej grupy zasobów. Informacje zawierają opis i inne właściwości woluminu.

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-mesh-volume-show"></a>Interfejs sfctl siatki woluminu show
Pobiera zasób woluminu o podanej nazwie.

Pobiera informacje o zasobie wolumin o podanej nazwie. Informacje zawierają opis i inne właściwości woluminu.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Nazwa -n [wymagane] | Nazwa woluminu. |

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
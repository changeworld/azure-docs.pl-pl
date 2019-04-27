---
title: Sieć platformy Azure Service Fabric interfejsu wiersza polecenia — interfejs sfctl siatki | Dokumentacja firmy Microsoft
description: W tym artykule opisano poleceń sieciowych interfejsu wiersza polecenia usługi Service Fabric sfctl siatki.
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
ms.openlocfilehash: feec5c4796c025c1707b4eb93bfe34b8d384ef3a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60694525"
---
# <a name="sfctl-mesh-network"></a>sfctl mesh network
Pobieranie i usuwanie zasobów sieciowych siatki.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| delete | Usuwa zasób sieci. |
| list | Wyświetla listę wszystkich zasobów sieciowych. |
| pokaż | Pobiera zasób sieciowy o podanej nazwie. |

## <a name="sfctl-mesh-network-delete"></a>Usuń sieć siatki interfejsu sfctl
Usuwa zasób sieci.

Usuwa zasób sieciowy identyfikowane przez nazwę.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Nazwa -n [wymagane] | Nazwa sieci. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-mesh-network-list"></a>Lista sieci siatki interfejsu sfctl
Wyświetla listę wszystkich zasobów sieciowych.

Pobiera informacje o wszystkich zasobów sieciowych w danej grupy zasobów. Informacje zawierają opis i inne właściwości sieci.

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-mesh-network-show"></a>Interfejs sfctl siatki sieci show
Pobiera zasób sieciowy o podanej nazwie.

Pobiera informacje o zasobie sieci o podanej nazwie. Informacje zawierają opis i inne właściwości sieci.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Nazwa -n [wymagane] | Nazwa sieci. |

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
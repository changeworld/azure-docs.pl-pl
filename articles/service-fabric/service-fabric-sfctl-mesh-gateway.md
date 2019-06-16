---
title: Brama platformy Azure interfejsu wiersza polecenia usługi Service Fabric - sfctl siatki | Dokumentacja firmy Microsoft
description: W tym artykule opisano poleceń interfejsu wiersza polecenia usługi Service Fabric sfctl siatki bramy.
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
ms.openlocfilehash: 27cfe380e5988f17ca79c430cde7defc7f7bc440
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60839083"
---
# <a name="sfctl-mesh-gateway"></a>sfctl mesh gateway
Pobieranie i usuwanie zasoby bramy siatki.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| delete | Usuwa zasobu bramy. |
| list | Wyświetla listę wszystkich zasobów bramy. |
| pokaż | Pobiera zasobu bramy o podanej nazwie. |

## <a name="sfctl-mesh-gateway-delete"></a>Usuwanie bramy siatki interfejsu sfctl
Usuwa zasobu bramy.

Usuwa zasób bramy identyfikowane przez nazwę.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Nazwa -n [wymagane] | Nazwa zasobu bramy. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-mesh-gateway-list"></a>listę bram siatki interfejsu sfctl
Wyświetla listę wszystkich zasobów bramy.

Pobiera informacje o wszystkich zasobów bramy w grupie danego zasobu. Informacje zawierają opis i innych właściwości bramy.

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-mesh-gateway-show"></a>Interfejs sfctl siatki gateway show
Pobiera zasobu bramy o podanej nazwie.

Pobiera informacje o zasobie bramy o podanej nazwie. Informacje zawierają opis i innych właściwości bramy.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Nazwa -n [wymagane] | Nazwa zasobu bramy. |

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
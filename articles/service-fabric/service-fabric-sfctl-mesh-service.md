---
title: Usługa Service Fabric interfejsu wiersza polecenia sfctl siatki usługi platformy Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano poleceń usługi siatki sfctl interfejsu wiersza polecenia usługi Service Fabric.
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
ms.openlocfilehash: 6c40cf2340568e704a5c76ad367c07c85826943b
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2018
ms.locfileid: "53284801"
---
# <a name="sfctl-mesh-service"></a>Interfejs sfctl siatki usługi
Uzyskaj szczegółowe informacje o usłudze i Lista usług zasobu aplikacji.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| list | Wyświetla listę wszystkich zasobów usługi. |
| Pokaż | Pobiera zasób usługi o podanej nazwie. |

## <a name="sfctl-mesh-service-list"></a>Lista usług siatki interfejsu sfctl
Wyświetla listę wszystkich zasobów usługi.

Pobiera informacje o wszystkich usług z zasobem aplikacji. Informacje zawierają opis i inne właściwości usługi.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --— Nazwa aplikacji — Nazwa aplikacji [wymagane] | Nazwa aplikacji. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debugowania | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| — Pomoc -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-mesh-service-show"></a>Interfejs sfctl siatki service show
Pobiera zasób usługi o podanej nazwie.

Pobiera informacje o zasobie usługi o podanej nazwie. Informacje zawierają opis i inne właściwości usługi.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --— Nazwa aplikacji — Nazwa aplikacji [wymagane] | Nazwa aplikacji. |
| — Nazwa -n [wymagane] | Nazwa usługi. |

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
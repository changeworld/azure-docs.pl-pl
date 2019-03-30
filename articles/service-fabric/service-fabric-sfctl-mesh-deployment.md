---
title: Wdrożenie usługi Azure Service Fabric interfejsu wiersza polecenia — sfctl siatki | Dokumentacja firmy Microsoft
description: W tym artykule opisano poleceń wdrażania siatki sfctl interfejsu wiersza polecenia usługi Service Fabric.
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
ms.openlocfilehash: e6b484dabd77a142961db2d97242896790fa3d8b
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58668470"
---
# <a name="sfctl-mesh-deployment"></a>sfctl mesh deployment
Tworzenie zasobów usługi Service Fabric siatki.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| create | Tworzy wdrożenie usługi Service Fabric siatki zasobów. |

## <a name="sfctl-mesh-deployment-create"></a>Tworzenie wdrożenia siatki interfejsu sfctl
Tworzy wdrożenie usługi Service Fabric siatki zasobów.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --input-yaml — pliki [wymagane] | Rozdzielana przecinkami względne i bezwzględne ścieżki plików wszystkich yaml pliki lub ścieżki względne i bezwzględne katalogu (rekursywnie) zawierające pliki yaml. |
| --Parametry | Względna/bezwzględną ścieżką do pliku yaml lub obiekt json, który zawiera parametry, które muszą zostać zastąpione. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

### <a name="examples"></a>Przykłady

Konsoliduje i służy do wdrażania wszystkich zasobów do klastra przez zastąpienie parametry określone w pliku yaml

```
sfctl mesh deployment create --input-yaml-files ./app.yaml,./network.yaml --parameters
./param.yaml
```

Konsoliduje i służy do wdrażania wszystkich zasobów w katalogu, do klastra przez zastąpienie parametry określone w pliku yaml

```
sfctl mesh deployment create --input-yaml-files ./resources --parameters ./param.yaml
```

Konsoliduje i służy do wdrażania wszystkich zasobów w katalogu, do klastra przez zastąpienie parametry, które są przekazywane bezpośrednio jako obiekt json

```
sfctl mesh deployment create --input-yaml-files ./resources --parameters "{ 'my_param' :
{'value' : 'my_value'} }"
```


## <a name="next-steps"></a>Kolejne kroki
- [Konfigurowanie](service-fabric-cli.md) interfejsu wiersza polecenia usługi Service Fabric.
- Dowiedz się, jak używać przy użyciu interfejsu wiersza polecenia usługi Service Fabric [przykładowe skrypty](/azure/service-fabric/scripts/sfctl-upgrade-application).
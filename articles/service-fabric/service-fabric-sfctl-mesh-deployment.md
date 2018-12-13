---
title: Wdrożenie usługi Azure Service Fabric interfejsu wiersza polecenia — sfctl siatki | Dokumentacja firmy Microsoft
description: W tym artykule opisano poleceń wdrażania siatki sfctl interfejsu wiersza polecenia usługi Service Fabric.
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
ms.openlocfilehash: b25384d8f3c6e41b6c5cca723d41b79f00b17494
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2018
ms.locfileid: "53285445"
---
# <a name="sfctl-mesh-deployment"></a>Interfejs sfctl siatki wdrożenia
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
| --debugowania | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| — Pomoc -h | Pokaż ten komunikat pomocy i zakończenia. |
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
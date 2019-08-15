---
title: Interfejs wiersza polecenia platformy Azure Service Fabric — wdrożenie siatki sfctl | Microsoft Docs
description: Opisuje sfctl interfejsu wiersza polecenia Service Fabric dla poleceń wdrożenia siatki.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: b3f506b46ef563f47fc7c67b759d3fcd08b7509d
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035187"
---
# <a name="sfctl-mesh-deployment"></a>sfctl mesh deployment
Utwórz zasoby siatki Service Fabric.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| tworzenie | Tworzy wdrożenie zasobów siatki Service Fabric. |

## <a name="sfctl-mesh-deployment-create"></a>Tworzenie wdrożenia siatki sfctl
Tworzy wdrożenie zasobów siatki Service Fabric.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Input-YAML-Files [wymagane] | Oddzielone przecinkami względne/bezwzględne ścieżki plików wszystkich plików YAML lub ścieżki względnej/bezwzględnej katalogu (rekursywnie), które zawierają pliki YAML. |
| --parametry | Ścieżka względna/bezwzględna do pliku YAML lub obiekt JSON zawierający parametry, które muszą zostać zastąpione. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, Table, TSV.  Domyślny\: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać\:więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

### <a name="examples"></a>Przykłady

Konsoliduje i wdraża wszystkie zasoby w klastrze, zastępując parametry wymienione w pliku YAML

```
sfctl mesh deployment create --input-yaml-files ./app.yaml,./network.yaml --parameters
./param.yaml
```

Konsoliduje i wdraża wszystkie zasoby w katalogu w klastrze, zastępując parametry wymienione w pliku YAML

```
sfctl mesh deployment create --input-yaml-files ./resources --parameters ./param.yaml
```

Konsoliduje i wdraża wszystkie zasoby w katalogu w klastrze przez zastąpienie parametrów, które są przesyłane bezpośrednio jako obiekt JSON

```
sfctl mesh deployment create --input-yaml-files ./resources --parameters "{ 'my_param' :
{'value' : 'my_value'} }"
```


## <a name="next-steps"></a>Następne kroki
- [Skonfiguruj](service-fabric-cli.md) interfejs wiersza polecenia Service Fabric.
- Dowiedz się, jak używać interfejsu wiersza polecenia Service Fabric przy użyciu [przykładowych skryptów](/azure/service-fabric/scripts/sfctl-upgrade-application).
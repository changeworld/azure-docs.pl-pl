---
title: Interfejs wiersza polecenia platformy Azure Service Fabric — wdrożenie siatki sfctl | Microsoft Docs
description: Opisuje sfctl interfejsu wiersza polecenia Service Fabric dla poleceń wdrożenia siatki.
services: service-fabric
documentationcenter: na
author: jeffj6123
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: dbecf8e96c6cb5d0d6d873309d848304fe33eaaa
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901249"
---
# <a name="sfctl-mesh-deployment"></a>sfctl mesh deployment
Utwórz zasoby siatki Service Fabric.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| create | Tworzy wdrożenie zasobów siatki Service Fabric. |

## <a name="sfctl-mesh-deployment-create"></a>Tworzenie wdrożenia siatki sfctl
Tworzy wdrożenie zasobów siatki Service Fabric.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Input-YAML-Files [wymagane] | Oddzielone przecinkami względne/bezwzględne ścieżki plików wszystkich plików YAML lub ścieżki względnej/bezwzględnej katalogu (rekursywnie), które zawierają pliki YAML. |
| --parametry | Ścieżka względna/bezwzględna do pliku YAML lub obiektu JSON, który zawiera parametry, które muszą zostać zastąpione. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości\: JSON, jsonc, Table, TSV.  Domyślny\: JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać więcej informacji i przykładów, zobacz http\://jmespath.org/. |
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
---
title: Wdrożenie sieci szkieletowej sieci szkieletowej usługi Azure— sfctl
description: Dowiedz się więcej o sfctl, interfejsie wiersza polecenia sieci szkieletowej usługi Azure. Zawiera listę poleceń służących do tworzenia zasobów siatki sieci szkieletowej usług.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 108389407221779ed20e81310f084b7b5c23b8c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906025"
---
# <a name="sfctl-mesh-deployment"></a>sfctl mesh deployment
Tworzenie zasobów siatki sieci szkieletowej usług.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| create | Tworzy wdrożenie zasobów siatki sieci szkieletowej usług. |

## <a name="sfctl-mesh-deployment-create"></a>tworzenie wdrożenia siatki sfctl
Tworzy wdrożenie zasobów siatki sieci szkieletowej usług.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --input-yaml-files [Wymagane] | Oddzielone przecinkami względne lub bezwzględne ścieżki plików wszystkich plików yaml lub względnej lub bezwzględnej ścieżki katalogu (cyklicznego), które zawierają pliki yaml. |
| --parametry | Względna lub bezwzględna ścieżka do pliku yaml lub obiektu json, który zawiera parametry, które muszą zostać zastąpione. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

### <a name="examples"></a>Przykłady

Konsoliduje i wdraża wszystkie zasoby do klastra, zastępując parametry wymienione w pliku yaml
``` 
sfctl mesh deployment create --input-yaml-files ./app.yaml,./network.yaml --parameters  
./param.yaml    
```

Konsoliduje i wdraża wszystkie zasoby w katalogu do klastra, zastępując parametry wymienione w pliku yaml

``` 
sfctl mesh deployment create --input-yaml-files ./resources --parameters ./param.yaml
```

Konsoliduje i wdraża wszystkie zasoby w katalogu do klastra, zastępując parametry, które są przekazywane bezpośrednio jako json obiektu
``` 
sfctl mesh deployment create --input-yaml-files ./resources --parameters "{ 'my_param' :    
{'value' : 'my_value'} }"   
```

## <a name="next-steps"></a>Następne kroki
- [Konfigurowanie](service-fabric-cli.md) interfejsu wiersza polecenia sieci szkieletowej usług.
- Dowiedz się, jak używać interfejsu wiersza polecenia sieci szkieletowej usług przy użyciu [przykładowych skryptów.](/azure/service-fabric/scripts/sfctl-upgrade-application)

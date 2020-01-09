---
title: Interfejs wiersza polecenia platformy Azure Service Fabric — wolumin siatki sfctl
description: Dowiedz się więcej na temat sfctl, interfejsu wiersza polecenia platformy Azure Service Fabric. Zawiera listę poleceń pobierania i usuwania zasobów woluminu.
author: jeffj6123
ms.topic: reference
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: e77c98bf384278b0bf27bb0f30f425375093ffab
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645314"
---
# <a name="sfctl-mesh-volume"></a>sfctl mesh volume
Pobierz i Usuń zasoby woluminu.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| delete | Usuwa zasób woluminu. |
| list | Wyświetla listę wszystkich zasobów woluminu. |
| pokaż | Pobiera zasób woluminu o podaną nazwę. |

## <a name="sfctl-mesh-volume-delete"></a>Usuwanie woluminu siatki sfctl
Usuwa zasób woluminu.

Usuwa zasób woluminu identyfikowany przez nazwę.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Name-n [wymagane] | Nazwa woluminu. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości\: JSON, jsonc, Table, TSV.  Domyślny\: JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać więcej informacji i przykładów, zobacz http\://jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-mesh-volume-list"></a>Lista woluminów siatki sfctl
Wyświetla listę wszystkich zasobów woluminu.

Pobiera informacje o wszystkich zasobach zbiorczych w danej grupie zasobów. Informacje obejmują opis i inne właściwości woluminu.

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości\: JSON, jsonc, Table, TSV.  Domyślny\: JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać więcej informacji i przykładów, zobacz http\://jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-mesh-volume-show"></a>Pokaż wolumin siatki sfctl
Pobiera zasób woluminu o podaną nazwę.

Pobiera informacje o zasobie woluminu o podanej nazwie. Informacje obejmują opis i inne właściwości woluminu.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Name-n [wymagane] | Nazwa woluminu. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości\: JSON, jsonc, Table, TSV.  Domyślny\: JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać więcej informacji i przykładów, zobacz http\://jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |


## <a name="next-steps"></a>Następne kroki
- [Skonfiguruj](service-fabric-cli.md) interfejs wiersza polecenia Service Fabric.
- Dowiedz się, jak używać interfejsu wiersza polecenia Service Fabric przy użyciu [przykładowych skryptów](/azure/service-fabric/scripts/sfctl-upgrade-application).
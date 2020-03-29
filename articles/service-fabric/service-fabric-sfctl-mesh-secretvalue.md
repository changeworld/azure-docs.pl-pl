---
title: Sieć szkieletowa usługi Azure CLI- sfctl siatki secretvalue
description: Dowiedz się więcej o sfctl, interfejsie wiersza polecenia sieci szkieletowej usługi Azure. Zawiera listę poleceń do uzyskiwania i usuwania zasobów secretvalue sieci szkieletowej usługi.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: b2fe3a7995cdd88a8f63d0d865e72e9f161cf9d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905967"
---
# <a name="sfctl-mesh-secretvalue"></a>sfctl mesh secretvalue
Pobierz i usuń zasoby siatki secretvalue.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| delete | Usuwa określoną wartość nazwanego zasobu tajnego. |
| list | Lista nazw wszystkich wartości określonego tajnego zasobu. |
| pokaż | Wyświetla określoną wartość tajnego zasobu. |

## <a name="sfctl-mesh-secretvalue-delete"></a>sfctl mesh secretvalue delete sfctl mesh secretvalue delete sfctl mesh secretvalue delete sfc
Usuwa określoną wartość nazwanego zasobu tajnego.

Usuwa tajny zasób wartości identyfikowany przez nazwę. Nazwa zasobu jest zazwyczaj wersją skojarzoną z tą wartością. Usunięcie zakończy się niepowodzeniem, jeśli określona wartość jest w użyciu.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --secret-name -n [Wymagane] | Nazwa tajnego zasobu. |
| --version -v [Wymagane] | Nazwa tajnej wersji. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-mesh-secretvalue-list"></a>lista secretvalue siatki sfctl
Lista nazw wszystkich wartości określonego tajnego zasobu.

Pobiera informacje o wszystkich tajnych zasobów wartości określonego zasobu tajnego. Informacje obejmują nazwy tajnych zasobów wartości, ale nie rzeczywiste wartości.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --secret-name -n [Wymagane] | Nazwa tajnego zasobu. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-mesh-secretvalue-show"></a>sfctl mesh secretvalue show
Wyświetla określoną wartość tajnego zasobu.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --secret-name -n [Wymagane] | Nazwa tajnego zasobu. |
| --version -v [Wymagane] | Nazwa tajnej wersji. |
| --show-wartość | Pokaż rzeczywistą wartość wersji tajnej. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |


## <a name="next-steps"></a>Następne kroki
- [Konfigurowanie](service-fabric-cli.md) interfejsu wiersza polecenia sieci szkieletowej usług.
- Dowiedz się, jak używać interfejsu wiersza polecenia sieci szkieletowej usług przy użyciu [przykładowych skryptów.](/azure/service-fabric/scripts/sfctl-upgrade-application)
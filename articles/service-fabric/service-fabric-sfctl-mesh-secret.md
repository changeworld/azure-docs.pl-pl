---
title: Sieć szkieletowa usługi Azure CLI- klucz tajny siatki sfctl
description: Dowiedz się więcej o sfctl, interfejsie wiersza polecenia sieci szkieletowej usługi Azure. Zawiera listę poleceń służących do uzyskiwania i usuwania tajnych zasobów sieci szkieletowej usługi.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: fab388ff223eb95020e2ba0945c76532bc54f224
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905988"
---
# <a name="sfctl-mesh-secret"></a>sfctl mesh secret
Pobierz i usuń tajne zasoby siatki.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| delete | Usuwa tajny zasób. |
| list | Wyświetla listę wszystkich tajnych zasobów. |
| pokaż | Pobiera tajny zasób o podanej nazwie. |

## <a name="sfctl-mesh-secret-delete"></a>Sfctl siatka klucz tajny usunąć
Usuwa tajny zasób.

Usuwa określony tajny zasób i wszystkie jego nazwane wartości.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --name -n [Wymagane] | Nazwa tajnego zasobu. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-mesh-secret-list"></a>sfctl siatka tajna lista
Wyświetla listę wszystkich tajnych zasobów.

Pobiera informacje o wszystkich tajnych zasobów w danej grupie zasobów. Informacje zawierają opis i inne właściwości klucza tajnego.

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-mesh-secret-show"></a>sfctl siatka tajny pokaz
Pobiera tajny zasób o podanej nazwie.

Pobiera informacje o tajnym zasobie o podanej nazwie. Informacje zawierają opis i inne właściwości klucza tajnego.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --name -n [Wymagane] | Nazwa tajnego zasobu. |

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
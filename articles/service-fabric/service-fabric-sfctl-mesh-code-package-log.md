---
title: Sieć szkieletowa usługi Azure CLI- sfctl siatki code-package-log
description: Dowiedz się więcej o sfctl, interfejsie wiersza polecenia sieci szkieletowej usługi Azure. Zawiera listę poleceń uzyskiwania dzienników dla określonego pakietu kodu.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: f8665e75e4c921a3305c9965601e2ee0825c8995
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906051"
---
# <a name="sfctl-mesh-code-package-log"></a>sfctl mesh code-package-log
Pobierz dzienniki dla kontenera określonego pakietu kodu dla danej repliki usługi.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| get | Pobiera dzienniki z kontenera. |

## <a name="sfctl-mesh-code-package-log-get"></a>sfctl mesh code-package-log get
Pobiera dzienniki z kontenera.

Pobiera dzienniki dla kontenera określonego pakietu kodu repliki usługi.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --app-name --application-name [Wymagane] | Nazwa aplikacji. |
| --code-package-name [Wymagane] | Nazwa pakietu kodu usługi. |
| --replica-name [Wymagane] | Nazwa repliki sieci szkieletowej usług. |
| --service-name [Wymagane] | Nazwa usługi. |
| --ogon | Liczba wierszy do wyświetlenia od końca dzienników. Wartość domyślna to 100. "wszystko", aby wyświetlić pełne dzienniki. |

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
---
title: Interfejs wiersza polecenia platformy Azure Service Fabric — kod siatki sfctl-Package-log
description: Dowiedz się więcej na temat sfctl, interfejsu wiersza polecenia platformy Azure Service Fabric. Zawiera listę poleceń do uzyskiwania dzienników dla określonego pakietu kodu.
author: jeffj6123
ms.topic: reference
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: 78edc9bb36b711f72300942bc9900b0fde7c51d2
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75646147"
---
# <a name="sfctl-mesh-code-package-log"></a>sfctl mesh code-package-log
Pobierz dzienniki dla kontenera określonego pakietu kodu dla danej repliki usługi.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| Pobierz | Pobiera dzienniki z kontenera. |

## <a name="sfctl-mesh-code-package-log-get"></a>kod siatki sfctl-Package-log Get
Pobiera dzienniki z kontenera.

Pobiera dzienniki dla kontenera określonego pakietu kodu repliki usługi.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --App-Name--Application-Name [wymagane] | Nazwa aplikacji. |
| --Code-package-name [wymagane] | Nazwa pakietu kodu usługi. |
| --Replica-Name [wymagane] | Nazwa repliki Service Fabric. |
| --Service-Name [wymagane] | Nazwa usługi. |
| --tail | Liczba wierszy do wyświetlenia na końcu dzienników. Wartość domyślna to 100. "wszystkie", aby wyświetlić kompletne dzienniki. |

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
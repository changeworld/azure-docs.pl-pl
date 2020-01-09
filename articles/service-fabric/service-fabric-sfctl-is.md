---
title: Interfejs wiersza polecenia platformy Azure Service Fabric — sfctl
description: Dowiedz się więcej na temat sfctl, interfejsu wiersza polecenia platformy Azure Service Fabric. Zawiera listę poleceń związanych z zarządzaniem infrastrukturą.
author: jeffj6123
ms.topic: reference
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: c3c98e8da0c2a5449296a0fd108977ec0633f83d
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75639126"
---
# <a name="sfctl-is"></a>sfctl is
Wykonywanie zapytań i wysyłanie poleceń do usługi infrastruktury.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| command | Wywołuje polecenie administracyjne w danym wystąpieniu usługi infrastruktury. |
| query | Wywołuje zapytanie tylko do odczytu dla danego wystąpienia usługi infrastruktury. |

## <a name="sfctl-is-command"></a>sfctl jest poleceniem
Wywołuje polecenie administracyjne w danym wystąpieniu usługi infrastruktury.

W przypadku klastrów, dla których skonfigurowano co najmniej jedno wystąpienie usługi infrastruktury, ten interfejs API umożliwia wysyłanie poleceń specyficznych dla infrastruktury do określonego wystąpienia usługi infrastruktury. Dostępne polecenia i odpowiadające im formaty odpowiedzi różnią się w zależności od infrastruktury, w której działa klaster. Ten interfejs API obsługuje platformę Service Fabric. nie jest przeznaczona do użycia bezpośrednio w kodzie.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --polecenie [wymagane] | Tekst polecenia, który ma zostać wywołany. Zawartość polecenia jest specyficzna dla infrastruktury. |
| --service-id | Tożsamość usługi infrastruktury. <br><br> Jest to pełna nazwa usługi infrastruktury bez schematu identyfikatora URI "Fabric\:". Ten parametr jest wymagany tylko w przypadku klastra, na którym działa więcej niż jedno wystąpienie usługi infrastruktury. |
| --timeout-t | Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości\: JSON, jsonc, Table, TSV.  Domyślny\: JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać więcej informacji i przykładów, zobacz http\://jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-is-query"></a>sfctl jest kwerendą
Wywołuje zapytanie tylko do odczytu dla danego wystąpienia usługi infrastruktury.

W przypadku klastrów, dla których skonfigurowano co najmniej jedno wystąpienie usługi infrastruktury, ten interfejs API umożliwia wysyłanie zapytań specyficznych dla infrastruktury do określonego wystąpienia usługi infrastruktury. Dostępne polecenia i odpowiadające im formaty odpowiedzi różnią się w zależności od infrastruktury, w której działa klaster. Ten interfejs API obsługuje platformę Service Fabric. nie jest przeznaczona do użycia bezpośrednio w kodzie.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --polecenie [wymagane] | Tekst polecenia, który ma zostać wywołany. Zawartość polecenia jest specyficzna dla infrastruktury. |
| --service-id | Tożsamość usługi infrastruktury. <br><br> Jest to pełna nazwa usługi infrastruktury bez schematu identyfikatora URI "Fabric\:". Ten parametr jest wymagany tylko w przypadku klastra, na którym działa więcej niż jedno wystąpienie usługi infrastruktury. |
| --timeout-t | Domyślne\: 60. |

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
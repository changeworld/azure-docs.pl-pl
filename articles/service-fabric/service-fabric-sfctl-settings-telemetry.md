---
title: Dane telemetryczne ustawień interfejsu wiersza polecenia sieci szkieletowej usługi Azure Azure
description: Dowiedz się więcej o sfctl, interfejsie wiersza polecenia sieci szkieletowej usługi Azure. Zawiera listę poleceń służących do konfigurowania telemetrii sfctl.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 6af5fa944ef399756f9e890ddd77a7f5f32e2bfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76903018"
---
# <a name="sfctl-settings-telemetry"></a>sfctl settings telemetry
Skonfiguruj ustawienia telemetryczne lokalne dla tego wystąpienia sfctl.

Telemetria Sfctl zbiera nazwę polecenia bez podanych parametrów lub ich wartości, wersję sfctl, typ systemu operacyjnego, wersję systemu python, powodzenie lub niepowodzenie polecenia, zwracany komunikat o błędzie.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| set-telemetria | Włączanie lub wyłączanie danych telemetrycznych. |

## <a name="sfctl-settings-telemetry-set-telemetry"></a>ustawienia sfctl set-telemetria ustawień
Włączanie lub wyłączanie danych telemetrycznych.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --off | Wyłącz telemetrię. |
| --on | Włącz telemetrię. Jest to wartość domyślna. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

### <a name="examples"></a>Przykłady

Wyłącz telemetrię.

```
sfctl settings telemetry set_telemetry --off
```

Włącz telemetrię.

```
sfctl settings telemetry set_telemetry --on
```


## <a name="next-steps"></a>Następne kroki
- [Konfigurowanie](service-fabric-cli.md) interfejsu wiersza polecenia sieci szkieletowej usług.
- Dowiedz się, jak używać interfejsu wiersza polecenia sieci szkieletowej usług przy użyciu [przykładowych skryptów.](/azure/service-fabric/scripts/sfctl-upgrade-application)
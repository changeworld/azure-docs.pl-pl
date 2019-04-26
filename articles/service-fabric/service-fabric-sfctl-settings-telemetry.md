---
title: Azure interfejsu wiersza polecenia usługi Service Fabric - sfctl ustawieniami telemetrii | Dokumentacja firmy Microsoft
description: W tym artykule opisano polecenia interfejsu wiersza polecenia usługi Service Fabric sfctl ustawienia danych telemetrycznych.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: 42a82ab0be37f260a48a1da6cecab5120c24d293
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60556342"
---
# <a name="sfctl-settings-telemetry"></a>sfctl settings telemetry
Skonfiguruj ustawienia danych telemetrycznych lokalnych, jak to wystąpienie interfejsu sfctl.

Interfejs Sfctl telemetrii zbiera nazwę polecenia bez parametrów podanych oraz ich wartości, wersja sfctl, typ systemu operacyjnego, wersji języka python, Powodzenie lub niepowodzenie polecenia, zwracany komunikat o błędzie.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| dane telemetryczne zestawu | Włączanie lub wyłączanie telemetrii. |

## <a name="sfctl-settings-telemetry-set-telemetry"></a>Interfejs sfctl ustawienia danych telemetrycznych zestawu telemetrii
Włączanie lub wyłączanie telemetrii.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — wyłączone | Wyłączyć obsługę telemetrii. |
| — na | Włącz telemetrię. Jest to wartość domyślna. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

### <a name="examples"></a>Przykłady

Wyłączyć obsługę telemetrii.

```
sfctl settings telemetry set_telemetry --off
```

Włącz telemetrię.

```
sfctl settings telemetry set_telemetry --on
```


## <a name="next-steps"></a>Kolejne kroki
- [Konfigurowanie](service-fabric-cli.md) interfejsu wiersza polecenia usługi Service Fabric.
- Dowiedz się, jak używać przy użyciu interfejsu wiersza polecenia usługi Service Fabric [przykładowe skrypty](/azure/service-fabric/scripts/sfctl-upgrade-application).
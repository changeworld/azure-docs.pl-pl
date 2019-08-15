---
title: Interfejs wiersza polecenia platformy Azure Service Fabric — Telemetria ustawień sfctl | Microsoft Docs
description: Zawiera Service Fabric opis poleceń telemetrycznych sfctl ustawień interfejsu wiersza polecenia.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: cf5ebbeb4d9b4757e0c55eeb1a9268065efb2c7c
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035195"
---
# <a name="sfctl-settings-telemetry"></a>sfctl settings telemetry
Skonfiguruj ustawienia telemetrii lokalnie dla tego wystąpienia elementu sfctl.

Sfctl telemetrii zbiera nazwę polecenia bez parametrów dostarczonych lub ich wartości, wersję Sfctl, typ systemu operacyjnego, wersję języka Python, powodzenie lub niepowodzenie polecenia, zwracany komunikat o błędzie.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| Set-Telemetria | Włączać lub wyłączać dane telemetryczne. |

## <a name="sfctl-settings-telemetry-set-telemetry"></a>zestaw telemetrii ustawień sfctl — Telemetria
Włączać lub wyłączać dane telemetryczne.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --off | Wyłącz telemetrię. |
| --na | Włącz telemetrię. Jest to wartość domyślna. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, Table, TSV.  Domyślny\: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać\:więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

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
- [Skonfiguruj](service-fabric-cli.md) interfejs wiersza polecenia Service Fabric.
- Dowiedz się, jak używać interfejsu wiersza polecenia Service Fabric przy użyciu [przykładowych skryptów](/azure/service-fabric/scripts/sfctl-upgrade-application).
---
title: Interfejs wiersza polecenia platformy Azure Service Fabric — Telemetria ustawień sfctl | Microsoft Docs
description: Zawiera Service Fabric opis poleceń telemetrycznych sfctl ustawień interfejsu wiersza polecenia.
services: service-fabric
documentationcenter: na
author: jeffj6123
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: d926c71ae8fd9e196b86c14c7fb96cc65b587b65
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900977"
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
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości\: JSON, jsonc, Table, TSV.  Domyślny\: JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać więcej informacji i przykładów, zobacz http\://jmespath.org/. |
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
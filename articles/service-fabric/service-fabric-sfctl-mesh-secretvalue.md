---
title: Interfejs wiersza polecenia platformy Azure Service Fabric sfctl secretvalue | Microsoft Docs
description: Opisuje Service Fabric interfejsu wiersza polecenia sfctl siatkę secretvalue.
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
ms.openlocfilehash: 96ce4efe00d89c136a0c11d445170b2f67be6fcd
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901190"
---
# <a name="sfctl-mesh-secretvalue"></a>sfctl mesh secretvalue
Pobieranie i usuwanie zasobów secretvalue siatki.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| delete | Usuwa określoną wartość nazwanego zasobu tajnego. |
| list | Wyświetla listę nazw wszystkich wartości określonego zasobu tajnego. |
| pokaż | Wyświetla określoną wartość zasobu tajnego. |

## <a name="sfctl-mesh-secretvalue-delete"></a>sfctl siatki secretvalue usuwanie
Usuwa określoną wartość nazwanego zasobu tajnego.

Usuwa zasób wartości tajnej identyfikowany przez nazwę. Nazwa zasobu jest zazwyczaj wersją skojarzoną z tą wartością. Usuwanie zakończy się niepowodzeniem, jeśli określona wartość jest używana.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Secret-Name-n [wymagane] | Nazwa zasobu tajnego. |
| --Version-v [wymagane] | Nazwa wersji tajnej. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości\: JSON, jsonc, Table, TSV.  Domyślny\: JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać więcej informacji i przykładów, zobacz http\://jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-mesh-secretvalue-list"></a>Lista secretvalue siatki sfctl
Wyświetla listę nazw wszystkich wartości określonego zasobu tajnego.

Pobiera informacje o wszystkich zasobach wartości tajnych określonego zasobu tajnego. Informacje te obejmują nazwy zasobów wartości tajnych, ale nie wartości rzeczywiste.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Secret-Name-n [wymagane] | Nazwa zasobu tajnego. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości\: JSON, jsonc, Table, TSV.  Domyślny\: JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać więcej informacji i przykładów, zobacz http\://jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-mesh-secretvalue-show"></a>sfctl siatki secretvalue Pokaż
Wyświetla określoną wartość zasobu tajnego.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Secret-Name-n [wymagane] | Nazwa zasobu tajnego. |
| --Version-v [wymagane] | Nazwa wersji tajnej. |
| --show-Value | Pokaż rzeczywistą wartość wersji tajnej. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości\: JSON, jsonc, Table, TSV.  Domyślny\: JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać więcej informacji i przykładów, zobacz http\://jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |


## <a name="next-steps"></a>Następne kroki
- [Skonfiguruj](service-fabric-cli.md) interfejs wiersza polecenia Service Fabric.
- Dowiedz się, jak używać interfejsu wiersza polecenia Service Fabric przy użyciu [przykładowych skryptów](/azure/service-fabric/scripts/sfctl-upgrade-application).
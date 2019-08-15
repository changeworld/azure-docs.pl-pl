---
title: Interfejs wiersza polecenia platformy Azure Service Fabric sfctl secretvalue | Microsoft Docs
description: Opisuje Service Fabric interfejsu wiersza polecenia sfctl siatkę secretvalue.
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
ms.openlocfilehash: 5200c40afe62ce3a236a3a16b59b64deb8d24ced
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035925"
---
# <a name="sfctl-mesh-secretvalue"></a>sfctl mesh secretvalue
Pobieranie i usuwanie zasobów secretvalue siatki.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| usuwanie | Usuwa określoną wartość nazwanego zasobu tajnego. |
| list | Wyświetla listę nazw wszystkich wartości określonego zasobu tajnego. |
| pokaż | Pobierz wartość określonej wersji tajnego zasobu. |

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
| --debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, Table, TSV.  Domyślny\: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać\:więcej informacji i przykładów, zobacz http//jmespath.org/. |
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
| --debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, Table, TSV.  Domyślny\: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać\:więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-mesh-secretvalue-show"></a>sfctl siatki secretvalue Pokaż
Pobierz wartość określonej wersji tajnego zasobu.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Secret-Name-n [wymagane] | Nazwa zasobu tajnego. |
| --Version-v [wymagane] | Nazwa wersji tajnej. |
| --show-value | Pokaż rzeczywistą wartość wersji tajnej. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, Table, TSV.  Domyślny\: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać\:więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |


## <a name="next-steps"></a>Następne kroki
- [Skonfiguruj](service-fabric-cli.md) interfejs wiersza polecenia Service Fabric.
- Dowiedz się, jak używać interfejsu wiersza polecenia Service Fabric przy użyciu [przykładowych skryptów](/azure/service-fabric/scripts/sfctl-upgrade-application).
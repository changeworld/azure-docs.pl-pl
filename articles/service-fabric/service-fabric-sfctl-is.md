---
title: Interfejs wiersza polecenia platformy Azure Service Fabric to | Microsoft Docs
description: Service Fabric opisuje sfctl interfejsu wiersza polecenia.
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
ms.openlocfilehash: 9a09d8c36fd282450767880a9ad144e1192dcd2e
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901581"
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
| --ID usługi | Tożsamość usługi infrastruktury. <br><br> Jest to pełna nazwa usługi infrastruktury bez schematu identyfikatora URI "Fabric\:". Ten parametr jest wymagany tylko w przypadku klastra, na którym działa więcej niż jedno wystąpienie usługi infrastruktury. |
| --timeout-t | Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
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
| --ID usługi | Tożsamość usługi infrastruktury. <br><br> Jest to pełna nazwa usługi infrastruktury bez schematu identyfikatora URI "Fabric\:". Ten parametr jest wymagany tylko w przypadku klastra, na którym działa więcej niż jedno wystąpienie usługi infrastruktury. |
| --timeout-t | Domyślne\: 60. |

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
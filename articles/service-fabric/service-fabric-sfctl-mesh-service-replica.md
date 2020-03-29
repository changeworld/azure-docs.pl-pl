---
title: Replika usługi sieci szkieletowej usługi Azure Azure CLI- sfctl mesh
description: Dowiedz się więcej o sfctl, interfejsie wiersza polecenia sieci szkieletowej usługi Azure. Zawiera listę poleceń uzyskiwania szczegółów repliki dla zasobów aplikacji.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: ac604ec0a854964746ef34627b054e853662265f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905939"
---
# <a name="sfctl-mesh-service-replica"></a>sfctl mesh service-replica
Pobierz szczegóły repliki i listy replik danej usługi w zasobie aplikacji.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| list | Wyświetla listę wszystkich replik usługi. |
| pokaż | Pobiera danej repliki usługi aplikacji. |

## <a name="sfctl-mesh-service-replica-list"></a>lista replik usługi-replika siatki sfctl
Wyświetla listę wszystkich replik usługi.

Pobiera informacje o wszystkich replikach usługi. Informacje obejmują opis i inne właściwości repliki usługi.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --app-name --application-name [Wymagane] | Nazwa aplikacji. |
| --service-name [Wymagane] | Nazwa usługi. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-mesh-service-replica-show"></a>sfctl mesh service-replica show sfctl mesh service-replica show Sfctl mesh service-replica show Sfc
Pobiera danej repliki usługi aplikacji.

Pobiera informacje o repliki usługi o podanej nazwie. Informacje obejmują opis i inne właściwości repliki usługi.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --app-name --application-name [Wymagane] | Nazwa aplikacji. |
| --name -n [Wymagane] | Nazwa repliki usługi. |
| --service-name [Wymagane] | Nazwa usługi. |

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
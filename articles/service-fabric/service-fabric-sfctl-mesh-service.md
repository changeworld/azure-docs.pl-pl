---
title: Usługa sieci szkieletowej sieci szkieletowej usługi Azure— usługa siatki sfctl
description: Dowiedz się więcej o sfctl, interfejsie wiersza polecenia sieci szkieletowej usługi Azure. Zawiera listę poleceń uzyskiwania szczegółów usługi dla zasobu aplikacji.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 2b9f16062b02d7a1a3403d396643a7989b8976b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905926"
---
# <a name="sfctl-mesh-service"></a>sfctl mesh service
Pobierz szczegóły usługi i lista usług zasobu aplikacji.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| list | Wyświetla listę wszystkich zasobów usługi. |
| pokaż | Pobiera zasób usługi o podanej nazwie. |

## <a name="sfctl-mesh-service-list"></a>Lista usług siatki sfctl
Wyświetla listę wszystkich zasobów usługi.

Pobiera informacje o wszystkich usługach zasobu aplikacji. Informacje te zawierają opis i inne właściwości Serwisu.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --app-name --application-name [Wymagane] | Nazwa aplikacji. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-mesh-service-show"></a>pokaz usługi siatki sfctl
Pobiera zasób usługi o podanej nazwie.

Pobiera informacje o zasobie usługi o podanej nazwie. Informacje te zawierają opis i inne właściwości Serwisu.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --app-name --application-name [Wymagane] | Nazwa aplikacji. |
| --name -n [Wymagane] | Nazwa usługi. |

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
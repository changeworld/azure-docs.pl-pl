---
title: Azure interfejsu wiersza polecenia usługi Service Fabric - sfctl siatki klucz tajny | Dokumentacja firmy Microsoft
description: W tym artykule opisano poleceń wpisu tajnego siatki sfctl interfejsu wiersza polecenia usługi Service Fabric.
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
ms.openlocfilehash: acc5862a8794da4fde991025eacfcccf6e16916a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60607683"
---
# <a name="sfctl-mesh-secret"></a>sfctl mesh secret
Pobierz i usunąć zasoby wpisu tajnego siatki.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| delete | Usuwa zasób wpisu tajnego. |
| list | Wyświetla wszystkie zasoby, klucza tajnego. |
| pokaż | Pobiera wpisu tajnego zasobów o podanej nazwie. |

## <a name="sfctl-mesh-secret-delete"></a>Interfejs sfctl siatki secret delete
Usuwa zasób wpisu tajnego.

Usuwa określony zasób wpisu tajnego i wszystkie jego nazwanych wartości.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Nazwa -n [wymagane] | Nazwa klucza tajnego. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-mesh-secret-list"></a>sfctl siatki secret list
Wyświetla wszystkie zasoby, klucza tajnego.

Pobiera informacje o wszystkich zasobów wpisu tajnego w określonej grupy zasobów. Informacje zawierają opis i inne właściwości wpisu tajnego.

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-mesh-secret-show"></a>Interfejs sfctl siatki secret show
Pobiera wpisu tajnego zasobów o podanej nazwie.

Pobiera informacje o zasobie tajny o podanej nazwie. Informacje zawierają opis i inne właściwości wpisu tajnego.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Nazwa -n [wymagane] | Nazwa klucza tajnego. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| --help -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |


## <a name="next-steps"></a>Kolejne kroki
- [Konfigurowanie](service-fabric-cli.md) interfejsu wiersza polecenia usługi Service Fabric.
- Dowiedz się, jak używać przy użyciu interfejsu wiersza polecenia usługi Service Fabric [przykładowe skrypty](/azure/service-fabric/scripts/sfctl-upgrade-application).
---
title: Usługa sieci szkieletowej interfejsu wiersza polecenia — sfctl kontenera Azure | Dokumentacja firmy Microsoft
description: Zawiera opis poleceń interfejsu wiersza polecenia usługi sieć szkieletowa sfctl kontenera.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 05/23/2018
ms.author: bikang
ms.openlocfilehash: cd3725ac547a1ed1fd9207dc48ba3b6227e85ef1
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34764019"
---
# <a name="sfctl-container"></a>kontener sfctl
Uruchom polecenia związane z kontenera w węźle klastra.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| Wywołanie interfejsu api | Wywołanie interfejsu API REST kontenera. |
| dzienniki | Podczas pobierania dzienników kontenera. |

## <a name="sfctl-container-invoke-api"></a>kontener sfctl invoke-api
Wywołanie interfejsu API REST kontenera.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator aplikacji [wymagane] | Tożsamość aplikacji. |
| --kodu — pakiet-— identyfikator wystąpienia [wymagane] | Identyfikator wystąpienia pakietu kodu, który mogą zostać pobrane przez "usługi kodu pakiet list". |
| --kodu — pakiet — wymagana jest nazwa [] | Nazwa pakietu kodu. |
| ---api-uri ścieżka kontenera [wymagane] | Ścieżka identyfikatora URI interfejsu API REST kontenera, użyj {ID} zamiast identyfikatora nazwy kontenera. |
| --Nazwa węzła [wymagane] | Nazwa węzła. |
| --service-manifest-name    [Required] | Nazwa manifestu usługi. |
| --kontenera interfejsu api treści | Treść żądania HTTP dla kontenera interfejsu API REST. |
| --kontenera api-content-type | Typ zawartości dla kontenera interfejsu API REST, wartość domyślna to "application/json". |
| --kontenera api-— zlecenie http | Zlecenie HTTP dla kontenera interfejsu API REST, domyślnie GET. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="sfctl-container-logs"></a>Dzienniki kontenera sfctl
Podczas pobierania dzienników kontenera.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator aplikacji [wymagane] | Tożsamość aplikacji. |
| --kodu — pakiet-— identyfikator wystąpienia [wymagane] | Identyfikator wystąpienia pakietu kodu, który mogą zostać pobrane przez "usługi kodu pakiet list". |
| --kodu — pakiet — wymagana jest nazwa [] | Nazwa pakietu kodu. |
| --Nazwa węzła [wymagane] | Nazwa węzła. |
| --service-manifest-name    [Required] | Nazwa manifestu usługi. |
| --tail | Zwraca tylko tej liczby wierszy dziennika od końca dzienniki. Określ jako liczba całkowita lub wszystkie dane wyjściowe wszystkich wierszy dziennika. Domyślnie do wszystkich. |
| limit czasu — -t | W sekundach limit czasu serwera.  Domyślna\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększenie szczegółowości rejestrowania, aby pokazać wszystkie debugowania dzienniki. |
| — Pomoc -h | Pokaż ten komunikat pomocy i Zakończ. |
| --output -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabeli, tsv.  Domyślna\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ dodatkowe informacje i przykłady. |
| -verbose | Zwiększ poziom szczegółowości rejestrowania. Użycie--debugowania dla dzienników debugowania pełna. |

## <a name="next-steps"></a>Kolejne kroki
- [Konfigurowanie](service-fabric-cli.md) interfejsu wiersza polecenia usługi sieci szkieletowej.
- Dowiedz się, jak używać przy użyciu interfejsu wiersza polecenia usługi sieć szkieletowa [przykładowe skrypty](/azure/service-fabric/scripts/sfctl-upgrade-application).
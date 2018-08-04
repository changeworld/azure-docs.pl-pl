---
title: Usługa Azure container sfctl interfejsu wiersza polecenia usługi Service Fabric - | Dokumentacja firmy Microsoft
description: Zawiera opis poleceń interfejsu wiersza polecenia usługi Service Fabric sfctl kontenera.
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
ms.date: 07/31/2018
ms.author: bikang
ms.openlocfilehash: 27108d27ee27346e4cba44e6778faff56df70a36
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2018
ms.locfileid: "39495132"
---
# <a name="sfctl-container"></a>sfctl container
Uruchom kontener związane z poleceń w węźle klastra.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| wywoływanie interfejsu api | Wywoływanie interfejsu API REST kontenera. |
| dzienniki | Podczas pobierania dzienników kontenera. |

## <a name="sfctl-container-invoke-api"></a>Interfejs sfctl kontenera wywołania interfejsów api
Wywoływanie interfejsu API REST kontenera.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator aplikacji [wymagane] | Tożsamość aplikacji. |
| --Kod —-wystąpienie — identyfikator pakietu [wymagane] | Kod pakietu wystąpienia Identyfikatora, który można pobrać za "usługę kod —-lista pakietów". |
| --Kod pakietu nazwa-[wymagane] | Nazwa pakietu kodu. |
| --container-api ścieżka identyfikatora uri — [wymagane] | Ścieżka identyfikatora URI interfejsu API REST kontenera, użyj "{id}" zamiast identyfikatora nazwy kontenera. |
| — [wymagane] Nazwa węzła | Nazwa węzła. |
| --service-manifest-name    [Required] | Nazwa manifestu usługi. |
| --container api treści | Treść żądania HTTP dla kontenera interfejsu API REST. |
| --container-api-content-type | Typ zawartości dla kontenera interfejsu API REST, wartość domyślna to "application/json". |
| --container-api — — zlecenie http | Czasownik HTTP dla kontenera interfejsu API REST, wartość domyślna to GET. |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debugowania | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| — Pomoc -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |

## <a name="sfctl-container-logs"></a>Interfejs sfctl dzienników kontenera
Podczas pobierania dzienników kontenera.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator aplikacji [wymagane] | Tożsamość aplikacji. |
| --Kod —-wystąpienie — identyfikator pakietu [wymagane] | Kod pakietu wystąpienia Identyfikatora, który można pobrać za "usługę kod —-lista pakietów". |
| --Kod pakietu nazwa-[wymagane] | Nazwa pakietu kodu. |
| — [wymagane] Nazwa węzła | Nazwa węzła. |
| --service-manifest-name    [Required] | Nazwa manifestu usługi. |
| --tail | Ta liczba wierszy dziennika należy zwracać tylko od końca dzienniki. Określ jako liczba całkowita lub wszystkie dane wyjściowe wszystkich wierszy dziennika. Domyślne ustawienia pozycji "all". |
| limit czasu — -t | Limit czasu serwera w ciągu kilku sekund.  Domyślne\: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debugowania | Zwiększyć szczegółowość rejestrowania, aby pokazać, że debugowanie wszystkich dzienników. |
| — Pomoc -h | Pokaż ten komunikat pomocy i zakończenia. |
| --dane wyjściowe -o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, tabela, tsv.  Domyślne\: json. |
| — zapytania | Ciąg zapytania JMESPath. Zobacz http\://jmespath.org/ uzyskać więcej informacji i przykładów. |
| — pełne | Zwiększ poziom szczegółowości rejestrowania. Użyj parametru--debugowania dzienniki pełnego debugowania. |


## <a name="next-steps"></a>Kolejne kroki
- [Konfigurowanie](service-fabric-cli.md) interfejsu wiersza polecenia usługi Service Fabric.
- Dowiedz się, jak używać przy użyciu interfejsu wiersza polecenia usługi Service Fabric [przykładowe skrypty](/azure/service-fabric/scripts/sfctl-upgrade-application).
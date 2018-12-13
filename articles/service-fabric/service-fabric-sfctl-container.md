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
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: 455b2a70568566bff5b1ea4c185568a1758f7db3
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2018
ms.locfileid: "53274908"
---
# <a name="sfctl-container"></a>sfctl container
Uruchom kontener związane z poleceń w węźle klastra.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| wywoływanie interfejsu api | Wywołania interfejsu API kontenera, do kontenera wdrażane w węźle usługi Service Fabric dla pakietu danego kodu. |
| dzienniki | Pobiera dzienniki kontenera dla kontenera wdrażane w węźle usługi Service Fabric dla pakietu danego kodu. |

## <a name="sfctl-container-invoke-api"></a>Interfejs sfctl kontenera wywołania interfejsów api
Wywołania interfejsu API kontenera, do kontenera wdrażane w węźle usługi Service Fabric dla pakietu danego kodu.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator aplikacji [wymagane] | Tożsamość aplikacji. <br><br> Zazwyczaj jest to pełna nazwa aplikacji bez "Service fabric\:" schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchiczne nazwy są oddzielane za pomocą znaku "\~" znaków. Na przykład, jeśli nazwa aplikacji jest "Service fabric\:/myapp/app1", tożsamość aplikacji będzie "myapp\~app1" w wersji 6.0 + i "myapp app1" w poprzednich wersjach. |
| --Kod —-wystąpienie — identyfikator pakietu [wymagane] | Identyfikator, który jednoznacznie identyfikuje wystąpienie pakietu kodu, wdrożone w węźle usługi Service fabric. <br><br> Mogą być pobierane przez "Usługa-pakietu — lista kodu". |
| --Kod pakietu nazwa-[wymagane] | Nazwa pakietu kodu określonego w manifeście usługi zarejestrowany jako część typu aplikacji w klastrze usługi Service Fabric. |
| --container-api ścieżka identyfikatora uri — [wymagane] | Ścieżka identyfikatora URI interfejsu API REST kontenera, użyj "{id}" zamiast identyfikatora nazwy kontenera. |
| — [wymagane] Nazwa węzła | Nazwa węzła. |
| --service-manifest-name    [Required] | Nazwa manifestu usługi zarejestrowany jako część typu aplikacji w klastrze usługi Service Fabric. |
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
Pobiera dzienniki kontenera dla kontenera wdrażane w węźle usługi Service Fabric dla pakietu danego kodu.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| — Identyfikator aplikacji [wymagane] | Tożsamość aplikacji. <br><br> Zazwyczaj jest to pełna nazwa aplikacji bez "Service fabric\:" schemat identyfikatora URI. Począwszy od wersji 6.0, hierarchiczne nazwy są oddzielane za pomocą znaku "\~" znaków. Na przykład, jeśli nazwa aplikacji jest "Service fabric\:/myapp/app1", tożsamość aplikacji będzie "myapp\~app1" w wersji 6.0 + i "myapp app1" w poprzednich wersjach. |
| --Kod —-wystąpienie — identyfikator pakietu [wymagane] | Kod pakietu wystąpienia Identyfikatora, który można pobrać za "usługę kod —-lista pakietów". |
| --Kod pakietu nazwa-[wymagane] | Nazwa pakietu kodu określonego w manifeście usługi zarejestrowany jako część typu aplikacji w klastrze usługi Service Fabric. |
| — [wymagane] Nazwa węzła | Nazwa węzła. |
| --service-manifest-name    [Required] | Nazwa manifestu usługi zarejestrowany jako część typu aplikacji w klastrze usługi Service Fabric. |
| --tail | Liczba wierszy do pokazania od końca dzienniki. Domyślna to 100. "wszystkie", aby wyświetlić pełne dzienniki. |
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
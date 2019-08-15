---
title: Interfejs wiersza polecenia platformy Azure Service Fabric — kontener sfctl | Microsoft Docs
description: Opisuje Service Fabric polecenia kontenera sfctl CLI.
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
ms.openlocfilehash: 0ce6cf7c627657cf757b0c1ef9aa22428c17a7e7
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036481"
---
# <a name="sfctl-container"></a>sfctl container
Uruchom polecenia związane z kontenerem w węźle klastra.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| Invoke-API | Wywołaj interfejs API kontenera dla kontenera wdrożonego w węźle Service Fabric dla danego pakietu kodu. |
| logs | Pobiera dzienniki kontenerów dla kontenera wdrożonego w węźle Service Fabric dla danego pakietu kodu. |

## <a name="sfctl-container-invoke-api"></a>Wywołanie kontenera sfctl — interfejs API
Wywołaj interfejs API kontenera dla kontenera wdrożonego w węźle Service Fabric dla danego pakietu kodu.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --ID aplikacji [wymagane] | Tożsamość aplikacji. <br><br> Jest to zazwyczaj pełna nazwa aplikacji bez schematu identyfikatora URI "Sieć szkieletowa\:". Począwszy od wersji 6,0, hierarchiczne nazwy są rozdzielane znakami\~"". Na przykład jeśli nazwa aplikacji to "Fabric\:/MyApp/APP1", tożsamość aplikacji byłaby "MojaApl\~APP1" w wersji 6.0 + i "MojaApl/APP1" w poprzednich wersjach. |
| --Code-Package-instance-ID [wymagane] | Identyfikator, który jednoznacznie identyfikuje wystąpienie pakietu kodu wdrożone w węźle usługi Service Fabric. <br><br> Może być pobrany przez "kod usługi — lista pakietów". |
| --Code-package-name [wymagane] | Nazwa pakietu kodu określona w manifeście usługi zarejestrowana w ramach typu aplikacji w klastrze Service Fabric. |
| --Container-API-URI-Path [wymagane] | Ścieżka identyfikatora URI interfejsu API REST kontenera, użyj "{ID}" zamiast nazwy kontenera/identyfikatora. |
| --Node-Name [wymagane] | Nazwa węzła. |
| --service-manifest-name    [Required] | Nazwa manifestu usługi zarejestrowana w ramach typu aplikacji w klastrze Service Fabric. |
| --kontener — treść | Treść żądania HTTP dla interfejsu API REST kontenera. |
| --Container-API-Content-Type | Typ zawartości dla interfejsu API REST kontenera — wartość domyślna to "Application/JSON". |
| --Container-API-http-Verb | Zlecenie HTTP dla interfejsu API REST kontenera; wartość domyślna to GET. |
| --timeout-t | Limit czasu serwera (w sekundach).  Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości\: json, jsonc, Table, TSV.  Domyślny\: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać\:więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-container-logs"></a>sfctl dzienników kontenerów
Pobiera dzienniki kontenerów dla kontenera wdrożonego w węźle Service Fabric dla danego pakietu kodu.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --ID aplikacji [wymagane] | Tożsamość aplikacji. <br><br> Jest to zazwyczaj pełna nazwa aplikacji bez schematu identyfikatora URI "Sieć szkieletowa\:". Począwszy od wersji 6,0, hierarchiczne nazwy są rozdzielane znakami\~"". Na przykład jeśli nazwa aplikacji to "Fabric\:/MyApp/APP1", tożsamość aplikacji byłaby "MojaApl\~APP1" w wersji 6.0 + i "MojaApl/APP1" w poprzednich wersjach. |
| --Code-Package-instance-ID [wymagane] | Identyfikator wystąpienia pakietu kodu, który może zostać pobrany przez "kod usługi — pakiet-lista". |
| --Code-package-name [wymagane] | Nazwa pakietu kodu określona w manifeście usługi zarejestrowana w ramach typu aplikacji w klastrze Service Fabric. |
| --Node-Name [wymagane] | Nazwa węzła. |
| --service-manifest-name    [Required] | Nazwa manifestu usługi zarejestrowana w ramach typu aplikacji w klastrze Service Fabric. |
| --tail | Liczba wierszy do wyświetlenia na końcu dzienników. Wartość domyślna to 100. "wszystkie", aby wyświetlić kompletne dzienniki. |
| --timeout-t | Limit czasu serwera (w sekundach).  Wartość\: domyślna 60. |

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
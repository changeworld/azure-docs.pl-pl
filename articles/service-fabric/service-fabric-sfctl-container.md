---
title: Kontener interfejsu WIERSZA POLECENIA usługi Azure Service
description: Dowiedz się więcej o sfctl, interfejsie wiersza polecenia sieci szkieletowej usługi Azure. Zawiera listę poleceń dla kontenerów.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 07861e2b67eea67740b341cbea994de978973664
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906121"
---
# <a name="sfctl-container"></a>sfctl container
Uruchamianie poleceń związanych z kontenerem w węźle klastra.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| invoke-api | Wywoływanie interfejsu API kontenera w kontenerze wdrożonym w węźle sieci szkieletowej usług dla danego pakietu kodu. |
| dzienniki | Pobiera dzienniki kontenera dla kontenera wdrożony w węźle sieci szkieletowej usług. |

## <a name="sfctl-container-invoke-api"></a>sfctl container invoke-api
Wywoływanie interfejsu API kontenera w kontenerze wdrożonym w węźle sieci szkieletowej usług dla danego pakietu kodu.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --application-id [Wymagane] | Tożsamość aplikacji. <br><br> Zazwyczaj jest to pełna nazwa aplikacji bez\:schematu URI 'fabric'. Począwszy od wersji 6.0, nazwy hierarchiczne\~są rozdzielane znakiem " ". Na przykład jeśli nazwa aplikacji\:to "fabric /myapp/app1", tożsamość\~aplikacji będzie "myapp app1" w wersji 6.0+ i "myapp/app1" w poprzednich wersjach. |
| --code-package-instance-id [Wymagane] | Identyfikator, który jednoznacznie identyfikuje wystąpienie pakietu kodu wdrożone w węźle sieci szkieletowej usług. <br><br> Można pobrać przez "service code-package-list". |
| --code-package-name [Wymagane] | Nazwa pakietu kodu określonego w manifeście usługi zarejestrowany jako część typu aplikacji w klastrze sieci szkieletowej usług. |
| --container-api-uri-path [Wymagane] | Ścieżka identyfikatora URI interfejsu API interfejsu KONTENERA, użyj "{ID}" zamiast nazwy/identyfikatora kontenera. |
| --node-name [Wymagane] | Nazwa węzła. |
| --service-manifest-name [Wymagane] | Nazwa manifestu usługi zarejestrowanego jako część typu aplikacji w klastrze sieci szkieletowej usług. |
| --container-api-body | Treść żądania HTTP dla interfejsu API REST kontenera. |
| --container-api-content-type | Typ zawartości dla interfejsu API REST kontenera, domyślnie "application/json". |
| --container-api-http-czasownik | Zlecenie HTTP dla interfejsu API REST kontenera, domyślnie get. |
| --timeout -t | Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-container-logs"></a>dzienniki kontenerów sfctl
Pobiera dzienniki kontenera dla kontenera wdrożony w węźle sieci szkieletowej usług.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --application-id [Wymagane] | Tożsamość aplikacji. <br><br> Zazwyczaj jest to pełna nazwa aplikacji bez\:schematu URI 'fabric'. Począwszy od wersji 6.0, nazwy hierarchiczne\~są rozdzielane znakiem " ". Na przykład jeśli nazwa aplikacji\:to "fabric /myapp/app1", tożsamość\~aplikacji będzie "myapp app1" w wersji 6.0+ i "myapp/app1" w poprzednich wersjach. |
| --code-package-instance-id [Wymagane] | Identyfikator wystąpienia pakietu kodu, który można pobrać przez "service code-package-list". |
| --code-package-name [Wymagane] | Nazwa pakietu kodu określonego w manifeście usługi zarejestrowany jako część typu aplikacji w klastrze sieci szkieletowej usług. |
| --node-name [Wymagane] | Nazwa węzła. |
| --service-manifest-name [Wymagane] | Nazwa manifestu usługi zarejestrowanego jako część typu aplikacji w klastrze sieci szkieletowej usług. |
| --ogon | Liczba wierszy do wyświetlenia od końca dzienników. Wartość domyślna to 100. "wszystko", aby wyświetlić pełne dzienniki. |
| --timeout -t | Wartość\: domyślna 60. |

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
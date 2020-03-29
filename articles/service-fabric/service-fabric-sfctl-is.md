---
title: Platforma Azure Service Fabric CLI- sfctl jest
description: Dowiedz się więcej o sfctl, interfejsie wiersza polecenia sieci szkieletowej usługi Azure. Zawiera listę poleceń do zarządzania infrastrukturą.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: a64aaca97c9df61d795c82d64e6048d6f9ae032f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906075"
---
# <a name="sfctl-is"></a>sfctl is
Wysyłaj kwerendy i wysyłaj polecenia do usługi infrastruktury.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| command | Wywołuje polecenie administracyjne w danym wystąpieniu usługi infrastruktury. |
| query | Wywołuje kwerendę tylko do odczytu w danym wystąpieniu usługi infrastruktury. |

## <a name="sfctl-is-command"></a>sfctl jest poleceniem
Wywołuje polecenie administracyjne w danym wystąpieniu usługi infrastruktury.

W przypadku klastrów, które mają jedno lub więcej wystąpień usługi infrastruktury skonfigurowane, ten interfejs API umożliwia wysyłanie poleceń specyficznych dla infrastruktury do określonego wystąpienia usługi infrastruktury. Dostępne polecenia i odpowiadające im formaty odpowiedzi różnią się w zależności od infrastruktury, w której jest uruchomiony klaster. Ten interfejs API obsługuje platformę sieci szkieletowej usług; nie jest przeznaczony do użycia bezpośrednio z kodu.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --command [Wymagane] | Tekst polecenia, które ma zostać wywołane. Zawartość polecenia jest specyficzne dla infrastruktury. |
| --service-id | Tożsamość usługi infrastruktury. <br><br> Jest to pełna nazwa usługi infrastruktury bez\:schematu URI "fabric". Ten parametr jest wymagany tylko dla klastra, który ma więcej niż jedno wystąpienie uruchomionej usługi infrastruktury. |
| --timeout -t | Wartość\: domyślna 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --debug | Zwiększ szczegółowość rejestrowania, aby wyświetlić wszystkie dzienniki debugowania. |
| --help -h | Pokaż ten komunikat pomocy i zamknij. |
| --wyjście -o | Format wyjściowy.  Dozwolone wartości\: json, jsonc, table, tsv.  Domyślny\: json. |
| --zapytanie | Ciąg zapytania JMESPath. Zobacz\:http //jmespath.org/ aby uzyskać więcej informacji i przykładów. |
| --pełne | Zwiększ szczegółowość rejestrowania. Użyj --debug dla pełnych dzienników debugowania. |

## <a name="sfctl-is-query"></a>sfctl jest kwerendą
Wywołuje kwerendę tylko do odczytu w danym wystąpieniu usługi infrastruktury.

W przypadku klastrów, które mają co najmniej jedno wystąpienie usługi infrastruktury skonfigurowane, ten interfejs API umożliwia wysyłanie zapytań specyficznych dla infrastruktury do określonego wystąpienia usługi infrastruktury. Dostępne polecenia i odpowiadające im formaty odpowiedzi różnią się w zależności od infrastruktury, w której jest uruchomiony klaster. Ten interfejs API obsługuje platformę sieci szkieletowej usług; nie jest przeznaczony do użycia bezpośrednio z kodu.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --command [Wymagane] | Tekst polecenia, które ma zostać wywołane. Zawartość polecenia jest specyficzne dla infrastruktury. |
| --service-id | Tożsamość usługi infrastruktury. <br><br> Jest to pełna nazwa usługi infrastruktury bez\:schematu URI "fabric". Ten parametr jest wymagany tylko dla klastra, który ma więcej niż jedno wystąpienie uruchomionej usługi infrastruktury. |
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
